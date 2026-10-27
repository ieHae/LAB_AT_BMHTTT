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
5. Sử dụng VMnet5 với subnet `10.6.0.0/24` cho LAN.
6. Chuẩn bị một VMnet riêng cho DMZ với subnet `172.16.6.0/24`.
7. Cấu hình các máy ảo kết nối vào đúng vùng mạng.
8. Kiểm tra để tránh trùng địa chỉ IP giữa các thiết bị.

### 2. Cấu hình pfSense

pfSense sử dụng bốn card mạng tương ứng:

- WAN: kết nối NAT.
- MGMT: kết nối mạng quản trị.
- LAN: kết nối mạng nội bộ.
- DMZ: kết nối vùng máy chủ web.

Các bước cấu hình:

1. Khởi động máy ảo pfSense.
2. Kiểm tra và gán đúng interface.
3. Cấu hình WAN nhận IP bằng DHCP.
4. Đặt MGMT là `192.168.56.254/24`.
5. Đặt LAN là `10.6.0.1/24`.
6. Đặt DMZ là `172.16.6.1/24`.
7. Lưu cấu hình.
8. Truy cập WebGUI bằng địa chỉ `https://192.168.56.254`.
9. Kiểm tra trạng thái các interface.

### 3. Chuẩn bị Active Directory

Máy chủ DC01 sử dụng Windows Server với địa chỉ `10.6.0.10`.

Các bước:

1. Cấu hình IP tĩnh cho Domain Controller.
2. Cài đặt Active Directory Domain Services.
3. Cài đặt dịch vụ DNS.
4. Tạo domain `lab6.test`.
5. Tạo OU `LAB6-Users`.
6. Tạo tài khoản `sv_a`.
7. Tạo tài khoản `sv_b`.
8. Tạo tài khoản dịch vụ `svc_pfsense_ldap`.
9. Kiểm tra hoạt động của Active Directory và DNS.

Tài khoản LDAP được sử dụng để phục vụ truy vấn thông tin người dùng, không sử dụng tài khoản Domain Administrator làm tài khoản kết nối.

### 4. Chuẩn bị Client-A và Client-B

Client-A:

- IP: `10.6.0.21`
- Gateway: `10.6.0.1`
- DNS: `10.6.0.10`
- User kiểm thử: `sv_a`

Client-B:

- IP: `10.6.0.22`
- Gateway: `10.6.0.1`
- DNS: `10.6.0.10`
- User kiểm thử: `sv_b`

Kiểm tra khả năng kết nối mạng và phân giải DNS trước khi thực hiện cấu hình chính sách.

### 5. Chuẩn bị máy chủ DMZ

DMZ-WEB sử dụng Ubuntu Server và Nginx.

Các địa chỉ website mô phỏng:

| IP | Alias | Mục đích |
|---|---|---|
| 172.16.6.10 | GENERAL_SITE | Website thông thường |
| 172.16.6.20 | JOB_SITE | Website tìm việc |
| 172.16.6.30 | SOCIAL_SITE | Mạng xã hội |
| 172.16.6.40 | RESTRICTED_SITE | Nội dung bị hạn chế |

Một máy chủ Nginx có thể sử dụng nhiều địa chỉ IP để mô phỏng các website khác nhau.

## Các nội dung thực hành

### Nội dung 1 - Tích hợp LDAP với Active Directory

Trên pfSense truy cập:

`System > User Manager > Authentication Servers`

Các thông số:

- Authentication Server: `LAB6-AD`
- Type: LDAP
- Server IP: `10.6.0.10`
- Port: `389`
- Protocol Version: `3`
- Base DN: `DC=lab6,DC=test`
- Authentication Containers: `OU=LAB6-Users,DC=lab6,DC=test`
- User Naming Attribute: `samAccountName`

Sau khi lưu cấu hình, sử dụng:

`Diagnostics > Authentication`

Kiểm tra xác thực lần lượt với hai tài khoản `sv_a` và `sv_b`.

