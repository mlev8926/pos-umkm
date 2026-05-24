# SKILL.md — KasirPOS Universal Agentic Skill Specification

> **Versi Skill:** 1.1.0 | **Kompatibilitas:** Agentic Framework v2+ | **Tipe:** Web Application Skill | **App Version:** 2.2.0

---

## Metadata

```yaml
name: KasirPOS
version: 1.1.0
app_version: 2.2.0
type: web-application
category: point-of-sale
subtype: interactive-pos
runtime: browser
backend: none (localStorage)
framework: bootstrap-5.3.2
language: javascript-es6
single_file: true
entry_point: pos-interaktif.html
developer: Super Z AI Assistant (Z.ai)
```

---

## Deskripsi

KasirPOS adalah skill agentic yang menghasilkan aplikasi Point of Sale interaktif berbasis web. Skill ini mampu membuat, mengkonfigurasi, dan meng-deploy sistem kasir lengkap dengan modul transaksi, diskon (per item & global), manajemen inventori, dan pelaporan penjualan — semuanya dalam satu file HTML yang berjalan tanpa server.

Skill ini dirancang untuk digunakan oleh AI agent dalam merespons permintaan pengguna yang membutuhkan sistem POS/kasir, dengan kemampuan kustomisasi produk, format struk, konfigurasi shortcut pembayaran, dan sistem diskon yang fleksibel.

---

## Trigger Conditions

Skill ini diaktifkan ketika pengguna meminta:

### Pola Bahasa Indonesia
- "buatkan POS", "sistem kasir", "aplikasi kasir"
- "point of sale", "POS interaktif"
- "sistem penjualan", "kasir digital"
- "cetak struk", "struk termal"
- "manajemen produk" + "kasir" / "penjualan"
- "laporan penjualan" + "real-time"
- "shortcut nominal bayar"
- "diskon" + "kasir" / "POS"
- "diskon per barang", "diskon persen", "diskon nominal"

### Pola Bahasa Inggris
- "create POS system", "cash register app"
- "point of sale interactive"
- "thermal receipt printing"
- "product management with buy/sell price"
- "sales report real-time"
- "discount system", "item discount", "percentage discount"

### Konteks Tambahan
- Pengguna menyediakan file referensi (HTML/CSS) untuk diadopsi style-nya
- Pengguna menyebutkan kebutuhan spesifik: printer termal, nominal shortcut, CRUD produk
- Pengguna meminta localStorage sebagai penyimpanan data
- Pengguna meminta fitur diskon (persen, nominal, per item)

---

## Capabilities

### Capability 1: Modul Kasir Interaktif
```yaml
id: pos-transaction
description: Modul transaksi kasir dengan grid produk, keranjang belanja, diskon, dan pembayaran
inputs:
  - products: list of {name, buyPrice, sellPrice}
  - shortcut_amounts: list of nominal shortcut (default: [5000,10000,15000,20000,25000,50000,75000,100000])
  - currency: string (default: "Rp")
  - locale: string (default: "id-ID")
outputs:
  - interactive_product_grid: HTML grid dengan event delegation
  - shopping_cart: kartu item dengan qty control (+/-) dan tombol diskon
  - payment_section: shortcut nominal + input manual + kalkulasi kembalian
  - audio_feedback: beep on item add, success chime on payment
```

### Capability 2: Sistem Diskon
```yaml
id: discount-system
description: Sistem diskon fleksibel per item dan global
inputs:
  - enable_item_discount: boolean (default: true)
  - enable_global_discount: boolean (default: true)
  - discount_types: list of ["percent", "nominal"] (default: both)
  - max_percent: number (default: 100)
outputs:
  - item_discount_modal: modal diskon per item dengan tipe dan nilai
  - global_discount_section: bagian diskon global di keranjang
  - discount_summary: ringkasan diskon di keranjang (item + global)
  - discount_on_receipt: detail diskon tercetak di struk
  - discount_in_report: kolom diskon di tabel laporan
discount_calculation_order:
  - step 1: Hitung subtotal (harga jual x qty per item)
  - step 2: Hitung diskon per item (persen atau nominal)
  - step 3: Hitung subtotal setelah diskon item
  - step 4: Hitung diskon global dari subtotal setelah diskon item
  - step 5: Total = subtotal - diskon item - diskon global
```

