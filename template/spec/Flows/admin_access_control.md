# Luồng hệ thống: Kiểm soát truy cập của Admin

Luồng này mô tả cách hệ thống phân quyền truy cập vào một tài nguyên chỉ dành cho quản trị viên.

**Tài nguyên:** Giao diện quản lý sản phẩm (Admin Panel).
**API Endpoint ví dụ:** `GET /api/admin/products`

## Luồng thành công (Admin truy cập)

1.  **Actor:** Người dùng có vai trò `ADMIN`.
2.  **Trigger:** Admin truy cập vào đường dẫn `/admin/products`.
3.  **Các bước:**
    1.  Request được gửi đến server với token xác thực của Admin.
    2.  Middleware `auth:api` xác thực người dùng.
    3.  Middleware `role:ADMIN` (hoặc `permission:manage-products`) kiểm tra vai trò/quyền của người dùng.
    4.  Vì người dùng là `ADMIN`, middleware cho phép request đi tiếp.
    5.  `ProductController@indexAdmin` (ví dụ) được thực thi, lấy danh sách sản phẩm.
    6.  Hệ thống trả về danh sách sản phẩm.

## Luồng thất bại (Customer truy cập)

1.  **Actor:** Người dùng có vai trò `CUSTOMER`.
2.  **Trigger:** Customer cố gắng truy cập vào đường dẫn `/api/admin/products`.
3.  **Các bước:**
    1.  Request được gửi đến server với token xác thực của Customer.
    2.  Middleware `auth:api` xác thực người dùng.
    3.  Middleware `role:ADMIN` kiểm tra vai trò của người dùng.
    4.  Vì người dùng là `CUSTOMER`, middleware từ chối truy cập.
    5.  Hệ thống trả về lỗi `403 Forbidden`.
