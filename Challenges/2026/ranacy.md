# Phân tich lỗi
<img width="987" height="656" alt="Screenshot 2026-11-18 182233" src="https://github.com/user-attachments/assets/507c9191-6d4b-489f-be3d-e904b9132f94" />
<img width="1025" height="255" alt="Screenshot 2026-11-18 181653" src="https://github.com/user-attachments/assets/9f63a932-fa4a-4214-9879-ae38a0f9c1b0" />

- Bài này chỉ có 1 lỗi buffer overflow cho mình nhập tràn 24 byte
- NX bật -> bỏ qua shellcode
- Canary -> Leak canary
- Mình kiểm tra ROPgadget trong file binary thì không có đủ gadget để gọi execve syscall, cả pop rdi và pop rsi cũng không có
- Mình xem tiếp trong libc thì may mắn là có đủ các gadget cần thiết để gọi execve syscall -> Mình sẽ leak libc 


# Khai thác 
- 4 lần nhập đầu mình đã dùng để leak canary và leak libc. Mình chỉ còn 1 lần gửi để gửi payload ghi đè rip, nhưng mình chỉ ghi tràn được 24 byte, như thế sẽ không đủ cho các gadget mình dùng -> mình cần nhảy tới 1 vùng rộng hơn ở lần payload cuối để nhét đủ gadget -> Mình sẽ gửi gadget lên đầu buf trước rồi ghi đè rip

<img width="1891" height="652" alt="Screenshot 2026-11-19 125055" src="https://github.com/user-attachments/assets/8b6c3c0f-7f73-49a1-8fa5-d14adb0a101e" />

- Do là read() nó sẽ nối chuỗi byte đến khi gặp null nên tận dụng lúc leak canary thì nó cũng nối cả canary lẫn địa chỉ phía dưới là saved-rbp, mình recv canary rồi recv luôn 8 byte tiếp theo để leak địa chỉ stack, trừ đi buf là ra được offset
<img width="508" height="852" alt="Screenshot 2026-11-19 125401" src="https://github.com/user-attachments/assets/3ab4398a-2b24-4742-b425-8c7651a17b77" />
