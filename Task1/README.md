
# BUFFER OVERFLOW
## Khái niệm
-**Buffer overflow** (Tràn bộ đệm) là một lỗi lập trình, xảy ra khi một **chương trình cố gắng lưu trữ lượng dữ liệu vượt quá dung lượng** cho phép của **vùng nhớ đệm** (buffer, là vùng nhớ được cấp phát tạm thời để chứa dữ liệu)  
-Nếu dữ liệu bị tràn, nó sẽ ghi đè lên lên các vùng nhớ liền kề, cụ thể là các ô nhớ địa chỉ byte tiếp theo    
-Việc kiểm soát được BOF có thể ghi đè địa chỉ trở về và dẫn đến 1 hàm mà mình muốn, hoặc ghi đè RIP để thực hiện instruction   
<img width="1600" height="900" alt="overflow-vulnerabilities-example-4" src="https://github.com/user-attachments/assets/e4aa5504-5ec2-4ce3-9347-aba2449483f4" />

### Vài trường hợp ví dụ mình tìm được: 
- Một hàm tự gọi chính nó liên tục mà không có điểm dừng, mà mỗi lần gọi thì sẽ thêm một vùng stack frame mới vào      
- Hoặc hàm gọi chồng nhau quá sâu(Hàm A gọi B, B gọi C, C gọi D,...) vượt quá giới hạn độ sâu của Stack      
- Hoặc là mình ghi đè vào return address nhưng không phải địa chỉ mà là chuỗi ký tự ABCDXYZ đi, thì chương trình sẽ gặp lỗi và crash vì chuỗi đó khôgn phải địa chỉ để nhảy vào 

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

<img width="3244" height="1280" alt="Gemini_Generated_Image_kohrmhkohrmhkohr" src="https://github.com/user-attachments/assets/56072766-d014-41e4-ace3-bf76c93834be" />


-Lúc này ret đã nhảy đến <win+0005>, địa chỉ stack đã thỏa điều kiện và mình đã chiếm được shell

## BOF4 - ROPchain
-Return-Oriented Program chain là 1 **chuỗi các đoạn mã máy ngắn hợp lệ (gadget)** có sẵn trong bộ nhớ chương trình, được liên kết với nhau thông qua Stack    
-Gadget là 1 dãy lệnh ngắn, gồm vài lệnh thao tác dữ liệu và luôn kết thúc bằng `ret`    
<br>
<img width="662" height="217" alt="Screenshot 2026-09-22 205129" src="https://github.com/user-attachments/assets/28c48443-35b5-4b9e-b7b7-8f848425833e" />

-Ở BOF4, hàm main() và các hàm khác đều ko có hàm `system("/bin/sh")` để lấy shell, NX cũng đã được bật để tránh việc execute shellcode được chèn vào trên Stack. Nhưng chương trình vẫn chạy lệnh trên vùng `.text`(thường mặc định là có quyền RX), mình có thể tận dụng các lệnh có sẵn để gọi hệ thống và tạo shell.   

<img width="3244" height="1280" alt="Gemini_Generated_Image_kohrmhkohrmhkohr" src="https://github.com/user-attachments/assets/6b1189e4-2a14-45f7-a4b9-8945f0467389" />


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
Trước khi ghi đè

<img width="1550" height="577" alt="Screenshot 2026-09-24 190651" src="https://github.com/user-attachments/assets/7dfdd94d-49bb-4496-85cb-8fda6002ce46" />

Sau khi ghi đè   
<img width="1507" height="770" alt="Screenshot 2026-09-24 191804" src="https://github.com/user-attachments/assets/91718025-f226-4e01-99fc-9a22ff5fa589" />

## BOF5 - Ret2shellcode no leak 
-Ret2shellcode là kỹ thuật chèn các đoạn mã máy (shellcode) vào chương trình, sau đó ghi đè địa chỉ trở về trên Stack, ép CPU nhảy đến và thực thi các đoạn shellcode đó (thường là mở /bin/sh hoặc lệnh hệ thống)    
-Nhưng kỹ thuật này cũng có thể bị hạn chế nếu cơ chế NX được bật 

