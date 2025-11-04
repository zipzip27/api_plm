# API v2 - PLM Print

## Tổng Quan

API v2 cho phép các đối tác tích hợp với hệ thống PLM Print để tạo đơn hàng,
theo dõi trạng thái, và quản lý số dư tài khoản. API này được thiết kế để sử dụng cho các hệ thống bên ngoài thông qua
xác thực API Key.

**Base URL**: `https://print-api.polimellc.media`

---

## Xác Thực (Authentication)

Tất cả các endpoint trong API v2 yêu cầu xác thực thông qua **API Key**.

### Cách Sử Dụng API Key

Gửi API Key trong header của mỗi request:

```
x-api-key: YOUR_API_KEY_HERE
```

### Ví Dụ Request với cURL

```bash
curl -X GET "https://print-api.polimellc.media/api/v2/orders" \
  -H "x-api-key: YOUR_API_KEY"
```

### Lấy API Key

API Key được cấp bởi hệ thống. Truy cập [link](https://print.polimellc.media/settings) để lấy api key

---

## Danh Sách Endpoints

### 1. Tạo Đơn Hàng Mới

**Endpoint**: `POST /api/v2/orders`

**Mô Tả**: Tạo một đơn hàng in ấn mới. Hệ thống sẽ tự động kiểm tra số dư, tính phí, và tạo các bản ghi liên quan.

#### Request Body

```json
{
  "order_number": "ORD-123456",
  "tracking_number": "TRACK-123456",
  "reference": "REF-001",
  "shipping_label": "https://example.com/label.pdf",
  "shipping_name": "Nguyễn Văn A",
  "shipping_phone": "0901234567",
  "shipping_address": "123 Đường ABC",
  "shipping_address2": "Phòng 456",
  "shipping_city": "Hồ Chí Minh",
  "shipping_state": "HCM",
  "shipping_country": "VN",
  "shipping_zip": "1234",
  "customer_note": "Ghi chú đặc biệt",
  "items": [
    {
      "name": "Áo thun cổ tròn",
      "sku": "TSH-001",
      "quantity": 2,
      "custom_sku": "CUSTOM-TSH-001",
      "designs": [
        {
          "url": "https://example.com/design-front.png",
          "type": "DESIGN_FRONT"
        },
        {
          "url": "https://example.com/design-back.png",
          "type": "DESIGN_BACK"
        }
      ]
    }
  ]
}
```

#### Các Trường

| Trường              | Kiểu              | Bắt Buộc | Mô Tả                                   |
|---------------------|-------------------|----------|-----------------------------------------|
| `order_number`      | string (max 100)  | Có       | Mã đơn hàng duy nhất                    |
| `tracking_number`   | string (max 100)  | Có       | Mã vận đơn                              |
| `shipping_label`    | URL (max 1000)    | Có       | Link đến file nhãn vận chuyển           |
| `shipping_name`     | string (max 100)  | Có       | Tên người nhận                          |
| `shipping_email`    | string (max 100)  | Không    | Email người nhận                        |
| `shipping_phone`    | string (max 20)   | Không    | SDT người nhận                          |
| `shipping_address`  | string (max 100)  | Có       | Địa chỉ giao hàng                       |
| `shipping_address2` | string (max 100)  | Không    | Địa chỉ giao hàng 2                     |
| `shipping_city`     | string (max 100)  | Có       | Thành phố                               |
| `shipping_state`    | string (max 100)  | Có       | Tỉnh/Bang                               |
| `shipping_country`  | string (max 100)  | Có       | Quốc gia                                |
| `shipping_zip`      | string (max 50)   | Có       | Zipcode                                 |
| `customer_note`     | string (max 1000) | Không    | Ghi chú khách hàng                      |
| `items`             | array             | Có       | Danh sách sản phẩm (ít nhất 1 sản phẩm) |

#### Cấu Trúc Item

| Trường       | Kiểu             | Bắt Buộc | Mô Tả                                                                     |
|--------------|------------------|----------|---------------------------------------------------------------------------|
| `sku`        | string (max 100) | Có       | Mã SKU sản phẩm (phải tồn tại trong hệ thống)                             |
| `name`       | string (max 255) | Không    | Tên sản phẩm nếu có                                                       |
| `custom_sku` | string (max 100) | Không    | Mã SKU tùy chỉnh nếu có                                                   |
| `designs`    | array            | Có       | Danh sách file thiết kế (ít nhất 1 file, áo trơn chỉ cần gửi mockup trơn) |

#### Cấu Trúc Design

| Trường | Kiểu           | Mô Tả                  |
|--------|----------------|------------------------|
| `url`  | URL (max 1000) | Link đến file thiết kế |
| `type` | enum           | Loại thiết kế          |

**Các Loại Design (type)**:

- `DESIGN_FRONT`: Thiết kế mặt trước
- `DESIGN_BACK`: Thiết kế mặt sau
- `DESIGN_FOLDER`: Folder thiết kế (tuỳ chọn)
- `MOCKUP`: Mockup
- `OTHER`: Khác

#### Response - Thành Công (201 Created)

```json
{
  "id": 123,
  "group": 1,
  "order_number": "ORD-123456",
  "tracking_number": "123123456789",
  "reference": "REF-001",
  "status": "PENDING",
  "shipping_name": "Nguyễn Văn A",
  "shipping_phone": "0901234567",
  "shipping_address": "123 Đường ABC",
  "shipping_address2": "Phòng 456",
  "shipping_city": "Hồ Chí Minh",
  "shipping_state": "HCM",
  "shipping_country": "VN",
  "shipping_zip": null,
  "shipping_email": null,
  "shipping_label": null,
  "carrier": "USPS",
  "currency_code": null,
  "total_amount": "0.00",
  "total_items": 2,
  "customer_note": "Ghi chú đặc biệt",
  "created_at": "2025-11-02T10:30:00Z"
}
```

#### Response - Lỗi (400 Bad Request)

**Lưu ý**: Tất cả response lỗi đều được chuẩn hóa theo format sau:

**Format**:

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "ERROR_CODE",
    "message": "Thông báo lỗi chính",
    "details": {
      "field_name": [
        "Chi tiết lỗi"
      ]
    }
  }
}
```

**Ví dụ các trường hợp lỗi:**

**1. Mã đơn hàng bị trùng:**

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "order_number": [
        "Order number already exists."
      ]
    }
  }
}
```

