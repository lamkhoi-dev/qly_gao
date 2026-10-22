# 04 – Đặc tả Web Admin (chủ cửa hàng)

> Web chạy trên trình duyệt (ưu tiên máy tính, dùng được trên điện thoại). **1 tài khoản admin duy nhất** [CHỐT].
> Ngôn ngữ: tiếng Việt. Nhãn: [CHỐT] [MẶC ĐỊNH] [MỞ] như `docs/02`.
> Luật điểm/tiền: `docs/02` mục 1 và `docs/05` mục 1.

## 0. Đăng nhập & bảo mật [MẶC ĐỊNH]

- Màn hình đăng nhập bằng **tên đăng nhập + mật khẩu** (không dùng OTP). 1 tài khoản; tài khoản gốc tạo bằng seed/script, mật khẩu ban đầu do Bên B đặt rồi bàn giao.
- Mật khẩu băm (bcrypt/argon2); khóa tạm sau nhiều lần sai; phiên có hạn; HTTPS bắt buộc.
- Có chức năng **đổi mật khẩu**.
- Không có đăng ký admin, không có phân quyền nhân viên.

## 1. Menu

`Tổng quan/Báo cáo` · `Đơn hàng` · `Sản phẩm` · `Khách hàng` · `Cấu hình điểm & quà` (gồm Quà quy đổi, Duyệt đổi quà) · `Khung giờ giao` · `Thông báo` · `Cài đặt cửa hàng`

## 2. Đơn hàng [CHỐT]

**Danh sách**: lọc theo trạng thái, ngày, tìm theo mã đơn/tên/SĐT. Cột: mã đơn, khách, SĐT, tổng, khung giờ giao, trạng thái, thời gian đặt. Có badge số đơn **Chờ xác nhận** (ưu tiên nổi bật).

**Chi tiết**: toàn bộ thông tin đơn (khách, địa chỉ giao – bản chụp lúc đặt, khung giờ + ngày giao, danh sách gạo, tiền hàng, tiền tích lũy đã dùng, tổng thu COD, ghi chú, điểm sẽ cộng/đã cộng, lịch sử đổi trạng thái).

**Hành động**
| Trạng thái hiện tại | Admin có thể |
|---|---|
| Chờ xác nhận | **Xác nhận** (→ Đang chuẩn bị) · **Hủy** (bắt nhập lý do) |
| Đang chuẩn bị | Chuyển **Đang giao** · Hủy (lý do) |
| Đang giao | Chuyển **Hoàn thành** · Hủy (lý do) |
| Hoàn thành / Hủy | Chỉ xem (**không** đổi ngược) |

- Đổi trạng thái chỉ đi **tiến** theo thứ tự, trừ Hủy. Mỗi lần đổi ghi log (ai, lúc nào).
- **Hoàn thành** → hệ thống cộng tiền tích lũy cho khách. **Hủy** → hoàn lại tiền tích lũy khách đã dùng; không cộng. Chi tiết: `docs/05`.
- **In phiếu giao hàng** [CHỐT]: nút "In phiếu" mở trang in (CSS `@media print`, khổ A5 hoặc A4). Nội dung phiếu: tên cửa hàng + hotline · mã đơn · ngày & khung giờ giao · tên + SĐT khách · địa chỉ giao · bảng gạo (loại, quy cách, SL, thành tiền) · tiền tích lũy đã dùng · **TỔNG THU COD** (in đậm) · ghi chú. Có chỗ chữ ký người giao/nhận. In nhiều đơn cùng lúc: tùy chọn [MẶC ĐỊNH: có, chọn nhiều đơn → in].

## 3. Sản phẩm [CHỐT]

- Danh sách 6 loại gạo (có thể nhiều hơn/ít hơn nếu admin thêm/ẩn; app chỉ hiện sản phẩm **đang bật**).
- **Thêm / sửa**: tên, mô tả, ảnh (upload), thứ tự hiển thị, **ẩn/hiện**.
- Mỗi sản phẩm có các **quy cách (variant) 5 / 10 / 25 kg**: mỗi quy cách có **giá** và **tồn kho** (số bao/túi), bật/tắt riêng.
- **Cập nhật giá** nhanh (sửa ngay trên danh sách).
- **Quản lý tồn kho**: xem/sửa số lượng; nhắc/đánh dấu hết hàng. (Không có thông báo "sắp hết gạo" – đã loại bỏ.)
- Xóa sản phẩm: chỉ **ẩn** nếu đã có đơn hàng liên quan (giữ dữ liệu lịch sử).

## 4. Khách hàng [CHỐT]

- **Danh sách**: tên, SĐT, số đơn, tổng chi tiêu, tiền tích lũy/điểm hiện có, ngày đăng ký. Tìm kiếm theo tên/SĐT.
- **Chi tiết**: hồ sơ (tên, SĐT, 2 địa chỉ) · **lịch sử mua** (danh sách đơn) · **điểm tích lũy hiện có** (hiển thị cả tiền và điểm) · **lịch sử cộng/trừ điểm** (chỉ đọc).
- **KHÔNG có nút cộng/trừ điểm thủ công** [CHỐT – khách: "NÀY APP TÍNH TOÁN TỰ ĐỘNG"].
- **Xuất Excel (.xlsx)** danh sách khách [CHỐT]: nút "Xuất Excel". Admin **tự bấm xuất và tự gửi** mỗi tuần; **không** cần lịch tự động / gửi email. Cột gợi ý: Họ tên, SĐT, Địa chỉ 1, Địa chỉ 2, Số đơn hoàn thành, Tổng chi tiêu, Điểm hiện có, Tiền tích lũy tương đương, Ngày đăng ký, Ngày mua gần nhất.

