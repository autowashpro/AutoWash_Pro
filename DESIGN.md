# Design System: AutoWash Pro
**Project ID:** 

## 1. Visual Theme & Atmosphere
The atmosphere is **"Refined Brutalism"** — a harmonious blend of Apple's immaculate, airy minimalism and the structured, unapologetic geometry of a modern Bento grid. It feels highly systematic, transparent, and effortlessly premium. The design relies on stark white spaces, ultra-crisp typography, and distinct compartmentalized blocks (Bento boxes) to convey a sense of flawless organization, speed, and high-tech automotive care.

## 2. Color Palette & Roles
* **Pure Core (Background):** `#FFFFFF` (Sắc trắng tinh khiết dùng cho nền tổng thể, tạo cảm giác sạch sẽ tuyệt đối của một tiệm rửa xe cao cấp).
* **Grid Surface (Cards/Bento):** `#F8FAFC` (Màu trắng xám/Slate-50 dùng làm nền cho các thẻ (cards) trong lưới bento, giúp phân tách với nền chính).
* **Auto Blue (Primary Action):** `#0055FF` (Màu xanh điện sắc sảo, mang âm hưởng của Stripe. Tượng trưng cho nước, sự nhanh chóng. Dùng cho nút CTA chính, trạng thái active và viền focus).
* **Carbon Black (Primary Text):** `#0F172A` (Màu đen sâu/Slate-900 dùng cho các tiêu đề chính, thông số quan trọng và viền cấu trúc mỏng).
* **Steel Gray (Secondary Text/Borders):** `#64748B` (Dùng cho văn bản phụ, ghi chú và các đường kẻ phân cách nhẹ nhàng).
* **Trust & Status (Semantic):** 
  * *Emerald Green:* `#10B981` (Dành cho Booking thành công, Trust Score cao).
  * *Rose Red:* `#E11D48` (Dành cho việc hủy lịch, Trust Score thấp, cảnh báo).
  * *Loyalty Gold:* `#F59E0B` (Dùng riêng cho nhãn hạng thành viên Gold/Platinum).

## 3. Typography Rules
* **Font Family:** Sử dụng `Inter` hoặc `SF Pro Display` làm font chữ không chân chủ đạo để đảm bảo tính hiện đại.
* **Headings (Tiêu đề):** Sử dụng phông chữ đậm (Semibold 600 hoặc Bold 700) với khoảng cách chữ hẹp (tight letter-spacing) để tạo ra nét mạnh mẽ (brutalist edge) nhưng vẫn sang trọng.
* **Body Text (Văn bản thường):** Trọng lượng chữ thường (400), khoảng cách dòng lớn (relaxed line height) để tối đa hóa khả năng đọc trong các form đặt lịch.
* **Data/Numbers:** Khuyến khích dùng phông chữ Monospace (như `JetBrains Mono`) cho Biển số xe, Khung giờ (Time slots) và Điểm uy tín (Trust Score) nhằm tôn lên vẻ "công nghệ" của hệ thống quản lý.

## 4. Component Stylings
* **Cards & Bento Grid:** Các thẻ (cards) đại diện cho các khối Bento. Chúng phải có độ bo góc mượt mà (`rounded-2xl`) kết hợp với một đường viền rất mỏng và sắc nét (`border border-slate-200`). Thêm một lớp bóng đổ siêu mờ (`soft drop shadow`) để tạo hiệu ứng nổi nhẹ như phong cách Apple.
* **Buttons:** Bo góc vừa phải (`rounded-lg` hoặc `rounded-xl`). Nút Primary là khối màu Auto Blue đặc với chữ trắng. Trạng thái Hover sẽ có hiệu ứng bóng đổ sáng (Glow shadow) thay vì đổi màu.
* **Inputs & Forms:** Form nhập liệu có nền xám nhạt (`bg-slate-50`), không có viền cho đến khi được click vào (Focus) thì sẽ xuất hiện một đường viền màu Auto Blue sắc nét (`ring-2 ring-blue-600`).
* **Badges/Tags:** Hình viên thuốc (`rounded-full`), sử dụng rất nhiều cho các trạng thái Đặt lịch (Assigned, Completed) và Hạng thành viên (Member, Silver, Gold).

## 5. Layout Principles
* **The Bento Grid:** Đây là triết lý xương sống. Trang Dashboard (của Manager/Admin) hay trang xem chi tiết Dịch vụ của khách hàng đều phải được sắp xếp vào các khối hình chữ nhật/hình vuông gạch lát.
* **Whitespace (Khoảng trắng):** Áp dụng padding rất hào phóng (rộng rãi) bên trong các thẻ bento (`p-6` hoặc `p-8`) để nội dung được "thở". Khoảng cách giữa các thẻ bento (`gap`) luôn đồng đều (ví dụ: `gap-4` hoặc `gap-6`).
* **Navigation:** Sidebar hoặc Top Navbar tối giản, chỉ có icon line-art sắc nét và chữ nhỏ. Chừa lại toàn bộ "sân khấu" cho nội dung Bento grid.
