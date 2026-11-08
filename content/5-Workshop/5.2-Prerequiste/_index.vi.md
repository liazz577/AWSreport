---
title: "Chuẩn bị"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## 1. Tài khoản AWS và IAM user
Một tài khoản AWS và một **IAM user có quyền quản trị** để làm lab. Không dùng root user cho công việc hằng ngày.

## 2. Chọn region
Ở góc trên bên phải console, chọn **Asia Pacific (Singapore) ap-southeast-1** và giữ nguyên trong suốt workshop. Đây là region gần Việt Nam nhất nên upload ảnh nhanh.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> ô chọn region đang là ap-southeast-1 → <code>static/images/5-Workshop/5.2/01-region.png</code></p>
<!-- ![ô chọn region đang là ap-southeast-1](/images/5-Workshop/5.2/01-region.png?width=90pc) -->

## 3. Tạo cảnh báo chi phí
1. **Billing and Cost Management → Budgets → Create budget**.
2. **Use a template** → **Monthly cost budget**, nhập **5** USD và email → **Create budget**.

## 4. Ghi lại Account ID
Bấm vào tên user ở góc trên bên phải và sao chép **Account ID** 12 chữ số. Các IAM policy trong workshop sẽ dùng số này.

## 5. Chuẩn bị ảnh thử
Vài ảnh chụp điện thoại (JPEG) và một ảnh PNG nền trong suốt, mỗi ảnh dưới 10 MB.
