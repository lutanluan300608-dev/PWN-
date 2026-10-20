<img width="900" height="487" alt="Screenshot 2026-10-16 175205" src="https://github.com/user-attachments/assets/9489bb7d-1262-4edb-bad3-c2de97d49d53" /> 
-Lỗi bof xảy ra ở hàm fgets() khi biến s chỉ khai báo 8 byte nhưng nhận đến 256 byte   

<img width="975" height="250" alt="Screenshot 2026-10-20 154707" src="https://github.com/user-attachments/assets/0b918af1-30c4-42a5-b898-d4a5fd891a12" />

-Khi kiểm tra thì mình thấy có hàm win() và win cũng đang chứa hàm system() thực thi /bin/sh. PIE cũng đang tắt nên mình có thể dùng exe.symbol để lấy địa chỉ hàm win() và cho rip nhảy đến    
**NOTE:** mình có thể sử dụng lệnh p&win để lấy luôn địa chỉ hàm win(), trong chall này là 0x4011f3  

<img width="1472" height="277" alt="image" src="https://github.com/user-attachments/assets/002f634a-77b4-4419-aa1f-e25ab661bba1" />

-Đầu tiên mình disas main và đặt break point tại lệnh printf. Mình tel và tính offset từ đầu buffer đến rip bằng cách `p/x 0x00007fffffffdf98 - 0x00007fffffffdf50` = 0x48.
  
<img width="871" height="407" alt="Screenshot 2026-10-20 155423" src="https://github.com/user-attachments/assets/30c345d7-7e66-4c33-8c32-7ed05148d2b3" />  
-Sau khi tính được offset mình gửi 0x48 byte rác để ghi đè và đưa địa chỉ hàm win() vào

<img width="1792" height="701" alt="Screenshot 2026-10-20 225244" src="https://github.com/user-attachments/assets/da857ec9-984d-4b2d-b337-744d32179c70" />

-Nhưng lại có lỗi và chương trình hiện Access denied!. Mình kiểm tra lại hàm win() thì thấy là 

<img width="853" height="117" alt="Screenshot 2026-10-20 225501" src="https://github.com/user-attachments/assets/33952b87-06f4-4251-af15-ba24c3383aa0" />

-Mình cần phải đưa 0xDEADBEEFDEADBEEF vào 2 biến a1 và a2, a1 và a2 chính là 2 tham số đầu tiên được nhận vào, tức là rdi và rsi. Vậy mình phải đưa 0xDEADBEEFDEADBEEF vào 2 thanh rdi và rsi trước khi nhảy vào hàm win()  

<img width="840" height="468" alt="Screenshot 2026-10-20 225908" src="https://github.com/user-attachments/assets/2aa4cbe6-b813-467c-8e26-26716db071cc" />

-Mình dùng ROPgadget --binary để tìm 2 gadget pop rdi và pop rsi và gán giá trị vào


