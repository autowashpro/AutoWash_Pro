# API Contract — AutoWash Pro

**Version:** 1.0 | **BRD:** v2.3 | **Ngày:** 2026-05-23

---

## 1. Conventions

### 1.1 Base URL
```
Production : https://api.autowash.pro/v1
Development: http://localhost:5000/api/v1
```

### 1.2 Authentication
```
Header: Authorization: Bearer <jwt_access_token>
```

| Level | Mô tả |
|---|---|
| `PUBLIC` | Không cần token |
| `CUSTOMER` | JWT — role: CUSTOMER |
| `MANAGER` | JWT — role: MANAGER |
| `CAR_WASHER` | JWT — role: CAR_WASHER |
| `ADMIN` | JWT — role: ADMIN |
| `STAFF` | JWT — role: MANAGER hoặc CAR_WASHER |

### 1.3 Standard Response Format

**Success:**
```json
{
  "success": true,
  "data": { },
  "message": "OK",
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

**Error:**
```json
{
  "success": false,
  "error": {
    "code": "BOOKING_NOT_FOUND",
    "message": "Booking không tồn tại hoặc bạn không có quyền truy cập",
    "details": []
  }
}
```

### 1.4 Common Error Codes

| HTTP | Code | Mô tả |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Request body/params không hợp lệ |
| 401 | `UNAUTHORIZED` | Thiếu hoặc token không hợp lệ |
| 403 | `FORBIDDEN` | Không có quyền thực hiện |
| 404 | `NOT_FOUND` | Resource không tồn tại |
| 409 | `CONFLICT` | Trùng lặp dữ liệu (email, phone...) |
| 422 | `BUSINESS_RULE_VIOLATION` | Vi phạm business rule |
| 429 | `RATE_LIMITED` | Quá giới hạn request |
| 500 | `INTERNAL_ERROR` | Lỗi server |

### 1.5 Pagination (List endpoints)

| Query Param | Default | Mô tả |
|---|---|---|
| `page` | 1 | Trang |
| `limit` | 20 | Số item/trang (max 100) |
| `sort` | `created_at` | Field sort |
| `order` | `desc` | `asc` hoặc `desc` |

---

## 2. Auth APIs

### 2.1 Đăng ký tài khoản
```
POST /auth/register
Auth: PUBLIC
```
**Request:**
```json
{
  "full_name": "Nguyễn Văn A",
  "email": "a@gmail.com",
  "phone": "0901234567",
  "password": "Abc@12345"
}
```
**Response:** `201`
```json
{
  "data": {
    "user_id": "uuid",
    "message": "OTP đã được gửi đến email của bạn"
  }
}
```
**Errors:** `409 CONFLICT` nếu phone/email đã tồn tại

---

### 2.2 Xác thực OTP
```
POST /auth/verify-otp
Auth: PUBLIC
```
**Request:**
```json
{ "user_id": "uuid", "otp": "123456" }
```
**Response:**
```json
{
  "data": {
    "access_token": "eyJ...",
    "refresh_token": "eyJ...",
    "expires_in": 3600,
    "user": {
      "user_id": "uuid",
      "full_name": "Nguyễn Văn A",
      "role": "CUSTOMER",
      "membership_tier": "MEMBER"
    }
  }
}
```

---

### 2.3 Đăng nhập
```
POST /auth/login
Auth: PUBLIC
```
**Request:**
```json
{ "phone": "0901234567", "password": "Abc@12345" }
```
**Response:** Giống 2.3

**Errors:** `401` nếu sai thông tin | `403 ACCOUNT_BANNED` nếu bị ban

---

### 2.4 Refresh Token
```
POST /auth/refresh-token
Auth: PUBLIC
```
**Request:**
```json
{ "refresh_token": "eyJ..." }
```
**Response:**
```json
{
  "data": {
    "access_token": "eyJ...",
    "expires_in": 3600
  }
}
```

---

### 2.5 Đăng xuất
```
POST /auth/logout
Auth: CUSTOMER | STAFF | ADMIN
```
**Response:** `200 OK`

---

## 3. Public APIs

### 3.1 Thông tin cửa hàng
```
GET /store-info
Auth: PUBLIC
```
**Response:**
```json
{
  "data": {
    "name": "AutoWash Pro",
    "address": "123 Nguyễn Huệ, Q1, HCM",
    "phone": "028-1234-5678",
    "opening_time": "07:00",
    "closing_time": "18:00"
  }
}
```

---

### 3.2 Danh sách dịch vụ & giá theo loại xe
```
GET /services?vehicle_size=MEDIUM
Auth: PUBLIC
```
**Query:** `vehicle_size` — SMALL | MEDIUM | LARGE (required)

**Response:**
```json
{
  "data": {
    "categories": [
      {
        "category_id": "uuid",
        "name": "Rửa xe & Combo",
        "is_wash_group": true,
        "services": [
          {
            "service_id": "uuid",
            "service_code": "SVC-W-01",
            "name": "Rửa xe cơ bản",
            "estimated_duration_minutes": 30,
            "price": 80000,
            "status": "ACTIVE"
          }
        ]
      }
    ]
  }
}
```

---

### 3.3 Kiểm tra slot trống
```
POST /slots/check-availability
Auth: PUBLIC
```
**Request:**
```json
{
  "date": "2026-06-01",
  "service_ids": ["uuid1", "uuid2"],
  "vehicle_size": "MEDIUM"
}
```
**Response:**
```json
{
  "data": {
    "booking_type": "WASH",
    "num_slots_required": 2,
    "estimated_duration_minutes": 60,
    "available_slots": [
      {
        "slot_id": "uuid",
        "start_time": "09:00",
        "end_time": "09:30",
        "remaining_capacity": 3
      }
    ],
    "booking_window_note": "Bạn có thể đặt tối đa 3 ngày trước"
  }
}
```
> FLEX booking: `num_slots_required = 1`, `remaining_capacity` không hiển thị

---

### 3.4 Reward Catalog (public)

```
GET /rewards/catalog
Auth: PUBLIC
```
**Response:**
```json
{
  "data": [
    {
      "reward_id": "uuid",
      "name": "Rửa xe miễn phí",
      "reward_type": "FREE_WASH",
      "points_required": 500,
      "value": 80000,
      "min_tier_required": null,
      "valid_days": 30
    }
  ]
}
```

---

## 4. Customer APIs

### 4.1 Xem profile
```
GET /customer/profile
Auth: CUSTOMER
```
**Response:**
```json
{
  "data": {
    "user_id": "uuid",
    "full_name": "Nguyễn Văn A",
    "email": "a@gmail.com",
    "phone": "0901234567",
    "membership_tier": "SILVER",
    "total_points": 1250,
    "trust_score": 95,
    "total_spending_12m": 3500000,
    "tier_review_at": "2027-05-10T00:00:00Z",
    "booking_window_days": 7
  }
}
```

---

### 4.2 Cập nhật profile
```
PUT /customer/profile
Auth: CUSTOMER
```
**Request:**
```json
{ "full_name": "Nguyễn Văn B", "birth_month": 8 }
```
> Email và phone không được đổi qua endpoint này.

---

### 4.3 Danh sách xe
```
GET /customer/vehicles
Auth: CUSTOMER
```

### 4.4 Thêm xe
```
POST /customer/vehicles
Auth: CUSTOMER
```
**Request:**
```json
{
  "license_plate": "51A-12345",
  "brand": "Toyota",
  "model": "Camry",
  "color": "Trắng",
  "vehicle_size": "MEDIUM",
  "notes": ""
}
```
**Errors:** `409` nếu biển số đã tồn tại

---

### 4.5 Cập nhật xe
```
PUT /customer/vehicles/:vehicle_id
Auth: CUSTOMER
```

### 4.6 Xoá xe (soft delete)
```
DELETE /customer/vehicles/:vehicle_id
Auth: CUSTOMER
```
**Errors:** `422` nếu xe đang có booking active

---

### 4.7 Giữ slot (bước 1 booking)
```
POST /bookings/hold-slot
Auth: CUSTOMER
```
> Giữ slot 10 phút. Trả về `slot_hold_token` cho bước tạo booking.

**Request:**
```json
{
  "slot_id": "uuid",
  "service_ids": ["uuid1", "uuid2"],
  "vehicle_size": "MEDIUM"
}
```
**Response:**
```json
{
  "data": {
    "slot_hold_id": "uuid",
    "slot_hold_token": "tok_abc123",
    "expires_at": "2026-06-01T09:10:00Z",
    "booking_type": "WASH",
    "num_slots": 2,
    "estimated_total_price": 160000,
    "estimated_duration_minutes": 60
  }
}
```
**Errors:**
- `422 SLOT_FULLY_BOOKED`
- `422 SLOT_WINDOW_EXCEEDED` — ngoài booking window của tier
- `422 CONSECUTIVE_SLOTS_UNAVAILABLE` — WASH: không đủ slot liên tiếp

---

### 4.8 Tạo booking (bước 2)
```
POST /bookings
Auth: CUSTOMER
```
**Request:**
```json
{
  "slot_hold_token": "tok_abc123",
  "vehicle_id": "uuid",
  "service_ids": ["uuid1", "uuid2"],
  "voucher_code": "VCH-XXXXXX",
  "notes": "Xe có vết xước nhỏ bên hông phải"
}
```

**Response:** `201`
```json
{
  "data": {
    "booking_id": "uuid",
    "booking_source": "ONLINE",
    "status": "CONFIRMED",
    "booking_type": "WASH",
    "slot": { "date": "2026-06-01", "start_time": "09:00" },
    "services": [{ "name": "Rửa xe cơ bản", "price": 80000 }],
    "estimated_total_price": 160000,
    "discount_amount": 0,
    "final_estimate": 160000
  }
}
```
**Errors:**
- `410 SLOT_HOLD_EXPIRED`
- `422 VOUCHER_INVALID`
- `422 MAX_ACTIVE_BOOKINGS_EXCEEDED`
- `422 DUPLICATE_VEHICLE_BOOKING`
- `422 TRUST_SCORE_BLOCKED`

---

### 4.9 Danh sách booking
```
GET /customer/bookings?status=CONFIRMED&page=1&limit=10
Auth: CUSTOMER
```

### 4.10 Chi tiết booking
```
GET /customer/bookings/:booking_id
Auth: CUSTOMER
```

---

### 4.11 Xác nhận T-2h
```
POST /customer/bookings/:booking_id/confirm-attendance
Auth: CUSTOMER | PUBLIC (via email token)
```
**Request:**
```json
{ "confirm_token": "tok_t2h_abc" }
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "CONFIRMED",
    "message": "Đã xác nhận. Hẹn gặp bạn lúc 09:00!"
  }
}
```

---

### 4.12 Hủy booking
```
DELETE /customer/bookings/:booking_id
Auth: CUSTOMER
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "CANCELLED_BY_CUSTOMER",
    "trust_score_change": -20,
    "reason": "Hủy trong vòng 2 giờ trước giờ hẹn"
  }
}
```
**Errors:**
- `422 CANCEL_NOT_ALLOWED` — đã confirm T-2h
- `422 CANCEL_NOT_ALLOWED` — đang IN_PROGRESS

---

### 4.13 Đánh giá booking
```
POST /customer/bookings/:booking_id/ratings
Auth: CUSTOMER
```
> Chỉ tạo được khi booking = CLOSED.

**Request:**
```json
{
  "overall_score": 5,
  "service_quality_score": 5,
  "staff_attitude_score": 4,
  "comment": "Nhân viên nhiệt tình, xe sạch bóng!"
}
```
**Errors:** `422 BOOKING_NOT_CLOSED` | `409 ALREADY_RATED`

---

### 4.14 Gửi khiếu nại
```
POST /customer/bookings/:booking_id/complaints
Auth: CUSTOMER
Content-Type: multipart/form-data
```
```
title       : Xe bị trầy xước
description : Phát hiện vết trầy sau khi nhận xe
files[]     : [file1.jpg, file2.jpg]
```
**Errors:** `422 COMPLAINT_WINDOW_EXPIRED` — quá 7 ngày từ CLOSED

---

### 4.15 Lịch sử loyalty & điểm
```
GET /customer/loyalty
Auth: CUSTOMER
```
**Response:**
```json
{
  "data": {
    "total_points": 1250,
    "membership_tier": "SILVER",
    "point_multiplier": 1.5,
    "total_spending_12m": 3500000,
    "next_tier": "GOLD",
    "spending_to_next_tier": 1500000,
    "transactions": [
      {
        "transaction_id": "uuid",
        "type": "EARN",
        "points": 160,
        "description": "Booking #BK-001 — Rửa xe cơ bản",
        "created_at": "2026-05-20T12:00:00Z"
      }
    ]
  }
}
```

---

### 4.16 Đổi điểm lấy voucher
```
POST /customer/loyalty/redeem
Auth: CUSTOMER
```
**Request:**
```json
{ "reward_id": "uuid" }
```
**Response:**
```json
{
  "data": {
    "customer_reward_id": "uuid",
    "voucher_code": "VCH-ABCDEF",
    "reward_name": "Rửa xe miễn phí",
    "expires_at": "2026-07-01T00:00:00Z",
    "points_deducted": 500,
    "remaining_points": 750
  }
}
```
**Errors:** `422 INSUFFICIENT_POINTS` | `422 TIER_NOT_ELIGIBLE`

---

### 4.17 Danh sách voucher của tôi
```
GET /customer/rewards?status=ACTIVE
Auth: CUSTOMER
```

---

## 5. Manager APIs

### 5.1 Dashboard — Danh sách booking
```
GET /manager/bookings?date=2026-06-01&status=CONFIRMED&page=1
Auth: MANAGER
```
**Query:** `date`, `status`, `booking_type`, `car_washer_id`

**Response:**
```json
{
  "data": [
    {
      "booking_id": "uuid",
      "customer_name": "Nguyễn Văn A",
      "phone": "0901234567",
      "license_plate": "51A-12345",
      "vehicle_size": "MEDIUM",
      "services_summary": "Rửa xe, Hút bụi",
      "slot_start_time": "09:00",
      "booking_type": "WASH",
      "num_slots": 2,
      "status": "CONFIRMED",
      "trust_score": 95,
      "assigned_washer": "Trần Văn B"
    }
  ]
}
```

---

### 5.2 Chi tiết booking (Manager view)
```
GET /manager/bookings/:booking_id
Auth: MANAGER
```

### 5.3 Xác nhận booking (Pending → Confirmed)
```
PUT /manager/bookings/:booking_id/confirm
Auth: MANAGER
```

---

### 5.4 Tạo Walk-in Booking
```
POST /manager/bookings/walk-in
Auth: MANAGER
```
> Manager tạo tài khoản cho khách walk-in (hoặc tìm tài khoản theo SĐT nếu đã tồn tại), sau đó tạo booking. Nếu tài khoản mới được tạo, hệ thống sẽ tự động kích hoạt gửi Welcome Email chứa thông tin đăng nhập, mật khẩu tạm thời (`temp_password`) và link thay đổi mật khẩu.

**Request:**
```json
{
  "customer_info": {
    "full_name": "Lê Thị C",
    "phone": "0912345678",
    "email": "c@gmail.com",
    "temp_password": "Temp@12345"
  },
  "vehicle": {
    "license_plate": "51B-99999",
    "brand": "Honda",
    "model": "CR-V",
    "color": "Trắng",
    "vehicle_size": "LARGE"
  },
  "slot_id": "uuid",
  "service_ids": ["uuid1"],
  "notes": "Khách walk-in"
}
```
**Response:** `201`
```json
{
  "data": {
    "booking_id": "uuid",
    "booking_source": "WALK_IN",
    "status": "CONFIRMED",
    "user_id": "uuid",
    "account_created": true,
    "message": "Tài khoản mới đã được tạo cho khách."
  }
}
```
> `account_created`: `true` nếu tài khoản mới được tạo, `false` nếu đã tồn tại theo SĐT.

---

### 5.5 Phân công Car Washer
```
PUT /manager/bookings/:booking_id/assign
Auth: MANAGER
```
**Request:**
```json
{ "car_washer_id": "uuid" }
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "ASSIGNED",
    "car_washer_id": "uuid",
    "car_washer_name": "Trần Văn B"
  }
}
```
**Errors:** `422 BOOKING_STATUS_INVALID` — chỉ assign khi CONFIRMED

---

### 5.6 Reassign Car Washer
```
PUT /manager/bookings/:booking_id/reassign
Auth: MANAGER
```
**Request:**
```json
{ "car_washer_id": "uuid", "reason": "Nhân viên cũ bị bệnh" }
```
> Chỉ khi ASSIGNED (trước CHECKED_IN). Tạo BookingAssignment mới, CANCEL cái cũ.

---

### 5.7 Check-in hộ khách
```
POST /manager/bookings/:booking_id/check-in
Auth: MANAGER
```
**Response:** `status → CHECKED_IN`

---

### 5.8 Đánh dấu NO_SHOW
```
PUT /manager/bookings/:booking_id/no-show
Auth: MANAGER
```
**Request:**
```json
{ "note": "Khách không đến sau 10 phút chờ" }
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "NO_SHOW",
    "trust_score_change": -40,
    "customer_trust_score_after": 55
  }
}
```
**Errors:** `422 NO_SHOW_TOO_EARLY`

---

### 5.9 Hủy booking On-site
```
POST /manager/bookings/:booking_id/cancel
Auth: MANAGER
```
**Request:**
```json
{
  "penalty_applied": false,
  "cancellation_reason": "Máy rửa xe bị hỏng, không thể phục vụ"
}
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "CANCELLED_BY_MANAGER",
    "penalty_applied": false,
    "trust_score_change": 0
  }
}
```
**Errors:** `422 CANNOT_CANCEL_IN_PROGRESS`

---

### 5.10 Chỉnh sửa dịch vụ tại chỗ
```
PUT /manager/bookings/:booking_id/services
Auth: MANAGER
```
**Request:**
```json
{
  "service_ids": ["uuid1", "uuid2", "uuid3"],
  "note": "Khách yêu cầu thêm dịch vụ hút bụi"
}
```
**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "services": [],
    "estimated_total_price": 250000,
    "estimated_duration_minutes": 75,
    "pending_customer_confirmation": true
  }
}
```
> Khách phải xác nhận giá mới trước khi bắt đầu.

