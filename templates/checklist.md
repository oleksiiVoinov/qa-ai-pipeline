# Checklist — <TICKET-KEY> <Short title>

> Source: <ticket link> · Generated: <date> · Review status: ⬜ not reviewed

## Coverage (traceability)

| Block | Items | Sources |
| --- | --- | --- |
| UI | N | AC-1..3 |
| Logic | N | AC, comments |
| Events | N | events table |
| Data | N | JSON examples |
| Localization | N | dev "What to Test" |

---

## Checklist

### UI / display
- [ ] <atomic check> _(source: AC-2)_

### Logic / behavior
- [ ] <atomic check> _(source: comment Q&A)_

### Analytics / events
- [ ] Event `<event_name>` is sent on trigger <...> with parameters <...> _(source: events table)_

### Data
- [ ] <field combination> → <expected display> _(source: JSON example)_

### Localization / placements
- [ ] <translation check on the placement> _(source: dev "What to Test")_

---

## Raw / unclear requirements (NOT invented)

- <requirement> — <what is missing / question for the human> · `need more info`

## Cross-cutting checks (self-review)

- [ ] Localization in all supported languages
- [ ] Feature flag OFF = fully disabled
- [ ] Feature flag by country (if applicable)
- [ ] Offline behavior
- [ ] Fallback to English
- [ ] Update vs Reinstall
- [ ] Free vs VIP
- [ ] Multi-device / sign in-out (if there is an account)
- [ ] Negative / silent fail
