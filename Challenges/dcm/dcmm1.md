<img width="637" height="301" alt="Screenshot 2026-11-18 135640" src="https://github.com/user-attachments/assets/df5d7b27-16c6-4d58-8b65-4c38ca8e06fa" />

- Bài này thấy ngay có lỗi Format String, điều kiện lấy flag cũng đơn giản, chỉ cần ghi đè biến key thành 0x1337 là được. Nhưng biến key thuộc vùng .bss nên mình phải lấy địa chỉ biến key trước.

<img width="697" height="60" alt="Screenshot 2026-11-18 135717" src="https://github.com/user-attachments/assets/983b29df-9d09-40ee-be58-f2da69e5babb" />

<img width="412" height="283" alt="Screenshot 2026-11-18 140913" src="https://github.com/user-attachments/assets/58c93d48-ea50-4629-990f-0e25e2f794d9" />

- Mình cứ chạy thử trước rồi chỉnh padding của %n cho nó trỏ vào đúng địa chỉ key 
