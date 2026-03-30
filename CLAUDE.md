# Little Star Daily Sales — Project Memory

## Project Overview
Single-file web app (`index.html`) for Little Star kids vehicle factory in Karachi, Pakistan.
Used daily on a **15.6" laptop in Chrome** to generate invoices, track sales logs, and manage wholesaler ledger.

## File
- **Main file:** `index.html` (was previously `LittleStar_Daily_Sales_ver_4.html`)
- **Path:** `C:/Users/USER/Desktop/daily sales/index.html`
- **GitHub:** https://github.com/HamzaaminLS/dailycyclelog (branch: `main`)

## Database
- **Primary:** `localStorage` — keys prefixed with `ls_` (salesLog, prices, ledger, priceHistory, etc.)
- **Cloud backup:** Firebase Realtime Database — **Southeast Asia region**
  - URL: `https://daily-sales-c55bd-default-rtdb.asia-southeast1.firebasedatabase.app`
  - Project ID: `daily-sales-c55bd`
  - Ref: `fbDB.ref('littlestar')`
  - `apiKey` and `appId` still need to be filled in by user
- **Offline auto-sync:** When factory WiFi is down, data saves locally. When laptop reconnects, it auto-pushes to Firebase. Green/red dot in nav bar shows status.

## Key Data Structures

### Sale object
```js
{
  id: Date.now(),          // unique ID
  date: "YYYY-MM-DD",
  time: ISO string,        // full datetime for H:MM AM/PM display
  slipNo: "0001",          // bill number (4-digit padded)
  wholesaler: "Name",
  city: "Karachi",
  items: [{name, qty, price}],  // price stored per-item (editable per bill)
  totalUnits: number,
  withPrice: bool,         // include prices on invoice
  cashPaid: number
}
```

### localStorage keys
| Key | Value |
|-----|-------|
| `ls_salesLog` | array of sale objects |
| `ls_prices` | `{modelName: price}` |
| `ls_priceHistory` | array of `{date, changes:[{model,from,to}]}` |
| `ls_ledger` | wholesaler payment ledger |
| `ls_billStart` | bill counter start number |
| `ls_pendingSync` | `'1'` if offline changes not yet synced |

## Car Models (21 models with default prices PKR)
Sprinter(2750), Stroller(2400), Twilight(2000), Super Tolo(1750), Swift Rider(1350), Sparkle(1550), Panda(1800), Mercedese(1700), Mic Cruiser(2850), Sunflower(1800), Rainbow(1900), 3D(1150), Classic(1500), Crystal(1700), Duckling(2000), Jaguar(1400), Jungle(1300), Mick. Mouse(2050), Mick. Rider(2200), Minion(2150), Skyjet(1550)

## Wholesalers by City
- **Karachi:** Khwaja, Naqi, Saleem, Mairaj, Moosa, Umair, Evergreen, Noor Alam, Irfan, Aslam, Ashraf, Rajesh, Khaleel Cycle, Shamshad, Hamza Col, PVT, Tanzeel, Super Cycle, Memon Cycle, Mudassir, Usman Aziz, Shandar Cycle, Alkaswa
- **Sindh:** Faisal Digri, Raja Bright, Fancy Cycle, Ghousia Digri
- **Punjab:** A1 Lahore, Farhan Yousuf, Fahad Lahore, Talat Sialkot, Pak Punjab, Aslam Cycle, Imran Cycle, Abdul Hafeez, Khurram Pindi, Aslam Cycle 2, Sheikhpura, Fine Cycle, Dilawar But, Khurram Pindi 2
- **Balochistan:** Quetta Khalid, Mudassir 2
- **KPK:** Umer Cycle, Ayaz Banaras, Irfan Mangora, Mardan

## Features Implemented

### Invoice / Entry
- Auto bill number (4-digit padded), resets if last bill deleted
- Bill number reverts ONLY when the LAST/highest bill is deleted (not gaps)
- Date + time shown as `DD/MM/YYYY • H:MM AM/PM`
- A4 landscape invoice — 2 copies (Buyer + Office) side by side
- Optional: include prices on invoice + cash paid / carry forward / return amount
- Arrow key navigation between fields and car grid

### Sales Log
- Filter by buyer, date range, month pills
- Paginated (50 per page)
- Each card shows: date+time, bill no, buyer, city, units, total PKR, item chips
- Reprint 🖨️, Edit ✏️, Delete ✕ per card

### Edit Sale Modal
- Edit date, bill no, buyer, city, include-price checkbox, cash paid
- **Each car model row has qty + orange price field (editable per bill)**
- Saves item-level custom price (overrides global price)

### Price Manager
- Edit all car model prices
- Orange highlight on changed inputs
- **Price History** (purple button): shows all past changes with timestamps (DD/MM/YYYY HH:MM, old→new per model)

### Wholesaler Ledger
- Tracks total sales, cash paid on bills, extra payments
- Balance calculation per wholesaler
- Add/delete payments with notes
- Print all balances

### Settings
- Reset bill counter start number
- Firebase connection status

### Export / Backup
- Export to Excel (.xlsx)
- JSON backup & restore
- Firebase cloud sync (manual via sync, auto on reconnect)

## UI / Design
- **Target:** 15.6" laptop, Chrome browser
- Dark navy nav (`#1a1a2e`), white entry card, dark log background
- Font: DM Sans (Google Fonts)
- Car grid: 4 columns, fills available height with scroll
- Sticky top nav
- Green sync dot = online, Red = offline

## Tech Stack
- Pure HTML/CSS/JS — **single file, no build process**
- Firebase 9.23.0 (compat mode via CDN)
- XLSX.js 0.18.5 (export to Excel)
- DM Sans font (Google Fonts CDN)

## Git
```
Remote: https://github.com/HamzaaminLS/dailycyclelog.git
Branch: main
User: HamzaaminLS
```
To push changes:
```bash
cd "C:/Users/USER/Desktop/daily sales"
git add index.html
git commit -m "your message"
git push
```

## Important Notes
- The file is large (~830 lines), reading it in chunks of 10-15 lines at a time is needed
- Use `Grep` to find specific functions before reading/editing
- All JS is minified/compact — functions are on single lines
- `fmtD(dateStr)` → DD/MM/YYYY, `fmtTime(isoStr)` → H:MM AM/PM
- `save()` handles both localStorage + Firebase sync
- `doSync()` is the direct Firebase push function
- Invoice print opens in new window with embedded CSS
