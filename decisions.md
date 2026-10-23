# Nhật ký quyết định

Mới nhất ở trên. Mỗi mục: ngày, quyết định, lý do.

## 2026-10-23: Repo `ecosystem-knowledge` là nơi chia sẻ kiến thức giữa các project

Mỗi project Claude có bộ nhớ riêng và không tự đồng bộ. Repo này được thêm vào các project (Ecosystem, Event Manager, Journei), và instructions của mỗi project yêu cầu đọc repo này trước khi làm, ghi lại quyết định ảnh hưởng tới app khác. Việc cần làm vẫn ở Linear.

## 2026-10-22: Chỉ có môi trường production

Mock mode sẽ bị bỏ (DAN-108). Không có dev/staging, không `.env` trên máy dev; thay đổi được kiểm tra trên site thật sau khi deploy. Doppler chỉ giữ environment `prd`. Xem [workflow.md](workflow.md).

## 2026-10-22: Giữ cấu trúc Doppler có reference chéo project

Dang đã thử `${shared.prd.X}` chạy được trên gói miễn phí, nên giữ thiết kế: project `shared` + một project mỗi app. Rủi ro chấp nhận: Doppler có thể chặn tính năng này (tài liệu ghi là gói trả phí). Phương án dự phòng ở [infra/doppler.md](infra/doppler.md).

## 2026-10-22: Journei dùng Clerk app chung, không tạo app riêng

Code Journei đã dùng Clerk nhưng chưa có Clerk app và key. Journei sẽ đợi Clerk app chung (DAN-82) và đọc key qua Doppler `shared`.

## 2026-10-22: Event Manager dùng instance Development của Clerk ở production

Lựa chọn có chủ đích (`pk_test_` / `sk_test_`); không định tạo Production instance cho Clerk app cũ của Event Manager. Event Manager sẽ chuyển sang Clerk app chung (DAN-100).

## 2026-10-12: Tạo Clerk application MỚI cho cả hệ sinh thái

Thay cho đề xuất ban đầu (dùng lại Clerk app của Event Manager), để tránh nhầm lẫn về sau. Production instance gắn root domain `dantepham.site` để SSO qua mọi subdomain. Event Manager cũng phải migrate (DAN-100).

## 2026-10-12: Quản lý secret tập trung bằng Doppler

Làm trước Clerk (DAN-99 chặn DAN-82). DAN-99 đã Done ngày 2026-10-22.

## 2026-10-12: Kiến trúc chung

- Clerk user ID là danh tính chung; không app nào tự quản lý đăng nhập.
- Một bảng user mỏng `core.users` đồng bộ bằng Clerk webhook, thay cho bảng User riêng của từng app.
- Chia sẻ dữ liệu qua API (GraphQL của app kia, dùng token Clerk của người dùng), không đọc thẳng DB của app khác. Chỉ `core.users` là dữ liệu dùng chung.
- Liên kết giữa app lưu dạng tham chiếu `{ app, id }`, không copy dữ liệu.

Nguồn: báo cáo phân tích khả thi ngày 2026-10-12. Chi tiết ở [architecture.md](architecture.md).
