# Phân tích 
<img width="843" height="290" alt="Screenshot 2026-11-03 161515" src="https://github.com/user-attachments/assets/48274345-17d1-4237-9e22-937ed0fd014a" />
<img width="643" height="283" alt="Screenshot 2026-11-03 173829" src="https://github.com/user-attachments/assets/3580eb2b-225c-424f-a56f-d4c0767c6534" />
<img width="941" height="191" alt="Screenshot 2026-11-03 155910" src="https://github.com/user-attachments/assets/de18df5d-2007-4370-97c4-cfea91c3a37d" />

-Trong chall này có hàm banned_execve() chặn việc gọi hàm execve() bằng syscall. Nên trong bài này, mình sẽ dùng 1 kỹ thuật gọi là ORW, tức mình sẽ mở đường dẫn file chứa flag, đọc và ghi flag ra     

<img width="452" height="31" alt="Screenshot 2026-11-03 211715" src="https://github.com/user-attachments/assets/ea6c19e2-f306-48b2-b55f-ec2381e7623b" />

-Do trong bài này, return lại nhảy thẳng về buf nên đơn giản mình chỉ cần viết shellcode rồi gửi, rồi cho return nhảy về  

<img width="895" height="386" alt="Screenshot 2026-11-03 160248" src="https://github.com/user-attachments/assets/26c41cc5-b5f1-4289-9bfa-74255f606ea9" />

-Mình chuyển đường dẫn chứa flag thành dạng int để đưa lên Stack  

<img width="525" height="167" alt="Screenshot 2026-11-03 211244" src="https://github.com/user-attachments/assets/f957cf0a-d7f6-46ef-917e-2abef7d8f319" />

-Mình nhận thấy rằng khi đến return, thanh ghi rdx lại đang trỏ tới địa chỉ của buffer (nhận biết bằng việc lúc chạy mình đã nhập vào "c\n"). Mình sẽ muốn giữ lại giá trị này để khi read và write mình sẽ đưa nó vào rsi để chỉ định đọc và in từ chỗ địa chỉ buffer  
-Mình lưu địa chỉ buffer vào thanh ghi r13, vì trong suốt quá trình chạy chương trình, nó không thay đổi giá trị  
