# Quy tắc nghiệp vụ cho Vouchers & Khuyến mãi

## 1. Tính hợp lệ của Voucher (Voucher Validity)

-   **VR-01**: Một voucher được coi là hợp lệ để kiểm tra nếu đáp ứng TẤT CẢ các điều kiện sau:
    -   `is_active` LÀ `true`.
    -   Ngày hiện tại NẰM TRONG khoảng từ `start_at` đến `end_at`.
    -   `used_count` NHỎ HƠN `usage_limit`.

## 2. Áp dụng Voucher vào Giỏ hàng

-   **VR-02**: Người dùng có thể áp dụng một voucher vào giỏ hàng nếu đáp ứng TẤT CẢ các điều kiện sau:
    -   Voucher đó PHẢI hợp lệ (theo quy tắc **VR-01**).
    -   Tổng giá trị các mặt hàng trong giỏ (`cart.subtotal`) PHẢI LỚN HƠN hoặc BẰNG `vouchers.min_order_total` (nếu có).
    -   Số lần người dùng này đã sử dụng voucher (đếm trong `voucher_usages`) PHẢI NHỎ HƠN `vouchers.per_user_limit`.
-   **VR-03**: Tại một thời điểm, mỗi giỏ hàng chỉ được áp dụng TỐI ĐA một voucher. Nếu người dùng áp dụng voucher mới, voucher cũ sẽ bị gỡ bỏ.

## 3. Tính toán giá trị giảm giá

-   **VR-04**: Giá trị được giảm (`discount_total`) được tính như sau:
    -   Nếu `vouchers.type` là `FIXED`: `discount_total` = `vouchers.value`.
    -   Nếu `vouchers.type` là `PERCENT`:
        -   `calculated_discount` = `cart.subtotal` * (`vouchers.value` / 100).
        -   `discount_total` = `MIN(calculated_discount, vouchers.max_discount)`. (Lấy giá trị nhỏ hơn giữa số tiền tính được và mức giảm giá tối đa).

## 4. Xử lý sau khi Đặt hàng

-   **VR-05**: Khi một đơn hàng được đặt thành công với một voucher, trong cùng một DB transaction, hệ thống PHẢI:
    -   Tăng `vouchers.used_count` lên 1.
    -   Tạo một bản ghi mới trong bảng `voucher_usages` với `user_id`, `voucher_id`, và `order_id` tương ứng.
