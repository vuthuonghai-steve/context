# Quy tắc nghiệp vụ cho Đánh giá & Phản hồi

1.  **Điều kiện Viết Đánh giá:**
    *   Một người dùng (`CUSTOMER`) chỉ có thể viết đánh giá cho một sản phẩm NẾU họ đã mua sản phẩm đó VÀ đơn hàng chứa sản phẩm đó đã ở trạng thái `'delivered'`.
    *   Mỗi `order_item` chỉ có thể được đánh giá một lần duy nhất. Cột `order_item_id` trong `product_reviews` phải là `UNIQUE`.

2.  **Nội dung Đánh giá:**
    *   `rating` là bắt buộc, giá trị từ 1 đến 5.
    *   `comment` là không bắt buộc.

3.  **Tính toán Rating Trung bình:**
    *   Sau khi một đánh giá mới được tạo (hoặc một đánh giá bị xóa/cập nhật), hệ thống PHẢI tính toán lại điểm đánh giá trung bình cho sản phẩm tương ứng.
    *   Điểm trung bình này nên được lưu vào một cột trong bảng `products` hoặc `product_statistics` (ví dụ: `average_rating`) để tối ưu việc truy vấn.
    *   Việc tính toán lại này nên được thực hiện bất đồng bộ thông qua một `Job` trong `Queue` để không làm chậm request của người dùng.

4.  **Hiển thị:**
    *   Tất cả các đánh giá đã được duyệt (nếu có quy trình duyệt) sẽ được hiển thị công khai trên trang chi tiết sản phẩm.
