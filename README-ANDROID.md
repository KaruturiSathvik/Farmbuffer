# FarmLedger Android wrapper (starter)

This packages the FarmLedger web UI inside a Capacitor Android shell.

## Requirements on Windows
- Node.js LTS
- Android Studio (current stable)
- Android SDK installed through Android Studio

## Build the Android project
1. Extract this folder.
2. Open PowerShell in this folder.
3. Run:
   ```powershell
   npm install
   npm install capacitor-native-biometric @capacitor-community/bluetooth-le
   npx cap add android
   npx cap sync android
   npx cap open android
   ```
4. In Android Studio, wait for Gradle sync.
5. Use Build > Build Bundle(s) / APK(s) > Build APK(s).

## Important
This APK starter bundles the web UI, but server-side features require FarmLedger's backend
hosted and reachable over HTTPS. The deployment has not succeeded yet. The UI may open,
but login, registration, ledger persistence and API-backed features will not work until the
backend is deployed and the app is configured to call its API URL.

Do not embed server secrets in the Android app.

## Face / fingerprint sign-in
Uses Android's built-in biometric prompt through the `capacitor-native-biometric` plugin.
1. `npm install capacitor-native-biometric` (already in the steps above).
2. After `npx cap add android`, open `android/app/src/main/AndroidManifest.xml` and add inside `<manifest>`:
   ```xml
   <uses-permission android:name="android.permission.USE_BIOMETRIC" />
   ```
3. `npx cap sync android`, rebuild the APK.
4. Sign in once with your password and tap Yes on the prompt. After that the app asks for face/fingerprint on launch.
The device must have a face or fingerprint enrolled in Android Settings. Which one is offered is decided by Android
and the phone's hardware; the password always remains available.

## Camera for Plant Check
The Plant Check page has "Take photo" and "Choose from gallery" buttons. For the camera to open on Android, after
`npx cap add android` add these inside `<manifest>` in `android/app/src/main/AndroidManifest.xml`, then run `npx cap sync android`:
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" android:required="false" />
```
Photos are resized and stored on the phone only (they are not uploaded to the server).

## Device pairing (Bluetooth)
The Devices page can scan for nearby Bluetooth Low Energy devices, pair/connect them, and also save devices manually by ID.
1. `npm install @capacitor-community/bluetooth-le` (already in the steps above), then `npx cap sync android`.
2. After `npx cap add android`, add inside `<manifest>` in `android/app/src/main/AndroidManifest.xml` (see the plugin's README if scanning finds nothing):
   ```xml
   <uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
   <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
   <uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
   <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
   <uses-feature android:name="android.hardware.bluetooth_le" android:required="false" />
   ```
This pairs and connects only. Reading sensor values or switching a pump needs code for that specific device's Bluetooth profile.
