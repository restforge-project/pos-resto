# INDEKS DOKUMEN
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Versi Dokumen** | 1.2 |
| **Tanggal Dibuat** | 2026-06-05 |
| **Terakhir Diperbarui** | 2026-06-05 |
| **PIC** | Project Lead |
| **Status** | Draft |

---

## 1. Deskripsi Project

POS Rumah Makan adalah sistem Point of Sale terintegrasi untuk operasional restoran, rumah makan, dan warung makan. Sistem ini menangani seluruh alur layanan, mulai dari pemesanan di meja maupun kasir, pengiriman order ke dapur, pembayaran multi-metode, hingga pencatatan stok bahan baku dan pelaporan penjualan. Platform dirancang untuk mendukung operasi harian yang cepat dan andal, baik saat online maupun offline (offline-first), serta dapat berjalan pada perangkat kasir, tablet pelayan, dan layar dapur (Kitchen Display).

Pembangunan dilakukan secara bertahap dalam beberapa fase dengan total 12 modul, sehingga setiap fase menghasilkan produk yang dapat langsung digunakan (MVP). Fase awal berfokus pada alur kasir inti (produk, order, pembayaran), kemudian diperluas dengan manajemen meja, dapur, inventori bahan baku, hingga loyalitas pelanggan dan analitik penjualan.

---

## 2. Daftar Dokumen

Seluruh dokumen project disusun mengikuti tahapan SDLC (Software Development Life Cycle). Penomoran dokumen mencerminkan urutan fase pengembangan.

### 2.1 Fase Perencanaan

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 00 | [00-document-index.md](00-document-index.md) | Indeks Dokumen (Document Index) | 1.2 | ✓ Draft | Project Lead |
| 01 | [01-project-charter.md](../01-project-charter/01-project-charter.md) | Project Charter | 1.0 | ✓ Draft | Project Lead |
| 02 | [02-product-roadmap.md](../02-product-roadmap.md) | Product Roadmap | — | ☐ Belum Dibuat | [PIC] |

### 2.2 Fase Analisis Kebutuhan

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 03 | [03-product-requirements/](../03-product-requirements/README.md) | Product Requirements Document (folder) | 1.0 | ✓ Draft | Project Lead |

> **Catatan PRD:** Dokumen 03 berupa **folder** `03-product-requirements/` dengan `README.md` sebagai induk (pendahuluan, konvensi ID, NFR, indeks modul). **Skema tiap modul** ada pada berkas terpisah `03-product-requirements/M-NNN-<nama-modul>.md`. Modul M-001 sudah selesai; M-002 s.d. M-012 menyusul.

### 2.3 Fase Perancangan

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 04 | [04-technical-specification/](../04-technical-specification/README.md) | Technical Specification (folder) | 0.6 | ✓ Draft (sebagian) | Project Lead |
| 05 | [05-database-design/](../05-database-design/README.md) | Database Design (folder) | 0.1 | ✓ Draft | Project Lead |
| 06 | [06-api-specification.md](../06-api-specification.md) | API Specification | — | ☐ Belum Dibuat | [PIC] |
| 07 | [07-traceability-matrix.md](../07-traceability-matrix.md) | Traceability Matrix | — | ☐ Belum Dibuat | [PIC] |

> **Catatan Database Design:** Dokumen 05 berupa **folder** `05-database-design/` dengan `README.md` sebagai index (konvensi, kolom audit, tipe data, daftar modul). Skema tiap modul ada pada berkas terpisah `M-NNN-<nama-modul>.md` di dalam folder tersebut.

### 2.4 Fase Implementasi

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 08 | [08-feature-specification.md](../08-feature-specification.md) | Feature Specification | — | ☐ Belum Dibuat | [PIC] |

> **Catatan:** File feature specification dapat dipecah per modul atau fitur kompleks, misalnya: `08-feature-order-management.md`, `08-feature-payment.md`, `08-feature-kitchen-display.md`, dsb.

