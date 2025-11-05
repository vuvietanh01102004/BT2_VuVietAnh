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

- Vô hiệu hoá IIS:
<img width="662" height="314" alt="image" src="https://github.com/user-attachments/assets/97ec8bf4-50e1-4b12-887f-a294a4352fa8" />

- Truy cập trang web: https://httpd.apache.org/ để truy cập Apache
<img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/6383bd4a-8313-4566-b5fe-30247c74dcf4" />

- Tải bản 32bit hoặc 64bit để phù hợp cho windows:
<img width="1919" height="929" alt="image" src="https://github.com/user-attachments/assets/eb8ab515-d3c2-4216-8694-d6d107d8f699" />

- Đặt Apache Win64 tại ổ D:
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/e3e26776-fea0-4cb9-83b9-ad09de5c5ba8" />

- Cấu hình các file  lấy domain: vuvietanh.com:
<img width="1916" height="1021" alt="image" src="https://github.com/user-attachments/assets/716aba47-a0c8-441a-856a-ed2ab90554d8" />

- Fake ip 127.0.0.1 cho domain web:
<img width="882" height="729" alt="image" src="https://github.com/user-attachments/assets/5b01d01e-c1bb-433e-bce0-a3599551f0b6" />

- Cài đặt và khởi chạy Apache:
<img width="1481" height="596" alt="image" src="https://github.com/user-attachments/assets/e42e32c8-5415-41c1-8e9e-ababe8a6c59a" />

- Kiểm tra kết quả:
<img width="1919" height="1004" alt="image" src="https://github.com/user-attachments/assets/f97e16e7-04f5-495e-8b5e-ab347761afa1" />

## 2. Cài đặt nodejs và nodered

- Mở cmd chạy quyền Admin để kiểm tra:
<img width="667" height="457" alt="image" src="https://github.com/user-attachments/assets/54548178-4d1b-48e7-a5af-138ce3cf11d9" />

- Mở cmd chạy bằng quyền Admin rồi vào thư mục D:\nodejs, chạy lệnh: npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered" :
<img width="1006" height="438" alt="image" src="https://github.com/user-attachments/assets/2bd9f63d-33e6-4d43-978d-5af44954d7d3" />

- Cài đặt Nodejs:
<img width="460" height="352" alt="image" src="https://github.com/user-attachments/assets/2580324d-2b3f-493f-820f-130a8514101a" />

- Cài đặt Nodered:
<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/d6d7cb8c-49c3-40be-a001-374031ecf116" />

- Cài đặt service bằng nssm:
<img width="1018" height="355" alt="image" src="https://github.com/user-attachments/assets/8dcfe1a0-fb2e-4136-96e1-ef74b15dc055" />

- Chạy service "a1-nodered" bằng lệnh: nssm start a1-nodered
<img width="1027" height="425" alt="image" src="https://github.com/user-attachments/assets/b7fc7566-53f1-4a9e-9a77-3d1368a9d54a" />

## 3. Tạo csdl trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name
- Tạo 1 Database:
<img width="448" height="662" alt="image" src="https://github.com/user-attachments/assets/c57ccd89-e71b-43ce-8f9f-a2b7abe6475b" />

- Tạo User:
<img width="1919" height="1019" alt="image" src="https://github.com/user-attachments/assets/92cc4046-381e-4316-b9e2-46ad05288ad4" />

- Tạo Table SINH_VIEN:
<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/7d182c20-e989-437e-9dcd-7f3b675d9459" />

<img width="758" height="259" alt="image" src="https://github.com/user-attachments/assets/efc17552-47bb-45c5-9a0b-fd5433234445" />

- IP:
<img width="950" height="200" alt="image" src="https://github.com/user-attachments/assets/a418fdfd-1682-41b0-82f1-3bdf7a60e793" />

- PORT:
<img width="1175" height="883" alt="image" src="https://github.com/user-attachments/assets/0ef30e64-7a74-4747-8c04-b6528bf49b5a" />