**2. SKU không tồn tại:**

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "items.[0]": [
        "Invalid SKU TSH-999"
      ]
    }
  }
}
```

**3. Không đủ số dư:**

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Insufficient balance.",
    "details": null
  }
}
```

**4. Thiếu trường bắt buộc:**

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "shipping_name": [
        "This field is required."
      ],
      "shipping_address": [
        "This field is required."
      ],
      "items": [
        "This field is required."
      ]
    }
  }
}
```

#### Lưu Ý Quan Trọng

1. **Kiểm Tra Số Dư**: Hệ thống sẽ tự động kiểm tra số dư của Group trước khi tạo đơn. Nếu không đủ số dư, request sẽ bị
   từ chối.

2. **Mã Đơn Hàng Duy Nhất**: `order_number` phải là duy nhất trong phạm vi Group của bạn.

3. **SKU Phải Tồn Tại**: Tất cả các SKU trong `items` phải trong hệ thống.

4. URL hình ảnh design sẽ sử dụng link gốc không lưu vào hệ thống, vui lòng đảm bảo link tồn tại được lâu dài

#### Ví Dụ với cURL

```bash
curl -X POST "https://print-api.polimellc.media/api/v2/orders" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "order_number": "ORD-123456",
    "tracking_number": "TRACK-123456",
    "shipping_label": "https://example.com/label.pdf",
    "shipping_name": "Nguyễn Văn A",
    "shipping_address": "123 Đường ABC",
    "shipping_city": "Hồ Chí Minh",
    "shipping_state": "HCM",
    "shipping_country": "VN",
    "items": [
      {
        "sku": "TSH-001",
        "quantity": 2,
        "designs": [
          {
            "url": "https://example.com/design.png",
            "type": "DESIGN_FRONT"
          }
        ]
      }
    ]
  }'
