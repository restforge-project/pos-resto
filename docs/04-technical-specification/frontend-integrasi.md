# TECHNICAL SPECIFICATION — Arsitektur Frontend & Integrasi (Bagian 6–7)
## POS Rumah Makan — Point of Sale System untuk Restoran

> Bagian dari dokumen [04 — Technical Specification](README.md).

---

## 6. Arsitektur Frontend

> **Status: belum ditulis.** Akan memuat: struktur design template (halaman, `assets/`, modul JS per-halaman), pemetaan halaman M-001 (`categories.html`, `items.html`, `variants.html`, `modifiers.html`, `pos.html`), serta pola `config.js` (`appCode`, `authBaseUrl`, `apiBaseUrl`).

> Sementara bagian ini belum ditulis, kondisi yang sudah berjalan terekam pada `src/pos-frontend-integrasi/docs/`, termasuk struktur halaman, pola modul, dan alur unggah foto.

## 7. Integrasi Frontend–Backend

> **Status: belum ditulis.** Akan memuat: pemanggilan API via `$.ajaxSetup` (Bearer JWT), DataTables server-side ke endpoint `/datatables`, contoh request/response untuk operasi M-001 (`product_category/create`, `read`, `first`, `update`, `delete`), serta penanganan envelope & error (`success`, `errors` per field, kode status 400/409).

> Kontrak yang sudah dipastikan terhadap runtime yang berjalan, termasuk sembilan action per resource dan empat bentuk error, ada pada `src/pos-frontend-integrasi/docs/03-kontrak-endpoint.md`.
