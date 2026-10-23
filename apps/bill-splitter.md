# Bill Splitter

- Repo: [DangPham112000/bill-splitter](https://github.com/DangPham112000/bill-splitter). Chưa deploy, chưa có workflow deploy.
- Stack: pnpm monorepo; frontend React 19 + Vite + Tailwind + Apollo Client; backend Express + Apollo GraphQL + Mongoose; `shared/` chứa helper tiền tệ dùng chung (VND mặc định, hỗ trợ THB, CNY...).
- Chia chi phí cho chuyến đi, dự án, bữa tiệc. Thành viên tham gia bằng link, không cần đăng nhập; app tính ai trả ai với ít lượt chuyển nhất.
- Đăng nhập: chưa có. `Splitter.adminUserId` và `Member.userId` là string chờ clerkId. Kế hoạch: Clerk tuỳ chọn, vẫn cho thành viên ẩn danh, gắn `userId` khi họ đăng nhập (DAN-84).
- Secret: Doppler project `bill-splitter`, chưa có secret production.
- Là đích của các luồng chia sẻ: Journei → Bill Splitter, Event Manager → Bill Splitter.
