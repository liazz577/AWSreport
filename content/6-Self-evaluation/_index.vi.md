---
title: "Tự đánh giá"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Trong quá trình tham gia chương trình **First Cloud AI Journey (FCAJ)**, tôi bắt đầu với rất ít kiến thức về AWS: chỉ mới tạo tài khoản, tạo database và IAM user. Sau 12 tuần, tôi đã tự xây dựng được một ứng dụng serverless dùng Amazon S3, AWS Lambda, Amazon DynamoDB, Amazon API Gateway và Amazon CloudWatch, đồng thời viết lại thành workshop để người khác làm theo.

Bên cạnh kiến thức kỹ thuật, tôi rèn luyện được thói quen đọc tài liệu AWS bằng tiếng Anh, đọc log để tìm lỗi, và luôn dọn dẹp tài nguyên để kiểm soát chi phí.

{{% notice info %}}
Bảng dùng đúng 8 tiêu chí trong quy định project của FCAJ. Mức đánh giá và nhận xét là bản nháp dựa trên quá trình làm SnapThumb; hãy tự chọn lại cho đúng với bản thân bạn.
{{% /notice %}}

## Tự đánh giá

| STT | Tiêu chí | Tốt | Khá | Trung bình | Nhận xét |
| --- | --- | :---: | :---: | :---: | --- |
| 1 | **Kiến thức** |  | ✅ |  | Tự xây dựng và deploy ứng dụng serverless với 10 dịch vụ AWS; cần học sâu hơn về mạng và bảo mật |
| 2 | **Khả năng học hỏi** | ✅ |  |  | Từ chỗ mới tạo tài khoản đến CloudFormation và kiến trúc hướng sự kiện trong 12 tuần, chủ yếu tự đọc tài liệu AWS |
| 3 | **Tính chủ động** | ✅ |  |  | Tự đổi đề tài khi bị chặn quota Bedrock; chủ động test trên AWS thật và phát hiện lỗi mà test giả lập bỏ sót |
| 4 | **Kỷ luật** |  | ✅ |  | Viết worklog hằng tuần và luôn dọn tài nguyên; đôi lúc viết worklog muộn |
| 5 | **Giao tiếp** |  |  | ✅ | Giải thích được kiến trúc nhưng cần trình bày ngắn gọn hơn và hỏi mentor sớm hơn |
| 6 | **Làm việc nhóm** |  | ✅ |  | Chia sẻ cách sửa lỗi và mẹo AWS với các bạn thực tập; bản thân project chủ yếu làm cá nhân |
| 7 | **Giải quyết vấn đề** |  | ✅ |  | Tự xử lý vòng lặp S3–Lambda, phụ thuộc vòng trong CloudFormation và đóng gói Pillow nhờ đọc log và tài liệu |
| 8 | **Đóng góp cho project** | ✅ |  |  | Tự thiết kế, xây dựng, kiểm thử và viết tài liệu SnapThumb từ đầu đến cuối (code, template, workshop song ngữ) |
| 9 | **Tổng thể** |  | ✅ |  | Tiến bộ rõ về kỹ năng thực hành AWS; giao tiếp là điểm cần cải thiện nhất |

---

# Điểm mạnh

- Tiếp thu nhanh các dịch vụ mới, từ nền tảng gần như bằng 0 đến dựng được ứng dụng hoàn chỉnh.
- Tự gỡ được các lỗi IAM, timeout và CORS bằng cách đọc log CloudWatch.
- <span style="background:#fff3c4">[Thêm điểm mạnh của riêng bạn, kèm ví dụ cụ thể]</span>

# Điểm cần cải thiện

- Chủ động hỏi mentor sớm hơn thay vì tự loay hoay quá lâu với một lỗi.
- Học sâu hơn về Infrastructure as Code (AWS CDK hoặc Terraform) sau khi đã dùng AWS CloudFormation cho SnapThumb.
- Bổ sung đăng nhập (Amazon Cognito) và CDN (Amazon CloudFront) để ứng dụng sẵn sàng chạy thật.
- Nâng cao kỹ năng thuyết trình kỹ thuật.

---

# Kết luận

Chương trình FCAJ giúp tôi đi từ người mới bắt đầu đến chỗ hiểu cách các dịch vụ AWS kết nối với nhau để tạo thành một ứng dụng thực tế. Đây là nền tảng để tôi tiếp tục theo đuổi lĩnh vực Cloud và AI.
