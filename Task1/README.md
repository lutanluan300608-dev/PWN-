
# BUFFER OVERFLOW
## Khái niệm
-**Buffer overflow** (Tràn bộ đệm) là một lỗi lập trình, xảy ra khi một **chương trình cố gắng lưu trữ lượng dữ liệu vượt quá dung lượng** cho phép của **vùng nhớ đệm** (buffer, là vùng nhớ được cấp phát tạm thời để chứa dữ liệu)  
-Nếu dữ liệu bị tràn, nó sẽ ghi đè lên lên các vùng nhớ liền kề, cụ thể là các ô nhớ địa chỉ byte tiếp theo    
-Việc kiểm soát được BOF có thể ghi đè địa chỉ trở về và dẫn đến 1 hàm mà mình muốn, hoặc ghi đè RIP để thực hiện instruction   
<img width="1600" height="900" alt="overflow-vulnerabilities-example-4" src="https://github.com/user-attachments/assets/e4aa5504-5ec2-4ce3-9347-aba2449483f4" />

**1. Trên Stack**   
   +Thường xảy ra khi một hàm tự gọi chính nó liên tục mà không có điểm dừng, mỗi lần gọi sẽ thêm một vùng stack frame mới vào      
   +Hoặc hàm gọi chồng nhau quá sâu(Hàm A gọi B, B gọi C, C gọi D,...) vượt quá giới hạn độ sâu của Stack      
   +Hoặc khai báo biến cục bộ hay mảng có kích thước quá lớn vượt quá giới hạn ngăn xếp của luồng   
   **=>Phần lớn khi tràn dữ liệu sẽ khiến chương trình crash ngay lập tức:**   
         Địa chỉ bộ nhớ không hợp lệ: Máy tính cố đọc dữ liệu bị đè nhưng nhận ra đó không phải là một địa chỉ phân vùng được phép truy cập. Hệ điều hành sẽ can thiệp và đưa ra lỗi Segmentation Fault (Core Dumped) để bảo vệ hệ thống.   
         Địa chỉ trả về của hàm (Return Address): Khi hàm chạy xong, nó lấy dữ liệu rác (ví dụ chuỗi AAAA) làm địa chỉ để nhảy tiếp. Vì AAAA không phải là địa chỉ của lệnh nào cả, chương trình sẽ crash.   
   +Việc nhập ký tự vượt quá kích thước mảng cũng có thể gây ra Stack-based Overflow(Tràn bộ đệm ngăn xếp)   
**2. Trên Heap**   
   Vùng nhớ Heap là nơi chứa các dữ liệu được cấp phát động   
   Nếu làm tràn một đối tượng hoặc một mảng trên Heap, dữ liệu sẽ đè lên:   
   Siêu dữ liệu của bộ quản lý (Chunk Metadata): Hệ điều hành dùng các ô nhớ nhỏ nằm ngay trước hoặc sau mỗi vùng cấp phát để lưu thông tin như: **Kích thước vùng nhớ này là bao nhiêu? Nó đang trống hay đã dùng?** Đè lên phần này sẽ làm hỏng trình quản lý bộ nhớ (malloc chunks), gây sập chương trình khi gọi lệnh giải phóng bộ nhớ (free()).   
   Các đối tượng/biến động khác: Các biến được tạo ra ngay sau đó. Nếu vùng nhớ liền kề chứa một "con trỏ hàm" (function pointer), việc ghi đè có thể thay đổi hàm mà chương trình sẽ gọi tiếp theo, dẫn đến chiếm quyền điều khiển.   

## BOF1 
<img width="956" height="741" alt="Thiết kế chưa có tên (1)" src="https://github.com/user-attachments/assets/d8e89809-64e6-4fd4-9cc3-32fda6423b98" />

