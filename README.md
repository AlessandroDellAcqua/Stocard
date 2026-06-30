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
  as a colored tile with the store's logo.
- **Barcode generation** — Cards are rendered as real, scannable
  barcodes/QR codes directly in the browser. Supports EAN-13, EAN-8,
  UPC-A, UPC-E, Code 128, Code 39, ITF, Codabar, and QR codes, generated
  from scratch with a custom encoder (no external barcode library).
- **Barcode scanning** — Add a new card by scanning its barcode with
  the camera. Uses the native `BarcodeDetector` API where available,
  and falls back to bundled jsQR / Quagga libraries (loaded on demand)
  for browsers that don't support it.
- **Built-in store directory** — A database of well-known retailers
  (mostly Italian, plus some UK/CH) with their brand colors and logos
  baked in, so adding a recognized store auto-fills its look.
- **Manual card entry** — For stores not in the directory, enter the
  store name and card number by hand.
- **Notes/badges** — Attach a short note to a card (e.g. "EXPIRES",
  "PIN 1234") shown as a badge on the tile.
- **Search & sort** — Filter cards by name and sort alphabetically or
  by last used.
- **Local persistence** — Everything is stored in the browser's
  `localStorage`; no account, no server, no network requests for your
  card data.
- **iOS home-screen app (PWA-style)** — Designed to be added to the iOS
  home screen via Safari's "Add to Home Screen," at which point it runs
  full-screen with no browser chrome, like a native app.

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
- **Store database** (`STORE_DATABASE`) maps store names to a brand
  color and a base64-embedded PNG logo, so logos work fully offline
  with no external image requests.

## Usage

1. Open `index.html` in a browser (works locally, no server needed).
2. On iPhone: open it in Safari, tap the Share button, then **Add to
   Home Screen** for the full native-app experience.
3. Tap the **+** button to add a card — either pick a store from the
   directory, scan a barcode, or enter details manually.
4. Tap any card to bring up its full-screen barcode for scanning at
   checkout.

## Notes

- All data stays on-device in `localStorage`; clearing browser data or
  reinstalling the home-screen icon will remove saved cards.
- Logos embedded in the store directory are only applied automatically
  on first add; a small one-time migration step keeps already-saved
  cards in sync when a logo in the directory is updated.
