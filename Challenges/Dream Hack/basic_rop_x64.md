Source: https://dreamhack.io/wargame/challenges/29
# Phân tích lỗi
<img width="672" height="243" alt="Screenshot 2026-11-18 110236" src="https://github.com/user-attachments/assets/3b6f5c2e-9340-4836-ad6a-1c13147711d9" />
<img width="732" height="221" alt="Screenshot 2026-11-18 110259" src="https://github.com/user-attachments/assets/cdd54497-2192-40fc-8433-b6dc133cbd0a" />

- Bài này chỉ có 1 lần read duy nhất, không có hàm khác tạo shell hay đọc flag trong chương trình, NX bật nên mình sẽ dùng ROPchain.
<img width="1217" height="180" alt="Screenshot 2026-11-18 110515" src="https://github.com/user-attachments/assets/7ca934ef-cc36-4e35-9e8d-24bcbf32b71f" />

- Nhưng gadget không có đủ để thực hiện syscall execve, giờ mình chỉ có 2 gadget pop rdi và pop rsi. Mình có thể dùng 2 gadget này chỉ định 2 tham số cho 1 hàm nào đó. Mình sẽ thử hàm system()

<img width="755" height="67" alt="Screenshot 2026-11-18 110749" src="https://github.com/user-attachments/assets/1b869cbc-cf66-41a9-9ba0-f8020f477261" />

- Mình tìm thì chỉ có hàm system() của Libc, nhưng để dùng được thì phải leak được địa chỉ libc và tính libc base.
- Vì chỉ có 1 lần read() nên mình phải dùng cách khác để leak địa chỉ. Mình sẽ tận dụng hàm write() để in ra địa chỉ libc

<img width="673" height="932" alt="Screenshot 2026-11-21 083437" src="https://github.com/user-attachments/assets/83b2c741-dc6e-4480-bf1c-27dbc01fe51f" />
