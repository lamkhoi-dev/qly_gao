# Hồ sơ bàn giao – App "Đại lý Gạo Phan Rang" (iOS + Android + Web Admin)

> Bộ tài liệu này để **AI/dev ở máy khác đọc và hiểu đúng 100%** rồi viết code.
> Viết cho người **chưa từng thấy cuộc trao đổi gốc**. Mọi thứ cần biết nằm trong thư mục này.

**Sản phẩm:** app đặt gạo giao tận nhà (khách trong khu vực Phan Rang) + Web Admin cho chủ cửa hàng.
Thanh toán COD, miễn phí ship, khách tích 2% tiền hoàn mỗi đơn (cộng dồn), có thể đổi quà cuối tháng.
Slogan: *"Giao hàng không ngại nắng mưa 24/7"*. Hợp đồng 15.000.000đ, làm trong 1,5–2 tuần.

---

## ⛔ ĐỌC 9 ĐIỀU NÀY TRƯỚC KHI LÀM (các chỗ dễ hiểu sai nhất)

1. **Điểm = 2% tiền mặt của ĐƠN VỪA MUA, cộng dồn, khách tự chọn lúc nào dùng.** Mua 100.000đ → số dư +2.000đ. **KHÔNG PHẢI** "giảm 2% cho đơn tiếp theo". (Khách nói thẳng: *"2% của đơn mua, chứ ko phải −2% đơn tiếp theo"*.) → `docs/02` mục 1.
2. **Giao diện hiển thị bằng TIỀN (VNĐ)**, vd "2.000đ", không bắt khách tự nhẩm điểm. Điểm chỉ là phụ. (Khách: *"show lên UI 2k luôn"*.)
3. **Hợp đồng có câu nói khác** ("chiết khấu 2% đơn kế tiếp"). Đó là cách nói cũ/gần đúng; khách bảo **không sửa hợp đồng**. **Làm theo tài liệu này, không làm theo chữ trong hợp đồng** về điểm/chiết khấu.
4. **Admin KHÔNG cộng/trừ điểm thủ công.** Hệ thống tự tính; admin chỉ xem. Không làm nút đó.
5. **Công tắc BẬT/TẮT chỉ điều khiển phần QUÀ** (mục "Quy đổi điểm ra sản phẩm" + nhận quà tại buổi phát). Dùng tiền tích lũy khi đặt hàng thì **luôn có**.
6. **Không có push thông báo trạng thái đơn.** Push chỉ dùng để báo **địa điểm/giờ phát quà** cuối tháng. Khách tự mở app xem trạng thái đơn.
7. **Phí ship luôn = 0.** Chỉ **COD**. Không cổng thanh toán, không tính ship theo km.
8. **Chỉ 1 tài khoản admin.** Chỉ **tạo tài khoản mới** mới kiểm tra GPS vùng Phan Rang; khách đã có tài khoản vẫn đăng nhập mọi nơi.
9. **Đừng bịa dữ liệu thật.** Giá/ảnh/tên 6 loại gạo, số điểm quà, tọa độ cửa hàng… chưa có; dùng dữ liệu giả ở `docs/08` và để admin sửa. Chỗ nào ghi **[MỞ]** thì dùng mặc định đã nêu, **và nhớ hỏi lại**.

---

## Thứ tự đọc

| # | File | Nội dung | Ghi chú |
|---|---|---|---|
| 1 | **`docs/02-lam-ro-va-quyet-dinh.md`** | **Nguồn sự thật mới nhất**: luật điểm/tiền, các quyết định, cái gì đã bị thay thế, câu hỏi mở + mặc định | **Đọc đầu tiên** |
| 2 | `docs/01-tong-quan-va-pham-vi.md` | Bối cảnh, các bên, trong/ngoài phạm vi, thương mại, tiến độ | |
| 3 | `docs/05-quy-tac-nghiep-vu.md` | Công thức & ràng buộc chính xác (điểm, đơn, tồn kho, GPS, OTP…) | Cần đọc trước khi viết logic |
| 4 | `docs/03-dac-ta-app-khach-hang.md` | Từng màn hình app khách hàng | |
| 5 | `docs/04-dac-ta-web-admin.md` | Từng trang Web Admin | |
| 6 | `docs/06-mo-hinh-du-lieu-goi-y.md` | Gợi ý bảng dữ liệu + API | Gợi ý, đổi được |
| 7 | `docs/07-ky-thuat-va-phat-hanh.md` | Gợi ý stack, hạ tầng, lưu ý lên App Store/Google Play | Gợi ý, đổi được |
| 8 | `docs/08-seed-data-va-noi-dung-con-thieu.md` | Dữ liệu giả để chạy thử, danh sách việc/nội dung còn thiếu | |
| – | `source/` | Bản gốc (nguyên văn) để tra cứu, **không** dùng làm đặc tả cuối cùng | |

