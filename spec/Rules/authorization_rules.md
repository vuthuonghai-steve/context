# Quy tắc nghiệp vụ cho Phân quyền

Hệ thống sử dụng gói `spatie/laravel-permission` để quản lý vai trò và quyền hạn.

## 1. Vai trò (Roles)

Hệ thống định nghĩa 2 vai trò chính trong Giai đoạn 1:

1.  **`CUSTOMER`**:
    *   Vai trò mặc định cho tất cả người dùng mới đăng ký.
    *   Đại diện cho khách hàng của trang web.

2.  **`ADMIN`**:
    *   Vai trò quản trị viên cấp cao.
    *   Có quyền truy cập vào toàn bộ hệ thống, bao gồm cả Admin Panel.

## 2. Quyền hạn (Permissions)

Quyền hạn sẽ được định nghĩa một cách chi tiết để kiểm soát các hành động cụ thể. Tên quyền hạn nên tuân theo quy ước `verb-noun` (ví dụ: `view-products`, `edit-orders`).

### Quyền của CUSTOMER:
*   `view-products`
*   `view-orders` (chỉ các đơn hàng của chính họ)
*   `create-orders`
*   `create-reviews`
*   `manage-cart`
*   `manage-own-profile`

### Quyền của ADMIN:
*   Bao gồm tất cả các quyền của `CUSTOMER`.
*   `manage-products` (create, read, update, delete)
*   `manage-categories`
*   `manage-brands`
*   `manage-orders` (view all, update status, delete)
*   `manage-users`
*   `view-statistics`
*   `confirm-cod-payments`

## 3. Áp dụng
*   **API Routes:** Các route nhạy cảm (đặc biệt là các route cho admin) sẽ được bảo vệ bằng middleware của Spatie (ví dụ: `role:ADMIN` hoặc `permission:manage-products`).
*   **Controller Methods:** Sử dụng các directive của Spatie trong controller (ví dụ: `$this->authorize('manage-products');`) để kiểm tra quyền hạn ở cấp độ phương thức.
*   **Policies:** Đối với các model phức tạp như `Order`, có thể sử dụng Laravel Policies để định nghĩa logic phân quyền (ví dụ: một `User` có thể `view` một `Order` nếu họ là người tạo ra đơn hàng đó hoặc họ là `ADMIN`).