<img width="850" height="316" alt="Screenshot 2026-09-26 121005" src="https://github.com/user-attachments/assets/9ab02000-66e5-46db-87af-aa4e42b98214" />


<img width="772" height="456" alt="Screenshot 2026-09-26 121037" src="https://github.com/user-attachments/assets/f2274d7a-54e8-4954-a476-9ffbe29e0eea" />

-Lỗi bof lần này xuất hiện trong hàm run(), biến v2 được khai báo 524 byte nhưng cho phép đọc đến 544 byte    
-Để thực thi được shellcode trong chương trình, mình phải cho ret nhảy đến 1 con trỏ đang trỏ vào shellcode    

<img width="1613" height="548" alt="Screenshot 2026-09-26 173656" src="https://github.com/user-attachments/assets/140ea414-525a-4ea1-8047-3affa1b1ec88" />

-Dùng objdump -d file để xem   
-Mỗi câu lệnh assembly sẽ tương ứng với những byte cố định, và dãy byte đó là shellcode   

<img width="1272" height="735" alt="Screenshot 2026-09-26 174952" src="https://github.com/user-attachments/assets/acada632-b7cf-4d4b-890e-9d7b74055df4" />

-Mình cũng có thể dùng cách này để in ra trực tiếp shellcode lúc chạy file   
-Ở đây mình sẽ set RAX sẽ là thanh ghi trỏ tới shellcode, và mình tìm 1 gadget, mình sử dụng 'call_rax' 

<img width="1005" height="662" alt="Screenshot 2026-09-26 183944" src="https://github.com/user-attachments/assets/427d65b2-47a5-4dc9-881a-a339d0b22b50" />   

## BOF6 - Ret2shellcode cần Leak   



<img width="1525" height="732" alt="Screenshot 2026-09-28 160146" src="https://github.com/user-attachments/assets/56d06817-b860-4d21-8bbb-215c615c79ca" /> <br>


<img width="1917" height="776" alt="Screenshot 2026-09-28 194812" src="https://github.com/user-attachments/assets/1f91015a-01f4-4ace-a431-5f758522147f" /> <br>
<br>

-Có 1 điều đặc biệt ở hàm read(), nó sẽ chỉ đọc và ghi đúng số byte được nhập vào Stack mà không thêm `/0`(Null byte) vào cuối để kết thúc chuỗi như `scanf` hay `fgets`   
-Nếu không có null byte, việc sử dụng `x/s`(examine string) để in chuỗi byte từ 1 địa chỉ sẽ làm nối theo những byte ở địa chỉ tiếp theo cho đến khi gặp null byte (0x00)   
<br>
<img width="1912" height="678" alt="Screenshot 2026-09-28 202322" src="https://github.com/user-attachments/assets/15c17358-333a-45b1-b613-b243e03bbfd2" /> <br>
<br>
-Mình đã nhập vào 1 chuỗi 123456789123456\n gồm 16 byte (/n là enter, được tính là 1 byte), các byte địa chỉ phía dưới đã bị ghi đè. Nhưng quan trọng hơn, khi mình x/s 0x00007fffffffdf20 thì không chỉ in ra 8 byte đầu là 12345678 mà còn in ra những byte ở địa chỉ phía dưới đến khi gặp byte 0x00 thì dừng      
-> Đây cũng là 1 cách để leak địa chỉ   
<br>
<img width="1720" height="927" alt="Screenshot 2026-09-29 120403" src="https://github.com/user-attachments/assets/77e74d20-be54-45a4-bee8-7ab2c4111a0d" /> <br>
<br>
-Những địa chỉ Stack này thông thường là những địa chỉ không xác định, nên việc lấy địa chỉ này khá rủi ro. Do vùng nằm ở đỉnh Stack có thể thay đổi chiều dài khác đi khi chạy chương trình thật so với khi chạy trên GDB, hoặc có trường hợp cả dữ liệu buffer bị set hết về null byte   
<br>
<img width="1721" height="925" alt="Screenshot 2026-09-29 122717" src="https://github.com/user-attachments/assets/be84b5c7-1097-4b28-9003-3223e9b24b82" /> <br>
<br>
-Nên lấy địa chỉ Stack tại saved-RBP, vì khoảng cách từ buffer được nhập đến saved-RBP luôn là 1 con số cố định. Như thế sẽ đảm bảo được sự ổn định     <br>
<br>
<img width="1812" height="1016" alt="Screenshot 2026-09-29 164742" src="https://github.com/user-attachments/assets/9f4f3169-b671-482a-9fa7-f85f58f7c156" />

