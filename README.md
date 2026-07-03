# Stocard Clone — Loyalty Card Wallet

A single-file, offline-first web app that stores your loyalty/membership
cards as scannable barcodes, designed to be added to your iPhone home
screen and used like a native app.

## What it is

This is a self-contained `index.html` file (no build step, no server, no
external dependencies at runtime) that recreates the core experience of
apps like Stocard: a grid of your loyalty cards, each rendered with its
brand color and logo, that opens into a full-screen barcode/QR code when
tapped — ready to be scanned at checkout.

## Features

- **Card grid** — Add, view, edit, and delete loyalty cards, each shown
  as a colored tile with the store's logo. Long-press a tile to quickly
  delete cards.
- **Barcode generation** — Cards are rendered as real, scannable
  barcodes/QR codes directly in the browser. Supports EAN-13, EAN-8,
  UPC-A, UPC-E, Code 128, Code 39, ITF, Codabar, and QR codes, generated
  from scratch with a custom encoder (no external barcode library).
- **Barcode scanning** — Add a new card by scanning its barcode with
  the camera. Uses the native `BarcodeDetector` API where available,
  and falls back to jsQR / Quagga for browsers that don't support it.
- **Built-in store directory** — A database of 140+ retailers (mostly
  Italian, plus some UK/CH) with brand colors and logos baked in, so
  adding a recognized store auto-fills its look.
- **Smart search** — Fuzzy, forgiving search in both the card grid and
  the store directory: matches any part of any word (`ath` finds
  *Bath & Body Works*), ignores case and accents (`tigota` finds
  *Tigotà*), and treats `&`, `and`, and the Italian `e` as
  interchangeable.
- **Import / Export** — Open the *Importa/Esporta* page from the red
  camera button in the "Aggiungi carta" dialog (or by long-pressing +):
  - **Photo import** — Select up to 50 photos or screenshots of your
    cards (JPG, PNG, HEIC). Barcodes are decoded automatically, then
    you confirm each card one at a time: number (editable), store name
    with logo suggestions, and an optional note.
  - **Excel export** — Download all your cards as an `.xlsx` backup
    (name, number, code type, displayed format, note). On iPhone this
    opens the native share sheet.
  - **Excel import** — Load that same file (or any `.xlsx`/`.csv` with
    matching columns, Italian or English headers) to add all cards at
    once. Duplicates are skipped; logos attach automatically for
    recognized stores.
  The Excel engine (SheetJS) is embedded in the file, so this works
  fully offline.
- **Manual card entry** — For stores not in the directory, enter the
  store name and card number by hand.
- **Notes/badges** — Attach a short note to a card (e.g. "PIN 1234")
  shown as a badge on the tile.
- **Search & sort** — Filter cards by name and sort alphabetically or
  by last used.
- **Local persistence** — Everything is stored in the browser's
  `localStorage`; no account, no server, no network requests for your
  card data. Each device/browser has its own private storage.
- **iOS home-screen app (PWA-style)** — Add it to the iOS home screen
  via Safari's "Add to Home Screen" and it runs full-screen with no
  browser chrome, with its own icon (`Logo_light.png`).

## How it works

Everything — markup, styles, and logic — lives in one HTML file:

- **Styling** uses CSS custom properties for a dark, frosted-glass
  iOS-style UI, including safe-area insets (`env(safe-area-inset-*)`)
  so the layout respects the notch, Dynamic Island, and home indicator
  both in Safari and as an installed home-screen app.
- **Barcode/QR encoding** is implemented as a self-contained
  `BarcodeEngine` module that validates and encodes the supported
  symbologies and renders them to a `<canvas>`.
- **Card data** is normalized and persisted to `localStorage` under a
  versioned key, with automatic migration from older storage formats.
  A one-time logo sync keeps already-saved cards up to date when a
  logo in the directory is improved.
- **Store database** (`STORE_DATABASE`) maps store names to a brand
  color and a base64-embedded PNG logo, so logos work fully offline
  with no external image requests.

## Repository layout

- `index.html` — the entire app.
- `Logo_light.png` — home-screen icon (must stay in the same folder as
  `index.html`).
- `.nojekyll` — tells GitHub Pages to serve the files as-is.

## Usage

1. Open `index.html` in a browser (works locally, no server needed) or
   host it on GitHub Pages.
2. On iPhone: open it in Safari, tap the Share button, then **Add to
   Home Screen** for the full native-app experience.
3. Tap **+** to add a card — pick a store from the directory, scan a
   barcode, or enter details manually.
4. Tap the red 📷 button inside "Aggiungi carta" to open Import/Export
   for migrating from other apps or backing up.
5. Tap any card to bring up its full-screen barcode for scanning at
   checkout.

## Notes

- All data stays on-device in `localStorage`; clearing browser data
  removes saved cards (updating `index.html` does **not**).
- Excel exports write card numbers as text to preserve leading zeros;
  be careful when editing numbers directly in Excel.
