---
name: ab-localize-run
description: >-
  Create a localized copy of a completed Shiva run. Reads RUN_LANGUAGE from
  standards/config.md; if it is set (Russian/Ukrainian), copies runs/<TICKET>/
  into runs/<TICKET>-<LANG>/ and translates every artifact into that language.
  Use as the final step after a run. Triggers: "localize run", "translate the
  run", "make a localized copy of AB-XXXX".
---

# Skill 10 — Localize Run

You produce a translated copy of a finished run's artifacts, for team members who
prefer reading in another language. The English artifacts remain the source of
truth; this is a convenience copy.

## Step 0. Read the language setting

Read `RUN_LANGUAGE` from `standards/config.md`.

- If `RUN_LANGUAGE` is **empty** → do nothing. Stop silently (no folder, no output).
- If set to `Russian` → target suffix `-RU`.
- If set to `Ukrainian` → target suffix `-UK`.
- Any other non-empty value → ask the user to confirm the language and suffix.

## Step 1. Copy the run folder

Given the run at `runs/<TICKET>/`, create `runs/<TICKET>-<LANG>/` and copy every
artifact file into it (same filenames, same structure).

## Step 2. Translate each artifact

Translate the prose of every copied artifact into the target language. **Do not
translate**, keep verbatim:

- code, file paths, FQCNs, git commands;
- event names, property keys, feature-flag keys, enum values (e.g. `home_tap_connect`,
  `ab_3235_social_signup`, ISO-2 codes, `YYYY-MM-DD`);
- ticket keys, URLs, Testomatio IDs;
- the test-case section markers `### Requirements` / `### Steps` / `### Expected result`
  (keep as-is — they are the team's fixed format);
- status tokens `PASS` / `FAIL` / `QA` / `N/A`.

Translate everything else (descriptions, explanations, comments, table prose).
Preserve markdown structure and tables exactly.

## Step 3. Mark the copy

At the top of each localized file add a one-line note:
`> Localized copy ({{LANG}}) generated from the English source of truth. Do not edit here — edit the English version.`

## Hard rules

- Never translate identifiers, code, or fixed-format markers (see Step 2).
- Never treat the localized copy as source of truth — it is generated from English.
- If `RUN_LANGUAGE` is empty, produce nothing.
- The localized folder lives under `runs/` (git-ignored) — it is not committed.

## When to run

Automatically offer / run this as the last step of a full run, after all other
artifacts are ready and (optionally) exported to Testomatio.

## Model

Sonnet, effort Medium, temperature 0. This is translation, not analysis.
