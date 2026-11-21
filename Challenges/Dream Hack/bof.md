<img width="653" height="288" alt="Screenshot 2026-11-21 172834" src="https://github.com/user-attachments/assets/4d8bc9a2-ce85-4603-a4ae-83bacec893c6" />

<img width="556" height="247" alt="Screenshot 2026-11-21 173338" src="https://github.com/user-attachments/assets/8c567eac-2a86-4dd6-8ff0-ef47afe913ee" />

- Bài này mình cần chú ý ở hàm read_cat(), nó sẽ đọc file cat có sẵn và lưu vào biến v5, nếu mình ghi đè chạm đến ./cat trên stack thì nó sẽ nối luôn với chuỗi dữ liệu của cat(v5) và in ra ngoài.
- Biến v5 nằm ngay sau v4, mình có thể ghi tràn qua biến v5, hay nói cách khác mình có thể ghi đè file cat thành flag để khi read_cat() nó sẽ đọc file flag thay vì file cat, và với việc nối chuỗi của %s thì mình có thể in ra luôn flag.

<img width="396" height="172" alt="Screenshot 2026-11-21 173245" src="https://github.com/user-attachments/assets/8d2d3612-4310-41da-b928-93e8d8f8f1dd" />
