# Máy tính cơ bản gồm:
```
             ┌──────────────┐
             │     CPU      │
             │              │
             │  Registers   │
             │     ALU      │                                              
             │  Control     │
             └──────┬───────┘
                    │
          ┌─────────┴─────────┐
          │                   │
      ┌───▼───┐          ┌────▼────┐
      │  RAM  │          │ I/O     │
      └───────┘          └─────────┘

```

<img width="586" height="321" alt="image" src="https://github.com/user-attachments/assets/6a9d23a1-08d4-4da5-9dba-31b3408c24ab" />

# Kiến trúc x86-64
```
x86 là kiến trúc 32-bit truyền thống và x86-64 là phiên bản mở rộng hơn.
x86-64 là một loại kiến trúc tập lệnh (ISA), tức là nó quy định CPU cso những instruction nào, register nào, cách xử lí dữ liệu ra sao, truy cập memory ntn,....
Con số 64 chỉ thế hệ ISA mở rộng của ISA với khả năng xử lí dữ liệu và địa chỉ theo phạm vi 64-bit. (nó vẫn thao tác được với 8, 16, 32-bit tùy vào instruction)


```
# CPU (Central Processing Unit): 
```
Bộ xử lí trung tâm của máy tính, hiểu đơn giản CPU là thành phần thực thi các lệnh chương trình.
Chức năng cơ bản thì là điều khiển hoạt động máy tính, xử lý dữ liệu.
CPU hoạt động dựa trên các lệnh có sẵn trong Bộ nhớ.
Nhưng lưu ý,  các lệnh sẽ được nạp từ ổ đĩa vào Bộ nhớ RAM, sau đó CPU mới lấy lệnh và thực thi.

Ổ đĩa (SSD/HDD)
      │
      │ nạp chương trình
      ▼
     RAM
      │
      │ CPU đọc lệnh
      ▼
     CPU
      │
      ├── Fetch     → lấy lệnh
      ├── Decode    → giải mã lệnh
      └── Execute   → thực thi lệnh


CPU cũng gồm các thành phần nhỏ khác:
                 CPU
        ┌───────────────────┐
        │                   │
        │  Registers        │ ← dữ liệu rất nhanh
        │                   │
        │  ALU              │ ← tính toán
        │                   │
        │  Control Unit     │ ← điều khiển
        │                   │
        │  Cache            │ ← bộ nhớ tốc độ cao
        │                   │
        └───────────────────┘
```

# CU (Control Unit)
```
-Điều khiển máy tính theo chương trình đã định sẵn, phối hợp hành động với các thiết bị khác trong CPU.
->Có thể hiểu đơn giản, CU là nơi ra lệnh cho các thành phần khác phải làm gì.
Ví dụ: CU không trực tiếp tính 9 + 10 mà sẽ điều  ALU làm.
 
Có thể hình dung:
              CPU
              │
              │ 1. Fetch
              ▼
       lấy instruction
              │
              ▼
         2. Decode
              │
      CPU giải mã instruction
              │
              ▼
     CU / control logic
       tạo tín hiệu điều khiển
              │
              ▼
   3. Execute
   ┌──────────┼──────────┐
   ▼          ▼          ▼
Registers     ALU       Memory

Quá trình trên gọi là **Fetch**

Instruction = CPU được giao việc gì
Fetch = đi lấy công việc
Decode = đọc và hiểu công việc
Control = điều phối ai phải làm
Execute = thực hiện công việc
Result = nhận kết quả

==>Ví dụ một đội bóng:
CU  = HLV
ALU = cầu thủ thực hiện phép tính
Registers = nơi giữ dữ liệu tạm thời
Memory = kho chứa dữ liệu/instruction
```


# ALU (Arithmetic Logic Unit) = Bộ số học và logic.
```
Thành phần thực hiện các phép toán và phép logic trên dữ liệu.
Phép toán số học: +, -, ×, ÷ .
Phép toán logic: AND, OR, XOR, NOT.

```

# RAM(Random Access Memory)
```
Là bộ nhớ mà máy tính dùng để lưu trữ TẠM THỜI chương trình và dữ liệu đang được sử dụng.
RAM là nơi máy tính đặt những thứ đang cần làm việc tại đó.

          SSD
           ↓
đưa chương trình/dữ liệu vào RAM
           ↓
    CPU sử dụng chúng


          RAM
┌──────────────────────┐
│ instruction          │
│ instruction          │
│ data                 │
│ data                 │
│ ...                  │
└──────────────────────┘

Mỗi Data hay Instruction trong bộ nhớ có một ĐỊA CHỈ.

Địa chỉ      Nội dung

0x401000 → instruction 1
0x401005 → instruction 2
0x401008 → instruction 3

Địa chỉ       Dữ liệu        

0x1000   →       42
0x1001   →       17
0x1002   →       99
0x1003   →       25
0x1004   →       ...

=>Tại địa chỉ 0x1000 đang chứa giá trị 42

  RAM cũng giống một dãy hộp

┌────────┬────────┬────────┬────────┐
│ 1 byte │ 1 byte │ 1 byte │ 1 byte │
├────────┼────────┼────────┼────────┤
│  0x1000│  0x1001│  0x1002│  0x1003│
└────────┴────────┴────────┴────────┘

Mỗi ô có một địa chỉ, chứa 1 byte.

NHƯNG LƯU Ý có dữ liệu sẽ chiếm nhiều hơn 1 byte, cũng tức là chiếm nhiều hơn 1 địa chỉ.
Ví dụ là 0x123456789 sẽ chiếm 4 byte, hoặc đơn giản 1 số nguyên 32-bit sẽ chiếm 4 byte.
Địa chỉ xác định 1 byte, dữ liệu có thể chiếm 1 hoặc nhiều hơn 1 địa chỉ, byte.



```
# Register (Thanh ghi)
```
-Register là những vùng lưu trữ rất NHỎ và rất NHANH nằm bên trong CPU dùng để giữ các giá trị mà CPU đang cần xử lí.
-Register chỉ là nơi giữ bit, tức là Register không hiểu các con số hay chữ cái thông thường, nó giữ bit và CPU sẽ diễn giải các bit đó ra.
-RAX, RBX,... là tên của Register. Một Register 64-bit chứa được 64 bit dữ liệu.
-Register có nhiều chức năng khác chứ không chỉ riêng chứa dữ liệu.
RSP → liên quan đến stack
RBP → liên quan đến stack frame
RIP → instruction pointer

==>Lưu ý quan trọng: Register và RAM là hai loại bộ nhớ khác nhau.
Register
→ nằm trong CPU
→ rất ít
→ cực kỳ nhanh
→ dùng để giữ dữ liệu/giá trị mà CPU đang sử dụng

RAM
→ nằm bên ngoài lõi CPU
→ dung lượng lớn hơn rất nhiều
→ chậm hơn Register
→ chứa chương trình và dữ liệu đang được sử dụng
```

