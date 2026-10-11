# Báo cáo thực tập AWS FCAJ – StudyMate

Source Hugo của báo cáo thực tập song ngữ (English / Tiếng Việt), dựng từ [AWS-First-Cloud-Journey/Workshop-template](https://github.com/AWS-First-Cloud-Journey/Workshop-template).

## Cấu trúc nội dung

```
content/
├── _index.md / _index.vi.md          Trang chủ: thông tin sinh viên
├── 1-Worklog/1.1-Week1 … 1.12-Week12 Nhật ký 12 tuần
├── 2-Proposal/                       Bản đề xuất
├── 3-BlogsTranslated/3.1 … 3.3       Các bài blog đã dịch
├── 4-EventParticipated/4.1, 4.2      Sự kiện đã tham gia
├── 5-Workshop/5.1 … 5.9              Workshop StudyMate (phần chính)
├── 6-Self-evaluation/                Tự đánh giá
└── 7-Feedback/                       Chia sẻ, góp ý
```

Mỗi thư mục có 2 file: `_index.md` (tiếng Anh) và `_index.vi.md` (tiếng Việt).

## Những việc bạn cần tự làm

1. Tìm các chỗ **tô vàng** (`background:#fff3c4`) và thay bằng thông tin thật: số điện thoại, email, lớp, ngày thực tập, sự kiện, bản dịch blog, kết quả kiểm thử, phần góp ý.
2. Sửa ngày trong worklog cho đúng lịch thực tập của bạn.
3. Chụp ảnh màn hình khi làm workshop, lưu vào `static/images/5-Workshop/...` đúng đường dẫn ghi trong từng ô "📷 Chèn ảnh", rồi bỏ comment dòng `<!-- ![...](...) -->` ngay bên dưới.
4. Đổi `baseURL` trong `config.toml` thành địa chỉ GitHub Pages của bạn.

## Xem thử trên máy

Cài [Hugo extended 0.134.3](https://github.com/gohugoio/hugo/releases/tag/v0.134.3), rồi chạy:

```bash
hugo server
```

Mở http://localhost:1313/ (tiếng Anh) hoặc http://localhost:1313/vi/ (tiếng Việt).

## Đưa lên GitHub Pages

1. Tạo repo mới trên GitHub, ví dụ `AWSreport`, rồi đẩy toàn bộ thư mục này lên nhánh `main`.
2. Workflow `.github/workflows/hugo.yml` sẽ tự build và đẩy kết quả sang nhánh `gh-pages`.
3. Vào **Settings → Pages**, chọn **Source: Deploy from a branch**, nhánh `gh-pages`, thư mục `/ (root)`.
4. Sau 1–2 phút, trang sẽ có ở `https://<github-username>.github.io/AWSreport/`.
