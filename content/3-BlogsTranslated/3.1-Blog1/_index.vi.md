---
title: "Blog 1"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Uploading to Amazon S3 directly from a web or mobile application

| Mục | Chi tiết |
| --- | --- |
| **Bài gốc** | [Uploading to Amazon S3 directly from a web or mobile application](https://aws.amazon.com/blogs/compute/uploading-to-amazon-s3-directly-from-a-web-or-mobile-application/) |
| **Nguồn** | AWS Compute Blog · James Beswick · 2020 |
| **Chủ đề** | Serverless · Lưu trữ |
| **Ngày dịch** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

{{% notice info %}}
Chương trình yêu cầu dịch bài gốc sang tiếng Việt. Dán bản dịch của bạn vào phần "Bản dịch" bên dưới; phần tóm tắt và liên hệ dự án là phần phân tích riêng của bạn.
{{% /notice %}}

## Tóm tắt

Bài viết hướng dẫn cách cho trình duyệt hoặc ứng dụng di động upload file thẳng lên Amazon S3 thay vì đi qua máy chủ ứng dụng. Client gọi một endpoint API Gateway, Lambda tạo presigned URL có thời hạn 300 giây với tên file ngẫu nhiên, rồi client gửi file thẳng tới S3. Cách này giảm tải cho máy chủ và lưu lượng mạng; bài cũng lưu ý cần cấu hình CORS cho bucket và nên thêm xác thực (JWT authorizer) để giới hạn ai được upload.

## Liên hệ với dự án SnapThumb

SnapThumb dùng đúng mô hình này ở bước 2–5. Điểm khác: SnapThumb dùng presigned **POST** thay vì presigned PUT, vì POST cho phép đặt điều kiện `content-length-range` để S3 tự từ chối file lớn hơn 10 MB, điều mà bài gốc ghi nhận là chưa xử lý.

## Bản dịch

<span style="background:#fff3c4">[Dán bản dịch tiếng Việt của bài viết vào đây, giữ nguyên các tiêu đề và hình ảnh của bài gốc]</span>
