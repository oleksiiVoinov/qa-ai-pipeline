# AB-3003 — Streaks

- **Type:** Task | **Priority:** Medium | **Status:** Done | **Label:** Blue_App
- **URL:** https://superunlimited.atlassian.net/browse/AB-3003
- **Testomatio suite:** Streaks (cf5d0846) — 51 tests

## Description

Retention feature for free and premium. A streaks icon with a counter (0 / 1 / X days)
on the Home screen. Tapping the icon opens a contextual popup based on the streak status.
A streak = ≥1 successful connection per calendar day (the user's local timezone).
Device-local (per app install). Controlled via Firebase Remote Config (global + country).
A/B test of icon visibility through Firebase.

## Acceptance Criteria (BDD, verbatim)

1. feature flag ON → the streak icon with a counter is visible on Home.
2. No active streak → white "0" icon.
3. Active streak (≥1 day) → green icon with the current counter.
4. Tap on the icon → contextual popup based on the streak status.
5. 0 days + tap → "No streak yet" popup with a "Connect" CTA and a close button (X).
6. CTA on the 0-day popup, disconnected → a VPN connection is initiated, the popup closes.
7. CTA on the 0-day popup, already connected → the popup just closes.
8. X on the 0-day popup → popup closed, we stay on Home.
9. 1-day streak + tap → "Keep up the good work" popup with a CTA and X.
10. CTA on the 1-day popup → the popup closes without connecting.
11. X on the 1-day popup → popup closed.
12. ≥2 days + tap → "X days in a row" popup with a CTA and X.
13. CTA on the multi-day popup → the popup closes without connecting.
14. X on the multi-day popup → popup closed.
15. 0 successful connections over a full calendar day (local TZ) → the streak resets to 0.
16. App reinstall → the counter resets to 0 on this device only.
17. Sign in / sign out → streak tracking does not change.
18. Logged in on multiple devices → each has its own independent streak.
19. A country where the feature is disabled (Remote Config OFF) → the icon is not visible, streak calculation and analytics are fully disabled.
20. Any supported language → all streak texts are translated.

## Tracking Events (GA4)

streaks_icon_screen_view, streaks_icon_user_taps, streaks_0d_popup_screen_view,
streaks_0d_popup_user_taps_x, streaks_0d_popup_user_taps_cta, streaks_1d_popup_screen_view,
streaks_1d_popup_user_taps_x, streaks_1d_popup_user_taps_cta, streaks_multi_popup_screen_view,
streaks_multi_popup_user_taps_x, streaks_multi_popup_user_taps_cta, streaks_3d_recorded,
streaks_5d_recorded, streaks_10d_recorded, streaks_20d_recorded, streaks_50d_recorded,
streaks_100d_recorded, streaks_broken.

## Feature flag / A/B

Remote config flag: `feature_ab_3003_streaks`
Baseline `{"enabled":false,"whitelistedCountries":[]}` / Variant A `{"enabled":true,"whitelistedCountries":[]}`

## Key clarifications from the comments (became the source of truth for tests)

- A "done" day = at least 1 **successful** connection in a calendar day (attempts do NOT count — otherwise false positives).
- Streak break = no successful connection at all over a full day (local TZ).
- The CTA triggers a connection **only** in the 0-day state; 1-day and multi-day just close the popup.
- Device-local: stored locally, reinstall resets it, sign in/out has no effect, different devices have different values.
- A persistent connection >24h without a reconnect does NOT extend the streak (variant A: a new successful connect event is required) — a corner case, reactive approach.
- Feature OFF = full shutdown of the feature, not just hiding the UI.
