# Business Requirement Document (BRD)
# AutoWash Pro — Hệ Thống Quản Lý Rửa Xe Ô Tô Thông Minh

**Version:** 2.0 — FINALIZED  
**Ngày hoàn thiện:** 2026-05-21  
**Trạng thái:** ✅ Đã chốt nghiệp vụ — Sẵn sàng thiết kế hệ thống  
**Phạm vi:** Ô tô (Car) — Không áp dụng cho xe máy

---

# 1. Executive Summary

AutoWash Pro là nền tảng quản lý rửa xe ô tô thông minh, tích hợp đặt lịch trực tuyến, quy trình tiếp nhận - kiểm tra xe chuyên nghiệp, chương trình khách hàng thân thiết đa hạng và cơ chế chống spam booking.

Hệ thống phục vụ 4 actor chính: **Customer** (đặt lịch, tích điểm, khiếu nại), **Manager** (quản lý vận hành, xác nhận, tạo booking vãng lai), **Car Washer** (tiếp nhận xe, thực hiện dịch vụ), **Admin** (cấu hình hệ thống, báo cáo).

Điểm khác biệt cốt lõi so với phần mềm quản lý rửa xe thông thường:
- **Loyalty Program 4 hạng** (Member / Silver / Gold / Platinum) với đặc quyền booking window ưu tiên, perks vận hành và kho quà đổi thưởng 3 nhóm.
- **Chống spam booking 4 lớp** (OTP, Rate Limit, Auto-Cancel T-2h, Trust Score).
- **Quy trình kiểm tra xe** (Vehicle Inspection) bắt buộc trước và sau dịch vụ, làm cơ sở xử lý khiếu nại.
- **Thanh toán sau dịch vụ** (Pay After Service) kết hợp Cash + PayOS, có cơ chế kiểm soát rủi ro qua Trust Score.

---

# 2. Business Objectives

## 2.1 Mục tiêu kinh doanh

1. Cho phép khách hàng đặt lịch rửa xe trực tuyến nhanh chóng, minh bạch, với cửa sổ đặt lịch ưu tiên theo hạng thành viên.
2. Giảm triệt để tình trạng slot ma (booking spam), no-show và quá tải vào giờ cao điểm.
3. Xây dựng quy trình tiếp nhận xe chuyên nghiệp có kiểm tra tình trạng xe trước/sau dịch vụ để giảm tranh chấp.
4. Hỗ trợ thanh toán sau dịch vụ an toàn kết hợp Cash và PayOS, có cơ chế kiểm soát rủi ro.
5. Tăng tỷ lệ khách quay lại qua chương trình loyalty 4 hạng với đặc quyền thực tế (booking window, VIP lounge, senior technician).
6. Hỗ trợ vận hành linh hoạt: Manager tạo booking hộ khách vãng lai, phân công Car Washer.
7. Cung cấp báo cáo doanh thu, hiệu suất nhân viên và chất lượng dịch vụ cho ban quản lý.

## 2.2 Mục tiêu hệ thống

1. Quản lý user, role và phân quyền theo từng actor.
2. Quản lý hồ sơ xe của khách theo 3 phân khúc kích thước.
3. Quản lý danh mục dịch vụ, bảng giá theo loại xe, thời lượng ước tính.
4. Quản lý slot theo công thức: `Capacity = min(Active_Bays, floor(Washers_Online / Min_Washers_Per_Car))`.
5. Quản lý booking lifecycle từ SLOT_HELD đến CLOSED với đầy đủ ngoại lệ.
6. Quản lý Vehicle Inspection trước/sau dịch vụ với hình ảnh bằng chứng.
7. Quản lý thanh toán Cash + PayOS sau dịch vụ, kiểm soát qua Trust Score.
8. Quản lý Loyalty: tích điểm, đổi quà, thăng/hạ hạng tự động.
9. Quản lý Trust Score và cơ chế chống spam booking 4 lớp.
10. Quản lý khiếu nại có bằng chứng và audit trail đầy đủ.

---

# 3. Scope

## 3.1 In Scope

### Customer

| # | Chức năng |
|---|---|
| 1 | Đăng ký tài khoản (xác thực OTP qua Email) |
| 2 | Đăng nhập / Đăng xuất |
| 3 | Quản lý thông tin cá nhân |
| 4 | Tạo / Xem / Sửa / Xóa xe cá nhân (nhiều xe) |
| 5 | Xem danh mục dịch vụ và bảng giá theo loại xe |
| 6 | Xem slot trống theo chi nhánh và ngày |
| 7 | Chọn xe, dịch vụ (tự do nhiều dịch vụ), chi nhánh, slot |
| 8 | Xác nhận booking trong thời gian giữ slot (**10 phút**) |
| 9 | Theo dõi trạng thái booking real-time |
| 10 | Xác nhận tình trạng xe trước khi bắt đầu dịch vụ |
| 11 | Kiểm tra và xác nhận xe sau dịch vụ |
| 12 | Thanh toán sau dịch vụ (Cash / PayOS) |
| 13 | Xem điểm tích lũy, hạng thành viên, Trust Score |
| 14 | Đổi điểm lấy quà (Voucher / Free Wash / Add-on) |
| 15 | Gửi khiếu nại gắn với booking |
| 16 | Đánh giá dịch vụ sau khi hoàn tất |
| 17 | Hủy booking (áp dụng quy tắc hủy và trust score) |
| 18 | Xác nhận lịch hẹn qua Email khi nhận nhắc T-2h |

### Manager

| # | Chức năng |
|---|---|
| 1 | Xem dashboard booking theo ngày / chi nhánh / trạng thái |
| 2 | Xác nhận booking khi cần (các trường hợp đặc biệt) |
| 3 | Gọi điện xác nhận booking rủi ro cao |
| 4 | Tạo booking hộ khách vãng lai |
| 5 | Phân công Car Washer cho booking |
| 6 | Hủy hoặc điều chỉnh booking khi cần vận hành |
| 7 | Xử lý khách đến trễ / đánh dấu NO_SHOW |
| 8 | Theo dõi tiến trình dịch vụ real-time |
| 9 | Xem và xử lý khiếu nại |
| 10 | Điều chỉnh điểm loyalty trong trường hợp được phê duyệt |
| 11 | Xem báo cáo vận hành hàng ngày |
| 12 | Unlock tài khoản bị khóa Trust Score (phê duyệt thủ công) |

### Car Washer

| # | Chức năng |
|---|---|
| 1 | Xem danh sách task được phân công trong ngày |
| 2 | Xác nhận khách đã tới cửa hàng (Check-in) |
| 3 | Tạo Vehicle Inspection trước dịch vụ (ghi chú + hình ảnh) |
| 4 | Bắt đầu dịch vụ (sau khi khách xác nhận tình trạng xe) |
| 5 | Cập nhật tiến trình dịch vụ |
| 6 | Hoàn tất dịch vụ |
| 7 | Ghi nhận tình trạng xe sau dịch vụ nếu cần |

### Admin

| # | Chức năng |
|---|---|
| 1 | Quản lý user và role |
| 2 | Quản lý chi nhánh |
| 3 | Quản lý danh mục và bảng giá dịch vụ |
| 4 | Cấu hình slot (số cầu nâng, số washers tối thiểu/xe) |
| 5 | Cấu hình loyalty rules (conversion rate, tier threshold, multiplier, expiry) |
| 6 | Quản lý kho quà đổi điểm (Reward Catalog) |
| 7 | Cấu hình Trust Score thresholds |
| 8 | Xem báo cáo tổng quan |
| 9 | Quản lý cấu hình hệ thống |

## 3.2 Out of Scope (Phiên bản đầu)

1. Tự động phân công Car Washer bằng thuật toán tối ưu.
2. Thanh toán trả trước / đặt cọc.
3. Mobile app native (iOS/Android).
4. AI nhận diện vết xước qua hình ảnh.
5. Dynamic pricing theo giờ cao điểm.
6. CRM marketing automation nâng cao.
7. Fleet management / hợp đồng B2B.
8. Quản lý kho hóa chất / vật tư.
9. Tính lương nhân viên (Payroll).
10. GPS tracking đội xe pick-up/drop-off.
11. Dịch vụ Dán PPF (Báo giá riêng, ngoài luồng booking thông thường).

---

# 4. Stakeholders và Actors

## 4.1 Stakeholders

| Stakeholder | Mục tiêu chính |
|---|---|
| Customer | Đặt lịch nhanh, giá rõ ràng, nhận xe đúng chất lượng, tích điểm quyền lợi thực tế |
| Manager | Quản lý vận hành hàng ngày, phân công nhân viên, xử lý ngoại lệ |
| Car Washer | Biết rõ task, quy trình tiếp nhận xe, cập nhật tiến độ dễ dàng |
| Admin | Cấu hình hệ thống, quản lý dữ liệu nền, xem báo cáo toàn hệ thống |
| Business Owner | Tăng doanh thu, giữ chân khách hàng, giảm tranh chấp, kiểm soát chất lượng |

## 4.2 System Actors

### 4.2.1 Guest
Người chưa đăng nhập. Có thể xem danh mục dịch vụ, bảng giá và slot trống công khai. Có thể đặt lịch lần đầu qua **CTA Phone Number Flow** (nhập SĐT → xác thực OTP) mà không cần tạo tài khoản thủ công — hệ thống tự động tạo **Shadow Account** ngầm. Nếu là khách vãng lai đến trực tiếp tại cửa hàng, Manager tạo booking hộ và cũng sử dụng cùng cơ chế Shadow Account.

### 4.2.2 Customer
Khách hàng có tài khoản đã xác thực OTP. Có thể quản lý xe, đặt lịch, xác nhận tình trạng xe, thanh toán, tích điểm, đổi quà, gửi khiếu nại.

### 4.2.3 Manager
Người quản lý vận hành tại chi nhánh. Chỉ thấy data của chi nhánh được gán. Quản lý booking, phân công Car Washer, xử lý ngoại lệ và khiếu nại.

### 4.2.4 Car Washer
Nhân viên trực tiếp tiếp nhận và thực hiện dịch vụ. Xem task được phân công, thực hiện check-in, inspection, cập nhật trạng thái.

### 4.2.5 Admin
Quản trị viên hệ thống. Quản lý cấu hình toàn bộ: dịch vụ, chi nhánh, user, loyalty rules, trust score threshold, báo cáo.

---

# 5. Assumptions (Giả định)

1. Một Customer có thể sở hữu nhiều xe ô tô.
2. Mỗi booking gắn với đúng một xe cụ thể.
3. Một booking có thể chứa một hoặc nhiều dịch vụ (chọn tự do).
4. Hệ thống tính tổng giá và tổng thời lượng dự kiến dựa trên tổng các dịch vụ đã chọn + loại xe.
5. Slot capacity tính theo công thức: `min(Active_Bays, floor(Washers_Online / Min_Washers_Per_Car))`.
6. Hệ thống giữ slot **10 phút** trong quá trình Customer xác nhận booking.
7. Customer thanh toán sau khi dịch vụ hoàn tất (Cash hoặc PayOS).
8. Một biển số xe chỉ được có tối đa 1 booking "active" trên toàn hệ thống tại cùng thời điểm.
9. Mỗi tài khoản tối đa 2 booking ở trạng thái "Chờ phục vụ" cùng lúc.
10. Hệ thống gửi email nhắc xác nhận theo 2 ngưỡng: (A) Slot từ 10:00 trở đi → gửi tại T-2h, phản hồi trong 30 phút → không phản hồi → AUTO_CANCEL. (B) Slot trước 10:00 → gửi lúc 20:00 hôm trước, phản hồi trước T-1h sáng hôm hẹn → không phản hồi → AUTO_CANCEL.
11. Manager tạo booking hộ khách vãng lai (nhập tối thiểu: tên, SĐT, biển số, dịch vụ, slot). Email của khách là tùy chọn nhưng nếu có thì Claim Account link mới hoạt động được.
12. Điểm tích lũy chỉ được cộng sau khi Payment status = PAID.
13. Tier thành viên tính theo tổng chi tiêu hợp lệ trong 12 tháng gần nhất.
14. Khi đủ ngưỡng tier → thăng hạng ngay lập tức → giữ hạng đúng 12 tháng → review sau 30 ngày ân hạn.
15. Trust Score bắt đầu từ 100 điểm cho tài khoản mới; recovery +5/booking thành công.
16. Trust Score < 60: mất Pay After Service, phải thanh toán bằng PayOS trước khi nhận xe.
17. Trust Score < 20: khóa đặt lịch, cần Manager review và unlock.
18. Platinum được miễn phạt trust score tối đa 2 lần NO_SHOW/CANCEL sát giờ mỗi tháng.
19. Manager chỉ thấy và quản lý data thuộc chi nhánh được gán.
20. **Shadow Account** là tài khoản tự động tạo bởi hệ thống (không có mật khẩu) khi khách đặt lịch lần đầu không qua đăng ký thủ công, hoặc khi Manager tạo booking hộ khách vãng lai. Shadow Account có đầy đủ Trust Score, loyalty tracking và lịch sử booking.
21. Khi khách có Shadow Account nhập SĐT vào CTA, hệ thống nhận diện và **gộp booking mới vào Shadow Account cũ** (không tạo thêm tài khoản mới), đảm bảo điểm tích lũy và Trust Score liên tục.
22. Khi khách nhập SĐT vào CTA mà SĐT đó đã có tài khoản **ACTIVE** → hệ thống **bắt buộc yêu cầu đăng nhập** trước khi đặt lịch. Không cho phép đặt ẩn danh để tránh rủi ro mạo danh biển số và sai lệch Trust Score.
23. Shadow Account được kích hoạt thành **ACTIVE** bằng cách đặt mật khẩu qua link được gửi trong email sau khi thanh toán thành công (**Claim Account Flow**). Toàn bộ điểm, lịch sử và Trust Score được giữ nguyên.
24. Trong luồng Guest, CTA chỉ hỏi SĐT. **Email được thu thập bắt buộc tại trang "Thông tin liên hệ"**, sau khi khách đã chọn kích thước xe, chi nhánh, dịch vụ và slot. Email dùng để gửi OTP, xác nhận lịch, hóa đơn và link Claim Account.
25. **Slot đơn vị = 30 phút.** Hệ thống áp dụng **2 loại booking** dựa trên dịch vụ được chọn:
    - **WASH booking (Type A):** Booking có ít nhất 1 dịch vụ thuộc nhóm **"Rửa xe & Combo"**. Chiếm cầu nâng. Hệ thống tính `num_slots = ceil(total_duration / 30)` và chặn các slot liên tiếp tương ứng.
    - **FLEX booking (Type B):** Booking chỉ gồm dịch vụ nhóm 2–5 (vệ sinh, xử lý bề mặt, bảo vệ...). Không chiếm cầu nâng. Khách chọn **bất kỳ slot nào từ 7:00 – 17:30** mà không bị giới hạn capacity. Manager lên kế hoạch nhân sự thủ công.
