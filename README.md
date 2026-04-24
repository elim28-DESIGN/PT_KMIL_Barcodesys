# PT KMIL — Work Order System V31

> **Multi-division, single-file web application** for managing work orders (WS) from Marketing through Engineering, PPIC, Production, QC, and Accounting — with QR code generation, real-time tracking, Google Sheets sync, and PWA support.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Divisions & Modules](#divisions--modules)
- [Tools](#tools)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Google Sheets Integration](#google-sheets-integration)
- [User Accounts](#user-accounts)
- [Customers](#customers)
- [Production Operations](#production-operations)
- [Data Storage](#data-storage)
- [Version History](#version-history)
- [File Structure](#file-structure)

---

## Overview

PT KMIL System is a **zero-dependency, single HTML file** industrial work order management system. It covers the full lifecycle of a manufacturing work order — from initial order creation in Marketing, through Engineering drawing release, PPIC material planning, Production tracking, Quality Control, and final delivery confirmation.

The system works **offline-first** using `localStorage`, with optional sync to Google Sheets via a Google Apps Script (GAS) backend. It includes QR code generation for every Work Order (WS), barcode scanner support, and a print-ready report system.

```
Order Created → Drawing Released → BOM Released → Production → QC → Delivered
   Marketing       Engineering          PPIC          Production    QC    Accounting
```

---

## Features

### Core
- ✅ **Single HTML file** — no build step, no server required, open directly in browser
- ✅ **PWA ready** — installable on mobile and desktop (iOS, Android, Chrome)
- ✅ **Offline-first** — all data stored in `localStorage`, syncs to Sheets when online
- ✅ **QR Code generation** for every Work Order (WS number encoded)
- ✅ **Barcode scanner support** — plug in a USB scanner or scan via camera
- ✅ **Dark / Light theme** toggle
- ✅ **Mobile responsive** with bottom navigation bar

### Work Order Types
- 📦 **Single Part** — standard one-part work order
- 🔩 **Assembling** — multi-child WS (e.g. `26003131-01`, `-02`, ...) with per-part production tracking

### Tracking & Search
- 🔍 Track any WS by **WS number**, **Customer**, or **PO number**
- 🔍 Full support for **child WS tracking** — search `26003131-01` shows both parent and child events
- 📊 Timeline view of all events per WS
- 🚨 Deadline expiry warnings and color-coded status

### Print
- 🖨️ **Work Order Event Log** — full history per WS including Assembling Parts table
- 🖨️ **Production Assembling Parts Log** — standalone print per WS with columns: NO_WS Child, Part/Item, Operator, Operation, Shift, Qty, Status
- 🖨️ **QR Code** — single or batch print all WS QR codes
- 🖨️ **Deadline List** — sorted by nearest/furthest deadline
- 🖨️ **Serah Terima (Handover) Log**
- 🖨️ **Downtime Machine Grid** — monthly machine status calendar
- 🖨️ **Management Dashboard Report** — executive summary CSV + print

---

## Divisions & Modules

### 📋 Marketing
Creates and manages work orders.

| Field | Description |
|-------|-------------|
| Customer | Select from master list or custom |
| NO_WS | Work Order number (e.g. `26020017`) |
| NO_PO | Purchase Order number |
| Part Name | Description of part |
| Deadline PO | Due date — shown in red when expired |
| Qty | Quantity (with confirmation warning) |
| Remarks | Optional notes |

**Events:** `Order Created`, `Drawing Tambahan`, `NO DRAWING`

**Features:**
- Tabs for **Single Part** vs **Assembling** order types
- Assembling: auto-generates child WS range (e.g. `-01` to `-05`) with qty per child
- Drawing upload with thumbnail preview and Vault storage
- QR Code Finder — search, select, and print any WS QR with child WS qty table
- Save warning modal — confirms all fields before submitting

---

### ⚙️ Engineering
Drawing approval and release to PPIC.

**Events:** `Wait Approval Drawing`, `Drawing Released`, `Drawing Tambahan`

**Features:**
- Scan/type WS to auto-fill customer, PO, deadline, part name
- Auto-detects WS type (Single Part or Assembling) from Marketing data
- Drawing Tambahan: revision number selector
- **WS Turun ke PPIC** — track which child WS drawings have been released (checklist with progress bar)
- Users: Andry, Fadhil, Pram, Heri

---

### 📦 PPIC
Planning, BOM, and raw material list management.

**Events:** `BOM Released`, `Drawing Tambahan`, `Revision Drawing`, `Material Problem`

**Features:**
- Scan/type WS to auto-fill from Marketing
- **Raw Material List table** — per-item entry with: Name, Item Type, Place Process, Material grade, Dimensions (L×W×T mm), Qty, Material Source, Harden/HRC
- Material list syncs automatically to Production and QC
- WS Reference panel shows all child WS for Assembling orders
- User: Kiki (locked)

**Material fields:**
```
Name | Item Type | Place Process | Material | Ø Dia | L × W × T | Qty | Source | Harden | HRC | Notes
```

**Material types:** SS400, SKD11, SKD61, S45C, SCM440, SPHC, AB2, SUS304, SUS316, SUS201, SUS410, PIPE, AS PUTIH, STD PART, FCD60, FCD70, CASTING, RUBBER, PEJAL, CUSTOM

**Place/Process codes:** CDR, BTG, JBBK, SETU, SUBCONT, MCP, CPM, MBW, CUSTOM

---

### 🏭 Production
Operation tracking, assembling part logging, and handover (serah terima).

**Events:** `Production - OP Start`, `Revision Drawing`, `Material Problem`

**Features:**
- Auto-detects Single Part vs Assembling from Marketing/PPIC data
- **Single Part mode:** Shift, Operation, Qty
- **Assembling mode:** per-child-WS table with NO_WS Child, Part/Item, Operator, Operation, Shift, Qty, Status
- Material list from PPIC shown for reference
- **Serah Terima (S/T)** log — record handover date, qty, and status (Parsial/Complete) per child WS
- Print Assy Parts — standalone print of assembling production log
- Operators: DAYAT, HADRAH, NANA, AMSORI, AGUS, HENDRIYANA, ANDREAS, NANANG, WAHYUDI, DAVID, JOHAN

**Operations:**
```
BUBUT - Manual | CNC - TC | CNC - MC
MILLING - Manual | MILLING - HBM | MILLING - Planner | MILLING - BOKO
GRINDING - Cylindrical | GRINDING - Grinding | GRINDING - Surface
B/W | INDUCTION | WELDING | LASERCUT | SUBCONT (→ PPIC)
```

---

### ✅ QC (Quality Control)
Inspection and final quality gate — **login protected**.

**Events:** `Incoming Check`, `QC OK`, `Repair Required`, `Reject / NG`, `Special Acceptance`

**Features:**
- Login required: User ID + Password
- Scan/type WS — auto-detects Single Part or Assembling
- Material list from PPIC displayed with per-item qty and status
- Parsial QC qty — forces Remarks if qty < full qty
- Event warnings for Reject/NG and Repair Required
- Users (ID : Password): `erik : qc1`

---

### 🚚 Customer & A/F (Accounting)
Delivery confirmation and finance.

**Events:** `Customer - Delivered`

**Features:**
- Scan WS to auto-fill customer, PO, part name
- Full / Parsial delivery selection
- Surat Jalan (SJ) photo/file upload
- Delivery proof stored in Bukti Pengiriman vault
- Users: Raynold, Veny, Lasma, Putri

---

## Tools

| Tool | Description |
|------|-------------|
| 📡 **Track WS** | Search by WS number, Customer, or PO. Supports child WS (e.g. `26003131-01`). Shows full event timeline, material list, and print button per WS |
| 🔧 **Downtime Mesin** | Monthly machine status grid (OK / Problem / Off). Click cells to toggle. Save to localStorage. Print monthly report |
| 🔄 **Flow Diagram** | SVG diagram of the full work order process from Marketing to delivery |
| 🏷️ **Katalog QR WS Assembling** | Browse and print QR codes for all Assembling WS and their child WS |
| 📊 **Item Stock** | Simple stock count tracker per customer / WS / item name |
| 🚫 **Item Reject / NG** | View and filter all Reject and Repair events across all WS |
| 📐 **Drawing Vault** | All uploaded drawings — filterable by customer or WS. Click to preview |
| 📮 **Bukti Pengiriman** | All delivery proof photos (SJ) from Accounting |
| 👔 **Management Dashboard** | Executive overview — login protected (`elim : coo`). KPI grid, pipeline chart, WS table with status, reject summary, top customers. Export CSV |

---

## Tech Stack

| Component | Detail |
|-----------|--------|
| **Runtime** | Pure HTML + Vanilla JavaScript (ES2020) |
| **Styling** | CSS custom properties, no framework |
| **Fonts** | Barlow Condensed, Barlow, IBM Plex Mono (Google Fonts) |
| **QR Code** | [qrcodejs](https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js) via CDN |
| **Charts** | HTML5 Canvas (custom bar + pie charts) |
| **Storage** | `localStorage` (offline-first) |
| **Backend** | Google Apps Script (optional, for Sheets sync) |
| **PWA** | Web App Manifest meta tags, installable |

---

## Getting Started

### 1. Open the file
```bash
# No installation required — just open in a modern browser
open V31_Industrial.html
```

Or host it on any static file server, GitHub Pages, or Google Drive.

### 2. Configure Google Sheets (optional)
1. Click **⚙️** (Settings) in the top-right
2. Paste your Google Apps Script Web App URL
3. Click **Save**
4. Click **☁️ Load from Sheets** to pull existing data

> Without GAS URL configured, all data is saved locally only. The "Save to Sheets" button will fail gracefully without breaking local save.

### 3. Start using
1. Go to **Marketing** → fill in customer, WS number, PO, part, deadline
2. Click **💾 Save** → data appears in the right-panel timeline
3. Each division scans the WS QR code to auto-fill their form
4. Track progress anytime in **Track WS**

---

## Google Sheets Integration

The system communicates with a Google Apps Script (GAS) Web App deployed from a Google Sheet. The GAS endpoint handles:

| Action | Description |
|--------|-------------|
| `listEvents` | Pull all events from Sheet (JSONP) |
| `saveEvent` | Push a new event row |
| `generateWsRange` | Bulk-create child WS rows for Assembling orders |

**JSONP** is used for cross-origin requests (no CORS issue). The payload structure per event:

```json
{
  "ts": "2026-04-24T...",
  "department": "Marketing",
  "event": "Order Created",
  "status": "CREATED",
  "no_ws": "26020017",
  "no_po": "N309195",
  "customer": "KPI",
  "part": "ROLL F3 YN 323",
  "deadline": "2026-06-30",
  "qty": 5,
  "user": "Dicky S",
  "ws_type": "SINGLE_PART",
  "remarks": ""
}
```

For Assembling (Production), additional fields:
```json
{
  "assy_parts": [
    { "ws": "26003131-01", "partName": "Body", "operator": "DAYAT", "op": "CNC - TC", "shift": "Shift 1", "qty": 1, "status": "DONE" }
  ],
  "assy_parts_summary": "26003131-01:DAYAT:CNC - TC:1"
}
```

---

## User Accounts

| Division | User | Credential |
|----------|------|------------|
| Marketing | Dicky S | *(no login — auto-assigned)* |
| Engineering | Andry, Fadhil, Pram, Heri | *(select from dropdown)* |
| PPIC | Kiki | *(locked — auto-assigned)* |
| Production | Select from operator list | *(dropdown)* |
| QC | Erik | `erik` / `qc1` |
| Accounting | Raynold, Veny, Lasma, Putri | *(select from dropdown)* |
| Management Dashboard | Elim | `elim` / `coo` |

---

## Customers

The master customer list (A–Z):

| | | | |
|-|-|-|-|
| AHM | ASTEMO | BAKRIE PIPE | BEKAERT |
| BINA PERTIWI | BUMA | EDICO | HPU |
| KE | KMIL | KOMATSU | KPI |
| KPP | MBW | MCP | PINDAD |
| POSCO | SANKEI | SEAPI | STOCKLIN |
| TENARIS SPIJ | UNION FOODS | UTPE | *(Custom...)* |

---

## Production Operations

```
Bubut    : BUBUT - Manual
CNC      : CNC - TC, CNC - MC
Milling  : MILLING - Manual, MILLING - HBM, MILLING - Planner, MILLING - BOKO
Grinding : GRINDING - Cylindrical, GRINDING - Grinding, GRINDING - Surface
Other    : B/W, INDUCTION, WELDING, LASERCUT, SUBCONT (→ PPIC)
```

---

## Data Storage

All data is persisted in `localStorage` under these keys:

| Key | Contents |
|-----|----------|
| `kmil_v10_events` | All work order events (main data array) |
| `kmil_v2_config` | App config (GAS URL, theme) |
| `kmil_v10_drawings` | Drawing thumbnails and metadata per WS |
| `kmil_v10_mats` | PPIC material lists per WS |
| `kmil_v10_dt` | Downtime machine grid data |
| `kmil_v10_sj` | Surat Jalan / delivery proof per WS |
| `kmil_v10_st` | Serah Terima (handover) data per WS |
| `kmil_v11_stock` | Item stock entries |
| `kmil_v13_eng_assy` | Engineering per-child-WS drawing release checklist |

> ⚠️ `localStorage` is browser and origin scoped. If you open the file from a different path or browser, data will not carry over. Use **Sheets sync** for persistence across devices.

---

## Version History

| Version | Changes |
|---------|---------|
| **V31** | Fix `const` inside unbraced `switch case` (Engineering save buttons broken). Production Assembling Parts print (`🖨️ Print Assy Parts`). Assy parts detail in Work Order Event Log print. Timeline shows assy parts preview badge. |
| **V31** (debug) | Track WS: child WS support (`26003131-01`). Customer filter case-insensitive fix. Child WS shows parent events when no own events exist. Child badge in track card. |
| **V31** (revisions) | Management Dashboard (renamed from COO). Customer list A–Z + 10 new customers. QR Finder print shows child WS qty table. Track WS / Scan support for child WS. Downtime print button. |
| **V30** | Initial multi-division release. QR code system. Google Sheets sync. Assembling order type. PPIC material list. QC login gate. Drawing Vault. Serah Terima log. |

---

## File Structure

```
V31_Industrial.html          ← Entire application (single file)
README.md                    ← This file
```

The application is intentionally a **single self-contained HTML file** for maximum portability — no npm, no bundler, no dependencies to install. Deploy anywhere that can serve a static file.

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `/` | Focus scan / search input |
| `Esc` | Close dialog or lightbox |
| `?` | Show shortcuts panel |
| `Alt + 1` | Go to Marketing |
| `Alt + 2` | Go to Engineering |
| `Alt + 3` | Go to PPIC |
| `Alt + 5` | Go to Production |
| `Alt + 6` | Go to QC |
| `Alt + T` | Go to Track WS |
| `Alt + D` | Go to Dashboard |

Barcode scanner (USB HID) is supported — scan into any active input field or anywhere on the page (auto-routes to visible scan input).

---

## License

Internal tool — PT KMIL. All rights reserved.
