# Phân tich lỗi
<img width="987" height="656" alt="Screenshot 2026-11-18 182233" src="https://github.com/user-attachments/assets/507c9191-6d4b-489f-be3d-e904b9132f94" />
<img width="1025" height="255" alt="Screenshot 2026-11-18 181653" src="https://github.com/user-attachments/assets/9f63a932-fa4a-4214-9879-ae38a0f9c1b0" />

- Bài này chỉ có 1 lỗi buffer overflow cho mình nhập tràn 24 byte
- NX bật -> bỏ qua shellcode
- Canary -> Leak canary
- Mình kiểm tra ROPgadget trong file binary thì không có đủ gadget để gọi execve syscall, cả pop rdi và pop rsi cũng không có
- Mình xem tiếp trong libc thì may mắn là có đủ các gadget cần thiết để gọi execve syscall -> Mình sẽ leak libc 
