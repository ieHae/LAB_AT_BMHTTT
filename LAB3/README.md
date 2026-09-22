# LAB 3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## Thông tin sinh viên

- Họ và tên: Lê Văn Hà
- MSSV: 1150080091
- Mã lớp: 11_THMT

## Môi trường thực hành

- VMware Workstation Pro 26H1
- Windows 11 x64
- Microsoft Defender: bật Real-time Protection
- Windows PowerShell 5.1
- Python 3.14.7
- Wireshark 4.6.8
- Sysmon 15.22
- Autoruns 14.3
- Process Explorer 17.14

## Cách dựng môi trường

1. Khởi động máy ảo Windows 11 bằng VMware Workstation.
2. Tạo thư mục `C:\LAB3` để lưu công cụ, tài nguyên và bằng chứng.
3. Kiểm tra trạng thái Microsoft Defender và Windows Firewall.
4. Cài đặt và kiểm tra Sysmon, Autoruns, Process Explorer, Wireshark và Python.
5. Giữ Microsoft Defender hoạt động trong suốt quá trình thực hành.
6. Thu thập output và log của từng tình huống vào thư mục `Evidence`.
7. Sau khi hoàn thành các tình huống, thực hiện cleanup và kiểm tra lại trạng thái hệ thống.
8. Tính SHA-256 cho các file bằng chứng và đóng gói thành `LAB3_Evidence.zip`.

## Các tình huống đã thực hiện

### Tình huống 1 - Asset, Vulnerability, Threat, Risk và Control

- Xác định và phân biệt Asset, Vulnerability, Threat, Risk, Attack và Control.
- Phân tích mối quan hệ giữa tài sản, lỗ hổng, mối đe dọa và biện pháp kiểm soát.
- Thu thập baseline của hệ thống trước khi thực hiện các tình huống.
- Kết quả: **PASS**.

### Tình huống 2 - EICAR và Microsoft Defender

- Sử dụng file kiểm thử EICAR để kiểm tra khả năng phát hiện của Microsoft Defender.
- Theo dõi phản ứng của Defender đối với file kiểm thử.
- Kiểm tra lịch sử phát hiện và lưu bằng chứng phục vụ báo cáo.
- Microsoft Defender vẫn được duy trì hoạt động trong quá trình thực hành.
- Kết quả: **PASS**.

### Tình huống 3 - Xác thực, Password Attack và Keylogging

- Tạo tài khoản `lab3user` phục vụ thực hành.
- Kiểm tra các trường hợp đăng nhập đúng và đăng nhập sai.
- Quan sát Windows Security Event ID 4624, 4625 và 4648.
- Thực hiện thay đổi mật khẩu và kiểm tra lại quá trình xác thực.
- Phân tích nguyên lý của Brute Force và Dictionary Attack trong phạm vi LAB.
- Thực hiện tình huống keylogging theo yêu cầu bài thực hành và kiểm tra artefact liên quan.
- Kết quả: **PASS**.

### Tình huống 4 - Persistence, Sysmon, Autoruns và Process Explorer

- Cài đặt và cấu hình Sysmon.
- Kiểm tra Process Create thông qua Sysmon Event ID 1.
- Sử dụng Process Explorer để quan sát tiến trình đang hoạt động.
- Sử dụng Autoruns để kiểm tra các mục tự khởi động.
- Tạo Run Value `LAB3_Run_Demo` phục vụ quan sát cơ chế persistence.
- Tạo Scheduled Task `LAB3_Persistence_Demo` phục vụ thực hành.
- Kiểm tra listener cục bộ trên cổng 8080 và tiến trình liên quan.
- Thu thập bằng chứng Sysmon, Autoruns và Process Explorer.
- Kết quả: **PASS**.

### Tình huống 5 - Sniffing, HTTP và HTTPS

- Sử dụng Wireshark để capture lưu lượng mạng trong môi trường LAB.
- Thực hiện HTTP loopback trên `127.0.0.1:8080`.
- Quan sát Request URI và chuỗi dữ liệu huấn luyện trong lưu lượng HTTP.
- So sánh khả năng quan sát nội dung giữa HTTP và HTTPS/TLS.
- Với HTTPS, chỉ quan sát được các metadata mạng phù hợp trong khi nội dung ứng dụng được bảo vệ bởi TLS.
- Không thực hiện ARP poisoning, DNS spoofing, Wi-Fi giả mạo hoặc session hijacking.
- Kết quả: **PASS**.

### Tình huống 6 - DoS, DDoS và Mail Bombing

- Thực hiện kiểm thử tải cục bộ bằng `local_load_test.py`.
- Chỉ gửi request đến `127.0.0.1:8080` trong phạm vi máy ảo.
- Giới hạn kiểm thử ở 50 request và 5 worker.
- Phân tích dữ liệu DDoS mẫu bằng `ddos_sample.csv`.
- Phân tích dữ liệu Mail Bombing bằng `mailbomb_sample.csv`.
- Đánh giá số lượng thư theo sender và thông tin kích thước dữ liệu.
- Không tạo lưu lượng gây tải ra mạng bên ngoài.
- Kết quả: **PASS**.

