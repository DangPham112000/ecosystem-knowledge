# Event Manager

- Repo: [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager). URL: `https://events.dantepham.site`.
- Stack: pnpm workspace, TypeScript; backend Express + Apollo GraphQL + MongoDB/Mongoose; frontend React + Vite; Docker Compose + nginx.
- Đăng nhập: Clerk app riêng, instance Development dùng cả ở production (có chủ đích). `User { email, name, clerkId, googleId, avatar }`; lần đăng nhập đầu tự gắn vào user cũ theo email (`backend/src/auth.ts`). Có Clerk OAuth cho MCP/AI agent.
- Có `find_conflicts` (kiểm tra trùng giờ), dùng được cho luồng Journei ↔ Event Manager.
- Secret: Doppler project `event-manager`, đã deploy qua Doppler (PR #26, 2026-10-23). VPS `/opt/event-manager`.
- Việc tiếp theo: chuyển sang Clerk app chung (DAN-100), đọc `core.users` (DAN-92), bỏ mock mode (DAN-108).
