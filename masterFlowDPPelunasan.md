Berikut adalah perbaikan dan penyelarasan menyeluruh untuk dokumen **Master Flow DP dan Pembayaran Full**.

### 💡 Poin Utama Konsistensi yang Diselaraskan:

1. **Pemisahan Peran Accounting (Treasury) vs Finance (AP):**
* **Bu Janice (Pin Transfer)**: Mengeksekusi transfer fisik dana via e-banking setelah transaksi disiapkan di m-banking.
* **Accounting (Treasury)**: Menyiapkan transaksi pembayaran di m-banking (input nominal, rekening tujuan, dan detail pembayaran), menerbitkan bukti bayar / voucher pembayaran resmi di ERP, dan mengirimkan bukti transfer ke Supplier.
* **Finance (AP)**: Menerima invoice, verifikasi *3-Way Matching*, pencatatan *Advance Payment*, dan melakukan *Closing Reconciliation*.


2. **Standardisasi Pintu Masuk Invoice (Resepsionis):** Di semua alur, Invoice fisik/asli yang datang dari Supplier konsisten diterima oleh **Resepsionis** untuk diinput log penerimaannya ke ERP sebelum diverifikasi oleh **Finance (AP)**.
3. **Pemberian Tagging Aset Paralel:** Di ketiga alur, setelah GRN disetujui, **Accounting (Fixed Asset)** menentukan kategori aset, sehingga Logistik bisa langsung mencetak barcode tanpa perlu menunggu proses administrasi pembayaran di **Accounting (Treasury)** selesai.
4. **Penyelarasan Narasi & Diagram:** Narasi di setiap alur telah disesuaikan 100% mengikuti langkah demi langkah yang ada pada flowchart terbaru.

---

# Master Flow DP dan Pembayaran Full

Dokumen ini dibagi menjadi 3 flowchart terpisah agar lebih mudah dibaca dan dikoreksi:

1. Flow COD / pembayaran setelah barang diterima.
2. Flow DP before GRN.
3. Flow pembayaran full sebelum GRN.

---

## 1. Flow COD / Pembayaran Setelah Barang Diterima

### Koreksi Utama

* Mengubah aktor penerima invoice dan pencocokan tagihan menjadi **Finance (AP)** dengan bantuan log penerimaan oleh **Resepsionis**.
* Mengubah eksekusi transfer ke **Bu Janice (Pin Transfer)** dengan tahapan persiapan transaksi di m-banking oleh **Accounting (Treasury)** sebelum PIN diinput.
* Mengubah pembuatan kategori aset menjadi **Accounting (Fixed Asset)** yang berjalan paralel setelah GRN.

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD menyetujui MR"]
    B --> C["Purchasing membuat PO"]
    C --> D["HOD Purchasing / GM / HOD Finance menyetujui PO"]
    D --> E["PO di-issue ke Supplier"]
    
    E --> F["Supplier mengirim barang, Surat Jalan, & Invoice"]
    
    F --> G["Logistics mencocokkan fisik barang dengan Surat Jalan & PO"]
    G --> H{"Fisik & Spesifikasi Sesuai?"}
    H -->|Tidak| I1["Logistics membuat Laporan Retur / Defect Report"]
    H -->|Ya| I["Logistics membuat & menyetujui GRN di ERP"]
    
    F --> J["Resepsionis menerima Invoice fisik dari Supplier & menginput Log Penerimaan di ERP"]
    J --> K["Finance (AP) menerima berkas Invoice & Verifikasi Log ERP"]
    
    I --> L["ERP Otomatis membuat Unbilled AP & Kirim Data GRN ke AP"]
    
    K --> M["Finance (AP) melakukan 3-Way Matching di ERP <br> (PO, GRN, & Invoice)"]
    L --> M
    
    M --> N{"Verifikasi Matches?"}
    N -->|Ada Selisih| O1["Hold Invoice & Minta Klarifikasi / Revisi ke Supplier"]
    N -->|Cocok| O["Asst HOD & HOD Finance / Accounting menyetujui Tagihan"]
    
    O --> P["Accounting (Treasury) menyiapkan transaksi di m-banking <br> (input nominal, rekening tujuan, & detail pembayaran)"]
    P --> Q["Bu Janice meninjau & menyetujui Dokumen Invoice/Pelunasan"]
    Q --> R["Bu Janice (Pin Transfer) menerbitkan Pembayaran"]
    R --> S1["Accounting (Treasury) mengirim Bukti Bayar ke Supplier"]

    I --> S["Accounting (Fixed Asset) menentukan & membuat Kategori Aset"]
    S --> T{"Termasuk Aset?"}
    T -->|Ya| U["Logistics membuat & menempel Barcode Aset"]
    T -->|Tidak| V["Logistics menyiapkan Serah Terima barang Non-Aset"]
    U --> W["Barang / Aset siap diambil oleh Requestor"]
    V --> W

