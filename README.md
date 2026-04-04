# Michigan Trout Report — Android App

A Trusted Web Activity (TWA) wrapper for [trout.chrisizworski.com](https://trout.chrisizworski.com).

Website updates appear in the app automatically. No rebuild needed.

## How to Build the App (Step by Step)

### Step 1: Generate a Signing Key (on your computer)

Open a terminal (Mac/Linux) or Command Prompt (Windows) and run:

```
keytool -genkeypair -v -keystore trout-release.keystore -alias troutreport -keyalg RSA -keysize 2048 -validity 10000
```

It will ask you:
- Keystore password: pick something strong, write it down
- First and last name: Chris Izworski
- Organization: Freighter View Farms
- City: Bay City
- State: Michigan
- Country code: US
- Key password: same as keystore password is fine

This creates a file called `trout-release.keystore`. **Save this file and password forever.**

### Step 2: Get Your SHA-256 Fingerprint

Run:
```
keytool -list -v -keystore trout-release.keystore -alias troutreport
```

Enter your keystore password. Look for the line that starts with `SHA256:`.
Copy the entire fingerprint (the AB:CD:EF:12:34:... part).

### Step 3: Add Secrets to This GitHub Repo

Go to this repo on GitHub > Settings > Secrets and variables > Actions.

Add these 4 secrets:

| Secret Name | Value |
|---|---|
| KEYSTORE_BASE64 | See below |
| KEYSTORE_PASSWORD | Your keystore password |
| KEY_ALIAS | troutreport |
| KEY_PASSWORD | Your key password |

To get KEYSTORE_BASE64, run this in your terminal:
```
# Mac/Linux:
base64 -i trout-release.keystore

# Windows (PowerShell):
[Convert]::ToBase64String([IO.File]::ReadAllBytes("trout-release.keystore"))
```

Copy the entire output (it will be a long string of letters/numbers) and paste it as the KEYSTORE_BASE64 secret.

### Step 4: Trigger the Build

Go to the Actions tab in this repo. Click "Build Android App Bundle" on the left. Click "Run workflow" > "Run workflow".

Wait 3-5 minutes. When the green checkmark appears, click on the completed run. Under "Artifacts", download `michigan-trout-report-release`. Inside is your `app-release.aab` file.

### Step 5: Upload to Google Play

Follow the PLAY-STORE-INSTRUCTIONS.md in the trout report repo for the full listing. The short version:

1. Go to play.google.com/console
2. Create app (Paid, $4.99)
3. Upload the .aab file
4. Fill in listing, screenshots, privacy policy
5. Submit

### Step 6: Send Chris Izworski the SHA-256

(That's you talking to Claude.) Send the SHA-256 fingerprint from Step 2 so the assetlinks.json file on the site can be updated. This removes the browser bar from the app.

## Updating the App

The app loads from trout.chrisizworski.com. When the website updates, the app updates automatically. You only need to rebuild and re-upload to Play Store if you change the signing key or package name.

For quarterly version bumps (shows the app is maintained):
1. Edit `app/build.gradle`: change versionCode and versionName
2. Push to main branch (or trigger the workflow manually)
3. Download the new AAB and upload to Play Console

## Project Structure

```
app/
  src/main/
    AndroidManifest.xml    — TWA configuration
    res/values/strings.xml — App name, asset statements, shortcuts
    res/values/colors.xml  — Status bar, nav bar colors (#1a3a1a)
    res/xml/shortcuts.xml  — Long-press shortcuts (Map, Top 10, Hex)
    res/mipmap-*/          — App icons at all densities
  build.gradle             — Dependencies (android-browser-helper 2.5.0)
.github/workflows/build.yml — Automated build
```