**Errors:** `422 MODIFICATION_NOT_ALLOWED` — đã IN_PROGRESS

---

### 5.11 Xem slot theo ngày
```
GET /manager/slots?date=2026-06-01
Auth: MANAGER
```
**Response:**
```json
{
  "data": [
    {
      "slot_id": "uuid",
      "start_time": "09:00",
      "end_time": "09:30",
      "capacity": 3,
      "booked_count": 2,
      "held_count": 1,
      "remaining": 0,
      "status": "FULLY_BOOKED",
      "bookings": [
        {
          "booking_id": "uuid",
          "customer_name": "Nguyễn Văn A",
          "license_plate": "51A-12345",
          "status": "CONFIRMED"
        }
      ]
    }
  ]
}
```

---

### 5.12 Cập nhật slot instance
```
PUT /manager/slots/:slot_id
Auth: MANAGER
```
**Request:**
```json
{ "washers_online": 3, "active_bays": 2, "status": "AVAILABLE" }
```
> Hệ thống tự tính lại `capacity = min(bays, floor(washers/min_washer))`.

---

### 5.13 Tạo payment record
```
POST /manager/bookings/:booking_id/payment
Auth: MANAGER
```
**Request:**
```json
{ "method": "CASH", "amount": 160000 }
```
> Nếu `method: PAYOS` → hệ thống tạo PayOS link và trả về.