<br>

<br>
<img width="1917" height="925" alt="Screenshot 2026-09-29 163247" src="https://github.com/user-attachments/assets/54b2e99c-fe40-4cdb-aa39-605735a1b8a3" />
<br> <br>

-Mình đã kiểm tra và đúng shellcode mình gửi, mình sẽ lấy địa chỉ Stack leak được trừ đi địa chỉ trỏ vào shellcode, khoảng cách là 0x220 byte. Đồng nghĩa với việc lấy leaked stack address trừ đi 0x220 byte sẽ ra được **địa chỉ đang trỏ vào shellcode**   
<br>
<img width="1917" height="528" alt="Screenshot 2026-09-29 164528" src="https://github.com/user-attachments/assets/766d5980-3718-4ffa-a6a0-1c0d2dd0a84f" />
<img width="1621" height="913" alt="Screenshot 2026-09-29 165051" src="https://github.com/user-attachments/assets/4b78ffa7-b0c8-48a3-a7e6-5c3979c698ec" />
<br> <br>
-Vì `ljust` đã ghi đè lố 16 byte nên mình phải trừ đi để dời địa chỉ leaked stack lên và địa chỉ mới lúc đó sẽ là `0x00007ffeab9cc290 - 0x220`

## BOF7 - Ret2libc
-Ret2libc là 1 kỹ thuật chuyển hướng luồng thực thi của chương trình sang các hàm có sẵn trong thư viện C (libc) và thực hiện mã độc, thay vì tự chèn shellcode trên Stack  
-Thay vì tự viết shellcode, mình có thể tận dụng những hàm thực thi có sẵn trong libc ví dụ như hàm system() để thực thi /bin/sh 

<br>
<img width="1200" height="357" alt="Screenshot 2026-10-01 131648" src="https://github.com/user-attachments/assets/c7661e5b-ecb0-4964-a9bd-e6eebbad9ae4" />
<br>
<img width="1917" height="1042" alt="Screenshot 2026-10-01 153933" src="https://github.com/user-attachments/assets/daaf4a66-e2be-4746-86a2-75f038ddf9f6" />

-Mình cần phải leak địa chỉ Libc để thực thi được hàm system 
<img width="1917" height="638" alt="Screenshot 2026-10-01 155733" src="https://github.com/user-attachments/assets/6d1d8d80-7378-4ec0-8c91-4e8502a0e034" />

-Thường các địa chỉ libc đều là địa chỉ động, còn địa chỉ hiển thị trong gdb chỉ là địa chỉ tĩnh

<img width="1912" height="667" alt="Screenshot 2026-10-02 174655" src="https://github.com/user-attachments/assets/8383f88c-542a-4eba-8ccd-eefa84a0568e" />

-Đối với hàm puts(), mình chỉ cần 1 tham số là có thể in dữ liệu của địa chỉ trỏ ra, như trong ảnh nếu mình đưa địa chỉ trỏ của rsi xuống rdi (tham số đầu tiên) thì puts() sẽ in ra dữ liệu mà địa chỉ đó đang trỏ tới là "aaa....."

### GOT - Global offset table
-Nơi chứa **địa chỉ các hàm** của libc, nằm ở vùng nhớ dữ liệu `.got`/`.got.plt` 

### PLT - Procedure linkage table 
-Thực thi hàm được chứa ở trong GOT, nằm ở vùng nhớ chứa mã lệnh (`.text`/`.plt`). Ví dụ hàm puts() có địa chỉ là `0x7ffff7c8e860` thì PLT sẽ thực thi địa chỉ hàm puts() đó. Và địa chỉ `0x403fd8` (GOT Entry/`puts@got.plt`) là địa chỉ ô nhớ thuộc bảng GOT, dùng để lưu giá trị con trỏ trỏ đến `0x7ffff7c8e860` 

