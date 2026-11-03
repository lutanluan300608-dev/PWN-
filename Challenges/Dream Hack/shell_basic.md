# Phân tích 
<img width="843" height="290" alt="Screenshot 2026-11-03 161515" src="https://github.com/user-attachments/assets/48274345-17d1-4237-9e22-937ed0fd014a" />
<img width="643" height="283" alt="Screenshot 2026-11-03 173829" src="https://github.com/user-attachments/assets/3580eb2b-225c-424f-a56f-d4c0767c6534" />
-Trong chall này có hàm banned_execve() chặn việc gọi hàm execve() bằng syscall. Nên trong bài này, mình sẽ dùng 1 kỹ thuật gọi là ORW, tức mình sẽ mở đường dẫn file chứa flag, đọc và ghi flag ra thong qua FD
