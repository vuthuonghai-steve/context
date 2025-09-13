# Quy tắc nghiệp vụ cho Thống kê

1.  **Cập nhật Dữ liệu:**
    *   Dữ liệu trong bảng `product_statistics` là dữ liệu được tổng hợp (aggregated) và không được cập nhật trực tiếp khi có sự kiện.
    *   Hệ thống nên có một tác vụ chạy định kỳ (ví dụ: `Scheduled Job` chạy hàng đêm) để tính toán và cập nhật lại dữ liệu cho các bảng thống kê.
    *   **`total_sold`**: Được tính bằng cách `SUM(order_items.quantity)` cho các đơn hàng đã `delivered`.
    *   **`average_rating` & `reviews_count`**: Được tính từ bảng `product_reviews`.

2.  **Hiển thị trên Dashboard:**
    *   Các số liệu thống kê chính (doanh thu, sản phẩm bán chạy) được hiển thị trên trang dashboard chính của Filament Admin Panel.
    *   Doanh thu được tính theo khoảng thời gian (ngày, tuần, tháng).

3.  **Hiệu năng:**
    *   Việc sử dụng các bảng thống kê tổng hợp giúp tránh các câu truy vấn phức tạp và chậm chạp trên các bảng giao dịch chính (`orders`, `order_items`) khi hiển thị dashboard.
