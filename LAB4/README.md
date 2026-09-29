# LAB 4 - Khảo sát và đánh giá bề mặt mạng bằng Nmap

## Thông tin sinh viên

- Họ và tên: Lê Văn Hà
- MSSV: 1150080091
- Mã lớp: 11_THMT

## Môi trường thực hành

- VMware Workstation Pro 26H1
- Kali Linux 2026.2 x64
- Metasploitable 2
- Windows 11 x64
- Nmap 7.99
- Mạng thực hành: VMware Host-Only
- Kali Linux: máy quét
- Metasploitable 2: máy đích chính
- Windows 11 VM: máy đích đối chiếu

## Cách dựng môi trường

1. Khởi động VMware Workstation.
2. Chuẩn bị máy ảo Kali Linux, Metasploitable 2 và Windows 11.
3. Cấu hình các máy phục vụ thực hành vào cùng mạng Host-Only.
4. Không sử dụng Bridged Network cho Metasploitable 2.
5. Khởi động Kali Linux và Metasploitable 2.
6. Kiểm tra địa chỉ IP thực tế của từng máy trước khi quét.
7. Kiểm tra khả năng kết nối giữa các máy trong mạng LAB.
8. Sử dụng Kali Linux làm máy quét và các máy ảo còn lại làm mục tiêu.
9. Chỉ thực hiện các lệnh Nmap đối với các máy ảo thuộc môi trường LAB.

## Các nội dung đã thực hiện

### Nội dung 1 - Kiểm tra địa chỉ IP và kết nối mạng

- Kiểm tra địa chỉ IP trên Kali Linux.
- Kiểm tra địa chỉ IP trên Metasploitable 2.
- Kiểm tra địa chỉ IP trên Windows 11 VM.
- Xác nhận các máy phục vụ thực hành nằm trong mạng LAB.
- Kiểm tra kết nối trước khi thực hiện các bước quét.
- Xác định Kali Linux là máy quét và Metasploitable 2 là máy đích chính.
- Kết quả: **PASS**.

### Nội dung 2 - Host Discovery

- Sử dụng Nmap để thực hiện host discovery trong mạng LAB.
- Xác định các host đang hoạt động.
- Ghi nhận địa chỉ IP của các host được phát hiện.
- Quan sát địa chỉ MAC và thông tin nhà sản xuất NIC khi Nmap cung cấp.
- Đối chiếu các host được phát hiện với các máy ảo đang hoạt động.
- Không thực hiện host discovery đối với mạng bên ngoài.
- Kết quả: **PASS**.

### Nội dung 3 - Khảo sát cổng TCP

- Thực hiện TCP Connect Scan (`-sT`).
- Thực hiện SYN Scan (`-sS`).
- Quan sát các trạng thái cổng `open`, `closed` và `filtered`.
- So sánh kết quả giữa TCP Connect Scan và SYN Scan.
- Quan sát sự khác biệt về quyền cần thiết và thời gian thực hiện.
- Ghi nhận các dịch vụ được Nmap suy đoán trên các cổng mở.
- Kết quả: **PASS**.

### Nội dung 4 - FIN, Xmas, NULL và ACK Scan

- Thực hiện FIN Scan trong môi trường LAB.
- Thực hiện Xmas Scan trong môi trường LAB.
- Thực hiện NULL Scan trong môi trường LAB.
- Quan sát trạng thái `open|filtered` và phân tích ý nghĩa của kết quả.
- Thực hiện ACK Scan để quan sát chính sách lọc.
- Phân biệt trạng thái `filtered` và `unfiltered`.
- So sánh kết quả ACK Scan với SYN Scan trên cùng mục tiêu.
- Kết quả: **PASS**.

### Nội dung 5 - Quét UDP

- Thực hiện UDP Scan có kiểm soát.
- Chỉ kiểm tra nhóm cổng UDP phổ biến để giới hạn thời gian quét.
- Quan sát các trạng thái của cổng UDP.
- Phân tích trạng thái `open|filtered` trong quá trình quét UDP.
- Ghi nhận dịch vụ tương ứng khi Nmap có thể nhận diện.
- Kết quả: **PASS**.

