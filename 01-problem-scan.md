# 01 Problem Scan - Vin Smart Future

## Huong chon de tai

Nhom minh chon huong: **Xanh SM ho tro dieu phoi vien xu ly su co xe dien sap het pin**.

Ly do chon huong nay:

- Xanh SM van hanh taxi va xe may dien, nen pin va tram sac la mot phan rat gan voi cong viec hang ngay.
- VinFast da co he thong tram sac, nhung khi gap tinh huong gap thi dieu phoi vien van phai quyet dinh nhanh.
- De tai nay cung khop voi file code mau: neu pin duoi 5%, AI khong duoc goi y tram sac qua xa va nen de xuat xe sac pin di dong.

Nguon da xem:

- Gioi thieu Xanh SM: https://xanhsm.taxi/gioi-thieu/
- Tim kiem showroom va tram sac VinFast: https://vinfastauto.com/vn_vi/tim-kiem-showroom-tram-sac
- Dich vu pin va sac xe dien VinFast: https://vinfastauto.com/vn_vi/dich-vu-pin-oto-dien

---

## Phase 1 - SCAN

| # | Cong ty thanh vien | Lens | Mo ta ngan bai toan |
|---|---|---|---|
| 1 | Xanh SM | Ton thoi gian | Dieu phoi vien phai xu ly thu cong su co xe gan het pin: hoi tai xe, xem vi tri xe, tim tram sac gan nhat, roi soan huong dan. |
| 2 | Xanh SM | Pain tu nguoi khac | Tai xe co the khong biet tram sac nao tot nhat khi pin qua thap, nhat la gio cao diem hoac dang co khach cho. |
| 3 | VinFast | Lap lai | Nhan vien phai doi chieu nhieu phien sac, log tram sac va du lieu thanh toan de tim loi tinh phi hoac thieu du lieu. |
| 4 | Vinhomes | AI co the tot hon | Phan anh cua cu dan tren app co the bi phan loai cham, lam cac van de gap nhu mat nuoc, hong thang may bi xu ly tre. |
| 5 | Vinmec | Ton thoi gian | Bac si mat nhieu thoi gian viet tom tat xuat vien vi phai doc ghi chu dieu tri, ket qua xet nghiem va lich su thuoc. |
| 6 | Vinpearl | Pain tu nguoi khac | Quan ly khach san phai doc nhieu review tu nhieu nen tang de tim cac phan nan lap lai nhu phong ban, check-in cham, nhan vien thai do kem. |

---

## Phase 2 - QUICK-ASSESS

Nhom chon 3 bai toan tiem nang nhat:

1. Xanh SM ho tro dieu phoi vien xu ly xe sap het pin
2. Vinhomes phan loai va dieu huong phan anh cu dan
3. Vinmec ho tro viet tom tat xuat vien

---

## Quick Problem Card #1

```text
QUICK PROBLEM CARD #1

Bai toan:
Dieu phoi vien Xanh SM mat qua nhieu thoi gian khi xu ly su co xe dien sap het pin.

Cong ty thanh vien:
[x] Xanh SM

Ai dang dau?
Nguoi chinh: Dieu phoi vien.
Nguoi bi anh huong: Tai xe va khach hang.

Workflow thu cong hien tai:
1. Tai xe goi hoac nhan tin bao xe sap het pin.
2. Dieu phoi vien kiem tra vi tri xe va muc pin hien tai.
3. Dieu phoi vien tim cac tram sac VinFast gan do.
4. Dieu phoi vien kiem tra tram nao du gan va phu hop.
5. Dieu phoi vien viet huong dan cho tai xe hoac goi ho tro cuu ho.

Buoc ton thoi gian nhat:
Buoc 3 va 4, khoang 8-12 phut moi ca.

AI co the ho tro o dau?
AI co the doc tin nhan cua tai xe, muc pin, vi tri xe va danh sach tram sac.
Sau do AI tao ban nhap goi y an toan cho dieu phoi vien.

Metric thanh cong:
Giam thoi gian xu ly tu khoang 15 phut xuong duoi 3 phut moi ca.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vi sao chon bai nay:

- Bai toan nay gap va co anh huong truc tiep den van hanh.
- AI khong can tu quyet dinh cuoi cung. AI chi viet ban nhap, dieu phoi vien van duyet.

Vi sao khong chi dung rule-based:

- Rule co the kiem tra muc pin va khoang cach, nhung tin nhan cua tai xe thuong khong gon gang.
- LLM huu ich vi co the hieu tieng Viet tu nhien va viet huong dan de doc cho tai xe.

---

## Quick Problem Card #2

```text
QUICK PROBLEM CARD #2

