# Requirements Review — AB-3003 Streaks

> Source: AB-3003 context · Run of the ab-requirements-review skill (validation) · Stage: ☑ questions (Stage 1)

---

## STAGE 1 — for human review

### Questions for the human (PM/dev decision needed)

| # | Question | Source | Options / proposal |
| --- | --- | --- | --- |
| Q1 | What counts as a "done" day for the streak? | "≥1 successful connection per calendar day" | A) only a successful connect event; B) a connection attempt was made; C) there was a connection at some point during the day |
| Q2 | Should a connection attempt count when there are internet problems? | streak definition | A) only success (otherwise false positive); B) an attempt also counts |
| Q3 | When exactly does the streak break? | AC-15 | a full calendar day (local TZ) without a successful connection? |
| Q4 | Behavior with a persistent connection >24h without a reconnect — does it extend the streak? | edge | A) requires a new connect event; B) a continuous connection counts |
| Q5 | Feature flag OFF — full shutdown or just hiding the UI? | AC-19 | A) full shutdown (UI + logic + analytics); B) hide the UI, keep counting in the background |
| Q6 | What does the popup CTA do per state? | AC-6,7,10,13 | 0-day: connect if disconnected, otherwise close; 1-day/multi: just close |
| Q7 | Does sign in / sign out affect the streak? | AC-17 | presumably not — to be confirmed |
| Q8 | Is the streak device-local or account-shared? | "device-local" + reinstall/auth | confirm: local, reinstall resets it, multi-device independent |
| Q9 | Is there an event for a streak break? | events table | add `streaks_broken`? |
| Q10 | Targeting in Remote Config — by premium/free and language, or by countries only? | A/B config | |

### Contradictions (do not resolve ourselves)

| # | Side A | Side B | Where |
| --- | --- | --- | --- |
| C1 | AC-20: texts in all supported languages | the list of languages is not specified in the ticket | AC vs missing reference list |

### Gaps

- The list of supported languages is not specified (there is a Confluence Localisation page) · `need more info`
- No explicit event for a streak reset (if not `streaks_broken`) · `need more info`
- Experiment tracking: segment, duration, metrics, allocation events · `need more info`
