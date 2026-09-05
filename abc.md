# TEST
<img width="1917" height="1075" alt="Screenshot 2026-09-05 204227" src="https://github.com/user-attachments/assets/e0e2b3d6-b842-4128-b1f7-8135013fa4ed" />  

-Mình dùng **set disassembly-flavor intel** để chuyển cú pháp hiển thị mã máy thành chuẩn Intel    
-Break _start để mình đặt break point tại _start    
-Lệnh disassemble _start là dunngf để dịch ngược mã máy tại lúc mà khởi chạy chương trình    
-Dòng => 0x0000000000401000 <+0>:    mov    rax,0xa cho biết CPU nằm ở đâu, qua dấu => . Dòng này nghĩa là instruction mà CPU chuẩn bị thực hiện là mov rax,0xa với địa chỉ là 0x401000.    
-Dòng rbx cũng tương tự    
-Lưu ý là sau khi mov rax,0xa thì địa chỉ tăng lên từ 0x401000 lên 0x401007 khoảng cách 2 địa chỉ là 7 bytes. Tức là instruction mov rax,0xa chiếm 7 bytes trong machine code  

