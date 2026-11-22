---
title: "Dọn dẹp tài nguyên"
date: 2026-09-07
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

## Nếu bạn dựng bằng tay (5.3–5.7)
1. **API Gateway:** xoá `snapthumb-api`.
2. **Lambda:** xoá `snapthumb-api` và `snapthumb-thumbnail`; **Layers:** xoá `pillow-py312`.
3. **CloudWatch → Log groups:** xoá `/aws/lambda/snapthumb-api` và `/aws/lambda/snapthumb-thumbnail`.
4. **DynamoDB:** xoá `snapthumb-images`.
5. **S3:** **Empty** rồi **Delete** `snapthumb-images-…` và `snapthumb-web-…`.
6. **IAM → Roles:** xoá các role bắt đầu bằng `snapthumb-api-role` và `snapthumb-thumbnail-role`.
7. **CloudWatch → Alarms:** xoá ba alarm `snapthumb-…`; **Amazon SNS:** xoá topic `snapthumb-alarms` và subscription của nó. (Metric filter bị xoá cùng log group.)

## Nếu bạn dùng CloudFormation (5.9)

```bash
cd ~/snapthumb && bash destroy.sh
```

Gõ `yes`; script làm trống hai bucket rồi xoá stack. Xoá stack cũng xoá luôn ba alarm, SNS topic và subscription, metric filter, hai hàm Lambda, API, bảng, các IAM role và log group.

Nếu bạn có tạo layer Pillow ở mục 5.5.1, hãy xoá thêm: **Lambda → Layers → `pillow-py312` → Delete** cho từng phiên bản. Layer không nằm trong stack.

## Chi phí nếu để hệ thống chạy
Nếu cần để SnapThumb chạy một thời gian (ví dụ đến khi chấm xong báo cáo), chi phí gần như bằng 0 (bảng ước tính chi tiết theo lượng sử dụng ở [Proposal, mục 6](../../2-proposal/)):

| Thành phần | Chi phí khi không có người dùng | Ghi chú |
| --- | --- | --- |
| Lambda, API Gateway, DynamoDB on-demand | 0 USD | Chỉ tính tiền theo request |
| S3 | Khoảng 0,025 USD mỗi GB mỗi tháng | Vài ảnh test chỉ vài MB |
| CloudWatch | 0 USD | 3 alarm và 1 custom metric nằm trong Free Tier (10 alarm, 10 custom metric); log giữ 14 ngày |
| SNS email | 0 USD | Chỉ gửi email khi alarm đổi trạng thái |

Hãy xoá toàn bộ ngay khi không còn cần dùng.

## Kiểm tra hoá đơn
Một ngày sau, mở **Billing and Cost Management → Bills** để chắc chắn không còn chi phí phát sinh.
