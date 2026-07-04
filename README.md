# PT KMIL — ERP System

Single-file web app (HTML/CSS/JS) untuk mengelola alur kerja manufaktur PT KMIL — mulai dari Marketing (order masuk) sampai Accounting (closing), dengan Supabase sebagai backend penyimpanan data.

**Versi saat ini:** `V45.3`
**Live app:** https://elim28-design.github.io/PT_KMIL_Barcodesys/

---

## 📋 Divisi yang didukung

| Divisi | Fungsi utama |
|---|---|
| 📋 Marketing | Input order baru (Single Part / Assembling), generate & print QR Code, Routing Process |
| 🛠️ Engineering | Review drawing, approve WS |
| 📦 PPIC | Material planning per WS |
| 🏭 Production | Serah terima antar proses, Routing Process, tracking downtime |
| ✅ QC | Inspeksi & bulk item check |
| 💰 Accounting | Closing WS |
| 📡 Track WS | Cari histori event per NO_WS (scan manual / kamera) |

Setiap divisi (kecuali Marketing & Dashboard) punya login gate dengan kredensial masing-masing.

---

## ⚙️ Arsitektur teknis

- **Frontend:** 1 file `index.html` — HTML + CSS + vanilla JS, tanpa build step, tanpa dependency eksternal (selain font Inter & JetBrains Mono).
- **Backend:** Supabase (REST API) untuk penyimpanan event/data utama. Sebagian data lama masih memakai Google Apps Script (GAS) dengan JSONP untuk menghindari CORS — **selalu redeploy GAS sebagai versi baru** setiap kali endpoint diubah, karena URL Web App lama tidak otomatis update.
- **Local cache:** `localStorage` dipakai untuk cache event, routing state per WS, dan draft form sebelum tersimpan ke Supabase.
- **QR Scanning:** kamera browser (`getUserMedia`) dengan fallback input manual, dipakai di semua divisi lewat tombol 📷 di panel "📡 SCAN QR / NO_WS".

---

## 🆕 Changelog

### V45.3 — Marketing Routing Process fix + Scan QR/NO_WS
**Bug fix:**
- Tombol-tombol di panel Routing Process Marketing (selector *Jumlah Routing*, ⚡ Auto-fill, 🗑 Clear, 💾 Save Routing to Supabase, + Add Row) sebelumnya tidak berfungsi karena semua fungsi JS terkait routing (`addRoutingRow`, `setRoutingCount`, `renderRoutingTable`, `autoFillRoutingFromType`, `saveRoutingToSheets`, dll.) hardcode ke DOM ID divisi Production (`prd-*`) alih-alih menyesuaikan divisi yang sedang aktif.
- `saveRoutingToSheets()` juga salah deteksi divisi aktif karena mengecek `document.getElementById('dept-Marketing')`, sebuah elemen yang tidak pernah ada di markup manapun — sehingga fungsi ini selalu mengira sedang berada di Production.
- Diperbaiki dengan helper `curRoutingPrefix()` yang membaca variabel `activeDept` untuk menentukan target DOM (`mkt-*` vs `prd-*`) secara dinamis di semua fungsi routing terkait.

**Fitur baru:**
- Panel **📡 SCAN QR / NO_WS** ditambahkan ke Routing Process Marketing, konsisten dengan pola yang sudah ada di Engineering/PPIC/Production/QC/Accounting — termasuk tombol 📷 untuk scan QR lewat kamera HP (iOS Safari & Android Chrome).
- Setelah scan, WS badge dan tabel Routing Process Marketing otomatis termuat/reset sesuai WS yang di-scan (mengambil data tersimpan dari `localStorage` jika ada).

**Cakupan perubahan:** hanya menyentuh section Marketing + fungsi JS routing yang dipakai bersama (shared). Panel/fungsi divisi Engineering, PPIC, Production, QC, dan Accounting tidak diubah.

### V45.2 dan sebelumnya
- Login gate per divisi dengan kredensial spesifik.
- Format tanggal Deadline PO (ISO → format Indonesia).
- Perbaikan tipografi Dashboard.
- `clearDivision()` untuk reset seluruh form state per divisi.
- Migrasi sebagian penyimpanan routing/backend ke Supabase.
- V44: overhaul UI (font Inter + JetBrains Mono, data lebih bold, border lebih tebal), QR work order card printing, Downtime tracker per-mesin, tombol Clear di semua form divisi.

---

## 🚀 Deploy

1. Upload `index.html` ke branch GitHub Pages (`elim28-design.github.io/PT_KMIL_Barcodesys/`).
2. Jika ada perubahan pada backend GAS, **redeploy sebagai versi Web App baru** — URL lama tidak otomatis mengambil kode terbaru.
3. Tidak ada build step; file bisa langsung dibuka di browser atau di-host sebagai static file.

## 🐞 Reporting bug

Sertakan: divisi yang bermasalah, langkah reproduksi, dan screenshot/console error jika ada — supaya lebih cepat ditelusuri ke DOM ID / fungsi JS yang relevan.
