# Code Review — <TICKET-KEY> <Title>

> Ticket: <link> · Branch: `<branch>` · Base: `dev` · Collected: <date>
> Input: test cases (04), PR summary (05)

## Summary

| Status | Count |
| --- | --- |
| ❌ FAIL | N |
| 🔍 QA (manual) | N |
| ⚪ N/A | N |
| ✅ PASS | N |

> The order below is attention-first: first what needs human action, then PASS.

## ❌ FAIL (deal with first)

### <case> — FAIL
- **Requirement:** <verbatim>
- **What's in the code:** `path/File.kt` → <what's actually there>
- **Discrepancy:** <why it doesn't match>

## 🔍 QA — finish manually

| # | Test case | Why manual verification is needed |
| --- | --- | --- |
| <n> | <title> | <visual/UX/device/translation/persistence> |

## ⚪ N/A (not in this PR)

| # | Test case | Why |
| --- | --- | --- |
| <n> | <title> | <not found in diff / out of scope> |

---

<details>
<summary>✅ PASS (N) — implemented, verified against the code</summary>

| # | Test case | Evidence (code) | Comment |
| --- | --- | --- | --- |
| <n> | <title> | `path/File.kt` → `funcName()` | <how it's implemented> |

</details>
