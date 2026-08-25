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

Quá trình trên gọi là **FETCH**.
```

### Register (Thanh ghi)
```

```
###
```


```