26. **FLEX dịch vụ thời gian dài (Coating, PPF, Detailing):** Dịch vụ có `estimated_duration_minutes` > 480 phút (nhiều giờ hoặc nhiều ngày) vẫn đặt slot bình thường theo luồng FLEX. Slot chọn là **giờ hẹn tư vấn**, không phải giờ hoàn thành. Khách hiểu và chấp nhận tính chất dài hạn của dịch vụ. Manager lên lịch thi công chi tiết sau khi tư vấn tại chỗ.
27. **Cross-branch loyalty:** Điểm tích lũy và đổi thưởng (voucher, quà) áp dụng toàn hệ thống, không giới hạn chi nhánh. Quản lý điểm tập trung tại `CustomerProfile`.
28. **Bảng giá dịch vụ thống nhất toàn hệ thống — OUT OF SCOPE v1.0:** `ServicePrice` không có `branch_id`. Tất cả chi nhánh dùng chung bảng giá. Giá theo chi nhánh là tính năng nâng cấp trong phiên bản sau.
29. **Mỗi chi nhánh có slot template, cầu nâng, số washer, và khung giờ hoạt động riêng** (cấu hình qua SlotTemplate + SlotInstance). Manager chi nhánh nhìn thấy danh sách booking sắp tới để chuẩn bị nhân sự và thiết bị phù hợp qua FR-RPT-01 (dashboard booking theo ngày/chi nhánh).

---

# 6. Service Catalog — Danh mục dịch vụ ô tô

## 6.1 Phân loại xe (Vehicle Size)

| Loại xe | Ký hiệu | Ví dụ |
|---|---|---|
| Xe nhỏ (Small) | S | Mini, Hatchback, Sedan (i10, Morning, Mazda 3, Camry...) |
| Xe trung (Medium) | M | SUV, CUV, MPV, Pickup (SantaFe, CX5, Ranger, Everest...) |
| Xe lớn (Large) | L | SUV cỡ lớn (Land Rover, Lexus LX, Explorer, Alphard...) |

> **Quy tắc giá:** Giá dịch vụ được cấu hình riêng cho từng cặp `(service_id, vehicle_size)`. Admin có thể cập nhật bảng giá bất kỳ lúc nào.

## 6.2 Danh mục dịch vụ

> **Phân loại slot logic:**
> - **`WASH`** (🟥 chiếm cầu nâng): Nhóm 1 — Rửa xe & Combo. Cần chặn slot liên tiếp theo tổng thời lượng.
> - **`FLEX`** (🟢 không chiếm cầu nâng): Nhóm 2–5. Khách chọn slot hẹn giờ bất kỳ 7:00–17:30, không bị capacity constraint.

### Nhóm 1: Rửa xe & Combo 🟥 WASH

| Mã | Tên dịch vụ | S | M | L | Thời gian |
|---|---|---|---|---|---|
| SVC-W-01 | VW Basic Wash | 170k | 180k | 190k | 20 phút |
| SVC-W-02 | VW Detail Wash | 270k | 280k | 290k | 20 phút |
| SVC-W-03 | VW Ultimate Wash | 590k | 640k | 690k | 40 phút |
| SVC-W-04 | Rửa xe ngoài | 80k | 90k | 100k | 20 phút |
| SVC-W-05 | Rửa gầm | 40k | 50k | 60k | 20 phút |

### Nhóm 2: Vệ sinh trong 🟢 FLEX

| Mã | Tên dịch vụ | S | M | L | Thời gian ước tính |
|---|---|---|---|---|---|
| SVC-CI-01 | Vệ sinh nội thất Super Clean | 1,200k | 1,400k | 1,600k | 180 phút |
| SVC-CI-02 | Vệ sinh nội thất Ultimate Clean | 1,700k | 1,900k | 2,100k | 240 phút |
| SVC-CI-03 | Vệ sinh nội thất Ultimate Clean Plus | 2,100k | 2,300k | 2,500k | 300 phút |
| SVC-CI-04 | Xử lý vị trí ngồi nội thất — 1 vị trí | 350k | 350k | 350k | 60 phút |
| SVC-CI-05 | Vệ sinh nội soi / dàn lạnh | 1,200k | 1,200k | 1,200k | 120 phút |
| SVC-CI-06 | Vệ sinh nội soi 2 dàn lạnh | 1,700k | 1,700k | 1,700k | 180 phút |
| SVC-CI-07 | Khử mùi bằng công nghệ C-Air Fog | 300k | 300k | 300k | 60 phút |
| SVC-CI-08 | Khử mùi bằng máy Ozone | 200k | 200k | 200k | 60 phút |

### Nhóm 3: Vệ sinh ngoài 🟢 FLEX

| Mã | Tên dịch vụ | S | M | L | Thời gian ước tính |
|---|---|---|---|---|---|
| SVC-CE-01 | Vệ sinh khoang máy | 700k | 800k | 900k | 60 phút |
| SVC-CE-02 | Tẩy nhựa đường | 350k | 400k | 500k | 60 phút |
| SVC-CE-03 | Tẩy bụi sơn, bụi sắt | 600k | 700k | 800k | 90 phút |
| SVC-CE-04 | Tẩy ố kính | 600k | 700k | 800k | 90 phút |
| SVC-CE-05 | Tẩy gầm ô tô | 800k | 900k | 1,000k | 90 phút |
| SVC-CE-06 | Tẩy ố Chrome | 200k | 250k | 300k | 30 phút |
| SVC-CE-07 | Tẩy nhựa cây | 200k | 250k | 300k | 30 phút |
| SVC-CE-08 | Vệ sinh mâm bánh lazang trong ngoài — 4 bánh | 450k | 500k | 550k | 60 phút |

### Nhóm 4: Xử lý bề mặt 🟢 FLEX

| Mã | Tên dịch vụ | S | M | L | Thời gian ước tính |
|---|---|---|---|---|---|
| SVC-SP-01 | Đánh bóng sơn xe Basic | 1,300k | 1,500k | 1,700k | 180 phút |
| SVC-SP-02 | Đánh bóng sơn xe hiệu chỉnh | 2,000k | 2,200k | 2,400k | 300 phút |
| SVC-SP-03 | Đánh bóng kính lái / lưng — 1 tấm | 1,700k | 1,800k | 1,900k | 120 phút |
| SVC-SP-04 | Đánh bóng kính sườn ô tô — 1 tấm | 1,100k | 1,200k | 1,300k | 90 phút |
| SVC-SP-05 | Đánh bóng đèn pha ô tô — 1 cặp | 800k | 900k | 1,000k | 90 phút |
| SVC-SP-06 | Wax bóng sáp sơn xe | 500k | 600k | 700k | 60 phút |

### Nhóm 5: Bảo vệ 🟢 FLEX

| Mã | Tên dịch vụ | S | M | L | Thời gian ước tính |
|---|---|---|---|---|---|
| SVC-PR-01 | Phủ gầm ô tô | 3,000k | 3,500k | 4,000k | 120 phút |
| SVC-PR-02 | Pro Coating — Ceramic 2 lớp | 7,500k | 8,500k | 9,500k | 480 phút |
| SVC-PR-03 | Ultimate Coating — Ceramic 3 lớp | 8,500k | 9,500k | 10,000k | 480 phút |
| SVC-PR-04 | Phủ Nano kính | 1,200k | 1,300k | 1,400k | 90 phút |
| SVC-PR-05 | PPF Dopon Save Protection 7.5 mil | 20,000k | 21,000k | 23,000k | 480 phút |
| SVC-PR-06 | PPF Dopon Shining 7.5 mil | 28,000k | 30,000k | 32,000k | 480 phút |
| SVC-PR-07 | PPF Tecwrap 6.5 mil | 28,000k | 30,000k | 32,000k | 480 phút |
| SVC-PR-08 | PPF Tecwrap M75 7.5 mil | 40,000k | 42,000k | 45,000k | 480 phút |
| SVC-PR-09 | Phìm cách nhiệt 3M Crystalline | 13,900k | 15,600k | 15,600k | 480 phút |

> **Lưu ý vận hành:** Thời gian dịch vụ FLEX (nhóm 2–5) là ước tính tham khảo, Manager có thể điều chỉnh thực tế. Dịch vụ Coating/PPF có thể mất nhiều ngày — Manager tạo booking thủ công (Walk-in style) cho những trường hợp này.

---

# 7. Loyalty Program — Chương trình Khách hàng Thân thiết

## 7.1 Membership Tiers

| Hạng | Tổng chi tiêu 12 tháng gần nhất | Booking Window | Multiplier |
|---|---|---|---|
| **Member** | 0 – 4,999,999 đ | **3 ngày** trước | × 1.0 |
| **Silver** | 5,000,000 – 14,999,999 đ | 7 ngày trước | × 1.0 |
| **Gold** | 15,000,000 – 39,999,999 đ | 10 ngày trước | × 1.2 |
| **Platinum** | ≥ 40,000,000 đ | 14 ngày trước | × 1.5 |

## 7.2 Quy trình Thăng/Hạ hạng

```
Chi tiêu đạt ngưỡng tier mới
→ Thăng hạng ngay lập tức
→ Giữ hạng đúng 12 tháng kể từ ngày thăng
→ Sau 12 tháng: Bắt đầu 30 ngày ân hạn (Grace Period)
   → Hệ thống quét tổng chi tiêu 12 tháng vừa qua
   → Kết quả: DUY TRÌ / LÊN HẠNG TIẾP / HẠ HẠNG
→ Sau 30 ngày ân hạn: Áp dụng hạng mới chính thức
```

**Ví dụ:**
- Khách đạt Gold (15M) vào ngày 01/03/2026 → giữ Gold đến 01/03/2027.
- Ngày 01/03/2027: bắt đầu 30 ngày ân hạn. Hệ thống quét chi tiêu từ 01/03/2026 → 01/03/2027.
- Nếu tổng đạt 40M → thăng Platinum. Nếu còn 20M → tiếp tục Gold. Nếu chỉ 8M → hạ về Silver. Nếu chỉ 3M → hạ về Member.
- Quyết định chính thức áp dụng từ ngày 31/03/2027.

## 7.3 Tier Perks — Đặc quyền từng hạng

> **Quy tắc kế thừa:** Mọi hạng cao hơn đều bảo lưu toàn bộ đặc quyền của hạng thấp hơn và bổ sung thêm quyền riêng.

### 🪴 Member — Hạng cơ bản

| Đặc quyền | Chi tiết |
|---|---|
| Booking window | Đặt lịch trước tối đa **3 ngày** |
| Tích điểm | × 1.0 |
| Hóa đơn điện tử | Nhận hóa đơn sau mỗi lần sử dụng dịch vụ qua Email |
| Lịch sử booking | Xem và tải lịch sử booking online |
| Hỗ trợ | Hotline tiêu chuẩn |

### 🥈 Silver — Nhóm trải nghiệm

| Đặc quyền | Chi tiết |
|---|---|
| Booking window | Đặt lịch trước tối đa **7 ngày** |
| Tích điểm | × 1.0 |
| Quà sinh nhật | Mã giảm **10%** dịch vụ bất kỳ, valid cả tháng sinh nhật |
| Nhận khuyến mãi sớm | Thông báo chương trình ưu đãi **trước 24h** so with Member |
| Hỗ trợ ưu tiên | Hotline ưu tiên (không xếp hàng chung với Member) |

### 🥇 Gold — Nhóm Trung thành Trung cấp

| Đặc quyền | Chi tiết |
|---|---|
| Booking window | Đặt lịch trước tối đa **10 ngày** |
| Tích điểm | × **1.2** |
| Quà sinh nhật | Tặng **1 suất Rửa xe Full Premium** miễn phí (dùng trong tháng sinh nhật) |
| Ưu tiên cầu nâng | Ưu tiên đưa xe vào bốt trước khi trạm quá tải |
| Phòng chờ | Miễn phí **1 phần nước pha chế** (cà phê / trà sữa) tại sảnh chờ |
| Giảm giá Detailing | Giảm **5%** tổng hóa đơn cho dịch vụ nhóm Detailing & Paint Protection |
| Post-Inspection Report | Nhận báo cáo tình trạng xe đầy đủ sau dịch vụ qua thông báo điện tử (kèm ảnh) |

### 💎 Platinum — Nhóm Thượng lưu / VIP

| Đặc quyền | Chi tiết |
|---|---|
| Booking window | Đặt lịch trước tối đa **14 ngày** |
| Tích điểm | × **1.5** |
| Miễn phạt | Miễn phạt Trust Score tối đa **2 lần NO_SHOW/CANCEL sát giờ / tháng** |
| Giảm giá toàn bộ | Giảm **10%** tổng hóa đơn mọi dịch vụ |
| VIP Lounge | Phòng chờ riêng biệt, yên tĩnh, ghế massage miễn phí |
| Senior Technician | Xe được đích thân **Tổ trưởng kỹ thuật** thi công và kiểm định (QC) trước bàn giao |
| Pick-up / Drop-off | Hỗ trợ đón/giao xe tận nhà trong bán kính **3km**, miễn phí hoặc phí ưu đãi |
| Ưu tiên khiếu nại | Xử lý khiếu nại trong **24 giờ** (SLA nhanh gấp 3 lần so với hạng khác) |
| Hotline VIP | Đường dây riêng, nối thẳng Manager chi nhánh (không qua tổng đài) |
| Thông báo sớm nhất | Ưu tiên nhận thông tin dịch vụ mới & gói ưu đãi đặc biệt trước mọi hạng |

## 7.4 Công thức Tích điểm

```
Base_Points  = floor(Paid_Amount / Conversion_Rate)
Final_Points = floor(Base_Points × Tier_Multiplier)
```

**Tham số cấu hình (Admin chỉnh được toàn bộ):**

| Tham số | Giá trị mặc định |
|---|---|
| Conversion Rate | 10,000 VND = 1 điểm |
| Member Multiplier | × 1.0 |
| Silver Multiplier | × 1.0 |
| Gold Multiplier | × 1.2 |
| Platinum Multiplier | × 1.5 |
| Point Expiry | 12 tháng không có giao dịch mới thì điểm expire |

**Ví dụ minh họa:**

| Hóa đơn | Hạng | Tính | Điểm nhận |
|---|---|---|---|
| 250,000đ | Member | (250,000 ÷ 10,000) × 1.0 | **25 điểm** |
| 250,000đ | Gold | (250,000 ÷ 10,000) × 1.2 | **30 điểm** |
| 1,000,000đ | Platinum | (1,000,000 ÷ 10,000) × 1.5 | **150 điểm** |

## 7.5 Reward Catalog — Kho quà đổi điểm

### Nhóm 1: Voucher Giảm giá (Discount)

| Quà | Điểm cần | Giá trị |
|---|---|---|
| Voucher giảm 20,000đ | 220 điểm | ~9% |
| Voucher giảm 50,000đ | 530 điểm | ~9.4% |
| Voucher giảm 100,000đ | 1,000 điểm | ~10% |