### Các loại Register

RAX, RBX,... là thanh ghi 64-bit trong kiến trúc x86-64.  
RAX  
┌────────────────────────────────────────────────┐  
│                 64 bits                        │ = 8 byte  
└────────────────────────────────────────────────┘  
  
Chữ R ở đầu thường gắn với phiên bản 64-bit của nhóm register đó.  
Ví dụ:  
  
RAX → 64-bit  
EAX → 32-bit  
AX  → 16-bit  
AL  → 8-bit  
  
  

  
**General-Purpose Register**  
-RAX, RBX, RCX, RDX: là các thanh ghi có thể được dùng cho nhiều mục đích khác nhau trong quá trình tính toán và xử lí dữ liệu.  
  
**RIP = Instruction Pointer**   
RIP giữ địa chỉ instruction mà CPU sẽ thực hiện tiếp theo.  
  
**RSP là một Register đặc biệt, R: Register, SP: Stack Pointer.**  
Nó có nhiệm vụ giữ địa chỉ liên quan đến đỉnh (top) của stack.  
  
**RBP = Base Pointer**  hoặc Frame Pointer
RBP thường dùng để đánh dấu vị trí cố  trong stack frame của hàm.  
Stack  
┌──────────────┐  
│ dữ liệu      │  
├──────────────┤  
│ ...          │    
├──────────────┤  
│              │ ← RBP  
├──────────────┤  
│              │  
├──────────────┤  
│              │ ← RSP  
└──────────────┘  
Trong lúc hàm chạy thì RSP có thể thay đổi khi stack được sử dụng nên RBP giúp việc tham chiếu dữ liệu thuận lợi hơn.  
LƯU Ý là RBP không nhất thiết phải làm Frame Pointer.  
  
**RDI** chứa giá trị Argument đầu tiên.  

**RSI** chứa giá trị Argument thứ hai.
# EFLAG (Thanh ghi trạng thái)
**1. Cờ trạng thái (Status Flags)Nhóm cờ này phản ánh kết quả của các lệnh tính toán (như cộng, trừ, so sánh). CPU dựa vào đây để thực hiện các lệnh rẽ nhánh điều kiện (như lệnh nhảy JZ, JNZ).**  
-ZF (Zero Flag): Bật khi kết quả phép toán bằng 0.  
-SF (Sign Flag): Bật khi kết quả là số âm (bit cao nhất của kết quả là 1).  
-CF (Carry Flag): Bật khi có hiện tượng "nhớ" (carry) hoặc "mượn" (borrow) từ bit cao nhất (thường dùng cho số không dấu).  
-OF (Overflow Flag): Bật khi kết quả vượt quá giới hạn lưu trữ của kiểu dữ liệu (tràn số có dấu).  
-AF (Auxiliary Carry Flag): Bật khi có hiện tượng nhớ ở bit thứ 3 sang bit thứ 4 (dùng cho toán BCD).  
-PF (Parity Flag): Bật nếu tổng số bit 1 trong byte thấp nhất của kết quả là một số chẵn.  
  
**2. Cờ điều khiển (Control Flags)**  
-DF (Direction Flag): Điều khiển hướng xử lý của các chuỗi ký tự (String instructions). Nếu DF = 0, chuỗi được xử lý từ trái sang phải (tăng dần địa chỉ). Nếu DF = 1, chuỗi xử lý từ phải sang trái (giảm dần địa chỉ).  
  
**3. Cờ hệ thống (System Flags)Nhóm này điều hành các chức năng quản lý hệ thống của CPU và hệ điều hành (HĐH). Người dùng thông thường không nên tự ý thay đổi.**  
-IF (Interrupt Enable Flag): Nếu IF = 1, CPU cho phép các ngắt ngoại vi (như nhấn phím, chuột) xen vào quá trình xử lý.  
-TF (Trap Flag): Bật chế độ chạy từng bước (Single-step mode). Thường được các công cụ sửa lỗi (Debugger) dùng để kiểm tra từng dòng code.  
-IOPL (I/O Privilege Level): Gồm 2 bit, xác định mức độ đặc quyền cần thiết để thực hiện các lệnh xuất/nhập (I/O).NT (Nested Task): Kiểm soát chuỗi các tác vụ được gọi nối tiếp nhau.  
# Pointer
```
Là giá trị dùng để chỉ đến một vị trí trong Memory.
Pointer chỉ chứa địa chỉ không chứa giá trị.

```


