# Luồng người dùng: Xem danh sách và chi tiết sản phẩm

Luồng này mô tả cách người dùng tương tác với hệ thống để duyệt, tìm kiếm, lọc và xem sản phẩm.

**Actor:** Người dùng (Guest hoặc đã đăng nhập)

## Các bước chính

1.  **Truy cập trang danh sách sản phẩm:**
    *   Người dùng truy cập vào trang hiển thị danh sách sản phẩm (ví dụ: trang chủ, trang danh mục).
    *   Hệ thống gọi API `GET /api/products` để lấy và hiển thị một danh sách các sản phẩm đã được phân trang.

2.  **Tìm kiếm và Lọc sản phẩm:**
    *   Người dùng nhập từ khóa vào ô tìm kiếm (ví dụ: "inverter").
    *   Người dùng sử dụng các bộ lọc có sẵn:
        *   Lọc theo thương hiệu (ví dụ: "BrandA").
        *   Lọc theo khoảng giá (ví dụ: dưới 5,000,000).
        *   Lọc theo đánh giá (ví dụ: từ 4 sao trở lên).
    *   Khi người dùng áp dụng bộ lọc, hệ thống gọi lại API `GET /api/products` với các query parameter tương ứng (ví dụ: `?q=inverter&brand=branda&max_price=5000000`).
    *   Danh sách sản phẩm được cập nhật để phản ánh kết quả lọc.

3.  **Xem chi tiết sản phẩm:**
    *   Người dùng nhấp vào một sản phẩm trong danh sách.
    *   Hệ thống điều hướng đến trang chi tiết sản phẩm.
    *   Hệ thống gọi API `GET /api/products/{id}` với ID của sản phẩm đó.
    *   Thông tin chi tiết của sản phẩm được hiển thị, bao gồm tên, hình ảnh, mô tả, giá, và tất cả các thông số kỹ thuật.

## Kịch bản chấp nhận (tham chiếu)

*   **Kịch bản 1:** **Given** một người dùng ở trang chủ, **When** họ tìm kiếm từ khóa "inverter" và lọc theo thương hiệu "BrandA" với mức giá dưới 5,000,000, **Then** hệ thống hiển thị danh sách các lò vi sóng phù hợp.
*   **Kịch bản 2:** **Given** một người dùng đang xem chi tiết sản phẩm, **When** họ xem thông tin, **Then** họ sẽ thấy đầy đủ các thông số như công suất, dung tích, có inverter hay không, v.v.
