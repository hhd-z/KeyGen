<div align="center">

## 🔐 DTZ&nbsp;Key&nbsp;Generate

**Công cụ web nhỏ gọn để tạo & kiểm tra key kích hoạt chuẩn HMAC-SHA256.**  
*100% Client-side • 1 File HTML • Không cần cài đặt • Không cần Server*

[![Live Demo](https://img.shields.io/badge/🚀_Live_Demo-Mở_Web_Ngay-2ea44f?style=flat-square)](https://hhd-z.github.io/KeyGen/)
[![GitHub Pages](https://img.shields.io/badge/Deploy-GitHub_Pages-181717?style=flat-square&logo=github)](https://hhd-z.github.io/KeyGen/)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-None-007ec6?style=flat-square)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-f1c40f?style=flat-square)](LICENSE)

🌐 **Web chính thức:** 
[**hhd-z.github.io/KeyGen**](https://hhd-z.github.io/KeyGen/)

<!-- Sếp có thể bỏ comment dòng dưới nếu muốn chèn ảnh chụp màn hình giao diện web nhé -->
<!-- ![Preview Screenshot](docs/screenshot.png) -->

</div>

---

## ✨ Tính năng nổi bật

- ⚡ **Tạo một key nhanh**: Sinh ngẫu nhiên `UID` và `Key` tương ứng chỉ với 1 cú click.
- 🎯 **Tạo key theo UID**: Nhập `UID` của thiết bị cụ thể, nhận key khóa cứng chỉ dùng được cho đúng `UID` đó.
- 📦 **Tạo hàng loạt (Bulk Generation)**: Hỗ trợ tạo từ `1` đến `5.000` key mỗi lần. Tích hợp sẵn bộ lọc tìm kiếm, sao chép nhanh và xuất dữ liệu ra các định dạng `.TXT`, `.CSV` hoặc `.JSON`.
- 🔍 **Kiểm tra key (Validator)**: Xác thực nhanh cặp `UID` và `Key` xem có hợp lệ và khớp nhau hay không.
- 🏷️ **Tiền tố tùy chỉnh (Custom Prefix)**: Tùy biến tiền tố đầu của key tối đa 5 ký tự (`A-Z`, `0-9`, mặc định là `DTZ`) và tự động lưu vào bộ nhớ trình duyệt cho lần dùng sau.
- 📱 **Tích hợp Deep Link**: Nút **"Mở app"** gọi trực tiếp giao thức `digitalvolume://activate?key=...&uid=...` để kích hoạt nhanh ứng dụng.
- 🌗 **Giao diện Hiện đại (Dark/Light Mode)**: Tự động tối ưu hiển thị, tương thích mượt mà trên cả máy tính lẫn điện thoại.
- 🛡️ **Bảo mật & Riêng tư tuyệt đối**: 100% logic xử lý nằm tại trình duyệt người dùng (Client-side), cam kết không thu thập hay gửi dữ liệu đi bất kỳ đâu.

---

## 📖 Hướng dẫn sử dụng

💡 *Mẹo: Ô **Tiền tố (Prefix)** nằm ở thanh trên cùng sẽ áp dụng chung cho toàn bộ các tab. Nếu bỏ trống, hệ thống mặc định dùng `DTZ`.*

| Chế độ (Tab) | Thao tác thực hiện |
| :--- | :--- |
| **1️⃣ Một key** | Bấm **Tạo key** ➡️ Sao chép Key, sao chép UID hoặc bấm **Mở app** để kích hoạt trực tiếp. |
| **2️⃣ Theo UID** | Nhập `UID` thiết bị cần cấp phép ➡️ Bấm **Tạo key** để nhận mã gắn kết riêng với UID đó. |
| **3️⃣ Hàng loạt** | Nhập số lượng (hoặc chọn mốc nhanh `10`, `50`, `100`, `500`, `1000`) ➡️ Bấm **Tạo key** ➡️ Lọc và tải file về máy. |
| **4️⃣ Kiểm tra** | Nhập cặp `UID` và `Key` cần đối chiếu ➡️ Bấm **Kiểm tra** để xem trạng thái hợp lệ. |

---

## 🧬 Cấu trúc & Thuật toán Key

Mỗi mã kích hoạt được sinh ra tuân theo định dạng chuẩn:

~~~text
DTZ-ABCD-EFGH-U9PC-87C5
 │    │    │    └──┴── Checksum xác thực (8 ký tự)
 │    └────┴────────── 2 cụm ký tự ngẫu nhiên (4 ký tự / cụm)
 └──────────────────── Tiền tố tùy chỉnh (1 đến 5 ký tự)
~~~

### Nguyên lý hoạt động:
- **Bảng chữ cái (Alphabet)**: `ABCDEFGHJKLMNPQRSTUVWXYZ23456789` *(Đã loại bỏ `0`, `O`, `1`, `I` để tránh nhầm lẫn thị giác khi người dùng nhập tay)*.
- **Thuật toán băm**: Sử dụng **HMAC-SHA256**, trích xuất 8 byte đầu tiên, mỗi byte thực hiện phép toán `& 31` để ánh xạ sang bảng chữ cái trên.
- **Chuỗi thông điệp ký (Payload)**: `DTZ_V2|<Tiền tố>|<Đoạn 1>|<Đoạn 2>|<UID>`
- **Khóa công khai (Secret Key)**: Hằng số `DTZ_PUBLIC_V2` *(Xem thêm tại mục [Lưu ý bảo mật](#-lưu-ý-bảo-mật-quan-trọng))*.

> 📌 **Cơ chế ràng buộc**: Vì chuỗi thông điệp chứa cả **Tiền tố** và **UID**, một key chỉ được coi là hợp lệ khi đi kèm đúng Tiền tố và đúng UID lúc khởi tạo.

---

## 💻 Tích hợp kiểm tra Key vào App của bạn

Để xác thực key trong ứng dụng đích, bạn chỉ cần tái tạo lại công thức băm ở trên. Dưới đây là mã nguồn mẫu bằng **Python**:

~~~python
import hmac
import hashlib

SECRET = b"DTZ_PUBLIC_V2"
ALPHABET = "ABCDEFGHJKLMNPQRSTUVWXYZ23456789"

def generate_checksum(prefix: str, part_a: str, part_b: str, uid: str) -> str:
    msg = f"DTZ_V2|{prefix}|{part_a}|{part_b}|{uid}".encode("utf-8")
    digest = hmac.new(SECRET, msg, hashlib.sha256).digest()
    return "".join(ALPHABET[byte & 31] for byte in digest[:8])

def verify_key(key: str, uid: str) -> bool:
    parts = key.strip().upper().split("-")
    if len(parts) != 5:
        return False
    prefix, part_a, part_b, chk1, chk2 = parts
    return generate_checksum(prefix, part_a, part_b, uid) == (chk1 + chk2)
~~~

### 🧪 Bộ dữ liệu kiểm thử (Test Vector)
Dùng bảng dưới đây để đối chiếu kết quả khi bạn viết hàm kiểm tra bằng ngôn ngữ khác (Java, Kotlin, C#, JS, C++...):

| Tiền tố | Đoạn 1 | Đoạn 2 | UID | Checksum đầu ra | Key hoàn chỉnh hợp lệ |
| :---: | :---: | :---: | :---: | :---: | :---: |
| `DTZ` | `ABCD` | `EFGH` | `uid1` | `U9PC87C5` | `DTZ-ABCD-EFGH-U9PC-87C5` |

---

## ⚙️ Tùy chỉnh & Triển khai

<details>
<summary><b>🛠️ Xem hướng dẫn tùy chỉnh thông số mã nguồn (Click để mở)</b></summary>
<br />

Các hằng số cấu hình cốt lõi nằm ngay đầu thẻ `<script>` trong file `index.html`:

~~~javascript
const SECRET = "DTZ_PUBLIC_V2",
      SCHEME = "digitalvolume://activate",
      DEF    = "DTZ",
      MAXP   = 5;
~~~

| Hằng số | Ý nghĩa cấu hình |
| :--- | :--- |
| `SECRET` | Khóa bí mật dùng để tính toán Checksum. Ứng dụng của bạn phải khai báo khớp 100% giá trị này. |
| `SCHEME` | Đường dẫn Deep Link kích hoạt khi người dùng bấm nút **Mở app**. |
| `DEF` | Tiền tố mặc định khi ô nhập liệu để trống. |
| `MAXP` | Độ dài ký tự tối đa cho phép của tiền tố. |

🔴 **Cảnh báo**: Việc thay đổi giá trị `SECRET` hoặc cấu trúc chuỗi băm sẽ làm **vô hiệu hóa toàn bộ các key đã phát hành trước đó**, đồng thời bạn phải cập nhật lại thuật toán tương ứng trên App.
</details>

<details>
<summary><b>🚀 Xem hướng dẫn tự triển khai lên GitHub Pages (Click để mở)</b></summary>
<br />

1. Fork hoặc tải toàn bộ file trong kho lưu trữ này lên Repository GitHub của bạn.
2. Truy cập vào mục **Settings** ➡️ **Pages** trên thanh công cụ của Repo.
3. Tại phần **Build and deployment**, chọn nguồn **Deploy from a branch** ➡️ Nhánh `main` ➡️ Thư mục `/ (root)` ➡️ Bấm **Save**.
4. Đợi khoảng 1 phút để hệ thống khởi tạo, trang web của bạn sẽ sẵn sàng hoạt động! *(Hoặc bạn có thể tải về máy và mở trực tiếp file `index.html` bằng trình duyệt bất kỳ)*.
</details>

---

## 📂 Cấu trúc thư mục

~~~text
KeyGen/
├── 📄 index.html     # Toàn bộ giao diện (UI) và mã nguồn xử lý (Logic)
├── 🖼️ donate.png     # Mã QR ủng hộ nhà phát triển (Hiển thị trong modal "Ủng hộ dev")
├── 📘 README.md      # Tài liệu hướng dẫn sử dụng chi tiết
└── ⚖️ LICENSE        # Giấy phép nguồn mở MIT
~~~

---

## 🔴 Lưu ý bảo mật quan trọng

Do đặc thù là ứng dụng tĩnh chạy 100% phía trình duyệt (Client-side), hằng số `SECRET` nằm trực tiếp trong mã nguồn JavaScript và bất kỳ ai có kiến thức kỹ thuật đều có thể đọc được. Vì vậy:

* ❗ **Mục đích của Checksum**: Chỉ có tác dụng **chống người dùng gõ nhầm key** và **ràng buộc key với một UID thiết bị cụ thể**. Cơ chế này **không** ngăn chặn được việc dịch ngược mã nguồn để tự tạo key.
* ✅ **Trường hợp nên dùng**: Rất phù hợp cho các ứng dụng miễn phí, bản Beta thử nghiệm, công cụ nội bộ, dự án cá nhân hoặc mục đích học tập.
* 🔒 **Đối với ứng dụng thương mại / trả phí**: Hãy chuyển toàn bộ quá trình tạo và xác thực key lên máy chủ riêng (Server-side API) hoặc sử dụng chữ ký số bất đối xứng (RSA / Ed25519) để bảo vệ khóa bí mật tuyệt đối.

---

## ☕ Ủng hộ nhà phát triển (Donate)

Nếu công cụ này giúp ích cho công việc và dự án của bạn, hãy tiếp thêm động lực cho mình bằng cách bấm vào nút **"Ủng hộ dev"** ở góc trên bên phải của [trang web](https://hhd-z.github.io/KeyGen/) (mã QR từ file `donate.png`). Cảm ơn sự ủng hộ của bạn rất nhiều! ❤️

---

<div align="center">

Phát hành dưới giấy phép **[MIT License](LICENSE)**.  
Developed with ❤️ by **[hhd-z (Kriz Neo)](https://github.com/hhd-z)**

</div>
