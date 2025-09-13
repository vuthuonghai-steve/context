# Quy tắc nghiệp vụ cho CMS

-   **CMS-01**: Chỉ người dùng có vai trò `ADMIN` mới có quyền truy cập các API quản lý Pages và Posts (tạo, sửa, xóa).
-   **CMS-02**: `slug` của một `page` PHẢI là duy nhất trong bảng `pages`.
-   **CMS-03**: `slug` của một `post` PHẢI là duy nhất trong bảng `posts`.
-   **CMS-04**: Khi tạo mới một `page` hoặc `post`, nếu `slug` không được cung cấp, hệ thống PHẢI tự động sinh ra một `slug` duy nhất từ `title`.
-   **CMS-05**: Một `post` khi được tạo PHẢI được gắn với một `author_id` hợp lệ.
