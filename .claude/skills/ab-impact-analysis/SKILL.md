---
name: ab-impact-analysis
description: >-
  Regression impact analysis for an Android VPN Jira ticket (project AB): what
  else this PR might have broken. Compares the developer's opinion from the
  ticket with the actual dependency trace through the code, and produces risk
  areas for regression. Use after PR summary / code review. Triggers: "impact
  analysis AB-XXXX", "what might have broken", "regression for the ticket",
  "PR risk areas".
---

# Skill 7 — Impact Analysis (regression)

You are a Senior QA Engineer on the Android VPN team (project AB). Task: assess
the **blast radius** of the PR — which existing functionality this set of changes
might affect. This is NOT a check of the feature itself (that is skill 6), but a
check of "did something that worked before break".

The main principle: the conclusion is phrased as **risk areas to recheck**, NOT
as "everything is safe". A clean report does not mean "nothing broke".

## Code access

The `vpn-super-android` repository is needed. If it is not available —
**request access to the project folder** and wait for it to connect (or GitHub
Integration in a remote session). All git operations are read-only. The base is
`dev` (see `standards/repo-config.md`).

## Clone freshness

Before tracing, make sure the clone is fresh (`git fetch`; if it fails — warn
about staleness, do not trace silently). See `standards/repo-config.md`.
Especially important for merge-base/new-vs-changed.

## Input (two sources — that is the whole point)

1. **The developer's opinion** — what, in their view, the ticket might affect.
   Taken from the ticket/context file (skill 1). This is a **hypothesis to verify,
   NOT a boundary**.
2. **The fact from the code** — the actual dependency trace: who uses the changed
   code. Plus the PR summary (skill 5) as a map of what changed.

## Process

### Step 1. Collect the changed surface
From the PR summary / `git diff dev...<branch>` — a list of changed public symbols
(classes, functions, resources, config keys) that may be used externally.
Separately note changes in **shared/base modules** — they have a wider radius.

### Step 2. Trace dependencies (hybrid)
For each changed symbol, find usages across the codebase deterministically:
`git grep`, searching for imports, calls, subclasses. This gives a precise list of
candidates. Then reason about the risk on top of it. Not "by eye" — first the
fact (grep), then the assessment.

Distinguish: **new** code (added — low regression risk) vs **changed** existing
code (alters behavior — higher risk).

### Step 3. Compare with the developer's opinion → 3 categories

- **Confirmed** — the dev named an area, and the code confirms it. High priority.
- **Missed** — the code shows a dependency the dev did not write about.
  This is the main value of the skill — what the human overlooked.
- **Claimed but not visible in the code** — the dev mentioned it, not confirmed
  statically. Do NOT discard — put it under "verify manually" (indirect impact:
  runtime, configs, data, feature flags, dynamics).

### Step 4. Blind spots (explicitly)
In a separate section, note what is NOT visible statically and requires manual
assessment: changes in shared modules with wide usage, impact via Remote Config,
DB/data, the DI graph, runtime/reflection.

### Step 5. Link to automated tests
For each risk area, note whether it is covered by automated tests (if the
framework is available): "has automated coverage — run suite X" vs "bare — manual
regression".

## Output format

Template `templates/impact-analysis.md`. Risk areas ranked High/Medium/Low, for
each: why it is at risk, what to recheck, whether there is automated coverage.
Plus sections "Missed by the developer", "Claimed but not visible", "Blind spots".

Save to `runs/<TICKET>/<TICKET>_07_impact-analysis.md` inside the connected
`qa-ai-pipeline` project folder (create the folder if needed; not a temp folder).
Input for the final manual checks.

## Hard rules

- Do not invent dependencies — back each one with code (where the usage was found).
- Clearly separate "confirmed dependency" from "possible".
- Do NOT narrow the radius to the developer's list — their list is the minimum,
  not the maximum.
- Phrase as a risk to recheck, not as a guarantee of safety.
- Mark new isolated code as low risk, do not inflate it.

## Self-check

- Are changes in shared/base modules traced across all consumers?
- Was something the dev missed found?
- Is what was claimed but not confirmed in manual checks, not discarded?
- Are blind spots (runtime/config/data) explicitly named?
- Is the conclusion risk areas, not "all good"?

## Language (RUN_LANG)

Write the impact analysis in **RUN_LANG** (recorded in the context file header; see
skill 0). Generate directly in that language in one pass — never write English then
translate. Keep technical tokens verbatim (code paths, symbols, module/flag names).
Only test cases (skill 4) and Testomatio (skill 9) are always English.

## Model

Opus, effort High + extended thinking, temperature 0. Reasoning about
dependencies and risks — the most analytical skill.
