# AB-3003 — reference test set (Testomatio: Streaks, 51)

Format of each test: **Title** + `### Requirements` (preconditions) + `### Steps` (numbered) + `### Expected result`.

## UI / icon / popup (suite 9d487f64)

- The feature icon matches the mockup
- The icon streak without streak displaying in white color and displaying "0"
- The icon streak with an active streak (more 1 day) displaying in green color
- Popup without streak displaying Connect button and X button
- Popup with streak displaying "Keep up the good work" and X button
- Feature supports localisation in all languages available in our app (all 18 languages are listed)

## Connection entry points — expanded through test design (suite 19d07f5c)

- Connect from the main page change streak counter
- Connect from the FREE tab change streak counter
- Connect from the VIP tab change streak counter
- Connect from the History tab change streak counter
- Connect from the browser attention popup change streak counter
- Connect with Always on feature change streak counter
- Connect from notification Wi-Fi Protection feature change streak counter
- Connect from notification for VIP user change streak counter

## Logic and behavior (suite b5cc0e50)

- Feature present on home screen
- Feature counter start from 0
- Streak defined as ≥1 successful connection per calendar day
- Streak is device-local (per app install)
- Tapping the icon opens a contextual pop-up based on streak status
- Streak days displaying on icon on the home screen
- Streak days displaying on the feature popup screen
- Tap to connect button on the popup without streak initialised connect to server and close popup
- Tap to connect button while user is connect to the server closes popup
- Tap to X button on the popup closes feature popup
- Tap to CTA on popup (1-day or more streak) closes popup without connection
- If user dont have successful connections during a full day streak resets to 0
- Streak start from 0 after streak reset
- Reinstall the app resets streak to 0
- Update the app dont resets streak
- Authentication (login/logout) does not impact streak logic
- Streaks on multiple devices have own independent streak value
- When feature disabled streak calculation, streak icon and feature analytics are disabled
- Feature controlled via Firebase Remote Config (global + country)

## Events (suite 37d59ef5) — each event = one test

- streaks_icon_screen_view / streaks_icon_user_taps
- streaks_0d_popup_screen_view / _user_taps_x / _user_taps_cta
- streaks_1d_popup_screen_view / _user_taps_x / _user_taps_cta
- streaks_multi_popup_screen_view / _user_taps_x / _user_taps_cta
- streaks_3d / 5d / 10d / 20d / 50d / 100d _recorded
- streaks_broken (after a reset due to inactivity)

## What this tells us about the decomposition

- 20 AC → 51 tests. Each AC is expanded into at least 1 test.
- Each UI state (icon 0/active, popup 0/1/multi) → a separate test.
- Each GA4 event → a separate test.
- Connection entry points (8 of them) — NOT from the AC, but added through test design (wherever you can connect → check the streak there).
- Cross-cutting team checks: localization in all languages, feature flag OFF (full shutdown), reinstall vs update, sign in/out, multi-device.
