# NX (No-Execute)
-NX đánh dấu các vùng nhớ dữ liệu như Stack hay Heap là **Non-Execute**, chỉ được write/read. Điều này ngăn việc bộ vi xử lý thực hiện các đoạn shellcode, mã độc được chèn vào vùng nhớ, tránh việc bên tấn công muốn thay đổi con trỏ RIP
-NX hoạt động dựa trên nguyên tắc phân quyền vùng nhớ, 1 memory page chỉ có thể sở hữu quyền Write hoặc Execute, ko thể sở hữu cả 2 cùng lúc  
-Do ko thể thực thi code tự chèn, mình có thể dùng các đoạn code có sẵn trong vùng nhớ có quyền Execute (thư viện `libc` hoặc phân đoạn `.text`)   
->Return-to-libc: Gọi trực tiếp các hàm có sẵn như system("/bin/sh").  
->ROP (Return-Oriented Programming): Tận dụng các đoạn mã ngắn kết thúc bằng lệnh ret (gọi là Gadget) ghép nối lại thành chuỗi lệnh hoàn chỉnh.  
# PIE (Position Independent Executable)
-PIE là cơ chế biên dịch tập tin thực thi dưới dạng **Mã độc lập vị trí (PIC)**. Khi chương trình khởi chạy, toàn bộ phân đoạn của tệp thực thi chính (`.text`, `.data`, `.got`) sẽ được nạp vào một địa chỉ gốc (Base Address) **Ngẫu nhiên** trong bộ nhớ  
-Có 1 kỹ thuật bảo mật khác gọi là ASLR (Address Space Layout Randomization), dùng để chặn việc khai thác bộ nhớ bằng cách sắp xếp **ngẫu nhiên vị trí** các vùng dữ liệu chính trong bộ nhớ ảo. Nhưng chỉ có Stack, Heap, Shared Libs được ngẫu nhiên vị trí, còn file thực thi chính (`main binary`) vẫn nằm ở vị trí cố định  
-Do địa chỉ bị xáo trộn mỗi lần chạy, mình ko thể biết chính xác ROP Gadget hay hàm nào đang ở đâu   
->**Information Leak (Rò rỉ bộ nhớ):** Khai thác một lỗ hổng khác (như Format String hoặc Out-of-bounds Read) để đọc một địa chỉ bộ nhớ đang chạy 

# Canary 
-Cơ chế chống khai thác BOF bằng cách phát hiện các hành vi ghi đè dữ liệu trên vùng Stack trước khi hàm kết thúc  
-Khi một hàm bắt đầu thực thi, chương trình sẽ chèn một giá trị ngẫu nhiên bí mật vào Stack, nằm ngay trước Saved-RBP và Return address. Vì thế khi BOF xảy ra thì dữ liệu sẽ tràn đến Canary trước return address.  
-Trước khi hàm kết thúc, chương trình sẽ so sánh giá trị được lưu trong vùng nhớ an toàn 
