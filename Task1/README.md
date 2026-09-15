# BUFFER OVERFLOW
-**Buffer overflow** (Tràn bộ đệm) là một lỗi lập trình, xảy ra khi một **chương trình cố gắng lưu trữ lượng dữ liệu vượt quá dung lượng** cho phép của **vùng nhớ đệm** (buffer, là vùng nhớ được cấp phát tạm thời để chứa dữ liệu)  
-Nếu dữ liệu bị tràn, nó sẽ ghi đè lên lên các vùng nhớ liền kề, cụ thể là các ô nhớ địa chỉ byte tiếp theo 
1. Trên Stack (Stack Overflow - Tràn ngăn xếp)   
   +Thường xảy ra khi một hàm tự gọi chính nó liên tục mà không có điểm dừng, mỗi lần gọi sẽ thêm một vùng stack frame mới vào      
   +Hoặc hàm gọi chồng nhau quá sâu(Hàm A gọi B, B gọi C, C gọi D,...) vượt quá giới hạn độ sâu của Stack      
   +Hoặc khai báo biến cục bộ hay mảng có kích thước quá lớn vượt quá giới hạn ngăn xếp của luồng
   =>Phần lớn khi tràn dữ liệu sẽ khiến chương trình crash ngay lập tức.
         Địa chỉ bộ nhớ không hợp lệ: Máy tính cố đọc dữ liệu bị đè nhưng nhận ra đó không phải là một địa chỉ phân vùng được phép truy cập. Hệ điều hành sẽ can             thiệp và đưa ra lỗi Segmentation Fault (Core Dumped) để bảo vệ hệ thống.
         Địa chỉ trả về của hàm (Return Address): Khi hàm chạy xong, nó lấy dữ liệu rác (ví dụ chuỗi AAAA) làm địa chỉ để nhảy tiếp. Vì AAAA không phải là địa chỉ            của lệnh nào cả, chương trình sẽ "chết đứng" (Crash).
   +Việc nhập ký tự vượt quá kích thước mảng cũng có thể gây ra Stack-based Overflow(Tràn bộ đệm ngăn xếp)   
3. Trên Heap
   Vùng nhớ Heap là nơi chứa các dữ liệu được cấp phát động
   Nếu làm tràn một đối tượng hoặc một mảng trên Heap, dữ liệu sẽ đè lên:
   Siêu dữ liệu của bộ quản lý (Chunk Metadata): Hệ điều hành dùng các ô nhớ nhỏ nằm ngay trước hoặc sau mỗi vùng cấp phát để lưu thông tin như: **Kích thước vùng nhớ này là bao nhiêu? Nó đang trống hay đã dùng?** Đè lên phần này sẽ làm hỏng trình quản lý bộ nhớ (malloc chunks), gây sập chương trình khi gọi lệnh giải phóng bộ nhớ (free()).
   Các đối tượng/biến động khác: Các biến được tạo ra ngay sau đó. Nếu vùng nhớ liền kề chứa một "con trỏ hàm" (function pointer), việc ghi đè có thể thay đổi hàm mà chương trình sẽ gọi tiếp theo, dẫn đến chiếm quyền điều khiển.
   
