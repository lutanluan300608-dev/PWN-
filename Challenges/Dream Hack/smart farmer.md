# Phân tích lỗi
<img width="871" height="722" alt="Screenshot 2026-11-17 001419" src="https://github.com/user-attachments/assets/11bc8888-4299-4e32-8d51-c61f27585f16" />

- Bài này mình chỉ cần ghi đè lên  biến v6 thành 0x13371337 là có được flag
- Mình cần tính offset từ buf tới v6 để ghi đè
- Mình đã biết được đầu buf nằm ở rbp-0x30 và biến v6 nằm ở rbp-0x10
# Khai thác

<img width="1135" height="222" alt="Screenshot 2026-11-17 002806" src="https://github.com/user-attachments/assets/3b2ea75b-9b17-4d42-b1de-c3b4a484df7f" />
<img width="442" height="275" alt="Screenshot 2026-11-17 001007" src="https://github.com/user-attachments/assets/60ecdfdf-b95f-4097-a287-9ff9dc744777" />