Kết quả mong đợi:

- pfSense có thể kết nối đến Domain Controller.
- Hai tài khoản domain được LDAP nhận diện.
- Thông tin xác thực đúng được chấp nhận.
- Thông tin xác thực sai bị từ chối.

**Trạng thái: Chờ xác nhận bằng kết quả kiểm thử thực tế.**

### Nội dung 2 - Captive Portal xác thực người dùng domain

Truy cập:

`Services > Captive Portal`

Các bước:

1. Tạo Captive Portal Zone `LAB6_USERS`.
2. Chọn interface LAN.
3. Bật Captive Portal.
4. Chọn xác thực thông qua Authentication Backend.
5. Sử dụng Authentication Server `LAB6-AD`.
6. Lưu và áp dụng cấu hình.
7. Truy cập trang HTTP thử nghiệm từ Client-A.
8. Kiểm tra giao diện đăng nhập.
9. Đăng nhập bằng tài khoản `sv_a`.
10. Kiểm tra tương tự với Client-B và `sv_b`.

Kết quả mong đợi:

- Captive Portal yêu cầu xác thực.
- Người dùng hợp lệ được đăng nhập.
- pfSense ghi nhận phiên đăng nhập của người dùng.

LDAP giúp xác thực danh tính, còn Firewall Rules thông thường vẫn quản lý lưu lượng theo địa chỉ IP, port, interface và thời gian.

**Trạng thái: Chờ xác nhận bằng kết quả kiểm thử thực tế.**

### Nội dung 3 - Tạo Firewall Alias

Truy cập:

`Firewall > Aliases`

Các Alias được sử dụng:

| Alias | Giá trị |
|---|---|
| CLIENT_A | 10.6.0.21 |
| CLIENT_B | 10.6.0.22 |
| POLICY_CLIENTS | 10.6.0.21, 10.6.0.22 |
| GENERAL_SITE | 172.16.6.10 |
| JOB_SITE | 172.16.6.20 |
| SOCIAL_SITE | 172.16.6.30 |
| RESTRICTED_SITE | 172.16.6.40 |
| WEB_PORTS | 80, 443 |

Alias giúp cấu hình firewall dễ đọc và dễ quản lý hơn so với việc nhập nhiều địa chỉ IP trực tiếp.

### Nội dung 4 - Cấu hình Schedule

Truy cập:

`Firewall > Schedules`

Các Schedule:

| Schedule | Thời gian |
|---|---|
| LUNCH_WINDOW | 11:30–13:00, thứ Hai đến thứ Sáu |
| WORK_HOURS | 08:00–17:00, thứ Hai đến thứ Sáu |
| TEST_NOW | Khoảng thời gian ngắn phục vụ kiểm thử |

Schedule được sử dụng để quyết định thời điểm một Firewall Rule có hiệu lực.

Khi một rule Pass hết thời gian hoạt động, cần có rule Block phù hợp phía dưới để ngăn truy cập ngoài khung giờ cho phép.

## Các kịch bản kiểm thử

### Kịch bản 1 - Chặn Client-A truy cập JOB_SITE

**Yêu cầu:** Client-A không được truy cập website mô phỏng tìm việc nhưng vẫn được truy cập website thông thường.

Cấu hình:

- Source: `CLIENT_A`
- Destination: `JOB_SITE`
- Protocol: TCP
- Destination Port: `80`
- Action: Block

Tạo thêm rule Pass cho Client-A đến GENERAL_SITE.

Kiểm thử:

1. Từ Client-A truy cập `http://172.16.6.10`.
2. Kiểm tra khả năng tải website thông thường.
3. Truy cập `http://172.16.6.20`.
4. Kiểm tra lưu lượng bị chặn.
5. Đối chiếu thứ tự Firewall Rules.

Kết quả mong đợi:

- GENERAL_SITE: Allow.
- JOB_SITE: Block.

