# 06 – Mô hình dữ liệu & API (GỢI Ý – không bắt buộc)

> Công nghệ do bên làm chọn. Đây là **đề xuất** để dev khỏi phải suy luận từ đầu. Được phép đổi tên/cấu trúc miễn là giữ đúng **nghiệp vụ** ở `docs/05`.
> Tên bảng/cột bằng tiếng Anh; nhãn hiển thị bằng tiếng Việt.

## 1. Thực thể

```
users
  id, phone (UNIQUE, chuẩn hóa), full_name, role ('customer'|'admin'), status ('active'|'deleted'),
  reg_lat, reg_lng, created_at, deleted_at
admin_credentials            -- chỉ cho 1 admin
  user_id, username, password_hash, last_login_at

addresses                    -- tối đa 2/khách (ràng buộc ở server)
  id, user_id, label, address_line, receiver_name?, receiver_phone?, lat?, lng?, created_at

device_tokens
  id, user_id, platform ('ios'|'android'), token, updated_at

otp_requests
  id, phone, code_hash, expires_at, attempts, created_at, ip, verified_at

products
  id, name, description, image_url, is_active, sort_order
product_variants             -- quy cách
  id, product_id, weight_kg (5|10|25), price_vnd, stock_qty, is_active

delivery_slots
  id, label, start_time, end_time, is_active, sort_order

orders
  id, code (UNIQUE), user_id, status ('pending'|'preparing'|'delivering'|'completed'|'cancelled'),
  subtotal_vnd, points_used, discount_vnd, shipping_fee_vnd (=0), total_payable_vnd,
  points_earned, payment_method ('COD'),
  address_snapshot (json), delivery_date, delivery_slot_snapshot (json), note,
  cancel_reason, cancelled_by ('customer'|'admin'),
  created_at, confirmed_at, delivering_at, completed_at, cancelled_at
order_items
  id, order_id, product_id, variant_id, name_snapshot, weight_kg, unit_price_vnd, qty, line_total_vnd
order_status_log
  id, order_id, from_status, to_status, changed_by, changed_at

points_ledger                -- CHỈ THÊM, không sửa/xóa
  id, user_id, type ('earn'|'redeem_cash'|'refund'|'redeem_gift'), points (+/−),
  order_id?, gift_request_id?, note, created_at

gifts
  id, name, image_url, points_cost, is_active, sort_order
gift_requests
  id, user_id, gift_id, points_cost_snapshot, status ('pending'|'approved'|'rejected'),
  reject_reason?, created_at, decided_at, decided_by

announcements
  id, title, body, sent_at, created_by

settings                     -- key/value
  earn_vnd_per_point=1000, point_value_vnd=20, gift_redemption_enabled=false,
  service_area_center_lat, service_area_center_lng, service_area_radius_km=10,
  hotline='0918999727', store_address='35 Trần Đại Nghĩa, phường Đông Hải, tỉnh Khánh Hòa',
  zalo_link='https://zalo.me/0918999727'
```

Số dư khách: `SELECT COALESCE(SUM(points),0) FROM points_ledger WHERE user_id = ?` (có thể cache nhưng luôn đối chiếu được bằng tổng).

## 2. API gợi ý (REST, JSON)

Tiền tố `/api/v1`. Lỗi trả `{ "code": "...", "message": "tiếng Việt thân thiện" }`.

**Công khai / xác thực**
- `GET  /config/public` → hotline, địa chỉ, Zalo, `gift_redemption_enabled`, thông tin hiển thị cho khách.
- `POST /auth/otp/request` `{phone, lat, lng}` → gửi OTP; lỗi `OUT_OF_SERVICE_AREA` nếu tài khoản mới ngoài vùng.
- `POST /auth/otp/verify` `{phone, code}` → token + `{is_new_user}`.
- `POST /auth/refresh`, `POST /auth/logout`.

**Khách (cần đăng nhập)**
- `GET/PUT /me` (họ tên) · `DELETE /me` (xóa tài khoản).
- `GET/POST /me/addresses`, `PUT/DELETE /me/addresses/{id}` (tối đa 2 → lỗi `ADDRESS_LIMIT`).
- `POST /me/device-token`.
- `GET  /products` (kèm variants đang bật, tồn).
- `GET  /delivery-slots`.
- `POST /orders/preview` `{items, address_id, slot_id, date, use_points}` → trả tính toán server (tiền hàng, điểm dùng, thanh toán, điểm sẽ cộng) để hiển thị "Đơn này sẽ tích +X đ".
- `POST /orders` (tạo đơn) · `GET /orders` · `GET /orders/{id}` · `POST /orders/{id}/cancel` (chỉ `pending`) · `POST /orders/{id}/reorder`.
- `GET  /me/points` → `{points, cash_vnd}` · `GET /me/points/ledger`.
- `GET  /gifts` (chỉ khi bật) · `POST /gift-requests` · `GET /gift-requests`.
- `GET  /announcements`.

**Admin (cần đăng nhập admin)**
- `POST /admin/login`, `POST /admin/change-password`.
- `GET /admin/orders` (+lọc) · `GET /admin/orders/{id}` · `POST /admin/orders/{id}/status` `{to, reason?}` · `GET /admin/orders/{id}/print`.
- CRUD `/admin/products` (+variants, upload ảnh).
- `GET /admin/customers` · `GET /admin/customers/{id}` · `GET /admin/customers/export.xlsx`.
- `GET/PUT /admin/settings` (điểm, công tắc quà, vùng phục vụ, thông tin cửa hàng).
- CRUD `/admin/gifts` · `GET /admin/gift-requests` · `POST /admin/gift-requests/{id}/approve|reject`.
- CRUD `/admin/delivery-slots`.
- `POST /admin/announcements` (lưu + gửi push) · `GET /admin/announcements`.
- `GET /admin/reports/summary|revenue|top-products|top-customers`.

## 3. Quy tắc dữ liệu cần nhớ
- Mọi thay đổi số dư điểm đi qua **một hàm** duy nhất (service) – transaction – để khỏi sai lệch.
- Snapshot địa chỉ/giá/tên hàng vào đơn.
- Xóa mềm cho sản phẩm/khách (giữ lịch sử).
- Index: `orders(user_id, created_at)`, `orders(status, created_at)`, `points_ledger(user_id)`, `users(phone)`.
