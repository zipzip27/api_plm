# API Quản lý Đơn hàng 

## Tổng quan
API này cung cấp các endpoint để quản lý thông tin đơn hàng, bao gồm tạo, đọc và xóa đơn hàng. API sử dụng định dạng JSON cho request và response, yêu cầu xác thực bằng API key qua header `x-token`.

## Base URL
```
https://tiktokshop-api.polimellc.media/api/v2
```

## Xác thực
- Yêu cầu tất cả các endpoint: Header `x-token: <API_KEY>`
- Ví dụ: `x-token: abc123xyz`

## Endpoint

### 1. Lấy danh sách đơn hàng
- **Phương thức**: GET
- **URL**: `/orders`
- **Mô tả**: Lấy danh sách tất cả đơn hàng.
- **Query Parameters**:
  - `page` (tùy chọn): Số trang (mặc định: 1)
  - `page_size` (tùy chọn): Số lượng đơn hàng mỗi trang (mặc định: 10)
- **Response**:
  - **200 OK**: Trả về danh sách đơn hàng
  - **401 Unauthorized**: Thiếu hoặc sai API key
- **Ví dụ Request**:
  ```
  GET /orders?page=1&limit=10
  x-token: abc123xyz
  ```
- **Ví dụ Response**:
  ```json
  {
    "count": 1000,
    "next": "http://localhost:8000/api/v2/orders?page=2",
    "previous": null,
    "results": [
        {
            "id": 178675,
            "reference": "Api",
            "shipping_name": "ELIJAH CAMPBELL",
            "shipping_address_line_1": "7905 S JUNIPER AVE",
            "shipping_address_line_2": null,
            "shipping_city": "BREN ARROW",
            "shipping_province": "OK",
            "shipping_zip": "74011",
            "shipping_country": "US",
            "order_number": "577101356264821406",
            "package_weight": null,
            "created_at": "2025-09-10T08:04:17.689965Z",
            "status": "FULFILL",
            "customer_note": "",
            "label_status": null,
            "order_fee": null,
            "tracking_number": "9200190393392200622042",
            "tracking_url": "https://tools.usps.com/go/TrackConfirmAction?qtc_tLabels1=9200190393392200622042",
            "tracking_carrier": "USPS",
            "shipping_label": "https://plm-media.sgp1.digitaloceanspaces.com/plm-media/labels/1154749139437064862_577101356264821406_plm.pdf"
        }
    ]
  }
  ```

### 2. Lấy thông tin đơn hàng theo ID
- **Phương thức**: GET
- **URL**: `/orders/:id`
- **Mô tả**: Lấy thông tin chi tiết của một đơn hàng dựa trên ID.
- **Path Parameters**:
  - `id` (bắt buộc): ID của đơn hàng
- **Response**:
  - **200 OK**: Trả về thông tin đơn hàng
  - **404 Not Found**: Không tìm thấy đơn hàng
- **Ví dụ Request**:
  ```
  GET /orders/1
  x-token: abc123xyz
  ```
- **Ví dụ Response**:
  ```json
  {
    "data": {
        "id": 178675,
        "reference": "API",
        "shipping_name": "ELIJAH CAMPBELL",
        "shipping_address_line_1": "7905 S JUNIPER AVE",
        "shipping_address_line_2": null,
        "shipping_city": "BREN ARROW",
        "shipping_province": "OK",
        "shipping_zip": "74011",
        "shipping_country": "US",
        "order_number": "577101356264821406",
        "package_weight": null,
        "created_at": "2025-09-10T08:04:17.689965Z",
        "status": "FULFILL",
        "customer_note": "",
        "label_status": null,
        "order_fee": null,
        "tracking_number": "9200190393392200622042",
        "tracking_url": "https://tools.usps.com/go/TrackConfirmAction?qtc_tLabels1=9200190393392200622042",
        "tracking_carrier": "USPS",
        "shipping_label": "https://plm-media.sgp1.digitaloceanspaces.com/plm-media/labels/1154749139437064862_577101356264821406_plm.pdf",
        "items": [
            {
                "id": 213961,
                "product_name": "HOLLOW KNIGHT Game graphic Washed Unisex Tee, Gamer Gift, Menswear Game graphic tee Oversized Seamless Tropical vintage trendy t-shirt Top Tshirt",
                "item_name": "BLACK-WT-L",
                "item_weight": 315.0,
                "item_length": 15.0,
                "item_width": 20.0,
                "item_height": 3.5,
                "des_name": "Black SML HOLLOW KNIGHT Game graphic Washed Unisex Tee, Gamer Gift, Menswear Game graphic tee Oversized Seamless Tropical vintage trendy t-shirt Top Tshirt",
                "tags": null
            }
        ]
    }
  }
  ```

### 3. Tạo đơn hàng mới
- **Phương thức**: POST
- **URL**: `/orders`
- **Mô tả**: Tạo một đơn hàng mới. mỗi 1 item là 1 sản phẩm, nếu số lượng là 2 thì chuyển thành 2 item
- **Body Parameters**:
```json
{
    "order_number": "12324",
    "customer_note": "",
    "shipping_name": "Test",
    "shipping_city": "BROOKLYN",
    "shipping_country": "US",
    "shipping_province": "NY",
    "shipping_zip": "11204",
    "shipping_address_line_1": "a1",
    "tracking_number": "123456789",
    "shipping_label": "URL",
    "items": [
      {
        "name": "Product name",
        "size": "XL",
        "color": "Black",
        "image": "URL",
        "sku": "1234563"
      },
      {
        "name": "Product name",
        "size": "XL",
        "color": "Black",
        "image": "URL",
        "sku": "1234563"
      }
    ]
}
```
- **Response**:
  - **201 Created**: Tạo đơn hàng thành công
  - **400 Bad Request**: Thiếu hoặc sai tham số
- **Ví dụ Response**:
  ```json
  {
    "success": true,
    "message": "Create order successful!"
  }
  ```

### 4. Xóa đơn hàng (chỉ đơn hàng vừa tạo chưa được fulfill)
- **Phương thức**: DELETE
- **URL**: `/orders/:id`
- **Mô tả**: Xóa một đơn hàng dựa trên ID.
- **Path Parameters**:
  - `id` (bắt buộc): ID của đơn hàng
- **Response**:
  - **204 No Content**: Xóa thành công
  - **404 Not Found**: Không tìm đơn hàng
  - **400 Bad Request**: Đơn hàng không thể xoá
- **Ví dụ Request**:
  ```
  DELETE /users/1
  x-token: abc123xyz
  ```
- **Ví dụ Response**:
  ```json
  {
    "success": true,
    "message": "Order deleted successfully"
  }
  ```

### 5. Lấy số dư
- **Phương thức**: GET
- **URL**: `/balance`
- **Mô tả**: Lấy số dư hiện có.
- **Response**:
  - **200 No Content**: Yêu cầu thành công
- **Ví dụ Request**:
  ```
  GET /balance
  x-token: abc123xyz
  ```
- **Ví dụ Response**:
  ```json
  {
    "success": true,
    "balance": 1234.00
  }
  ```

## Mã lỗi
| Mã | Mô tả |
|-----|-------|
| 200 | Yêu cầu thành công |
| 201 | Tạo mới thành công |
| 204 | Xóa thành công |
| 400 | Yêu cầu không hợp lệ |
| 401 | Không được phép (sai hoặc thiếu API key) |
| 404 | Không tìm thấy tài nguyên |

## Ghi chú
- Tất cả request và response sử dụng định dạng JSON.
- API key phải được gửi trong header `x-token` cho mọi yêu cầu.
- Giới hạn số lượng request: 60 request/phút.