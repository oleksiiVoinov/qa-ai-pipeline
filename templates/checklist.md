# Checklist — <TICKET-KEY> <Short title>

> Source: <ticket link> · Generated: <date> · Review status: ⬜ not reviewed

## Coverage (traceability)

| Block | Item IDs | Sources |
| --- | --- | --- |
| UI | REQ-2.1..2.3 | AC-1..3 |
| Logic | REQ-3.1..3.2 | AC, comments |
| Events | REQ-4.1..4.5 | events table |
| Data | REQ-5.1 | JSON examples |
| Localization | REQ-6.1 | dev "What to Test" |

---

## Checklist

> Each item carries a traceability ID derived from its parent requirement:
> `REQ-<N>.<M>` (item M of requirement REQ-N). If there is no REQ-N list yet
> (working straight from the ticket), use the AC number instead: `AC-<N>.<M>`.
> Test cases (skill 4) will reference these IDs as `TC-REQ-N.M` / `TC-AC-N.M`.

### UI / display
- [ ] **REQ-2.1** <atomic check> _(source: AC-2)_

### Logic / behavior
- [ ] **REQ-3.1** <atomic check> _(source: comment Q&A)_

### Analytics / events
- [ ] **REQ-4.1** Event `<event_name>` is sent on trigger <...> with parameters <...> _(source: events table)_

### Data
- [ ] **REQ-5.1** <field combination> → <expected display> _(source: JSON example)_

### Localization / placements
- [ ] **REQ-6.1** <translation check on the placement> _(source: dev "What to Test")_

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