```

### Narasi Proses COD / Pembayaran Setelah Barang Diterima

1. Requestor membuat `MR`.
2. `MR` disetujui oleh `Asst HOD`.
3. Purchasing membuat `PO`.
4. `PO` disetujui oleh `HOD Purchasing`, `GM`, dan `HOD Finance` untuk kontrol anggaran.
5. `PO` dikirim ke Supplier.
6. Supplier mengirimkan barang beserta `Surat Jalan` dan `Invoice`.
7. Tim Logistics mencocokkan fisik barang yang datang dengan `Surat Jalan` dan dokumen `PO`.
8. Jika fisik dan spesifikasi sesuai, Logistics membuat dan menyetujui `GRN` di ERP. (Jika tidak sesuai, Logistics membuat Laporan Retur).
9. Secara terpisah, Resepsionis menerima berkas `Invoice` fisik dari Supplier dan menginput Log Penerimaan di ERP.
10. `Finance (AP)` menerima berkas `Invoice` fisik dan memverifikasi Log Penerimaan di ERP.
11. ERP secara otomatis membuat *Unbilled AP* dan mengirimkan data `GRN` ke modul `Finance (AP)`.
12. `Finance (AP)` melakukan *3-Way Matching* di ERP dengan mencocokkan `PO`, `GRN`, dan `Invoice`.
13. Jika hasil matching sesuai, tagihan disetujui oleh `Asst HOD` dan `HOD Finance / Accounting`.
14. `Accounting (Treasury)` menyiapkan transaksi pembayaran di m-banking dengan menginput nominal, rekening tujuan, dan detail pembayaran lainnya.
15. Bu Janice meninjau dokumen tagihan dan memberikan persetujuan pelunasan.
16. `Bu Janice (Pin Transfer)` mengeksekusi transfer pembayaran via e-banking.
17. `Accounting (Treasury)` menerbitkan voucher pembayaran di ERP dan mengirimkan bukti bayar ke Supplier.
18. Paralel setelah `GRN` terbit, `Accounting (Fixed Asset)` menentukan dan membuat Kategori Aset di ERP.
19. Logistics menerima data klasifikasi aset dari sistem. Jika barang termasuk aset, Logistics mencetak dan menempelkan barcode aset.
20. Jika bukan aset, barang disiapkan untuk prosedur serah terima Non-Aset.
21. Barang atau aset selesai diproses dan siap diambil oleh Requestor.

---
Berikut adalah perbaikan dan penyelarasan untuk **Flow 2 (Flow DP Before GRN)** beserta narasi prosesnya.

Perubahan telah disesuaikan dengan permintaanmu:

1. **Penerbit Bukti Bayar Pelunasan ke Supplier:** Dikirim oleh **Purchasing**.
2. **Status PO:** Ditutup secara resmi oleh **Finance (AP)** menjadi *Purchase Order (Closed)* setelah transaksi pembayaran pelunasan selesai diterbitkan oleh **Accounting (Treasury)**.
3. **Penerimaan Resepsionis & Approval:** Menyelaraskan teks penerimaan berkas di Resepsionis serta persetujuan tagihan oleh `Asst HOD & HOD Finance / Accounting`.

---

## 2. Flow DP Before GRN

### Koreksi Utama

* Konsistensi aktor eksekusi transfer pada `Bu Janice (Pin Transfer)` dengan tahapan persiapan transaksi di m-banking oleh `Accounting (Treasury)` sebelum PIN diinput.
* Pengaliran data DP ke jurnal `Finance (Advance Payment)` di ERP.
* Pengiriman bukti bayar pelunasan ke Supplier dilakukan oleh **Purchasing**, dilanjutkan dengan penutupan status **Purchase Order (Closed)** oleh **Finance (AP)**.

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD menyetujui MR"]
    B --> C["Purchasing membuat PO <br> (Detail Item & Klausul DP)"]
    C --> D["HOD Purchasing / GM / HOD Finance menyetujui PO"]
    D --> E["Accounting (Treasury) menyiapkan transaksi DP di m-banking <br> (input nominal, rekening tujuan, & detail pembayaran)"]
    E --> F["Bu Janice meninjau PO & Invoice DP Supplier"]
    F --> G["Bu Janice (Pin Transfer) menerbitkan Pembayaran DP"]
    G --> H1["Accounting (Treasury) menerbitkan Pembayaran DP di ERP"]
    
    H1 --> H["Purchasing mengirim PO & Bukti DP ke Supplier"]
    H1 --> I["Finance mencatat Uang Muka (DP) & Sisa Komitmen Pelunasan di ERP"]
    
    H --> J["Supplier mengirim barang, Surat Jalan, & Invoice Pelunasan"]
    
    J --> K["Logistics mencocokkan fisik barang dengan Surat Jalan & PO"]
    K --> L{"Fisik & Spesifikasi Sesuai?"}
    L -->|Tidak| M1["Logistics membuat Laporan Retur / Defect Report"]
    L -->|Ya| M["Logistics membuat & menyetujui GRN di ERP"]
    
    J --> N["Resepsionis menerima Invoice Pelunasan fisik & dokumen pendukung"]
    N --> O["Finance (AP) menerima berkas Invoice Pelunasan & Verifikasi Log ERP"]
    
    M --> P["ERP Otomatis menarik data GRN & Bukti DP ke AP"]
    I --> P
    
    O --> Q["Finance (AP) melakukan 3-Way Matching di ERP <br> (PO, GRN, Invoice, & Bukti DP)"]
    P --> Q
    
    Q --> R{"Verifikasi Matches?"}
    R -->|Ada Selisih| S1["Hold Invoice & Minta Klarifikasi / Revisi ke Supplier"]
    R -->|Cocok| S["Asst HOD & HOD Finance / Accounting menyetujui Tagihan"]
    
    S --> T["Accounting (Treasury) menyiapkan transaksi pelunasan di m-banking <br> (input nominal, rekening tujuan, & detail pembayaran)"]
    T --> U["Bu Janice meninjau & menyetujui Dokumen Pelunasan"]
    U --> V["Bu Janice (Pin Transfer) menerbitkan Pembayaran Pelunasan"]
    V --> V1["Accounting (Treasury) menerbitkan Pembayaran Pelunasan di ERP"]
    
    V1 --> V2["Purchasing mengirim Bukti Bayar Pelunasan ke Supplier"]
    V1 --> V3["Finance (AP) menutup status Purchase Order (Closed)"]

    M --> W["Accounting (Fixed Asset) menentukan & membuat Kategori Aset"]
    W --> X{"Termasuk Aset?"}
    X -->|Ya| Y["Logistics membuat & menempel Barcode Aset"]
    X -->|Tidak| Z1["Logistics menyiapkan Serah Terima barang Non-Aset"]
    Y --> Z["Barang / Aset siap diambil oleh Requestor"]
    Z1 --> Z

```

