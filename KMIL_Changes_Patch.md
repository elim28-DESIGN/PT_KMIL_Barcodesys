# PT KMIL — Patch & Perubahan Kode
## V22 → V23 · 22 April 2026

Semua perubahan di bawah ini diterapkan pada **`index.html`** di repository `elim28-design/PT_KMIL_Barcodesys`.

---

## 1. 🔧 FIX — QC Incoming Check: Auto-generate event di sheet `kmil_events` jika WS = Assembling

### Masalah
Saat QC memilih event **Incoming Check** pada WS yang tipe-nya **Assembling**, event tidak ter-generate otomatis ke Google Sheets di sheet `kmil_events`.

### Lokasi Kode
Cari fungsi `saveQC` atau `handleQCSave` (biasanya di bagian `// === QC ===`).

### Fix
Dalam fungsi save QC, tambahkan pengecekan tipe WS. Jika `marketingType === 'assembling'` dan event = `Incoming Check`, loop semua child WS dan kirim event ke Sheets.

```javascript
// === PATCH: QC Incoming Check — auto-generate untuk WS Assembling ===
async function saveQCWithAssemblingCheck(wsNo, event, user, remarks, statusMap) {
  // Kirim event utama (parent WS)
  await saveQCToSheets(wsNo, event, user, remarks, statusMap);

  // Cek apakah WS ini adalah Assembling
  const wsData = getWSData(wsNo); // fungsi yang sudah ada — ambil data dari cache/Sheets
  if (wsData && wsData.marketingType === 'assembling' && event === 'Incoming Check') {
    const childList = wsData.childWS || []; // array child WS dari data assembling
    for (const childWS of childList) {
      await saveQCToSheets(childWS, event, user, remarks + ' [auto — parent: ' + wsNo + ']', statusMap);
    }
  }
}
```

**Cara integrasi:** Ganti pemanggilan `saveQCToSheets(...)` pada tombol "💾 Save to Sheets" di bagian QC menjadi `saveQCWithAssemblingCheck(...)`.

---

## 2. 📋 MARKETING — Tambah Customer "Tenaris SPIJ"

### Lokasi
Cari semua kemunculan `STOCKLIN` atau `— Pilih —` di dalam `<select id="mktCustomer">` (atau nama ID yang mirip). **Ada 7+ lokasi** di HTML yang perlu diupdate:

1. Marketing → dropdown Customer
2. Track WS → filter Customer
3. Drawing Vault → filter Customer
4. Bukti Pengiriman → filter Customer
5. Item Stock → dropdown Customer (2 tempat: input + filter)
6. Katalog QR → Customer Tag filter
7. Dashboard → filter Customer

### Perubahan di setiap `<select>` yang berisi customer list

**Tambahkan baris ini** setelah `<option value="STOCKLIN">STOCKLIN</option>`:

```html
<option value="TENARIS SPIJ">TENARIS SPIJ</option>
```

### Juga update array konstanta di JavaScript

Cari baris seperti:
```javascript
const CUSTOMERS = ['AHMAS', 'TEMOK', 'PIKE', 'POSCO', 'BAKRIE PIPE', 'EDICO', 'KOMATSU', 'UTPE', 'HPU', 'BUMA', 'STOCKLIN'];
```
Ganti menjadi:
```javascript
const CUSTOMERS = ['AHMAS', 'TEMOK', 'PIKE', 'POSCO', 'BAKRIE PIPE', 'EDICO', 'KOMATSU', 'UTPE', 'HPU', 'BUMA', 'STOCKLIN', 'TENARIS SPIJ'];
```

> ⚠️ Nama array mungkin berbeda (`customerList`, `custOptions`, dll). Cari dengan kata kunci `'STOCKLIN'` di JS untuk menemukan semua lokasi.

---

## 3. 👷 PRODUKSI — Tambah User "Johan"

### Lokasi
Cari `<select>` di bagian Production yang berisi user list (ada `DAYAT`, `HADRAH`, dll).

```html
<!-- SEBELUM -->
<select id="prdUser">
  <option value="">— Pilih —</option>
  <option value="DAYAT">DAYAT</option>
  <option value="HADRAH">HADRAH</option>
  <option value="NANA">NANA</option>
  <option value="AMSORI">AMSORI</option>
  <option value="AGUS">AGUS</option>
  <option value="HENDRIYANA">HENDRIYANA</option>
  <option value="ANDREAS">ANDREAS</option>
  <option value="NANANG">NANANG</option>
  <option value="WAHYUDI">WAHYUDI</option>
  <option value="DAVID">DAVID</option>
  <option value="custom">Custom...</option>
</select>
```

```html
<!-- SESUDAH — tambahkan JOHAN sebelum Custom -->
<select id="prdUser">
  <option value="">— Pilih —</option>
  <option value="DAYAT">DAYAT</option>
  <option value="HADRAH">HADRAH</option>
  <option value="NANA">NANA</option>
  <option value="AMSORI">AMSORI</option>
  <option value="AGUS">AGUS</option>
  <option value="HENDRIYANA">HENDRIYANA</option>
  <option value="ANDREAS">ANDREAS</option>
  <option value="NANANG">NANANG</option>
  <option value="WAHYUDI">WAHYUDI</option>
  <option value="DAVID">DAVID</option>
  <option value="JOHAN">JOHAN</option>
  <option value="custom">Custom...</option>
</select>
```

