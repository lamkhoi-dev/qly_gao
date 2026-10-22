# 03 – Đặc tả app khách hàng (iOS + Android)

> Ngôn ngữ giao diện: **tiếng Việt**. Tiền: số nguyên VNĐ, hiển thị `2.000đ` (dấu chấm ngăn nghìn, hậu tố `đ`).
> Nhãn trạng thái như `docs/02`: [CHỐT] [MẶC ĐỊNH] [MỞ]. Chữ in nghiêng nằm trong dấu ngoặc kép là **câu chữ nguyên văn phải dùng**.
> Luật điểm/tiền tích lũy: xem `docs/02` mục 1 và `docs/05` mục 1.

## 1. Bản đồ màn hình & điều hướng

```
Mở app lần đầu ──► Onboarding (2 panel) ──► Đăng nhập (quyền lợi + SĐT)
                                               │ (tài khoản mới) kiểm tra GPS
                                               │    ├─ ngoài vùng → Màn hình "Ngoài vùng phục vụ" (dừng)
                                               │    └─ trong vùng → OTP → nhập tên + địa chỉ đầu tiên
                                               ▼
                              ┌───────────── Khung chính (tab dưới) ─────────────┐
                              │ Trang chủ │ Giỏ hàng │ Đơn hàng │ Tích lũy │ Tài khoản │   [MẶC ĐỊNH]
                              └──────────────────────────────────────────────────┘
```
Lần mở sau, nếu còn phiên đăng nhập → vào thẳng Trang chủ. Onboarding chỉ hiện **lần đầu cài app**.

## 2. Màn hình Onboarding (lần đầu mở app) [CHỐT]

2 panel vuốt ngang, có nút "Bỏ qua" và "Tiếp tục":
1. *"Đại lý Gạo Phan Rang hân hạnh được phục vụ quý khách"*
2. *"Giao hàng tận nơi, quý khách không lo phí ship"*

Slogan *"Giao hàng không ngại nắng mưa 24/7"* hiển thị ở màn này hoặc màn đăng nhập (chỗ nào hợp thiết kế). Ảnh minh họa: xem `docs/08`.

## 3. Phạm vi phục vụ (GPS) [CHỐT]

- Chỉ **tạo tài khoản mới** mới kiểm tra vị trí. Tài khoản đã có vẫn đăng nhập bình thường ở bất kỳ đâu [MẶC ĐỊNH – Q13].
- Khi khách bấm "Đăng ký/Tiếp tục" lần đầu: xin quyền vị trí. Lấy tọa độ → gửi **server** kiểm tra (khoảng cách tới tâm vùng ≤ bán kính cấu hình; xem `docs/05` mục 4). Không tin kết quả chỉ tính trên máy khách.
- **Không cấp quyền vị trí** → không đăng ký được; hiện giải thích ngắn và nút mở Cài đặt.
- **Ngoài vùng** → màn hình chặn với **nguyên văn**:

> "Quý khách hiện KHÔNG GẦN khu vực phục vụ kinh doanh của đại lý, quý khách vui lòng chờ thêm một thời gian cho đến khi chúng tôi mở rộng phạm vi phục vụ. Cảm ơn quý khách đã kiên nhẫn và xin lỗi vì sự bất tiện này."

  (giữ chữ "KHÔNG GẦN" in hoa như trên). Không cho tạo tài khoản; có nút quay lại.

## 4. Màn hình Đăng nhập (giới thiệu quyền lợi) [CHỐT]

Phần trên: **giới thiệu quyền lợi** (3 ý, nội dung đúng tinh thần yêu cầu gốc nhưng **cập nhật theo luật mới**):
1. **Mỗi đơn hàng được tích 2% tiền hoàn**, cộng dồn, dùng bất cứ lúc nào (vd "Mua 100.000đ, tích ngay 2.000đ"). Khi quà đang bật: *"hoặc đổi quà tại buổi phát cuối tháng"*.
2. Đặt hàng online nhanh chóng, tiện lợi, nhận hàng tại nhà.
3. **Không phát sinh chi phí ship.**

