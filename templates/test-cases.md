# Test Cases — <TICKET-KEY> <Short title>

> Source: <ticket link> · Generated: <date> · Review status: ⬜ not reviewed

## Coverage (traceability)

| # | Test | Source | Type |
| --- | --- | --- | --- |
| 1 | <test title> | AC-2 / event X / comment | functional / negative / data / localization / analytics |

<!-- The coverage table is a "what comes from where" map. Every test below must be in it. -->

---

## Test cases

### <Group / Suite, e.g. UI / Events / Logic>

#### <Test title — short, to the point of the check>

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
