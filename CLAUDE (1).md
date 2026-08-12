# CLAUDE.md — SeaFin project guide

Context file for AI assistants (and humans) working on this codebase. Read this first.

**Project:** SeaFin — Seafarers Finance
**For:** Professional Bangladeshi seafarers. USD earned at sea, BDT spent at home, gold/land as wealth, Zakat + Dan as duty.
**Owner:** Syed Saifullah · © 2026
**Stack:** React 18 + Vite. PWA. Encrypted localStorage. Live rates. Tesseract OCR. **Fully bilingual EN/বাংলা.** Encrypted `.seafin` backup + Drive (manual + auto). 6-step onboarding. Local notifications. Compass brand mark. Design system in `index.css`.

---

## Run it

```bash
npm install
npm run dev      # http://localhost:5173
npm run build    # production → /dist
npm run preview  # serve build (test PWA, SW, notifications)
```

First launch → 6-step onboarding → creates the encrypted vault. Returning users → PIN unlock. No recovery. "Wipe vault" in More → back to onboarding.
**Icons:** open `generate-icons.html` (compass brand mark), download all 4, drop in `public/icons/`.
`.env` (copy `.env.example`): all keys optional.

---

## Architecture

```
public/  manifest.webmanifest · sw.js (offline + notificationclick) · icons/
src/
├── main.jsx                 <I18nProvider><App/></I18nProvider> + registerPWA()
├── pwa.js · index.css
├── App.jsx                  shell + nav + Home/Analytics/Goals/More + Ledger/Alerts/Chat + AddSheet + PinView
├── components/BrandMark.jsx  compass-and-dollar mark (PIN screen; icon gen mirrors it)
├── i18n/  index.jsx (useI18n) · strings.js (EN+BN, full app coverage)
├── hooks/  useNawabAI · useExchangeRate · useDriveSync
├── services/  crypto · storage · backup · drive · rate · gemini · vision · ocr · notify
├── utils/  currency · zakat · runway · budget · debt · land · networth · score · offlineNLP
└── screens/  Onboarding · Zakat · Contracts · Scan · Import · Runway · Budget · Debt
             · NetWorth · Backup · Notifications
```

### i18n — COMPLETE
Every screen now flows through `t()`. `useI18n() → {t, lang, setLang, bnNum}`. Keys in both languages in `strings.js`. `currency.setDigitMode(lang)` renders Bengali numerals in money. Language toggle on PIN + Onboarding + More; persists in `localStorage['seafin_lang']`. **All 11 screens + chrome + onboarding + PIN + notifications are translated.** When adding UI, add the string to BOTH `en` and `bn` in `strings.js` and reference via `t('key')`; never hardcode.

### SeafarerScore (`utils/score.js`)
`computeScore(state, goldPrice)` → `{ score 0–1000, level, pillars[] }`. Weighted: savings 35%, safety net 30%, debt load 20%, runway 15%. Ranks Cadet→Commodore. `AnalyticsView` draws the gauge, driver bars, and 6-month sea-vs-shore bars from real data.

### Notifications (`services/notifyService.js` + `NotificationsView`)
Local, via `registration.showNotification` (SW handles tap). `evaluateTriggers` runs on state/runway change, once-per-day guarded: runway < 60d, Zakat haol ≤ 15d, budget over > 20%. DND window silences watch hours. Prefs in `seafin_notify`.

### Brand mark
`components/BrandMark.jsx`: compass ring + rose + gold growth arrow + $ hub, in teal/gold. On the PIN screen; `generate-icons.html` paints the same for PWA icons.

### Everything else
- **Onboarding** 6-step, seeds clean vault. **Auto-sync** `useDriveSync` (8s debounce), **Drive** `driveService` (drive.file), **Backup** `backupService` (.seafin).
- **Net worth** gold(live)+land(katha)+assets−debts · **Debt** avalanche/snowball · **Budget** 50/30/20·envelope·flex · **Runway** savings÷90-day burn.
- **Rates** `getRates()` 12h cache → `setLiveRate()`. **Encryption** PIN→PBKDF2(210k)→AES-256-GCM, `keyRef`, `seafin_secure_v1`.
- **Nawab** Gemini→Groq→offlineNLP. **Import** CSV/TXT + PDF/image OCR. **Scan** Gemini Vision.

### Navigation
`App.jsx`: `tab` (4 tabs) + `sub` (overlay). New screen = add `sub`, render in `stage`, link from More.

### Data model
`transactions[]`, `goals[]`, `contracts[]`, `zakatHoldings`, `zakatHistory[]`, `dan`, `budget`, `debts`, `debtBudget`, `assets{land[],items[]}`, `profile`, `baseWealthBDT`. localStorage: `seafin_secure_v1`, `seafin_lang`, `seafin_autosync`, `seafin_notify`, `seafin_rate_cache`.

---

## Conventions
- Money whole BDT; `fmtBDT`/`fmtUSD` (auto Bengali digits), convert with `getRate()`.
- All UI text via `t()`; keep `strings.js` synced across both langs. No em dashes.
- Design: teal primary, coral actions/alerts, gold for wealth + Islamic finance. Never navy+gold. CSS vars.
- Offline-first. Async storage only via `saveState(state, key)`. Heavy libs on demand.

---

## Status — feature-complete v6.0

### Done
- **Full bilingual EN/বাংলা** across every screen, Bengali numerals in money.
- Compass brand mark (PIN + icons). Real SeafarerScore. Local notifications (triggers + DND).
- 6-step onboarding · Auto-sync · Drive · Encrypted backup.
- Net worth (land in katha) · Debt planner · Budget · Runway engine · Live rates · AES-256 encryption · PWA.
- Transactions, dashboard, ledger, analytics, goals, more, Nawab chat, Zakat & Dan, Contracts, Receipt scan, Statement import (OCR).

Every master-prompt module and infra target is built and translated.

### Next up (optional polish)
- [ ] Bengali date formatting in `relativeDay` (dates still en-GB).
- [ ] Monthly net-worth snapshots + trend chart.
- [ ] Background notification scheduling (Periodic Background Sync) so alerts fire when app is closed.
- [ ] Asset Statement PDF/Excel export.
- [ ] Per-district katha ratio; user-editable needs/wants split.

### Known limitations
- Dates render en-GB even in Bengali mode (numerals localise, month names don't yet).
- Notifications evaluate while the app is open (no server push / background schedule).
- Icons generated once. SW needs HTTPS/localhost. Cloud AI + ExchangeRate keys in bundle.
- Runway burn flat 90-day. Debt sim fixed rates. No PIN/backup recovery. Biometric stubbed. goldprice.org unofficial.

---

## Working style for AI assistants
- `storageService.js` = source of truth (async + encrypted). `backup`/`drive`/`useDriveSync` = portability.
- All UI text via `t()` (both langs in `strings.js`); money via `fmtBDT`/`fmtUSD`. Score via `computeScore`. Notifications via `evaluateTriggers`.
- AES key in `keyRef` in `App.jsx`; screens get `state`+`setState`; persistence automatic.
- Engines in `utils/`; read live rate via `getRate()`. Keep screens in `src/screens/`. Match the visual system.
