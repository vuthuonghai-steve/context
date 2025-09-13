# Quy tắc nghiệp vụ cho Loyalty

## 1. Chương trình Khách hàng thân thiết (Loyalty Program)

-   **LR-01 (Tích điểm)**:
    -   Người dùng PHẢI được tích điểm sau khi một đơn hàng được chuyển sang trạng thái `status = 'delivered'` VÀ `payment_status = 'paid'`.
    -   Tỷ lệ tích điểm là: **1 điểm** cho mỗi **10,000 VND** trên `subtotal` của đơn hàng (không tính phí ship và giảm giá). Điểm được làm tròn xuống.
    -   Hệ thống PHẢI tạo một bản ghi trong `loyalty_transactions` với `type = 'earn'` cho mỗi lần tích điểm.
-   **LR-02 (Phân hạng thành viên)**:
    -   Hạng thành viên (`loyalty_tiers`) được xác định dựa trên tổng số điểm tích lũy (`user_loyalty.points_balance`).
    -   Ngưỡng điểm (ví dụ):
        -   **Bronze**: 0 - 99 điểm
        -   **Silver**: 100 - 499 điểm
        -   **Gold**: >= 500 điểm
    -   Việc xét lại hạng thành viên NÊN được thực hiện mỗi khi `points_balance` của người dùng thay đổi.
-   **LR-03 (Sử dụng & Hết hạn điểm)**:
    -   (Giai đoạn 2 chưa định nghĩa việc tiêu điểm) Quy tắc về việc tiêu điểm sẽ được định nghĩa sau.
    -   Điểm tích lũy sẽ hết hạn sau **365 ngày** kể từ ngày giao dịch tích điểm. Một tác vụ nền (scheduled job) NÊN chạy hàng ngày để xử lý việc này và ghi nhận vào `loyalty_transactions` với `type = 'expire'`.
