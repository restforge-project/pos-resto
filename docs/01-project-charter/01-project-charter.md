# PROJECT CHARTER
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **Nama Proyek** | POS Rumah Makan (Point of Sale System untuk Restoran) |
| **Versi Dokumen** | 1.1 |
| **Tanggal Dibuat** | 2026-06-05 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

---

## 1. Latar Belakang (Background)

Banyak rumah makan dan restoran kecil–menengah masih mencatat pesanan secara manual atau memakai aplikasi kasir yang terlalu mahal dan rumit untuk kebutuhan mereka. Akibatnya sering terjadi salah catat order, antrian dapur tidak teratur, stok bahan baku sulit dipantau, dan laporan penjualan tidak rapi.

POS Rumah Makan dibangun sebagai produk perangkat lunak (SaaS) yang dapat dijual ke banyak rumah makan. Fokusnya adalah alur kasir yang cepat dan sederhana, andal saat koneksi internet tidak stabil (offline-first), dengan harga dan kompleksitas yang sesuai untuk usaha kuliner skala kecil hingga menengah.

---

## 2. Tujuan Proyek (Project Objectives)

Tujuan dirumuskan secara ringkas dan terukur:

| Kode | Tujuan | Indikator Keberhasilan |
|------|--------|------------------------|
| OBJ-1 | Mempercepat proses order dan pembayaran | Satu transaksi (order → bayar) dapat diselesaikan di bawah 60 detik |
| OBJ-2 | Menyediakan POS yang cepat dan andal saat online | Operasi order → bayar berjalan lancar pada koneksi internet yang stabil |
| OBJ-3 | Memberi pemilik visibilitas penjualan harian | Laporan penjualan & produk terlaris tersedia real-time |
| OBJ-4 | Merilis MVP yang dapat dipakai pelanggan pertama | MVP (alur kasir inti) selesai dan dipakai minimal 1 rumah makan |

> **Catatan revisi (2026-06-06):** Dukungan **offline-first ditunda** ke fase lanjutan karena framework backend (RESTForge) belum mendukung mode offline. Fokus MVP saat ini adalah operasi **online** dengan satu rumah makan pilot (single-tenant). Modul M-012 (Integrasi & Sinkronisasi Offline) tetap berada pada rencana pengembangan berikutnya.

---

## 3. Ruang Lingkup (Scope)

### 3.1 Termasuk dalam Lingkup (In Scope)

MVP berfokus pada alur kasir inti, kemudian diperluas bertahap. Modul mengacu pada [00-document-index.md](../00-document-index/00-document-index.md):

**Prioritas MVP (Must Have):**

- M-001 Manajemen Produk & Kategori
- M-002 Manajemen Order (Kasir & Pelayan)
- M-005 Pembayaran & Split Bill
- M-009 Manajemen Karyawan & Shift (login & buka/tutup kasir)
- M-010 Laporan & Analitik Penjualan (laporan dasar)

**Pengembangan Berikutnya (Should/Could Have):**

- M-003 Manajemen Meja & Reservasi
- M-004 Kitchen Display System (KDS)
- M-006 Manajemen Inventori Bahan Baku
- M-007 Diskon, Promo & Voucher
- M-008 Manajemen Pelanggan & Loyalitas
- M-011 Pengaturan & Multi-Outlet
- M-012 Integrasi & Sinkronisasi Offline

### 3.2 Di Luar Lingkup (Out of Scope)

Untuk menjaga proyek tetap fokus, hal berikut tidak dikerjakan pada tahap awal:

- Modul akuntansi/pembukuan lengkap (jurnal, neraca, pajak badan)
- Manajemen penggajian (payroll) karyawan
- Aplikasi pemesanan untuk pelanggan akhir (customer-facing ordering app)
- Integrasi aggregator pihak ketiga (GoFood, GrabFood, dll) — dipertimbangkan setelah MVP
- Hardware/perangkat kasir — sistem berjalan di perangkat yang sudah dimiliki pelanggan

