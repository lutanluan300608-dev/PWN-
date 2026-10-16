<img width="900" height="487" alt="Screenshot 2026-10-16 175205" src="https://github.com/user-attachments/assets/9489bb7d-1262-4edb-bad3-c2de97d49d53" />
-Vì không có hàm nào để lấy `/bin/sh`, PIE cũng đang bật nên mình sẽ dùng ROPchain để giải bài này  
-Lỗi bof xảy ra ở hàm fgets() khi biến s chỉ khai báo 8 byte nhưng nhận đến 256 byte   
