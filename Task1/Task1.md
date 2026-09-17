Task 1: Tools & Buffer Overflow exploitation
Tools
Làm quen và cài đặt các công cụ cần thiết cho quá trình học Pwn:

IDA / Ghidra: Reverse engineering, phân tích binary.
Linux: WSL hoặc máy ảo Linux, khuyến nghị Ubuntu 22.04.
GDB + pwndbg/GEF: Debug và phân tích chương trình.
pwntools: Viết script exploit.
gcc: Compile và build chương trình.

Buffer Overflow & các kỹ thuật khai thác
Tìm hiểu bản chất của Buffer Overflow và các cơ chế bảo vệ phổ biến:

PIE
NX
RELRO
Canary

Sau đó tìm hiểu các kỹ thuật bypass và khai thác cơ bản:

ret2win
ROP / ROP Chain
Shellcode Injection
ret2libc
Stack Pivot
Off-by-one
Bypass Canary
GOT overwrite / manipulate GOT entries
Leak địa chỉ để bypass PIE

Thực hành các kiến thức trên thông qua các challenge Pwn trên PicoCTF, DreamHack hoặc các wargame/CTF platform khác.

🎯 Target
Hiểu rõ bản chất của Buffer Overflow, biết sử dụng các tool cơ bản và có khả năng áp dụng các kỹ thuật khai thác để giải challenge.

Đồng thời rèn CTF sense, khả năng debug, đọc binary và tư duy tìm primitive → leak → bypass mitigation → exploit.

📝 Requirement
Viết write-up đầy đủ về các kiến thức đã học và các challenge đã giải.

Không bắt buộc phải học máy móc theo một nguồn duy nhất. Khuyến khích research thêm từ các tài liệu và write-up khác.

Có thể tham khảo playlist học Pwn của Admin CLB:

Playlist học Pwn
Tool dùng để debug, chọn 1 trong 2:
https://github.com/pwndbg/pwndbg
https://github.com/hugsy/gef
