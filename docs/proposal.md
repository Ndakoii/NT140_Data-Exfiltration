# Đề cương dự án NT140

## 1. Tên đề tài

**Đề tài 06 – Nghiên cứu và giả lập các kỹ thuật Data Exfiltration qua Network.**

## 2. Bối cảnh và mục tiêu

Data Exfiltration qua Network là hành vi đưa dữ liệu từ một hệ thống ra khỏi phạm vi kiểm soát thông qua các giao thức hoặc kênh truyền thông mạng. Hoạt động này có thể lợi dụng nhiều network protocols/channels khác nhau, khiến việc giám sát và phát hiện trở nên khó khăn nếu không có cơ chế kiểm soát phù hợp.

Dự án xây dựng một phòng thí nghiệm mạng ảo cô lập nhằm nghiên cứu, giả lập và phân tích một số kỹ thuật Data Exfiltration tiêu biểu. Phạm vi thực nghiệm tập trung vào ba phương thức: HTTP POST-based Exfiltration, DNS Tunneling-based Exfiltration và FTP-based Exfiltration. Toàn bộ quá trình sử dụng dữ liệu tổng hợp và chỉ diễn ra giữa các máy trong môi trường lab được kiểm soát.

Mục tiêu cụ thể:

- Nghiên cứu nguyên lý hoạt động và đặc điểm của các kỹ thuật Data Exfiltration qua Network.
- Xây dựng môi trường lab gồm Internal Network, Victim và External Server.
- Thực hiện controlled data exfiltration với dữ liệu giả lập thông qua tối thiểu ba network channels.
- Thu thập PCAP và network logs bằng tcpdump, Wireshark và Zeek khi môi trường hỗ trợ.
- Phân tích và so sánh các channels theo throughput, packet/flow characteristics, frequency và mức độ quan sát được.
- Đề xuất và thực nghiệm một số biện pháp hạn chế Data Exfiltration như Firewall, DNS Filtering và giám sát lưu lượng mạng.
- Ghi nhận quy trình, kết quả và điều kiện thử nghiệm để bảo đảm khả năng tái lập.

## 3. Phạm vi

### Trong phạm vi

- Xây dựng môi trường VirtualBox cô lập gồm Windows Host, Ubuntu Victim, Kali Linux Gateway/Monitoring và SEED Ubuntu External Server.
- Nghiên cứu và giả lập ba phương thức Data Exfiltration:
  - **HTTP POST-based Exfiltration:** Truyền dữ liệu giả lập thông qua HTTP POST request đến máy chủ lab.
  - **DNS Tunneling-based Exfiltration:** Mô phỏng việc truyền dữ liệu thông qua các DNS query đến DNS server được kiểm soát trong lab.
  - **FTP-based Exfiltration:** Truyền tệp dữ liệu giả lập từ Victim đến FTP Server trong môi trường thử nghiệm.
- Thực hiện packet capture và phân tích traffic bằng tcpdump, Wireshark; sử dụng Zeek để trích xuất network logs khi khả dụng.
- Phân tích thống kê, throughput, packet/flow characteristics, frequency và mức độ quan sát được của từng channel.
- Thử nghiệm một số biện pháp hạn chế và phát hiện Data Exfiltration trong môi trường lab.
- Chỉ sử dụng dữ liệu tổng hợp, không chứa thông tin cá nhân, bí mật hoặc thông tin đăng nhập thật.

### Ngoài phạm vi

- Truyền dữ liệu đến Internet, hệ thống bên thứ ba hoặc dịch vụ công cộng.
- Sử dụng dữ liệu thật, đánh cắp credentials, vượt cơ chế bảo vệ, ẩn mình hoặc duy trì truy cập trái phép.
- Xây dựng công cụ phục vụ Data Exfiltration trên hệ thống thực tế.
- Thay đổi cấu hình mạng hoặc bảo mật của Windows Host và mạng đang sử dụng.
- Khẳng định kết quả lab đại diện cho mọi mạng hoặc mọi môi trường triển khai thực tế.

## 4. Kiến trúc và công nghệ dự kiến

Windows Host được sử dụng làm môi trường phát triển, quản lý mã nguồn và vận hành VirtualBox.

Các máy ảo trong phòng thí nghiệm:

- **Ubuntu Victim:** Lưu trữ dữ liệu tổng hợp và thực hiện các hoạt động truyền dữ liệu mô phỏng.
- **Kali Linux:** Đóng vai trò Gateway giữa các segment mạng lab và điểm giám sát lưu lượng.
- **SEED Ubuntu External Server:** Cung cấp các dịch vụ thử nghiệm tương ứng với từng channel, bao gồm HTTP Server, DNS Server và FTP Server.

Các công nghệ và công cụ dự kiến:

- VirtualBox: Xây dựng môi trường máy ảo và mạng lab.
- Python 3: Xây dựng các chương trình mô phỏng và phân tích dữ liệu.
- HTTP, DNS, FTP: Các giao thức/kênh truyền được lựa chọn để thực nghiệm.
- tcpdump và Wireshark: Thu thập và phân tích PCAP.
- Zeek: Trích xuất và phân tích network logs.
- Matplotlib: Trực quan hóa kết quả phân tích.

Địa chỉ mạng `10.10.10.0/24` và `10.10.20.0/24` là phương án đề xuất, chưa được xác nhận là cấu hình triển khai thực tế. Cấu hình chi tiết và điều kiện định tuyến được trình bày trong [tài liệu kiến trúc](architecture.md).

## 5. Sản phẩm dự kiến

- Bộ khung mã nguồn và tài liệu hướng dẫn xây dựng môi trường lab.
- Ba mô hình giả lập Data Exfiltration qua HTTP, DNS và FTP.
- Bộ PCAP và network logs được thu thập tương ứng với từng phương thức.
- Quy trình phân tích và các script Python phục vụ tính toán metrics.
- Bảng so sánh các channels theo throughput, packet/flow characteristics, frequency và mức độ quan sát được.
- Kết quả thực nghiệm các biện pháp hạn chế Data Exfiltration, có đối chiếu trước và sau khi áp dụng.
- Báo cáo tổng kết và slide thuyết trình.

Phase 1 chỉ hoàn thành bộ khung thư mục và tài liệu khởi tạo. Các mô hình mô phỏng, công cụ phân tích và kết quả thực nghiệm sẽ được phát triển ở những giai đoạn tiếp theo.

## 6. Rủi ro và biện pháp kiểm soát

| Rủi ro | Biện pháp |
|---|---|
| Máy ảo vô tình kết nối mạng bên ngoài | Sử dụng VirtualBox Internal Network/Host-only theo thiết kế đã duyệt; không sử dụng Bridged Adapter; không cấu hình default route ra Internet |
| Thử nghiệm nhầm đích | Cấu hình đích đến là địa chỉ máy chủ lab cụ thể; kiểm tra IP và route trước mỗi lần chạy |
| Dữ liệu thật xuất hiện trong PCAP | Chỉ tạo dữ liệu tổng hợp; kiểm tra file capture; không commit PCAP lớn hoặc dữ liệu nhạy cảm |
| Kết quả không thể tái lập | Ghi phiên bản công cụ, tham số, thời lượng, điều kiện mạng và phương pháp tính metrics |
| So sánh bị ảnh hưởng bởi nhiều yếu tố | Lặp lại các lần đo và duy trì điều kiện thử nghiệm nhất quán; nếu chưa thể kiểm soát, nêu rõ hạn chế |
| Lưu lượng mô phỏng vượt khỏi phạm vi lab | Giới hạn địa chỉ đích và dịch vụ được phép; kiểm tra cấu hình mạng trước mỗi thử nghiệm |

## 7. Tiêu chí đánh giá

- Môi trường lab được xây dựng và cách ly theo kiến trúc đã thiết kế.
- Ít nhất ba phương thức Data Exfiltration được nghiên cứu và giả lập bằng dữ liệu tổng hợp.
- Mỗi phương thức có PCAP và network logs tương ứng hoặc ghi nhận rõ giới hạn thu thập.
- Quy trình thực nghiệm và phân tích có thể tái lập.
- Kết quả so sánh có đầy đủ các tiêu chí:
  - Throughput.
  - Packet/flow characteristics.
  - Frequency.
  - Mức độ quan sát được (observability).
- Các biện pháp phòng thủ có kết quả trước và sau khi áp dụng, kèm theo cấu hình và bằng chứng.
- Kết luận phản ánh đúng dữ liệu thực nghiệm và nêu rõ giới hạn của môi trường lab.

## 8. Kế hoạch theo giai đoạn

1. **Phase 1 – Khởi tạo dự án:** Xây dựng bộ khung thư mục, tài liệu đề cương và kiến trúc.
2. **Phase 2 – Khảo sát môi trường:** Xác minh cấu hình VirtualBox, hệ điều hành, Network Adapter và thiết kế mạng lab.
3. **Phase 3 – Xây dựng mô hình mô phỏng:** Triển khai và kiểm thử riêng từng phương thức HTTP POST, DNS Tunneling và FTP Upload.
4. **Phase 4 – Thu thập và phân tích:** Xây dựng quy trình capture PCAP, trích xuất log, tính toán metrics và so sánh các channels.
5. **Phase 5 – Phòng thủ và đánh giá:** Thực nghiệm các biện pháp hạn chế, tổng hợp kết quả, hoàn thiện báo cáo và thuyết trình.