-Biến buf được khai báo chỉ có kích thước 16 bytes nhưng chương trình lại cho phép đọc buf đến tối đa 0x30 = 48 bytes, nếu nhập quá dữ liệu của buf thì dữ liệu sẽ bị tràn và lan đến các biến v5, v6, v7. Đồng thời chương trình cũng cho phép chúng mình nắm quyền điều khiển shellcode nếu các biến v5, v6, v7 đều khác 0. Mình có thể tận dụng việc tràn biến để có thể chiếm quyền điều khiển shell      
<img width="1472" height="620" alt="Screenshot 2026-09-19 182507" src="https://github.com/user-attachments/assets/c60ade8a-1fd9-417e-9ff7-5b2986ab27ac" />  

-Mình đã chạy file và sau khi nhập 40 chữ A, dữ liệu đã bị tràn và ghi đè lên v5, v6, v7 -> mình đã chiếm được shell 
## BOF2
<img width="955" height="695" alt="Thiết kế chưa có tên (2)" src="https://github.com/user-attachments/assets/9d164a4e-07d0-4c96-8350-82999c7d88f8" />   

-Cũng giống như BOF1, mình cần ghi đè vào 3 biến a, b, c để chiếm shell. Nhưng chương trình yêu cầu giá trị được ghi đè phải khớp với điều kiện if, và các giá trị trên không thể nhập được từ bàn phím. Ví dụ 0xCAFEBABE gồm các byte 0xBE/0xBA/0xFE/0xCA, các byte này thuộc nhóm **ký tự không in được (non-printable ASCII)** <br>

<img width="1917" height="1068" alt="Screenshot 2026-09-20 140326" src="https://github.com/user-attachments/assets/a045a04a-bd9e-4303-8ede-11cc0fd50538" />

-Mình đã cài Sublime Text qua Snap và tạo 1 file  
<img width="955" height="695" alt="Thiết kế chưa có tên (3)" src="https://github.com/user-attachments/assets/05171524-ecd3-4bc6-afc3-1b0eb4ef3e26" />

-Đây là script python sử dụng pwntools, nhờ đó mình có thể nhập trực tiếp các byte vào chương trình. p64() để định dạng đúng kích thước 8 byte, ví dụ `payload += p64(0xCAFEBABE)` thì dữ liệu được nhập vào là **0x00000000CAFEBABE**. Còn nếu chỉ nhập `payload += p32(0xCAFEBABE)`, dữ liệu nhập vào sẽ sai yêu cầu, lúc này biến c sẽ lưu giá trị là 0xCAFEBABE00000000 -> sai với chương trình yêu cầu, các giá trị sau sẽ bị ghi sai vị trí. 

## BOF3 - RET2WIN

<img width="955" height="695" alt="Thiết kế chưa có tên (5)" src="https://github.com/user-attachments/assets/df1b0881-ef0b-401c-95c0-f769b34b6192" />

-Lần này lệnh lấy shell được đặt trong hàm win(), nhưng ở hàm main() không có lệnh nào để gọi hàm win(). Mình có thể truy cập được hàm win bằng cách overwrite địa chỉ hàm win() vào địa chỉ quay về (RIP) sau khi thực hiện xong hàm main()   

<img width="955" height="695" alt="Thiết kế chưa có tên (6)" src="https://github.com/user-attachments/assets/e2a782ce-b4f8-42db-8a78-b186c271ab3e" />

-Chương trình sẽ return về __libc_start_main sau khi xong hàm main(), lúc này mình muốn địa chỉ return mà nó nhảy đến là của hàm win(), nên mình sẽ phải ghi đè saved rip của main() trên Stack <br>  
- Cách 1, tìm địa chỉ hàm win và ghi nối vào payload, nhưng cách này sẽ khá lâu
<img width="1170" height="495" alt="Screenshot 2026-09-20 223109" src="https://github.com/user-attachments/assets/63faa0dd-c777-45a7-be80-b208bc64a63b" />

- Cách 2, sử dụng lệnh `exe = ELF('ten_file')` để phân tích 1 file định dạng ELF. Từ đó mà mình có thể trích xuất địa chỉ hàm qua lệnh `exe.sym['ten_ham']` để lấy địa chỉ hàm mà không cần tìm chay <br>
<br>
-Nhưng có 1 lỗi xảy ra,

