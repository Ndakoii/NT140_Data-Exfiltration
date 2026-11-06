 <p align="center">
  <img src="https://github.com/user-attachments/assets/f990b2cf-f099-48fc-9caa-c654ac47e21c" width="180" alt="UIT Logo">
</p>

<h1 align="center">NT140 - DATA EXFILTRATION OVER NETWORK</h1>

<p align="center">
  <strong>ĐỀ TÀI 06 – NGHIÊN CỨU VÀ GIẢ LẬP CÁC KỸ THUẬT DATA EXFILTRATION QUA NETWORK</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Course-NT140-blue?style=flat-square">
  <img src="https://img.shields.io/badge/Environment-VirtualBox-orange?style=flat-square">
  <img src="https://img.shields.io/badge/Status-Phase%201%20Completed-success?style=flat-square">
</p>

---

## 1. Giới thiệu

Dự án được thực hiện trong khuôn khổ học phần NT140, tập trung nghiên cứu và giả lập các kỹ thuật **Data Exfiltration** – hành vi đưa dữ liệu ra khỏi hệ thống thông qua các kênh truyền thông mạng.

Nhóm xây dựng một môi trường phòng thí nghiệm mạng ảo cô lập bằng VirtualBox nhằm mô phỏng, thu thập và phân tích lưu lượng dữ liệu qua ba phương thức:

- **HTTP POST-based Exfiltration:** Truyền dữ liệu thông qua HTTP POST.
- **DNS Tunneling-based Exfiltration:** Truyền dữ liệu thông qua các truy vấn DNS trong môi trường được kiểm soát.
- **FTP Upload-based Exfiltration:** Tải tệp dữ liệu lên FTP Server bên ngoài mạng nội bộ.

Thông qua các kịch bản thử nghiệm, dự án hướng đến việc phân tích đặc điểm lưu lượng, đánh giá khả năng quan sát và so sánh các phương thức Data Exfiltration. Đồng thời, nhóm nghiên cứu và thử nghiệm một số biện pháp phòng thủ nhằm hạn chế hành vi rò rỉ dữ liệu qua mạng.

> **Phạm vi an toàn:** Toàn bộ thử nghiệm chỉ sử dụng dữ liệu tổng hợp trong môi trường VirtualBox được kiểm soát. Không kết nối đến hệ thống hoặc mạng của bên thứ ba, không sử dụng dữ liệu thật, thông tin đăng nhập thật hoặc thực hiện các hành vi truy cập trái phép.

## 2. Thành viên thực hiện

| MSSV | Họ và tên |
|---|---|
| 24520862 | Nguyễn Đình Anh Khôi |
| 24520871 | Trần Minh Khôi |

## 3. Mục tiêu

- Nghiên cứu nguyên lý hoạt động và đặc điểm lưu lượng của ba phương thức Data Exfiltration.
- Xây dựng môi trường thử nghiệm gồm Internal Network, Victim và External Server.
- Thực hiện các kịch bản truyền dữ liệu tổng hợp qua HTTP, DNS và FTP.
- Thu thập packet capture (PCAP) và network logs bằng tcpdump, Wireshark và Zeek.
- Phân tích và so sánh các phương thức dựa trên:
  - Application payload bytes.
  - Captured network bytes.
  - Throughput.
  - Packet/flow characteristics.
  - Transmission frequency.
  - Observability.
- Thử nghiệm các biện pháp phòng thủ như Firewall và DNS Filtering.
- Đánh giá sự khác biệt trước và sau khi áp dụng biện pháp phòng thủ.
- Ghi lại cấu hình, phương pháp và bằng chứng nhằm đảm bảo khả năng tái lập thử nghiệm.

## 4. Kiến trúc phòng thí nghiệm

Môi trường thử nghiệm được xây dựng trên VirtualBox với ba máy ảo chính:

| Máy | Vai trò |
|---|---|
| Ubuntu Victim | Máy nạn nhân, chứa dữ liệu tổng hợp và thực hiện các hoạt động truyền dữ liệu |
| Kali Linux | Gateway giữa hai mạng lab, giám sát và thu thập lưu lượng mạng |
| SEED Ubuntu External Server | Máy chủ nhận dữ liệu, cung cấp HTTP Server, DNS Server/Resolver và FTP Server |

