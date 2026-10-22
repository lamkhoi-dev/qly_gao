# Tin nhắn của chủ dự án (Bên B) và khách (Bên A) – theo thứ tự thời gian

> Nguyên văn các chỉ đạo của Bên B (chủ dự án, người ký hợp đồng cung cấp dịch vụ) trong quá trình làm hợp đồng,
> cộng nội dung tin nhắn khách làm rõ cuối cùng (ảnh chụp `zalo-khach-lam-ro-2phan-tram.png`).
> Giữ nguyên lỗi chính tả. Đọc kèm `docs/02-lam-ro-va-quyet-dinh.md` (bản đã diễn giải).
> Ghi chú `→` là diễn giải của người ghi hồ sơ, KHÔNG phải lời của Bên B/khách.

---

## 1. Bên B – lúc nhận yêu cầu

> lên cái hd này nha e 16m app gạo
> e cứ coi hiểu đã đi, khoan lên hđ, a sẽ đưa hđ mẫu bên a sau

→ "16m" = 16 triệu VNĐ (sau đó đổi thành 15 triệu).

## 2. Bên B – đưa hợp đồng mẫu + trả lời 10 câu hỏi

> mẫu đây e nhé; làm theo nhé, giá này chưa tinhs vps mõi tháng ~250k; domain 400k 1 năm; otp ( rẻ thôi); phí store ....
>
> 1/ cái này admin sétau
> 2/ đúng r em
> 3/ e cứ để tamj, admin có thể chỉnh nha
> 4/ noti em
> 5/ gps em
> 6/ admin gửi
> 7/ ko + điểm
> 8/ khách đứng tên
> 9/ a sẽ nhờ e tạp promt.md a đưa AI tạo ảnh sau
> 10/ 1 tk nha e

Các câu hỏi tương ứng (do trợ lý đặt) và cách hiểu:

| # | Câu hỏi | Trả lời của Bên B | Cách hiểu |
|---|---|---|---|
| 1 | 1 điểm quy đổi bằng bao nhiêu đồng? | "admin set sau" | Giá trị 1 điểm do admin cấu hình trên Web Admin. (Sau đó khách chốt: tương đương 2% → xem `docs/02`.) |
| 2 | "Áp dụng tức thì" (A.3) vs "áp dụng cho đơn tiếp theo" (A.7) | "đúng rồi em" | Khách dùng tiền tích lũy ngay ở bước giỏ hàng của đơn đang đặt. |
| 3 | Các mốc x/y/z điểm của quà, danh sách + giá 6 loại gạo | "em cứ để tạm, admin có thể chỉnh" | Dev nhập dữ liệu tạm để chạy thử; admin sửa được. |
| 4 | Thông báo địa điểm/thời gian phát quà: push hay trong app? | "noti em" | Push notification. |
| 5 | Xác định vùng Phan Rang bằng GPS hay chọn phường/xã? | "gps em" | Dùng GPS. |
| 6 | Xuất Excel "định kỳ mỗi tuần": tự động hay admin bấm? | "admin gửi" | Admin tự xuất và tự gửi mỗi tuần; hệ thống KHÔNG tự gửi. |
| 7 | Điểm khi đơn bị hủy? | "ko + điểm" | Đơn hủy không được cộng điểm. |
| 8 | Ai đứng tên tài khoản store? | "khách đứng tên" | Tài khoản Google Play / Apple Developer do khách (Bên A) đứng tên. |
| 9 | Nội dung/hình ảnh panel, banner, ảnh gạo | "a sẽ nhờ e tạo prompt.md a đưa AI tạo ảnh sau" | Bên B sẽ tự tạo ảnh bằng AI, cần file `prompt.md` (CHƯA làm). |
| 10 | Tài khoản admin | "1 tk nha e" | Chỉ 1 tài khoản admin. |

Chi phí ngoài giá hợp đồng Bên B nêu: VPS ~250k/tháng; domain 400k/năm; OTP "rẻ thôi"; phí store (Google Play, Apple).