<img width="813" height="55" alt="Screenshot 2026-10-05 145507" src="https://github.com/user-attachments/assets/070a4715-033b-450b-94f0-57494279243d" />

-Mình dùng gadget pop rdi để trỏ tới địa chỉ thực thi hàm puts()    

<img width="973" height="502" alt="Screenshot 2026-10-05 163856" src="https://github.com/user-attachments/assets/04317220-16f9-426b-9e6c-2e6136716dad" />

-Vì hiện tại mình đang chạy trên máy nên libc hiện tại là của local nên có thể sẽ khác địa chỉ với libc của server, nên lúc này mình cần tìm libc server 

 
-Đầu tiên mình mình tải docker theo hướng dẫn của Gemini, sau đó build docker:   
```
docker build . -t bof7
```
-Chạy docker tạo container:
```
sudo docker run -d -p 9993:9993 --name bof7_container bof7

```
-Dừng và xóa container:
```
sudo docker stop bof7_container && sudo docker rm bof7_container

```

<img width="432" height="52" alt="Screenshot 2026-10-05 173649" src="https://github.com/user-attachments/assets/79c43ec8-0239-4bff-b478-5ffc790d2196" />

-Mình dùng lệnh p=remote(ip,port) để chuyển từ local sang server. Lúc này địa chỉ leak được sẽ là của server   
-Sau khi mình chạy lại file thì địa chỉ libc leak được đã thay đổi sang của server. 

<img width="1392" height="573" alt="Screenshot 2026-10-05 181623" src="https://github.com/user-attachments/assets/5f29b49e-351d-4b94-a982-009d858c37f8" />

-Do offset mỗi hàm thay đổi theo phiên bản libc, đồng thời có thể libc trên máy mình đang dùng sẽ khác với libc của server mình kết nối đến. Mình truy cập libc.rip, paste địa chỉ vào và tìm phiên bản libc phù hợp, vì có rất nhiều phiên bản khớp với 3 byte cuối của địa chỉ nên mình bắt buộc phải thử từng phiên bản libc một.   

<img width="1055" height="767" alt="Screenshot 2026-10-08 132302" src="https://github.com/user-attachments/assets/84a8b97c-45e9-49e3-9247-6f50fc96b6a1" />

### Script
Stage 1   
`payload=b'A'*88` lấp đầy đến vị trí Return address   
`payload += p64(pop_rdi) + p64(exe.sym[puts]` đặt địa chỉ của gadget pop rdi vào và cung cấp giá trị tham số là địa chỉ GOT của hàm puts() cho rdi   
`payload += p64(exe.plt['puts']` ngay sau lệnh pop rdi, gadget thực hiện ret và nhảy đến puts@plt. Hàm puts() sẽ nhận giá trị trong rdi (địa chỉ GOT) làm tham số và in nội dung địa chỉ đó   
`payload += p64(exe.sym['main'])` Khi hàm puts() hoàn thành và ret, nó sẽ nhảy về địa chỉ của hàm main() để chạy lại chương trình  
Stage 2   
`payload += b'A'*88` ghi đè lại buffer đến saved-rbp  
`payload += p64(pop_rdi) + p64( next(libc.search(b'/bin/sh')))` đưa địa chỉ gadget pop rdi vào retrun address và lấy địa chỉ chuỗi /bin/sh nạp vào rdi   
`payload += p64(libc.sym['system'])` lệnh ret nhảy thẳng vào hàm system trong libc, rdi đã chứa chuỗi /bin/sh nên có thể thực thi được `system("/bin/sh")`   

