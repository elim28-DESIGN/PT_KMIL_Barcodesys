# PT KMIL — ERP Barcode System V44

> Industrial-grade, single-file web ERP for shop-floor tracking.  
> Runs entirely in the browser — no server required. Google Sheets as the backend.

---

## Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML/CSS/JS — single file, zero build step |
| Font | Inter (UI) + JetBrains Mono (codes, timestamps) |
| Backend | Google Apps Script (GAS) — deployed as Web App |
| Storage | Google Sheets + `localStorage` (offline cache) |
| QR Scan | jsQR (camera) + barcode scanner (USB/BT) |

---

## Features

### Divisions & Flow
All work orders follow a strict sequential flow:

```
Marketing → Engineering → PPIC → Production → QC → Accounting
```

Each division can only fill their form after the previous division has an event saved. Validation is real-time against Google Sheets.

| Division | Key Capabilities |
|---|---|
| **Marketing** | Create WS (Single Part / Assembling range), upload drawing, set deadline |
| **Engineering** | Drawing release, NC report, auto-sync child WS from Marketing |
| **PPIC** | Material planning, BOM entry, auto-generate from Marketing data |
| **Production** | OP events, routing process table, assembly parts, material check |
| **QC** | Bulk inspection per material, pass/fail/rework per item, SJ upload |
| **Accounting** | Final save, SJ confirmation, delivery tracking |

### Other Features
- **Catalog / Vault** — searchable archive of all WS events from Sheets
- **Dashboard** — live status per WS across all divisions
- **Stock Tracker** — simple in/out per material
- **DT (Downtime) Tracker** — machine-level downtime logging
- **Camera QR Scanner** — works on iOS/Android without native app
- **Offline-first** — all data saved to `localStorage`, sync to Sheets on demand
- **Light/Dark mode** — system preference, T&C always light
- **Responsive** — mobile-friendly with bottom nav

---

## Setup

### 1. Google Apps Script

1. Go to [script.google.com](https://script.google.com) → New project
2. Paste the entire contents of `Code.gs` (V44)
3. Set your Spreadsheet ID:
   ```js
   var SPREADSHEET_ID = '1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms';
   ```
4. Run `setupSheets()` once from the editor to create sheet tabs
5. **Deploy → New deployment → Web App**
   - Execute as: **Me**
   - Who has access: **Anyone**
6. Copy the deployment URL

### 2. Website Settings

1. Open `index.html` in a browser
2. Click **⚙️ Settings** (top-right)
3. Paste the GAS URL into **Google Apps Script URL**
4. The **GAS URL Routing** field can use the same URL
5. Click Save

### 3. Sheet Structure

Two sheets are auto-created by `setupSheets()`:

**`Events`** — all division events

| Column | Description |
|---|---|
| `ts` | ISO timestamp |
| `department` | Marketing / Engineering / PPIC / Production / QC / Accounting |
| `event` | Event name (WS Created, Drawing Released, etc.) |
| `no_ws` | Work order number |
| `customer`, `part`, `operation` | WO details |
| `status` | CREATED / IN PROGRESS / DONE / etc. |
| ... | See `HEADERS_EVENTS` in Code.gs for full list |

**`RoutingProcess`** — production routing steps

| Column | Description |
|---|---|
| `no_ws` | Work order |
| `routing_seq` | Step sequence number |
| `routing_op` | Operation name (BUBUT, MILLING, etc.) |
| `routing_dur` | Duration estimate |
| `routing_status` | PENDING / IN PROGRESS / DONE |

---

## File Structure

```
index.html          ← Main ERP app (single file, self-contained)
Code.gs             ← Google Apps Script backend
README.md           ← This file
```

---

## GAS subActions

| subAction | Direction | Description |
|---|---|---|
| `appendEvent` | Write | Save one event to `Events` sheet |
| `appendRouting` | Write | Save routing step(s) to `RoutingProcess` |
| `listEvents` | Read | Return all rows from `Events` as JSON |
| `listRouting` | Read | Return all rows from `RoutingProcess` as JSON |
| `generateWsRange` | Write | Batch-create Assembling child WS in `Events` |

All communication uses **JSONP** (no CORS issues, works on mobile Safari).

---

## Changelog

### V44 (Current)
- Font changed to **Inter** (UI) + **JetBrains Mono** (codes) — Segoe UI family, readable on all screens
- Event names, NO_WS, division labels → **bold** in all tables and timeline
- Timestamps → Inter 11.5px medium (no more monospace timestamps)
- Table borders → **2px** throughout all divisions
- Buttons → Inter semibold, industrial flat style
- T&C modal → **always light mode**, Inter body font
- `saveRoutingToSheets` → fixed to use `jsonpCall` instead of `fetch no-cors` (data now actually reaches Sheets)
- `appendRouting` added to `isWrite` in `jsonpCall`
- Status messages (`smsg`) → Inter font for readability
- Version bumped from V42 → V44

### V42.1 (GAS)
- Added `listRouting` handler
- Fixed `handleListEvents` to not skip rows with empty `ts`
- `appendRouting` supports batch (array) and single row
- All extended Marketing fields (`ws_start`, `ws_end_suffix`) included in `listEvents`

---

## Browser Support

| Browser | Support |
|---|---|
| Chrome / Edge 90+ | ✅ Full |
| Safari 15+ (iOS/macOS) | ✅ Full incl. camera scan |
| Firefox 88+ | ✅ Full |
| Samsung Internet | ✅ |

> **Note:** Requires a live GAS deployment URL for Sheets sync. Offline mode (localStorage only) works without internet.

---

## License

Internal tool — PT KMIL. Not for redistribution.
