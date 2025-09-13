# Luồng Hệ thống: Xử lý Xác nhận Thanh toán Online (IPN)

-   **Bối cảnh (Given):**
    1.  Người dùng đã được chuyển hướng đến cổng thanh toán (MoMo/VNPay) để thanh toán cho một đơn hàng.
    2.  Đơn hàng có `payment_status` là `unpaid`.
-   **Khi (When):** Cổng thanh toán gửi một request (IPN) đến endpoint `POST /api/payment/ipn` của hệ thống để thông báo kết quả giao dịch.
-   **Thì (Then):**
    1.  Hệ thống thực hiện các bước xác thực trong `payment_rules.md` (kiểm tra chữ ký, số tiền, trạng thái đơn hàng).
-   **Và Nếu (And If):** Giao dịch được xác nhận là thành công:
    -   **Thì (Then):**
        1.  Hệ thống cập nhật `orders.payment_status` thành `paid` và ghi nhận `paid_at`.
        2.  Hệ thống cập nhật `payment_transactions` thành `succeeded`.
        3.  Hệ thống kích hoạt sự kiện `OrderPaid` (để gửi email, cộng điểm loyalty, etc.).
-   **Và Nếu (And If):** Giao dịch được xác nhận là thất bại:
    -   **Thì (Then):**
        1.  Hệ thống cập nhật `payment_transactions` thành `failed`.
        2.  Hệ thống KHÔNG thay đổi trạng thái đơn hàng, nhưng ghi log lỗi.
