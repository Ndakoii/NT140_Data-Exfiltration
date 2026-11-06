# Mẫu ghi nhận kết quả và phát hiện

> Đây là mẫu trống cho các giai đoạn sau. Hiện chưa có thí nghiệm nào được chạy và chưa có kết quả thực nghiệm. Không điền hoặc trình bày số liệu nếu chưa có bằng chứng. Địa chỉ mạng trong tài liệu dự án là đề xuất, không phải xác nhận cấu hình thực tế.

## Tóm tắt

- Mã/phiên thí nghiệm:
- Ngày giờ:
- Người thực hiện:
- Phương thức/kịch bản (HTTP POST, DNS Tunneling hoặc FTP Upload):
- Kết quả chính (chỉ điền khi đã có bằng chứng):
- Hạn chế:

## Môi trường và điều kiện

| Trường | Giá trị |
|---|---|
| Phiên bản VirtualBox / OS VM | |
| Máy gửi, máy nhận và IP thực tế đã xác nhận | |
| Mạng lab và route hai chiều đã kiểm tra | |
| Phiên bản công cụ | |
| Tham số / firewall / DNS filter | |
| Bộ dữ liệu tổng hợp, kích thước và hash (nếu có) | |
| Số lần lặp / thời lượng | |
| Vị trí capture, interface và filter | |

## Kết quả định lượng

| Lần | Request / query / upload | Packet | Flow/kết nối | Application payload bytes | Captured network bytes | Thời gian đo (s) | Payload throughput (B/s hoặc bit/s) | Network throughput (B/s hoặc bit/s) | Frequency (sự kiện/s hoặc phút) |
|---:|---:|---:|---:|---:|---:|---:|---|---|---|
| 1 | | | | | | | | | |
| 2 | | | | | | | | | |
| 3 | | | | | | | | | |

Application payload bytes phải được xác định theo dữ liệu ứng dụng và/hoặc log phía server nhận; captured network bytes là tổng byte packet/frame trong capture đã chọn. Ghi rõ chiều traffic, khoảng thời gian, cách tính throughput/frequency và định nghĩa flow theo [phương pháp thực nghiệm](../docs/methodology.md). Không coi hai loại byte hoặc throughput là cùng một chỉ số.

## Phát hiện và observability

- Nhận diện giao thức:
- Xác định nguồn/đích và chiều traffic:
- Dấu hiệu truyền dữ liệu quan sát được:
- Dấu hiệu bất thường so với baseline:
- So sánh với baseline:
- Lỗi hoặc packet bị mất:
- Diễn giải và mức độ chắc chắn:

## Kết quả phòng thủ

- Biện pháp (firewall / DNS filtering):
- Bằng chứng và cấu hình trước khi áp dụng:
- Bằng chứng và cấu hình sau khi áp dụng:
- Kết quả phía client/server trước và sau:

## Artifact

- PCAP (đường dẫn nội bộ, không commit nếu lớn/nhạy cảm):
- Log tcpdump/Wireshark/Zeek và log dịch vụ nhận:
- Bảng và biểu đồ:
- Cấu hình đã dùng:

## Hạn chế và khả năng lặp lại

- Yếu tố chưa kiểm soát:
- Sai số / nguy cơ diễn giải sai:
- Các bước cần lặp lại:
- Danh sách xác nhận an toàn mạng lab:
