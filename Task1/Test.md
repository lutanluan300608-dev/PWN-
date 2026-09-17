# BÀI 1: BUFFER OVERFLOW CƠ BẢN
-Mình đã cài Pwndbg trên Ubuntu theo Chat GPT
## Ví dụ 1: Nhìn binary bằng pwndbg 
<img width="1047" height="497" alt="Screenshot 2026-09-17 202132" src="https://github.com/user-attachments/assets/d3999acf-952c-4913-9573-d63709a8aa6b" />
-Hàm vuln có vùng đệm được cung cấp **'char buf[32]'** chỉ có 32 bytes, nhưng lệnh **'fread(buf, 1, 200, stdin)'** lại đọc dữ tối đa 200 bytes  
<img width="1917" height="1078" alt="Screenshot 2026-09-17 211100" src="https://github.com/user-attachments/assets/26c4e515-70cc-4e65-b434-0465e974f47b" />   
