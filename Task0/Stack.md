# Stack
<img width="1152" height="582" alt="image" src="https://github.com/user-attachments/assets/2970c39a-2df2-41c9-b0f5-97fdca4e12b0" />  
  
Stack nó là một vùng bộ nhớ trong RAM, chương trình dùng để lưu giữ tạm thời dữ liệu trong quá trình chạy.  
Stack hoạt động theo nguyên tắc **LIFO(Last in, First out)**, dễ hiểu thì vào trước ra sau, vào cuối thì ra đầu.  
  
**RSP** chứa một địa chỉ liên quan đến đỉnh của **Stack**    
Ví dụ đơn giản:  
Ta có:  
  ```
             CPU  
        ┌─────────────┐  
        │ RSP         │  
        │ = 0x7FF8    │  
        └──────┬──────┘  
               │  
               │ địa chỉ  
               ▼  
            Memory  
        ┌─────────────┐  
        │ Stack       │  
        │             │  
        │ 0x7FF8      │ ← vị trí hiện tại  
        │ 0x7FF0      │  
        │ 0x7FE8      │  
        └─────────────┘
```
**RSP chứa địa chỉ để CPU biết vị trí hiện tại của đỉnh Stack.**  
  
Stack không chỉ dùng để chứa một loại dữ liệu.  
Nó thường được dùng để những thứ tạm thời liên quan đến việc thực thi chương trình.  
    dữ liệu tạm thời  
    thông tin liên quan đến function call  
    local variables trong một số trường hợp  
    return address    
    ...  
    
    
### Stack thực sự nằm ở đâu trong Memory?  
  
-Stack không có địa chỉ cố định duy nhất.  
Khi một chương trình chạy, hđh dành cho nó 1 vùng địa chỉ bộ nhớ để làm stack.  
Ta có thể hình dung đơn giản:  

Memory  

0x8000  
0x7FF8  
0x7FF0  
0x7FE8  
0x7FE0  
...  
  
Một phần trong vùng này được sử dụng làm Stack.  
Ví dụ:  
  ```
Memory  
┌───────────────┐  
│               │    
│     Stack     │  
│               │
│   0x7FF8      │  
│   0x7FF0      │  
│   0x7FE8      │  
│      ...      │  
└───────────────┘  

```
### Stack lớn lên như thế nào?  

-Stack sẽ thường phát triển về phía **địa chỉ thấp hơn**.  
Khi Stack cần thêm không gian, nó phát triển xuống:  
```
Địa chỉ cao  
    │  
    │  
 0x8000  ← RSP  
    │  
    │  
 0x7FF8  
    │    
 0x7FF0  
    │  
    ▼  
Địa chỉ thấp  
  
Stack sau khi mở rộng:  
0x8000  
  ↓  
0x7FF8  
  ↓  
0x7FF0  
  ↓  
0x7FE8  
  ```
Ngược lại, khi thu hẹp nó sẽ thu hẹp dần lên địa chỉ cao hơn.  

  
# Push và Pop
### Push

**Push** là một **instruction** của x86-64 để **đưa một giá trị vào Stack**.  
<img width="1013" height="142" alt="Screenshot 2026-08-29 171053" src="https://github.com/user-attachments/assets/156f546b-f5db-42bf-ab67-9b7c69bfa058" />  
Lúc này sẽ lấy giá trị của RAX đặt lên Stack.  
**Quá trình Push:**  
**+Trước Push**  
```
RAX = 42  
RSP = 0x7000  
  
Memory:      

0x7000 → ...  <-Top of stack  
0x6FF8 → ...  
0x6FF0 → ...
  ```
**+Sau Push**  
```
RAX = 42    
RSP = 0x6FF8  
  
Memory:  
  
0x7000 → ...  
0x6FF8 → 42   ← TOP  
0x6FF0 → ...
```
**Sau khi Push thì RSP sẽ giảm, giá trị được ghi vào vị trí mới.**  
**Vì RAX là 64-bit = 8 byte nên RSPsau = RSPtrước - 8**  
VD: 0x7000 - 8 = 0x6FF8  
=>> Push đã làm Stack lớn thêm.  
-Giả sử mình có một RAX = 0x12345678ABCDEF00, nó tách giá trị ra từng byte.  
Khi tách ra được 8 byte: 12 34 56 78 AB CD EF 00. Nó sẽ được ghi vào 8 địa chỉ trong Memory sau khi Push.  
Lưu ý x86-64 dùng Little-Endian nên sẽ xếp ngược lại.  


