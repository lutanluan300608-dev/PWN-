<img width="900" height="487" alt="Screenshot 2026-10-16 175205" src="https://github.com/user-attachments/assets/9489bb7d-1262-4edb-bad3-c2de97d49d53" /> 
-Lỗi bof xảy ra ở hàm fgets() khi biến s chỉ khai báo 8 byte nhưng nhận đến 256 byte   

<img width="975" height="250" alt="Screenshot 2026-10-20 154707" src="https://github.com/user-attachments/assets/0b918af1-30c4-42a5-b898-d4a5fd891a12" />

-Khi kiểm tra thì mình thấy có hàm win() và win cũng đang chứa hàm system() thực thi /bin/sh. PIE cũng đang tắt nên mình có thể dùng exe.symbol để lấy địa chỉ hàm win() và nhảy đến 

<img width="871" height="407" alt="Screenshot 2026-10-20 155423" src="https://github.com/user-attachments/assets/30c345d7-7e66-4c33-8c32-7ed05148d2b3" />


