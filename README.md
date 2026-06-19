# Shiva 🕉️ — AI pipeline for testing tickets

> "Empower the tester to the state of Shiva" — a many-armed QA: while you keep
> control, Shiva in parallel gathers context, grooms requirements, writes cases,
> reviews code, and selects regression.

A Claude-based AI pipeline for testing tickets (Android VPN project, AB). A chain
of narrow skills where the output of each step (an MD file) becomes the input to the next, with
a human controller standing between the steps.

_Repository: `qa-ai-pipeline` · Project code name: **Shiva**._

## Quick start

Just address Shiva with a ticket (any spelling — `Shiva` / `Шива` / `Шіва`):

```
Shiva https://superunlimited.atlassian.net/browse/AB-1234
шива AB-1234
```

That fires the entry-point skill (`shiva-run`, `skills/00-shiva-run/`), which runs
the default chain with human checkpoints. It does NOT auto-run skill 8 (autotests)
and never touches the automation repo unless you explicitly ask.

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
| `standards/` | Processing rules + distilled standards, incl. `app-navigation-map.md` (screens/elements/navigation, used by skills 3-4 to write concrete steps). |
| `golden/`    | Golden reference tickets and expected results for quality checks. |

## Skill chain

1. Context — Jira → a single context file (source of truth).
2. Requirements review — grooming, questions for the human.
3. Checklist — decomposing requirements into atomic checks.
4. Test cases — generation from the checklist.
5. PR Summary — a navigation map of the PR (local project + git).
6. Code review — reconciling test cases with the code (PASS/FAIL/QA/N-A).
7. Impact Analysis — regression risk zones.
8. Test Selection — recommending autotest classes by risk zone. **Opt-in**: runs only on an explicit autotest-recommendation request, NOT on a generic ticket run; reads `Android-automation-test` lazily (only when this step runs).
9. Testomatio Export — uploading cases into a new suite for the ticket (link to Jira, marking manual/auto).
10. Manual checks — human.

The ordering is strict, but the skills can be used partly independently.

## Requirements (what must be connected)

- **Atlassian (Jira) MCP** — required for all skills that read a ticket
  (1-5). Without it the skill cannot go to the ticket.
- **Testomatio MCP** — for working with test cases/suites (reconciling with golden references;
  skill 9 creates a suite and uploads cases — a write operation, with confirmation).
- **GitHub Integration or the project folder** — for the code skills (5-7): access to
  the `vpn-super-android` repository (see below).
- **The `Android-automation-test` folder** — only for skill 8 (autotest
  recommendation), and only when you explicitly ask for that step. Do NOT connect it
  for a normal ticket run; skill 8 requests it lazily when it actually runs.

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

### When the app navigation changes
Skills 3-4 write steps using `standards/app-navigation-map.md`, generated from the
automation framework. When navigation changes (new screens, renamed buttons, new
paths):

1. Make sure the automation framework (`Android-automation-test`) is updated for the
   new navigation — the map is built from it.
2. Connect that repo to the session and `git pull` it (fresh).
3. Tell Shiva: **`regenerate app map`** (also: "update navigation map" /
   "rebuild app-navigation-map"). This runs the `ab-app-map` skill, which re-reads
   the Page Objects + Navigator and rewrites `standards/app-navigation-map.md`.

This is a periodic maintenance action, not part of a normal ticket run.

### Skill development rule
You always edit a skill in this repository (`skills/<...>/SKILL.md`), then
reinstall it in Capabilities. The installed copy is the "compiled"
version, we don't touch it by hand; the repository is the source of truth.

### Language of the artifacts
All artifacts are produced in **English** only.

## Status

Ready: entry point `shiva-run` (skill 0) + skills 1-9 + manual checks (human).
Skill 8 (Test Selection) is opt-in: it runs only on an explicit request and reads
the automation repo lazily, so a normal ticket run never touches the automation
framework. The chain ticket → context → requirements review → checklist → test
cases → PR Summary → code review → impact analysis → Testomatio export has been
verified against golden references in `golden/` (paper skills 1-4 — on 3 tickets;
code skills 5-7 — on live code of the `AB-3003-Streaks` branch; skill 8 — on the
live `Android-automation-test` repo). All artifacts are English only.
