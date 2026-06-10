# TECHNICAL SPECIFICATION — Reliability & Deployment (Bagian 8–9)
## POS Rumah Makan — Point of Sale System untuk Restoran

> Bagian dari dokumen [04 — Technical Specification](README.md).

---

## 8. Reliability & Infrastruktur

> **Status: belum ditulis.** Akan memuat: pemanfaatan Redis untuk **idempotency**, **distributed lock**, **rate limit**, dan **cache**; feature-flag ENV terkait; serta catatan Live Sync (WebSocket) yang ditunda hingga KDS (M-004).

## 9. Deployment & Lingkungan

> **Status: belum ditulis.** Akan memuat: topologi pilot (single-tenant, online), konfigurasi `.env` (koneksi DB, Redis, `app_code`, license key), port & `restforge serve` (opsi `--cluster`/PM2), penyajian frontend statis, dan **reverse proxy nginx sebagai opsi produksi** (TLS, same-origin).
