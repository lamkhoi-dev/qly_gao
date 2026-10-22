# 08 – Dữ liệu mẫu (giả) & nội dung còn thiếu

> ⚠️ **Mọi số liệu ở file này là GIẢ, do người ghi hồ sơ đặt để dev có dữ liệu chạy thử.** KHÔNG phải số liệu của khách. Bên B đã nói: *"em cứ để tạm, admin có thể chỉnh"* → dev nhập tạm, admin/khách sửa sau.
> Đặt chúng trong **script seed** có thể chạy lại/dễ xóa. Đừng để dữ liệu giả lọt vào bản chính thức.

## 1. Thông tin THẬT (đã có)

| Mục | Giá trị |
|---|---|
| Tên | Đại lý Gạo Phan Rang |
| Slogan | Giao hàng không ngại nắng mưa 24/7 |
| Hotline | 0918 999 727 |
| Địa chỉ | 35 Trần Đại Nghĩa, phường Đông Hải, tỉnh Khánh Hòa |
| Quy cách | 5 / 10 / 25 kg |
| Quà (tên) | Chảo chống dính Sunhouse · Nồi cơm điện Sunhouse · Bộ nồi nấu |
| Tỷ lệ hoàn | 2% (1.000đ = 1 điểm; 1 điểm = 20đ) |

## 2. Dữ liệu GIẢ để chạy thử

**6 loại gạo (giữ chỗ)** – tên/giá/ảnh thật do khách cung cấp
| # | Tên giữ chỗ | 5kg | 10kg | 25kg |
|---|---|---|---|---|
| 1 | Gạo mẫu 1 | 95.000đ | 185.000đ | 450.000đ |
| 2 | Gạo mẫu 2 | 105.000đ | 205.000đ | 500.000đ |
| 3 | Gạo mẫu 3 | 115.000đ | 225.000đ | 550.000đ |
| 4 | Gạo mẫu 4 | 125.000đ | 245.000đ | 600.000đ |
| 5 | Gạo mẫu 5 | 135.000đ | 265.000đ | 650.000đ |
| 6 | Gạo mẫu 6 | 145.000đ | 285.000đ | 700.000đ |
Tồn kho giả: 50 mỗi quy cách. Mô tả: "Mô tả loại gạo (khách sẽ cung cấp)".

**Quà (giữ chỗ)** – số điểm giả
| Quà | Điểm cần (giả) | Tương đương tiền (×20đ) |
|---|---|---|
| Chảo chống dính Sunhouse (x) | 5.000 | 100.000đ |
| Nồi cơm điện Sunhouse (y) | 12.000 | 240.000đ |
| Bộ nồi nấu (z) | 8.000 | 160.000đ |

(Ghi chú: với tỷ lệ 2%, khách phải mua khoảng 5 triệu để đủ 5.000 điểm. Khách/Bên B nên xem lại mức quà cho hợp lý khi đặt số thật.)

**Khung giờ giao (giả)**: 07:00–09:00 · 09:00–11:00 · 14:00–16:00 · 16:00–18:00.

**Vùng phục vụ**
- Tâm gần đúng thành phố Phan Rang – Tháp Chàm: khoảng `11.5667, 108.9833` (**cần thay bằng tọa độ thật của cửa hàng 35 Trần Đại Nghĩa – hãy tra bản đồ**).
- Bán kính mặc định **10 km** (chưa chốt – Q8).

**Admin**: tạo 1 tài khoản bằng script (mật khẩu đặt riêng, **không** commit vào git).

**Khách thử**: 2–3 khách giả với vài đơn mẫu để test báo cáo/điểm; xóa khi lên production.

## 3. Nội dung/hình ảnh còn thiếu (TODO – chờ Bên B/khách)

| Việc | Ai làm | Trạng thái |
|---|---|---|
| Ảnh **2 panel chào** (onboarding) | Bên B tạo bằng AI | **Chờ `prompt.md`** (xem mục 4) |
| Ảnh **4 banner** trang chủ (Sứ mệnh / Gạo / Tích điểm / Giao hàng) | Bên B tạo bằng AI | Chờ `prompt.md` |
| **Ảnh 6 loại gạo** thật + tên + mô tả + giá | Khách | Chưa có |
| **Ảnh 3 món quà** | Khách / lấy ảnh hãng | Chưa có |
| **Chữ banner "Sứ mệnh"** | Khách | Chưa có (Q11) |
| **Logo + icon app** + màu thương hiệu | Khách/Bên B | Chưa có |
| **Danh sách quà thật + số điểm** (x, y, z) | Khách | Chưa có |
| **Tọa độ cửa hàng + bán kính vùng phục vụ** | Khách | Chưa có |
| **Link Zalo** (OA hay số cá nhân) | Khách | Chưa có (Q10) |
| **Chính sách quyền riêng tư** (URL) | Bên B soạn | Chưa có |
| Ảnh chụp màn hình + mô tả store | Bên B | Sau khi app xong |
| Tài khoản Google Play / Apple Developer | Khách (đứng tên) | Chưa tạo; iOS: xem Q6 |
| Nhà cung cấp OTP + nạp tiền | Khách (trả phí) | Chưa chọn (Q5) |
| Tên miền + VPS | Khách trả; Bên B cấu hình | Chưa mua |

## 4. `prompt.md` cho AI tạo ảnh – CHƯA LÀM

Bên B có nói sẽ nhờ viết **`prompt.md`** để đưa cho AI tạo ảnh (panel/banner/ảnh gạo…). Việc này **chưa thực hiện**; khi được yêu cầu, sẽ gồm: bối cảnh thương hiệu (đại lý gạo Phan Rang, thân thiện, giao hàng mọi thời tiết), kích thước từng ảnh (theo thiết kế app), bảng màu, nội dung chữ trên ảnh, phong cách. Đừng tự bịa ảnh/nội dung banner thay Bên B.
