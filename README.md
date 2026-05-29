# 🚗 AutoWash Pro — Hệ thống Đặt lịch Rửa xe Thông minh

> **SWP391** · FPT University · Summer 2026

AutoWash Pro là hệ thống Web tích hợp quản lý đặt lịch và vận hành rửa xe thông minh cho một cửa hàng duy nhất (Single-Store). Hệ thống tập trung tối ưu hóa hiệu suất cầu nâng, quản lý nhân viên rửa xe (Car Washer), cung cấp quy trình kiểm tra xe chặt chẽ (Vehicle Inspection) và nâng cao trải nghiệm khách hàng qua chương trình Loyalty.

---

## 👥 Thành viên Dự án (Team)

| MSSV | Họ và tên | Vai trò | Công nghệ |
|---|---|---|---|
| **SE170313** | Nguyễn Thành Đạt | ★ Leader / BA / Frontend | ReactJS |
| **SE170044** | Nguyễn Thành Toàn | Backend | .NET |
| **SE170434** | Nguyễn Anh Tuấn | Backend | .NET |
| **SE185025** | Nguyễn Trí Vũ | Frontend | ReactJS |

---

## 📁 Hệ thống Tài liệu Thiết kế (Design Docs)

Các tài liệu thiết kế chi tiết nằm tại thư mục [docs/](docs) đã được hoàn thiện và đồng bộ hóa:

| Tài liệu | Mô tả | Trạng thái |
|---|---|---|
| [📋 BRD — Business Requirements](docs/brd_he_thong_rua_xe_thong_minh.md) | Đặc tả yêu cầu nghiệp vụ, luồng xử lý, quy tắc phạt điểm uy tín, kịch bản demo | ✅ Hoàn thành (v2.4) |
| [🛒 Service Catalog](docs/service_catalog.md) | Danh mục chi tiết 36 dịch vụ chia làm 5 nhóm chính (WASH & FLEX) | ✅ Hoàn thành (v1.0) |
| [🗄️ ERD Diagram](docs/erd_diagram.md) | Sơ đồ cơ sở dữ liệu chi tiết với 25 thực thể được chuẩn hóa | ✅ Hoàn thành (v1.0) |
| [🔌 API Contract](docs/api_contract.md) | Tài liệu mô tả chi tiết khoảng 73 endpoints API phân quyền theo 5 roles chính | ✅ Hoàn thành (v1.0) |
| [🖼️ Wireframe & UI Flow](docs/ui_flow_wireframe.md) | Sơ đồ chuyển trang, chi tiết wireframe của từng màn hình (Customer, Washer, Manager, Admin) | ✅ Hoàn thành (v1.0) |

---

## 🏗️ Kiến trúc Hệ thống

Hệ thống được phát triển hoàn toàn trên nền tảng **Web** (Responsive trên Mobile cho Customer và Car Washer; Desktop-first cho Manager và Admin). Không xây dựng Mobile App độc lập.

```
AutoWash Pro (Single Store)
├── 🌐 Customer Web     (React + Next.js - Responsive)
├── 🌐 Washer Web       (React + Next.js - Responsive)
├── 🌐 Manager Web      (React + Next.js - Desktop)
├── 🌐 Admin Web        (React + Next.js - Desktop)
└── 🔧 Backend API      (.NET Core + SQL Server)
```

### Công nghệ Sử dụng (Tech Stack)

*   **Backend:** .NET (ASP.NET Core 8)
*   **Database:** SQL Server
*   **Frontend Web:** React + Next.js
*   **Authentication:** JWT (Access Token + Refresh Token)
*   **Payment Gateway:** Tích hợp PayOS (Thanh toán qua mã QR động)
*   **Storage Cloud:** Firebase Storage hoặc Amazon S3 (để lưu trữ ảnh biên bản kiểm tra xe, ảnh profile)
*   **Notification:** Email Service (Gửi mail xác nhận, mã OTP, Welcome Email cho khách Walk-in)

---

## 🎯 Các Quy tắc Nghiệp vụ nổi bật (Business Logic)

### 1. Đặt lịch Hai phân lớp (2-tier Booking)
*   **WASH (Phân nhóm 1 - Chiếm cầu nâng):** Đặt lịch các dịch vụ rửa xe thông dụng. Hệ thống chặn các slot 30 phút liên tiếp (`ceil(duration/30)`), kiểm tra Capacity (số lượng cầu nâng tối đa) để đảm bảo không bị quá tải.
*   **FLEX (Phân nhóm 2-5 - Không chiếm cầu nâng):** Dành cho các dịch vụ chăm sóc nâng cao (vệ sinh nội thất, đánh bóng, phủ ceramic...). Khách chọn slot giờ hẹn tư vấn bất kỳ (từ 7:00 – 17:30). Không kiểm tra capacity cầu nâng, Manager sẽ phân bổ Car Washer thủ công dựa trên workload thực tế.