## 5. Cấu hình điểm & quà [CHỐT]

**5.1 Cấu hình điểm**
- `Mỗi bao nhiêu đồng được 1 điểm` (mặc định **1.000**) — `earn_vnd_per_point`.
- `Giá trị 1 điểm quy ra tiền` (mặc định **20đ**, tương đương hoàn 2%) — `point_value_vnd`.
- Hiển thị bên cạnh: "Tỷ lệ hoàn tương đương: **2%**" (tự tính = giá trị 1 điểm ÷ số đồng mỗi điểm × 100) để admin dễ hiểu.
- Đổi cấu hình chỉ áp dụng cho giao dịch **sau đó**; điểm cũ giữ nguyên (sổ cái lưu theo điểm). Hiển thị cảnh báo khi đổi: tiền tương đương của số dư hiện có của mọi khách sẽ thay đổi theo giá trị 1 điểm mới.

**5.2 Công tắc BẬT/TẮT quy đổi quà** [CHỐT]
- 1 công tắc lớn. **BẬT**: app hiện mục "Quy đổi điểm ra sản phẩm" + cho phép gửi yêu cầu nhận quà. **TẮT**: app ẩn hoàn toàn mục quà. Tiền tích lũy dùng khi đặt hàng **không** phụ thuộc công tắc.
- Hiển thị trạng thái hiện tại rõ ràng, hỏi xác nhận khi gạt.

**5.3 Quà quy đổi** [CHỐT]
- Danh sách quà: **tên, ảnh, số điểm cần đạt**, bật/tắt, thứ tự. Mặc định 3 quà: chảo chống dính Sunhouse (x), nồi cơm điện Sunhouse (y), bộ nồi nấu (z). Admin thêm/sửa/xóa được.

**5.4 Duyệt đổi quà** [CHỐT] – chỉ áp dụng cho "nhận quà tại buổi phát"
- Danh sách yêu cầu: khách, SĐT, quà, điểm cần, **số điểm khách hiện có**, thời gian gửi, trạng thái (Chờ duyệt / Đã đổi / Từ chối).
- **Duyệt** → kiểm tra lại số dư còn đủ → trạng thái **Đã đổi** + **trừ điểm** (ghi sổ cái). Không đủ → báo lỗi, không duyệt.
- **Từ chối** (kèm lý do) [MẶC ĐỊNH].
- Bộ lọc theo trạng thái; xuất danh sách "cần phát quà tháng này" (Excel hoặc in) [MẶC ĐỊNH, tùy chọn] để chủ tiệm chuẩn bị quà cho buổi phát.

## 6. Khung giờ giao [CHỐT]

- Danh sách khung giờ (vd 07:00–09:00): **thêm / sửa / xóa / bật-tắt / thứ tự**. App chỉ hiện khung đang bật.
- (Tùy chọn [MẶC ĐỊNH – có thể bỏ]) số đơn tối đa mỗi khung. Mặc định **không giới hạn**.

## 7. Thông báo (push) [CHỐT]

- Form soạn: **tiêu đề + nội dung** (vd: "Phát quà tháng 11: 9h sáng Chủ nhật 30/11 tại TTTM …") → nút **Gửi** tới **tất cả khách** có thiết bị nhận push [MẶC ĐỊNH; có thể thêm lọc "chỉ khách có yêu cầu đã duyệt"].
- Lưu **lịch sử thông báo đã gửi** (tiêu đề, nội dung, thời gian) – dùng cho màn "Thông báo" trong app (Q15).
- Chỉ dùng cho **địa điểm/thời gian phát quà**; không có thông báo tự động theo trạng thái đơn.

## 8. Báo cáo [CHỐT]

Bộ lọc thời gian: hôm nay / 7 ngày / tháng này / tùy chọn khoảng ngày. Chỉ tính đơn **Hoàn thành** cho doanh thu [MẶC ĐỊNH].
- **Doanh thu** (tổng tiền khách thực trả) theo ngày/tháng (biểu đồ + tổng).
- **Số đơn** (theo trạng thái: tổng, hoàn thành, hủy).
- **Mặt hàng bán chạy** (theo loại gạo/quy cách: số lượng, doanh thu) – top N.
- **Khách mua nhiều** (top N theo tổng chi tiêu và số đơn).
- Gợi ý thêm (không bắt buộc): tổng tiền tích lũy đã phát sinh/đã dùng (để chủ tiệm biết "nợ" hoàn tiền).

## 9. Cài đặt cửa hàng [MẶC ĐỊNH]

Chỉnh được (bảng `settings`): hotline, địa chỉ cửa hàng, link Zalo, **vùng phục vụ** (tâm + bán kính km), nội dung 4 banner/2 panel (nếu làm CMS đơn giản – tùy chọn; nếu không thì để trong app/asset), logo.
