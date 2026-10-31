# NX (No-Execute)
-NX đánh dấu các vùng nhớ dữ liệu như Stack hay Heap là **Non-Execute**, chỉ được write/read. Điều này ngăn việc bộ vi xử lý thực hiện các đoạn shellcode, mã độc được chèn vào vùng nhớ, tránh việc bên tấn công muốn thay đổi con trỏ RIP
-NX hoạt động dựa trên nguyên tắc phân quyền vùng nhớ, 1 memory page chỉ có thể sở hữu quyền Write hoặc Execute, ko thể sở hữu cả 2 cùng lúc  
-Do ko thể thực thi code tự chèn, mình có thể dùng các đoạn code có sẵn trong vùng nhớ có quyền Execute (thư viện `libc` hoặc phân đoạn `.text`)   
->Return-to-libc: Gọi trực tiếp các hàm có sẵn như system("/bin/sh").  
->ROP (Return-Oriented Programming): Tận dụng các đoạn mã ngắn kết thúc bằng lệnh ret (gọi là Gadget) ghép nối lại thành chuỗi lệnh hoàn chỉnh.  
# PIE (Position Independent Executable)
-PIE là cơ chế biên dịch tập tin thực thi dưới dạng **Mã độc lập vị trí (PIC)**. Khi chương trình khởi chạy, toàn bộ phân đoạn của tệp thực thi chính (`.text`, `.data`, `.got`) sẽ được nạp vào một địa chỉ gốc (Base Address) **NGẪU NHIÊN** trong bộ nhớ  
-Có 1 kỹ thuật bảo mật khác gọi là ASLR (Address Space Layout Randomization), dùng để chặn việc khai thác bộ nhớ bằng cách sắp xếp **ngẫu nhiên vị trí** các vùng dữ liệu chính trong bộ nhớ ảo. Nhưng chỉ có Stack, Heap, Shared Libs được ngẫu nhiên vị trí, còn file thực thi chính (`main binary`) vẫn nằm ở vị trí cố định  
-Do địa chỉ bị xáo trộn mỗi lần chạy, mình ko thể biết chính xác ROP Gadget hay hàm nào đang ở đâu   
->**Information Leak (Rò rỉ bộ nhớ):** Khai thác một lỗ hổng khác (như Format String hoặc Out-of-bounds Read) để đọc một địa chỉ bộ nhớ đang chạy 

# Canary 
-Cơ chế chống khai thác BOF bằng cách phát hiện các hành vi ghi đè dữ liệu trên vùng Stack trước khi hàm kết thúc  
-Khi một hàm bắt đầu thực thi, chương trình sẽ chèn một giá trị ngẫu nhiên bí mật vào Stack, nằm ngay trước saved-rbp và saved-rip. Vì thế khi BOF xảy ra thì dữ liệu sẽ tràn đến Canary trước return address.  
-Trước khi hàm kết thúc, chương trình sẽ so sánh giá trị được lưu trong vùng nhớ an toàn (Thread Local Storage - TLS)

<img width="1413" height="745" alt="Screenshot 2026-10-29 155706" src="https://github.com/user-attachments/assets/6b7f5084-e798-45ca-b9bc-a4f1004e1a60" />

### Ví dụ Canary trong 1 chương trình  
<img width="1381" height="637" alt="Screenshot 2026-10-29 163540" src="https://github.com/user-attachments/assets/34601874-91a4-4c4d-a33a-f1f008454c99" />


<img width="1327" height="588" alt="Screenshot 2026-10-29 164602" src="https://github.com/user-attachments/assets/5bd0278d-5b80-4661-9f3a-cb6c3e61ced2" />
-Canary value được giữ trong vùng TLS, thanh ghi fs trỏ tới vùng TLS, với độ lệch 0x28 byte (fs:[0x28]) là địa chỉ chứa canary value  
-Khi chạy chương trình này, giá trị canary từ ô nhớ fs:[0x28] sẽ được đẩy vào rax, sau đó giá trị đó sẽ được đưa lên stack nằm ở địa chỉ rbp-8, tức ngay trước saved-rbp  
**-ĐẶC ĐIỂM:** Byte đầu tiên (little endian) luôn là NULL BYTE, 7 byte còn lại là random và KHÁC NULL và nó sẽ nằm ngay trước rbp