**Response:**
```json
{
  "data": {
    "payment_id": "uuid",
    "method": "CASH",
    "status": "PAID",
    "paid_at": "2026-06-01T10:30:00Z"
  }
}
```

---

### 5.14 Retry PayOS link
```
POST /manager/bookings/:booking_id/payment/retry-payos
Auth: MANAGER
```
**Response:**
```json
{
  "data": {
    "payment_link": "https://pay.payos.vn/web/...",
    "expires_at": "2026-06-01T11:00:00Z"
  }
}
```

---

### 5.15 Danh sách complaint
```
GET /manager/complaints?status=OPEN&page=1
Auth: MANAGER
```

### 5.16 Chi tiết complaint
```
GET /manager/complaints/:complaint_id
Auth: MANAGER
```

---

### 5.17 Xử lý complaint
```
PUT /manager/complaints/:complaint_id
Auth: MANAGER
```
**Request:**
```json
{
  "status": "RESOLVED",
  "resolution_note": "Đã kiểm tra camera, cung cấp 1 lần rửa xe miễn phí.",
  "loyalty_adjustment": {
    "customer_id": "uuid",
    "points": 200,
    "description": "Bồi thường khiếu nại #CPL-001"
  }
}
```

---

### 5.18 Điều chỉnh Trust Score thủ công
```
POST /manager/customers/:customer_id/trust-score/adjust
Auth: MANAGER
```
**Request:**
```json
{ "score_change": 10, "reason": "Khách thường xuyên, unlock sau review" }
```

