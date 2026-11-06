# Phân tích chương trình
<img width="487" height="347" alt="Screenshot 2026-11-06 081823" src="https://github.com/user-attachments/assets/a63ec2bf-502a-417d-83e8-8994413a7fd1" />
<img width="602" height="191" alt="Screenshot 2026-11-05 163605" src="https://github.com/user-attachments/assets/308856f2-83d5-4aee-b165-5e5469ed4ce4" />

- Không có sẵn hàm gọi /bin/sh, trong hàm loop() cho phép mình read 2 lần
- NX bật -> ko thể shellcode
- Mình sẽ sử dụng ROPchain. Nhưng vì PIE đang bật, mình sẽ phải leak địa chỉ cùng nằm trong vùng `.text`

# Ý tưởng 
- Đầu tiên mình cần leak 1 địa chỉ trong vùng `.text` để tính được base address.
- Vì PIE đang bật nên địa chỉ của 

