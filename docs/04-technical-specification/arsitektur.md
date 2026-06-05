# TECHNICAL SPECIFICATION — Arsitektur (Bagian 2–4)
## POS Rumah Makan — Point of Sale System untuk Restoran

> Bagian dari dokumen [04 — Technical Specification](README.md). Berkas ini memuat **Bagian 2 (Ikhtisar Arsitektur)**, **Bagian 3 (Technology Stack)**, dan **Bagian 4 (Arsitektur Backend)**.

---

## 2. Ikhtisar Arsitektur (Architecture Overview)

### 2.1 Gaya Arsitektur (Architecture Style)

Sistem memakai arsitektur **client–server tiga lapis (3-tier)** dengan backend *definition-first*. Frontend adalah aplikasi web **multi-page** statis (HTML + Bootstrap 5 + jQuery) yang berperan sebagai *thin client* — seluruh logika data, validasi, dan persistensi berada di backend. Backend dihasilkan oleh **RESTForge** sebagai HTTP API bergaya **action-based** (`POST /api/{project}/{resource}/{action}`), berkomunikasi dengan frontend melalui JSON. Penyimpanan data memakai **PostgreSQL**, dengan **Redis** sebagai lapisan state bersama untuk keandalan (cache, distributed lock, idempotency, rate limit).

Frontend dan data API berjalan sebagai **dua layanan terpisah** dan berkomunikasi **langsung lintas-origin**: frontend statis dilayani sebagai berkas (di development cukup static server seperti `npx serve`), sedangkan data API dilayani runtime RESTForge pada port tersendiri. Karena itu **CORS diaktifkan pada runtime**, dan setiap permintaan AJAX menyertakan header `Authorization: Bearer <JWT>` secara otomatis. Pendekatan baseline ini **tidak memerlukan reverse proxy** — sesuai use-case nyata RESTForge. Reverse proxy (nginx) bersifat **opsional dan diperuntukkan bagi produksi** (terminasi TLS, satu hostname/same-origin); pembahasannya ada di [Bagian 9 — Deployment](reliability-deployment.md), bukan komponen inti.

Autentikasi memakai **Auth service bawaan RESTForge** (layanan terpisah, diidentifikasi `app_code`) yang menerbitkan **JWT access + refresh token** beserta data role & permission pengguna. Frontend mengonsumsinya lewat pola `AuthClient`. Detail pada [Bagian 5 — Autentikasi](autentikasi.md).

### 2.2 Diagram Komponen (Component Diagram)

```
┌──────────────────────────────────────────────────────────────────┐
│  KLIEN (Browser kasir / pelayan / manajer)                        │
│  Bootstrap 5 + jQuery (static MPA) — design-template               │
│  AuthClient: simpan JWT access/refresh di localStorage            │
└───────┬───────────────────────────────────────────┬───────────────┘
        │ (1) login / refresh                        │ (2) panggilan data (AJAX)
        │ POST {authBaseUrl}/session/login           │ POST {apiBaseUrl}/{resource}/{action}
        │ { app_code, username, password }           │ Authorization: Bearer <JWT>
        ▼                                            ▼
┌──────────────────────────────┐   ┌──────────────────────────────────┐   ┌──────────────┐
│  RESTForge AUTH SERVICE       │   │  RESTForge DATA RUNTIME (Node.js) │◄─►│   Redis      │
│  • app_code                   │   │  • Endpoint action-based          │   │ cache, lock, │
│  • JWT access + refresh       │   │  • Validasi JWT + permission      │   │ idempotency, │
│  • roles & permissions        │   │  • CORS enabled                   │   │ rate limit   │
│  • register, reset, verify    │   │  • Definition-first: SDF · RDF    │   └──────────────┘
└──────────────────────────────┘   └───────────────────┬──────────────┘
                                                        │ SQL
                                                        ▼
                                    ┌──────────────────────────────────┐
                                    │  PostgreSQL (single-tenant pilot) │
                                    └──────────────────────────────────┘

  (opsional, produksi) nginx reverse proxy — TLS + same-origin di depan kedua layanan → Bagian 9
```

> **Catatan:** UDF (UI Definition File) RESTForge **tidak dipakai** karena frontend memakai design template yang sudah ditentukan. Dari tiga format RESTForge, hanya **SDF** (skema) dan **RDF** (endpoint) yang digunakan. Live Sync (WebSocket+Redis) belum diaktifkan pada MVP; lihat [Bagian 8 — Reliability](reliability-deployment.md).

### 2.3 Komponen Utama (Key Components)

