
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

# SUB
<img width="1917" height="486" alt="Screenshot 2026-09-07 164921" src="https://github.com/user-attachments/assets/d1a4e215-cf9e-4794-b0ee-a8f3936495fe" />  

# CMP
<img width="1917" height="1077" alt="Screenshot 2026-09-07 173545" src="https://github.com/user-attachments/assets/e7ea5641-8617-47cf-a5c0-5fe41fe04288" />  
-Khi lệnh cmp chạy thì nó sẽ so sánh giữa rax và 10, lấy 10 - 10 = 0 và ghi nhận qua ZF  
-Dòng eflags  0x246   [ PF ZF IF ] thông tin của eflags
+ZF(Zero Flag) = 1 và CPU bật. VÌ sau khi cmp, kết quẩ được trả ra là 0. **Nói đơn giản, ZF = 1 khi kết quả = 0, ZF = 0 khi kết quả khác 0**
-Và đây cũng là bước đầu cho các lệnh giống if trong C
<img width="1917" height="1078" alt="Screenshot 2026-09-07 175029" src="https://github.com/user-attachments/assets/9430b693-85ff-44a5-ad7c-3016ab0a3638" />
-Sau khi sửa cmp rax, 5 thì kết quả ko có ZF.  

# JE
<img width="1900" height="383" alt="Screenshot 2026-09-07 180410" src="https://github.com/user-attachments/assets/a92e833d-c620-49c0-a6e7-abd5ffebd52c" />
<img width="1917" height="1075" alt="Screenshot 2026-09-07 181053" src="https://github.com/user-attachments/assets/e99df4c5-d02a-457d-854b-b7448b7de847" />
-Như đã thấy, rbx = 2 thay vì 1, chứng tỏ sau khi cmp, ZF = 1 và đã thực hiện nhảy đến equal, bỏ qua mov rbx,1 mà thực hiện mov rbx, 2.

# JNE
<img width="1917" height="1078" alt="Screenshot 2026-09-07 183711" src="https://github.com/user-attachments/assets/721258b7-5332-48ba-8801-673b4bc6ce7a" />  
-Ngược lại với JE thôi, JNE thì nó sẽ nhảy nếu ko bằng nhau.    
-Mình vừa đổi thành cmp rax, 5. Lúc này thì ZF khác 0 nên nó không nhảy nữa.  
-Như đã thấy nó không nhảy mà thực hiện lệnh mov rbx, 1.    
-Và nếu si thêm nháy nữa thì nó vẫn thực hiện mov rbx, 2. Tức lúc này nó ko bỏ qua mov rbx, 1 nữa mà chạy từ trên xuống như thường.  

# JG (Jump if Greater)
<img width="1917" height="1078" alt="Screenshot 2026-09-07 203305" src="https://github.com/user-attachments/assets/472acd9f-5d26-43fb-a112-0ce65fce27e0" />
  <img width="1917" height="1075" alt="Screenshot 2026-09-07 203810" src="https://github.com/user-attachments/assets/b2f3f65f-164a-4dcb-a735-de9f6a319ba6" />

# CALL
<img width="1920" height="1080" alt="Screenshot (49)" src="https://github.com/user-attachments/assets/e0200346-9d50-4948-87d0-2cd0832678e3" />  
<img width="1917" height="1078" alt="Screenshot 2026-09-08 182504" src="https://github.com/user-attachments/assets/b6600fc2-9c59-4928-9bc0-d07054eb3021" />  
-Sau khi call thì RSP đã thay đổi, nó giảm đi 8 byte, đồng nghĩa stack cũng đã được mở rộng, call đã lưu return address vào stack  

<img width="1917" height="212" alt="Screenshot 2026-09-08 183153" src="https://github.com/user-attachments/assets/fce865aa-69ac-474c-8485-576ab85b7746" />
```
(gdb) x/gx $rsp
0x7fffffffe0c8: 0x000000000040100c
```
-Dòng code trên có thể đọc 0x7fffffffe0c8 là địa chỉ stack, 0x000000000040100c là dữ liệu tại đó: mov rbx, 0x14 
-Lệnh x/gx dùng để xem nội dung tại 1 địa chỉ bộ nhớ dưới dạng số nguyên hệ hex. x: examine, g:giant, x:hex 
