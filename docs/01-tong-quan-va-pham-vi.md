# 01 – Tổng quan & phạm vi

## 1. Sản phẩm là gì

**App "Đại lý Gạo Phan Rang"** – ứng dụng di động (iOS + Android) cho khách đặt gạo giao tận nhà, kèm **Web Admin** cho chủ cửa hàng quản lý bán hàng.
Slogan: **"Giao hàng không ngại nắng mưa 24/7"**.

Mô hình kinh doanh: đại lý gạo ở Phan Rang (tỉnh Khánh Hòa). Khách đặt gạo qua app → chủ tiệm xác nhận → giao tận nhà → thu tiền mặt (COD). **Không tính phí ship.** Khách mua càng nhiều càng tích được tiền hoàn (xem `docs/02` mục 1) và có thể đổi quà vào cuối tháng.

## 2. Các bên & vai trò

| Vai trò | Là ai | Ghi chú |
|---|---|---|
| **Bên A** (khách thuê làm) | Đại lý Gạo Phan Rang – 35 Trần Đại Nghĩa, phường Đông Hải, tỉnh Khánh Hòa – hotline 0918 999 727 | Chủ sở hữu sản phẩm; đứng tên tài khoản Google Play / Apple (xem Q6 ở `docs/02`) |
| **Bên B** (bên làm) | Lâm Nhật Khôi | Người nhận làm, chủ dự án. Chọn công nghệ, giao hàng cho Bên A |
| **Người dùng cuối** | Khách mua gạo trong khu vực Phan Rang | Dùng app iOS/Android |
| **Admin** | Chủ cửa hàng (1 tài khoản) | Dùng Web Admin trên trình duyệt |
| **AI/dev đọc hồ sơ này** | Bạn | Viết code theo hồ sơ |

## 3. Trong phạm vi (IN)

**App khách hàng**
- 2 panel chào lần đầu mở app; màn hình đăng nhập giới thiệu quyền lợi.
- Kiểm tra vùng phục vụ bằng GPS khi **đăng ký**.
- Đăng ký / đăng nhập SĐT + OTP; hồ sơ (tên, SĐT, tối đa 2 địa chỉ, điểm/tiền tích lũy); xóa tài khoản.
- Trang chủ: 4 banner, 6 loại gạo (ảnh, giá, nút MUA), mục "Quy đổi điểm ra sản phẩm" (bật/tắt bởi admin), hotline + địa chỉ cuối trang.
- Chọn quy cách 5/10/25kg → giỏ hàng → chọn địa chỉ + khung giờ giao → dùng tiền tích lũy (tùy chọn) → ghi chú → đặt hàng COD.
- Đơn hàng: danh sách/lịch sử, chi tiết, trạng thái, mua lại, tự hủy khi còn "Chờ xác nhận".
- Tiền tích lũy: 2% mỗi đơn hoàn thành, cộng dồn, xem lịch sử cộng/trừ.
- Đổi quà tại buổi phát cuối tháng (khi admin bật).
- Gọi hotline, chat Zalo; push thông báo địa điểm/thời gian phát quà.

**Web Admin (1 tài khoản)**
- Sản phẩm (thêm/sửa/ẩn-hiện/giá/tồn kho); đơn hàng (xem/xác nhận/đổi trạng thái/hủy/in phiếu giao);
  khách hàng (danh sách/lịch sử mua/điểm – chỉ xem; xuất Excel); cấu hình điểm + công tắc quà; quà quy đổi;
  duyệt đổi quà; khung giờ giao; gửi push thông báo; báo cáo.

## 4. Ngoài phạm vi (OUT) – đừng làm, đừng "tiện tay thêm"

