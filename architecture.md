# Kiến trúc đích

Dựa trên báo cáo phân tích khả thi ngày 2026-10-12, đã điều chỉnh theo quyết định dùng Clerk app mới. Chưa làm xong; tiến độ ở [linear.md](linear.md).

```
          Clerk (1 application MỚI, production trên dantepham.site)
                         │  session token (JWT)
   ┌──────────────┬──────┴───────┬──────────────┐
events.*      journei         bills          news
   │              │              │              │
   └──── verify token bằng cùng CLERK_SECRET_KEY ┘
                         │
          MongoDB chung (1 instance, sau DAN-90)
          ├─ core.users      ← đồng bộ từ Clerk webhook (DAN-91)
          ├─ event-manager
          ├─ journey-planner
          ├─ bill-splitter
          └─ news
```

## Đăng nhập chung (SSO)

- Production instance của Clerk gắn root domain `dantepham.site` đặt cookie phiên ở root, nên đăng nhập một app là đăng nhập mọi subdomain. Không cần satellite domain (gói trả phí) miễn là mọi app ở `*.dantepham.site`.
- Mọi backend verify token bằng cùng `CLERK_SECRET_KEY`; `CLERK_AUTHORIZED_PARTIES` liệt kê đủ các subdomain.
- Google login giữ được bằng cách bật Google trong Clerk.
- Người dùng cũ được gắn theo email ở lần đăng nhập đầu (Event Manager và Journei đã có logic này trong `backend/src/auth.ts`).

## Bảng user chung

- Collection `core.users` (email, tên, avatar, clerkId), cập nhật bởi webhook `user.created / user.updated / user.deleted`.
- Dữ liệu nghiệp vụ của từng app tham chiếu người dùng bằng `clerkId`.
- Cần gom Mongo về một instance (mỗi app vẫn database riêng); tiết kiệm RAM trên VPS.

## Chia sẻ dữ liệu

App A gọi GraphQL của app B bằng chính token Clerk của người dùng. Các luồng đã đề xuất (DAN-94..98):

- Journei → Bill Splitter: tạo splitter cho chuyến đi, mời sẵn người đi cùng.
- Journei ↔ Event Manager: hoạt động trong lịch trình hiện thành sự kiện, kiểm tra trùng giờ (`find_conflicts`).
- Event Manager → Bill Splitter: quỹ chung cho sự kiện.
- Danh bạ chung "người tôi hay đi cùng".
- News → Journei / Event Manager: tin theo điểm đến hoặc chủ đề sự kiện.

## Rủi ro

- Clerk là điểm lỗi chung cho mọi app.
- Hạn mức gói miễn phí của Clerk tính chung cho cả hệ sinh thái (chưa kiểm chứng con số).
- App đặt ngoài `*.dantepham.site` sẽ cần satellite domain (trả phí).
- Đọc thẳng DB của nhau sẽ tạo coupling; chỉ `core.users` được dùng chung.
