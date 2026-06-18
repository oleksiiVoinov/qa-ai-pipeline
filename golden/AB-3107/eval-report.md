# Eval: candidate-run vs golden (AB-3107)

A run of the `ab-test-cases` skill on ticket AB-3107, compared against the 16 reference Testomatio tests.

## Matches (14 of 16 references covered)

| Reference | Covered by the candidate |
| --- | --- |
| Country value / city empty | ✅ |
| Country value / city value | ✅ |
| Server has type value | ✅ |
| Server object without country (hidden) | ✅ |
| City not translated | ✅ |
| SL free / VIP / History | ✅ ✅ ✅ |
| Other placements (notif/widget/Fastest/Connect Detail) | ✅ |
| Customer support — not translated (English) | ✅ |
| Device lang not supported → English | ✅ |
| Offline | ✅ |
| Translations after update | ✅ |
| Sorting / searching / filtering localized | ✅ |

## Missed by the candidate (2)

- **Optimal location flow (only Country)** and **(Country + city)** — pinging servers
  through the admin panel. This is an adjacent feature (Optimal Location) that the
  ticket text says nothing about — pure QA tribal knowledge.

## Extra in the candidate (valid, but beyond the reference)

- Tests at the integration/endpoint level (V6 request, passing the locale),
  "a device language change refreshes the names". Derived from the AC, reasonable,
  but the team did not author them manually.

## Conclusion

- **~88% of the reference reproduced from a single ticket text** + the cross-checks
  applied correctly, raw requirements found, terminology preserved. Good for a prototype.
- **The main systemic gap:** the skill does not know adjacent features (Optimal Location, the
  placement list) if they aren't in the ticket. Two fixes:
  1. Skill 1 (context) + linked documentation (Confluence) will pull in the placements.
  2. Create a `standards/glossary.md` — a list of recurring app areas
     (Optimal Location pinging, all server-name placements, protocols,
     the sanity set) so the skill checks the task against them.
- Minor: a slight tendency toward over-generation at the integration level — not critical,
  filtered out by human review.
