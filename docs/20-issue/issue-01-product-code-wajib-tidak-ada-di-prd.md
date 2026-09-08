# Issue #01: `product_code` Wajib dan Unik pada Implementasi, Tidak Ada di PRD

**Status:** Selesai (2026-09-08)
**Severity:** Medium (PRD dan implementasi menyatakan atribut wajib yang berbeda)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian PRD, database design, schema, dan frontend M-001
**Komponen Terdampak:** PRD M-001 (FR-005), schema `product`, payload `product.json`, halaman produk frontend

## Ringkasan

FR-005 pada PRD menetapkan atribut item produk: nama (wajib), kategori (wajib), deskripsi (opsional), dan harga jual (wajib). Schema, payload, dan form frontend menambahkan `product_code` sebagai kolom wajib sekaligus unik, sehingga pengguna tidak dapat menyimpan produk tanpa mengisi kode. Atribut ini tidak pernah disebut di PRD, sementara FR-001 justru menegaskan bahwa kategori tidak memakai kode.

## Fakta yang Ditemukan

| Sumber | Pernyataan |
|--------|------------|
| [PRD M-001, FR-005](../03-product-requirements/M-001-produk-kategori.md) | Atribut item: nama, kategori, deskripsi, harga jual. Tidak ada kode produk. |
| [Database design M-001, 4.2](../05-database-design/M-001-produk-kategori.md) | `product_code: 'string:20 notnull unique'` |
| [schema/product.js](../../src/pos-server/schema/product.js) | `product_code: 'string:20 notnull unique'` |
| [payload/product.json](../../src/pos-server/payload/product.json) | `product_code` dengan `required: true` dan `unique: true` |
| [items.html](../../src/pos-frontend-integrasi/apps/pos/items.html) | Input "Item Code" bertanda wajib pada form tambah dan ubah |
| [js/items.js](../../src/pos-frontend-integrasi/apps/pos/js/items.js) | Aturan validasi `product_code` diberi komentar "FR-005: product_code bersifat notnull unique pada skema" |

Komentar pada `items.js` merujuk FR-005 sebagai sumber, padahal FR-005 tidak memuat atribut tersebut.

## Dampak

1. Traceability FR ke schema putus: ada kolom wajib yang tidak dapat ditelusuri ke kebutuhan mana pun.
2. AC-005 (simpan item dengan nama, kategori, dan harga valid) tidak dapat lolos apa adanya, karena form menolak simpan tanpa kode.

## Rekomendasi Tindak Lanjut

Pilih salah satu, lalu selaraskan seluruh lapisan:

1. Tambahkan kode produk ke FR-005 dan AC-005 pada PRD sebagai atribut wajib dan unik, beserta alasan bisnisnya (misalnya kode pada struk atau integrasi kasir).
2. Atau jadikan `product_code` opsional (atau hapus) pada schema, payload, dan form, mengikuti PRD yang ada.

## File Terdampak

- `docs/03-product-requirements/M-001-produk-kategori.md`
- `docs/05-database-design/M-001-produk-kategori.md`
- `src/pos-server/schema/product.js`
- `src/pos-server/payload/product.json`
- `src/pos-frontend-integrasi/apps/pos/items.html`
- `src/pos-frontend-integrasi/apps/pos/js/items.js`

## Penyelesaian

Dipilih rekomendasi pertama: kode produk ditambahkan ke PRD sehingga schema, payload, dan form tidak berubah.

| File | Perubahan |
|------|-----------|
| [PRD M-001](../03-product-requirements/M-001-produk-kategori.md) v1.3 | US-003 menyebut kode; FR-005 menambah atribut kode produk (wajib, unik, maksimal 20 karakter) beserta alasan bisnisnya; AC-005 menyertakan kode; BR-011 (kode produk unik) dan AC-016 (tolak kode duplikat) ditambahkan |
| [PRD induk](../03-product-requirements/README.md) v1.1 | Rentang ID M-001 menjadi BR-001–011 dan AC-001–016 |
| [Database design M-001](../05-database-design/M-001-produk-kategori.md) v1.5 | Catatan 4.2 menautkan `product_code notnull unique` ke FR-005 dan BR-011 |

Komentar pada `items.js` yang merujuk FR-005 kini sesuai dengan isi PRD. Alasan bisnis pada FR-005 (identitas pada struk dan layar kasir, rujukan tetap bagi M-006 dan M-007) ditulis sebagai asumsi awal dan perlu dikonfirmasi pemilik produk.
