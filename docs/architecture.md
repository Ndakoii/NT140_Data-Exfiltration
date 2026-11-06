# Kiến trúc phòng thí nghiệm đề xuất

## 1. Trạng thái và phạm vi

Kiến trúc dưới đây phục vụ **Đề tài 06 – Nghiên cứu và giả lập các kỹ thuật Data Exfiltration qua Network**, giới hạn ở HTTP POST-based Exfiltration, DNS Tunneling-based Exfiltration và FTP Upload-based Exfiltration trong lab được kiểm soát.

Sơ đồ, địa chỉ và cách định tuyến là **đề xuất**, không phải cấu hình đã triển khai hay xác minh. Cần xác nhận mạng VirtualBox, adapter, route, firewall và phạm vi capture trước khi thử nghiệm. Không thay đổi cấu hình mạng của Windows Host trong tài liệu này.

## 2. Sơ đồ logic và luồng dữ liệu

```text
Windows Host (phát triển/quản lý VirtualBox; ngoài mạng lab)
VirtualBox lab (mạng riêng, không bridged; Host không nằm trong lab)

Internal Network: 10.10.10.0/24 (đề xuất)
                                              External Laboratory Network:
                                              10.10.20.0/24 (đề xuất)
+-------------------------+     +-------------------------+     +----------------------------+
| Ubuntu Victim           |     | Kali Linux              |     | SEED Ubuntu External      |
| 10.10.10.10/24          |     | internal: 10.10.10.1/24 |     | Server: 10.10.20.10/24    |
| GW: 10.10.10.1          |     | external: 10.10.20.1/24 |     | GW: 10.10.20.1             |
|                         |     | gateway / monitor       |     | HTTP Server                |
|                         |     |                         |     | DNS Server/Resolver        |
|                         |     |                         |     | FTP Server                 |
+-------------------------+     +-------------------------+     +----------------------------+
     -- HTTP POST / DNS / FTP request -->        -- forwarded request -->
     <-- HTTP / DNS / FTP response --            <-- forwarded response --
```

Hai dòng mũi tên mô tả luồng logic giữa Victim và SEED Ubuntu qua Kali: yêu cầu truyền dữ liệu đi đến máy chủ; phản hồi quay về Victim theo chiều ngược lại. Kali chỉ chuyển tiếp theo route/chính sách đã xác minh. “External Laboratory Network” là segment máy chủ thứ hai **bên trong VirtualBox**, không phải Internet hoặc mạng của bên thứ ba. Với DNS, Victim gửi truy vấn đến DNS Server/Resolver trong lab và nhận phản hồi từ dịch vụ đó.

## 3. Máy và vai trò

| Thành phần | Vai trò dự kiến | Địa chỉ/cấu hình đề xuất |
|---|---|---|
| Windows Host | Môi trường phát triển, quản lý mã nguồn và VirtualBox; không phải máy trong lab, gateway hay đích truyền dữ liệu. | Không gán địa chỉ trong các segment lab ở tài liệu này |
| Ubuntu Victim | Máy gửi trong các kịch bản; lưu dữ liệu tổng hợp và tạo hoạt động HTTP POST, truy vấn DNS trong zone lab hoặc FTP upload theo từng bài thử. | Internal Network: `10.10.10.10/24`; gateway dự kiến `10.10.10.1` |
| Kali Linux | Gateway hai interface giữa hai segment lab và điểm quan sát/capture trên các interface phù hợp. | Interface nội bộ: `10.10.10.1/24`; interface mạng máy chủ: `10.10.20.1/24` |
| SEED Ubuntu External Server | Máy nhận; cung cấp HTTP Server, DNS Server/Resolver và FTP Server được nhóm kiểm soát trong lab. | Mạng máy chủ lab: `10.10.20.10/24`; gateway dự kiến `10.10.20.1` |

Vai trò “Victim” và “External Server” chỉ là tên gọi của các máy trong bài thực hành, không cho phép kết nối đến hệ thống thật.

## 4. Mạng và định tuyến