### Tình huống 7 - Social Engineering và Phishing

- Phân tích mẫu `phishing_email.txt`.
- Xác định các dấu hiệu thường gặp của email phishing như tạo cảm giác khẩn cấp, tên hiển thị đáng tin, domain cần xác minh, Reply-To khác From và yêu cầu truy cập liên kết/cung cấp credential.
- Phân tích các case trong `social_engineering_cases.csv`.
- Phân loại các hình thức:
  - Phishing
  - Spear Phishing
  - Watering Hole
  - Pretexting
  - Baiting
  - Quid Pro Quo
- Kết quả: **PASS**.

## Cleanup và kiểm tra sau thực hành

Sau khi hoàn thành các tình huống, tiến hành cleanup các artefact được tạo trong LAB:

- Xóa Run Value `LAB3_Run_Demo`.
- Xóa Scheduled Task `LAB3_Persistence_Demo`.
- Dừng listener trên cổng 8080.
- Xử lý tài khoản `lab3user` sau khi hoàn thành thực hành.
- Kiểm tra lại trạng thái Microsoft Defender.
- Kiểm tra lại các thành phần persistence đã tạo.
- Thu thập kết quả kiểm tra vào `recovery_verification.txt`.
- Tính SHA-256 cho các file trong thư mục `Evidence`.
- Xuất danh sách hash vào `Evidence_SHA256.csv`.
- Đóng gói bằng chứng thành `LAB3_Evidence.zip`.

Kết quả kiểm tra sau cleanup cho thấy Microsoft Defender vẫn được bật và các artefact phục vụ thực hành đã được xử lý.

## Lỗi gặp phải và cách khắc phục

### 1. Không tìm thấy sự kiện đăng nhập của lab3user

- Nguyên nhân: Security log chứa nhiều sự kiện nên khó xác định đúng sự kiện của tài khoản LAB.
- Khắc phục: lọc Event ID 4624, 4625 và kiểm tra trường `Account Name` trong từng sự kiện.

### 2. Lỗi đăng nhập tài khoản lab3user

- Xuất hiện thông báo sai username/password khi sử dụng `runas`.
- Khắc phục: kiểm tra lại trạng thái tài khoản, đặt lại mật khẩu LAB và thực hiện lại quá trình xác thực.

### 3. Không đọc được Security log khi đăng nhập bằng lab3user

- Xuất hiện lỗi `Access is denied`.
- Nguyên nhân: tài khoản `lab3user` không có đủ quyền để đọc Windows Security log.
- Khắc phục: sử dụng tài khoản có quyền Administrator để kiểm tra Event Viewer và thu thập bằng chứng.

### 4. PowerShell không nhận lệnh Set-LocalUser

- Lệnh `Set-LocalUser` không hoạt động trong môi trường PowerShell đang sử dụng.
- Khắc phục: sử dụng công cụ quản lý tài khoản tương thích với môi trường Windows hiện tại và kiểm tra lại tài khoản bằng lệnh `net user`.

## Kết quả

Các tình huống của LAB 3 đã được thực hiện trong môi trường máy ảo Windows 11.

Trong quá trình thực hành đã thu thập được các bằng chứng liên quan đến:

- Windows Security Event Log
- Microsoft Defender
- Windows Firewall
- Sysmon
- Autoruns
- Process Explorer
- Wireshark
- Process và Network
- Persistence
- HTTP/HTTPS
- DoS/DDoS và Mail Bombing
- Social Engineering và Phishing

Sau khi hoàn thành, các artefact phục vụ thực hành được cleanup và hệ thống được kiểm tra lại. Các file Evidence được tính SHA-256 nhằm kiểm tra tính toàn vẹn và được đóng gói để phục vụ việc đối chiếu.

**Kết quả tổng thể: PASS**

## Cấu trúc thư mục nộp bài

LAB3/
- README.md
- Báo cáo LAB3 (.docx)
- Evidence/
  - autoruns_after.csv
  - baseline_defender.txt
  - baseline_firewall.txt
  - baseline_network.txt
  - baseline_os.txt
  - baseline_processes.txt
  - defender_detections.txt
  - firewall_status.txt
  - recovery_verification.txt
  - start_time.txt
  - sysmon_process_create.txt
- Evidence_SHA256.csv
- LAB3_Evidence.zip

## Video demo

https://youtu.be/Ji_S_xX2AHs

## Lưu ý

Repository không chứa mật khẩu thật, token/API key, cookie/session, email thật, dữ liệu cá nhân hoặc các thông tin nhạy cảm.

Các installer, executable của Sysinternals/Wireshark/Python và các file đã bị Microsoft Defender quarantine không được đưa vào repository.

Toàn bộ nội dung thực hành chỉ được thực hiện trong môi trường LAB/máy ảo phục vụ mục đích học tập.
