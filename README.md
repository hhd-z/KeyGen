# DTZ Key Generate

Công cụ web nhỏ gọn để **tạo và kiểm tra key kích hoạt**. Chạy hoàn toàn trên trình duyệt, chỉ một file HTML, không cần cài đặt, không cần server.

![GitHub Pages](https://img.shields.io/badge/deploy-GitHub%20Pages-2ea44f)
![No dependencies](https://img.shields.io/badge/dependencies-none-blue)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

**Demo:** https://TEN-GITHUB.github.io/TEN-REPO/

<!-- Thay TEN-GITHUB và TEN-REPO bằng tên tài khoản và tên repo của bạn.
     Nên thêm ảnh chụp màn hình: ![Ảnh chụp màn hình](docs/screenshot.png) -->

---

## Tính năng

- **Tạo một key**: sinh UID ngẫu nhiên và key đi kèm chỉ bằng một lần bấm.
- **Tạo theo UID**: nhập UID của thiết bị, nhận key chỉ dùng được cho đúng UID đó.
- **Tạo hàng loạt**: từ 1 đến 5000 key mỗi lần, có ô lọc, sao chép nhanh và xuất file TXT, CSV hoặc JSON.
- **Kiểm tra key**: nhập UID và key để biết key có hợp lệ hay không.
- **Tiền tố tùy chỉnh**: đổi tiền tố key tối đa 5 ký tự (chữ A-Z, số 0-9). Mặc định là `DTZ`.
- **Mở app bằng deep link**: nút "Mở app" gọi thẳng `digitalvolume://activate?key=...&uid=...`.
- **Giao diện sáng và tối**, dùng tốt trên điện thoại.
- **Riêng tư**: toàn bộ xử lý nằm trong trình duyệt, không gửi dữ liệu đi đâu.

## Cách dùng

| Tab | Việc làm |
| --- | --- |
| Một key | Bấm **Tạo key**, sau đó sao chép key, sao chép UID hoặc mở app. |
| Theo UID | Nhập UID rồi bấm **Tạo key** để lấy key khớp UID đó. |
| Hàng loạt | Nhập số lượng (hoặc chọn nhanh 10, 50, 100, 500, 1000), bấm **Tạo key**, lọc và tải file. |
| Kiểm tra | Nhập UID và key, bấm **Kiểm tra**. |

Tiền tố nằm ở ô trên cùng và áp dụng cho mọi tab. Để trống ô này thì dùng `DTZ`. Tiền tố được lưu lại trong trình duyệt cho lần sau.

## Định dạng key

```
DTZ-ABCD-EFGH-U9PC-87C5
 │    │    │    └──┴── checksum (8 ký tự)
 │    └────┴────────── 2 đoạn ngẫu nhiên (4 ký tự mỗi đoạn)
 └─────────────────── tiền tố (1 đến 5 ký tự)
```

- Bảng ký tự: `ABCDEFGHJKLMNPQRSTUVWXYZ23456789` (đã bỏ `0`, `O`, `1`, `I` để khỏi nhầm khi đọc).
- Checksum là **HMAC-SHA256**, lấy 8 byte đầu, mỗi byte `& 31` rồi tra vào bảng ký tự ở trên.
- Thông điệp được ký: `DTZ_V2|<tiền tố>|<đoạn 1>|<đoạn 2>|<UID>`
- Khóa: hằng số `DTZ_PUBLIC_V2` (xem phần [Lưu ý bảo mật](#lưu-ý-bảo-mật)).

Do thông điệp có cả tiền tố và UID, một key chỉ hợp lệ với đúng tiền tố và đúng UID đã dùng để tạo ra nó.

## Kiểm tra key trong app của bạn

Làm lại đúng công thức trên ở phía app. Ví dụ bằng Python:

```python
import hmac, hashlib

SECRET = b"DTZ_PUBLIC_V2"
ALPHABET = "ABCDEFGHJKLMNPQRSTUVWXYZ23456789"

def checksum(prefix, a, b, uid):
    msg = f"DTZ_V2|{prefix}|{a}|{b}|{uid}".encode()
    digest = hmac.new(SECRET, msg, hashlib.sha256).digest()
    return "".join(ALPHABET[x & 31] for x in digest[:8])

def verify(key, uid):
    parts = key.upper().split("-")
    if len(parts) != 5:
        return False
    prefix, a, b, t1, t2 = parts
    return checksum(prefix, a, b, uid) == t1 + t2
```

**Bộ kiểm thử** để đối chiếu khi làm ở ngôn ngữ khác:

| Tiền tố | Đoạn 1 | Đoạn 2 | UID | Checksum |
| --- | --- | --- | --- | --- |
| `DTZ` | `ABCD` | `EFGH` | `uid1` | `U9PC87C5` |

Tức là key `DTZ-ABCD-EFGH-U9PC-87C5` hợp lệ với UID `uid1`.

## Triển khai lên GitHub Pages

1. Tạo repo mới trên GitHub và upload các file trong thư mục dự án (xem [Cấu trúc thư mục](#cấu-trúc-thư-mục)).
2. Vào **Settings → Pages**.
3. Ở mục **Build and deployment**, chọn **Deploy from a branch**, nhánh `main`, thư mục `/ (root)`, rồi **Save**.
4. Sau khoảng một phút, trang chạy tại `https://<tên-github>.github.io/<tên-repo>/`.

Có thể chạy thử ngay trên máy: mở `index.html` bằng trình duyệt.

## Tùy chỉnh

Các giá trị cấu hình nằm ở đầu phần `<script>` trong `index.html`:

```js
const SECRET="DTZ_PUBLIC_V2",SCHEME="digitalvolume://activate",DEF="DTZ",MAXP=5;
```

| Hằng số | Ý nghĩa |
| --- | --- |
| `SECRET` | Khóa dùng để tính checksum. App phải dùng đúng giá trị này. |
| `SCHEME` | Địa chỉ deep link của nút **Mở app**. |
| `DEF` | Tiền tố mặc định khi người dùng để trống. |
| `MAXP` | Số ký tự tối đa của tiền tố. |

Đổi `SECRET` hoặc đổi công thức đồng nghĩa với việc mọi key đã tạo trước đó không còn hợp lệ, và app cũng phải sửa theo.

## Cấu trúc thư mục

```
.
├── index.html     # toàn bộ giao diện và logic
├── donate.png     # mã QR ủng hộ (hiện trong nút "Ủng hộ dev")
├── README.md
└── LICENSE
```

## Lưu ý bảo mật

Trang này chạy hoàn toàn phía trình duyệt, nên `SECRET` nằm ngay trong mã nguồn và ai cũng đọc được. Vì vậy:

- Checksum ở đây chỉ có tác dụng **chống gõ nhầm** và **gắn key với một UID cụ thể**. Nó **không** ngăn người khác tự tạo key hợp lệ.
- Phù hợp cho app miễn phí, bản thử nghiệm, dự án cá nhân hoặc mục đích học tập.
- Nếu key dùng để giới hạn tính năng trả phí, hãy tạo và kiểm tra key ở phía server, với khóa bí mật không bao giờ đưa ra trình duyệt.

## Ủng hộ

Nếu công cụ hữu ích với bạn, có thể ủng hộ dev bằng nút **Ủng hộ dev** ở góc trên bên phải của trang (hiện mã QR từ file `donate.png`). Cảm ơn bạn rất nhiều.

## Giấy phép

Phát hành theo giấy phép [MIT](LICENSE).