### 2.5 Fase Pengujian dan Penyebaran

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 09 | [09-test-plan.md](../09-test-plan.md) | Test Plan | — | ☐ Belum Dibuat | [PIC] |
| 10 | [10-deployment-guide.md](../10-deployment-guide.md) | Deployment Guide | — | ☐ Belum Dibuat | [PIC] |

### 2.6 Referensi

| No | File | Judul | Versi | Status | PIC |
|----|------|-------|-------|--------|-----|
| 11 | [11-glossary.md](../11-glossary.md) | Glossary & Data Dictionary | — | ☐ Belum Dibuat | [PIC] |

### 2.7 Ringkasan Status

| Status | Jumlah |
|--------|--------|
| ✓ Draft | 3 dokumen (00, 01, 03) |
| ☐ Belum Dibuat | 9 dokumen (02, 04–11) |
| **Total** | **12 dokumen** |

> Catatan: berkas PRD per modul (`03-product-requirements/M-NNN-...`) adalah turunan dari dokumen 03 dan tidak dihitung sebagai dokumen inti tersendiri; daftarnya ada di dalam README induk folder 03.

---

## 3. Peta Hubungan Antar Dokumen

Diagram berikut menunjukkan hubungan ketergantungan (dependency) dan aliran informasi antar dokumen. Setiap dokumen berfungsi sebagai rujukan tunggal untuk domain spesifik yang dicakupnya.

### 3.1 Diagram Dependensi

```
┌─────────────────────────────────────────────────────────────────┐
│                    FASE PERENCANAAN (PLANNING)                   │
│                                                                  │
│  ┌──────────────────────┐  ┌──────────────────────────────────┐ │
│  │  01 Project Charter  │  │  02 Product Roadmap              │ │
│  │  (Visi & Lingkup)    │  │  (Strategi Pembangunan Bertahap) │ │
│  └──────────┬───────────┘  └──────────────────────────────────┘ │
│             │                                                    │
├─────────────┼────────────────────────────────────────────────────┤
│             FASE ANALISIS KEBUTUHAN (REQUIREMENTS)               │
│             │                                                    │
│             v                                                    │
│  ┌──────────────────────┐                                        │
│  │       03 PRD         │                                        │
│  │ (Kebutuhan Produk)   │                                        │
│  └──────────┬───────────┘                                        │
│             │                                                    │
├─────────────┼────────────────────────────────────────────────────┤
│             FASE PERANCANGAN (DESIGN)                            │
│             │                                                    │
│  ┌──────────┼──────────────┐                                     │
│  v          v              v                                     │
│  ┌────────────┐ ┌───────────┐ ┌──────────────┐                  │
│  │ 04 Tech    │ │ 05 DB     │ │ 06 API       │                  │
│  │ Spec       │ │ Design    │ │ Spec         │                  │
│  └─────┬──────┘ └─────┬─────┘ └──────┬───────┘                  │
│        │              │              │                           │
│        └──────┬───────┴──────┬───────┘                           │
│               │              │                                   │
│     ┌─────────v──────┐  ┌───v───────────────┐                    │
│     │ 07 Traceability│  │ 08 Feature Spec   │                    │
│     └────────────────┘  └─────────┬─────────┘                    │
│                                   │                              │
├───────────────────────────────────┼──────────────────────────────┤
│        FASE PENGUJIAN & PENYEBARAN (TESTING & DEPLOYMENT)        │
│                                   │                              │
│              ┌────────────────────┼──────────┐                   │
│              v                               v                   │
│     ┌──────────────┐              ┌──────────────────┐           │
│     │ 09 Test Plan │              │ 10 Deployment    │           │
│     │ (Pengujian)  │              │    Guide         │           │
│     └──────────────┘              └──────────────────┘           │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘

        Dokumen Referensi (selalu dirujuk oleh semua dokumen):
        ┌────────────────────────────────────┐
        │  11 Glossary & Data Dictionary     │
        │  (Terminologi & Kamus Data)        │
        └────────────────────────────────────┘
```