Bai toan:
Phan anh cua cu dan Vinhomes khong phai luc nao cung duoc chuyen den dung bo phan mot cach nhanh.

Cong ty thanh vien:
[x] Vinhomes

Ai dang dau?
Nguoi chinh: Nhan vien ban quan ly toa nha.
Nguoi bi anh huong: Cu dan va doi bao tri.

Workflow thu cong hien tai:
1. Cu dan gui phan anh tren app.
2. Nhan vien doc noi dung thu cong.
3. Nhan vien phan loai van de, vi du thang may, nuoc, tieng on, bai xe, an ninh.
4. Nhan vien chuyen den doi phu trach.
5. Nhan vien viet phan hoi cho cu dan.

Buoc ton thoi gian nhat:
Buoc 2 va 3, khoang 5-10 phut moi ticket.

AI co the ho tro o dau?
AI co the phan loai phan anh, nhan dien muc do khan cap va draft cau tra loi ngan.

Metric thanh cong:
Dieu huong dung 85% phan anh thong thuong den dung doi trong duoi 30 giay.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vi sao chon bai nay:

- Co nhieu tin nhan lap lai, nen co kha nang tiet kiem thoi gian.
- Phan loai van ban va viet cau tra loi la viec LLM lam kha tot.

Vi sao khong chon lam de tai cuoi:

- Mot so phan anh co the lien quan den phi, tranh chap hoac an toan.
- Can ranh gioi chinh sach ro hon truoc khi cho AI goi y cau tra loi.

---

## Quick Problem Card #3

```text
QUICK PROBLEM CARD #3

Bai toan:
Bac si Vinmec mat qua nhieu thoi gian de viet tom tat xuat vien sau dieu tri.

Cong ty thanh vien:
[x] Vinmec

Ai dang dau?
Nguoi chinh: Bac si.
Nguoi bi anh huong: Dieu duong, benh nhan va bo phan hanh chinh benh vien.

Workflow thu cong hien tai:
1. Bac si doc chan doan va ghi chu dieu tri.
2. Bac si kiem tra ket qua xet nghiem va lich su dung thuoc.
3. Bac si viet tom tat xuat vien thu cong.
4. Bac si giai thich buoc tiep theo cho benh nhan.
5. Bo phan hanh chinh luu tai lieu vao he thong.

Buoc ton thoi gian nhat:
Buoc 1 den 3, khoang 20-30 phut moi benh nhan.

AI co the ho tro o dau?
AI co the tao ban nhap tom tat xuat vien tu ghi chu va ket qua co cau truc.
Bac si bat buoc phai doc lai va duyet truoc khi su dung.

Metric thanh cong:
Giam thoi gian viet ban nhap dau tien tu 25 phut xuong duoi 8 phut.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vi sao chon bai nay:

- Co kha nang tiet kiem thoi gian ro.
- Dau vao va dau ra deu nhieu van ban, nen phu hop voi LLM.

Vi sao khong chon lam de tai cuoi:

- Noi dung y te co rui ro cao.
- Neu tom tat sai co the anh huong den an toan benh nhan, nen can kiem tra chat che va bac si phe duyet.

---

## Lua chon cuoi cung

De tai nhom chon la:

> **Xanh SM ho tro dieu phoi vien xu ly xe dien sap het pin**

Nhom chon de tai nay vi no cu the, gan voi van hanh, va co the test nhanh trong lab 30 phut. Rui ro cung co the kiem soat vi AI chi viet ban nhap. Dieu phoi vien van la nguoi duyet hanh dong cuoi cung.

Metric chinh:

> Giam thoi gian xu ly tu khoang 15 phut xuong duoi 3 phut cho moi su co xe sap het pin.

Ranh gioi chinh:

> AI luon phai xuat ket qua co tag `[DRAFT_ONLY]`. Neu pin duoi 5%, AI khong duoc goi y tram sac xa hon 5km. AI nen tra ve hanh dong `dispatch_mobile_charger`.
