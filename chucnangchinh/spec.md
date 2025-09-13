# Feature Specification: Hệ thống thương mại điện tử bán lò vi sóng

**Feature Branch**: `001-h-th-ng`  
**Created**: 2025-09-13  
**Status**: Draft  
**Input**: User description from files: `Design_database/database.json`, `chucnangchinh/dac-ta-thong-nhat.md`

---

## User Scenarios & Testing *(mandatory)*

### Primary User Story
Người dùng có thể truy cập trang web để tìm kiếm, xem chi tiết, lọc sản phẩm lò vi sóng theo các thông số kỹ thuật. Họ có thể quản lý giỏ hàng, đặt hàng (với phương thức COD ở giai đoạn đầu), theo dõi trạng thái đơn hàng và để lại đánh giá. Quản trị viên sử dụng một giao diện riêng để quản lý toàn bộ hệ thống từ sản phẩm, đơn hàng đến khách hàng và xem các báo cáo thống kê.

### Acceptance Scenarios
1.  **Given** một người dùng ở trang chủ, **When** họ tìm kiếm từ khóa "inverter" và lọc theo thương hiệu "BrandA" với mức giá dưới 5,000,000, **Then** hệ thống hiển thị danh sách các lò vi sóng phù hợp.
2.  **Given** một người dùng đang xem chi tiết sản phẩm, **When** họ nhấn nút "Thêm vào giỏ", **Then** sản phẩm đó được thêm vào giỏ hàng và số lượng tồn kho khả dụng được kiểm tra.
3.  **Given** người dùng có sản phẩm trong giỏ hàng, **When** họ tiến hành thanh toán và điền thông tin giao hàng, **Then** một đơn hàng mới được tạo với trạng thái "pending", phương thức thanh toán "COD", và tồn kho của sản phẩm được tạm giữ (reserved).
4.  **Given** một đơn hàng đã được giao thành công, **When** quản trị viên xác nhận thanh toán COD, **Then** trạng thái đơn hàng chuyển thành "delivered" và trạng thái thanh toán là "paid".
5.  **Given** một người dùng đã đăng nhập và có đơn hàng ở trạng thái "delivered", **When** họ vào xem lịch sử đơn hàng, **Then** họ có thể viết đánh giá (rating và comment) cho các sản phẩm đã mua.

### Edge Cases
-   Xử lý tình huống khi hai người dùng cùng lúc đặt mua sản phẩm cuối cùng trong kho (race condition).
-   Hệ thống sẽ xử lý như thế nào khi người dùng cố gắng áp dụng một mã giảm giá đã hết hạn hoặc không đủ điều kiện?
-   Người dùng cố gắng đặt hàng với số lượng lớn hơn số lượng tồn kho hiện có.
-   Hệ thống xử lý thế nào khi nhận được callback/webhook không hợp lệ hoặc thất bại từ cổng thanh toán/đơn vị vận chuyển?
-   Một người dùng đăng ký bằng email đã được sử dụng cho một tài khoản social login.

---

## Requirements *(mandatory)*

### Functional Requirements (Phase 1 - MVP)
-   **FR-P1-001**: Hệ thống PHẢI hiển thị danh sách và chi tiết sản phẩm với đầy đủ thông tin: tên, mô tả, hình ảnh, giá, thương hiệu, và các thông số kỹ thuật chuyên biệt (công suất, dung tích, inverter, khóa trẻ em, v.v.).
-   **FR-P1-002**: Hệ thống PHẢI cung cấp bộ lọc và tìm kiếm sản phẩm theo: từ khóa, khoảng giá, thương hiệu, rating, và các thông số kỹ thuật.
-   **FR-P1-003**: Hệ thống PHẢI hiển thị các sản phẩm bán chạy dựa trên doanh số hoặc điểm đánh giá trung bình.
-   **FR-P1-004**: Người dùng PHẢI có thể đăng ký/đăng nhập bằng email/mật khẩu và đăng nhập qua Google (Socialite). Hệ thống PHẢI xử lý việc liên kết tài khoản nếu email đã tồn tại.
-   **FR-P1-005**: Hệ thống PHẢI có phân quyền vai trò (khách hàng, quản trị viên).
-   **FR-P1-006**: Hệ thống PHẢI quản lý giỏ hàng (thêm/xóa/cập nhật số lượng) và kiểm tra tồn kho theo thời gian thực.
-   **FR-P1-007**: Hệ thống PHẢI cho phép người dùng đặt hàng với phương thức thanh toán khi nhận hàng (COD).
-   **FR-P1-008**: Hệ thống PHẢI quản lý và theo dõi lịch sử trạng thái đơn hàng (pending, processing, packed, shipping, delivered, cancelled).
-   **FR-P1-009**: Người dùng đã hoàn tất đơn hàng PHẢI có thể đánh giá (1-5 sao) và bình luận về sản phẩm đã mua.
-   **FR-P1-010**: Quản trị viên PHẢI có một giao diện (Admin Panel) để CRUD sản phẩm/danh mục, quản lý đơn hàng (bao gồm xác nhận COD), và xem báo cáo doanh thu cơ bản.
-   **FR-P1-011**: Hệ thống PHẢI đảm bảo tính toàn vẹn dữ liệu tồn kho, sử dụng transaction và cơ chế locking để ngăn chặn đặt hàng vượt quá số lượng và xử lý race condition.
-   **FR-P1-012**: Hệ thống PHẢI cung cấp các API endpoints (headless) cho các chức năng phía client.

