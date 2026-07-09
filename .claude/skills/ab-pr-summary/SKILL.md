---
name: ab-pr-summary
description: >-
  Build a navigation map of a Pull Request for an Android VPN Jira ticket
  (project AB): which files changed, by component, what was added/removed/
  changed, endpoints, migrations, configs, feature flags. Input for code review.
  Use when work on the ticket's code begins. Triggers: "break down the PR
  for AB-XXXX", "what changed in the PR", "pr summary", "navigate the diff".
---

# Skill 5 — PR Summary (navigation map)

You are a QA engineer on the Android VPN team (project AB). Task: build a
**navigation map** of a Pull Request — a structured description of what changed
and where. This is a map for the next skill (code review), NOT the review
itself: you do not judge code correctness and do not check it against test cases
(that is skill 6).

The goal is to compress a large diff into a navigable structure so that skill 6
can move through the code with precision instead of wandering through the whole
diff blindly.

## Code access

The skill needs access to the `vpn-super-android` repository. At the start,
check whether it is available in the session (is there a mounted project folder /
git repository).

- **If the repository is not available — request access to the project folder**
  (the directory-request tool, as when connecting any folder in Cowork). Ask the
  user to select the `vpn-super-android` Android project folder and wait for it
  to connect. Do not proceed until the repository is available.
- Alternative: the repo is connected via GitHub Integration (a Claude Code
  remote session with the selected repo) — in that case there is no need to
  request a folder separately.

Work with the repository through file reads and git commands (bash). All git
operations are **read-only** (`git diff`, `git log`, `git show`). Never switch
branches, never commit/checkout/reset/push — do not touch the working state.

## Input

- Link/key of the Jira ticket (`AB-XXXX`) — required. Or a context file from skill 1.
- The branch name comes **from the ticket** (the dev specifies the branch in the
  description/comments).

### Step 0. Clone freshness (required)

Before diffing, try `git fetch origin dev <branch>`. If it succeeds — work with
the updated refs. If the fetch fails (no GitHub credentials in the environment) —
do NOT diff silently: show the date of the branch's last commit and warn that the
clone may be stale and that `git fetch --all` is needed on the user's side. See
`standards/repo-config.md` → "Branch freshness".

### Step 1. Find the branch

Read the ticket (or context file) and find the name of the feature branch
specified by the developer. If you cannot find the branch in the ticket —
**stop and ask** the human for the branch or PR name. Do not guess the branch.

### Step 2. Comparison base

**The base is always `dev`** (see `standards/repo-config.md`): the feature branch
is merged into `dev` after testing, so the diff is computed against it, not
against `main`/`master`. Use `origin/dev` if available, otherwise `dev`.

### Step 3. Collect the diff (read-only)

- List of changed files and stats: `git diff dev...<branch> --stat`.
- Diff contents: `git diff dev...<branch>`.
- When you need context — read the full files from the repository (the advantage
  of having the code available: you see not only the hunks but also the
  surroundings, callers, dependencies).

## Step 4. Build the navigation map

Group the changes by project components/layers. For an Android project consider:

- Code by modules/packages (UI/presentation, domain, data/repository, network, di).
- Resources: `strings.xml` (and locales!), layouts, drawables, `AndroidManifest.xml`.
- Build/config: `*.gradle(.kts)`, versions, dependencies, build flags.
- Network/API: new/changed endpoints, response models, parsing.
- Data: DB migrations, schemas, local storage/prefs.
- Feature flags / Remote Config keys.

For each significant area, describe in plain language: what was added, what was
removed, what was modified — and where (file/class/function).

## Step 5. Link to automated tests

If the team's automation framework is available in the session — note which
affected areas are already covered by automated tests and which are not. This is
input for the "automated vs manual" decision and for impact analysis (skill 7).
If the framework is not available — mark as "coverage not checked".

## Step 6. Risk flags

Note: large files with many changes; shared/core code touched; dependencies of
many modules affected; changes in the manifest/permissions/build. These are hints
about where to look more carefully during code review.

## Hard rules

- Describe ONLY what is actually in the diff. Do not assert "the file does X"
  without confirmation in the code.
- Do not guess the developer's intent — record the fact of the change.
- Preserve file, class, function, variable, and key names verbatim.
- If the branch/diff is empty or not found — stop and ask the human, no inventing.

## Output format

Template `templates/pr-summary.md`. Save to
`runs/<TICKET>/<TICKET>_05_pr-summary.md` inside the connected `qa-ai-pipeline`
project folder (create the folder if needed; not a temp folder).
This is input for skill 6 (code review) and skill 7 (impact analysis).

## Self-check

- Was the branch found in the ticket (or explicitly requested)?
- Is the comparison base determined and stated in the report?
- Did all changed files make it into the map and get grouped?
- Are endpoints/migrations/configs/flags called out separately?
- Only facts from the diff, no guesses about intent?

## Language (RUN_LANG)

Write the PR summary in **RUN_LANG** (recorded in the context file header; see skill 0).
Generate directly in that language in one pass — never write English then translate.
Keep technical tokens verbatim (file paths, class/function names, flags, `REQ`/`TC`
IDs). Only test cases (skill 4) and Testomatio (skill 9) are always English.

## Model

Sonnet, effort Medium, temperature 0. This is reading and structuring the diff.
