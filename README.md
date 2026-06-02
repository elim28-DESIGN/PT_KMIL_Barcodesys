# PT KMIL — WS Barcode & Tracking System

> Sistem manajemen Work Sheet (WS) berbasis web untuk PT KMIL.  
> Pencatatan event antar divisi, QR barcode, routing proses produksi, dan dashboard tracking real-time.

**🔗 Live:** https://elim28-design.github.io/PT_KMIL_Barcodesys/  
**📊 Tracking:** https://elim28-design.github.io/PT_KMIL_Barcodesys/tracking.html

---

## Fitur

| Fitur | Keterangan |
|---|---|
| Multi-divisi | Marketing, Engineering, PPIC, Purchasing, Production, QC, Accounting |
| Real-time sync | Semua PC otomatis sinkron via Supabase — tanpa konfigurasi |
| QR Code & Barcode | Generate dan print label WS |
| Routing Process | Tracking urutan operasi di Production |
| Tracking Dashboard | Kanban board + Timeline per WS, light/dark mode |
| Offline-first | Data cache di localStorage, sync ke cloud saat online |

---

## File

```
PT_KMIL_Barcodesys/
├── index.html       ← Aplikasi utama (input event semua divisi)
├── tracking.html    ← Dashboard Kanban & Timeline
└── README.md
```

---

## Arsitektur

```
Browser (index.html / tracking.html)
    │
    ├── localStorage  ← offline cache
    │
    └── Supabase REST API
            │
            └── PostgreSQL (Singapore)
                    ├── events
                    └── routing_process
```

Tidak ada server sendiri.  
**GitHub Pages** = static hosting.  
**Supabase** = database cloud PostgreSQL.

---

## Database

**Project ID:** `ltxjikrlcnyfyzlsvxof`  
**Region:** Southeast Asia (Singapore)  
**Plan:** Free (500 MB database, cukup untuk jutaan rows)

### Tabel `events`

| Kolom | Keterangan |
|---|---|
| `id` | Primary key (auto) |
| `ts` | Timestamp ISO 8601 |
| `department` | Divisi pencatat |
| `event` | Nama event |
| `no_ws` | Nomor Work Sheet |
| `no_po` | Nomor Purchase Order |
| `customer` | Nama customer |
| `part` | Nama part/komponen |
| `operation` | Operasi mesin |
| `ws_type` | REGULER / ASSEMBLING |
| `qty` | Jumlah |
| `user` | Nama operator |
| `status` | Status WS |
| `deadline` | Tanggal deadline |
| `remarks` | Catatan |
| `unit_set` | Unit/set (Marketing) |
| `assy_parts` | Parts assembling |
| `assy_qty_json` | Qty parts (JSON) |
| `ws_start` | WS awal range |
| `ws_end_suffix` | WS akhir range |

### Tabel `routing_process`

| Kolom | Keterangan |
|---|---|
| `id` | Primary key (auto) |
| `no_ws` | Nomor Work Sheet |
| `routing_seq` | Urutan operasi |
| `routing_op` | Nama operasi |
| `routing_dur` | Estimasi durasi |
| `routing_status` | PENDING / IN PROGRESS / DONE |
| `user` | Operator |
| `department` | Divisi |
| `ts` | Timestamp |
| `saved_at` | Waktu disimpan |

---

## Setup Supabase (jika perlu reset)

Jalankan di **Supabase → SQL Editor:**

```sql
-- Tabel Events
create table if not exists events (
  id bigserial primary key,
  ts text, department text, event text, revisi text,
  no_ws text, no_po text, customer text, part text,
  operation text, ws_type text, qty text, "user" text,
  ws_turun text, ws_turun_detail text, parsial text,
  parsial_qty text, parsial_sisa text, mat_count text,
  drawing_filename text, deadline text, remarks text,
  status text, shift text, ws_type_prd text,
  assy_parts_summary text, qc_bulk text, sj_filename text,
  assy_turun text, ws_start text, ws_end_suffix text,
  unit_set text, assy_qty_json text, assy_parts text,
  created_at timestamptz default now()
);

-- Tabel Routing
create table if not exists routing_process (
  id bigserial primary key,
  ts text, no_ws text, routing_seq integer,
  routing_ws_ref text, routing_op text, routing_dur text,
  routing_status text, "user" text, department text,
  event text, saved_at text,
  created_at timestamptz default now()
);

-- Row Level Security
alter table events enable row level security;
alter table routing_process enable row level security;
create policy "allow all" on events for all using (true) with check (true);
create policy "allow all" on routing_process for all using (true) with check (true);
```

---

## Flow WS antar Divisi

```
Marketing ──► Engineering ──► PPIC ──► Production ──► QC ──► Accounting
   │               │            │           │           │
 WS Created    Drawing       BOM &      Routing      Inspeksi    Delivered
 Order         Released    Material     Process      QC OK/NG
```

---

## Deploy

Website otomatis update saat push ke branch `main`:

```bash
git add index.html tracking.html README.md
git commit -m "Update: deskripsi perubahan"
git push origin main
```

Tunggu 1–2 menit → live site terupdate.

---

## Changelog

### V43 — Supabase Integration (current)
- ✅ Migrasi dari Google Apps Script ke Supabase PostgreSQL
- ✅ Auto-sync real-time — tidak perlu isi URL di setiap PC
- ✅ Semua tombol "Save to Sheets" → "Save to Supabase"
- ✅ `tracking.html` — Kanban board + Timeline dengan light/dark mode
- ✅ Font formal IBM Plex (tracking dashboard)
- ✅ Fix routing_process save ke Supabase
- ✅ Fix missing columns (unit_set, assy_parts, assy_qty_json)

### V42.1 — GAS Fixes
- Fix listRouting handler
- Fix filter ts di fetchFromSheets
- Fix dedup composite key no_ws+ts

### V42 — Multi-divisi
- Tambah Engineering, PPIC, Purchasing, QC, Accounting
- Routing Process Production
- Assembling WS range generator