## BOF8 - Stack Pivot   
-Stack Pivot là kỹ thuật biến đổi (pivot) giá trị của thanh ghi con trỏ ngăn xếp (RSP) để trỏ đến 1 vùng nhớ khác có quyền READ-WRITE (.BSS, Heap) do mình kiểm soát, mình sẽ biến vùng đó thành 1 vùng **Stack giả(Fake Stack)**    
-Stack Pivot thường được sử dụng khi vùng Stack thật không đủ để chứa toàn bộ ROPchain. Vì kích thước của buffer thường giới hạn số byte mình có thể ghi đè sau địa chỉ trả về (Saved RET)   
-Thông thường khi ghi đè đến Saved-RBP sẽ không có vấn đề gì, nhưng trong BOF8 này sẽ có những trường hợp đặc biệt xảy ra    

<img width="1062" height="662" alt="Screenshot 2026-10-11 150714" src="https://github.com/user-attachments/assets/0cbe17f2-dcf9-4a7b-a4b5-0b513c5616c4" />

-Ở đây hàm win() chứa lệnh thực hiện tạo shell được gán vào 1 con trỏ, mình chỉ cần chuyển hướng đến vùng nhớ chứa hàm win() để thực thi   

<img width="1046" height="410" alt="Screenshot 2026-10-11 150745" src="https://github.com/user-attachments/assets/589cd54b-157c-4aed-bafe-3b395f7351b4" />

-Lỗi Bof lần này xuất hiện ở hàm buy()   

<img width="532" height="31" alt="Screenshot 2026-10-11 152747" src="https://github.com/user-attachments/assets/799bb20f-2e63-484d-96d0-2a80d1ec974e" />

-Sau khi ghi đè thì chương trình xảy ra SIGBUS, lúc này mình cần kiểm tra ngay lại điểm mà chương trình gặp lỗi, mình xem thanh ghi và thấy là RBP đã bị ghi đè và giá trị này không hợp lệ để thực thi. 

<img width="805" height="30" alt="Screenshot 2026-10-11 153243" src="https://github.com/user-attachments/assets/d21f5b1e-442a-4415-a66f-4c5c3c836448" />

-Địa chỉ binary lần này là địa chỉ tĩnh nên mình có thể dùng địa chỉ đó thay vào RBP để chạy bình thường   

<img width="867" height="70" alt="Screenshot 2026-10-11 154413" src="https://github.com/user-attachments/assets/b9495df0-5d4b-490a-ad3e-43cd1052b7ca" />

-Offset là 32 byte 

<img width="952" height="27" alt="Screenshot 2026-10-11 154614" src="https://github.com/user-attachments/assets/c73bb1c6-ab8c-4178-b168-29e37b745d86" />

-Mình tìm và chọn địa chỉ có quyền RW rồi đưa vào payload, tuy nhiên ở đây <img width="600" height="20" alt="Screenshot 2026-10-11 153243" src="https://github.com/user-attachments/assets/64629445-0282-4b20-9ada-f270b3c37296" /> là RBP-0x2 nên nếu copy thẳng địa chỉ vào sẽ bị lùi lại địa chỉ phía trên nên mình phải tăng địa chỉ `0x0000000000404000` lên `0x0000000000404a00`
 
<img width="635" height="22" alt="Screenshot 2026-10-11 163200" src="https://github.com/user-attachments/assets/54d30279-4742-4d88-83cc-9082d5d7c31d" />

-RBP đã được thay đổi, mình sẽ đặt break point tại hàm read() của hàm buy() để xem sự thay đổi  

### Lệnh leave   
-Như mình đã biết khi 1 hàm con được gọi thì nó sẽ `push rbp` để lưu RBP hàm cha và `mov rbp, rsp` để tạo RBP mới cho hàm con.   
-Lệnh leave là để dọn dẹp nhanh biến cục bộ trong stackframe khi kết thúc hàm. Leave sẽ thực thi `mov rsp, rbp` để kéo con trỏ đầu Stack(RSP) về lại vị trí của saved-RBP, sau đó `pop rbp` để khôi phục RBP thành saved-RBP của hàm cha.   

<img width="1796" height="877" alt="image" src="https://github.com/user-attachments/assets/90b3d5b5-2c80-42b5-8da7-6b98735c2dbd" />

<img width="976" height="170" alt="Screenshot 2026-10-11 211729" src="https://github.com/user-attachments/assets/72262e58-2bc4-437b-a07e-2040bb415358" />


