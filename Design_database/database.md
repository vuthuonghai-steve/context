# Thiết kế Cơ sở Dữ liệu (Chuẩn hóa & Tối ưu hóa)

## 1. Bảng `brands`

| Tên cột    | Kiểu dữ liệu | Ghi chú                  |
| ---------- | ------------ | ------------------------ |
| id         | BIGINT       | PK, AUTO\_INCREMENT      |
| name       | VARCHAR(255) | Unique                   |
| slug       | VARCHAR(255) | Unique                   |
| is\_active | BOOLEAN      | Mặc định `true`          |
| timestamps | TIMESTAMP    | created\_at, updated\_at |

## 2. Bảng `categories`

| Tên cột    | Kiểu dữ liệu | Ghi chú                                 |
| ---------- | ------------ | --------------------------------------- |
| id         | BIGINT       | PK, AUTO\_INCREMENT                     |
| parent\_id | BIGINT       | FK → categories.id (nullable, SET NULL) |
| name       | VARCHAR(255) |                                         |
| slug       | VARCHAR(255) | Unique                                  |
| is\_active | BOOLEAN      | Mặc định `true`                         |
| timestamps | TIMESTAMP    | created\_at, updated\_at                |

## 3. Bảng `products`

| Tên cột          | Kiểu dữ liệu  | Ghi chú                       |
| ---------------- | ------------- | ----------------------------- |
| id               | BIGINT        | PK, AUTO\_INCREMENT           |
| category\_id     | BIGINT        | FK → categories.id (RESTRICT) |
| brand\_id        | BIGINT        | FK → brands.id (RESTRICT)     |
| name             | VARCHAR(255)  |                               |
| slug             | VARCHAR(255)  | Unique                        |
| price            | DECIMAL(12,2) |                               |
| sale\_price      | DECIMAL(12,2) | Nullable, CHECK: <= price     |
| capacity\_liters | INT           |                               |
| power\_watt      | INT           | Nullable                      |
| has\_grill       | BOOLEAN       | Mặc định `false`              |
| inverter         | BOOLEAN       |                               |
| child\_lock      | BOOLEAN       |                               |
| energy\_rating   | TINYINT       | Nullable                      |
| warranty\_months | INT           |                               |
| thumbnail        | VARCHAR(255)  | Nullable                      |
| description      | LONGTEXT      | Nullable                      |
| is\_active       | BOOLEAN       | Mặc định `true`               |
| timestamps       | TIMESTAMP     | created\_at, updated\_at      |

## 4. Bảng `inventories`

| Tên cột         | Kiểu dữ liệu | Ghi chú                   |
| --------------- | ------------ | ------------------------- |
| product\_id     | BIGINT       | PK, FK → products.id      |
| stock\_on\_hand | INT UNSIGNED | Tổng tồn kho thực tế      |
| stock\_reserved | INT UNSIGNED | Số lượng đã đặt chưa giao |

## 5. Bảng `inventory_movements`

| Tên cột     | Kiểu dữ liệu | Ghi chú                                        |
| ----------- | ------------ | ---------------------------------------------- |
| id          | BIGINT       | PK, AUTO\_INCREMENT                            |
| product\_id | BIGINT       | FK → products.id                               |
| type        | ENUM         | 'in','out','reserve','release','sell','adjust' |
| quantity    | INT UNSIGNED | Số lượng                                       |
| order\_id   | BIGINT       | FK → orders.id, nullable                       |
| note        | VARCHAR(255) | Ghi chú                                        |
| created\_at | TIMESTAMP    | Thời điểm ghi nhận                             |

## 6. Bảng `product_stats`

| Tên cột         | Kiểu dữ liệu    | Ghi chú                  |
| --------------- | --------------- | ------------------------ |
| product\_id     | BIGINT          | PK, FK → products.id     |
| total\_sold     | BIGINT UNSIGNED | Tổng số lượng đã bán     |
| average\_rating | DECIMAL(3,2)    | Điểm trung bình đánh giá |
| reviews\_count  | INT UNSIGNED    | Số lượt đánh giá         |

