# Issue #08: Hapus Produk Menghapus Varian dan Modifier Secara Cascade Tanpa Peringatan

**Status:** Open
**Severity:** High (data varian dan modifier hilang tanpa konfirmasi; komentar kode menyatakan perilaku yang salah)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian schema dengan halaman produk frontend
**Komponen Terdampak:** halaman produk frontend (`js/items.js`), PRD BR-009

## Ringkasan

Schema `product_variant` dan `product_modifier_group` memakai `onDelete: cascade` ke `product`, dan `product_modifier_option` cascade ke grupnya. Menghapus satu produk berarti seluruh varian, grup modifier, dan opsinya ikut terhapus. Komentar pada `performDelete()` di `js/items.js` menyatakan sebaliknya: backend menolak dengan 409 bila produk masih memiliki varian atau grup modifier. Modal konfirmasi hapus hanya menampilkan nama produk, tanpa menyebut data turunan yang ikut hilang. BR-009 mensyaratkan peringatan sebelum memutus tautan varian atau modifier dari item.

## Fakta yang Ditemukan

| Sumber | Pernyataan atau kondisi |
|--------|-------------------------|
| [schema/product_variant.js](../../src/pos-server/schema/product_variant.js) | Relasi ke `product` dengan `onDelete: 'cascade'` |
| [schema/product_modifier_group.js](../../src/pos-server/schema/product_modifier_group.js) | Relasi ke `product` dengan `onDelete: 'cascade'` |
| [schema/product_modifier_option.js](../../src/pos-server/schema/product_modifier_option.js) | Relasi ke `product_modifier_group` dengan `onDelete: 'cascade'` |
| [Database design M-001, 4.3](../05-database-design/M-001-produk-kategori.md) | "FK `onDelete: cascade`, yaitu varian ikut terhapus bila produk dihapus" |
| [js/items.js](../../src/pos-frontend-integrasi/apps/pos/js/items.js), `performDelete()` | Komentar: "BR-005: item yang masih direferensikan data lain, mis. varian atau grup modifier, tidak dapat dihapus. Backend membalas 409 beserta pesannya." |
| [PRD M-001, BR-009](../03-product-requirements/M-001-produk-kategori.md) | Penghapusan yang memutus tautan varian atau modifier dari item aktif harus memberi peringatan |

BR-005 pada PRD membahas referensi dari transaksi (M-002), bukan dari varian atau modifier. Komentar kode salah mengutip aturan tersebut.

## Dampak

1. Pengguna dapat menghapus produk beserta seluruh varian dan modifiernya hanya dengan satu konfirmasi bernama produk, tanpa tahu ada data turunan yang ikut hilang.
2. Komentar kode menyesatkan pengembang berikutnya tentang perilaku backend.

## Rekomendasi Tindak Lanjut

1. Perbaiki komentar `performDelete()` agar mencatat perilaku cascade yang sebenarnya.
2. Sebelum menghapus, hitung jumlah varian dan grup modifier milik produk (data varian sudah dimuat untuk modal detail; grup modifier dapat diambil dengan `read` berfilter `product_id`), lalu tampilkan jumlahnya pada modal konfirmasi sebagai peringatan sesuai BR-009.
3. Tinjau apakah `onDelete: cascade` memang kebijakan yang diinginkan, atau BR-009 menghendaki `restrict` sehingga pengguna harus menghapus varian dan modifier lebih dulu. Keputusan ini perlu dicatat pada database design 4.3 dan 4.4.

## File Terdampak

- `src/pos-frontend-integrasi/apps/pos/js/items.js`
- `src/pos-frontend-integrasi/apps/pos/items.html` (modal `delete_item`)
- `docs/05-database-design/M-001-produk-kategori.md` (bila kebijakan cascade diubah)