---

### Narasi Proses DP Before GRN

1. Requestor membuat `MR`.
2. `MR` disetujui oleh `Asst HOD`.
3. Purchasing membuat `PO` yang memuat detail item beserta klausul persetujuan `DP`.
4. `PO` disetujui oleh `HOD Purchasing`, `GM`, dan `HOD Finance`.
5. `Accounting (Treasury)` menyiapkan transaksi `DP` di m-banking dengan menginput nominal, rekening tujuan, dan detail pembayaran.
6. Bu Janice meninjau dokumen `PO` dan pengajuan invoice `DP` dari Supplier.
7. `Bu Janice (Pin Transfer)` mengeksekusi transfer pembayaran `DP`.
8. `Accounting (Treasury)` menerbitkan bukti pembayaran `DP` di ERP.
9. Purchasing mengirimkan `PO` resmi beserta bukti bayar `DP` ke Supplier agar pesanan diproses.
10. Finance mencatat pembayaran tersebut sebagai Uang Muka (`Advance Payment`) dan mencatat sisa komitmen pelunasan di ERP.
11. Supplier mengirimkan barang beserta `Surat Jalan` dan `Invoice Pelunasan`.
12. Tim Logistics mencocokkan fisik barang dengan `Surat Jalan` dan `PO`. Jika sesuai, Logistics membuat dan menyetujui `GRN` di ERP.
13. Resepsionis menerima `Invoice Pelunasan` fisik beserta dokumen pendukung lainnya dan menginput Log Penerimaan di ERP.
14. `Finance (AP)` menerima berkas `Invoice Pelunasan` fisik dan memverifikasi Log Penerimaan di ERP.
15. ERP secara otomatis menarik data `GRN` serta catatan pembayaran `DP` sebelumnya ke modul `Finance (AP)`.
16. `Finance (AP)` melakukan *3-Way Matching* di ERP dengan mencocokkan `PO`, `GRN`, `Invoice Pelunasan`, dan `Bukti DP`.
17. Tagihan disetujui oleh `Asst HOD` dan `HOD Finance / Accounting`.
18. `Accounting (Treasury)` menyiapkan transaksi pelunasan di m-banking dengan menginput nominal, rekening tujuan, dan detail pembayaran.
19. Bu Janice meninjau dan menyetujui dokumen pelunasan.
20. `Bu Janice (Pin Transfer)` mengeksekusi transfer pembayaran pelunasan.
21. `Accounting (Treasury)` menerbitkan bukti pembayaran pelunasan di ERP.
22. **Purchasing** mengirimkan bukti bayar pelunasan ke Supplier.
23. **Finance (AP)** memverifikasi kelengkapan seluruh transaksi dan mengubah status dokumen menjadi `Purchase Order (Closed)`.
24. Paralel setelah `GRN` disetujui, `Accounting (Fixed Asset)` menentukan dan membuat Kategori Aset di ERP.
25. Logistics menerima data filter aset; jika termasuk aset, Logistics mencetak dan menempelkan barcode aset. Jika non-aset, disiapkan untuk serah terima.
26. Barang atau aset selesai diproses secara administrasi dan fisik, serta siap diambil oleh Requestor.

