# Unsigned và Signed Integer 
### Unsiged Integer

- Đối với số nguyên không dấu thì sẽ sử dụng toàn bộ các BIT để biểu diễn 1 số nguyên dương. Ví dụ có 8 bit thì sẽ biểu diễn được **$$2^8$$** giá trị số nguyên dương tức từ 0->265.

<img width="942" height="461" alt="Screenshot 2026-11-26 154813" src="https://github.com/user-attachments/assets/0ce4ddfb-7a1a-4dea-a7cc-b7b21b1a0fb6" />


### Signed Integer

- Đối với số nguyên có dấu thì sẽ sử dụng BIT đầu tiên để quy định dấu, vì BIT đầu tiên luôn lớn hơn tổng các BIT còn lại. Nên nếu BIT đầu tiên là 1 (bật) thì BIT đầu tiên là số âm, và kết quả cuối cùng chắc chắn là 1 số nguyên âm
- Tuy nhiên giá trị tối đa mà số nguyên có dấu biểu diễn được sẽ ít hơn 1 nửa không dấu. Ví dụ có 8 bit thì vẫn sẽ biểu diễn được **$$2^8$$** giá trị nhưng tối đa chỉ từ 1->127 nếu là số nguyên dương và -127->-1 nếu là số nguyên âm và thêm giá trị 0.

<img width="1217" height="817" alt="Screenshot 2026-11-26 161418" src="https://github.com/user-attachments/assets/37b46107-3b4f-4008-a614-40b64145a758" />

- NOTE: đối với số nguyên có dấu trong dạng hex có thể nhận biết số nguyên dương và âm bằng cách nhìn byte đầu tiên. Nếu bắt đầu từ 0x0.. đến 0x7f.. thì là nguyên dương, từ 0x80.. trở đi là số nguyên âm. Còn không dấu thì mặc định luôn dương. 

# Integer Overflow - Tràn số nguyên có dấu
- Tràn số nguyên có dấu là việc mà mình nhập vào dữ liệu có kích thước lớn hơn kích thước của **kiểu dữ liệu khai báo biến**.
- `signed char (8 bit)`: -128 đến 127
- `short (16 bit)`: -32.768 đến 32.767
- `int (32 bit)`: -2.147.483.648 đến 2.147.483.647
- `long long (64 bit)`: khoảng ±9,2 × 10¹⁸

- Ví dụ mình có 1 chương trình:

<img width="417" height="242" alt="Screenshot 2026-11-26 182954" src="https://github.com/user-attachments/assets/92863c58-b1a5-4c1c-a1d5-87a7ba21bcf0" />

<img width="656" height="82" alt="Screenshot 2026-11-26 183301" src="https://github.com/user-attachments/assets/e2c9e5e2-bfa3-4613-97ff-2f7c0497f316" />

- Nó sẽ đếm số byte mình nhập vào và in ra (biến size là biến chứa), mình nhập vào "123\n" là 4 byte gồm Enter, và nó in ra 4.

<img width="1552" height="78" alt="Screenshot 2026-11-26 183653" src="https://github.com/user-attachments/assets/07fba85f-e2e6-4381-b840-7ef4bd386ce0" />

- Lần này mình nhập thử 128 chữ A (thêm \n) nhưng nó lại in ra -127 là số âm thay vì 129. Do size là biến chứa số ký tự mà size được khai báo kiểu `char` (thường mặc định là signed) nên kích thước số nguyên dương tối đa chỉ có 127 mà mình nhập tận 129 ký tự -> Tràn số nguyên có dấu
- NOTE: kiểu char 8 bit -> $$2^8$$ -> 256 -> 129 - 256 = -127.
