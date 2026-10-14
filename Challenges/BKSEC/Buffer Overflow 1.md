<img width="836" height="555" alt="Screenshot 2026-10-13 235137" src="https://github.com/user-attachments/assets/b3da89e8-1494-481b-ab4c-512649163d2d" />

-Buf được khai báo kích thước 50 byte nhưng fgets lại lấy đến 0x64 = 100 byte -> Đây là 1 bài buffer overflow cơ bản, chỉ cần làm tràn và ghi đè flag. 
-Ban đầu mình lấn cấn rằng flag được khai báo trước buf thì làm sao ghi đè flag được. Nhưng flag khi được khai báo trước thường sẽ nằm ở địa chỉ cao hơn. buf bắt đầu từ một địa chỉ thấp và khi tràn sẽ ghi đè từ thấp đến cao và ghi đè trực tiếp vào vùng nhớ biến flag.
-Chỉ cần tính offset từ buf đến flag và ghi đè thôi. 

<img width="876" height="313" alt="Screenshot 2026-10-14 180429" src="https://github.com/user-attachments/assets/39540e23-0be6-4944-a2b5-26d410e438b5" />

-Nhưng thay vì tính Offset, mình gửi chuỗi flag đó nhân lên 100 lần, nên từ đầu đến cuối đều là chuỗi flag đó mà không cần tính offset
