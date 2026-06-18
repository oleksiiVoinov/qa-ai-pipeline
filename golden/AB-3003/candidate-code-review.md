# Code Review — AB-3003 Streaks

> Ticket: https://superunlimited.atlassian.net/browse/AB-3003 · Branch: `AB-3003-Streaks` · Base: `dev`
> Live run of the ab-code-review skill · Input: test cases (golden) + PR summary (05)

## Summary

| Status | Count |
| --- | --- |
| ❌ FAIL | 0 |
| 🔍 QA (manual) | 5 |
| ⚪ N/A | 0 |
| ✅ PASS | 16 |

> Attention-first ordering: things that require action first, then PASS.

## ❌ FAIL (deal with first)

None.

## 🔍 QA — finish manually

| # | Test case | Why manual verification is needed |
| --- | --- | --- |
| 6 | Update does NOT reset the streak | store survival across an update — outside the diff, check on a device |
| 7 | Login/logout does not affect the streak | the absence of an auth link can't be strictly proven from the code — confirm by behavior |
| 19 | White "0" icon / green active icon | color/asset — visual |
| 20 | Popup texts (No streak / Keep up / X days) | copy and layout — visual |
| 21 | Localization in all languages | the 17 locale files are present; translation quality — manual |

## ⚪ N/A (not in this PR)

None (the entire reviewed sample is implemented in the branch).

---

<details>
<summary>✅ PASS (13) — implemented, verified against the code</summary>

| # | Test case | Proof (code) | Comment |
| --- | --- | --- | --- |
| 1 | Streak = ≥1 successful connection in a cal. day | `RecordStreakAndLogMilestoneUseCaseImpl.invoke` + `StreakRules.recordSuccessfulConnection` | trigger on `ConnectionState.Connected`, increment on a new day (tz-aware) |
| 2 | No connections over a full day → reset to 0 | `StreakRules.isBrokenAsOf` (`daysSince >= 2`) + `normalizedCountAsOf` | by local TZ |
| 3 | Streak starts from 0 after a reset | `normalizedCountAsOf` → 0 when broken | |
| 4 | Counter starts from 0 | default `StreaksStoreState` count=0; `render` when `<=0` | |
| 5 | Reinstall resets the streak | `StreaksStore : DataStore` — local storage | a consequence of device-local |
| 8 | Multi-device: independent values | `StreaksStore` local per-install, no sync | |
| 9 | Feature via Remote Config (global + country) | `StreaksFeatureConfig.isEnabledForCountry` + `GetStreaksUseCaseImpl` | empty whitelist = all countries |
| 10 | Feature OFF = full shutdown | early return in `RecordStreakAndLogMilestoneUseCaseImpl` + `MainViewState.isStreaksIconVisible` | kills logic, events and UI |
| 11 | `streaks_broken` after a reset | `OnStreakBrokenDetectedMsg` → `LogAnalyticsEventCmd(STREAKS_BROKEN)` | |
| 12 | Milestones 3/5/10/20/50/100 | `streakMilestoneEventNameOrNull` | logs only when the count changes |
| 13 | Popup events 0d/1d/multi | `StreakViewModel.popup*EventName` by `streakCount` | thresholds `<=0/==1/else` |
| 14 | Icon events | `MainActivity.updateStreaksIcon` → `STREAKS_ICON_SCREEN_VIEW`/`_USER_TAPS` | |
| 15 | 0-day CTA (disconnected) → connect + close | `StreaksDialogFragment.onCtaClicked` (count==0 → `ConnectRequested`) | caught by `ConnectFragment` |
| 16 | 0-day CTA (connected) → close | Connect TEA no-op when connected | matches AC-7 |
| 17 | 1-day/multi CTA → close without connecting | `onCtaClicked`: `streakCount > 0` → close | |
| 18 | X closes the popup | `logPopupXTap` + dismiss | |

</details>
