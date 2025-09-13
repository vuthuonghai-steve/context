# Quy tắc nghiệp vụ cho Sản phẩm

Dựa trên các tài liệu đặc tả hệ thống.

1.  **Toàn vẹn dữ liệu:**
    *   `sale_price` (giá khuyến mãi) nếu được cung cấp, PHẢI nhỏ hơn hoặc bằng `price` (giá gốc).
    *   `slug` PHẢI là duy nhất (unique) trong bảng `products`, `categories`, và `brands`.
    *   Khi một `category` hoặc `brand` bị xóa, hành vi mặc định là `RESTRICT`, ngăn chặn việc xóa nếu có sản phẩm đang tham chiếu đến.

2.  **Quản lý tồn kho:**
    *   Việc đặt hàng một sản phẩm PHẢI được kiểm tra với số lượng tồn kho (`product_stocks`).
    *   Hệ thống PHẢI sử dụng transaction và locking để đảm bảo không có hai người dùng cùng lúc đặt mua sản phẩm cuối cùng. (Chi tiết sẽ được mô tả trong `stock_rules.md`).

3.  **Hiển thị:**
    *   Chỉ những sản phẩm có `is_active = true` mới được hiển thị cho người dùng thông thường.
    *   Sản phẩm bán chạy được xác định dựa trên `product_statistics.total_sold` và/hoặc `product_statistics.average_rating`.

4.  **Đánh giá:**
    *   Điểm đánh giá trung bình (`average_rating`) của một sản phẩm được tính toán từ bảng `product_reviews`.
