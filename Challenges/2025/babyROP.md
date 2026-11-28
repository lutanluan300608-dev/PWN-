# Phân tích lỗi

<img width="920" height="351" alt="Screenshot 2026-11-27 131133" src="https://github.com/user-attachments/assets/f7454047-c6f5-4af8-9596-a0918432aa8f" />
<img width="951" height="245" alt="Screenshot 2026-11-27 131221" src="https://github.com/user-attachments/assets/29810a8d-9820-4264-8c17-eee8376de8fb" />

- Ban đầu xem bài này mình chỉ biết được là có buffer overflow, dù PIE tắt nhưng lúc kiểm tra thì binary gadget không có đủ để gọi syscall execve

<img width="1217" height="256" alt="Screenshot 2026-11-28 002031" src="https://github.com/user-attachments/assets/f6340693-6e15-41e9-bba9-837c9526457b" />

- Bài này yêu cầu mình cần phải đào kỹ, đọc vào phần code assembly có lệnh mov rdi, rax. Nếu mình điều khiển được rax thì tức là mình cũng điều khiển được rdi.

<img width="1391" height="51" alt="Screenshot 2026-11-28 001647" src="https://github.com/user-attachments/assets/db59197f-05cd-4f25-be23-25e29c94c107" />

- Lúc này có gadget pop rax là mình thấy có thể khai thác được, nhưng vì có nhiều lệnh rác phía sau nên mình cần phải vào gdb xem nó làm gì
- Ý tưởng là mình sẽ đưa vào rax giá trị libc, rồi return về lệnh mov rdi, rax trước puts(). Lúc hàm puts() thực thi sẽ lấy chuỗi trong rdi để in ra và mình leak được libc, cuối cùng chỉ cần return về 1 hàm tạo.  

# Khai thác

<img width="952" height="812" alt="Screenshot 2026-11-27 215641" src="https://github.com/user-attachments/assets/be2a641c-7e10-4fd8-8e1a-f076ce7c9a53" />

- Mình chạy và thấy có 1 lệnh `add eax, 0x2ecb` sẽ tăng giá trị của rax lên dẫn đến địa chỉ libc đưa vào sẽ bị sai, mình cần trừ đi giá trị đó

<img width="642" height="295" alt="Screenshot 2026-11-28 162116" src="https://github.com/user-attachments/assets/947fba6a-9461-45e1-bbfc-33060b8663ba" />

- Do có hàm strlen() nên mình cần bypass bằng cách thêm null byte vào để ngắt.
- Mình cũng sẽ đưa rw-section vào saved-rbp để tránh việc giá trị không hợp lệ

<img width="423" height="182" alt="Screenshot 2026-11-28 162512" src="https://github.com/user-attachments/assets/6676f208-06f7-4888-8025-fd72a7a7e646" />
<img width="925" height="166" alt="Screenshot 2026-11-28 012224" src="https://github.com/user-attachments/assets/6b87470c-2aa3-48e9-b34d-7221f4019539" />

- Leak được libc rồi thì mình chỉ cần nhảy vào 1 hàm tạo shell. Mình sẽ chọn dùng one_gadget (hoặc có thể lấy các gadget tham số trong libc rồi nhảy vào hàm system()).

### Final Script

<img width="655" height="847" alt="Screenshot 2026-11-28 162907" src="https://github.com/user-attachments/assets/87bdab2c-a2fb-442a-95ee-0ad2fff2836b" />
