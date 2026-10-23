# Cách dùng và cập nhật repo này

## Khi bắt đầu một việc

1. Đọc [README.md](README.md), rồi file của app mình đang làm trong `apps/` và [decisions.md](decisions.md).
2. Nếu việc đụng tới đăng nhập, secret hay deploy, đọc thêm file tương ứng trong `infra/`.

## Khi nào phải ghi vào đây

Ghi lại khi một thay đổi **ảnh hưởng tới app khác hoặc tới hạ tầng chung**, ví dụ:

- Quyết định kiến trúc (Clerk, user chung, cách chia sẻ dữ liệu, gom Mongo).
- Thay đổi cấu trúc Doppler, tên secret dùng chung, thư mục deploy trên VPS, subdomain.
- Trạng thái của một app thay đổi theo cách app khác cần biết (đã chuyển sang Clerk chung, đã có API mới để app khác gọi).
- Thay đổi cách làm việc của Dang (ví dụ bỏ mock mode).

Chi tiết chỉ dùng trong một app (component, resolver, bug nội bộ) thì để trong repo của app đó, không ghi ở đây.

## Cách ghi

- Quyết định mới: thêm một mục **ở đầu** [decisions.md](decisions.md) với ngày (YYYY-MM-DD), quyết định, lý do, issue Linear nếu có.
- Sửa file liên quan cho đúng hiện trạng, không để nội dung cũ mâu thuẫn. Thông tin bị thay thế thì xoá hoặc ghi "đã thay bằng ... (ngày)".
- Cập nhật dòng "Cập nhật lần cuối" trong README.
- File ngắn, viết tiếng Việt, tên biến/lệnh giữ nguyên tiếng Anh. Ngày luôn ghi tuyệt đối.
- **Không bao giờ ghi secret** (key, token, mật khẩu, connection string có mật khẩu). Chỉ ghi tên biến và nơi chứa nó trong Doppler.
- Commit thẳng vào `main` hoặc mở PR đều được; commit message nói rõ app nào thay đổi.