## 7. Bảng `users`

| Tên cột             | Kiểu dữ liệu | Ghi chú                  |
| ------------------- | ------------ | ------------------------ |
| id                  | BIGINT       | PK                       |
| name                | VARCHAR(255) |                          |
| email               | VARCHAR(255) | Unique                   |
| password            | VARCHAR(255) | Mã hóa                   |
| role                | ENUM         | 'CUSTOMER', 'ADMIN'      |
| email\_verified\_at | TIMESTAMP    | Nullable                 |
| timestamps          | TIMESTAMP    | created\_at, updated\_at |

## 8. Bảng `addresses`

| Tên cột       | Kiểu dữ liệu | Ghi chú       |
| ------------- | ------------ | ------------- |
| id            | BIGINT       | PK            |
| user\_id      | BIGINT       | FK → users.id |
| full\_name    | VARCHAR(255) |               |
| phone         | VARCHAR(20)  |               |
| line1         | VARCHAR(255) |               |
| district      | VARCHAR(100) |               |
| city          | VARCHAR(100) |               |
| country\_code | CHAR(2)      | Mặc định 'VN' |
| is\_default   | BOOLEAN      |               |

## 9. Bảng `orders`

| Tên cột               | Kiểu dữ liệu  | Ghi chú                                                            |
| --------------------- | ------------- | ------------------------------------------------------------------ |
| id                    | BIGINT        | PK, AUTO\_INCREMENT                                                |
| user\_id              | BIGINT        | FK → users.id                                                      |
| code                  | VARCHAR(20)   | Mã đơn duy nhất                                                    |
| status                | ENUM          | 'pending','processing','packed','shipping','delivered','cancelled' |
| payment\_status       | ENUM          | 'unpaid','paid'                                                    |
| payment\_method       | ENUM          | 'cod'                                                              |
| subtotal              | DECIMAL(12,2) |                                                                    |
| discount\_total       | DECIMAL(12,2) |                                                                    |
| shipping\_fee         | DECIMAL(12,2) |                                                                    |
| grand\_total          | DECIMAL(12,2) |                                                                    |
| shipping\_address\_id | BIGINT        | FK → addresses.id                                                  |
| notes                 | TEXT          | Ghi chú đơn hàng                                                   |
| timestamps            | TIMESTAMP     | placed\_at, paid\_at, delivered\_at, created\_at                   |

## 10. Bảng `order_items`

| Tên cột         | Kiểu dữ liệu  | Ghi chú                             |
| --------------- | ------------- | ----------------------------------- |
| id              | BIGINT        | PK, AUTO\_INCREMENT                 |
| order\_id       | BIGINT        | FK → orders.id                      |
| product\_id     | BIGINT        | FK → products.id                    |
| name\_snapshot  | VARCHAR(255)  | Tên sản phẩm tại thời điểm đặt hàng |
| price\_snapshot | DECIMAL(12,2) | Giá tại thời điểm đặt hàng          |
| quantity        | INT           |                                     |
| total           | DECIMAL(12,2) | Tổng tiền cho mục này               |

## 11. Bảng `reviews`

| Tên cột         | Kiểu dữ liệu | Ghi chú                      |
| --------------- | ------------ | ---------------------------- |
| id              | BIGINT       | PK, AUTO\_INCREMENT          |
| order\_item\_id | BIGINT       | FK → order\_items.id, UNIQUE |
| user\_id        | BIGINT       | FK → users.id                |
| product\_id     | BIGINT       | FK → products.id             |
| rating          | TINYINT      | 1–5 sao                      |
| comment         | TEXT         | Bình luận                    |
| created\_at     | TIMESTAMP    |                              |

## 12. Bảng `carts`

| Tên cột    | Kiểu dữ liệu | Ghi chú                            |
| ---------- | ------------ | ---------------------------------- |
| id         | BIGINT       | PK                                 |
| user\_id   | BIGINT       | FK → users.id                      |
| status     | ENUM         | 'active', 'converted', 'abandoned' |
| timestamps | TIMESTAMP    | created\_at                        |