### 3.2 Tabel Rujukan Tunggal

Setiap domain informasi memiliki satu dokumen otoritatif sebagai rujukan tunggal. Jika terjadi konflik informasi antar dokumen, dokumen rujukan tunggal yang berlaku.

| Domain Informasi | Rujukan Tunggal | Dokumen Konsumen |
|------------------|-----------------|-------------------|
| Visi, misi, dan lingkup project | [01-project-charter.md](../01-project-charter/01-project-charter.md) | Seluruh dokumen |
| Strategi pembangunan bertahap (phasing) | [02-product-roadmap.md](../02-product-roadmap.md) | 01, 03, 04, 08 |
| Kebutuhan fungsional dan non-fungsional | [03-product-requirements/](../03-product-requirements/README.md) | 04, 05, 06, 07, 09 |
| Arsitektur sistem dan technology stack | [04-technical-specification/](../04-technical-specification/README.md) | 05, 06, 08, 10 |
| Skema database dan relasi entitas | [05-database-design.md](../05-database-design.md) | 06, 07, 08, 09, 10 |
| Kontrak API (endpoint, request, response) | [06-api-specification.md](../06-api-specification.md) | 07, 08, 09, 10 |
| Keterlacakan requirement ke implementasi | [07-traceability-matrix.md](../07-traceability-matrix.md) | — |
| Spesifikasi fitur per modul | [08-feature-specification.md](../08-feature-specification.md) | 09 |
| Skenario dan kriteria pengujian | [09-test-plan.md](../09-test-plan.md) | 10 |
| Prosedur deployment dan konfigurasi | [10-deployment-guide.md](../10-deployment-guide.md) | — |
| Definisi istilah dan kamus data | [11-glossary.md](../11-glossary.md) | Seluruh dokumen |

---

## 4. Daftar Modul

Sistem POS Rumah Makan terdiri dari 12 modul yang dibangun secara bertahap. Detail cakupan dan urutan pembangunan diatur dalam [02-product-roadmap.md](../02-product-roadmap.md).

| ID | Modul | Ringkasan |
|----|-------|-----------|
| M-001 | Manajemen Produk & Kategori | Pengelolaan daftar produk, kategori, harga, varian, dan modifier (level pedas, topping, dll) |
| M-002 | Manajemen Order (Kasir & Pelayan) | Pembuatan dan perubahan order dari kasir maupun tablet pelayan |
| M-003 | Manajemen Meja & Reservasi | Denah meja, status meja, penggabungan/pemindahan order, reservasi |
| M-004 | Kitchen Display System (KDS) | Tampilan order untuk dapur, status pengerjaan, antrian masak |
| M-005 | Pembayaran & Split Bill | Pembayaran tunai, kartu, QRIS, e-wallet, split bill, pajak & service charge |
| M-006 | Manajemen Inventori Bahan Baku | Stok bahan baku, resep/BOM, pengurangan stok otomatis saat penjualan |
| M-007 | Diskon, Promo & Voucher | Aturan diskon, promo waktu tertentu, voucher, dan paket bundling |
| M-008 | Manajemen Pelanggan & Loyalitas | Data pelanggan, poin loyalitas, dan riwayat transaksi |
| M-009 | Manajemen Karyawan & Shift | Akun karyawan, peran/hak akses, jadwal shift, dan buka/tutup kasir |
| M-010 | Laporan & Analitik Penjualan | Laporan penjualan, produk terlaris, performa shift, dan margin |
| M-011 | Pengaturan & Multi-Outlet | Konfigurasi outlet, struk, perangkat, dan dukungan multi-cabang |
| M-012 | Integrasi & Sinkronisasi Offline | Sinkronisasi offline-first, printer dapur/kasir, dan integrasi pihak ketiga |

---

## 5. Panduan Pembacaan Dokumen

Urutan pembacaan dokumen disesuaikan dengan peran dan kebutuhan pembaca. Berikut adalah jalur baca yang disarankan berdasarkan peran.

