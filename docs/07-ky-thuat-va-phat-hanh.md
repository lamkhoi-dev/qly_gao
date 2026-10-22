# 07 – Kỹ thuật, hạ tầng & phát hành (GỢI Ý + lưu ý thực tế)

> Hợp đồng ghi: *"công nghệ cụ thể do Bên B lựa chọn và nêu trong tài liệu bàn giao"*. Nên mọi thứ dưới đây là **đề xuất**, trừ phần ghi [RÀNG BUỘC].

## 1. Ràng buộc thật

- [RÀNG BUỘC] **1,5–2 tuần**, ngân sách **15 triệu** → ưu tiên **một codebase cho cả iOS + Android** và stack đơn giản, ít bộ phận chuyển động. Tránh microservice, tránh hạ tầng phức tạp.
- [RÀNG BUỘC] Chạy trên **1 VPS** (~250.000đ/tháng) → cấu hình nhỏ (khoảng 1–2 vCPU, 1–2GB RAM). Lượng khách nhỏ (cửa hàng phục vụ một thành phố).
- [RÀNG BUỘC] Bàn giao **source code + tài liệu triển khai/vận hành** cho Bên A.
- [RÀNG BUỘC] Tên miền ≈ 400.000đ/năm → dùng cho API + Web Admin (HTTPS).
- [RÀNG BUỘC] Push notification: dùng dịch vụ miễn phí (FCM). OTP: tốn phí theo tin → cần chống spam OTP.

## 2. Đề xuất stack (một trong nhiều lựa chọn hợp lý)

| Lớp | Đề xuất | Lý do |
|---|---|---|
| App di động | **Flutter** hoặc **React Native (Expo)** – 1 codebase | Cả iOS + Android trong thời gian ngắn |
| Backend | **Node.js (NestJS/Express)** hoặc **Laravel** + REST JSON | Phổ biến, nhanh dựng, dễ bàn giao |
| CSDL | **PostgreSQL** (hoặc MySQL) | Giao dịch (transaction) cho điểm/tồn kho |
| Web Admin | SPA (React/Vue) hoặc trang server-render | Admin 1 người, không cần phức tạp |
| Triển khai | Docker + Nginx + Let's Encrypt trên VPS | Rẻ, tự quản được |
| Push | Firebase Cloud Messaging (FCM) | Miễn phí, gửi cả iOS (qua APNs) + Android |
| Ảnh sản phẩm | Lưu trên VPS (thư mục) hoặc object storage nhỏ | Ít ảnh |
| Excel | Thư viện xuất .xlsx (ExcelJS/openpyxl/…) | Xuất danh sách khách |

Nếu có lý do khác (đã quen stack nào) thì dùng stack đó – miễn đáp ứng `docs/03–05`.

## 3. OTP

- Làm lớp `OtpProvider` (interface): `send(phone, code)`. Cài đặt ban đầu: **SMS** qua nhà cung cấp VN (eSMS, SpeedSMS, … – chọn 1; khách thanh toán trực tiếp). Có thể thêm `ZaloZnsProvider` sau.
- Giá tham khảo (từ trang công khai của nhà cung cấp, **không chính thức**, có thể đổi): SMS OTP ≈ **300–520đ/tin**; Zalo ZNS OTP ≈ **300đ/tin** (chưa VAT). Zalo ZNS cần **Zalo OA xác thực theo diện doanh nghiệp/tổ chức** (giấy ĐKKD, mã số thuế), mẫu tin được Zalo duyệt, chỉ gửi tới SĐT đang dùng Zalo. Bảng so sánh đã đưa vào hợp đồng (Điều 3.1).
- Chống lạm dụng: giới hạn theo SĐT/IP/ngày, hết hạn 5 phút, tối đa 5 lần thử (`docs/05` mục 5).

## 4. Quyền & cấu hình theo nền tảng

- **GPS**: Android cần `ACCESS_FINE_LOCATION`/`COARSE`; iOS cần `NSLocationWhenInUseUsageDescription` với lời giải thích tiếng Việt rõ ràng (vd *"Ứng dụng dùng vị trí để kiểm tra bạn có nằm trong khu vực giao hàng của đại lý hay không."*). Chỉ xin **When In Use**, không xin quyền nền.
- **Push**: xin quyền thông báo (iOS bắt buộc hỏi; Android 13+ có quyền `POST_NOTIFICATIONS`).
- **Gọi điện**: mở `tel:` (không cần quyền đặc biệt). **Zalo**: mở link `https://zalo.me/…` (nếu app Zalo có thì mở app).
- Dùng HTTPS cho mọi API; không để key/secret trong app.

## 5. Phát hành lên App Store / Google Play (lưu ý dễ vấp)

1. **Tài khoản nhà phát triển do Bên A đứng tên** (Google Play 25 USD một lần; Apple Developer 99 USD/năm) **hoặc** iOS dùng chung tài khoản Bên B (80 USD/năm) – chưa chốt (Q6). Cần chuẩn bị bundle ID / package name theo cấu hình.
2. **Xóa tài khoản trong app là bắt buộc** (Apple yêu cầu với app có tạo tài khoản; Google Play cũng yêu cầu đường dẫn/luồng xóa dữ liệu) → đã có trong đặc tả.
3. **Tài khoản test cho người duyệt**: người duyệt Apple/Google ở ngoài Việt Nam và không nhận được SMS VN, lại bị chặn GPS → cần **SĐT thử + OTP cố định + bỏ qua GPS** (bật bằng cấu hình server, ghi thông tin trong phần "Ghi chú cho người duyệt" của store). Tắt khi không cần.
4. Cần **Chính sách quyền riêng tư (URL công khai)**, mô tả dữ liệu thu thập: SĐT, tên, địa chỉ, vị trí (chỉ để kiểm tra vùng), lịch sử mua.
5. Ảnh chụp màn hình store, icon, mô tả tiếng Việt – xem `docs/08`.
6. App **không bán hàng số / không dùng IAP**: bán gạo = hàng hóa vật lý, thanh toán COD → không vướng quy tắc thanh toán trong app của Apple.
7. Thời gian duyệt Apple/Google có thể vài ngày → **nộp sớm** (đừng để cuối tuần 2). Sự cố/chậm do store **không** phải lỗi của bên làm theo hợp đồng.

## 6. Môi trường & vận hành

- 2 môi trường: **staging** (thử, để khách UAT) và **production**. DB sao lưu tự động hằng ngày (cron `pg_dump` + giữ vài bản).
- Log lỗi cơ bản; giám sát uptime đơn giản (UptimeRobot hoặc tương đương).
- Biến môi trường cho: DB, khóa JWT/session, nhà cung cấp OTP, FCM, bypass reviewer.
- Tài liệu bàn giao cho Bên A: cách triển khai lại, cách sao lưu/khôi phục, danh sách tài khoản/khóa API, cách gia hạn tên miền/VPS.

## 7. Kiểm thử tối thiểu nên có

- Unit test cho **tính điểm/tiền tích lũy**, trạng thái đơn, hoàn điểm khi hủy, duyệt quà (dùng ví dụ A–D ở `docs/02` mục 1.4).
- Test đồng thời: hai đơn cùng dùng tiền tích lũy một lúc không được làm số dư âm.
- Test luồng chính end-to-end: đăng ký (trong vùng) → đặt hàng → admin xác nhận → hoàn thành → số dư +2% → đặt đơn tiếp dùng tiền tích lũy.
- Test ngoài vùng: tọa độ ngoài bán kính → bị chặn, message đúng nguyên văn.