---

### 5.19 Điều chỉnh điểm Loyalty thủ công
```
POST /manager/customers/:customer_id/loyalty/adjust
Auth: MANAGER
```
**Request:**
```json
{ "points": 200, "reason": "Bồi thường từ complaint #CPL-001" }
```

---

### 5.20 Mở khóa tài khoản bị block
```
PUT /manager/customers/:customer_id/unblock
Auth: MANAGER
```

---

### 5.21 Báo cáo booking
```
GET /manager/reports/bookings?from=2026-06-01&to=2026-06-30
Auth: MANAGER
```
**Response:**
```json
{
  "data": {
    "total_bookings": 312,
    "by_status": {
      "COMPLETED": 285,
      "NO_SHOW": 12,
      "CANCELLED": 15
    },
    "by_type": { "WASH": 200, "FLEX": 112 },
    "total_revenue": 45600000
  }
}
```
> `CANCELLED` là tổng hợp của `CANCELLED_BY_CUSTOMER + CANCELLED_BY_MANAGER + AUTO_CANCELLED`.

---

### 5.22 Báo cáo hiệu suất Car Washer
```
GET /manager/reports/car-washers?from=2026-06-01&to=2026-06-30
Auth: MANAGER
```
**Response:**
```json
{
  "data": [
    {
      "car_washer_id": "uuid",
      "full_name": "Trần Văn B",
      "total_assigned": 85,
      "total_completed": 82,
      "avg_overall_score": 4.7,
      "avg_service_quality_score": 4.8
    }
  ]
}
```

