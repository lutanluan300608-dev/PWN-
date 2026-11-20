# Phân tích lỗi
<img width="722" height="390" alt="Screenshot 2026-11-20 170843" src="https://github.com/user-attachments/assets/17743dcd-56ce-42f6-b422-3310d5c69529" />
<img width="760" height="241" alt="Screenshot 2026-11-20 155457" src="https://github.com/user-attachments/assets/21073fc7-97bb-457f-a66e-2bc399244f5a" />

- Bài này gặp lỗi Format String và cho phép mình nhập nhiều lần
- Chỉ cần ghi đè biến changeme thành 1337 là mình sẽ có được shell, nhưng PIE đang bật nên mình cần phải leak 1 địa chỉ binary và tính base để lấy được địa chỉ chính xác của changeme
- Có điều trước khi chạy, mình cần build và run docker, sau đó dùng docker ps để xem docker ID và tìm file libc.so.6 vì khi chạy trên local và server thì binary có thể sẽ khác.

# Khai thác
<img width="762" height="792" alt="Screenshot 2026-11-20 172419" src="https://github.com/user-attachments/assets/e77ce828-3f54-46b8-8e9c-cfd4f7d66c36" />
- Do là lúc gửi thì địa chỉ changeme bị nằm lệch đi (không nằm trên 1 hàng) 6 byte nên mình thêm 6 byte rác vào để địa chỉ changeme nằm đúng vị trí hợp lệ