### Capability 3: Cetak Struk Termal
```yaml
id: thermal-receipt
description: Cetak struk pembelian untuk printer termal 58mm
inputs:
  - paper_width: string (default: "58mm")
  - shop_name: string (default: "KasirPOS")
  - footer_message: string (default: "Terima kasih atas kunjungan Anda!")
outputs:
  - receipt_preview: modal preview struk
  - print_stylesheet: CSS @media print dengan ukuran termal
  - receipt_data: format monospace Courier New
  - item_format: nama barang full 1 baris, di bawahnya qty x harga satuan = jumlah
  - discount_lines: detail diskon per item dan global di struk
```

### Capability 4: Manajemen Produk CRUD
```yaml
id: product-crud
description: CRUD produk dengan nama, harga beli, harga jual, dan margin preview
inputs:
  - default_products: list of {name, buyPrice, sellPrice}
  - allow_reset: boolean (default: true)
outputs:
  - add_form: form input dengan validasi
  - product_table: tabel dengan edit/delete per row
  - margin_preview: badge margin real-time saat input harga
  - persistence: localStorage save/load
```

### Capability 5: Laporan Penjualan
```yaml
id: sales-report
description: Laporan penjualan real-time dengan filter dan export
inputs:
  - filter_options: list of {value, label} (default: today/week/month/all)
  - enable_csv_export: boolean (default: true)
  - enable_delete: boolean (default: true)
  - show_discount_column: boolean (default: true)
outputs:
  - daily_summary: ringkasan kasir hari ini (omzet, laba, transaksi, item terjual)
  - sales_table: tabel riwayat dengan sort terbaru + kolom diskon
  - summary_cards: omzet, profit, transaction count
  - csv_download: file CSV dengan header + rows (termasuk kolom diskon)
```

### Capability 6: Keyboard Shortcut
```yaml
id: keyboard-shortcuts
description: Keyboard shortcut untuk operasi kasir cepat
bindings:
  F1: switch_to_pos_tab
  F2: switch_to_report_tab
  F3: focus_product_search
  Enter: process_payment (when payment input focused)
  Escape: clear_cart
```

---

## Configuration Schema

```json
{
  "pos_config": {
    "app_name": "KasirPOS",
    "version": "2.2.0",
    "developer": "Super Z AI Assistant (Z.ai)",
    "currency": {
      "symbol": "Rp",
      "locale": "id-ID",
      "position": "prefix"
    },
    "receipt": {
      "paper_width": "58mm",
      "font_family": "Courier New",
      "shop_name": "KasirPOS",
      "footer_message": "Terima kasih atas kunjungan Anda!",
      "show_transaction_id": true,
      "item_format": "name_full_line_then_qty_x_price_equals_total",
      "show_discount_lines": true
    },
    "discount": {
      "enable_item_discount": true,
      "enable_global_discount": true,
      "types": ["percent", "nominal"],
      "max_percent": 100,
      "calculation_order": ["item_discount_first", "then_global_discount"]
    },
    "shortcuts": {
      "amounts": [5000, 10000, 15000, 20000, 25000, 50000, 75000, 100000],
      "include_exact": true,
      "exact_label": "UANG PAS"
    },
    "storage": {
      "type": "localStorage",
      "products_key": "pos_products_db",
      "sales_key": "pos_sales_db"
    },
    "ui": {
      "framework": "bootstrap-5.3.2",
      "icons": "bootstrap-icons-1.11.1",
      "theme": "light",
      "sticky_header": true,
      "audio_feedback": true,
      "animations": true,
      "daily_summary_location": "report_tab",
      "tabs": ["kasir_pos", "laporan_penjualan"]
    },
    "products": {
      "defaults": [
        {"name": "Kopi Susu Gula Aren", "buyPrice": 8000, "sellPrice": 15000},
        {"name": "Es Teh Manis", "buyPrice": 3000, "sellPrice": 7000}
      ]
    }
  }
}
```