## 4. Cài đặt thư viện trên nodered
- Truy cập Nodered bằng URL: http://localhost:1880
<img width="1919" height="864" alt="image" src="https://github.com/user-attachments/assets/cc77a336-6c44-496f-bb96-b78eb3e72f99" />

- Cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus

- Ở giao diện nodered => ấn vào dấu 3 gạch trên cùng => Manage palette => chọn mục install để cài đặt các thư viện theo yêu cầu:

  + node-red-contrib-mssql-plus:
<img width="606" height="110" alt="image" src="https://github.com/user-attachments/assets/f1b5aad5-9227-4467-8213-bcf35ea3f3fb" />

  + node-red-node-mysql:
<img width="605" height="110" alt="image" src="https://github.com/user-attachments/assets/2f417b8c-be40-492b-a304-b2cf7e53ae8b" />

  + node-red-contrib-telegrambot:
<img width="607" height="108" alt="image" src="https://github.com/user-attachments/assets/5dc89cbd-48a6-494a-bb5a-7232ec4f61f2" />

  + node-red-contrib-moment:
<img width="603" height="106" alt="image" src="https://github.com/user-attachments/assets/cc1ebea4-e587-42df-8774-cb5f829a6698" />

  + node-red-contrib-influxdb:
<img width="607" height="108" alt="image" src="https://github.com/user-attachments/assets/b9c42e76-4582-4713-9feb-d216fc30447a" />

  + node-red-contrib-duckdns:
<img width="600" height="105" alt="image" src="https://github.com/user-attachments/assets/9fbcd385-464a-44fd-b855-964f877c5a45" />

  + node-red-contrib-cron-plus:
<img width="604" height="104" alt="image" src="https://github.com/user-attachments/assets/cf334632-aa50-4db8-a578-2320af40388d" />

- Chỉnh sửa file: "D:\nodejs\nodered\work\settings.js":
<img width="1045" height="295" alt="image" src="https://github.com/user-attachments/assets/1667ef0f-aaa6-4c0c-8fdc-db7244cd8722" />

- Mã hoá mật khẩu có thể dùng bằng: https://tms.tnut.edu.vn/pw.php
<img width="572" height="140" alt="image" src="https://github.com/user-attachments/assets/b53c82db-1238-4ae8-8c3f-c4eacf014271" />

- Chạy lại nodered:
<img width="781" height="400" alt="image" src="https://github.com/user-attachments/assets/7f90f912-577f-4341-bd5b-6ca5c50a0d27" />

- Truy cập lại nodered và nodered sẽ yêu cầu mật khẩu mới vào được giao diện cho admin: http://vuvietanh.com:1880
<img width="1919" height="966" alt="image" src="https://github.com/user-attachments/assets/8b8c649a-e6a2-48e4-8542-71ef8d9202df" />

## 5. Tạo api back-end bằng nodered
- Tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api

- Đặt URL cho http in:
<img width="1919" height="869" alt="image" src="https://github.com/user-attachments/assets/7242dc32-a380-4489-a4ca-52ffe823c6c5" />

- Function:
<img width="1509" height="865" alt="image" src="https://github.com/user-attachments/assets/4a713211-bdf8-4565-8e12-b02dafdc3518" />

- MSSQL:
<img width="1500" height="861" alt="image" src="https://github.com/user-attachments/assets/869fd0d5-4ebb-46c9-8a7a-58bdf2a58ef8" />

<img width="1493" height="869" alt="image" src="https://github.com/user-attachments/assets/4fbd0a68-0ae0-4cd2-9136-a635ebe2bfc4" />

- http response:
<img width="1502" height="868" alt="image" src="https://github.com/user-attachments/assets/cab84fff-45d0-4fc3-a154-d4e9b82897ce" />

- Debug:
<img width="1509" height="866" alt="image" src="https://github.com/user-attachments/assets/4d5f5b9a-ad39-4cdb-b05f-c9891feac23a" />

- Tạo flow gồm 4 node (nối dây):
<img width="1919" height="867" alt="image" src="https://github.com/user-attachments/assets/417d66fa-eda7-4ca6-b3f3-c9ecb3c6b4cb" />

- Chạy thử API:
















