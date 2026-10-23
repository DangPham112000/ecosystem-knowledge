# ecosystem-knowledge

Kho kiến thức chung của hệ sinh thái app của Dang Pham (Event Manager, Journei, News, Bill Splitter). Mọi project (Claude project, repo app) đọc ở đây trước khi làm việc, và ghi lại ở đây khi có quyết định ảnh hưởng tới app khác. Cách cập nhật: xem [CONTRIBUTING.md](CONTRIBUTING.md).

_Cập nhật lần cuối: 2026-10-23._

## Tóm tắt nhanh

- Các app chạy trên subdomain của `dantepham.site`, cùng một VPS, mỗi app Docker Compose + nginx riêng.
- Mục tiêu: **một Clerk application chung** cho cả hệ sinh thái (chưa tạo, DAN-82), **một bảng user mỏng dùng chung** (`core.users`, đồng bộ bằng Clerk webhook), **chia sẻ dữ liệu qua API**, không đọc thẳng DB của app khác.
- Secret nằm trong **Doppler**: project `shared` + một project mỗi app, chỉ một environment `prd`.
- **Không có môi trường dev/staging**: thay đổi được kiểm tra thẳng trên production sau khi deploy.
- Việc cần làm theo dõi trên **Linear**, project "Ecosystem: đăng nhập và dữ liệu dùng chung" (DAN-82..DAN-108).

## Mục lục

| File | Nội dung |
| --- | --- |
| [decisions.md](decisions.md) | Nhật ký quyết định, mới nhất ở trên |
| [architecture.md](architecture.md) | Kiến trúc đích: SSO, user chung, chia sẻ dữ liệu |
| [workflow.md](workflow.md) | Cách làm việc: chỉ production, không mock |
| [infra/clerk.md](infra/clerk.md) | Clerk: hiện trạng và kế hoạch |
| [infra/doppler.md](infra/doppler.md) | Doppler: cấu trúc project, token, deploy |
| [infra/vps-deploy.md](infra/vps-deploy.md) | VPS, thư mục deploy, GitHub Actions |
| [linear.md](linear.md) | Linear project, milestone, issue chính |
| [apps/event-manager.md](apps/event-manager.md) | Event Manager |
| [apps/journei.md](apps/journei.md) | Journei (Journey Planner) |
| [apps/news.md](apps/news.md) | News |
| [apps/bill-splitter.md](apps/bill-splitter.md) | Bill Splitter |

## Repo

| App | Repo | Subdomain |
| --- | --- | --- |
| Event Manager | [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager) | `events.dantepham.site` |
| Journei | [DangPham112000/Journei](https://github.com/DangPham112000/Journei) | lấy từ biến `DOMAIN` trong Doppler |
| News | [DangPham112000/news](https://github.com/DangPham112000/news) | chưa ghi lại |
| Bill Splitter | [DangPham112000/bill-splitter](https://github.com/DangPham112000/bill-splitter) | chưa deploy |