### 5.1 Untuk Seluruh Peran (Pembacaan Umum)

Pembacaan wajib yang berlaku untuk semua peran sebelum menelusuri dokumen spesifik:

```
01 Project Charter  -->  02 Product Roadmap  -->  03 PRD  -->  11 Glossary
```

### 5.2 Untuk Product Owner / Business Analyst

```
01 Project Charter
 └─> 02 Product Roadmap
      └─> 03 PRD
           └─> 07 Traceability Matrix
                └─> 09 Test Plan (bagian acceptance criteria)
```

### 5.3 Untuk Technical Lead / Architect

```
01 Project Charter
 └─> 02 Product Roadmap
      └─> 03 PRD
           ├─> 04 Technical Specification
           ├─> 05 Database Design
           └─> 06 API Specification
```

### 5.4 Untuk Developer

```
03 PRD (ringkasan kebutuhan)
 └─> 04 Technical Specification
      ├─> 05 Database Design
      ├─> 06 API Specification
      └─> 08 Feature Specification
           └─> 11 Glossary (referensi istilah)
```

### 5.5 Untuk QA Engineer

```
03 PRD (kebutuhan dan acceptance criteria)
 └─> 06 API Specification
      └─> 07 Traceability Matrix
           └─> 09 Test Plan
```

### 5.6 Untuk DevOps / Infrastructure

```
04 Technical Specification (arsitektur dan infrastructure)
 └─> 05 Database Design (migrasi skema)
      └─> 10 Deployment Guide
```

### 5.7 Untuk Pemilik / Manajer Restoran

```
01 Project Charter (visi dan scope)
 └─> 02 Product Roadmap (tahapan pembangunan)
      └─> 03 PRD (fitur yang tersedia)
```

---

## 6. Konvensi Penulisan Dokumen

Seluruh dokumen dalam project ini mengikuti konvensi berikut:

| Aspek | Ketentuan |
|-------|-----------|
| Bahasa utama | Bahasa Indonesia baku dengan istilah teknis IT dalam bentuk asli |
| Heading non-teknis | Format bilingual: `## Indonesia (English)` |
| Istilah teknis IT | Bentuk asli: file, database, API, server, deploy, framework, library |
| Placeholder | `[NAMA_PROJECT]`, `[TANGGAL]`, `[NAMA_PIC]`, `[DISESUAIKAN]` |
| Format tanggal | `YYYY-MM-DD` |
| ID artefak | `US-NNN` (User Story), `FR-NNN` (Functional Req), `NFR-NNN` (Non-Functional), `F-NNN` (Feature), `TC-NNN` (Test Case) |
| ID modul | `M-NNN` sesuai product roadmap (M-001 s.d. M-012) |
| Prioritas | `Must Have`, `Should Have`, `Could Have` |
| Status dokumen | `Draft`, `In Review`, `Approved`, `Final` |
| Referensi silang | Markdown link: `[nama-file.md](nama-file.md)` |
| Diagram | ASCII art dalam code block atau Mermaid |
| Code block | Sertakan language tag: ` ```sql `, ` ```json `, ` ```bash ` |

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 1.0 | 2026-06-05 | Pembuatan awal indeks dokumen POS Rumah Makan dengan 11 entri dokumen dan 12 modul | Project Lead |
| 1.1 | 2026-06-05 | Penomoran dokumen dibuat berurutan (00–11), menghapus penomoran 01a | Project Lead |
| 1.2 | 2026-06-05 | Status 01-project-charter.md diperbarui menjadi Draft v1.0 | Project Lead |
| 1.3 | 2026-06-06 | Dokumen 03 (PRD) dibuat dan dipecah per modul; M-001 selesai (file induk + berkas modul) | Project Lead |

---

*Dokumen ini merupakan indeks dan peta navigasi untuk seluruh dokumentasi project POS Rumah Makan. Dokumen diperbarui setiap kali ada penambahan, penghapusan, atau perubahan status dokumen.*
