# Luồng người dùng: Đăng ký và Đăng nhập

## Kịch bản 1: Đăng ký tài khoản mới thành công (Happy Path)

-   **Bối cảnh (Given):** Một người dùng mới (Guest) đang ở trang "Đăng ký".
-   **Khi (When):**
    1.  Người dùng điền đầy đủ và hợp lệ các thông tin: `name`, `email` (chưa tồn tại trong hệ thống), `password` và `password_confirmation` (trùng khớp và đủ độ phức tạp).
    2.  Và người dùng nhấn nút "Đăng ký".
-   **Thì (Then):**
    1.  Hệ thống gọi API `POST /api/auth/register`.
    2.  Hệ thống tạo một bản ghi `user` mới với trạng thái "chưa xác thực" và vai trò `CUSTOMER`.
    3.  Hệ thống gửi một email chứa link kích hoạt tài khoản tới địa chỉ email đã đăng ký.
    4.  Hệ thống hiển thị thông báo yêu cầu người dùng kiểm tra email để hoàn tất đăng ký.
-   **Và Khi (And When):**
    1.  Người dùng mở email và nhấp vào link kích hoạt.
-   **Thì (Then):**
    1.  Hệ thống xác thực link, kích hoạt tài khoản người dùng (cập nhật `email_verified_at`).
    2.  Hệ thống chuyển hướng người dùng đến trang "Đăng nhập" với thông báo "Xác thực thành công".

## Kịch bản 2: Đăng nhập bằng Email thành công

-   **Bối cảnh (Given):** Một người dùng đã có tài khoản đã được kích hoạt.
-   **Khi (When):**
    1.  Người dùng ở trang "Đăng nhập", điền đúng `email` và `password`.
    2.  Và người dùng nhấn nút "Đăng nhập".
-   **Thì (Then):**
    1.  Hệ thống gọi API `POST /api/auth/login`.
    2.  Hệ thống xác thực thông tin, trả về thông tin `user` và cặp `access_token`, `refresh_token`.
    3.  Giao diện lưu lại token và chuyển hướng người dùng vào trang cá nhân hoặc trang chủ.

## Kịch bản 3: Đăng nhập bằng Google (Email chưa tồn tại)

-   **Bối cảnh (Given):** Một người dùng mới (Guest) ở trang "Đăng nhập".
-   **Khi (When):** Người dùng nhấn nút "Đăng nhập với Google" và hoàn tất việc xác thực bên phía Google.
-   **Thì (Then):**
    1.  Hệ thống nhận callback từ Google qua API `GET /api/auth/google/callback`.
    2.  Hệ thống kiểm tra và thấy email từ Google trả về chưa tồn tại trong CSDL.
    3.  Hệ thống tự động tạo một tài khoản `user` mới với thông tin từ Google, và tài khoản này được coi là đã kích hoạt.
    4.  Hệ thống trả về thông tin `user` và cặp `access_token`, `refresh_token`.
    5.  Giao diện lưu lại token và đăng nhập cho người dùng.

## Kịch bản 4: Đăng nhập bằng Google (Email đã tồn tại)

-   **Bối cảnh (Given):** Người dùng đã có tài khoản đăng ký bằng email/password (ví dụ: `user@example.com`), nhưng chưa liên kết với Google.
-   **Khi (When):** Người dùng nhấn "Đăng nhập với Google" và chọn tài khoản Google cũng có email là `user@example.com`.
-   **Thì (Then):**
    1.  Hệ thống nhận callback, kiểm tra và thấy email đã tồn tại.
    2.  Hệ thống **KHÔNG** tự động đăng nhập. Thay vào đó, hiển thị thông báo: "Email này đã được đăng ký. Vui lòng đăng nhập bằng mật khẩu để liên kết tài khoản."
-   **Và Khi (And When):**
    1.  Người dùng nhập đúng mật khẩu cho tài khoản `user@example.com`.
-   **Thì (Then):**
    1.  Hệ thống xác nhận quyền sở hữu, cập nhật bản ghi `user` đã có với `google_id` từ Google.
    2.  Hệ thống tiến hành đăng nhập và trả về token.

## Các kịch bản lỗi và luồng phụ

-   **Đăng ký - Email đã tồn tại:**
    -   **Khi:** Người dùng đăng ký với email đã có trong hệ thống.
    -   **Thì:** API trả về lỗi `422`, giao diện hiển thị thông báo "Email này đã được sử dụng".
-   **Đăng ký - Mật khẩu không khớp:**
    -   **Khi:** Người dùng nhập `password` và `password_confirmation` không giống nhau.
    -   **Thì:** API trả về lỗi `422`, giao diện hiển thị thông báo lỗi tại trường `password_confirmation`.
-   **Đăng nhập - Sai thông tin:**
    -   **Khi:** Người dùng nhập sai email hoặc mật khẩu.
    -   **Thì:** API trả về lỗi `401 Unauthorized`, giao diện hiển thị thông báo "Email hoặc mật khẩu không chính xác".
-   **Đăng nhập - Tài khoản chưa xác thực:**
    -   **Khi:** Người dùng cố gắng đăng nhập với tài khoản chưa được kích hoạt qua email.
    -   **Thì:** API trả về lỗi `403 Forbidden` (hoặc `401` với mã lỗi riêng), giao diện hiển thị thông báo "Tài khoản của bạn chưa được kích hoạt. Vui lòng kiểm tra email."
