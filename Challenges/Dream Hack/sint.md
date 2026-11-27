<img width="685" height="400" alt="Screenshot 2026-11-27 094124" src="https://github.com/user-attachments/assets/185ff45a-fc3f-4eca-9a93-8785b29d7731" />
<img width="382" height="152" alt="Screenshot 2026-11-27 095735" src="https://github.com/user-attachments/assets/feec5e01-9511-486c-957d-0f94f3c542b8" />
<img width="335" height="47" alt="Screenshot 2026-11-27 095753" src="https://github.com/user-attachments/assets/1e364223-fe87-41df-8f11-2aeb59ce242f" />

- Bài này ban đầu mình nhìn không thấy được buffer overflow
- Bài cho nhập vào biến size, nếu lớn hơn 256 hoặc nhỏ hơn 0 thì sẽ exit. Rồi cho mình nhập vào biến buf số byte tương ứng size - 1.
- Nhưng có 1 lỗ hổng là mình có thể nhập vào số 0 và khi cmp thì chương trình vẫn cho qua. Khi read() size - 1 tức là 0 - 1 = -1. Mà khi truyền vào read(), -1 nó sẽ hiểu là 0xffffffff tức là 1 số rất lớn, lớn hơn cả kích thước buf dẫn đến buffer overflow. Từ đó mình có thể lấy địa chỉ get_shell() và ghi đè saved-eip (PIE đang tắt)