| Komponen | Teknologi | Tanggung Jawab |
|----------|-----------|----------------|
| Klien (frontend) | Bootstrap 5, jQuery, plugin (DataTables, flatpickr, dll) | Menyajikan UI, memanggil API via AJAX, menyimpan JWT (localStorage), route/permission guard |
| Auth service | RESTForge Auth (hosted) | Login/refresh/register, terbit JWT access+refresh, sumber role & permission |
| Runtime data | RESTForge (Node.js) | Endpoint action-based, validasi payload, verifikasi JWT, akses data; CORS aktif |
| State bersama | Redis | Cache, distributed lock, idempotency, rate limit |
| Database | PostgreSQL | Penyimpanan persisten data POS |
| Reverse proxy *(opsional, produksi)* | nginx | Terminasi TLS, satu hostname/same-origin di depan frontend + API |

### 2.4 Alur Request (Request Flow)

**Login (sekali di awal sesi):** Browser mengirim `POST {authBaseUrl}/session/login` berisi `{ app_code, username, password }`. Auth service mengembalikan **access token + refresh token (JWT)** beserta profil, role, dan permission; `AuthClient` menyimpannya di localStorage dan memasang idle-timeout.

**Panggilan data — contoh membuat kategori (mewujudkan FR-001 pada M-001):**

1. Frontend memanggil `POST {apiBaseUrl}/product_category/create` dengan body JSON; `$.ajaxSetup` otomatis menambahkan header `Authorization: Bearer <access_token>`.
2. **RESTForge data runtime** memverifikasi JWT (dan permission, mis. `PRODUCT_CATEGORY_CREATE`), lalu memvalidasi payload sesuai aturan RDF.
3. Bila valid, runtime menjalankan `INSERT` ke **PostgreSQL**; **Redis** dipakai untuk *distributed lock*/idempotency bila relevan (mis. mencegah duplikasi akibat klik ganda).
4. Runtime mengembalikan **envelope response** JSON (`success`, `message`, `data`, `timestamp`) dengan kode status semantik — `201` sukses, `400` validasi gagal, `409` nama kategori duplikat (lihat Bagian 4 di bawah).
5. Bila runtime membalas `401` (token kedaluwarsa), `AuthClient` otomatis me-`refresh` token lalu mengulang permintaan; jika gagal, pengguna diarahkan ke halaman login.
6. Frontend membaca envelope dan memperbarui tampilan (mis. menampilkan baris kategori baru atau pesan error per field).

### 2.5 Prinsip Arsitektur (Architecture Principles)

Arsitektur berpegang pada beberapa prinsip yang memandu keputusan di bagian-bagian berikutnya: **definition-first** — perubahan skema dan endpoint diawali dari berkas definisi (SDF/RDF) demi konsistensi dan portabilitas; **runtime stateless** — state lintas-request ditaruh di Redis/PostgreSQL agar runtime dapat diskalakan dan di-restart tanpa kehilangan konteks; **rujukan tunggal per lapisan** — skema adalah otoritas struktur data (05), RDF adalah otoritas kontrak endpoint (06); **manfaatkan kapabilitas bawaan** — autentikasi, validasi, dan reliability memakai mekanisme RESTForge daripada membangun ulang; serta **kesederhanaan baseline** — tanpa reverse proxy saat development, dengan proxy diperkenalkan hanya bila produksi membutuhkannya.

---

## 3. Technology Stack

Bagian ini menetapkan teknologi yang dipakai di setiap lapisan beserta alasannya. Komponen yang ditandai **wajib** berasal dari batasan tetap (lihat Bagian 1.3 pada [README](README.md)); komponen lain dipilih agar selaras dengan batasan tersebut.

### 3.1 Ringkasan Stack (Stack Summary)

| Lapisan | Teknologi | Versi (acuan) | Status |
|---------|-----------|---------------|--------|
| Frontend | Bootstrap 5, jQuery, design-template (Dreams POS) | Bootstrap 5.x · jQuery 3.7.1 | Wajib |
| Backend | RESTForge (`@restforgejs/platform`) di atas Node.js | Node.js LTS (≥ 18) | Wajib |
| Database | PostgreSQL | ≥ 14 | Ditetapkan |
| State bersama | Redis | ≥ 6 | Ditetapkan |
| Autentikasi | RESTForge Auth service (`app_code`, JWT) | — | Ditetapkan |
| Reverse proxy *(opsional, produksi)* | nginx | stable | Opsional |
| Process manager *(produksi)* | PM2 | stable | Opsional |

### 3.2 Backend

Backend dibangun di atas **RESTForge** (`@restforgejs/platform`), framework *definition-first* berbasis **Node.js**. Dari tiga format definisi RESTForge, proyek ini memakai dua: **SDF** (Schema Definition File, `schema/<table>.js`) untuk struktur database dan **RDF** (Resource Definition File, `payload/<resource>.json`) untuk endpoint API. **UDF tidak dipakai** karena UI memakai design template tersendiri.

Runtime dijalankan via CLI (`npx restforge serve`), menghasilkan HTTP API bergaya **action-based** dengan envelope JSON yang konsisten. Pemilihan ini bukan preferensi melainkan **batasan tetap** proyek; seluruh keputusan turunan (skema, endpoint, validasi) mengikuti konvensi RESTForge.

### 3.3 Frontend

