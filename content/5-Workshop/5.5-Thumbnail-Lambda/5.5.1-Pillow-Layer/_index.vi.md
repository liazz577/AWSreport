---
title: "Đóng gói layer Pillow"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Vì sao cần layer
Pillow có phần code đã biên dịch, nên phải đúng bản cho Linux của Lambda và Python 3.12, không phải bản cho máy tính của bạn. Lệnh dưới đây yêu cầu pip tải đúng bản Linux.

## Đóng gói và đăng layer trong CloudShell
1. Mở **CloudShell** (biểu tượng `>_` trên thanh trên cùng), ở region `ap-southeast-1`.
2. Chạy:

```bash
mkdir -p pillow-layer/python
python3 -m pip install --target pillow-layer/python \
  --platform manylinux_2_28_x86_64 --platform manylinux2014_x86_64 \
  --implementation cp --python-version 3.12 --only-binary=:all: Pillow==11.0.0
cd pillow-layer && zip -qr ../pillow-layer.zip python && cd ..
aws lambda publish-layer-version --layer-name pillow-py312 \
  --zip-file fileb://pillow-layer.zip \
  --compatible-runtimes python3.12 --compatible-architectures x86_64 \
  --query LayerVersionArn --output text
```

3. Lệnh cuối in ra ARN của layer, ví dụ `arn:aws:lambda:ap-southeast-1:123456789012:layer:pillow-py312:1`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> CloudShell in ra ARN của layer → <code>static/images/5-Workshop/5.5/01-layer.png</code></p>
<!-- ![CloudShell in ra ARN của layer](/images/5-Workshop/5.5/01-layer.png?width=90pc) -->

{{% notice note %}}
Layer Python phải đặt thư viện trong thư mục `python/` bên trong file zip; Lambda tự thêm thư mục này vào đường dẫn import.
{{% /notice %}}
