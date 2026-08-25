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
### Register (Thanh ghi)
```

```
###
```
<img width="586" height="321" alt="2" src="https://github.com/user-attachments/assets/7d7239b5-2a3c-4869-acd1-758623122229" />
<img width="586" height="321" alt="2" src="https://github.com/user-attachments/assets/06b6837d-efdc-4614-9135-28b2030165c6" />

```
