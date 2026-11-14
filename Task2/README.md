# FORMAT STRING
## Khái niệm 
Source: https://youtu.be/b8GMf5kM2LU?si=CJeDrYdz5ugZhW46  
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
Source: https://youtu.be/Vkv3s-_DYnM?si=tvlficM9LUUChxON  

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
Source: https://youtu.be/jsnxpBg2y28?si=3OtvWY0qnObCns0x  

<img width="1687" height="832" alt="Screenshot 2026-11-09 175239" src="https://github.com/user-attachments/assets/47622486-b404-42b4-ad06-2a8fa7269e46" />

- Vì flag lần này được read vào buf nằm ở vùng Heap chứ không phải Stack, thứ nằm trên Stack sẽ là địa chỉ vùng Heap đang chứa flag, nên nếu dùng %p thì thứ mình leak được là địa chỉ heap chứ không phải flag.

<img width="1408" height="638" alt="Screenshot 2026-11-09 180240" src="https://github.com/user-attachments/assets/9600add2-a33f-4400-914d-d0a6b87a140a" />

- Sử dụng %s thay vì in ra dữ liệu là địa chỉ heap, thì sẽ sẽ truy cập vào địa chỉ đó và in ra dữ liệu trong đó (hiểu đơn giản %p in ra cha, %s in ra con) 

<img width="546" height="180" alt="Screenshot 2026-11-09 181134" src="https://github.com/user-attachments/assets/abe63c0c-e740-4533-b1e4-e2c1ea01e0e4" />

<img width="970" height="153" alt="Screenshot 2026-11-09 181209" src="https://github.com/user-attachments/assets/ed633d6b-cd87-4594-9cf4-6414bd34f368" />

- Vì %s in ra đến khi nào gặp null byte, nên việc flag không có null byte bên trong thì khá dễ để leak vì nó in ra hết được 
- Nhập thẳng trong lúc chạy chương trình hoặc trên gdb sẽ nhanh hơn thay vì viết Script cho bài này
## FMTSTR 3 - Leak dữ liệu kết hợp %p và %s
Source: https://youtu.be/WXBnSjwfcBw?si=od5CRzN7H9gGwgV_
<img width="1457" height="652" alt="Screenshot 2026-11-10 153316" src="https://github.com/user-attachments/assets/5b2fc771-d4ae-4544-a367-8af01f0fbf1e" />
<img width="486" height="246" alt="Screenshot 2026-11-10 153731" src="https://github.com/user-attachments/assets/5932c295-ba1d-4ba5-a856-07a32b4c6cc7" />

- Trong bài này, flag được đọc 1 nửa vào con trỏ flag1 và nửa còn lại vào global var flag2.
- Có 2 lỗi Format String và không có BOF
  
<img width="1192" height="492" alt="Screenshot 2026-11-10 160729" src="https://github.com/user-attachments/assets/2142ae33-d6b3-4ee5-9dd1-3a93521c9763" />
<img width="288" height="70" alt="Screenshot 2026-11-10 161116" src="https://github.com/user-attachments/assets/d4fade8c-e741-4be1-a20f-660dcbabbb10" />

- Nửa đầu flag thì mình còn có thể dùng %s để leak do nằm trên Stack. Do PIE đang bật nên nửa còn lại thì buộc minh phải leak địa chỉ binary.

<img width="1831" height="637" alt="Screenshot 2026-11-10 163124" src="https://github.com/user-attachments/assets/042e9a69-2bbe-4822-b300-8259364d331d" />

- Mình có 1 địa chỉ binary của chương trình nằm ở saved-rip, mình có thể tel và đếm. Hoặc có cách tính nhanh:
```
(Địa chỉ muốn lấy - địa chỉ rsp)/8 + 6
```

<img width="847" height="357" alt="Screenshot 2026-11-10 185455" src="https://github.com/user-attachments/assets/f705584c-82f8-44d3-9305-e14f7d174670" />


