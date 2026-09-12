# BIT

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



# Hex(Hexadecimal): Hệ thập lục phân-> 16
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

# ENDIANNESS
Ví dụ ta có số: 0x12345678
Số này gồm 8 ký tự Hex.
Mà:
2 ký tự Hex = 1 byte
nên:

0x12  0x34  0x56  0x78
  │     │     │     │
  └─────┴─────┴─────┘
       4 byte


RAM chỉ lưu theo từng byte, tức nghĩa để lưu "0x12345678" cần 4 địa chỉ tương ứng 4 byte.
NHƯNG nó sẽ được lưu theo thứ tự như thế nào
## BIG-ENDIAN VÀ LITTLE-ENDIAN
----**Big-endian** đặt **byte lớn nhất(MSB-Most Significant Byte)** ở địa chỉ nhỏ nhất.

Với: 0x12345678
ta có:

0x12  0x34  0x56  0x78
 ↑
MSB

Memory:
   Địa chỉ       Dữ liệu

   0x1000   →    12
   0x1001   →    34
   0x1002   →    56
   0x1003   →    78

----**Little-endian** đặt **byte nhỏ nhất(LSB-Least Significant Byte)** ở địa chỉ nhỏ nhất.

Với: 0x12345678
Byte cuối: 78 là LSB.

Memory sẽ thành:
   Địa chỉ       Dữ liệu

   0x1000   →    78
   0x1001   →    56
   0x1002   →    34
   0x1003   →    12

Trong: 0x12345678
ta có:

12 34 56 78
↑           ↑
MSB         LSB

12 = MSB (Most Significant Byte)
78 = LSB (Least Significant Byte)
=>Big-endian hay Little-endian chỉ **thay đổi thứ tự** các byte trong Memory, **Không thay đổi nội dung**