**Trạng thái: Chưa có bằng chứng kiểm thử để đánh dấu PASS.**

### Kịch bản 2 - Chặn RESTRICTED_SITE đối với cả hai Client

**Yêu cầu:** Client-A và Client-B đều không được truy cập RESTRICTED_SITE.

Cấu hình:

- Action: Block
- Source: `POLICY_CLIENTS`
- Destination: `RESTRICTED_SITE`
- Port: TCP/80

Kiểm thử:

1. Client-A truy cập `http://172.16.6.40`.
2. Client-B truy cập `http://172.16.6.40`.
3. Kiểm tra truy cập bị chặn.
4. Truy cập GENERAL_SITE để đối chiếu.

Kết quả mong đợi:

- Client-A đến RESTRICTED_SITE: Block.
- Client-B đến RESTRICTED_SITE: Block.
- Cả hai vẫn có thể truy cập GENERAL_SITE khi có rule cho phép.

**Trạng thái: Chưa có bằng chứng kiểm thử để đánh dấu PASS.**

### Kịch bản 3 - Client-B chỉ được truy cập SOCIAL_SITE trong giờ nghỉ

**Yêu cầu:** Client-B chỉ được truy cập website mạng xã hội trong khung giờ nghỉ trưa.

Schedule:

`LUNCH_WINDOW: 11:30–13:00, thứ Hai đến thứ Sáu`

Cấu hình:

Rule 1:

- Action: Pass
- Source: `CLIENT_B`
- Destination: `SOCIAL_SITE`
- Port: TCP/80
- Schedule: `LUNCH_WINDOW`

Rule 2:

- Action: Block
- Source: `CLIENT_B`
- Destination: `SOCIAL_SITE`
- Port: TCP/80
- Schedule: Always

Kiểm thử:

1. Kiểm tra truy cập khi Schedule đang hoạt động.
2. Sử dụng TEST_NOW nếu cần kiểm thử nhanh.
3. Kiểm tra truy cập khi Schedule không hoạt động.
4. So sánh hai trạng thái.
5. Khôi phục Schedule LUNCH_WINDOW sau khi kiểm thử.

Kết quả mong đợi:

- Trong giờ nghỉ: Allow.
- Ngoài giờ nghỉ: Block.

**Trạng thái: Chưa có bằng chứng kiểm thử để đánh dấu PASS.**

### Kịch bản 4 - Chỉ cho phép HTTP/HTTPS trong giờ hành chính

**Yêu cầu:** Giới hạn dịch vụ truy cập của Client-A và Client-B theo thời gian làm việc.

Schedule:

`WORK_HOURS: 08:00–17:00, thứ Hai đến thứ Sáu`

Cấu hình:

Rule 1:

- Action: Pass
- Source: `POLICY_CLIENTS`
- Destination: Any
- Port: `WEB_PORTS`
- Schedule: `WORK_HOURS`

Rule 2:

- Action: Block
- Source: `POLICY_CLIENTS`
- Destination: Any
- Schedule: Always

Kiểm thử:

1. Kiểm tra HTTP trong giờ hành chính.
2. Kiểm tra HTTPS trong giờ hành chính.
3. Kiểm tra lưu lượng không thuộc cổng web.
4. Kiểm tra khi Schedule không hoạt động.
5. Đối chiếu kết quả.

Kết quả mong đợi:

- HTTP/HTTPS được cho phép trong giờ hành chính nếu không bị rule ưu tiên cao hơn chặn.
- Các dịch vụ khác bị chặn theo chính sách.
- Ngoài giờ hành chính, lưu lượng thuộc phạm vi rule Block bị từ chối.

**Trạng thái: Chưa có bằng chứng kiểm thử để đánh dấu PASS.**

### Kịch bản 5 - Cô lập DMZ khỏi LAN

**Yêu cầu:** Không cho phép thiết bị DMZ chủ động truy cập mạng LAN, nhưng LAN vẫn có thể truy cập các dịch vụ cần thiết ở DMZ.

