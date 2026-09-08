# DATABASE DESIGN — INDEX
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Dokumen** | 05 — Database Design (folder) |
| **Versi Dokumen** | 0.3 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-09-08 |
| **PIC** | Project Lead |
| **Status** | Draft |

> **Struktur dokumen:** Database design dipecah per modul agar tiap berkas tetap ringkas. **File ini (README)** memuat bagian bersama, yaitu konvensi penamaan, kolom audit standar, tipe data, dan indeks modul. **Skema tiap modul** berada pada berkas terpisah `M-NNN-<nama-modul>.md` di folder ini.

---

## 1. Pendahuluan

Dokumen ini adalah **rujukan tunggal** untuk desain database POS Rumah Makan. Skema diturunkan dari kebutuhan pada [03-product-requirements](../03-product-requirements/README.md) dan mengikuti arsitektur backend pada [04-technical-specification](../04-technical-specification/README.md).

Skema ditulis dalam format **SDF (Schema Definition File)** RESTForge, yaitu berkas `schema/<table>.js` yang mengekspor `defineModel(...)`. Dokumen ini **tidak menulis ulang spesifikasi SDF**; konvensi SDF dirujuk **berdasarkan nama** dari catalog RESTForge (lihat Bagian 5). Yang ditulis di sini adalah **skema konkret POS** (tabel, kolom, tipe, constraint, relasi) per modul.

---

## 2. Konvensi Penamaan

Mengikuti konvensi SDF RESTForge:

| Elemen | Konvensi | Contoh |
|--------|----------|--------|
| Nama tabel | `snake_case`, singular | `product_category`, `product` |
| Nama field | `snake_case` | `product_category_id`, `created_at` |
| Primary key | `<table>_id`, `string:36` | `product_category_id`, `product_id` |
| Foreign key | sama dengan PK target | FK ke `product_category.product_category_id` → `product_category_id` |
| Unique / index / FK constraint | auto-generate oleh generator (`uq_*`, `idx_*`, `fk_*`) | — |

Setiap model memakai `schema: 'public'` dan diletakkan pada satu berkas `schema/<table>.js`.

---

## 3. Kolom Audit Standar

Setiap tabel menyertakan empat kolom audit dengan deklarasi seragam berikut:

```javascript
created_at: 'timestamp default:now()',
created_by: 'string:100',
updated_at: 'timestamp',
updated_by: 'string:100'
```

> `updated_at` ditulis **plain** (`timestamp`). Auto-update ditangani oleh layer RDF (konvensi `auditColumns` di BaseModel runtime), **bukan** marker SDF; modifier `autoUpdate` sudah *deprecated*.

---

## 4. Tipe Data & Pola yang Dipakai

| Kebutuhan | Tipe SDF | Catatan |
|-----------|----------|---------|
| Identifier (PK/FK) | `string:36` | UUID disimpan sebagai string 36 karakter |
| Kode/label pendek | `string:20` – `string:100` | mis. `product_code`, `product_name` |
| Deskripsi panjang | `text` | bebas panjang |
| Metadata file upload | `json` | JSONB pada PostgreSQL; berisi array metadata file hasil endpoint `/upload` (mis. `photo_url`) |
| Harga & penyesuaian harga | `decimal:15,2` | nominal Rupiah; `checks` `gte: 0` menjaga non-negatif (BR-004) |
| Urutan tampil | `integer default:0` | mis. `sort_order` (FR-003) |
| Status aktif/ketersediaan | `boolean default:true` | `is_active` = status aktif/nonaktif record; `is_available` = sold-out (BR-006, BR-010) |
| Tanggal/waktu | `date` / `timestamp` | audit & tanggal transaksi |

**Pola umum:** `is_active` adalah **status aktif/nonaktif** sebuah record (menampilkan/menyembunyikan dari operasi), **bukan** penanda penghapusan. Pencegahan penghapusan record yang masih direferensikan ditangani **FK `onDelete: restrict`** (BR-005), terpisah dari `is_active`. Ketersediaan produk memakai `is_available` (BR-010); seluruh nominal harga dijaga `>= 0` lewat CHECK constraint.

---

## 5. Rujukan Spesifikasi SDF (berdasarkan nama)

Detail sintaks tidak diduplikasi di sini; lihat catalog SDF RESTForge berdasarkan nama: *Naming Convention*, *Field Shorthand*, *Field Types*, *Constraints*, *Check Constraints*, *Composite Unique*, *Foreign Keys*, *Relations*, *Indexes*.

---

## 6. Indeks Modul

| ID | Modul | Berkas Skema | Tabel | Status |
|----|-------|-------------|-------|--------|
| M-001 | Manajemen Produk & Kategori | [M-001-produk-kategori.md](M-001-produk-kategori.md) | `product_category`, `product`, `product_variant`, `product_modifier_group`, `product_modifier_option` | ✓ Draft lengkap |
| M-002 | Manajemen Order, bagian 1: data master penjualan | [M-002-master-penjualan.md](M-002-master-penjualan.md) | `customer`, `dining_table`, `employee`, `tax`, `payment_method` | ✓ Draft (tabel transaksi menyusul) |
| … | … | … | … | … |

> Penambahan modul: buat berkas `M-NNN-<nama-modul>.md`, rancang tabelnya mengikuti konvensi di atas, lalu daftarkan barisnya di tabel ini.

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 0.1 | 2026-06-06 | Dokumen index dibuat: konvensi penamaan, kolom audit, tipe data, dan indeks modul | Project Lead |
| 0.2 | 2026-08-30 | Tabel tipe data ditambah baris `json` untuk metadata file upload; contoh kode pendek disesuaikan setelah `category_code` dihapus | Project Lead |
| 0.3 | 2026-09-08 | Indeks modul: M-002 bagian 1 (data master penjualan) didaftarkan dengan berkas `M-002-master-penjualan.md` | Project Lead |