---
title: "Create the Function and S3 Trigger"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Step 1: Create the function
1. **AWS Lambda → Create function → Author from scratch**.
2. **Name:** `snapthumb-thumbnail`, **Runtime:** **Python 3.12**, **Architecture:** **x86_64** → **Create function**.
3. Replace `lambda_function.py` with the code below → **Deploy**.

```python
"""SnapThumb - hàm Lambda tạo ảnh thu nhỏ.

Kích hoạt bởi sự kiện S3 ObjectCreated trên tiền tố uploads/.
Đọc ảnh gốc, tạo thumbnail JPEG (cạnh dài tối đa THUMB_MAX_SIZE px), ghi vào thumbnails/,
rồi cập nhật trạng thái và thông số vào DynamoDB.
"""
import io
import logging
import os
import urllib.parse
from datetime import datetime, timezone

import boto3
from PIL import Image, ImageOps

logger = logging.getLogger()
logger.setLevel(logging.INFO)

UPLOAD_PREFIX = "uploads/"
THUMB_PREFIX = "thumbnails/"
THUMB_MAX_SIZE = int(os.environ.get("THUMB_MAX_SIZE", "320"))
JPEG_QUALITY = 85

_s3 = None
_table = None


def s3():
    global _s3
    if _s3 is None:
        _s3 = boto3.client("s3")
    return _s3


def table():
    global _table
    if _table is None:
        _table = boto3.resource("dynamodb").Table(os.environ["TABLE_NAME"])
    return _table


def make_thumbnail(data, max_size=THUMB_MAX_SIZE):
    """Trả về (bytes JPEG của thumbnail, (rộng, cao) gốc, (rộng, cao) thumbnail)."""
    with Image.open(io.BytesIO(data)) as original:
        img = ImageOps.exif_transpose(original)  # xoay đúng chiều theo EXIF của ảnh điện thoại
        original_size = img.size
        if img.mode in ("RGBA", "LA") or (img.mode == "P" and "transparency" in img.info):
            # JPEG không có kênh trong suốt: đặt ảnh lên nền trắng
            rgba = img.convert("RGBA")
            img = Image.new("RGB", rgba.size, (255, 255, 255))
            img.paste(rgba, mask=rgba.split()[-1])
        elif img.mode not in ("RGB", "L"):
            img = img.convert("RGB")
        img.thumbnail((max_size, max_size), Image.LANCZOS)  # giữ nguyên tỉ lệ
        out = io.BytesIO()
        img.save(out, "JPEG", quality=JPEG_QUALITY, optimize=True, progressive=True)
        return out.getvalue(), original_size, img.size


def image_id_from_key(key):
    name = key[len(UPLOAD_PREFIX):]
    return name.rsplit(".", 1)[0]


def parse_time(value):
    return datetime.fromisoformat(value.replace("Z", "+00:00"))


def process_record(record):
    bucket = record["s3"]["bucket"]["name"]
    key = urllib.parse.unquote_plus(record["s3"]["object"]["key"])
    if not key.startswith(UPLOAD_PREFIX):
        logger.info("Skip %s (not under %s)", key, UPLOAD_PREFIX)
        return
    image_id = image_id_from_key(key)
    thumb_key = f"{THUMB_PREFIX}{image_id}.jpg"

    try:
        data = s3().get_object(Bucket=bucket, Key=key)["Body"].read()
        thumb, (ow, oh), (tw, th) = make_thumbnail(data)
        s3().put_object(Bucket=bucket, Key=thumb_key, Body=thumb, ContentType="image/jpeg")
        now = datetime.now(timezone.utc)
        processing_ms = int((now - parse_time(record["eventTime"])).total_seconds() * 1000)
        table().update_item(
            Key={"id": image_id},
            UpdateExpression=(
                "SET #s = :s, thumbKey = :tk, originalBytes = :ob, thumbBytes = :tb, "
                "originalWidth = :ow, originalHeight = :oh, thumbWidth = :tw, thumbHeight = :th, "
                "processedAt = :pa, processingMs = :pm REMOVE errorMessage"
            ),
            ExpressionAttributeNames={"#s": "status"},
            ExpressionAttributeValues={
                ":s": "DONE", ":tk": thumb_key, ":ob": len(data), ":tb": len(thumb),
                ":ow": ow, ":oh": oh, ":tw": tw, ":th": th,
                ":pa": now.isoformat(), ":pm": max(processing_ms, 0),
            },
        )
        logger.info("Thumbnail %s: %dx%d -> %dx%d, %d -> %d bytes",
                    image_id, ow, oh, tw, th, len(data), len(thumb))
    except Exception as err:  # ảnh hỏng hoặc không đọc được: đánh dấu lỗi để giao diện hiển thị
        logger.exception("Failed to process %s", key)
        table().update_item(
            Key={"id": image_id},
            UpdateExpression="SET #s = :s, errorMessage = :e",
            ExpressionAttributeNames={"#s": "status"},
            ExpressionAttributeValues={":s": "FAILED", ":e": str(err)[:300]},
        )


def handler(event, context):
    for record in event.get("Records", []):
        process_record(record)
    return {"processed": len(event.get("Records", []))}


lambda_handler = handler  # tên hàm mặc định của Lambda console / default handler name in the console
```

