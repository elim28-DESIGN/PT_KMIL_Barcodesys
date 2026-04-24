# PT KMIL — Barcode Work Order System `V32`

<div align="center">

![Version](https://img.shields.io/badge/version-V32-8b5cf6?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAyTDIgN2wxMCA1IDEwLTV6TTIgMTdsOSA1IDktNXYtNWwtOSA1LTktNXoiLz48L3N2Zz4=)
![Platform](https://img.shields.io/badge/platform-Web%20%7C%20iOS%20%7C%20Android-8b5cf6?style=for-the-badge)
![Status](https://img.shields.io/badge/status-Production--Ready-10b981?style=for-the-badge)
![License](https://img.shields.io/badge/license-Proprietary-f97316?style=for-the-badge)

**Single-file PWA · Multi-division Work Order management · Real-time QR scanning**

*Dibangun untuk transformasi operasional PT KMIL — transparansi, akuntabilitas, dan efisiensi seluruh divisi*

</div>

---

## 📋 Overview

PT KMIL Barcode System adalah aplikasi **Progressive Web App (PWA)** berbasis single HTML file untuk manajemen Work Order (WS) lintas divisi secara real-time. Sistem ini menggantikan proses manual dengan alur kerja digital yang terintegrasi, mulai dari pembuatan order di Marketing hingga pengiriman di Accounting.

```
Marketing → Engineering → PPIC → Production → QC → Accounting
              ↕              ↕           ↕
           Drawing        BOM        Serah Terima
```

---

## ✨ Features

### 🏭 Core System
| Feature | Detail |
|---|---|
| **Multi-Division Workflow** | Marketing, Engineering, PPIC, Purchasing, Production, QC, Accounting |
| **QR Code / Barcode Scan** | Physical scanner + Camera scan (iOS & Android) |
| **Google Sheets Sync** | Real-time push via Google Apps Script |
| **Local Storage Backup** | Offline-capable data persistence |
| **PWA Ready** | Installable di iOS (Add to Home Screen) & Android |
| **Work Order Tracking** | Status per-divisi dengan timeline lengkap |
| **Material List (BOM)** | PPIC input → auto-sync ke Produksi & QC |
| **QC Gating** | Password-protected, akses terbatas |
| **COO Dashboard** | KPI management view untuk eksekutif |
| **QR Finder** | Generate & cetak QR Code per WS |

### 📷 Camera QR Scanner (V32 New)
| Feature | Detail |
|---|---|
| **Rear Camera Default** | Otomatis pakai kamera belakang (environment) |
| **Auto-detect** | Decode QR dalam < 1 detik menggunakan jsQR |
| **Auto-use** | QR valid langsung trigger `processScan` otomatis |
| **Torch / Flash** | Toggle flashlight saat kondisi gelap |
| **Flip Camera** | Switch kamera depan ↔ belakang |
| **Haptic Feedback** | Vibrate saat QR berhasil di-scan |
| **Fallback Input** | Manual input jika kamera tidak tersedia |
| **iOS Safari** | Kompatibel penuh dengan `playsinline` + `getUserMedia` |
| **Android Chrome** | Rear camera constraint optimal untuk warehouse |

### 🎨 UI / UX (V32)
- **Purple Industrial Theme** — full dark purple palette `#8b5cf6 → #6d28d9`
- **Spark Particle System** — ambient + click burst + button hover
- **Rotating Gear Animation** — ambient industrial decoration
- **Uptime Piston Badge** — live session timer di topbar
- **Police Line Warnings** — hazard tape sebelum save di Production & PPIC
- **T&C Screen** — muncul otomatis setelah splash, compliance barcode system
- **Scanline / HUD Overlay** — CRT scanlines + corner brackets
- **Stagger Animations** — entrance animation per elemen

---

## 🚀 Quick Start

### Cara Pakai (Tanpa Install)
1. Download file `V32_Industrial_Purple_kmil.html`
2. Buka di browser modern (Chrome / Safari)
3. Baca & setujui T&C → masuk ke dashboard
4. Isi **Settings (⚙️)** → masukkan Google Apps Script URL
5. Klik **☁️ Load from Sheets** untuk sync data

### Install sebagai PWA
**Android (Chrome):**
1. Buka file di Chrome
2. Tap menu `⋮` → **"Add to Home Screen"**
3. Konfirmasi → icon PT KMIL muncul di home

**iOS (Safari):**
1. Buka file di Safari
2. Tap **Share** `⬆️` → **"Add to Home Screen"**
3. Konfirmasi → app tersimpan dengan nama *PT KMIL*

---

## 📷 Cara Pakai Camera QR Scanner

Scanner tersedia di semua divisi yang memiliki input NO_WS:

```
Engineering · PPIC · Production · QC · Accounting
```

**Langkah penggunaan:**
1. Buka divisi (contoh: Production)
2. Tap tombol **📷** di sebelah input NO_WS
3. Izinkan akses kamera saat diminta browser
4. Arahkan kamera ke QR Code pada surat WS
5. QR terdeteksi → muncul hasil → otomatis proses dalam 1 detik

**Tips lapangan:**
- Gunakan **🔦 Flash** saat cahaya kurang (gudang, malam)
- Gunakan **🔄 Flip** untuk switch ke kamera depan jika perlu
- Jika kamera tidak bisa diakses → ketik NO_WS manual di fallback input

> **Note iOS:** Buka via Safari, bukan Chrome/Firefox — hanya Safari yang punya akses `getUserMedia` di iOS

---

## 🏗️ Arsitektur

```
V32_Industrial_Purple_kmil.html  (single file, ~560 KB)
│
├── <head>
│   ├── Fonts: Barlow Condensed, Barlow, IBM Plex Mono (Google Fonts)
│   ├── jsQR 1.4.0 (QR decode library, CDN)
│   └── <style> — 1,400+ lines CSS
│       ├── Design System (CSS Variables, Purple Palette)
│       ├── Component Library (topbar, sidebar, cards, forms)
│       ├── Motion Engine (30+ keyframe animations)
│       ├── T&C Screen
│       ├── Police Line Warning
│       └── Camera Scanner
│
├── <body>
│   ├── T&C Compliance Screen
│   ├── Splash Screen (logo + progress)
│   ├── Topbar (brand + clock + status + uptime)
│   ├── Sidebar (navigation per divisi)
│   ├── Mobile Bottom Nav
│   ├── Views (Marketing, Engineering, PPIC, Purchasing,
│   │          Production, QC, Accounting, Track, Reject,
│   │          COO/Management, Flow Diagram)
│   ├── Modals (Config, Mass Lookup, MKT Warning, Shortcuts)
│   └── Camera QR Scanner Modal
│
└── <script> — ~3,000+ lines JS
    ├── Data Layer (localStorage, GAS sync)
    ├── Scan Engine (processScan, clearScan)
    ├── Form Logic (per-divisi)
    ├── QR Generator (qrcodejs)
    ├── Material List (PPIC BOM)
    ├── Serah Terima / Timeline
    ├── COO KPI Dashboard
    ├── Animation Engine (sparks, gears, piston)
    └── Camera Scanner Engine (openCamScan, jsQR loop)
```

---

## 🔌 Google Apps Script Setup

Sistem sync ke Google Sheets melalui Google Apps Script (GAS).

**Langkah Setup GAS:**
1. Buka [script.google.com](https://script.google.com)
2. Buat project baru
3. Deploy sebagai **Web App** → akses: *Anyone*
4. Copy URL deploy
5. Paste di aplikasi: **⚙️ Settings → GAS URL → Save**

**Struktur Sheet yang dibutuhkan:**

| Sheet Name | Keterangan |
|---|---|
| `Events` | Semua event dari semua divisi |
| `Materials` | BOM / Material list dari PPIC |
| `Config` | Konfigurasi sistem |

---

## 📱 Divisi & Fitur Per-Divisi

### 📋 Marketing
- Input order baru (Single Part / Assembling)
- Upload drawing (link URL)
- Generate NO_WS otomatis
- QR Finder: generate & cetak QR Code WS
- Warning modal konfirmasi sebelum save

### ⚙️ Engineering
- Scan WS → auto-fill dari Marketing
- Approval drawing
- Drawing release / tambahan
- Camera QR scan support

### 📦 PPIC
- Scan WS → load referensi dari Marketing
- Input BOM / Material List
- Sync material ke Production & QC
- Police line warning sebelum simpan

### 🏭 Production
- Scan WS → auto-fill + load material PPIC
- Input event operasional (OP Start, dll)
- Single Part & Assembling mode
- Serah Terima ke QC
- Camera QR scan support
- Police line warning sebelum save

### ✅ QC (Quality Control)
- Password-gated access
- Incoming check, QC OK, Repair, Reject, Special Acceptance
- Bulk input material check dari PPIC
- Camera QR scan support

### 🚚 Accounting & Finance
- Scan WS untuk konfirmasi pengiriman
- Customer Delivered event
- Camera QR scan support

### 📡 Track WS
- Cari riwayat lengkap per WS
- Timeline event seluruh divisi
- Material list dari PPIC

### 👔 COO / Management
- Password-protected
- KPI dashboard (total WS, reject rate, delivery)
- Downtime monitoring grid
- Laporan bulanan

---

## 🎨 Design System

### Color Palette (Purple Industrial)

```css
--bg:       #07060f   /* Deep space black  */
--surface:  #0d0b1a   /* Dark navy         */
--card:     #13102a   /* Card background   */
--accent:   #8b5cf6   /* Primary violet    */
--text:     #d8d4f0   /* Soft white        */
--muted:    #6b5f90   /* Muted purple      */

/* Per-division colors */
--mkt:  #a855f7   /* Marketing  — Purple  */
--eng:  #3b82f6   /* Engineering — Blue   */
--ppic: #7c3aed   /* PPIC        — Violet */
--prd:  #f97316   /* Production  — Orange */
--qc:   #10b981   /* QC          — Green  */
--acc:  #ec4899   /* Accounting  — Pink   */
```

### Typography
| Role | Font | Weight |
|---|---|---|
| Display / Headings | Barlow Condensed | 800–900 |
| Body | Barlow | 400–600 |
| Monospace / Data | IBM Plex Mono | 400–700 |

---

## ⚠️ Police Line Warnings

Ditampilkan otomatis sebelum tombol save di:

**Production** — `💾 Save to Sheets`
```
⚠ WARNING ⚠ WARNING ⚠ WARNING ⚠
PERIKSA KEMBALI SEBELUM MENYIMPAN!
Pastikan semua data sudah BENAR dan LENGKAP
```

**PPIC** — `💾 Simpan List Material`
```
⚠ PERIKSA ⚠ PERIKSA ⚠ PERIKSA ⚠
PERIKSA LIST MATERIAL SEBELUM MENYIMPAN!
Pastikan nama material, qty, dan satuan sudah benar.
```

---

## 📜 Terms & Conditions

T&C Compliance Screen muncul otomatis setelah splash animation setiap kali membuka aplikasi (kecuali user centang "Jangan tampilkan lagi di sesi ini").

**Isi T&C mencakup:**
1. Kewajiban Penggunaan Sistem
2. Akurasi dan Tanggung Jawab Data
3. Kepatuhan Proses Workflow
4. Real-Time Execution
5. Audit & Monitoring
6. Sanksi Pelanggaran ⚠️
7. Komitmen Keberlangsungan Perusahaan

User harus klik **"Saya Setuju & Masuk ke Dashboard"** untuk melanjutkan.

---

## 🔐 Keamanan & Akses

| Area | Proteksi |
|---|---|
| QC | Password per-session |
| COO / Management | Password terpisah |
| T&C | Wajib setuju setiap sesi |
| GAS URL | Disimpan di localStorage, tidak terekspos |

---

## 📦 Dependencies

| Library | Version | Source | Fungsi |
|---|---|---|---|
| jsQR | 1.4.0 | CDN jsDelivr | Decode QR dari kamera |
| qrcodejs | 1.0.0 | CDN Cloudflare | Generate QR Code |
| Barlow Condensed | — | Google Fonts | Display font |
| IBM Plex Mono | — | Google Fonts | Monospace / data |

> Semua dependency diload via CDN — tidak perlu `npm install` apapun.

---

## 🔄 Changelog

### V32 (Current) — *April 2025*
- ✅ **Camera QR Scanner** — iOS & Android, jsQR, haptic, torch, flip
- ✅ **Purple Industrial Theme** — full palette migration
- ✅ **T&C Compliance Screen** — post-splash, 7 ketentuan, stagger animation
- ✅ **Police Line Warnings** — Production & PPIC save confirmation
- ✅ **Spark Particle System** — ambient + click + hover bursts
- ✅ **Rotating Gear** — SVG ambient decoration
- ✅ **Uptime Piston Badge** — live session timer
- ✅ **HUD Corner Brackets** — industrial overlay
- ✅ **Scanline / Grid Overlay** — CRT texture
- ✅ **Topbar Height** — 48px → 52px
- ✅ **Size System Refinement** — proportional typography & spacing

### V31
- QR Finder module
- Assembling WS type support
- COO / Management dashboard
- Serah Terima system
- Mass lookup modal

### V30
- Multi-division workflow
- Google Sheets sync
- Material list (BOM)
- QC access gate

---

## 🛠️ Development

### Edit & Deploy
Karena ini single-file app, tidak perlu build process:

```bash
# Edit langsung file HTML
code V32_Industrial_Purple_kmil.html

# Test di local server (opsional, untuk kamera perlu HTTPS)
npx serve .
# atau
python3 -m http.server 8080
```

> **Penting:** Camera `getUserMedia` membutuhkan **HTTPS** atau **localhost**.  
> Untuk testing kamera di jaringan lokal, gunakan `ngrok` atau deploy ke GitHub Pages.

### Deploy ke GitHub Pages
```bash
git init
git add V32_Industrial_Purple_kmil.html
git commit -m "feat: V32 Purple Industrial release"
git branch -M main
git remote add origin https://github.com/YOUR_ORG/kmil-system.git
git push -u origin main
# Enable GitHub Pages → Settings → Pages → Deploy from main
```

URL format: `https://YOUR_ORG.github.io/kmil-system/V32_Industrial_Purple_kmil.html`

---

## 🐛 Troubleshooting

| Masalah | Solusi |
|---|---|
| Kamera tidak muncul (iOS) | Gunakan Safari, bukan Chrome/Firefox |
| "Izin kamera ditolak" | Settings → Safari/Chrome → Camera → Allow |
| QR tidak terbaca | Pastikan pencahayaan cukup, aktifkan Flash 🔦 |
| Data tidak sync ke Sheets | Cek GAS URL di Settings, pastikan deploy ulang Script |
| T&C muncul terus | Centang "Jangan tampilkan lagi" atau clear localStorage |
| Kamera lambat decode | Kurangi jarak ke QR, jaga agar tidak blur |
| Layout rusak di iOS | Gunakan Safari versi terbaru |

---

## 📊 Browser Support

| Browser | Camera | QR Scan | PWA |
|---|---|---|---|
| Chrome Android | ✅ | ✅ | ✅ |
| Safari iOS ≥ 14.3 | ✅ | ✅ | ✅ (A2HS) |
| Chrome Desktop | ✅ | ✅ | ✅ |
| Firefox | ⚠️ | ⚠️ | ❌ |
| Samsung Internet | ✅ | ✅ | ✅ |

---

## 👨‍💼 Credit

```
System Design & Development
  Elim K — PT KMIL Digital Operations
  Built 2024–2026

Stack
  Vanilla HTML · CSS · JavaScript
  No framework. No build tool. No npm.
  Just one file that works everywhere.
```

---

## 📄 License

**Proprietary** — PT KMIL Internal Use Only

Sistem ini merupakan properti PT KMIL. Dilarang mendistribusikan, mereplikasi, atau menggunakan sistem ini di luar lingkungan PT KMIL tanpa izin tertulis dari Management.

---

<div align="center">

**PT KMIL Barcode System V32**  
*Bersatu Kita Hebat*

`⚙️ Industrial · 📷 Camera · 🟣 Purple · 📋 Compliance`

</div>