<img width="482" height="52" alt="Screenshot 2026-10-11 211719" src="https://github.com/user-attachments/assets/4d9b9fac-6c08-4de5-a364-9393d7893571" />

-Vì ở trên, hàm win() được gán vào 1 con trỏ nên mình dùng `x/xg50` để kiểm tra và thấy tại ô nhớ 0x404850 đang chứa giá trị 0x0000000000401366 là con trỏ trỏ tới địa chỉ hàm win(). Mình đã `x/xg` và tìm được địa chỉ hàm win()   

### Cách khai thác 
-Mình có thể ghi đè địa chỉ vùng nhớ mà mình muốn chuyển hướng đến vào rbp thì khi leave, `mov rsp, rbp` sẽ kéo rsp theo đến vùng nhớ đó. Nhưng `pop rbp` sẽ làm lùi rsp xuống 8 byte mình cần lấy địa chỉ rbp trừ đi 8 byte. (`ret` cũng sẽ đẩy rsp lùi xuống 8 byte)   






## BOF9 - Stack Pivot thay đổi biến 
<img width="421" height="119" alt="Screenshot 2026-10-24 181736" src="https://github.com/user-attachments/assets/2f4912d1-1e05-4c96-9f43-74110ccea3ae" />
-Hàm printf() trong chương trình này sẽ in ra địa chỉ của v4, mình có thể lợi dụng để leak địa chỉ Stack
<img width="612" height="27" alt="Screenshot 2026-10-25 113010" src="https://github.com/user-attachments/assets/f440055e-c658-402a-b004-3e0131062553" />

-Mình đã ghi đè 2 byte vào địa chỉ rbp thông qua hàm read() username. Lúc này rbp đã bị thay đổi và khi kết thúc hàm get_credencial(), nó đã nhảy vào 1 vùng khác và không còn trỏ vào giá trị ở hàm main()

<img width="612" height="32" alt="Screenshot 2026-10-25 112354" src="https://github.com/user-attachments/assets/e44932f6-7dcd-4849-80ec-db6e688fb3f2" />

<img width="1917" height="675" alt="Screenshot 2026-10-25 112422" src="https://github.com/user-attachments/assets/a4274640-14a2-42b8-a6e3-7ee7004cdf76" />

-Chính vì thế mình có thể lợi dụng việc ghi đè rbp để trỏ đến 1 vùng khác chứa đúng giá trị của biến v4 để tạo shell. Mình sẽ tạo 1 mảng fake để rbp-0x20 nhảy đến. 


<img width="1757" height="637" alt="Screenshot 2026-10-25 125539" src="https://github.com/user-attachments/assets/6800c4dc-27be-452c-bd4b-ba1255515a28" />


<img width="447" height="38" alt="Screenshot 2026-10-25 125517" src="https://github.com/user-attachments/assets/5cacc818-e833-4828-94bc-075fd8ea9eaf" />

-Mình chạy lại chương trình và tính khoảng cách từ hàm read() của username cho đến địa chỉ Stack leak được.  
-Vì mình muốn tạo 1 mảng fake trong username, nhưng nếu mình ghi địa chỉ saved-rbp thành 0xdf10 thì khi nhảy về hàm main, rbp sẽ trừ 0x20 và khiến cho nó nhảy về 0xdef0 là địa chỉ cuẩ password.   
->Mình phải trỏ vào địa chỉ lớn hơn username 0x20 để khi rbp trừ đi nó sẽ nhảy đúng vào username.   
 <img width="517" height="56" alt="Screenshot 2026-10-25 131034" src="https://github.com/user-attachments/assets/c0f18b44-3297-4799-9244-60ffa92e355b" />

<img width="1577" height="285" alt="Screenshot 2026-10-25 170950" src="https://github.com/user-attachments/assets/4e945f9c-2e42-4609-a3c8-496aa9cc622e" />

-Mình đã ghi đè 24 byte cần thiết, nhưng do password khai báo 32 byte nên mình cần nhập thêm 8 byte rác(hoặc set null) nữa để  ghi đè được đến rbp. Nhưng hàm read() username chỉ đọc được tối đa 34 byte nên mình chỉ có thể ghi đè 2 byte vào rbp, nếu ghi lố sẽ xảy ra lỗi.    