---

## 6. Car Washer APIs

### 6.1 Danh sách task hôm nay
```
GET /washer/tasks?date=2026-06-01
Auth: CAR_WASHER
```
**Response:**
```json
{
  "data": [
    {
      "booking_id": "uuid",
      "assignment_id": "uuid",
      "slot_start_time": "09:00",
      "customer_name": "Nguyễn Văn A",
      "license_plate": "51A-12345",
      "vehicle_size": "MEDIUM",
      "services": ["Rửa xe cơ bản", "Hút bụi"],
      "booking_type": "WASH",
      "status": "ASSIGNED",
      "booking_notes": "Xe có vết xước nhỏ"
    }
  ]
}
```

### 6.2 Chi tiết task
```
GET /washer/tasks/:booking_id
Auth: CAR_WASHER
```

---

### 6.3 Xác nhận khách đến (Check-in)
```
POST /washer/tasks/:booking_id/check-in
Auth: CAR_WASHER
```
**Response:** `status → CHECKED_IN`

---

### 6.4 Tạo inspection
```
POST /washer/tasks/:booking_id/inspections
Auth: CAR_WASHER
```
**Request:**
```json
{
  "inspection_type": "BEFORE_SERVICE",
  "exterior_condition": "Xe bẩn, có muối bám ở gầm. Có 1 vết xước ở cửa sau phải.",
  "interior_condition": "Nội thất bình thường",
  "notes": "Đã chụp ảnh vết xước trước khi rửa"
}
```
**Response:** `201` — `status → VEHICLE_INSPECTED`

