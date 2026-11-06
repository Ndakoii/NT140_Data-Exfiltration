# NT140 - Data Exfiltration over Network

**Đề tài 06 – Nghiên cứu và giả lập các kỹ thuật Data Exfiltration qua Network.**

Dự án nghiên cứu và giả lập ba phương thức trong phòng thí nghiệm VirtualBox cô lập: **HTTP POST-based Exfiltration**, **DNS Tunneling-based Exfiltration** và **FTP Upload-based Exfiltration**. Mọi dữ liệu dùng trong thử nghiệm phải là dữ liệu tổng hợp; không kết nối đến hệ thống hoặc mạng của bên thứ ba.

> **Phạm vi an toàn:** Đây là đề cương và bộ khung tài liệu. Mọi thử nghiệm ở các giai đoạn sau chỉ được giao tiếp với máy chủ/resolver phòng thí nghiệm đã cấu hình. Không sử dụng dữ liệu thật, thông tin đăng nhập thật, kỹ thuật ẩn mình, duy trì truy cập, vượt cơ chế bảo vệ hoặc truy cập trái phép.

## Muc tieu

- Mô tả và so sánh traffic của ba phương thức thử nghiệm trong môi trường được ủy quyền.
- Ghi nhận packet và flow bằng tcpdump/Wireshark; phân tích log Zeek khi môi trường hỗ trợ.
- Phân tích application payload bytes, captured network bytes, throughput, packet/flow characteristics, frequency và observability.
- Thử nghiệm biện pháp phòng thủ như firewall và DNS filtering trong lab; ghi nhận kết quả trước và sau khi áp dụng.
- Ghi lại cấu hình, phương pháp, bằng chứng và giới hạn để có thể tái lập; chỉ báo cáo kết quả khi có bằng chứng thực nghiệm.

## Trang thai giai doan

**Phase 1 - Khởi tạo tài liệu và bộ khung thư mục.** Địa chỉ mạng trong tài liệu là đề xuất, chưa được xác nhận trên VirtualBox. Chưa có mã mô phỏng, cấu hình máy ảo, bộ dữ liệu hay kết quả đo. Không tài liệu nào trong repo hiện báo cáo kết quả thử nghiệm đã thực hiện.

## Kien truc de xuat

| May | Vai tro | Mang/IP de xuat |
|---|---|---|
| Windows Host | Phat trien, quan ly ma nguon va dieu khien VirtualBox | Khong gan dia chi lab trong tai lieu nay |
| Ubuntu Victim | May nguon du lieu tong hop va client mo phong | Mang noi bo: `10.10.10.10/24`; gateway du kien: `10.10.10.1` |
| Kali Linux | Gateway giua hai mang lab, giam sat va thu thap luu luong | Interface noi bo: `10.10.10.1/24`; interface mang server: `10.10.20.1/24` |
| SEED Ubuntu External Server | Máy nhận trong lab, cung cấp HTTP Server, DNS Server/Resolver do nhóm kiểm soát và FTP Server | Mạng server lab: `10.10.20.10/24`; gateway dự kiến: `10.10.20.1` |

Mạng `10.10.10.0/24` là mạng nội bộ của Victim; `10.10.20.0/24` là segment server lab thứ hai bên trong VirtualBox, **không phải Internet**. Windows Host nằm ngoài các segment lab. Thiết kế VirtualBox, định tuyến và địa chỉ đều là đề xuất, cần được kiểm tra trước khi triển khai. Kali chỉ quan sát traffic đi qua interface đang capture; muốn quan sát cả hai chiều cần xác minh route hai chiều. Xem [kiến trúc](docs/architecture.md).

## Phạm vi thử nghiệm

1. **HTTP POST-based Exfiltration:** truyền dữ liệu tổng hợp bằng HTTP POST đến HTTP Server trong lab.
2. **DNS Tunneling-based Exfiltration:** nghiên cứu traffic truyền dữ liệu qua DNS trong zone và DNS Server/Resolver do nhóm kiểm soát.
3. **FTP Upload-based Exfiltration:** tải tệp dữ liệu tổng hợp từ Victim lên FTP Server trong lab.

## Cau truc kho ma

```text
docs/                 De cuong, kien truc, phuong phap va bao cao
environment/          Du kien cau hinh VirtualBox va huong dan cai dat
simulation/           Vi tri danh cho mo phong HTTP, DNS va FTP (chua co ma)
monitoring/           Vi tri danh cho huong dan tcpdump, Wireshark va Zeek
analysis/              Vi tri danh cho script, metrics va bieu do phan tich
defense/               Vi tri danh cho thu nghiem firewall va loc DNS
results/               Mau phat hien va vi tri luu bang/bang chup ket qua
presentation/slides/   Vi tri danh cho slide thuyet trinh
```

Thu muc chi co file `.gitkeep` la bo khung cho cac giai doan tiep theo; chua co tinh nang tuong ung.

## Tai lieu

- [De cuong du an](docs/proposal.md)
- [Kien truc phong thi nghiem](docs/architecture.md)
- [Phuong phap thuc nghiem](docs/methodology.md)
- [Mau tong hop phat hien](results/findings.md)

## Nguyen tac an toan va van hanh

1. Su dung VirtualBox va cac mang lab rieng; khong dung bridged networking cho cac may thi nghiem.
2. Khong cau hinh default route cua lab ra Internet. Chi mo dinh tuyen giua hai mang lab khi can cho phep thu nghiem.
3. Chi dung du lieu tong hop; khong dua bi mat, thong tin ca nhan hoac credentials vao repo.
4. Gioi han dich den client vao may chu lab da chi dinh; DNS chi truy van resolver DNS lab.
5. Chi bat packet capture trong pham vi mang lab duoc phe duyet. PCAP co the chua du lieu nhay cam va bi gioi han/khu tru khoi Git.
6. Ghi ro cau hinh, phien ban cong cu, dieu kien do va sai so. Khong xem ket qua mo phong la bang chung ve he thong ben ngoai.

## Huong dan giai doan nay

Giai doan 1 chi tao bo khung tai lieu. Khong co lenh chay mo phong hay yeu cau cai dat goi. Truoc khi chay bat ky thi nghiem nao o giai doan sau, hay doc [kien truc](docs/architecture.md) va xac nhan cach ly mang VirtualBox. Cac buoc cai dat va lenh thuc thi se duoc bo sung cung voi module tuong ung.
