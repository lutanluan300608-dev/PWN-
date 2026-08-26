## 1.Máy tính cơ bản gồm:
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



### CPU (Central Processing Unit): 
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

### CU (Control Unit)
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


### ALU (Arithmetic Logic Unit) = Bộ số học và logic.
```
Thành phần thực hiện các phép toán và phép logic trên dữ liệu.
Phép toán số học: +, -, ×, ÷ .
Phép toán logic: AND, OR, XOR, NOT.

```

### RAM(Random Access Memory)
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
### Register (Thanh ghi)
```


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