### Mô hình mạng đề xuất

- **Internal Network (`10.10.10.0/24`):** Mạng nội bộ kết nối Ubuntu Victim với Kali Linux.
- **External Server Network (`10.10.20.0/24`):** Segment server lab kết nối Kali Linux với SEED Ubuntu.

| Máy | Địa chỉ IP đề xuất |
|---|---|
| Ubuntu Victim | `10.10.10.10/24` |
| Kali Linux (Internal) | `10.10.10.1/24` |
| Kali Linux (External Server) | `10.10.20.1/24` |
| SEED Ubuntu | `10.10.20.10/24` |

Mạng External Server là segment thứ hai bên trong môi trường VirtualBox, không phải Internet. Kali Linux đảm nhiệm việc định tuyến giữa hai mạng và giám sát lưu lượng truyền qua các interface tương ứng.

Địa chỉ IP và kiến trúc trên là phương án đề xuất, cần được kiểm tra và xác nhận trước khi triển khai thực tế.

Chi tiết xem tại [Kiến trúc phòng thí nghiệm](docs/architecture.md).

## 5. Phạm vi thử nghiệm

### 5.1. HTTP POST-based Exfiltration

Mô phỏng việc truyền dữ liệu tổng hợp từ Victim đến HTTP Server trong phòng thí nghiệm bằng phương thức HTTP POST.

Các đặc điểm cần quan sát:
- HTTP request và response.
- Kích thước application payload.
- Số lượng packet và flow.
- Tần suất truyền dữ liệu.
- Đặc điểm lưu lượng HTTP.

### 5.2. DNS Tunneling-based Exfiltration

Nghiên cứu việc truyền dữ liệu thông qua DNS query trong một DNS zone và DNS Server/Resolver do nhóm kiểm soát.

Các đặc điểm cần quan sát:
- Số lượng và tần suất DNS query.
- Độ dài tên miền và truy vấn DNS.
- Đặc điểm dữ liệu được mã hóa trong DNS query.
- Kích thước packet và lưu lượng DNS.
- Dấu hiệu bất thường trong hoạt động DNS.

### 5.3. FTP Upload-based Exfiltration

Mô phỏng việc tải tệp dữ liệu tổng hợp từ Victim lên FTP Server trong phòng thí nghiệm.

Các đặc điểm cần quan sát:
- FTP control connection và data connection.
- FTP command, đặc biệt là STOR.
- Dung lượng dữ liệu truyền tải.
- Số lượng packet, flow và thời gian truyền.
- Đặc điểm lưu lượng FTP trong quá trình upload.

## 6. Công cụ sử dụng

| Công cụ | Mục đích |
|---|---|
| VirtualBox | Xây dựng môi trường máy ảo và mạng lab |
| Ubuntu | Máy Victim và External Server |
| Kali Linux | Giám sát và phân tích lưu lượng mạng |
| Wireshark | Phân tích packet capture |
| tcpdump | Thu thập lưu lượng mạng |
| Zeek | Phân tích network logs |
| Python | Xây dựng module mô phỏng và xử lý dữ liệu |
| Bash | Hỗ trợ cấu hình và tự động hóa |
| Git / GitHub | Quản lý mã nguồn và tài liệu |

## 7. Phương pháp đánh giá

Các phương thức được thử nghiệm trong điều kiện có thể so sánh và tái lập, sử dụng dữ liệu tổng hợp với kích thước đầu vào được kiểm soát.

| Nhóm chỉ số | Nội dung đánh giá |
|---|---|
| Application Payload | Dung lượng dữ liệu ứng dụng thực tế được truyền |
| Captured Network Bytes | Tổng dung lượng lưu lượng được ghi nhận trong PCAP |
| Throughput | Tốc độ truyền dữ liệu ở tầng ứng dụng và tầng mạng |
| Packet/Flow | Số lượng packet, flow, kích thước và thời lượng |
| Frequency | Tần suất truyền dữ liệu trong một khoảng thời gian |
| Observability | Khả năng quan sát và nhận diện lưu lượng bất thường |
| Defense Effectiveness | Hiệu quả hạn chế hoặc phát hiện Data Exfiltration |

