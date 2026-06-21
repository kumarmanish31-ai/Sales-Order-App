# Sales Order Book — Setup Guide

A mobile-friendly sales order app: book orders, auto-calculate GST (CGST/SGST or IGST), generate a PDF, share it straight to WhatsApp, and manage your Party (customer) master with GST details — entered by hand or imported from Excel.

## What's in this folder
- `index.html` — the entire app (open this to run it)
- `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png` — make it installable as an app
- `README.md` — this file

## 1. Try it right now
Just open `index.html` in a phone or desktop browser. It works immediately in **single-device mode** (data stays on that one phone, via browser storage) — good for testing.

To get it properly installable and to **sync data across your whole sales team**, follow the steps below.

---

## 2. Turn on team-wide shared data (Firebase — free, ~5 minutes)

Without this step, each phone has its own separate data. With it, every phone sees the same parties, products, and orders, live.

1. Go to https://console.firebase.google.com and create a free project (no credit card needed).
2. In the project, open **Build → Firestore Database → Create database**. Choose **Start in test mode** for now (lets the app read/write without a login system — fine for an internal team tool; lock it down later with Firebase Auth if needed).
3. Click the **gear icon → Project settings → General**, scroll to "Your apps," click the **Web (`</>`)** icon, and register an app (any nickname).
4. Firebase will show you a `firebaseConfig` object like:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "yourproject.firebaseapp.com",
     projectId: "yourproject",
     storageBucket: "yourproject.appspot.com",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
5. Open `index.html` in a text editor, find the `firebaseConfig` block near the top of the `<script>` section, and paste your real values in place of the `YOUR_...` placeholders.
6. Save the file. Re-open it (or re-deploy it, see step 3) — Settings screen should now say "✅ Connected — this device is syncing live."

That's it — every phone that opens this same hosted app now shares the same data.

---

## 3. Host it so your team can open it as a real link

Pick whichever is easiest for you:

**Netlify (easiest, no account strictly required):**
1. Go to https://app.netlify.com/drop
2. Drag this whole folder onto the page.
3. You'll get a live `https://...netlify.app` link in seconds. Share that link with your team.

**GitHub Pages:**
1. Create a new GitHub repository and upload these files.
2. Repo Settings → Pages → set source to the `main` branch / root folder.
3. GitHub gives you a `https://yourname.github.io/yourrepo/` link.

Either way, you now have a real HTTPS link. Anyone on your team can open it on their phone and tap **"Add to Home Screen"** to get an app icon — this already works on both Android and iPhone, no app store needed.

---

## 4. Turn it into an installable `.apk` (optional)

If you specifically want a `.apk` file your Android team can install:

1. Host the app as above (you need a live HTTPS URL — PWABuilder needs to fetch it).
2. Go to https://www.pwabuilder.com
3. Paste your hosted URL and click **Start**.
4. Click **Android** → it will package a signed, ready-to-install APK (or Android App Bundle) for you — free, no coding.
5. Download the APK and share it with your team (e.g. via WhatsApp or Google Drive). On each Android phone, they'll need to allow **"Install unknown apps"** for whichever app they downloaded it through — a one-time permission prompt.
6. (Optional) The same package can be submitted to the Google Play Store if you want a proper store listing later.

**Note on iPhones:** Apple doesn't allow installing `.apk` files (that's an Android format) or side-loaded apps outside the App Store. iPhone users should instead open your hosted link in Safari and tap **Share → Add to Home Screen** — they'll get the same app experience with an icon on their home screen.

---

## 5. Using the app

- **New Order tab** — pick or add a party, add items (pick from your saved Product list or type manually), GST is calculated automatically per line (CGST+SGST if the party is in your own state, IGST if a different state). You can also enter the customer's **PO Number / PO Date**, and optionally upload a photo or PDF of their PO (max 500KB) — it gets merged in as extra page(s) on the order PDF automatically.
- **Orders tab** — view past orders, download as PDF (includes the merged PO copy if one was attached), share straight to WhatsApp, or export everything to Excel — the export is a combined sheet with both order totals and PO No/PO Date columns side by side.
- **Parties tab** — add customers one at a time, or click **Import Excel** to bulk-upload. Use **Download Excel template** to get the exact column headings expected (Name, GSTIN, State, Address, Phone, Email — column order/case doesn't matter, the app matches common variations like "GST No" or "Mobile"). Tap **📋 Order History** on any party to see every order placed for them, with a running total.
- **Products tab** — your catalog of items with default rate, unit, and GST% (saves typing on every order; you can still always type an item manually instead).
- **Settings tab** — your business name, address, GSTIN, state (used to decide CGST/SGST vs IGST), and order number prefix.

## 7. About the Party PO merge
When you attach a customer's PO (a photo or a PDF) to an order, the app combines it with the generated order PDF into one file: if the PO is itself a PDF, its pages are appended after the order; if it's a photo, it's placed on its own page. This combined PDF is what gets downloaded and what gets sent via the WhatsApp share button — so your team always shares one file containing both your order and the customer's original PO. Keep the upload under 500KB (compress photos if needed) — this keeps it well within Firestore's per-document size limit so it saves reliably.

## 6. A note on the WhatsApp share button
Mobile browsers can't silently attach a file to WhatsApp via a link — only your phone's own share sheet can hand a file to another app. So this button uses your phone's native **Share** feature with the order PDF attached; you just tap **WhatsApp** in the share sheet that pops up, same as sharing a photo. On older or unsupported browsers, it falls back to downloading the PDF and opening a WhatsApp chat where you can attach it manually.
