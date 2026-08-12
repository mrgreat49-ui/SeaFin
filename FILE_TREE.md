# SeaFin — File Tree & Setup

Exactly where every file goes, then how to install. 45 files total.

```
seafin/
├── package.json                     ← deps + scripts
├── vite.config.js
├── index.html
├── .env                             ← YOU create this (copy of .env.example, keys filled)
├── .env.example
├── .gitignore
├── README.md
├── CLAUDE.md                        ← project guide / handoff doc
├── INSTALL_APK.md                   ← how to install the APK on your phone
├── FILE_TREE.md                     ← this file
├── generate-icons.html             ← open in browser → download 4 PNGs → public/icons/
├── sample-statement.csv            ← test file for the statement importer
│
├── public/
│   ├── manifest.webmanifest
│   ├── sw.js                        ← service worker (offline + notifications)
│   └── icons/                       ← put the 4 generated PNGs here:
│       ├── icon-192.png
│       ├── icon-512.png
│       ├── icon-maskable-192.png
│       └── icon-maskable-512.png
│
└── src/
    ├── main.jsx                     ← entry: I18nProvider + App + registerPWA
    ├── index.css                    ← full design system
    ├── App.jsx                      ← app shell, navigation, dashboard + core screens
    ├── pwa.js                       ← service-worker registration + install banner
    │
    ├── components/
    │   └── BrandMark.jsx            ← compass-and-dollar logo mark
    │
    ├── i18n/
    │   ├── index.jsx                ← useI18n() provider + Bengali numerals
    │   └── strings.js               ← EN + বাংলা dictionary
    │
    ├── hooks/
    │   ├── useNawabAI.js            ← 3-tier chat hook
    │   ├── useExchangeRate.js       ← live rate/gold → app
    │   └── useDriveSync.js          ← debounced Drive auto-sync
    │
    ├── services/
    │   ├── cryptoService.js         ← PBKDF2 + AES-256-GCM
    │   ├── storageService.js        ← encrypted vault (source of truth)
    │   ├── rateService.js           ← ExchangeRate + Metals/gold, cached
    │   ├── geminiService.js         ← Nawab cloud AI: Gemini → Groq
    │   ├── visionService.js         ← Gemini Vision: receipts, statements
    │   ├── ocrService.js            ← OCR.space → Tesseract (offline)
    │   ├── backupService.js         ← encrypted .seafin export/restore
    │   ├── driveService.js          ← Google Drive sync (drive.file)
    │   └── notifyService.js         ← local notifications + triggers
    │
    ├── utils/
    │   ├── currency.js              ← money + date formatting (bilingual)
    │   ├── offlineNLP.js            ← instant offline AI (Tier 3)
    │   ├── zakat.js                 ← Hanafi Zakat engine
    │   ├── runway.js                ← survival runway engine
    │   ├── budget.js                ← 50/30/20 · envelope · flex
    │   ├── debt.js                  ← avalanche/snowball simulator
    │   ├── land.js                  ← Bengali land units (katha…)
    │   ├── networth.js              ← assets − liabilities
    │   └── score.js                 ← SeafarerScore (0–1000)
    │
    └── screens/
        ├── OnboardingView.jsx       ← 6-step first run
        ├── ZakatView.jsx
        ├── ContractsView.jsx
        ├── ScanView.jsx
        ├── ImportView.jsx
        ├── RunwayView.jsx
        ├── BudgetView.jsx
        ├── DebtView.jsx
        ├── NetWorthView.jsx
        ├── BackupView.jsx
        └── NotificationsView.jsx
```

## Folders you do NOT create by hand
- `node_modules/` — created by `npm install`
- `dist/` — created by `npm run build`
- `android/` — created by `npx cap add android` (only if building the APK)

## After placing all files
```bash
cp .env.example .env      # then paste your keys into .env
npm install
npm run dev               # http://localhost:5173
```
