# News

- Repo: [DangPham112000/news](https://github.com/DangPham112000/news). VPS `/opt/news`.
- Hiện cùng stack với Event Manager: pnpm workspace, Express 5 + Apollo GraphQL + MongoDB/Mongoose; frontend React 19 + Vite + Tailwind + Redux Toolkit. (Báo cáo ngày 2026-10-12 mô tả bản M1 dùng REST + SQLite; đã thay từ khoảng 2026-10-14.)
- AI agent đẩy tin theo topic qua MCP (`POST /mcp`: `list_topics`, `list_news`, `upsert_news`) và mutation `upsertArticles`; cả hai yêu cầu API key `Authorization: Bearer nwk_...` (tạo bằng `pnpm --filter backend apikey create <name>`).
- Chưa có người dùng và chưa có Clerk. Khi thêm tính năng theo người dùng: lưu `clerkId`, verify token Clerk (DAN-86).
- Secret: Doppler project `news` (`RATE_LIMIT_PER_MIN`, ...).
- Việc tiếp theo: xác nhận DAN-83 đã xong nhờ API key, DAN-86, DAN-108.