### Nội dung 6 - Nhận diện dịch vụ và hệ điều hành

- Sử dụng Version Detection (`-sV`) để nhận diện dịch vụ.
- Ghi nhận các cổng TCP mở và phiên bản dịch vụ được Nmap phát hiện.
- Quan sát các dịch vụ như FTP, SSH, HTTP, SMB và MySQL trên máy đích khi có.
- Sử dụng OS Detection (`-O`) để fingerprint hệ điều hành.
- Thực hiện Aggressive Scan (`-A`) để tổng hợp thêm thông tin.
- Quan sát thông tin về service version, OS detection, traceroute và default scripts.
- So sánh lượng thông tin thu được giữa các kiểu quét.
- Kết quả: **PASS**.

### Nội dung 7 - Kiểm tra SMB bằng NSE

- Xác nhận cổng `445/tcp` trên máy đích.
- Sử dụng NSE `smb-os-discovery` để thu thập thông tin SMB.
- Ghi nhận thông tin hệ điều hành, tên máy và domain khi script trả về.
- Kết quả thực hành nhận diện máy Metasploitable với Samba.
- Thực hiện kiểm tra MS17-010 bằng NSE trong phạm vi máy ảo LAB.
- Không thực hiện khai thác lỗ hổng.
- Kết luận chỉ dựa trên output thực tế do Nmap/NSE trả về.
- Kết quả: **PASS**.

### Nội dung 8 - Xuất kết quả và tạo hồ sơ bằng chứng

- Xuất kết quả Nmap dạng normal text.
- Tạo file `nmap_result.txt`.
- Xuất kết quả Nmap dạng XML.
- Tạo file `nmap_result.xml`.
- Xuất kết quả dạng grepable để phục vụ lọc nhanh.
- Tạo file `smb.txt`.
- Sử dụng `grep` để lọc kết quả cổng `445/open`.
- Kiểm tra công cụ `xsltproc`.
- Chuyển kết quả XML thành HTML.
- Tạo file `nmap_result.html`.
- Kiểm tra sự tồn tại của các file kết quả sau khi xuất.
- Kết quả: **PASS**.

### Nội dung 9 - Kiểm tra trước và sau khi hardening

- Sử dụng Windows 11 VM làm máy đích đối chiếu.
- Xác định địa chỉ IP của Windows 11 trong mạng LAB.
- Tạo một dịch vụ HTTP thử nghiệm cục bộ trên cổng `8080`.
- Từ Kali Linux, sử dụng Nmap để kiểm tra dịch vụ trên cổng 8080.
- Trước khi hardening, Nmap phát hiện `8080/tcp open`.
- Dịch vụ được nhận diện là `SimpleHTTPServer 0.6 (Python 3.14.7)`.
- Thực hiện thay đổi phòng thủ bằng Windows Firewall.
- Quét lại đúng mục tiêu sau khi áp dụng thay đổi.
- Sau hardening, Nmap ghi nhận `8080/tcp filtered`.
- So sánh kết quả trước và sau để chứng minh tác động của biện pháp phòng thủ.
- Kết quả: **PASS**.

## Kết quả trước và sau hardening

### Before

- Máy đích: Windows 11 VM
- IP: `192.168.2.130`
- Cổng kiểm tra: `8080/tcp`
- Trạng thái: `open`
- Dịch vụ: HTTP
- Phiên bản nhận diện: `SimpleHTTPServer 0.6 (Python 3.14.7)`

### After

- Máy đích: Windows 11 VM
- IP: `192.168.2.130`
- Cổng kiểm tra: `8080/tcp`
- Trạng thái: `filtered`
- Dịch vụ Nmap hiển thị: `http-proxy`

### Nhận xét

Kết quả quét trước và sau hardening cho thấy trạng thái của cổng 8080 đã thay đổi từ `open` sang `filtered`.

Trước khi áp dụng thay đổi phòng thủ, Kali Linux có thể phát hiện dịch vụ HTTP đang chạy trên Windows 11 VM. Sau khi cấu hình Windows Firewall, lưu lượng đến cổng 8080 bị lọc nên Nmap không còn xác nhận cổng ở trạng thái `open`.

