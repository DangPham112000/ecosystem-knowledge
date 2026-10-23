# Linear

Team `DangPhamWorkspace` (key `DAN`). Project: [Ecosystem: đăng nhập và dữ liệu dùng chung](https://linear.app/dangphamworkspace/project/ecosystem-djang-nhap-va-du-lieu-dung-chung-e53aa7c5cd47).

Khi bắt đầu hoặc xong một phần việc của hệ sinh thái, cập nhật issue DAN tương ứng thay vì tạo issue mới. Các issue có quan hệ blocked-by.

## Milestone

1. Đăng nhập chung (SSO)
2. Bảng user chung
3. Chia sẻ dữ liệu giữa các app

## Issue chính

| Issue | Nội dung | Trạng thái (2026-10-23) |
| --- | --- | --- |
| DAN-99 | Doppler quản lý secret (chặn DAN-82) | Done |
| DAN-82 | Tạo Clerk app mới cho hệ sinh thái, kèm hướng dẫn | Chưa làm |
| DAN-100 | Event Manager chuyển sang Clerk app mới | |
| DAN-83 | News: khoá API ghi (Urgent) | Cần kiểm tra lại: News đã có API key cho `upsertArticles` và MCP |
| DAN-84 | Bill Splitter gắn Clerk | |
| DAN-85 | Journei sang Clerk (sub-issue DAN-87/88/89) | Code đã có Clerk, chờ DAN-82 |
| DAN-86 | News verify token Clerk | |
| DAN-90 | Gom Mongo về một instance | |
| DAN-91 | `core.users` + endpoint webhook Clerk | |
| DAN-92 | Event Manager đọc `core.users` | |
| DAN-93 | Journei, Bill Splitter dùng `core.users` | |
| DAN-94..98 | Các luồng chia sẻ dữ liệu | |
| DAN-107 | Chuyển thư mục deploy Journei sang `/opt/journei` (trước bước Doppler của Journei) | |
| DAN-108 | Bỏ mock mode (Event Manager, Journei, News) | |

Trạng thái để trống là chưa ghi nhận; xem Linear để biết chính xác.