---

### 6.5 Upload ảnh inspection
```
POST /washer/tasks/:booking_id/inspections/:inspection_id/images
Auth: CAR_WASHER
Content-Type: multipart/form-data
```
```
files[]     : [img1.jpg, img2.jpg]
description : Vết xước cánh cửa sau phải
```

---

### 6.6 Bắt đầu dịch vụ
```
PUT /washer/tasks/:booking_id/start
Auth: CAR_WASHER
```
> Chỉ sau khi `customer_confirmed = true` trên inspection BEFORE_SERVICE. Khách xác nhận qua endpoint `POST /customer/bookings/:booking_id/confirm-vehicle-condition`.

**Response:** `status → IN_PROGRESS`

**Errors:** `422 INSPECTION_NOT_CONFIRMED`

---

### 6.6b Xác nhận tình trạng xe (Customer)
```
POST /customer/bookings/:booking_id/confirm-vehicle-condition
Auth: CUSTOMER
```
> Khách xác nhận inspection BEFORE_SERVICE sau khi xem kết quả kiểm tra xe. Chuyển trạng thái booking sang `CUSTOMER_CONFIRMED_CONDITION`.

**Response:**
```json
{
  "data": {
    "booking_id": "uuid",
    "status": "CUSTOMER_CONFIRMED_CONDITION",
    "message": "Đã xác nhận tình trạng xe. Dịch vụ sắp bắt đầu."
  }
}
```
**Errors:** `422 INSPECTION_NOT_FOUND` | `409 ALREADY_CONFIRMED`

