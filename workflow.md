# Cách làm việc

_Quyết định ngày 2026-10-22._

- **Chỉ có production.** Không môi trường dev hay staging. Push lên `main` → GitHub Actions deploy → kiểm tra trên subdomain thật.
- **Mock mode sẽ bị bỏ** khỏi Event Manager, Journei và News (DAN-108). Không đề xuất cách test dựa vào mock hay setup chạy local.
- **Máy dev không giữ secret**: không `.env`, không `doppler.yaml`. Doppler CLI trên máy chỉ dùng để thêm, sửa secret.
- Dang dùng Windows/PowerShell; lệnh hướng dẫn nên có bản PowerShell.
- Dang viết tiếng Việt; trả lời và tài liệu bằng tiếng Việt.
- Push lên `main` của một app là **deploy production**, nên PR cần được Dang review trước khi merge.