## 13. Bảng `cart_items`

| Tên cột         | Kiểu dữ liệu  | Ghi chú          |
| --------------- | ------------- | ---------------- |
| id              | BIGINT        | PK               |
| cart\_id        | BIGINT        | FK → carts.id    |
| product\_id     | BIGINT        | FK → products.id |
| price\_snapshot | DECIMAL(12,2) | Giá snapshot     |
| quantity        | INT           | Số lượng         |

## 14. Bảng `payments`

| Tên cột   | Kiểu dữ liệu  | Ghi chú                          |
| --------- | ------------- | -------------------------------- |
| id        | BIGINT        | PK                               |
| order\_id | BIGINT        | FK → orders.id, UNIQUE           |
| provider  | ENUM          | 'cod'                            |
| amount    | DECIMAL(12,2) |                                  |
| txn\_code | VARCHAR(255)  | Nullable                         |
| status    | ENUM          | 'initiated','succeeded','failed' |
| paid\_at  | TIMESTAMP     |                                  |

---

## 15. Bảng `vouchers`

| Tên cột                 | Kiểu dữ liệu            | Ghi chú                     |
| ----------------------- | ----------------------- | --------------------------- |
| id                      | BIGINT                  | PK, AUTO\_INCREMENT         |
| code                    | VARCHAR(50)             | Unique, UPPERCASE           |
| type                    | ENUM('PERCENT','FIXED') | Loại giảm giá               |
| value                   | DECIMAL(12,2)           | % (0–100) hoặc số tiền      |
| max\_discount           | DECIMAL(12,2)           | Nullable, trần giảm         |
| min\_order\_total       | DECIMAL(12,2)           | Nullable, điều kiện áp dụng |
| start\_at               | TIMESTAMP               |                             |
| end\_at                 | TIMESTAMP               |                             |
| usage\_limit            | INT                     | Tổng số lượt sử dụng        |
| used\_count             | INT                     | Đã dùng                     |
| per\_user\_limit        | INT                     | Mặc định 1                  |
| is\_active              | BOOLEAN                 |                             |
| created\_at/updated\_at | TIMESTAMP               |                             |

## 16. Bảng `voucher_usages`

| Tên cột     | Kiểu dữ liệu                           | Ghi chú                  |
| ----------- | -------------------------------------- | ------------------------ |
| id          | BIGINT                                 | PK                       |
| voucher\_id | BIGINT                                 | FK → vouchers.id         |
| user\_id    | BIGINT                                 | FK → users.id            |
| order\_id   | BIGINT                                 | FK → orders.id, nullable |
| status      | ENUM('reserved','consumed','released') |                          |
| used\_at    | TIMESTAMP                              | Nullable                 |
| created\_at | TIMESTAMP                              |                          |

## 17. Bảng `sales` (Chương trình khuyến mãi)

| Tên cột                 | Kiểu dữ liệu                     | Ghi chú                |
| ----------------------- | -------------------------------- | ---------------------- |
| id                      | BIGINT                           | PK                     |
| name                    | VARCHAR(255)                     |                        |
| description             | TEXT                             |                        |
| scope                   | ENUM('ALL','CATEGORY','PRODUCT') | Phạm vi áp dụng        |
| payload                 | JSON                             | Danh sách id/điều kiện |
| start\_at/end\_at       | TIMESTAMP                        |                        |
| is\_active              | BOOLEAN                          |                        |
| created\_at/updated\_at | TIMESTAMP                        |                        |

## 18. Loyalty (Điểm thưởng & hạng thành viên)

### 18.1 `loyalty_tiers`

| Tên cột     | Kiểu dữ liệu | Ghi chú                     |
| ----------- | ------------ | --------------------------- |
| id          | BIGINT       | PK                          |
| name        | VARCHAR(100) | Hạng (Silver/Gold/Platinum) |
| min\_points | INT          | Ngưỡng lên hạng             |
| benefits    | JSON         | Mô tả quyền lợi             |

