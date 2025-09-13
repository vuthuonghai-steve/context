# Quy tắc nghiệp vụ cho Quản lý Đơn hàng

1.  **Lịch sử Trạng thái:**
    *   Mỗi khi `orders.status` thay đổi, một bản ghi mới PHẢI được tạo trong bảng `order_status_histories` để lưu lại dấu vết.

2.  **Quy tắc chuyển đổi Trạng thái:**
    *   Một đơn hàng chỉ có thể chuyển trạng thái theo một luồng hợp lệ.
    *   Luồng hợp lệ chính: `pending` -> `processing` -> `packed` -> `shipping` -> `delivered`.
    *   Từ hầu hết các trạng thái (trừ `delivered`, `cancelled`), đơn hàng có thể được chuyển sang `cancelled`.
    *   Không thể chuyển trạng thái ngược lại (ví dụ: từ `shipping` về `processing`).
    *   Hệ thống PHẢI xác thực các lần chuyển đổi trạng thái này.

3.  **Hành vi theo Trạng thái:**
    *   `pending`: Đơn hàng có thể bị hủy bởi khách hàng hoặc admin.
    *   `processing`/`packed`: Đơn hàng đang được xử lý, không thể bị hủy bởi khách hàng. Chỉ admin có thể hủy.
    *   `shipping`: Đơn hàng đang trên đường đi, không thể hủy.
    *   `delivered`: Đơn hàng đã hoàn tất. Kích hoạt khả năng viết đánh giá cho khách hàng.
    *   `cancelled`: Đơn hàng đã bị hủy. Kích hoạt luồng hoàn lại hàng vào kho (`release` stock).

4.  **Phân quyền:**
    *   `CUSTOMER` chỉ có thể xem các đơn hàng của chính mình (`GET /api/orders`, `GET /api/orders/{id}`).
    *   `ADMIN` có thể xem và quản lý tất cả các đơn hàng (`GET /api/admin/orders`, `PATCH /api/admin/orders/{id}`).
