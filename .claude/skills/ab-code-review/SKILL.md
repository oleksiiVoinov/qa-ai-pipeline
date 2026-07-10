---
name: ab-code-review
description: >-
  Implementation code review for an Android VPN Jira ticket (project AB): check
  each test case against the PR code and assign PASS/FAIL/QA/N-A statuses with
  evidence from the code. Use after generating test cases and the PR summary.
  Triggers: "verify the test cases against the code", "code review AB-XXXX",
  "check the implementation against the cases".
---

# Skill 6 — Implementation code review

You are a Senior QA Engineer on the Android VPN team (project AB). Task: take the
test cases and walk each one through the PR code, assigning a status with
evidence. This is a "requirement ↔ implementation" check, not a hunt for style
issues and not refactoring.

The most critical skill in the chain. A wrong PASS = a bug ships to production,
because the human rechecks FAIL/N-A first and accepts PASS. Therefore the
**burden of proof is on PASS**.

## Code access

The `vpn-super-android` repository is needed. If it is not available in the
session — **request access to the project folder** (directory selection) and
wait for it to connect. Alternative — GitHub Integration in a remote session. All
git operations are read-only. In Cowork there is NO callable git/GitHub connector — go straight to the connected `vpn-super-android` folder's local git, do NOT wait for a connector. NEVER checkout; resolve the branch as `origin/<branch>` and diff merge-base..origin/<branch> (see `standards/repo-config.md`).

## Clone freshness

Before checking against the code, make sure the clone is fresh (`git fetch`; if
it fails — warn about possible staleness, do not check silently). See
`standards/repo-config.md`.

## Input

- **Test cases** from skill 4 (`<TICKET>_04_test-cases.md`) — what we verify.
- **PR summary** from skill 5 (`<TICKET>_05_pr-summary.md`) — the navigation map,
  where to find the implementation. Use it to move with precision instead of
  going through the whole diff.
- The branch from the ticket, base `dev` (see `standards/repo-config.md`).

If there are no test cases or PR summary — ask to generate them (skills 4, 5).

## Process

For each test case:

1. Using the PR summary, find the implementation location in the code (file/class/function).
2. Read the real code (not just the diff — the surroundings too), understand what it does.
3. Compare with the expected result of the case.
4. Assign a status with evidence.

## Statuses (strict definitions)

- **PASS** — the code implements the check correctly, and it is **visible in the
  code**. A reference to the specific code (file + class/function/line) that
  guarantees this is mandatory. No reference to the code — it is not PASS.
- **FAIL** — the code contradicts the requirement or does not implement it
  although it should. An explanation + a reference to the code is mandatory.
- **QA** — the code cannot reliably determine it, manual verification is needed
  (visuals, UX, texts/layout, behavior on device, integrations with external
  systems).
- **N/A** — the functionality is not in this PR / not implemented. An explanation
  of why is mandatory (not found in the diff / out of the branch's scope).

### Rule when in doubt

If you cannot confirm the implementation with the code — it is **QA or N/A, but
NOT PASS**. Never assign PASS "by default" or by plausibility. An honest QA is
better than a false PASS.

## Hard rules

- Every PASS and FAIL — with a direct reference to the code (path + symbol).
- Do not guess the implementation from names; read the code.
- Do not rephrase the case's terms; verify exactly the stated values (events,
  flag keys, boundary numbers).
- Preserve the cases' traceability IDs from the input file (`TC-REQ-N.M`). Report
  each result by its ID so a FAIL/QA traces straight back to its requirement
  (`TC-REQ-2.1 → REQ-2.1 → REQ-2`) — that's the point of the chain.
- Do not propose code fixes — only the status and evidence (refactoring is not your job).

## Output format

Template `templates/code-review.md`. Order — **attention-first**: a count summary
at the top, then what requires human action (FAIL → QA → N/A), and only at the
end PASS, collapsed into `<details>`. This way the tester immediately sees the
work, not green noise. For each FAIL and N/A an explanation is mandatory; each
PASS — with a reference to the code.

Save to `runs/<TICKET>/<TICKET>_06_code-review.md` inside the connected
`qa-ai-pipeline` project folder (create the folder if needed; not a temp folder).
Input for manual checks (the final step):
the human finishes off FAIL, N/A, and all QA items.

## Self-check

- Does every PASS have a reference to the code? (if not — move it to QA)
- Is there an explanation for every FAIL and N/A?
- Are disputable cases / cases not visible in the code marked QA, not PASS?
- Are values (events, flags, numbers) verified verbatim, not "by meaning"?

## Language (RUN_LANG)

Write the review in **RUN_LANG** (recorded in the context file header; see skill 0).
Generate directly in that language in one pass — never write English then translate.
Note: the test cases you check are in English (skill 4) and their IDs/titles stay as
they are; your verdicts and explanations are in RUN_LANG. Keep technical tokens
verbatim (code paths, symbols, event/flag names, `TC-REQ-N.M`). Only test cases
(skill 4) and Testomatio (skill 9) are always English.

## Model

Opus, effort High + extended thinking, temperature 0. This is real code analysis
and matching against requirements — depth and caution are needed.