---

### 6.7 Hoàn thành dịch vụ
```
PUT /washer/tasks/:booking_id/complete
Auth: CAR_WASHER
```
**Request:**
```json
{ "after_inspection_notes": "Xe đã sạch, không phát sinh vấn đề" }
```
**Response:** `status → COMPLETED`

---

## 7. Admin APIs

### 7.1 Quản lý thông tin cửa hàng (StoreInfo)
```
GET /admin/store-info
PUT /admin/store-info
Auth: ADMIN
```
**PUT Request:**
```json
{
  "name": "AutoWash Pro",
  "address": "456 Đinh Tiên Hoàng, Q.Bình Thạnh",
  "phone": "028-9876-5432",
  "opening_time": "07:00",
  "closing_time": "18:00"
}
```

---

### 7.2 Quản lý dịch vụ
```
GET  /admin/services
POST /admin/services
PUT  /admin/services/:service_id
Auth: ADMIN
```
**POST Request:**
```json
{
  "category_id": "uuid",
  "service_code": "SVC-W-05",
  "name": "Rửa xe siêu sạch",
  "estimated_duration_minutes": 60,
  "prices": [
    { "vehicle_size": "SMALL",  "price": 100000 },
    { "vehicle_size": "MEDIUM", "price": 130000 },
    { "vehicle_size": "LARGE",  "price": 160000 }
  ]
}
```

---

### 7.3 Cập nhật bảng giá (versioned)
```
POST /admin/services/:service_id/prices
Auth: ADMIN
```
**Request:**
```json
{
  "effective_from": "2026-07-01",
  "prices": [
    { "vehicle_size": "SMALL",  "price": 110000 },
    { "vehicle_size": "MEDIUM", "price": 140000 },
    { "vehicle_size": "LARGE",  "price": 170000 }
  ]
}
```
> Tạo bản ghi ServicePrice mới — không xóa bản cũ (giữ lịch sử giá).

---

### 7.4 Quản lý Slot Template
```
GET  /admin/slot-templates
POST /admin/slot-templates
PUT  /admin/slot-templates/:template_id
Auth: ADMIN
```
**POST Request:**
```json
{
  "day_of_week": 1,
  "start_time": "07:00",
  "end_time": "07:30",
  "active_bays": 3,
  "min_washers_per_car": 1
}
```

---

### 7.5 Generate SlotInstance từ Template
```
POST /admin/slots/generate
Auth: ADMIN
```
**Request:**
```json
{ "from_date": "2026-07-01", "to_date": "2026-07-31" }
```

---

### 7.6 Quản lý Users
```
GET /admin/users?role=MANAGER&status=ACTIVE
PUT /admin/users/:user_id/status
Auth: ADMIN
```
**PUT Request:**
```json
{ "status": "BANNED", "note": "Vi phạm nghiêm trọng điều khoản" }
```

---

### 7.7 Tạo tài khoản Staff
```
POST /admin/users/staff
Auth: ADMIN
```
**Request:**
```json
{
  "full_name": "Trần Văn B",
  "email": "b@autowash.pro",
  "phone": "0987654321",
  "role": "CAR_WASHER",
  "password": "Temp@12345"
}
```

---

### 7.8 Cấu hình Loyalty
```
GET /admin/config/loyalty
PUT /admin/config/loyalty
Auth: ADMIN
```
**PUT Request:**
```json
{ "conversion_rate": 10000, "point_expiry_months": 12 }
```

---

### 7.9 Cấu hình Membership Tier
```
GET /admin/config/tiers
PUT /admin/config/tiers/:tier
Auth: ADMIN
```
**PUT Request (tier = GOLD):**
```json
{
  "spending_threshold": 5000000,
  "point_multiplier": 2.0,
  "booking_window_days": 10
}
```

---

### 7.10 Quản lý Reward Catalog
```
GET  /admin/rewards/catalog
POST /admin/rewards/catalog
PUT  /admin/rewards/catalog/:reward_id
Auth: ADMIN
```
**POST Request:**
```json
{
  "name": "Giảm 20% lần rửa tiếp theo",
  "reward_type": "DISCOUNT_VOUCHER",
  "points_required": 300,
  "value": 30000,
  "valid_days": 30,
  "min_tier_required": "SILVER"
}
```

---