---

## 3. Flow Pembayaran Full Sebelum GRN

### Koreksi Utama

* Menyelaraskan awal alur di mana Purchasing menerima dokumen Penawaran / Proforma Invoice dari Supplier sebagai dasar pengajuan bayar.
* Menyelaraskan peran **Resepsionis** saat barang fisik dan Invoice Asli tiba untuk kebutuhan penutupan jurnal (*Closing Reconciliation*).
* Memisahkan penutupan status PO oleh **Finance (AP)** dan pembuatan kategori aset oleh **Accounting (Fixed Asset)**.

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD menyetujui MR"]
    B --> C["Purchasing menerima Penawaran / Proforma Invoice dari Supplier"]
    C --> D["Purchasing membuat PO <br> (Melampirkan Dokumen Penawaran / Proforma Invoice)"]
    D --> E["HOD Purchasing / GM / HOD Finance menyetujui PO"]
    
    E --> F["Accounting (Treasury) menyiapkan transaksi full payment di m-banking <br> (input nominal, rekening tujuan, & detail pembayaran)"]
    F --> G["Bu Janice meninjau PO & Lampiran Dokumen Penawaran"]
    G --> H["Bu Janice (Pin Transfer) menerbitkan Pembayaran Full"]
    H --> I1["Accounting (Treasury) menerbitkan Pembayaran Full di ERP"]
    
    I1 --> I["Purchasing mengirim PO resmi & Bukti Bayar ke Supplier"]
    I1 --> J["Finance mencatat Uang Muka Penuh (Advance Payment) di ERP"]
    
    I --> K["Supplier memproses & mengirim barang, Surat Jalan, & Invoice Asli"]
    
    K --> L["Logistics mencocokkan fisik barang dengan Surat Jalan & PO"]
    L --> M{"Fisik & Spesifikasi Sesuai?"}
    M -->|Tidak| N1["Logistics membuat Laporan Retur / Defect Report"]
    M -->|Ya| N["Logistics membuat & menyetujui GRN di ERP"]
    
    K --> O["Resepsionis menerima Invoice Asli & menginput Log Penerimaan di ERP"]
    O --> P["Finance (AP) menerima berkas Invoice Asli & Verifikasi Log ERP"]
    
    N --> Q["ERP Otomatis menarik data GRN & Status Pembayaran Full ke AP"]
    J --> Q
    
    P --> R["Finance (AP) melakukan Closing Reconciliation di ERP <br> (PO, GRN, Invoice Asli, & Clearing Uang Muka Penuh)"]
    Q --> R
    
    R --> S{"Reconciliation Matches?"}
    S -->|Ada Selisih| T1["Hold & Minta Klarifikasi / Adjustment ke Supplier"]
    S -->|Cocok| T["Finance (AP) menutup status Purchase Order (Closed)"]
    
    N --> U["Accounting (Fixed Asset) menentukan & membuat Kategori Aset"]
    U --> V{"Termasuk Aset?"}
    V -->|Ya| W["Logistics membuat & menempel Barcode Aset"]
    V -->|Tidak| X["Logistics menyiapkan Serah Terima barang Non-Aset"]
    W --> Y["Barang / Aset siap diambil oleh Requestor"]
    X --> Y

