# 2. Quản lý Breakpoint (Điểm dừng)
b *<address> hoặc break <function_name>: Đặt breakpoint tại một địa chỉ hoặc tên hàm cụ thể.

bl: Liệt kê tất cả các breakpoint đang hoạt động.

delete <id>: Xóa breakpoint theo ID.

# 3. Điều hướng thực thi (Execution Control)
r (Run): Chạy chương trình từ đầu.

c (Continue): Tiếp tục chạy cho đến khi gặp breakpoint tiếp theo.

ni (Next Instruction): Thực thi lệnh assembly tiếp theo (bước qua hàm con - step over).

si (Step Instruction): Thực thi lệnh assembly tiếp theo (nhảy vào hàm con - step into).

finish: Chạy tiếp cho đến khi hàm hiện tại kết thúc và trả về hàm gọi nó.

# 4. Kiểm tra trạng thái bộ nhớ và thanh ghi (GEF Context View)

registers (hoặc i r): Xem giá trị hiện tại của các thanh ghi (RIP, RSP, RBP, RAX...).

telescope <address> (hoặc tele): Lệnh "vô địch" của GEF. Nó hiển thị nội dung bộ nhớ bắt đầu từ một địa chỉ, tự động nhận diện con trỏ, chuỗi ký tự (string) và các hàm, giúp bạn dò stack hoặc heap cực nhanh.

Ví dụ: tele $rsp 40 (xem 40 ô nhớ từ đỉnh stack).

x/<n>xw <address>: Xem bộ nhớ theo định dạnghex truyền thống của GDB.

vmmap: Hiển thị phân vùng bộ nhớ của tiến trình (xem đoạn nào đọc/ghi/thực thi - RWX, Stack, Heap, libc base...).

# 5. Khai thác lỗi tràn bộ đệm (Stack Overflow & ROP)

Tạo Pattern (Pattern Generation)
pattern create <length>: Tạo một chuỗi ký tự ngẫu nhiên có độ dài <length> để tìm khoảng cách đến thanh ghi Return Address (RIP/EIP).

pattern search <value>: Tìm offset chính xác dựa trên giá trị bị ghi đè ở thanh ghi (ví dụ: pattern search $rsp hoặc pattern search 0x41414141).

Hỗ trợ ROP (Return Oriented Programming)
ropgadget: Tự động tìm kiếm các ROP gadget (các đoạn mã kết thúc bằng lệnh ret) trực tiếp trong binary hoặc các thư viện đã load.

search-pattern <string/bytes>: Tìm kiếm một chuỗi hoặc byte cụ thể trong bộ nhớ (ví dụ tìm chuỗi "/bin/sh").

# 6. Theo dõi Heap (Dành cho bài Heap Exploitation)
Nếu bài CTF thuộc thể loại Heap (Fastbin attack, Use-After-Free, House of...):

heap chunks: Hiển thị trạng thái các chunk trên heap hiện tại (đã cấp phát hay nằm trong bin).

heap bins: Kiểm tra các bin (fast, unsorted, small, large) xem đang chứa các chunk nào.

vis: Trực quan hóa cấu trúc heap rất sinh động.
