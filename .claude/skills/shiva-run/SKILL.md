---
name: shiva-run
description: >-
  Entry point / orchestrator for the Shiva QA pipeline. Fires when the user
  addresses Shiva with a Jira ticket, in any spelling: "Shiva <ticket-link>",
  "шива <ссылка на тикет>", "шіва <лінк>", "shiva AB-1234", or a bare message
  that is just "Shiva" + a superunlimited Jira link/key. On that, start working
  the ticket through the default chain. Also triggers (with Shiva + a ticket):
  "прогони тикет через шиву", "run this ticket through Shiva", "шива сделай прогон",
  "шива прогони", "сделай прогон", "прогон", "do a run", "run it". IMPORTANT: here
  "прогон" / "run" means RUN THE SHIVA PIPELINE starting at Context (skill 1). It does
  NOT mean run autotests, execute tests, or start a Testomatio run. Never ask "where
  to run — autotests or Testomatio?"; never interpret "прогон/run" as a test
  execution. IMPORTANT: "Shiva" here is the NAME OF THIS
  PIPELINE, not a person. When a message is "Shiva" + a Jira ticket, it ALWAYS
  means "run this ticket through the Shiva pipeline". Never interpret "Shiva" as
  a Jira user/assignee and never offer to tag, mention, comment, or assign the
  ticket to someone called Shiva — that is the wrong reading. Takes precedence over
  any Jira/Atlassian "assign issue / add comment" interpretation. IMPORTANT: this is a
  documentation/QA pipeline — it is NOT Jenkins and NOT CI. Do NOT open the
  jenkins-android-farm skill or any CI/build tool, and do NOT web-search, to start a
  run. "Shiva pipeline" = the skills in this repo (1..9); "run the pipeline" = begin at
  skill 1 (Context), nothing to do with Jenkins, builds, or CI jobs.
---

# Skill 0 — Shiva Run (entry point / orchestrator)