<img width="412" height="47" alt="Screenshot 2026-10-25 175919" src="https://github.com/user-attachments/assets/63d5d478-a9dd-43e3-a029-6fb47f2e9fef" />

-Trên là 2 cách để nhập 2 byte

<img width="842" height="198" alt="Screenshot 2026-10-25 224244" src="https://github.com/user-attachments/assets/705efc26-a5d2-4902-8b15-bdc3ad858d17" />

<img width="488" height="49" alt="Screenshot 2026-10-25 224225" src="https://github.com/user-attachments/assets/e7cd330f-fbd7-451d-b90e-c0539b444360" />


<img width="876" height="32" alt="Screenshot 2026-10-25 224238" src="https://github.com/user-attachments/assets/09a010d8-1cd7-42b7-ade0-4db1754e35bc" />

-Khi chạy thì mình thấy rbp đã bị ghi đè đúng như mong muốn, địa chỉ của username khi cộng thêm 0x20 byte đã thành địa chỉ saved-rbp.   

<img width="585" height="64" alt="Screenshot 2026-10-25 224614" src="https://github.com/user-attachments/assets/4e45a2ea-f266-48f5-a9ca-9b9492ce8c7e" />

## BOF10 - Off by One
-Off-by-one là kỹ thuật mà mình chỉ ghi tràn được đúng 1 byte 

<img width="897" height="308" alt="Screenshot 2026-10-26 174026" src="https://github.com/user-attachments/assets/c41f3349-f9eb-46b2-ac6c-9afc52d7762d" />

-Hàm `fgets` cho phép mình nhập tới byte thứ n-1, tức trong chương trình này chỉ cho nhập đến 79 byte, byte thứ 80 sẽ thiết lập về null và không xảy ra Overflow
-Hàm printf in %p tức là địa chỉ của biến name, name là biến cục bộ nên địa chỉ được in ra cũng là địa chỉ Stack 

<img width="737" height="247" alt="Screenshot 2026-10-28 220658" src="https://github.com/user-attachments/assets/fa104616-5c68-481f-9af4-8f0cd7f9a0e8" />

-Trong playgame(), hàm scanf cho phép nhập 512 byte, bằng với biến s, nên cũng không xảy ra Buffer Overflow.   
-**NHƯNG**, hàm scanf() sẽ tự động thêm null byte vào cuối cùng, thế nên nếu mình nhâp vào 512 byte, thì byte thứ 513 sẽ là null byte -> Lỗi   
-Có điều byte null thì mình cũng không kiểm soát được do nó được scanf() thêm vào 

<img width="837" height="35" alt="Screenshot 2026-10-28 230041" src="https://github.com/user-attachments/assets/98e9516d-a534-45ae-a025-f512dbcc24d4" />

<img width="936" height="45" alt="Screenshot 2026-10-28 230123" src="https://github.com/user-attachments/assets/83b06d25-49a7-4145-958f-5829fc7fcb7f" />

-Khi nhập đủ 512 byte thì null byte đã ghi vào byte cuối của rbp, nên khi return thì chương trình sẽ lỗi. Nhưng vì đây là debug tĩnh nên mới lỗi. Còn khi debug động sẽ khác 
```
set disable-randomiztion off
```
-Lệnh này dùng để bật lại cơ chế xáo trộn bộ nhớ (ASLR), do bình thường xài gdb thì ASLR thường sẽ bị tắt, ví dụ để giúp dễ đặt breakpoint 

<img width="1917" height="237" alt="Screenshot 2026-10-28 235454" src="https://github.com/user-attachments/assets/c6acdca3-99c6-4288-8236-9bd37b3c0e08" />

-Do lúc đầu tắt ASLR nên khi ghi đè rbp, nó nhảy cố định vô 1 chỗ duy nhất, chỗ mình nhảy vô là biến môi trường. 

<img width="1917" height="626" alt="Screenshot 2026-10-29 003605" src="https://github.com/user-attachments/assets/3e228367-7c1b-47f3-8118-84a3fb43d82c" />

