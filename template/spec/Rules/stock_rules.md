# Quy tắc nghiệp vụ cho Quản lý Tồn kho

1.  **Tính toàn vẹn:**
    *   Số lượng tồn kho khả dụng (`available_stock`) không phải là một cột trong CSDL, mà được tính toán động: `available_stock = product_stocks.stock_on_hand - product_stocks.stock_reserved`.
    *   `stock_on_hand` và `stock_reserved` không bao giờ được là số âm.

2.  **Luồng nghiệp vụ:**
    *   **Thêm vào giỏ hàng:** Hệ thống có thể kiểm tra `available_stock` nhưng chưa giữ hàng.
    *   **Khi đặt hàng (Checkout):**
        *   Hệ thống PHẢI kiểm tra `available_stock` cho mỗi sản phẩm trong đơn hàng.
        *   Nếu không đủ hàng, giao dịch thất bại và báo lỗi cho người dùng.
        *   Nếu đủ hàng, hệ thống PHẢI đặt tất cả các thao tác cập nhật kho vào trong một DB transaction.
        *   Bên trong transaction:
            1.  Tăng `product_stocks.stock_reserved` lên theo số lượng đặt mua.
            2.  Tạo một bản ghi trong `stock_movements` với `type = 'reserve'`.
    *   **Khi đơn hàng được vận chuyển (Shipped):**
        *   Bên trong transaction:
            1.  Giảm `product_stocks.stock_on_hand` theo số lượng.
            2.  Giảm `product_stocks.stock_reserved` theo số lượng.
            3.  Tạo bản ghi `stock_movements` với `type = 'sell'` hoặc `'out'`.
    *   **Khi đơn hàng bị hủy (Cancelled/Released):**
        *   Bên trong transaction:
            1.  Giảm `product_stocks.stock_reserved` theo số lượng.
            2.  Tạo bản ghi `stock_movements` với `type = 'release'`.

3.  **Xử lý Đồng thời (Concurrency):**
    *   Hệ thống PHẢI sử dụng cơ chế khóa (locking) để ngăn chặn race conditions khi nhiều người dùng đặt cùng một sản phẩm cuối cùng.
    *   Khuyến nghị sử dụng `SELECT ... FOR UPDATE` (Pessimistic Locking) trên dòng `product_stocks` khi kiểm tra và cập nhật tồn kho trong transaction đặt hàng.
