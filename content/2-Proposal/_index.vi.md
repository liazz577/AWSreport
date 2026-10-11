---
title: "Bản đề xuất"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# StudyMate
## Chatbot hỏi đáp Sổ tay sinh viên trên AWS

# 1. Tóm tắt

StudyMate là một trang web chat giúp sinh viên tra cứu quy chế đào tạo, sổ tay sinh viên và các quy định học vụ bằng tiếng Việt. Hệ thống dùng kỹ thuật **RAG (Retrieval-Augmented Generation)**: tìm đoạn tài liệu liên quan trước, sau đó để mô hình ngôn ngữ viết câu trả lời ngắn gọn kèm tên file nguồn. Toàn bộ hệ thống chạy **serverless** trên AWS nên không phải quản lý máy chủ, chi phí cho bản demo ước tính dưới 1 USD/tháng.

# 2. Vấn đề

## Vấn đề hiện tại

- Quy chế, sổ tay sinh viên dài hàng chục trang, nằm rải rác trong nhiều file PDF.
- Sinh viên khó tìm đúng thông tin (điều kiện học bổng, cách tính điểm rèn luyện, thủ tục bảo lưu...).
- Các câu hỏi lặp lại dồn về phòng đào tạo và cố vấn học tập.

## Giải pháp

- Đưa toàn bộ tài liệu vào một **Amazon Bedrock Knowledge Base**.
- Sinh viên đặt câu hỏi bằng ngôn ngữ tự nhiên trên trang web.
- Hệ thống trả lời dựa **chỉ trên tài liệu của trường** và luôn hiển thị nguồn để sinh viên kiểm tra lại.

## Lợi ích

- Sinh viên tra cứu nhanh, mọi lúc.
- Giảm câu hỏi lặp lại cho phòng đào tạo.
- Dễ cập nhật: chỉ cần tải file mới lên S3 và bấm Sync.
- Chi phí rất thấp nhờ kiến trúc serverless và S3 Vectors.

# 3. Kiến trúc giải pháp

## Kiến trúc giải pháp

![Kiến trúc StudyMate](/images/5-Workshop/architecture.svg?width=100pc)

**Luồng hỏi đáp:** Sinh viên mở trang web (Amazon S3) → gửi câu hỏi tới Amazon API Gateway → AWS Lambda gọi Amazon Bedrock Knowledge Base → Knowledge Base tìm đoạn tài liệu liên quan trong Amazon S3 Vectors → mô hình Amazon Nova Lite viết câu trả lời → kết quả trả về trình duyệt.

**Luồng nạp tài liệu:** File PDF trong Amazon S3 → Knowledge Base chia nhỏ tài liệu → Titan Text Embeddings V2 chuyển mỗi đoạn thành vector → lưu vào Amazon S3 Vectors.

## Các dịch vụ AWS sử dụng

| Dịch vụ | Vai trò |
| --- | --- |
| Amazon S3 | Lưu file PDF nguồn; host giao diện web tĩnh |
| Amazon Bedrock Knowledge Bases | Chia nhỏ tài liệu, tạo embedding, truy xuất và sinh câu trả lời |
| Amazon S3 Vectors | Kho vector chi phí thấp cho Knowledge Base |
| Amazon Titan Text Embeddings V2 | Chuyển văn bản thành vector |
| Amazon Nova Lite | Mô hình ngôn ngữ viết câu trả lời |
| AWS Lambda | Nhận câu hỏi, gọi Bedrock, trả kết quả JSON |
| Amazon API Gateway | Cung cấp endpoint HTTPS cho trang web |
| Amazon CloudWatch | Lưu log, theo dõi lỗi và thời gian chạy |
| AWS IAM | Phân quyền tối thiểu cho từng dịch vụ |
| AWS Budgets | Cảnh báo chi phí |

## Thiết kế thành phần

### Frontend
- Một file `index.html` thuần (HTML, CSS, JavaScript), không dùng framework.
- Host bằng S3 static website hosting.

### Backend
- Hàm Lambda Python 3.12 khoảng 40 dòng code.
- Gọi API `retrieve_and_generate` của Bedrock: một lệnh vừa tìm tài liệu vừa sinh câu trả lời.
- Kiểm tra đầu vào (câu hỏi 1–500 ký tự).

### Dữ liệu
- Tài liệu nguồn: 2–3 file PDF có lớp chữ.
- Vector lưu trong S3 Vectors do Knowledge Base tự tạo và quản lý.

