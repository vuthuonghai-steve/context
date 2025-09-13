# Quy tắc nghiệp vụ cho Thanh toán Online

## 1. Khởi tạo Thanh toán

-   **PM-01**: Người dùng chỉ có thể khởi tạo thanh toán online cho một đơn hàng khi `orders.status` là `pending` và `orders.payment_status` là `unpaid`.
-   **PM-02**: Số tiền được gửi đến cổng thanh toán PHẢI bằng chính xác `orders.grand_total`.
-   **PM-03**: Sau khi khởi tạo giao dịch với cổng thanh toán, hệ thống PHẢI lưu lại mã giao dịch của cổng thanh toán (`txn_ref`) và tạo một bản ghi trong `payment_transactions` với trạng thái `initiated` hoặc `redirected`.

## 2. Xử lý IPN (Instant Payment Notification)

-   **PM-04**: Endpoint IPN (`/api/payment/ipn`) PHẢI xác thực chữ ký (signature) của mọi request đến bằng `secret_key` của cổng thanh toán để đảm bảo tính toàn vẹn và chống giả mạo.
-   **PM-05**: Sau khi xác thực chữ ký, hệ thống PHẢI thực hiện các bước kiểm tra sau:
    -   Tìm đơn hàng tương ứng với mã đơn hàng (`order_code`) trong payload của IPN. Nếu không tìm thấy, bỏ qua xử lý.
    -   Kiểm tra xem `orders.payment_status` có phải là `unpaid` hay không. Nếu đã `paid`, bỏ qua xử lý để tránh double-payment.
    -   Kiểm tra xem số tiền trong payload của IPN có khớp với `orders.grand_total` hay không. Nếu không khớp, ghi log cảnh báo và thông báo cho admin.
-   **PM-06**: Nếu tất cả các bước kiểm tra ở **PM-05** đều hợp lệ và IPN báo thanh toán thành công:
    -   Hệ thống PHẢI cập nhật `orders.payment_status` thành `paid`.
    -   Hệ thống PHẢI cập nhật `orders.paid_at` với thời gian hiện tại.
    -   Hệ thống PHẢI cập nhật bản ghi `payment_transactions` tương ứng sang trạng thái `succeeded`.
    -   Hệ thống NÊN kích hoạt một sự kiện (event) `OrderPaid` để các hệ thống con khác (ví dụ: Notification) có thể xử lý (gửi email xác nhận cho khách hàng).
-   **PM-07**: Nếu IPN báo thanh toán thất bại, hệ thống PHẢI cập nhật `payment_transactions` sang trạng thái `failed` và ghi log chi tiết.