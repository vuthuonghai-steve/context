# Luồng người dùng: Áp dụng Mã giảm giá (Voucher)

## Kịch bản 1: Người dùng áp dụng voucher hợp lệ thành công

-   **Bối cảnh (Given):**
    1.  Một người dùng đã đăng nhập và có sản phẩm trong giỏ hàng.
    2.  Người dùng đang ở trang "Giỏ hàng" hoặc "Thanh toán".
    3.  Có một voucher hợp lệ (ví dụ: `SALE10`) mà người dùng đủ điều kiện áp dụng.
-   **Khi (When):**
    1.  Người dùng nhập mã `SALE10` vào ô "Mã giảm giá".
    2.  Và người dùng nhấn nút "Áp dụng".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/cart/apply-voucher` với payload `{"code": "SALE10"}`.
    2.  Backend thực hiện các bước kiểm tra trong `voucher_rules.md` (VR-01, VR-02) và xác nhận voucher hợp lệ.
    3.  Backend tính toán lại tổng tiền của giỏ hàng, áp dụng mức giảm giá.
    4.  API trả về thông tin giỏ hàng đã được cập nhật, bao gồm cả `discount_total`.
    5.  Giao diện hiển thị số tiền được giảm và cập nhật lại `grand_total`.

## Kịch bản 2: Người dùng áp dụng voucher không hợp lệ

-   **Bối cảnh (Given):** Người dùng đang ở trang "Giỏ hàng".
-   **Khi (When):** Người dùng nhập một mã không hợp lệ (ví dụ: `INVALIDCODE`, hoặc một mã đã hết hạn, hoặc không đủ điều kiện `min_order_total`).
-   **Và Khi (And When):** Người dùng nhấn nút "Áp dụng".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/cart/apply-voucher`.
    2.  Backend kiểm tra và phát hiện voucher không hợp lệ.
    3.  API trả về lỗi `400 Bad Request` hoặc `404 Not Found`.
    4.  Giao diện hiển thị một thông báo lỗi cụ thể, ví dụ: "Mã giảm giá không hợp lệ hoặc đã hết hạn." hoặc "Đơn hàng của bạn không đủ điều kiện để áp dụng mã này."

## Kịch bản 3: Người dùng thay đổi voucher đã áp dụng

-   **Bối cảnh (Given):** Người dùng đã áp dụng thành công một voucher (`SALE10`) vào giỏ hàng.
-   **Khi (When):**
    1.  Người dùng nhập một mã voucher hợp lệ khác (ví dụ: `FREESHIP`).
    2.  Và người dùng nhấn nút "Áp dụng".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/cart/apply-voucher` với payload `{"code": "FREESHIP"}`.
    2.  Backend nhận thấy đã có voucher cũ, liền gỡ bỏ nó, sau đó xác thực và áp dụng voucher mới.
    3.  API trả về thông tin giỏ hàng đã được cập nhật với mức giảm giá của voucher `FREESHIP`.
    4.  Giao diện cập nhật lại thông tin giảm giá.
