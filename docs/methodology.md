# Phương pháp thực nghiệm

## 1. Mục tiêu và nguyên tắc

Phương pháp này phục vụ **Đề tài 06 – Nghiên cứu và giả lập các kỹ thuật Data Exfiltration qua Network**, chỉ giới hạn ở ba phương thức:

1. **HTTP POST-based Exfiltration:** truyền có kiểm soát dữ liệu tổng hợp bằng HTTP POST.
2. **DNS Tunneling-based Exfiltration:** nghiên cứu dấu hiệu truyền dữ liệu qua DNS trong zone và DNS Server/Resolver do nhóm kiểm soát.
3. **FTP Upload-based Exfiltration:** tải tệp dữ liệu tổng hợp lên FTP Server trong lab.

Đây là các bài thử nghiệm giới hạn trong mạng VirtualBox cô lập, không phải hướng dẫn triển khai trên hệ thống thật. Chỉ sử dụng dữ liệu tổng hợp và dịch vụ do nhóm kiểm soát. Không gửi traffic đến Internet, resolver công cộng, máy chủ hoặc zone của bên thứ ba; không dùng dữ liệu thật, thông tin đăng nhập thật, kỹ thuật ẩn mình, né tránh giám sát hay vượt chính sách.

Các IP trong [tài liệu kiến trúc](architecture.md) là đề xuất, chưa được xác nhận. Trước mỗi lần chạy, cần xác nhận máy gửi/nhận, route hai chiều, dịch vụ đích, interface capture và tính cô lập của lab. Không thực hiện thử nghiệm nếu chưa xác định rõ đích đến và điểm thu thập.

## 2. Dữ liệu đầu vào và điều kiện so sánh

- Tạo bộ dữ liệu tổng hợp dành riêng cho lab; không dùng tài liệu cá nhân, dữ liệu thu thập từ hệ thống thật hoặc credentials.
- Ghi loại dữ liệu, kích thước chính xác theo byte và hash nếu cần kiểm tra tính toàn vẹn. Giữ nguyên cùng một bộ dữ liệu đầu vào cho các phương thức cần so sánh.
- Ghi ngày giờ, phiên bản hệ điều hành/công cụ, máy gửi và máy nhận, địa chỉ thực tế đã xác minh, route, firewall, tham số thử nghiệm, điểm capture và dung lượng còn trống.
- Dùng cùng kích thước dữ liệu ứng dụng, cùng cặp máy ảo, cùng điều kiện mạng, cấu hình capture và giới hạn tài nguyên cho các lần so sánh khi có thể.
- Lặp lại mỗi kịch bản theo kế hoạch thống nhất. Ghi số lần chạy, khoảng nghỉ, thời điểm bắt đầu/kết thúc và các lần lỗi; không bỏ kết quả lỗi mà không giải thích.
- Chỉ so sánh trực tiếp các đại lượng có cùng định nghĩa. Nếu khác biệt giao thức buộc phải khác cấu hình, nêu rõ khác biệt và giới hạn diễn giải.

## 3. Các phương thức thử nghiệm

### 3.1 HTTP POST-based Exfiltration

| Thành phần | Mô tả |
|---|---|
| Đầu vào | Dữ liệu tổng hợp đã định kích thước và ghi nhận hash (nếu dùng) |
| Máy gửi | Ubuntu Victim trong mạng nội bộ lab |
| Máy nhận | HTTP Server trên SEED Ubuntu, địa chỉ/port lab đã xác minh |
| Hoạt động | Thực hiện controlled data transfer bằng HTTP POST; chỉ gửi đến endpoint thử nghiệm trên máy chủ lab |
| Traffic cần thu thập | HTTP request/response và các packet liên quan trên đường đi đã xác minh; ghi interface, filter và chiều quan sát |
| Bằng chứng cần ghi | Kích thước dữ liệu đầu vào, số POST, phản hồi HTTP, số byte ứng dụng được server xác nhận, thời gian, PCAP/log và lỗi |

Đối chiếu lượng dữ liệu ứng dụng gửi với lượng server nhận/xác nhận. Không coi mã phản hồi thành công đơn lẻ là bằng chứng đầy đủ nếu chưa đối chiếu log phía nhận và kích thước dữ liệu.

### 3.2 DNS Tunneling-based Exfiltration

| Thành phần | Mô tả |
|---|---|
| Đầu vào | Dữ liệu tổng hợp, kích thước cố định cho kịch bản; chỉ dùng trong zone lab |
| Máy gửi | Ubuntu Victim trong mạng nội bộ lab |
| Máy nhận | DNS Server/Resolver do nhóm kiểm soát trên SEED Ubuntu; zone thử nghiệm nội bộ |
| Hoạt động | Nghiên cứu mẫu truyền dữ liệu qua các DNS query/response trong lab. Không truy vấn resolver công cộng hoặc domain bên thứ ba |
| Traffic cần thu thập | Truy vấn và phản hồi DNS, transport, địa chỉ nguồn/đích, kích thước, tần suất, lỗi và log DNS phía server |
| Bằng chứng cần ghi | Kích thước đầu vào, số truy vấn, phản hồi/log phía DNS server, dữ liệu thử nghiệm được server ghi nhận, thời gian, PCAP và truy vấn lỗi |

