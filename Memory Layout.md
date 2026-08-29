Memory Layout (bố cục bộ nhớ) là cách mà chương trình máy tính phân chia, sắp xếp các vùng nhớ RAM khi được nạp lên để chạy
<img width="1000" height="800" alt="image" src="https://github.com/user-attachments/assets/8ce99e90-16da-407a-8ecc-6fefcb419f0a" />
# Code Segment (Text)

Là một vùng memory dùng để chứa các instruction của chương trình đang chạy, hay dễ hiểu là nó chứa code mình viết.  
Ví dụ chương trình có các instruction:
<img width="1042" height="185" alt="Screenshot 2026-08-29 200024" src="https://github.com/user-attachments/assets/6086312e-7fa9-4514-9074-a336a551a659" />
Sau khi chương trình được biên dịch thành Machine code, các instruction sẽ được chứa trong **Code Segment**  
Memory  

0x401000 → instruction 1  
0x401005 → instruction 2  
0x401008 → instruction 3  
...  
  
CPU có thể fetch instruction dựa trên địa chỉ của nó.  
