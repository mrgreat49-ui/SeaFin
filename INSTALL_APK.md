# SeaFin — Install the APK on Your Phone

A step-by-step guide for getting SeaFin from Android Studio onto your Redmi Note 10 Pro (or any Android phone). No prior Android knowledge assumed.

© 2026 Syed Saifullah · SeaFin — Seafarers Finance

---

## Before you start

You need:
- The project built once: `npm install`, then `npm run build`, then `npx cap sync android`.
- **Android Studio** installed (Giraffe or newer).
- A **USB cable** (or you can transfer the APK any other way, see Method B).
- Your phone: **Redmi Note 10 Pro**, Android 11.

There are two paths. **Method A** (Android Studio straight to phone) is easiest if your phone is plugged in. **Method B** (build the APK file, copy it over) is best if you want a shareable `.apk` you can install anytime.

---

## Method A — Run straight from Android Studio (fastest)

Use this the first time, to confirm it works.

### 1. Open the Android project
In your project folder, run:
```bash
npx cap open android
```
This opens the `android/` folder in Android Studio. Wait for the bottom status bar to finish **"Gradle sync"** (can take a few minutes the first time).

### 2. Turn on Developer Options on your phone
On the Redmi:
1. **Settings → About phone**.
2. Tap **MIUI version** 7 times fast. You'll see "You are now a developer."
3. Go back to **Settings → Additional settings → Developer options**.
4. Turn on **USB debugging**.
5. Turn on **Install via USB** (MIUI needs this, and it may ask you to sign in to a Mi account).

### 3. Plug in the phone
- Connect the phone to your PC by USB.
- On the phone, a popup asks **"Allow USB debugging?"** → tap **Allow** (tick "Always allow from this computer").
- Set the USB mode to **File Transfer (MTP)** if asked.

### 4. Run it
- In Android Studio's top toolbar, your phone should appear in the device dropdown (e.g. "Redmi Note 10 Pro").
- Click the green **▶ Run** button (or press **Shift+F10**).
- Android Studio builds, installs, and launches SeaFin on your phone automatically.

That's it. The app is now on your phone. But this is a **debug** build tied to your PC. For a permanent, shareable app, use Method B.

---

## Method B — Build a shareable APK file (recommended for keeping)

This produces an `.apk` you can install on any phone, email to yourself, or keep as a backup.

### 1. Build the release APK
In Android Studio:
1. Top menu: **Build → Generate Signed Bundle / APK…**
2. Choose **APK** → **Next**.
3. **Key store:** first time, click **Create new…**
   - Pick a path like `seafin-key.jks`, set a password you'll remember.
   - Fill alias (e.g. `seafin`), a key password, and your name. Validity: leave 25 years.
   - **Save that .jks file and both passwords somewhere safe.** You need the exact same key to ship updates later. Losing it means you can't update the app.
4. Back on the dialog, select your keystore, enter the passwords → **Next**.
5. Build variant: choose **release**. Tick both signature versions (V1 + V2). → **Finish**.

### 2. Find the APK
When it finishes, a little popup in the bottom-right says **"APK(s) generated successfully"** with a **locate** link. Click it. The file is here:
```
android/app/release/app-release.apk
```
That `app-release.apk` is your installable app.

### 3. Get the APK onto your phone
Any of these works:
- **USB:** copy `app-release.apk` to the phone's Downloads folder.
- **Email/Telegram/Drive:** send it to yourself, open on the phone.
- **Direct:** with the phone plugged in, run from your project folder:
  ```bash
  adb install android/app/release/app-release.apk
  ```
  (adb comes with Android Studio; if "command not found", see Troubleshooting.)

### 4. Install it on the phone
1. On the phone, open **Files** (or Mi File Manager) → **Downloads** → tap `app-release.apk`.
2. MIUI will warn: **"For your security, your phone is not allowed to install unknown apps from this source."** Tap **Settings**.
3. Toggle on **Allow from this source** for the app you're installing from (Files or your browser).
4. Go back, tap the APK again → **Install**.
5. MIUI may run a scan and show **"App scanned, no risks."** Tap **Install anyway** / **Done**.
6. Open SeaFin from your home screen.

Done. SeaFin is installed as a real app.

---

## First launch checklist (do this on WiFi before going to sea)

Because SeaFin is built to work offline at sea, prime it once while you have internet:

- [ ] Complete the **6-step onboarding** (language, profile, contract, goal, PIN).
- [ ] Open the app once online so the **exchange rate + gold price cache**.
- [ ] Go to **More → Offline AI** and **download the Nawab model** on WiFi (not mobile data). This is what lets the AI answer with no internet.
- [ ] Add your current balance and a few transactions so **Runway** and **SeafarerScore** have data.
- [ ] If using cloud backup: **More → Backup → Drive**, connect and set your backup password.
- [ ] Test it: turn on **airplane mode** and confirm Nawab still replies and the app still opens.

---

## Troubleshooting

**Phone doesn't show up in Android Studio**
- Re-check USB debugging is on, and you tapped **Allow** on the phone.
- Try a different USB cable/port (some cables are charge-only).
- On MIUI, make sure **Install via USB** is enabled in Developer options.

**"adb is not recognized / command not found"**
- adb lives in `~/Library/Android/sdk/platform-tools` (Mac) or `%LOCALAPPDATA%\Android\Sdk\platform-tools` (Windows). Add that folder to your PATH, or `cd` into it and run `./adb install ...`.

**"App not installed" on the phone**
- You may already have a version signed with a different key. Uninstall the old SeaFin first, then install again.
- Make sure you built **release**, not a half-finished build.

**MIUI keeps blocking the install**
- Settings → Privacy protection → **Special permissions → Install unknown apps** → allow your file manager/browser.
- Temporarily turn off **MIUI Optimization** (Developer options) if it's aggressively scanning.

**White screen / app opens then closes**
- Rebuild the web layer and re-sync before generating the APK:
  ```bash
  npm run build
  npx cap sync android
  ```
- Then regenerate the signed APK.

**Nawab AI won't answer offline**
- You must download the offline model on WiFi first (More → Offline AI). Without it, offline mode falls back to the instant keyword engine, which is limited but still works.

---

## Updating the app later

When you change code:
```bash
npm run build
npx cap sync android
```
Then in Android Studio, **Build → Generate Signed Bundle / APK** again, using the **same keystore** as before. Install the new APK over the old one; your data stays (it's encrypted in the app's storage).

To bump the version number, edit `android/app/build.gradle`:
```gradle
versionCode 2          // increase by 1 each release
versionName "6.0.1"    // human-readable version
```

---

## Quick command reference

```bash
npm install                 # once, installs dependencies
npm run build               # build the web app into /dist
npx cap sync android        # copy web build into the Android project
npx cap open android        # open the project in Android Studio
adb devices                 # list connected phones
adb install path/to/app-release.apk   # install over USB
```

---

*Keep your keystore file and passwords safe. They are the only way to ship future updates.*
