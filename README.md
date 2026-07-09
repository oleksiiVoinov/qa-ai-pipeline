# Shiva 🕉️ — AI pipeline for testing tickets

> "Empower the tester to the state of Shiva" — a many-armed QA: while you keep
> control, Shiva in parallel gathers context, grooms requirements, writes cases,
> reviews code, and selects regression.

A Claude-based AI pipeline for testing tickets (Android VPN project, AB). A chain
of narrow skills where the output of each step (an MD file) becomes the input to the next, with
a human controller standing between the steps.

_Repository: `qa-ai-pipeline` · Project code name: **Shiva**._

## Quick start

How you start a run depends on the environment:

**In Cowork** — just address Shiva with a ticket (any spelling — `Shiva` / `Шива` /
`Шіва`). The trigger fires the entry-point skill directly:

```
Shiva https://superunlimited.atlassian.net/browse/AB-1234
шива AB-1234
```

**In Claude Code** — use the slash command (`.claude/commands/shiva.md`), the ticket
is the argument:

```
/shiva https://superunlimited.atlassian.net/browse/AB-1234
/shiva AB-1234
```

Both do the same thing: run the entry-point skill (`shiva-run`, `.claude/skills/shiva-run/`),
which drives the default chain with human checkpoints. It never executes tests or
touches the automation repo.

Results are saved into the connected `qa-ai-pipeline` folder under
`runs/<TICKET>/` (e.g. `runs/AB-1234/AB-1234_03_checklist.md`). The folder is created
if missing; its contents are git-ignored (local-only), so a fresh clone starts with
an empty `runs/`.

Right after you start, Shiva shows a **step picker**: a few one-click **presets**
(Checklist only · Cases → Testomatio · Code pack · Full run) plus **Custom**
checkboxes (in Cowork) to tick exactly which skills to run (1, 1b, 2, 3, 4, 5, 6, 7,
9). It runs only what you pick, always in the fixed order 1→9, and pulls in any
upstream step a picked one depends on.

> Note: the `/shiva` slash command is a Claude Code feature (needs the skills
> available in that session). In Cowork use the `Shiva <ticket>` trigger — slash
> commands from `.claude/commands` don't apply there.

## Principle

- One skill = one narrow area of responsibility.
- Data travels between skills via MD files, not through the chat context.
- End-to-end traceability by ID: requirement `REQ-2` → checklist item `REQ-2.1` →
  test case `TC-REQ-2.1` → code-review verdict → Testomatio case. Any artifact traces
  straight back to its requirement.
- Hard rules: tie to a quote, no guessing allowed, preserve the
  original wording. We make errors visible rather than forbidding them.

## Repository structure

| Folder       | Purpose                                                          |
| ------------ | ---------------------------------------------------------------- |
| `.claude/skills/` | The skills themselves (one folder per skill = its name, with `SKILL.md` inside). This is the canonical location Cowork / Claude Code auto-discover when the folder is connected — no manual install. |
| `.claude/commands/` | The `/shiva` slash command (Claude Code). |
| `templates/` | Templates for the skills' output MD files.                       |
| `standards/` | Processing rules + distilled standards, incl. `app-navigation-map.md` (screens/elements/navigation, used by skills 3-4 to write concrete steps). |
| `golden/`    | Golden reference tickets and expected results for quality checks. |

## Skill chain

1. Context — Jira → a single context file (source of truth). Pulls child work items
   and linked issues too (for Epics, the testable detail lives in the children).
1b. Design context — Figma → screens/states/exact texts/flows. **Opt-in/lazy**: runs
   only when the ticket has a Figma link; feeds skills 3, 4, 6.
2. Requirements review — grooming, questions for the human (asked one at a time).
3. Checklist — decomposing requirements into atomic checks.
4. Test cases — generation from the checklist.
5. PR Summary — a navigation map of the PR (local project + git).
6. Code review — reconciling test cases with the code (PASS/FAIL/QA/N-A).
7. Impact Analysis — regression risk zones.
9. Testomatio Export — uploading cases into a new suite for the ticket (link to Jira, marking manual/auto).
10. Manual checks — human.

The ordering is strict, but the skills can be used partly independently.

## Requirements (what must be connected)

- **Atlassian (Jira) MCP** — required for all skills that read a ticket
  (1-5). Without it the skill cannot go to the ticket.
- **Testomatio MCP** — for working with test cases/suites (reconciling with golden references;
  skill 9 creates a suite and uploads cases — a write operation, with confirmation).
- **Figma MCP** — only for skill 1b (design context), and only when the ticket has a
  Figma link. Note: Dev-Mode tools may need a Dev/Full seat on the design's team;
  screenshots/metadata usually work on a View seat.
- **GitHub Integration or the project folder** — for the code skills (5-7): access to
  the `vpn-super-android` repository (see below).

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
Skills live in `.claude/skills/<name>/SKILL.md` — the canonical location that Cowork /
Claude Code auto-discover when the project folder is connected. **No manual install:**
edit the `SKILL.md`, and the change is picked up when the folder is connected (a fresh
session/turn). Just `git pull` to get others' updates. There is no separate "installed
copy" to keep in sync — the repo file IS what runs.

### Language of the artifacts
Artifacts are produced in the **run language** (RUN_LANG): the language of your
message, detected once (trigger spelling is a hint — `shiva`→EN, `шива`→RU,
`шіва`→UA) and recorded in the context file header. So `шива <ticket>` yields
Russian artifacts in `runs/`, `shiva <ticket>` yields English, etc.

Each artifact is generated **directly in RUN_LANG in one pass** — never English-then-
translate, never duplicate copies — so localization costs almost nothing. Technical
tokens (event/flag/ID/screen names, `REQ`/`TC` IDs) stay verbatim in any language.

**Two things are always English:** the **test cases** (skill 4) and the **Testomatio
export** (skill 9) — cases feed Testomatio, so keeping them English means zero
conversion at export.

## Status

Ready: entry point `shiva-run` (skill 0) + skills 1, 1b, 2-7, 9 + manual checks
(human). The chain ticket → context → (design context) → requirements review →
checklist → test cases → PR Summary → code review → impact analysis → Testomatio
export has been verified against golden references in `golden/` (paper skills 1-4 —
on 3 tickets; code skills 5-7 — on live code of the `AB-3003-Streaks` branch).
Artifacts are in the run language; test cases and Testomatio are always English.
