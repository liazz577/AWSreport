---
title: "Kiểm thử và giám sát"
date: 2026-09-07
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Kiểm thử chức năng

| # | Kiểm thử | Kết quả mong đợi |
| --- | --- | --- |
| 1 | Upload ảnh chụp điện thoại (JPEG) | `DONE` sau vài giây, thumbnail cạnh dài 320 px |
| 2 | Upload ảnh chụp dọc | Thumbnail đúng chiều, cao hơn rộng |
| 3 | Upload ảnh PNG nền trong suốt | Thumbnail có nền trắng |
| 4 | Upload 5 ảnh cùng lúc | Cả năm ảnh đều xong; Lambda chạy song song |
| 5 | Upload file `.txt` đổi đuôi thành `.png` | Thẻ ảnh báo `FAILED` kèm lỗi |
| 6 | Chọn ảnh 12 MB | Trang từ chối ngay trước khi upload |
| 7 | Xoá một ảnh | Thẻ ảnh biến mất; cả hai file trên S3 bị xoá |

## Số liệu đo được
Đo trên AWS (ap-southeast-1, 18/11/2026). Sáu ảnh test được upload **cùng lúc** từ một client gọi API và presigned POST giống hệt trang web.

| Ảnh | Ảnh gốc | Thumbnail | Dung lượng | Giảm | Thời gian xử lý |
| --- | --- | --- | --- | --- | --- |
| JPEG ngang | 4032×3024 | 320×240 | 6,1 MB → 12,7 KB | 99,8% | 2.686 ms |
| JPEG dọc (EXIF xoay 90°) | 3000×4000 | 240×320, đúng chiều | 5,4 MB → 12,5 KB | 99,8% | 2.778 ms |
| PNG trong suốt | 1200×1200 | 320×320, nền trắng | 11,4 KB → 5,2 KB | 53,9% | 2.547 ms |
| WebP | 1920×1080 | 320×180 | 818 KB → 16,4 KB | 98,0% | 2.763 ms |
| JPEG nhỏ | 200×150 | 200×150 (không phóng to) | 21,0 KB → 14,9 KB | 29,1% | 1.076 ms |
| File text đổi đuôi `.png` | – | – | – | – | `FAILED`: *cannot identify image file* |

**Khởi động lạnh và khi đã chạy sẵn.** Thời gian trên gồm cả lúc S3 gửi sự kiện và *cold start*: lần chạy đầu của mỗi instance Lambda mất khoảng **0,4 giây** để nạp Python và Pillow (`Init Duration` trong log). Khi instance đã chạy sẵn, ảnh 6 MB chỉ mất **0,48 giây** thời gian chạy hàm. Bộ nhớ dùng tối đa **213 MB** trên 1024 MB; vẫn để 1024 MB vì Lambda cấp nhiều CPU hơn khi tăng bộ nhớ.

| Kiểm tra khác | Kết quả |
| --- | --- |
| API từ chối GIF / khai dung lượng 11 MB | `400` |
| Client khai 1 KB nhưng gửi 10,5 MB | S3 từ chối: `400 EntityTooLarge` |
| CORS preflight `OPTIONS /uploads` | `204`, `Access-Control-Allow-Origin: *` |
| Ảnh upload thẳng lên S3 (bước 5.5) rồi gọi `GET /images` | `200`, ảnh có trong danh sách; `DELETE` → `204`, xoá cả hai file |

{{% notice tip %}}
Hãy đo lại bảng này với ảnh chụp điện thoại của bạn: mỗi thẻ ảnh trên trang web đều hiện kích thước, mức giảm và thời gian xử lý.
{{% /notice %}}

## Giám sát
1. **Lambda → snapthumb-thumbnail → Monitor**: số lần chạy, thời gian chạy, số lỗi.
2. **CloudWatch → Log groups → /aws/lambda/snapthumb-thumbnail**: mỗi ảnh một dòng log ghi dung lượng trước và sau.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> tab Monitor của Lambda Thumbnail → <code>static/images/5-Workshop/5.8/01-monitor.png</code></p>
<!-- ![tab Monitor của Lambda Thumbnail](/images/5-Workshop/5.8/01-monitor.png?width=90pc) -->

## Lỗi thường gặp

| Hiện tượng | Nguyên nhân | Cách sửa |
| --- | --- | --- |
| Log báo `No module named 'PIL'` | Chưa gắn layer, hoặc layer đóng gói sai nền tảng | Gắn `pillow-py312` (5.5.2); đóng gói lại đúng lệnh ở 5.5.1 |
| Ảnh đứng mãi ở "Đang xử lý" | Thiếu trigger S3 hoặc sai prefix | Kiểm tra trigger có prefix `uploads/` |
| Log báo `AccessDenied` | Inline policy sai tên bucket hoặc account ID | Sửa `YOURNAME` / `ACCOUNT_ID` |
| Lỗi CORS khi upload | Bucket chưa cấu hình CORS | Thêm CORS cho bucket (5.3) |
| Lỗi CORS khi gọi `/images` | API chưa cấu hình CORS | Cấu hình CORS cho API (5.6.2) |
| S3 trả `400 EntityTooLarge` khi upload | File lớn hơn 10 MB (presigned POST chỉ cho tối đa 10 MB) | Chọn file nhỏ hơn |
| S3 trả `403` khi upload | Link upload đã hết hạn (5 phút) | Upload lại: trang sẽ xin link mới |
| Thẻ ảnh đứng ở "Đang xử lý" sau khi upload lỗi | Trang bản cũ để lại bản ghi `PENDING` | Dùng `index.html` mới: trang xoá bản ghi khi upload lỗi và ngừng hỏi lại sau 2 phút |

## Hạn chế và hướng phát triển

SnapThumb là bản demo. Dưới đây là các hạn chế đã biết và dịch vụ AWS có thể dùng để khắc phục:

| Hạn chế | Rủi ro | Hướng phát triển |
| --- | --- | --- |
| API chưa có đăng nhập | Ai biết địa chỉ API cũng xem được và **xoá được mọi ảnh** | Thêm **Amazon Cognito** user pool và JWT authorizer cho HTTP API; lưu `userId` cùng mỗi ảnh |
| Trang web là S3 website endpoint public (chỉ HTTP) | Không có HTTPS; tài khoản phải cho phép bucket policy public | Bucket private đặt sau **Amazon CloudFront** với Origin Access Control, HTTPS và **AWS WAF** |
| `GET /images` quét toàn bộ bảng | Chậm và tốn hơn khi số ảnh tăng | Thêm GSI theo `createdAt` (hoặc theo người dùng) và dùng `Query` có phân trang |
| Giám sát còn thủ công (tab Monitor, log) | Phát hiện lỗi muộn | **CloudWatch alarm** cho `Errors` của Lambda và lỗi `5xx` của API, gửi email qua **Amazon SNS** |
| Thumbnail lỗi không được thử lại | Lỗi tạm thời khiến ảnh bị `FAILED` | Cấu hình on-failure destination / **SQS dead-letter queue** và thử lại |
