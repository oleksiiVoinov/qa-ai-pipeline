# Jira field map (project AB → context file)

Reference for skill 1 (Context). Maps Jira issue fields to where they go in the
`<TICKET>_01_context.md` file, so the collector is robust and nothing is dropped.
cloudId: `superunlimited.atlassian.net`. Read with `getJiraIssue`,
`responseContentFormat: markdown`.

## System fields → context section

| Jira field | Context section | Notes |
| --- | --- | --- |
| `summary` | Title / header | the ticket title |
| `issuetype` | Header | **Epic / Story / Task / Sub-task / Bug** — drives behavior: Epic ⇒ pull children (skill 1, Step 1b) |
| `status` | Header | current workflow status |
| `priority` | Header | |
| `labels` | Header / notes | e.g. `Blue_App` |
| `components` | Header / notes | |
| `assignee`, `reporter` | Header | who owns / raised it |
| `description` | Requirements (the skeleton) | parse the sub-structures below |
| `comment` | Comments / Clarifications | preserve author + date + text; dev blocks verbatim |
| `attachment` | Attachments | name, type, link; describe mockups if readable |
| `subtasks` | Child & linked work items | feeds Step 1b |
| `issuelinks` | Child & linked work items | relates / blocks / causes / duplicates — Bugs are gold |

## Inside `description` — sub-structures to extract verbatim

| Pattern in the description | Goes to | Why |
| --- | --- | --- |
| **Acceptance Criteria** tables / BDD | Requirements (number as `AC-1`, `AC-2`…) | the testable skeleton |
| **Tracking / events** table (event + description) | Events table | each row = a future checklist item / test |
| **Feature Flag** block (`{ "enabled": …, "whitelistedCountries": … }`) | Requirements (flag) | exact key + shape, verbatim |
| **Figma / design** link | Sources → design | hand to skill 1b (design context) |
| **Confluence / PRD** link | Sources | pull via `getConfluencePage`; the AC often live in the PRD, not the ticket |
| **Success Metrics / KPI** table | Notes | context, usually not directly testable |
| Dev blocks: **What's Done / What to Test / Impacted Areas** (in description or comments) | Comments / Impacted areas | placements & scenarios outside the AC; impact list feeds skill 7 |

## Custom fields

Custom fields appear as `customfield_NNNNN` with opaque IDs. To resolve their
human names, request with `expand: "names"` (or `fields: ["*all"]`) and read the
`names` map. Map any QA-relevant custom field (e.g. a dedicated "Acceptance
Criteria", "QA notes", or "Impacted Areas" field) to the matching section above.
Do not hard-code IDs blindly — resolve the name first, then map by meaning.

## Rule

If a field is empty or absent, record that as a fact in "Gaps and notes" (don't
invent its content). If a linked source (Confluence/Figma/Sheet) is unreachable,
follow `standards/source-access-policy.md` — stop and ask, don't substitute.
