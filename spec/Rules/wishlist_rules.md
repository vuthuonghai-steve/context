# Quy tắc nghiệp vụ cho Danh sách Yêu thích (Wishlist)

-   **WR-01**: Mỗi người dùng (`user`) chỉ có một danh sách yêu thích (`wishlist`) duy nhất. Nếu người dùng chưa có, hệ thống sẽ tự động tạo khi họ thêm sản phẩm đầu tiên.
-   **WR-02**: Một sản phẩm (`product`) chỉ có thể được thêm vào danh sách yêu thích một lần duy nhất. Hệ thống PHẢI ngăn chặn việc thêm sản phẩm đã có sẵn trong danh sách.
-   **WR-03**: Người dùng chỉ có thể thêm các sản phẩm đang có trạng thái `is_active = true` vào danh sách yêu thích.
-   **WR-04**: Người dùng có toàn quyền xóa bất kỳ sản phẩm nào ra khỏi danh sách yêu thích của mình.
