# SKILL.md — KasirPOS Universal Agentic Skill Specification

> **Versi Skill:** 1.0.0 | **Kompatibilitas:** Agentic Framework v2+ | **Tipe:** Web Application Skill

---

## Metadata

```yaml
name: KasirPOS
version: 1.0.0
type: web-application
category: point-of-sale
subtype: interactive-pos
runtime: browser
backend: none (localStorage)
framework: bootstrap-5.3.2
language: javascript-es6
single_file: true
entry_point: pos-interaktif.html
```

---

## Deskripsi

KasirPOS adalah skill agentic yang menghasilkan aplikasi Point of Sale interaktif berbasis web. Skill ini mampu membuat, mengkonfigurasi, dan meng-deploy sistem kasir lengkap dengan modul transaksi, manajemen inventori, dan pelaporan penjualan — semuanya dalam satu file HTML yang berjalan tanpa server.

Skill ini dirancang untuk digunakan oleh AI agent dalam merespons permintaan pengguna yang membutuhkan sistem POS/kasir, dengan kemampuan kustomisasi produk, format struk, dan konfigurasi shortcut pembayaran.

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

### Pola Bahasa Inggris
- "create POS system", "cash register app"
- "point of sale interactive"
- "thermal receipt printing"
- "product management with buy/sell price"
- "sales report real-time"

### Konteks Tambahan
- Pengguna menyediakan file referensi (HTML/CSS) untuk diadopsi style-nya
- Pengguna menyebutkan kebutuhan spesifik: printer termal, nominal shortcut, CRUD produk
- Pengguna meminta localStorage sebagai penyimpanan data

---

## Capabilities

### Capability 1: Modul Kasir Interaktif
```yaml
id: pos-transaction
description: Modul transaksi kasir dengan grid produk, keranjang belanja, dan pembayaran
inputs:
  - products: list of {name, buyPrice, sellPrice}
  - shortcut_amounts: list of nominal shortcut (default: [5000,10000,15000,20000,25000,50000,75000,100000])
  - currency: string (default: "Rp")
  - locale: string (default: "id-ID")
outputs:
  - interactive_product_grid: HTML grid dengan event delegation
  - shopping_cart: kartu item dengan qty control (+/-)
  - payment_section: shortcut nominal + input manual + kalkulasi kembalian
  - audio_feedback: beep on item add, success chime on payment
```

### Capability 2: Cetak Struk Termal
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
```

### Capability 3: Manajemen Produk CRUD
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

### Capability 4: Laporan Penjualan
```yaml
id: sales-report
description: Laporan penjualan real-time dengan filter dan export
inputs:
  - filter_options: list of {value, label} (default: today/week/month/all)
  - enable_csv_export: boolean (default: true)
  - enable_delete: boolean (default: true)
outputs:
  - sales_table: tabel riwayat dengan sort terbaru
  - summary_cards: omzet, profit, transaction count
  - csv_download: file CSV dengan header + rows
```

### Capability 5: Keyboard Shortcut
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
    "version": "2.1.0",
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
      "show_transaction_id": true
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
      "animations": true
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
   ↓
3. GENERATE pos-interaktif.html:
   - Adopt CSS architecture from reference file (if provided)
   - Inject default products or custom products
   - Configure shortcuts, currency, receipt format
   - Apply security patterns (escapeHtml, event delegation, try-catch)
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
- [x] **Input Validation** — Harga jual > 0, nama tidak kosong, max-length pada input
- [x] **Try-Catch Fallback** — `JSON.parse` dari localStorage dengan error recovery
- [x] **No eval()** — Tidak menggunakan `eval()`, `Function()`, atau `innerHTML` tanpa sanitasi
- [x] **Confirm Dialogs** — Konfirmasi sebelum operasi destruktif (hapus, reset, clear)
- [x] **Print Safety** — Menggunakan `@media print` CSS alih-alih `window.open()` yang bisa diblokir

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
- [ ] Shortcut nominal mengisi input bayar
- [ ] UANG PAS mengisi = total belanja
- [ ] Kembalian dihitung otomatis
- [ ] Peringatan "Uang kurang" muncul saat bayar < total
- [ ] Bayar berhasil → struk muncul + keranjang kosong
- [ ] Cetak struk → dialog print muncul
- [ ] CRUD: Tambah produk baru
- [ ] CRUD: Edit produk
- [ ] CRUD: Hapus produk
- [ ] CRUD: Margin preview muncul saat input harga
- [ ] Laporan: Filter berfungsi
- [ ] Laporan: Export CSV berfungsi
- [ ] Laporan: Hapus transaksi individual
- [ ] Keyboard shortcut F1-F3, Enter, Esc berfungsi
- [ ] Data persist setelah refresh halaman
- [ ] Tidak ada kerentanan XSS (test input `<script>alert(1)</script>` di nama produk)

---

## Changelog

### Skill v1.0.0 (24 Mei 2026)
- Spesifikasi awal skill KasirPOS
- Definisi 5 capability utama
- Configuration schema
- Security checklist
- Customization points
- Integration patterns
- Testing checklist
