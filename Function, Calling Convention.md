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
**Argument đầu tiên của Function sẽ được truyền qua thanh ghi RDI**  
  
# Calling convention = quy tắc để các function giao tiếp với nhau.
CPU cần biết một cách thống nhất:  
  
  dữ liệu đưa vào function ở đâu?  
  function trả kết quả ở đâu?  
  khi function kết thúc thì quay lại đâu?  