<img width="1542" height="872" alt="Screenshot 2026-11-10 174058" src="https://github.com/user-attachments/assets/69fea46b-50e4-4ca8-b5c9-b78745a55bf2" />


- Mình sử dụng debug động để thao tác nhanh hơn thay vì attach thủ công
- Mình tính địa chỉ binary leak được trừ đi binary base và ra được offset của base (đang debug gdb nên dùng địa chỉ tĩnh cho dễ)
- Có được địa chỉ rồi thì mình chỉ cần tính offset từ base đến flag2, rồi lấy base cộng offset đấy là ra được địa chỉ chính xác của flag2

<img width="1172" height="692" alt="Screenshot 2026-11-10 182416" src="https://github.com/user-attachments/assets/632ef7c0-d2e9-44f6-9c41-35570f60f4f6" />

- Tại sao mình phải thêm 3 byte rác vào payload? Do lúc chạy có 1 vấn đề xảy ra

<img width="1482" height="832" alt="Screenshot 2026-11-10 183051" src="https://github.com/user-attachments/assets/88d40c7e-93fa-4c55-bf73-feb9ffcd62ce" />

- Địa chỉ của flag2 đang nằm không đúng vị trí, nói chính xác thì một nửa đang nằm ở trên, một nửa đang nằm ở dưới. Thứ nhất nó sẽ sai địa chỉ flag2 là điều chắc chắn, thứ 2 là %s cần 1 địa chỉ hợp lệ để trỏ tới, nhưng cả 2 cái đều không phải địa chỉ hợp lệ, chính vì thế khi gửi payload, mình phải kẹp thêm trước địa chỉ flag2 3 byte rác để địa chỉ flag2 nằm đúng vị trí

## FMTSTR 4 - Format String & Bof
- Soucre: https://youtu.be/Q0SPwsxW_-s?si=EeKamsD8MVKaOk54
<img width="1361" height="798" alt="Screenshot 2026-11-11 143141" src="https://github.com/user-attachments/assets/796cc1dd-2d19-43ed-9470-f2ea4a2ad8ed" />

<img width="703" height="271" alt="Screenshot 2026-11-11 143153" src="https://github.com/user-attachments/assets/2b26d54c-fcc1-41d1-8c77-57e6f30e3571" />

- Bài này có 2 lỗi Format String và Buffer Overflow. Lỗi Format String nằm ở biến ID, hàm stringcompare chỉ so sánh 11 byte đầu xem có giống không. Thế nên mình có thể tận dụng để leak địa chỉ Canary và thậm chí là địa chỉ binary

<img width="447" height="66" alt="Screenshot 2026-11-11 152252" src="https://github.com/user-attachments/assets/64ddecde-cb6f-42a2-8408-4d8481cce777" />

<img width="878" height="156" alt="Screenshot 2026-11-11 152219" src="https://github.com/user-attachments/assets/ec57352d-5f0a-40f5-aceb-9bf8d5aaf553" />

- Mình đã thành công Leak Canary và địa chỉ libc (mình định dùng ROPchain nhưng lúc ghi đè chỉ ghi được tới saved-rip là tối đa).

<img width="521" height="132" alt="Screenshot 2026-11-11 152533" src="https://github.com/user-attachments/assets/7bee0aff-b341-471c-9bef-f705e2d22fc5" />
<img width="1920" height="1080" alt="Screenshot (130)" src="https://github.com/user-attachments/assets/9e115f75-7eb4-4040-959b-e3cf1d5ad74b" />

- Lần recv thứ nhất sẽ nhận cho tới byte '9', nên lần recv thứ 2 sẽ nhận từ byte 0x của canary đến byte 'Enter ', mình bỏ Enter đi là được chuỗi canary và địa chỉ Libc
- Sau đó mình dùng split để chia ra, nó được chia ra thành 1 mảng có 3 phần tử
- Leak được địa chỉ libc rồi mình sẽ tính offser từ libc base tới địa chỉ đó. Rồi lấy địa chỉ đó trừ ngược lại là ra được chính xác libc.address

