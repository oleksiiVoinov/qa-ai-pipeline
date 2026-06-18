# PR Summary — AB-3003 Streaks

> Ticket: https://superunlimited.atlassian.net/browse/AB-3003 · Branch: `AB-3003-Streaks` · Base: `dev` · Live run of the ab-pr-summary skill

## Overview

- **Files changed:** 73 · **+lines / −lines:** +2526 / −1
- **Branch:** `origin/AB-3003-Streaks` (merge-base with `origin/dev`: `ead45a0`)
- A new feature in a separate `feature/streaks` module (api + impl) + integration into main/app.

## Changes by component

### feature/streaks/impl (feature core, 46 files)
- **Domain:**
  - `StreakRules.kt` — streak logic: `recordSuccessfulConnection`, `isBrokenAsOf`
    (reset when `daysSince >= 2`), `normalizedCountAsOf`; timezone-dependent
    (`kotlinx.datetime.TimeZone`, handling DST / clock set back).
  - usecases: `GetStreakCountUseCaseImpl`, `GetStoredStreakCountUseCaseImpl`,
    `GetStreaksUseCaseImpl`, `RecordStreakAndLogMilestoneUseCase`,
    `InitStreaksTrackingAppInitAction`.
  - entities: `StreakParams`, `StreaksStore`, `StreaksStoreState`,
    `OnConnectionStreakRecordedMsg`.
  - **remoteconfig:** `StreaksFeatureRemoteConfig` (key `feature_ab_3003_streaks`),
    `StreaksFeatureConfig {enabled, whitelistedCountries}` — filter by country.
  - **analytics:** `Events.kt` — all event constants (`streaks_icon_screen_view`,
    `..._0d/1d/multi_popup_*`, `streaks_3d/5d/10d/20d/50d/100d_recorded`, `streaks_broken`).
- **Presentation:** `StreaksDialogFragment.kt`, `StreakViewModel.kt`.
- **DI:** `StreaksModule.kt` (+ `bindRemoteConfig`).
- **Resources:** drawables (`streaks_img_streak/no_streak`, `streaks_ic_sad_24`,
  `streaks_dialog_bg`), layout `streaks_dialog.xml`, `colors/dimens/styles`,
  `strings.xml` + **17 locales** (ar, de, en-rGB, es, fa, fr, in, ja, ko, ms,
  pt-rBR, ru, th, tr, uk, vi, zh-rCN, zh-rTW).

### feature/streaks/api (contracts, 5 files)
- usecases: `GetStreakCountUseCase`, `GetStoredStreakCountUseCase`,
  `GetStreaksFeatureEnabledUseCase`.
- navigation: `StreaksDialogScreen`, `StreaksDialogResult`.

### feature/main/impl (integration on Home, 8 files)
- `ObserveStreaksStateCmd`, `OnStreaksStateUpdatedMsg`, `OnStreakBrokenDetectedMsg`,
  `MainCmdHandler`, `MainViewState`, `Msgs`, `MainModule`.
- Sends `STREAKS_BROKEN` on the next cold start after a break.

### base/system-time (shared module, ~3 files)
- `SystemTimeChangesSource`, `GetSystemTimeContextChangesUseCase`, `SystemTimeModule`.
- Tracks device time/timezone changes — related to streak-day calculation
  and the "date change on device" scenarios.

### vpnsuper (app, 5 files)
- `MainActivity.java` (+42) — streak icon (`btnStreaks`), `updateStreaksIcon`,
  events `STREAKS_ICON_SCREEN_VIEW` / `STREAKS_ICON_USER_TAPS`, opening the dialog.
- `ConnectFragment.kt` (+11), `KoinApplicationFactory.kt` (+4, DI),
  `activity_main.xml` (+36, icon), drawable `ic_streaks_flame.xml`.

### Build
- `build.gradle.kts` ×4 — module dependencies (`remoteConfig.api`, `analytics.core`).

## Highlighted separately

| Type | What | File |
| --- | --- | --- |
| Feature flag | `feature_ab_3003_streaks` `{enabled, whitelistedCountries}` | StreaksFeatureRemoteConfig.kt |
| Events | the entire `streaks_*` set | analytics/Events.kt |
| DB migration | none | — |
| Endpoint | none (device-local) | — |

## Automated test coverage

| Area | Auto coverage | Note |
| --- | --- | --- |
| StreakRules (day/reset logic) | yes (unit) | StreakRulesTest.kt |
| usecases (count/get/record+milestone) | yes (unit) | 3 *Test.kt |
| Dialog/popup (UI) | no | manual / UI automation |
| Integration in MainActivity | no | manual |
| Localization | no | manual |

(The automation framework is not connected in this session — checked against the unit tests in the diff.)

## Risk flags

- `MainActivity.java` — the app's core entry point (Java), +42 integration lines.
- `KoinApplicationFactory.kt` — app-wide DI.
- `base/system-time` — a shared base module; if anyone else uses it, time changes may be affected.
- 17 locales — low risk, but check the completeness of translations.

## Notes

- Base `dev` (per `standards/repo-config.md`). Diff `dev...AB-3003-Streaks`.
- The streak is stored locally (no DB migrations and no network calls) — consistent with
  the "device-local, per app install" requirement.
