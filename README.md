#  Môn: Phát triển ứng dụng trên nền web
# Vũ Việt Anh   
# MSSV: K225480106082
# BÀI TẬP VỀ NHÀ 02:
## 1. Cài đặt Apache web server:
- Vô hiệu hoá IIS: nếu iis đang chạy thì mở cmd quyền admin để chạy lệnh: iisreset /stop
- Download apache server, giải nén ra ổ D, cấu hình các file:
  + D:\Apache24\conf\httpd.conf
  + D:Apache24\conf\extra\httpd-vhosts.conf
  để tạo website với domain: fullname.com
  code web sẽ đặt tại thư mục: `D:\Apache24\fullname` (fullname ko dấu, liền nhau)
- sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này
  ví dụ sv tên là: `Đỗ Duy Cốp` thì tạo website với domain là fullname ko dấu, liền nhau: `doduycop.com`
- thao tác dòng lệnh trên file `D:\Apache24\bin\httpd.exe` với các tham số `-k install` và `-k start` để cài đặt và khởi động web server apache.
## 2. Cài đặt nodejs và nodered => Dùng làm backend:
- Cài đặt nodejs:
  + download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`  (đây ko phải bản mới nhất, nhưng ổn định)
  + cài đặt vào thư mục `D:\nodejs`
- Cài đặt nodered:
  + chạy cmd, vào thư mục `D:\nodejs`, chạy lệnh `npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"`
  + download file: https://nssm.cc/release/nssm-2.24.zip
    giải nén được file nssm.exe
    copy nssm.exe vào thư mục `D:\nodejs\nodered\`
  + tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):
@echo off
REM fix path
set PATH=D:\nodejs;%PATH%
REM Run Node-RED
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`
## 3. Tạo csdl tuỳ ý trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name
## 4. Cài đặt thư viện trên nodered:
- truy cập giao diện nodered bằng url: http://localhost:1880
- cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus
- Sửa file `D:\nodejs\nodered\work\settings.js` : 
  tìm đến chỗ adminAuth, bỏ comment # ở đầu dòng (8 dòng), thay chuỗi mã hoá mật khẩu bằng chuỗi mới
    adminAuth: {
        type: "credentials",
        users: [{
            username: "admin",
            password: "chuỗi_mã_hoá_mật_khẩu",
            permissions: "*"
        }]
    },   
   với mã hoá mật khẩu có thể thiết lập bằng tool: https://tms.tnut.edu.vn/pw.php
- chạy lại nodered bằng cách: mở cmd, vào thư mục `D:\nodejs\nodered` và chạy lệnh `nssm restart a1-nodered`
  khi đó nodered sẽ yêu cầu nhập mật khẩu mới vào được giao diện cho admin tại: http://localhost:1880
## 5. tạo api back-end bằng nodered:
- tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api
- thêm node `MSSQL` để truy vấn tới cơ sở dữ liệu
- logic flow sẽ gồm 4 node theo thứ tự sau (thứ tự nối dây): 
  + 5.1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem
  + 5.2. function : để tiền xử lý dữ liệu gửi đến
  + 5.3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý
  + 5.4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json
  có thể thêm node `debug` để quan sát giá trị trung gian.
- test api thông qua trình duyệt, ví dụ: http://localhost:1880/timkiem?q=thị
## 6. Tạo giao diện front-end:
- html form gồm các file : index.html, fullname.js, fullname.css
  cả 3 file này đặt trong thư mục: `D:\Apache24\fullname`
  nhớ thay fullname là tên của bạn, viết liền, ko dấu, chữ thường, vd tên là Đỗ Duy Cốp thì fullname là `doduycop`
  khi đó 3 file sẽ là: index.html, doduycop.js và doduycop.css
- index.html và fullname.css: trang trí tuỳ ý, có dấu ấn cá nhân, có form nhập được thông tin.
- fullname.js: lấy dữ liệu trên form, gửi đến api nodered đã làm ở bước 2.5, nhận về json, dùng json trả về để tạo giao diện phù hợp với kết quả truy vấn của bạn.
## 7. Nhận xét bài làm của mình:
- đã hiểu quá trình cài đặt các phần mềm và các thư viện như nào?
- đã hiểu cách sử dụng nodered để tạo api back-end như nào?
- đã hiểu cách frond-end tương tác với back-end ra sao?

# Bài làm

## 1. Cài đặt Apache web server
- Truy cập trang web: https://httpd.apache.org/ để truy cập Apache
<img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/6383bd4a-8313-4566-b5fe-30247c74dcf4" />
- Tải bản 32bit hoặc 64bit để phù hợp cho windows:
<img width="1919" height="929" alt="image" src="https://github.com/user-attachments/assets/eb8ab515-d3c2-4216-8694-d6d107d8f699" />
- Đặt Apache Win64 tại ổ D:
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/e3e26776-fea0-4cb9-83b9-ad09de5c5ba8" />
- Cấu hình các file  lấy domain: vuvietanh.com:
<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/e9aba60d-29b3-4784-b02f-8a696c618774" />
- Fake ip 127.0.0.1 cho domain web:
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/e84bc270-5d8a-4856-b362-a1fa1d9124e4" />

## 2. Cài đặt nodejs và nodered
- Cài đặt Nodejs:
<img width="460" height="352" alt="image" src="https://github.com/user-attachments/assets/2580324d-2b3f-493f-820f-130a8514101a" />
- Cài đặt Nodered:
<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/d6d7cb8c-49c3-40be-a001-374031ecf116" />
- Cài đặt service và chạy service `a1-nodered`:
<img width="1065" height="681" alt="image" src="https://github.com/user-attachments/assets/aa99b38f-50f6-4b35-b43b-6203df0f3f97" />

## 3. Tạo csdl trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name
- CSDL trong bài:
<img width="718" height="311" alt="image" src="https://github.com/user-attachments/assets/f9f29140-59bd-4181-a196-612b9ff2c5b5" />

<img width="926" height="236" alt="image" src="https://github.com/user-attachments/assets/45b72c30-aa18-4d1f-9eed-514159779ecf" />

## 4. Cài đặt thư viện trên nodered
- Truy cập Nodered bằng URL: http://localhost:1880
<img width="1919" height="865" alt="image" src="https://github.com/user-attachments/assets/03e91cb5-3a48-4c82-bf89-64e926c4ebe5" />
- Cài đặt các thư viện:
<img width="1476" height="757" alt="image" src="https://github.com/user-attachments/assets/9d2e1bc5-bc62-48db-ba1d-aeb1d7aa9f98" />
- Chỉnh sửa file: "setting.js" ở D:\nodejs\nodered\work\settings.js
<img width="1008" height="274" alt="image" src="https://github.com/user-attachments/assets/fe29c26c-b3b8-4f34-992a-28fb273a0450" />