Kết quả sẽ được ghi nhận thông qua PCAP, network logs, bảng số liệu và ảnh chụp màn hình sau khi thực hiện thử nghiệm.

## 8. Biện pháp phòng thủ

Dự án dự kiến nghiên cứu và thử nghiệm các biện pháp:

- **Firewall:** Kiểm soát kết nối và giới hạn lưu lượng đến các dịch vụ không cần thiết.
- **DNS Filtering:** Giới hạn truy vấn DNS đến các resolver được cho phép và quan sát các truy vấn bất thường.
- **Network Monitoring:** Theo dõi packet, flow và các chỉ số lưu lượng để nhận diện hành vi truyền dữ liệu bất thường.
- **Traffic Analysis:** Phân tích đặc điểm HTTP, DNS và FTP nhằm hỗ trợ phát hiện Data Exfiltration.

Các biện pháp sẽ được đánh giá bằng cách so sánh kết quả trước và sau khi áp dụng trong cùng điều kiện thử nghiệm.

## 9. Cấu trúc kho mã

```text
NT140_Data-Exfiltration/
│
├── docs/                 # Đề cương, kiến trúc và phương pháp
│
├── environment/          # Cấu hình và hướng dẫn môi trường lab
│
├── simulation/           # Mô phỏng HTTP, DNS và FTP
│
├── monitoring/           # Hướng dẫn thu thập và giám sát traffic
│
├── analysis/             # Script và phân tích dữ liệu
│
├── defense/              # Thử nghiệm các biện pháp phòng thủ
│
├── results/              # Kết quả và bằng chứng thực nghiệm
│
├── presentation/
│   └── slides/           # Slide thuyết trình
│
├── .gitignore
└── README.md
```

Các thư mục hiện là bộ khung cho những giai đoạn tiếp theo. Những module chưa có mã nguồn sẽ được bổ sung trong quá trình thực hiện dự án.

## 10. Tài liệu dự án

- [Đề cương dự án](docs/proposal.md)
- [Kiến trúc phòng thí nghiệm](docs/architecture.md)
- [Phương pháp thực nghiệm](docs/methodology.md)
- [Mẫu tổng hợp phát hiện](results/findings.md)

## 11. Nguyên tắc an toàn

1. Chỉ thực hiện thử nghiệm trong môi trường VirtualBox được kiểm soát.
2. Không sử dụng Bridged Networking cho các máy thử nghiệm.
3. Chỉ sử dụng dữ liệu tổng hợp, không sử dụng dữ liệu cá nhân, bí mật hoặc credentials thật.
4. Chỉ giao tiếp với HTTP Server, DNS Server/Resolver và FTP Server do nhóm kiểm soát.
5. Không sử dụng hệ thống hoặc mạng của bên thứ ba làm mục tiêu thử nghiệm.
6. Không triển khai kỹ thuật ẩn mình, duy trì truy cập, vượt cơ chế bảo vệ hoặc truy cập trái phép.
7. Kiểm soát định tuyến để lưu lượng thử nghiệm không đi ra Internet.
8. Chỉ thu thập PCAP và log trong phạm vi mạng được phép.
9. Không đưa dữ liệu nhạy cảm, mật khẩu hoặc token vào repository.
10. Mọi kết luận phải dựa trên bằng chứng thực nghiệm và ghi rõ giới hạn đánh giá.

---

> **Trạng thái hiện tại:** Dự án đã hoàn thành giai đoạn khởi tạo tài liệu và bộ khung repository. Chưa triển khai cấu hình máy ảo, mã mô phỏng, bộ dữ liệu hoặc thử nghiệm đo lường. Chưa có kết quả thực nghiệm 

---

<p align="center">
  <strong>NT140 – Data Exfiltration over Network</strong>
  <br>
  University of Information Technology – VNU-HCM
</p>
