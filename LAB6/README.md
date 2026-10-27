# LAB 6 - Xác thực Active Directory và chính sách truy cập trên pfSense

## Thông tin sinh viên

- Họ và tên: Lê Văn Hà
- MSSV: 1150080091
- Mã lớp: 11_THMT

## Mục tiêu thực hành

LAB 6 tập trung xây dựng hệ thống mạng mô phỏng doanh nghiệp bằng pfSense, kết hợp Active Directory để xác thực người dùng và triển khai các chính sách kiểm soát truy cập.

Các nội dung chính:

- Xây dựng mô hình mạng gồm WAN, MGMT, LAN và DMZ.
- Cấu hình pfSense làm firewall và gateway.
- Tích hợp Active Directory với pfSense thông qua LDAP.
- Sử dụng Captive Portal để xác thực người dùng domain.
- Tạo Firewall Alias để quản lý máy trạm và website.
- Cấu hình Schedule để giới hạn truy cập theo thời gian.
- Thiết lập các chính sách cho Client-A và Client-B.
- Cô lập DMZ nhằm hạn chế truy cập vào mạng LAN.
- Kiểm tra hoạt động của các Firewall Rules.
- Phân tích cơ chế xử lý rule theo thứ tự ưu tiên.

Phạm vi bài thực hành gồm kịch bản 1 đến kịch bản 5. Kịch bản 6 và 7 được miễn theo yêu cầu của giảng viên.

## Môi trường thực hành

- Phần mềm ảo hóa: VMware Workstation.
- Firewall: pfSense Community Edition.
- Hệ thống máy chủ: Windows Server sử dụng Active Directory Domain Services và DNS.
- Máy trạm: Windows 11.
- Máy chủ web DMZ: Ubuntu Server và Nginx.
- Giao thức xác thực: LDAP.
- Mạng thực hành: VMware Host-Only và các mạng ảo riêng.
- Công cụ kiểm tra: trình duyệt web, ping, curl và giao diện quản trị pfSense.

Các máy ảo được bố trí trong mạng LAB để kiểm tra chính sách truy cập mà không ảnh hưởng đến hệ thống mạng bên ngoài.

## Mô hình mạng

Hệ thống được chia thành bốn vùng mạng chính:

| Vùng mạng | Địa chỉ | Chức năng |
|---|---|---|
| WAN | DHCP | Kết nối ra mạng ngoài thông qua NAT |
| MGMT | 192.168.56.0/24 | Quản trị pfSense |
| LAN | 10.6.0.0/24 | Chứa Domain Controller và các máy trạm |
| DMZ | 172.16.6.0/24 | Chứa máy chủ web mô phỏng |

### Địa chỉ IP các thiết bị

| Thiết bị | Địa chỉ IP | Vai trò |
|---|---|---|
| pfSense MGMT | 192.168.56.254 | Giao diện quản trị |
| pfSense LAN | 10.6.0.1 | Gateway mạng LAN |
| pfSense DMZ | 172.16.6.1 | Gateway mạng DMZ |
| DC01 | 10.6.0.10 | Active Directory và DNS |
| Client-A | 10.6.0.21 | Máy trạm sử dụng tài khoản sv_a |
| Client-B | 10.6.0.22 | Máy trạm sử dụng tài khoản sv_b |
| DMZ-WEB | 172.16.6.10 | Máy chủ web mô phỏng |

Domain sử dụng trong bài thực hành là `lab6.test`.

## Cách dựng môi trường

### 1. Chuẩn bị mạng ảo VMware

1. Khởi động VMware Workstation.
2. Mở Virtual Network Editor.
3. Chuẩn bị mạng NAT cho WAN.
4. Sử dụng VMnet4 với subnet `192.168.56.0/24` cho MGMT.
5. Sử dụng VMnet5 với subnet `10.6