### Nhóm 2: Free Wash (Rửa xe miễn phí)

| Quà | Điểm cần | Tương đương |
|---|---|---|
| 1 suất Gói Full Basic (xe Small) | 1,800 điểm | ~150,000đ |
| 1 suất Gói Full Basic (xe Medium) | 1,900 điểm | ~160,000đ |
| 1 suất Gói Full Basic (xe Large) | 2,000 điểm | ~170,000đ |

### Nhóm 3: Add-on Services (Dịch vụ cộng thêm miễn phí)

| Quà | Điểm cần | Tương đương |
|---|---|---|
| Tẩy ố kính lái | 2,200 điểm | ~200,000đ |
| Khử mùi Ozone nội thất | 1,600 điểm | ~150,000đ |
| Xịt dưỡng lốp | 600 điểm | ~50,000đ |
| Châm nước rửa kính | 250 điểm | ~20,000đ |

> **Tỷ lệ hoàn vốn điểm ≈ 9–10%** — mức chuẩn ngành, đủ hấp dẫn nhưng không tạo lỗ cho hệ thống.

---

# 8. Anti-Spam Booking — Cơ chế Chống Spam 4 Lớp

## Lớp 1: Xác thực Danh tính (Identity Verification)

- **OTP bắt buộc cho Guest:** Sau khi nhập thông tin liên hệ (tên, email, biển số), hệ thống gửi OTP qua **Email** để xác thực danh tính.
- **OTP cho tài khoản ACTIVE:** Xác thực OTP qua **Email** khi đăng ký. Số điện thoại và email gắn với account ID vĩnh viễn.
- **Biển số xe ràng buộc:** Hệ thống bắt buộc nhập biển số hợp lệ. **Một biển số xe tại một thời điểm chỉ được có tối đa 1 booking active** trên toàn hệ thống.

## Lớp 2: Giới hạn Đặt lịch (Rate Limiting & Booking Cap)

- **IP & Device Limit:** Nếu phát hiện 1 IP hoặc 1 thiết bị thực hiện quá **3 booking thành công trong 10 phút** → tạm khóa tính năng đặt lịch trên IP/device đó.
- **Max Active Booking:** Một tài khoản chỉ được phép có tối đa **2 booking ở trạng thái Pending/Confirmed** cùng lúc. Muốn đặt tiếp, phải hoàn thành hoặc hủy lịch cũ.

## Lớp 3: Nhắc nhở & Tự động hủy trước giờ hẹn (T-2h Auto-Cancel)

```
T - 2 giờ trước giờ hẹn:
  → Hệ thống gửi Email với 2 nút: [XÁC NHẬN SẼ ĐẾN] | [HỦY LỊCH]
  → Khách có 30 phút để phản hồi

Nếu khách nhấn [XÁC NHẬN SẼ ĐẾN]:
  → Booking giữ nguyên trạng thái CONFIRMED
  → Không bị phạt Trust Score

Nếu khách nhấn [HỦY LỊCH]:
  → Booking chuyển CANCELLED_BY_CUSTOMER
  → Trừ 20 Trust Score (vì hủy < 2h)

Nếu không phản hồi sau 30 phút:
  → Booking tự động chuyển AUTO_CANCELLED
  → Slot được giải phóng ngay lập tức
  → Trừ 20 Trust Score
```

## Lớp 4: Trust Score — Điểm Uy tín

| Sự kiện | Thay đổi Trust Score |
|---|---|
| Tài khoản mới tạo | Bắt đầu: **100 điểm** |
| Hoàn thành booking thành công | **+5 điểm** (tối đa 100) |
| NO_SHOW (không đến) | **−40 điểm** |
| AUTO_CANCELLED (không confirm T-2h) | **−20 điểm** |
| CANCEL sát giờ (< 30 phút trước) | **−20 điểm** |
| CANCEL sớm (≥ 2 giờ trước) | **0 điểm** (không phạt) |
| Platinum miễn phạt | Tối đa **2 lần/tháng** không bị trừ điểm |

**Ngưỡng kích hoạt chế tài:**

| Trust Score | Chế tài |
|---|---|
| 60 – 100 | Bình thường, Pay After Service được phép |
| < 60 | **Mất Pay After Service** — Phải thanh toán PayOS trước khi nhận xe |
| < 20 | **Khóa đặt lịch** — Cần Manager xem xét và unlock thủ công |

---

# 9. Slot Logic

## 9.1 Slot Unit & Capacity Formula

**Mỗi slot = 30 phút** (7:00, 7:30, 8:00 … 17:30). Hệ thống tạo SlotInstance tương ứng theo template của từng chi nhánh.

```
Slot_Capacity = min(
    Active_Bays,
    floor(Car_Washers_Online / Min_Washers_Per_Car)
)
```

**Trong đó:**
- `Active_Bays`: Số cầu nâng / bốt rửa đang hoạt động tại chi nhánh trong ca đó (Admin/Manager cấu hình).
- `Car_Washers_Online`: Số Car Washer được lên lịch làm việc trong ca đó.
- `Min_Washers_Per_Car`: Số Car Washer tối thiểu cần thiết để phục vụ 1 xe cùng lúc (mặc định = 1, Admin cấu hình).

## 9.2 2-Tier Booking Slot Logic

Hệ thống phân biệt 2 loại booking dựa trên dịch vụ được chọn:

### Type A — WASH Booking (Chiếm cầu nâng)

> **Điều kiện:** Booking có ít nhất 1 dịch vụ thuộc nhóm **"Rửa xe & Combo"** (Nhóm 1).

```
total_duration = SUM(estimated_duration_minutes) của tất cả dịch vụ được chọn
num_slots      = ceil(total_duration / 30)
```

- Hệ thống **chặn `num_slots` slot liên tiếp** bắt đầu từ `start_slot_id`.
- Tất cả `num_slots` slot liên tiếp đó phải có `(booked_count + held_count) < capacity`.
- Khi booking CONFIRMED: `booked_count += 1` trên mỗi slot trong chuỗi.
- Khi booking CANCELLED/EXPIRED: `booked_count -= 1` trên mỗi slot trong chuỗi.
- Hệ thống **chỉ hiển thị** slot bắt đầu sao cho toàn bộ `num_slots` liên tiếp đều khả dụng.

> ⚠️ *Lưu ý:* Nếu khách chọn cả WASH + FLEX trong cùng 1 booking → booking được phân loại là **WASH** (Type A). Tổng thời lượng = tổng tất cả dịch vụ.

### Type B — FLEX Booking (Không chiếm cầu nâng)

> **Điều kiện:** Booking **chỉ** gồm dịch vụ Nhóm 2–5 (không có dịch vụ nào thuộc Nhóm 1).

- Khách chọn **bất kỳ slot nào** trong khoảng 7:00 – 17:30 (vẫn áp dụng rule ≥ 2h từ hiện tại).
- **Không kiểm tra** `booked_count` vs `capacity` của SlotInstance → không chiếm cầu nâng.
- `booked_count` trên SlotInstance **không thay đổi** khi có FLEX booking.
- Booking vẫn có `start_slot_id` (giờ hẹn) và `num_slots = 1` (chỉ để tracking giờ hẹn).
- Manager quản lý workload FLEX thủ công qua dashboard danh sách FLEX booking theo ngày.

---

# 10. Business Process Flows

## 10.1 Online Booking Flow

```
═══════════════════════════════════════════════════
BƯỚC 0 — Trang đặt lịch (CTA Phone Number)
═══════════════════════════════════════════════════
→ Khách nhập SĐT vào ô CTA chính ("Đặt lịch ngay")
→ Hệ thống kiểm tra SĐT trong Database:

   ┌── Case A: SĐT chưa tồn tại
   │   → Hệ thống tạo Shadow Account mới (status=SHADOW, không có mật khẩu)
   │   → Tiếp tục → Luồng Guest (xem bên dưới)
   │
   ├── Case B: SĐT đã là Shadow Account
   │   → Hệ thống nhận diện, gộp booking vào Shadow Account cũ
   │   → Tiếp tục → Luồng Guest (xem bên dưới)
   │
   └── Case C: SĐT đã có tài khoản ACTIVE
       → Popup: "Số điện thoại này đã có tài khoản thành viên.
                 Vui lòng đăng nhập để tích điểm và dùng đặc quyền thành viên."
       → [Đăng nhập ngay] → Tiếp tục → Luồng Logged-in (xem bên dưới)

═══════════════════════════════════════════════════
LUỒNG GUEST — Shadow Account (Case A & B)
═══════════════════════════════════════════════════
BƯỜC 1 — Chọn dịch vụ
→ Khách chọn kích thước xe (S/M/L)
→ Khách chọn chi nhánh
→ Khách chọn dịch vụ (tự do 1 hoặc nhiều)
   → Có thể áp Voucher nếu Shadow Account cũ đã có điểm
→ Hệ thống phân loại booking:
   - **WASH (Type A):** có dịch vụ Nhóm 1 → tính `num_slots = ceil(total_duration/30)`
     → Hiển thị slot có ít nhất `num_slots` slot liên tiếp còn trống (từ hiện tại + 2h)
   - **FLEX (Type B):** không có dịch vụ Nhóm 1 → hiển thị mọi slot từ 7:00–17:30 (từ hiện tại + 2h)
→ Hệ thống tính tổng giá dự kiến + tổng thời lượng dự kiến

BƯỜC 2 — Thông tin liên hệ
→ Khách nhập: Họ tên (bắt buộc), **Email (bắt buộc)**, Biển số xe (bắt buộc)
   (Email dùng để gửi OTP, xác nhận lịch, hóa đơn và link Claim Account)

BƯỜC 3 — Xác thực OTP
→ Hệ thống gửi OTP qua **Email** vừa nhập
→ Khách nhập OTP → Hệ thống xác thực danh tính

BƯỜC 4 — Xác nhận booking
→ Hệ thống kiểm tra: biển số đang active? rate limit (IP)? max active booking?
→ Hệ thống giữ slot 10 phút (SLOT_HELD)
→ Khách review toàn bộ thông tin → Xác nhận → Booking tạo (CONFIRMED)
→ Hệ thống lưu Email vào Shadow Account
→ Hệ thống gửi Email xác nhận lịch hẹn

**Nhắc xác nhận (2-tier theo giờ hẹn):**
→ Nếu slot **≥ 10:00**: gửi Email tại T-2h + nút [XÁC NHẬN SẼ ĐẾN] | [HỦY LỊCH]
   → Không phản hồi trong **30 phút** → AUTO_CANCELLED (-20 Trust Score)
→ Nếu slot **< 10:00**: gửi Email lúc **20:00 hôm trước** + nút [XÁC NHẬN SẼ ĐẾN] | [HỦY LỊCH]
   → Không phản hồi trước **T-1h sáng hôm hẹn** → AUTO_CANCELLED (-20 Trust Score)

Khách đến → Car Washer check-in → Inspection → Dịch vụ → COMPLETED
→ Khách thanh toán (Cash hoặc PayOS) → Payment PAID → Booking CLOSED
→ Hệ thống cộng điểm vào Shadow Account

[Claim Account Flow — Sau khi PAID]:
→ Hệ thống tự động gửi **Email**:
   "Cảm ơn bạn! Bạn vừa tích được X điểm (hạng **Member**).
    Kích hoạt tài khoản để dùng điểm đổi quà và nhận đặc quyền thành viên!"
   [Link kích hoạt — hết hạn sau 7 ngày]
→ Khách click link → Đặt mật khẩu mới
→ Tài khoản: SHADOW → ACTIVE
→ Toàn bộ điểm, lịch sử, Trust Score được giữ nguyên

═══════════════════════════════════════════════════
LUỒNG LOGGED-IN — Tài khoản ACTIVE (Case C)
═══════════════════════════════════════════════════
→ Khách đăng nhập thành công
→ Chọn xe từ danh sách xe đã đăng ký (hoặc thêm xe mới)
→ Khách chọn chi nhánh, dịch vụ (tự do 1 hoặc nhiều), slot trống
   (Slot hiển thị theo booking window của tier: Member/Silver/Gold/Platinum)
   (Logic WASH/FLEX giống luồng Guest: WASH → slot liên tiếp; FLEX → bất kỳ slot 7:00–17:30)
   → Có thể áp Voucher/quà đổi điểm tại bước chọn dịch vụ
→ Hệ thống tính tổng giá dự kiến + thời lượng dự kiến
→ Hệ thống kiểm tra: biển số active? max booking (2)? rate limit?
→ Hệ thống giữ slot **10 phút** (SLOT_HELD)
→ Khách xác nhận booking
→ Booking tạo (PENDING_CONFIRMATION hoặc CONFIRMED tùy rule BR-BOOK-07)
→ [Nếu cần] Manager xác nhận → CONFIRMED
→ Manager phân công Car Washer → ASSIGNED
→ Hệ thống gửi Email xác nhận

**Nhắc xác nhận (2-tier theo giờ hẹn):**
→ Nếu slot **≥ 10:00**: gửi Email tại T-2h + nút [XÁC NHẬN SẼ ĐẾN] | [HỦY LỊCH]
   → Không phản hồi trong **30 phút** → AUTO_CANCELLED (-20 Trust Score)
→ Nếu slot **< 10:00**: gửi Email lúc **20:00 hôm trước** + nút [XÁC NHẬN SẼ ĐẾN] | [HỦY LỊCH]
   → Không phản hồi trước **T-1h sáng hôm hẹn** → AUTO_CANCELLED (-20 Trust Score)

Khách đến → Car Washer check-in → Inspection → Dịch vụ → COMPLETED
→ Khách kiểm tra xe
→ Khách thanh toán (Cash hoặc PayOS) → PAID
→ Hệ thống cộng điểm loyalty (theo tier multiplier)
→ Hệ thống cập nhật tổng chi tiêu, recalculate tier nếu cần
→ Booking CLOSED
→ Khách có thể đánh giá hoặc gửi khiếu nại
```

## 10.2 Walk-in Booking Flow (Khách vãng lai)

> **Lưu ý kỹ thuật:** Walk-in Booking sử dụng **chính xác cùng cơ chế Shadow Account** như luồng Guest online. Manager là người thao tác thay khách hàng.

```
Khách vãng lai đến cửa hàng
→ Manager mở form tạo booking nhanh
→ Manager nhập SĐT của khách
→ Hệ thống kiểm tra SĐT:

   ├── Case A: SĐT chưa tồn tại
   │   → Tạo Shadow Account mới tự động
   │
   ├── Case B: SĐT đã là Shadow Account
   │   → Gộp booking mới vào Shadow Account cũ
   │
   └── Case C: SĐT đã có tài khoản ACTIVE
       → Gắn booking trực tiếp vào tài khoản đó
       → Điểm loyalty được cộng đúng vào tài khoản Active

→ Manager nhập thêm: tên khách, biển số, loại xe, dịch vụ, slot
→ Manager phân công Car Washer
→ Booking chuyển thẳng ASSIGNED (bỏ qua PENDING_CONFIRMATION)
→ Car Washer thực hiện check-in, inspection, dịch vụ
→ Khách kiểm tra xe, thanh toán
→ Nếu Case A/B (Shadow): hệ thống cộng điểm vào Shadow Account
→ Nếu Case C (Active): hệ thống cộng điểm vào tài khoản Active
→ [Claim Account Flow]: Sau PAID, gửi link kích hoạt nếu là Shadow Account
```