---

## 4. ⚙️ PRODUKSI — CNC diganti 2 item: "TC" dan "MC"

### Lokasi
Cari `<select>` Operation di bagian Production. Cari option `CNC`.

```html
<!-- SEBELUM -->
<option value="CNC">CNC</option>
```

```html
<!-- SESUDAH — hapus CNC, tambahkan TC dan MC -->
<option value="TC">TC</option>
<option value="MC">MC</option>
```

**Urutan yang dianjurkan** (sesuai urutan existing):
```html
<option value="">— Pilih —</option>
<option value="Bubut">Bubut</option>
<option value="TC">TC</option>
<option value="MC">MC</option>
<option value="Milling">Milling</option>
<option value="Grinding">Grinding</option>
<option value="Konstruksi">Konstruksi</option>
<option value="BW">BW</option>
<option value="Induction">Induction</option>
<option value="Assy">Assy</option>
<option value="Lasercut">Lasercut</option>
<option value="HBM">HBM</option>
<option value="PLANNER">PLANNER</option>
<option value="STIK">STIK</option>
<option value="PAINTING">PAINTING</option>
<option value="Subcont (→ PPIC)">Subcont (→ PPIC)</option>
<option value="custom">Custom...</option>
```

> ⚠️ Jika ada validasi / label di Flow Diagram atau bagian lain yang menyebut "CNC", update juga ke "TC/MC".

---

## 5. 📱 FIX iOS / Android — "Request Blocked"

### Masalah
Fetch ke Google Apps Script diblok di iOS/Android karena **CORS policy** + **mixed content** atau **header `X-Frame-Options`**. Browser mobile (terutama Safari iOS) lebih ketat dalam menangani request ke URL `script.google.com`.

### Fix — Tambahkan Header & Mode CORS yang Benar

Cari semua `fetch(GAS_URL` atau `fetch(scriptUrl` di JavaScript, lalu pastikan setiap fetch menggunakan konfigurasi ini:

```javascript
// SEBELUM (kemungkinan)
const res = await fetch(GAS_URL, {
  method: 'POST',
  body: JSON.stringify(payload)
});

// SESUDAH — tambahkan mode, cache, credentials
const res = await fetch(GAS_URL, {
  method: 'POST',
  mode: 'no-cors',          // ← WAJIB untuk GAS dari browser mobile
  cache: 'no-cache',
  headers: {
    'Content-Type': 'text/plain;charset=UTF-8'  // ← no-cors hanya izinkan text/plain
  },
  body: JSON.stringify(payload)
});
```

> ⚠️ Dengan `mode: 'no-cors'`, response akan jadi **opaque** (tidak bisa dibaca). Jika aplikasi butuh konfirmasi dari GAS, gunakan teknik redirect:

```javascript
// Alternatif: pakai GET + URLSearchParams (lebih kompatibel di mobile)
const params = new URLSearchParams({ data: JSON.stringify(payload) });
const res = await fetch(`${GAS_URL}?${params.toString()}`, {
  method: 'GET',
  mode: 'no-cors'
});
```

### Fix di sisi Google Apps Script (GAS)

Pastikan fungsi `doPost` dan `doGet` di GAS mengembalikan header CORS:

```javascript
// Di Google Apps Script — tambahkan ini di doPost dan doGet
function doPost(e) {
  const output = ContentService.createTextOutput();
  output.setMimeType(ContentService.MimeType.JSON);
  
  // ... proses data ...
  
  output.setContent(JSON.stringify({ status: 'ok' }));
  return output;
}

// Tambahkan doGet untuk handle preflight / GET fallback
function doGet(e) {
  const payload = JSON.parse(e.parameter.data || '{}');
  // ... proses data ...
  return ContentService.createTextOutput(JSON.stringify({ status: 'ok' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

---

## Ringkasan Semua Perubahan

| # | Area | Perubahan | File |
|---|------|-----------|------|
| 1 | QC | Fix Incoming Check auto-generate child WS Assembling ke Sheets | `index.html` (JS) |
| 2 | Marketing | Tambah customer "TENARIS SPIJ" di semua dropdown | `index.html` (HTML + JS) |
| 3 | Produksi | Tambah user "JOHAN" di list operator | `index.html` (HTML) |
| 4 | Produksi | Ganti "CNC" → "TC" dan "MC" di list operasi | `index.html` (HTML) |
| 5 | iOS/Android | Fix request blocked — gunakan `mode: 'no-cors'` + perbaiki GAS | `index.html` (JS) + GAS |

---

## Cara Deploy

1. Edit `index.html` di GitHub langsung (atau clone → edit → push)
2. GitHub Pages akan auto-deploy dalam ~1-2 menit
3. Hard refresh di browser: `Ctrl+Shift+R` (desktop) atau clear cache di mobile
4. Test QR scan di iOS Safari dan Android Chrome setelah fix no-cors
