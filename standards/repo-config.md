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

- Taken from the ticket (dev specifies the branch in the description/comments, or the
  Development panel / linked PR).
- Match the ticket key **anywhere** in the branch name, any case, with any prefix or
  separator: `feature/AB-3122_Killswitch`, `AB-3003-Streaks`, `bugfix/ab-3330`, etc.
  Look at both local and `origin/*` branches (`git branch -a`).
- If no branch matches — the skill stops and asks a human for it.

## Access model — local-first (read the whole thing)

- **In Cowork there is NO callable git / GitHub connector.** Do not wait for one, do
  not "try the connector then fall back." Go straight to the **connected
  `vpn-super-android` folder** and use its local git. (GitHub Integration only gives
  repo access in **Claude Code** sessions, not as a tool in Cowork.)
- **Never check out the branch and never change the working tree.** All git is
  read-only. Resolve the branch as the remote-tracking ref **`origin/<branch>`** and
  read it directly — you do NOT need it checked out. Compute:
  `MB=$(git merge-base origin/dev origin/<branch>)` then diff `"$MB"..origin/<branch>`,
  and read files with `git show origin/<branch>:<path>`.
- This is why the user does **not** need to switch branches locally — they only need
  the branch **fetched** (their IDE usually auto-fetches, so `origin/<branch>` is
  already present).

## Branch freshness (before diffing)

1. Try `git fetch origin dev <feature-branch>` once.
2. **Fetch succeeded** → use the fresh `origin/...` refs.
3. **Fetch failed** (no creds — normal in the Cowork sandbox): do NOT give up and do
   NOT diff silently.
   - If `origin/<branch>` **already exists locally** (the IDE fetched it) → **proceed
     on that ref** and note the branch's last-commit date so the user can judge
     staleness (`git log -1 --format=%cd origin/<branch>`).
   - If `origin/<branch>` is **absent** → stop and give ONE precise instruction:
     "In your local `vpn-super-android` clone run `git fetch origin <branch>` (no
     checkout needed), then say continue." Do not ask them to switch branches.

⚠️ Lesson from practice: a silent diff against a stale local snapshot produces false
conclusions (e.g. "the events are not in the branch," when they were merged in after
the snapshot). Fully hands-off fetching only works in **Claude Code** (it uses your
git credentials); the Cowork sandbox can read local refs but cannot fetch from GitHub.