## 10.3 Vehicle Inspection Flow

```
Car Washer xác nhận khách đã tới (Check-in)
→ Car Washer mở form Inspection trước dịch vụ (Pre-service)
→ Ghi nhận: tình trạng ngoại thất, nội thất (nếu có dịch vụ liên quan)
→ Ghi chú các vết xước, móp, nứt, mất phụ kiện, bất thường
→ Upload hình ảnh xe trước dịch vụ (bắt buộc ít nhất 1 ảnh)
→ Car Washer submit inspection
→ Customer nhận thông báo / xem trực tiếp tại màn hình kiosk
→ Customer xác nhận "Đồng ý với tình trạng xe được ghi nhận"
→ Dịch vụ được phép bắt đầu

Sau dịch vụ (Post-service Inspection — optional nhưng khuyến nghị):
→ Car Washer ghi nhận tình trạng xe sau dịch vụ
→ Upload hình ảnh xe sau dịch vụ
→ Customer kiểm tra xe thực tế
→ Customer xác nhận "Nhận xe đạt yêu cầu" / hoặc ghi nhận vấn đề
→ Nếu có tranh chấp → Customer tạo Complaint gắn với Booking + Inspection
```

## 10.4 Payment Flow

```
Dịch vụ COMPLETED
→ Hệ thống tạo Payment record (UNPAID)
→ Hệ thống tính final_total_price (dịch vụ thực tế + phụ phí nếu có - discount nếu có)
→ Kiểm tra Trust Score của Customer:
   - Trust Score ≥ 60: Cho phép Pay After Service (Cash hoặc PayOS)
   - Trust Score < 60: Bắt buộc thanh toán PayOS trước khi rời cửa hàng

Customer chọn phương thức:
   CASH:
   → Thu ngân ghi nhận nhận tiền mặt
   → Payment PENDING → PAID
   → Hệ thống cộng điểm + update spending
   → Booking CLOSED

   PAYOS:
   → Hệ thống tạo payment link / QR code PayOS
   → Customer quét/click thanh toán
   → PayOS callback → Payment PAID
   → Hệ thống cộng điểm + update spending
   → Booking CLOSED
```

## 10.5 Complaint Flow

```
Customer tạo khiếu nại liên quan đến booking
→ Nhập tiêu đề, mô tả vấn đề, upload bằng chứng (ảnh/video)
→ Complaint status: OPEN
→ Manager nhận thông báo, tiếp nhận complaint
→ Manager xem: Booking detail, Pre/Post Inspection, hình ảnh, Payment, nhân viên phụ trách
→ Manager phản hồi:
   - Cần thêm thông tin → WAITING_FOR_CUSTOMER
   - Có đủ thông tin → IN_REVIEW
→ Manager đưa ra hướng xử lý:
   - Chấp nhận → RESOLVED (kèm ghi chú: hoàn tiền / điểm bù / xin lỗi)
   - Từ chối → REJECTED (kèm lý do)
→ Customer xác nhận → CLOSED
```

## 10.6 Cancellation Flow

```
Customer muốn hủy booking (trạng thái CONFIRMED hoặc ASSIGNED):

Nếu hủy trước giờ hẹn ≥ 2 giờ:
→ Booking → CANCELLED_BY_CUSTOMER
→ Slot được giải phóng
→ Trust Score: KHÔNG bị trừ điểm
→ Hệ thống gửi email xác nhận hủy

Nếu hủy trong khoảng 30 phút — 2 giờ trước giờ hẹn (trong cửa sổ T-2h, chưa confirm):
→ Booking → CANCELLED_BY_CUSTOMER
→ Slot được giải phóng
→ Trust Score: -20 điểm
→ [Nếu Platinum & còn miễn phạt trong tháng]: Không bị trừ điểm

Nếu hủy trong vòng 30 phút trước giờ hẹn (chưa confirm T-2h):
→ Booking → CANCELLED_BY_CUSTOMER
→ Slot được giải phóng
→ Trust Score: -20 điểm
→ [Nếu Platinum & còn miễn phạt trong tháng]: Không bị trừ điểm

**[SAU KHI KHÁCH XÁC NHẬN SỬ ĐẾỬN tại T-2h]:**
→ Nút hủy trên web/app bị vô hiệu hóa (disabled)
→ Khách KHÔNG thể tự hủy online
→ Nếu có sự cố khẩn cấp: gọi điện cho Manager → Manager hủy thủ công (CANCELLED_BY_MANAGER) -20 Trust Score (không ngoại lệ, trừ Platinum)

Nếu không hủy mà không đến (NO_SHOW):
→ Manager đánh dấu NO_SHOW sau ngưỡng **10 phút** trễ
→ Booking → NO_SHOW
→ Slot được giải phóng
→ Trust Score: -40 điểm
→ [Nếu Platinum & còn miễn phạt trong tháng]: Không bị trừ điểm
```

---

# 11. Functional Requirements

## 11.1 Authentication & Authorization

| ID | Yêu cầu |
|---|---|
| FR-AUTH-01 | Guest đặt lịch lần đầu: hệ thống gửi OTP xác thực qua **Email** (nhập ở trang thông tin liên hệ). Customer đăng ký tài khoản ACTIVE: OTP xác thực qua Email. |
| FR-AUTH-02 | Đăng nhập bằng email + mật khẩu. |
| FR-AUTH-03 | Phân quyền theo role: CUSTOMER / MANAGER / CAR_WASHER / ADMIN. |
| FR-AUTH-04 | Manager chỉ thấy data của chi nhánh được gán. |
| FR-AUTH-05 | Session management, đăng xuất. |
| FR-AUTH-06 | **CTA Phone Number Check:** Khi khách nhập SĐT vào ô đặt lịch nhanh, hệ thống kiểm tra: (A) SĐT chưa tồn tại → tạo Shadow Account mới, tiếp tục đặt; (B) SĐT là Shadow Account → gộp vào Shadow cũ, tiếp tục đặt; (C) SĐT là tài khoản ACTIVE → bắt buộc đăng nhập trước khi đặt. |
| FR-AUTH-07 | **Claim Account Flow:** Sau khi Shadow Account có Payment PAID, hệ thống tự động gửi **Email** chứa link kích hoạt tài khoản (có hạn 7 ngày). Khách click link, đặt mật khẩu → tài khoản chuyển từ SHADOW sang ACTIVE, giữ nguyên toàn bộ dữ liệu. (Requirement: Shadow Account phải có email mới gửi được — luồng Guest bắt buộc thu thập email; Walk-in nếu không có email thì Claim Account link không được gửi.) |

## 11.2 Vehicle Management

| ID | Yêu cầu |
|---|---|
| FR-VEH-01 | Customer tạo xe với: biển số, hãng xe, dòng xe, màu, loại xe (S/M/L), ghi chú. |
| FR-VEH-02 | Một Customer có thể có nhiều xe. |
| FR-VEH-03 | Customer cập nhật / xóa / vô hiệu hóa xe. |
| FR-VEH-04 | Biển số xe phải là duy nhất trong hệ thống (unique constraint). |
| FR-VEH-05 | Một biển số tại một thời điểm chỉ được có 1 booking active. |

## 11.3 Service Catalog

| ID | Yêu cầu |
|---|---|
| FR-SVC-01 | Hiển thị danh mục dịch vụ với giá theo loại xe (S/M/L). |
| FR-SVC-02 | Mỗi dịch vụ có: tên, mô tả, nhóm, giá theo S/M/L, thời lượng, trạng thái active. |
| FR-SVC-03 | Admin tạo / sửa / vô hiệu hóa dịch vụ. |
| FR-SVC-04 | Admin cập nhật bảng giá theo loại xe bất kỳ lúc nào. |
| FR-SVC-05 | Hệ thống tính tổng giá + tổng thời lượng dự kiến khi Customer chọn dịch vụ. |
| FR-SVC-06 | ServiceCategory có cờ `is_wash_group` (BOOLEAN). Hệ thống tự động phân loại booking là WASH nếu có ít nhất 1 dịch vụ thuộc category có `is_wash_group = true`. |

## 11.4 Slot Management

| ID | Yêu cầu |
|---|---|
| FR-SLOT-01 | Hệ thống tính capacity mỗi slot: `min(Active_Bays, floor(Washers_Online / Min_Washer_Per_Car))`. |
| FR-SLOT-02 | Customer chỉ thấy slot available trong booking window của hạng thành viên. |
| FR-SLOT-03 | Slot hold: khi Customer chọn slot, hệ thống giữ tối đa **10 phút**. |
| FR-SLOT-04 | Slot expired: quá 10 phút không confirm → slot giải phóng, booking EXPIRED. |
| FR-SLOT-05 | Admin/Manager cấu hình số cầu nâng, số washer ca theo ngày. |
| FR-SLOT-06 | Hệ thống chặn booking nếu Customer đã có 2 booking active hoặc biển số đã có booking active. |
| FR-SLOT-07 | Hệ thống chỉ hiển thị và cho phép chọn slot có giờ bắt đầu **≥ 2 giờ** từ thời điểm hiện tại. Slot trong vòng 2 giờ tới bị ẩn/khoa với Customer. |

## 11.5 Booking Management

| ID | Yêu cầu |
|---|---|
| FR-BOOK-01 | Customer tạo booking: chọn xe, dịch vụ (tự do), chi nhánh, slot. |
| FR-BOOK-02 | Hệ thống kiểm tra rate limit (IP, max active booking, biển số) trước khi tạo booking. |
| FR-BOOK-03 | Booking hiển thị: giá dự kiến, thời lượng dự kiến, trạng thái. |
| FR-BOOK-04 | Manager xác nhận booking khi thuộc nhóm cần xác nhận. |
| FR-BOOK-05 | Manager phân công Car Washer cho booking. |
| FR-BOOK-06 | Car Washer xác nhận Customer đã tới (Check-in). |
| FR-BOOK-07 | Car Washer tạo Vehicle Inspection trước dịch vụ. |
| FR-BOOK-08 | Customer xác nhận tình trạng xe. |
| FR-BOOK-09 | Car Washer bắt đầu dịch vụ (chỉ sau khi đã có xác nhận của Customer). |
| FR-BOOK-10 | Car Washer cập nhật trạng thái IN_PROGRESS, COMPLETED. |
| FR-BOOK-11 | **Nhắc xác nhận 2-tier:** (A) Slot ≥ 10:00 AM → gửi email tại T-2h kèm nút [Xác nhận sẽ đến] / [Hủy lịch]. (B) Slot < 10:00 AM → gửi email lúc 20:00 hôm trước kèm nút [Xác nhận sẽ đến] / [Hủy lịch]. |
| FR-BOOK-12 | **Thời hạn phản hồi để tránh AUTO_CANCEL:** (A) Slot ≥ 10:00 AM → **30 phút** sau khi email được gửi. (B) Slot < 10:00 AM → trước **T-1h** (1 giờ trước giờ hẹn) sáng hôm hẹn. |
| FR-BOOK-13 | Customer tự hủy booking (áp dụng quy tắc trust score). |
| FR-BOOK-14 | Manager đánh dấu NO_SHOW nếu khách trễ quá **10 phút**. |
| FR-BOOK-15 | Hệ thống cho phép Car Washer hoặc Manager điều chỉnh kích thước xe thực tế tại bước Check-in, tự động cập nhật đơn giá theo bảng giá dịch vụ tương ứng và tính toán lại tổng tiền. |
| FR-BOOK-16 | **Chỉnh sửa dịch vụ tại chỗ:** Manager có thể thêm hoặc bớt dịch vụ khi booking đang ở trạng thái CHECKED_IN hoặc VEHICLE_INSPECTED (trước IN_PROGRESS). Hệ thống tự động tính lại tổng giá và tổng thời lượng, cập nhật lại `booking_type` (nếu cần). Customer phải xác nhận thiết lập mới trước khi dịch vụ bắt đầu. |
| FR-BOOK-17 | Manager có thể phân công lại (reassign) Car Washer khi booking đang ở trạng thái ASSIGNED (trước CHECKED_IN). |

## 11.6 Payment

| ID | Yêu cầu |
|---|---|
| FR-PAY-01 | Payment được tạo khi dịch vụ COMPLETED. |
| FR-PAY-02 | Hệ thống kiểm tra Trust Score để quyết định cho phép Pay After Service hay không. |
| FR-PAY-03 | Hỗ trợ 2 phương thức: CASH (ghi nhận thủ công) và PAYOS (tích hợp API). |
| FR-PAY-04 | PAYOS: Hệ thống tạo payment link/QR, nhận callback webhook, cập nhật PAID. |
| FR-PAY-05 | Booking status và Payment status quản lý độc lập. |
| FR-PAY-06 | Sau Payment PAID: tự động cộng điểm loyalty + cập nhật tổng chi tiêu. |

## 11.7 Loyalty & Redemption

| ID | Yêu cầu |
|---|---|
| FR-LOY-01 | Cộng điểm theo công thức sau khi Payment PAID. |
| FR-LOY-02 | Admin cấu hình: conversion rate, multiplier theo tier, point expiry. |
| FR-LOY-03 | Mỗi thay đổi điểm tạo 1 LoyaltyTransaction (EARN / REDEEM / ADJUSTMENT / EXPIRE). |
| FR-LOY-04 | Hệ thống recalculate tier sau mỗi payment thành công. |
| FR-LOY-05 | Hệ thống review tier định kỳ 12 tháng (cron job), áp dụng sau 30 ngày grace period. |
| FR-LOY-06 | Customer đổi điểm từ Reward Catalog (3 nhóm: Voucher, Free Wash, Add-on). |
| FR-LOY-07 | Voucher do đổi điểm có mã riêng, valid time giới hạn, áp dụng **tại bước chọn dịch vụ** khi tạo booking. |
| FR-LOY-08 | Quà sinh nhật tự động gửi email đầu tháng sinh nhật (Silver: voucher 10%, Gold: Free Wash). |

## 11.8 Trust Score

| ID | Yêu cầu |
|---|---|
| FR-TRUST-01 | Mỗi tài khoản có Trust Score, bắt đầu từ 100. |
| FR-TRUST-02 | Hệ thống tự động trừ/cộng điểm theo sự kiện booking. |
| FR-TRUST-03 | Trust Score < 60: hệ thống tự động block Pay After Service. |
| FR-TRUST-04 | Trust Score < 20: hệ thống tự động block đặt lịch, cần Manager unlock. |
| FR-TRUST-05 | Platinum: miễn phạt tối đa 2 lần/tháng (cần log rõ số lần đã dùng miễn phạt). |
| FR-TRUST-06 | Admin/Manager xem lịch sử Trust Score của từng tài khoản. |

