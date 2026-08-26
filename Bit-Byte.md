## BIT

-Bit là đơn vị thông tin nhỏ nhất mà máy tính thường dùng để biểu diễn dữ liệu.

Một bit chỉ có 2 trạng thái:

0
1

-Có thể tưởng tượng nó giống một công tắc:

TẮT  → 0
BẬT  → 1
Vậy:

1 bit = 0 hoặc 1.

-Máy tính biểu diễn thông tin bằng rất nhiều bit:

10110100

Đây là một chuỗi gồm 8 bit.

-1 byte = 8 bits

**Vì 1 bit chỉ có: 0 hoặc 1 nên nó biểu diễn được 2 giá trị.**
n bit → 2ⁿ khả năng

Ví dụ:

1 bit  → 2¹ = 2
2 bit  → 2² = 4
3 bit  → 2³ = 8
4 bit  → 2⁴ = 16
8 bit  → 2⁸ = 256

Vì vậy 1 byte (8 bit) có thể biểu diễn 256 giá trị khác nhau, từ:

0 → 255



## Hex(Hexadecimal): Hệ thập lục phân-> 16
-Gồm: 0 1 2 3 4 5 6 7 8 9 A B C D E 
Trong Binary: 1010
Trong Hex: A


0x5A thì:

5 → 0101
A → 1010

nên:
0x5A = 01011010

Tức là:
2 ký tự Hex = 8 bit = 1 byte


**Ta thường hình dung bộ nhớ theo byte:**
Địa chỉ       Dữ liệu

0x1000   →    10101100
0x1001   →    11100010
0x1002   →    00001111

Mỗi ô ở đây có thể hình dung là 1 byte = 8 bit.
Như vậy:

RAM
 ↓
nhiều byte
 ↓
mỗi byte gồm 8 bit
