# Test Cases — AB-3107 Implement Server List V6 & Translate Cities/Countries

> Source: https://superunlimited.atlassian.net/browse/AB-3107 · Run of the ab-test-cases skill (validation) · Review status: ⬜

## Coverage (traceability)

| # | Test | Source | Type |
| --- | --- | --- | --- |
| 1 | Server list is fetched via the V6 endpoint | AC Integration | functional |
| 2 | V6 response contains localized strings | AC Integration | functional |
| 3 | The SL language follows the app language | AC Language Detection | functional |
| 4 | The current locale is passed in the request (if the API requires it) | AC Language Detection | functional |
| 5 | Fallback to English if the language is unavailable in the API response | AC Edge | negative |
| 6 | SL translations for a FREE user | dev "What to Test" | localization |
| 7 | SL translations for a VIP user | dev "What to Test" | localization |
| 8 | SL translations in History | dev "What to Test" | localization |
| 9 | Translations on other placements (notif/widget/Fastest/Connect Detail) | dev Impacted Areas | localization |
| 10 | Customer support: country/city are NOT translated (English + code) | dev "What to Test" | localization |
| 11 | Unsupported device language → English on all placements | dev "What to Test" | negative |
| 12 | Offline → doesn't crash, bundled translations file | dev "What to Test" | negative |
| 13 | Update from a previous version to V6 → doesn't crash, translates | dev "What to Test" | functional |
| 14 | Sort/search/filter with localized names | dev "What to Test" | functional |
| 15 | A device language change refreshes the localized names | dev "What to Test" | functional |
| 16 | Server object: country filled, city empty | data (JSON) | data |
| 17 | Server object: country + city filled | data (JSON) | data |
| 18 | Server object: has a type value | data (JSON) | data |
| 19 | Server object without country → server hidden | data (JSON) | data/negative |
| 20 | City not translated → English by default | data (JSON) | data |

---

## Test cases

### Integration / Language detection

#### Server list is fetched via the Server List API V6
**Source:** AC — Server List V6 Integration

### Requirements
- Free / VIP user

### Steps
1. Open the app
2. Go to the Server List screen

### Expected result
Server list data is retrieved via the V6 endpoint; the list is displayed.

#### Fallback to English if the language is unavailable in the API response
**Source:** AC — Edge Cases

### Requirements
- An app language for which the API response has no localized strings

### Steps
1. Open the app
2. Open the Server List screen

### Expected result
The server list is displayed in English (fallback).

### Localization by placement

#### SL translations for FREE / VIP / History
**Source:** dev "What to Test" + AC
(3 separate cases: free, VIP, History)

### Requirements
- Free / VIP user; the device language is supported, not English

### Steps
1. Open the app
2. Open Fastest location → Server list (for History: connect → disconnect → History tab)

### Expected result
All servers in the SL are translated into the device language.

#### Customer support: values are NOT translated
**Source:** dev "What to Test" (country name in ENGLISH + country code)

### Requirements
- Free / VIP user, non-English device language

### Steps
1. Connect to a server → Disconnect
2. Open Help and Support → submit a ticket

### Expected result
Country and city in the ticket are in English (not translated) + country code.

### Negative / edge

#### Offline behavior
**Source:** dev "What to Test"

### Requirements
- Internet disabled

### Steps
1. Open the app → Server List screen

### Expected result
Doesn't crash, the bundled translations file is used.

#### Unsupported device language
**Source:** dev "What to Test"

### Requirements
- The device language is not supported by the app

### Steps
1. Open the app → Server List screen

### Expected result
English by default on all placements.

### Data-driven (server object structure)

#### Country filled, city empty
**Source:** data (JSON example from the reference)

### Requirements
- A server with `location.country` filled, `city` = ""

### Steps
1. Open Fastest location → Server list
2. Find a cluster where the country is filled and the city is empty

### Expected result
Cluster: Country name (ex. Germany); server: Country + number (ex. Germany - 1).

<!-- similarly: country+city; type value; without country (hidden); city not translated -->

## Raw / unclear requirements (NOT invented)

- "Server has city that's not translated" — exactly how to display an untranslated city
  in the UI (English placeholder?) — to be confirmed with PM. `need more info`
- AC "pass the locale if the API requires it" — it is not specified whether V6 requires the locale in the request.
  `need more info`

## Cross-cutting checks (self-control)

- [x] Localization in all languages (a separate case exists + a bug was found: no Chinese)
- [ ] Feature flag — the feature has no flag (N/A)
- [x] Offline behavior
- [x] Fallback to English
- [x] Update (update migration to V6)
- [x] Free vs VIP
- [ ] Multi-device — N/A for this feature
- [x] Negative (offline, unsupported language, missing translation)
