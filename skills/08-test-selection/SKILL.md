---
name: ab-test-selection
description: >-
  Automated-test recommendation for an Android VPN Jira ticket (project AB):
  which existing Appium/TestNG test classes to run for the changes and risk
  areas. Maps the impact analysis/PR to classes in the Android-automation-test
  repository and provides run commands. Use at the end of the chain, before
  manual checks. Triggers: "which automated tests to run", "recommend tests
  AB-XXXX", "regression set for the ticket", "what automated tests to run".
---

# Skill 8 — Automated-test recommendation

You are a QA Automation engineer on the Android VPN team (project AB). Task: based
on the ticket's changes and risk areas, recommend which **existing** automated
test classes to run, and produce run commands. You do NOT write new tests and do
NOT run them (a device/Appium/keys are required) — you only select the set.

## Access

The `Android-automation-test` automation repository is needed. If it is not
available in the session — **request access to the folder** and wait for it to
connect. Read-only. See `standards/automation-repo.md` (structure, mapping
signals, commands).

## Repository freshness

Before selecting, make sure both the project repo and the automation repo are
fresh (`git fetch`; if it fails — warn about staleness). See
`standards/repo-config.md`.

## Input

- **Impact analysis** (`<TICKET>_07_impact-analysis.md`) — risk areas (the main source).
- **PR summary** (`<TICKET>_05_pr-summary.md`) — affected areas/placements.
- **Test cases** (`<TICKET>_04_test-cases.md`) — what is verified at all.
- If they are absent — you can work directly from the ticket, but warn that
  without the impact analysis the selection is coarser.

## Process

### Step 1. Collect "what is affected"
From the impact analysis take the risk areas (High/Medium prioritized) and the
affected placements/features. From the PR summary — the specific screens/components.

### Step 2. Find classes in the repository (deterministically)
For each area, find the real classes by searching the repo (`git grep` / find):
- by **class name** (feature): `StreakTest`, `ServerListVIPTest`, `MainScreenTest`,
  `ConnectionReportTest`, `ProtocolsTest`, `NotificationTest`...
- by **package** (user type): `users/free`, `users/vip`, `users/mpf`;
- by **Allure** `@Feature` / `@Story` (grep by the area's text).

Recommend ONLY classes that actually exist (confirm their presence by searching
and use the full FQCN). Do not invent class names.

### Step 3. Rank the set
- **Direct** — a class directly for the ticket's feature (e.g. `StreakTest` for AB-3003).
- **Regression** — classes for the risk areas from impact (affected screens/flows).
- **Smoke** — the basics: connection by protocols, app boot, if the
  connection flow/DI/shared code is affected.

### Step 4. Note coverage gaps
Risk areas for which there is NO suitable class (e.g. localization, Remote Config
behavior, persistence on update) — put under "no automated coverage → manual".
This is an honest picture, not a "fully covered" look.

## Output format

Template `templates/test-selection.md`. For each class: FQCN, why (which risk
area / feature), priority (Direct/Regression/Smoke). Ready commands
`./gradlew test --tests "<FQCN>"`. Separately — a "no automated coverage → manual
regression" block.

Save to `<TICKET>_08_test-selection.md`.

## Hard rules

- Only existing classes (confirm by searching, full FQCN).
- Each recommended class — tied to a risk area/feature.
- Do not run tests; only the recommendation and commands.
- Do not claim "fully covered" — honestly note gaps for manual regression.

## Self-check

- Does each class exist in the repository (verified by search)?
- Are High/Medium risk areas from impact covered by the recommendation or
  explicitly noted as a gap?
- Are the FQCNs correct, the commands working?
- Are coverage gaps moved to manual regression?

## Model

Sonnet, effort Medium, temperature 0. This is mapping and searching, not deep
analysis.
