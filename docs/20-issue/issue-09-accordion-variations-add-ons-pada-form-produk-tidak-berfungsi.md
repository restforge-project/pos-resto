# Issue #09: Accordion "Variations" dan "Add Ons" pada Form Produk Tidak Berfungsi

**Status:** Open
**Severity:** Medium (elemen form yang tampak dapat diisi tetapi tidak disimpan)
**Ditemukan:** 2026-09-08 saat pemeriksaan halaman produk frontend
**Komponen Terdampak:** `items.html` (form tambah dan ubah produk)

## Ringkasan

Form tambah dan ubah produk pada `items.html` masih memuat accordion "Variations" dan "Add Ons" bawaan template, lengkap dengan input Group, Option, dan Price Adjustment. Input di dalamnya tidak memiliki atribut `data-field`, sehingga `collectForm()` melewatkannya dan tidak ada yang dikirim ke backend. Saat membuka form ubah, accordion juga tidak diisi dari data varian atau modifier yang ada. Pengelolaan varian dan modifier yang sebenarnya berada di halaman `variants.html` dan `modifiers.html`.

## Fakta yang Ditemukan

| Lokasi | Kondisi |
|--------|---------|
| [items.html](../../src/pos-frontend-integrasi/apps/pos/items.html), form `add-item-form` | Accordion `addVariantAccordion` dan `addModifierAccordion` berisi input tanpa `data-field` |
| [items.html](../../src/pos-frontend-integrasi/apps/pos/items.html), form `edit-item-form` | Accordion `editVariantAccordion` dan `editModifierAccordion` dengan kondisi sama |
| Komentar HTML pada form tambah | "Accordion Variations dan Add Ons dipertahankan dari template ... sengaja tidak ikut dikirim collectForm()" |
| [js/common.js](../../src/pos-frontend-integrasi/apps/pos/js/common.js), `collectForm()` | Hanya membaca elemen ber-`data-field` |
| [js/items.js](../../src/pos-frontend-integrasi/apps/pos/js/items.js), `fillEditForm()` | Tidak menyentuh accordion |
| [layout/sidebar.html](../../src/pos-frontend-integrasi/apps/pos/layout/sidebar.html) | Menu "Variants" dan "Modifiers" sudah tersedia sebagai halaman terpisah |

## Dampak

1. Pengguna mengisi varian atau add-on pada form produk, menekan Save, dan tidak menerima pesan apa pun bahwa isian itu diabaikan.
2. Form produk memiliki dua sumber kebenaran visual untuk varian: accordion yang mati dan halaman varian yang aktif.

## Rekomendasi Tindak Lanjut

Pilih salah satu:

1. Hapus kedua accordion dari form tambah dan ubah, lalu tambahkan tautan "Kelola varian" dan "Kelola modifier" yang membuka halaman terkait dengan filter produk yang sedang dibuka.
2. Atau fungsikan accordion sebagai pengelolaan bertingkat: kirim baris varian dan modifier ke resource masing-masing setelah produk tersimpan, dan isi accordion dari data yang ada saat form ubah dibuka.

## File Terdampak

- `src/pos-frontend-integrasi/apps/pos/items.html`
- `src/pos-frontend-integrasi/apps/pos/js/items.js` (bila opsi 2 dipilih)
