# Test Cases — <TICKET-KEY> <Short title>

> Source: <ticket link> · Generated: <date> · Review status: ⬜ not reviewed

## Coverage (traceability)

| Test ID | Test | Checklist item | Requirement | Type |
| --- | --- | --- | --- | --- |
| TC-REQ-2.1 | <test title> | REQ-2.1 | REQ-2 | functional / negative / data / localization / analytics |

<!-- Full chain: TC-REQ-2.1 → REQ-2.1 (checklist) → REQ-2 (requirement) → AC/source.
     Every test below must be in this table. If there is no REQ-N list (built straight
     from the ticket), use AC IDs: TC-AC-2.1 → AC-2.1 → AC-2. -->

---

## Test cases

### <Group / Suite, e.g. UI / Events / Logic>

#### TC-REQ-2.1 — <Test title, short, to the point of the check>

**Traceability:** TC-REQ-2.1 → REQ-2.1 → REQ-2
**Source:** <AC-N / event / comment / doc — verbatim reference>

### Requirements
- <precondition: user type, feature/flag state, test data>

### Steps
1. <step>
2. <step>

### Expected result
<expected result — verbatim values from the ticket>

---

<!-- Repeat the block for each case. Group by logical suite. -->

## Raw / unclear requirements (NOT invented)

- <requirement> — <what is missing, what to ask the human> · `need more info`

## Cross-cutting checks (self-review)

- [ ] Localization in all supported languages
- [ ] Feature flag OFF = fully disabled
- [ ] Feature flag by country (if applicable)
- [ ] Offline behavior
- [ ] Fallback to English for an unsupported language
- [ ] Update vs Reinstall
- [ ] Free vs VIP
- [ ] Multi-device / sign in-out (if there is an account)
- [ ] Negative / silent fail
