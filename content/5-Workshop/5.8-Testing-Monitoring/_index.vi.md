---
title: "Kiểm thử và giám sát"
date: 2026-09-07
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Kiểm thử chất lượng câu trả lời

1. Soạn 20 câu hỏi mà bạn biết chắc đáp án nằm trong tài liệu.
2. Hỏi lần lượt trên trang web và ghi kết quả vào bảng như sau:

| # | Câu hỏi | Đúng? | Nguồn trích dẫn |
| --- | --- | --- | --- |
| 1 | Điều kiện xét học bổng là gì? | ✅ | quy-che-dao-tao.pdf |
| 2 | ... | | |

3. Hỏi thêm 2–3 câu nằm ngoài tài liệu (ví dụ *"Thời tiết hôm nay thế nào?"*) để kiểm tra hệ thống không bịa câu trả lời.

Kết quả: <span style="background:#fff3c4">[x/20 câu đúng]</span>

## Giám sát bằng CloudWatch

1. Mở **CloudWatch** → **Log groups** → `/aws/lambda/studymate-ask`.
2. Mở log stream mới nhất. Mỗi request ghi lại **Duration** (thời gian chạy) và lỗi nếu có.
3. Trong console Lambda, tab **Monitor** có biểu đồ số lần gọi, thời gian chạy và số lỗi.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> biểu đồ ở tab Monitor của Lambda → <code>static/images/5-Workshop/5.8/01-monitor.png</code></p>
<!-- ![biểu đồ ở tab Monitor của Lambda](/images/5-Workshop/5.8/01-monitor.png?width=90pc) -->

## Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách sửa |
| --- | --- | --- |
| `AccessDeniedException` | Role của Lambda thiếu quyền | Kiểm tra `ACCOUNT_ID`, `KB_ID` và region trong policy (5.5.2) |
| `Task timed out after 3.00 seconds` | Timeout mặc định | Tăng timeout lên 30 giây (5.5.1) |
| Lỗi CORS trong console trình duyệt | Chưa cấu hình CORS | Cấu hình CORS cho API (5.6.2) |
| Câu nào cũng trả lời "Sorry, I am unable to assist..." | Chưa Sync dữ liệu hoặc PDF là ảnh scan | Chạy Sync (5.4.2); dùng PDF có lớp chữ |
| `{"message":"Not Found"}` | Sai URL | Kiểm tra URL có kết thúc bằng `/ask` |
