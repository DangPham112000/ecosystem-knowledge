# Clerk

_Cập nhật 2026-10-23._

## Kế hoạch

Một Clerk application **mới** cho cả hệ sinh thái (DAN-82, chưa tạo). Production instance trên root domain `dantepham.site`. Key đặt vào Doppler project `shared` (config `prd`), các app reference bằng `${shared.prd.CLERK_SECRET_KEY}`, `${shared.prd.CLERK_PUBLISHABLE_KEY}`.

Hướng dẫn tạo app nằm trong issue [DAN-82](https://linear.app/dangphamworkspace/issue/DAN-82/clerk-tao-clerk-application-moi-cho-he-sinh-thai).

## Hiện trạng từng app

| App | Clerk |
| --- | --- |
| Event Manager | Clerk app riêng (cũ), chỉ dùng instance **Development** (`pk_test_`/`sk_test_`) kể cả ở production, có chủ đích. Có Clerk OAuth cho MCP. Sẽ chuyển sang app chung (DAN-100). |
| Journei | Code đã dùng Clerk (merge 2026-10-11) nhưng **chưa có Clerk app và key**. Đợi app chung (DAN-82). Có Clerk OAuth cho MCP. |
| Bill Splitter | Chưa có đăng nhập. `adminUserId`, `Member.userId` là string chờ clerkId (DAN-84). |
| News | Chưa có Clerk. Ghi bằng API key `nwk_...` cho agent/MCP. Verify token Clerk ở DAN-86. |

## Biến môi trường thường gặp

- Backend: `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` (OAuth discovery cho MCP), `CLERK_AUTHORIZED_PARTIES` (danh sách origin, phân cách bằng dấu phẩy).
- Frontend (lúc build): `VITE_CLERK_PUBLISHABLE_KEY`, trong Doppler đặt bằng `${CLERK_PUBLISHABLE_KEY}`.
