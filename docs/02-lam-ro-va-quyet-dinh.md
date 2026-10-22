# 02 – Làm rõ & quyết định (NGUỒN SỰ THẬT MỚI NHẤT)

> **Đọc file này ĐẦU TIÊN sau README.** Khi file này mâu thuẫn với bất kỳ file nào khác
> (yêu cầu gốc của khách, hợp đồng, các docs khác) thì **file này thắng**.
> Nhãn: **[CHỐT]** khách/Bên B đã xác nhận · **[SUY RA]** suy ra từ lời khách, nên xác nhận lại ·
> **[MẶC ĐỊNH]** Bên B chưa nói gì, dev tạm chọn như vầy (đổi được) · **[MỞ]** chưa biết, cần hỏi.

---

## 1. Luật điểm / tiền tích lũy (CHỖ DỄ HIỂU SAI NHẤT)

### 1.1 Câu chốt của khách [CHỐT]

Nguyên văn khách (xem `source/zalo-khach-lam-ro-2phan-tram.png`):

> "điểm là **2% quy ra tiền mặt luôn** cho khách hàng nó biết. vd mua bao gạo 100k => 2% của 100k là 2k => **show lên UI 2k luôn**.
> cứ thế **cộng dồn** lên. nó muốn **đổi** thì đổi, ko thì cứ cộng dồn 2% của đơn."
> "**2% của đơn mua.** chứ ko phải **-2% đơn tiếp theo** nha e"

### 1.2 Diễn giải thành quy tắc cho dev

1. Mỗi đơn **Hoàn thành** → khách được cộng **2% giá trị đơn đó** vào "tiền tích lũy" của khách. [CHỐT]
2. Số tiền này **hiển thị bằng VNĐ** (vd "+2.000đ"), không để khách phải tự nhẩm điểm → tiền. [CHỐT]
3. Tiền tích lũy **cộng dồn** qua các đơn. [CHỐT]
4. Khách **tự chọn** lúc nào dùng ("đổi"). Không dùng thì cứ cộng dồn tiếp. [CHỐT]
5. **KHÔNG** phải kiểu "đơn này mua xong thì đơn sau bị trừ 2% giá". Đó là hiểu sai. [CHỐT]
6. Không phân biệt đơn mua bao nhiêu kg gạo (Bên B: "ko care là bao nhiêu kg gạo"). 2% tính trên **số tiền**, không phụ thuộc kg/quy cách. [CHỐT]
7. Hệ thống **tự tính**, admin **không** cộng/trừ tay (khách: "NÀY APP TÍNH TOÁN TỰ ĐỘNG"). [CHỐT]
8. Đơn bị **hủy** không được cộng. [CHỐT] (Bên B: "ko + điểm")

### 1.3 Quan hệ với "1.000đ = 1 điểm" [SUY RA – cần xác nhận với khách]

Yêu cầu gốc có quy ước **1.000đ = 1 điểm tích lũy** (khách viết HOA, nhấn mạnh) và admin có 2 cấu hình:
(a) bao nhiêu đồng được 1 điểm, (b) giá trị quy đổi 1 điểm ra tiền. Hai quy tắc ghép với "2% = tiền mặt" **khớp nhau** nếu:

```
earn_vnd_per_point = 1000    -- mỗi 1.000đ thanh toán → 1 điểm        (cấu hình admin)
point_value_vnd    = 20      -- 1 điểm = 20đ tiền mặt                 (cấu hình admin)
=> tỷ lệ hoàn = 20 / 1000 = 2%
```

Tức là **"điểm" và "2% tiền mặt" là hai cách nhìn cùng một số dư**:
100.000đ → 100 điểm → 100 × 20đ = **2.000đ**. Đúng ví dụ của khách.

**Cách triển khai khuyên dùng**
- Sổ cái (ledger) lưu bằng **điểm** (số nguyên) – vì quà đổi theo "x/y/z điểm" (xem mục 3).
- Giao diện **luôn hiển thị tiền VNĐ nổi bật** (= điểm × `point_value_vnd`), điểm hiển thị phụ, nhỏ hơn.
  Vd thẻ số dư: **"Tiền tích lũy: 2.000đ"** và dòng nhỏ "(100 điểm)".
