---
title: "Tạo Knowledge Base"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Bước 1: Mở trình tạo

1. Mở console **Amazon Bedrock**.
2. Ở menu bên trái, chọn **Knowledge Bases** → **Create** → **Knowledge Base with vector store**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> menu Create với lựa chọn Knowledge Base with vector store → <code>static/images/5-Workshop/5.4/01-create-menu.png</code></p>
<!-- ![menu Create với lựa chọn Knowledge Base with vector store](/images/5-Workshop/5.4/01-create-menu.png?width=90pc) -->

## Bước 2: Thông tin Knowledge Base

1. **Knowledge Base name:** `studymate-kb`.
2. **IAM permissions:** Create and use a new service role. Bedrock sẽ tự tạo một role được phép đọc bucket của bạn và gọi mô hình embedding.
3. **Data source:** Amazon S3.
4. Bấm **Next**.

## Bước 3: Cấu hình nguồn dữ liệu

1. **S3 URI:** bấm **Browse S3** và chọn `studymate-docs-<ten-ban>`.
2. **Parsing strategy** và **Chunking strategy:** giữ **Default**.
3. Bấm **Next**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> data source đã chọn S3 URI → <code>static/images/5-Workshop/5.4/02-data-source.png</code></p>
<!-- ![data source đã chọn S3 URI](/images/5-Workshop/5.4/02-data-source.png?width=90pc) -->

## Bước 4: Mô hình embedding và kho vector

1. **Embeddings model:** **Titan Text Embeddings V2**.
2. **Vector store:** **Quick create a new vector store** → **Amazon S3 Vectors**.
3. Bấm **Next**, xem lại, rồi **Create Knowledge Base**. Quá trình tạo mất vài phút.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> chọn mô hình embedding và S3 Vectors → <code>static/images/5-Workshop/5.4/03-vector-store.png</code></p>
<!-- ![chọn mô hình embedding và S3 Vectors](/images/5-Workshop/5.4/03-vector-store.png?width=90pc) -->

{{% notice warning %}}
**Không** chọn Amazon OpenSearch Serverless. Dịch vụ này tính phí tối thiểu theo giờ kể cả khi không dùng, có thể lên tới hàng trăm USD mỗi tháng.
{{% /notice %}}

## Bước 5: Ghi lại Knowledge Base ID

Trên trang Knowledge Base, sao chép **Knowledge Base ID** (10 ký tự, ví dụ `ABCDEFGHIJ`). Bạn sẽ cần nó ở mục 5.5.

{{% notice note %}}
Tên nút trên console có thể thay đổi đôi chút theo thời gian. Nếu không thấy đúng tên, hãy tìm lựa chọn có ý nghĩa tương đương.
{{% /notice %}}