<img width="1527" height="615" alt="Screenshot 2026-11-11 175041" src="https://github.com/user-attachments/assets/631dd1cd-7df8-48e8-9b83-270a9524c965" />


- Mình sẽ dùng 1 công cụ là one_gadget để tự động tìm trong libc các địa chỉ offset tạo shell
- Mỗi cái đều có điều kiện riêng, ví dụ cái đầu tiên con của r15 bằng null hoặc r15 bằng null và con của r12 bằng null hoặc r12 bằng null.

<img width="1541" height="832" alt="Screenshot 2026-11-11 172552" src="https://github.com/user-attachments/assets/83ef5abd-2647-43f6-8e1f-a2dc6d2c96dd" />

- Như mình check thì thấy điều kiện thỏa rồi. Còn để mà check con thì mình dùng x/xg. Nếu mà không thỏa điều kiện, mình gọi vẫn được nhưng nó sẽ không tạo shell

<img width="905" height="342" alt="Screenshot 2026-11-11 173545" src="https://github.com/user-attachments/assets/cdf00a87-e44b-4e04-bdd3-96b298daf46c" />
<img width="957" height="832" alt="Screenshot 2026-11-11 174101" src="https://github.com/user-attachments/assets/259ede75-e682-4de3-933a-908c5bd7b0ad" />
<img width="443" height="197" alt="Screenshot 2026-11-11 175339" src="https://github.com/user-attachments/assets/afc7ddad-d71d-4555-96cf-bccc2b3990db" />

- Mình tính offset từ buf đến rip, và xem khi return thì thanh ghi có thỏa điều kiện không. -> Có
- Nên lần payload mình cứ gửi byte rác đến canary thì ghi đè, byte rác cho rbp và ghi đè địa chỉ nhảy đến gadget vào rip. (Khi dùng one_gadget thì nó sẽ cho mình offset từ libc base đến gadget đó, do địa chỉ động nên mình phải cộng thêm địa chỉ libc base mới ra đúng địa chỉ one_gadget)

### Final Script
<img width="553" height="918" alt="Screenshot 2026-11-11 180021" src="https://github.com/user-attachments/assets/a659d48a-6c80-415b-b08c-bbe769a5e259" />

## FMTSTR 5 - Ghi đè dữ liệu bằng %n
<img width="720" height="457" alt="Screenshot 2026-11-12 101739" src="https://github.com/user-attachments/assets/77394502-6ae2-4846-8078-cb9a49fde7e7" />

- Trong bài này, mình chỉ cần ghi đè được biến check thành 0xdeadbeef là lấy được shell. Mình sẽ dùng %n, %n sẽ đếm số byyte được in ra trước nó và ghi đè giá trị mà con trỏ đang trỏ vào.

<img width="701" height="105" alt="Screenshot 2026-11-12 110915" src="https://github.com/user-attachments/assets/ca6d6a3b-9f4c-4368-89d3-8ce311084540" />

- Biến check hiện tại đang là 0, và quan trọng, địa chỉ check vẫn chưa có trên Stack, nên mình phải đưa địa chỉ check lên Stack. Sau đó chạy thử chương trình để xem chính xác địa chỉ biến check nằm ở vị trí nào rồi mới để %n trỏ vào 

<img width="432" height="102" alt="Screenshot 2026-11-12 114952" src="https://github.com/user-attachments/assets/e572b1c2-c9a0-48c3-97af-5dd66bf1d48e" />
<img width="952" height="126" alt="Screenshot 2026-11-12 115013" src="https://github.com/user-attachments/assets/651a9dea-1d00-4c43-b39b-beeb07085093" />

