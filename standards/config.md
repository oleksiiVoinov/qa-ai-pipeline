# Shiva — Run Configuration

Per-user settings. Each team member sets their own values here (do not commit
personal overrides if the team prefers a shared default — keep it simple for now).

## RUN_LANGUAGE

Controls the optional localized copy of a run (skill 10 `localize-run`).

- `RUN_LANGUAGE =` *(empty)* — default. No localized copy is produced. All
  artifacts stay in English only.
- `RUN_LANGUAGE = Russian` — after a run, skill 10 creates `runs/<TICKET>-RU/`
  with all artifacts translated to Russian.
- `RUN_LANGUAGE = Ukrainian` — same, into `runs/<TICKET>-UK/`.

```
RUN_LANGUAGE =
```

Language → folder suffix map:
- Russian → `-RU`
- Ukrainian → `-UK`

If `RUN_LANGUAGE` is empty, skill 10 does nothing (skipped silently).

> The English artifacts are always the source of truth. The localized copy is a
> convenience translation generated from them, never the other way around.