### 18.2 `loyalty_accounts`

| Tên cột         | Kiểu dữ liệu | Ghi chú                |
| --------------- | ------------ | ---------------------- |
| user\_id        | BIGINT       | PK, FK → users.id      |
| points\_balance | INT          | Số điểm hiện tại       |
| tier\_id        | BIGINT       | FK → loyalty\_tiers.id |
| updated\_at     | TIMESTAMP    |                        |

### 18.3 `loyalty_transactions`

| Tên cột     | Kiểu dữ liệu                           | Ghi chú                  |
| ----------- | -------------------------------------- | ------------------------ |
| id          | BIGINT                                 | PK                       |
| user\_id    | BIGINT                                 | FK → users.id            |
| type        | ENUM('earn','spend','expire','adjust') |                          |
| points      | INT                                    | +/-                      |
| order\_id   | BIGINT                                 | FK → orders.id, nullable |
| note        | VARCHAR(255)                           |                          |
| created\_at | TIMESTAMP                              |                          |

## 19. Wishlist

### 19.1 `wishlists`

| Tên cột     | Kiểu dữ liệu | Ghi chú       |
| ----------- | ------------ | ------------- |
| id          | BIGINT       | PK            |
| user\_id    | BIGINT       | FK → users.id |
| created\_at | TIMESTAMP    |               |

### 19.2 `wishlist_items`

| Tên cột      | Kiểu dữ liệu | Ghi chú                            |
| ------------ | ------------ | ---------------------------------- |
| id           | BIGINT       | PK                                 |
| wishlist\_id | BIGINT       | FK → wishlists.id                  |
| product\_id  | BIGINT       | FK → products.id                   |
| created\_at  | TIMESTAMP    | UNIQUE (wishlist\_id, product\_id) |

## 20. Vận chuyển & Theo dõi (GHN/GHTK)

### 20.1 `carriers`

| Tên cột    | Kiểu dữ liệu | Ghi chú                                     |
| ---------- | ------------ | ------------------------------------------- |
| id         | BIGINT       | PK                                          |
| code       | VARCHAR(50)  | Unique (ghn, ghtk, vnpost…)                 |
| name       | VARCHAR(100) |                                             |
| config     | JSON         | Endpoint, service codes (không chứa secret) |
| is\_active | BOOLEAN      |                                             |

### 20.2 `shipments`

| Tên cột                   | Kiểu dữ liệu                                                                      | Ghi chú                |
| ------------------------- | --------------------------------------------------------------------------------- | ---------------------- |
| id                        | BIGINT                                                                            | PK                     |
| order\_id                 | BIGINT                                                                            | FK → orders.id, UNIQUE |
| carrier\_id               | BIGINT                                                                            | FK → carriers.id       |
| tracking\_code            | VARCHAR(100)                                                                      |                        |
| status                    | ENUM('requested','ready','picking','in\_transit','delivered','failed','returned') |                        |
| fee                       | DECIMAL(12,2)                                                                     | Nullable               |
| label\_url                | VARCHAR(255)                                                                      | Nullable               |
| estimated\_delivery\_date | DATE                                                                              | Nullable               |
| shipped\_at               | TIMESTAMP                                                                         | Nullable               |
| delivered\_at             | TIMESTAMP                                                                         | Nullable               |
| raw\_payload              | JSON                                                                              | Payload trả về từ hãng |
| created\_at/updated\_at   | TIMESTAMP                                                                         |                        |

### 20.3 `shipment_events`

| Tên cột      | Kiểu dữ liệu | Ghi chú               |
| ------------ | ------------ | --------------------- |
| id           | BIGINT       | PK                    |
| shipment\_id | BIGINT       | FK → shipments.id     |
| status       | VARCHAR(50)  | Mã sự kiện từ carrier |
| message      | VARCHAR(255) | Mô tả                 |
| occurred\_at | TIMESTAMP    | Thời điểm sự kiện     |
| raw          | JSON         | Dữ liệu thô           |