---

## Agent Execution Flow

```
1. DETECT trigger condition (user request matching patterns)
   ↓
2. PARSE configuration from user context:
   - Product list (if provided)
   - Style reference (if file uploaded)
   - Currency/locale preferences
   - Shortcut amounts
   - Discount requirements (per item, global, types)
   ↓
3. GENERATE pos-interaktif.html:
   - Adopt CSS architecture from reference file (if provided)
   - Inject default products or custom products
   - Configure shortcuts, currency, receipt format
   - Apply discount system (per item + global, percent + nominal)
   - Apply security patterns (escapeHtml, event delegation, try-catch)
   - Format receipt items: name full line + qty x price = total
   - Place daily summary in report tab
   ↓
4. SAVE to /home/z/my-project/download/pos-interaktif.html
   ↓
5. GENERATE documentation:
   - README.md with full feature documentation + changelog
   - SKILL.md (this file) for future agent reference
   ↓
6. NOTIFY user with:
   - File location
   - Feature summary
   - Changelog of all changes made
```

---

## Security Checklist

Skill ini menerapkan pola keamanan berikut saat generating kode:

- [x] **XSS Prevention** — `escapeHtml()` pada semua data yang di-render ke DOM
- [x] **Event Delegation** — Produk grid menggunakan `data-product-id` + delegation, bukan inline onclick
- [x] **Input Validation** — Harga jual > 0, nama tidak kosong, max-length pada input, diskon persen max 100%
- [x] **Try-Catch Fallback** — `JSON.parse` dari localStorage dengan error recovery
- [x] **No eval()** — Tidak menggunakan `eval()`, `Function()`, atau `innerHTML` tanpa sanitasi
- [x] **Confirm Dialogs** — Konfirmasi sebelum operasi destruktif (hapus, reset, clear)
- [x] **Print Safety** — Menggunakan `@media print` CSS alih-alih `window.open()` yang bisa diblokir
- [x] **Discount Bounds** — Persen diskon maks 100%, nominal diskon tidak melebihi subtotal

---

## Customization Points

Agent dapat mengkustomisasi output berdasarkan parameter berikut:

