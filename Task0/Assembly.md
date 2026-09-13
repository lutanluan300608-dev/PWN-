<img width="1020" height="650" alt="Screenshot 2026-09-04 185529" src="https://github.com/user-attachments/assets/0b2c3832-3c3a-472e-8791-0beece893a77" />
Code được viết sẽ được biên dịch thông qua một ompiler, từ ngôn ngữ bậc cao thành các mã trung gian, sau đó được chuyển thành Machine Code và được đưa đến CPU      



# ASSEMBLY
**Assembly** là một ngôn ngữ lập trình bậc thấp, nó rất gần với ngôn ngữ máy tính, dùng các từ tiếng anh rút ngắn giúp con người dễ đọc và viết các lệnh mà CPU có thể thực thi. Và Assembly cũng có rất nhiều loại.  
-Để CPU hiểu được code thông qua assembly program cần đổi code file thành object file. Sau đó cần link các object file lại thành 1 file hoàn chỉnh(executeable) 
```
as -o file.o file.s
ld -o file file.o
```
-Hoặc có thể làm tắt 2 lệnh trên
```
gcc -o file file.s
```
-> gcc sẽ tự gọi as và ld
Note: GCC là 1 bộ trình biên dịch
-Một instruction Assembly thường có dạng
```
instruction destination, source
```
Ví dụ
```
mov rax, 10
mov rbx, 20
mov rdi, rax
mov rsi, [rbx]
add rbx, 5
....
```
# Các lệnh Assembly cơ bản
### MOV (MOVE)
-Công dụng: gán giá trị   
-Cú pháp:
```
instruction destination, source
```
Ví dụ
```
mov rax, 10 //ghi giá trị 10 vào rax
mov rax, rbx //sao chép giá trị rbx vào rax
mov rdi, rsp //sao chép địa chỉ rsp đang trỏ tới (top of stack) vào rdi
```
-Ngoài ra, lệnh mov còn có thể đọc dữ liệu từ bộ nhớ mà thanh ghi khác đang trỏ tới và lưu nó vào thanh ghi hiện tại  
Ví dụ
```
mov rdi, [rsp] //sao chép dữ liệu top of stack mà rsp trỏ tới vào rdi
mov rdi, [rax] //sao chép dữ liệu mà địa chỉ của thanh rax đang trỏ tới vào rdi
```
### ADD
-Công dụng: cộng 2 giá trị  
-Cú pháp: 
```
add destination, source
```
->destination + source
### SUB
-Công dụng: trừ 2 giá trị
-Cú pháp:
```
sub destination, source
```
->destination - source
### CMP (Compare)
-Công dụng: thực hiện logic (==, >, <, ...)
-Nó dùng phép trừ để cập nhật FLAGS  
```
mov rax, 10
cmp rax, 10
```
10 - 10 = 0 => ZF = 1
### JE (Jump if Equal) và JNE (Jump if Not Equal)
```
je label
jne label
```
-Nó kiểm tra   
+nếu ZF = 1 -> nhảy   
+nếu ZF = 0 -> nhảy

### JG (Jump if Greater)
-Đối với JE thì chỉ cần quan tâm đến ZF, bằng hoặc khác.    
-Còn JG thì phải xét đến các FLAGS liên quan đến signed integer, như SF(Sign Flag) và OF (Singed Overflow).  
+SF = 0 khi dương, SF = 1 khi âm  
+OF = 0 khi ko xảy ra Singed Overflow, OF = 1 khi xảy ra  
-Sau cmp, CPU không tự biết đâu là số dương hay âm, nó sẽ lưu bit. Ví dụ 00000101 là 5, 11111011 là -5, xét xem là signed hay unsigned.    
-Giả sử  
```
mov rax, 10
cmp rax, 5
jg greater
greater:
mov rbx, 2
```
-Lúc này   
+10 > 5 -> ZF = 0  
+5 là dương -> SF = 0  
+ko có signed overflow -> OF = 0  
=> VẬY **ZF = 0, SF = OF** -> nhảy   
### JL (Jump if Less)  
-Ngược lại với JG nhưng điều kiện là ZF =0, SF != OF 
-Giữ nguyên cmp rax, 5 thì nó sẽ ko nhảy, còn đổi sang một số lớn hơn rax thì sẽ nhảy (đang đề cập đến code trên phần JG)

### JMP  
-Khác với 4 lệnh JE, JNE, JG, JL. Gặp JMP là nhảy luôn, không xét điều kiện.  
=> **Conditional jump**: JE, JNE, JG, JL. **Unconditional jump**: JMP   
# GDB
 là một trình gỡ lỗi (debugger) cho phép tạm dừng chương trình và xem phía trong CPU đang làm gì  
<img width="1040" height="720" alt="image" src="https://github.com/user-attachments/assets/c50893a1-1348-407a-bab1-86f9957cab98" />
<img width="1132" height="510" alt="Screenshot 2026-09-05 201621" src="https://github.com/user-attachments/assets/1766edca-2c17-4cab-8562-768c626aded4" />

### CALL 
-Ví dụ:
```
call foo
```
-Như đã học thì Call dùng để gọi hàm, đồng thời sẽ đánh dấu 1 địa chỉ là return address để trở về 
```
foo:
ret
```
-Sau khi chạy xong CPU sẽ dùng return address để quay lại chỗ vừa gọi foo  

### SYSCALL - SYSTEM CALL
-Vì chương trình User-Space không được tự ý làm mọi thứ với phần cứng, nên lệnh syscall dùng để gọi kernel. Ví dụ như các thao tác in ra màn hình, mở file, cấp phát bộ nhớ, thoát chương trình,...  
Ví dụ:
```
mov rax, 60
mov rdi, 0
syscall
```
+rax = 60 -> chọn system call exit  
+rdi = 0 -> mã thoát 0  
+syscall -> chuyển quyền điều khiển vào kernel  
