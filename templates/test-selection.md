# Test Selection — <TICKET-KEY> <Title>

> Ticket: <link> · Autotest repo: Android-automation-test · Collected: <date>
> Input: impact (07), PR summary (05), test cases (04)

## Recommended set

| Priority | Class (FQCN) | Why (risk area / feature) |
| --- | --- | --- |
| 🎯 Direct | `apps.multiplatform...XTest` | the ticket's direct feature |
| 🔁 Regression | `apps.multiplatform...YTest` | risk area <...> from impact |
| 💨 Smoke | `apps.multiplatform...ZTest` | connection/boot affected |

## Run commands

```bash
./gradlew test --tests "apps.multiplatform.regression.users.free.mainPage.StreakTest"
# ...one class at a time or several --tests
```

> Required: device (`-Dudid=<UDID>`), a running Appium, `local.properties` with keys.

## No auto coverage → manual regression

- <risk area without a suitable class> — check manually
