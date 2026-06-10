# PRD MODUL — M-001 Manajemen Produk & Kategori
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **ID Modul** | M-001 |
| **Nama Modul** | Manajemen Produk & Kategori |
| **Bagian dari** | [03-product-requirements.md](README.md) (PRD induk) |
| **Versi Dokumen** | 1.0 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

> Dokumen ini adalah bagian dari Product Requirements Document POS Rumah Makan. Bagian bersama — pendahuluan, konvensi ID artefak, dan Non-Functional Requirement lintas modul — berada pada file induk [03-product-requirements.md](README.md). Penomoran artefak (US/FR/BR/AC) bersifat unik lintas modul; rentang ID modul ini dicatat pada indeks file induk.

---

## 1. Ringkasan Modul

Modul Manajemen Produk & Kategori adalah fondasi data produk bagi seluruh alur transaksi POS. Modul ini mengelola katalog item yang dijual rumah makan: daftar produk, pengelompokan ke dalam kategori, penetapan harga, serta varian dan modifier (misalnya level pedas, pilihan topping, ukuran porsi). Data yang dikelola di sini menjadi sumber bagi modul Order (M-002), Pembayaran (M-005), dan Inventori (M-006), sehingga akurasi dan kerapian data produk sangat menentukan kelancaran operasional.

| Atribut | Keterangan |
|---|---|
| **ID Modul** | M-001 |
| **Nama** | Manajemen Produk & Kategori |
| **Prioritas** | Must Have (bagian dari MVP) |
| **Pengguna Utama** | Pemilik/Manajer Rumah Makan (pengelola data), Kasir & Pelayan (pengguna katalog saat order) |
| **Tujuan Bisnis** | Menyediakan katalog produk yang akurat, terstruktur, dan mudah diperbarui sebagai dasar seluruh transaksi |
| **Modul Terkait** | M-002 (Order) menggunakan katalog; M-005 (Pembayaran) memakai harga; M-006 (Inventori) menautkan resep/BOM ke produk; M-007 (Promo) merujuk item & kategori |

**Cakupan modul:**

Pengelolaan kategori produk (buat, ubah, urutkan, aktif/nonaktif); pengelolaan item produk beserta harga, deskripsi, dan foto; pengelolaan varian (mis. ukuran) dan modifier (mis. topping, level pedas) beserta penyesuaian harganya; serta status ketersediaan produk (tersedia/habis).

**Di luar cakupan modul:**

Pengurangan stok bahan baku otomatis dan definisi resep/BOM dikelola pada M-006 Manajemen Inventori. Aturan diskon dan promo dikelola pada M-007. Modul M-001 hanya menyediakan referensi item & kategori yang dipakai modul-modul tersebut.

---

## 2. User Stories (US-NNN)

User story menggambarkan kebutuhan dari sudut pandang pengguna dalam format: *Sebagai [peran], saya ingin [tujuan], agar [manfaat]*. Setiap story diberi prioritas dan menjadi dasar penurunan Functional Requirement pada bagian berikutnya.

