# Checklist — AB-3107 Server List V6 & Translate Cities/Countries

> Source: https://superunlimited.atlassian.net/browse/AB-3107 · Run of the ab-checklist skill (validation) · Review status: ⬜

## Coverage (traceability)

| Block | Items | Sources |
| --- | --- | --- |
| Integration | 3 | AC Integration |
| Language detection | 3 | AC Language Detection |
| Localization / placements | 5 | dev "What to Test" + AC |
| Data | 5 | JSON examples |
| Negative / edge | 3 | dev "What to Test" |
| Optimal location | 2 | glossary (adjacent feature) |

---

## Checklist

### Integration
- [ ] Server list is fetched via the V6 endpoint _(source: AC Integration)_
- [ ] The V6 response contains localized strings when available _(source: AC Integration)_
- [ ] The app is integrated with the Server List API V6 _(source: AC Integration)_

### Language detection
- [ ] The SL language follows the selected app language _(source: AC Language Detection)_
- [ ] The current locale is passed in the request if the API requires it _(source: AC Language Detection)_
- [ ] Supported languages match the app's localization languages _(source: AC)_

### Localization / placements
- [ ] SL translations for a FREE user _(source: dev "What to Test")_
- [ ] SL translations for a VIP user _(source: dev "What to Test")_
- [ ] SL translations in History _(source: dev "What to Test")_
- [ ] Translations on placements: notifications (free/vip), widget, Fastest location, Connection Detail _(source: dev Impacted Areas)_
- [ ] Customer support: country/city are NOT translated (English + country code) _(source: dev "What to Test")_

### Data (server object structure)
- [ ] country filled, city empty → cluster Country, server Country+number _(source: JSON)_
- [ ] country + city filled → cluster Country, server City+number _(source: JSON)_
- [ ] has a type value → cluster Country+type, server City+number _(source: JSON)_
- [ ] no country → server hidden from the list _(source: JSON)_
- [ ] city not translated → English by default _(source: JSON)_

### Negative / edge
- [ ] Unsupported device language → English on all placements _(source: dev "What to Test")_
- [ ] Offline → doesn't crash, bundled translations file _(source: dev "What to Test")_
- [ ] Update from a previous version to V6 → doesn't crash, translates _(source: dev "What to Test")_
- [ ] Sort / search / filter with localized names _(source: dev "What to Test")_

### Optimal location (adjacent feature)
- [ ] Optimal location: only Country filled → all servers of the country are pinged _(source: glossary)_
- [ ] Optimal location: Country + city → only servers of that city are pinged _(source: glossary)_

## Raw / unclear requirements (NOT invented)

- "City not translated" — how to display it in the UI (English placeholder?) — to be confirmed with PM · `need more info`
- AC "pass the locale if the API requires it" — does V6 require the locale in the request? · `need more info`

## Cross-cutting checks (self-control)

- [x] Localization in all languages
- [ ] Feature flag — N/A (no flag)
- [x] Offline
- [x] Fallback to English
- [x] Update migration
- [x] Free vs VIP
- [ ] Multi-device — N/A
- [x] Negative
