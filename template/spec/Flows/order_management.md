# Luồng Quản lý Đơn hàng

## Kịch bản 1: Khách hàng xem lịch sử và chi tiết đơn hàng

-   **Bối cảnh (Given):** Một khách hàng (`CUSTOMER`) đã đăng nhập và có ít nhất một đơn hàng đã đặt.
-   **Khi (When):** Khách hàng truy cập trang "Lịch sử đơn hàng".
-   **Thì (Then):**
    1.  Giao diện gọi API `GET /api/orders`.
    2.  Hệ thống trả về danh sách các đơn hàng của khách hàng đó.
-   **Và Khi (And When):** Khách hàng nhấp vào một đơn hàng cụ thể để xem chi tiết.
-   **Thì (Then):**
    1.  Giao diện gọi API `GET /api/orders/{id}`.
    2.  Hệ thống kiểm tra quyền sở hữu và trả về thông tin chi tiết của đơn hàng đó.

## Kịch bản 2: Admin xử lý một đơn hàng mới

-   **Bối cảnh (Given):** Một quản trị viên (`ADMIN`) đã đăng nhập và có một đơn hàng mới ở trạng thái `pending`.
-   **Khi (When):** Admin truy cập trang "Quản lý Đơn hàng".
-   **Thì (Then):**
    1.  Giao diện gọi API `GET /api/admin/orders` (có thể có filter `status=pending`) và hiển thị danh sách các đơn hàng.
-   **Và Khi (And When):**
    1.  Admin xem xét đơn hàng và nhấn nút "Xác nhận xử lý".
-   **Thì (Then):**
    1.  Giao diện gọi API `PATCH /api/admin/orders/{id}` với payload `{"status": "processing"}`.
    2.  Hệ thống cập nhật trạng thái đơn hàng thành `processing`.
    3.  Hệ thống NÊN gửi một thông báo (email/in-app) cho khách hàng về việc đơn hàng đang được xử lý.
-   **Và Khi (And When):**
    1.  Admin chuẩn bị xong hàng và nhấn "Đã đóng gói".
-   **Thì (Then):**
    1.  Giao diện gọi API `PATCH /api/admin/orders/{id}` với payload `{"status": "packed"}`.
    2.  Hệ thống cập nhật trạng thái đơn hàng thành `packed`.

## Kịch bản 3: Admin xác nhận thanh toán cho đơn hàng COD đã giao

-   **Bối cảnh (Given):**
    1.  Một đơn hàng đã được đẩy sang hãng vận chuyển và có trạng thái là `shipping`.
    2.  Hãng vận chuyển đã giao hàng thành công và cập nhật trạng thái qua webhook, khiến `orders.status` được cập nhật thành `delivered`.
    3.  Shipper đã thu tiền COD và đối soát với công ty.
-   **Khi (When):** Admin vào trang chi tiết của đơn hàng đó để xác nhận đã nhận được thanh toán.
-   **Thì (Then):**
    1.  Giao diện hiển thị đơn hàng có trạng thái `delivered` và `payment_status` là `unpaid`.
-   **Và Khi (And When):** Admin nhấn nút "Xác nhận thanh toán COD".
-   **Thì (Then):**
    1.  Giao diện gọi API `PATCH /api/admin/orders/{id}` với payload `{"payment_status": "paid"}`.
    2.  Hệ thống cập nhật `payment_status` của đơn hàng thành `paid` và cập nhật `paid_at`.
    3.  Hệ thống NÊN kích hoạt sự kiện `OrderPaid` để các hệ thống con (ví dụ: Loyalty) có thể xử lý (cộng điểm cho khách hàng).
    4.  Đơn hàng lúc này được coi là hoàn tất.