> ⚠️ Yêu cầu gốc của khách ghi ý 1 theo kiểu cũ ("Giá trị mỗi đơn hàng tương ứng số điểm tích lũy…"). Câu chữ cuối cùng cho màn này: dùng ý "2% tiền hoàn" như trên, không viết "chiết khấu đơn kế tiếp".

Phần dưới: ô nhập **số điện thoại** (chuẩn hóa `0xxxxxxxxx` ↔ `+84…`), nút "Nhận mã OTP". Sau đó màn nhập **OTP 6 số** (nút gửi lại sau 60 giây).
Tài khoản mới: sau OTP → màn **"Thông tin của bạn"**: Họ tên (bắt buộc) + **địa chỉ giao hàng đầu tiên** (bắt buộc).

## 5. Trang chủ [CHỐT]

Từ trên xuống:
1. **Khối 4 banner** (carousel hoặc 4 panel): **Sứ mệnh · Gạo · Tích điểm · Giao hàng**. Nội dung chữ banner "Sứ mệnh": [MỞ – Q11]. Banner "Tích điểm" nói theo luật mới (2% tiền hoàn).
2. **Danh mục sản phẩm chính – 6 loại gạo.** Mỗi thẻ: **hình ảnh, giá, nút MUA**. Hiển thị trực tiếp trên màn hình chính, không phải bấm vào trang khác mới thấy.
   - Giá hiển thị trên thẻ: "**Từ {giá quy cách rẻ nhất}đ**" [MẶC ĐỊNH]. Ghi rõ thêm "/5kg" nếu quy cách rẻ nhất là 5kg.
   - Sản phẩm bị admin ẩn → không hiện. Hết hàng (mọi quy cách tồn = 0) → thẻ mờ + nhãn "Tạm hết hàng", nút MUA vô hiệu.
3. **Mục "Quy đổi điểm ra sản phẩm"** (nằm DƯỚI danh mục gạo):
   - Liệt kê quà: chảo chống dính Sunhouse (đạt **x** điểm), nồi cơm điện Sunhouse (**y** điểm), bộ nồi nấu (**z** điểm). Số điểm/tên/ảnh lấy từ bảng `gifts` (admin sửa được).
   - **Hiển thị rõ số điểm hiện có của khách** để đối chiếu (kèm tiền tương đương: "Bạn có 193 điểm (≈ 3.860đ)").
   - Mỗi quà thể hiện đủ điểm / chưa đủ điểm (còn thiếu bao nhiêu).
   - **Chỉ hiện khi admin BẬT công tắc quy đổi quà. Khi TẮT thì ẩn hoàn toàn** (không để khoảng trống, không chữ "tính năng đang tắt").
4. **Cuối trang (footer)**: Hotline **0918 999 727** (chạm = gọi) · Địa chỉ **35 Trần Đại Nghĩa, phường Đông Hải, tỉnh Khánh Hòa** · nút **Chat Zalo**.

### 5.1 Hộp chọn khi bấm MUA [CHỐT, chi tiết nút = Q9]

Bottom sheet hiện: tên gạo, **mô tả loại gạo**, chọn **quy cách 5 / 10 / 25 kg**, **đơn giá** của quy cách đang chọn, số lượng (mặc định 1), 2 nút **MUA** và **XÁC NHẬN** (cách hiểu mặc định: MUA = thêm vào giỏ và ở lại trang chủ; XÁC NHẬN = thêm vào giỏ rồi đi tới Giỏ hàng).
Quy cách hết tồn kho → vô hiệu hóa (mờ) và ghi "Hết hàng".

## 6. Giỏ hàng & Đặt hàng [CHỐT]

**Giỏ hàng**
- Danh sách dòng hàng (loại gạo – quy cách – đơn giá – số lượng – thành tiền). **Thêm, sửa số lượng, xóa**.
- Giỏ lưu trên máy (và/hoặc server) – giữ lại khi thoát app [MẶC ĐỊNH].
- Kiểm tra tồn kho khi vào bước đặt hàng; thiếu → báo dòng nào, cho sửa.

