# Luồng người dùng: Quản lý Danh sách Yêu thích

## Kịch bản 1: Người dùng thêm sản phẩm vào danh sách yêu thích

-   **Bối cảnh (Given):**
    1.  Một người dùng đã đăng nhập.
    2.  Người dùng đang xem một trang chi tiết sản phẩm chưa có trong danh sách yêu thích của họ.
-   **Khi (When):** Người dùng nhấn vào nút "Thêm vào danh sách yêu thích" (biểu tượng trái tim).
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/wishlist` với `product_id` tương ứng.
    2.  Hệ thống thêm sản phẩm vào danh sách yêu thích của người dùng.
    3.  API trả về response `200 OK` với danh sách yêu thích đã được cập nhật.
    4.  Giao diện hiển thị thông báo thành công và biểu tượng trái tim đổi trạng thái (ví dụ: được tô đầy).

## Kịch bản 2: Người dùng xem danh sách yêu thích

-   **Bối cảnh (Given):** Một người dùng đã đăng nhập và đã thêm một vài sản phẩm vào danh sách yêu thích.
-   **Khi (When):** Người dùng truy cập vào trang "Danh sách yêu thích" của mình.
-   **Thì (Then):**
    1.  Giao diện gọi API `GET /api/wishlist`.
    2.  Hệ thống trả về một danh sách các sản phẩm mà người dùng đã yêu thích.
    3.  Giao diện hiển thị các sản phẩm đó.

## Kịch bản 3: Người dùng xóa sản phẩm khỏi danh sách yêu thích

-   **Bối cảnh (Given):** Người dùng đang ở trang "Danh sách yêu thích" của mình.
-   **Khi (When):** Người dùng nhấn nút "Xóa" (hoặc bỏ yêu thích) trên một sản phẩm.
-   **Thì (Then):**
    1.  Giao diện gọi API `DELETE /api/wishlist/products/{product_id}`.
    2.  Hệ thống xóa sản phẩm khỏi danh sách yêu thích của người dùng.
    3.  API trả về response `200 OK` với danh sách yêu thích đã được cập nhật.
    4.  Sản phẩm đó biến mất khỏi danh sách hiển thị trên giao diện.

## Kịch bản 4: Người dùng cố gắng thêm sản phẩm đã có

-   **Bối cảnh (Given):** Người dùng đang xem một trang chi tiết sản phẩm đã có trong danh sách yêu thích.
-   **Khi (When):** Người dùng nhấn lại nút "Thêm vào danh sách yêu thích".
-   **Thì (Then):**
    1.  Giao diện gọi API `POST /api/wishlist`.
    2.  Backend kiểm tra và phát hiện sản phẩm đã tồn tại (theo quy tắc **WR-02**).
    3.  API trả về lỗi `422 Unprocessable Entity`.
    4.  Giao diện hiển thị thông báo "Sản phẩm này đã có trong danh sách yêu thích của bạn."
