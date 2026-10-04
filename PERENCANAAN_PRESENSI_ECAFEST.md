# 📋 Blueprint & Perencanaan Sistem Presensi Website "Ecafest"
**Telkom University Event Management**

---

## 1. Ikhtisar & Tujuan Sistem
Website Presensi **Ecafest** dirancang untuk memfasilitasi pencatatan kehadiran peserta (mahasiswa, tamu undangan, dan panitia) secara cepat, akurat, dan nirsentuh (*contactless*) berbasis **QR Code Scanning**.

### Sasaran Utama:
1. **Kecepatan Check-in:** Durasi check-in maksimal 2–3 detik per peserta di setiap gerbang/gate.
2. **Pencegahan Tiket Ganda (*Anti-Fraud / Anti-Duplicate*):** Sistem menolak QR Code yang sudah pernah di-scan pada sesi yang sama.
3. **Monitoring Real-time:** Panitia inti dapat memantau kuota, jumlah pengunjung masuk, dan kepadatan gate secara langsung.
4. **Fleksibilitas Operasional:** Mendukung scan via kamera smartphone panitia, webcam laptop, serta input manual (pencarian NIM/Nama/ID Tiket) saat barcode terkendala.

---

## 2. Struktur Role & Hak Akses Pengguna

| Role | Deskripsi & Hak Akses |
| :--- | :--- |
| **Super Admin / Koordinator** | Akses penuh: Kelola event/sesi, impor/ekspor data peserta (.xlsx/.csv), kelola akun panitia, audit log, reset status kehadiran, dashboard analitik utama. |
| **Operator Gate / Panitia Scanner** | Akses gate: Memilih sesi/gate aktif, scan QR Code peserta, cek manual NIM/ID jika QR rusak, melihat log scan terbaru di gate tersebut. |
| **Peserta / Pengunjung** | Halaman publik / personal: Melihat status tiket, check-in mandiri (jika gate mengizinkan geolokasi/self-checkin), dan e-badge bukti kehadiran. |

---

## 3. Matriks Fitur Lengkap

### A. Modul Autentikasi & Gate Setup
- Login Admin & Panitia (PIN cepat untuk operator gate atau akun email/password).
- Pemilihan **Active Gate** (misal: *Gate A - GSG Telkom Univ*, *Gate B - VIP Lobby*, dll.) dan **Active Session** (misal: *Day 1 - Opening & Talkshow*, *Day 2 - Music Fest*).

### B. Modul Scanner & Verifikasi Real-Time
- **Live Camera QR Reader:** Mendukung kamera depan/belakang smartphone & webcam laptop.
- **Audio & Visual Feedback:**
  - 🟢 **Success (Hijau):** Bip 1x, muncul identitas peserta (Nama, NIM/Institusi, Kategori Tiket).
  - 🔴 **Already Used (Merah):** Bip 2x panjang, alert bahwa tiket sudah di-scan pada jam sekian di gate mana.
  - 🟡 **Invalid (Kuning):** Kode QR tidak terdaftar di sistem.
- **Offline Buffer (PWA Support):** Jika sinyal Wi-Fi/data terputus sementara, data scan tersimpan di IndexedDB browser dan auto-sync saat koneksi pulih.
- **Manual Fast-Search:** Input cepat NIM / No. Registrasi / Nama lengkap untuk check-in darurat.

### C. Modul Manajemen Peserta & Tiket
- Import data massal via Excel/CSV (Kolom: ID Tiket, Nama, NIM, Email, Kategori Tiket, Status Pembayaran).
- Export data kehadiran terfilter (Waktu masuk, Gate, Operator scanner).
- Generator QR Code unik untuk setiap peserta (dapat dikirim via email/blast WhatsApp).

### D. Modul Dashboard & Analitik Live
- Metric cards: Total Peserta Terdaftar, Total Hadir, Belum Hadir, Persentase Kehadiran.
- Grafik tren kehadiran per jam (*Peak Hours Arrival*).
- Live feed ticker: Daftar peserta yang baru saja masuk secara real-time.

---

