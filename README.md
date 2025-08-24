# 📖 Hướng dẫn sử dụng BeautifulSoup4 (bs4) -- Tiếng Việt

[![Made with
Python](https://img.shields.io/badge/Made%20with-Python-blue?logo=python)](https://www.python.org/)
[![BeautifulSoup4](https://img.shields.io/badge/BeautifulSoup4-bs4-green)](https://www.crummy.com/software/BeautifulSoup/)
[![License:
MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

------------------------------------------------------------------------

## 📌 Mục lục

-   [1️⃣ Cài đặt](#1️⃣-cài-đặt)
-   [2️⃣ Nhập thư viện](#2️⃣-nhập-thư-viện)
-   [3️⃣ Cấu trúc đối tượng](#3️⃣-cấu-trúc-đối-tượng)
-   [4️⃣ Duyệt cây DOM](#4️⃣-duyệt-cây-dom)
-   [5️⃣ Tìm kiếm phần tử](#5️⃣-tìm-kiếm-phần-tử)
-   [6️⃣ Thao tác với nội dung](#6️⃣-thao-tác-với-nội-dung)
-   [7️⃣ Xử lý mã hoá và lỗi HTML](#7️⃣-xử-lý-mã-hoá-và-lỗi-html)
-   [8️⃣ Ví dụ thực tế](#8️⃣-ví-dụ-thực-tế--scraping-một-trang-tin)
-   [9️⃣ Một vài mẹo "nhanh"](#9️⃣-một-vài-mẹo-nhanh)
-   [📚 Tổng kết](#-tổng-kết)

------------------------------------------------------------------------

## 1️⃣ Cài đặt

  Phương pháp           Lệnh
  --------------------- ------------------------------------
  pip (cách phổ biến)   `pip install beautifulsoup4`
  pip3 (cho Python 3)   `pip3 install beautifulsoup4`
  apt (Ubuntu/Debian)   `sudo apt-get install python3-bs4`
  easy_install          `easy_install beautifulsoup4`

> **Lưu ý**: Gói trên PyPI có tên `beautifulsoup4`. Khi dùng pip, hãy
> chắc chắn dùng phiên bản pip tương ứng với Python bạn đang chạy (pip
> vs pip3).

------------------------------------------------------------------------

## 2️⃣ Nhập thư viện

``` python
from bs4 import BeautifulSoup

html = "<html><body><h1>Tiêu đề</h1></body></html>"
soup = BeautifulSoup(html, "html.parser")   # parser mặc định của Python
```

### Các parser thường dùng

  ---------------------------------------------------------------------------
  Parser          Ghi chú
  --------------- -----------------------------------------------------------
  `html.parser`   Có sẵn trong Python, nhanh nhưng hạn chế với HTML lỗi.

  `lxml`          Nhanh, mạnh mẽ, cần cài lxml (`pip install lxml`).

  `html5lib`      Đọc giống trình duyệt, chậm hơn nhưng chuẩn nhất.
  ---------------------------------------------------------------------------

------------------------------------------------------------------------

## 3️⃣ Cấu trúc đối tượng

-   **soup** -- đối tượng gốc, đại diện cho toàn bộ tài liệu.
-   **Tag (thẻ)** -- ví dụ `soup.title`, `soup.body`.
-   **NavigableString** -- nội dung văn bản bên trong thẻ.

``` python
title_tag = soup.title          # <title>...</title>
print(title_tag.string)         # nội dung của thẻ title
```

------------------------------------------------------------------------

## 4️⃣ Duyệt cây DOM

  ----------------------------------------------------------------------------
  Phương thức           Mô tả
  --------------------- ------------------------------------------------------
  `.children` /         Lặp qua các phần tử con (trực tiếp hoặc đệ quy).
  `.descendants`        

  `.parent` /           Truy cập phần tử cha, hoặc toàn bộ chuỗi cha.
  `.parents`            

  `.next_sibling` /     Thẻ kế tiếp/trước trong cùng cấp.
  `.previous_sibling`   

  `.next_element` /     Phần tử kế tiếp/trước trong luồng tài liệu.
  `.previous_element`   
  ----------------------------------------------------------------------------

Ví dụ:

``` python
for parent in soup.h1.parents:
    print(parent.name)
```

------------------------------------------------------------------------

## 5️⃣ Tìm kiếm phần tử

### 5.1 `find()` và `find_all()`

``` python
first_a = soup.find("a")  # Tìm thẻ <a> đầu tiên

links = soup.find_all("a", class_="link")  # Tất cả thẻ <a> có class="link"
```

### 5.2 Tham số `name`, `attrs`, `string`

``` python
div = soup.find("div", id="content")  # Tìm thẻ <div> có id="content"

short = soup.find(lambda tag: isinstance(tag, NavigableString) and len(tag) < 10)
```

### 5.3 CSS selector (`select`)

``` python
nav_links = soup.select("nav a")  # Tất cả thẻ <a> trong <nav>
```

------------------------------------------------------------------------

## 6️⃣ Thao tác với nội dung

``` python
# Lấy văn bản thuần (loại bỏ thẻ)
text = soup.get_text(separator="\n", strip=True)

# Thay đổi thuộc tính
img = soup.find("img")
img["src"] = "new_image.jpg"

# Thêm thẻ mới
new_tag = soup.new_tag("p")
new_tag.string = "Đoạn văn mới"
soup.body.append(new_tag)
```

------------------------------------------------------------------------

## 7️⃣ Xử lý mã hoá và lỗi HTML

-   **Mã hoá**: Beautiful Soup tự động phát hiện và chuyển sang Unicode.
    Nếu gặp lỗi:

``` python
soup = BeautifulSoup(html_bytes, "html.parser", from_encoding="utf-8")
```

-   **HTML không hợp lệ**: Dùng parser `html5lib` để tự động "sửa" như
    trình duyệt.

------------------------------------------------------------------------

## 8️⃣ Ví dụ thực tế -- Scraping một trang tin

``` python
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
```

------------------------------------------------------------------------

## 9️⃣ Một vài mẹo "nhanh"

-   **Cache**: Khi scraping lớn, lưu HTML vào file để tránh gọi lại
    server.
-   **Giới hạn tốc độ**: Thêm `time.sleep()` giữa các request để tránh
    bị chặn.
-   **Kiểm tra robots.txt**: Đảm bảo tuân thủ quy định của website.

------------------------------------------------------------------------

## 📚 Tổng kết

-   **Cài đặt**: `pip install beautifulsoup4` (hoặc apt).
-   **Khởi tạo**: `BeautifulSoup(content, parser)`.
-   **Duyệt & tìm**: `find`, `find_all`, `select`, `.children` /
    `.parents`.
-   **Chỉnh sửa**: thay đổi thuộc tính, thêm/xoá thẻ.
-   **Parser**: `html.parser`, `lxml`, `html5lib`.
-   **Ứng dụng**: thu thập dữ liệu, trích xuất thông tin, xử lý HTML.

------------------------------------------------------------------------

## 📝 Giấy phép

Dự án phát hành theo giấy phép **MIT License** -- Xem chi tiết tại
[LICENSE](LICENSE).

🚀 Hy vọng hướng dẫn này giúp bạn bắt đầu và phát triển dự án
web-scraping hiệu quả!
