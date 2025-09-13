# Đặc tả hệ thống thương mại điện tử (bán lò vi sóng) — Bản hợp nhất

Tài liệu này hợp nhất và chuẩn hóa nội dung từ hai file trong thư mục `context` thành một đặc tả thống nhất, được lưu với mã hóa UTF‑8.

## 1. Mục tiêu & Phạm vi
- Bán lò vi sóng và phụ kiện liên quan, tập trung vào việc thể hiện thông số kỹ thuật (công suất, hiệu suất năng lượng, dung tích, inverter, khóa trẻ em…), quản lý tồn kho, giỏ hàng, đặt hàng, thanh toán COD (giai đoạn đầu), theo dõi đơn, đánh giá và thống kê.
- Backend dùng Laravel (Eloquent ORM, Resource Controllers), API headless phục vụ web và mobile qua RESTful endpoints. Admin dashboard dùng Filament.

## 2. Kiến trúc & Công nghệ khuyến nghị
- Nền tảng: Laravel 12, PHP ≥ 8.2, MySQL.
- Xác thực: JWT (expire 60 phút, hỗ trợ silent refresh ở frontend) hoặc Sanctum; Social login Google (Laravel Socialite).
- Phân quyền: Spatie Permissions (roles/permissions).
- Tìm kiếm: Laravel Scout (+ Algolia/Meilisearch) cho realtime search và bộ lọc.
- Admin: Filament Admin Panel.
- Hàng đợi: Redis/Queue cho email, sự kiện đơn hàng.
- Realtime/Live chat: Tidio (tích hợp script) ở giai đoạn nâng cao.

## 3. Chức năng chính (MVP/Phase 1)
### 3.1. Sản phẩm & Danh mục
- Hiển thị danh sách và chi tiết sản phẩm: tên, mô tả, hình ảnh, giá, tình trạng còn hàng, thông số chuyên biệt (công suất W, dung tích L, hiệu suất năng lượng, inverter, khóa trẻ em, thương hiệu, kích thước).
- Phân loại theo danh mục (ví dụ: lò vi sóng nướng, inverter…).
- Bộ lọc và tìm kiếm theo: thương hiệu, giá, rating, thông số (công suất, hiệu suất, dung tích…), từ khóa.
- Hiển thị sản phẩm bán chạy dựa trên doanh số và/hoặc điểm đánh giá trung bình.

### 3.2. Quản lý tồn kho
- Cập nhật số lượng tồn khi thêm vào giỏ/đặt hàng (đồng bộ và chống trừ lố bằng transaction/locking).
- Trường dữ liệu gợi ý: `stock_quantity` và/hoặc `is_in_stock` (boolean).

### 3.3. Người dùng & Xác thực
- Đăng ký/Đăng nhập bằng email/password; xác thực email.
- Đăng nhập Google (Socialite), nếu email đã tồn tại thì gợi ý liên kết/hợp nhất tài khoản.
- Hồ sơ người dùng: xem/sửa thông tin (địa chỉ, lịch sử đơn hàng).

### 3.4. Phân quyền
- Vai trò cơ bản: khách hàng, quản trị viên. Quản trị viên có quyền xác nhận COD, quản lý sản phẩm, đơn hàng, báo cáo.

### 3.5. Giỏ hàng & Đặt hàng
- Thêm/Xóa/Cập nhật số lượng sản phẩm trong giỏ; kiểm tra tồn kho realtime.
- Chọn địa chỉ giao hàng, tính tổng tiền (có thể cộng phí vận chuyển nếu cấu hình).

### 3.6. Thanh toán (Phase 1: COD)
- Hỗ trợ COD (Cash on Delivery). Xác nhận thanh toán thủ công khi giao thành công; quản trị cập nhật trạng thái “paid/delivered”.
- Gợi ý trường: `cod_confirmed_at` (timestamp).

### 3.7. Quản lý đơn hàng
- Theo dõi trạng thái: đặt hàng → đang xử lý → đã đóng gói → đang vận chuyển → đã giao.
- Lịch sử đơn hàng cho khách; quản trị duyệt/hủy/cập nhật trạng thái.
- Gợi ý trường: `ordered_at` (timestamp), lịch sử trạng thái.

### 3.8. Đánh giá & Phản hồi
- Khách hàng được đánh giá (1–5 sao) và bình luận sau khi đơn ở trạng thái “đã giao”.
- Tính rating trung bình, ảnh hưởng đến xếp hạng/bộ lọc.

### 3.9. Thống kê (cơ bản)
- Doanh thu theo khoảng thời gian, sản phẩm bán chạy, rating trung bình theo sản phẩm; hiển thị trên Filament.

### 3.10. API Headless
- Ví dụ: `GET /api/products`, `GET /api/products/{id}`, lọc qua query string; `POST /api/orders`, `GET /api/orders`.
- Auth endpoints: `POST /api/auth/register`, `/login`, `/logout`, `/refresh`, `GET /api/me`.

Ghi chú: Tài liệu tổng quát gợi ý tích hợp cổng thanh toán sớm, nhưng để giảm rủi ro và rút ngắn MVP, bản hợp nhất chốt lộ trình: COD ở Phase 1; cổng thanh toán sẽ ở Phase 2.