- Mình để %n trỏ tạm vào %6 và chạy thử chương trình, lúc này mình nhận thấy rằng địa chỉ check đang dính với %6$n, nó tạo thành 1 địa chỉ không hợp lệ để mà %n ghi vào. Thế nên mình cần tách địa chỉ check ra bằng cách thêm byte rác cho đủ 8 byte của %6$n.

<img width="452" height="121" alt="Screenshot 2026-11-12 115851" src="https://github.com/user-attachments/assets/162a6a39-1bd8-41e5-b12d-1530c1961e23" />

<img width="888" height="150" alt="Screenshot 2026-11-12 115903" src="https://github.com/user-attachments/assets/81973654-b9c5-4452-8af9-7e74d371881f" />

- Địa chỉ biến check đã được tách ra và gdb cũng nhận biết được địa chỉ biến check
- Nhưng mục tiêu của mình là phải ghi đè 0xdeadbeef vào check, mình sẽ sử dụng %c

<img width="536" height="128" alt="Screenshot 2026-11-12 123849" src="https://github.com/user-attachments/assets/ef031d38-55b7-4e61-80b7-882a78ab80b7" />


- Mình sẽ dùng f để đưa 0xdeadbeef vào.
- Mình cần phải chừa thêm 1 khoảng dành cho Format String, vì Format String của mình có thể sẽ thay đổi tiếp, có thể lùi ra lùi vào và làm thay đổi địa chỉ check
- Mình chừa ra 0x20 byte rồi địa check nằm ở đâu thì mình thay đổi padding là được. -> đảm bảo được địa chỉ check vẫn giữ nguyên trên 1 hàng

<img width="835" height="848" alt="Screenshot 2026-11-12 124200" src="https://github.com/user-attachments/assets/ddf73b6d-6411-4996-9b85-e9d340d4ad8f" />

<img width="431" height="160" alt="Screenshot 2026-11-12 131202" src="https://github.com/user-attachments/assets/87dbdd71-0d54-4ff2-acaa-6cb83d9bf9fa" />

<img width="921" height="206" alt="Screenshot 2026-11-12 131253" src="https://github.com/user-attachments/assets/d9bc3905-639d-49d6-8091-5d7ccad57476" />

- Nhưng vì {0xdeadbeef} quá lớn nên printf không xử lý được, nên mình sẽ in trước 2 byte {0xbeef} 
- Giờ mình sẽ nối chuỗi còn lại {0xdead} vào trong địa chỉ check

<img width="536" height="157" alt="Screenshot 2026-11-12 131156" src="https://github.com/user-attachments/assets/421e5f19-a5bf-461a-ae9d-f01a8f6161d0" />

<img width="557" height="205" alt="Screenshot 2026-11-12 133206" src="https://github.com/user-attachments/assets/ee4d1fce-5a12-4b4b-95d6-ba77145dc4f2" />


- Để ghi {0xdead} vào thì mình phải cộng địa chỉ lên 2 byte, khi cộng lên nó sẽ trỏ đúng vị trí để %n ghi đè vào
- Còn 1 vấn đề nữa cần lưu ý, %n sẽ ghi vào số byte nó đếm được khi in ra trước nó, mà trước đó mình đã in ra 0xbeef rồi thì tức là lần này nếu ghi mỗi f'%{0xdead}%11$n' thôi thì nó ghi đếm luôn cả 0xbeef và 0xdead cộng vào rồi ghi vô. Nên mình cần trừ đi 0xbeef trước đó đã ghi để %n ghi chính xác vào (hoặc mình có thể dùng %hn để chỉ định ghi đúng số byte) 

### Final Script
<img width="675" height="577" alt="Screenshot 2026-11-12 133111" src="https://github.com/user-attachments/assets/49a3b49b-f30a-49ba-901f-b2fb514caf27" />

