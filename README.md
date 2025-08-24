# BeautifulSoup4
Hướng dẫn chi tiết sử dụng thư viện Beautiful Soup 4 (bs4) – Tiếng Việt

1️⃣ Cài đặt
Phương pháp	Lệnh
pip (cách phổ biến)	pip install beautifulsoup4
pip3 (cho Python 3)	pip3 install beautifulsoup4
apt (Ubuntu/Debian)	sudo apt‑get install python3‑bs4
easy_install	easy_install beautifulsoup4
Lưu ý: Gói trên PyPI có tên beautifulsoup4. Khi dùng pip, hãy chắc chắn dùng phiên bản pip tương ứng với Python bạn đang chạy (pip vs pip3).

2️⃣ Nhập thư viện
from bs4 import BeautifulSoup
Bạn có thể truyền chuỗi HTML, file handle, hoặc đối tượng response của requests/urllib vào hàm khởi tạo.

html = "<html><body><h1>Tiêu đề</h1></body></html>"
soup = BeautifulSoup(html, "html.parser")   # parser mặc định của Python
Các parser thường dùng:

Parser	Ghi chú
html.parser	Có sẵn trong Python, nhanh nhưng hạn chế với HTML lỗi.
lxml	Nhanh, mạnh mẽ, cần cài lxml (pip install lxml).
html5lib	Đọc giống trình duyệt, chậm hơn nhưng chuẩn nhất.
Bạn có thể chọn parser bằng cách truyền tên vào tham số thứ hai của BeautifulSoupviblo.asia.

3️⃣ Cấu trúc đối tượng
soup – đối tượng gốc, đại diện cho toàn bộ tài liệu.
Các Tag (thẻ) – ví dụ soup.title, soup.body.
NavigableString – nội dung văn bản bên trong thẻ.
title_tag = soup.title          # <title>...</title>
print(title_tag.string)         # nội dung của thẻ title
4️⃣ Duyệt cây DOM
Phương thức	Mô tả
.children / .descendants	Lặp qua các phần tử con (trực tiếp hoặc đệ quy).
.parent / .parents	Truy cập phần tử cha, hoặc toàn bộ chuỗi cha.
.next_sibling / .previous_sibling	Thẻ kế tiếp/trước trong cùng cấp.
.next_element / .previous_element	Phần tử kế tiếp/trước trong luồng tài liệu.
Ví dụ duyệt lên tới gốc:

for parent in soup.h1.parents:
    print(parent.name)
5️⃣ Tìm kiếm phần tử
5.1 find() và find_all()
# Tìm thẻ <a> đầu tiên
first_a = soup.find("a")

# Tất cả thẻ <a> có thuộc tính class="link"
links = soup.find_all("a", class_="link")
5.2 Tham số name, attrs, string
# Tìm thẻ <div> có id="content"
div = soup.find("div", id="content")

# Tìm thẻ chứa chuỗi ngắn (< 10 ký tự)
short = soup.find(lambda tag: isinstance(tag, NavigableString) and len(tag) < 10)
5.3 Sử dụng CSS selector (select)
# Tất cả thẻ <a> trong <nav>
nav_links = soup.select("nav a")
6️⃣ Thao tác với nội dung
# Lấy văn bản thuần (loại bỏ thẻ)
text = soup.get_text(separator="\n", strip=True)

# Thay đổi thuộc tính
img = soup.find("img")
img["src"] = "new_image.jpg"

# Thêm thẻ mới
new_tag = soup.new_tag("p")
new_tag.string = "Đoạn văn mới"
soup.body.append(new_tag)
7️⃣ Xử lý mã hoá và lỗi HTML
Mã hoá: Beautiful Soup tự động phát hiện và chuyển sang Unicode. Nếu gặp lỗi, có thể truyền from_encoding:
soup = BeautifulSoup(html_bytes, "html.parser", from_encoding="utf-8")
HTML không hợp lệ: Dùng parser html5lib để “sửa” tự động như trình duyệtviblo.asia.
8️⃣ Ví dụ thực tế – Scraping một trang tin
import requests
from bs4 import BeautifulSoup

url = "https://vnexpress.net"
resp = requests.get(url)
soup = BeautifulSoup(resp.text, "html.parser")

# Lấy tiêu đề các bài viết nổi bật
for a in soup.select("section.featured a"):
    title = a.get_text(strip=True)
    link = a["href"]
    print(f"{title}\n{link}\n")
Kết quả sẽ liệt kê các tiêu đề và đường dẫn, giống như trong tutorial của Kteamtutsplus.com.

9️⃣ Một vài mẹo “nhanh”
Cache: Khi làm scraping lớn, lưu HTML vào file để tránh gọi lại server.
Giới hạn tốc độ: Thêm time.sleep() giữa các request để không bị chặn.
Kiểm tra robots.txt: Đảm bảo tuân thủ quy định của website.
📚 Tổng kết
Cài đặt: pip install beautifulsoup4 (hoặc apt).
Khởi tạo: BeautifulSoup(content, parser).
Duyệt & tìm: find, find_all, select, .children/.parents.
Chỉnh sửa: thay đổi thuộc tính, thêm/xóa thẻ.
Parser: html.parser (mặc định), lxml, html5lib.
Ứng dụng: thu thập dữ liệu, trích xuất thông tin, xử lý HTML.
Hy vọng hướng dẫn này giúp bạn bắt đầu và phát triển các dự án web‑scraping hoặc xử lý HTML một cách hiệu quả! 🚀


H
T
V
Sources
