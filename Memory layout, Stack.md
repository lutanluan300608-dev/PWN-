# Stack
<img width="1152" height="582" alt="image" src="https://github.com/user-attachments/assets/2970c39a-2df2-41c9-b0f5-97fdca4e12b0" />
```
Stack nó là một vùng bộ nhớ trong RAM, chương trình dùng để lưu giữ tạm thời dữ liệu trong quá trình chạy.
Stack hoạt động theo nguyên tắc **LIFO(Last in, First out)**, dễ hiểu thì vào trước ra sau, vào cuối thì ra đầu.

**RSP** chứa một địa chỉ liên quan đến đỉnh của **Stack**
Ví dụ đơn giản:
Ta có:

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
**RSP chứa địa chỉ để CPU biết vị trí hiện tại của đỉnh Stack.**

Stack không chỉ dùng để chứa một loại dữ liệu.
Nó thường được dùng để những thứ tạm thời liên quan đến việc thực thi chương trình.
    dữ liệu tạm thời
    thông tin liên quan đến function call
    local variables trong một số trường hợp
    return address
    ...
```

### Stack thực sự nằm ở đâu trong Memory?
```
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
```
-Stack sẽ thường phát triển về phía **địa chỉ thấp hơn**.
Khi Stack cần thêm không gian, nó phát triển xuống:
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

Ngược lại, khi thu hẹp nó sẽ thu hẹp dần lên địa chỉ cao hơn.
```

# Push và Pop
### Push
**Push** là một **instruction** của x86-64 để **đưa một giá trị vào Stack**.
<img width="1013" height="142" alt="Screenshot 2026-08-29 171053" src="https://github.com/user-attachments/assets/156f546b-f5db-42bf-ab67-9b7c69bfa058" />
Lúc này sẽ lấy giá trị của RAX đặt lên Stack.
**Quá trình Push:**
**+Trước Push**
RAX = 42
RSP = 0x7000

Memory:

0x7000 → ...   <-Top of stack
0x6FF8 → ...
0x6FF0 → ...

**+Sau Push**
RAX = 42
RSP = 0x6FF8

Memory:

0x7000 → ...
0x6FF8 → 42   ← TOP
0x6FF0 → ...
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
