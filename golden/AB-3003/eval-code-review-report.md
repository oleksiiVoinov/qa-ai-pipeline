# Eval: live run of code review (AB-3003)

Skill 6 was run against the real code of the `AB-3003-Streaks` branch (read-only `git show`),
a sample of 21 test cases from golden.

## Result

- 16 PASS — each with a direct reference to the code (file + class/function).
- 0 FAIL — no discrepancies found in the sample.
- 5 QA — honestly flagged the places that can't be proven from the code: survival across
  an update, independence from auth, icon color, popup texts, translation quality.
- 0 N/A.

## What confirmed the value (and the false-PASS risk removed)

- **PASS only with proof.** Every green status referenced concrete
  code (`StreakRules.isBrokenAsOf`, `StreaksFeatureConfig.isEnabledForCountry`,
  `streakMilestoneEventNameOrNull`, `onCtaClicked`, etc.). There are no unsupported PASSes.
- **Doubtful items went to QA, not PASS.** "Update does not reset the streak" and
  "login/logout has no effect" — things that can't be strictly proven from the diff — are marked QA.
  This is exactly the protection against false PASS that the approach in the article lacked.
- **Precise value comparison:** popup thresholds (`<=0 / ==1 / else`), the milestones
  mapping (3/5/10/20/50/100), the flag key `feature_ab_3003_streaks`,
  the `STREAKS_BROKEN` event — all verified verbatim against the code, not "by meaning".

## Subtle spots the skill caught correctly

- Feature OFF actually disables both the logic and the events (early return in
  `RecordStreakAndLogMilestoneUseCaseImpl`), and the UI (`isStreaksIconVisible`) — it does not
  just hide the icon. Matches the grooming clarification (full shutdown).
- 0-day CTA when already connected — no-op (Connect TEA decides by ConnectionState),
  which precisely matches AC-7.

## Conclusion

- Skill 6 works on live code and behaves conservatively: PASS with proof,
  doubtful items in QA. It is the most quality-critical skill, and it does not hand out false PASSes.
- The eval of skill 6 — ✅ (on live AB-3003 code).
- A note: the final human finishes the 5 QA items — that is the correct
  human-in-the-loop, not "everything's green, ship it".