### Pop
  
**Pop** thì ngược lại với **Push** thôi, nó lấy giá trị đỉnh của Stack ra.  
Sau khi **Pop** thì RSP cũng sẽ thay đổi. Pop xong thì RSP sẽ tăng lên.  
  **RSPsau = RSPtrước + 8**  

# Stack Frame 
-Khi một function được gọi thì nó cần 1 vùng trên stack để chứa dữ liệu chính function đó. Vùng đó gọi là **Stack Frame**  
Stack  
Ví dụ frame 1 hàm
```
Địa chỉ thấp
        │
        │
        ├─────────────────┐
        │ local variable  │ 
        ├─────────────────┤
        │ local variable  │ 
RBP →   ├─────────────────┤
        │ saved RBP       │← push rbp
        ├─────────────────┤
        │ return address  │← được call tạo
RSP →   └─────────────────┘
        │
        ▼
Địa chỉ cao
```   
-Một function truyền thống thường có dạng  
```
push rbp    ->lưu base pointer cũ của hàm cha vào stack
mov  rbp, rsp      ->tạo base pointer mới của hàm con

...code trong hàm

mov  rsp, rbp    ->đưa rsp quay lại vị trí rbp, giải phóng vùng nhớ đc cấp
pop  rbp    ->lấy rbp hàm cha ra và nạp lại vào thanh ghi rbp để trở về
ret 
```
Phần đầu gọi là function prologue  
  
Phần cuối gọi là function epilogue  
  
Lưu ý: không phải mọi function hiện đại đều bắt buộc có push rbp / mov rbp, rsp. Compiler có thể tối ưu và bỏ frame pointer  

### Cách 1 stack frame được hình thành
-Khi 1 hàm được gọi, nó sẽ được cấp 1 stack frame mới để chứa dữ liệu (các biến cục bộ,...), như thế sẽ tránh việc ghi đè frame của hàm trước đó, thêm vào đó nữa là do độ rộng vùng nhớ mỗi hàm cần là khác nhau.  
-Khi 1 hàm foo được gọi,   
đầu tiên nó sẽ đẩy địa chỉ tiếp theo (saved-rip) lên stack, rồi cập nhật rip là lệnh đầu tiên trong foo  
tiếp theo foo sẽ thiết lập Stack frame mới bằng cách lưu rbp_main bằng push rbp  
lúc này stack frame sẽ có dạng:  
```
----------- high address  
main frame  
-----------   
saved_rip (main command after call foo())  
-----------  
saved_rbp (rbp_main)  
-----------   
```

mov rbp, rsp để lấy rbp mới là rbp_foo, lúc này rsp và rbp đang trỏ về cùng 1 chỗ
tiếp theo rsp sẽ bị trừ đi 1 khoảng dựa vào kích thước   
```
----------- high address
main frame
----------- 
saved_rip (main command after call foo())
-----------
saved_rbp (rbp_main) <-- rbp_foo
-----------
foo frame
----------- <- rsp_foo
```
khi khôi phục,  
mov rsp, rbp lúc này rsp_foo sẽ trỏ về cùng vị trí với rbp_foo  
```
----------- high address
main frame
----------- 
saved_rip (main command after call foo())
-----------
saved_rbp (rbp_main) <-- rbp_foo  <- rsp_foo
-----------
foo frame
-----------
```
pop rbp để lấy main_rbp vào thanh ghi rbp khôi phục lại frame của main  
```
pop rbp để lấy main_rbp vào thanh ghi rbp khôi phục lại frame của main  
----------- high address
...
----------- <- rbp_main
main frame
----------- 
saved_rip (main command after call foo())  <-- rsp_main
-----------
saved_rbp (rbp_main)
-----------
foo frame
-----------
```
