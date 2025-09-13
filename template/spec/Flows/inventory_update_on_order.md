# Luồng Hệ thống: Cập nhật Tồn kho theo vòng đời Đơn hàng

## Kịch bản 1: Khách hàng đặt hàng thành công

-   **Bối cảnh (Given):** Một đơn hàng được tạo thành công (theo luồng `user_checkout.md`).
-   **Khi (When):** Transaction tạo đơn hàng được commit.
-   **Thì (Then):**
    1.  Đối với mỗi `order_item` trong đơn hàng:
    2.  Hệ thống cập nhật `product_stocks.stock_reserved`, **cộng thêm** số lượng đã mua.
    3.  `product_stocks.stock_on_hand` không thay đổi.
    4.  Tồn kho khả dụng (`stock_on_hand` - `stock_reserved`) giảm xuống.

## Kịch bản 2: Admin đẩy đơn hàng cho hãng vận chuyển

-   **Bối cảnh (Given):** Một đơn hàng ở trạng thái `packed` hoặc `processing`.
-   **Khi (When):** Admin thực hiện hành động "Giao hàng" (push to shipping provider).
-   **Thì (Then):**
    1.  Trạng thái đơn hàng được chuyển thành `shipping`.
    2.  Đối với mỗi `order_item` trong đơn hàng:
    3.  Hệ thống cập nhật `product_stocks.stock_on_hand`, **trừ đi** số lượng đã giao.
    4.  Hệ thống cập nhật `product_stocks.stock_reserved`, **trừ đi** số lượng đã giao.
    5.  (Kết quả là tồn kho khả dụng không đổi ở bước này, nhưng tồn kho vật lý đã giảm).

## Kịch bản 3: Đơn hàng bị hủy trước khi giao

-   **Bối cảnh (Given):** Một đơn hàng đang ở trạng thái `pending` hoặc `processing`.
-   **Khi (When):** Đơn hàng được chuyển sang trạng thái `cancelled` (bởi khách hàng hoặc admin).
-   **Thì (Then):**
    1.  Đối với mỗi `order_item` trong đơn hàng:
    2.  Hệ thống cập nhật `product_stocks.stock_reserved`, **trừ đi** số lượng đã đặt.
    3.  (Kết quả là số lượng hàng tạm giữ được "giải phóng" trở lại thành tồn kho khả dụng).