#### What the code does

| Part | Explanation |
| --- | --- |
| `make_thumbnail` | Opens the image, fixes EXIF rotation, flattens transparency onto white, shrinks so the longest edge is at most 320 px keeping the aspect ratio, saves as JPEG quality 85 |
| `image_id_from_key` | `uploads/3f9c.jpg` → `3f9c`, the item's key in DynamoDB |
| `key.startswith(UPLOAD_PREFIX)` | Safety check: ignores anything outside `uploads/` |
| `processing_ms` | Time from the S3 event to the finished thumbnail |
| `except` branch | A broken file is marked `FAILED` with the error, so the page can show it |

## Step 2: Configuration
1. **Configuration → General configuration → Edit:** **Memory** `1024` MB (more memory also means more CPU, so resizing is faster), **Timeout** `30` sec.
2. **Configuration → Environment variables → Edit:** `TABLE_NAME` = `snapthumb-images`, `THUMB_MAX_SIZE` = `320`.
3. **Code** tab, bottom → **Layers → Add a layer → Custom layers** → `pillow-py312`, version `1` → **Add**.

## Step 3: Permissions
**Configuration → Permissions** → click the role name → **Add permissions → Create inline policy → JSON**. Replace `YOURNAME` and `ACCOUNT_ID`, then name it `snapthumb-thumbnail-access`.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadOriginals",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::snapthumb-images-YOURNAME/uploads/*"
    },
    {
      "Sid": "WriteThumbnails",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::snapthumb-images-YOURNAME/thumbnails/*"
    },
    {
      "Sid": "UpdateMetadata",
      "Effect": "Allow",
      "Action": "dynamodb:UpdateItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:ACCOUNT_ID:table/snapthumb-images"
    }
  ]
}
```

## Step 4: Add the S3 trigger
1. In the function overview, **Add trigger → S3**.
2. **Bucket:** `snapthumb-images-<your-name>`, **Event types:** *All object create events*, **Prefix:** `uploads/`.
3. Tick the acknowledgement about recursive invocation → **Add**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> function overview with the S3 trigger and Pillow layer → <code>static/images/5-Workshop/5.5/02-trigger.png</code></p>
<!-- ![function overview with the S3 trigger and Pillow layer](/images/5-Workshop/5.5/02-trigger.png?width=90pc) -->

{{% notice warning %}}
Never leave the prefix empty. The function writes to the same bucket; without `uploads/` it would trigger itself for every thumbnail.
{{% /notice %}}

## Step 5: Test
1. Upload a photo to the bucket's `uploads/` folder in the S3 console.
2. After a few seconds, open `thumbnails/`: a `.jpg` with the same name should be there.
3. In DynamoDB → **Explore items**, the new item has `status` = `DONE`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> thumbnail in thumbnails/ and the DONE item → <code>static/images/5-Workshop/5.5/03-test.png</code></p>
<!-- ![thumbnail in thumbnails/ and the DONE item](/images/5-Workshop/5.5/03-test.png?width=90pc) -->
