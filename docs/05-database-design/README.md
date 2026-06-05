# DATABASE DESIGN — INDEX
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Dokumen** | 05 — Database Design (folder) |
| **Versi Dokumen** | 0.1 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

> **Struktur dokumen:** Database design dipecah per modul agar tiap berkas tetap ringkas. **File ini (README)** memuat bagian bersama — konvensi penamaan, kolom audit standar, tipe data, dan indeks modul. **Skema tiap modul** berada pada berkas terpisah `M-NNN-<nama-modul>.md` di folder ini.

---

## 1. Pendahuluan (Introduction)

Dokumen ini adalah **rujukan tunggal** untuk desain database POS Rumah Makan. Skema diturunkan dari kebutuhan pada [03-product-requirements](../03-product-requirements/README.md) dan mengikuti arsitektur backend pada [04-technical-specification](../04-technical-specification/README.md).

Skema ditulis dalam format **SDF (Schema Definition File)** RESTForge — berkas `schema/<table>.js` yang mengekspor `defineModel(...)`. Dokumen ini **tidak menulis ulang spesifikasi SDF**; konvensi SDF dirujuk **berdasarkan nama** dari catalog RESTForge (lihat Bagian 5). Yang ditulis di sini adalah **skema konkret POS** (tabel, kolom, tipe, constraint, relasi) per modul.

---

## 2. Konvensi Penamaan (Naming Convention)

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

## 3. Kolom Audit Standar (Standard Audit Columns)

Setiap tabel menyertakan empat kolom audit dengan deklarasi seragam berikut:

```javascript
created_at: 'timestamp default:now()',
created_by: 'string:100',
updated_at: 'timestamp',
updated_by: 'string:100'
```

> `updated_at` ditulis **plain** (`timestamp`). Auto-update ditangani oleh layer RDF (konvensi `auditColumns` di BaseModel runtime), **bukan** marker SDF — modifier `autoUpdate` sudah *deprecated*.

---

## 4. Tipe Data & Pola yang Dipakai (Data Types & Patterns)

| Kebutuhan | Tipe SDF | Catatan |
|-----------|----------|---------|
| Identifier (PK/FK) | `string:36` | UUID disimpan sebagai string 36 karakter |
| Kode/label pendek | `string:20` – `string:100` | mis. `category_code`, `product_name` |
| Deskripsi panjang | `text` | bebas panjang |
| Harga & penyesuaian harga | `decimal:15,2` | nominal Rupiah; `checks` `gte: 0` menjaga non-negatif (BR-004) |
| Urutan tampil | `integer default:0` | mis. `sort_order` (FR-003) |
| Status aktif/ketersediaan | `boolean default:true` | `is_active` = status aktif/nonaktif record; `is_available` = sold-out (BR-006, BR-010) |
| Tanggal/waktu | `date` / `timestamp` | audit & tanggal transaksi |

**Pola umum:** `is_active` adalah **status aktif/nonaktif** sebuah record (menampilkan/menyembunyikan dari operasi), **bukan** penanda penghapusan. Pencegahan penghapusan record yang masih direferensikan ditangani **FK `onDelete: restrict`** (BR-005), terpisah dari `is_active`. Ketersediaan menu memakai `is_available` (BR-010); seluruh nominal harga dijaga `>= 0` lewat CHECK constraint.

---

## 5. Rujukan Spesifikasi SDF (by name)

Detail sintaks tidak diduplikasi di sini; lihat catalog SDF RESTForge berdasarkan nama: *Naming Convention*, *Field Shorthand*, *Field Types*, *Constraints*, *Check Constraints*, *Composite Unique*, *Foreign Keys*, *Relations*, *Indexes*.

---

## 6. Indeks Modul (Module Index)

| ID | Modul | Berkas Skema | Tabel | Status |
|----|-------|-------------|-------|--------|
| M-001 | Manajemen Produk & Kategori | [M-001-produk-kategori.md](M-001-produk-kategori.md) | `product_category`, `product`, `product_variant`, `product_modifier_group`, `product_modifier_option` | ✓ Draft lengkap |
| M-002 | Manajemen Order | *(belum dibuat)* | — | ☐ Belum |
| … | … | … | … | … |

> Penambahan modul: buat berkas `M-NNN-<nama-modul>.md`, rancang tabelnya mengikuti konvensi di atas, lalu daftarkan barisnya di tabel ini.

---

## Riwayat Perubahan (Changelog)

| Versi | Tanggal | Peru