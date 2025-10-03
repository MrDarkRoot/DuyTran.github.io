---
layout: post
title: "Writeup: Khai Thác Injectionics - SQLi, Second-Order SQLi & SSTI RCE"
date: 2025-10-03 12:00:00 +0700
categories: [CTF, Web_Security, Writeup]
tags: [SQLi, SecondOrderSQLi, SSTI, RCE, Twig, Nmap, Dirsearch]
author: [ĐIỀN TÊN TÁC GIẢ CỦA BẠN] 
image: /assets/img/injectics/main.jpg # Thay bằng đường dẫn ảnh bìa nếu có
---

# InJectics - Writeup Chi Tiết

Bài viết này mô tả các bước khai thác thành công box **InJectics**, tập trung vào các lỗ hổng **SQL Injection (Auth Bypass)**, **Second-Order SQL Injection** và **Server-Side Template Injection (SSTI)** dẫn đến **Remote Code Execution (RCE)**.

---

## Bước 1: Quét các cổng trên server

Sử dụng **nmap** để xác định các cổng đang mở và dịch vụ đang chạy nhằm phát hiện các dịch vụ ẩn.

> [**HÌNH ẢNH:** Kết quả nmap scan]

---

## Bước 2: Tương tác và khám phá các chức năng web

Dò thủ công các chức năng chính trên trang web. Quan sát các hành vi bất thường, URL lạ, và thông tin ẩn trong mã nguồn **HTML/JS**.

> [**HÌNH ẢNH:** Khám phá giao diện web]

---

## Bước 3: Dò file và folder ẩn với Dirsearch

Sử dụng **dirsearch** để dò tìm file/folder bị ẩn.

### 🛠️ Lệnh Dirsearch

```bash
dirsearch -u [http://10.201.7.209](http://10.201.7.209)

🎯 Kết quả nổi bật

    File quan trọng: /composer.json, /composer.lock, /mail.log (chứa hint), /login.php, /dashboard.php.

    Phân tích Twig: File composer.json xác nhận sử dụng template engine twig/twig, gợi ý khả năng khai thác SSTI.


{{ 7 * 7 }}  -> 49

=> Khẳng định có thể khai thác SSTI.

Bước 4: Khai thác SQL Injection (Auth Bypass)

💌 Gợi ý từ mail.log

Nội dung /mail.log tiết lộ thông tin đăng nhập admin và dev.

    [HÌNH ẢNH: Nội dung mail.log]

🛑 Bypass Client-Side Filter

Phân tích script.js cho thấy có filter ở client-side. Ta bỏ qua logic JavaScript này và gửi request trực tiếp đến server bằng Burp Suite để thực hiện SQL Injection.

🛠️ Auth Bypass với Burp Suite Intruder

    Tải PayloadBox SQLi Auth Bypass Wordlist vào Intruder.

    Chạy brute-force và tìm payload thành công.

    [HÌNH ẢNH: Payloads thành công]

Kết quả: Đăng nhập thành công với tài khoản Dev.

Bước 5: Khai thác SQLi Bậc 2 (Second-Order SQL Injection)

Trong Dev Panel, SQL Injection không phản hồi ngay lập tức trên các form nhập liệu, mà chỉ trigger SQL từ dữ liệu đã lưu sau khi thực hiện thao tác kế tiếp.

Đây là dấu hiệu của Second-Order SQL Injection.

    [HÌNH ẢNH: Thử nghiệm Second-Order SQL Injection]

Bước 6: Khai thác RCE thông qua SSTI Bypass

Sử dụng thông tin từ mail.log để đăng nhập bằng tài khoản Admin: superadmin@injectics.thm.

🚩 SSTI Injection ở Tên Admin

Sau khi đăng nhập Admin, ta phát hiện flag nằm trong output của template engine và có thể inject SSTI thông qua tên hiển thị của admin.

    [HÌNH ẢNH: Thao tác SSTI Injection]

💥 Payload RCE

Sử dụng kỹ thuật filter bypass để thực thi lệnh hệ thống:

{{ ['id', ''] | sort('passthru') }}


Giải thích: sort('passthru') gọi hàm passthru trên các phần tử của mảng, cho phép Remote Code Execution.

🎯 Kết quả: Truy xuất Flag

Thực thi lệnh cat /flag.txt để lấy flag cuối cùng.

    [HÌNH ẢNH: Kết quả RCE và flag]

📝 Kết luận

Bài khai thác đã chứng minh sự hiệu quả của việc kiểm tra toàn diện: Bypass Client-Side JS → SQLi Auth Bypass → Second-Order SQLi → SSTI RCE.

💡 Tips Khai Thác

    Luôn kiểm tra hậu quả phía sau (second-order), không chỉ phản hồi ngay lập tức.

    Khi gặp template engine (Twig), hãy thử mọi dạng SSTI (bao gồm cả filter bypass như sort('passthru')).

🫡 Chúc mọi người khai thác thành công!