## 11.9 Complaint

| ID | Yêu cầu |
|---|---|
| FR-COM-01 | Customer tạo complaint gắn với booking đã COMPLETED/PAID, trong vòng **7 ngày** kể từ khi booking CLOSED. |
| FR-COM-02 | Complaint có: tiêu đề, mô tả, hình ảnh/video bằng chứng. |
| FR-COM-03 | Manager xem complaint kèm toàn bộ context: inspection, hình ảnh, payment, washer. |
| FR-COM-04 | Manager phản hồi, chuyển trạng thái, ghi resolution note. |
| FR-COM-05 | Manager có thể authorize loyalty adjustment hoặc ghi nhận refund từ complaint. |
| FR-COM-06 | Mọi thay đổi complaint được log trong audit trail. |

## 11.10 Notification

| ID | Yêu cầu | Kênh |
|---|---|---|
| FR-NOTI-01 | Xác nhận OTP khi đăng ký | Email |
| FR-NOTI-02 | Xác nhận booking thành công | Email + In-app |
| FR-NOTI-03 | Nhắc lịch + nút Confirm/Cancel: slot ≥ 10:00 gửi tại T-2h; slot < 10:00 gửi lúc 20:00 hôm trước | Email |
| FR-NOTI-04 | Thông báo AUTO_CANCELLED | Email + In-app |
| FR-NOTI-05 | Thông báo bị đánh dấu NO_SHOW | In-app |
| FR-NOTI-06 | Phân công task mới | In-app (Car Washer) |
| FR-NOTI-07 | Booking update (Manager xác nhận) | In-app (Customer) |
| FR-NOTI-08 | Complaint update | Email + In-app |
| FR-NOTI-09 | Thăng hạng thành viên | Email + In-app |
| FR-NOTI-10 | Điểm sắp expire (trước 30 ngày) | Email |
| FR-NOTI-11 | Quà sinh nhật | Email |

## 11.11 Reporting

| ID | Yêu cầu |
|---|---|
| FR-RPT-01 | Dashboard booking theo ngày / chi nhánh / trạng thái (Manager, Admin). |
| FR-RPT-02 | Báo cáo doanh thu theo ngày, tháng, chi nhánh, dịch vụ (Admin). |
| FR-RPT-03 | Báo cáo hiệu suất Car Washer: số booking xử lý, rating trung bình. |
| FR-RPT-04 | Báo cáo complaint: số lượng, trạng thái, thời gian xử lý trung bình. |
| FR-RPT-05 | Báo cáo loyalty: phân bổ hạng thành viên, điểm đổi thưởng. |
| FR-RPT-06 | Báo cáo Trust Score: tài khoản bị chặn, trend no-show. |

---

# 12. Business Rules

## 12.1 Booking Rules

| Mã | Rule |
|---|---|
| BR-BOOK-01 | Booking phải gắn với đúng 1 xe cụ thể. |
| BR-BOOK-02 | Booking phải có ít nhất 1 dịch vụ. |
| BR-BOOK-03 | Slot hold tối đa **10 phút**. Hết 10 phút → slot giải phóng, booking EXPIRED. |
| BR-BOOK-04 | Một biển số xe tại một thời điểm chỉ được có 1 booking active. |
| BR-BOOK-05 | Một tài khoản tối đa 2 booking ở trạng thái Pending/Confirmed cùng lúc. |
| BR-BOOK-06 | Customer chỉ được xem slot trong booking window của tier (Member: 3 ngày, Silver: 7 ngày, Gold: 10 ngày, Platinum: 14 ngày). Slot có giờ bắt đầu < 2 giờ từ hiện tại không được hiển thị với bất kỳ hạng nào. |
| BR-BOOK-07 | Manager không bắt buộc xác nhận tất cả booking. Chỉ cần xác nhận booking đặc biệt: giá trị cao (> 2M), thời lượng dài (> 4h), booking sát giờ (< 1h), khách có trust score < 60, booking do Manager tạo hộ. |
| BR-BOOK-08 | Car Washer assignment nên xảy ra trước khi khách đến. Walk-in có thể assign khi check-in. |
| BR-BOOK-09 | Inspection bắt buộc trước khi bắt đầu dịch vụ. Customer phải xác nhận. |
| BR-BOOK-10 | Dịch vụ không thể bắt đầu nếu chưa có Customer confirmation trên inspection. |
| BR-BOOK-11 | Booking chỉ CLOSED sau khi Payment PAID và không còn action bắt buộc. |
| BR-BOOK-12 | Tại bước Check-in, nếu kích thước xe thực tế khác với kích thước xe khách đặt trực tuyến, Car Washer/Manager có quyền điều chỉnh kích thước xe về đúng thực tế. Hệ thống sẽ tự động cập nhật lại đơn giá dịch vụ theo bảng giá chuẩn và tính lại tổng tiền. Khách hàng phải xác nhận lại tổng tiền và tình trạng xe trước khi bắt đầu dịch vụ. **Nếu khách từ chối xác nhận giá mới:** Manager thực hiện hủy on-site không phạt (BR-CANCEL-11 — option B), ghi rõ lý do "Khách không đồng ý đơn giá sau điều chỉnh size xe". |
| BR-BOOK-13 | Hệ thống tự động xác định `booking_type`: nếu booking có ít nhất 1 dịch vụ thuộc ServiceCategory có `is_wash_group = true` → WASH; ngược lại → FLEX. |
| BR-BOOK-14 | **WASH booking:** `num_slots = ceil(total_estimated_duration / 30)`. Hệ thống kiểm tra và chặn `num_slots` slot liên tiếp bắt đầu từ `start_slot_id`. Khi CONFIRMED: tăng `booked_count` trên tất cả slot trong chuỗi. Khi CANCELLED/EXPIRED: giảm `booked_count` trên tất cả slot trong chuỗi. |
| BR-BOOK-15 | **FLEX booking:** `num_slots = 1`. Không kiểm tra capacity. Không ảnh hưởng `booked_count` trên SlotInstance. Khách có thể chọn bất kỳ slot có giờ bắt đầu ≥ 2h từ hiện tại. |
| BR-BOOK-16 | **Chỉnh sửa dịch vụ tại chỗ:** Manager có thể thêm/bớt dịch vụ khi booking ở CHECKED_IN hoặc VEHICLE_INSPECTED. Hệ thống tính lại tổng giá, tổng thời lượng, và `booking_type` (nếu thay đổi). Customer phải xác nhận thiết lập mới. Không cho phép chỉnh sửa sau khi IN_PROGRESS. |
| BR-BOOK-17 | Manager có thể reassign Car Washer khi booking ở trạng thái ASSIGNED (trước CHECKED_IN). Mọi thay đổi assignment được log vào BookingAssignment với trạng thái CANCELLED cho nhân viên cũ và ASSIGNED mới cho nhân viên mới. |

## 12.2 Cancellation & No-show Rules

| Mã | Rule |
|---|---|
| BR-CANCEL-01 | Hủy ≥ 2 giờ trước giờ hẹn: không phạt Trust Score. |
| BR-CANCEL-02 | Hủy trong khoảng 30 phút – 2 giờ (trong cửa sổ T-2h hoặc không kịp nhận email T-2h): -20 Trust Score. |
| BR-CANCEL-03 | Hủy trong vòng 30 phút trước giờ hẹn: -20 Trust Score. |
| BR-CANCEL-04 | **AUTO_CANCEL (không phản hồi email nhắc trong thời hạn):** -20 Trust Score. Thời hạn áp dụng: (A) Slot ≥ 10:00 AM → 30 phút sau khi gửi email T-2h. (B) Slot < 10:00 AM → T-1h sáng hôm hẹn (không phản hồi email gửi lúc 20:00 hôm trước). |
| BR-CANCEL-05 | **SAU KHI KHÁCH XÁC NHẬN SẼ ĐẾN (T-2h confirm):** Nút hủy bị vô hiệu hóa. Khách không thể hủy online. Chỉ Manager mới có thể hủy thủ công khi khách gọi điện khẩn cấp (→ CANCELLED_BY_MANAGER, `penalty_applied = true`, -20 Trust Score). |
| BR-CANCEL-06 | NO_SHOW (trễ quá **10 phút** sau giờ hẹn, Manager đánh dấu): -40 Trust Score. |
| BR-CANCEL-07 | NO_SHOW chỉ Manager có quyền đánh dấu, sau khi xác nhận khách không có mặt tại cửa hàng. |
| BR-CANCEL-08 | Platinum được miễn phạt Trust Score tối đa 2 lần/tháng cho mọi loại vi phạm. |
| BR-CANCEL-09 | Manager có thể hủy booking bất kỳ lúc nào vì lý do vận hành (CANCELLED_BY_MANAGER). Manager **bắt buộc nhập lý do** (`cancellation_reason`). |
| BR-CANCEL-10 | **Hủy tại chỗ (On-site):** Khi khách đã CHECKED_IN hoặc VEHICLE_INSPECTED hoặc CUSTOMER_CONFIRMED_CONDITION mà muốn không tiếp tục dịch vụ — khách **không thể tự hủy online**. Khách thông báo trực tiếp với nhân viên, Manager thực hiện hủy trên hệ thống. |
| BR-CANCEL-11 | **Manager hủy On-site — 2 option:** (A) **Có phạt** (`penalty_applied = true`): -20 Trust Score — dùng khi lỗi chủ quan từ khách (đổi ý, không chấp nhận giá thực tế, lý do cá nhân). (B) **Không phạt** (`penalty_applied = false`): 0 Trust Score — dùng khi lỗi khách quan từ tiệm (máy hỏng, nhân sự không đủ, dịch vụ không phù hợp sau tư vấn). Manager **bắt buộc nhập lý do** khi chọn option (B). |
| BR-CANCEL-12 | **Khi booking đã IN_PROGRESS:** Không cho phép hủy dưới bất kỳ hình thức nào (kể cả Manager). Nếu có sự cố trong quá trình thực hiện, xử lý qua Complaint sau khi COMPLETED. |

## 12.3 Walk-in Rules

| Mã | Rule |
|---|---|
| BR-WALK-01 | Manager tạo booking hộ khách vãng lai. |
| BR-WALK-02 | Thông tin tối thiểu: tên khách, SĐT, biển số, loại xe, dịch vụ, slot. |
| BR-WALK-03 | Hệ thống kiểm tra SĐT: (A) chưa tồn tại → tạo Shadow Account mới; (B) đã là Shadow → gộp booking vào Shadow cũ; (C) đã có tài khoản ACTIVE → gắn booking vào tài khoản đó. Mọi trưộng hợp đều có loyalty tracking. |
| BR-WALK-04 | Walk-in booking bỏ qua bước PENDING_CONFIRMATION, chuyển thẳng ASSIGNED. |

## 12.4 Vehicle Inspection Rules

| Mã | Rule |
|---|---|
| BR-INS-01 | Pre-service Inspection bắt buộc trước khi bắt đầu dịch vụ bất kỳ. |
| BR-INS-02 | Inspection phải có ít nhất 1 ảnh xe trước dịch vụ. |
| BR-INS-03 | Customer phải xác nhận inspection trước khi dịch vụ bắt đầu. |
| BR-INS-04 | Dữ liệu inspection (ảnh, ghi chú) không được xóa sau khi booking closed. |
| BR-INS-05 | Post-service Inspection được khuyến nghị nhưng không bắt buộc (Customer có quyền từ chối nhận xe). |

## 12.5 Payment Rules

| Mã | Rule |
|---|---|
| BR-PAY-01 | Payment chỉ được tạo sau khi booking COMPLETED. |
| BR-PAY-02 | Trust Score ≥ 60: cho phép Pay After Service (Cash hoặc PayOS). |
| BR-PAY-03 | Trust Score < 60: chỉ chấp nhận PayOS, phải thanh toán trước khi rời cửa hàng. |
| BR-PAY-04 | Điểm loyalty chỉ cộng khi Payment status = PAID. |
| BR-PAY-05 | Booking có thể ở trạng thái COMPLETED nhưng payment UNPAID (trường hợp payment thất bại). |
| BR-PAY-06 | Refund/adjustment phải tạo transaction mới, không sửa record cũ. Cần audit log và Manager approval. |
| BR-PAY-07 | **PayOS timeout:** Nếu booking COMPLETED nhưng Payment vẫn UNPAID sau **30 phút**, hệ thống tự động gửi thông báo cho Manager (“Khách X — booking Y — chưa thu được tiền”). Manager xử lý bằng cách thu tiền mặt hoặc gửi lại link PayOS. |

## 12.6 Loyalty Rules

| Mã | Rule |
|---|---|
| BR-LOY-01 | Điểm tính trên paid_amount thực tế (sau khi áp discount nếu có). |
| BR-LOY-02 | Admin cấu hình conversion rate, tier multiplier, tier threshold, point expiry. |
| BR-LOY-03 | LoyaltyTransaction là immutable (không sửa). Điều chỉnh tạo transaction loại ADJUSTMENT. |
| BR-LOY-04 | Tier recalculate sau mỗi payment PAID. Nếu đạt ngưỡng mới → thăng hạng ngay. |
| BR-LOY-05 | Tier review định kỳ 12 tháng + 30 ngày grace period (cron job). |
| BR-LOY-06 | Điểm expire nếu tài khoản không có giao dịch mới trong 12 tháng. |
| BR-LOY-07 | Voucher đổi điểm có mã unique, valid trong 30 ngày, không hoàn điểm nếu không dùng. |
| BR-LOY-08 | Voucher/quà đổi điểm được áp dụng **tại bước chọn dịch vụ khi tạo booking** (trước khi confirm). Free Wash voucher → dịch vụ tương ứng giảm về 0đ. Discount Voucher → giảm estimated_total_price. Không thể áp sau khi booking đã CONFIRMED. |
| BR-LOY-09 | Hệ thống kiểm tra voucher: còn hiệu lực (ACTIVE), chưa hết hạn, đúng loại dịch vụ (với Free Wash), và khách hàng sở hữu voucher trước khi cho phép áp. |
| BR-LOY-10 | **Cross-branch loyalty:** Điểm tch lũy và đổi thưởng áp dụng toàn hệ thống (không giới hạn chi nhánh). Khách tch điểm tại chi nhánh A có thể sử dụng voucher đổi điểm tại chi nhánh B. Quản lý điểm tập trung tại CustomerProfile, không phần mảnh theo chi nhánh. |

## 12.7 Trust Score Rules