- Khi cần ghi chú cho khách ở màn hình đặt hàng/chi tiết đơn: "Đơn này sẽ tích **+2.000đ**".

> ⚠️ Nếu sau này khách nói "không có điểm, chỉ có tiền", đổi sổ cái sang VNĐ trực tiếp với `rate = 2%` là xong; phần còn lại của tài liệu không đổi.

### 1.4 Ví dụ chạy số (dùng làm test case)

Khách mới, số dư ban đầu 0. `earn_vnd_per_point = 1000`, `point_value_vnd = 20`.
"Tính điểm trên số tiền khách **thực trả**" (xem mục 4, câu hỏi mở Q2).

| Đơn | Giá trị hàng | Khách dùng tiền tích lũy | Khách trả (COD) | Điểm cộng khi Hoàn thành | Tiền cộng | Số dư sau (điểm / tiền) |
|---|---|---|---|---|---|---|
| A | 100.000đ | không | 100.000đ | +100 | +2.000đ | 100 điểm / **2.000đ** |
| B | 250.000đ | không | 250.000đ | +250 | +5.000đ | 350 điểm / **7.000đ** |
| C | 200.000đ | dùng hết 7.000đ (350 điểm) | 193.000đ | +193 | +3.860đ | 193 điểm / **3.860đ** |
| D (bị hủy trước khi hoàn thành) | 150.000đ | dùng 3.860đ (193 điểm) | – | **không cộng** | – | điểm đã dùng được **hoàn lại** → 193 điểm / 3.860đ |

### 1.5 Hai cách hiểu SAI cần tránh

| ❌ SAI | ✅ ĐÚNG |
|---|---|
| Mua đơn A xong, đơn B được giảm 2% giá | Mua đơn A (100k) → số dư +2.000đ. Đơn B muốn dùng thì dùng 2.000đ đó, không muốn thì để dồn |
| Chiết khấu tự động áp lên đơn kế tiếp | Khách **chủ động** bật "Dùng tiền tích lũy" ở giỏ hàng |
| Hiển thị "100 điểm" cho khách | Hiển thị "**2.000đ**" (điểm là phụ) |
| Admin có nút cộng/trừ điểm | Không có. Chỉ xem |

### 1.6 Hợp đồng nói khác – và đã được khách đồng ý bỏ qua

Hợp đồng bản mới nhất (`source/Hop_dong_ung_dung_Gao_Phan_Rang.docx`) có câu *"mỗi đơn hàng hoàn thành được tích 01 lượt chiết khấu 2% giá trị đơn hàng tiếp theo"*
và *"chiết khấu 2% cho đơn kế tiếp"*. Đây là **cách nói cũ / gần đúng**, đã bị khách làm rõ ở trên.
Khách nói **"ko cần sửa lại hd"** → **không sửa hợp đồng**; **dev làm theo tài liệu này, không làm theo chữ trong hợp đồng** về luật điểm/chiết khấu.

---

## 2. Các thay đổi theo thời gian (cái nào đã bị thay thế)

| Chủ đề | Phiên bản cũ (ĐỪNG làm) | Phiên bản hiện hành (LÀM) | Nguồn |
|---|---|---|---|
| Cách dùng điểm | "Đổi điểm ra tiền, áp dụng giảm giá cho đơn tiếp theo" (yêu cầu gốc A.7) | Điểm = 2% tiền mặt của đơn mua, cộng dồn, khách tự chọn dùng lúc nào | Mục 1 |
| Luật điểm bản "2% đơn tiếp theo" | "Mỗi đơn tích 2% chiết khấu cho đơn tiếp theo" (tin Bên B + bản hợp đồng) | Xem mục 1 – **2% của đơn mua**, không phải −2% đơn sau | Tin khách |
| Cộng/trừ điểm thủ công | Admin cộng/trừ tay (yêu cầu gốc B) | Bỏ. App tự tính; admin chỉ xem | Khách: "NÀY APP TÍNH TOÁN TỰ ĐỘNG" |
| Thông báo trạng thái đơn | Push khi đổi trạng thái (yêu cầu gốc A.8) | **Bỏ**, nâng cấp sau. Khách vẫn tự xem trạng thái trong app | Khách: "bỏ mục này" |
| Giá trị 1 điểm | "Admin set sau" | Mặc định 20đ/điểm (=2%); admin chỉnh được | Tin Bên B + khách |
| Quà quy đổi | Có | **Vẫn giữ** (công tắc bật/tắt) | Yêu cầu gốc |
| Thời gian | 2–3 tuần (hợp đồng mẫu) | **1,5–2 tuần** | Bên B |
| Bảo hành | 10 năm (mẫu) | **1 năm** | Bên B |
| Giá | 25 triệu (mẫu) → 16 triệu → **15 triệu** | 15.000.000đ, đợt 4/5/6 triệu | Bên B + khách |

