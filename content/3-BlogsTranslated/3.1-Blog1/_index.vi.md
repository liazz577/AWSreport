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

## Tóm tắt

Bài viết hướng dẫn cách cho trình duyệt hoặc ứng dụng di động upload file thẳng lên Amazon S3 thay vì đi qua máy chủ ứng dụng. Client gọi một endpoint API Gateway, Lambda tạo presigned URL có thời hạn 300 giây với tên file ngẫu nhiên, rồi client gửi file thẳng tới S3. Cách này giảm tải cho máy chủ và lưu lượng mạng; bài cũng lưu ý cần cấu hình CORS cho bucket và nên thêm xác thực (JWT authorizer) để giới hạn ai được upload.

## Liên hệ với dự án SnapThumb

SnapThumb dùng đúng mô hình này ở bước 2–5. Điểm khác: SnapThumb dùng presigned **POST** thay vì presigned PUT, vì POST cho phép đặt điều kiện `content-length-range` để S3 tự từ chối file lớn hơn 10 MB, điều mà bài gốc ghi nhận là chưa xử lý.

## Nội dung chính

### Vấn đề khi upload qua máy chủ
Cách làm truyền thống là người dùng gửi file lên máy chủ ứng dụng, máy chủ lưu tạm rồi mới chuyển sang nơi lưu trữ. Mỗi file đi qua máy chủ hai lần, tốn băng thông và CPU. Với ứng dụng có lưu lượng tăng vọt theo mùa (ví dụ ứng dụng gửi thiệp dịp lễ), máy chủ dễ quá tải đúng lúc đông người dùng nhất.

### Ý tưởng: tách việc xin quyền và việc gửi file
Client làm hai bước:

1. Gọi một endpoint API Gateway. Endpoint này kích hoạt hàm Lambda `getSignedURL`, hàm xin S3 một **presigned URL** rồi trả về cho client.
2. Client gửi file thẳng lên S3 bằng URL đó. Backend không bao giờ phải xử lý nội dung file.

S3 có độ sẵn sàng và độ bền cao, nên là nơi phù hợp để lưu file người dùng tải lên.

### Hàm Lambda tạo link
- Tên object được sinh ngẫu nhiên (một số ngẫu nhiên kèm đuôi `.jpg`), nên người dùng không ghi đè được file của nhau.
- Kiểu nội dung được cố định là `image/jpeg`, và link hết hạn sau **300 giây**. Mặc định của SDK là 15 phút; tác giả khuyên chọn thời hạn ngắn nhất đủ dùng.
- Hàm trả về JSON gồm link upload và tên object. Role của hàm chỉ cần quyền ghi object vào bucket (policy mẫu `S3WritePolicy` của SAM).

### Triển khai và thử nghiệm
- Mã mẫu nằm trong repo `aws-samples/amazon-s3-presigned-urls-aws-sam`, triển khai bằng `sam deploy --guided`. Output `APIendpoint` cộng thêm `/uploads` là địa chỉ xin link.
- Thử bằng Postman: gửi GET để lấy `uploadURL`, rồi gửi PUT với nội dung là file ảnh; S3 trả `200 OK` và file xuất hiện trong bucket.
- Thử bằng trang web mẫu: trang dùng `fetch` gửi PUT file lên link vừa nhận.

### CORS
Vì trình duyệt gửi request sang một domain khác (S3), bucket phải có quy tắc CORS. Bản mẫu cho phép mọi origin và header với các phương thức GET, PUT, HEAD; khi chạy thật nên giới hạn lại theo domain của ứng dụng.

### Bảo mật
- Bản mẫu thứ hai (`templateWithAuth.yaml`) gắn **JWT authorizer** vào HTTP API. Client phải gửi token trong header `Authorization`, API Gateway kiểm tra token trước khi gọi Lambda. Bài dùng Auth0 làm ví dụ, và nêu Amazon Cognito là lựa chọn tương đương.
- Mặc định object tải lên là riêng tư. Muốn ai cũng xem được thì đặt ACL `public-read` khi tạo link, và hàm cần thêm quyền `s3:PutObjectAcl`.

### Kết luận của bài
Upload thẳng lên S3 chuyển phần tải nặng nhất ra khỏi ứng dụng, giúp hệ thống chịu được lưu lượng đột biến mà không phải tăng máy chủ. Bài không bàn tới upload nhiều phần (multipart) hay giới hạn dung lượng file.