Frontend memakai **design template admin Bootstrap 5 + jQuery** yang sudah ditetapkan. Komposisi pustaka pada template:

| Kategori | Pustaka |
|----------|---------|
| Core | Bootstrap 5, jQuery 3.7.1 |
| Ikon | Lucide |
| Tabel data | DataTables (server-side processing) |
| Input & tanggal | flatpickr, daterangepicker, select2 |
| Interaksi | dragula (drag-and-drop), slick (carousel), SimpleBar (scrollbar) |
| Grafik | ApexCharts |
| Styling | SCSS (dikompilasi ke `style.css`) |

Halaman yang relevan untuk **M-001** sudah tersedia di template: `categories.html`, `items.html`, dan `pos.html`. Pola integrasi (AuthClient, `$.ajaxSetup`, modul per-halaman) mengikuti contoh use-case RESTForge; pemetaan rinci dibahas di [Bagian 6 — Frontend](frontend-integrasi.md).

### 3.4 Data & Infrastruktur

**PostgreSQL** dipilih sebagai database karena dukungan constraint dan tipe data yang lengkap (unique, foreign key, check, JSON), cocok untuk integritas data POS dan didukung penuh RESTForge. Untuk pilot single-tenant dipakai **satu database**.

**Redis** dipakai sebagai lapisan state bersama yang menopang primitif keandalan RESTForge — cache, *distributed lock*, idempotency, dan rate limit (lihat [Bagian 8 — Reliability](reliability-deployment.md)). Redis disertakan sejak MVP sesuai keputusan arsitektur.

### 3.5 Perkakas & Lingkungan Pengembangan (Tooling)

| Perkakas | Fungsi |
|----------|--------|
| Node.js + npm | Runtime & manajemen paket backend |
| RESTForge CLI (`npx restforge`) | `schema migrate`, `endpoint create`, `serve`, `key generate` |
| Static server (mis. `npx serve`) | Menyajikan frontend statis saat development |
| PostgreSQL client (`psql`) | Inisialisasi skema & seed |
| Git | Version control untuk definisi (SDF/RDF) dan generated code |

> **Catatan versi:** Angka versi di atas adalah acuan minimum; versi pasti dikunci saat setup environment dan dicatat pada [10-deployment-guide.md](../10-deployment-guide.md). RESTForge memerlukan **license key** yang valid untuk menjalankan runtime (lihat [Bagian 9 — Deployment](reliability-deployment.md)).

---

## 4. Arsitektur Backend (RESTForge)

Backend POS Rumah Makan **mengadopsi arsitektur RESTForge apa adanya**. Dokumen ini **tidak menulis ulang** arsitektur tersebut; arsitektur resmi sudah dipelihara pada dokumentasi RESTForge Systems dan menjadi **rujukan tunggal**. Bila terjadi perbedaan, dokumentasi RESTForge yang berlaku.

### 4.1 Dokumen Arsitektur Rujukan (by name)

| Dokumen Arsitektur RESTForge | Cakupan |
|------------------------------|---------|
| Arsitektur RESTForge Systems — Kondisi Saat Ini | Komponen/package, alur data, lapisan runtime, integrasi eksternal |
| Cara Kerja RESTForge | Alur definition-first dari definisi data sampai deployment |
| RESTForge Product Overview | Ikhtisar produk dan kapabilitas |
| RESTForge Brand Philosophy | Prinsip *schema-driven* platform |
| Diagram Tiga Pilar | Relasi tiga format definisi (SDF · RDF · UDF) |

### 4.2 Elemen Arsitektur yang Diadopsi (by name)

Proyek ini memakai elemen arsitektur RESTForge berikut, sesuai definisi pada handbook (dirujuk berdasarkan nama, tidak disalin):

- **Tiga Pilar Definisi** — dari ketiganya, proyek memakai **SDF** (skema) dan **RDF** (endpoint); **UDF tidak dipakai** (frontend memakai design template).
- **Pola endpoint action-based** — `POST /api/{project}/{resource}/{action}` dengan envelope JSON dan kode status semantik (spesifikasi pada *API Endpoint Spec* RESTForge).
- **Lapisan runtime** — Express + middleware (`cors`, `idempotency`, `rate-limiter`, `security-headers`) → handler CRUD/workflow → query-builder per-dialect → database.
- **Reliability primitives** — *distributed lock*, idempotency, cache, dan ID Generator di atas Redis (diaktifkan via feature-flag ENV).
- **Dukungan multi-database via dialect** — proyek memilih **PostgreSQL** (lihat Bagian 3).

> Detail teknis tiap elemen (struktur SDF/RDF, daftar endpoint, format WHERE/sort, kode status, konfigurasi lock & idgen) berada pada handbook RESTForge dan **tidak diduplikasi** di dokumen ini. Penerapan konkret untuk modul M-001 dibahas pada [05-database-design](../05-database-design/README.md) (skema) dan [06-api-specification](../06-api-specification.md) (endpoint).
