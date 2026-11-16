# Phân tích lỗi 
<img width="631" height="358" alt="Screenshot 2026-11-16 142254" src="https://github.com/user-attachments/assets/db335093-7e41-4051-8919-8d22455f6a09" />
<img width="577" height="635" alt="Screenshot 2026-11-16 142546" src="https://github.com/user-attachments/assets/0efeb9df-3986-485e-b81a-8f132b1c9a2d" />

- Bài này có 2 lỗi BOF, lần đầu chắc chắn dùng để leak canary
- Bài không có sẵn hàm win() hay hàm nào để in ra flag
- Ban đầu mình định dùng one_gadget nhưng PIE của libc lại bật nên cách này là không thể vì chỉ có 2 lần gửi payload

<img width="1296" height="422" alt="Screenshot 2026-11-16 144805" src="https://github.com/user-attachments/assets/adda57e5-3840-4cc0-a0ce-eedc258f72a6" />

- Gadget chỉ có pop rdi và pop rsi, cũng ko gọi syscall execve được -> rdi và rsi có vai trò là 2 tham số đầu, mình có thể thử dùng 1 hàm có sẵn trong binary -> system()

<img width="811" height="87" alt="Screenshot 2026-11-16 152111" src="https://github.com/user-attachments/assets/42368e92-2346-4e0d-97d4-d3b9c1f7f0de" />


- Mình cũng tìm thử hàm system() nhưng nó chỉ có libc system. Mình sẽ thử leak canary, sau đó return về main để có 2 lần read(). Lần 1 leak libc, lần 2 return về libc system với 2 gadget.