**Đặt hàng (checkout)** – các phần, theo thứ tự:
1. **Địa chỉ giao**: chọn **1 trong tối đa 2 địa chỉ đã lưu**. Chưa có địa chỉ → bắt thêm. Có nút "Thêm/sửa địa chỉ" (tối đa 2).
2. **Khung giờ giao hàng**: chọn từ danh sách khung giờ admin cấu hình (chỉ hiện khung đang bật). Có chọn **ngày giao** (hôm nay/ngày mai…) [MẶC ĐỊNH: hôm nay và 2 ngày tới; khung giờ đã qua của hôm nay bị ẩn].
3. **Dùng tiền tích lũy** (công tắc, mặc định TẮT) [CHỐT là khách tự chọn; mức dùng = Q3/Q4]:
   - Hiện số dư: "Bạn có 3.860đ tiền tích lũy".
   - Bật → trừ vào đơn số tiền = min(số dư, giá trị hàng). Hiện dòng "Tiền tích lũy: −3.860đ".
   - Số dư = 0 → công tắc mờ.
4. **Ghi chú** cho đơn (tùy chọn).
5. **Tổng kết**: Tiền hàng · Tiền tích lũy đã dùng (nếu có) · **Phí giao: Miễn phí** (hiển thị chữ "Miễn phí", luôn 0đ) · **Tổng thanh toán**.
   - Dòng nhắc: **"Đơn này sẽ tích +X đ"** (X = 2% số tiền khách thực trả, làm tròn theo `docs/05` mục 1). Dòng này giúp khách "biết ngay" (đúng tinh thần "show lên UI 2k luôn").
6. **Phương thức thanh toán**: chỉ **COD – trả tiền khi nhận hàng** (cố định, không có lựa chọn khác).
7. Nút **"Đặt hàng"** → tạo đơn ở trạng thái *Chờ xác nhận* → màn hình thành công (mã đơn, tóm tắt, nút "Xem đơn").

## 7. Đơn hàng [CHỐT]

**Danh sách / lịch sử đơn** (mới nhất trên cùng): mã đơn, ngày đặt, tổng tiền, nhãn trạng thái.

**Chi tiết đơn** hiển thị: **mã đơn · danh sách gạo (loại, quy cách, số lượng, giá) · tổng tiền · địa chỉ giao · khung giờ giao (+ngày) · phương thức thanh toán (COD) · tiền tích lũy đã dùng (nếu có) · tiền tích lũy sẽ được cộng / đã cộng · ghi chú · trạng thái**.

**Trạng thái** (4 bước + hủy): `Chờ xác nhận` → `Đang chuẩn bị` → `Đang giao` → `Hoàn thành`; hoặc `Hủy`. Hiển thị dạng thanh tiến trình.

**Hành động của khách**
- **Hủy đơn**: chỉ khi trạng thái còn **Chờ xác nhận** (admin chưa xác nhận). Hỏi xác nhận; có thể chọn/nhập lý do. Sau khi admin xác nhận thì nút Hủy biến mất (muốn hủy phải gọi hotline).
- **Mua lại**: đưa các dòng hàng của đơn cũ vào giỏ (kiểm tra còn bán/còn hàng, giá lấy **giá hiện tại**, không phải giá cũ).

> Không có push thông báo trạng thái đơn [CHỐT – ngoài phạm vi]. Khi khách mở app, trạng thái phải cập nhật (kéo để làm mới / tự làm mới khi mở màn hình).

## 7b. Màn hình Tích lũy (tab "Tích lũy") [CHỐT: xem điểm & lịch sử; bố cục = MẶC ĐỊNH]

1. **Thẻ số dư**: **"Tiền tích lũy: 3.860đ"** (to) + "(193 điểm)" (nhỏ). Giải thích 1 dòng: *"Mỗi đơn hàng bạn được tích 2% giá trị. Tiền cộng dồn, dùng khi đặt hàng bất cứ lúc nào."*
2. **Lịch sử cộng/trừ** (mới nhất trên cùng). Mỗi dòng: ngày · lý do · `+2.000đ` (xanh) hoặc `−7.000đ` (đỏ) · (điểm) · mã đơn liên quan.
   Lý do: "Tích từ đơn #…", "Dùng cho đơn #…", "Hoàn lại do hủy đơn #…", "Đổi quà: {tên quà}".
