# Phân tích chương trình
<img width="487" height="347" alt="Screenshot 2026-11-06 081823" src="https://github.com/user-attachments/assets/a63ec2bf-502a-417d-83e8-8994413a7fd1" />
<img width="602" height="191" alt="Screenshot 2026-11-05 163605" src="https://github.com/user-attachments/assets/308856f2-83d5-4aee-b165-5e5469ed4ce4" />

- Không có sẵn hàm gọi /bin/sh, trong hàm loop() cho phép mình read 2 lần
- NX bật -> ko thể shellcode
- Mình sẽ sử dụng ROPchain. Nhưng vì PIE đang bật, mình sẽ phải leak địa chỉ cùng nằm trong vùng `.text`

# Ý tưởng 
- Đầu tiên mình cần leak 1 địa chỉ trong vùng `.text` để tính được base address.
- Vì PIE đang bật nên địa chỉ của các tệp thực thi sẽ được đưa ngẫu nhiên vào 1 base address -> offset của gadget cũng sẽ ngẫu nhiên thay đổi theo binary base. Ví dụ ban đầu "pop rdi" cách binary base 0x1690, nhưng khi chạy, binary base tăng lên 0x5, thì lúc này offset "pop rdi" cũng tăng lên 0x5. Thế nên mình mới cần tính toán
- Nhưng offset là cố định. Mình sẽ lấy địa chỉ leak được trừ đi binary base để ra được base address. Có base address rồi thì mình cứ việc cộng thêm vào là ra chính xác offset của gadget

# Khai thác 
<img width="1253" height="878" alt="Screenshot 2026-11-06 090714" src="https://github.com/user-attachments/assets/5c68b0ee-d59f-4097-8c71-234e3351c112" />
<img width="1212" height="151" alt="Screenshot 2026-11-06 082105" src="https://github.com/user-attachments/assets/97972895-07e6-462d-8981-59a503e9c52a" />

- Mình chạy thử và tìm trên Stack và tìm thấy 1 địa chỉ hợp lệ. Các địa chỉ màu đỏ thường sẽ là địa chỉ trong vùng `.text`. Mình cần phải vmmap để check





