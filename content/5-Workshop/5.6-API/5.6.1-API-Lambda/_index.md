---
title: "Create the API Lambda"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Step 1: Create the function
1. **Lambda → Create function**, name `snapthumb-api`, **Python 3.12** → **Create function**.
2. Replace `lambda_function.py` with the code below → **Deploy**.

```python
"""SnapThumb - hàm Lambda cho API (API Gateway HTTP API, payload 2.0).

POST   /uploads      -> kiểm tra file, tạo bản ghi PENDING, trả về presigned POST để trình duyệt upload thẳng lên S3
GET    /images       -> danh sách ảnh mới nhất kèm link xem có thời hạn
DELETE /images/{id}  -> xoá ảnh gốc, thumbnail và bản ghi
"""
import json
import logging
import os
import uuid
from datetime import datetime, timezone
from decimal import Decimal

import boto3
from botocore.config import Config

logger = logging.getLogger()
logger.setLevel(logging.INFO)

ALLOWED_TYPES = {"image/jpeg": "jpg", "image/png": "png", "image/webp": "webp"}
MAX_BYTES = 10 * 1024 * 1024
UPLOAD_EXPIRES = 300      # giây: link upload hết hạn sau 5 phút
VIEW_EXPIRES = 3600       # giây: link xem ảnh hết hạn sau 1 giờ
LIST_LIMIT = 50

_s3 = None
_table = None


def s3():
    global _s3
    if _s3 is None:
        region = os.environ.get("AWS_REGION", "us-east-1")
        # Endpoint theo region để link upload đi thẳng tới đúng region của bucket.
        _s3 = boto3.client("s3", region_name=region, endpoint_url=f"https://s3.{region}.amazonaws.com",
                           config=Config(signature_version="s3v4", s3={"addressing_style": "virtual"}))
    return _s3


def table():
    global _table
    if _table is None:
        _table = boto3.resource("dynamodb").Table(os.environ["TABLE_NAME"])
    return _table


def bucket():
    return os.environ["BUCKET_NAME"]


def _json_default(value):
    if isinstance(value, Decimal):
        return int(value) if value == value.to_integral_value() else float(value)
    raise TypeError(type(value))


def reply(status, payload=None):
    res = {"statusCode": status, "headers": {"Content-Type": "application/json; charset=utf-8"}}
    if payload is not None:
        res["body"] = json.dumps(payload, ensure_ascii=False, default=_json_default)
    return res


def create_upload(event):
    try:
        body = json.loads(event.get("body") or "{}")
    except (json.JSONDecodeError, TypeError):
        return reply(400, {"error": "Body must be JSON"})
    filename = str(body.get("filename") or "").strip()[:200]
    content_type = str(body.get("contentType") or "")
    size = body.get("size")
    if not filename:
        return reply(400, {"error": "filename is required"})
    if content_type not in ALLOWED_TYPES:
        return reply(400, {"error": "Only JPEG, PNG and WebP images are allowed"})
    if not isinstance(size, int) or size <= 0 or size > MAX_BYTES:
        return reply(400, {"error": f"File size must be between 1 byte and {MAX_BYTES // (1024 * 1024)} MB"})

    image_id = uuid.uuid4().hex
    key = f"uploads/{image_id}.{ALLOWED_TYPES[content_type]}"
    table().put_item(Item={
        "id": image_id, "filename": filename, "contentType": content_type,
        "originalKey": key, "status": "PENDING",
        "createdAt": datetime.now(timezone.utc).isoformat(),
    })
    upload = s3().generate_presigned_post(
        Bucket=bucket(), Key=key,
        Fields={"Content-Type": content_type},
        # S3 tự từ chối nếu file sai loại hoặc lớn hơn giới hạn, kể cả khi trình duyệt khai báo sai.
        Conditions=[{"Content-Type": content_type}, ["content-length-range", 1, MAX_BYTES]],
        ExpiresIn=UPLOAD_EXPIRES,
    )
    return reply(201, {"id": image_id, "upload": upload})


def view_url(key):
    return s3().generate_presigned_url("get_object", Params={"Bucket": bucket(), "Key": key},
                                       ExpiresIn=VIEW_EXPIRES)


def list_images(event):
    items, kwargs = [], {}
    while True:
        page = table().scan(**kwargs)
        items.extend(page.get("Items", []))
        if "LastEvaluatedKey" not in page or len(items) >= 500:
            break
        kwargs["ExclusiveStartKey"] = page["LastEvaluatedKey"]
    items.sort(key=lambda i: i.get("createdAt", ""), reverse=True)
    items = items[:LIST_LIMIT]
    for item in items:
        item["originalUrl"] = view_url(item["originalKey"])
        if item.get("thumbKey"):
            item["thumbUrl"] = view_url(item["thumbKey"])
    return reply(200, {"images": items})


def delete_image(event):
    image_id = (event.get("pathParameters") or {}).get("id", "")
    item = table().get_item(Key={"id": image_id}).get("Item")
    if not item:
        return reply(404, {"error": "Image not found"})
    keys = [item["originalKey"]] + ([item["thumbKey"]] if item.get("thumbKey") else [])
    s3().delete_objects(Bucket=bucket(), Delete={"Objects": [{"Key": k} for k in keys]})
    table().delete_item(Key={"id": image_id})
    return reply(204)


ROUTES = {
    "POST /uploads": create_upload,
    "GET /images": list_images,
    "DELETE /images/{id}": delete_image,
}


def handler(event, context):
    route = ROUTES.get(event.get("routeKey", ""))
    if not route:
        return reply(404, {"error": "Not found"})
    try:
        return route(event)
    except Exception:
        logger.exception("Unhandled error on %s", event.get("routeKey"))
        return reply(500, {"error": "Internal error"})


lambda_handler = handler  # tên hàm mặc định của Lambda console / default handler name in the console
```

#### What the code does

| Part | Explanation |
| --- | --- |
| `ROUTES` | Maps API Gateway's `routeKey` (e.g. `POST /uploads`) to a Python function |
| `create_upload` | Checks name, type (JPEG/PNG/WebP) and size (≤ 10 MB), writes `PENDING`, calls `generate_presigned_post` |
| `Conditions` | Rules baked into the signature: S3 rejects a file of another type or over 10 MB |
| `list_images` | Reads all items, sorts newest first, adds `generate_presigned_url` view links |
| `_json_default` | DynamoDB returns numbers as `Decimal`; this converts them for JSON |

## Step 2: Configuration
1. **Timeout** `10` sec.
2. **Environment variables:** `TABLE_NAME` = `snapthumb-images`, `BUCKET_NAME` = `snapthumb-images-<your-name>`.

## Step 3: Permissions
Add an inline policy `snapthumb-api-access` to the function's role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Metadata",
      "Effect": "Allow",
      "Action": ["dynamodb:PutItem", "dynamodb:GetItem", "dynamodb:Scan", "dynamodb:DeleteItem"],
      "Resource": "arn:aws:dynamodb:ap-southeast-1:ACCOUNT_ID:table/snapthumb-images"
    },
    {
      "Sid": "SignUploads",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::snapthumb-images-YOURNAME/uploads/*"
    },
    {
      "Sid": "SignViewsAndDelete",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::snapthumb-images-YOURNAME/*"
    }
  ]
}
```

{{% notice note %}}
Signing a link makes no call to S3, but the link only works if the signer is allowed to do what the link does. That is why the API role needs `s3:PutObject` on `uploads/*` even though it never uploads anything itself.
{{% /notice %}}
