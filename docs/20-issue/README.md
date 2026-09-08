# DAFTAR ISSUE
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Dokumen** | 20 — Daftar Issue (folder) |
| **Versi Dokumen** | 0.4 |
| **Tanggal Dibuat** | 2026-09-08 |
| **Terakhir Diperbarui** | 2026-09-08 |
| **PIC** | Project Lead |
| **Status** | Aktif |

> Folder ini menampung issue hasil pemeriksaan kesesuaian antara dokumen dan implementasi. Setiap issue berada pada satu file `issue-NN-<slug>.md` dengan bagian tetap: Ringkasan, Fakta yang Ditemukan, Dampak, Rekomendasi Tindak Lanjut, dan File Terdampak. Status pada tiap file diperbarui saat issue ditangani, lalu baris pada tabel di bawah disesuaikan.

---

## 1. Sumber Pemeriksaan

Issue #01 sampai #11 berasal dari pemeriksaan tanggal 2026-09-08 terhadap empat sumber untuk modul M-001:

| Sumber | Lokasi |
|--------|--------|
| PRD M-001 | [03-product-requirements/M-001-produk-kategori.md](../03-product-requirements/M-001-produk-kategori.md) |
| Database design M-001 | [05-database-design/M-001-produk-kategori.md](../05-database-design/M-001-produk-kategori.md) |
| Schema dan payload backend | `src/pos-server/schema/`, `src/pos-server/payload/` |
| Frontend kategori, produk, varian, modifier | `src/pos-frontend-integrasi/apps/pos/` beserta dokumen di `src/pos-frontend-integrasi/docs/` |

Kelima file SDF di `src/pos-server/schema/` identik dengan definisi pada database design, dan payload RDF selaras dengan schema. Ketidaksesuaian yang ditemukan berada pada relasi PRD dengan schema, dokumen dengan konfigurasi dan perilaku aktual, serta frontend dengan schema dan acceptance criteria.

## 2. Daftar Issue

| No | File | Judul | Severity | Status |
|----|------|-------|----------|--------|
| 01 | [issue-01](issue-01-product-code-wajib-tidak-ada-di-prd.md) | `product_code` wajib dan unik pada implementasi, tidak ada di PRD | Medium | Selesai |
| 02 | [issue-02](issue-02-mode-harga-varian-absolut-tidak-didukung-schema.md) | FR-011 menyebut harga varian absolut atau selisih, schema hanya selisih | Medium | Selesai |
| 03 | [issue-03](issue-03-keunikan-nama-kategori-case-insensitive-belum-ditegakkan.md) | Keunikan nama kategori case-insensitive (BR-001) belum ditegakkan | High | Open |
| 04 | [issue-04](issue-04-min-select-max-select-hanya-divalidasi-frontend.md) | Aturan `min_select <= max_select` hanya ditegakkan di frontend | Medium | Selesai |
| 05 | [issue-05](issue-05-harga-negatif-dibalas-500-tanpa-validasi-payload.md) | Harga negatif dibalas 500 lewat CHECK constraint, bukan validasi payload | Medium | Open |
| 06 | [issue-06](issue-06-database-design-klaim-file-terhapus-saat-delete.md) | Database design menyatakan file terhapus saat `/delete`, perilaku aktual sebaliknya | Medium | Open |
| 07 | [issue-07](issue-07-status-redis-dan-cleanup-job-bertentangan-di-tiga-sumber.md) | Status Redis dan cleanup job bertentangan di tiga sumber | Medium | Open |
| 08 | [issue-08](issue-08-hapus-produk-cascade-varian-modifier-tanpa-peringatan.md) | Hapus produk menghapus varian dan modifier secara cascade tanpa peringatan | High | Open |
| 09 | [issue-09](issue-09-accordion-variations-add-ons-pada-form-produk-tidak-berfungsi.md) | Accordion Variations dan Add Ons pada form produk tidak berfungsi | Medium | Open |
| 10 | [issue-10](issue-10-pesan-tolak-hapus-kategori-tanpa-saran-ac-003.md) | Penolakan hapus kategori tanpa saran tindakan (AC-003) | Medium | Open |
| 11 | [issue-11](issue-11-halaman-kategori-tidak-bisa-urut-berdasarkan-sort-order.md) | Halaman kategori tidak menyediakan pengurutan berdasarkan `sort_order` | Low | Open |

## 3. Pengelompokan

| Kelompok | Issue |
|----------|-------|
| PRD belum selaras dengan schema | 01, 02 |
| Aturan bisnis hanya ditegakkan di frontend atau belum sama sekali | 03, 04, 05 |
| Dokumen tertinggal dari konfigurasi dan perilaku aktual | 06, 07 |
| Frontend menyimpang dari schema dan acceptance criteria | 08, 09, 10, 11 |

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 0.1 | 2026-09-08 | Folder dibuat; issue #01 sampai #11 dari pemeriksaan kesesuaian M-001 didaftarkan | Project Lead |
| 0.2 | 2026-09-08 | Issue #01 selesai: kode produk ditambahkan ke PRD M-001 v1.3 | Project Lead |
| 0.3 | 2026-09-08 | Issue #02 selesai: mode harga absolut ditambahkan pada schema, payload, form varian, dan PRD M-001 v1.4 | Project Lead |
| 0.4 | 2026-09-08 | Issue #04 selesai: penegakan `min_select <= max_select` dinyatakan hanya di frontend pada database design M-001 v1.7 | Project Lead |
