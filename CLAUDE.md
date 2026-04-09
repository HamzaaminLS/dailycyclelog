# Little Star Daily Sales — Project Memory

## Project Overview
Single-file web app (`index.html`) for Little Star kids vehicle factory in Karachi, Pakistan.
Used daily on a **15.6" laptop + 14" Dell Latitude 7420 in Chrome** to generate invoices, track sales logs, and manage wholesaler ledger.
Also viewed on **phone** (read-only, real-time sync via Supabase).

## File
- **Main file:** `index.html`
- **Path:** `C:/Users/USER/Desktop/daily sales/index.html`
- **GitHub:** https://github.com/HamzaaminLS/dailycyclelog (branch: `main`)

## Database
- **Primary:** `localStorage` — keys prefixed with `ls_`
- **Cloud:** Supabase Realtime Database
  - URL: `https://juaxobsfkulmhheemxwb.supabase.co`
  - Table: `littlestar`
  - Real-time listener via `postgres_changes` — phone sees changes instantly
  - Emergency fallback via Supabase REST API fetch
  - 30s auto-sync interval when offline changes pending
  - `_dirty` flag prevents echo from own push
  - Save debounce: 1000ms
- **Offline auto-sync:** saves locally when offline, auto-pushes on reconnect. Green/red dot in nav.

## Security
- **PIN lock screen** on page load — PIN: `2580`
- Stored in `sessionStorage` (`ls_auth_ok`) — must re-enter per browser session (tab close)
- Client-side only — prevents casual access

## Key Data Structures

### Sale object
```js
{
  id: Date.now(),
  date: "YYYY-MM-DD",
  time: ISO string,
  slipNo: "31",          // stored as plain number string, displayed as #31
  wholesaler: "Name",
  city: "Karachi",
  items: [{name, qty, price}],
  totalUnits: number,
  withPrice: bool,
  cashPaid: number,
  driver: string,
  signLine: true
}
```

### localStorage keys
| Key | Value |
|-----|-------|
| `ls_salesLog` | array of sale objects |
| `ls_prices` | `{modelName: price}` |
| `ls_priceHistory` | array of `{date, changes:[{model,from,to}]}` |
| `ls_ledger` | wholesaler payment ledger |
| `ls_billStart` | global bill counter start |
| `ls_billStartMap` | per-buyer bill start overrides |
| `ls_pendingSync` | `'1'` if offline changes not yet synced |
| `ls_archive_*` | yearly archived sales data |
| `ls_printSettings` | logo/tagline toggles |

## Car Models (21 models with default prices PKR)
Sprinter(2750), Stroller(2400), Twilight(2000), Super Tolo(1750), Swift Rider(1350), Sparkle(1550), Panda(1800), Mercedese(1700), Mic Cruiser(2850), Sunflower(1800), Rainbow(1900), 3D(1150), Classic(1500), Crystal(1700), Duckling(2000), Jaguar(1400), Jungle(1300), Mick. Mouse(2050), Mick. Rider(2200), Minion(2150), Skyjet(1550)

## Wholesalers by City
- **Karachi:** Khwaja, Naqi, Saleem, Mairaj, Moosa, Umair, Evergreen, Noor Alam, Irfan, Aslam, Ashraf, Rajesh, Khaleel Cycle, Shamshad, Hamza Col, PVT, Tanzeel, Super Cycle, Memon Cycle, Mudassir, Usman Aziz, Shandar Cycle, Alkaswa
- **Sindh:** Faisal Digri, Raja Bright, Fancy Cycle, Ghousia Digri
- **Punjab:** A1 Lahore, Farhan Yousuf, Fahad Lahore, Talat Sialkot, Pak Punjab, Aslam Cycle, Imran Cycle, Abdul Hafeez, Khurram Pindi, Aslam Cycle 2, Sheikhpura, Fine Cycle, Dilawar But, Khurram Pindi 2
- **Balochistan:** Quetta Khalid, Mudassir 2
- **KPK:** Umer Cycle, Ayaz Banaras, Irfan Mangora, Mardan

## Features

### Invoice / Entry
- Auto bill number, resets only when LAST bill deleted
- 4 quick buyer buttons: Khwaja, Naqi, Mairaj, Saleem (below buyer search)
- Two generate buttons: **✓ Generate** (preview only) | **🖨️ Generate & Print** (prints immediately)
- A4 landscape invoice — 2 copies (Buyer + Office) side by side
- Optional: include prices + cash paid / carry forward / return amount

### Sales Log
- Filter by buyer, date range, month pills
- **Sort toggle:** ↓ Newest / ↑ Oldest
- Paginated (50 per page) — pagination shown at **top and bottom**
- Slip numbers shown as `#31` format
- Reprint, Edit, Delete per card

### Export / Backup
- **📥 Export** — Excel (.xlsx)
- **📦 Backup All** — full JSON backup (nav bar, always available)
- JSON restore from backup file (Settings)
- Yearly archive (Settings)

## UI / Design
- **Primary targets:** 15.6" laptop + 14" Dell Latitude 7420 (1920×1080, 125-150% scale)
- Dark navy nav (`#1a1a2e`), white entry card, dark log background
- Font: DM Sans (Google Fonts)
- Sticky top nav, responsive breakpoints at 1280px, 1024px, 640px

## Tech Stack
- Pure HTML/CSS/JS — **single file, no build process** (~560 lines)
- Supabase JS SDK v2 (CDN)
- XLSX.js 0.18.5 (export to Excel)
- DM Sans font (Google Fonts CDN)

## Git
```
Remote: https://github.com/HamzaaminLS/dailycyclelog.git
Branch: main
User: HamzaaminLS
```
To push: `git add index.html CLAUDE.md && git commit -m "msg" && git push`

## Important Notes
- File is ~560 lines but each line is very long (minified) — use `Grep` to find functions, then `sed -n 'X,Yp'` to read
- `fmtD(dateStr)` → DD/MM/YYYY, `fmtTime(isoStr)` → H:MM AM/PM, `fmtSlip(s)` → #31
- `padSlip(n)` → plain number string (no leading zeros), `fmtSlip(s)` → `#` + parseInt
- `save()` → localStorage + debounced Supabase sync, `doSync()` → direct Supabase push
- Invoice print opens in new window with embedded CSS
- **Always commit and push to git after every code change**
- **Do NOT delete JSON/backup files without explicit permission**
