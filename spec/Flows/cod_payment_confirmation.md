# Luồng Admin: Xác nhận Thanh toán COD

-   **Bối cảnh (Given):**
    1.  Một đơn hàng COD đã được giao thành công (`status` = `delivered`).
    2.  `payment_status` của đơn hàng này đang là `unpaid`.
    3.  Admin đã nhận được tiền từ shipper.
-   **Khi (When):** Admin truy cập trang chi tiết đơn hàng và nhấn nút "Xác nhận đã thanh toán".
-   **Thì (Then):**
    1.  Giao diện gọi API `PATCH /api/admin/orders/{id}` với payload `{"payment_status": "paid"}`.
    2.  Hệ thống cập nhật `payment_status` thành `paid` và ghi nhận `paid_at`.
    3.  Hệ thống kích hoạt sự kiện `OrderPaid` để cộng điểm loyalty cho khách hàng.
