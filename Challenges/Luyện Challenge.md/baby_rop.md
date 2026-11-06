# Phân tích chương trình
<img width="487" height="347" alt="Screenshot 2026-11-06 081823" src="https://github.com/user-attachments/assets/a63ec2bf-502a-417d-83e8-8994413a7fd1" />
<img width="602" height="191" alt="Screenshot 2026-11-05 163605" src="https://github.com/user-attachments/assets/308856f2-83d5-4aee-b165-5e5469ed4ce4" />

- Không có sẵn hàm gọi /bin/sh, trong hàm loop() cho phép mình read 2 lần
- NX bật -> ko thể shellcode
- Mình sẽ sử dụng ROPchain. Nhưng vì PIE đang bật, mình sẽ phải leak địa chỉ cùng nằm trong vùng `.text`

# Ý tưởng 
- Đầu tiên mình cần leak 1 địa chỉ trong vùng `.text` để tính được base address.
- Vì PIE đang bật nên địa chỉ của các tệp thực thi sẽ được đưa ngẫu nhiên vào 1 base address -> offset của gadget cũng sẽ ngẫu nhiên thay đổi theo binary base. Ví dụ ban đầu "pop rdi" cách binary base 0x1690, nhưng khi chạy, binary base tăng lên 0x5, thì lúc này offset "pop rdi" cũng tăng lên 0x5. Thế nên mình mới cần tính toán
- Nhưng offset là cố định. Mình sẽ lấy địa chỉ leak được trừ đi binary base để ra được base address. Có base address rồi thì mình cứ việc cộng thêm vào là ra chính xác offset của gadget

# Khai thác 
<img width="1253" height="878" alt="Screenshot 2026-11-06 090714" src="https://github.com/user-attachments/assets/5c68b0ee-d59f-4097-8c71-234e3351c112" />
<img width="1212" height="151" alt="Screenshot 2026-11-06 082105" src="https://github.com/user-attachments/assets/97972895-07e6-462d-8981-59a503e9c52a" />

- Mình chạy thử và tìm trên Stack và tìm thấy 1 địa chỉ hợp lệ. Các địa chỉ màu đỏ thường sẽ là địa chỉ trong vùng `.text`. Mình cần phải vmmap để check

<img width="506" height="65" alt="Screenshot 2026-11-06 092008" src="https://github.com/user-attachments/assets/6f4df6e2-5c3c-48ce-bc47-50f0e947cb88" />

<img width="506" height="91" alt="Screenshot 2026-11-06 141519" src="https://github.com/user-attachments/assets/419df543-940d-414a-85b6-53ea43c349df" />

- Mình tính offset và tiến hành leak địa chỉ, vì địa chỉ chỉ có 6 byte nên mình nối thêm 2 byte null để hàm read tự ngắt

<img width="1695" height="477" alt="Screenshot 2026-11-06 143206" src="https://github.com/user-attachments/assets/598e8210-5a28-46d9-9c32-60e3d366be6b" />

<img width="1900" height="392" alt="Screenshot 2026-11-06 143031" src="https://github.com/user-attachments/assets/67854c6c-6c8a-4be3-853b-602a1edc43b0" />

<img width="610" height="51" alt="Screenshot 2026-11-06 142646" src="https://github.com/user-attachments/assets/97d1783c-5610-4c52-a62c-190016f215d3" />

<img width="470" height="237" alt="Screenshot 2026-11-06 144737" src="https://github.com/user-attachments/assets/f6a80f01-e5d7-46be-89dd-9b03c1b2a32c" />

- Mình đã tính được offset từ địa chỉ trong vùng `.text` đến binary base. Giờ trừ ngược lại binary leak với offset là ra được base address.
- Cộng base address vào gadget là ra được offset đúng của gadget

<img width="521" height="57" alt="Screenshot 2026-11-06 145659" src="https://github.com/user-attachments/assets/e870809f-075b-4f87-8ba6-b68192d3007b" />

- Mình tính offset từ buf đến saved-rip để ghi đè gadget


<img width="1358" height="776" alt="Screenshot 2026-11-06 150408" src="https://github.com/user-attachments/assets/b86fe380-64ad-487f-9149-a3cd09f30f0f" />

<img width="542" height="55" alt="Screenshot 2026-11-06 150949" src="https://github.com/user-attachments/assets/3e637efa-cf9c-4247-a6d7-1c6d4e91280a" />

<img width="262" height="36" alt="Screenshot 2026-11-06 152115" src="https://github.com/user-attachments/assets/d770a436-2a94-47f1-8efb-abb5aa5e8720" />

- Mình cũng cần tìm 1 vùng địa chỉ có quyền read, write, mình mở vmmap và tìm 1 vùng trống nhiều

<img width="621" height="695" alt="Screenshot 2026-11-06 152830" src="https://github.com/user-attachments/assets/87b97d0c-484f-4b4e-865d-84660c045045" />