### Bảo mật
- Bucket tài liệu để private; chỉ bucket web được đọc công khai.
- Lambda chỉ được phép gọi đúng Knowledge Base và mô hình cần dùng.
- API Gateway giới hạn tần suất gọi (throttling).

# 4. Triển khai kỹ thuật

## Các giai đoạn triển khai

1. Chuẩn bị tài khoản, region, cảnh báo chi phí.
2. Đưa tài liệu lên S3, tạo và kiểm thử Knowledge Base.
3. Viết hàm Lambda, cấu hình IAM.
4. Tạo API Gateway, cấu hình CORS.
5. Viết và host giao diện web.
6. Kiểm thử, giám sát, dọn dẹp.

## Yêu cầu kỹ thuật

### Ngôn ngữ lập trình
- Python 3.12 (Lambda)
- HTML, CSS, JavaScript (giao diện)

### Thư viện
- boto3 (có sẵn trong môi trường Lambda)

### Công cụ
- AWS Management Console, AWS CLI, Visual Studio Code, Git, Hugo

# 5. Lộ trình & Các mốc

| Tuần | Công việc |
| --- | --- |
| 1–5 | Học nền tảng AWS: IAM, VPC, EC2, S3, RDS, DynamoDB, Lambda, API Gateway |
| 6 | Tìm hiểu Bedrock và RAG, viết bản đề xuất |
| 7 | Dựng Knowledge Base |
| 8 | Viết Lambda và cấu hình IAM |
| 9 | API Gateway và giao diện web |
| 10 | Bảo mật, giám sát, đánh giá chất lượng |
| 11 | Viết workshop, đưa báo cáo lên GitHub Pages |
| 12 | Demo, dọn dẹp, tổng kết |

# 6. Ước tính chi phí

## Ước tính chi phí hạ tầng

Giả định: region us-east-1; 3 file PDF (khoảng 50.000 token); 1.000 câu hỏi/tháng, mỗi câu khoảng 3.000 token đầu vào và 300 token đầu ra.

| Hạng mục | Chi phí ước tính (USD/tháng) |
| --- | --- |
| Amazon S3 (tài liệu + web, dưới 1 GB) | ~0.03 |
| Amazon S3 Vectors (lưu trữ + truy vấn) | < 0.01 |
| Titan Text Embeddings V2 | < 0.01 |
| Amazon Nova Lite (3 triệu token vào, 0,3 triệu token ra) | ~0.25 |
| AWS Lambda, API Gateway, CloudWatch (trong Free Tier) | ~0.00 |
| **Tổng** | **< 1.00** |

{{% notice warning %}}
Giá trên là ước tính theo giá niêm yết và có thể thay đổi. <span style="background:#fff3c4">[Chèn link AWS Pricing Calculator của bạn tại đây]</span>
{{% /notice %}}

### Hướng dẫn kiểm soát chi phí

- Tạo AWS Budgets 5 USD ngay từ đầu.
- **Không** chọn Amazon OpenSearch Serverless làm vector store: dịch vụ này tính phí tối thiểu theo giờ kể cả khi không dùng, có thể lên tới hàng trăm USD/tháng.
- Xóa toàn bộ tài nguyên sau khi demo (mục 5.9).

# 7. Đánh giá rủi ro

## Ma trận rủi ro

| Rủi ro | Khả năng | Ảnh hưởng | Cách xử lý |
| --- | --- | --- | --- |
| Mô hình trả lời sai hoặc bịa | Trung bình | Cao | Chỉ trả lời từ tài liệu, luôn hiển thị nguồn |
| Phát sinh chi phí ngoài ý muốn | Thấp | Trung bình | AWS Budgets, dọn tài nguyên sau demo |
| API bị gọi tràn lan | Thấp | Trung bình | Throttling trên API Gateway, giới hạn độ dài câu hỏi |
| PDF dạng ảnh scan không đọc được | Trung bình | Trung bình | Dùng PDF có lớp chữ hoặc bật advanced parsing |

# 8. Kết quả mong đợi

## Kết quả kỹ thuật

- Trang web chat chạy được, trả lời trong vài giây, có trích dẫn nguồn.
- Trả lời đúng ít nhất 16/20 câu trong bộ câu hỏi kiểm thử.
- Workshop hướng dẫn dựng lại được toàn bộ hệ thống từ đầu.

## Giá trị thực tế

- Có thể mở rộng thành trợ lý học vụ cho toàn trường.
- Là nền tảng cho phần trợ lý AI trong các dự án quản lý sinh viên.
