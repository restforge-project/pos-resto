# PRODUCT REQUIREMENTS DOCUMENT (PRD) — INDUK
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Versi Dokumen** | 1.0 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

> **Struktur dokumen:** PRD ini dipecah per modul untuk menjaga tiap berkas tetap ringkas dan mudah di-review. **File induk ini** memuat bagian bersama (pendahuluan, konvensi ID artefak, Non-Functional Requirement lintas modul, dan indeks modul). **Detail kebutuhan tiap modul** berada pada berkas terpisah `M-NNN-<nama-modul>.md` yang ditautkan pada Bagian 2.

---

## 1. Pendahuluan (Introduction)

Dokumen ini adalah **rujukan tunggal** untuk kebutuhan fungsional dan non-fungsional produk POS Rumah Makan. Dokumen menerjemahkan visi dan ruang lingkup pada [01-project-charter.md](../01-project-charter.md) menjadi kebutuhan yang dapat dirancang, dibangun, dan diuji. Dokumen ini menjadi rujukan utama bagi Technical Specification (04), Database Design (05), API Specification (06), Traceability Matrix (07), dan Test Plan (09).

### 1.1 Tujuan Dokumen (Purpose)

Mendefinisikan **apa** yang harus dilakukan sistem (bukan **bagaimana** membangunnya), dalam bentuk User Story dan Functional Requirement yang tertelusur, sehingga setiap kebutuhan dapat diverifikasi melalui kriteria penerimaan (acceptance criteria).

### 1.2 Cakupan Dokumen (Scope)

Dokumen menjabarkan kebutuhan untuk 12 modul (M-001 s.d. M-012) sesuai [00-document-index.md](../00-document-index.md). Penulisan dilakukan bertahap per modul; setiap modul didokumentasikan pada berkasnya sendiri dan didaftarkan pada indeks Bagian 2.

### 1.3 Konvensi & ID Artefak (Conventions)

Mengikuti konvensi pada [00-document-index.md](../00-document-index.md):

| ID | Arti | Format |
|----|------|--------|
| US-NNN | User Story | `US-001`, `US-002`, … |
| FR-NNN | Functional Requirement | `FR-001`, `FR-002`, … |
| NFR-NNN | Non-Functional Requirement | `NFR-001`, … |
| BR-NNN | Business Rule | `BR-001`, … |
| AC-NNN | Acceptance Criteria | `AC-001`, … |

Prioritas memakai skala: **Must Have**, **Should Have**, **Could Have**.

> **Catatan penomoran:** ID artefak (US/FR/BR/AC) bersifat **unik dan berurutan lintas seluruh berkas modul**, bukan diulang per modul. Rentang ID yang dipakai tiap modul dicatat pada kolom "Rentang ID" di indeks Bagian 2, agar tidak terjadi duplikasi saat dirujuk dokumen lain.

---

## 2. Indeks Modul (Module Index)

Setiap modul memiliki berkas PRD tersendiri. Detail User Story, Functional Requirement, Business Rule, dan Acceptance Criteria berada di berkas masing-masing.

| ID | Modul | Berkas PRD | Prioritas | Rentang ID | Status |
|----|-------|-----------|-----------|-----------|--------|
| M-001 | Manajemen Produk & Kategori | [M-001-produk-kategori.md](M-001-produk-kategori.md) | Must Have | US-001–012 · FR-001–016 · BR-001–010 · AC-001–015 | ✓ Draft lengkap |
| M-002 | Manajemen Order (Kasir & Pelayan) | *(belum dibuat)* | Must Have | — | ☐ Belum |
| M-003 | Manajemen Meja & Reservasi | *(belum dibuat)* | Should Have | — | ☐ Belum |
| M-004 | Kitchen Display System (KDS) | *(belum dibuat)* | Should Have | — | ☐ Belum |
| M-005 | Pembayaran & Split Bill | *(belum dibuat)* | Must Have | — | ☐ Belum |
| M-006 | Manajemen Inventori Bahan Baku | *(belum dibuat)* | Could Have | — | ☐ Belum |
| M-007 | Diskon, Promo & Voucher | *(belum dibuat)* | Could Have | — | ☐ Belum |
| M-008 | Manajemen Pelanggan & Loyalitas | *(belum dibuat)* | Could Have | — | ☐ Belum |
| M-009 | Manajemen Karyawan & Shift | *(belum dibuat)* | Must Have | — | ☐ Belum |
| M-010 | Laporan & Analitik Penjualan | *(belum dibuat)* | Must Have | — | ☐ Belum |
| M-011 | Pengaturan & Multi-Outlet | *(belum dibuat)* | Could Have | — | ☐ Belum |
| M-012 | Integrasi & Sinkronisasi Offline | *(belum dibuat)* | Could Have | — | ☐ Belum |

> **Cara menambah modul baru:** buat berkas `M-NNN-<nama-modul>.md`, lanjutkan penomoran artefak dari ID terakhir yang terpakai (lihat kolom "Rentang ID"), lalu perbarui baris modul tersebut pada tabel di atas.

---

## 3. Non-Functional Requirements (NFR-NNN)

NFR berlaku **lintas modul** dan menjadi acuan kualitas bagi seluruh berkas modul. Bagian ini akan dirinci setelah modul-modul fungsional inti didefinisikan, dan dirujuk dari [04-technical-specification.md](../04-technical-specification/README.md).

| ID | Kategori | Kebutuhan Non-Fungsional | Status |
|----|----------|--------------------------|--------|
| NFR-001 | Performa | *(akan diisi — mis. waktu respons pencarian produk, pemrosesan order)* | ☐ Draft |
| NFR-002 | Ketahanan Offline | *(akan diisi — operasi tetap berjalan tanpa internet, sinkron otomatis)* | ☐ Draft |
| NFR-003 | Keamanan & Hak Akses | *(akan diisi — autentikasi, peran/hak akses)* | ☐ Draft |
| NFR-004 | Keterpakaian (Usability) | *(akan diisi — kemudahan UI kasir)* | ☐ Draft |

> Bagian NFR sengaja ditempatkan di file induk agar tidak diulang pada tiap berkas modul. Modul cukup merujuk ID NFR yang relevan.

---

## Riwayat Perubahan (Changelog)

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 0.1 | 2026-06-06 | Pembuatan kerangka PRD dan Ringkasan Modul M-001 | Project Lead |
| 0.2 | 2026-06-06 | Penambahan User Stories M-001 (US-001 s.d. US-012) | Project Lead |
| 0.3 | 2026-06-06 | Penambahan Functional Requirements M-001 (FR-001 s.d. FR-016) | Project Lead |
| 0.4 | 2026-06-06 | Penambahan Business Rules M-001 (BR-001 s.d. BR-010) | Project Lead |
| 0.5 | 2026-06-06 | Penambahan Acceptance Criteria M-001 (AC-001 s.d. AC-015); revisi bahasa hasil review | Project Lead |
| 1.0 | 2026-06-06 | PRD dipecah per modul: file induk berisi pendahuluan, konvensi, NFR, dan indeks modul; isi M-001 dipindah ke berkas modul terpisah | Project Lead |
