# Luồng Admin: Xem Dashboard Thống kê

## Kịch bản 1: Admin xem các số liệu tổng quan

-   **Bối cảnh (Given):** Một quản trị viên (`ADMIN`) đã đăng nhập vào hệ thống.
-   **Khi (When):** Admin truy cập vào trang "Dashboard" hoặc trang tổng quan chính.
-   **Thì (Then):**
    1.  Giao diện gọi một hoặc nhiều API để lấy dữ liệu thống kê, ví dụ: `GET /api/admin/statistics/summary`.
    2.  Hệ thống tổng hợp và trả về các số liệu chính.
    3.  Giao diện hiển thị các thông tin sau dưới dạng các widget hoặc biểu đồ:
        -   Tổng doanh thu trong ngày / 7 ngày qua / tháng này.
        -   Số lượng đơn hàng mới trong ngày.
        -   Số lượng người dùng mới đăng ký trong ngày.
        -   Danh sách các sản phẩm bán chạy nhất trong tháng.
        -   Danh sách các đơn hàng cần xử lý (`status = 'pending'`).
