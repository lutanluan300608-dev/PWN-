# BUFFER OVERFLOW
-**Buffer overflow** (Tràn bộ đệm) là một lỗi lập trình, xảy ra khi một **chương trình cố gắng lưu trữ lượng dữ liệu vượt quá dung lượng** cho phép của **vùng nhớ đệm** (buffer, là vùng nhớ được cấp phát tạm thời để chứa dữ liệu)  
-Nếu dữ liệu bị tràn, nó sẽ ghi đè lên lên các vùng nhớ liền kề, cụ thể là các ô nhớ địa chỉ byte tiếp theo 
1. Trên Stack (Stack Overflow)
   Thường xảy ra khi một hàm tự gọi chính nó liên tục mà không có điểm dừng, mỗi lần gọi sẽ thêm một vùng stack frame mới vào  
   Hoặc khai báo biến cục bộ hay mảng có kích thước quá lớn vượt quá giới hạn ngăn xếp của luồng
   ->Chương trình sẽ bị crash 

