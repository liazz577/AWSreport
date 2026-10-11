---
title: "Tổng quan workshop"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## StudyMate hoạt động như thế nào

Hãy hình dung StudyMate như **một quầy hỏi đáp ở thư viện trường**. Mỗi dịch vụ AWS đóng một vai:

| Vai trong thư viện | Dịch vụ AWS | Nhiệm vụ |
| --- | --- | --- |
| Quầy để sinh viên hỏi | **Amazon S3** (website tĩnh) | Phục vụ trang web chat |
| Cửa nhận phiếu hỏi | **Amazon API Gateway** | Cho trang web một địa chỉ HTTPS để gửi câu hỏi |
| Nhân viên trực quầy | **AWS Lambda** | Nhận câu hỏi, đi hỏi thủ thư, mang câu trả lời ra |
| Thủ thư | **Amazon Bedrock Knowledge Base** | Tìm đúng trang tài liệu có thông tin |
| Người viết | **Amazon Nova Lite** | Đọc các trang đó và viết câu trả lời ngắn gọn |
| Kệ sách | **Amazon S3** (bucket riêng tư) | Lưu các file PDF gốc |
| Tủ mục lục | **Amazon S3 Vectors** | Lưu "ý nghĩa" của từng đoạn tài liệu để tìm nhanh |

## Các khái niệm chính

#### RAG (Retrieval-Augmented Generation)
Hỏi thẳng mô hình AI về quy chế của trường bạn sẽ không hiệu quả: nó chưa từng đọc quy chế đó và có thể bịa ra câu trả lời. RAG thêm một bước: **tìm (retrieve)** đoạn tài liệu liên quan trước, rồi để mô hình **viết (generate)** câu trả lời chỉ dựa trên những đoạn đó.

#### Chunking (chia nhỏ tài liệu)
Knowledge Base cắt mỗi file PDF thành nhiều đoạn nhỏ (chunk), mỗi đoạn vài đoạn văn, để chỉ trả về phần liên quan thay vì cả file 50 trang.

#### Embedding
Mô hình embedding (ở đây là **Titan Text Embeddings V2**) biến mỗi đoạn thành một dãy số (vector). Những đoạn có ý nghĩa giống nhau sẽ có dãy số gần nhau, kể cả khi dùng từ khác nhau. Ví dụ "học bổng" và "hỗ trợ tài chính cho sinh viên giỏi" sẽ nằm gần nhau.

#### Vector store (kho vector)
Cơ sở dữ liệu chuyên lưu vector và tìm các vector gần nhau thật nhanh. Khi có câu hỏi, câu hỏi cũng được đổi thành vector, rồi kho vector trả về những đoạn gần nhất. Workshop dùng **Amazon S3 Vectors** vì chi phí gần như bằng 0 với lượng dữ liệu nhỏ.

#### IAM role
IAM user cấp quyền cho một người; **IAM role** cấp quyền cho một dịch vụ. Hàm Lambda được gắn một role chỉ cho phép gọi đúng Knowledge Base và mô hình nó cần.

## Bạn sẽ xây dựng những gì

1. Một bucket S3 riêng tư chứa các file PDF.
2. Một Bedrock Knowledge Base lập chỉ mục PDF vào S3 Vectors.
3. Một hàm Lambda gọi Knowledge Base.
4. Một HTTP API trên API Gateway để gọi hàm Lambda.
5. Một bucket S3 công khai host trang web chat.

{{% notice tip %}}
Làm theo thứ tự và **kiểm tra từng phần trước khi sang phần tiếp theo**: thử Knowledge Base trên console → test event của Lambda → gọi API bằng curl → trang web. Khi có lỗi, bạn sẽ biết ngay lỗi nằm ở bước nào.
{{% /notice %}}
