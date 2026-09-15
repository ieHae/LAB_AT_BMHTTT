# LAB 1 - Bắt gói tin Telnet và SSH

- Họ và tên: Lê Văn Hà
- MSSV: 1150080091
- Tên bài Lab: Examining SSH & Telnet in Wireshark

## Nội dung đã thực hiện
- Thiết lập Ubuntu Server, Windows 11 Client và Kali Linux Attacker.
- Kiểm tra kết nối mạng giữa các máy.
- Cấu hình Telnet và SSH trên Server.
- Dùng PuTTY để kết nối từ Windows 11.
- Dùng Wireshark để bắt và phân tích TCP/23 và TCP/22.
- So sánh Telnet và SSH bằng Follow TCP Stream.

## Kết quả thực hiện
- Telnet kết nối thành công và dữ liệu có thể đọc được khi bắt đúng lưu lượng.
- SSH kết nối thành công nhưng payload được mã hóa.
- Wireshark vẫn quan sát được IP, port, kích thước và thời gian gói SSH.

## Môi trường
- Server: Ubuntu Server 26.04.1 LTS
- Client: Windows 11 + PuTTY
- Attacker: Kali Linux + Wireshark
