# Phân tích lỗi 
<img width="477" height="198" alt="Screenshot 2026-11-08 132906" src="https://github.com/user-attachments/assets/56dbf8fc-bd9b-400d-98cb-b6204a1aefae" />

<img width="808" height="217" alt="Screenshot 2026-11-08 133422" src="https://github.com/user-attachments/assets/9995ad13-91c4-4cf6-8079-3b5bd7fd5cea" />
<img width="681" height="115" alt="Screenshot 2026-11-08 135212" src="https://github.com/user-attachments/assets/46c2e629-27cf-4149-9f64-848068987257" />

- Bài này chỉ cho phép ghi tràn 2 byte, mình chỉ ghi đè được 2 byte đầu của rbp trong bài này.
- Bài này có sẵn hàm Win() in ra flag cho mình

# Ý tưởng
- Mình sẽ leak saved-rbp trong stack ra, có được saved rbp rồi thì mình có thể tự do di chuyển trong stack.
- Lần read() đầu tiên: mình sẽ dùng để leak saved-rbp.
- PIE cũng tắt nên ở lần read() thứ 2: Có địa chỉ saved-rbp rồi, mình tính offset đầu buffer để di chuyển ngược lên. Mình sẽ đặt địa chỉ hàm Win() ở đầu buffer, sau đó ghi đè địa chỉ đầu buffer vào hàm saved-rbp -> lúc này saved-rbp sẽ là địa chỉ đầu buffer (là trỏ tới địa chỉ Win()), đến lần leave thứ 2 ở hàm main() thì mình sẽ nhảy vào được Win()

# Khai thác 
<img width="545" height="195" alt="Screenshot 2026-11-08 141824" src="https://github.com/user-attachments/assets/a73005c1-6e86-4f44-96cb-a0c6562f923f" />

<img width="422" height="50" alt="Screenshot 2026-11-08 142100" src="https://github.com/user-attachments/assets/0106295f-7bbc-43ee-94b1-0ed19a50eeda" />
<img width="593" height="112" alt="Screenshot 2026-11-08 142036" src="https://github.com/user-attachments/assets/a6a19a93-d65e-45a8-b93e-55368b9d2c14" />
<img width="513" height="47" alt="Screenshot 2026-11-08 142220" src="https://github.com/user-attachments/assets/0a751e16-e70a-4b34-b648-a7f5baec5b70" />

- Thành công Leak saved-rbp và tính offset đầu buffer

<img width="790" height="362" alt="Screenshot 2026-11-08 143529" src="https://github.com/user-attachments/assets/0fa7ce4c-e6ca-4512-8e5c-632551fa0c6a" />

- Mình tìm vị trí của đầu buffer bằng cách lấy saved-rbp trừ đi offset.
- Nhưng khi chạy thì mình bị lỗi SIGSEGV. Mình mới nhớ ra là khi leave thì thực thì mov rsp, rbp và pop rbp. Nó sẽ khiến cho ret đọc và nhảy vào địa chỉ [RBP+8] (RBP đang chứa saved rbp bị ghi đè)
- Nên mình sẽ dời địa chỉ Win() xuống 8 byte để ret đọc và nhảy đúng vị trí


<img width="757" height="385" alt="Screenshot 2026-11-08 144157" src="https://github.com/user-attachments/assets/d5f6a37f-7851-4bd9-bdf1-2ae4bb0e0b88" />

 
