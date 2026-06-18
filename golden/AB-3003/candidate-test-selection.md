# Test Selection — AB-3003 Streaks

> Ticket: https://superunlimited.atlassian.net/browse/AB-3003 · Repo: Android-automation-test
> Live run of the ab-test-selection skill · Input: impact (07), PR summary (05), test cases

## Recommended set

| Priority | Class (FQCN) | Why (risk area / feature) |
| --- | --- | --- |
| 🎯 Direct | `apps.multiplatform.regression.users.free.mainPage.StreakTest` | the feature directly: streak count=0, no-streak dialog, Connect now, day-based increment |
| 🔁 Regression | `apps.multiplatform.regression.users.free.mainPage.MainScreenTest` | Home onClick affected (`btnStreaks` inserted into the shared chain) — Free |
| 🔁 Regression | `apps.multiplatform.regression.users.vip.mainPage.MainScreenTest` | the same for the VIP home screen |
| 💨 Smoke | `apps.multiplatform.regression.users.free.connections.ProtocolsTest` | connection hook: the streak is written on `Connected` — check the Free connect flow |
| 💨 Smoke | `apps.multiplatform.regression.users.vip.connections.ProtocolsTest` | the VIP connect flow is not broken by the new observer |

## Run commands

```bash
# The feature directly
./gradlew test --tests "apps.multiplatform.regression.users.free.mainPage.StreakTest" -Dudid=<UDID>

# Home regression (onClick affected)
./gradlew test --tests "apps.multiplatform.regression.users.free.mainPage.MainScreenTest" \
               --tests "apps.multiplatform.regression.users.vip.mainPage.MainScreenTest" -Dudid=<UDID>

# Connection smoke (hook on Connected)
./gradlew test --tests "apps.multiplatform.regression.users.free.connections.ProtocolsTest" \
               --tests "apps.multiplatform.regression.users.vip.connections.ProtocolsTest" -Dudid=<UDID>
```

> Required: a device (`-Dudid`), a running Appium, `local.properties` with keys.

## No auto coverage → manual regression

- **Streak localization in all languages** — no class for localization (checked by search).
- **Feature flag / Remote Config (global + country)** — no class for remote config.
- **Analytics (`streaks_broken`, milestones, popup events)** — no class for events;
  `StreakTest` covers the counter/dialog/connect, but not event sending.
- **Persistence across an app update** — no class.
