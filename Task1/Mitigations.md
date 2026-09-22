# NX (No-Execute)
-NX đánh dấu các vùng nhớ dữ liệu như Stack hay Heap là **Non-Execute**, chỉ được write/read. Điều này ngăn việc bộ vi xử lý thực hiện các đoạn shellcode, mã độc được chèn vào vùng nhớ, tránh việc bên tấn công muốn thay đổi con trỏ RIP
-NX hoạt động dựa trên nguyên tắc phân quyền vùng nhớ, 1 memory page chỉ có thể sở hữu quyền Write hoặc Execute, ko thể sở hữu cả 2 cùng lúc  
-Do ko thể thực thi code tự chèn, mình có thể dùng các đoạn code có sẵn trong vùng nhớ có quyền Execute (thư viện `libc` hoặc phân đoạn `.text`)