## 21. Webhooks (Callback từ carrier/gateway)

### 21.1 `webhooks`

| Tên cột      | Kiểu dữ liệu | Ghi chú             |
| ------------ | ------------ | ------------------- |
| id           | BIGINT       | PK                  |
| provider     | VARCHAR(50)  | ghn/ghtk/vnpay/momo |
| topic        | VARCHAR(100) |                     |
| secret\_hash | VARCHAR(255) | Dùng verify HMAC    |
| is\_active   | BOOLEAN      |                     |
| created\_at  | TIMESTAMP    |                     |

### 21.2 `webhook_logs`

| Tên cột        | Kiểu dữ liệu | Ghi chú          |
| -------------- | ------------ | ---------------- |
| id             | BIGINT       | PK               |
| webhook\_id    | BIGINT       | FK → webhooks.id |
| request\_id    | VARCHAR(100) | Idempotency      |
| status\_code   | INT          |                  |
| received\_at   | TIMESTAMP    |                  |
| payload        | JSON         |                  |
| processed\_at  | TIMESTAMP    |                  |
| error\_message | VARCHAR(255) | Nullable         |

## 22. Thanh toán mở rộng (VNPay/MoMo)

### 22.1 `payment_transactions`

| Tên cột        | Kiểu dữ liệu                                                   | Ghi chú               |
| -------------- | -------------------------------------------------------------- | --------------------- |
| id             | BIGINT                                                         | PK                    |
| payment\_id    | BIGINT                                                         | FK → payments.id      |
| gateway        | ENUM('vnpay','momo','cod')                                     |                       |
| txn\_ref       | VARCHAR(100)                                                   | Mã tham chiếu gateway |
| amount         | DECIMAL(12,2)                                                  |                       |
| status         | ENUM('initiated','redirected','succeeded','failed','refunded') |                       |
| response\_code | VARCHAR(50)                                                    | Mã phản hồi           |
| message        | VARCHAR(255)                                                   | Mô tả ngắn            |
| raw\_request   | JSON                                                           |                       |
| raw\_response  | JSON                                                           |                       |
| created\_at    | TIMESTAMP                                                      |                       |

## 23. Hoàn tiền (Partial Refund ≤ 7 ngày)

### 23.1 `refunds`

| Tên cột       | Kiểu dữ liệu                                                     | Ghi chú                         |
| ------------- | ---------------------------------------------------------------- | ------------------------------- |
| id            | BIGINT                                                           | PK                              |
| order\_id     | BIGINT                                                           | FK → orders.id                  |
| payment\_id   | BIGINT                                                           | FK → payments.id, nullable      |
| code          | VARCHAR(30)                                                      | Unique                          |
| status        | ENUM('requested','approved','rejected','processing','completed') |                                 |
| amount        | DECIMAL(12,2)                                                    | Tổng tiền hoàn                  |
| reason        | VARCHAR(255)                                                     |                                 |
| approved\_by  | BIGINT                                                           | FK → users.id (admin), nullable |
| requested\_at | TIMESTAMP                                                        |                                 |
| processed\_at | TIMESTAMP                                                        | Nullable                        |

### 23.2 `refund_items`

| Tên cột         | Kiểu dữ liệu  | Ghi chú              |
| --------------- | ------------- | -------------------- |
| id              | BIGINT        | PK                   |
| refund\_id      | BIGINT        | FK → refunds.id      |
| order\_item\_id | BIGINT        | FK → order\_items.id |
| quantity        | INT           | Số lượng hoàn        |
| amount          | DECIMAL(12,2) | Tiền hoàn mục này    |

### 23.3 `refund_events`

| Tên cột     | Kiểu dữ liệu | Ghi chú               |
| ----------- | ------------ | --------------------- |
| id          | BIGINT       | PK                    |
| refund\_id  | BIGINT       | FK → refunds.id       |
| status      | VARCHAR(50)  | Trạng thái tiến trình |
| note        | VARCHAR(255) |                       |
| created\_at | TIMESTAMP    |                       |

