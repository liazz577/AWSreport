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
Ghi lại 5 ảnh thật vào bảng: <span style="background:#fff3c4">[điền kết quả của bạn]</span>

| Ảnh | Ảnh gốc | Thumbnail | Giảm | Thời gian xử lý |
| --- | --- | --- | --- | --- |
| 1 | | | | |

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
| S3 trả 403 khi upload | File lớn hơn 10 MB hoặc link đã hết hạn (5 phút) | Thử lại với file nhỏ hơn |