### 7.11 Báo cáo toàn hệ thống
```
GET /admin/reports/revenue?from=2026-06-01&to=2026-06-30
GET /admin/reports/trust-score?threshold=60
GET /admin/reports/loyalty?tier=GOLD
GET /admin/reports/no-show-trend
Auth: ADMIN
```

---

### 7.12 Xem Audit Log
```
GET /admin/audit-logs?entity_type=Booking&entity_id=uuid&page=1
Auth: ADMIN
```

---

## 8. Notification APIs

### 8.1 Danh sách thông báo
```
GET /notifications?is_read=false&page=1
Auth: CUSTOMER | STAFF
```
**Response:**
```json
{
  "data": [
    {
      "notification_id": "uuid",
      "type": "BOOKING_CONFIRMED",
      "title": "Đặt lịch thành công",
      "body": "Booking lúc 09:00 ngày 01/06 đã được xác nhận.",
      "is_read": false,
      "booking_id": "uuid",
      "created_at": "2026-05-20T10:00:00Z"
    }
  ]
}
```

### 8.2 Đánh dấu đã đọc
```
PUT /notifications/:notification_id/read
PUT /notifications/read-all
Auth: CUSTOMER | STAFF
```

---

## 9. Webhook

### 9.1 PayOS Callback
```
POST /webhooks/payos
Auth: PUBLIC (xác thực bằng PayOS signature)
Header: x-payos-signature: <hmac_sha256>
```
**Request (từ PayOS):**
```json
{
  "code": "00",
  "desc": "Thành công",
  "data": {
    "orderCode": 1234567890,
    "amount": 160000,
    "status": "PAID",
    "paymentLinkId": "link_xxx"
  },
  "signature": "abc123..."
}
```
**Business Logic:**
1. Verify HMAC signature
2. Tìm Payment theo `payos_order_code`
3. Cập nhật `Payment.status = PAID`, `paid_at = now`
4. Trigger: cộng loyalty points → `Booking.status = CLOSED` → gửi notification

**Response:**
```json
{ "code": "00", "desc": "OK" }
```

---

## 10. Business Error Code Reference

| Code | HTTP | Mô tả |
|---|---|---|
| `SLOT_FULLY_BOOKED` | 422 | Slot đã đầy capacity |
| `CONSECUTIVE_SLOTS_UNAVAILABLE` | 422 | WASH: không đủ slot liên tiếp |
| `SLOT_WINDOW_EXCEEDED` | 422 | Ngoài booking window của tier |
| `SLOT_TOO_SOON` | 422 | Slot < 2 giờ từ hiện tại |
| `SLOT_HOLD_EXPIRED` | 410 | Slot hold token hết hạn 10 phút |
| `VOUCHER_INVALID` | 422 | Voucher không hợp lệ / hết hạn / đã dùng |
| `VOUCHER_TIER_INELIGIBLE` | 422 | Tier không đủ điều kiện dùng voucher |
| `MAX_ACTIVE_BOOKINGS_EXCEEDED` | 422 | Đã có 2 booking active |
| `DUPLICATE_VEHICLE_BOOKING` | 422 | Xe đã có booking active |
| `TRUST_SCORE_BLOCKED` | 403 | Trust Score < 20 |
| `CANCEL_NOT_ALLOWED` | 422 | Không được hủy ở trạng thái hiện tại |
| `CANNOT_CANCEL_IN_PROGRESS` | 422 | Không thể hủy khi đang IN_PROGRESS |
| `INSPECTION_NOT_CONFIRMED` | 422 | Khách chưa xác nhận inspection |
| `MODIFICATION_NOT_ALLOWED` | 422 | Không thể sửa dịch vụ sau IN_PROGRESS |
| `NO_SHOW_TOO_EARLY` | 422 | Chưa đủ 10 phút để đánh dấu NO_SHOW |
| `COMPLAINT_WINDOW_EXPIRED` | 422 | Quá 7 ngày từ CLOSED |
| `ALREADY_RATED` | 409 | Booking đã được đánh giá |
| `BOOKING_NOT_CLOSED` | 422 | Chỉ rating khi CLOSED |
| `ACCOUNT_BANNED` | 403 | Tài khoản bị khóa vĩnh viễn |
| `INSUFFICIENT_POINTS` | 422 | Không đủ điểm để đổi reward |
| `TIER_NOT_ELIGIBLE` | 422 | Tier không đủ để đổi reward này |