| Mã | Rule |
|---|---|
| BR-TRUST-01 | Trust Score khởi tạo = 100, tối đa = 100, không âm. |
| BR-TRUST-02 | Trust Score thay đổi ngay lập tức khi sự kiện xảy ra. |
| BR-TRUST-03 | Trust Score < 60 → hệ thống tự block Pay After Service, không cần can thiệp thủ công. |
| BR-TRUST-04 | Trust Score < 20 → hệ thống tự block đặt lịch. Manager có thể unlock thủ công sau khi xem xét. |
| BR-TRUST-05 | Platinum: Hệ thống track số lần miễn phạt trong tháng. Đủ 2 lần → áp dụng phạt bình thường từ lần 3. |
| BR-TRUST-06 | TrustScoreLog là immutable, ghi nhận mọi thay đổi với lý do và actor. |

---

# 13. Booking State Machine

## 13.1 Tất cả trạng thái

| State | Ý nghĩa |
|---|---|
| `SLOT_HELD` | Slot đang được giữ tạm thời 10 phút |
| `EXPIRED` | Slot hold hết hạn 10 phút, chưa tạo booking chính thức |
| `PENDING_CONFIRMATION` | Booking tạo, chờ Manager xác nhận |
| `CONFIRMED` | Booking được xác nhận (tự động hoặc bởi Manager) |
| `ASSIGNED` | Manager đã phân công Car Washer |
| `CHECKED_IN` | Customer đã tới cửa hàng, Car Washer xác nhận |
| `VEHICLE_INSPECTED` | Car Washer đã tạo inspection trước dịch vụ |
| `CUSTOMER_CONFIRMED_CONDITION` | Customer đã xác nhận tình trạng xe |
| `IN_PROGRESS` | Dịch vụ đang được thực hiện |
| `COMPLETED` | Dịch vụ hoàn tất, chờ thanh toán |
| `CLOSED` | Booking đã đóng hoàn toàn (Payment PAID, không còn action bắt buộc) |
| `CANCELLED_BY_CUSTOMER` | Customer tự hủy |
| `CANCELLED_BY_MANAGER` | Manager hủy vì lý do vận hành hoặc hủy hộ khách khẩn cấp |
| `AUTO_CANCELLED` | Hệ thống tự hủy vì không confirm T-2h |
| `NO_SHOW` | Customer không đến, Manager đánh dấu sau 10 phút trễ |

## 13.2 State Transition Diagram

```
SLOT_HELD (10 phút)
├── → EXPIRED                        [Hết 10 phút, không confirm]
└── → PENDING_CONFIRMATION            [Customer confirm booking]
    └── → CONFIRMED                  [Manager xác nhận / Auto-confirm]
        │   └── (Nếu walk-in: bỏ qua, thẳng ASSIGNED)
        ├── → ASSIGNED               [Manager phân công Car Washer]
        │   ├── → CHECKED_IN         [Car Washer xác nhận khách đến]
        │   │   ├── → VEHICLE_INSPECTED       [Car Washer tạo inspection]
        │   │   │   ├── → CUSTOMER_CONFIRMED_CONDITION  [Customer xác nhận tình trạng xe]
        │   │   │   │   ├── → IN_PROGRESS     [Car Washer bắt đầu dịch vụ] ← KHÔNG THỂ HỦY
        │   │   │   │   │   └── → COMPLETED   [Car Washer hoàn tất]
        │   │   │   │   │       └── → CLOSED    [Trigger: Payment PAID]
        │   │   │   │   └── → CANCELLED_BY_MANAGER  [On-site, có/không phạt — BR-CANCEL-11]
        │   │   │   └── → CANCELLED_BY_MANAGER      [On-site, có/không phạt — BR-CANCEL-11]
        │   │   ├── → CANCELLED_BY_MANAGER           [On-site, có/không phạt — BR-CANCEL-11]
        │   │   └── → NO_SHOW                        [Trễ > 10 phút, Manager đánh dấu]
        │   └── → CANCELLED_BY_MANAGER               [Manager hủy vì vận hành / khẩn cấp]
        ├── → CANCELLED_BY_CUSTOMER                  [Customer tự hủy ≥ 2h: 0 trust / <2h: -20 trust]
        └── → AUTO_CANCELLED                         [Không confirm T-2h trong 30 phút: -20 trust]
```

## 13.3 State Transition Rules

1. `SLOT_HELD` chỉ tồn tại tối đa **10 phút**.
2. `CONFIRMED` là điều kiện cần để booking tiến vào vận hành.
3. Walk-in booking bỏ qua `PENDING_CONFIRMATION`, chuyển thẳng `ASSIGNED`.
4. `CHECKED_IN` phải xảy ra trước `VEHICLE_INSPECTED`.
5. `CUSTOMER_CONFIRMED_CONDITION` phải xảy ra trước `IN_PROGRESS`.
6. `CLOSED` chỉ trigger khi Payment record chuyển sang PAID. Không có state trung gian PAID trong Booking.
7. `AUTO_CANCELLED` chỉ trigger từ T-2h email flow, không phải Manager action.
8. `NO_SHOW` chỉ Manager có quyền đánh dấu, sau ngưỡng **10 phút** trễ không có mặt.
9. Sau khi khách confirm T-2h (`t2h_confirmed_at` được set): nút hủy **disabled** trên giao diện khách. Chỉ Manager mới có thể hủy thủ công.
10. **`IN_PROGRESS` là trạng thái không thể hủy.** Không có transition CANCELLED nào từ IN_PROGRESS. Mọi vấn đề phát sinh xử lý qua Complaint sau COMPLETED.
11. **On-site cancellation** (CHECKED_IN / VEHICLE_INSPECTED / CUSTOMER_CONFIRMED_CONDITION → CANCELLED_BY_MANAGER): chỉ Manager thực hiện, bắt buộc nhập `cancellation_reason`, chọn `penalty_applied` (true/false) theo BR-CANCEL-11.

---

# 14. Payment State Machine

```
UNPAID
└── → PENDING      [Customer khởi tạo thanh toán]
    ├── → PAID     [Thanh toán thành công (Cash hoặc PayOS callback)]
    │   └── → REFUNDED  [Manager xử lý hoàn tiền từ complaint]
    └── → FAILED   [Thanh toán thất bại]
        └── → PENDING  [Thử lại]
```

**Rules:**
1. Payment status hoàn toàn độc lập với Booking status.
2. Booking có thể COMPLETED nhưng Payment vẫn UNPAID.
3. Loyalty points chỉ cộng khi Payment = PAID.
4. Refund tạo LoyaltyTransaction loại ADJUSTMENT kèm lý do.
5. PayOS dùng webhook callback để cập nhật trạng thái tự động.

---

# 15. Complaint State Machine

```
OPEN
└── → IN_REVIEW           [Manager tiếp nhận]
    ├── → WAITING_FOR_CUSTOMER  [Cần thêm thông tin từ Customer]
    │   └── → IN_REVIEW        [Customer bổ sung thông tin]
    ├── → RESOLVED         [Manager chấp nhận, có resolution note]
    │   └── → CLOSED       [Customer xác nhận]
    └── → REJECTED         [Manager từ chối kèm lý do]
        └── → CLOSED       [Auto-close hoặc Customer xác nhận]
```

**Rules:**
1. Complaint phải gắn với booking đã COMPLETED hoặc PAID.
2. Manager là người xử lý chính.
3. Customer bổ sung bằng chứng khi WAITING_FOR_CUSTOMER.
4. Complaint CLOSED không chỉnh sửa nội dung, chỉ thêm audit log.
5. SLA đề xuất: Manager phản hồi trong 3 ngày làm việc kể từ OPEN.

---

# 16. Loyalty Tier State Machine

```
Customer mới → **MEMBER**
    → Đạt 5M chi tiêu trong 12 tháng → SILVER (ngay lập tức)
    → Đạt 15M → GOLD
    → Đạt 40M → PLATINUM

Sau 12 tháng giữ hạng:
    → Grace Period 30 ngày
    → Review tổng chi tiêu 12 tháng vừa qua
    → DUY TRÌ / LÊN TIẾP / HẠ HẠNG (xuống MEMBER nếu < 5M)
```

---

# 16.5 User Account State Machine

```
[Tạo mới] → SHADOW
│
├──────────────────────────────────────────────
│  SHADOW ← tạo tự động khi:
│     - Khách đặt lịch lần đầu qua CTA Phone Number (Case A)
│     - Manager tạo booking hộ khách vãng lai chưa có tài khoản
│     - không có password_hash
│     - Có đầy đủ Trust Score (100), loyalty tracking, lịch sử booking
│
│  SHADOW → ACTIVE
│     Trigger: Khách click Claim Account Link và đặt mật khẩu
│     Điều kiện: claim_token hợp lệ + chưa hết hạn (7 ngày)
│     Kết quả: password_hash được set, status = ACTIVE, claim_token xoá
│
│  SHADOW → SHADOW (Case B)
│     Trigger: Khách có Shadow Account đặt lịch thêm (gộp vào Shadow cũ)
│     Kết quả: Tạo booking mới trong cùng Shadow Account, điểm cộng dồn
│
├──────────────────────────────────────────────
│  ACTIVE ← tạo khi:
│     - Khách tự đăng ký thông thường (email + mật khẩu)
│     - Shadow Account được kích hoạt qua Claim Account Flow
│
│  ACTIVE → INACTIVE
│     Trigger: Admin/Manager vô hiệu hóa tài khoản
│
│  ACTIVE → BANNED
│     Trigger: Admin ban vĩnh viễn (vi phạm nghiêm trọng)
│
├──────────────────────────────────────────────
   SHADOW hết hạn (Cleanup Policy)
     Trigger: Cron job hàng ngày
     Điều kiện: Shadow Account không có giao dịch nào trong 12 tháng
     Kết quả: Chuyển sang INACTIVE (giữ dữ liệu, không xóa)
```

**Business Rules bổ sung cho Shadow Account:**

| Mã | Rule |
|---|---|
| BR-AUTH-01 | Shadow Account được tạo với `password_hash = NULL` và `status = SHADOW`. |
| BR-AUTH-02 | Shadow Account có `claim_token` duy nhất, hết hạn sau 7 ngày kể từ lần có Payment PAID gần nhất. |
| BR-AUTH-03 | Nếu claim_token hết hạn, hệ thống có thể tự generate token mới khi khách yêu cầu ("Gửi lại link kích hoạt"). |
| BR-AUTH-04 | Một SĐT chỉ có được duy nhất 1 Shadow Account hoặc 1 Active Account trong hệ thống (không song song). |
| BR-AUTH-05 | Khi Shadow Account chuyển sang ACTIVE, `claim_token` phải được xóa ngay lập tức (invalidate). |
| BR-AUTH-06 | Shadow Account không thể sửa thông tin cá nhân hay đổi mật khẩu cho đến khi chuyển sang ACTIVE. |

---

# 17. Data Requirements / Core Entities

## 17.1 User

| Field | Type | Description |
|---|---|---|
| user_id | UUID | Primary Key |
| full_name | VARCHAR | Họ tên |
| email | VARCHAR | Email (unique, nullable nếu chỉ có SĐT) |
| phone | VARCHAR | Số điện thoại (unique) |
| password_hash | VARCHAR | Mật khẩu đã hash (NULL nếu là Shadow Account) |
| role | ENUM | CUSTOMER / MANAGER / CAR_WASHER / ADMIN |
| branch_id | UUID | Chi nhánh gán (cho MANAGER / CAR_WASHER) |
| status | ENUM | **SHADOW** / ACTIVE / INACTIVE / BANNED |
| email_verified | BOOLEAN | Đã xác thực OTP email/SĐT |
| claim_token | VARCHAR | Token kích hoạt tài khoản từ Shadow → ACTIVE (nullable) |
| claim_token_expires_at | TIMESTAMP | Hạn sử dụng token kích hoạt (7 ngày) |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | |

## 17.2 CustomerProfile

| Field | Type | Description |
|---|---|---|
| customer_id | UUID | Primary Key, FK → User |
| membership_tier | ENUM | MEMBER / SILVER / GOLD / PLATINUM |
| total_points | INT | Điểm tích lũy hiện tại |
| total_spending_12m | BIGINT | Tổng chi tiêu 12 tháng gần nhất (VND) |
| trust_score | INT | 0–100, mặc định 100 |
| platinum_exempt_count | INT | Số lần miễn phạt đã dùng trong tháng (Platinum) |
| platinum_exempt_reset_at | DATE | Ngày reset đếm miễn phạt |
| tier_achieved_at | TIMESTAMP | Thời điểm đạt hạng hiện tại |
| tier_review_at | TIMESTAMP | Thời điểm review hạng tiếp theo |
| no_show_count | INT | Tổng số lần no-show |
| birth_month | INT | Tháng sinh (1-12) để gửi quà sinh nhật |

## 17.3 Vehicle

| Field | Type | Description |
|---|---|---|
| vehicle_id | UUID | Primary Key |
| customer_id | UUID | FK → CustomerProfile |
| license_plate | VARCHAR | Biển số (unique) |
| brand | VARCHAR | Hãng xe |
| model | VARCHAR | Dòng xe |
| color | VARCHAR | Màu xe |
| vehicle_size | ENUM | SMALL / MEDIUM / LARGE |
| notes | TEXT | Ghi chú thêm |
| status | ENUM | ACTIVE / INACTIVE |
| created_at | TIMESTAMP | |

## 17.4 Branch

| Field | Type | Description |
|---|---|---|
| branch_id | UUID | Primary Key |
| name | VARCHAR | Tên chi nhánh |
| address | TEXT | Địa chỉ |
| phone | VARCHAR | Hotline chi nhánh |
| opening_time | TIME | Giờ mở cửa |
| closing_time | TIME | Giờ đóng cửa |
| status | ENUM | ACTIVE / INACTIVE |
| created_at | TIMESTAMP | |

## 17.5 ServiceCategory

| Field | Type | Description |
|---|---|---|
| category_id | UUID | Primary Key |
| name | VARCHAR | Tên nhóm dịch vụ |
| description | TEXT | Mô tả |
| display_order | INT | Thứ tự hiển thị |
| is_wash_group | BOOLEAN | **True** = thuộc Nhóm Rửa xe (WASH, chiếm cầu nâng). False = FLEX (không chiếm cầu nâng). |

## 17.6 Service

| Field | Type | Description |
|---|---|---|
| service_id | UUID | Primary Key |
| category_id | UUID | FK → ServiceCategory |
| service_code | VARCHAR | Mã dịch vụ (SVC-BW-01...) |
| name | VARCHAR | Tên dịch vụ |
| description | TEXT | Mô tả |
| estimated_duration_minutes | INT | Thời lượng ước tính (phút) |
| status | ENUM | ACTIVE / INACTIVE |
| created_at | TIMESTAMP | |

## 17.7 ServicePrice

| Field | Type | Description |
|---|---|---|
| price_id | UUID | Primary Key |
| service_id | UUID | FK → Service |
| vehicle_size | ENUM | SMALL / MEDIUM / LARGE |
| price | BIGINT | Giá (VND) |
| effective_from | DATE | Hiệu lực từ ngày |

