# Impact Analysis — <TICKET-KEY> <Title>

> Ticket: <link> · Branch: `<branch>` · Base: `dev` · Collected: <date>
> Sources: developer's opinion (ticket) + code tracing + PR summary (05)

## Risk areas (for re-verification, ranked)

| Area | Risk | Why it's at risk | What to re-verify | Auto coverage |
| --- | --- | --- | --- | --- |
| <module/feature> | 🔴 High | <shared code changed that is used by ...> | <scenarios> | yes / no |
| <...> | 🟡 Medium | <...> | <...> | <...> |
| <...> | 🟢 Low | <new isolated code> | <smoke> | <...> |

## Missed by the developer (the main value)

- <dependency found in the code that the dev didn't mention> — `git grep` → `path/File`

## Claimed by the developer but not visible in the code

- <what the dev named but can't be confirmed statically> → manual check
  (indirect impact: runtime/config/data/flags)

## Confirmed (dev + code match)

- <area> — `path/File` uses the changed `<symbol>`

## Blind spots (not visible statically — assess manually)

- <shared modules with wide usage / Remote Config / DB / DI graph / runtime>

## Notes

- New isolated code: <module> — low regression risk.
- Base `dev`, diff `dev...<branch>`.