## 24. CMS (Trang & Bài viết)

### 24.1 `pages`

| Tên cột                 | Kiểu dữ liệu | Ghi chú  |
| ----------------------- | ------------ | -------- |
| id                      | BIGINT       | PK       |
| slug                    | VARCHAR(150) | Unique   |
| title                   | VARCHAR(255) |          |
| content                 | LONGTEXT     |          |
| meta                    | JSON         | SEO, OG  |
| is\_published           | BOOLEAN      |          |
| published\_at           | TIMESTAMP    | Nullable |
| created\_at/updated\_at | TIMESTAMP    |          |

### 24.2 `posts`

| Tên cột                 | Kiểu dữ liệu | Ghi chú       |
| ----------------------- | ------------ | ------------- |
| id                      | BIGINT       | PK            |
| author\_id              | BIGINT       | FK → users.id |
| title                   | VARCHAR(255) |               |
| slug                    | VARCHAR(150) | Unique        |
| excerpt                 | TEXT         | Nullable      |
| content                 | LONGTEXT     |               |
| is\_published           | BOOLEAN      |               |
| published\_at           | TIMESTAMP    | Nullable      |
| created\_at/updated\_at | TIMESTAMP    |               |

### 24.3 `post_categories`, `post_category_post`

| Bảng                 | Cột chính              | Ghi chú             |
| -------------------- | ---------------------- | ------------------- |
| post\_categories     | id, name, slug         | slug unique         |
| post\_category\_post | post\_id, category\_id | Pivot, composite PK |

### 24.4 `post_tags`, `post_post_tag`

| Bảng            | Cột chính         | Ghi chú             |
| --------------- | ----------------- | ------------------- |
| post\_tags      | id, name, slug    | slug unique         |
| post\_post\_tag | post\_id, tag\_id | Pivot, composite PK |

## 25. Notifications (Email/SMS/Push/In-app)

### 25.1 `notification_templates`

| Tên cột    | Kiểu dữ liệu                       | Ghi chú                                   |
| ---------- | ---------------------------------- | ----------------------------------------- |
| id         | BIGINT                             | PK                                        |
| key        | VARCHAR(100)                       | Unique (order\_placed, order\_shipped...) |
| channel    | ENUM('email','sms','push','inapp') |                                           |
| subject    | VARCHAR(255)                       | Nullable cho non-email                    |
| body       | LONGTEXT                           | Nội dung template                         |
| is\_active | BOOLEAN                            |                                           |

### 25.2 `notification_logs`

| Tên cột        | Kiểu dữ liệu                       | Ghi chú                      |
| -------------- | ---------------------------------- | ---------------------------- |
| id             | BIGINT                             | PK                           |
| user\_id       | BIGINT                             | FK → users.id, nullable      |
| channel        | ENUM('email','sms','push','inapp') |                              |
| template\_key  | VARCHAR(100)                       |                              |
| to\_address    | VARCHAR(255)                       | email/số điện thoại/endpoint |
| subject        | VARCHAR(255)                       | Snapshot                     |
| body\_snapshot | LONGTEXT                           | Snapshot                     |
| status         | ENUM('queued','sent','failed')     |                              |
| sent\_at       | TIMESTAMP                          | Nullable                     |
| error\_message | VARCHAR(255)                       | Nullable                     |

## 26. Hỗ trợ (Tickets)

### 26.1 `tickets`

| Tên cột                 | Kiểu dữ liệu                               | Ghi chú                         |
| ----------------------- | ------------------------------------------ | ------------------------------- |
| id                      | BIGINT                                     | PK                              |
| user\_id                | BIGINT                                     | FK → users.id                   |
| order\_id               | BIGINT                                     | FK → orders.id, nullable        |
| subject                 | VARCHAR(255)                               |                                 |
| status                  | ENUM('open','pending','resolved','closed') |                                 |
| priority                | ENUM('low','normal','high','urgent')       |                                 |
| assigned\_to            | BIGINT                                     | FK → users.id (admin), nullable |
| created\_at/updated\_at | TIMESTAMP                                  |                                 |

