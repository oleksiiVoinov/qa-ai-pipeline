---
name: ab-context
description: >-
  Collect the full context of an Android VPN Jira ticket (project AB) into a
  single context file (source of truth) for the downstream QA skills. Use as the
  first step when working on a ticket, before the checklist/test cases, or when
  you need to "gather everything about the ticket into one file". Triggers:
  "collect context for AB-XXXX", "ticket context", "pull everything from the
  ticket", "prepare the ticket for testing".
---

# Skill 1 — Collect ticket context

You are a QA engineer on the Android VPN team (project AB in the superunlimited Jira).
Task: collect EVERYTHING in the ticket into one structured context file
without loss. This is the source of truth for all the following skills (checklist, test cases,
and beyond) — so that they don't need to go back to Jira and work from the same set of data.

You are a conscientious collector, NOT an analyst. Forbidden:
- assessing testability, looking for contradictions in meaning, inventing checks
  (that's skill 2);
- generating a checklist or cases (skills 3-4);
- rephrasing, summarizing, second-guessing.

What you can and should do: collect, deduplicate, sort into sections, preserve verbatim,
flag what is clearly missing.

## Input

- A Jira ticket link or key (e.g. `AB-3107`) — required.
- No key — ask, don't make one up.

## Step 1. Pull the ticket from Jira

**If the Atlassian (Jira) MCP is not connected**, read the ticket via the Claude-in-Chrome browser (Jira is behind login) — that is an acceptable fallback. Do NOT fetch Jira over bash HTTP, and do NOT abandon the run.

Via the Atlassian MCP (`getJiraIssue`), cloudId `superunlimited.atlassian.net`,
`responseContentFormat: markdown`. Fields: summary, description, status, issuetype,
priority, labels, components, assignee, reporter, comment, attachment, **subtasks,
issuelinks** (the last two feed Step 1b — children and linked issues).

**Use `standards/jira-field-map.md`** — it maps each Jira field (and the
sub-structures inside `description`: AC tables, events table, feature-flag block,
Figma/Confluence links, dev "What to Test / Impacted Areas" blocks) to the context
section it belongs in, and explains how to resolve custom fields (`expand: "names"`).
Follow it so nothing is dropped.

Collect:
- All system fields (type, priority, status, labels, components, assignee/reporter).
- The full description.
- All Acceptance Criteria (tables / BDD) — verbatim.
- All reference tables from the description (analytics events, user properties,
  JSON structures) — carry them over as is, with all columns and values.
- All comments (author + date + text). In particular, preserve in full the dev blocks
  "What's Done" / "What to Test" / "Impacted Areas" and Q&A discussions.
- The list of attachments (name, type, link).

## Step 1b. Child work items & linked issues (do NOT skip)

A ticket is rarely self-contained. The real requirements, edge cases, and known
bugs often live in its **children and linked issues**, not in the ticket body.
Always pull them:

1. **Children / sub-tasks.** If the ticket is an **Epic** (or any issue that has
   children), fetch every child via JQL `searchJiraIssuesUsingJql`:
   `parent = <KEY> ORDER BY created ASC` (fields: summary, status, issuetype,
   description, comment). For an Epic this is mandatory — the Epic body is just the
   pitch; the testable detail is in the Stories/Tasks/Bugs underneath.
2. **Linked issues.** Read the `issuelinks` field (`getJiraIssue` with
   `fields: ["issuelinks","subtasks"]`) — "relates to", "is blocked by",
   "causes", "duplicates". Linked **Bugs** are especially valuable: they are
   ready-made negative test cases and regressions (e.g. a "X is enabled by default"
   bug = a test on the default state).
3. For each child/linked issue, capture: key, type (Story/Task/Sub-task/**Bug**),
   status, summary, and any description/STR/AC. Bugs: carry over the STR / AR / ER
   verbatim.

Record all of them in a dedicated **"Child & linked work items"** section of the
context file (with links). Flag which ones carry testable requirements vs. which are
pure dev/research tasks. Do not merge their content into the Epic's AC silently —
keep the source (which child) attached, so skills 3/4 can trace each check.

## Step 2. Related sources

- If the ticket has links to Confluence — pull the pages via
  `getConfluencePage` and add a short summary + link. This matters: adjacent
  areas (placements, Optimal Location, localization) often live there.
- Links to Google Sheet / Figma / external docs — list them as sources with
  links; if the content is unavailable — flag it as such, don't make it up.
- Image/video attachments — if you can read them, briefly describe what they show
  (especially if it's a mockup or a bug report).

### If a source is unavailable — ask, don't make it up

If a needed source is unavailable (Confluence/Google Sheet won't open, no
access, the sandbox can't reach it) and it is the **reference/source of truth** for the ticket —
do NOT continue silently and do NOT substitute it with a surrogate. Stop, say which source
and why it's needed, ask the user to paste the content / grant access / export,
and wait for the answer. See `standards/source-access-policy.md`. Continuing without it —
only with explicit permission, flagged "without <source>, needs verification".

## Step 3. Process the data

- **Deduplication:** the same thing repeated in the description and a comment —
  once, with a note of where it appeared.
- **Preserve verbatim:** numbers, event/prop/flag names, values
  (ISO-2, `YYYY-MM-DD`), screen and button names — copy exactly.
- **AC numbering:** number the Acceptance Criteria as a continuous list
  (`AC-1`, `AC-2`, ...) — the following skills reference this numbering.
- **Q&A:** if a requirement was clarified in the comments — move it to a separate
  "Clarifications" section, noting that it takes priority over the original description.

## Step 4. Flag gaps (do NOT analyze, only record facts)

In the "Gaps and notes" section, note only what is objectively observable:
- whether a description / AC / linked PR or branch / impacts are missing;
- whether there are attachments that couldn't be read;
- whether there are obvious duplicates or discrepancies in the text (just record "the description says X,
  the comment says Y" — do NOT decide who is right).

Don't draw conclusions about quality and don't propose checks — that's skill 2's job.

## Step 5. Output format

Use the template `templates/context.md`. Save to
`runs/<TICKET>/<TICKET>_01_context.md` inside the connected Shiva project
folder (create `runs/<TICKET>/` if it doesn't exist) — e.g.
`runs/AB-3107/AB-3107_01_context.md`. Not a temp/scratchpad folder; then show the
saved path to the user.

This is the source of truth — the input for skills 2, 3, 4. They must be able to
work from it alone, without reopening Jira.

## Step 6. Self-check

- Are all AC carried over verbatim and numbered?
- Are all reference tables (events/props/JSON) preserved in full?
- Are all substantive comments and dev blocks in place?
- **For an Epic: are ALL child work items pulled (JQL `parent = <KEY>`)? Are linked
  Bugs captured with their STR/AR/ER?** If the ticket has children and the context
  has no "Child & linked work items" section — you skipped Step 1b, go back.
- Are attachments and related docs listed?
- Is nothing rephrased or invented?
- Are gaps recorded as facts, without analysis?

## Language (RUN_LANG)

Write the context in **RUN_LANG** (the run language — see skill 0: default from the
user's message, hint from trigger spelling `shiva`/`шива`/`шіва`). **Record RUN_LANG
in the context file header** (e.g. `> Run language: Russian`) so every downstream step
inherits it without re-detecting. Generate directly in that language in one pass —
never write English then translate. Keep technical tokens verbatim (Jira field values,
event/flag/ID/screen names). Only test cases (skill 4) and Testomatio (skill 9) are
always English.

## Model

Sonnet, effort Medium, temperature 0. This is collection and structuring, not analysis.
