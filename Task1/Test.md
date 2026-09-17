# BÀI 1: BUFFER OVERFLOW CƠ BẢN
-Mình đã cài Pwndbg trên Ubuntu theo Chat GPT
## Ví dụ 1: Nhìn binary bằng pwndbg 
<img width="1047" height="497" alt="Screenshot 2026-09-17 202132" src="https://github.com/user-attachments/assets/d3999acf-952c-4913-9573-d63709a8aa6b" />
-Hàm vuln có vùng đệm được cung cấp **'char buf[32]'** chỉ có 32 bytes, nhưng lệnh **'fread(buf, 1, 200, stdin)'** lại đọc dữ tối đa 200 bytes  
<img width="1917" height="1078" alt="Screenshot 2026-09-17 211100" src="https://github.com/user-attachments/assets/26c4e515-70cc-4e65-b434-0465e974f47b" />  
1. Checksec    
- Mình dùng **'checksec'** để kiểm tra cơ chế bảo mật (mitigations) bật hay tắt:     
   - **Stack:** No canary found -> khi BOF, ko cần phải vượt qua lớp bảo vệ Canary    
   - **NX:** NX enabled -> vùng Stack không được phép vừa ghi vừa thực thi code. Nên muốn chạy shellcode trên Stack, NX sẽ là vấn đề    
   - **PIE:** No PIE (0x400000) -> binary ko dùng PIE.Các địa chỉ code như: **0x401136** hay **0x401163** có thể giữ cố định giữa các lần chạy của binary, trong môi trường không có yếu tố khác làm thay đổi chúng     
   - **RELRO:** Partial RELRO -> GOT chưa được bảo vệ hoàn toàn.  
