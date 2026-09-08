# Issue #04: Aturan `min_select <= max_select` Hanya Ditegakkan di Frontend

**Status:** Selesai (2026-09-08)
**Severity:** Medium (dokumen menyatakan penegakan di backend yang tidak ada)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian database design, payload, dan frontend M-001
**Komponen Terdampak:** payload `product-modifier-group.json`, database design Bagian 5, halaman modifier frontend

## Ringkasan

Database design Bagian 4.4 dan Bagian 5 menyatakan konsistensi `min_select <= max_select` tidak didukung CHECK SDF dan ditegakkan di layer RDF/aplikasi. Payload `product-modifier-group.json` tidak memuat aturan itu, dan module hasil generate tidak memeriksanya. Satu-satunya pemeriksaan ada di `js/modifiers.js`, yang mudah dilewati dengan memanggil API langsung.

## Fakta yang Ditemukan

| Sumber | Pernyataan atau kondisi |
|--------|-------------------------|
| [Database design M-001, 4.4 dan Bagian 5](../05-database-design/M-001-produk-kategori.md) | "Konsistensi `min_select <= max_select` antar-field ... ditegakkan di layer RDF/aplikasi" |
| [payload/product-modifier-group.json](../../src/pos-server/payload/product-modifier-group.json) | `min_select` dan `max_select` hanya memuat `default` dan `required`; tidak ada aturan silang antar-field |
| [js/modifiers.js](../../src/pos-frontend-integrasi/apps/pos/js/modifiers.js) | `VALIDATION_RULES` memeriksa `max_select >= min_select` dan `min_select >= 1` untuk grup wajib |

Aturan tambahan di frontend, yaitu grup wajib harus memiliki `min_select` minimal 1 (BR-008), juga tidak ada di backend maupun di database design.

## Dampak

Grup modifier dengan `min_select` lebih besar dari `max_select`, atau grup wajib dengan `min_select` nol, dapat tersimpan lewat API. Validasi order pada M-002 (BR-008) kemudian tidak mungkin dipenuhi untuk grup tersebut.

## Rekomendasi Tindak Lanjut

1. Tambahkan aturan silang antar-field pada payload bila katalog field validation platform mendukungnya. Bila tidak, dokumentasikan bahwa penegakan berada di frontend dan modul Order, lalu ubah Bagian 5 database design agar tidak menyatakan penegakan di RDF.
2. Tambahkan aturan "grup wajib mensyaratkan `min_select >= 1`" ke database design Bagian 5 agar sejalan dengan yang sudah dipasang di frontend.

## File Terdampak

- `docs/05-database-design/M-001-produk-kategori.md` (4.4 dan Bagian 5)
- `src/pos-server/payload/product-modifier-group.json`
- `src/pos-frontend-integrasi/apps/pos/js/modifiers.js`

## Penyelesaian

Keputusan: penegakan dinyatakan hanya di frontend (rekomendasi pertama, cabang kedua). Payload dan modul backend tidak diubah.

Sebelum memutuskan, kemampuan platform diverifikasi lewat katalog runtime yang terpasang di `pos-server`, bukan hanya dari dokumen fitur:

| Mekanisme | Hasil verifikasi |
|-----------|------------------|
| `fieldValidation` RDF | Constraint lintas field hanya `before` dan `after`, dan keduanya terbatas pada tipe `date`, `datetime`, `timestamp`. Tipe `integer` hanya menerima `min`, `max`, `positive`, `negative`, `precision`, `scale`, `integer` dengan nilai literal |
| CHECK pada SDF | Operasi `in`, `eq`, `neq`, `gt`, `gte`, `lt`, `lte` hanya membandingkan satu kolom dengan nilai skalar |
| Component engine (`onBeforeInsert`/`onBeforeUpdate`) | Secara teknis dapat menolak record lewat handler JavaScript, tetapi kegagalan handler dibalas HTTP 500 dan belum ada payload di proyek ini yang memakai mekanisme tersebut. Tidak dipilih agar M-001 tetap bebas dari handler custom |

| File | Perubahan |
|------|-----------|
| [Database design M-001](../05-database-design/M-001-produk-kategori.md) v1.7 | Catatan 4.4 tidak lagi menyatakan penegakan di RDF; Bagian 5 memuat dua baris BR-008 (`min_select <= max_select` dan `min_select >= 1` pada grup wajib) dengan penegakan di `js/modifiers.js` beserta alasan keterbatasan platform |

Konsekuensi yang diterima: nilai yang melanggar masih dapat tersimpan lewat pemanggilan API langsung. Pengaman berikutnya berada pada validasi order M-002, yang harus menangani grup dengan kombinasi nilai tidak konsisten tanpa gagal.
