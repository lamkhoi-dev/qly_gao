# 05 – Quy tắc nghiệp vụ (đọc kỹ trước khi viết logic)

> Đây là nơi định nghĩa **chính xác** công thức & ràng buộc. Nhãn: [CHỐT] [MẶC ĐỊNH] [MỞ] như `docs/02`.
> Đơn vị tiền: **VNĐ, số nguyên** (không dùng số thực). Múi giờ: `Asia/Ho_Chi_Minh`.

## 1. Điểm & tiền tích lũy

### 1.1 Cấu hình (bảng `settings`, admin sửa được)
| Khóa | Mặc định | Ý nghĩa |
|---|---|---|
| `earn_vnd_per_point` | 1000 | Mỗi 1.000đ khách thanh toán → 1 điểm |
| `point_value_vnd` | 20 | 1 điểm = 20đ tiền mặt (→ hoàn 2%) |
| `gift_redemption_enabled` | false | Công tắc quy đổi quà |

### 1.2 Công thức
```
điểm_cộng        = floor( số_tiền_khách_thực_trả / earn_vnd_per_point )
tiền_tương_đương = điểm × point_value_vnd
số_tiền_khách_thực_trả = tiền_hàng − tiền_tích_lũy_đã_dùng       (xem Q2 ở docs/02)
số_dư_điểm       = SUM(ledger.points) của khách
số_dư_tiền       = số_dư_điểm × point_value_vnd
```
Ví dụ: đơn 100.000đ không dùng tiền tích lũy → 100 điểm → +2.000đ.
Làm tròn: dùng `floor`; số lẻ dưới 1.000đ không được điểm [MẶC ĐỊNH]. (Giá gạo thường chẵn nghìn nên hầu như không lệch so với "đúng 2%".)

### 1.3 Khi nào ghi sổ cái (`points_ledger`, chỉ thêm, không sửa/xóa)
| Sự kiện | Dòng sổ cái | `points` |
|---|---|---|
| Khách đặt đơn có dùng tiền tích lũy | `redeem_cash` (trừ ngay lúc tạo đơn để chặn dùng 2 lần) | `−điểm_dùng` |
| Đơn → **Hoàn thành** | `earn` | `+điểm_cộng` (tính trên số tiền thực trả) |
| Đơn **Hủy** (khách hoặc admin) khi đã dùng điểm | `refund` | `+điểm_dùng` (hoàn lại đúng số đã trừ) |
| Admin **duyệt** đổi quà | `redeem_gift` | `−điểm_quà` |
| Đơn Hủy | (không có `earn`) | – |

- Mỗi dòng có `order_id` hoặc `gift_request_id` liên quan, `created_at`, ghi chú. Số dư = tổng; **không** lưu số dư rời rạc cần đồng bộ (hoặc nếu cache thì luôn kiểm tra bằng tổng).
- Thao tác phải **transaction**; số dư không bao giờ âm.
- **Không có** thao tác "admin cộng/trừ tay". Sửa sai nếu cần = can thiệp DB bởi Bên B, không đưa vào giao diện.

### 1.4 Dùng tiền tích lũy ở giỏ hàng
- Khách bật công tắc "Dùng tiền tích lũy". Số dùng = `min(số_dư_tiền, tiền_hàng)` [MẶC ĐỊNH – Q3/Q4], làm tròn xuống bội của `point_value_vnd` (vì điểm là số nguyên) rồi quy ra điểm: `điểm_dùng = floor(số_tiền_dùng / point_value_vnd)`.
- Server **tính lại** mọi con số khi tạo đơn; không tin số từ client.
- Tổng thanh toán = tiền hàng − tiền tích lũy dùng (có thể = 0đ). Phí ship = 0.

