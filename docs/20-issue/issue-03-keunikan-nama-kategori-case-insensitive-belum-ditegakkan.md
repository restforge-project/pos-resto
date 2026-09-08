# Issue #03: Keunikan Nama Kategori Case-Insensitive (BR-001) Belum Ditegakkan di Layer Mana Pun

**Status:** Open
**Severity:** High (business rule Must Have dan AC-002 tidak terpenuhi)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian PRD, database design, payload, dan frontend M-001
**Komponen Terdampak:** payload `product-category.json`, module hasil generate `product-category`, halaman kategori frontend, database design Bagian 5

## Ringkasan

BR-001 mewajibkan nama kategori unik tanpa membedakan huruf besar dan kecil, dan AC-002 menguji penolakan "minuman" ketika "Minuman" sudah ada. Database design Bagian 5 menyatakan aturan ini ditegakkan di layer RDF lewat `fieldValidation`. Pemeriksaan payload, module hasil generate, dan frontend memperlihatkan tidak ada satu pun lapisan yang melakukannya. Constraint unik di database bersifat case-sensitive, sehingga kedua nama tersebut saat ini diterima.

## Fakta yang Ditemukan

| Lapisan | Kondisi |
|---------|---------|
| Database | `category_name: 'string:100 notnull unique'` menghasilkan UNIQUE constraint case-sensitive pada PostgreSQL |
| [payload/product-category.json](../../src/pos-server/payload/product-category.json) | `category_name` hanya memuat `required`, `maxLength`, dan `unique: true`; tidak ada aturan case-insensitive |
| [src/modules/pos/product-category.js](../../src/pos-server/src/modules/pos/product-category.js) | Duplikat hanya ditangani lewat penangkapan error PostgreSQL `23505`, yang baru terpicu bila nilainya persis sama |
| [js/categories.js](../../src/pos-frontend-integrasi/apps/pos/js/categories.js) | `VALIDATION_RULES` hanya memeriksa nama terisi dan `sort_order` bilangan bulat; tidak ada pembandingan dengan daftar nama yang sudah dimuat |
| [Database design M-001, Bagian 5](../05-database-design/M-001-produk-kategori.md) | Mengklaim penegakan di RDF (`fieldValidation`) atau query `UPPER()` |

Katalog constraint platform yang terpasang menyediakan `lowercase` dan `uppercase` sebagai transformasi nilai, tetapi tidak menyediakan opsi unik case-insensitive.

## Dampak

1. AC-002 gagal: kategori "Minuman" dan "minuman" dapat hidup berdampingan.
2. Database design menyatakan penegakan yang tidak ada, sehingga pembaca dokumen mengira aturan sudah berjalan.

## Rekomendasi Tindak Lanjut

Pilih mekanisme, lalu perbarui Bagian 5 database design agar sesuai dengan yang benar-benar dipasang:

1. Unique index fungsional `UPPER(category_name)` di database. Dukungan SDF untuk index ekspresi perlu dipastikan; bila tidak ada, index dibuat lewat migrasi manual dan dicatat.
2. Atau normalisasi nilai lewat constraint `lowercase` atau `uppercase` pada payload sehingga constraint unik biasa menjadi efektif, dengan konsekuensi nama tersimpan dalam satu bentuk huruf.
3. Pemeriksaan di frontend sebelum kirim tetap ditambahkan sebagai umpan balik cepat, bukan sebagai penegakan utama.

## File Terdampak

- `docs/05-database-design/M-001-produk-kategori.md` (Bagian 5)
- `src/pos-server/payload/product-category.json`
- `src/pos-server/schema/product_category.js` (bila memakai index fungsional)
- `src/pos-frontend-integrasi/apps/pos/js/categories.js`
