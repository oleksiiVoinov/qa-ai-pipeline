# Shiva — before your first run (checklist)

A short setup list so a run works and its results actually land in the project.

## One-time setup

1. **Get the project.** Clone or `git pull` the `qa-ai-pipeline-android` repo to your computer.
2. **Connect the folder in Cowork.** Select the `qa-ai-pipeline-android` folder as your
   working folder, so Shiva can save results into it.
3. **No manual install needed.** The skills live in `.claude/skills/` inside the repo
   and are auto-discovered once the folder is connected (step 2). To get updates, just
   `git pull` — editing/pulling a `SKILL.md` is enough; there's nothing to "install".
4. **Connect the connectors you'll use:**
   - **Jira (Atlassian) MCP** — required (every run reads the ticket).
   - **Testomatio MCP** — only if you'll export cases (step 9).
   - **Figma MCP** — only for tickets with a design (step 1b).
   - **`vpn-super-android` folder / GitHub** — only for the code half (steps 5-7).

## Start a run

5. **In Cowork:** type `Shiva <ticket link>`. **In Claude Code:** `/shiva <ticket>`.
   - Language: `шива <ticket>` → Russian artifacts, `shiva <ticket>` → English,
     `шіва <ticket>` → Ukrainian.
6. **Pick steps** in the checkbox picker (or a preset: Checklist only / Cases →
   Testomatio / Code pack / Full run).
7. **Answer the grooming questions** (step 2) — they come one at a time.
8. **Review each artifact** and say "ok" or "redo" before the next step.

## Where results go

9. Artifacts are saved in the connected folder under **`runs/<TICKET>/`** (e.g.
   `runs/AB-1234/AB-1234_03_checklist.md`). A fresh clone starts with an empty
   `runs/`; the folder is created on the first run. `runs/` is local-only (not
   committed to git).
   - If you see no `runs/` folder after a run, the folder wasn't connected in
     Cowork (step 2) — reconnect it and run again.

## Testomatio export (step 9)

10. Cases are pushed only after you confirm the plan ("create suite X, N cases?").
    On the first case-creation prompt, click **"Allow always"** so it doesn't ask per
    case. (In Cowork this may reset between sessions — a known app limitation.)

## Good to know

- Steps run **strictly one at a time** (never parallel); each step's input is the
  previous step's file.
- Test cases and Testomatio are **always English**; all other artifacts are in your
  run language.
- Shiva never runs autotests and never writes to git.