### 1.5 Hai nhánh sử dụng điểm
1. **Dùng như tiền** (luôn có, mọi lúc): như 1.4.
2. **Đổi quà** (chỉ khi `gift_redemption_enabled = true`): khách gửi yêu cầu → admin duyệt → trừ `điểm_quà`. Quà tính theo **điểm** (bảng `gifts.points_cost`).

> **Tuyệt đối không** cài "giảm 2% đơn tiếp theo". Xem `docs/02` mục 1.5.

## 2. Đơn hàng

### 2.1 Trạng thái (máy trạng thái)
```
 pending (Chờ xác nhận) ──xác nhận──► preparing (Đang chuẩn bị) ──► delivering (Đang giao) ──► completed (Hoàn thành)
        │                                   │                              │
        └────────────── hủy ────────────────┴──────────────────────────────┘──► cancelled (Hủy)
```
- Chỉ đi tiến, không quay lui; `completed` và `cancelled` là **kết thúc**.
- **Khách** tự hủy: chỉ ở `pending`. **Admin** hủy: từ `pending`, `preparing`, `delivering` (bắt nhập lý do).
- Lưu `cancel_reason`, `cancelled_by` (customer/admin), thời điểm mỗi trạng thái.
- Tên hiển thị tiếng Việt đúng như trên (đúng chính tả: "Chờ xác nhận", "Đang chuẩn bị", "Đang giao", "Hoàn thành", "Hủy").

### 2.2 Tạo đơn (server validate)
- Khách đã đăng nhập, tài khoản còn hoạt động.
- Có ≥ 1 dòng hàng; mỗi dòng: sản phẩm còn **bật**, quy cách còn **bật**, **đủ tồn kho**.
- Địa chỉ giao thuộc về khách (trong 2 địa chỉ đã lưu) → **chụp lại (snapshot)** vào đơn.
- Khung giờ giao đang bật; ngày giao hợp lệ.
- Phương thức thanh toán = `COD` (chỉ giá trị này).
- Giá lấy **giá hiện tại ở server** → lưu snapshot (tên, quy cách, đơn giá) vào `order_items` để sau đổi giá không ảnh hưởng đơn cũ.
- `shipping_fee_vnd = 0` luôn.
- Mã đơn duy nhất, dễ đọc: `GPR-YYMMDD-NNNN` [MẶC ĐỊNH].
- Trừ điểm nếu dùng tiền tích lũy (1.3). Tạo đơn + trừ điểm trong **một transaction**.

### 2.3 Tồn kho [MẶC ĐỊNH – Q12]
- Trừ tồn khi admin **xác nhận** đơn (`pending → preparing`); nếu lúc xác nhận không đủ tồn → báo admin, không cho xác nhận.
- Hủy đơn ở `preparing`/`delivering` → **cộng trả** tồn. Hủy ở `pending` → chưa trừ nên không trả.
- Khi tồn quy cách = 0 → app hiển thị hết hàng.

### 2.4 Mua lại
- Sao chép dòng hàng cũ vào giỏ (đang bật, còn hàng), **giá hiện tại**. Dòng nào không còn bán → bỏ qua và báo khách.

## 3. Quà quy đổi
- Yêu cầu: `pending` → `approved` (Đã đổi) hoặc `rejected`. Khách không tự hủy yêu cầu [MẶC ĐỊNH; có thể cho hủy khi còn pending].
- Tạo yêu cầu: kiểm tra `gift_redemption_enabled`, quà đang bật, **số dư ≥ điểm quà**, và tổng điểm các yêu cầu đang `pending` của khách + điểm quà ≤ số dư.
- Duyệt: kiểm tra lại số dư; trừ điểm trong transaction; ghi `decided_at`, `decided_by`.
- "Phát quà" ngoài đời là việc thủ công của cửa hàng; hệ thống chỉ ghi nhận **đã đổi** (không theo dõi "đã giao quà" – nếu muốn, thêm cột `handed_over` tùy chọn).

