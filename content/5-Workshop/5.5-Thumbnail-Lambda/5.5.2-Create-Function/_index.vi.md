---
title: "Tạo hàm và trigger S3"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Bước 1: Tạo hàm
1. **AWS Lambda → Create function → Author from scratch**.
2. **Name:** `snapthumb-thumbnail`, **Runtime:** **Python 3.12**, **Architecture:** **x86_64** → **Create function**.
3. Thay nội dung `lambda_function.py` bằng code dưới đây → **Deploy**.

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

#### Giải thích code

| Phần code | Giải thích |
| --- | --- |
| `make_thumbnail` | Mở ảnh, xoay đúng chiều theo EXIF, đặt phần trong suốt lên nền trắng, thu nhỏ để cạnh dài nhất tối đa 320 px mà giữ tỉ lệ, lưu thành JPEG chất lượng 85 |
| `image_id_from_key` | `uploads/3f9c.jpg` → `3f9c`, chính là khoá của bản ghi trong DynamoDB |
| `key.startswith(UPLOAD_PREFIX)` | Kiểm tra an toàn: bỏ qua mọi file ngoài `uploads/` |
| `processing_ms` | Thời gian từ lúc S3 phát sự kiện tới khi thumbnail xong |
| Nhánh `except` | File hỏng được đánh dấu `FAILED` kèm lỗi để trang web hiển thị |

## Bước 2: Cấu hình
1. **Configuration → General configuration → Edit:** **Memory** `1024` MB (nhiều bộ nhớ hơn thì cũng nhiều CPU hơn, xử lý ảnh nhanh hơn), **Timeout** `30` giây.
2. **Configuration → Environment variables → Edit:** `TABLE_NAME` = `snapthumb-images`, `THUMB_MAX_SIZE` = `320`.
3. Tab **Code**, cuối trang → **Layers → Add a layer → Custom layers** → `pillow-py312`, version `1` → **Add**.

## Bước 3: Cấp quyền
**Configuration → Permissions** → bấm tên role → **Add permissions → Create inline policy → JSON**. Thay `YOURNAME` và `ACCOUNT_ID`, đặt tên `snapthumb-thumbnail-access`.

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

## Bước 4: Thêm trigger S3
1. Ở phần tổng quan của hàm, **Add trigger → S3**.
2. **Bucket:** `snapthumb-images-<ten-ban>`, **Event types:** *All object create events*, **Prefix:** `uploads/`.
3. Tick ô xác nhận về recursive invocation → **Add**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> tổng quan hàm có trigger S3 và layer Pillow → <code>static/images/5-Workshop/5.5/02-trigger.png</code></p>
<!-- ![tổng quan hàm có trigger S3 và layer Pillow](/images/5-Workshop/5.5/02-trigger.png?width=90pc) -->

{{% notice warning %}}
Không bao giờ để trống Prefix. Hàm ghi vào chính bucket này; nếu không có `uploads/`, mỗi thumbnail sẽ lại kích hoạt hàm thành vòng lặp vô hạn.
{{% /notice %}}

## Bước 5: Kiểm thử
1. Trong console S3, upload một ảnh vào thư mục `uploads/` của bucket.
2. Vài giây sau, mở `thumbnails/`: phải có file `.jpg` cùng tên.
3. Vào DynamoDB → **Explore items**, bản ghi mới có `status` = `DONE`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> thumbnail trong thumbnails/ và bản ghi DONE → <code>static/images/5-Workshop/5.5/03-test.png</code></p>
<!-- ![thumbnail trong thumbnails/ và bản ghi DONE](/images/5-Workshop/5.5/03-test.png?width=90pc) -->
