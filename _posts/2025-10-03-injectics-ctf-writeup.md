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