## 4. Chức năng nâng cao (Phase 2)
- Wishlist: lưu sản phẩm quan tâm; thêm vào giỏ trực tiếp, kiểm tra tồn kho.
- Khuyến mãi/Voucher: tạo và quản lý mã giảm giá, điều kiện áp dụng.
- Chương trình khách hàng thân thiết: tích điểm, xếp hạng thành viên.
- Email Marketing/Thông báo: xác nhận đơn, khuyến mãi, cập nhật trạng thái.
- Live Chat (Tidio) và/hoặc hệ thống Ticket hỗ trợ.
- CMS: trang tĩnh (Về chúng tôi, Liên hệ, Chính sách), Blog/tin tức.
- Vận chuyển: tích hợp GHN/GHTK để theo dõi vận đơn (webhook/callback nếu có).
- Cổng thanh toán trực tuyến: MoMo/VNPay; redirect gateway sau checkout, callback cập nhật đơn.
- Refund một phần: cho phép hoàn tiền một phần trong 7 ngày, quản trị duyệt.
- Phân tích hành vi (Google Analytics); gợi ý cá nhân hóa sản phẩm theo lịch sử duyệt/mua.
- API nâng cao: ví dụ `POST /api/refunds/{orderId}` (JWT; params: số tiền hoàn, lý do; trả về JSON kết quả).

## 5. Mô hình dữ liệu (đề xuất tối giản)
- users, roles, permissions, model_has_roles (Spatie Permissions)
- products (các trường chính) và product_images
  - Trường gợi ý cho sản phẩm lò vi sóng: `brand`, `power_watt`, `capacity_l`, `inverter` (bool), `child_lock` (bool), `energy_rating`, `size_mm` (W×H×D), `average_rating`
- categories, category_product (n–n)
- inventories hoặc cột trong products: `stock_quantity`, `is_in_stock`
- carts, cart_items
- orders, order_items, order_status_histories
- payments (Phase 2), refunds (Phase 2)
- addresses (shipping/billing)
- reviews (rating, comment, user_id, product_id, order_id)
- vouchers, voucher_user (Phase 2)
- wishlists (Phase 2)
- pages, posts (CMS), tickets (hỗ trợ) — Phase 2

## 6. API chi tiết (ví dụ)
- Public:
  - `GET /api/products?brand=&min_price=&max_price=&rating>=&power_watt=&capacity_l=&inverter=&q=`
  - `GET /api/products/{id}`
  - `GET /api/categories`
- Auth:
  - `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/logout`, `POST /api/auth/refresh`, `GET /api/me`
- Cart/Order:
  - `GET /api/cart`, `POST /api/cart/items`, `PATCH /api/cart/items/{id}`, `DELETE /api/cart/items/{id}`
  - `POST /api/orders`, `GET /api/orders`, `GET /api/orders/{id}`
- Reviews:
  - `POST /api/products/{id}/reviews`, `GET /api/products/{id}/reviews`
- Admin (bảo vệ bằng role):
  - CRUD sản phẩm/danh mục, xác nhận COD, cập nhật trạng thái đơn, báo cáo.
- Phase 2:
  - `POST /api/vouchers/apply`, `GET/POST /api/wishlists`
  - `POST /api/payments/momo|vnpay/checkout` (redirect URL), `POST /api/payments/momo|vnpay/callback`
  - `POST /api/refunds/{orderId}`

## 7. Quy tắc nghiệp vụ (chính)
- Đặt hàng chỉ thành công khi tồn kho đủ; giảm tồn kho theo transaction; rollback nếu thanh toán/đặt hàng thất bại.
- Đánh giá chỉ cho phép khi đơn ở trạng thái “đã giao” và sản phẩm thuộc đơn đó.
- Kiểm soát race condition khi nhiều người đặt cùng sản phẩm (row-level lock hoặc optimistic lock).
- Voucher: thời hạn, số lần dùng, điều kiện giỏ hàng, không cộng dồn theo quy tắc.
- Refund một phần chỉ trong 7 ngày từ khi “đã giao”, cần quản trị duyệt.

## 8. Bảo mật, hiệu năng, chất lượng
- Validation bằng FormRequest; Rate limiting cho endpoints nhạy cảm; CSRF đối với web; JWT/Sanctum cho API.
- Logging/audit sự kiện đơn hàng; giám sát lỗi.
- Indexing cho cột lọc/tìm kiếm; phân trang.
- Dùng Queue cho email/thông báo; lazy-loading vs eager-loading hợp lý.
- Sao lưu DB định kỳ; môi trường DEV/PROD tách biệt; cấu hình env secrets.

## 9. Roadmap triển khai
- Phase 1 (4–6 tuần):
  - Sản phẩm + danh mục + tồn kho; Auth + Roles; Giỏ hàng + Đặt hàng; COD; Đơn hàng + trạng thái; Đánh giá; Thống kê cơ bản; API headless; Admin (Filament).
- Phase 2 (4–6 tuần):
  - Wishlist, Voucher, Loyalty, Email; Live Chat + Ticket; CMS; Vận chuyển GHN/GHTK; Cổng thanh toán MoMo/VNPay + Refund một phần; Analytics; Gợi ý cá nhân hóa.

## 10. Phụ lục
- Trạng thái đơn hàng mẫu: `pending` → `processing` → `packed` → `shipping` → `delivered` → `cancelled`/`returned`.
- Các trường gợi ý: `is_in_stock` (boolean), `cod_confirmed_at` (timestamp), `ordered_at` (timestamp).