- Mạng nội bộ đề xuất: `10.10.10.0/24`; Ubuntu Victim và interface trong của Kali nằm trên segment này.
- Mạng máy chủ lab đề xuất: `10.10.20.0/24`; SEED Ubuntu và interface còn lại của Kali nằm trên segment này.
- Gateway Victim dự kiến là `10.10.10.1`; gateway SEED Ubuntu dự kiến là `10.10.20.1`.
- Muốn Victim gửi đến các dịch vụ trên SEED Ubuntu, cần có route đến mạng máy chủ qua Kali. Phản hồi từ SEED Ubuntu về Victim cũng cần route hợp lệ qua Kali.
- Việc Kali chuyển tiếp gói giữa hai mạng chỉ được bật khi cần cho bài thử và theo chính sách firewall đã ghi nhận.
- Không cấu hình route hoặc NAT từ Kali ra Internet; không sử dụng VirtualBox Bridged Adapter. Ưu tiên các segment VirtualBox riêng. Host-only chỉ dùng cho quản trị nếu đã đánh giá và giới hạn phù hợp.
- Windows Host không phải gateway lab hay máy chủ đích. Nếu dùng Host-only để quản trị, adapter đó phải được ghi riêng và không được làm mất tính cô lập của lab.

Các địa chỉ, gateway và route ở trên là phương án đề xuất. Cần kiểm tra xung đột địa chỉ và xác minh định tuyến thực tế trước khi chạy thử nghiệm.

## 5. Dịch vụ và giới hạn kết nối

SEED Ubuntu là máy chủ lab dự kiến cung cấp cả ba dịch vụ:

- **HTTP Server:** chỉ nhận HTTP POST từ máy gửi trong lab theo kịch bản đã duyệt.
- **DNS Server/Resolver:** do nhóm kiểm soát, phục vụ zone/tên thử nghiệm nội bộ; DNS của Victim phải trỏ trực tiếp đến dịch vụ lab này. Không dùng resolver công cộng hoặc zone của bên thứ ba.
- **FTP Server:** chỉ nhận file upload thử nghiệm từ Victim trong lab, với dữ liệu tổng hợp và tài khoản dành riêng cho lab.

Các dịch vụ chỉ được truy cập qua địa chỉ và cổng lab đã ghi nhận; không cấu hình client trỏ đến dịch vụ bên ngoài.

## 6. Giám sát và giới hạn quan sát

- Kali chỉ quan sát được traffic đi qua interface đang capture. Capture trên interface nội bộ không tự động bao gồm traffic chỉ đi qua interface mạng máy chủ, và ngược lại.
- Để quan sát đầy đủ traffic giữa hai segment qua Kali, phải xác minh routing **hai chiều**: gói yêu cầu từ Victim đến SEED Ubuntu và gói phản hồi từ SEED Ubuntu về Victim đều đi qua Kali. Kiểm tra bảng định tuyến, đường đi thực tế và capture ở cả hai interface liên quan trước khi kết luận.
- Nếu một chiều đi đường khác, traffic bất đối xứng, hoặc giao tiếp nằm cùng một segment, Kali có thể chỉ thấy một phần hoặc không thấy traffic đó. Khi cần, đối chiếu capture ở hai đầu hoặc ghi rõ phần không quan sát được; không suy diễn rằng một capture đơn lẻ bao phủ toàn mạng.
- tcpdump/Wireshark dùng để capture và kiểm tra PCAP; Zeek có thể dùng để tạo log phân tích khi được cài đặt/cấu hình ở giai đoạn sau.
- PCAP, log và kết quả có thể chứa nội dung truyền hoặc thông tin phiên. Chỉ dùng dữ liệu tổng hợp, giới hạn quyền truy cập và không đưa artifact nhạy cảm vào Git.

## 7. Kiểm tra trước khi triển khai

- [ ] Ghi nhận phiên bản VirtualBox và hệ điều hành của từng máy ảo.
- [ ] Xác nhận các segment là mạng VirtualBox riêng, không bridge ra mạng thật.
- [ ] Xác nhận các IP đề xuất không trùng với mạng host hoặc segment hiện có.
- [ ] Xác minh route hai chiều giữa hai mạng lab và xác nhận không có đường ra Internet.
- [ ] Xác nhận HTTP Server, DNS Server/Resolver và FTP Server chỉ phục vụ lab trên interface cần thiết.
- [ ] Xác nhận DNS của Victim trỏ đến DNS Server/Resolver do nhóm kiểm soát trong lab.
- [ ] Xác định interface capture và kiểm tra đường đi của cả request lẫn response qua interface đó.
- [ ] Ghi lại chính sách firewall, vị trí capture và nơi lưu PCAP.
- [ ] Chỉ bắt đầu thử nghiệm sau khi các bước trên được xác minh và phê duyệt.