| ID | Peran | User Story | Prioritas |
|----|-------|------------|-----------|
| US-001 | Pemilik/Manajer | Sebagai pemilik, saya ingin membuat dan mengubah **kategori produk** (mis. Makanan, Minuman, Cemilan), agar daftar produk tersusun rapi dan mudah ditemukan kasir. | Must Have |
| US-002 | Pemilik/Manajer | Sebagai pemilik, saya ingin **mengatur urutan tampil kategori**, agar kategori yang paling sering dipakai muncul lebih dulu di layar kasir. | Should Have |
| US-003 | Pemilik/Manajer | Sebagai pemilik, saya ingin menambah, mengubah, dan menghapus **item produk** beserta nama, deskripsi, dan kategorinya, agar katalog selalu mencerminkan produk yang benar-benar dijual. | Must Have |
| US-004 | Pemilik/Manajer | Sebagai pemilik, saya ingin menetapkan **harga jual** untuk setiap item produk, agar kasir menagih harga yang konsisten dan benar. | Must Have |
| US-005 | Pemilik/Manajer | Sebagai pemilik, saya ingin menambahkan **foto** pada item produk, agar kasir/pelayan lebih cepat dan akurat memilih produk saat order. | Could Have |
| US-006 | Pemilik/Manajer | Sebagai pemilik, saya ingin mendefinisikan **varian** sebuah item (mis. ukuran Kecil/Sedang/Besar) dengan penyesuaian harga, agar satu item dapat dijual dalam beberapa pilihan tanpa membuat banyak item terpisah. | Should Have |
| US-007 | Pemilik/Manajer | Sebagai pemilik, saya ingin mendefinisikan **modifier** (mis. level pedas, tambahan topping) beserta tambahan harganya, agar pelanggan bisa menyesuaikan pesanan dan harga ikut menyesuaikan secara otomatis. | Should Have |
| US-008 | Pemilik/Manajer | Sebagai pemilik, saya ingin menandai modifier sebagai **wajib atau opsional** dan membatasi jumlah pilihannya, agar input order konsisten (mis. level pedas wajib dipilih satu). | Should Have |
| US-009 | Pemilik/Manajer | Sebagai pemilik, saya ingin menandai item produk sebagai **tersedia atau habis (sold out)**, agar kasir tidak menjual produk yang stoknya kosong. | Must Have |
| US-010 | Pemilik/Manajer | Sebagai pemilik, saya ingin **menonaktifkan** item atau kategori tanpa menghapusnya, agar data historis penjualan tetap utuh sementara item disembunyikan dari layar order. | Should Have |
| US-011 | Kasir/Pelayan | Sebagai kasir, saya ingin **mencari dan memfilter** produk berdasarkan nama atau kategori, agar saya bisa memilih item dengan cepat saat melayani pelanggan. | Must Have |
| US-012 | Kasir/Pelayan | Sebagai kasir, saya ingin melihat **hanya produk yang tersedia** beserta varian dan modifier-nya saat membuat order, agar tidak salah menjual produk yang habis atau nonaktif. | Must Have |

> **Catatan keterlacakan:** Setiap user story di atas diturunkan menjadi satu atau lebih Functional Requirement (FR) pada Bagian 3, dan dipetakan ke Acceptance Criteria pada Bagian 5. Pemetaan lengkap dirangkum di [07-traceability-matrix.md](../07-traceability-matrix.md).

---

## 3. Functional Requirements (FR-NNN)

Functional Requirement menyatakan kemampuan konkret yang harus disediakan sistem. Setiap FR ditautkan ke User Story sumbernya dan menjadi acuan langsung bagi desain database, API, serta pengujian.

**A. Manajemen Kategori**

| ID | Kebutuhan Fungsional | Sumber | Prioritas |
|----|----------------------|--------|-----------|
| FR-001 | Sistem harus memungkinkan pengguna **membuat kategori** dengan atribut: nama (wajib, unik), deskripsi (opsional), dan status aktif. | US-001 | Must Have |
| FR-002 | Sistem harus memungkinkan pengguna **mengubah dan menghapus** kategori. Penghapusan ditolak jika masih ada item produk yang tertaut pada kategori tersebut (lihat Bagian 4). | US-001 | Must Have |
| FR-003 | Sistem harus memungkinkan pengguna **menetapkan urutan tampil** kategori (mis. melalui nomor urut atau drag-and-drop) dan menyimpannya. | US-002 | Should Have |
| FR-004 | Sistem harus memungkinkan pengguna **mengaktifkan/menonaktifkan** kategori. Kategori nonaktif tidak ditampilkan pada layar order namun datanya tetap tersimpan. | US-010 | Should Have |

**B. Manajemen Item Produk**

