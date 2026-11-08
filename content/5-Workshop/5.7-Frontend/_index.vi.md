---
title: "Đưa giao diện web lên S3"
date: 2026-09-07
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Bước 1: Bucket cho website
1. **S3 → Create bucket** `snapthumb-web-<ten-ban>`, **bỏ chọn** *Block all public access* và xác nhận.
2. **Properties → Static website hosting → Edit → Enable**, index document `index.html`.
3. **Permissions → Bucket policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadForWebsite",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::snapthumb-web-YOURNAME/*"
    }
  ]
}
```

## Bước 2: Tải trang web lên
1. Lấy file `web/index.html` trong gói mã nguồn SnapThumb <span style="background:#fff3c4">[link mã nguồn của bạn]</span>.
2. Tạo file `config.js` cùng thư mục, điền Invoke URL của bạn:

```js
window.SNAPTHUMB_CONFIG = { apiUrl: "https://abc123.execute-api.ap-southeast-1.amazonaws.com" };
```

3. Upload cả hai file lên bucket rồi mở **Bucket website endpoint** ở tab **Properties**.

## Trang web dùng API như thế nào

1. Với mỗi file được chọn, trang gọi `POST /uploads` và nhận về `upload.url` cùng `upload.fields`.
2. Trang tạo `FormData` gồm tất cả các field, rồi đặt file **cuối cùng** (S3 yêu cầu đúng thứ tự này), và gửi tới `upload.url`.
3. Trang gọi `GET /images` mỗi 1,5 giây khi còn ảnh `PENDING`, và hiện thumbnail khi ảnh chuyển sang `DONE`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> trang SnapThumb hiển thị các ảnh thu nhỏ đã xong → <code>static/images/5-Workshop/5.7/01-website.png</code></p>
<!-- ![trang SnapThumb hiển thị các ảnh thu nhỏ đã xong](/images/5-Workshop/5.7/01-website.png?width=90pc) -->

{{% notice tip %}}
Nếu không có `config.js`, trang chạy chế độ demo và thu nhỏ ảnh ngay trong trình duyệt. Huy hiệu ở đầu trang cho biết đang ở chế độ nào.
{{% /notice %}}
