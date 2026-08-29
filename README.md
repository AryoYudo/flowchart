# MASTER FLOW DP DAN PEMBAYARAN FULL

## Standard Role

* **Finance (AP)** → Matching, Cash Flow Prediction, Advance Payment, Reconciliation, Closing PO.
* **Manager Finance** → Approval Cash Flow Prediction & Payment Voucher untuk semua jenis pembayaran.
* **Bu Janice (Director)** → Approval daftar pembayaran.
* **Selvia / Finance** → Create Payment Voucher.
* **HOD Finance** → Approval PO sesuai flow pengadaan.
* **Accounting (Treasury)** → Prepare transaksi & mencatat realisasi pembayaran.
* **Bu Janice (Pin Transfer)** → Eksekusi transfer.
* **Purchasing** → Mengirim PO / Bukti Bayar ke Supplier.
* **Accounting (Fixed Asset)** → Klasifikasi aset setelah GRN.

---

# 1. Flow COD / Pembayaran Setelah Barang Diterima

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD Approve MR"]
    B --> C["Purchasing membuat PO"]
    C --> D["HOD Purchasing / GM / HOD Finance Approve PO"]
    D --> E["Purchasing issue PO ke Supplier"]

    E --> F["Supplier mengirim Barang, Surat Jalan & Invoice"]

    F --> G["Logistics Matching Barang vs Surat Jalan & PO"]
    G --> H{"Sesuai?"}
    H -->|Tidak| H1["Retur / Defect Report"]
    H -->|Ya| I["Logistics membuat & Approve GRN"]

    F --> J["Resepsionis menerima Invoice & Input Log ERP"]
    J --> K["Finance AP Verifikasi Invoice"]

    I --> L["ERP kirim Data GRN ke AP"]
    K --> M["Finance AP 3-Way Matching"]
    L --> M

    M --> N{"Matching Sesuai?"}
    N -->|Tidak| N1["Hold & Klarifikasi"]
    N -->|Ya| O["Finance AP membuat Cash Flow Prediction"]

    O --> P["Manager Finance Approve"]
    P --> Q["Otomatis masuk Table Cash Flow Bu Janice"]
    Q --> R["Bu Janice Approve Payment List"]

    R --> S["Selvia / Finance Create Payment Voucher"]
    S --> T["Manager Finance Approve"]

    T --> V["Accounting Treasury Prepare Transaction"]
    V --> W["Bu Janice Transfer"]
    W --> X["Accounting Treasury mencatat Realisasi Pembayaran"]

    X --> Y["Purchasing mengirim Bukti Bayar ke Supplier"]
    X --> Z["Finance AP Verifikasi Kelengkapan Transaksi"]
    Z --> ZA["Purchase Order Closed"]

    I --> FA["Accounting Fixed Asset menentukan Kategori Aset"]
    FA --> FB{"Aset?"}
    FB -->|Ya| FC["Logistics Barcode Aset"]
    FB -->|Tidak| FD["Serah Terima Non-Aset"]
    FC --> FE["Barang siap diambil Requestor"]
    FD --> FE
