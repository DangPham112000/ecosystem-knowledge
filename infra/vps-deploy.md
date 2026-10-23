# VPS và deploy

- Một VPS chung cho mọi app, nằm sau Cloudflare (TLS) và nginx. Repo này public nên **không ghi IP VPS hay user SSH**; xem GitHub Secrets `VPS_HOST`, `VPS_USERNAME` của từng repo app.
- Mỗi app: GitHub Actions build Docker image, push lên GHCR, SSH vào VPS, `docker compose pull` rồi `doppler run -- docker compose up -d`.
- Push lên `main` của repo app = deploy production.

| App | Thư mục trên VPS | Ghi chú |
| --- | --- | --- |
| Event Manager | `/opt/event-manager` | Đọc secret từ Doppler |
| Journei | `~/journei` | Sẽ chuyển sang `/opt/journei` (DAN-107). `~/journei/.env` còn biến Google OAuth/JWT cũ, code không dùng nữa; giữ tới khi redeploy qua Doppler |
| News | `/opt/news` | |
| Bill Splitter | chưa deploy | Chưa có workflow deploy |

- Mỗi app hiện chạy container `mongo:7` riêng; kế hoạch gom về một instance (DAN-90).
- Journei: header `Cross-Origin-Opener-Policy: same-origin-allow-popups` phải giống nhau ở `frontend/vite.config.ts`, `frontend/nginx.conf`, `nginx/nginx.conf`. Biến backend mới phải thêm vào `environment:` trong `docker-compose.yml`.
