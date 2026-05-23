# 🚗 AutoWash Pro — Hệ thống Đặt lịch Rửa xe Thông minh

> **SWP391** · FPT University · Summer 2026

Hệ thống quản lý đặt lịch rửa xe đa chi nhánh với các tính năng: booking thông minh 2 loại (WASH / FLEX), loyalty program, quản lý vận hành real-time.

---

## 👥 Team

| Họ tên | MSSV | Vai trò |
|---|---|---|
| [Tên thành viên 1] | [MSSV] | Team Leader / Backend |
| [Tên thành viên 2] | [MSSV] | Backend |
| [Tên thành viên 3] | [MSSV] | Frontend |
| [Tên thành viên 4] | [MSSV] | Frontend |
| [Tên thành viên 5] | [MSSV] | Tester / BA |

---

## 📁 Tài liệu Thiết kế

| Tài liệu | Mô tả | Phiên bản |
|---|---|---|
| [📋 BRD — Business Requirements](docs/brd_he_thong_rua_xe_thong_minh.md) | Đặc tả nghiệp vụ, state machine, edge cases | v2.3 |
| [🛒 Service Catalog](docs/service_catalog.md) | Danh mục dịch vụ, phân nhóm WASH / FLEX | v1.0 |
| [🗄️ ERD Diagram](docs/erd_diagram.md) | Entity Relationship Diagram (25 entities) | v1.0 |
| [🔌 API Contract](docs/api_contract.md) | Đặc tả API ~72 endpoints, 6 roles | v1.0 |
| [🖼️ Wireframe & UI Flow](docs/ui_flow_wireframe.md) | Screen inventory, navigation, flow diagrams | v1.0 |

---

## 🏗️ Kiến trúc hệ thống

```
AutoWash Pro
├── 📱 Customer App    (React Native / Flutter)
├── 🌐 Manager Web     (React / Next.js)
├── 📱 Car Washer App  (React Native / Flutter)
├── ⚙️  Admin Web      (React / Next.js)
└── 🔧 Backend API     (Spring Boot + PostgreSQL)
```

### Tech Stack (dự kiến)
| Layer | Technology |
|---|---|
| Backend | Spring Boot 3.x, Java 21 |
| Database | PostgreSQL 16 |
| Mobile | React Native / Flutter |
| Web | React + Next.js |
| Auth | JWT (Access + Refresh token) |
| Payment | PayOS |
| Storage | Firebase Storage / S3 |

---

## 🎯 Business Logic nổi bật

- **2-tier Booking:** WASH (chiếm slot liên tiếp, kiểm tra capacity) vs FLEX (chọn slot tự do, không chiếm bay)
- **30-Shine Pattern:** CTA bằng SĐT → Shadow/Active account flow
- **T-2h Notification:** Nhắc lịch + phạt Trust Score nếu không phản hồi
- **Loyalty System:** Tích điểm, 4 tiers (MEMBER / SILVER / GOLD / PLATINUM), đổi voucher
- **Multi-branch:** Dữ liệu phân tách theo chi nhánh, thống nhất pricing toàn quốc

---

## 📊 Trạng thái dự án

| Phase | Status |
|---|---|
| ✅ Business Analysis & BRD | Hoàn thành |
| ✅ ERD & Database Design | Hoàn thành |
| ✅ API Contract | Hoàn thành |
| ✅ Wireframe & UI Flow | Hoàn thành |
| 🔄 Implementation | Đang thực hiện |
| ⏳ Testing | Chưa bắt đầu |
| ⏳ Deployment | Chưa bắt đầu |

---

## 🚀 Hướng dẫn chạy dự án

> *(Cập nhật sau khi có code)*

```bash
# Backend
cd backend
./mvnw spring-boot:run

# Frontend Web
cd frontend-web
npm install && npm run dev

# Mobile
cd mobile
npm install && npx expo start
```

---

*Cập nhật lần cuối: 2026-05-23*
