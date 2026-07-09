# Android-specific test checklist (project AB)

Cross-cutting checks skill 4 must run through when writing test cases. Add a case
for every item that is relevant to the feature — skip the ones that aren't (don't
pad). These are the Android realities that docs usually omit.

## Lifecycle & state
- **Foreground / background** — does the state refresh correctly when the app returns to foreground?
- **Cold launch vs warm launch** — does behavior differ between a cold start and resuming from background?
- **Persistence (DataStore / SharedPreferences)** — are show-once flags / cached states stored and kept across sessions?
- **Clear data / reinstall** — does clearing app data reset the right things? (update keeps, reinstall resets)

## Tooling to reproduce states (name it in the case's Requirements)
- **Charles Proxy** — can all API error states be simulated (500, 429, timeout)? Name the exact endpoint + HTTP status.
- **ADB** — can device/account state be forced via ADB if needed?
- **Firebase DebugView** — are all analytics events verifiable? ADB to enable:
  `adb shell setprop debug.firebase.analytics.app com.free.vpn.super.hotspot.open`

## Environment
- **Android OS versions** — behavior holds on Android 8+ (min supported).
- **Screen sizes** — UI renders on small screens (no overflow / truncation).

## User & network variants (also see the cross-cutting list in skill 4)
- Free / Premium / trial / logged-out / secondary device.
- Offline → online transition within the flow.
- Feature flag OFF = fully disabled; country-gated flag if applicable.
- Localization in all supported languages; fallback to English.
