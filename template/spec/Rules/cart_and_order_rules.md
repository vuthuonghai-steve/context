# Quy tắc nghiệp vụ cho Giỏ hàng & Đặt hàng

## 1. Giỏ hàng (Cart)

-   **CO-01**: Mỗi người dùng đã đăng nhập chỉ có một giỏ hàng (`cart`) với trạng thái `active`.
-   **CO-02**: Khi thêm sản phẩm vào giỏ, hệ thống PHẢI kiểm tra sản phẩm đó đang `is_active` và có số lượng tồn kho khả dụng (`stock_on_hand` - `stock_reserved`) lớn hơn 0.
-   **CO-03**: Giá của sản phẩm trong giỏ hàng (`cart_items.price_snapshot`) PHẢI được lấy tại thời điểm thêm vào giỏ và KHÔNG được thay đổi, kể cả khi giá gốc của sản phẩm thay đổi sau đó.
-   **CO-04**: Giỏ hàng không hoạt động (không có sự thay đổi) trong 7 ngày NÊN được chuyển trạng thái sang `abandoned`.

## 2. Đặt hàng (Checkout)

-   **CO-05**: Người dùng không thể tiến hành checkout nếu giỏ hàng trống.
-   **CO-06**: Toàn bộ quá trình checkout (từ lúc kiểm tra kho đến khi tạo đơn hàng thành công) PHẢI được thực thi bên trong một Database Transaction để đảm bảo tính toàn vẹn dữ liệu.
-   **CO-07**: Sau khi người dùng xác nhận đặt hàng, giỏ hàng (`cart`) PHẢI được chuyển trạng thái sang `converted`. Các `cart_items` sẽ được chuyển thành các `order_items`.

## 3. Quản lý Tồn kho khi Đặt hàng

-   **CO-08**: (Race Condition) Trong transaction checkout, hệ thống PHẢI sử dụng `Pessimistic Locking` (ví dụ: `lockForUpdate()` trong Laravel) trên các dòng `product_stocks` của những sản phẩm có trong giỏ hàng.
-   **CO-09**: Ngay sau khi lock, hệ thống PHẢI kiểm tra lại lần cuối xem số lượng mua có nhỏ hơn hoặc bằng tồn kho khả dụng (`stock_on_hand` - `stock_reserved`) hay không. Nếu không, transaction PHẢI được rollback và báo lỗi cho người dùng.
-   **CO-10**: Nếu việc kiểm tra tồn kho thành công, sau khi đơn hàng được tạo, hệ thống PHẢI cập nhật bảng `product_stocks`:
    -   `stock_reserved` PHẢI được **tăng lên** theo số lượng sản phẩm trong `order_items`.
    -   `stock_on_hand` được **giữ nguyên** ở bước này.

## 4. Tính toán Chi phí

-   **CO-11**: `subtotal` của đơn hàng là tổng của (`order_items.price_snapshot` * `order_items.quantity`).
-   **CO-12**: (Giai đoạn 1) `shipping_fee` PHẢI được áp dụng một mức phí cố định là 30,000 VND cho mọi đơn hàng.
-   **CO-13**: `grand_total` của đơn hàng được tính bằng: `subtotal` - `discount_total` + `shipping_fee`.

## 5. Trạng thái Đơn hàng & Hủy đơn

-   **CO-14**: Đơn hàng mới tạo PHẢI có `status` là `pending` và `payment_status` là `unpaid`.
-   **CO-15**: Việc hủy đơn hàng được quy định như sau:
    -   Người dùng (vai trò `CUSTOMER`) chỉ có thể tự hủy đơn hàng của mình khi `status` của đơn hàng là `pending`.
    -   Quản trị viên (vai trò `ADMIN`) có thể hủy bất kỳ đơn hàng nào miễn là `status` chưa phải là `delivered` hoặc `cancelled`.
