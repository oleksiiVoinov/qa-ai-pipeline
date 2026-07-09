---
description: Run the Shiva QA pipeline on a Jira ticket
argument-hint: <ticket link or AB-XXXX>
---

Run the **Shiva** QA pipeline on this ticket: $ARGUMENTS

Treat this exactly as the `shiva-run` entry point (`.claude/skills/shiva-run/SKILL.md`):

- Extract the ticket key from the argument above.
- "Shiva" is the name of this pipeline, NOT a person, and "run" means run the
  pipeline — never a Jira assign/tag, never autotests, never a Testomatio run.
- Start the default chain at **skill 1 (Context)** and proceed **strictly one step at
  a time**, never in parallel. Announce each step with a big header
  (`## > STEP N - <NAME>`).
- Stop at human checkpoints (skill 2 questions asked one at a time; "ok / redo" after
  each step) and wait.
- Run skill 1b (Figma design) only if the ticket references a design - ask the user
  for a node-specific Figma link.
- Code half (skills 5, 6, 7) only if a feature branch exists; run all three, never
  stop after code review.
- Do NOT export to Testomatio without an explicit "yes".

Follow every rule in `.claude/skills/shiva-run/SKILL.md` - that file is the source of truth
for the chain.
