---
title: "Tạo API bằng API Gateway"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Vì sao cần API Gateway?

Hàm Lambda không tự có địa chỉ web. **Amazon API Gateway** cấp cho nó một đường link HTTPS công khai để trang web gửi câu hỏi tới. `POST /ask` nghĩa là "gửi dữ liệu tới đường dẫn `/ask`".

#### Nội dung

1. [Tạo HTTP API](5.6.1-create-http-api/)
2. [Cấu hình CORS và kiểm thử](5.6.2-configure-cors/)