| ID | Kebutuhan Fungsional | Sumber | Prioritas |
|----|----------------------|--------|-----------|
| FR-005 | Sistem harus memungkinkan pengguna **membuat item produk** dengan atribut: nama (wajib), kategori (wajib, satu kategori), deskripsi (opsional), dan harga jual (wajib). | US-003, US-004 | Must Have |
| FR-006 | Sistem harus memvalidasi bahwa **harga jual** berupa angka ≥ 0 dan menolak input tidak valid (negatif/non-numerik). | US-004 | Must Have |
| FR-007 | Sistem harus memungkinkan pengguna **mengubah dan menghapus** item produk. Item yang masih direferensikan oleh transaksi tidak dapat dihapus; sistem menolak penghapusan tersebut (lihat Bagian 4). Penonaktifan item (`is_active`) adalah fungsi terpisah, bukan pengganti penghapusan. | US-003 | Must Have |
| FR-008 | Sistem harus memungkinkan pengguna **mengunggah satu foto** per item produk dengan batas format dan ukuran tertentu, serta menampilkannya pada layar order. | US-005 | Could Have |
| FR-009 | Sistem harus memungkinkan pengguna **mengaktifkan/menonaktifkan** item produk tanpa menghapus data historisnya. | US-010 | Should Have |
| FR-010 | Sistem harus memungkinkan pengguna **menandai item sebagai "Tersedia" atau "Habis (Sold Out)"**. Item berstatus Habis tetap terlihat di pengelolaan namun tidak dapat dipilih saat membuat order. | US-009, US-012 | Must Have |

**C. Varian & Modifier**

| ID | Kebutuhan Fungsional | Sumber | Prioritas |
|----|----------------------|--------|-----------|
| FR-011 | Sistem harus memungkinkan pengguna mendefinisikan **varian** untuk sebuah item (mis. ukuran) dengan daftar opsi, di mana setiap opsi memiliki **penyesuaian harga** (absolut atau selisih terhadap harga dasar). | US-006 | Should Have |
| FR-012 | Sistem harus memungkinkan pengguna mendefinisikan **grup modifier** (mis. Topping, Level Pedas) berisi beberapa opsi, di mana setiap opsi dapat memiliki **tambahan harga** (≥ 0). | US-007 | Should Have |
| FR-013 | Sistem harus memungkinkan pengguna menetapkan grup modifier sebagai **wajib atau opsional**, serta menetapkan **batas minimum dan maksimum** jumlah opsi yang dapat dipilih. | US-008 | Should Have |
| FR-014 | Sistem harus **menghitung harga akhir item** secara otomatis = harga dasar/varian + total tambahan modifier terpilih, dan menyediakannya bagi modul Order (M-002). | US-007 | Should Have |

**D. Tampilan untuk Order (Konsumsi Katalog)**

| ID | Kebutuhan Fungsional | Sumber | Prioritas |
|----|----------------------|--------|-----------|
| FR-015 | Sistem harus menyediakan **pencarian item** berdasarkan nama (pencocokan sebagian, tidak peka huruf besar/kecil) dan **filter berdasarkan kategori**. | US-011 | Must Have |
| FR-016 | Sistem harus menampilkan **hanya item dan kategori yang aktif dan tersedia** beserta varian/modifier terkait kepada kasir/pelayan saat membuat order. | US-012 | Must Have |

> **Catatan:** Non-Functional Requirement (NFR) yang berlaku lintas modul (performa pencarian, ketahanan offline, validasi data) dirangkum pada file induk [03-product-requirements.md](README.md) dan dirujuk dari [04-technical-specification.md](../04-technical-specification/README.md).

---

## 4. Business Rules (BR-NNN)

Business Rule adalah ketentuan bisnis yang harus selalu dipenuhi sistem, terlepas dari antarmuka atau alur yang dipakai. Aturan ini menjaga integritas data produk dan mencegah kondisi yang merusak transaksi.