## FMTSTR 6 - Tấn công GOT 
Source: https://youtu.be/ZosW20QjET8?si=Js2CTy5OQO2r1X4u
<img width="743" height="305" alt="Screenshot 2026-11-12 173636" src="https://github.com/user-attachments/assets/46b3ef31-9566-4cf5-8510-1d3eaaf749f6" />
<img width="795" height="282" alt="Screenshot 2026-11-12 173623" src="https://github.com/user-attachments/assets/d2a9a19f-fb61-4ef9-afb1-96712833160b" />

- Trong bài này chỉ có lỗi Format String, và địa chỉ là địa chỉ động nên mình phải leak địa chỉ. Và bài này có vòng lặp cho phép mình nhập liên tục nhiều lần.

<img width="1462" height="410" alt="Screenshot 2026-11-12 175137" src="https://github.com/user-attachments/assets/8a4fd66f-8756-45b9-a73f-ae3d62c07721" />

- Do RELRO tắt nên mình có thể tận dụng khả năng RW- của GOT 
- Mình có thể ghi đè địa chỉ các hàm trong GOT, ví dụ mình ghi đè địa chỉ printf() địa chỉ hàm system(), thì sau khi thay đổi printf() khi chạy chương trình nó sẽ biến thành system()

<img width="538" height="115" alt="Screenshot 2026-11-12 181430" src="https://github.com/user-attachments/assets/82baa10b-c18c-44c9-8408-ba3a174bfe2f" />
<img width="520" height="125" alt="Screenshot 2026-11-12 180640" src="https://github.com/user-attachments/assets/984cc6bc-9cc8-418a-803d-01337557f422" />

- Đầu tiên mình sẽ leak libc address trước
- Mình cũng sẽ chạy file buildnstart.sh trong docker mà anh Trí đã cho sẵn để lấy đúng phiên bản libc. Do trong thực tế thì local libc của mình sẽ khác với libc trên server
- Vì địa chỉ libc sẽ khác 3 byte cuối theo mỗi phiên bản (ví dụ của mình là 601 thì có chạy bao nhiêu lần nó vẫn là 601), mà địa chỉ khác nhau sẽ khiến offset cũng khác nhau.

<img width="676" height="546" alt="Screenshot 2026-11-14 111448" src="https://github.com/user-attachments/assets/7ce9da00-4479-4bc3-87e3-95646ca0dedc" />

<img width="800" height="123" alt="Screenshot 2026-11-14 111504" src="https://github.com/user-attachments/assets/7cc74c36-f999-4683-866f-524cf29dab54" />

- Mình đã kết nối server và địa chỉ libc leak được ra với 3 byte cuối là 023

<img width="1192" height="642" alt="Screenshot 2026-11-14 112621" src="https://github.com/user-attachments/assets/2a0756e6-a471-4027-b7e4-a5228afd08e4" />
<img width="1882" height="270" alt="Screenshot 2026-11-14 112908" src="https://github.com/user-attachments/assets/b9aa0853-e202-4172-84f7-d5285e5e9cfd" />


- Mình lên libc.rip để tải phiên bản phù hợp có đuôi 023, mình phải thử từng libc. Mình sẽ chọn phiên bản cuối, copy linl và wget để tải về cho nhanh, sau đó pwninit 

<img width="732" height="572" alt="Screenshot 2026-11-14 113513" src="https://github.com/user-attachments/assets/927b0cc5-5f67-4ce0-a158-2cb9ecf91be4" />

<img width="1878" height="308" alt="Screenshot 2026-11-14 113503" src="https://github.com/user-attachments/assets/cd72ff81-be2d-4a0b-bf61-98c6657fb814" />


- Rồi mình đưa libc lên, chạy local thì đuôi __libc_start_main_ret đax thành 023 chứ không phải 601 như lúc đầu -> giờ mình đã có thể khai thác đúng offset tính địa chỉ libc base 

<img width="663" height="147" alt="Screenshot 2026-11-14 114326" src="https://github.com/user-attachments/assets/c9afbac9-9166-454c-96b8-fad75947f615" />