```

---

### 2. Lấy Danh Sách Đơn Hàng

**Endpoint**: `GET /api/v2/orders`

**Mô Tả**: Lấy danh sách tất cả đơn hàng của Group với phân trang và bộ lọc.

#### Query Parameters

| Tham Số           | Kiểu    | Mô Tả                                                  |
|-------------------|---------|--------------------------------------------------------|
| `order_number`    | string  | Lọc theo mã đơn hàng (tìm kiếm gần đúng)               |
| `tracking_number` | string  | Lọc theo mã vận đơn (tìm kiếm gần đúng)                |
| `status`          | enum    | Lọc theo trạng thái đơn hàng                           |
| `date_after`      | string  | Lọc theo ngày (ISO date string)                        |
| `date_before`     | string  | Lọc theo ngày (ISO date string)                        |
| `page`            | integer | Số trang (mặc định: 1)                                 |
| `pageSize`        | integer | Số lượng kết quả mỗi trang (mặc định: 10, tối đa: 100) |

#### Các Trạng Thái Đơn Hàng (status)

| Giá Trị          | Mô Tả              |
|------------------|--------------------|
| `PENDING`        | Đang chờ xử lý     |
| `PROCESSING`     | Đang xử lý         |
| `READY_TO_PRINT` | Sẵn sàng in        |
| `READY_TO_SHIP`  | Sẵn sàng giao hàng |
| `IN_TRANSIT`     | Đang vận chuyển    |
| `DELIVERED`      | Đã giao hàng       |
| `CLOSED`         | Đã đóng            |
| `CANCELLED`      | Đã hủy             |
| `REFUNDED`       | Đã hoàn tiền       |
| `ERROR`          | Lỗi                |

#### Response - Thành Công (200 OK)

```json
{
  "count": 150,
  "next": "https://print-api.polimellc.media/api/v2/orders?page=2",
  "previous": null,
  "results": [
    {
      "id": 123,
      "order_number": "ORD-123456",
      "tracking_number": "TRACK-123456",
      "status": "PROCESSING",
      "total_amount": "50.00",
      "total_items": 2,
      "created_at": "2025-11-02T10:30:00Z",
      "items": [
        {
          "sku": {
            "sku": "TSH-001",
            "name": "Áo thun cổ tròn",
            "price": "25.00"
          },
          "quantity": 2
        }
      ]
    }
  ]
}
```

#### Ví Dụ với cURL

```bash
# Lấy tất cả đơn hàng
curl -X GET "https://print-api.polimellc.media/api/v2/orders" \
  -H "x-api-key: YOUR_API_KEY"

# Lọc theo trạng thái
curl -X GET "https://print-api.polimellc.media/api/v2/orders?status=PROCESSING" \
  -H "x-api-key: YOUR_API_KEY"

# Tìm kiếm theo mã đơn hàng
curl -X GET "https://print-api.polimellc.media/api/v2/orders?order_number=ORD-123" \
  -H "x-api-key: YOUR_API_KEY"

# Phân trang
curl -X GET "https://print-api.polimellc.media/api/v2/orders?page=2&pageSize=20" \
  -H "x-api-key: YOUR_API_KEY"
```

---

### 3. Lấy Chi Tiết Đơn Hàng

**Endpoint**: `GET /api/v2/orders/{order_id}`

**Mô Tả**: Lấy thông tin chi tiết của một đơn hàng cụ thể.

#### Path Parameters

| Tham Số    | Kiểu    | Mô Tả           |
|------------|---------|-----------------|
| `order_id` | integer | ID của đơn hàng |

#### Response - Thành Công (200 OK)

```json
{
  "data": {
    "id": 123,
    "group": 1,
    "order_number": "ORD-123456",
    "tracking_number": "TRACK-123456",
    "reference": "REF-001",
    "status": "PROCESSING",
    "shipping_name": "Nguyễn Văn A",
    "shipping_phone": "0901234567",
    "shipping_address": "123 Đường ABC",
    "shipping_city": "Hồ Chí Minh",
    "shipping_state": "HCM",
    "shipping_country": "VN",
    "total_amount": "50.00",
    "total_paid": "50.00",
    "total_refunded": null,
    "total_items": 2,
    "printer_status": "PROCESSING",
    "shipping_status": null,
    "package_status": "in_production",
    "created_at": "2025-11-02T10:30:00Z"
  }
}
```

#### Response - Lỗi (404 Not Found)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "NOT_FOUND",
    "message": "Not found.",
    "details": null
  }
}
```

#### Ví Dụ với cURL

```bash
curl -X GET "https://print-api.polimellc.media/api/v2/orders/123" \
  -H "x-api-key: YOUR_API_KEY"
```

---

### 4. Hủy Đơn Hàng

**Endpoint**: `POST /api/v2/orders/{order_id}/cancel`

**Mô Tả**: Hủy một đơn hàng đang ở trạng thái PROCESSING. Chỉ có thể hủy đơn hàng ở trạng thái PROCESSING.

#### Path Parameters

| Tham Số    | Kiểu    | Mô Tả                   |
|------------|---------|-------------------------|
| `order_id` | integer | ID của đơn hàng cần hủy |

#### POST Parameters