## 4. Rekomendasi Tech Stack
- **Frontend:** Next.js (React) atau Vite React + Tailwind CSS (Responsif, cepat, support PWA).
- **Scanner Library:** `html5-qrcode` atau `@zxing/library` (ringan dan kompatibel di iOS Safari & Android Chrome).
- **Backend / Realtime Database:** Supabase (PostgreSQL + Realtime WebSockets) atau Firebase / Node.js Express + Socket.io.
- **Exporting Data:** SheetJS (`xlsx`) untuk import/export Excel.

---

## 5. Layout Wireframe (ASCII)

### Wireframe 1: Desktop Operator Scanner (Laptop di Meja Registrasi)

```text
+-----------------------------------------------------------------------------------------------+
| [LOGO ECAFEST]  Presensi Gate A - GSG Telkom University                 User: Panitia_01 [LOGOUT] |
| Status: (•) Online | Sesi Aktif: Day 1 - Grand Opening Seminar | Jam: 08:42:15 WIB             |
+-----------------------------------------------------------------------------------------------+
|                                                                                               |
|  [ AREA SCANNER & KAMERA ]                    [ DETAIL VERIFIKASI PESERTA ]                  |
|  +-----------------------------------------+  +--------------------------------------------+  |
|  |                                         |  | STATUS: [ 🟢 CHECK-IN BERHASIL ]          |  |
|  |          [ FRAME KAMERA AKTIF ]         |  | Waktu : 08:42:11 WIB (Gate A)              |  |
|  |                                         |  |--------------------------------------------|  |
|  |              +-------+                  |  | Nama       : Muhammad Rafli                |  |
|  |              | [|||] |  <- Arahkan QR   |  | NIM        : 1301213045                    |  |
|  |              +-------+                  |  | Jurusan    : S1 Informatika - FIF          |  |
|  |                                         |  | Tipe Tiket : VIP / Presale 1               |  |
|  |                                         |  | Seat/Nomor : A-14                          |  |
|  +-----------------------------------------+  +--------------------------------------------+  |
|  | [📷 Ganti Kamera]  [⚙️ Auto-Bip: ON]    |  | [ Cetak Wristband / Badge ]  [ Koreksi / Batal ]|
|  +-----------------------------------------+  +--------------------------------------------+  |
|                                                                                               |
|  [ PENCARIAN & MANUAL CHECK-IN ]              [ LOG SCAN REAL-TIME TERAKHIR (Gate A) ]        |
|  +-----------------------------------------+  +--------------------------------------------+  |
|  | Cari NIM / Nama / Kode Tiket:           |  | 08:42:11 | Muhammad Rafli    | VIP   | ✅ |  |
|  | [ 1301213045                  ] [CARI]  |  | 08:41:55 | Siti Nurhaliza    | Reg   | ✅ |  |
|  |                                         |  | 08:41:30 | Daffa Aryaputra   | Reg   | ⚠️ DUPL|  |
|  | Hasil: Muhammad Rafli (Belum Check-in)  |  | 08:40:12 | Farhan Pratama    | Reg   | ✅ |  |
|  | [ TOMBOL MANUAL CHECK-IN ]              |  | 08:39:50 | Kevin Sanjaya     | VIP   | ✅ |  |
|  +-----------------------------------------+  +--------------------------------------------+  |
|                                                                                               |
|  STATISTIK SEMENTARA GATE A:                                                                  |
|  [ Total Masuk Gate: 342 ]   [ VIP: 48 ]   [ Reguler: 294 ]   [ Tiket Gagal/Ditolak: 3 ]      |
+-----------------------------------------------------------------------------------------------+
```

---

### Wireframe 2: Mobile Scanner View (Smartphone Panitia di Pintu Masuk)

