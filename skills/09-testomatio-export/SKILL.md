---
name: ab-testomatio-export
description: >-
  Export generated test cases to Testomatio: creates a new suite for an Android
  VPN Jira ticket (project AB) and uploads the cases into it with steps, a link
  to Jira, and a manual/automated-coverage marker. Use after generating test
  cases, to publish them for execution. Triggers: "push the cases to testomatio",
  "export test cases AB-XXXX", "create a suite in Testomatio".
---

# Skill 9 — Export test cases to Testomatio

You are a QA engineer on the Android VPN team (project AB). Task: publish the
generated test cases to Testomatio — create a suite for the ticket and add the
cases into it so they can be executed (including manually). You do not rewrite the
case contents — you transfer them as is.

**Language: always English.** Cases are exported in English regardless of
`RUN_LANGUAGE` — Testomatio is kept in a single language for the international team.
`RUN_LANGUAGE` only affects the local run copy (skill 10), never the export.

## When to offer

Once the test cases (skill 4) are ready, **proactively offer the user** to push
them to Testomatio ("Push these cases to Testomatio as a new suite for the
ticket?"). Run the export only if the user agreed.

## ⚠️ This is a write to an external system

Creating a suite and cases is an irreversible action in the team's Testomatio.
Before bulk creation, **show a plan** (suite name + how many cases) and **wait for
the human's confirmation**. Do not create anything before an explicit "yes".

## Input

- **Test cases** from skill 4 (`<TICKET>_04_test-cases.md`) — the primary source.
- Optionally: code review (06) and test-selection (08) — to mark which cases are
  covered by automated tests and which need to be run manually.
- The ticket's key and title (from the ticket/context).

## Process

### Step 1. Duplicate check
Find an existing suite in Testomatio for this ticket (`suites_list` / search by
title/key). If one already exists — **warn the human** and ask: create a new one,
add to the existing one, or cancel. Do not breed duplicates silently.

### Step 2. Plan and confirmation
Show: the new suite name (`AB-XXXX <title>`), the number of cases, the
manual/automated markers. Wait for "yes".

### Step 3. Create the suite
`suites_create`: title = `AB-XXXX <title>`, `link` = jira issue (`{type:"jira",
value:"AB-XXXX", action:"add"}`). Record the returned `suite_id`.

### Step 4. Create the cases
For each test case `tests_create`:
- `title` — the case heading (verbatim).
- `suite_id` — from step 3.
- `description` — the body in your format: `### Requirements` / `### Steps` /
  `### Expected result` (as in the Testomatio reference cases).
- `state` — `manual`.
- `priority` — from the case (default `normal`).
- `link` — jira issue `AB-XXXX`; plus a coverage marker: the tag/label `automated`
  for cases covered by automated tests (per skill 8), otherwise `manual-required`.

Preserve the terminology and values verbatim (events, flags, numbers).

### Step 5. Log
Produce a local log `<TICKET>_09_testomatio-export.md`: link to the suite, the
list of created cases (id + manual/automated status), what was skipped.

### Step 6. Return links to the user
After the upload, **always return links to the user**: a link to the suite
(`https://app.testomat.io/projects/<project>/suite/<suite_id>`) and direct links
to the created cases (`.../test/<test_id>`) — from the `url` field in the API
responses.

## Hard rules

- Create nothing without confirmation (step 2).
- Do not duplicate an existing suite silently (step 1).
- Transfer the case contents verbatim, do not rewrite.
- Link each case/suite to the Jira ticket.
- Mark manual vs automated-covered, so it is clear what to run manually.
- Do not run tests; do not set run statuses (that is not this skill's job).

## Output format

Template `templates/testomatio-export.md` — a local log of the operation.

## Self-check

- Was the duplicate suite checked before creation?
- Was the plan shown and confirmed by the human?
- Were all cases created with a description (Requirements/Steps/Expected) and a
  link to Jira?
- Are manual/automated marked correctly?

## Model

Sonnet, effort Medium, temperature 0.