## 17.8 SlotTemplate

| Field | Type | Description |
|---|---|---|
| template_id | UUID | Primary Key |
| branch_id | UUID | FK → Branch |
| day_of_week | INT | 0=Chủ nhật..6=Thứ 7 |
| start_time | TIME | Giờ bắt đầu |
| end_time | TIME | Giờ kết thúc |
| active_bays | INT | Số cầu nâng active |
| min_washers_per_car | INT | Washer tối thiểu/xe (mặc định 1) |

## 17.9 SlotInstance

| Field | Type | Description |
|---|---|---|
| slot_id | UUID | Primary Key |
| branch_id | UUID | FK → Branch |
| date | DATE | Ngày cụ thể |
| start_time | TIME | Giờ bắt đầu |
| end_time | TIME | Giờ kết thúc |
| active_bays | INT | Cầu nâng hoạt động |
| washers_online | INT | Số washer trong ca |
| min_washers_per_car | INT | |
| capacity | INT | Tính tự động: min(bays, floor(washers/min_washer)) |
| booked_count | INT | Số booking đã xác nhận |
| held_count | INT | Số booking đang SLOT_HELD |
| status | ENUM | AVAILABLE / FULLY_BOOKED / BLOCKED |

## 17.10 Booking

| Field | Type | Description |
|---|---|---|
| booking_id | UUID | Primary Key |
| customer_id | UUID | FK → CustomerProfile (nullable nếu guest) |
| vehicle_id | UUID | FK → Vehicle |
| branch_id | UUID | FK → Branch |
| booking_type | ENUM | **WASH** / **FLEX** — xác định tự động dựa trên dịch vụ được chọn |
| start_slot_id | UUID | FK → SlotInstance (slot bắt đầu) |
| num_slots | INT | Số slot liên tiếp bị chiếm (WASH: `ceil(duration/30)`, FLEX: 1) |
| booking_source | ENUM | ONLINE / WALK_IN |
| status | ENUM | Booking state |
| voucher_code | VARCHAR | Mã voucher áp dụng (nullable, FK → CustomerReward) |
| discount_amount | BIGINT | Số tiền được giảm từ voucher (VND) |
| estimated_total_price | BIGINT | Giá dự kiến (trước discount) |
| final_total_price | BIGINT | Giá thực tế sau khi hoàn tất (sau discount) |
| estimated_duration_minutes | INT | Tổng thời lượng dự kiến (tổng tất cả dịch vụ) |
| hold_expires_at | TIMESTAMP | Hết hạn giữ slot |
| t2h_reminder_sent_at | TIMESTAMP | Thời điểm gửi email T-2h |
| t2h_confirmed_at | TIMESTAMP | Thời điểm khách confirm T-2h |
| confirmed_at | TIMESTAMP | |
| checked_in_at | TIMESTAMP | |
| completed_at | TIMESTAMP | |
| closed_at | TIMESTAMP | |
| cancelled_by | UUID | FK → User (Manager thực hiện hủy, nullable) |
| cancelled_at | TIMESTAMP | Thời điểm hủy (nullable) |
| cancellation_reason | TEXT | Lý do hủy — bắt buộc khi Manager hủy on-site không phạt (nullable) |
| penalty_applied | BOOLEAN | True = trừ Trust Score khi hủy; False = miễn phạt. Nullable nếu không phải CANCELLED state. |
| created_by | UUID | FK → User (ai tạo) |
| notes | TEXT | Ghi chú |
| created_at | TIMESTAMP | |

## 17.11 BookingService

| Field | Type | Description |
|---|---|---|
| booking_service_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking |
| service_id | UUID | FK → Service |
| price_snapshot | BIGINT | Giá tại thời điểm đặt |
| duration_snapshot | INT | Thời lượng tại thời điểm đặt |

## 17.12 BookingAssignment

| Field | Type | Description |
|---|---|---|
| assignment_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking |
| car_washer_id | UUID | FK → User (Car Washer) |
| assigned_by | UUID | FK → User (Manager) |
| assigned_at | TIMESTAMP | |
| status | ENUM | ASSIGNED / IN_PROGRESS / COMPLETED / CANCELLED |

## 17.13 VehicleInspection

| Field | Type | Description |
|---|---|---|
| inspection_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking |
| inspected_by | UUID | FK → User (Car Washer) |
| inspection_type | ENUM | BEFORE_SERVICE / AFTER_SERVICE |
| exterior_condition | TEXT | Mô tả ngoại thất |
| interior_condition | TEXT | Mô tả nội thất (nếu có) |
| notes | TEXT | Ghi chú bất thường |
| customer_confirmed | BOOLEAN | Khách đã xác nhận chưa |
| customer_confirmed_at | TIMESTAMP | |
| created_at | TIMESTAMP | |

## 17.14 InspectionImage

| Field | Type | Description |
|---|---|---|
| image_id | UUID | Primary Key |
| inspection_id | UUID | FK → VehicleInspection |
| image_url | VARCHAR | URL ảnh |
| description | TEXT | Mô tả ảnh |
| uploaded_at | TIMESTAMP | |

## 17.15 Payment

| Field | Type | Description |
|---|---|---|
| payment_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking |
| amount | BIGINT | Số tiền (VND) |
| method | ENUM | CASH / PAYOS |
| status | ENUM | UNPAID / PENDING / PAID / FAILED / REFUNDED |
| payos_order_code | VARCHAR | Mã đơn PayOS |
| payos_payment_link_id | VARCHAR | ID link thanh toán PayOS |
| paid_at | TIMESTAMP | |
| created_by | UUID | Thu ngân / hệ thống |
| created_at | TIMESTAMP | |

## 17.16 MembershipTierConfig

| Field | Type | Description |
|---|---|---|
| config_id | UUID | Primary Key |
| tier | ENUM | MEMBER / SILVER / GOLD / PLATINUM |
| spending_threshold | BIGINT | Ngưỡng chi tiêu 12 tháng (VND) |
| point_multiplier | DECIMAL | Hệ số nhân điểm |
| booking_window_days | INT | Số ngày booking window (Member=3, Silver=7, Gold=10, Platinum=14) |
| is_active | BOOLEAN | |
| updated_at | TIMESTAMP | |

## 17.17 LoyaltyConfig

| Field | Type | Description |
|---|---|---|
| config_id | UUID | Primary Key |
| conversion_rate | INT | VND cần để đổi 1 điểm (mặc định 10,000) |
| point_expiry_months | INT | Số tháng không giao dịch thì điểm expire (12) |
| updated_by | UUID | |
| updated_at | TIMESTAMP | |

## 17.18 LoyaltyTransaction

| Field | Type | Description |
|---|---|---|
| transaction_id | UUID | Primary Key |
| customer_id | UUID | FK → CustomerProfile |
| booking_id | UUID | FK → Booking (nullable) |
| type | ENUM | EARN / REDEEM / ADJUSTMENT / EXPIRE |
| points | INT | Số điểm (dương = cộng, âm = trừ) |
| description | TEXT | Mô tả |
| created_by | UUID | User tạo (system / manager) |
| created_at | TIMESTAMP | Immutable |

## 17.19 RewardCatalog

| Field | Type | Description |
|---|---|---|
| reward_id | UUID | Primary Key |
| name | VARCHAR | Tên quà |
| reward_type | ENUM | DISCOUNT_VOUCHER / FREE_WASH / ADD_ON_SERVICE |
| points_required | INT | Điểm cần để đổi |
| value | BIGINT | Giá trị tiền tương đương (VND) |
| service_id | UUID | FK → Service (nếu là Free Wash / Add-on) |
| valid_days | INT | Số ngày hiệu lực sau khi đổi |
| status | ENUM | ACTIVE / INACTIVE |
| min_tier_required | ENUM | Hạng tối thiểu để đổi (nullable = all) |

## 17.20 CustomerReward (Voucher đã đổi)

| Field | Type | Description |
|---|---|---|
| customer_reward_id | UUID | Primary Key |
| customer_id | UUID | FK → CustomerProfile |
| reward_id | UUID | FK → RewardCatalog |
| voucher_code | VARCHAR | Mã voucher unique |
| status | ENUM | ACTIVE / USED / EXPIRED |
| redeemed_at | TIMESTAMP | Ngày đổi điểm |
| expires_at | TIMESTAMP | Ngày hết hạn |
| used_at | TIMESTAMP | Ngày sử dụng |
| used_in_booking | UUID | FK → Booking (nullable) |

## 17.21 TrustScoreLog

| Field | Type | Description |
|---|---|---|
| log_id | UUID | Primary Key |
| customer_id | UUID | FK → CustomerProfile |
| booking_id | UUID | FK → Booking (nếu liên quan) |
| event_type | ENUM | NO_SHOW / AUTO_CANCEL / LATE_CANCEL / EARLY_CANCEL / BOOKING_COMPLETED / MANUAL_ADJUST |
| score_before | INT | Điểm trước khi thay đổi |
| score_change | INT | Mức thay đổi (dương / âm) |
| score_after | INT | Điểm sau khi thay đổi |
| is_platinum_exempted | BOOLEAN | Có dùng lượt miễn phạt Platinum không |
| created_at | TIMESTAMP | Immutable |

## 17.22 Complaint

| Field | Type | Description |
|---|---|---|
| complaint_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking |
| customer_id | UUID | FK → CustomerProfile |
| status | ENUM | OPEN / IN_REVIEW / WAITING_FOR_CUSTOMER / RESOLVED / REJECTED / CLOSED |
| title | VARCHAR | Tiêu đề |
| description | TEXT | Nội dung |
| resolution_note | TEXT | Ghi chú xử lý |
| handled_by | UUID | Manager xử lý |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | |
| closed_at | TIMESTAMP | |

## 17.23 ComplaintEvidence

| Field | Type | Description |
|---|---|---|
| evidence_id | UUID | Primary Key |
| complaint_id | UUID | FK → Complaint |
| file_url | VARCHAR | URL file/ảnh |
| description | TEXT | Mô tả |
| uploaded_by | UUID | Customer hoặc Manager |
| uploaded_at | TIMESTAMP | |

## 17.24 BookingRating

| Field | Type | Description |
|---|---|---|
| rating_id | UUID | Primary Key |
| booking_id | UUID | FK → Booking (unique — 1 booking chỉ có 1 rating) |
| customer_id | UUID | FK → CustomerProfile |
| car_washer_id | UUID | FK → User (Car Washer được đánh giá) |
| overall_score | INT | Điểm tổng thể: 1–5 sao |
| service_quality_score | INT | Điểm chất lượng dịch vụ: 1–5 |
| staff_attitude_score | INT | Điểm thái độ nhân viên: 1–5 |
| comment | TEXT | Nhận xét tự do (nullable) |
| created_at | TIMESTAMP | Immutable |

> **Constraints:** Chỉ có thể tạo khi booking ở trạng thái `CLOSED`. Chỉ tạo được 1 lần (unique trên booking_id). Dùng để tính `FR-RPT-03` (rating trung bình Car Washer).

## 17.25 AuditLog

| Field | Type | Description |
|---|---|---|
| audit_id | UUID | Primary Key |
| actor_id | UUID | User thực hiện |
| action | VARCHAR | Tên action |
| entity_type | VARCHAR | Loại entity |
| entity_id | UUID | ID entity |
| old_value | JSONB | Giá trị cũ |
| new_value | JSONB | Giá trị mới |
| ip_address | VARCHAR | IP của actor |
| created_at | TIMESTAMP | |

---

# 18. Use Case List

## 18.1 Customer Use Cases

| ID | Use Case | Priority |
|---|---|---|
| UC-CUS-01 | Register Account (with OTP Email) | Must Have |
| UC-CUS-02 | Login | Must Have |
| UC-CUS-03 | Manage Vehicles (CRUD) | Must Have |
| UC-CUS-04 | View Service Catalog with Pricing by Vehicle Size | Must Have |
| UC-CUS-05 | View Available Slots (filtered by tier booking window) | Must Have |
| UC-CUS-06 | Create Booking (select services freely) | Must Have |
| UC-CUS-07 | Confirm/Cancel via T-2h Email | Must Have |
| UC-CUS-08 | Track Booking Status | Must Have |
| UC-CUS-09 | Confirm Vehicle Inspection Condition | Must Have |
| UC-CUS-10 | Make Payment (Cash / PayOS) | Must Have |
| UC-CUS-11 | View Loyalty Points, Tier, Trust Score | Must Have |
| UC-CUS-12 | Redeem Points for Rewards | Should Have |
| UC-CUS-13 | Cancel Booking (with trust score rules) | Must Have |
| UC-CUS-14 | Submit Complaint | Should Have |
| UC-CUS-15 | Rate Service | Could Have |

## 18.2 Manager Use Cases

| ID | Use Case | Priority |
|---|---|---|
| UC-MAN-01 | View Booking Dashboard (by date/branch/status) | Must Have |
| UC-MAN-02 | Confirm Booking (special cases) | Must Have |
| UC-MAN-03 | Create Walk-in Booking | Must Have |
| UC-MAN-04 | Assign Car Washer | Must Have |
| UC-MAN-05 | Mark No-show | Must Have |
| UC-MAN-06 | Cancel Booking (operational) | Should Have |
| UC-MAN-07 | Handle Complaint (review, resolve, reject) | Should Have |
| UC-MAN-08 | Unlock Customer Trust Score Block | Should Have |
| UC-MAN-09 | Adjust Customer Loyalty Points | Could Have |
| UC-MAN-10 | View Daily Operations Report | Could Have |

## 18.3 Car Washer Use Cases

| ID | Use Case | Priority |
|---|---|---|
| UC-CW-01 | View Assigned Tasks (today) | Must Have |
| UC-CW-02 | Check-in Customer Arrival | Must Have |
| UC-CW-03 | Create Pre-service Vehicle Inspection (with photos) | Must Have |
| UC-CW-04 | Start Service (after Customer confirmation) | Must Have |
| UC-CW-05 | Update Service Progress | Should Have |
| UC-CW-06 | Complete Service | Must Have |
| UC-CW-07 | Create Post-service Inspection (optional) | Could Have |

## 18.4 Admin Use Cases

| ID | Use Case | Priority |
|---|---|---|
| UC-ADM-01 | Manage Users & Roles | Must Have |
| UC-ADM-02 | Manage Branches | Must Have |
| UC-ADM-03 | Manage Service Catalog & Pricing | Must Have |
| UC-ADM-04 | Configure Slot Template (bays, washers per shift) | Must Have |
| UC-ADM-05 | Configure Loyalty Rules (conversion, multiplier, threshold) | Must Have |
| UC-ADM-06 | Manage Reward Catalog | Should Have |
| UC-ADM-07 | Configure Trust Score Thresholds | Should Have |
| UC-ADM-08 | View Reports (Revenue, Booking, Washer Performance, Loyalty) | Should Have |

---