3. **Khi admin BẬT quy đổi quà**: khối "Đổi quà tại buổi phát" – xem mục 8. **Khi TẮT**: khối này không tồn tại.

## 8. Đổi quà tại buổi phát [CHỐT; chỉ khi công tắc BẬT]

Luồng:
1. Khách thấy danh sách quà (ở Trang chủ mục "Quy đổi điểm ra sản phẩm" và/hoặc tab Tích lũy).
2. Chọn quà **đủ điểm** → màn xác nhận: tên quà, số điểm cần, số điểm còn lại sau đổi, nhắc *"Quà được phát vào cuối tháng tại buổi phát. Địa điểm và thời gian sẽ được thông báo cụ thể."* → **Gửi yêu cầu đổi**.
3. Yêu cầu có trạng thái **Chờ duyệt**. **Điểm chưa bị trừ** lúc này; hệ thống chỉ cho phép tổng điểm các yêu cầu đang chờ ≤ số dư hiện tại [MẶC ĐỊNH].
4. Admin **duyệt** → trạng thái **Đã đổi** + **trừ điểm** (ghi vào lịch sử "Đổi quà: …"). Admin **từ chối** → trạng thái **Từ chối** (kèm lý do), không trừ điểm [MẶC ĐỊNH].
5. Khách xem danh sách yêu cầu của mình và trạng thái.
6. Địa điểm/giờ phát quà: admin gửi **push notification**; app hiển thị trong màn "Thông báo" (Q15).

Khi công tắc chuyển BẬT → TẮT: ẩn danh sách quà + lựa chọn đổi quà. Các yêu cầu **đang chờ duyệt** vẫn giữ nguyên để admin xử lý (không tự hủy) [MẶC ĐỊNH]; yêu cầu mới không tạo được.

## 9. Tài khoản / Hồ sơ khách hàng [CHỐT]

Khách bổ sung vì "chưa thấy mục hồ sơ khách hàng trong app". Màn hình **Hồ sơ** gồm:
- **Họ tên** (sửa được).
- **Số điện thoại** (hiển thị; **không cho đổi** – là định danh đăng nhập) [MẶC ĐỊNH].
- **Địa chỉ giao hàng** (tối đa 2): xem/thêm/sửa/xóa. Mỗi địa chỉ: nhãn (Nhà/Cơ quan/khác) + địa chỉ chi tiết (số nhà, đường, phường/xã…) + (tuỳ chọn) tên & SĐT người nhận.
- **Điểm tích lũy**: hiển thị tiền **và** điểm (như thẻ số dư), chạm vào → tab Tích lũy.
- **Hỗ trợ**: nút **Gọi hotline** (0918 999 727) và **Chat Zalo**.
- **Đăng xuất**.
- **Xóa tài khoản** (khu vực nguy hiểm cuối trang): hỏi xác nhận 2 bước (cảnh báo mất số dư tích lũy), có thể yêu cầu nhập lại OTP. Sau xóa: đăng xuất, dữ liệu cá nhân bị xóa/ẩn danh (`docs/05` mục 8).

## 10. Hỗ trợ & thông báo [CHỐT]

- Nút **gọi hotline** (mở trình quay số `tel:0918999727`), nút **chat Zalo** (mở link Zalo, Q10) – xuất hiện ở footer Trang chủ và trong Hồ sơ.
- **Push notification** chỉ dùng để báo **địa điểm + thời gian phát quà cuối tháng** (admin soạn). **Không** push trạng thái đơn.
- Xin quyền nhận thông báo ở thời điểm hợp lý (sau đăng nhập), không ép.

## 11. Trạng thái rỗng / lỗi (tối thiểu cần có)

| Tình huống | Hiển thị |
|---|---|
| Giỏ trống | "Giỏ hàng đang trống" + nút "Xem gạo" |
| Chưa có đơn | "Bạn chưa có đơn hàng nào" |
| Chưa có lịch sử điểm | "Chưa có giao dịch tích lũy" |
| Mất mạng | Báo rõ, cho thử lại; không mất giỏ hàng |
| OTP sai/hết hạn | Báo cụ thể, cho gửi lại |
| Lỗi server | Thông báo thân thiện, không lộ chi tiết kỹ thuật |
