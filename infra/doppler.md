# Doppler

_DAN-99 Done ngày 2026-10-22. Hướng dẫn chi tiết: [Hướng dẫn setup Doppler (DAN-99)](https://claude.ai/artifact/5Nku2RXAowyjbveJGygo7Y)._

## Cấu trúc

| Project | Config | Chứa gì |
| --- | --- | --- |
| `shared` | `prd` | Key dùng chung `CLERK_*` (thêm ở DAN-82); các app reference sang |
| `event-manager` | `prd` | Secret riêng + reference sang `shared` |
| `journei` | `prd` | Secret riêng + reference sang `shared` |
| `bill-splitter` | `prd` | Secret riêng (chưa có secret production) |
| `news` | `prd` | Secret riêng |

- Mỗi project **chỉ một environment `prd`** (đã xoá `dev`, `stg` ngày 2026-10-22).
- Reference chéo project: `${shared.prd.CLERK_SECRET_KEY}`. Reference trong cùng config: `${CLERK_PUBLISHABLE_KEY}`.
- Gói miễn phí: 10 project, đang dùng 5.

## Token và deploy

- Mỗi app hai service token trên config `prd`, quyền Read:
  - `vps-<app>`: gắn trên VPS theo thư mục app, `doppler configure set token --scope /opt/<app>`.
  - `github-actions-<app>`: lưu thành GitHub secret `DOPPLER_TOKEN`, dùng để lấy các key `VITE_*` lúc build frontend.
- Deploy trên VPS: `doppler run -- docker compose up -d --remove-orphans`. `docker-compose.yml` không cần sửa.
- Đổi secret: chạy lại workflow deploy. Đổi publishable key: phải build lại frontend.
- Secret phục vụ deploy (`VPS_HOST`, `VPS_SSH_KEY`, `GH_PAT`, `GHCR_PAT`) vẫn ở GitHub Secrets.
- `MONGODB_URI` ở production là hostname nội bộ Docker, không phải secret, để trong `docker-compose.yml` tới DAN-90.

## Trạng thái từng app

- Event Manager: deploy qua Doppler (EventManager PR #26), token scope `/opt/event-manager`.
- Journei: chưa. Các bước Doppler cho Journei ghi trong comment của DAN-82, làm sau DAN-107 (chuyển thư mục deploy sang `/opt/journei`).
- News, Bill Splitter: làm khi gắn Clerk (DAN-86, DAN-84).

## Rủi ro

Tài liệu Doppler ghi reference chéo project là tính năng trả phí, nhưng hiện vẫn chạy trên gói miễn phí. Nếu bị chặn: gom về một project với branch config kế thừa config gốc, hoặc chép key Clerk vào từng app.

Lịch sử git của 4 repo app đã được quét ngày 2026-10-22: không có key bị lộ.
