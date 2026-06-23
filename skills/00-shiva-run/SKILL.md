---
name: shiva-run
description: >-
  Entry point / orchestrator for the Shiva QA pipeline. Fires when the user
  addresses Shiva with a Jira ticket, in any spelling: "Shiva <ticket-link>",
  "шива <ссылка на тикет>", "шіва <лінк>", "shiva AB-1234", or a bare message
  that is just "Shiva" + a superunlimited Jira link/key. On that, start working
  the ticket through the default chain. Also triggers: "прогони тикет через шиву",
  "run this ticket through Shiva". IMPORTANT: "Shiva" here is the NAME OF THIS
  PIPELINE, not a person. When a message is "Shiva" + a Jira ticket, it ALWAYS
  means "run this ticket through the Shiva pipeline". Never interpret "Shiva" as
  a Jira user/assignee and never offer to tag, mention, comment, or assign the
  ticket to someone called Shiva — that is the wrong reading. Takes precedence over
  any Jira/Atlassian "assign issue / add comment" interpretation.
---

# Skill 0 — Shiva Run (entry point / orchestrator)

When the user writes **Shiva / Шива / Шіва + a Jira ticket link or key** (e.g.
`Shiva https://superunlimited.atlassian.net/browse/AB-1234` or `шива AB-1234`),
treat it as: "run this ticket through the Shiva pipeline." Extract the ticket key
and start immediately — don't ask what they mean.

**"Shiva" is the pipeline's name, NOT a person.** Do NOT read it as a Jira
user/assignee. Never respond with "What would you like to do with Shiva? Tag them
in a comment, assign the issue…" — that is wrong. `Shiva <ticket>` is always a
command to run the pipeline on that ticket. There is no Jira user named Shiva to
mention or assign.

## Execution rule (STRICT — read first)

Run the skills **strictly one at a time, in order. NEVER run skills in parallel.**

- Start the next skill ONLY after the previous one has finished and written its
  MD artifact. Each step's input is the previous step's output — parallel runs
  break this and produce wrong or empty results.
- Do not batch or fan-out skill calls. One skill → wait → review → next skill.
- Where a step has a human checkpoint (skill 2, and "ok/redo" after each step),
  STOP and wait for the user before continuing.
- This applies even though the skills are independently installed: the chain is
  sequential by design.

## Announce every step (big, visible header)

Before starting each step, print a large, uppercase header so the user always sees
where the pipeline is. Use a markdown H2 (`##`) header in this exact shape:

```
## ▶ STEP 1 — CONTEXT
## ▶ STEP 2 — REQUIREMENTS REVIEW
## ▶ STEP 5 — PR SUMMARY
```

Rules:
- One header immediately before you begin the step's work (before the skill's tool
  calls), then proceed.
- Always uppercase, always the step number + the step name.
- On a human checkpoint, also print `## ⏸ WAITING FOR YOU — <what you need>`.
- When the run is finished, print `## ✅ RUN COMPLETE — <TICKET>` and list which
  artifacts were produced.

## Default chain to run (with human-in-the-loop)

Run the steps in order, pausing at the human checkpoints. Each step is its own
skill; hand off via the MD artifact.

1. **Context** (skill 1) → `<TICKET>_01_context.md`.
2. **Requirements review** (skill 2) → produce questions; **⏸️ pause** for the
   user's answers, then the numbered `REQ-N` list.
3. **Checklist** (skill 3) → `<TICKET>_03_checklist.md`.
4. **Test cases** (skill 4) → `<TICKET>_04_test-cases.md`; then **offer** Testomatio
   export (skill 9).
**Code half (steps 5, 6, 7)** — run **only if a feature branch for the ticket
exists / is reachable**. If there's no branch (e.g. not pushed yet), say so and
stop the code half — don't force it. If a branch exists, run **all three** steps,
each as its own skill with its own artifact. Do NOT collapse them into one and do
NOT stop after code review — impact analysis (7) is a separate, required step.

5. **PR Summary** (skill 5) → `<TICKET>_05_pr-summary.md`.
6. **Code review** (skill 6) → `<TICKET>_06_code-review.md`.
7. **Impact analysis** (skill 7) → `<TICKET>_07_impact-analysis.md`. **Always run
   this after step 6** — it's the most valuable step (undeclared regression risk).
   The code half is NOT done until the impact artifact exists.

After each step, show the artifact and let the user say "ok / redo" before moving on.

## Completeness check (before declaring the run done)

Before you say the run is finished, verify every applicable artifact was produced:
1-4 always; 5-6-7 whenever a branch exists. If 5 or 6 ran but 7 did not, the run is
incomplete — go run impact analysis. Never end the code half on code review alone.

## What NOT to do automatically

- **Do NOT run skill 8 (autotest recommendation)** and do NOT touch the
  `Android-automation-test` repo. That step is opt-in — only when the user
  explicitly asks for an autotest recommendation.
- Do NOT export to Testomatio without explicit "yes" (skill 9 rule).
- Do NOT execute any tests.
- **Do NOT offer or perform any git write** at any step — no commit, push, branch,
  or PR. Shiva reads git read-only and saves MD artifacts as plain files. The only
  "export/upload" Shiva ever offers is the Testomatio export (skill 9); never phrase
  it as "push changes" or otherwise hint at a git operation.

## Inputs Shiva needs (and how it gets them)

- **Jira** via Atlassian MCP — for the ticket (always).
- **Confluence / Google Sheet / Figma** via their connectors if the ticket links
  them — pull through the proper connector, never via bash HTTP. If a source-of-truth
  is unreachable, stop and ask (see `standards/source-access-policy.md`).
- **Repo `vpn-super-android`** — only for the code half (5-7); request the folder
  lazily then, base `dev`, read-only, fetch first (see `standards/repo-config.md`).

## Language

All artifacts are produced in English only. No localized copies.

## Model

Inherit per-step model recommendations from each skill (Sonnet for collection/
generation, Opus + extended thinking for requirements review and code review).