Kết quả này chứng minh thay đổi cấu hình phòng thủ đã tác động đến khả năng truy cập dịch vụ từ máy quét trong mạng LAB.

## Lỗi gặp phải và cách khắc phục

### 1. Kali Linux không ping được Windows 11 VM

- Hiện tượng: ping từ Kali đến Windows 11 không nhận được phản hồi.
- Nguyên nhân: Windows Firewall có thể chặn ICMP mặc dù hai máy vẫn có khả năng giao tiếp trong mạng LAB.
- Khắc phục: kiểm tra bằng Nmap với `-Pn` thay vì kết luận máy đích không hoạt động.

### 2. Nmap ban đầu không phát hiện cổng mở trên Windows 11

- Hiện tượng: quá trình quét cho thấy các cổng được lọc.
- Khắc phục: tạo dịch vụ HTTP thử nghiệm trên cổng 8080 và thực hiện lại quá trình kiểm tra.
- Kết quả: Nmap phát hiện cổng `8080/tcp open` trước khi áp dụng hardening.

### 3. Cổng 8080 chuyển sang filtered sau khi cấu hình Firewall

- Đây không phải lỗi của Nmap.
- Kết quả cho thấy Windows Firewall đã lọc lưu lượng đến cổng 8080.
- Kết quả này được sử dụng làm bằng chứng cho phần so sánh trước và sau hardening.

### 4. Aggressive Scan mất nhiều thời gian hơn

- Aggressive Scan (`-A`) thực hiện nhiều kỹ thuật thu thập thông tin trong cùng một lần quét.
- Thời gian thực hiện lâu hơn các lệnh quét đơn lẻ.
- Kết quả thu được bao gồm nhiều thông tin hơn về dịch vụ, hệ điều hành và các thông tin liên quan.

## Kết quả

LAB 4 đã được thực hiện trong môi trường máy ảo VMware.

Trong quá trình thực hành đã thực hiện và thu thập bằng chứng liên quan đến:

- Kiểm tra IP và kết nối mạng
- Host Discovery
- TCP Connect Scan
- SYN Scan
- FIN Scan
- Xmas Scan
- NULL Scan
- ACK Scan
- UDP Scan
- Version Detection
- OS Detection
- Aggressive Scan
- SMB OS Discovery
- Kiểm tra MS17-010 bằng NSE
- Xuất kết quả Nmap dạng TXT
- Xuất kết quả Nmap dạng XML
- Xuất kết quả dạng grepable
- Chuyển XML sang HTML
- Kiểm tra trước và sau hardening trên Windows 11 VM

Kết quả trước và sau hardening cho thấy Nmap có thể được sử dụng để chứng minh sự thay đổi của bề mặt mạng sau khi áp dụng biện pháp phòng thủ.

Toàn bộ quá trình quét được giới hạn trong các máy ảo thuộc môi trường LAB.

**Kết quả tổng thể: PASS**

## Cấu trúc thư mục nộp bài

LAB4/
- README.md
- Báo cáo LAB4 (.docx)
- Evidence/
  - Các ảnh chụp màn hình quá trình thực hành
  - nmap_result.txt
  - nmap_result.xml
  - nmap_result.html
  - smb.txt

## Bài tập bổ sung

Không thực hiện phần bài tập bổ sung theo phạm vi bài nộp hiện tại.

## Video demo

Chưa bổ sung.

## Lưu ý

Repository không chứa mật khẩu thật, token/API key, cookie/session hoặc các thông tin nhạy cảm.

Metasploitable 2 chỉ được sử dụng trong mạng LAB cô lập và không được kết nối trực tiếp với mạng công cộng.

Các thao tác Nmap/NSE trong LAB 4 chỉ được thực hiện đối với các máy ảo do sinh viên quản lý.

Không sử dụng các kỹ thuật trong bài thực hành để quét IP, tên miền, hệ thống công cộng hoặc hệ thống của người khác khi chưa được cho phép.

Toàn bộ nội dung thực hành được thực hiện nhằm mục đích học tập và nghiên cứu an toàn thông tin.