| ID | Aturan Bisnis | Terkait FR |
|----|---------------|------------|
| BR-001 | **Nama kategori harus unik** (tidak peka huruf besar/kecil) dalam satu outlet. Sistem menolak pembuatan/perubahan kategori dengan nama yang sudah ada. | FR-001 |
| BR-002 | **Kategori tidak dapat dihapus** selama masih ada item produk yang tertaut padanya. Pengguna harus memindahkan atau menghapus item tersebut terlebih dahulu, atau cukup menonaktifkan kategori. | FR-002, FR-004 |
| BR-003 | **Setiap item produk wajib tertaut tepat pada satu kategori.** Item tidak boleh berada tanpa kategori. | FR-005 |
| BR-004 | **Harga jual item, penyesuaian harga varian, dan tambahan harga modifier tidak boleh bernilai negatif.** Harga akhir hasil perhitungan juga tidak boleh negatif. | FR-006, FR-011, FR-012, FR-014 |
| BR-005 | **Item yang masih direferensikan oleh transaksi tidak dapat dihapus.** Sistem menolak penghapusan demi menjaga konsistensi laporan & riwayat penjualan. Penonaktifan (`is_active = false`) adalah fungsi terpisah untuk menyembunyikan item dari penjualan — **bukan** mekanisme penghapusan. | FR-007, FR-009 |
| BR-006 | **Item atau kategori berstatus nonaktif/habis tidak boleh muncul pada layar pembuatan order**, namun tetap tampil pada modul pengelolaan produk. | FR-004, FR-010, FR-016 |
| BR-007 | **Perubahan harga hanya berlaku untuk transaksi baru.** Order yang sudah dibuat sebelum perubahan tetap memakai harga yang berlaku saat order dibuat (harga dikunci pada saat transaksi). | FR-006, FR-014 |
| BR-008 | **Grup modifier wajib** mengharuskan minimal satu opsi dipilih saat order; jumlah pilihan harus mematuhi batas minimum dan maksimum yang ditetapkan. | FR-013 |
| BR-009 | **Penghapusan grup modifier atau varian** yang masih tertaut ke item aktif harus memberi peringatan dan tidak boleh memutus integritas item tersebut (opsi: tolak hapus atau lepas tautan secara eksplisit). | FR-011, FR-012 |
| BR-010 | **Status "Habis (Sold Out)" bersifat manual pada M-001.** Pengosongan/pengisian stok otomatis berbasis bahan baku diatur oleh M-006 dan, bila aktif, dapat menimpa status ketersediaan item. | FR-010 |

> **Catatan integrasi:** BR-007 (penguncian harga saat transaksi) dan BR-010 (sumber status ketersediaan) berada di perbatasan dengan M-002 dan M-006. Detail mekanismenya akan diselaraskan pada [04-technical-specification.md](../04-technical-specification/README.md) dan spesifikasi modul terkait.

---

## 5. Acceptance Criteria (AC-NNN)

Acceptance Criteria menetapkan kondisi yang harus terpenuhi agar sebuah kebutuhan dinyatakan selesai dan dapat diuji. Kriteria ditulis dalam format **Given–When–Then** (Diberikan–Ketika–Maka) agar langsung dapat diturunkan menjadi test case pada [09-test-plan.md](../09-test-plan.md).

**Manajemen Kategori**

| ID | Skenario (Given–When–Then) | Verifikasi FR |
|----|----------------------------|---------------|
| AC-001 | **Diberikan** sistem belum memiliki kategori bernama "Minuman", **ketika** pengguna membuat kategori "Minuman", **maka** kategori tersimpan dan muncul di daftar kategori. | FR-001 |
| AC-002 | **Diberikan** kategori "Minuman" sudah terdaftar, **ketika** pengguna membuat kategori dengan nama "minuman", **maka** sistem menolak dan menampilkan pesan nama kategori sudah dipakai. | FR-001, BR-001 |
| AC-003 | **Diberikan** kategori "Makanan" masih memiliki item tertaut, **ketika** pengguna mencoba menghapusnya, **maka** sistem menolak dan menyarankan memindahkan item atau menonaktifkan kategori. | FR-002, BR-002 |
| AC-004 | **Diberikan** beberapa kategori dengan urutan tertentu, **ketika** pengguna mengubah urutan dan menyimpan, **maka** layar order menampilkan kategori sesuai urutan baru. | FR-003 |

**Manajemen Item Produk**