```

---

# 2. Flow DP Before GRN

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD Approve MR"]
    B --> C["Purchasing membuat PO + Klausul DP"]
    C --> D["HOD Purchasing / GM / HOD Finance Approve PO"]

    D --> E["Finance AP Matching Data Pengajuan"]
    E --> F["Finance AP membuat Cash Flow Prediction"]
    F --> G["Manager Finance Approve"]
    G --> H["Otomatis masuk Table Cash Flow Bu Janice"]
    H --> I["Bu Janice Approve Payment List"]

    I --> J["Selvia / Finance membuat<br/>Payment Voucher DP + Pelunasan"]
    J --> K["Manager Finance Approve"]

    K --> M["Accounting Treasury Prepare DP"]
    K --> PV["Payment Voucher Pelunasan<br/>Approved - Waiting Matching"]

    M --> N["Bu Janice Transfer DP"]
    N --> O["Accounting Treasury mencatat Realisasi DP"]
    O --> P["Purchasing mengirim PO & Bukti DP ke Supplier"]
    O --> Q["Finance AP mencatat Advance Payment"]

    P --> R["Supplier mengirim Barang, Surat Jalan & Invoice Pelunasan"]

    R --> S["Logistics Matching Barang"]
    S --> T{"Sesuai?"}
    T -->|Tidak| T1["Retur / Defect Report"]
    T -->|Ya| U["Logistics membuat & Approve GRN"]

    R --> V["Resepsionis menerima Invoice Pelunasan"]
    V --> W["Finance AP Verifikasi Invoice"]

    U --> X["ERP menarik GRN & Data DP"]
    Q --> X
    W --> Y["Finance AP 3-Way Matching"]
    X --> Y

    Y --> Z{"Matching Sesuai?"}
    Z -->|Tidak| Z1["Hold & Klarifikasi"]
    Z -->|Ya| AA["Accounting Treasury Prepare Pelunasan"]

    PV --> AA

    AA --> AB["Bu Janice Transfer Pelunasan"]
    AB --> AC["Accounting Treasury mencatat Realisasi Pelunasan"]

    AC --> AD["Purchasing mengirim Bukti Bayar ke Supplier"]
    AC --> AE["Finance AP Verifikasi Seluruh Transaksi"]
    AE --> AF["Purchase Order Closed"]

    U --> AG["Accounting Fixed Asset menentukan Kategori Aset"]
    AG --> AH{"Aset?"}
    AH -->|Ya| AI["Logistics Barcode Aset"]
    AH -->|Tidak| AJ["Serah Terima Non-Aset"]
```

---

# 3. Flow Pembayaran Full Sebelum GRN

```mermaid
flowchart TD
    A["Requestor membuat MR"] --> B["Asst HOD Approve MR"]
    B --> C["Purchasing menerima Penawaran / Proforma Invoice"]
    C --> D["Purchasing membuat PO"]
    D --> E["HOD Purchasing / GM / HOD Finance Approve PO"]

    E --> F["Finance AP Matching Data Pengajuan"]
    F --> G["Finance AP membuat Cash Flow Prediction"]
    G --> H["Manager Finance Approve"]
    H --> I["Otomatis masuk Table Cash Flow Bu Janice"]
    I --> J["Bu Janice Approve Payment List"]

    J --> K["Selvia / Finance Create Payment Voucher Full"]
    K --> L["Manager Finance Approve"]

    L --> N["Accounting Treasury Prepare Full Payment"]
    N --> O["Bu Janice Transfer Full"]
    O --> P["Accounting Treasury mencatat Realisasi Pembayaran"]

    P --> Q["Purchasing mengirim PO & Bukti Bayar ke Supplier"]
    P --> R["Finance AP mencatat Advance Payment"]

    Q --> S["Supplier mengirim Barang, Surat Jalan & Invoice Asli"]

    S --> T["Logistics Matching Barang"]
    T --> U{"Sesuai?"}
    U -->|Tidak| U1["Retur / Defect Report"]
    U -->|Ya| V["Logistics membuat & Approve GRN"]

    S --> W["Resepsionis menerima Invoice Asli"]
    W --> X["Finance AP Verifikasi Invoice"]

    V --> Y["ERP menarik GRN & Advance Payment"]
    R --> Y

    X --> Z["Finance AP Closing Reconciliation"]
    Y --> Z

    Z --> AA{"Reconciliation Sesuai?"}
    AA -->|Tidak| AA1["Hold & Adjustment"]
    AA -->|Ya| AB["Finance AP Verifikasi Seluruh Transaksi"]
    AB --> AC["Purchase Order Closed"]

    V --> AD["Accounting Fixed Asset menentukan Kategori Aset"]
    AD --> AE{"Aset?"}
    AE -->|Ya| AF["Logistics Barcode Aset"]
    AE -->|Tidak| AG["Serah Terima Non-Aset"]
```

---

## Standard Penutupan Semua Flow

**Bu Janice Transfer**
→ **Accounting Treasury Record Realisasi**
→ **Purchasing Kirim Bukti Bayar ke Supplier**
→ **Finance AP Verifikasi / Reconciliation**
→ **Purchase Order Closed**

Dengan standard ini, role ERP tidak berubah-ubah antara COD, DP, dan Full Payment.
