# Impact Analysis — AB-3003 Streaks

> Ticket: https://superunlimited.atlassian.net/browse/AB-3003 · Branch: `AB-3003-Streaks` · Base: `dev`
> Live run of the ab-impact-analysis skill · merge-base: `ead45a0`
> Sources: ticket (dev's opinion), code tracing, PR summary (05)

## Risk areas (for re-verification, ranked)

| Area | Risk | Why it's at risk | What to re-check | Auto coverage |
| --- | --- | --- | --- | --- |
| Home screen — onClick dispatch | 🟡 Medium | the existing if-else click chain in `MainActivity.java` is edited (`btnStreaks` inserted next to `btnShare`); `activity_main.xml` +36 lines | the existing Home buttons (Share etc.) work; the Home layout didn't break | no (UI) |
| Connection flow hook | 🟡 Medium | the streak is written on `ConnectionState.Connected` (new subscriber: `InitStreaksTrackingAppInitAction`, observer in `ConnectFragment`) | connect/disconnect on all protocols not broken by the new observer | partial (unit on usecases) |
| Streaks feature module | 🟢 Low | a **new** isolated module (`feature/streaks` is absent at merge-base) | a smoke of the feature itself (that's skill 6) | yes (unit) |
| base/system-time | 🟢 Low | a **new** module; tracing: used only by `feature/streaks` + Koin | — | no |
| DI — KoinApplicationFactory | 🟢 Low | additive registration of the streak module | the app boots without crashing | no |
| build.gradle (deps) | 🟢 Low | dependencies added (`remoteConfig.api`, `analytics.core`) | the build passes | CI |

## Missed by the developer (the main value)

> In ticket AB-3003 the dev did not leave a list of Impacted Areas (grooming was about
> requirements). So everything below was found by code tracing, declared nowhere:

- **`MainActivity.java` onClick** — an insertion into the shared Home click-handling chain.
  The risk of affecting existing buttons is flagged nowhere. `git diff` → one edit of
  the existing `else if (id == R.id.btnShare)` branch.
- **Subscription to `ConnectionState.Connected`** — a new observer in the connect flow.

## Declared by the developer but not visible in the code

- None (the dev did not declare impact areas for this ticket).

## Confirmed (dev + code matched)

- None (nothing to match — the dev did not specify areas).

## Blind spots (not statically visible — assess manually)

- Rollout behavior via Remote Config (`feature_ab_3003_streaks`) on real segments.
- Event delivery to GA4 (the code shows the send, not the delivery).
- `StreaksStore` (DataStore) survival across an app update.
- Home screen rendering on different resolutions/devices after `activity_main.xml`.

## Notes

- Most of the changes are new isolated code (streaks, system-time) → low
  regression risk. The real attention points are the two additive integrations into existing code
  (Home onClick and the connection hook), marked Medium.
- Base `dev`, the diff is computed from merge-base `ead45a0` (the branch is already merged into dev —
  so a "does it exist on dev" comparison is misleading; merge-base is used).