## Thứ tự ưu tiên khi các nguồn mâu thuẫn

1. `docs/02-lam-ro-va-quyet-dinh.md` (mới nhất, thắng tất cả)
2. Các `docs/` còn lại (đã tổng hợp từ nguồn)
3. `source/01-yeu-cau-goc-cua-khach.md` (yêu cầu gốc – một số chỗ đã lỗi thời, nhất là phần điểm)
4. `source/` hợp đồng (chỉ để biết phạm vi thương mại; **câu chữ về điểm/chiết khấu không chính xác**)

## Nhãn dùng trong tài liệu

- **[CHỐT]** khách/Bên B đã xác nhận → làm đúng như vậy.
- **[SUY RA]** suy ra từ lời khách → làm theo, nhưng nên xác nhận lại.
- **[MẶC ĐỊNH]** chưa ai nói → dev tạm chọn như vầy, đổi được.
- **[MỞ]** chưa biết → dùng mặc định nêu bên cạnh và đánh dấu để hỏi.
- **[RÀNG BUỘC]** ràng buộc thật từ hợp đồng/ngân sách.

## Từ điển (Việt → tên gợi ý trong code)

| Việt | Tiếng Anh gợi ý |
|---|---|
| Khách hàng | customer |
| Admin / chủ cửa hàng | admin |
| Loại gạo / sản phẩm | product |
| Quy cách (5/10/25kg) | variant |
| Giỏ hàng | cart |
| Đơn hàng | order |
| Chờ xác nhận / Đang chuẩn bị / Đang giao / Hoàn thành / Hủy | pending / preparing / delivering / completed / cancelled |
| Khung giờ giao | delivery slot |
| Điểm tích lũy | points |
| Tiền tích lũy (2%) | cashback balance (= points × giá trị 1 điểm) |
| Sổ cái điểm | points ledger |
| Quà quy đổi | gift |
| Yêu cầu đổi quà | gift request |
| Buổi phát quà | gift distribution event |
| Công tắc quy đổi quà | `gift_redemption_enabled` |
| Vùng phục vụ | service area |
| Thông báo đẩy | push notification |
| Phiếu giao hàng | delivery slip |
| Hồ sơ khách hàng | customer profile |
| Bên A / Bên B | khách thuê làm (Đại lý Gạo Phan Rang) / bên làm (Lâm Nhật Khôi) |

## Tình trạng dự án (cập nhật 22/10/2026)

- Hợp đồng: **dự thảo** hoàn chỉnh nội dung (15 triệu; đợt 4/5/6 triệu; 1,5–2 tuần; bảo hành 1 năm); còn trống số HĐ, ngày ký, thông tin đại diện Bên A.
- Đặc tả: đủ để bắt đầu code. Còn các câu hỏi mở ở `docs/02` mục 4.
- **Chưa có**: bất kỳ dòng code nào; ảnh/banner; `prompt.md` cho AI tạo ảnh (Bên B sẽ nhờ làm sau); dữ liệu thật của khách (gạo, quà, tọa độ).

## Cách làm việc gợi ý cho AI

1. Đọc theo thứ tự ở trên. Tóm tắt lại bằng lời của bạn luật điểm (mục 1 ở `docs/02`) cho người dùng xác nhận **trước khi** viết code logic điểm.
2. Dựng khung (DB + API + app + admin), làm **luồng chính trước**: đăng ký (GPS+OTP) → xem gạo → đặt hàng → admin xác nhận/hoàn thành → tích tiền → dùng tiền ở đơn sau. Quà và báo cáo làm sau.
3. Viết test cho các ví dụ A–D ở `docs/02` mục 1.4.
4. Gặp điểm không rõ: dùng mặc định trong `docs/02` mục 4, ghi lại bạn đã dùng gì, và hỏi người dùng sau.
5. Không thêm tính năng ngoài phạm vi (mục 4 của `docs/01`).