- Push thông báo trạng thái đơn hàng (nâng cấp sau; khách tự mở app xem).
- Phí ship (km hoặc cấu hình). Phí ship **luôn bằng 0**, không cần trường nhập.
- Thông báo nhắc sắp hết gạo.
- Thanh toán online (VNPay/Momo/…); chỉ COD.
- Admin cộng/trừ điểm thủ công.
- Nhiều admin, phân quyền nhân viên.
- Đăng nhập Google/Apple/Zalo (chỉ SĐT + OTP).
- Tự động gửi báo cáo Excel qua email (admin tự bấm xuất).

## 5. Tóm tắt thương mại (chỉ để biết ràng buộc)

| Mục | Giá trị |
|---|---|
| Giá trị hợp đồng | **15.000.000 VNĐ** (chưa VAT) |
| Thanh toán | Đợt 1 (đặt cọc) **4 triệu** · Đợt 2 (bàn giao bản chạy thử, ~giữa tuần 2) **5 triệu** · Đợt 3 (sau nghiệm thu) **6 triệu** |
| Thời gian | **1,5 – 2 tuần làm việc** kể từ khi khách thanh toán đợt 1 |
| Bảo hành | **1 năm** từ ngày ký biên bản nghiệm thu |
| Bản quyền | Thuộc Bên A sau khi thanh toán đủ. Bên B bàn giao source code + tài liệu hướng dẫn triển khai/vận hành |
| Hỗ trợ lên store | Bên B hỗ trợ đăng tải app lên Google Play / App Store (tài khoản nhà phát triển do Bên A đứng tên) |
| Chi phí ngoài giá (Bên A trả) | Google Play 25 USD (1 lần) · Apple Developer 99 USD/năm (tài khoản mới) hoặc 80 USD/năm (dùng chung tài khoản Bên B) · VPS ≈ 250.000đ/tháng · tên miền ≈ 400.000đ/năm · phí OTP theo tin (SMS ≈ 300–520đ/tin; Zalo ZNS ≈ 300đ/tin chưa VAT) |

Hợp đồng là **dự thảo**: số hợp đồng, ngày ký, nơi ký, tên đại diện Bên A, CCCD Bên A, email Bên A còn để trống.
Bản đầy đủ: `source/Hop_dong_ung_dung_Gao_Phan_Rang.docx` (+ bản text `source/03-hop-dong-trich-van-ban.md`).

> ⚠️ Về luật điểm/chiết khấu, câu chữ trong hợp đồng **không chính xác** bằng `docs/02`. Khách đã nói không cần sửa hợp đồng. Làm theo `docs/02`.

## 6. Tiến độ dự kiến theo hợp đồng

| Giai đoạn | Nội dung |
|---|---|
| Tuần 1 | Nền tảng & đặt hàng: DB, tài khoản, OTP, GPS, hồ sơ, địa chỉ; màn giới thiệu, trang chủ, 6 loại gạo, giỏ hàng, đặt hàng COD |
| Nửa đầu tuần 2 | Trạng thái đơn, lịch sử/mua lại/hủy, tích điểm + tiền tích lũy, quà quy đổi + công tắc; Web Admin (sản phẩm, đơn, khách, cấu hình, khung giờ, báo cáo, Excel, gửi thông báo). **Bàn giao bản chạy thử** (căn cứ thanh toán đợt 2) |
| Nửa cuối tuần 2 | Khách nghiệm thu (UAT), sửa lỗi, triển khai bản chính thức, hỗ trợ lên store, bàn giao source + tài liệu, ký biên bản nghiệm thu |

**Tiêu chí nghiệm thu** (ký được khi đạt): luồng chính chạy ổn trên môi trường thật (đăng nhập OTP, kiểm tra vùng, đặt hàng COD, quản lý đơn, tích điểm/tiền, hồ sơ khách); Web Admin đủ chức năng ở `docs/04`; không có lỗi nghiêm trọng làm gián đoạn luồng chính. Sự cố do dịch vụ bên thứ ba (Apple/Google, SMS/Zalo, GPS, VPS) **không** tính là lỗi để từ chối nghiệm thu.