Rule trên interface DMZ:

- Action: Block
- Source: DMZ net
- Destination: LAN net

Rule trên interface LAN:

- Action: Pass
- Source: `POLICY_CLIENTS`
- Destination: `GENERAL_SITE`
- Port: TCP/80

Kiểm thử:

1. Client-A truy cập `http://172.16.6.10`.
2. Kiểm tra truy cập website từ LAN.
3. Từ DMZ-WEB thử kết nối mới đến DC01 `10.6.0.10`.
4. Kiểm tra rule chặn trên interface DMZ.
5. Phân biệt kết nối do LAN khởi tạo với kết nối mới do DMZ khởi tạo.

Kết quả mong đợi:

- LAN đến GENERAL_SITE: Allow theo chính sách.
- DMZ khởi tạo kết nối đến LAN: Block.

**Trạng thái: Chưa có bằng chứng kiểm thử để đánh dấu PASS.**

## Bảng tổng hợp kết quả

| Nội dung | Kết quả mong đợi | Trạng thái |
|---|---|---|
| LDAP sv_a | Xác thực thành công | Chờ kiểm thử |
| LDAP sv_b | Xác thực thành công | Chờ kiểm thử |
| Captive Portal | Đăng nhập bằng tài khoản domain | Chờ kiểm thử |
| Kịch bản 1 | Client-A bị chặn JOB_SITE | Chờ kiểm thử |
| Kịch bản 2 | Cả hai Client bị chặn RESTRICTED_SITE | Chờ kiểm thử |
| Kịch bản 3 | SOCIAL_SITE chỉ cho phép trong giờ nghỉ | Chờ kiểm thử |
| Kịch bản 4 | HTTP/HTTPS theo giờ hành chính | Chờ kiểm thử |
| Kịch bản 5 | DMZ không chủ động truy cập LAN | Chờ kiểm thử |

## Các lỗi có thể gặp và hướng xử lý

### 1. Không truy cập được WebGUI pfSense

- Kiểm tra card MGMT trên VMware.
- Kiểm tra IP của máy quản trị.
- Kiểm tra IP MGMT trên pfSense.
- Kiểm tra khả năng kết nối đến `192.168.56.254`.
- Kiểm tra Firewall Rules trên interface quản trị.

### 2. LDAP không xác thực được

- Kiểm tra kết nối từ pfSense đến DC01.
- Kiểm tra cổng LDAP 389.
- Kiểm tra Base DN.
- Kiểm tra Authentication Container.
- Kiểm tra tài khoản Bind DN.
- Kiểm tra mật khẩu và trạng thái tài khoản domain.

### 3. Captive Portal không xuất hiện

- Kiểm tra Zone đã được bật.
- Kiểm tra đúng interface LAN.
- Sử dụng trang HTTP để kiểm thử chuyển hướng.
- Kiểm tra session xác thực cũ.
- Kiểm tra cấu hình DNS và IP.

### 4. Firewall Rule không hoạt động như mong đợi

- Kiểm tra thứ tự các rule.
- Kiểm tra Source và Destination.
- Kiểm tra Alias.
- Kiểm tra Protocol và Destination Port.
- Kiểm tra interface đặt rule.
- Tạo kết nối mới sau khi thay đổi cấu hình.
- Reset States khi cần thiết.

### 5. Schedule hoạt động không đúng

- Kiểm tra múi giờ hệ thống pfSense.
- Kiểm tra khung giờ trong Schedule.
- Kiểm tra trạng thái Active/Inactive.
- Kiểm tra rule Block nằm dưới rule Pass theo Schedule.
- Sử dụng TEST_NOW để thử trong thời gian ngắn.

### 6. Không truy cập được website DMZ

- Kiểm tra địa chỉ IP DMZ-WEB.
- Kiểm tra Nginx đang hoạt động.
- Kiểm tra các địa chỉ IP phụ.
- Kiểm tra gateway DMZ.
- Kiểm tra Firewall Rules.
- Kiểm tra firewall trên Ubuntu Server.