<img width="963" height="428" alt="Screenshot 2026-09-21 103020" src="https://github.com/user-attachments/assets/ac9211c9-a0d5-4f06-9477-aa72e679541d" />


<img width="1432" height="651" alt="Tạm dừng chương trình để lấy PID" src="https://github.com/user-attachments/assets/70b84e34-d084-423d-ac38-36beb5768aba" />   

<img width="1470" height="622" alt="Screenshot 2026-09-21 104723" src="https://github.com/user-attachments/assets/0b7b9b5c-ab65-46cd-94d0-742bec93070e" />

-Mình disassemble main và đặt break point tại ret để xem thay đổi, lỗi gặp phải ở đây là Stack Alignment khi gọi hàm system().   
-Lệnh gây lỗi chương trình là `movaps XMMWORD PTR [rsp], xmm1`, lệnh movaps trên kiến trúc 64-bit buộc địa chỉ trong thanh ghi rsp phải chia hết cho 16(kết thúc bằng số 0 ở dạng hex)   
-Nhưng $rsp lại = 0x00007ffc70d5c458 điều này dẫn đến lỗi SIGSEGV
<img width="1453" height="378" alt="Screenshot 2026-09-21 110731" src="https://github.com/user-attachments/assets/a0c42152-d27f-4d78-8e26-9f22c6ed895e" />
<img width="1477" height="363" alt="Screenshot 2026-09-21 110807" src="https://github.com/user-attachments/assets/946bf9f7-26b9-4f05-a1d8-f36b46c1ae94" />
-Khi ở đầu hàm win(), địa chỉ stack vẫn đang chia hết cho 16, nhưng khi chạy tiếp và thực hiện `push rbp`, địa chỉ stack thay đổi và ko còn chia hết cho 16 nữa   
-Hướng giải quyết là ta có thể bỏ qua bước push rbp, và nhảy vào địa chỉ của lệnh phía sau đó là 0x40124e <win+0005> ->ko nhất thiết phải nhảy vào đầu hàm mà mình có thể nhảy vào vị trí khác để truy cập   
<img width="723" height="435" alt="Screenshot 2026-09-21 112152" src="https://github.com/user-attachments/assets/5da65ef5-0b15-47ed-8818-abfb10efaf73" />
<img width="1461" height="505" alt="Screenshot 2026-09-21 112011" src="https://github.com/user-attachments/assets/8a598529-981c-4c7d-894a-9112e6da83a2" />
-Lúc này ret đã nhảy đến win+0005, địa chỉ stack đã thỏa điều kiện và mình đã chiếm được shell

## BOF4 - ROPchain
-Return-Oriented Program chain là 1 **chuỗi các đoạn mã máy ngắn hợp lệ (gadget)** có sẵn trong bộ nhớ chương trình, được liên kết với nhau thông qua Stack 
-Gadget là 1 dãy lệnh ngắn, gồm vài lệnh thao tác dữ liệu và luôn kết thúc bằng `ret` 
<img width="662" height="217" alt="Screenshot 2026-09-22 205129" src="https://github.com/user-attachments/assets/28c48443-35b5-4b9e-b7b7-8f848425833e" />

-Ở BOF4, hàm main() và các hàm khác đều ko có hàm `system("/bin/sh")` để lấy shell, NX cũng đã được bật để tránh việc execute shellcode được chèn vào trên Stack. Nhưng chương trình vẫn chạy lệnh trên vùng `.text`(thường mặc định là có quyền RX), mình có thể tận dụng các lệnh có sẵn để gọi hệ thống và tạo shell.   
<img width="1485" height="590" alt="Screenshot 2026-09-22 203906" src="https://github.com/user-attachments/assets/031c46fc-acb6-40e6-b090-9e106c7f7349" />
<img width="947" height="480" alt="Screenshot 2026-09-23 205413" src="https://github.com/user-attachments/assets/99196ce3-2761-41ef-8b43-9368ec6e12d3" />

