# PT KMIL — Barcode Work Order System `V33`

<div align="center">

![Version](https://img.shields.io/badge/version-V33-8b5cf6?style=for-the-badge)
![Camera](https://img.shields.io/badge/Camera-QR%20Scan-10b981?style=for-the-badge)
![Platform](https://img.shields.io/badge/platform-Web%20%7C%20iOS%20%7C%20Android-8b5cf6?style=for-the-badge)
![Status](https://img.shields.io/badge/status-Production--Ready-10b981?style=for-the-badge)

**Single-file PWA · Multi-division Work Order · Real-time Camera QR Scan · Auto-fill**

</div>

---

## 🆕 What's New in V33

### 📡 Track WS — Camera QR Scan
Camera scan button added to **Track WS** search. Tap 📷 → scan QR → auto-loads full history.

### 🧮 Smart QR Formula Parser
Handles any QR format from any printer/scanner system:

| Input Format | Example | Result |
|---|---|---|
| Plain WS | `26020036` | `26020036` |
| Child WS | `26020036-01` | `26020036-01` |
| Pipe-delimited | `26020036\|STOCKLIN\|PO-123` | `26020036` |
| JSON encoded | `{"ws":"26020036"}` | `26020036` |
| URL params | `ws=26020036&cust=STOCKLIN` | `26020036` |
| With prefix | `WS:26020036` | `26020036` |
| Scanner noise | `26020036\x00\r` | `26020036` |

### ✨ Auto-fill Visual Feedback
When QR is scanned from camera in any division:
- Form fields **flash purple** one by one (staggered 60ms each)
- Scan result box border glows purple  
- Toast: `✓ Auto-fill dari QR scan berhasil`

### ⌨️ Physical Barcode Scanner Buffer
Zebra, Honeywell, and other hardware scanners type characters in `< 60ms` bursts. V33 detects rapid keystrokes and shows visual feedback before the Enter is sent.

### 🐛 Critical Bug Fix: `_dept` Race Condition
**V32 bug:** Camera close set `_dept = null` at same time `processScan(_dept)` was called → auto-fill never worked on iOS.  
**V33 fix:** `var dept = _dept` captured before `closeCamScan()`.

---

## 📷 Camera QR Scan — All Divisions

| Division | Camera Available | Auto-fills |
|---|---|---|
| Engineering | ✅ | NO_WS, Customer, NO_PO, Deadline, Part Name |
| PPIC | ✅ | NO_WS, Customer, NO_PO, Deadline, Part Name |
| Production | ✅ | NO_WS, Customer, Part Name |
| QC | ✅ | NO_WS, Customer, Part Name |
| Accounting | ✅ | NO_WS, Customer, NO_PO, Part Name |
| **Track WS** | ✅ **New V33** | Auto-search history |

### How to use on iOS (Safari)
1. Open system in **Safari** (not Chrome/Firefox — iOS only allows camera via Safari)
2. Navigate to any division (Engineering, PPIC, Production, QC, Accounting) or Track WS
3. Tap the **📷** button in the scan bar
4. Allow camera when prompted → **iOS: Settings → Safari → Camera → Allow**
5. Point camera at QR Code on Work Order sheet
6. Detected → fields flash purple → data auto-filled

### How to use on Android (Chrome)
1. Open system in **Chrome**
2. Tap **📷** button
3. If prompted: tap lock icon in address bar → Camera → Allow
4. Point camera at QR Code
5. Vibration + green flash → auto-filled

---

## 🔌 Deploy to GitHub Pages

The live site at `elim28-design.github.io/PT_KMIL_Barcodesys/` is running **V31**.  
To deploy V33:

```bash
# Option A: Replace as index.html
cp V33_Industrial_Purple_kmil.html index.html
git add index.html
git commit -m "deploy: V33 — camera QR scan, auto-fill, iOS/Android fixes"
git push origin main

# Option B: Deploy as versioned file
git add V33_Industrial_Purple_kmil.html
git commit -m "feat: V33 release"
git push origin main
# Then update GitHub Pages source in Settings
```

> Camera requires **HTTPS** — GitHub Pages provides this automatically ✓

---

## 🐛 Bug Fixes (V32 → V33)

| # | Bug | Fixed |
|---|---|---|
| 1 | `e.target.closest is not a function` crash (SVG/text nodes) | ✅ |
| 2 | `_dept` race condition — autofill never fired on iOS camera | ✅ |
| 3 | `100dvh` not supported on iOS < 15.4 | ✅ |
| 4 | Missing `-webkit-backdrop-filter` on 5 modals | ✅ |
| 5 | Input `font-size < 16px` → iOS auto-zoom on tap | ✅ |
| 6 | `-webkit-appearance:none` broke native date pickers | ✅ |
| 7 | No `visibilitychange` → camera drains battery on tab switch | ✅ |
| 8 | Complex `getUserMedia` constraints crash on some devices | ✅ Tiered fallback |
| 9 | No haptic/visual feedback when QR decoded | ✅ Vibrate + purple flash |
| 10 | Physical scanner sends noise chars (`\x00`) in WS value | ✅ Stripped in parseQRData |

---

## 🏗️ Architecture

```
V33_Industrial_Purple_kmil.html  (~570 KB, single file)
│
├── CSS (5 blocks)
│   ├── Design System + Purple Palette
│   ├── Components (topbar, sidebar, cards, forms)
│   ├── T&C Screen + Police Line Warning
│   ├── V33 Motion Engine (animations, sparks)
│   └── Camera QR Scanner
│
└── JS (5 blocks)
    ├── Splash + T&C Logic
    ├── App Core (events, scan, save, sync)
    ├── Animation Engine (particles, gear, piston)
    ├── V33 Industrial Runtime (uptime, HUD, ripple)
    └── Camera Engine (openCamScan, jsQR loop,
                       parseQRData, flashAutofillFields,
                       scannerBuffer, visibilitychange)
```

---

## 📦 Dependencies

| Library | Version | CDN | Usage |
|---|---|---|---|
| jsQR | 1.4.0 | jsDelivr | Decode QR from camera frame |
| qrcodejs | 1.0.0 | Cloudflare | Generate QR Code images |
| Barlow Condensed | — | Google Fonts | Display font |
| IBM Plex Mono | — | Google Fonts | Monospace / data |

---

## 📱 Browser Support

| Browser | Camera | Auto-fill | PWA |
|---|---|---|---|
| **Safari iOS ≥ 14.3** | ✅ | ✅ | ✅ A2HS |
| **Chrome Android** | ✅ | ✅ | ✅ |
| Chrome Desktop | ✅ | ✅ | ✅ |
| Samsung Internet | ✅ | ✅ | ✅ |
| Firefox | ⚠️ | ✅ | ❌ |

---

## 🔄 Changelog

### V33 — April 2026
- ✅ Camera QR scan for **Track WS** (was missing)
- ✅ Smart QR formula parser (7 formats + noise stripping)
- ✅ Auto-fill visual flash on form fields
- ✅ Scan result border glow on QR detect
- ✅ Physical barcode scanner buffer (Zebra/Honeywell)
- ✅ `_dept` race condition fixed (critical iOS bug)
- ✅ `-webkit-appearance:none` excludes date/time pickers
- ✅ Better iOS/Android permission error messages
- ✅ `e.target.closest` crash fixed (SVG/text node events)

### V32 — April 2026  
- Purple Industrial Theme full migration
- T&C Compliance Screen post-splash
- Police Line Warnings (Production & PPIC)
- Spark Particle System + Rotating Gear
- iOS/Android bug fixes (18 total)
- Camera QR Scanner (all 5 divisions)

### V31 (Live)
- QR Finder, COO Dashboard, Assembling WS
- Serah Terima system, Material List BOM

---

```
System Design & Development
  Elim K — PT KMIL Digital Operations
  2024–2026 · Bersatu Kita Hebat
```