# 19. API Requirements Draft

## Customer APIs

```
POST   /auth/register                         → Đăng ký + gửi OTP
POST   /auth/verify-otp                       → Xác thực OTP
POST   /auth/login
POST   /auth/logout
GET    /me

GET    /vehicles
POST   /vehicles
PATCH  /vehicles/{vehicleId}
DELETE /vehicles/{vehicleId}

GET    /services?vehicleSize=SMALL            → Danh mục dịch vụ + giá theo loại xe
GET    /services/{serviceId}
GET    /branches
GET    /slots?branchId=&date=&vehicleSize=    → Slot trống (lọc theo tier)
POST   /slots/estimate                        → Tính tổng giá + thời lượng dự kiến

POST   /bookings/hold                         → Giữ slot 10 phút
POST   /bookings                              → Tạo booking
GET    /bookings                              → Danh sách booking của tôi
GET    /bookings/{bookingId}
PATCH  /bookings/{bookingId}/cancel           → Customer tự hủy
PATCH  /bookings/{bookingId}/confirm-t2h      → Xác nhận qua email T-2h
PATCH  /bookings/{bookingId}/confirm-vehicle-condition  → Xác nhận tình trạng xe

GET    /payments/{bookingId}
POST   /payments/{bookingId}/payos            → Tạo PayOS payment link
GET    /payments/{bookingId}/payos/status

GET    /loyalty/me                            → Điểm, tier, trust score
GET    /loyalty/transactions
GET    /loyalty/rewards                       → Danh sách quà đổi
POST   /loyalty/redeem                        → Đổi điểm lấy quà
GET    /loyalty/my-rewards                    → Voucher đã đổi

POST   /complaints
GET    /complaints
GET    /complaints/{complaintId}

POST   /bookings/{bookingId}/rating           → Đánh giá dịch vụ
```

## Manager APIs

```
GET    /manager/bookings                      → Dashboard
PATCH  /manager/bookings/{bookingId}/confirm
PATCH  /manager/bookings/{bookingId}/assign   → Phân công Car Washer
PATCH  /manager/bookings/{bookingId}/cancel
PATCH  /manager/bookings/{bookingId}/no-show
POST   /manager/bookings/walk-in              → Tạo booking vãng lai

GET    /manager/complaints
PATCH  /manager/complaints/{complaintId}      → Xử lý complaint
PATCH  /manager/customers/{customerId}/trust-score/unlock  → Unlock Trust Score block
PATCH  /manager/customers/{customerId}/loyalty/adjust      → Điều chỉnh điểm

GET    /manager/reports/daily
GET    /manager/slots?date=&branchId=         → Xem slot + capacity realtime
```

## Car Washer APIs

```
GET    /washer/tasks                          → Task được phân công hôm nay
PATCH  /washer/bookings/{bookingId}/checkin
POST   /washer/bookings/{bookingId}/inspections         → Tạo inspection + upload ảnh
PATCH  /washer/bookings/{bookingId}/start
PATCH  /washer/bookings/{bookingId}/progress
PATCH  /washer/bookings/{bookingId}/complete
POST   /washer/bookings/{bookingId}/inspections/post    → Post-service inspection (optional)
```

## Admin APIs

```
GET/POST/PATCH /admin/users
GET/POST/PATCH /admin/branches
GET/POST/PATCH /admin/services
GET/POST/PATCH /admin/service-prices
GET/POST/PATCH /admin/slot-templates
GET/POST/PATCH /admin/loyalty-config
GET/POST/PATCH /admin/tier-configs
GET/POST/PATCH /admin/reward-catalog
PATCH          /admin/trust-score-config

GET    /admin/reports/revenue
GET    /admin/reports/bookings
GET    /admin/reports/washer-performance
GET    /admin/reports/complaints
GET    /admin/reports/loyalty

POST   /webhooks/payos                        → PayOS payment callback
```

---

# 20. Non-Functional Requirements

## 20.1 Security

1. Mật khẩu phải được hash (bcrypt / Argon2).
2. API bắt buộc kiểm tra role trước mọi action.
3. Customer chỉ xem/thao tác data của chính mình.
4. Manager chỉ xem data của chi nhánh được gán.
5. PayOS webhook phải xác thực signature.
6. Các action quan trọng ghi AuditLog (tạo/hủy booking, payment, loyalty adjustment, trust score change, complaint resolution).
7. Rate limiting trên tất cả endpoint public (đặc biệt endpoint đặt lịch).

## 20.2 Performance

1. Endpoint xem slot trống phải phản hồi < 500ms.
2. Slot hold phải dùng transaction/locking để tránh double booking.
3. PayOS callback xử lý idempotent (không cộng điểm 2 lần nếu webhook gửi nhiều lần).
4. Trust score và loyalty points phải cập nhật đồng bộ trong cùng transaction với payment.

## 20.3 Reliability

1. Slot hold tự hết hạn đúng thời gian (cron job hoặc scheduled task mỗi 1 phút).
2. T-2h email reminder phải gửi đúng thời điểm (scheduled job).
3. AUTO_CANCEL sau 30 phút không confirm phải trigger chính xác.
4. Tier review cron job chạy hàng ngày, kiểm tra các tier cần review.
5. Point expiry cron job kiểm tra hàng ngày, expire điểm đã 12 tháng không có giao dịch.

## 20.4 Usability

1. Customer booking flow tối đa 5 bước từ trang chủ đến confirm.
2. Manager dashboard lọc nhanh theo ngày / trạng thái / chi nhánh.
3. Car Washer UI tối giản, thao tác 1 chạm cho mỗi bước workflow.
4. Email T-2h phải có nút bấm rõ ràng (không cần đăng nhập để confirm).

## 20.5 Auditability

Các action sau bắt buộc có AuditLog:

- Tạo / hủy / AUTO_CANCEL booking
- Xác nhận booking (Manager)
- Phân công Car Washer
- Check-in, Inspection confirmation, Start/Complete service
- Payment update (PAID, FAILED, REFUNDED)
- Trust Score thay đổi
- Loyalty adjustment (EARN, REDEEM, ADJUSTMENT)
- Complaint status change
- Admin thay đổi cấu hình (loyalty config, tier config, trust config)

---

# 21. Risks and Mitigation

| Risk | Mức độ | Mitigation |
|---|---|---|
| Double booking slot | Cao | DB transaction + locking khi hold slot |
| Spam booking qua tool | Cao | 4 lớp anti-spam: OTP, Rate Limit, Auto-Cancel, Trust Score |
| PayOS webhook thất bại | Trung bình | Retry mechanism + idempotency key, polling backup |
| Customer no-show gây lãng phí slot | Trung bình | T-2h auto-cancel + trust score penalty |
| Tranh chấp xe bị hư hỏng | Cao | Pre-service inspection bắt buộc + ảnh bằng chứng |
| Điểm tích bị lỗi (cộng 2 lần) | Cao | Idempotency trong payment callback, unique constraint |
| Tier review sai dữ liệu | Trung bình | Kiểm tra kỹ cron job, log kết quả review |
| Business rule thay đổi giữa chừng | Trung bình | Tất cả config trong DB (loyalty, trust threshold), Admin sửa không cần deploy lại |
| AI tool generate code sai nghiệp vụ | Cao | BRD này là source of truth, agent không được tự quyết business rule |

---

# 22. Open Questions — ĐÃ CHỐT

| # | Câu hỏi | Quyết định |
|---|---|---|
| 1 | Phân loại xe | **Small / Medium / Large** theo kích thước |
| 2 | Danh mục dịch vụ | **6 nhóm** theo Section 6 |
| 3 | Loyalty tier reset | **12 tháng rolling + 30 ngày grace period** |
| 4 | Redeem điểm | **3 nhóm**: Voucher, Free Wash, Add-on |
| 5 | Booking window | **Member: 3 ngày**, Silver: 7 ngày, Gold: 10 ngày, Platinum: 14 ngày. Mọi hạng đều bị chặn slot < 2h từ hiện tại |
| 6 | Anti-spam | **4 lớp**: OTP, Rate Limit, Auto-Cancel T-2h, Trust Score |
| 7 | Point formula | `floor(Amount/10000) × Multiplier` — Admin cấu hình |
| 8 | Hủy booking | Hủy ≥ 2h: miễn phí. Hủy trong T-2h window hoặc < 30min: -20 trust. NO_SHOW (> 10 phút): -40 trust. Sau khi confirm T-2h: không thể hủy online |
| 9 | Chọn dịch vụ | **Tự do nhiều dịch vụ** trong 1 booking |
| 10 | Payment | **Cash + PayOS** |
| 11 | Slot capacity | `min(Active_Bays, floor(Washers_Online/Min_Per_Car))` |
| 12 | Booking Flow | **CTA chỉ hỏi SĐT**. Guest flow: (1) Chọn dịch vụ/slot → (2) Nhập thông tin liên hệ khách (tên + Email bắt buộc) → (3) OTP qua Email → (4) Xác nhận booking |
| 13 | Guest Booking | **Có**, qua Shadow Account + OTP qua Email. Không cho phép đặt hoàn toàn ẩn danh |
| 14 | Claim Account | **Có**, qua link gửi trong email sau Payment PAID. Link hết hạn sau 7 ngày |
| 15 | Multi-branch | **In Scope** — kiến trúc đã sẵn sàng từ đầu (branch_id trong mọi entity liên quan) |
| 16 | Slot hold time | **10 phút**. Hết hạn → EXPIRED, slot giải phóng |
| 17 | Hạng cơ bản | **Member** (thành viên, cũ là Bronze), booking window 3 ngày, multiplier ×1.0 |
| 18 | Slot unit | **30 phút**. WASH booking: chặn `num_slots = ceil(duration/30)` slot liên tiếp, kiểm tra capacity. FLEX booking: chọn bất kỳ slot 7:00–17:30, không kiểm tra capacity, không tăng `booked_count`. |

---

# 23. Demo Scenarios

## Scenario 1: Booking Online bình thường (Gold Customer)

1. Customer Gold đăng nhập.
2. Chọn xe Medium.
3. Xem dịch vụ → Chọn Gói Full Premium (280k) + Khử mùi Ozone (150k) = **430k dự kiến, 120 phút**.
4. Chọn slot ngày sau (trong 10-ngày window của Gold).
5. Confirm trong **10 phút** → Booking CONFIRMED.
6. Manager phân công Car Washer → ASSIGNED.
7. T-2h: Email nhắc → Customer bấm [XÁC NHẬN SẼ ĐẾN].
8. Khách đến → Car Washer check-in → CHECKED_IN.
9. Car Washer inspection, upload 4 ảnh xe → VEHICLE_INSPECTED.
10. Customer xác nhận → CUSTOMER_CONFIRMED_CONDITION.
11. Bắt đầu rửa → IN_PROGRESS.
12. Hoàn tất → COMPLETED.
13. Khách kiểm tra xe, thanh toán Cash 430k → PAID.
14. Hệ thống: cộng `floor(430000/10000) × 1.2 = 51 điểm`. Update spending Gold.
15. Booking CLOSED.

## Scenario 2: Auto-cancel vì không confirm T-2h

1. Customer đặt lịch thành công → CONFIRMED.
2. T-2h: Hệ thống gửi email.
3. Customer không phản hồi sau 30 phút.
4. Hệ thống: Booking → AUTO_CANCELLED, slot giải phóng, -20 Trust Score.
5. Nếu Trust Score về 60: lần sau phải PayOS trước khi nhận xe.

## Scenario 3: Khách vãng lai (Walk-in)

1. Khách đến không đặt trước.
2. Manager tạo booking: nhập SĐT → không tìm thấy account → tạo **Shadow Account** mới.
3. Chọn xe Large, dịch vụ Gói Full Basic (170k), slot ngay trong hôm nay.
4. Phân công Car Washer.
5. Car Washer inspection → Khách xác nhận → Thực hiện.
6. Khách kiểm tra xe, thanh toán Cash. Payment PAID → Booking CLOSED.
7. Hệ thống cộng điểm vào Shadow Account + gửi Email Claim Account link.

## Scenario 4: Khiếu nại sau dịch vụ

1. Booking PAID/CLOSED.
2. Khách phát hiện vết xước không có trước.
3. Tạo complaint, upload ảnh vết xước, mô tả.
4. Manager mở: xem pre-service inspection + ảnh → xác nhận vết xước không có trong inspection.
5. Manager RESOLVED: bồi thường 1 suất Rửa xe Free + 200 điểm.
6. Hệ thống: tạo LoyaltyTransaction ADJUSTMENT +200 điểm, tạo CustomerReward Free Wash.
7. Complaint CLOSED.

## Scenario 5: Platinum hủy sát giờ (dùng miễn phạt)

1. Platinum Customer hủy booking 20 phút trước giờ hẹn.
2. Hệ thống kiểm tra: Platinum, tháng này mới dùng 1/2 lần miễn phạt.
3. Trust Score: Không trừ. `platinum_exempt_count` tăng lên 2.
4. Slot giải phóng. Lần tiếp theo trong tháng này sẽ bị trừ -20 bình thường.

## Scenario 6: Trust Score < 60 — Bắt buộc PayOS

1. Customer có Trust Score = 55 (đã NO_SHOW 2 lần).
2. Đặt lịch thành công (vẫn đặt được vì score > 20).
3. Sau khi dịch vụ COMPLETED: Hệ thống kiểm tra Trust Score.
4. Trust Score < 60: Không cho phép Cash. Hiện PayOS QR/link bắt buộc.
5. Customer quét mã, thanh toán PayOS. Payment PAID.
6. Cộng điểm bình thường.

---

# 24. Acceptance Criteria — Điều kiện bắt đầu thiết kế

Tài liệu được xem là đạt nếu:

- [x] Actor list rõ ràng với phân quyền
- [x] Scope và Out-of-scope rõ ràng
- [x] Phân loại xe (S/M/L)
- [x] Danh mục dịch vụ với mã, giá theo loại xe, thời lượng
- [x] Business flow: Online Booking, Walk-in, Inspection, Payment, Complaint, Cancellation
- [x] Booking state machine đầy đủ (bao gồm AUTO_CANCELLED)
- [x] Payment state machine (Cash + PayOS)
- [x] Complaint state machine
- [x] Loyalty tier system (thăng/hạ hạng, booking window, perks)
- [x] Loyalty point formula (Admin cấu hình được)
- [x] Reward catalog (3 nhóm)
- [x] Trust score system (4 lớp anti-spam, ngưỡng chế tài)
- [x] Slot capacity formula
- [x] Cancellation policy (quy tắc trust score)
- [x] Business rules đầy đủ với mã định danh
- [x] Data entities với fields, types và relationships
- [x] API contract grouped by role
- [x] Non-functional requirements
- [x] Risk & Mitigation
- [x] Demo scenarios

**→ ✅ BRD READY. Sẵn sàng bước vào thiết kế ERD, API Contract chi tiết và Wireframe.**
