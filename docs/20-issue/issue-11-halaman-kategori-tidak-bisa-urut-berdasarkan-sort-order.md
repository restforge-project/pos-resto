# Issue #11: Halaman Kategori Tidak Menyediakan Pengurutan Berdasarkan `sort_order`

**Status:** Open
**Severity:** Low (FR-003 Should Have; data tersimpan benar, hanya tidak dapat diperiksa dari halaman pengelolaan)
**Ditemukan:** 2026-09-08 saat pemeriksaan halaman kategori frontend terhadap PRD
**Komponen Terdampak:** `categories.html`, `js/categories.js`

## Ringkasan

FR-003 dan AC-004 menyangkut urutan tampil kategori yang disimpan pada kolom `sort_order`. Halaman kategori menampilkan nilai itu pada kolom "Order" dan menyediakan input "Display Order" pada form, tetapi dropdown "Sort by" hanya menawarkan Newest, Oldest, Name A-Z, dan Name Z-A. Pengurutan bawaan `newest` mengikuti `created_at`, sehingga pengguna yang mengubah beberapa nilai `sort_order` tidak dapat melihat hasil urutannya di halaman ini. Layar order yang menjadi tempat pengujian AC-004 belum dikembangkan.

## Fakta yang Ditemukan

| Sumber | Kondisi |
|--------|---------|
| [PRD M-001, FR-003 dan AC-004](../03-product-requirements/M-001-produk-kategori.md) | Pengguna menetapkan urutan tampil dan menyimpannya; layar order menampilkan kategori sesuai urutan baru |
| [categories.html](../../src/pos-frontend-integrasi/apps/pos/categories.html) | Opsi `sort-option`: `newest`, `oldest`, `asc`, `desc` |
| [js/categories.js](../../src/pos-frontend-integrasi/apps/pos/js/categories.js), `sortRows()` | Tidak ada cabang untuk `sort_order`; DataTables dipasang dengan `ordering: false` sehingga kolom "Order" tidak dapat diklik untuk mengurutkan |

## Dampak

Nilai `sort_order` hanya dapat diverifikasi dengan membuka satu per satu detail kategori. Kesalahan urutan (misalnya dua kategori bernilai sama) baru terlihat saat layar order dibuat.

## Rekomendasi Tindak Lanjut

1. Tambahkan opsi "Display Order" pada dropdown Sort by dan jadikan mode bawaan, karena urutan inilah yang dipakai layar kasir.
2. Pertimbangkan drag-and-drop pada tabel kategori sebagaimana disebut FR-003, atau cukup input angka seperti sekarang, dan catat pilihannya pada PRD.

## File Terdampak

- `src/pos-frontend-integrasi/apps/pos/categories.html`
- `src/pos-frontend-integrasi/apps/pos/js/categories.js`
