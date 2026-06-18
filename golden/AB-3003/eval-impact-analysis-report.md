# Eval: live run of impact analysis (AB-3003)

Skill 7 was run against the real code of the `AB-3003-Streaks` branch, tracing from
merge-base `ead45a0`.

## What the skill did right

- **Distinguished new from changed code by merge-base, not by tip dev.** It caught
  the trap: the branch is already merged into `dev`, so the "does it exist on dev" check
  gives a false "modified". The correct base is merge-base; the new modules
  (streaks, system-time) are recognized as new → low risk.
- **Traced the shared module.** `git grep` showed that the new `base/system-time`
  is used only by `feature/streaks` + Koin — there's no external radius.
- **Found the real attention points in existing code:** the edit of the onClick chain
  in `MainActivity.java` (risk of affecting Home buttons) and the new subscriber to
  `ConnectionState.Connected` (connect flow). Both are additive, marked Medium.
- **Honest about the dev's opinion:** for this ticket the dev gave no impact list, so
  everything was found by tracing; the skill did not invent "confirmed" areas, but explicitly
  wrote that there's nothing to compare against.
- **Blind spots named:** Remote Config rollout, delivery to GA4, persistence
  across an update, Home rendering — the things not statically visible.

## Conclusion

- Skill 7 works on live code, behaves conservatively (risk areas, not
  "all good"), and its strength — catching undeclared impact — was confirmed
  even on a ticket without a dev list.
- The eval of skill 7 — ✅ (on live AB-3003 code).
- A lesson for all code skills: the comparison base = merge-base, especially for already
  merged branches. Worth keeping this in `repo-config.md`.
