# Issue #02: FR-011 Menyebut Penyesuaian Harga Varian Absolut atau Selisih, Schema Hanya Mendukung Selisih

**Status:** Selesai (2026-09-08)
**Severity:** Medium (kebutuhan fungsional tidak sepenuhnya diwujudkan schema)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian PRD dan schema M-001
**Komponen Terdampak:** PRD M-001 (FR-011, AC-010), schema `product_variant`, halaman varian frontend

## Ringkasan

FR-011 menyatakan setiap opsi varian memiliki penyesuaian harga "absolut atau selisih terhadap harga dasar". Tabel `product_variant` hanya menyediakan kolom `price_adjustment` yang dimaknai sebagai selisih, tanpa penanda mode. Frontend mengikuti schema: label harga varian ditulis dengan awalan tanda tambah, dan modal detail produk menampilkan nilainya sebagai tambahan terhadap harga dasar.

## Fakta yang Ditemukan

| Sumber | Pernyataan |
|--------|------------|
| [PRD M-001, FR-011](../03-product-requirements/M-001-produk-kategori.md) | "penyesuaian harga (absolut atau selisih terhadap harga dasar)" |
| [Database design M-001, 4.3](../05-database-design/M-001-produk-kategori.md) | Hanya `price_adjustment: 'decimal:15,2 notnull default:0'`; catatan menyebutnya penyesuaian terhadap harga dasar |
| [schema/product_variant.js](../../src/pos-server/schema/product_variant.js) | Identik dengan database design |
| [js/variants.js](../../src/pos-frontend-integrasi/apps/pos/js/variants.js) | `adjustmentLabel()` memberi tanda tambah pada nilai di atas nol, dibaca sebagai selisih |

Contoh data pada database design dan AC-010 ("Besar +3.000") hanya memakai mode selisih. Mode absolut tidak muncul di contoh mana pun.

## Dampak

Harga varian yang lebih murah dari harga dasar tidak dapat dinyatakan, karena `price_adjustment` dijaga tidak negatif oleh CHECK constraint (BR-004) dan tidak ada mode absolut sebagai alternatif.

## Rekomendasi Tindak Lanjut

1. Bila mode absolut memang dibutuhkan: tambahkan kolom penanda mode (misalnya `price_mode` bernilai `adjustment` atau `absolute`) pada schema, database design, payload, dan form varian, lalu sesuaikan FR-014 tentang perhitungan harga akhir.
2. Bila cukup mode selisih: ubah FR-011 menjadi "selisih terhadap harga dasar" saja agar PRD tidak menjanjikan kemampuan yang tidak ada.

## File Terdampak

- `docs/03-product-requirements/M-001-produk-kategori.md`
- `docs/05-database-design/M-001-produk-kategori.md`
- `src/pos-server/schema/product_variant.js`
- `src/pos-server/payload/product-variant.json`
- `src/pos-frontend-integrasi/apps/pos/js/variants.js`

## Penyelesaian

Keputusan: mode absolut ditambahkan (rekomendasi pertama), dengan ketentuan tambahan bahwa harga varian selalu sama atau lebih mahal dari harga dasar item.

| File | Perubahan |
|------|-----------|
| [PRD M-001](../03-product-requirements/M-001-produk-kategori.md) v1.4 | FR-011 menegaskan dua mode harga (selisih dan absolut); FR-014 menyebut cara hitung per mode; BR-012 (harga varian tidak di bawah harga dasar), AC-017, dan AC-018 ditambahkan; AC-010 ditandai mode selisih |
| [PRD induk](../03-product-requirements/README.md) v1.2 | Rentang ID M-001 menjadi BR-001–012 dan AC-001–018 |
| [Database design M-001](../05-database-design/M-001-produk-kategori.md) v1.6 | Kolom `price_mode` dengan CHECK `in ('adjustment', 'absolute')`; catatan dan contoh data 4.3; Bagian 5 memuat penegakan BR-012 di client |
| `schema/product_variant.js`, `payload/product-variant.json`, `payload/query/product-variant-datatables.sql` | Kolom `price_mode` (`string:10 notnull default:'adjustment'`), validasi `enum` pada payload, kolom ikut pada query datatables; kolom ditambahkan ke database lewat `schema apply` dan endpoint `product-variant` dibuat ulang |
| `variants.html`, `js/variants.js` | Select Price Mode pada form tambah dan ubah, teks bantuan mengikuti mode, label harga mode absolut tanpa tanda tambah, aturan BR-012 memakai `base_price` produk yang dimuat lewat `Api.readAll('product')` |
| `js/items.js` | Modal detail produk menampilkan harga varian sesuai mode |
| `docs/04-langkah-kerja/09-varian-modifier.md` (frontend) | Bagian Mode Harga Varian dan baris aturan BR-012 |

Nama kolom `price_adjustment` dipertahankan meski pada mode absolut isinya harga jual opsi, karena mengganti nama kolom berarti drop dan add pada database serta menyentuh data varian yang sudah ada. Aturan BR-012 hanya ditegakkan di client karena CHECK constraint tidak dapat membandingkan kolom lintas tabel dan `fieldValidation` RDF hanya menilai field pada record yang sama; pola keterbatasannya sama dengan [issue #04](issue-04-min-select-max-select-hanya-divalidasi-frontend.md).

CHECK `in ('adjustment', 'absolute')` pada `price_mode` sudah ada di SDF dan database design, tetapi belum ada di database: `schema apply` menangguhkan perubahan CHECK constraint (hanya menerapkan `ADD COLUMN`). Pembatasan nilai mode saat ini bertumpu pada validasi `enum` di payload (HTTP 400). CHECK di database akan terpasang saat tabel dibuat ulang lewat `schema migrate`, atau dapat ditambahkan manual dengan `ALTER TABLE product_variant ADD CONSTRAINT chk_product_variant_price_mode CHECK (price_mode IN ('adjustment', 'absolute'))`.
