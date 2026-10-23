# Journei (Journey Planner)

- Repo: [DangPham112000/Journei](https://github.com/DangPham112000/Journei). Domain lấy từ biến `DOMAIN`.
- Stack: pnpm workspace; backend Express + Apollo GraphQL + MongoDB; frontend React + Vite + shadcn (Base UI). Google Maps (`VITE_GOOGLE_MAPS_API_KEY`). Có MCP server tại `POST /mcp` (Clerk OAuth).
- Đăng nhập: code đã chuyển sang Clerk (merge 2026-10-11), lần đầu gắn user cũ theo email. **Chưa có Clerk app và key**; sẽ dùng Clerk app chung (DAN-82), không tạo app riêng.
- Dữ liệu: `Plan` thuộc `User` (`userId`), `Place` thuộc `Plan`, `ScheduledActivity` tham chiếu `Place`. Google Calendar chưa làm; `User.refreshToken` và `Plan.googleEventId` không dùng.
- Deploy: VPS `~/journei`, sẽ chuyển sang `/opt/journei` (DAN-107). `~/journei/.env` còn biến Google OAuth/JWT cũ (code không dùng), giữ tới khi deploy qua Doppler.
- Doppler: project `journei`; các bước cho Journei ghi trong comment DAN-82, làm sau DAN-107.
- Việc tiếp theo: DAN-85 (sub-issue DAN-87/88/89), DAN-93, DAN-108.
