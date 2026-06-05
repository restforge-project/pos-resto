# POS Rumah Makan (pos-resto)

Point of Sale System untuk restoran, rumah makan, dan warung makan skala kecil hingga menengah.

---

## Ikhtisar (Overview)

POS Rumah Makan adalah sistem Point of Sale terintegrasi yang menangani seluruh alur layanan restoran, mulai dari pemesanan di meja maupun kasir, pengiriman order ke dapur, pembayaran multi-metode, hingga pencatatan stok bahan baku dan pelaporan penjualan.

Produk dikembangkan sebagai perangkat lunak SaaS dengan fokus pada alur kasir yang cepat dan sederhana, serta harga dan kompleksitas yang sesuai untuk usaha kuliner skala kecil hingga menengah.

**Batasan MVP saat ini:**

- Mode operasi **online-only**. Dukungan offline-first ditunda ke fase lanjutan.
- **Single-tenant**, melayani satu rumah makan pilot terlebih dahulu.

## Status Pengembangan (Development Status)

Project berada pada fase perencanaan dan perancangan. Dokumentasi disusun mengikuti tahapan SDLC sebelum implementasi dimulai.

| Fase | Status |
|------|--------|
| Perencanaan (Project Charter, Document Index) | ✓ Draft |
| Analisis Kebutuhan (PRD, modul M-001) | ✓ Draft |
| Perancangan (Technical Specification) | ✓ Draft (berjalan) |
| Implementasi | ☐ Belum dimulai |

## Daftar Modul (Module Registry)

Sistem terdiri dari 12 modul yang dibangun secara bertahap.

**Prioritas MVP (Must Have):**

| ID | Modul |
|----|-------|
| M-001 | Manajemen Menu & Kategori |
| M-002 | Manajemen Order (Kasir & Pelayan) |
| M-005 | Pembayaran & Split Bill |
| M-009 | Manajemen Karyawan & Shift |
| M-010 | Laporan & Analitik Penjualan (laporan dasar) |

**Pengembangan Berikutnya (Should/Could Have):**

| ID | Modul |
|----|-------|
| M-003 | Manajemen Meja & Reservasi |
| M-004 | Kitchen Display System (KDS) |
| M-006 | Manajemen Inventori Bahan Baku |
| M-007 | Diskon, Promo & Voucher |
| M-008 | Manajemen Pelanggan & Loyalitas |
| M-011 | Pengaturan & Multi-Outlet |
| M-012 | Integrasi & Sinkronisasi Offline |

## Technology Stack

| Komponen | Teknologi |
|----------|-----------|
| Backend | RESTForge (definition-first framework, Node.js) |
| Frontend | Admin template berbasis Bootstrap 5 + jQuery |

Detail arsitektur dijelaskan pada [docs/04-technical-specification.md](docs/04-technical-specification.md).

## Struktur Repository (Repository Structure)

```
pos-resto/
├── docs/        # Dokumentasi project (SDLC: charter, PRD, spesifikasi teknis)
├── src/         # Source code aplikasi (belum dimulai)
└── README.md
```

## Dokumentasi (Documentation)

Seluruh dokumen disusun mengikuti tahapan SDLC dengan penomoran berurutan. Titik masuk navigasi dokumentasi adalah indeks dokumen.

| Dokumen | Deskripsi |
|---------|-----------|
| [00-document-index.md](docs/00-document-index.md) | Indeks dan peta navigasi seluruh dokumen |
| [01-project-charter.md](docs/01-project-charter.md) | Visi, tujuan, dan ruang lingkup project |
| [03-product-requirements.md](docs/03-product-requirements.md) | Kebutuhan fungsional dan non-fungsional |
| [04-technical-specification.md](docs/04-technical-specification.md) | Arsitektur sistem dan technology stack |

Urutan baca yang disarankan untuk pembaca baru: `01 Project Charter` kemudian `03 PRD` kemudian `04 Technical Specification`.
