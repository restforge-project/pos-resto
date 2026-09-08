# Issue #07: Status Redis dan Cleanup Job Orphan File Bertentangan di Tiga Sumber

**Status:** Open
**Severity:** Medium (status operasional fitur upload tidak dapat dipastikan dari dokumen)
**Ditemukan:** 2026-09-08 saat pemeriksaan kesesuaian database design, konfigurasi server, dan dokumen integrasi frontend
**Komponen Terdampak:** database design M-001 Bagian 6, `config/db-connection.env`, dokumen frontend `06-batasan.md` dan `08-upload-foto.md`

## Ringkasan

Tiga sumber menyatakan kondisi Redis dan cleanup job yang berbeda. Database design mencatat `REDIS_PASSWORD` masih kosong sehingga orphan file tracking dan cleanup job belum aktif. File konfigurasi server saat ini sudah mengisi `REDIS_PASSWORD`, tetapi `JOB_ENABLED` bernilai `false`. Dokumen integrasi frontend menyatakan cleanup job berjalan tiap enam jam dan menghapus orphan setelah 24 jam. Belum ada bukti verifikasi ulang setelah `REDIS_PASSWORD` diisi.

## Fakta yang Ditemukan

| Sumber | Pernyataan atau kondisi |
|--------|-------------------------|
| [Database design M-001, Bagian 6, status verifikasi 2026-08-30](../05-database-design/M-001-produk-kategori.md) | Redis `localhost:6380` belum dapat dipakai karena `REDIS_PASSWORD` kosong (`NOAUTH Authentication required`); orphan tracking dan cleanup job belum aktif |
| [config/db-connection.env](../../src/pos-server/config/db-connection.env) | `REDIS_PASSWORD` sudah terisi; `JOB_ENABLED=false`; `STORAGE_ENABLED=true`, `STORAGE_PROVIDER=s3` |
| [docs/06-batasan.md](../../src/pos-frontend-integrasi/docs/06-batasan.md) | "Cleanup job backend menghapusnya setelah 24 jam, dijadwalkan tiap enam jam" |
| [docs/04-langkah-kerja/08-upload-foto.md](../../src/pos-frontend-integrasi/docs/04-langkah-kerja/08-upload-foto.md) | Orphan "ditangani cleanup job backend yang berjalan tiap enam jam" |

Hal yang belum diverifikasi:

- Apakah koneksi Redis kini berhasil setelah password diisi.
- Apakah cleanup job orphan file bergantung pada `JOB_ENABLED` atau berjalan lewat timer internal handler upload. Kode platform yang terpasang terobfuskasi sehingga tidak dapat dipastikan lewat pembacaan source.

## Dampak

1. File yang diunggah lalu tidak disimpan (pengguna menutup modal tanpa Save) berpotensi menumpuk di bucket tanpa ada yang membersihkan, sementara dokumen frontend menjanjikan pembersihan otomatis.
2. Status verifikasi pada database design sudah tidak mencerminkan konfigurasi, sehingga pembaca tidak tahu langkah mana yang masih terbuka.

## Rekomendasi Tindak Lanjut

1. Verifikasi ulang koneksi Redis dengan konfigurasi saat ini, lalu catat hasilnya pada Bagian 6 database design dengan tanggal baru.
2. Pastikan lewat log server apakah cleanup job orphan file terdaftar saat start dengan `JOB_ENABLED=false`. Bila tidak, aktifkan `JOB_ENABLED=true` atau catat bahwa pembersihan belum berjalan.
3. Samakan pernyataan pada `06-batasan.md` dan `08-upload-foto.md` dengan hasil verifikasi. Jadwal "tiap enam jam" dan ambang "24 jam" perlu dirujuk ke konfigurasi atau dokumentasi platform, bukan ditulis tanpa sumber.

## File Terdampak

- `docs/05-database-design/M-001-produk-kategori.md` (Bagian 6)
- `src/pos-server/config/db-connection.env`
- `src/pos-frontend-integrasi/docs/06-batasan.md`
- `src/pos-frontend-integrasi/docs/04-langkah-kerja/08-upload-foto.md`