When the user writes **Shiva / Шива / Шіва + a Jira ticket link or key** (e.g.
`Shiva https://superunlimited.atlassian.net/browse/AB-1234` or `шива AB-1234`),
treat it as: "run this ticket through the Shiva pipeline." Extract the ticket key.
Don't ask what they mean — the intent is clear. Your **first action** is the step
picker below (that is not a "what do you mean" question — it's a scope selector).

**Do NOT do these on `шива <ticket>` (wrong turns seen in real runs):**
- Do NOT open the **Jenkins** skill (`jenkins-android-farm`) or any CI/build tool.
  Shiva is a QA-documentation pipeline, not CI. "прогон / run the pipeline" here has
  nothing to do with Jenkins, builds, or pipeline *jobs*.
- Do NOT **web-search** to begin — you don't need it. Go straight to the ticket.
- To read the ticket use the **Atlassian (Jira) MCP**. If it isn't connected, reading
  the ticket via the Claude-in-Chrome browser is an acceptable fallback (Jira may be
  behind login) — only for fetching the ticket, not a reason to abandon the pipeline.
- The right first move is always: recognize the ticket -> show the step picker ->
  start at skill 1 (Context).

## Step picker (checkboxes — show this first)

Before running anything, present the **step picker** and run only what the user
selects. Offer it in two layers: first a short list of **presets** (one-click common
scenarios), and a **Custom** option that opens the full checkbox list. In Cowork use
the multi-select question tool (renders as checkboxes); in a plain chat, list the
options with `[ ]` and let the user reply.

### Presets (offer these first)

- **Checklist only** → 1 (+ 1b if there's a design) + 3. Output: the checklist.
- **Cases → Testomatio** → 1 (+ 1b) + 2 + 3 + 4 + 9. Full paper block, then push
  the cases to Testomatio.
- **Code pack** → 5 + 6 + 7. The code half (PR summary, code review, impact); returns
  the reports. (Testomatio push of manual items is a separate future option.)
- **Full run** → everything applicable (1, 1b, 2, 3, 4, 5, 6, 7, 9 as available).
- **Custom** → show the full checkbox list below and let the user tick freely.

A preset just pre-selects a set of steps — the execution rules (fixed order,
dependencies, human checkpoints) apply identically. The user may adjust a preset's
ticks before confirming.

### Full checkbox list (for Custom, or to fine-tune a preset)

Pre-tick the sensible defaults:
- ☑ 1 Context · ☑ 2 Requirements review · ☑ 3 Checklist · ☑ 4 Test cases
- ☑ 1b Design context — **pre-tick only if the ticket references a Figma design**
- ☑ 5 PR Summary · ☑ 6 Code review · ☑ 7 Impact analysis — **pre-tick only if a
  feature branch exists**; otherwise leave unticked / greyed
- ☐ 9 Testomatio export — off by default (needs explicit confirmation anyway)

Rules for the picker:
- **Order is always 1→9 regardless of what's ticked.** Run the ticked steps in that
  fixed order; skip the unticked ones. Never reorder.
- **Respect dependencies.** Each step's input is a previous step's artifact
  (2←1, 3←2, 4←3, 6←4+5, 7←5, 9←4). If the user ticks a step but not the
  upstream it needs, say so and either (a) auto-include the missing upstream (tell
  the user) or (b) ask them to point to an existing artifact. Do not run a step with
  no input.
- After the user confirms the selection, proceed with the strict one-at-a-time
  execution below.

**"Shiva" is the pipeline's name, NOT a person.** Do NOT read it as a Jira
user/assignee. Never respond with "What would you like to do with Shiva? Tag them
in a comment, assign the issue…" — that is wrong. `Shiva <ticket>` is always a
command to run the pipeline on that ticket. There is no Jira user named Shiva to
mention or assign.

**"прогон" / "сделай прогон" / "run" = run THIS pipeline, from the start.** Do NOT
ask "where to run — autotests or Testomatio?". Do NOT interpret it as executing
running autotests or as a Testomatio test run. The
only correct reaction to "Shiva <ticket> + сделай прогон" is to start the pipeline —
show the step picker, then run the ticked steps from **skill 1 (Context)** onward. No
clarifying question about *what kind* of run — there is only one: the Shiva pipeline.

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
1b. **Design context** (skill 1b) → `<TICKET>_01b_design.md` — run **only if the
   ticket references a Figma design**. The skill **asks you for a node-specific Figma
   link** (ticket links are often stale) and ⏸️ waits. If the ticket has no design,
   skip silently. Feeds screens/states/texts to skills 3, 4, 6.
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

## Where artifacts are saved (do NOT skip)

All artifacts go into the **connected `qa-ai-pipeline` project folder**, in
`runs/<TICKET>/` — e.g. `runs/AB-3271/AB-3271_01_context.md`. Rules:

- Save into the **mounted project folder the user selected**, NOT a temporary /
  scratchpad / working directory. In Cowork the session's temp folder is invisible to
  the user — a file left only there looks "lost" (no `runs/` appears in their repo).
- **Create `runs/<TICKET>/` if it doesn't exist** (a fresh git clone has an empty
  `runs/` — its contents are git-ignored on purpose, only `.gitkeep` is tracked).
- After writing each artifact, **present it to the user and state the exact saved
  path** so they can see where it landed.
- If for any reason you cannot write into the project folder (no write access), STOP
  and tell the user — do not silently save to a temp location.

## Completeness check (before declaring the run done)

Before you say the run is finished, verify every applicable artifact was produced:
1-4 always; 5-6-7 whenever a branch exists. If 5 or 6 ran but 7 did not, the run is
incomplete — go run impact analysis. Never end the code half on code review alone.

## What NOT to do automatically

- Do NOT touch the `Android-automation-test` repo during a run.
- Do NOT export to Testomatio without explicit "yes" (skill 9 rule).
- Do NOT execute any tests.
- **Do NOT offer or perform any git write** at any step — no commit, push, branch,
  or PR. Shiva reads git read-only and saves MD artifacts as plain files. The only
  "export/upload" Shiva ever offers is the Testomatio export (skill 9); never phrase
  it as "push changes" or otherwise hint at a git operation.

## Inputs Shiva needs (and how it gets them)

- **Jira** via Atlassian MCP — for the ticket (always).
- **Confluence / Google Sheet** via their connectors if the ticket links
  them — pull through the proper connector, never via bash HTTP. If a source-of-truth
  is unreachable, stop and ask (see `standards/source-access-policy.md`).
- **Figma** via the Figma MCP connector — handled by skill 1b (design context), and
  only when the ticket has a Figma link.
- **Repo `vpn-super-android`** — only for the code half (5-7); request the folder
  lazily then, base `dev`, read-only, fetch first (see `standards/repo-config.md`).

## Language (RUN_LANG)

Detect the **run language once** and record it. Default from the language of the
user's message; the trigger spelling is a hint: `shiva` → English, `шива` → Russian,
`шіва` → Ukrainian. The user can override ("run in English" / "на русском"). Skill 1
writes RUN_LANG into the context file header; every downstream step inherits it — do
NOT re-detect.

Rules (this is how we stay cheap):
- Generate each artifact **directly in RUN_LANG in a single pass**. NEVER generate in
  English and then translate — that doubles tokens. NEVER produce two language copies
  of the same artifact.
- Keep technical tokens verbatim regardless of language: event names, flags, IDs,
  screen/element names, code paths, `REQ-N.M` / `TC-REQ-N.M`.
- **Two exceptions are always English:** the **test cases (skill 4)** and the
  **Testomatio export (skill 9)** — because cases feed Testomatio, keeping them
  English means zero conversion at export.

So: steps 1, 1b, 2, 3, 5, 6, 7 → RUN_LANG; step 4 + step 9 → English.

## Model

Inherit per-step model recommendations from each skill (Sonnet for collection/
generation, Opus + extended thinking for requirements review and code review).
