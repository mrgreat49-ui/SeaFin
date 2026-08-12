# SeaFin — Seafarers Finance

Personal finance app built for professional Bangladeshi seafarers. React + Vite, offline-first, with the **Nawab** AI advisor (Google Gemini → Groq → on-device fallback).

© 2026 Syed Saifullah.

---

## Run it locally

You need **Node.js 18+** installed ([nodejs.org](https://nodejs.org)).

```bash
# 1. Put all the files in a folder matching the structure below
# 2. From that folder:
npm install
npm run dev
```

Vite prints a local URL (usually `http://localhost:5173`) and opens it automatically. Unlock the app with PIN **1234** (or tap the fingerprint icon).

To build for production:

```bash
npm run build      # outputs to /dist
npm run preview    # serve the built app locally
```

---

## Project structure

```
seafin/
├── index.html
├── package.json
├── vite.config.js
├── .env.example          ← copy to .env and add your free API keys (optional)
└── src/
    ├── main.jsx
    ├── index.css         ← full design system (teal identity, tokens)
    ├── App.jsx           ← app shell, navigation, all screens
    ├── hooks/
    │   └── useNawabAI.js  ← 3-tier chat hook
    ├── services/
    │   ├── storageService.js  ← localStorage data layer
    │   └── geminiService.js   ← Gemini + Groq cloud AI
    └── utils/
        ├── currency.js        ← USD/BDT formatting + conversion
        └── offlineNLP.js      ← instant offline keyword AI
```

---

## Nawab AI — how it works

Three tiers, tried in order:

1. **Gemini 1.5 Flash** (free) — primary, when online and a key is set.
2. **Groq Llama 3.3 70B** (free) — backup if Gemini fails.
3. **Offline NLP** — instant on-device keyword engine, always works, reads your live financial data. This is what runs by default with no keys.

### Adding AI keys (optional)

The app works fully without any keys (Nawab uses the offline engine). To enable cloud AI:

```bash
cp .env.example .env
```

Then paste your free keys into `.env`:

- **Gemini:** https://aistudio.google.com/app/apikey
- **Groq:** https://console.groq.com

Restart `npm run dev` after editing `.env`.

> Note: calling these APIs directly from the browser exposes the keys to anyone using the app. Fine for a personal build on your own device. For a public release, proxy the calls through a small backend so the keys stay server-side.

---

## What works right now

- **PIN lock** (1234) with fingerprint shortcut
- **Add transactions** (income/expense) with number pad, category picker, live USD↔BDT conversion
- **Data persists** in your browser via localStorage; survives refresh
- **Dashboard** with live net balance, currency toggle, recent activity
- **Transaction ledger** with filters, grouped by day
- **Survival runway**, **analytics/SeafarerScore**, **goals**, **more/settings**
- **Nawab chat** grounded in your real numbers, with source badges

## What's stubbed (next steps)

Receipt scan, bank statement import, Zakat calculator, contracts CRUD, budget engine, PWA service worker, and encryption are designed in the screen set but not yet wired here. The data layer and AI service are built to extend into them.
