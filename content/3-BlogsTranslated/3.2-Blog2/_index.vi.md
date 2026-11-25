---
title: "Blog 2"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway

| Mục | Chi tiết |
| --- | --- |
| **Bài gốc** | [Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway](https://aws.amazon.com/blogs/compute/resize-images-on-the-fly-with-amazon-s3-aws-lambda-and-amazon-api-gateway/) |
| **Nguồn** | AWS Compute Blog · John Pignata · 2017 |
| **Chủ đề** | Serverless · Xử lý ảnh |

## Tóm tắt

Bài viết trình bày cách tạo ảnh đổi kích thước theo yêu cầu. Ảnh gốc lưu trên S3; khi người dùng yêu cầu một kích thước chưa có, quy tắc chuyển hướng của S3 đưa request tới API Gateway, Lambda tải ảnh gốc, đổi kích thước, lưu lại vào S3 và chuyển hướng người dùng tới file mới. Những lần sau, ảnh được phục vụ thẳng từ S3.

## Liên hệ với dự án SnapThumb

Bài này giúp so sánh hai cách thiết kế. Bài gốc tạo ảnh **khi có người xem** (lazy), phù hợp khi cần nhiều kích thước khác nhau. SnapThumb tạo ảnh **ngay khi upload** (eager) bằng sự kiện S3, phù hợp khi chỉ cần một kích thước cố định và muốn ảnh có sẵn ngay. Phần thảo luận này dùng cho mục rủi ro và hướng phát triển của báo cáo.

## Nội dung chính

### Xử lý sớm hay xử lý khi cần
Cách thường gặp là **xử lý sớm (eager)**: mỗi khi có ảnh mới, sự kiện S3 gọi Lambda tạo sẵn mọi kích thước. Bài đề xuất **xử lý khi cần (lazy)**: chỉ tạo một kích thước khi có người yêu cầu nó lần đầu. Tác giả nêu ba lợi ích:

- **Đổi thiết kế nhanh:** thêm kích thước mới không cần xử lý lại toàn bộ kho ảnh cũ.
- **Tiết kiệm lưu trữ:** kích thước không ai dùng thì không bao giờ được tạo; có thể dùng lifecycle rule để tự xoá ảnh đã resize lâu ngày.
- **Chịu lỗi tốt hơn:** nếu một lần resize lỗi, lần yêu cầu sau sẽ thử lại, thay vì lỗi vĩnh viễn cho mọi người.

### Luồng một yêu cầu
1. Trình duyệt xin một ảnh đã resize từ website endpoint của bucket.
2. Ảnh chưa tồn tại, S3 trả chuyển hướng tạm thời sang API.
3. Trình duyệt đi theo chuyển hướng tới API Gateway.
4. API Gateway gọi hàm Lambda.
5. Lambda tải ảnh gốc, đổi kích thước rồi lưu lại vào bucket đúng tên đã được yêu cầu.
6. API Gateway chuyển hướng vĩnh viễn trình duyệt về file mới trên S3.
7. Từ lần sau, S3 trả ảnh trực tiếp. Nếu file bị xoá, vòng trên lặp lại.

### Quy tắc chuyển hướng của S3
Bucket bật static website hosting và có một routing rule: khi gặp lỗi 404, chuyển hướng (mã **307**, tạm thời) tới hostname của API, đổi phần đầu đường dẫn thành tham số `key` của hàm resize. Dùng 307 để trình duyệt không nhớ chuyển hướng này mãi mãi, vì sau khi ảnh được tạo thì S3 đã trả được.

### Các bước dựng
- **Bucket:** cho phép đọc công khai, bật static website hosting, ghi lại endpoint.
- **Lambda:** hàm tên `resize`, trigger API Gateway mở công khai, mã lấy từ gói `function.zip` trong repo `serverless-image-resizing`. Bộ nhớ **1536 MB**, timeout **10 giây**. Role chỉ cần quyền ghi log CloudWatch và `s3:PutObject` vào bucket.
- **Biến môi trường:** `BUCKET` (tên bucket) và `URL` (endpoint website của bucket).
- **Thử nghiệm:** upload ảnh mẫu rồi mở các kích thước như 300x300, 25x25, 500x500; nếu lỗi thì xem CloudWatch Logs.

### Lưu ý khi đưa vào chạy thật
- Bản trong bài là ví dụ đơn giản. Khi chạy thật nên có **danh sách kích thước được phép**, nếu không người lạ có thể yêu cầu vô số kích thước và làm đầy bucket.
- Nên để ảnh đã resize trong bucket riêng để áp lifecycle rule.
- Bản cập nhật năm 2018 khuyên dùng giải pháp **Serverless Image Handler** của AWS cho môi trường production.
- Lợi ích chung của mô hình serverless: không có máy chủ phải vá lỗi hay mở rộng, và không phải dự trù dư công suất.