---

## 3. Danh sách quyết định đã chốt

### Phạm vi & nền tảng
- [CHỐT] App **iOS + Android** cho khách + **Web Admin** cho chủ cửa hàng. Công nghệ do Bên B chọn.
- [CHỐT] **1 tài khoản admin** duy nhất.
- [CHỐT] Chỉ **COD**. **Miễn phí ship toàn bộ**. Không cổng thanh toán online.
- [CHỐT] Chỉ cho **đăng ký** trong vùng Phan Rang, xác định bằng **GPS**.
- [CHỐT] Đăng ký/đăng nhập bằng **SĐT + OTP**. Kênh gửi OTP (SMS hay Zalo ZNS): [MỞ] xem Q5.
- [CHỐT] Lưu **tối đa 2 địa chỉ**; đặt hàng chọn 1 trong 2.
- [CHỐT] **Xóa tài khoản** trong app.
- [CHỐT] Hồ sơ khách hàng trong app: **tên, SĐT, địa chỉ, điểm tích lũy** (khách bổ sung vì thấy thiếu).
- [CHỐT] 6 loại gạo, quy cách **5 / 10 / 25 kg**. Dữ liệu thật do khách/admin nhập; dev nhập **tạm** để chạy thử.
- [CHỐT] Quà quy đổi: chảo chống dính Sunhouse (x điểm), nồi cơm điện Sunhouse (y điểm), bộ nồi nấu (z điểm). Số điểm do admin chỉnh; dev nhập **tạm**.
- [CHỐT] Công tắc admin **BẬT/TẮT quy đổi quà**: BẬT → hiện mục "Quy đổi điểm ra sản phẩm" + lựa chọn nhận quà ở buổi phát; TẮT → ẩn hoàn toàn. **Dùng tiền tích lũy luôn có, không phụ thuộc công tắc.**
- [CHỐT] Quà phát vào **cuối tháng** ở TTTM/nơi đông người; địa điểm + giờ báo cho khách bằng **push notification** (Bên B: "noti em").
- [CHỐT] Khi duyệt đổi quà: admin duyệt → ghi nhận đã đổi → **trừ điểm** lúc duyệt.
- [CHỐT] Xuất Excel danh sách khách: **admin tự bấm xuất và tự gửi** mỗi tuần. Hệ thống **không** tự gửi mail.
- [CHỐT] Admin cấu hình **khung giờ giao** để khách chọn.
- [CHỐT] Báo cáo: doanh thu, số đơn, mặt hàng bán chạy, khách mua nhiều.
- [CHỐT] In **phiếu giao hàng** từ Web Admin.
- [CHỐT] Slogan: **"Giao hàng không ngại nắng mưa 24/7"**.
- [CHỐT] Hotline **0918 999 727**; địa chỉ **35 Trần Đại Nghĩa, phường Đông Hải, tỉnh Khánh Hòa**.
- [CHỐT] Có nút **gọi hotline** và **chat Zalo**.
- [CHỐT] Khi mở app lần đầu: **2 panel chào** (1: "Đại lý Gạo Phan Rang hân hạnh được phục vụ quý khách"; 2: "Giao hàng tận nơi, quý khách không lo phí ship"). Trang chủ: **4 banner** (Sứ mệnh, Gạo, Tích điểm, Giao hàng).
- [CHỐT] Thông báo khách ngoài vùng: nguyên văn ở `docs/03` mục 3.

