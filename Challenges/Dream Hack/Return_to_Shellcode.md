# Phân tích lỗi  
<img width="802" height="420" alt="Screenshot 2026-11-01 215858" src="https://github.com/user-attachments/assets/a3d10521-e4bc-4ef3-966a-a787d2170692" />

-Bài này in thẳng địa chỉ Stack cho mình, NX cũng tắt, Canary đang bật, mình chỉ cần leak canary thông qua hàm read, rồi gửi shellcode ở lần thứ 2 và ghi đè Stack leak vào rip là được

# Khai thác
-Đầu tiên mình gửi trước 0x60 byte và tính được offset từ buffer đến canary là 0x58 byte. Mình sẽ ghi đè 0x59 byte để hàm read() sẽ nối thêm chuỗi Canary vào.  

<img width="1105" height="420" alt="Screenshot 2026-11-02 090718" src="https://github.com/user-attachments/assets/14797624-222b-4275-8412-aee0e8cd3fbd" />
<img width="520" height="45" alt="Screenshot 2026-11-02 090727" src="https://github.com/user-attachments/assets/ed4bf922-845f-49c0-aed1-87b95687d817" />

-Xử lí xong lần gửi đầu tiên, có địa chỉ stack rồi, có canary rồi,  giờ mình chỉ cần viết shellcode và ghi đè địa chỉ stack leak ra được vào saved-rip để nó nhảy vào và thực hiện như bình thường    

<img width="612" height="957" alt="Screenshot 2026-11-01 220500" src="https://github.com/user-attachments/assets/e237a4e0-6e3c-46d4-8eaf-79815612e9b8" />
