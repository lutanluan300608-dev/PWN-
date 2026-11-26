# Unsigned và Signed Integer 
### Unsiged Integer

- Đối với số nguyên không dấu thì sẽ sử dụng toàn bộ các BIT để biểu diễn 1 số nguyên dương. Ví dụ có 8 bit thì sẽ biểu diễn được **$$2^8$$** giá trị số nguyên dương tức từ 0->265.

<img width="942" height="461" alt="Screenshot 2026-11-26 154813" src="https://github.com/user-attachments/assets/0ce4ddfb-7a1a-4dea-a7cc-b7b21b1a0fb6" />


### Signed Integer

- Đối với số nguyên có dấu thì sẽ sử dụng BIT đầu tiên để quy định dấu, vì BIT đầu tiên luôn lớn hơn tổng các BIT còn lại. Nên nếu BIT đầu tiên là 1 (bật) thì BIT đầu tiên là số âm, và kết quả cuối cùng chắc chắn là 1 số nguyên âm
- Tuy nhiên giá trị tối đa mà số nguyên có dấu biểu diễn được sẽ ít hơn 1 nửa không dấu. Ví dụ có 8 bit thì vẫn sẽ biểu diễn được **$$2^8$$** giá trị nhưng tối đa chỉ từ 1->127 nếu là số nguyên dương và -127->-1 nếu là số nguyên âm và thêm giá trị 0.

<img width="1217" height="817" alt="Screenshot 2026-11-26 161418" src="https://github.com/user-attachments/assets/37b46107-3b4f-4008-a614-40b64145a758" />

