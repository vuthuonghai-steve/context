# Quy tắc nghiệp vụ cho Tích hợp Vận chuyển

## 1. Đẩy đơn hàng sang Hãng vận chuyển (HVC)

-   **SH-01**: Một đơn hàng chỉ có thể được đẩy sang HVC qua API `POST /api/admin/orders/{id}/push-to-shipping` khi `orders.status` là `processing` hoặc `packed`.
-   **SH-02**: Sau khi gọi API của HVC thành công:
    -   Hệ thống PHẢI tạo một bản ghi trong bảng `shipments` với `order_id`, `carrier_id`, và `tracking_code` nhận được từ HVC.
    -   Hệ thống PHẢI cập nhật `orders.status` thành `shipping`.
-   **SH-03**: Nếu việc gọi API của HVC thất bại, hệ thống PHẢI ghi nhận log lỗi và KHÔNG thay đổi trạng thái của đơn hàng.

## 2. Xử lý Webhook từ Hãng vận chuyển

-   **SH-04**: Endpoint webhook (`/api/shipping/webhook`) PHẢI xác thực chữ ký (signature) của request để đảm bảo request đến từ HVC hợp lệ.
-   **SH-05**: Hệ thống PHẢI ánh xạ trạng thái từ webhook của HVC sang trạng thái của hệ thống. Ví dụ:
    -   Nếu HVC báo "Lấy hàng thành công" -> Cập nhật `shipments.status` = `picking`.
    -   Nếu HVC báo "Đang vận chuyển" -> Cập nhật `shipments.status` = `in_transit`.
    -   Nếu HVC báo "Giao hàng thành công" -> Cập nhật `shipments.status` = `delivered` VÀ `orders.status` = `delivered`.
    -   Nếu HVC báo "Giao hàng thất bại" -> Cập nhật `shipments.status` = `failed` và gửi thông báo cho admin.
-   **SH-06**: Mỗi sự kiện từ webhook PHẢI được ghi lại trong bảng `shipment_events`.

## 3. Cập nhật Phí vận chuyển

-   **SH-07**: Nếu HVC trả về phí vận chuyển thực tế, hệ thống NÊN cập nhật `shipments.fee` và có thể so sánh với `orders.shipping_fee` để phục vụ cho việc đối soát sau này.