Phân tích tập trung vào khả năng nhận diện DNS, nguồn/đích, tần suất và các đặc điểm bất thường trong ngữ cảnh lab. Ghi rõ cách xác định số byte ứng dụng được nhận; không đồng nhất kích thước DNS packet với lượng dữ liệu đầu vào. Mọi thử nghiệm chỉ dùng zone và server nhóm kiểm soát.

### 3.3 FTP Upload-based Exfiltration

| Thành phần | Mô tả |
|---|---|
| Đầu vào | Tệp tổng hợp có kích thước và nội dung kiểm thử đã ghi nhận |
| Máy gửi | Ubuntu Victim trong mạng nội bộ lab |
| Máy nhận | FTP Server trên SEED Ubuntu, địa chỉ/port lab đã xác minh |
| Hoạt động | Thực hiện controlled file upload từ Victim đến thư mục thử nghiệm trên FTP Server trong lab |
| Traffic cần thu thập | Kết nối điều khiển và dữ liệu liên quan trên interface đã xác minh; phân biệt các kết nối khi phân tích |
| Bằng chứng cần ghi | Kích thước/hash tệp trước và sau (nếu dùng), trạng thái upload, log server, byte ứng dụng được nhận, thời gian, PCAP và lỗi |

Chỉ sử dụng tài khoản thử nghiệm dành riêng cho lab; không ghi credentials vào mã nguồn, log chia sẻ hoặc repo. PCAP cần được bảo vệ vì có thể chứa nội dung truyền hoặc thông tin phiên.

## 4. Thu thập packet capture và log

1. Xác định segment, interface và điểm quan sát trước khi capture. Kali chỉ thấy traffic đi qua interface đang thu thập; kiểm tra route hai chiều để xác định liệu cả request và response có thực sự đi qua Kali.
2. Ghi công cụ/phiên bản, interface, filter, đồng hồ, thời điểm bắt đầu/kết thúc, tên artifact và số packet bị drop nếu có.
3. Capture một lần chạy riêng hoặc ghi mốc thời gian rõ ràng để có thể phân tách từng kịch bản.
4. Kiểm tra PCAP mở được, chứa protocol mong đợi, đúng chiều và khoảng thời gian. Nếu không thấy một chiều traffic, điều tra giới hạn đường đi/điểm capture và báo cáo rõ; không suy luận rằng dữ liệu không được truyền.
5. Đối chiếu PCAP với log phía dịch vụ nhận. Dùng tcpdump/Wireshark để kiểm tra packet; dùng Zeek khi khả dụng và ghi phiên bản/cấu hình đã dùng.
6. Giới hạn truy cập PCAP/log, không đưa dữ liệu nhạy cảm vào artifact chia sẻ và không commit PCAP lớn vào Git.

## 5. Đặc tính packet/flow và tần suất

Với từng lần chạy, ghi rõ định nghĩa và phạm vi đếm:

- **Packet:** tổng số packet và số byte theo chiều gửi/nhận trong capture; ghi cách công cụ tính độ dài packet/frame và có bao gồm header lớp liên kết hay không.
- **Flow/kết nối:** số flow hoặc kết nối theo định nghĩa của công cụ, gồm protocol, địa chỉ/cổng nguồn-đích, thời lượng, số packet/byte mỗi chiều và trạng thái kết thúc nếu có.
- **Đặc tính kích thước/thời gian:** phân bố độ dài packet, khoảng cách thời gian giữa packet, thời lượng kết nối và các lần truyền lại/lỗi quan sát được.
- **Tần suất:** số HTTP POST/response, DNS query/response hoặc lần FTP upload trên giây/phút trong một khoảng đo xác định. Nêu rõ mẫu số thời gian và cách tính.
- **Nguồn/đích:** ghi địa chỉ và cổng thực tế đã xác minh, hướng traffic và điểm capture; không dựa riêng vào nhãn máy trong sơ đồ đề xuất.

Khi capture không đủ hai chiều hoặc có packet drop, đánh dấu metrics bị ảnh hưởng và không so sánh như thể dữ liệu đầy đủ.

## 6. Throughput và phân biệt byte ứng dụng với byte mạng

Báo cáo riêng hai đại lượng; không dùng thay thế cho nhau:

- **Application payload bytes (byte payload ứng dụng):** số byte dữ liệu thử nghiệm ở lớp ứng dụng theo định nghĩa của từng phương thức. Xác định từ dữ liệu đầu vào và/hoặc lượng dữ liệu phía server xác nhận nhận được. Ghi rõ byte nào được tính; không tự động tính HTTP header, DNS metadata, lệnh FTP hay thông tin điều khiển là payload dữ liệu.
- **Captured network bytes (byte mạng được capture):** tổng độ dài packet/frame trong capture đã chọn, trong khoảng thời gian và chiều đã nêu. Đại lượng này có thể bao gồm header giao thức, thông tin điều khiển, ACK, truyền lại và overhead khác; phụ thuộc điểm capture, encapsulation và cách công cụ báo độ dài.

