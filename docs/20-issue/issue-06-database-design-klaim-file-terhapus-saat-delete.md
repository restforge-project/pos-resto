# Issue #06: Database Design Menyatakan File Foto Ikut Terhapus saat `/delete`, Perilaku Aktual Sebaliknya

**Status:** Open
**Severity:** Medium (dokumen desain bertentangan dengan perilaku yang sudah diverifikasi)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian database design dengan dokumen integrasi frontend
**Komponen Terdampak:** database design M-001 Bagian 6, dokumen frontend `03-kontrak-endpoint.md` dan `08-upload-foto.md`

## Ringkasan

Database design M-001 Bagian 6 menyatakan file foto ikut dihapus otomatis saat `/delete` selama `uploadConfig.deleteOnRecordDelete` bernilai `true`. Dokumen integrasi frontend mencatat hasil pemanggilan langsung: response `delete` hanya berisi primary key pada `deleted_items`, sehingga backend tidak memiliki metadata foto untuk dibersihkan dan file tetap tinggal di storage. Penghapusan kemudian dipindahkan ke frontend lewat action `upload-delete`. Dua dokumen dalam satu repository menyatakan perilaku yang bertolak belakang untuk konfigurasi yang sama.

## Fakta yang Ditemukan

| Sumber | Pernyataan |
|--------|------------|
| [Database design M-001, Bagian 6](../05-database-design/M-001-produk-kategori.md) | "File ikut dihapus otomatis saat `/delete` selama `uploadConfig.deleteOnRecordDelete` bernilai `true` (default)" |
| [docs/03-kontrak-endpoint.md](../../src/pos-frontend-integrasi/docs/03-kontrak-endpoint.md) | "delete Tidak Menghapus File di Object Storage ... `deleted_items` hanya berisi primary key" |
| [docs/04-langkah-kerja/08-upload-foto.md](../../src/pos-frontend-integrasi/docs/04-langkah-kerja/08-upload-foto.md) | Pembersihan dipindahkan ke frontend lewat `removePhotoFiles()` setelah penghapusan record berhasil |
| [payload/product.json](../../src/pos-server/payload/product.json), [payload/product-category.json](../../src/pos-server/payload/product-category.json) | `deleteOnRecordDelete: true` masih terpasang |
| [js/categories.js](../../src/pos-frontend-integrasi/apps/pos/js/categories.js), [js/items.js](../../src/pos-frontend-integrasi/apps/pos/js/items.js) | `performDelete()` membaca `photo_url` dari state lalu memanggil `removePhotoFiles()` |

## Dampak

1. Pembaca database design mengandalkan pembersihan otomatis yang tidak terjadi. Penghapusan record lewat jalur selain frontend (misalnya Postman atau modul lain) meninggalkan file orphan di bucket.
2. Nilai `deleteOnRecordDelete: true` pada payload memberi kesan fitur aktif padahal tidak berfungsi pada versi platform yang terpasang.

## Rekomendasi Tindak Lanjut

1. Ubah baris "Hapus record" pada Bagian 6 database design agar mencatat perilaku aktual dan menyebut bahwa pembersihan dilakukan frontend.
2. Laporkan perilaku `deleteOnRecordDelete` yang tidak efektif ke pemilik platform RESTForge sebagai issue terpisah, karena akar masalahnya di luar repository ini.
3. Setelah platform diperbaiki, kembalikan tanggung jawab penghapusan ke backend dan lepas `removePhotoFiles()` dari alur hapus frontend.

## File Terdampak

- `docs/05-database-design/M-001-produk-kategori.md` (Bagian 6)
- `src/pos-frontend-integrasi/docs/03-kontrak-endpoint.md`
- `src/pos-frontend-integrasi/docs/04-langkah-kerja/08-upload-foto.md`
