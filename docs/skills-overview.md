# Shiva — skills overview (appendix)

Shiva is a chain of narrow QA skills for the Android VPN project (AB). Each skill does
one job; the output of one (an MD file) is the input to the next; a human reviews
between steps. Trigger the whole chain by writing **`Shiva <ticket>`**.

| # | Skill | What it does | In → Out |
| --- | --- | --- | --- |
| 0 | `shiva-run` | Entry point / orchestrator. Recognizes "Shiva + ticket", runs the chain strictly one step at a time with human checkpoints, announces each step, never runs steps in parallel, never triggers autotests or git writes on its own. | ticket → drives skills 1…9 |
| 1 | `ab-context` | Collects everything about the ticket into one source-of-truth file: fields, AC, events/props tables, feature flags, comments, dev blocks, attachments — plus **child work items and linked issues** (for Epics the real detail lives in the children). Uses `jira-field-map.md`. Collector only, no interpretation. | Jira ticket → `_01_context.md` |
| 1b | `ab-design-context` | **Opt-in / lazy.** When the ticket has a Figma design, asks the user for a node-specific link, then extracts screens, states (default/error/empty/loading), exact texts/CTA copy, and flows; flags design-vs-ticket mismatches. | Figma link → `_01b_design.md` |
| 2 | `ab-requirements-review` | Grooming through a QA lens: runs each requirement through 4 test-design questions, finds gaps/contradictions, asks the human **one question at a time**, then produces a numbered `REQ-N` list. The garbage-in filter. | context → `_02_requirements.md` |
| 3 | `ab-checklist` | Decomposes each requirement into atomic checks — one verifiable statement each, with a traceability ID `REQ-N.M`. The "what to check". | requirements → `_03_checklist.md` |
| 4 | `ab-test-cases` | Expands each checklist item into full test cases (preconditions, steps, expected result) with IDs `TC-REQ-N.M`. Concrete steps use the app-navigation map + design file; each case is self-contained (no cross-references). Offers Testomatio export at the end. | checklist → `_04_test-cases.md` |
| 5 | `ab-pr-summary` | Builds a navigation map of the PR: which files/modules changed and what each does, so code review doesn't dig the whole diff. Read-only git, base `dev`. | branch diff → `_05_pr-summary.md` |
| 6 | `ab-code-review` | Checks each test case inside the PR code and assigns **PASS / FAIL / QA / N/A** with a code reference. Burden of proof is on PASS; when in doubt → QA/N-A, never PASS. Attention-first output. | cases + PR map → `_06_code-review.md` |
| 7 | `ab-impact-analysis` | Measures the PR's blast radius — what existing functionality might have broken. Traces dependencies with `git grep`, compares against the dev's stated impact, and surfaces **what the developer missed**. Output = risk areas to recheck, never "all safe". | diff + dev opinion → `_07_impact-analysis.md` |
| 9 | `ab-testomatio-export` | Publishes the cases to Testomatio: creates a suite for the ticket and uploads cases (steps, Jira link, manual/automated marker, traceability ID). A write to an external system — shows a plan and waits for explicit "yes". English only. | cases → Testomatio suite + `_09_testomatio-export.md` |
| — | `ab-app-map` | Maintenance generator (not part of a run). Rebuilds `standards/app-navigation-map.md` from the automation framework's Page Objects + Navigator when app navigation changes. | automation repo → `app-navigation-map.md` |

## The chain at a glance

```
Shiva <ticket>
   → 1  Context            (Jira + children → source of truth)
   → 1b Design context     (Figma, only if there's a design)
   → 2  Requirements       (grooming, questions one at a time)  ⏸ human
   → 3  Checklist          (atomic checks, REQ-N.M)
   → 4  Test cases         (TC-REQ-N.M)  → offer Testomatio export (9)
   ── code half (only if a feature branch exists) ──
   → 5  PR Summary
   → 6  Code review        (PASS / FAIL / QA / N-A)
   → 7  Impact analysis    (regression risk areas)
   → human finishes FAIL / N-A / QA manually
```

## Principles

- One skill = one narrow job; data passes via MD files, not chat memory.
- More prohibitions than permissions — the AI must add nothing of its own.
- A human reviews between every step ("ok / redo").
- End-to-end traceability by ID: `REQ-2` → `REQ-2.1` → `TC-REQ-2.1` → code-review verdict → Testomatio case.
- Artifacts are in the run language (RUN_LANG, from your message); test cases (4) and
  Testomatio (9) are always English. Generated directly in-language, no translation pass.
