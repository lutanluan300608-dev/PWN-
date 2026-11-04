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







