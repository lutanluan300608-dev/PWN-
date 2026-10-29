# Linking
-Là việc kết hợp các tệp mã máy với thư viện tạo thành 1 tệp thực thi hoàn chỉnh 
# Static Linking (Liên kết tĩnh)
-1 chương trình liên kết tĩnh nó sẽ được biên dịch bằng cách Nhúng toàn bộ mã máy toàn bộ mã máy của thư viện C vào phân vùng `.text`. Các mã đó đã được biên dịch sẵn từ mã nguồn C của các hàm tiêu chuẩn (như printf, scanf, malloc, exit,...)  
# Dynamic Linking (Liên kết động) 
-1 chương trình liên kết động nó sẽ phụ thuộc vào thư viện bên ngoài, ví dụ `libc.so`. Khi chạy chương trình thì hệ điều hành mới nạp thư viện vào bộ nhớ và thực hiện liên kết địa chỉ  