-ROPgadget --binary bof4 | grep "instruction" giúp mình tìm 1 instruction cụ thể   
+ rdi chứa arg1
+ rsi chứa arg2
+ rdx chứa arg3
+ rax lưu số syscall
+ syscall để gọi hệ thống   
-Mục tiêu là thực thi hàm `system("/bin/sh")` để lấy shell, nhưng vì gadget là 1 chuỗi assembly, đồng nghĩa là mình phải tạo shell bằng assembly. Mà assembly ko có hàm system(), file cũng đang là static linking nên cx ko có libc để dùng system. Nên mình có thể sử dụng `execve` với các parameter `execve("/bin/sh", args, env)`, và mình cần phải thiết lập RDI thành 1 con trỏ chuỗi để trỏ đến chuỗi "/bin/sh"
<img width="512" height="55" alt="Screenshot 2026-09-23 213123" src="https://github.com/user-attachments/assets/f18f95d0-4488-4cbc-9e9a-2c9e35b7f746" />

-Chuỗi /bin/sh vẫn chưa có sẵn để sử dụng nên bắt buộc mình phải tạo bằng cách ghi chuỗi đó vào 1 địa chỉ có quyền Đọc/Ghi,    
-Mình gọi hàm `get` để nhập chuỗi "/bin/sh" vào địa chỉ rw_section = 0x406e0 


 <img width="605" height="525" alt="Screenshot 2026-09-23 215219" src="https://github.com/user-attachments/assets/cefd2bd6-6cd4-4cd2-b8f8-00d7355f05be" />
-Payload đầu tiên `p64(pop_rdi)` phải nối sau thêm 1 địa chỉ vì `pop rdi` sẽ lấy 8 byte tiếp theo trên đỉnh Stack nạp vào thanh ghi, còn `ret` sẽ lấy 8 byte kế tiếp trên Stack làm địa chỉ nhảy đến tiếp theo. Và mình cần 1 địa chỉ **tĩnh** để nối vào 

<img width="1173" height="706" alt="Screenshot 2026-09-24 170743" src="https://github.com/user-attachments/assets/927025d3-f0be-4e31-9e8f-c7ccedf71909" />

**GIẢI THÍCH SCRIPT**   
- Ghi chuỗi /bin/sh   
`payload = b'A'`dùng để lấp đầy buffer `v8` 80 byte và saved-RBP 8 byte để trỏ tới Return address       
`payload += p64(pop_rdi) + p64(rw_section)` đưa địa chỉ `rw_section` vào thanh ghi RDI (tham số thứ 1 của hàm`gets`)      
`payload += p64(exe.sỵm['gets']) sẽ gọi hàm `gets`, khi hàm chạy thì nó sẽ chờ nhận đầu vào và ghi thẳng vào địa chỉ đang nằm trong RDI là `rw_section`   
- Thực thi execve   
`payload += p64(pop_rdi) + p64(rw_section)` nạp lại địa chỉ `rw_section` đang chứa chuỗi `/bin/sh` cho RDI   
`payload += p64(pop_rsi) + p64(0)` Set RSI = 0   
`payload += p64(pop_rdx) + p64(0)` Set RDX = 0   
`payload += b'B'*0x28` vì trong chương trình có 1 lệnh `add rsp, 0x28` nên payload này để cộng thêm 0x28 byte vào và để nhảy đúng vào gadget tiếp theo   
`payload += p64(pop_rax) + p64(0x3b)` đặt RAX = 0x3b, 0x3b là mã syscall   
`payload += p64(syscall)` thực thi syscall   
- Mở shell   
`p.sendafter(b'something', payload)` chờ chuỗi "Say thomething: " rồi gửi payload   
`p.sendline('/bin/sh') gửi chuỗi `/bin/sh` để hàm `gets(rw_section)` lấy và ghi vào bộ nhớ   
 **LUỒNG THỰC THI**
  
