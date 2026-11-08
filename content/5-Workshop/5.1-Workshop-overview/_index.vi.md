---
title: "Tổng quan workshop"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## SnapThumb hoạt động như thế nào

Hệ thống có hai hàm Lambda với hai nhiệm vụ khác nhau:

| Thành phần | Dịch vụ AWS | Nhiệm vụ |
| --- | --- | --- |
| Trang web | **Amazon S3** (website tĩnh) | Kéo thả ảnh để upload và hiển thị thư viện ảnh thu nhỏ |
| Cửa vào | **Amazon API Gateway** | Các endpoint HTTPS mà trang web gọi tới |
| Lambda API | **AWS Lambda** | Kiểm tra file, ký link upload và link xem có thời hạn, liệt kê và xoá ảnh |
| Kho ảnh | **Amazon S3** (bucket riêng tư) | Ảnh gốc ở `uploads/`, ảnh thu nhỏ ở `thumbnails/` |
| Lambda Thumbnail | **AWS Lambda** + Pillow | Tự chạy với mỗi ảnh mới và tạo ảnh thu nhỏ |
| Hồ sơ ảnh | **Amazon DynamoDB** | Trạng thái (`PENDING` → `DONE` / `FAILED`), kích thước, dung lượng, thời gian xử lý |
| Nhật ký | **Amazon CloudWatch** | Ghi lại mọi lần chạy của hai hàm |

## Các khái niệm chính

#### Kiến trúc hướng sự kiện (event-driven)
Không có gì phải liên tục kiểm tra bucket. Khi có file mới, S3 phát ra một **sự kiện** và gọi thẳng hàm Lambda. Hàm chỉ chạy khi có việc, và khi nhiều ảnh đến cùng lúc, AWS tự chạy nhiều bản song song.

#### S3 Event Notifications
Một thiết lập của bucket theo kiểu "khi có file mới trong thư mục này thì gọi đích này". SnapThumb chỉ lắng nghe `uploads/`. Ảnh thu nhỏ được ghi vào `thumbnails/`, nên việc ghi thumbnail không kích hoạt lại hàm. Nếu không tách như vậy, hàm sẽ tự kích hoạt chính nó thành vòng lặp vô hạn.

#### Presigned URL / presigned POST
Một đường link được ký bằng quyền của Lambda API, cho phép trình duyệt upload đúng một file thẳng lên S3 trong thời gian ngắn (5 phút). Trình duyệt không bao giờ có khoá AWS, và file lớn không phải đi qua Lambda. Presigned POST còn kèm điều kiện: S3 tự từ chối file sai loại hoặc lớn hơn 10 MB.

#### Lambda layer
Một gói thư viện dùng chung cho các hàm. Pillow (thư viện xử lý ảnh của Python) không có sẵn trong Lambda, nên bạn đóng gói nó một lần thành layer rồi gắn vào hàm Thumbnail.

#### DynamoDB on-demand
Bảng NoSQL tính tiền theo số lượt đọc/ghi, không cần tính trước dung lượng. Mỗi ảnh là một bản ghi với khoá `id`.

{{% notice tip %}}
Kiểm tra từng phần trước khi sang phần tiếp theo: upload một file vào bucket bằng tay và xem có thumbnail không (5.5) → gọi API bằng curl (5.6) → dùng trang web (5.7).
{{% /notice %}}
