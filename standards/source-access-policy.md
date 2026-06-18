# Unavailable-source policy (for all skills)

When a skill needs a source (Google Sheet / Confluence / attachment / Figma mockup /
linked document) and it is **unavailable** (no access, the sandbox can't reach it,
not provided) — act as follows:

1. **Do NOT proceed silently** and **do NOT invent** what is missing.
2. **Do NOT substitute** the missing source with a surrogate (for example, "I'll take the events from
   the code instead of the SoT table") if that source was the golden reference / source of truth.
3. **Stop and ask the user.** Clearly state:
   - exactly which source is unavailable (link/name),
   - why it is needed for this step,
   - what can be done: paste the contents into the chat, upload (export/screenshot),
     grant access, or confirm an alternative.
4. **Wait for a reply** and continue with the provided data.
5. You may proceed without the source **only if the user explicitly allowed it** —
   and in that case mark the result as "obtained without <source>, requires verification."

## Use the right tool per source (do this BEFORE concluding "unavailable")

The sandbox shell (bash/curl) has NO credentials or network for Google/Atlassian —
HTTP fetch will fail. That failure does NOT mean the source is unavailable. Always
use the authorized MCP connector:

- **Google Sheet / Drive doc** → Google Drive connector `read_file_content`
  (by fileId from the URL, e.g. `14rY26w-...`). NOT curl/wget/HTTP in bash.
- **Confluence page** → Atlassian `getConfluencePage` (by pageId). NOT bash HTTP.
- **Jira ticket** → Atlassian `getJiraIssue` / `searchJiraIssuesUsingJql`.
- **Repo code** → git on the connected repo (read-only).

Only after the proper connector itself fails (no access / not connected) do you
fall back to the "Stop and ask the user" steps below. Never silently swap the
source of truth for a surrogate (e.g. code) because a bash HTTP attempt failed.

## Why

Silent degradation yields a confident but incomplete/incorrect result. This is especially dangerous
for analytics tickets, where the source of truth is an external table: the code may
diverge from it, and substituting the SoT with code masks exactly the discrepancies that
need to be found.

## Example (how it should be done)

> The SoT table (Google Sheet) is not reachable from the sandbox. It is the golden reference of events for
> this ticket; without it the implementation cannot be reliably verified. Please
> paste its contents into the chat or grant access. Which do you prefer?
> ⏸️ (waiting for a reply, not proceeding)
