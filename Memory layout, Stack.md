## Stack
<img width="1152" height="582" alt="image" src="https://github.com/user-attachments/assets/2970c39a-2df2-41c9-b0f5-97fdca4e12b0" />
Stack nó là một vùng bộ nhớ trong RAM, chương trình dùng để lưu giữ tạm thời dữ liệu trong quá trình chạy.
Stack hoạt động theo nguyên tắc **LIFO(Last in, First out)**, dễ hiểu thì vào trước ra sau, vào cuối thì ra đầu.

**RSP** chứa một địa chỉ liên quan đến đỉnh của **Stack**
Ví dụ đơn giản:

RSP = 0x7000

Ta có thể hình dung:
Memory

0x6FF0
0x6FF8
0x7000  ← RSP
0x7008
...

RSP giúp CPU biết vị trí hiện tại của **top of stack**.

Stack không chỉ dùng để chứa một loại dữ liệu.
Nó thường được dùng để những thứ tạm thời liên quan đến việc thực thi chương trình.
    dữ liệu tạm thời
    thông tin liên quan đến function call
    local variables trong một số trường hợp
    return address
    ...

