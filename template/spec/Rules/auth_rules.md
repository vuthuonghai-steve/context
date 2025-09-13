# Quy tắc nghiệp vụ cho Người dùng & Xác thực

## 1. Đăng ký (Registration)

-   **UR-01**: Email cung cấp PHẢI là duy nhất trong bảng `users`.
-   **UR-02**: Mật khẩu PHẢI tuân thủ các quy tắc sau:
    -   Dài tối thiểu 8 ký tự.
    -   Chứa ít nhất một chữ hoa (A-Z).
    -   Chứa ít nhất một chữ thường (a-z).
    -   Chứa ít nhất một chữ số (0-9).
-   **UR-03**: Sau khi đăng ký thành công, hệ thống PHẢI gửi một email chứa link xác thực đến địa chỉ email của người dùng. Tài khoản sẽ ở trạng thái "chưa xác thực".
-   **UR-04**: Người dùng PHẢI nhấp vào link xác thực để kích hoạt tài khoản. Hệ thống không cho phép đăng nhập đối với các tài khoản chưa xác thực.
-   **UR-05**: Người dùng mới đăng ký PHẢI có vai trò mặc định là `CUSTOMER`.

## 2. Đăng nhập (Login)

-   **UL-01**: Hệ thống PHẢI xác thực người dùng dựa trên `email` và `password`.
-   **UL-02**: Khi đăng nhập thành công, hệ thống PHẢI cấp cho client một `access_token` (JWT, thời hạn ngắn, ví dụ: 60 phút) và một `refresh_token` (thời hạn dài, ví dụ: 30 ngày).
-   **UL-03**: `access_token` PHẢI được gửi kèm trong header `Authorization` của mỗi request cần xác thực.
-   **UL-04**: Nếu người dùng đăng nhập sai mật khẩu quá 5 lần trong vòng 15 phút, tài khoản hoặc IP đó NÊN bị tạm khóa (rate limit).

## 3. Refresh Token

-   **UT-01**: Khi `access_token` hết hạn, client PHẢI sử dụng `refresh_token` để gọi đến endpoint `/api/auth/refresh`.
-   **UT-02**: Nếu `refresh_token` hợp lệ, server PHẢI cấp một cặp `access_token` và `refresh_token` mới. Nếu không, người dùng buộc phải đăng nhập lại.

## 4. Social Login (Google)

-   **US-01**: Khi người dùng chọn đăng nhập bằng Google:
    -   **Case 1: Email chưa tồn tại:** Hệ thống PHẢI tạo một tài khoản mới với email và tên từ Google, đánh dấu tài khoản là đã xác thực và cấp token.
    -   **Case 2: Email đã tồn tại:**
        -   Hệ thống PHẢI thông báo cho người dùng rằng email này đã được đăng ký.
        -   Hệ thống NÊN yêu cầu người dùng đăng nhập bằng mật khẩu của tài khoản đã có để xác minh quyền sở hữu.
        -   Sau khi xác minh thành công, hệ thống PHẢI liên kết tài khoản (lưu `google_id` vào user record) và tiến hành đăng nhập.

## 5. Bảo mật (Security)

-   **USC-01**: Mật khẩu PHẢI được hash bằng thuật toán `bcrypt`.
-   **USC-02**: Hệ thống KHÔNG BAO GIỜ được trả về hash mật khẩu trong bất kỳ phản hồi API nào.
-   **USC-03**: Vai trò `ADMIN` chỉ được gán thủ công bởi người có thẩm quyền trực tiếp trong CSDL hoặc qua một giao diện quản trị an toàn.
