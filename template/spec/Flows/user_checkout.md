# Luồng người dùng: Quản lý Giỏ hàng và Thanh toán

## Kịch bản 1: Người dùng quản lý giỏ hàng

-   **Bối cảnh (Given):** Một người dùng đã đăng nhập và đang xem các trang sản phẩm.
-   **Khi (When):** Người dùng nhấn "Thêm vào giỏ" cho một sản phẩm.
-   **Thì (Then):**
    1.  Hệ thống gọi `POST /api/cart/items`.
    2.  Giao diện hiển thị thông báo thành công và cập nhật số lượng sản phẩm trên icon giỏ hàng.
-   **Và Khi (And When):** Người dùng điều hướng đến trang "Giỏ hàng".
-   **Thì (Then):**
    1.  Hệ thống gọi `GET /api/cart` và hiển thị danh sách các sản phẩm, số lượng, đơn giá và tổng tiền.
-   **Và Khi (And When):** Người dùng thay đổi số lượng của một sản phẩm và nhấn "Cập nhật".
-   **Thì (Then):**
    1.  Hệ thống gọi `PATCH /api/cart/items/{itemId}`.
    2.  Giao diện cập nhật lại tổng tiền của giỏ hàng.
-   **Và Khi (And When):** Người dùng nhấn nút "Xóa" một sản phẩm.
-   **Thì (Then):**
    1.  Hệ thống gọi `DELETE /api/cart/items/{itemId}`.
    2.  Sản phẩm đó biến mất khỏi danh sách trong giỏ hàng.

## Kịch bản 2: Người dùng thanh toán thành công (Happy Path)

-   **Bối cảnh (Given):** Một người dùng đã đăng nhập, có ít nhất một sản phẩm trong giỏ hàng và đang ở trang "Giỏ hàng".
-   **Khi (When):** Người dùng nhấn nút "Thanh toán".
-   **Thì (Then):**
    1.  Hệ thống chuyển hướng đến trang "Thanh toán".
    2.  Giao diện hiển thị các địa chỉ đã lưu của người dùng và form để thêm địa chỉ mới.
-   **Và Khi (And When):**
    1.  Người dùng chọn một địa chỉ giao hàng hợp lệ.
    2.  Và người dùng xem lại tóm tắt đơn hàng (sản phẩm, phí ship, tổng tiền).
    3.  Và người dùng nhấn nút "Hoàn tất Đặt hàng".
-   **Thì (Then):**
    1.  Hệ thống gọi API `POST /api/checkout` với `shipping_address_id`.
    2.  Backend thực thi transaction (khóa tồn kho, kiểm tra lại, tạo `order`, cập nhật `stock_reserved`, chuyển trạng thái `cart`).
    3.  API trả về thông tin đơn hàng mới đã được tạo với trạng thái `pending`.
    4.  Hệ thống chuyển hướng người dùng đến trang "Đặt hàng thành công" và hiển thị mã đơn hàng.

## Các kịch bản lỗi và luồng phụ

-   **Thanh toán - Giỏ hàng trống:**
    -   **Bối cảnh:** Người dùng có một giỏ hàng trống.
    -   **Khi:** Người dùng cố gắng truy cập trang "Thanh toán".
    -   **Thì:** Hệ thống nên chặn người dùng và hiển thị thông báo "Giỏ hàng của bạn đang trống".
-   **Thanh toán - Hết hàng đột ngột:**
    -   **Bối cảnh:** Người dùng đang ở trang "Thanh toán" và chuẩn bị nhấn "Hoàn tất Đặt hàng".
    -   **Khi:** Một sản phẩm trong giỏ của họ vừa hết hàng do một người dùng khác mua trước.
    -   **Và Khi:** Người dùng nhấn "Hoàn tất Đặt hàng".
    -   **Thì:**
        1.  API `POST /api/checkout` được gọi.
        2.  Backend thực hiện kiểm tra tồn kho và phát hiện không đủ hàng.
        3.  API trả về lỗi `422 Unprocessable Entity` với thông báo chi tiết sản phẩm nào đã hết hàng.
        4.  Giao diện hiển thị thông báo lỗi cho người dùng, không cho phép đặt hàng và có thể gợi ý xóa sản phẩm đó khỏi giỏ.
