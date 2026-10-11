---
title: "Đồng bộ và kiểm thử"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Đồng bộ nguồn dữ liệu

Tạo xong Knowledge Base chưa có nghĩa là tài liệu đã được đọc. Bạn phải chạy **Sync**.

1. Trên trang `studymate-kb`, kéo xuống mục **Data source**.
2. Chọn data source → **Sync**.
3. Chờ đến khi trạng thái sync báo đã hoàn tất.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> data source đã sync xong → <code>static/images/5-Workshop/5.4/04-sync.png</code></p>
<!-- ![data source đã sync xong](/images/5-Workshop/5.4/04-sync.png?width=90pc) -->

{{% notice tip %}}
Mỗi khi thêm hoặc sửa file PDF trong bucket, chỉ cần bấm **Sync** lại là StudyMate được cập nhật kiến thức mới.
{{% /notice %}}

## Kiểm thử trên console

1. Bấm **Test Knowledge Base** ở bên phải trang.
2. Bấm **Select model** → **Amazon** → **Nova Lite** → **Apply**.
3. Hỏi một câu có đáp án trong tài liệu, ví dụ *"Điều kiện để được xét học bổng là gì?"*
4. Câu trả lời phải có số trích dẫn; bấm vào để xem đoạn tài liệu gốc.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> khung test có câu trả lời và trích dẫn → <code>static/images/5-Workshop/5.4/05-test.png</code></p>
<!-- ![khung test có câu trả lời và trích dẫn](/images/5-Workshop/5.4/05-test.png?width=90pc) -->

{{% notice note %}}
**Nếu bước này chạy được thì phần khó nhất đã xong.** Các mục sau chỉ là đưa chức năng này ra một trang web.
{{% /notice %}}
