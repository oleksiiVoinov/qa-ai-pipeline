# Repository configuration (AB project)

The team's git-flow parameters for the code skills (5, 6, 7).

## Git flow

- Development is done in a separate feature branch.
- After passing testing, the feature branch is merged into `dev`.

## Base branch for comparison

- **base = `dev`**

All diffs in the code skills are computed as `git diff dev...<feature-branch>`
(three dots = from the merge-base; or `origin/dev...<feature-branch>`).
The comparison is against `dev`, not against `main`/`master`.

⚠️ If the branch is already merged into `dev`, the "does the file/module exist on dev"
check is misleading (dev already contains the merge). To determine "new vs changed" code
use the **merge-base** (`git merge-base origin/dev <branch>`), not the tip of `dev`.

## Feature branch name

- Taken from the ticket (dev specifies the branch in the description/comments).
- If the ticket has no branch — the skill stops and asks a human for it.

## Branch freshness (mandatory before diffing)

Before any diff/trace, the code skill MUST make sure the clone is fresh:

1. Try to pull the required branches: `git fetch origin dev <feature-branch>`
   (or `git fetch --all --prune`).
2. **If the fetch succeeded** — work with the updated `origin/...` refs.
3. **If the fetch failed** (no credentials/network — typical for a sandbox without GitHub access):
   do NOT diff silently. Show the date of the branch's last commit
   (`git log -1 --format=%cd origin/<branch>`) and warn: "the clone may be
   stale, run `git fetch --all` locally." Let the user decide.

⚠️ Lesson from practice: a silent diff against a stale local snapshot produces
false conclusions (e.g. "the events are not in the branch," when in fact they were merged in after the snapshot).

## Code access

- The repository is available in the session via GitHub Integration (repo/branch/PR
  selection in Claude Code remote sessions) or a mounted project folder.
- All git operations are read-only. Do not switch branches, do not run
  commit/checkout/reset/push.