### 26.2 `ticket_messages`

| Tên cột     | Kiểu dữ liệu | Ghi chú         |
| ----------- | ------------ | --------------- |
| id          | BIGINT       | PK              |
| ticket\_id  | BIGINT       | FK → tickets.id |
| sender\_id  | BIGINT       | FK → users.id   |
| message     | LONGTEXT     |                 |
| created\_at | TIMESTAMP    |                 |

### 26.3 `ticket_attachments`

| Tên cột             | Kiểu dữ liệu | Ghi chú                  |
| ------------------- | ------------ | ------------------------ |
| id                  | BIGINT       | PK                       |
| ticket\_message\_id | BIGINT       | FK → ticket\_messages.id |
| path                | VARCHAR(255) |                          |
| filename            | VARCHAR(255) |                          |
| mime                | VARCHAR(100) |                          |
| size                | INT          | bytes                    |

## 27. Analytics (Sự kiện & tổng hợp)

### 27.1 `user_events`

| Tên cột      | Kiểu dữ liệu                                                | Ghi chú                    |
| ------------ | ----------------------------------------------------------- | -------------------------- |
| id           | BIGINT                                                      | PK                         |
| user\_id     | BIGINT                                                      | FK → users.id, nullable    |
| session\_id  | VARCHAR(64)                                                 |                            |
| event        | ENUM('view','search','add\_to\_cart','checkout','purchase') |                            |
| product\_id  | BIGINT                                                      | FK → products.id, nullable |
| order\_id    | BIGINT                                                      | FK → orders.id, nullable   |
| metadata     | JSON                                                        | từ khóa, giá, nguồn…       |
| occurred\_at | TIMESTAMP                                                   |                            |

### 27.2 `product_analytics_daily`

| Tên cột        | Kiểu dữ liệu        | Ghi chú          |
| -------------- | ------------------- | ---------------- |
| id             | BIGINT              | PK               |
| date           | DATE                |                  |
| product\_id    | BIGINT              | FK → products.id |
| views          | INT                 |                  |
| add\_to\_carts | INT                 |                  |
| purchases      | INT                 |                  |
| UNIQUE         | (date, product\_id) |                  |

## 28. Cá nhân hóa (Gợi ý sản phẩm)

### 28.1 `user_recommendations`

| Tên cột       | Kiểu dữ liệu            | Ghi chú                            |
| ------------- | ----------------------- | ---------------------------------- |
| id            | BIGINT                  | PK                                 |
| user\_id      | BIGINT                  | FK → users.id                      |
| product\_id   | BIGINT                  | FK → products.id                   |
| score         | DECIMAL(5,2)            | 0–1 hoặc 0–100                     |
| reason        | JSON                    | Nguồn gợi ý (collab/content-based) |
| generated\_at | TIMESTAMP               |                                    |
| expires\_at   | TIMESTAMP               | Nullable                           |
| UNIQUE        | (user\_id, product\_id) |                                    |

---

### Chỉ mục & ràng buộc khuyến nghị (cho tính năng bổ sung)

* `vouchers.code` unique, index `is_active, start_at, end_at`.
* `voucher_usages` composite index `(voucher_id, user_id, status)`.
* `shipments` index `(carrier_id, status)`, `shipment_events(shipment_id, occurred_at)`.
* `payment_transactions` index `(gateway, status, created_at)`, unique `(txn_ref, gateway)`.
* `refunds(order_id)`, `refund_items(refund_id, order_item_id)` composite.
* `posts` index `(is_published, published_at)`, `post_category_post`, `post_post_tag` composite PK.
* `notification_logs(channel, status, sent_at)`.
* `user_events(event, occurred_at)`, `(user_id, occurred_at)`, `(product_id, event)`.
* `user_recommendations(user_id, score DESC)` để query top-N.