---

## 4. Pemangku Kepentingan (Stakeholders)

| Peran | Tanggung Jawab |
|-------|----------------|
| Project Lead | Arah produk, prioritas, keputusan akhir |
| Developer | Pembangunan fitur (frontend & backend) |
| QA / Tester | Pengujian fungsional dan kualitas rilis |
| Pelanggan Awal (Pilot) | Rumah makan pertama yang mencoba dan memberi feedback |

> Catatan: pada tim kecil, satu orang dapat memegang lebih dari satu peran.

**Pengguna akhir produk:** Kasir, Pelayan, Staf Dapur, dan Pemilik/Manajer Rumah Makan.

---

## 5. Tahapan & Target Waktu (Milestones)

Penjadwalan dibuat sederhana berbasis target MVP. Detail urutan modul akan dirinci di [02-product-roadmap.md](../02-product-roadmap.md).

| Tahap | Fokus | Target |
|-------|-------|--------|
| M1 | Setup proyek, produk & order dasar | [DISESUAIKAN] |
| M2 | Pembayaran + shift kasir + laporan dasar (MVP siap) | [DISESUAIKAN] |
| M3 | Uji coba dengan rumah makan pilot & perbaikan | [DISESUAIKAN] |
| M4 | Penambahan modul lanjutan (meja, dapur, inventori) | [DISESUAIKAN] |

---

## 6. Asumsi & Batasan (Assumptions & Constraints)

**Asumsi:**

- Pelanggan memiliki perangkat (tablet/PC/HP) dan printer struk dasar.
- Koneksi internet tersedia dan cukup stabil di lokasi pemakaian; dukungan offline ditunda ke fase lanjutan (lihat catatan pada OBJ-2).
- Tim kecil bekerja iteratif; fitur dirilis bertahap, bukan sekaligus.

**Batasan:**

- Sumber daya tim terbatas, sehingga lingkup MVP harus dijaga tetap ramping.
- Anggaran dan timeline mengutamakan rilis MVP yang dapat dipakai lebih dulu.

---

## 7. Risiko Utama (Key Risks)

| Risiko | Dampak | Penanganan |
|--------|--------|------------|
| Scope membengkak (fitur terus bertambah) | Rilis MVP molor | Patuhi batas In/Out of Scope; fitur baru masuk backlog |
| Ketergantungan penuh pada koneksi internet (belum ada mode offline) | Operasional kasir terganggu saat internet putus | Pastikan koneksi/backup internet memadai di lokasi; jadwalkan offline-first setelah RESTForge mendukung |
| Adopsi pengguna rendah (kasir sulit memakai) | Produk tidak terpakai | Utamakan UI sederhana; libatkan pelanggan pilot lebih awal |

---

## 8. Kriteria Sukses (Success Criteria)

Proyek dianggap berhasil pada tahap awal apabila:

- MVP (modul Must Have) selesai dan stabil dipakai operasional harian.
- Minimal satu rumah makan pilot menggunakan sistem secara nyata.
- Alur order → bayar dapat diselesaikan dengan cepat dan tanpa error berarti.
- Operasional harian berjalan stabil pada koneksi online (dukungan offline menyusul di fase lanjutan).

---

## Riwayat Perubahan (Changelog)

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 1.0 | 2026-06-05 | Pembuatan awal project charter POS Rumah Makan | Project Lead |
| 1.1 | 2026-06-06 | Offline-first ditunda (RESTForge belum mendukung); fokus MVP online, single-tenant pilot. OBJ-2, asumsi, risiko, dan kriteria sukses disesuaikan | Project Lead |

---

*Dokumen ini adalah source of truth untuk visi, tujuan, dan ruang lingkup proyek POS Rumah Makan. Perubahan lingkup harus melalui persetujuan Project Lead dan dicatat pada changelog.*