-Sau khi bật ASLR thì địa chỉ Stack luôn thay đổi, nên sau mỗi lần chạy, địa chỉ rbp nhảy vào cũng luôn thay đổi
<br>
<br>
-Bây giờ, NX tắt và mình muốn viết shellcode. Mà thứ mình cần làm là tính offset từ địa chỉ Stack đã được leak sẵn và cho rbp nhảy vào shellcode. Nhưng vấn đề là mình chỉ ghi đè được đúng 1 null byte thôi, đồng nghĩa với việc địa chỉ sẽ là ngẫu nhiên, nên lúc này mình phải Brute-force

<img width="885" height="187" alt="Screenshot 2026-10-29 111429" src="https://github.com/user-attachments/assets/109566db-f104-40a6-b9f3-4a5125deee99" />
<img width="1158" height="530" alt="Screenshot 2026-10-29 114353" src="https://github.com/user-attachments/assets/5514deda-ef3c-44ef-85ea-69179bffd542" />

-Mình lấy chuỗi /bin/sh ở dạng int rồi viết shellcode
-Có điều bây giờ mình phải nhâp dữ liệu thế nào, bởi vì địa chỉ rbp nhảy vô là ngẫu nhiên, nên trong đống buffer mình nhập vô, nó có thể nhảy vào giữa, vào đầu, vào cuối, hoặc ra ngoài buffer. Thế nên mình sẽ dùng **nod sled**
<br>
<br>
=> Tức là mình sẽ ghi vô buffer 1 đống lệnh `ret`, khi mà nó chạy dọc buffer thì nó cứ return mãi cho đến khi gặp đúng cái lệnh `ret` nhảy tới con trỏ trỏ tới shellcode thì thôi 

<img width="276" height="110" alt="Screenshot 2026-10-29 121311" src="https://github.com/user-attachments/assets/1c78086b-ec03-4afc-aabd-6327d312dd72" />

<img width="515" height="182" alt="Screenshot 2026-10-29 121547" src="https://github.com/user-attachments/assets/84c6722a-fe3c-4111-a1c0-22b49dbbf62a" />

-Mình disas main và lấy địa chỉ return, vì có tận 512 byte buffer nên mình cứ cho nó return tầm 0x180/8 lần. Còn địa chỉ trỏ tới shellcode thì mình chưa biết nên cứ đặt tạm cho dễ nhận biết.   

<img width="647" height="270" alt="Screenshot 2026-10-29 135835" src="https://github.com/user-attachments/assets/a1869dc5-47db-414f-8152-f928b2b7f855" />

<img width="331" height="32" alt="Screenshot 2026-10-29 140101" src="https://github.com/user-attachments/assets/89d1d1a1-283a-4e26-b047-b516c30dc033" />

<img width="552" height="70" alt="Screenshot 2026-10-29 140211" src="https://github.com/user-attachments/assets/a53e8b4a-9f62-4469-96b5-d8aad76c701f" />



-Khi chạy thì nó cứ return cho tới khi gặp địa chỉ trỏ tới shellcode, shellcode nó nằm ngay phía dưới 
-Địa chỉ Stack đã leak ra được cao hơn địa chỉ shellcode nên mình tính toán offset ra được 0x88 byte 


<img width="672" height="752" alt="Screenshot 2026-10-29 144935" src="https://github.com/user-attachments/assets/52c8c0da-243c-48de-84f4-2762dfd8a0d5" />

<img width="933" height="487" alt="Screenshot 2026-10-29 144133" src="https://github.com/user-attachments/assets/11a56b1e-da04-4dd6-b894-0c635fdf31d9" />

-Xong Script rồi thì mình chỉ cần brute-force(Chạy lại chương trình liên tục) cho đến khi gặp đúng cái lệnh ret nhảy đến địa chỉ trỏ tới shellcode của mình là được

<img width="628" height="165" alt="Screenshot 2026-10-29 150004" src="https://github.com/user-attachments/assets/9852d2c6-2be4-4d6a-a96d-7947583e9559" />







