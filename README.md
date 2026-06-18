# Shiva 🕉️ — AI pipeline for testing tickets

> "Empower the tester to the state of Shiva" — a many-armed QA: while you keep
> control, Shiva in parallel gathers context, grooms requirements, writes cases,
> reviews code, and selects regression.

A Claude-based AI pipeline for testing tickets (Android VPN project, AB). A chain
of narrow skills where the output of each step (an MD file) becomes the input to the next, with
a human controller standing between the steps.

_Repository: `qa-ai-pipeline` · Project code name: **Shiva**._

## Principle

- One skill = one narrow area of responsibility.
- Data travels between skills via MD files, not through the chat context.
- Hard rules: tie to a quote, no guessing allowed, preserve the
  original wording. We make errors visible rather than forbidding them.

## Repository structure

| Folder       | Purpose                                                          |
| ------------ | ---------------------------------------------------------------- |
| `skills/`    | The skills themselves (one folder per skill, with `SKILL.md` inside). |
| `templates/` | Templates for the skills' output MD files.                       |
| `standards/` | Processing rules + distilled standards (ISTQB and internal).     |
| `golden/`    | Golden reference tickets and expected results for quality checks. |

## Skill chain

1. Context — Jira → a single context file (source of truth).
2. Requirements review — grooming, questions for the human.
3. Checklist — decomposing requirements into atomic checks.
4. Test cases — generation from the checklist.
5. PR Summary — a navigation map of the PR (local project + git).
6. Code review — reconciling test cases with the code (PASS/FAIL/QA/N-A).
7. Impact Analysis — regression risk zones.
8. Test Selection — recommending autotest classes by risk zone (Android-automation-test repo).
9. Testomatio Export — uploading cases into a new suite for the ticket (link to Jira, marking manual/auto).
10. Localize Run — optional translated copy of the run (`runs/<TICKET>-RU/` etc.), driven by `RUN_LANGUAGE` in `standards/config.md`.
11. Manual checks — human.

The ordering is strict, but the skills can be used partly independently.

## Requirements (what must be connected)

- **Atlassian (Jira) MCP** — required for all skills that read a ticket
  (1-5). Without it the skill cannot go to the ticket.
- **Testomatio MCP** — for working with test cases/suites (reconciling with golden references;
  skill 9 creates a suite and uploads cases — a write operation, with confirmation).
- **GitHub Integration or the project folder** — for the code skills (5-7): access to
  the `vpn-super-android` repository (see below).
- **The `Android-automation-test` folder** — for skill 8 (autotest recommendation):
  access to the autotest repository.

## How to run

### Paper skills (1-4: context, review, checklist, test cases)
They work through the Jira MCP — there is NO need to connect the project folder. Give the skill the ticket
key (`AB-XXXX`) — it will go to Jira on its own.

### Code skills (5-7: PR Summary, code review, impact analysis)
They need access to the repository's code.

1. You run the skill with a ticket key. If the project folder is not connected — **the skill
   will request access** to the folder itself (a folder picker opens), and you select
   `vpn-super-android`.
2. Make sure the required feature branch from the ticket is pulled locally (`git fetch`),
   and that the `dev` branch (the comparison base) is present too.
3. The skill takes the branch name from the ticket, compares it with `dev`, and builds the result.
   All git operations are read-only.

> Alternative: a Claude Code remote session with the chosen repo via GitHub
> Integration — then there is no need to mount the folder, the repo is available from the integration.

### Skill development rule
You always edit a skill in this repository (`skills/<...>/SKILL.md`), then
reinstall it in Capabilities. The installed copy is the "compiled"
version, we don't touch it by hand; the repository is the source of truth.

### Language of the artifacts
All artifacts are produced in **English** (source of truth). For a localized
reading copy, set `RUN_LANGUAGE` in `standards/config.md` (`Russian` / `Ukrainian`);
skill 10 then creates `runs/<TICKET>-RU/` (or `-UK/`) translated from the English
artifacts. Empty `RUN_LANGUAGE` → no localized copy. Localized copies live under
`runs/` and are git-ignored.

## Status

All 10 skills (1-10) + manual checks (step 11 — human) are ready. The full chain
ticket → context → requirements review → checklist → test cases → PR Summary →
code review → impact analysis → autotest recommendation → Testomatio export →
localize run has been verified against golden references in `golden/` (paper skills
1-4 — on 3 tickets; code skills 5-7 — on live code of the `AB-3003-Streaks` branch;
skill 8 — on the live `Android-automation-test` repo). The project is in English;
optional localized run copies via `RUN_LANGUAGE`.