```text
+------------------------------------+
| [=] Ecafest Gate Scanner   (•) LIVE|
| Sesi: Day 1 - Music Festival       |
+------------------------------------+
|                                    |
|   +----------------------------+   |
|   |                            |   |
|   |         ┌────────┐         |   |
|   |         │ █ ▄ █  │         |   |
|   |         │ █ █ █  │         |   |
|   |         └────────┘         |   |
|   |    Arahkan QR ke Kamera    |   |
|   +----------------------------+   |
|     [🔄 Balik Kamera]  [🔦 Flash]   |
|                                    |
|   HASIL SCAN:                      |
|   +----------------------------+   |
|   | 🟢 SUKSES VERIFIKASI       |   |
|   |----------------------------|   |
|   | Nama   : ANISA RAHMAWATI   |   |
|   | NIM    : 1202220199        |   |
|   | Tiket  : PRESALE 2         |   |
|   | Status : FIRST CHECK-IN    |   |
|   +----------------------------+   |
|                                    |
|   Atau Input Manual:               |
|   [ Masukkan NIM / ID Tiket  ]     |
|   [      SUBMIT CHECK-IN     ]     |
|                                    |
|   Ringkasan Gate Kamu:             |
|   Tervalidasi: 184 Peserta         |
+------------------------------------+
| [📸 Scan]   [📋 List]   [📊 Stats] |
+------------------------------------+
```

---

### Wireframe 3: Admin & Coordinator Live Analytics Dashboard

```text
+-----------------------------------------------------------------------------------------------+
| [ECAFEST LOGO]  ADMINISTRATOR DASHBOARD                               Admin: BPH Ecafest [⚙️]  |
| Filter Sesi: [ All Sessions v ]   Filter Kategori: [ Semua Tiket v ]   Auto-Refresh: [10s v]  |
+-----------------------------------------------------------------------------------------------+
|                                                                                               |
|  METRIK UTAMA KEHADIRAN:                                                                      |
|  +-------------------+  +-------------------+  +-------------------+  +--------------------+  |
|  | TOTAL REGISTERED  |  | TOTAL HADIR (LIVE)|  | BELUM HADIR       |  | KEHADIRAN RATE     |  |
|  |     1,500 Pax     |  |    1,120 Pax      |  |     380 Pax       |  |      74.6%         |  |
|  +-------------------+  +-------------------+  +-------------------+  +--------------------+  |
|                                                                                               |
|  TRAFFIC KEHADIRAN PER JAM                               DISTRIBUSI PER GATE                  |
|  1200 |                    __                            +---------------------------------+  |
|  1000 |                   /  \                           | Gate A (Utama) : 520 (46.4%)    |  |
|   800 |                  /    \                          | Gate B (VIP)   : 140 (12.5%)    |  |
|   600 |            _/\__/      \                         | Gate C (Samping: 460 (41.1%)    |  |
|   400 |        ___/             \                        +---------------------------------+  |
|   200 |   ____/                  \                       STATUS CHECK-IN PER KATEGORI:        |
|     0 +---------------------------                       | [■■■■■■■■■■■■] Presale 1: 95%   |  |
|       07.00 08.00 09.00 10.00 11.00                      | [■■■■■■■■□□□□] Reguler  : 68%   |  |
|                                                          | [■■■■■■■■■■□□] VIP Invit: 84%   |  |
|                                                                                               |
|  MONITORING GATE OPERATOR:                                                                    |
|  +-----------------------------------------------------------------------------------------+  |
|  | Gate Name   | Operator PIC      | Total Scan | Status   | Terakhir Scan                 |  |
|  |-------------|-------------------|------------|----------|-------------------------------|  |
|  | Gate A - 1  | Dimas (08123...)  | 270        | Active   | 2 detik yang lalu (130121...) |  |
|  | Gate A - 2  | Bella (08771...)  | 250        | Active   | 5 detik yang lalu (140220...) |  |
|  | Gate B-VIP  | Rian  (08529...)  | 140        | Active   | 1 menit yang lalu             |  |
|  | Gate C - 1  | Sarah (08190...)  | 230        | Active   | 12 detik yang lalu            |  |
|  +-----------------------------------------------------------------------------------------+  |
|  [ 📥 Export Laporan Presensi (.XLSX) ]     [ 🔄 Sync Data ]     [ 📢 Broadcast Notifikasi ]  |
+-----------------------------------------------------------------------------------------------+
```