```

### Narasi Proses Pembayaran Full Sebelum GRN

1. Requestor membuat `MR`.
2. `MR` disetujui oleh `Asst HOD`.
3. Purchasing menerima dokumen Penawaran / Proforma Invoice dari Supplier.
4. Purchasing membuat `PO` dengan metode pembayaran lunas di awal dan melampirkan dokumen Penawaran/Proforma Invoice tersebut di ERP.
5. `PO` disetujui oleh `HOD Purchasing`, `GM`, dan `HOD Finance`.
6. `Accounting (Treasury)` menyiapkan transaksi pembayaran full di m-banking dengan menginput nominal, rekening tujuan, dan detail pembayaran.
7. Bu Janice meninjau dokumen `PO` beserta lampiran dokumen Penawaran/Proforma Invoice.
8. `Bu Janice (Pin Transfer)` mengeksekusi pembayaran lunas via e-banking.
9. `Accounting (Treasury)` menerbitkan bukti pembayaran full di ERP.
10. Purchasing mengirimkan `PO` resmi dan bukti bayar lunas ke Supplier agar barang diproses dan dikirim.
11. Finance mencatat transaksi tersebut sebagai Uang Muka Penuh (*Advance Payment*) di ERP (belum diakui sebagai biaya/aset karena barang belum diterima).
12. Supplier memproses pesanan dan mengirimkan barang beserta `Surat Jalan` dan `Invoice Asli` / Faktur Pajak.
13. Logistics mencocokkan fisik barang yang datang dengan `Surat Jalan` dan `PO`. Jika sesuai, Logistics membuat dan menyetujui `GRN` di ERP.
14. Resepsionis menerima `Invoice Asli` dari Supplier dan menginput Log Penerimaan di ERP.
15. `Finance (AP)` menerima berkas `Invoice Asli` fisik dan memverifikasi Log Penerimaan di ERP.
16. ERP secara otomatis menarik data `GRN` dan status pembayaran *Advance Payment* ke modul `Finance (AP)`.
17. `Finance (AP)` melakukan *Closing Reconciliation* di ERP dengan mencocokkan `PO`, `GRN`, `Invoice Asli`, dan melakukan *clearing* akun Uang Muka Penuh.
18. Jika reconciliation sesuai, `Finance (AP)` mengoperasikan penutupan status `Purchase Order` menjadi *Closed*.
19. Paralel setelah `GRN` disetujui, `Accounting (Fixed Asset)` menentukan dan membuat Kategori Aset di ERP.
20. Logistics menerima data filter aset; jika barang termasuk aset, Logistics mencetak dan menempelkan barcode aset.
21. Jika barang non-aset, Logistics menyiapkan prosedur serah terima barang Non-Aset.
22. Barang atau aset selesai diproses secara administrasi dan fisik, serta siap diambil oleh Requestor.

---

## Catatan Tambahan untuk Implementasi ERP

* **Segregation of Duties (SoD):** Pemisahan peran antara `Logistics` (Inventory), `Resepsionis` (Document Control), `Finance (AP)` (Accounts Payable), `Accounting (Fixed Asset)` (Asset Management), `Accounting (Treasury)` (Payment Execution), dan `Bu Janice` (Approval & Transfer Authority) dalam diagram di atas sudah memenuhi kriteria kontrol internal standar ERP (*Good Corporate Governance*).
* **Otomasi ERP:** Seluruh proses transisi data bertanda *ERP Otomatis* tidak lagi memerlukan penginputan ulang data secara manual oleh staff, sehingga mengurangi potensi *human error*.
