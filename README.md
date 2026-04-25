# PT KMIL System — V39

> **Single-file industrial Work Order tracking system** with QR code scanning, Google Sheets sync, multi-division workflow, and offline-first architecture.

[![Version](https://img.shields.io/badge/version-V39-purple)](https://elim28-design.github.io/PT_KMIL_Barcodesys/)
[![Type](https://img.shields.io/badge/type-Single%20HTML%20File-blue)](#)
[![Storage](https://img.shields.io/badge/storage-LocalStorage%20%2B%20Google%20Sheets-green)](#)

---

## Daftar Isi

- [Overview](#overview)
- [Fitur Utama](#fitur-utama)
- [Alur Kerja per Divisi](#alur-kerja-per-divisi)
- [Arsitektur Teknis](#arsitektur-teknis)
- [Setup & Konfigurasi](#setup--konfigurasi)
- [Struktur Data](#struktur-data)
- [Changelog](#changelog)
- [Known Issues](#known-issues)

---

## Overview

PT KMIL System adalah aplikasi berbasis browser **satu file HTML** untuk melacak Work Order (WS) dari awal pembuatan oleh Marketing hingga pengiriman ke customer oleh Accounting & Finance. Setiap divisi memiliki form input sendiri; semua data disimpan secara lokal (offline-first) dan di-sync ke Google Sheets sebagai backend cloud.

**URL Produksi:** https://elim28-design.github.io/PT_KMIL_Barcodesys/

---

## Fitur Utama

### QR Code & Scanning
- **Generate QR Code** otomatis dari NO_WS saat Marketing membuat order
- **Scan via kamera HP** — autofill semua field form divisi yang relevan
- **Physical scanner support** — input langsung dari barcode scanner USB/Bluetooth
- **QR Finder** — cari dan print QR Code berdasarkan NO_WS di panel Marketing

### Multi-Divisi Workflow
| Divisi | Event Utama |
|--------|-------------|
| 📋 Marketing | Order Created, Drawing Tambahan, NO DRAWING |
| ⚙️ Engineering | Drawing Released, Turun ke PPIC |
| 📦 PPIC | BOM Released + List Raw Material |
| 🏭 Production | OP Start (Single/Assembling), Serah Terima |
| ✅ QC | Incoming Check, QC OK, Reject, Repair |
| 🚚 Accounting & Finance | Delivered |

### Assembling Order Support
- Marketing membuat parent WS (mis. `26030011`) + child WS (`26030011-01` s/d `26030011-05`)
- QC scan parent sekali → sistem auto-generate dan save ke semua child WS secara **berurutan dan terkonfirmasi** (tidak ada double-entry)
- PPIC auto-generate list material per child WS dari data Marketing

### Offline-First
- Data disimpan di `localStorage` secara **optimistik** (instan) sebelum dikirim ke Sheets
- Semua divisi bisa bekerja tanpa internet; sync dilakukan saat koneksi tersedia
- **Load from Sheets** — tombol manual untuk menarik data terbaru dari semua divisi

### Track WS
- Cari history WS by NO_WS, Customer, atau NO_PO
- Buka tanpa query → tampil 60 WS terbaru
- Timeline event lengkap per WS, dengan tombol **Print Log**
- Navigasi dari Dashboard langsung ke Track WS dengan QR scan

### Dashboard
- Ringkasan status WS aktif per divisi
- Klik card → langsung ke form divisi yang relevan
- Real-time count WS per stage

---

## Alur Kerja per Divisi

```
Marketing → Engineering → PPIC → Production → QC → Accounting & Finance
```

### 1. Marketing (`User: Dicky S`)
1. Isi Customer, NO_WS, NO_PO, Part Name, Deadline PO, Qty
2. Pilih tipe: **Single Part** atau **Assembling** (isi WS Start & End Suffix)
3. Upload Drawing (opsional)
4. Klik **💾 Save Single Part** / **💾 Save Assembling**
5. ⚠️ **WAJIB:** Print QR Code setelah save — tempel di benda kerja sebelum turun ke Engineering

### 2. Engineering (`User: Andry / Fadhil / Pram / Heri`)
1. Scan QR → data Marketing auto-fill
2. Pilih event **Drawing Released**
3. Pilih status turun ke PPIC (Semua / Parsial)
4. Save to Sheets

### 3. PPIC (`User: Kiki`)
1. Scan QR → data auto-fill
2. Isi Qty Total → event **BOM Released** → **💾 Save to Sheets** ← **WAJIB dilakukan lebih dulu**
3. Setelah PPIC Info tersimpan → isi **List Raw Material** (nama, dimensi, qty, source)
4. **💾 Simpan List Material** → toast notifikasi sukses muncul
5. Material list otomatis tersync ke Produksi & QC

### 4. Production (Operator)
1. Scan QR → data + material list dari PPIC muncul
2. Isi Serah Terima (NO_WS child, tanggal, qty, status)
3. Pilih **OP Start** → Operator, Shift, Operation, Qty
4. Save to Sheets

### 5. QC (`User: Anton / Greg / Erik`)
1. Login QC (ID + password)
2. Scan QR parent WS
3. Set Qty & status per material (OK / Repair / Reject)
4. Pilih event → Remarks → Save
5. **Assembling:** satu save = parent + semua child WS tersimpan berurutan

### 6. Accounting & Finance (`User: Raynold / Veny / Lasma / Putri`)
1. Scan QR → data customer & WS auto-fill
2. Isi Qty Kirim, Upload SJ (opsional)
3. Event **Delivered** → Save to Sheets

---

## Arsitektur Teknis

### Single-File Structure
```
index.html
├── <head>        — CSS (inline, ~1600 lines)
├── <body>
│   ├── Topbar    — Clock, Theme toggle, ⌨️ Shortcuts, 📖 SOP, ⚙️ Settings
│   ├── Sidebar   — Navigation divisi + Tools
│   ├── Views     — 17 view panels (dashboard, Marketing, Engineering, ...)
│   ├── Modals    — cfgModal, howToUseModal, massModal, shortcutPanel
│   └── <script>  — 5 inline JS blocks (~4000 lines total)
│       ├── Block 1: Core logic (saveEvent, processScan, normalizeSheetRow, ...)
│       ├── Block 2: Nemesis motion engine (IIFE, 'use strict')
│       ├── Block 3: V32 animation engine (sparks, progress)
│       ├── Block 4: Camera/QR scanner
│       └── Block 5: Misc utils
```

### Data Flow
```
User Action
    │
    ▼
getPayload(dept)        ← baca semua field form
    │
    ▼
validatePayload(p)      ← validasi wajib
    │
    ▼
saveEvs(rows)           ← OPTIMISTIC: simpan localStorage (instan)
    │
    ├─── [QC Assembling] ──→ _jsonpRaw() × (1 parent + N children)
    │                         (sequential, confirmed, no doubles)
    │
    └─── [Other dept] ──────→ postToSheets(p) via fetch no-cors
                               (background, non-blocking)
```

### LocalStorage Keys
| Key | Isi |
|-----|-----|
| `kmil_v10_events` | Array semua events (semua divisi) |
| `kmil_v2_config` | Config: `webAppUrl` GAS |
| `kmil_v10_drawings` | Drawing thumbnails (base64) |
| `kmil_v10_mats` | Material list per WS (PPIC) |
| `kmil_v10_dt` | Downtime data |
| `kmil_v10_sj` | Surat Jalan data |
| `kmil_v10_st` | Serah Terima data |
| `kmil_v11_stock` | Item stock data |

### GAS Integration
Sistem menggunakan **Google Apps Script (GAS)** sebagai backend via JSONP:

```
Browser → GET https://script.google.com/.../exec
          ?action=jsonp
          &subAction=appendEvent
          &payload={"no_ws":"26030011","department":"Marketing",...}
          &callback=kmilCb_1234567
          ←
          kmilCb_1234567({"ok":true})
```

**Write path (non-QC):** `fetch` dengan `mode: 'no-cors'` — cepat (~50ms), resolves segera  
**Write path (QC Assembling):** `_jsonpRaw()` — menunggu callback GAS sebelum request berikutnya dikirim → **order terjamin, no duplicates**

### Fungsi Kunci

| Fungsi | Lokasi | Deskripsi |
|--------|--------|-----------|
| `saveEvent(dept)` | ~line 5051 | Simpan event: optimistic local → background GAS |
| `processScan(dept)` | ~line 4683 | Handle QR scan → autofill form |
| `normalizeSheetRow(r)` | ~line 4989 | Normalisasi field GAS (string/int, alias) |
| `fetchFromSheets()` | ~line 4980 | JSONP read dari GAS, normalize |
| `loadFromSheets()` | ~line 5037 | Manual sync: merge Sheets + local by `ts` |
| `doTrack()` | ~line 5442 | Track WS: search + show-all |
| `_renderTrackCards()` | ~line 5490 | Render card timeline WS |
| `ppicMatGateUpdate()` | ~line 4500 | Show/hide PPIC material gate warning |
| `addPpicMatRow()` | ~line 4243 | Gate-checked add material row |
| `savePpicMat()` | ~line 4300 | Save material list + toast notifikasi |
| `jumpToWs(ws)` | ~line 6850 | Dashboard → Track WS dengan WS tertentu |
| `_jsonpRaw()` | ~line 4965 | True JSONP write (menunggu GAS response) |
| `postToSheets(p)` | ~line 4976 | Wrapper: jsonpCall → GAS appendEvent |

### Navigation Architecture
`window.switchTool` di-wrap **3 kali**:
1. `switchTool_orig` — navigasi dasar + Track WS init
2. Nemesis wrapper — indikator animasi posisi
3. V32 wrapper — spark effects

`window.saveEvent` di-wrap **2 kali**:
1. Progress tracker wrapper (show/hide progress bar)
2. QC login check wrapper

---

## Setup & Konfigurasi

### 1. Deploy
File `index.html` adalah self-contained — tidak ada dependency eksternal selain font Google Fonts dan CDN icons. Cukup upload ke GitHub Pages atau hosting statis manapun.

### 2. Google Apps Script
Siapkan GAS script dengan endpoint `doGet(e)` yang mendukung:
- `action=jsonp` + `subAction=appendEvent` — append satu row event ke Sheets
- `action=jsonp` + `subAction=getEvents` — return semua events sebagai JSON
- `action=jsonp` + `subAction=generateWsRange` — generate range WS Assembling

Deploy sebagai **Web App** dengan akses: `Anyone, even anonymous`.

### 3. Hubungkan ke Sheets
1. Buka sistem di browser
2. Klik ⚙️ (Settings) di kanan atas
3. Paste URL Web App GAS ke field **Web App URL**
4. Klik Save

### 4. Sync Data Pertama
- Klik ⚙️ → **☁️ Load from Sheets** untuk menarik semua data existing

---

## Struktur Data

### Event Object
```json
{
  "ts": "2026-04-25T08:30:00.000Z",
  "department": "Marketing",
  "event": "Order Created",
  "no_ws": "26030011",
  "customer": "AHM",
  "no_po": "N309195",
  "part": "Nama Part",
  "qty": 5,
  "deadline": "2026-05-31",
  "ws_type": "ASSEMBLING",
  "ws_start": "26030011-01",
  "ws_end_suffix": 5,
  "user": "Dicky S",
  "remarks": "",
  "drawing_ref": "",
  "drawing_thumbnail": ""
}
```

### Material Item (PPIC)
```json
{
  "id": "m1714000000_ab12",
  "name": "26030011-01",
  "item_type": "ASSEMBLING",
  "material": "S45C",
  "len": "200", "wid": "50", "thk": "30", "dia": "",
  "qty": 1,
  "mat_source": "STOCK",
  "harden": "YES",
  "hrc": "58-62",
  "notes": ""
}
```

---

## Changelog

### V39 (Current)
- ✅ **Police line warning** "JANGAN LUPA PRINT QR CODE" di bawah Save Single Part dan Save Assembling
- ✅ **Tombol 📖 How to Use** di topbar — modal SOP flow chart per divisi
- ✅ **Fix div imbalance** — closing `</div>` yang hilang dari `mkt-form-single` dan `mkt-form-assy` (menyebabkan Engineering, PPIC, Production, QC, Accounting, Tools tidak muncul)

### V38
- ✅ **QC Assembling — no doubles:** ganti `postToSheets` (fetch no-cors) dengan `_jsonpRaw` untuk sequential write; setiap write menunggu konfirmasi GAS sebelum request berikutnya → eliminasi duplicate rows di Sheets
- ✅ **QC Assembling — true ordering:** parent → child-01 → child-02 → ... dijamin berurutan di Sheets
- ✅ **PPIC success toast:** `savePpicMat()` menampilkan toast notifikasi hijau saat material berhasil disimpan
- ✅ **PPIC info gate:** `addPpicMatRow()` dan `savePpicMat()` diblokir jika PPIC Info belum disave ke Sheets; warning banner `#ppic-mat-gate-warn` muncul otomatis

### V37
- ✅ Fix Track WS history kosong — type mismatch `no_ws` integer vs string dari Sheets
- ✅ Fix printLog WS history tidak muncul
- ✅ `normalizeSheetRow`: `.trim()` + `String()` pada semua ID field setelah aliasing
- ✅ Card WS auto-expanded (`ws-card-body open`)

### V36
- ✅ `doTrack()` empty query → show 60 WS terbaru (tidak silent return)
- ✅ `switchTool_orig` Track WS: reset mode + auto-call `doTrack()`
- ✅ `clearTrack()` re-call `doTrack()` setelah clear
- ✅ `_renderTrackCards()` sebagai shared helper

### V35
- ✅ Fix `jumpToWs()` — reset `trackMode='ws'` sebelum call `doTrack()` (bug: jika user sebelumnya di tab "BY CUSTOMER", track tidak muncul)

### V34
- ✅ T&C checkbox default `checked`; CSS light-mode override untuk T&C screen
- ✅ `normalizeSheetRow()` — lowercase semua field GAS, mapping alias, stringify key fields
- ✅ `fetchFromSheets()` limit 500 → 9999; normalize-before-filter
- ✅ `saveEvent()` — optimistic local save (instant), GAS push background
- ✅ Camera QR autofill timeout 260ms → 350ms; `trackMode` force `'ws'`

### V33
- ✅ Camera QR scanning via `getUserMedia`
- ✅ Auto-fill form dari QR scan
- ✅ Track WS dengan camera button

---

## Known Issues

| Issue | Status | Keterangan |
|-------|--------|------------|
| `view-coo` div imbalance -3 | Pre-existing | Ada di file original sebelum V33; tidak mempengaruhi fungsi karena view-coo tidak digunakan aktif |
| GAS slow response (>15s) | Edge case | `_jsonpRaw` timeout 15s; jika GAS lambat, QC assembling akan fallback ke `fetch no-cors` + 600ms delay |
| iOS Safari fetch no-cors | Mitigated | Untuk QC assembling sudah pakai `_jsonpRaw`; divisi lain pakai fetch no-cors yang bisa gagal di iOS → fallback `_jsonpRaw` via `.catch()` |

---

## File Info

```
File    : index.html
Lines   : ~8,257
Size    : ~650 KB
JS      : 5 inline script blocks, ~4,000 lines
CSS     : ~1,600 lines inline
Views   : 17 (dashboard + 6 divisi + 10 tools)
```

---

*PT KMIL Industrial Work Order System — maintained by internal IT*
