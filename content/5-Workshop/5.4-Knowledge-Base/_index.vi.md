---
title: "Tạo Knowledge Base"
date: 2026-09-07
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Knowledge Base làm gì

Đây là phần cốt lõi của StudyMate, nhưng AWS làm gần hết. Khi bạn bấm **Sync**, Knowledge Base sẽ:

1. Đọc các file PDF trong bucket S3.
2. Chia tài liệu thành nhiều đoạn nhỏ (chunk).
3. Gọi **Titan Text Embeddings V2** để biến mỗi đoạn thành một vector.
4. Lưu các vector vào **Amazon S3 Vectors**.

Khi có câu hỏi, câu hỏi cũng được đổi thành vector, và Knowledge Base trả về những đoạn gần nhất.

#### Nội dung

1. [Tạo Knowledge Base](5.4.1-create-knowledge-base/)
2. [Đồng bộ và kiểm thử](5.4.2-sync-and-test/)
