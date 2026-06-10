# TECHNICAL SPECIFICATION — Autentikasi & Otorisasi (Bagian 5)
## POS Rumah Makan — Point of Sale System untuk Restoran

> Bagian dari dokumen [04 — Technical Specification](README.md).

---

## 5. Autentikasi & Otorisasi

> **Status: belum ditulis.** Akan memuat: Auth service bawaan RESTForge (`app_code`, endpoint `session/login`/`refresh`/`logout`), penerbitan **JWT access + refresh token**, pola `AuthClient` di frontend (penyimpanan token, auto-refresh saat 401, idle-timeout), serta **otorisasi berbasis role & permission** (route guard `requireLogin`/`requirePermission`, pembatasan tombol per `*_CREATE/READ/UPDATE/DELETE`).