| Parameter | Default | Kustomisasi |
|-----------|---------|-------------|
| Nama Toko | "KasirPOS" | Ganti di receipt preview + print area |
| Daftar Produk | 12 item F&B | Ganti array `DEFAULT_PRODUCTS` |
| Shortcut Nominal | 5K-100K | Ganti array amounts di HTML |
| Mata Uang | "Rp" | Ganti fungsi `formatRp()` |
| Locale | "id-ID" | Ganti `toLocaleString` parameter |
| Lebar Struk | 58mm | Ganti CSS `width: 58mm` |
| Footer Struk | "Terima kasih..." | Ganti string di receipt template |
| Warna Tema | Biru (#2563eb) | Ganti CSS `--pos-primary` variable |
| Bahasa UI | Indonesia | Ganti semua label text |
| Diskon Per Item | Aktif | Set `enable_item_discount` |
| Diskon Global | Aktif | Set `enable_global_discount` |
| Tipe Diskon | % + Rp | Set `discount_types` |
| Lokasi Ringkasan | Tab Laporan | Set `daily_summary_location` |

---

## Integration Patterns

### Pattern 1: Standalone
Deploy file HTML langsung ke browser. Tidak perlu server.

### Pattern 2: Iframe Embed
Embed ke dashboard atau aplikasi lain:
```html
<iframe src="pos-interaktif.html" width="100%" height="800"></iframe>
```

### Pattern 3: Backend Extension
Tambahkan sinkronisasi ke server dengan mengganti `localStorage` ke API calls:
```javascript
// Replace saveSales() with:
async function saveSales() {
    await fetch('/api/sales', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify(SALES)
    });
}
```

### Pattern 4: Multi-Terminal
Untuk multi-kasir, tambahkan WebSocket untuk real-time sync:
```javascript
const ws = new WebSocket('ws://server/pos');
ws.onmessage = (e) => {
    const data = JSON.parse(e.data);
    if (data.type === 'sale') SALES.push(data.sale);
};
```

---

## Testing Checklist

Saat agent menghasilkan kode POS, verifikasi:

- [ ] Buka file di browser tanpa error console
- [ ] Klik produk → masuk keranjang dengan qty=1
- [ ] Klik produk lagi → qty bertambah
- [ ] Tombol +/- mengubah qty dengan benar
- [ ] Qty=0 → item terhapus dari keranjang
- [ ] Diskon per item: klik ikon tag → modal muncul → pilih tipe → masukkan nilai → terapkan
- [ ] Diskon per item persen dihitung dengan benar
- [ ] Diskon per item nominal dihitung dengan benar
- [ ] Diskon global persen dihitung setelah diskon item
- [ ] Diskon global nominal dihitung dengan benar
- [ ] Tombol Reset Diskon menghapus semua diskon
- [ ] Ringkasan keranjang menampilkan subtotal, diskon item, diskon global, total
- [ ] Shortcut nominal mengisi input bayar (berdasarkan total setelah diskon)
- [ ] UANG PAS mengisi = total belanja setelah diskon
- [ ] Kembalian dihitung otomatis (berdasarkan total setelah diskon)
- [ ] Peringatan "Uang kurang" muncul saat bayar < total
- [ ] Bayar berhasil → struk muncul + keranjang kosong + diskon reset
- [ ] Struk: nama barang full 1 baris, qty x harga = jumlah di bawahnya
- [ ] Struk: diskon per item tercetak
- [ ] Struk: diskon global tercetak
- [ ] Cetak struk → dialog print muncul
- [ ] CRUD: Tambah produk baru
- [ ] CRUD: Edit produk
- [ ] CRUD: Hapus produk
- [ ] CRUD: Margin preview muncul saat input harga
- [ ] Laporan: Ringkasan Kasir Hari Ini ada di tab Laporan
- [ ] Laporan: Filter berfungsi
- [ ] Laporan: Kolom Diskon ada di tabel
- [ ] Laporan: Export CSV berfungsi
- [ ] Laporan: Hapus transaksi individual
- [ ] Tab Tentang/Developer ada di modal Info
- [ ] Keyboard shortcut F1-F3, Enter, Esc berfungsi
- [ ] Data persist setelah refresh halaman
- [ ] Tidak ada kerentanan XSS (test input `<script>alert(1)</script>` di nama produk)

---

## Changelog

### Skill v1.1.0 (24 Mei 2026)
- Update app_version ke 2.2.0
- Tambah Capability 2: Sistem Diskon (per item + global, persen + nominal)
- Update Capability 3: Format struk diperbaiki (nama full, qty x harga = jumlah)
- Update Capability 5: Kolom diskon di laporan, ringkasan harian di tab laporan
- Update Configuration Schema: tambah bagian `discount` dan `ui.daily_summary_location`
- Update Testing Checklist: tambah test case diskon
- Update Customization Points: tambah parameter diskon
- Tambah metadata developer

### Skill v1.0.0 (24 Mei 2026)
- Spesifikasi awal skill KasirPOS
- Definisi 6 capability utama
- Configuration schema
- Security checklist
- Customization points
- Integration patterns
- Testing checklist
