# PT KMIL — Work Order System V22

<p align="center">
  <img src="docs/preview.jpg" alt="PT KMIL System V22 Preview" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-V22-f59e0b?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/platform-Web%20%7C%20PWA-3b82f6?style=flat-square" alt="Platform">
  <img src="https://img.shields.io/badge/stack-Vanilla%20JS-10b981?style=flat-square" alt="Stack">
  <img src="https://img.shields.io/badge/license-Private-ef4444?style=flat-square" alt="License">
</p>

> **Multi-division work order tracking system** for PT KMIL — covers Marketing, Engineering, PPIC, Production, QC, and Customer delivery in a single offline-capable web app.

---

## 📁 Repository Structure

```
kmil-system/
├── index.html          ← Main app entry point (loads CSS + JS externally)
├── assets/
│   ├── main.css        ← All styles (dark/light theme, responsive)
│   └── main.js         ← All application logic
├── docs/
│   ├── preview.jpg     ← App screenshot / banner
│   ├── icon.jpg        ← App icon
│   └── CHANGELOG.md    ← Version history
└── README.md
```

---

## 🚀 Features

### Dashboard
- **Stat cards** — Total WS, Shipped, Expired, QC OK, Repair/Reject, BOM Released
- **Bar chart** (WS per divisi) & **Pie chart** (distribusi event) — ukuran diperbesar di V22
- **Deadline Mendekati** — sort Terdekat/Terjauh, filter by Customer, print langsung

### Marketing
- Order creation dengan QR code auto-generate
- Support Single Part & Assembling (multi-WS)
- Drawing upload & vault
- Pre-save confirmation modal dengan data review

### Engineering
- Drawing status tracking (Released, Tambahan, Revisi)
- WS Turun ke PPIC — Single & Assembling checklist dengan progress bar
- Wajib pilih User sebelum save

### PPIC
- BOM Release + Material list per WS (nama, dimensi, material, qty, harden)
- Qty Total wajib diisi sebelum save *(V22)*
- Auto-sync material list ke Production & QC

### Production
- OP Start, Revision Drawing, Material Problem events
- **S/T WS dari PPIC** — serah terima log dengan print *(rename V22)*
- Wajib isi Shift, Operation, dan Qty sebelum save *(V22)*
- Material list dari PPIC ditampilkan otomatis saat scan WS

### QC — Quality Control
- **Akses terkunci** — login wajib dengan ID & password *(V22)*
  - ID: `erik` / Password: `qc1`
- Scan WS → load material list dari PPIC → set status per item (Incoming / OK / Repair / Reject / Special)
- Wajib pilih User sebelum save *(V22)*

### Customer / A&F
- Delivery confirmation, parsial support
- Surat jalan photo upload & vault

### Tools
| Tool | Deskripsi |
|------|-----------|
| 📡 Track WS | Lacak history event per WS / Customer / PO |
| 🔧 Downtime Mesin | Grid status mesin per hari (OK / Problem / Off) |
| 🔄 Flow Diagram | Visual pipeline antar divisi |
| 🏷️ Katalog QR WS Assembling | Generate & print QR codes batch *(rename V22)* |
| 📊 Item Stock | Kelola stok item per Customer & WS |
| 🚫 Item Reject / NG | **Dipindah dari Dashboard ke Tools** *(V22)* — filter & print |

---

## 🆕 Changelog V22

| Area | Perubahan |
|------|-----------|
| Dashboard | Chart Bar & Pie diperbesar (height 140 → 220px) |
| Dashboard | Deadline Mendekati: sort + filter by Customer + print |
| Engineering | Warning: tidak bisa save jika User belum dipilih |
| Production | Warning: wajib isi Shift, Operation, Qty sebelum save |
| Production | "Serah Terima WS" → **"S/T WS dari PPIC"** |
| PPIC | Tidak bisa save jika Qty Total belum diisi / 0 |
| QC | Halaman dikunci — harus login (id: `erik`, pw: `qc1`) |
| QC | Tidak bisa save jika User belum dipilih |
| Tools | "Katalog QR" → **"Katalog QR WS Assembling"** |
| Tools | Item Reject / NG **dipindah** dari Dashboard ke Tools |

---

## 🛠 Tech Stack

- **Vanilla HTML / CSS / JavaScript** — zero framework, zero build step
- **QRCode.js** (CDN) — QR code generation
- **IBM Plex Mono + IBM Plex Sans** (Google Fonts)
- **localStorage** — semua data tersimpan lokal di browser
- **Google Apps Script** (opsional) — sinkronisasi ke Google Sheets

---

## 📦 Deployment

### Option 1 — GitHub Pages (Recommended)
1. Fork / clone repo ini
2. Buka **Settings → Pages**
3. Set Source: `Deploy from branch → main → / (root)`
4. Akses via `https://<username>.github.io/kmil-system/`

### Option 2 — Local File
```bash
git clone https://github.com/<org>/kmil-system.git
cd kmil-system
# Buka index.html langsung di browser
open index.html
```

### Option 3 — Any Static Host
Upload semua file ke Netlify, Vercel, Cloudflare Pages, atau server hosting biasa.

---

## ⚙️ Google Sheets Integration (Opsional)

1. Buat Google Apps Script Web App baru
2. Deploy sebagai **Web App** (Execute as: Me, Access: Anyone)
3. Copy URL deployment
4. Di aplikasi: klik ⚙️ Settings → paste URL → Save

Data akan dikirim ke Sheets setiap kali user menekan **Save to Sheets**.

---

## 🔐 QC Access

Halaman Quality Control memerlukan login:

| Field | Value |
|-------|-------|
| ID | `erik` |
| Password | `qc1` |

Untuk menambah user QC, edit konstanta `QC_USERS` di `assets/main.js`:
```js
const QC_USERS = { erik: 'qc1', namauser: 'password' };
```

---

## 📱 PWA / Mobile

App ini dapat diinstall sebagai PWA di iOS dan Android:
- **iOS**: Safari → Share → Add to Home Screen
- **Android**: Chrome → Menu → Add to Home Screen

---

## 📄 License

Internal use — PT KMIL. All rights reserved.
