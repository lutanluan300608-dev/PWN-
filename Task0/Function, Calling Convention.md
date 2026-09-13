# Function(hàm)   
Giả sử chương trình có 1 việc phải làm nhiều lần, thay vì viết lại taonf bộ code thì ta gom nó lại thành 1 Function và gọi khi cần.  
Ví dụ mình có 1 hàm để thực hiện phép cộng, chỉ cần gọi hàm và đưa dữ liệu cho nó là nó sẽ thực hiện.  

# Argument (đối số)
Là giá trị được truyền vào Function khi ta gọi Function đó.  
Còn **Parameter (tham số)** là tên biến được function dùng để nhận argument.
VD:
```
int add(int a, int b) {
return a+b;
}
```
a và b là Parameter
giá trị được truyền vào là Argument   

# Calling Convention = quy tắc để các function giao tiếp với nhau.
CPU cần biết một cách thống nhất:  
  
  dữ liệu đưa vào function ở đâu?  
  function trả kết quả ở đâu?  
  khi function kết thúc thì quay lại đâu?  

**Thứ tự các Argument truyền vào Register theo Calling Convention:**  
arg1 → RDI  
arg2 → RSI  
arg3 → RDX  
arg4 → RCX  
arg5 → R8  
arg6 → R9  

**Và nếu có từ Argument thứ 7 trở đi, chúng sẽ KHÔNG còn truyền qua Register và sẽ truyền qua STACK**  
Khi arg7 được truyền vào stack, nó sẽ ở vị trí RSP + 8.  
arg8 thì RSP + 16, arg9 thì RSP + 24,......  

**Return value**, giá trị sau cùng sẽ được trả về RAX.

## Lệnh CALL
Call dùng để gọi Function và ghi nhớ nơi CPU cần quay lại sau khi Function kết thúc.  
Để mà CPU biết được sau khi call function cần quay lại instruction nào thì: **call** sẽ lưu **return address** lên Stack.  
Giả sử:  
  
RIP = 0x401100  
RSP = 0x8000  
  
Tại 0x401100 có instruction:  
  
call foo  

Sau khi call được thực hiện, CPU cần nhớ địa chỉ của instruction ngay sau call.  

Giả sử instruction tiếp theo ở:  
  
0x401105

thì 0x401105 chính là return address.  
**=>call đã thay đổi RIP để chạy function được gọi.**  

## Lệnh RET
Ret dùng để lấy **return address** từ Stack và đưa nó vào RIP để CPU quay lại chỗ đã gọi Function.  
call → lưu nơi quay về + chuyển sang function  
ret  → lấy nơi quay về + quay lại  

# CALLER VÀ CALLEE
-Caller là hàm gọi, Callee là hàm được gọi
Ví dụ
```
main:
    mov rax, 19
    call foo
```
+main là hàm gọi  
+foo là hàm được gọi  
**Caller và Callee đều phải tuân theo Calling Convention**
### CALLER-SAVED
-Caller phải tự chịu trách nhiệm nếu muốn giữ giá trị  
Các thanh ghi:
```
RAX   -> lưu giá trị trả về của hàm 
RCX
RDX
RSI
R8 - R11
```
*R10 và R11 là thanh ghi tạm thời  
Ví dụ
```
main:
    mov rax, 7
    call foo
```
->Thì foo có quyền thay đổi RAX  
-Caller phải tự push vào Stack trước khi gọi hàm khác nếu còn muốn dùng tiếp.
### CALLEE-SAVED
Các thanh ghi:
```
RBX
RBP
RSP
R12
R13
R14
R15
```
Nếu callee muốn thay đổi những thanh ghi này, callee phải khôi phục giá trị cũ trước khi ret, ngắn gọn là push để sao lưu và pop để khôi phục trước khi ret  
Ví dụ:
```
foo:
  push rbp
  mov rbp, rsp
  sub rsp, 16

  pop rbp

  leave 
  ret
```

