# DATABASE DESIGN — M-002 Data Master Penjualan
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **ID Modul** | M-002 (bagian 1: data master) |
| **Nama Modul** | Manajemen Order, data master pendukung |
| **Bagian dari** | [README.md](README.md) (Database Design — index) |
| **Versi Dokumen** | 1.2 |
| **Tanggal Dibuat** | 2026-09-08 |
| **Terakhir Diperbarui** | 2026-09-08 |
| **PIC** | Project Lead |
| **Status** | Draft |

> Skema ditulis dalam format **SDF RESTForge** (`schema/<table>.js`). Konvensi penamaan, kolom audit, dan tipe data mengikuti [README.md](README.md) index. Tipe field, constraint, dan operasi CHECK yang dipakai sudah dicocokkan dengan catalog SDF platform terpasang (`codegen_get_dbschema_catalog`, schema 1.1) pada tanggal dokumen ini dibuat.
>
> **Cakupan:** dokumen ini hanya memuat lima tabel master yang menjadi prasyarat alur kasir pada M-002. Tabel transaksi (`order`, `order_item`, `payment`, dan turunannya) ditulis pada dokumen terpisah setelah PRD M-002 selesai. Kebutuhan sumber untuk saat ini adalah halaman kasir pada template (`pos-template/pos.html`), karena PRD M-002 belum dibuat.

---

## 1. Ruang Lingkup

Halaman kasir membutuhkan lima data master yang belum ada pada M-001: pelanggan untuk pilihan "Select Customer", meja untuk order Dine In, pegawai untuk pilihan "Waiter", pajak untuk baris Tax pada Payment Summary, dan metode pembayaran untuk alur bayar. Kelima tabel berdiri sendiri tanpa foreign key satu sama lain, dan baru dirujuk oleh tabel transaksi M-002 dan M-005.

Yang sengaja tidak dibuat pada tahap ini, beserta alasannya:

| Kebutuhan | Keputusan |
|-----------|-----------|
| Lantai atau area meja (`floor`) | Tidak dibuat. Meja cukup diidentifikasi lewat nama. |
| Peran pegawai sebagai tabel (`role`) | Tidak dibuat. Peran disimpan sebagai kolom `role_type` dengan CHECK `in`, karena daftarnya tetap dan tidak dikelola pengguna. |
| Akun login dan hak akses | Tidak dibuat. Sistem berjalan tanpa autentikasi; `employee` murni data master. |
| Tipe order (Dine In, Take Away, Delivery) | Tidak dibuat sebagai tabel. Nilainya tetap dan memicu logika berbeda, sehingga menjadi kolom CHECK pada tabel `order` nanti. |
| Kupon dan aturan diskon | Ditunda ke M-007. |
| Profil outlet dan service charge | Ditunda ke tahap cetak struk. |
| Penanda Veg/Non Veg pada produk | Tidak relevan untuk rumah makan lokal, tabel `product` tidak berubah. |

## 2. Daftar Tabel

| Tabel | Deskripsi | Relasi | Dipakai oleh |
|-------|-----------|--------|--------------|
| `customer` | Pelanggan yang dapat dilekatkan pada order | tidak ada | pilihan pelanggan pada panel order, modal tambah/ubah pelanggan |
| `dining_table` | Meja makan beserta kapasitas dan status pemakaian | tidak ada | pilihan meja pada order Dine In |
| `employee` | Pegawai dengan jenis peran | tidak ada | pilihan pelayan pada panel order |
| `tax` | Pajak persentase yang dihitung dari subtotal | tidak ada | baris Tax pada Payment Summary |
| `payment_method` | Metode pembayaran yang tersedia di kasir | tidak ada | pilihan metode saat bayar |

Kelima tabel memakai `is_active` sebagai saklar tampil/sembunyi pada pilihan kasir, bukan penanda hapus. Ketentuan ini sama dengan M-001 (lihat README Bagian 4).

---

## 3. Definisi Skema (SDF)