## Nhận xét và đánh giá

Qua mô hình LAB6 có thể thấy pfSense hỗ trợ nhiều cơ chế kiểm soát truy cập phù hợp với mạng doanh nghiệp quy mô nhỏ.

Việc kết hợp Active Directory và LDAP giúp quản lý thông tin người dùng tập trung. Captive Portal đảm nhiệm xác thực trước khi truy cập mạng, trong khi Firewall Rules kiểm soát lưu lượng dựa trên địa chỉ IP, cổng, giao thức và thời gian.

Firewall Alias giúp đơn giản hóa quá trình xây dựng chính sách. Schedule hỗ trợ giới hạn truy cập theo khung giờ mà không cần thay đổi rule thủ công nhiều lần.

Việc phân chia LAN và DMZ giúp hạn chế khả năng một thiết bị trong vùng máy chủ truy cập trực tiếp vào hệ thống nội bộ.

Một điểm quan trọng của bài thực hành là thứ tự Firewall Rules. pfSense xử lý rule theo nguyên tắc first-match, vì vậy rule cụ thể cần được đặt trước các rule cho phép rộng hơn.

Các nhận xét trên là cơ sở kỹ thuật của mô hình; kết luận về mức độ hoàn thành thực tế cần đối chiếu kết quả kiểm thử và ảnh minh chứng.

## Kết quả

Bài LAB6 được xây dựng theo phạm vi xác thực Active Directory và quản lý chính sách truy cập trên pfSense.

Các nội dung được trình bày gồm:

- Xây dựng mô hình mạng WAN, MGMT, LAN, DMZ.
- Cấu hình các interface pfSense.
- Chuẩn bị Windows Server và Active Directory.
- Cấu hình LDAP Authentication.
- Cấu hình Captive Portal.
- Tạo Firewall Alias.
- Tạo Schedule.
- Xây dựng chính sách chặn JOB_SITE.
- Xây dựng chính sách chặn RESTRICTED_SITE.
- Xây dựng chính sách SOCIAL_SITE theo giờ nghỉ.
- Giới hạn HTTP/HTTPS theo giờ hành chính.
- Thiết lập cô lập mạng DMZ.
- Phân tích cơ chế first-match và stateful firewall.

**Kết quả tổng thể: Đang hoàn thiện minh chứng thực hành.**

## Cấu trúc thư mục nộp bài

```text
LAB6/
├── README.md
└── Lab6_LeVanHa_1150080091.docx
```

File Word chứa nội dung báo cáo, giải thích cấu hình, bảng kết quả và hình ảnh liên quan.

README.md được dùng để giới thiệu nội dung và cách tổ chức bài thực hành trên GitHub.

Theo tài liệu LAB6, cấu hình pfSense cuối cùng cần được xuất thành `config.xml` để nộp kèm khi hoàn thành.


## Lưu ý

- LAB6 sử dụng môi trường máy ảo để thực hành.
- Các địa chỉ website được mô phỏng trong vùng DMZ.
- Không dùng website công cộng làm đối tượng thử nghiệm chặn truy cập.
- Không sử dụng tài khoản Domain Administrator để kết nối LDAP.
- Không đăng tải mật khẩu, thông tin xác thực hoặc dữ liệu nhạy cảm lên GitHub.
- File `config.xml` cần được kiểm tra và loại bỏ thông tin bí mật trước khi công khai.
- Việc cấu hình và kiểm thử phải thực hiện trong phạm vi mạng LAB được cho phép.
- Kết quả PASS chỉ được ghi nhận sau khi có kiểm thử và bằng chứng tương ứng.

Toàn bộ nội dung LAB6 phục vụ mục đích học tập, tìm hiểu Active Directory, xác thực người dùng và quản lý chính sách bảo mật mạng bằng pfSense.
