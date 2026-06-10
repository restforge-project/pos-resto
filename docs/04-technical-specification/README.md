# TECHNICAL SPECIFICATION — INDEX
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Dokumen** | 04 — Technical Specification (folder) |
| **Versi Dokumen** | 0.6 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

> **Struktur dokumen:** Technical Specification dipecah **per tema** di dalam folder ini. **File ini (README)** memuat Pendahuluan + indeks bagian. **Bagian tematik** berada pada berkas terpisah (lihat Bagian 2 — Daftar Bagian).

---

## 1. Pendahuluan

### 1.1 Tujuan Dokumen

Dokumen ini adalah **rujukan tunggal** untuk arsitektur sistem dan *technology stack* POS Rumah Makan. Dokumen menerjemahkan kebutuhan pada [03-product-requirements](../03-product-requirements/README.md) menjadi keputusan teknis: bagaimana sistem disusun, teknologi apa yang dipakai, bagaimana komponen saling berkomunikasi, serta bagaimana aspek keamanan, keandalan, dan deployment dipenuhi. Dokumen ini menjadi acuan bagi Database Design (05), API Specification (06), Feature Specification (08), dan Deployment Guide (10).

Dokumen ini menjelaskan **bagaimana** sistem dibangun pada level arsitektur, bukan kebutuhan bisnisnya (lihat 03) dan bukan detail skema tabel (lihat 05) atau kontrak endpoint (lihat 06).

### 1.2 Ruang Lingkup

Lingkup dokumen mencakup arsitektur untuk **MVP** POS Rumah Makan dengan batasan yang telah disepakati:

- **Mode operasi:** online-only. Dukungan offline-first **ditunda** ke fase lanjutan. Lihat catatan revisi pada [01-project-charter](../01-project-charter/01-project-charter.md) (OBJ-2).
- **Tenancy:** single-tenant — melayani satu rumah makan pilot terlebih dahulu. Desain multi-tenant dipertimbangkan setelah validasi pilot.
- **Cakupan modul:** keputusan arsitektur bersifat lintas modul, namun contoh konkret pada dokumen ini menggunakan **M-001 Manajemen Produk & Kategori** sebagai rujukan implementasi pertama.

Hal yang **berada di luar** dokumen ini: skema tabel rinci (05), kontrak request/response per endpoint (06), prosedur deployment langkah demi langkah (10), dan rencana pengujian (09).

### 1.3 Acuan & Dependensi

Arsitektur ini terikat pada dua referensi teknologi yang telah ditetapkan sebagai **batasan tetap (hard constraint)**:

| Referensi | Peran | Lokasi |
|-----------|-------|--------|
| **RESTForge** | Framework backend wajib (definition-first, Node.js) | `https://github.com/restforge/handbook` |
| **Design Template** | Template UI frontend wajib (admin Bootstrap 5 + jQuery) | `\src\design-template` |

Dokumen rujukan terkait:

| Dokumen | Hubungan |
|---------|----------|
| [01-project-charter](../01-project-charter/01-project-charter.md) | Sumber visi, lingkup, dan batasan (online-only, single-tenant) |
| [03-product-requirements](../03-product-requirements/README.md) | Sumber kebutuhan fungsional & non-fungsional yang diwujudkan arsitektur ini |
| [05-database-design](../05-database-design/README.md) | Konsumen: skema tabel diturunkan dari arsitektur data di sini |
| [06-api-specification](../06-api-specification.md) | Konsumen: kontrak endpoint mengikuti pola RESTForge yang dijelaskan di sini |

### 1.4 Pembaca yang Dituju

Technical Lead/Architect (pemilik dokumen), Developer backend & frontend (acuan implementasi), QA Engineer (memahami batas sistem), dan DevOps (acuan infrastruktur & deployment).

---

## 2. Daftar Bagian

Isi teknis dipecah per tema pada berkas berikut. Penomoran Bagian (1–10) tetap berurutan lintas berkas.

| Bagian | Tema | Berkas | Status |
|--------|------|--------|--------|
| 1 | Pendahuluan | README.md (file ini) | ✓ |
| 2–4 | Ikhtisar Arsitektur · Technology Stack · Arsitektur Backend | [arsitektur.md](arsitektur.md) | ✓ Draft |
| 5 | Autentikasi & Otorisasi | [autentikasi.md](autentikasi.md) | ☐ Belum |
| 6–7 | Arsitektur Frontend · Integrasi Frontend–Backend | [frontend-integrasi.md](frontend-integrasi.md) | ☐ Belum |
| 8–9 | Reliability & Infrastruktur · Deployment & Lingkungan | [reliability-deployment.md](reliability-deployment.md) | ☐ Belum |
| 10 | Keputusan Arsitektur & Trade-off | [keputusan.md](keputusan.md) | ☐ Belum |

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 0.1 | 2026-06-06 | Pembuatan kerangka dokumen dan Bagian 1 (Pendahuluan) | Project Lead |
| 0.2 | 2026-06-06 | Penambahan Bagian 2 (Ikhtisar Arsitektur) | Project Lead |
| 0.3 | 2026-06-06 | Revisi Bagian 2 sesuai implementasi nyata RESTForge (baseline tanpa reverse proxy; auth via Auth service) | Project Lead |
| 0.4 | 2026-06-06 | Penambahan Bagian 3 (Technology Stack) | Project Lead |
| 0.5 | 2026-06-06 | Penambahan Bagian 4 (Arsitektur Backend) | Project Lead |
| 0.6 | 2026-06-06 | Dokumen 04 diubah menjadi folder per tema: README (Pendahuluan + index) + berkas tematik | Project Lead |
