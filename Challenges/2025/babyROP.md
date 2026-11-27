# Phân tích lỗi

<img width="920" height="351" alt="Screenshot 2026-11-27 131133" src="https://github.com/user-attachments/assets/f7454047-c6f5-4af8-9596-a0918432aa8f" />
<img width="951" height="245" alt="Screenshot 2026-11-27 131221" src="https://github.com/user-attachments/assets/29810a8d-9820-4264-8c17-eee8376de8fb" />

- Ban đầu xem bài này mình chỉ biết được là có buffer overflow, dù PIE tắt nhưng lúc kiểm tra thì binary gadget không có đủ để gọi syscall execve

<img width="1217" height="256" alt="Screenshot 2026-11-28 002031" src="https://github.com/user-attachments/assets/f6340693-6e15-41e9-bba9-837c9526457b" />

- Bài này yêu cầu mình cần phải đào kỹ, đọc vào phần code assembly có lệnh mov rdi, rax. Nếu mình điều khiển được rax thì tức là mình cũng điều khiển được rdi.

<img width="1391" height="51" alt="Screenshot 2026-11-28 001647" src="https://github.com/user-attachments/assets/db59197f-05cd-4f25-be23-25e29c94c107" />

- Lúc này có gadget pop rax là mình thấy có thể khai thác được, nhưng vì có nhiều lệnh rác phía sau nên mình cần phải vào gdb xem nó làm gì


# Khai thác
<img width="952" height="812" alt="Screenshot 2026-11-27 215641" src="https://github.com/user-attachments/assets/be2a641c-7e10-4fd8-8e1a-f076ce7c9a53" />



