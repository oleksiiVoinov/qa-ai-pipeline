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

#### TC-REQ-2.1 [Component] [Type] <action — short outcome>

**Traceability:** TC-REQ-2.1 → REQ-2.1 → REQ-2
**Source:** <AC-N / event / comment / doc — verbatim reference>
**Variant:** <Social / Native / Both — omit if no A/B variants>

### Requirements (system setup)
- Feature flag `<flag_name>` enabled / disabled
- Charles Proxy: <endpoint + HTTP status, if needed>
- Firebase DebugView: `adb shell setprop debug.firebase.analytics.app com.free.vpn.super.hotspot.open` (if events)

### Pre-conditions (user / device state)
- User: <Free / Premium / trial / logged-out / secondary device>
- <screen/flow state before the test starts>

### Steps
1. <atomic step>
2. <atomic step>

### Expected result
- <observable outcome — verbatim values; event fires / does not fire>

**Type:** Functional | Negative | UI | API | E2E · **Priority:** High | Normal | Low

---

<!-- Repeat the block for each case. Group by logical suite.
     Title tags for grouping: [Entry Point] [Happy Path] [Error] [Event] [Offline]
     [Localization] [E2E]; type [Event]/[API]/[UI]; A/B: [Social]/[Native]/[Both].
     Always include an [E2E] group (journeys by phases). -->

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
