
# TEST
<img width="1917" height="1075" alt="Screenshot 2026-09-05 204227" src="https://github.com/user-attachments/assets/e0e2b3d6-b842-4128-b1f7-8135013fa4ed" />  

-Mình dùng **set disassembly-flavor intel** để chuyển cú pháp hiển thị mã máy thành chuẩn Intel    
-Break _start để mình đặt break point tại _start    
-Lệnh disassemble _start là dunngf để dịch ngược mã máy tại lúc mà khởi chạy chương trình    
-Dòng => 0x0000000000401000 <+0>:    mov    rax,0xa cho biết CPU nằm ở đâu, qua dấu => . Dòng này nghĩa là instruction mà CPU chuẩn bị thực hiện là mov rax,0xa với địa chỉ là 0x401000.    
-Dòng rbx cũng tương tự    
-Lưu ý là sau khi mov rax,0xa thì địa chỉ tăng lên từ 0x401000 lên 0x401007 khoảng cách 2 địa chỉ là 7 bytes. Tức là instruction mov rax,0xa chiếm 7 bytes trong machine code  
<img width="1917" height="392" alt="Screenshot 2026-09-05 211419" src="https://github.com/user-attachments/assets/29a6c9a1-505b-406d-b7cc-6465296da31c" />
-Đầu tiên mình info registers rax rbx để kiểm tra và thấy rax và rbx đều bằng 0, lúc này là CPU chỉ mới chuẩn bị thực hiện lệnh.  
-Sau khi mình si(step instruction), CPU sẽ thực hiện **một lệnh** mã máy, info registers rax rbx và mình thấy rax đã có giá trị 10. Địa chỉ cũng đã chuyển đến 0x401007. Lúc này RAX = 10
-Si thêm lần nữa thì RBX = 20
<img width="1306" height="183" alt="Screenshot 2026-09-07 122944" src="https://github.com/user-attachments/assets/6146f354-e312-434f-8027-575aad5d8271" />
-Sau khi chạy xong 2 lệnh thì mình kiểm tra RIP và thấy nó đang ở 0x40100e, tại đó có 1 instruction  
-Lệnh x/i $rip để mình xem cai instruction mà CPU đang trỏ tới. x: examine, xem nội dung. /i: instruction. $rip: giá trị của rip.  
-Tại sao lại là =>0x40100e: add BYTE PTR [rax],al ? Sau khi chạy si qua 2 instruction, RIP chạy đến địa chỉ sau đó, nếu không có instruction hợp lệ tiếp theo ở đó, CPU/GDB sẽ diễn giải các  byte trong vùng nhớ thành machine instruction.
# ADD
<img width="1917" height="1078" alt="Screenshot 2026-09-07 163750" src="https://github.com/user-attachments/assets/830f2f46-4002-4f96-a6b6-9294a6cb70fd" />
