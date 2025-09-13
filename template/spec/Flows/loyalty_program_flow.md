# Luồng người dùng: Chương trình Khách hàng thân thiết

## Kịch bản 1: Hệ thống tự động cộng điểm cho khách hàng sau khi mua hàng

-   **Bối cảnh (Given):**
    1.  Một đơn hàng của người dùng được Admin xác nhận là đã giao hàng và thanh toán thành công (`status` = `delivered`, `payment_status` = `paid`).
-   **Khi (When):** Sự kiện `OrderPaid` được kích hoạt trong hệ thống.
-   **Thì (Then):**
    1.  Một tiến trình xử lý (listener/job) sẽ được thực thi.
    2.  Hệ thống tính toán số điểm người dùng nhận được dựa trên `subtotal` của đơn hàng (theo quy tắc **LR-01**).
    3.  Hệ thống cập nhật `points_balance` trong bảng `user_loyalty` cho người dùng.
    4.  Hệ thống tạo một bản ghi mới trong `loyalty_transactions` với `type = 'earn'`.
    5.  Hệ thống kiểm tra và cập nhật `tier_id` của người dùng nếu cần (theo quy tắc **LR-02**).
    6.  Hệ thống NÊN gửi một thông báo cho người dùng về việc họ vừa được cộng điểm.

## Kịch bản 2: Người dùng xem thông tin điểm và lịch sử giao dịch

-   **Bối cảnh (Given):** Một người dùng đã đăng nhập.
-   **Khi (When):** Người dùng truy cập trang "Khách hàng thân thiết" trong hồ sơ cá nhân của mình.
-   **Thì (Then):**
    1.  Giao diện gọi API `GET /api/loyalty/profile` để lấy thông tin điểm và hạng hiện tại.
    2.  Giao diện gọi API `GET /api/loyalty/transactions` để lấy lịch sử giao dịch điểm.
    3.  Giao diện hiển thị các thông tin này cho người dùng.

## Kịch bản 3: Admin điều chỉnh điểm cho người dùng

-   **Bối cảnh (Given):** Một Admin đã đăng nhập và đang xem trang quản lý của một người dùng cụ thể.
-   **Khi (When):**
    1.  Admin nhấn vào nút "Điều chỉnh điểm".
    2.  Admin nhập vào số điểm cần thay đổi (có thể là số âm để trừ điểm) và một lý do.
    3.  Và Admin nhấn "Xác nhận".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/admin/users/{id}/loyalty/adjust`.
    2.  Hệ thống cập nhật `points_balance` của người dùng.
    3.  Hệ thống tạo một bản ghi mới trong `loyalty_transactions` với `type = 'adjust'` và ghi chú của Admin.
    4.  Giao diện hiển thị thông báo thành công.
