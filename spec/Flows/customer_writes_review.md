# Luồng người dùng: Viết Đánh giá cho Sản phẩm

## Kịch bản 1: Người dùng viết đánh giá thành công

-   **Bối cảnh (Given):**
    1.  Một người dùng đã đăng nhập.
    2.  Người dùng này đã mua một sản phẩm trong một đơn hàng đã có trạng thái `delivered`.
    3.  Người dùng chưa từng viết đánh giá cho sản phẩm này từ đơn hàng đó.
-   **Khi (When):**
    1.  Người dùng truy cập trang "Lịch sử đơn hàng" và xem chi tiết đơn hàng đã giao.
    2.  Và người dùng nhấn nút "Viết đánh giá" bên cạnh một sản phẩm (`order_item`).
    3.  Và người dùng điền vào form đánh giá (chọn 5 sao, nhập bình luận "Sản phẩm tuyệt vời!").
    4.  Và người dùng nhấn "Gửi đánh giá".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/reviews` với payload chứa `order_item_id`, `rating`, và `comment`.
    2.  Backend xác thực người dùng là chủ của `order_item` và các điều kiện hợp lệ khác.
    3.  Hệ thống lưu đánh giá mới vào CSDL.
    4.  API trả về response `201 Created` với nội dung của đánh giá vừa tạo.
    5.  Giao diện hiển thị thông báo "Cảm ơn bạn đã đánh giá!" và cập nhật lại khu vực đánh giá để hiển thị nội dung vừa gửi.

## Các kịch bản lỗi và luồng phụ

-   **Kịch bản 2: Người dùng cố gắng đánh giá sản phẩm chưa mua**
    -   **Bối cảnh (Given):** Một người dùng đã đăng nhập.
    -   **Khi (When):** Người dùng cố gắng gọi thẳng API `POST /api/reviews` với một `order_item_id` không thuộc về mình.
    -   **Thì (Then):** API trả về lỗi `403 Forbidden` và không tạo đánh giá.

-   **Kịch bản 3: Người dùng cố gắng đánh giá một mục đã được đánh giá trước đó**
    -   **Bối cảnh (Given):** Người dùng đã viết đánh giá cho một `order_item` trước đây.
    -   **Khi (When):** Giao diện không hiển thị nút "Viết đánh giá" nữa, nhưng người dùng cố tình gọi lại API `POST /api/reviews` với cùng `order_item_id`.
    -   **Thì (Then):** API trả về lỗi `422 Unprocessable Entity` với thông báo "Bạn đã đánh giá sản phẩm này rồi."

-   **Kịch bản 4: Người dùng cố gắng đánh giá sản phẩm trong đơn hàng chưa được giao**
    -   **Bối cảnh (Given):** Người dùng đã đặt một đơn hàng nhưng nó vẫn ở trạng thái `processing`.
    -   **Khi (When):** Người dùng cố gắng đánh giá một sản phẩm trong đơn hàng đó.
    -   **Thì (Then):** Giao diện không hiển thị nút "Viết đánh giá". Nếu người dùng gọi thẳng API, backend sẽ kiểm tra trạng thái đơn hàng và trả về lỗi `403 Forbidden`.
