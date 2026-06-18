# The AB team's "ticket → tests" pattern (derived from 3 golden references)

Analysis of AB-3107 (16), AB-3162 (30), AB-3003 (51). This is the rule base for the generation skill.

## 1. Test-case format (common to all)

Each case in Testomatio = title + body with three sections:

```
### Requirements      ← preconditions (user type, feature/flag state, test data)
### Steps             ← numbered steps (1. 2. 3. ...)
### Expected result   ← expected result
```

This is NOT a bare checklist, but a test case with steps. The title is short and describes the check.
All cases are `state: manual`, `priority: normal`.

## 2. Requirement sources (where to pull items from)

In order of importance — all three tickets confirm this:

1. **Acceptance Criteria** (tables / BDD scenarios) — the main skeleton.
2. **Dev comment "What to Test" / "Impacted Areas"** — provides placements and scenarios not present in the AC (almost the entire AB-3107 set comes from here).
3. **Reference tables** (GA4 events, user props, JSON structures) — each row = a test.
4. **Clarifications in comments (Q&A)** — resolve raw requirements and become the source of truth (AB-3003: streak-break logic, CTA behavior by state, device-local).
5. **Related documentation** (Confluence, Google Sheet, Figma) — mention as a source, pull details from there.

## 3. Decomposition rules (how to turn a requirement into tests)

- Each AC / BDD scenario → ≥1 test.
- Each UI state → a separate test (icon 0 / active; popup 0-day / 1-day / multi-day).
- Each analytics event → a separate test (with verification of its parameters).
- Each user property → a separate test (or a logical group).
- Each combination of data fields → a separate test (equivalence/boundary: country/city/type filled / empty / absent).
- Each placement where the entity is displayed → a separate check (SL, notifications, widget, Connection Detail, Customer support).
- Each entry point into an action → a separate test (8 ways to connect in AB-3003 — added by test design, NOT from the AC).

## 4. Cross-cutting checks the team adds almost always

These checks appear in tickets regardless of whether they are spelled out in the AC:

- **Localization** across all supported languages (list from Confluence Localisation).
- **Feature flag OFF** = complete disabling of the feature (UI + logic + analytics), not just hiding.
- **Feature flag by country** (whitelistedCountries) — if the flag is country-dependent.
- **Offline** behavior (does not crash, falls back to built-in data).
- **Fallback** for an unsupported language → English.
- **Update vs Reinstall** (update does not reset / reinstall resets).
- **Free vs VIP** user.
- **Multi-device / sign in-out** where there is an account.
- **Negative / silent fail** (an error does not block startup, no crashes).

## 5. Sanity / App Checks (the standard regression from dev comments)

The team runs a fixed sanity set on every QA build:
App boot; Ads displayed; Connection on IKEv2/IPsec, OpenVPN, Super, V2Ray;
Purchase; Connection as premium; the feature itself works.

## 6. Hard rules for the skill (anti-hallucination)

- Preserve the ticket's terminology verbatim: event names (`vpn_connection_failed`),
  props (`auto_renew_status`), flags (`feature_ab_3003_streaks`), values
  (ISO-2, YYYY-MM-DD), screen names. No "generalization."
- Tie each test to a source (which AC / event / comment / doc).
- Do NOT invent a raw requirement: leave a placeholder marked "need more info"
  (as in the AB-3162 reference) rather than making up steps.
- Carry over numbers and boundary values exactly (≥1 connection, 3/5/10/20/50/100 days).
- Do not reduce the set to the AC alone — apply the decomposition rules (§3) and cross-cutting checks (§4).

## 7. Volume reference points (sanity check on completeness)

- Sparse AC + rich dev comment → ~16 tests (AB-3107: 7 AC → 16).
- Table of events/props → ~30 (AB-3162: each row → a test).
- 20 BDD AC + events + states → ~51 (AB-3003).
If a rich ticket yields 5-6 tests — the skill underdid the decomposition.
