# Automation test repository configuration

Parameters for skill 8 (autotest recommendation). Repository: `Android-automation-test`
(Appium + TestNG + Allure, app package `com.free.vpn.super.hotspot.open`).

## Where the tests live

- Test classes: `src/test/java/apps/multiplatform/regression/users/<user type>/...`
- Suites: `src/test/java/apps/multiplatform/regression/regressionTests.xml`
  (+ `smoke.xml`, `regression_device1/2.xml`), connectivity: `connectivity/.../switchTests.xml`.

## How to map changes onto classes (signals)

1. **User type** = package: `users/free`, `users/vip`, `users/mpf`.
2. **Feature** = class name: `StreakTest`, `ServerListVIPTest`, `NotificationTest`,
   `ConnectionReportTest`, `MainScreenTest`, `ProtocolsTest`, etc.
3. **Allure annotations** in the classes: `@Feature("6. Free User")`, `@Story("6. Main screen test")` —
   semantic mapping of feature/area → class.
4. **Registration** in `regressionTests.xml` (blocks `<test name="free user">`, etc.).

## How to run (from the project's CLAUDE.md)

```bash
# A single class
./gradlew test --tests "apps.multiplatform.regression.users.free.mainPage.StreakTest"
# Full regression
./gradlew regressionTest -Dudid=<UDID> -DserverListKey=<KEY> -DzendeskToken=<TOKEN>
# Smoke
./gradlew smokeTest -Dudid=<UDID>
```

Requires a device (`-Dudid`), a running Appium, and `local.properties` with keys.

## Rule

The skill only **recommends** classes and assembles commands — it does NOT run the tests itself
(a device/Appium/keys are needed). Running them is up to a human.