### 2. Yêu cầu Đăng nhập bắt buộc (Login Required)
*   Để tăng tính định danh và phòng chống spam lịch ảo, hệ thống **không hỗ trợ Guest Booking**. Khách đặt lịch online bắt buộc phải đăng nhập tài khoản.
*   Không còn cơ chế tài khoản ảo (Shadow Account) phức tạp.

### 3. Đặt lịch Trực tiếp tại quầy (Walk-in Booking)
*   Manager có thể tạo tài khoản và đặt lịch trực tiếp cho khách vãng lai bằng Số điện thoại.
*   Nếu khách hàng chưa có tài khoản, Manager tạo tài khoản (ACTIVE) với mật khẩu tạm thời. Hệ thống sẽ tự động kích hoạt gửi **Welcome Email** chứa thông tin tài khoản, mật khẩu tạm và link đổi mật khẩu.
*   Booking tạo qua luồng Walk-in sẽ tự động bỏ qua bước xác nhận trực tuyến, chuyển thẳng sang trạng thái `ASSIGNED` (Đã phân công).

### 4. Quy trình Kiểm tra xe Chặt chẽ (Vehicle Inspection Flow)
*   **Trước dịch vụ (Pre-service):** Car Washer kiểm tra, ghi nhận tình trạng xe (vết xước, hư hại nếu có) kèm hình ảnh minh chứng. Khách hàng thực hiện xác nhận tình trạng xe thông qua trang Customer Web (`POST /customer/bookings/:id/confirm-vehicle-condition`) để chuyển trạng thái sang `CUSTOMER_CONFIRMED_CONDITION` trước khi thực hiện dịch vụ.
*   **Sau dịch vụ (Post-service):** Đánh giá chất lượng rửa và bàn giao xe an toàn cho khách.

### 5. Hệ thống Thành viên & Ưu đãi (Loyalty Program)
*   Khách hàng tích điểm (Loyalty Points) dựa trên giá trị thanh toán của booking.
*   Phân chia thành 4 hạng thành viên: **MEMBER ➔ SILVER ➔ GOLD ➔ PLATINUM**.
*   Khách hàng sử dụng điểm tích lũy để đổi thành các Voucher giảm giá áp dụng trực tiếp khi đặt lịch.

### 6. Cơ chế Chống spam lịch ảo (Trust Score System)
*   Mỗi tài khoản có điểm uy tín mặc định là **100**.
*   **Quy tắc đặt lịch:** Khách hàng chỉ được đặt lịch trước thời điểm bắt đầu tối thiểu 2 tiếng.
*   **Nhắc lịch (T-2h notification):** Hệ thống gửi email nhắc nhở trước 2 tiếng. Khách hàng phải xác nhận hoặc lịch sẽ tự động hủy (`AUTO_CANCELLED`).
*   **Phạt Uy tín:** Hủy lịch sát giờ (dưới 2 tiếng) hoặc không đến cửa hàng (No-show) sẽ bị trừ điểm uy tín (Trust Score). Nếu điểm uy tín giảm quá sâu, tài khoản sẽ bị khóa quyền đặt lịch online (phải đến tiệm làm Walk-in).

---

## 📊 Trạng thái Dự án (Project Status)

| Giai đoạn (Phase) | Chi tiết | Trạng thái (Status) |
|---|---|---|
| **Phase 1: Yêu cầu & Thiết kế** | BA, BRD, Catalog dịch vụ, ERD Database, Thiết kế API Contract, Thiết kế Wireframe | ✅ Hoàn thành |
| **Phase 2: Xây dựng cơ sở** | Thiết kế Database SQL Server, cài đặt khung Backend (.NET) và Frontend (Next.js) | 🔄 Đang triển khai |
| **Phase 3: Phát triển tính năng** | Phát triển API, dựng UI/UX cho các phân hệ phân quyền | ⏳ Chưa bắt đầu |
| **Phase 4: Tích hợp & Kiểm thử** | Tích hợp PayOS, Firebase Storage, test luồng nghiệp vụ | ⏳ Chưa bắt đầu |
| **Phase 5: Bàn giao & Triển khai** | Triển khai môi trường Production, nghiệm thu đề tài | ⏳ Chưa bắt đầu |

---

## 🚀 Hướng dẫn Cài đặt & Chạy dự án

*(Sẽ cập nhật chi tiết các lệnh cấu hình môi trường, file `.env` khi dự án chuyển sang giai đoạn Coding)*

```bash
# 1. Khởi chạy Backend (.NET Core API)
cd backend
dotnet restore
dotnet run

# 2. Khởi chạy Frontend Web (React + Next.js)
cd frontend-web
npm install
npm run dev
```

---
*Cập nhật lần cuối: 2026-05-29*
