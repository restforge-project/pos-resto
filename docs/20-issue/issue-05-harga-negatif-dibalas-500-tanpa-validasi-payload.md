# Issue #05: Harga Negatif Ditolak Lewat CHECK Constraint yang Dibalas 500, Bukan Validasi Payload

**Status:** Open
**Severity:** Medium (FR-006 terpenuhi secara teknis, tetapi lewat error server generik)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian PRD, payload, dan dokumen kontrak endpoint frontend
**Komponen Terdampak:** payload `product.json`, `product-variant.json`, `product-modifier-option.json`

## Ringkasan

FR-006 dan BR-004 mewajibkan sistem menolak harga negatif. Di backend penolakan itu hanya berasal dari CHECK constraint `gte: 0` pada schema. Dokumen kontrak endpoint frontend mencatat bahwa pelanggaran CHECK dibalas 500 dengan pesan "An error occurred while adding ...", tanpa menyebut kolom yang bermasalah. Payload ketiga resource tidak memakai `constraints.min: 0`, padahal platform yang terpasang mendukung constraint tersebut dan dapat membalas 400 dengan rincian per field.

## Fakta yang Ditemukan

| Sumber | Kondisi |
|--------|---------|
| [PRD M-001, FR-006 dan BR-004](../03-product-requirements/M-001-produk-kategori.md) | Sistem harus memvalidasi harga >= 0 dan menolak input negatif |
| [payload/product.json](../../src/pos-server/payload/product.json) | `base_price` hanya memuat `precision`, `default`, `required` |
| [payload/product-variant.json](../../src/pos-server/payload/product-variant.json) | `price_adjustment` sama, tanpa batas bawah |
| [payload/product-modifier-option.json](../../src/pos-server/payload/product-modifier-option.json) | `extra_price` sama, tanpa batas bawah |
| [docs/03-kontrak-endpoint.md](../../src/pos-frontend-integrasi/docs/03-kontrak-endpoint.md) | Pelanggaran CHECK dibalas 500 generik; nilai negatif ditahan validasi client lebih dulu |
| Platform `@restforgejs/platform` terpasang | Referensi `constraints.min` dan `constraints.max` ada pada kode validator |

Database design Bagian 5 tidak mencantumkan batas harga sebagai aturan yang perlu ditegakkan di layer RDF, sehingga penulis payload tidak diarahkan untuk menambahkannya.

## Dampak

1. Konsumen API selain frontend menerima 500 untuk kesalahan input biasa, bertentangan dengan AC-006 yang mengharapkan pesan validasi harga.
2. Log server mencatat kesalahan input pengguna sebagai error internal.

## Rekomendasi Tindak Lanjut

1. Tambahkan `min: 0` pada constraint `base_price`, `price_adjustment`, dan `extra_price` di tiga payload tersebut, lalu jalankan ulang pembuatan endpoint.
2. Catat aturan ini pada database design Bagian 5 sebagai penegakan ganda: CHECK di database dan `min` di RDF.
3. Perbarui catatan pada `03-kontrak-endpoint.md` setelah response berubah menjadi 400.

## File Terdampak

- `src/pos-server/payload/product.json`
- `src/pos-server/payload/product-variant.json`
- `src/pos-server/payload/product-modifier-option.json`
- `docs/05-database-design/M-001-produk-kategori.md` (Bagian 5)
- `src/pos-frontend-integrasi/docs/03-kontrak-endpoint.md`