### Functional Requirements (Phase 2 - Advanced)
-   **FR-P2-001**: Người dùng PHẢI có thể tạo và quản lý danh sách sản phẩm yêu thích (Wishlist).
-   **FR-P2-002**: Hệ thống PHẢI hỗ trợ tạo, quản lý và áp dụng mã giảm giá (Vouchers) với các điều kiện (giá trị đơn hàng tối thiểu, giới hạn sử dụng, ngày hết hạn).
-   **FR-P2-003**: Hệ thống PHẢI có chương trình khách hàng thân thiết (tích điểm khi mua hàng, phân hạng thành viên).
-   **FR-P2-004**: Hệ thống PHẢI gửi thông báo (email/in-app) cho các sự kiện quan trọng (xác nhận đơn hàng, cập nhật trạng thái giao hàng).
-   **FR-P2-005**: Tích hợp hệ thống hỗ trợ khách hàng qua Live Chat hoặc Ticket.
-   **FR-P2-006**: Tích hợp các cổng thanh toán trực tuyến (MoMo/VNPay), xử lý luồng redirect và callback.
-   **FR-P2-007**: Tích hợp API với các đơn vị vận chuyển (GHN/GHTK) để đẩy đơn hàng, lấy mã vận đơn và theo dõi trạng thái vận chuyển tự động qua webhook.
-   **FR-P2-008**: Hệ thống PHẢI cho phép xử lý hoàn tiền (Refund) một phần hoặc toàn bộ, yêu cầu quản trị viên phê duyệt.
-   **FR-P2-009**: Cung cấp hệ thống quản lý nội dung (CMS) cho các trang tĩnh (Giới thiệu, Chính sách) và bài viết (Blog/tin tức).
-   **FR-P2-010**: Hệ thống PHẢI thu thập dữ liệu hành vi người dùng để phục vụ cho việc phân tích và cá nhân hóa gợi ý sản phẩm.

### Non-Functional Requirements
-   **NFR-01**: Mọi request thay đổi dữ liệu quan trọng (đặt hàng, thanh toán) PHẢI được xử lý bên trong một transaction để đảm bảo tính toàn vẹn.
-   **NFR-02**: Hệ thống PHẢI có cơ chế rate limiting cho các API endpoint nhạy cảm (đăng nhập, đặt hàng) để chống tấn công brute-force.
-   **NFR-03**: Mật khẩu người dùng PHẢI được lưu trữ dưới dạng băm (hashed).
-   **NFR-04**: Các tác vụ tốn thời gian (gửi email, xử lý hình ảnh) PHẢI được đưa vào hàng đợi (Queue) để xử lý bất đồng bộ.
-   **NFR-05**: Hệ thống PHẢI có cơ chế ghi log chi tiết cho các lỗi và các sự kiện hệ thống quan trọng.
-   **NFR-06**: Các cột trong cơ sở dữ liệu thường xuyên được dùng để truy vấn, lọc, sắp xếp PHẢI được đánh chỉ mục (index).

### Key Entities *(include if feature involves data)*
-   **Quản lý Sản phẩm**: `Product`, `Category`, `Brand`, `ProductReview`.
-   **Quản lý Kho**: `ProductStock` (tồn kho thực tế), `StockMovement` (lịch sử xuất/nhập/giữ kho).
-   **Quản lý Người dùng & Phân quyền**: `User`, `Address`, `Role`, `Permission`.
-   **Quản lý Bán hàng**: `Order`, `OrderItem` (với snapshot của tên và giá sản phẩm), `Cart`, `CartItem`.
-   **Danh sách yêu thích (Wishlist)**: `Wishlist`, `WishlistItem`.
-   **Khuyến mãi & Loyalty**: `Voucher`, `VoucherUsage`, `Promotion`, `LoyaltyTier`, `UserLoyalty`, `LoyaltyTransaction`.
-   **Thanh toán & Hoàn tiền**: `Payment`, `PaymentTransaction`, `Refund`, `RefundItem`, `RefundProcessLog`.
-   **Vận chuyển**: `Carrier` (đơn vị vận chuyển), `Shipment` (thông tin vận đơn), `ShipmentEvent` (lịch sử trạng thái từ hãng vận chuyển).
-   **CMS & Hỗ trợ**: `Page`, `Post`, `PostCategory`, `PostTag`, `Ticket`, `TicketMessage`, `TicketAttachment`.
-   **Thông báo & Webhook**: `NotificationTemplate`, `NotificationLog`, `WebhookConfig`, `WebhookLog`.
-   **Thống kê & Phân tích**: `ProductStatistic`, `UserEvent`, `ProductDailyStat`, `UserRecommendation`.

---

## Review & Acceptance Checklist
*GATE: Automated checks run during main() execution*

### Content Quality
- [ ] No implementation details (languages, frameworks, APIs)
- [ ] Focused on user value and business needs
- [ ] Written for non-technical stakeholders
- [ ] All mandatory sections completed

### Requirement Completeness
- [ ] No [NEEDS CLARIFICATION] markers remain
- [ ] Requirements are testable and unambiguous  
- [ ] Success criteria are measurable
- [ ] Scope is clearly bounded
- [ ] Dependencies and assumptions identified

---

## Execution Status
*Updated by main() during processing*

- [ ] User description parsed
- [ ] Key concepts extracted
- [ ] Ambiguities marked
- [ ] User scenarios defined
- [ ] Requirements generated
- [ ] Entities identified
- [ ] Review checklist passed

---