### Ngoài phạm vi (KHÔNG làm) [CHỐT]
- Push thông báo **trạng thái đơn hàng** (nâng cấp sau).
- **Phí ship** theo km / admin cấu hình phí ship (nâng cấp sau). Phí ship luôn = 0.
- Thông báo **nhắc sắp hết gạo**.
- **Cổng thanh toán** online.
- Admin **cộng/trừ điểm thủ công**.
- Nhiều tài khoản admin / phân quyền nhân viên.

### Thương mại (để dev biết ràng buộc)
Xem tóm tắt ở `docs/01`. Điểm cần nhớ: **1,5–2 tuần**, ngân sách nhỏ → **đừng over-engineer**.

---

## 4. Câu hỏi mở (chưa ai trả lời) + mặc định tạm dùng

Dev **được phép** chạy tiếp với mặc định; mỗi mặc định đổi được bằng cấu hình hoặc sửa nhỏ. Hỏi lại Bên B khi rảnh.

| # | Câu hỏi | Mặc định tạm dùng |
|---|---|---|
| Q1 | **1 điểm = 20đ (2%)** có đúng ý khách không? (mục 1.3 là suy ra) | Dùng `earn_vnd_per_point=1000`, `point_value_vnd=20`. Cả hai nằm trong cấu hình admin |
| Q2 | 2% tính trên giá trị hàng **trước** hay **sau** khi trừ tiền tích lũy đã dùng? | **Sau** (tính trên số tiền khách thực trả). Tránh vòng lặp "dùng điểm để lấy thêm điểm" |
| Q3 | Có giới hạn khi dùng tiền tích lũy (vd tối đa bao nhiêu % đơn)? | Không giới hạn: dùng tối đa = min(số dư, giá trị hàng). Cho phép trả 0đ nếu số dư đủ |
| Q4 | Khách **chọn số tiền** muốn dùng hay chỉ bật/tắt "dùng hết"? | Công tắc "Dùng tiền tích lũy" = dùng tối đa có thể. (Có thể nâng cấp thành nhập số tiền) |
| Q5 | OTP gửi qua **SMS** hay **Zalo ZNS**? Làm cả hai kênh trong giá 15 triệu? | Viết lớp `OtpProvider` có thể thay kênh. Mặc định **SMS**. Zalo ZNS cần Zalo OA xác thực doanh nghiệp (khách có thể chưa đủ điều kiện) |
| Q6 | iOS: khách tự lập tài khoản Apple (99 USD/năm) hay **dùng chung** tài khoản Bên B (80 USD/năm)? Nếu dùng chung, chuyển app sang tài khoản khách khi nào? | Chưa chốt. Dev cứ build bình thường; bundle ID/team ID để cấu hình |
| Q7 | Giá trị 3 món quà x/y/z và danh sách thật 6 loại gạo + giá + ảnh | Dữ liệu **giả** ở `docs/08`. Admin/khách sửa sau |
| Q8 | Bán kính vùng phục vụ quanh Phan Rang | Tâm = vị trí cửa hàng (xem `docs/08`), bán kính mặc định **10 km**, cấu hình được |
| Q9 | Nút "MUA" và "XÁC NHẬN" trong hộp chọn quy cách: khác nhau thế nào? | **MUA** = thêm vào giỏ; **XÁC NHẬN** = chốt lựa chọn rồi chuyển tới Giỏ hàng. Nếu khách thấy thừa 1 nút thì gộp |
| Q10 | Link chat Zalo là Zalo OA hay số cá nhân? | `https://zalo.me/0918999727` (cấu hình được) |
| Q11 | Nội dung chữ cho banner "SỨ MỆNH" | Chưa có. Dùng chữ giữ chỗ, ghi TODO |
| Q12 | Trừ tồn kho lúc nào: khi đặt hay khi admin xác nhận? | Trừ khi **admin xác nhận**; hoàn kho nếu hủy sau xác nhận |
| Q13 | Khách ra khỏi vùng có đăng nhập tiếp được không? | **Được**. GPS chỉ chặn **tạo tài khoản mới** |
| Q14 | Có kiểm tra địa chỉ giao hàng có nằm trong vùng không? | Không kiểm tra. Chỉ kiểm tra GPS lúc đăng ký |
| Q15 | Có màn hình "Thông báo" (lịch sử thông báo) trong app không? | Có, đơn giản: danh sách thông báo admin đã gửi |
