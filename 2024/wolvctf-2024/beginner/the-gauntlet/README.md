# The Gauntlet

![the-gauntlet](images/the-gauntlet.png)

## Solution

Theo như mô tả, chúng ta sẽ cần hoàn thành 10 thử thách nhỏ để lấy được flag. Bắt đầu nào!

Truy cập vào URL được cung cấp, chúng ta sẽ thấy trang web sau:

![the-gauntlet-1](images/the-gauntlet-1.png)

Một lời chào cùng với đó là câu hỏi có gì bị ẩn đi trên trang web này không.

Mình thử View Source rồi kéo xuống cuối thì thấy có một comment:

![the-gauntlet-2](images/the-gauntlet-2.png)

Đó chính là đường dẫn tới page 1. Tại đây, chúng ta được yêu cầu đặt HTTP Request Header là `wolvsec: rocks`:

![the-gauntlet-3](images/the-gauntlet-3.png)

Mình sẽ sử dụng Burp Repeater để thực hiện, mình thêm vào Header `wolvsec: rocks` rồi gửi request:

![the-gauntlet-4](images/the-gauntlet-4.png)

Đã có được đường dẫn tới page 2. Tới đó thôi nào!

![the-gauntlet-5](images/the-gauntlet-5.png)

Giờ mình cần sử dụng một HTTP method khác để thấy được bí mật. Thử với `OPTIONS` được luôn:

![the-gauntlet-6](images/the-gauntlet-6.png)

Mình gửi GET request tới page 3:

![the-gauntlet-7](images/the-gauntlet-7.png)

Tại đây, mình cần thêm query string có tham số `wolvsec` với giá trị là `c#+l`.

Tuy nhiên, một điều cần lưu ý là phần sau dấu `#` được gọi là **fragment identifier** và nó sẽ không được gửi tới server mà sẽ được sử dụng bởi browser để liên kết tới một phần nhất định bên trong trang web.

Bởi vậy, mình cần encode URL `c#+l` thành `c%23%2bl` rồi mới gửi request:

![the-gauntlet-8](images/the-gauntlet-8.png)

Thấy được đường dẫn rồi, mình tiếp tục di chuyển tới page 4.

Mình được yêu cầu gửi một POST request tới trang này kèm theo Header `Content-Type: application/x-www-form-urlencoded` cùng với phần body chứa `wolvsec=rocks`:

![the-gauntlet-9](images/the-gauntlet-9.png)

Mình đổi method sang `POST` và thêm vào những thứ được yêu cầu:

![the-gauntlet-10](images/the-gauntlet-10.png)

Đã thấy được đường dẫn tới page 5, mình di chuyển tới đó:

![the-gauntlet-11](images/the-gauntlet-11.png)

Bí mật đã ở trong trang web này rồi nhưng View Source lại không thấy được. Vậy nguyên nhân chính là do đoạn code JavaScript kia đã thực thi để tạo ra phần comment chứa bí mật.

Mình sẽ copy URL của request:

![the-gauntlet-12](images/the-gauntlet-12.png)

Quay trở lại browser và paste URL vào thanh địa chỉ để truy cập vào page 5. Mình Inspect đã thấy được đường dẫn:

![the-gauntlet-13](images/the-gauntlet-13.png)

Request tới `/hidden5935562908234559` mình thấy response có status code `302 Found`:

![the-gauntlet-14](images/the-gauntlet-14.png)

Để ý header `Location: /hidden5935562908234558`, mình gửi request tới `/hidden5935562908234558` đã thấy comment chứa đường dẫn:

![the-gauntlet-15](images/the-gauntlet-15.png)

Request tới `/hidden82008753458651496`, mình tới được page 7:

![the-gauntlet-16](images/the-gauntlet-16.png)

Trang web nói mình mới tới đây có 1 lần, nếu 500 lần thì sẽ tiết lộ bí mật. Để ý thấy có response header `Set-Cookie: cookie-counter=2; Path=/`. Vậy là nó sẽ dựa vào giá trị của cookie này để đếm số lần tới trang web. Do đó, mình sẽ thêm header `Cookie: cookie-counter=500` vào rồi gửi request:

![the-gauntlet-17](images/the-gauntlet-17.png)

Yahh, thấy được bí mật rồi, tới page 8 thôi nào:

![the-gauntlet-18](images/the-gauntlet-18.png)

Lần này trang web kiểm tra bằng JWT. Mình lấy chuỗi `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb3VudGVyIjo1MDF9.2RgGGM3Mihm5kuLlMk-3zKaSlyuFuMNhSETavftqIKM` của cookie `jwt-cookie-counter` rồi paste vào trang [jwt.io](https://jwt.io/). Mình sửa đổi như bên dưới để có được chuỗi JWT mới:

![the-gauntlet-19](images/the-gauntlet-19.png)

Mình đổi cookie thành `jwt-cookie-counter=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb3VudGVyIjo1MDB9.LW10TYVnD5j3ffWMi_Es3UrusiIyWBum23O6yUUUrrc` và gửi request liền thấy được bí mật:

![the-gauntlet-20](images/the-gauntlet-20.png)

Sắp lấy được flag rồi, tới page 9 thôi:

![the-gauntlet-21](images/the-gauntlet-21.png)
![the-gauntlet-22](images/the-gauntlet-22.png)

Tại đây, chúng ta được nhắc nhở rằng không thể crack JWT và được gợi ý viết một script để tạo request rồi lấy cookie trong response và dùng nó để gửi request tiếp theo.

Mình thử lấy cookie `jwt-uncrackable-cookie-counter=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb3VudGVyIjoyfQ.mmGFwHhAssPPS6Xq3ptN1ViB7T4B_3KaAKySvnhdlUk` trong response bên trên rồi gửi request thì số lần ghé thăm trang web tăng lên 2:

![the-gauntlet-23](images/the-gauntlet-23.png)

Như vậy, cứ lặp lại quá trình lấy cookie mới trong response rồi lại gửi request với cookie đó thì chúng ta sẽ thấy được bí mật.

Tham khảo link gợi ý viết script trong response trên, mình viết đoạn code Python sau:

```python
import requests

url = 'https://gauntlet-okntin33tq-ul.a.run.app/hidden83365193635473293'
cookies = None  

for i in range(1000):
    r = requests.get(url, cookies=cookies)
    
    cookies = r.cookies.get_dict()
    print(r.text)
```

Chạy đoạn code trên và ngồi chờ đợi sẽ thấy điều bí mật:

![the-gauntlet-24](images/the-gauntlet-24.png)

Cuối cùng, mình gửi request tới `/flag620873537329327365` và lấy flag thành công:

![the-gauntlet-25](images/the-gauntlet-25.png)

## Flag

`wctf{w3_h0p3_y0u_l34rn3d_s0m3th1ng_4nd_th4t_w3b_c4n_b3_fun_853643}`
