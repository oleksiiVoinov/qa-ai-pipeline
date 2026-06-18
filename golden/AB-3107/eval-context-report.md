# Eval: candidate-context vs the source ticket (AB-3107)

A completeness check of the context file against the raw ticket from Jira.

## Completeness

- ✅ All 7 AC carried over verbatim and numbered (AC-1..AC-7).
- ✅ All substantive comments collected: dev "What's Done / Impacted / What to Test",
  bugs from QA, responses, final builds.
- ✅ The sanity App Checks preserved (useful as a standard regression).
- ✅ Attachments listed and honestly marked "not read" (image + video).
- ✅ Related Confluence pages and tickets extracted as sources.

## Useful things caught (the skill's value)

- **A real discrepancy:** AC-4 "selected application language" vs dev "device
  default language". Recorded in "Gaps", not resolved (correct — that's
  the job of skill 2 / the human). This is exactly the kind of semantic drift the skill
  should make visible.
- **A gap for the code skills:** the PR/branch is not linked in the ticket — flagged that
  skills 5-7 will need it separately.

## Conclusion

- Skill 1 collects the source of truth without losses and without analysis, correctly
  separating "facts" from "conclusions".
- The link works: this context can be fed to skills 3 and 4 — they already
  accept a context file as their priority input.
- The value of pulling in Confluence is confirmed: the placement list and adjacent
  areas (that same glossary gap) are partly closed right here if you
  pull in the related pages.