<img width="466" height="67" alt="Screenshot 2026-11-14 114318" src="https://github.com/user-attachments/assets/42460054-407c-47f6-b595-5155e90359cd" />

- Bây giờ, do chương trình chạy liên tục ở hàm main và không return, nên việc overwrite rip và vô nghĩa 

<img width="438" height="151" alt="Screenshot 2026-11-14 114620" src="https://github.com/user-attachments/assets/a3dca944-9868-48ad-9760-7043843cd7f5" />

- RELRO tắt và mình có thể ghi đè GOT, quan trọng bây giờ mình phải ghi đè system() lên fgets() hay printf(). Mình muốn thực thi system() với argument đầu tiên là chuỗi `/bin/sh` thì mình phải chọn hàm nào có argument đầu tiên mình điều khiển được
- Mà GOT mình tấn công là của binary, nên mình phải leak binary ra  

<img width="955" height="563" alt="Screenshot 2026-11-14 115801" src="https://github.com/user-attachments/assets/1ad5896a-e815-4e05-ba73-a743a42b1679" />

- Các địa chỉ màu đỏ thường là địa chỉ binary của chương trình. Nhưng mấy địa chỉ xuất hiện trên Stack này thường là địa chỉ rác, mình cần kiểm tra thử và kiếm thêm địa chỉ khác nếu sai, địa chỉ binary trên local chưa chắc đã giống khi chạy trên server. Mình sẽ chọn thử địa chỉ có đuôi 2cd 

<img width="1092" height="482" alt="Screenshot 2026-11-14 120417" src="https://github.com/user-attachments/assets/65cb9b3a-6d53-499e-ad3f-c8a50abca1f3" />

- Chạy trên server và đúng i chang địa chỉ binary mình chạy trên local -> dùng được địa chỉ này để tính offset binary base
<img width="431" height="95" alt="Screenshot 2026-11-14 130111" src="https://github.com/user-attachments/assets/7964baa3-a24b-4415-aa4c-261272151210" />

<img width="925" height="242" alt="Screenshot 2026-11-14 130118" src="https://github.com/user-attachments/assets/12f8b9cb-2b8a-40ab-a2b8-df9aed4adbcb" />

- NOTE: Địa chỉ base là số không âm và 3 ký tự cuối là 000

<img width="1142" height="535" alt="Screenshot 2026-11-14 131427" src="https://github.com/user-attachments/assets/b7046294-e0f7-4491-942b-0b5f9e3f0693" />

- Giờ mình sẽ đến bước ghi đè system, mình cần tìm sự tương quan giữa printf() và system(), ở đây khác nhau 3 byte cuối thì mình chỉ cần ghi đè 3 byte cuối, không cần phải ghi đè hết
- Ở đây ghi đè 3 byte thì mình sẽ ghi đè 1 byte trước rồi mới ghi đè 2 byte, không ghi 2 byte rồi đến 1 vì sẽ dẫn tới lỗi

<img width="462" height="127" alt="Screenshot 2026-11-14 163637" src="https://github.com/user-attachments/assets/814a0100-5762-44f6-b97e-894bd52cb6c5" />

- Đầu tiên mình lấy 1 byte đầu của địa chỉ system gắn làm part 1. Part 2 thì lấy 2 byte tiếp theo nhưng sẽ dịch vào 8 bit(1 byte đã lấy). 

<img width="540" height="323" alt="Screenshot 2026-11-14 163901" src="https://github.com/user-attachments/assets/305b9868-6d64-4e86-9bd5-ca31a827be57" />

- Sau đó khai thác như bài trước đó, sử dụng %hn. Đưa %hn lên trước, sau đó chừa 1 khoảng ra và đưa địa chỉ printf() lên. Sau khi ghi đè hàm system() lên hàm printf(), mình chỉ gần gửi chuỗi `/bin/sh\0` ở hàm fgets(), thì xuống dưới system() thực thi là mình có được shell 



