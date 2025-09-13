# Quy tắc nghiệp vụ cho Hoàn tiền (Refunds)

## 1. Điều kiện Yêu cầu Hoàn tiền

-   **RF-01**: Người dùng chỉ có thể gửi yêu cầu hoàn tiền cho một đơn hàng có trạng thái (`status`) là `delivered`.
-   **RF-02**: Yêu cầu hoàn tiền PHẢI được gửi trong vòng **7 ngày** kể từ ngày đơn hàng được giao (`delivered_at`).
-   **RF-03**: Các sản phẩm (`order_item_id`) trong yêu cầu hoàn tiền PHẢI thuộc về `order_id` được chỉ định.
-   **RF-04**: Số lượng hoàn tiền (`quantity`) của một sản phẩm không được vượt quá số lượng đã mua trong `order_items`.
-   **RF-05**: Một `order_item` chỉ có thể được đưa vào một yêu cầu hoàn tiền duy nhất để tránh trùng lặp.

## 2. Xử lý Yêu cầu Hoàn tiền (Admin)

-   **RF-06**: Một yêu cầu hoàn tiền mới sẽ có trạng thái (`status`) là `requested`.
-   **RF-07**: Quản trị viên (Admin) có quyền chuyển trạng thái của một yêu cầu từ `requested` sang `approved` (chấp thuận) hoặc `rejected` (từ chối).
    -   Nếu `rejected`, Admin NÊN cung cấp lý do từ chối.
-   **RF-08**: Sau khi được `approved`, yêu cầu sẽ được chuyển sang `processing` (đang xử lý) và cuối cùng là `completed` (hoàn tất) khi tiền đã được hoàn trả cho khách hàng.

## 3. Cập nhật Dữ liệu

-   **RF-09**: Khi một yêu cầu hoàn tiền được đánh dấu là `completed`, hệ thống PHẢI cập nhật `payment_status` của đơn hàng gốc (ví dụ: sang `partially_refunded` hoặc `refunded`).
-   **RF-10**: (Quản lý kho) Việc hoàn trả hàng hóa vào kho **KHÔNG** được thực hiện tự động. Admin PHẢI thực hiện một quy trình điều chỉnh kho thủ công riêng biệt sau khi đã nhận và kiểm tra hàng hoàn về.