| Tham Số  | Kiểu   | Bắt buộc | Mô Tả         |
|----------|--------|----------|---------------|
| `reason` | string | Không    | Lí do cần hủy |

#### Điều Kiện Hủy Đơn

- Đơn hàng phải ở trạng thái `PROCESSING`
- Cần huỷ đơn sau trạng thái `PROCESSING` vui lòng liên hệ hỗ trợ

#### Response - Thành Công (204 No Content)

```
HTTP/1.1 204 No Content
```

#### Response - Lỗi (400 Bad Request)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Can not cancel this order, current status is COMPLETED",
    "details": null
  }
}
```

#### Lưu Ý

- Khi hủy đơn hàng thành công, hệ thống sẽ tự động:
    - Cập nhật trạng thái đơn hàng thành `CANCELLED`
    - Hoàn trả phí đã trả (nếu có) về số dư của Group

#### Ví Dụ với cURL

```bash
curl -X POST "https://print-api.polimellc.media/api/v2/orders/123/cancel" \
  -H "x-api-key: YOUR_API_KEY"
```

---

### 5. Kiểm Tra Số Dư

**Endpoint**: `GET /api/v2/balance`

**Mô Tả**: Lấy thông tin số dư hiện tại của Group.

#### Response - Thành Công (200 OK)

```json
{
  "balance": "1500.50"
}
```

---

## Xử Lý Lỗi

### Mã Lỗi HTTP

| Mã  | Tên                   | Mô Tả                                    |
|-----|-----------------------|------------------------------------------|
| 200 | OK                    | Request thành công                       |
| 201 | Created               | Tạo tài nguyên thành công                |
| 204 | No Content            | Thành công nhưng không có dữ liệu trả về |
| 400 | Bad Request           | Request không hợp lệ                     |
| 401 | Unauthorized          | Chưa xác thực hoặc API Key không hợp lệ  |
| 403 | Forbidden             | Không có quyền truy cập                  |
| 404 | Not Found             | Không tìm thấy tài nguyên                |
| 500 | Internal Server Error | Lỗi server                               |

### Các Lỗi Thường Gặp

#### 1. Thiếu API Key (401 Unauthorized)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "NOT_AUTHENTICATED",
    "message": "Authentication credentials were not provided.",
    "details": null
  }
}
```

**Giải pháp**: Thêm header `x-api-key` vào request.

#### 3. Mã Đơn Hàng Bị Trùng (400 Bad Request)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "order_number": [
        "Order number already exists."
      ]
    }
  }
}
```

**Giải pháp**: Sử dụng mã đơn hàng khác hoặc kiểm tra đơn hàng cũ.

#### 4. SKU Không Tồn Tại (400 Bad Request)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "items.[0]": [
        "Invalid SKU TSH-999"
      ]
    }
  }
}
```

**Giải pháp**: Kiểm tra lại mã SKU hoặc tạo SKU mới trong hệ thống.

#### 5. Không Đủ Số Dư (400 Bad Request)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "{'non_field_errors': [ErrorDetail(string='Insufficient balance.', code='invalid')]}",
    "details": {
      "non_field_errors": [
        "Insufficient balance."
      ]
    }
  }
}
```

**Giải pháp**: Nạp thêm tiền vào tài khoản hoặc liên hệ quản trị viên.

#### 6. Thiếu Trường Bắt Buộc (400 Bad Request)

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": {
      "shipping_name": [
        "This field is required."
      ],
      "items": [
        "This field is required."
      ]
    }
  }
}
```

**Giải pháp**: Bổ sung đầy đủ các trường bắt buộc theo tài liệu.

---

## Rate Limiting

API có giới hạn số lượng request:

- **Xác thực bằng API Key**: 60 requests/phút

Khi vượt quá giới hạn, bạn sẽ nhận được response:

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Request was throttled. Expected available in 45 seconds.",
    "details": null
  }
}
```

---

## Webhooks (Tùy Chọn)

Bạn có thể cấu hình webhook để nhận thông báo khi có sự kiện quan trọng xảy ra với đơn hàng.

```
Method: POST
USER_AGENT: PLM-Print-Webhook/1.0
```

Payload:

```json
{
  "event": "ORDER_STATUS_CHANGED",
  "order": {
    "id": 63,
    "status": "PENDING",
    "order_number": "577131838386311805"
  },
  "timestamp": "2025-11-02T22:35:50.061924+00:00"
}
```

### Các Sự Kiện Webhook

- ORDER_STATUS_CHANGED - Order status changed

Liên hệ quản trị viên để cấu hình webhook cho Group của bạn.