Với khoảng đo có thời lượng `T` giây:

```text
Application payload throughput = application payload bytes được server xác nhận / T
Captured network throughput   = captured network bytes trong phạm vi đã chọn / T
```

Ghi kết quả bằng `B/s` hoặc đổi nhất quán sang `bit/s` (nhân byte/s với 8), đồng thời nêu chiều traffic và cách xác định `T`. Nếu báo cáo byte nhận được theo capture thay vì byte ứng dụng phía server, phải ghi rõ đó là chỉ số network, không phải payload throughput. Khi có thể, báo cáo cả số byte gửi và nhận để phản ánh phản hồi/control traffic; không cộng hai chiều mà không ghi chú.

## 7. Tiêu chí observability

Đánh giá riêng từng tiêu chí, kèm bằng chứng (PCAP, log hoặc log dịch vụ) và giới hạn:

| Tiêu chí | Câu hỏi đánh giá | Bằng chứng gợi ý |
|---|---|---|
| Nhận diện giao thức | Có xác định được HTTP, DNS hoặc FTP từ traffic/log trong capture không? | Protocol, packet/transaction hoặc log tương ứng |
| Xác định nguồn/đích | Có xác định được máy gửi, máy nhận, chiều và endpoint thực tế không? | Địa chỉ/cổng, route đã xác minh và log server |
| Quan sát dấu hiệu truyền dữ liệu | Có quan sát được request/upload/query, phản hồi, byte, thời gian hoặc số lượng truyền không? | Capture hai chiều và đối chiếu với log phía nhận |
| Phát hiện bất thường | Có dấu hiệu khác baseline lab đã định nghĩa về tần suất, kích thước, thời lượng hoặc số lượng kết nối không? | Metrics so sánh với baseline cùng điều kiện và ngưỡng (nếu có) |

Không kết luận rằng một hành vi được phát hiện hoặc không phát hiện chỉ dựa vào một công cụ hay một interface. Nêu rõ dữ liệu quan sát được, dữ liệu bị thiếu, tiêu chí/baseline dùng và mức độ chắc chắn.

## 8. So sánh và thí nghiệm phòng thủ

### So sánh các phương thức

- Dùng cùng bộ dữ liệu tổng hợp và cùng kích thước application payload cho các lần so sánh.
- Giữ nhất quán máy ảo, tài nguyên, segment, route, điểm capture, thời lượng đo và điều kiện nền khi có thể.
- Lặp lại theo cùng kế hoạch; ghi độ biến thiên, lỗi và sai khác giữa các lần chạy.
- So sánh application payload throughput và captured network throughput riêng biệt; kèm packet/flow characteristics, frequency và observability.
- Nếu không thể giữ một điều kiện giống nhau, ghi điều kiện khác biệt và giới hạn kết luận.

### Trước và sau khi áp dụng phòng thủ

- **Firewall:** ghi cấu hình baseline và kết quả khi chưa áp dụng quy tắc thử nghiệm; sau đó áp dụng quy tắc giới hạn trong VM/mạng lab, chạy lại cùng kịch bản và ghi rule, log, traffic được phép/bị chặn cùng kết quả phía server.
- **DNS filtering:** dùng DNS Server/Resolver do nhóm kiểm soát và zone lab; ghi baseline trước lọc, chính sách lọc sau khi bật, truy vấn được chấp nhận/bị từ chối, log và kết quả phía client/server. Không chuyển sang resolver công cộng.
- Giữ nguyên dữ liệu, kích thước, tham số, thời lượng, route và điểm capture giữa lần trước/sau khi có thể; ghi mọi khác biệt.
- Lưu bằng chứng trước và sau: cấu hình phòng thủ, PCAP/log, kết quả dịch vụ nhận, metrics và các trường hợp lỗi. Thay đổi chỉ thực hiện trong lab và cần có phương án khôi phục.
- Ghi nhận trường hợp chặn nhầm hoặc không chặn được nếu có; không xem một lần thử là bằng chứng về hiệu quả phòng thủ tổng quát.

## 9. Báo cáo và khả năng tái lập

Mỗi bảng/biểu đồ phải có đơn vị, nguồn dữ liệu, định nghĩa metrics, điều kiện chạy và ghi chú xử lý. Lưu lại các phiên bản công cụ, tham số, số lần lặp, lỗi, giới hạn quan sát và thay đổi phòng thủ. Nêu rõ artifact nào là PCAP/log và nơi lưu an toàn. Kết luận chỉ áp dụng cho dữ liệu và điều kiện của phòng lab; không suy diễn sang mạng hoặc hệ thống bên ngoài.