## 3. Bên B – chỉnh hợp đồng lần 1

> sửa lại xíu thời gian hoàn thiện 1.5-2 tuần ko phải 2. tuần
> cọc 4 6 6
> bảo hành 1 năm
> ios mới 99$/năm. dùng chung 80$
> show thêm bảng so sánh phí zalo sms vs otp nha e
>
> ; ghi thêm nộ dung kiểu này nha e; cần xác nhận gì hỏi a nha

→ "dùng chung 80$" = dùng chung tài khoản Apple Developer của Bên B, phí 80 USD/năm (so với 99 USD/năm nếu khách tự lập tài khoản mới). Chi tiết quyền sở hữu app khi dùng chung CHƯA chốt.

## 4. Bên B – khách đã chỉnh bản hợp đồng

> họ có chỉnh tí, e coi nhé

→ Khách sửa giá còn 15.000.000; các đợt 4/5/6 triệu.

## 5. Bên B – chốt giá + đổi flow điểm

> ok e; e giữ 15tr nha; đổi 1 ít này giúp a
> sưa lại xíu nha e
> còn 15m á. đợt 1 ứng 3. đợt 2 như hd. đợt 3 phần còn lại
> sửa lại flow đổi điểm là cứ 1 đơn khách mua là tích dc 2% chiết khấu đơn tiếp theo. ko care là bao nhiêu kg gạo

→ ⚠️ Câu "tích dc 2% chiết khấu đơn tiếp theo" là cách hiểu ban đầu và **ĐÃ BỊ KHÁCH LÀM RÕ LẠI Ở MỤC 7 BÊN DƯỚI**. Đừng làm theo câu này. Dùng mục 7.

## 6. Bên B – chốt lại các đợt thanh toán

> 4 5 6 đi e

→ Đợt 1 = 4 triệu, đợt 2 = 5 triệu, đợt 3 = 6 triệu. Tổng 15 triệu.

## 7. Khách làm rõ luật điểm (NGUỒN SỰ THẬT MỚI NHẤT) + Bên B chuyển tiếp

Ảnh chụp: `zalo-khach-lam-ro-2phan-tram.png`. Phiên âm nguyên văn:

**Khách:**
> note lại chỗ điểm là 2% quy ra tiền mặt luôn cho khách hàng nó biết
>
> vd mua bao gạo 100k => 2% của 100k là 2k => show lên UI 2k luôn
> cứ thế động dồn lên . nó muốn đổi thì đổi ko thì cứ cộng dồn 2% của đơn. đó

**Khách:**
> ko cần sửa lại hd

**Bên B (trả lời trích dẫn tin trên):**
> note để làm th đk a

**Khách:**
> 2% của đơn mua. chứ ko phải -2% đơn tiếp theo nha e

Diễn giải (chi tiết ở `docs/02-lam-ro-va-quyet-dinh.md`):
- Điểm = **2% giá trị đơn mua, quy ra TIỀN MẶT (VNĐ) và hiển thị luôn bằng tiền trên giao diện**. Ví dụ đơn 100.000đ → 2.000đ → UI hiện "2.000đ".
- **Cộng dồn** lên mỗi đơn. Khách muốn đổi (dùng) lúc nào thì đổi, không thì cứ để cộng dồn.
- **KHÔNG phải** "giảm 2% cho đơn tiếp theo".
- **Không cần sửa hợp đồng** (dù chữ trong hợp đồng nói "chiết khấu 2% cho đơn kế tiếp" – đó chỉ là cách nói gần đúng; làm theo tài liệu này).

## 8. Bên B – yêu cầu tài liệu bàn giao (chính là bộ hồ sơ này)

> a tính k code trên máy này nữa, sẽ code trên máy khác; nên e giúp a ghi lại bộ tài liệu để vô máy khác AI anh đọc hiểu 100% nhé; bổ sung thêm cái này để tránh làm e hiểu sai nhé
