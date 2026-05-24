# KasirPOS - Sistem Point of Sale Interaktif

> **Versi:** 2.2.0 | **Tanggal Rilis:** 24 Mei 2026 | **Lisensi:** MIT | **Developer:** Super Z AI Assistant (Z.ai)

## Daftar Isi

- [Tentang Aplikasi](#tentang-aplikasi)
- [Fitur Utama](#fitur-utama)
- [Arsitektur & Teknologi](#arsitektur--teknologi)
- [Struktur File](#struktur-file)
- [Panduan Instalasi & Penggunaan](#panduan-instalasi--penggunaan)
- [Panduan Fitur Detail](#panduan-fitur-detail)
- [Keyboard Shortcut](#keyboard-shortcut)
- [Penyimpanan Data](#penyimpanan-data)
- [Changelog](#changelog)
- [Keamanan & Limitasi](#keamanan--limitasi)
- [Kontribusi](#kontribusi)

---

## Tentang Aplikasi

**KasirPOS** adalah aplikasi Point of Sale (POS) interaktif berbasis web yang dirancang untuk kebutuhan kasir modern. Aplikasi ini berjalan sepenuhnya di browser tanpa memerlukan server backend, menjadikannya solusi ringan dan cepat untuk usaha kecil dan menengah seperti warung kopi, kedai makanan, toko kelontong, dan sejenisnya.

Aplikasi ini dibangun dengan mengadopsi arsitektur dan style CSS dari template medis "Jadwal Obat 10 Hari - Siklus Medis 24 Jam", yang menonjolkan pendekatan card-based layout, sticky header dengan backdrop-filter, Bootstrap 5.3.2, dan desain responsif yang bersih.

### Filosofi Desain

- **Zero Backend** — Tidak perlu instalasi server, database terpisah, atau konfigurasi kompleks. Cukup buka file HTML di browser.
- **Touch-First** — Dirancang untuk digunakan di tablet atau touchscreen kasir dengan tombol besar dan jarak tap yang nyaman.
- **Real-Time** — Semua perhitungan, statistik, dan laporan diperbarui secara instan tanpa perlu refresh.
- **Thermal-Ready** — Cetak struk langsung ke printer termal 58mm standar kasir.
- **Discount-Friendly** — Mendukung diskon per item dan diskon global, baik dalam persen maupun nominal tetap.

---

## Fitur Utama

### 1. Modul Kasir (POS)
- Grid produk interaktif dengan pencarian real-time
- Keranjang belanja dengan kontrol kuantitas (+/-)
- Tanda visual pada produk yang sudah ada di keranjang (border highlight + badge qty)
- Audio feedback (beep) saat menambahkan item
- Animasi slide-in pada item keranjang baru
- Ringkasan keranjang: Subtotal, Diskon Item, Diskon Global, Total

### 2. Sistem Diskon
- **Diskon Per Item** — Klik ikon tag di item keranjang untuk mengatur diskon per barang
  - Tipe persen (%) — Diskon berdasarkan persentase subtotal item
  - Tipe nominal (Rp) — Diskon tetap dalam Rupiah
  - Preview diskon real-time saat mengatur nilai
- **Diskon Global** — Diskon keseluruhan yang diterapkan ke total belanja
  - Tipe persen (%) — Maksimal 100%, dihitung setelah diskon item
  - Tipe nominal (Rp) — Potongan tetap dari subtotal setelah diskon item
- **Tombol Reset** — Hapus semua diskon sekaligus
- **Badge Diskon** — Visual tag pada item yang memiliki diskon
- **Info di Struk** — Detail diskon tercetak di struk pembelian

### 3. Shortcut Nominal Bayar
- **UANG PAS** — Otomatis mengisi nominal = total pembelian (setelah diskon)
- **Shortcut nominal**: 5K, 10K, 15K, 20K, 25K, 50K, 75K, 100K
- Highlight visual pada shortcut yang aktif
- Input manual nominal bayar dengan format Rupiah
- Otomatis bersih highlight saat input manual berubah

### 4. Kalkulasi Kembalian
- Tampilan kembalian otomatis saat nominal bayar >= total
- Animasi pulse pada angka kembalian
- Peringatan "Uang kurang" dengan nominal kekurangan
- Tombol bayar dicegah jika uang kurang

### 5. Cetak Struk Termal
- Format struk untuk kertas termal 58mm (standar printer kasir)
- **Format item diperbaiki**: Nama barang full 1 baris (tidak terpotong), di bawahnya qty x harga satuan = jumlah
- Informasi diskon per item tercetak di bawah item terkait
- Ringkasan diskon (item + global) tercetak sebelum total
- Preview struk di modal sebelum cetak
- Cetak langsung via `window.print()` dengan `@media print` CSS
- Informasi lengkap: nama toko, tanggal, waktu, detail item + diskon, subtotal, diskon, total, bayar, kembalian

### 6. Manajemen Produk (CRUD)
- Tambah produk baru dengan nama, harga beli, dan harga jual
- Edit produk yang sudah ada
- Hapus produk dengan konfirmasi
- Preview margin keuntungan real-time saat input harga
- Pulihkan produk ke daftar default
- Validasi: harga jual harus > 0, nama tidak boleh kosong
- Sanitasi input untuk mencegah XSS

### 7. Laporan Penjualan Real-Time
- Riwayat transaksi dengan filter: Hari Ini / 7 Hari / 30 Hari / Semua
- **Ringkasan Kasir Hari Ini** — Dipindah ke tab laporan agar tampilan kasir lebih bersih
- Kolom Diskon di tabel riwayat transaksi
- Ringkasan: Total Omzet, Laba Kotor, Jumlah Transaksi
- Detail per transaksi: waktu, item, total, diskon, bayar, kembalian, laba
- Hapus transaksi individual dari riwayat
- Hapus semua riwayat dengan konfirmasi
- Export laporan ke file CSV untuk backup/analisis

### 8. Keyboard Shortcut
- F1: Buka tab Kasir
- F2: Buka tab Laporan
- F3: Fokus pencarian produk
- Enter: Proses pembayaran
- Esc: Kosongkan keranjang

### 9. Info & Tentang
- Tab Panduan dengan alur kerja kasir lengkap
- Tab Shortcut dengan daftar keyboard shortcut
- Tab Changelog dengan riwayat versi
- **Tab Tentang** — Informasi developer, teknologi, dan versi aplikasi

---

## Arsitektur & Teknologi

### Tech Stack
| Komponen | Teknologi | Versi |
|----------|-----------|-------|
| UI Framework | Bootstrap | 5.3.2 |
| Icon Library | Bootstrap Icons | 1.11.1 |
| Bahasa | Vanilla JavaScript (ES6+) | - |
| Penyimpanan | localStorage Web API | - |
| Font | Segoe UI, system-ui | - |
| Cetak | CSS @media print | - |

### Arsitektur Aplikasi

```
pos-interaktif.html (Single-File Application)
├── <style>          → CSS Variables + Custom Styles + Print Styles
├── <body>           → Bootstrap 5 Layout
│   ├── Sticky Header → Logo, Jam, Tx Count, Tombol Aksi
│   ├── Tab Nav       → Kasir (POS) | Laporan Penjualan
│   ├── Tab: POS      → Product Grid + Cart + Discount + Payment
│   ├── Tab: Laporan  → Ringkasan Harian + Tabel Riwayat + Summary
│   ├── Modal: CRUD   → Form Produk + Tabel Produk
│   ├── Modal: Diskon → Diskon Per Item (%, Rp)
│   ├── Modal: Struk  → Preview + Cetak
│   ├── Modal: Info   → Panduan + Shortcut + Changelog + Tentang
│   └── Toast         → Notifikasi
└── <script>          → Business Logic (Data, Cart, Discount, Payment, CRUD, Report)
```

### Pola Desain
- **Single-File Architecture** — Seluruh HTML, CSS, dan JavaScript dalam satu file untuk kemudahan deployment
- **Event Delegation** — Klik produk menggunakan event delegation pada container, menghindari inline onclick rentan XSS
- **Escape Helper** — Fungsi `escapeHtml()` untuk sanitasi semua data yang di-render ke DOM
- **Try-Catch Fallback** — Semua operasi `JSON.parse` dari localStorage dibungkus try-catch dengan fallback ke data default
- **State Management** — Tiga variabel global (`PRODUCTS`, `CART`, `SALES`) + `CART_DISCOUNT` sebagai single source of truth
- **Discount Calculation** — Per-item discount dihitung terlebih dahulu, lalu global discount dihitung dari sisa subtotal

---

## Struktur File

```
/home/z/my-project/download/
├── pos-interaktif.html    → Aplikasi utama (self-contained HTML)
├── README.md              → Dokumentasi lengkap (file ini)
└── SKILL.md               → Spesifikasi agentic skill untuk KasirPOS
```

---

## Panduan Instalasi & Penggunaan

### Persyaratan
- Browser modern (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)
- Resolusi layar minimum: 1024x768 (optimal: 1280x800+)
- Printer termal 58mm (opsional, untuk cetak struk)

### Cara Menjalankan
1. Download file `pos-interaktif.html`
2. Buka file tersebut di browser (double-click atau drag ke browser)
3. Aplikasi siap digunakan

### Cara Deploy di Web Server
Jika ingin diakses via URL (bukan file lokal):
1. Upload file ke web server (Apache, Nginx, dll.)
2. Akses via `http://domain.com/pos-interaktif.html`
3. Pastikan MIME type `.html` terkonfigurasi dengan benar

### Backup Data
- **Manual**: Gunakan tombol "Export CSV" di tab Laporan Penjualan
- **Browser**: Data tersimpan di localStorage browser spesifik per domain/path
- **Penting**: Jangan hapus cache/cookie browser jika ingin menyimpan data

---

## Panduan Fitur Detail

### Alur Kerja Kasir

1. **Pilih Produk** — Klik kartu produk di grid kiri. Produk akan otomatis masuk keranjang dengan qty = 1. Klik lagi untuk menambah qty.
2. **Atur Jumlah** — Gunakan tombol (+) dan (-) di keranjang. Qty minimum = 1. Jika dikurangi di bawah 1, item dihapus.
3. **Diskon (Opsional)** — Klik ikon tag pada item di keranjang untuk diskon per barang, atau atur diskon global di bagian Diskon.
4. **Bayar** — Klik shortcut nominal atau ketik manual. Tombol "UANG PAS" mengisi nominal = total belanja (setelah diskon).
5. **Kembalian** — Sistem otomatis menghitung dan menampilkan kembalian. Jika uang kurang, ditampilkan peringatan.
6. **Konfirmasi** — Tekan "Bayar & Simpan". Transaksi tersimpan ke localStorage.
7. **Cetak Struk** — Modal struk otomatis muncul. Klik "Cetak Struk" untuk mengirim ke printer.

### Sistem Diskon

#### Diskon Per Item
1. Klik ikon tag (warna kuning) pada item di keranjang
2. Pilih tipe diskon: Tanpa / Persen (%) / Nominal (Rp)
3. Masukkan nilai diskon
4. Klik "Terapkan"
5. Diskon akan terlihat sebagai badge kuning di item dan baris merah di ringkasan keranjang

#### Diskon Global
1. Di bagian Diskon (antara keranjang dan pembayaran)
2. Pilih tipe: Off / % / Rp
3. Masukkan nilai diskon
4. Diskon otomatis diterapkan ke total

#### Urutan Perhitungan
1. Subtotal = (harga jual x qty) untuk semua item
2. Total Diskon Item = jumlah diskon dari semua item
3. Subtotal setelah diskon item = Subtotal - Total Diskon Item
4. Diskon Global = dihitung dari subtotal setelah diskon item
5. Total = Subtotal - Total Diskon Item - Diskon Global

### Manajemen Produk

1. Klik "Kelola Produk" di header
2. Isi form: Nama Barang, Harga Beli (Modal), Harga Jual
3. Preview margin otomatis muncul saat mengisi harga
4. Klik "Simpan" untuk menambah, atau "Edit" pada tabel untuk mengubah
5. Klik "Pulihkan Default" untuk reset ke daftar produk bawaan

### Laporan Penjualan

1. Klik tab "Laporan Penjualan"
2. **Ringkasan Kasir Hari Ini** ditampilkan di bagian atas (Omzet, Laba, Transaksi, Item Terjual)
3. Pilih filter waktu (Hari Ini / 7 Hari / 30 Hari / Semua)
4. Lihat ringkasan di bagian bawah tabel
5. Klik "Export CSV" untuk download data
6. Klik ikon sampah di kolom Aksi untuk hapus transaksi individual

---

## Keyboard Shortcut

| Tombol | Fungsi | Kondisi |
|--------|--------|---------|
| `F1` | Buka tab Kasir (POS) | Kapan saja |
| `F2` | Buka tab Laporan Penjualan | Kapan saja |
| `F3` | Fokus ke input pencarian produk | Kapan saja |
| `Enter` | Proses pembayaran | Fokus di input bayar |
| `Esc` | Kosongkan keranjang | Kapan saja |

---

## Penyimpanan Data

### Key localStorage

| Key | Isi | Tipe |
|-----|-----|------|
| `pos_products_db` | Daftar produk (id, name, buyPrice, sellPrice) | JSON Array |
| `pos_sales_db` | Riwayat transaksi (id, timestamp, items, discount, total, paid, change, profit) | JSON Array |

### Skema Data

**Produk:**
```json
{
  "id": "string (base36 timestamp + random)",
  "name": "string (max 100 char)",
  "buyPrice": "number (>= 0)",
  "sellPrice": "number (> 0)"
}
```

**Item Keranjang:**
```json
{
  "id": "string",
  "name": "string",
  "buyPrice": "number",
  "sellPrice": "number",
  "qty": "number",
  "discountType": "none | percent | nominal",
  "discountValue": "number"
}
```

**Transaksi:**
```json
{
  "id": "string (base36 timestamp + random)",
  "timestamp": "string (ISO 8601)",
  "items": [
    {
      "id": "string",
      "name": "string",
      "buyPrice": "number",
      "sellPrice": "number",
      "qty": "number",
      "discountType": "none | percent | nominal",
      "discountValue": "number",
      "discountAmount": "number"
    }
  ],
  "subtotal": "number",
  "itemDiscount": "number",
  "globalDiscountType": "none | percent | nominal",
  "globalDiscountValue": "number",
  "globalDiscount": "number",
  "totalDiscount": "number",
  "total": "number",
  "paid": "number",
  "change": "number",
  "profit": "number"
}
```

### Kapasitas
- localStorage biasanya menyimpan 5-10 MB per origin
- Estimasi: ~8.000-10.000 transaksi dapat disimpan sebelum mencapai batas
- Jika localStorage penuh, operasi `setItem` akan gagal dan data tidak tersimpan

---

## Changelog

### v2.2.0 (24 Mei 2026) — Fitur Diskon & Perbaikan Struk
- **[FEAT]** Fitur diskon per item — persen (%) atau nominal (Rp) langsung di keranjang
- **[FEAT]** Diskon global keseluruhan — persen (%) atau nominal (Rp) di bagian diskon
- **[FEAT]** Tombol Reset Diskon untuk menghapus semua diskon sekaligus
- **[FEAT]** Info diskon ditampilkan di ringkasan keranjang (subtotal, diskon item, diskon global, total)
- **[FEAT]** Tab Tentang/Developer di modal Info
- **[FEAT]** Kolom Diskon di tabel laporan penjualan
- **[FIX]** Format cetak struk diperbaiki — nama barang full 1 baris, di bawahnya qty x harga satuan = jumlah
- **[FIX]** Info diskon ditampilkan di struk pembelian (per item dan global)
- **[UX]** Ringkasan Kasir Hari Ini dipindah ke tab Laporan agar tampilan Kasir lebih bersih

### v2.1.0 (24 Mei 2026) — Perbaikan Komprehensif & Fitur Baru
- **[FIX]** Kerentanan XSS pada onclick handler produk — diubah ke event delegation dengan data-attribute
- **[FIX]** Shortcut nominal render duplikat akibat innerHTML concatenation — dipindahkan ke HTML statis
- **[FIX]** Duplikasi class `input-group` pada input pembayaran manual
- **[FIX]** Animasi kembalian hanya berjalan sekali — ditambahkan re-trigger via class toggle
- **[FIX]** Shortcut highlight tidak hilang saat input manual — ditambahkan `onPaymentInput()`
- **[FIX]** JSON.parse tanpa error handling — ditambahkan try-catch fallback
- **[FIX]** Cetak struk rentan diblokir popup blocker — diubah ke `@media print` dengan `window.print()`
- **[FIX]** Produk di keranjang tidak diberi tanda visual — ditambahkan class `.in-cart`
- **[FEAT]** Keyboard shortcut: F1, F2, F3, Enter, Esc
- **[FEAT]** Hapus transaksi individual di laporan penjualan
- **[FEAT]** Hapus semua riwayat penjualan dengan konfirmasi
- **[FEAT]** Modal Info & Panduan dengan tab Panduan, Shortcut, dan Changelog
- **[UX]** Konfirmasi sebelum mengosongkan keranjang yang sudah terisi
- **[UX]** Versi aplikasi ditampilkan di header
- **[UX]** Validasi harga jual tidak boleh 0 pada manajemen produk
- **[SECURITY]** Sanitasi input nama produk (max-length, escape HTML)

### v2.0.0 (24 Mei 2026) — Rilis Awal POS Interaktif
- Modul POS interaktif dengan grid produk dan keranjang belanja
- Shortcut nominal bayar 5K - 100K + tombol UANG PAS
- Kalkulasi kembalian otomatis dengan peringatan uang kurang
- Cetak struk pembelian format kertas termal 58mm
- Manajemen produk CRUD (nama, harga beli, harga jual)
- Tab laporan penjualan real-time dengan filter dan export CSV
- Penyimpanan data di localStorage
- Arsitektur dan style diadopsi dari template medis Jadwal Obat 10 Hari

---

## Keamanan & Limitasi

### Keamanan
- Semua input disanitasi dengan `escapeHtml()` sebelum rendering ke DOM
- Produk menggunakan event delegation alih-alih inline `onclick` untuk mencegah injection
- `JSON.parse` dibungkus try-catch untuk mencegah crash pada data corrupt
- Diskon dibatasi: persen maksimal 100%, nominal tidak melebihi subtotal item

### Limitasi
- **Single User** — Tidak mendukung multi-kasir atau sinkronisasi antar perangkat
- **localStorage** — Data tersimpan per browser, tidak bisa diakses lintas browser/perangkat
- **Tidak Ada Backup Otomatis** — Backup harus dilakukan manual via Export CSV
- **Kapasitas Terbatas** — localStorage dibatasi ~5-10 MB per origin
- **Tidak Ada Autentikasi** — Siapa saja yang membuka file bisa mengakses semua fitur
- **Offline Only** — Tidak ada integrasi pembayaran digital, QRIS, atau API eksternal

---

## Kontribusi

Proyek ini dikembangkan sebagai aplikasi mandiri. Untuk melakukan modifikasi:

1. Buka `pos-interaktif.html` di text editor
2. Modifikasi sesuai kebutuhan (CSS di `<style>`, HTML di `<body>`, JS di `<script>`)
3. Test di browser sebelum deploy
4. Update versi di variabel `APP_VERSION` dan section Changelog

---

*Dibangun dengan arsitektur yang diinspirasi oleh template medis "Jadwal Obat 10 Hari - Siklus Medis 24 Jam"*