| ID | Skenario (Given–When–Then) | Verifikasi FR |
|----|----------------------------|---------------|
| AC-005 | **Diberikan** form item produk, **ketika** pengguna menyimpan item dengan nama, kategori, dan harga valid, **maka** item tersimpan dan tampil di kategori yang dipilih. | FR-005 |
| AC-006 | **Diberikan** form item produk, **ketika** pengguna mengisi harga negatif atau non-numerik, **maka** sistem menolak simpan dan menampilkan pesan validasi harga. | FR-006, BR-004 |
| AC-007 | **Diberikan** sebuah item yang masih direferensikan transaksi, **ketika** pengguna mencoba menghapusnya, **maka** sistem menolak penghapusan karena data masih dipakai. Menonaktifkan item adalah aksi terpisah dan tidak menghapus record. | FR-007, BR-005 |
| AC-008 | **Diberikan** sebuah item berstatus "Tersedia", **ketika** pengguna menandainya "Habis", **maka** item tidak lagi dapat dipilih pada layar pembuatan order namun tetap terlihat di pengelolaan produk. | FR-010, BR-006 |
| AC-009 | **Diberikan** sebuah item, **ketika** pengguna mengunggah foto berformat dan berukuran sesuai batas, **maka** foto tersimpan dan tampil pada layar order; **ketika** file melebihi batas, **maka** sistem menolak unggah dengan pesan jelas. | FR-008 |

**Varian & Modifier**

| ID | Skenario (Given–When–Then) | Verifikasi FR |
|----|----------------------------|---------------|
| AC-010 | **Diberikan** item "Es Teh" dengan varian ukuran (Kecil +0, Besar +3.000), **ketika** kasir memilih varian "Besar", **maka** sistem menampilkan harga sesuai penyesuaian varian. | FR-011, FR-014 |
| AC-011 | **Diberikan** item dengan grup modifier "Topping" (Keju +5.000, Telur +4.000), **ketika** kasir memilih Keju dan Telur, **maka** harga akhir = harga dasar + 9.000. | FR-012, FR-014 |
| AC-012 | **Diberikan** grup modifier "Level Pedas" ditandai wajib (min 1, maks 1), **ketika** kasir menambahkan item ke order tanpa memilih level pedas, **maka** sistem menahan dan meminta pemilihan satu opsi. | FR-013, BR-008 |

**Tampilan untuk Order**

| ID | Skenario (Given–When–Then) | Verifikasi FR |
|----|----------------------------|---------------|
| AC-013 | **Diberikan** daftar produk berisi item aktif dan nonaktif/habis, **ketika** kasir membuka layar order, **maka** hanya item aktif dan tersedia yang ditampilkan. | FR-016, BR-006 |
| AC-014 | **Diberikan** katalog produk, **ketika** kasir mengetik sebagian nama (mis. "ayam") tanpa memperhatikan huruf besar/kecil, **maka** sistem menampilkan seluruh item yang namanya mengandung kata tersebut. | FR-015 |
| AC-015 | **Diberikan** layar order, **ketika** kasir memilih filter kategori "Minuman", **maka** hanya item kategori tersebut yang ditampilkan. | FR-015 |

> **Penutup M-001:** Seluruh User Story (US-001–US-012) telah diturunkan menjadi Functional Requirement (FR-001–FR-016), dijaga oleh Business Rules (BR-001–BR-010), dan diverifikasi melalui Acceptance Criteria (AC-001–AC-015). Modul M-001 dinyatakan **lengkap** pada level PRD dan siap dirujuk oleh dokumen desain berikutnya.

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 1.0 | 2026-06-06 | Isi M-001 dipindahkan ke file modul terpisah; penyesuaian penomoran bagian (1–5) dan revisi bahasa hasil review | Project Lead |
| 1.1 | 2026-06-06 | FR-007, BR-005, AC-007 direvisi: hapus framing soft-delete; `is_active` murni status aktif/nonaktif, pencegahan hapus via penolakan (FK referensi) | Project Lead |