### 3.1 `customer` — `schema/customer.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('customer', {
  schema: 'public',

  fields: {
    customer_id:   'string:36 pk',
    customer_name: 'string:100 notnull',
    phone:         'string:20',
    email:         'string:100',
    birth_date:    'date',
    gender:        'string:10',
    address:       'text',
    is_active:     'boolean default:true',
    created_at:    'timestamp default:now()',
    created_by:    'string:100',
    updated_at:    'timestamp',
    updated_by:    'string:100'
  },

  checks: [
    { field: 'gender', in: ['male', 'female', 'other'] }
  ],

  indexes: [
    'phone',
    'is_active'
  ]
});
```

**Catatan:** Hanya `customer_name` yang wajib, karena kasir sering mencatat pelanggan hanya dengan nama saat transaksi berjalan. `phone` tidak dibuat unik agar satu nomor keluarga dapat dipakai beberapa pelanggan, tetapi diberi index karena menjadi kunci pencarian utama di kasir. `gender` boleh kosong; CHECK `in` hanya menilai nilai yang diisi karena `NULL` lolos CHECK pada PostgreSQL. `address` disediakan untuk order Delivery. Template menandai email, tanggal lahir, dan gender sebagai wajib, tetapi ketiganya dibuat opsional di sini agar input kasir tetap cepat.

**Contoh data:**

| customer_name | phone | email | gender | is_active |
|---|---|---|---|---|
| Andi Wijaya | 081234567001 | andi@example.com | male | true |
| Siti Rahma | 081234567002 | | female | true |
| Budi Santoso | 081234567003 | budi@example.com | male | true |
| Rina Kusuma | | | | true |

### 3.2 `dining_table` — `schema/dining_table.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('dining_table', {
  schema: 'public',

  fields: {
    dining_table_id: 'string:36 pk',
    table_name:      'string:50 notnull unique',
    seat_count:      'integer notnull default:2',
    status:          "string:20 notnull default:'available'",
    sort_order:      'integer notnull default:0',
    is_active:       'boolean default:true',
    created_at:      'timestamp default:now()',
    created_by:      'string:100',
    updated_at:      'timestamp',
    updated_by:      'string:100'
  },

  checks: [
    { field: 'seat_count', gte: 1 },
    { field: 'status', in: ['available', 'occupied'] }
  ],

  indexes: [
    'status',
    'is_active'
  ]
});
```

**Catatan:** `table_name` unik sebagai satu-satunya identitas meja. `seat_count` minimal 1. `status` mencerminkan pemakaian saat ini dan diubah oleh alur order M-002 (menjadi `occupied` saat order Dine In dibuka, kembali `available` saat order ditutup); pada tahap ini nilainya hanya diubah lewat form master. Nilai `reserved` belum dimasukkan karena reservasi termasuk M-003; penambahannya nanti berupa perubahan CHECK. `sort_order` mengatur urutan tampil meja di kasir.

**Contoh data:**

| table_name | seat_count | status | sort_order | is_active |
|---|---|---|---|---|
| Meja 1 | 2 | available | 1 | true |
| Meja 2 | 2 | available | 2 | true |
| Meja 3 | 4 | available | 3 | true |
| Meja 4 | 4 | occupied | 4 | true |
| Meja 5 | 6 | available | 5 | true |
| Meja 6 | 8 | available | 6 | false |

### 3.3 `employee` — `schema/employee.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('employee', {
  schema: 'public',

  fields: {
    employee_id:   'string:36 pk',
    employee_name: 'string:100 notnull',
    role_type:     "string:20 notnull default:'waiter'",
    phone:         'string:20',
    is_active:     'boolean default:true',
    created_at:    'timestamp default:now()',
    created_by:    'string:100',
    updated_at:    'timestamp',
    updated_by:    'string:100'
  },

  checks: [
    { field: 'role_type', in: ['owner', 'supervisor', 'cashier', 'chef', 'waiter'] }
  ],

  indexes: [
    'role_type',
    'is_active'
  ]
});
```

**Catatan:** `role_type` adalah enum string dengan lima nilai yang diambil dari pilihan Role pada template (`users.html`). Kolom ini hanya menandai jenis pegawai untuk menyaring isi dropdown, misalnya panel order hanya menampilkan `waiter` dan `cashier`; tidak ada kaitan dengan hak akses karena sistem berjalan tanpa autentikasi. Index pada `role_type` mendukung penyaringan itu.

**Contoh data:**

| employee_name | role_type | phone | is_active |
|---|---|---|---|
| Pak Hendra | owner | 081234560001 | true |
| Dewi Lestari | supervisor | 081234560002 | true |
| Agus Pratama | cashier | 081234560003 | true |
| Rudi Hartono | chef | 081234560004 | true |
| Maya Sari | waiter | 081234560005 | true |
| Joko Susilo | waiter | 081234560006 | true |
| Nina Anggraini | waiter | | false |

### 3.4 `tax` — `schema/tax.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('tax', {
  schema: 'public',

  fields: {
    tax_id:       'string:36 pk',
    tax_name:     'string:50 notnull unique',
    tax_rate:     'decimal:5,2 notnull default:0',
    is_inclusive: 'boolean default:false',
    sort_order:   'integer notnull default:0',
    is_active:    'boolean default:true',
    created_at:   'timestamp default:now()',
    created_by:   'string:100',
    updated_at:   'timestamp',
    updated_by:   'string:100'
  },

  checks: [
    { field: 'tax_rate', gte: 0 }
  ],

  indexes: [
    'is_active'
  ]
});
```

**Catatan:** `tax_rate` disimpan sebagai persen dengan dua desimal (`decimal:5,2`, maksimum 999,99) dan dijaga tidak negatif lewat CHECK. Batas atas 100 tidak ditulis sebagai CHECK kedua pada field yang sama, melainkan ditegakkan di `fieldValidation` payload RDF (lihat Bagian 4). `is_inclusive` membedakan pajak yang sudah termasuk dalam harga (`true`) dan yang ditambahkan di atas subtotal (`false`), mengikuti pilihan Inclusive/Exclusive pada template `tax-settings.html`. Service charge belum dimodelkan; bila M-005 memutuskan perhitungannya identik dengan pajak persentase, service charge dapat menjadi satu baris pada tabel ini tanpa perubahan skema.

**Contoh data:**

| tax_name | tax_rate | is_inclusive | sort_order | is_active |
|---|---|---|---|---|
| PB1 (Pajak Restoran) | 10.00 | false | 1 | true |
| PPN | 11.00 | true | 2 | false |

### 3.5 `payment_method` — `schema/payment_method.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('payment_method', {
  schema: 'public',

  fields: {
    payment_method_id: 'string:36 pk',
    method_name:       'string:50 notnull unique',
    method_type:       "string:20 notnull default:'cash'",
    sort_order:        'integer notnull default:0',
    is_active:         'boolean default:true',
    created_at:        'timestamp default:now()',
    created_by:        'string:100',
    updated_at:        'timestamp',
    updated_by:        'string:100'
  },

  checks: [
    { field: 'method_type', in: ['cash', 'card', 'qris', 'ewallet', 'transfer'] }
  ],

  indexes: [
    'is_active'
  ]
});
```

**Catatan:** `method_name` adalah label yang tampil di kasir (misalnya "GoPay"), sedangkan `method_type` mengelompokkannya ke lima jenis yang menentukan perilaku alur bayar pada M-005: `cash` memunculkan kalkulator kembalian, `qris` memunculkan kode QR, sisanya hanya mencatat referensi. "Split Payment" pada template bukan metode, melainkan mode bayar yang memakai lebih dari satu baris `payment_detail`, sehingga tidak masuk daftar ini.

**Contoh data:**

| method_name | method_type | sort_order | is_active |
|---|---|---|---|
| Tunai | cash | 1 | true |
| Kartu Debit/Kredit | card | 2 | true |
| QRIS | qris | 3 | true |
| GoPay | ewallet | 4 | true |
| OVO | ewallet | 5 | true |
| Transfer Bank | transfer | 6 | false |

---

## 4. Penegakan di Layer Lain

| Aturan | Penegakan |
|--------|-----------|
| `tax.tax_rate` maksimum 100 | `fieldValidation` payload RDF pada field `tax_rate` (`max: 100`); nama constraint diambil dari catalog `codegen_get_field_validation_catalog` saat payload dibuat. CHECK SDF hanya menjaga batas bawah. |
| `dining_table.seat_count >= 1` dan `tax.tax_rate >= 0` | Penegakan ganda: CHECK `gte` di database dan `min` pada `fieldValidation` payload RDF. Tanpa `min`, pelanggaran CHECK dibalas 500 generik oleh endpoint, sedangkan `min` membalas 400 dengan nama field (pola yang sama dengan [issue #05](../20-issue/issue-05-harga-negatif-dibalas-500-tanpa-validasi-payload.md)). |
| Baris nonaktif pada halaman master | `defaultScope.read` yang ditulis generator pada payload **dihapus** agar action `read` mengembalikan seluruh baris, sama seperti lima payload M-001; halaman master menampilkan status Active/Inactive dan menyediakan filternya sendiri. `defaultScope.lookup` `is_active: true` **dipertahankan** karena `lookup` adalah action yang dipakai pilihan kasir. |
| Nilai enum `gender`, `status`, `role_type`, `method_type` | CHECK `in` di database, ditambah pilihan tetap pada form frontend agar pengguna tidak mengetik bebas. Pesan error dari database saat nilai di luar daftar tetap diteruskan apa adanya oleh endpoint. |
| Keunikan `table_name`, `tax_name`, `method_name` | UNIQUE di database, case-sensitive. Keunikan case-insensitive tidak ditegakkan pada tahap ini. |
| Perubahan `dining_table.status` oleh alur order | Belum ada. Ditangani M-002 saat tabel `order` dibuat. |

---

## 5. Endpoint yang Dihasilkan

Setiap tabel menghasilkan satu payload RDF `payload/<nama-dengan-tanda-hubung>.json` dan satu endpoint pada project `pos`, mengikuti pola yang sama dengan M-001:

| Tabel | Payload | Resource endpoint | Action |
|-------|---------|-------------------|--------|
| `customer` | `payload/customer.json` | `customer` | `datatables`, `create`, `update`, `delete`, `first`, `lookup`, `read` |
| `dining_table` | `payload/dining-table.json` | `dining-table` | sama |
| `employee` | `payload/employee.json` | `employee` | sama |
| `tax` | `payload/tax.json` | `tax` | sama |
| `payment_method` | `payload/payment-method.json` | `payment-method` | sama |

Tidak ada action `upload` karena tidak ada kolom file. Alamat endpoint mengikuti pola `POST http://127.0.0.1:3355/api/pos/{resource}/{action}`.

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 1.0 | 2026-09-08 | Dokumen dibuat: lima tabel master penjualan (`customer`, `dining_table`, `employee`, `tax`, `payment_method`) beserta keputusan cakupan dan contoh data | Project Lead |
| 1.1 | 2026-09-08 | Bagian 4: penegakan ganda `min` pada `seat_count` dan `tax_rate`, serta keputusan `defaultScope` (`read` dihapus, `lookup` dipertahankan) berdasarkan temuan smoke test campaign master-penjualan-v1 | Project Lead |
| 1.2 | 2026-09-08 | Catatan implementasi: skema, payload, endpoint, contoh data, dan lima halaman frontend selesai pada campaign `master-penjualan-v1` (backend `src/pos-server` commit `e7a5a27`, `0513967`, `8899910`, `9de6860`; frontend `src/pos-frontend-integrasi` commit `f43df15`, `f2985db`, `13112aa`, `66193b5`), sudah di-merge fast-forward ke `main` kedua repo pada 2026-09-08 | Project Lead |
