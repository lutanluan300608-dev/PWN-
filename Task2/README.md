# FORMAT STRING
## Khái niệm 
-Lỗi format string xảy ra khi mà chương trình truyền trực tiếp dữ liệu đầu vào của người dùng vào các hàm in (`printf`) mà không dùng chuỗi định dạng cố định
-VÍ DỤ
```
char buf[100];
fgets(buf, sizeof(buf), stdin);
printf(buf);      -> lỗi 
```
-> Thông thường khi in sẽ viết là `printf("%s", buf);`
