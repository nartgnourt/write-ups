# Voice Changer

![voice-changer](images/voice-changer.png)

## Solution

Theo liên kết mình tới được trang web.

![voice-changer-1](images/voice-changer-1.png)

Khi nhấn submit mình nhận được phần Output trông giống với shell. Mình nghĩ sẽ thực hiện **Command Injection** để giải được challenge này.

![voice-changer-2](images/voice-changer-2.png)

Mình thử thay đổi giá trị của Pitch rồi submit thì thấy Output có sự khác biệt.

![voice-changer-3](images/voice-changer-3.png)

Mình inspect thấy giá trị `max` của Pitch cũng là `2` nên mình sẽ thử chèn payload thông qua thẻ input này.

![voice-changer-4](images/voice-changer-4.png)

Mình đổi `type="range"` thành `type="text"` để có thể nhập payload.

![voice-changer-5](images/voice-changer-5.png)

Thử với payload `2" & ls;` mình thấy có các folder và file của web.

![voice-changer-6](images/voice-changer-6.png)

Tiếp tục thử với payload `2" & ls /;` mình thấy có file `secret.txt`.

![voice-changer-7](images/voice-changer-7.png)

Mình đọc file đó bằng payload `2" & cat /secret.txt;` thì lấy được flag.

![voice-changer-8](images/voice-changer-8.png)

## Flag

`uoftctf{Y0UR Pitch IS 70O H!9H}`