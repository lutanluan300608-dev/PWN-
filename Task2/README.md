# FORMAT STRING
## Khái niệm 
-Lỗi format string xảy ra khi mà chương trình truyền trực tiếp dữ liệu đầu vào của người dùng vào các hàm in (`printf`) mà không dùng chuỗi định dạng cố định
-VÍ DỤ
```
char buf[100];
fgets(buf, sizeof(buf), stdin);
printf(buf);      -> lỗi 
```
-> Thông thường khi in sẽ viết là `printf("%s", buf);` -> có định dạng là %s  

### Các loại Format (Định dạng)
- `%p` in ra địa chỉ con trỏ dưới dạng hex
- `%x` in ra số nguyên 32 bit (unsigned int) dạng hex  
- `%lx`  in ra số nguyên 64 bit (unsigned long) dạng hex
- `%d` in ra số nguyên có dấu 32 bit (signed int) dạng dec
- `%ld` in ra số nguyên có dấu 32 bit (signed int) dạng dec
- `%u` in số nguyên không dấu (unsigned int) dạng dec  
- `%c` in ra 1 ký tự (1 byte) dựa teho ASCII
- `%s` in ra chuỗi dữ liệu đến khi gặp null byte `(\x00)`. Nó sẽ truy cập vào địa chỉ đang chứa dữ liệu và in ra dữ liệu
- `%n` không in ra, nhưng nó sẽ đếm số byte được in ra trước nó và truyền vào biến tương ứng

### Cơ chế printf 
- 32 bit: In trên stack
- 64 bit: 5 format đầu sẽ được đưa vào 5 thanh ghi theo thứ tự: RSI, RDX, RCX, R8, R9. (RDI chứa chuỗi printf). Từ format thứ 6 sẽ là dữ liệu trên Stack.

### Leak Stack Memory 
- Vì `%p` là in địa chỉ con trỏ trỏ tới dữ liệu. Nên khi `printf("%p")`, nó sẽ in ra địa chỉ con trỏ(% đầu tiên thì sẽ là địa chỉ bên trong rsi). Mình có thể truyền nhiều `%p`.
- `%s` sẽ trỏ tới vị trí chứa địa chỉ HỢP LỆ để đọc chuỗi ký tự tại đó. 

### Ghi đè dữ liệu 
- `%n` sẽ ghi đè số byte đếm được vào địa chỉ trỏ tới. Mà `%c` lại lấy đúng duy nhất 1 byte -> mình có thể viết 1 hoặc 1 chuỗi `%c` trước `%n`, khi printf thì `%n` sẽ đếm số byte (số lần `%c`) và ghi đè dữ liệu tại vị trí tương ứng
- Padding: nếu minh muốn đếm 18 byte thì mình có thể viết là `%18c`. 
Ví dụ mình muốn ghi đè vào [rsp+16] thì mình sẽ `printf("%c%c%c%c%c%c%n")`
- Mình có thể in nhanh `%p` thứ 8 thay vì viết 8 lần %p, bằng cách `printf("%8$p")`
- Mình cũng có thể điều khiển `%n` ghi vào thứ tự nào, ví dụ `printf("%28c%8$n")` -> ghi 28 vào % thứ 8

## FMTSTR 1 - Leak dữ liệu bằng %p
<img width="1067" height="717" alt="Screenshot 2026-11-08 180713" src="https://github.com/user-attachments/assets/bf929cce-b115-4c18-9fa0-a3374f7d9dcf" />

- Mình chạy file và đi tới hàm open(), nó sẽ mở file flag.txt đọc dữ liệu vào. Khi chạy đến hàm printf(), dữ liệu đó đã được đưa lên Stack


<img width="1608" height="833" alt="Screenshot 2026-11-09 163443" src="https://github.com/user-attachments/assets/6d2f4c35-5852-4b99-af48-fd027296aea0" />

- Kiểm tra Stack thì chuỗi Flag nằm ở vị trí % thứ 12 trở đi
- Có 2 cách để mình leak ra:
- Cách 1 là chạy chương trình và nhập thủ công từng %, sau đó chuyển hết về đúng dạng

<img width="920" height="467" alt="Screenshot 2026-11-09 164133" src="https://github.com/user-attachments/assets/2c720dc5-fdd3-4033-8d6c-8eb9a478eb7c" />

- Cách 2 là viết Script

  <img width="655" height="381" alt="Screenshot 2026-11-09 164725" src="https://github.com/user-attachments/assets/0dc8d71d-2cea-4423-b6da-bc5ea94e0b75" />

- Do chương trình chỉ có 1 lần nhập nên mình sẽ chạy 1 vòng lặp chạy lại chạy chương trình để tự động nhập
- Vì in byte ra lúc chạy script nhìn khá rối nên mình sẽ tạo 1 biến flag để lưu
- Mình sẽ nhận hết các byte leak ra và đưa vào output, sau đó nối vào flag thành chuỗi, khi output có byte '}' thì sẽ in ra chuỗi flag và dừng vòng lặp. 

## FMTSTR 2 - Leak dữ liệu bằng %s
<img width="1687" height="832" alt="Screenshot 2026-11-09 175239" src="https://github.com/user-attachments/assets/47622486-b404-42b4-ad06-2a8fa7269e46" />

- Vì flag lần này được read vào buf nằm ở vùng Heap chứ không phải Stack, thứ nằm trên Stack sẽ là địa chỉ vùng Heap đang chứa flag, nên nếu dùng %p thì thứ mình leak được là địa chỉ heap chứ không phải flag.

<img width="1408" height="638" alt="Screenshot 2026-11-09 180240" src="https://github.com/user-attachments/assets/9600add2-a33f-4400-914d-d0a6b87a140a" />

- Sử dụng %s thay vì in ra dữ liệu là địa chỉ heap, thì sẽ sẽ truy cập vào địa chỉ đó và in ra dữ liệu trong đó (hiểu đơn giản %p in ra cha, %s in ra con) 

<img width="546" height="180" alt="Screenshot 2026-11-09 181134" src="https://github.com/user-attachments/assets/abe63c0c-e740-4533-b1e4-e2c1ea01e0e4" />

<img width="970" height="153" alt="Screenshot 2026-11-09 181209" src="https://github.com/user-attachments/assets/ed633d6b-cd87-4594-9cf4-6414bd34f368" />

- Vì %s in ra đến khi nào gặp null byte, nên việc flag không có null byte bên trong thì khá dễ để leak vì nó in ra hết được 
- Nhập thẳng trong lúc chạy chương trình hoặc trên gdb sẽ nhanh hơn thay vì viết Script cho bài này
## FMTSTR 3 - Leak dữ liệu kết hợp %p và %

<img width="1457" height="652" alt="Screenshot 2026-11-10 153316" src="https://github.com/user-attachments/assets/5b2fc771-d4ae-4544-a367-8af01f0fbf1e" />
<img width="486" height="246" alt="Screenshot 2026-11-10 153731" src="https://github.com/user-attachments/assets/5932c295-ba1d-4ba5-a856-07a32b4c6cc7" />

- Trong bài này, flag được đọc 1 nửa vào con trỏ flag1 và nửa còn lại vào global var flag2.
- Có 2 lỗi Format String và không có BOF
  
<img width="1192" height="492" alt="Screenshot 2026-11-10 160729" src="https://github.com/user-attachments/assets/2142ae33-d6b3-4ee5-9dd1-3a93521c9763" />

- Nửa đầu flag thì mình còn có thể dùng %s để leak do nằm trên Stack. Do PIE đang bật nên nửa còn lại thì buộc minh phải leak địa chỉ binary


