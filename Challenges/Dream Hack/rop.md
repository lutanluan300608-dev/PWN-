Source: https://dreamhack.io/wargame/challenges/354
# Phân tích lỗi 
<img width="631" height="358" alt="Screenshot 2026-11-16 142254" src="https://github.com/user-attachments/assets/db335093-7e41-4051-8919-8d22455f6a09" />
<img width="577" height="635" alt="Screenshot 2026-11-16 142546" src="https://github.com/user-attachments/assets/0efeb9df-3986-485e-b81a-8f132b1c9a2d" />

- Bài này có 2 lỗi BOF, lần đầu chắc chắn dùng để leak canary
- Bài không có sẵn hàm win() hay hàm nào để in ra flag
- Ban đầu mình định dùng one_gadget nhưng PIE của libc lại bật nên cách này là không thể vì chỉ có 2 lần gửi payload

<img width="1296" height="422" alt="Screenshot 2026-11-16 144805" src="https://github.com/user-attachments/assets/adda57e5-3840-4cc0-a0ce-eedc258f72a6" />

- Gadget chỉ có pop rdi và pop rsi, cũng ko gọi syscall execve được -> rdi và rsi có vai trò là 2 tham số đầu, mình có thể thử dùng 1 hàm có sẵn trong binary -> system()

<img width="811" height="87" alt="Screenshot 2026-11-16 152111" src="https://github.com/user-attachments/assets/42368e92-2346-4e0d-97d4-d3b9c1f7f0de" />


- Mình cũng tìm thử hàm system() nhưng nó chỉ có libc system. Mình sẽ thử leak canary, sau đó return về main để có 2 lần read(). Lần 1 leak libc, lần 2 return về libc system với 2 gadget.

# Khai thác 
- Mình cần build docker và pwninit để liên kết thư viện được cho
<img width="438" height="136" alt="Screenshot 2026-11-16 170820" src="https://github.com/user-attachments/assets/82d272a5-e95f-443f-8c85-66b97420f6d7" />

<img width="477" height="250" alt="Screenshot 2026-11-16 170832" src="https://github.com/user-attachments/assets/6f8ec2e0-a91a-4b7f-8a7c-74488bc2046d" />

- Đầu tiên mình leak canary ra trước

<img width="576" height="212" alt="Screenshot 2026-11-16 171004" src="https://github.com/user-attachments/assets/820e90df-c2c3-429c-b779-887af5ec1b6c" />

- Sau đó mình return về main, nhưng lúc chạy do có lỗi sigsegv nên mình ret về địa chỉ main+1 (bỏ qua push rbp)

<img width="337" height="152" alt="Screenshot 2026-11-16 171423" src="https://github.com/user-attachments/assets/9b4ba9eb-5064-4df8-8d03-e1bc3eaf9a5b" />

<img width="392" height="285" alt="Screenshot 2026-11-16 171502" src="https://github.com/user-attachments/assets/4fe47036-d8b6-468d-9fb4-8cae6e7292f4" />

- Rồi mình tính lại offset từ buffer đến chỗ trỏ tới địa chỉ libc, sau đó mình leak libc và bật vmmap tính offset libc base

<img width="441" height="177" alt="Screenshot 2026-11-16 172652" src="https://github.com/user-attachments/assets/62787505-d60e-4e5e-a240-c3a3c6ea52d0" />

- Cuối cùng mình chỉ cần đưa chuỗi /bin/sh làm tham số đầu cho hàm system(), `p64(next(libc.search('/bin/sh')))` sẽ tự tìm con trỏ trỏ tới địa chỉ chuỗi /bin/sh cho mình

### Final Script
<img width="607" height="842" alt="Screenshot 2026-11-16 173026" src="https://github.com/user-attachments/assets/55788292-e0f6-4254-8bdc-6b23924b7064" />