## 4. Vùng phục vụ (GPS) [CHỐT]
- Chỉ kiểm khi **tạo tài khoản mới**. Client gửi `lat,lng` kèm yêu cầu xin OTP (hoặc ở bước xác thực); **server** tính khoảng cách (Haversine) tới `service_area_center` ≤ `service_area_radius_km`.
- Ngoài vùng/không có tọa độ → từ chối với mã lỗi riêng để app hiện màn "ngoài vùng" (nguyên văn ở `docs/03` mục 3).
- Lưu tọa độ lúc đăng ký (để đối chiếu sau này) [MẶC ĐỊNH].
- Cấu hình (`settings`): `service_area_center_lat`, `service_area_center_lng`, `service_area_radius_km` (mặc định 10) – xem `docs/08`.
- Biết rõ: GPS có thể bị giả; chấp nhận được ở mức này.

## 5. OTP & phiên đăng nhập [MẶC ĐỊNH, chuẩn thông thường]
- OTP 6 chữ số, hết hạn **5 phút**, sai tối đa **5 lần**, gửi lại sau **60 giây**, giới hạn số lần xin OTP theo SĐT/IP/ngày (chống spam và tốn phí).
- Lưu **băm** của mã OTP, không lưu thô.
- Sau xác thực: cấp access token + refresh token (hoặc session); lưu an toàn trên máy (Keychain/Keystore).
- Lớp `OtpProvider` (interface) để thay kênh SMS ↔ Zalo ZNS mà không sửa nghiệp vụ (Q5).
- **Tài khoản test cho người duyệt store** (Apple/Google không nhận được SMS Việt Nam và ở ngoài vùng GPS): có cơ chế **bypass có kiểm soát** (SĐT thử + OTP cố định + bỏ qua GPS), bật/tắt bằng cấu hình server, **tắt ở production khi không cần**. Xem `docs/07`.

## 6. Hồ sơ & địa chỉ
- SĐT là duy nhất (định danh). Chuẩn hóa về một dạng (vd E.164 `+84…`) trước khi lưu/so sánh.
- Tối đa **2 địa chỉ**/khách (ràng buộc ở **server**, không chỉ UI). Xóa địa chỉ không ảnh hưởng đơn cũ (đã snapshot).
- Họ tên bắt buộc ở lần đăng nhập đầu.

## 7. Thông báo
- Push qua FCM (Android) + APNs qua FCM (iOS). Lưu `device_token` theo khách; xóa token khi đăng xuất/xóa tài khoản.
- Chỉ **một loại**: thông báo phát quà do admin soạn. Lưu lịch sử vào bảng `announcements`.

## 8. Xóa tài khoản (bắt buộc cho App Store/Google Play)
- Khách xóa trong app. Hệ quả [MẶC ĐỊNH]:
  - Xóa/ẩn danh dữ liệu cá nhân: tên, SĐT, địa chỉ, token thiết bị.
  - **Giữ** bản ghi đơn hàng để cửa hàng đối soát sổ sách, nhưng bỏ liên kết cá nhân (thay bằng "Khách đã xóa tài khoản").
  - Số dư điểm còn lại **mất** (hiện cảnh báo trước khi xóa).
  - Không đăng ký lại ngay bằng cùng SĐT? → cho phép đăng ký lại như tài khoản mới (số dư không khôi phục).
- Đơn đang xử lý (`pending/preparing/delivering`): chặn xóa tài khoản và báo "Bạn còn đơn đang xử lý" [MẶC ĐỊNH].

## 9. Định dạng & quy ước
- Tiền: `12.345.000đ` (vi-VN). Ngày giờ: `dd/MM/yyyy HH:mm`.
- Số liệu "kg": quy cách là **số nguyên 5/10/25** (cho phép cấu hình thêm quy cách khác trong tương lai nhưng UI hiện tại chỉ 3).
- Mọi chuỗi giao diện: tiếng Việt, có dấu; tập trung trong file i18n/hằng số để dễ sửa.
