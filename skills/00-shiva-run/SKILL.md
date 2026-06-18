---
name: shiva-run
description: >-
  Entry point / orchestrator for the Shiva QA pipeline. Fires when the user
  addresses Shiva with a Jira ticket, in any spelling: "Shiva <ticket-link>",
  "шива <ссылка на тикет>", "шіва <лінк>", "shiva AB-1234", or a bare message
  that is just "Shiva" + a superunlimited Jira link/key. On that, start working
  the ticket through the default chain. Also triggers: "прогони тикет через шиву",
  "run this ticket through Shiva".
---

# Skill 0 — Shiva Run (entry point / orchestrator)

When the user writes **Shiva / Шива / Шіва + a Jira ticket link or key** (e.g.
`Shiva https://superunlimited.atlassian.net/browse/AB-1234` or `шива AB-1234`),
treat it as: "run this ticket through the Shiva pipeline." Extract the ticket key
and start immediately — don't ask what they mean.

## Default chain to run (with human-in-the-loop)

Run the steps in order, pausing at the human checkpoints. Each step is its own
skill; hand off via the MD artifact.

1. **Context** (skill 1) → `<TICKET>_01_context.md`.
2. **Requirements review** (skill 2) → produce questions; **⏸️ pause** for the
   user's answers, then the numbered `REQ-N` list.
3. **Checklist** (skill 3) → `<TICKET>_03_checklist.md`.
4. **Test cases** (skill 4) → `<TICKET>_04_test-cases.md`; then **offer** Testomatio
   export (skill 9).
5-7. **Code half** (PR Summary → Code review → Impact) — run **only if a feature
   branch for the ticket exists / is reachable**. If there's no branch (e.g. an
   Epic, or not pushed yet), say so and stop the code half — don't force it.

After each step, show the artifact and let the user say "ok / redo" before moving on.

## What NOT to do automatically

- **Do NOT run skill 8 (autotest recommendation)** and do NOT touch the
  `Android-automation-test` repo. That step is opt-in — only when the user
  explicitly asks for an autotest recommendation.
- Do NOT export to Testomatio without explicit "yes" (skill 9 rule).
- Do NOT execute any tests.

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
