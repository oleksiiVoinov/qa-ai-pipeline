# Eval: candidate-checklist vs golden test-cases (AB-3107)

Check: does a checklist item expand into each of the 16 reference test cases
(the checklist must be a complete upstream of the test cases)?

## Result

- **14 of 16** reference tests have a directly corresponding checklist item.
- **2 of 16** (Optimal location flow ×2) are covered only by items marked
  `source: glossary`. Without `standards/glossary.md`, skill 3 — like skill 4 — will not
  derive these adjacent scenarios from the ticket text. The same systemic gap.

## Checklist → test cases link

- 20 checklist items → 16 reference cases. The checklist is a bit more granular in places
  (the 3 Integration items collapse on the way to cases) — that's normal:
  the checklist is about "what", the case groups close items into "how".
- Each item expands into ≥1 test case — the 3→4 link works.

## Conclusion

- Skill 3 gives the same coverage as skill 4 (~88%), but as a lightweight artifact without steps —
  exactly what's needed for small tickets and grooming.
- It confirms the priority of the next step: `standards/glossary.md` with the adjacent
  areas (Optimal Location, placements, protocols, sanity) will close the common gap
  of both skills at once.
