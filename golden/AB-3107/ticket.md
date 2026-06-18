# AB-3107 — Implement Server List V6 & Translate the name of the Cities & Countries

- **Type:** Task | **Priority:** Medium | **Status:** Done | **Label:** Blue_App
- **URL:** https://superunlimited.atlassian.net/browse/AB-3107
- **Testomatio suite:** Server list v6 (f61c6418) — 16 tests
- **Attachments:** Screenshot 2026-05-19 (bug), Screen Recording (search bug)

## Description

The server list is currently not localized — all strings are in English regardless
of the app language. Solution: integrate the Server List API V6 and enable
translations of the server list content. The client fetches data from the V6 endpoint
(localized strings) and renders it in the app's current locale language.
Support all languages available in the app.

Documentation: Confluence "Server list API v6" (BE), "ED-2 Server List Localisation" (ENG).

## Acceptance Criteria (verbatim)

**Server List V6 Integration**
- The app integrates with the Server List API V6.
- Server list data is fetched via the V6 endpoint.
- The response includes localized strings when available.

**Language Detection**
- The server list language follows the selected app language.
- The app passes the current locale on request if the API requires it.
- Supported languages match the app's localization languages.
- Edge: if the language is not available in the API response → fallback to English.

## Key points from the dev comment "What to Test" (source for tests)

- Localization by device language; translations from `server_list_localization.json` in the APK.
- Affected: Home (Fastest Location banner), Server List, Connect Details, Connect Report, VPN Connected notifications.
- Check server names in the FREE / VIP / HISTORY sections.
- Country name in customer support tickets — **in ENGLISH** + country code (do NOT translate).
- Behavior when localization data is missing; fallback for unsupported device languages; offline.
- Sorting / search / filter with localized names.
- A device language change refreshes the localized names.

## Bugs found (in the comments)

- Search does not work correctly in a different language.
- No translation for Chinese.
