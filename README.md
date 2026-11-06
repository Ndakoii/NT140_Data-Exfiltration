# NT140 - Data Exfiltration over Network

Du an hoc tap NT140 nghien cuu dac diem luu luong cua cac mo phong truyen du lieu qua HTTP, DNS va FTP trong phong thi nghiem VirtualBox co lap. Moi du lieu dung trong thi nghiem phai la du lieu tong hop; khong ket noi den he thong hoac mang cua ben thu ba.

> **Pham vi an toan:** Day la de cuong va bo khung tai lieu. Moi mo phong trong cac giai doan sau phai chi giao tiep voi may chu/resolver phong thi nghiem da cau hinh. Khong su dung du lieu that, thong tin dang nhap that, ky thuat an minh, duy tri truy cap, vuot co che bao ve hoac truy cap trai phep.

## Muc tieu

- Mo ta va so sanh luu luong mo phong HTTP, DNS va FTP trong moi truong duoc uy quyen.
- Ghi nhan goi tin va luong mang bang tcpdump/Wireshark; nghien cuu log Zeek khi da cai dat.
- Phan tich kich thuoc, tan suat, thong luong va dac diem goi/luong.
- Thu nghiem bien phap phong thu nhu quy tac firewall va loc DNS trong mang lab.
- Ghi lai cau hinh, phuong phap, ket qua va gioi han de co the lap lai.

## Trang thai giai doan

**Phase 1 - Khoi tao tai lieu va bo khung thu muc.** Dia chi mang trong tai lieu la de xuat, chua duoc xac nhan tren VirtualBox. Chua co ma mo phong, cau hinh may ao, bo du lieu, ket qua do, hay phan mem phan tich duoc cai dat boi kho ma nay.

## Kien truc de xuat

| May | Vai tro | Mang/IP de xuat |
|---|---|---|
| Windows Host | Phat trien, quan ly ma nguon va dieu khien VirtualBox | Khong gan dia chi lab trong tai lieu nay |
| Ubuntu Victim | May nguon du lieu tong hop va client mo phong | Mang noi bo: `10.10.10.10/24`; gateway du kien: `10.10.10.1` |
| Kali Linux | Gateway giua hai mang lab, giam sat va thu thap luu luong | Interface noi bo: `10.10.10.1/24`; interface mang server: `10.10.20.1/24` |
| SEED Ubuntu External Server | May chu dich vu thu nghiem HTTP/DNS/FTP | Mang server lab: `10.10.20.10/24`; gateway du kien: `10.10.20.1` |

Day la **mang lab ben ngoai** cua mo hinh, khong phai Internet. Thiet ke VirtualBox, dinh tuyen va cac dia chi deu la de xuat, can duoc kiem tra truoc khi trien khai. Xem [kien truc](docs/architecture.md).

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
