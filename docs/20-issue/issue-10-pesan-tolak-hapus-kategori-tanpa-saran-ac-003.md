# Issue #10: Penolakan Hapus Kategori Hanya Meneruskan Pesan Backend, AC-003 Menuntut Saran Tindakan

**Status:** Open
**Severity:** Medium (acceptance criteria Must Have belum terpenuhi pada sisi pesan)
**Ditemukan:** 2026-09-08 saat pemeriksaan halaman kategori frontend terhadap PRD
**Komponen Terdampak:** `js/categories.js`, PRD AC-003

## Ringkasan

AC-003 menyatakan bahwa ketika kategori yang masih memiliki item dihapus, sistem menolak dan menyarankan memindahkan item atau menonaktifkan kategori. Penolakan di backend sudah berjalan lewat FK `onDelete: restrict` dan dibalas 409. Frontend meneruskan pesan backend apa adanya, yaitu "Cannot delete: record is still referenced by other data", tanpa saran tindakan dan tanpa menyebut bahwa penyebabnya adalah produk yang tertaut.

## Fakta yang Ditemukan

| Sumber | Pernyataan atau kondisi |
|--------|-------------------------|
| [PRD M-001, AC-003](../03-product-requirements/M-001-produk-kategori.md) | "sistem menolak dan menyarankan memindahkan item atau menonaktifkan kategori" |
| [PRD M-001, BR-002](../03-product-requirements/M-001-produk-kategori.md) | Pengguna harus memindahkan atau menghapus item lebih dulu, atau menonaktifkan kategori |
| [js/categories.js](../../src/pos-frontend-integrasi/apps/pos/js/categories.js), `performDelete()` | Blok `catch` memanggil `handleApiError(err)` tanpa pesan khusus; komentar mengutip pesan backend generik |
| [docs/04-langkah-kerja/05-modul-kategori.md](../../src/pos-frontend-integrasi/docs/04-langkah-kerja/05-modul-kategori.md) | "Cukup teruskan pesan penolakannya ke pengguna lewat `handleApiError()`" |

## Dampak

Pengguna menerima pesan teknis berbahasa umum yang tidak menjelaskan data mana yang menahan penghapusan dan apa yang harus dilakukan. AC-003 tidak dapat dinyatakan lolos.

## Rekomendasi Tindak Lanjut

1. Pada blok `catch` di `performDelete()`, kenali status 409 dari action `delete` dan tampilkan pesan khusus kategori: kategori masih dipakai produk, pindahkan produk ke kategori lain atau nonaktifkan kategori.
2. Sesuaikan langkah 5 pada dokumen integrasi frontend agar tidak lagi menyarankan meneruskan pesan backend apa adanya.

## File Terdampak

- `src/pos-frontend-integrasi/apps/pos/js/categories.js`
- `src/pos-frontend-integrasi/docs/04-langkah-kerja/05-modul-kategori.md`