---

### Wireframe 4: Manajemen Peserta & Modal Manual Check-in (Admin View)

```text
+-----------------------------------------------------------------------------------------------+
| DATA MASTER PESERTA ECAFEST                                                                   |
| [ 🔍 Cari Nama / NIM / No. Tiket...         ]  Filter: [ Semua Status v ]  [ + Tambah Manual ]|
|                                                          [ 📤 Import Excel ] [ 📥 Export XLS ]|
+-----------------------------------------------------------------------------------------------+
| [x] | No. Tiket   | Nama Lengkap       | NIM / Institusi | Kategori | Kehadiran   | Aksi      |
|-----+-------------+--------------------+-----------------+----------+-------------+-----------|
| [ ] | ECA-VIP-001 | Fajar Nugraha      | 1301204011      | VIP      | ✅ 08:15:10 | [Detail]  |
| [ ] | ECA-REG-104 | Aurelia Putri      | 1202213011      | Reguler  | ❌ Belum    | [CheckIn] |
| [ ] | ECA-REG-105 | Bagas Wicaksono    | 1101210404      | Reguler  | ✅ 08:33:41 | [Detail]  |
| [ ] | ECA-INV-002 | Dr. Ir. Hendra ... | Telkom Univ     | Tamu     | ❌ Belum    | [CheckIn] |
+-----------------------------------------------------------------------------------------------+
| Menampilkan 1 - 4 dari 1,500 data                              [< Prev]  Hal [ 1 ] dari 150 [Next >] |
+-----------------------------------------------------------------------------------------------+
|                                                                                               |
|  POP-UP MODAL: MANUAL CHECK-IN KONFIRMASI                                                     |
|  +-----------------------------------------------------------------------+                    |
|  | Konfirmasi Presensi Manual Peserta                                [X] |                    |
|  |-----------------------------------------------------------------------|                    |
|  | Apakah Anda yakin ingin melakukan check-in manual untuk peserta ini?  |                    |
|  |                                                                       |                    |
|  | • Nama Peserta : Aurelia Putri                                        |                    |
|  | • NIM          : 1202213011 (S1 Sistem Informasi)                     |                    |
|  | • No. Tiket    : ECA-REG-104 (Reguler Presale 1)                     |                    |
|  | • Gate Masuk   : [ Gate A - GSG Telkom Univ   v ]                     |                    |
|  | • Alasan Manual: [ Barcode Peserta Rusak / HP Mati         v ]        |                    |
|  |                                                                       |                    |
|  |              [ BATAL ]          [ ✅ KONFIRMASI HADIR ]               |                    |
|  +-----------------------------------------------------------------------+                    |
+-----------------------------------------------------------------------------------------------+
```

---

### Wireframe 5: Tampilan Peserta / E-Badge Bukti Presensi (Mobile)

```text
+------------------------------------+
|            ECAFEST 2026            |
|       Telkom University Fest       |
+------------------------------------+
|                                    |
|   STATUS TIKET:                    |
|   ┌────────────────────────────┐   |
|   │     ✅ SUDAH CHECK-IN      │   |
|   └────────────────────────────┘   |
|                                    |
|     Nama: FARHAN ANDRIAN           |
|     NIM : 1301223941               |
|     Tipe: TIKET REGULER            |
|                                    |
|   +----------------------------+   |
|   | 🕒 Waktu Masuk:            |   |
|   |    04 Okt 2026, 08:35 WIB  |   |
|   | 📍 Pintu: Gate B Utama     |   |
|   | 🎟️ No Tiket: ECA-R-0492    |   |
|   +----------------------------+   |
|                                    |
|   [ E-WRISTBAND TOKEN: #9421 ]     |
|   Tunjukkan layar ini kepada       |
|   panitia pembagian merchandise /  |
|   snack untuk klaim benefit.       |
|                                    |
|   [ 📥 Unduh E-Badge / PDF ]       |
+------------------------------------+
|      Pusat Bantuan: Line @ecafest  |
+------------------------------------+
```
