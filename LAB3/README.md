# LAB 3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## Thông tin sinh viên

- Họ và tên: [Điền họ tên]
- MSSV: [Điền MSSV]
- Mã lớp: [Điền mã lớp]

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
2. Tạo thư mục C:\LAB3 để lưu công cụ, tài nguyên và bằng chứng.
3. Kiểm tra trạng thái Microsoft Defender và Windows Firewall.
4. Cài đặt và kiểm tra Sysmon, Autoruns, Process Explorer, Wireshark và Python.
5. Giữ Microsoft Defender hoạt động trong quá trình thực hành.
6. Thu thập output và log của từng tình huống vào thư mục Evidence.
7. Sau khi hoàn thành, thực hiện cleanup và kiểm tra lại hệ thống.

## Các tình huống đã thực hiện

### Tình huống 1 - Asset, Vulnerability, Threat, Risk và Control
- Phân tích tài sản, lỗ hổng, mối đe dọa, rủi ro và biện pháp kiểm soát.
- Kết quả: PASS.

### Tình huống 2 - EICAR và Microsoft Defender
- Sử dụng EICAR để kiểm tra khả năng phát hiện của Microsoft Defender.
- Defender phát hiện và cách ly tệp kiểm thử.
- Kết quả: PASS.

### Tình huống 3 - Xác thực và Password Attack
- Tạo tài khoản lab3user phục vụ thực hành.
- Kiểm tra đăng nhập đúng và sai.
- Quan sát Windows Security Event ID 4624 và 4625.
- Thực hiện thay đổi mật khẩu và kiểm tra lại xác thực.
- Kết quả: PASS.

### Tình huống 4 - Persistence, Sysmon, Autoruns và Process Explorer
- Cấu hình Sysmon và kiểm tra Process Create - Event ID 1.
- Quan sát tiến trình bằng Process Explorer.
- Kiểm tra các cơ chế persistence trong môi trường LAB.
- Kết quả: PASS.

### Tình huống 5 - HTTP/HTTPS và Wireshark
- Quan sát lưu lượng mạng bằng Wireshark.
- So sánh khả năng quan sát nội dung giữa HTTP và HTTPS.
- Chỉ thực hiện trong phạm vi VM/LAB.
- Kết quả: PASS.

### Tình huống 6 - DoS/DDoS và Mail Bombing
- Phân tích tình huống DoS trong phạm vi localhost.
- Phân tích dữ liệu mẫu DDoS và Mail Bombing.
- Không tạo lưu lượng tấn công ra mạng bên ngoài.
- Kết quả: PASS.

### Tình huống 7 - Social Engineering và Phishing
- Phân tích các dấu hiệu của email phishing.
- Phân loại Phishing, Spear Phishing, Watering Hole, Pretexting, Baiting và Quid Pro Quo.
- Kết quả: PASS.

## Lỗi gặp phải và cách khắc phục

### 1. Không tìm thấy sự kiện đăng nhập của lab3user
- Nguyên nhân: Security log có nhiều sự kiện và bộ lọc chưa phù hợp.
- Khắc phục: lọc Event ID 4624, 4625 và kiểm tra Account Name trong từng sự kiện.

### 2. Lỗi đăng nhập tài khoản lab3user
- Xuất hiện thông báo sai username/password khi sử dụng runas.
- Khắc phục: kiểm tra lại tài khoản, đặt lại mật khẩu LAB và thực hiện lại quá trình xác thực.

### 3. Không đọc được Security log khi đăng nhập bằng lab3user
- Xuất hiện lỗi Access is denied.
- Nguyên nhân: tài khoản lab3user không có đủ quyền đọc Security log.
- Khắc phục: sử dụng tài khoản có quyền Administrator để kiểm tra Event Viewer.

### 4. PowerShell không nhận lệnh Set-LocalUser
- Khắc phục bằng công cụ quản lý tài khoản tương thích với môi trường Windows hiện tại và kiểm tra lại bằng lệnh net user.

## Kết quả

Các tình huống của LAB3 đã được thực hiện trong môi trường máy ảo. Các bằng chứng được thu thập từ Windows Security, Microsoft Defender, Sysmon, Process Explorer và Wireshark. Sau khi hoàn thành, các artefact phục vụ thực hành được cleanup và hệ thống được kiểm tra lại.

**Kết quả tổng thể: PASS**

## Cấu trúc thư mục nộp bài

LAB3/
- README.md
- Báo cáo Word (.docx)
- evidence_sha256.csv
- Các file output/log đã làm sạch

## Lưu ý

Repository không chứa mật khẩu, token/API key, cookie/session, email thật, dữ liệu cá nhân, log chưa làm sạch, installer, executable Sysinternals/Wireshark/Python hoặc file đã bị Microsoft Defender quarantine.
