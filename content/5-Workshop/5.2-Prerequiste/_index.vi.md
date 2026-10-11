---
title: "Chuẩn bị"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## 1. Tài khoản AWS và IAM user

Bạn cần một tài khoản AWS và một **IAM user có quyền quản trị** để làm lab. Không dùng root user cho công việc hằng ngày.

## 2. Chọn region

Ở góc trên bên phải console, chọn **US East (N. Virginia) us-east-1** và giữ nguyên trong suốt workshop.

{{% notice note %}}
Region là một cụm trung tâm dữ liệu của AWS ở một khu vực. Tài nguyên tạo ở region này sẽ không hiện ở region khác. Nếu "tạo rồi mà không thấy đâu", hãy kiểm tra region trước tiên.
{{% /notice %}}

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> ô chọn region đang là us-east-1 → <code>static/images/5-Workshop/5.2/01-region.png</code></p>
<!-- ![ô chọn region đang là us-east-1](/images/5-Workshop/5.2/01-region.png?width=90pc) -->

## 3. Tạo cảnh báo chi phí

1. Mở **Billing and Cost Management → Budgets → Create budget**.
2. Chọn **Use a template** → **Monthly cost budget**.
3. Budget amount: **5** USD. Nhập email nhận cảnh báo.
4. Bấm **Create budget**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> budget đã tạo → <code>static/images/5-Workshop/5.2/02-budget.png</code></p>
<!-- ![budget đã tạo](/images/5-Workshop/5.2/02-budget.png?width=90pc) -->

## 4. Chuẩn bị tài liệu

Chuẩn bị 2–3 file PDF **có lớp chữ**, ví dụ sổ tay sinh viên và quy chế đào tạo của trường.

{{% notice warning %}}
Cách kiểm tra nhanh: mở file PDF và thử bôi đen một dòng chữ. Nếu không bôi đen được, đó là ảnh scan và Knowledge Base sẽ không đọc được.
{{% /notice %}}

## 5. Quyền dùng mô hình trên Bedrock

Các mô hình serverless trên Amazon Bedrock giờ đã **được bật sẵn**, không cần vào trang "Model access" nữa. Riêng mô hình của Anthropic (Claude) vẫn yêu cầu điền form mô tả mục đích sử dụng một lần, nhưng workshop này dùng **Amazon Nova Lite** nên bạn bỏ qua bước đó.
