# Context — AB-3107 Implement Server List V6 & Translate the name of the Cities & Countries

> Source of truth for skills 2-4 · Run of the ab-context skill (validation) · Source: https://superunlimited.atlassian.net/browse/AB-3107

## Metadata

- **Key:** AB-3107 · **URL:** https://superunlimited.atlassian.net/browse/AB-3107
- **Type:** Task · **Priority:** Medium · **Status:** Done
- **Labels:** Blue_App · **Components:** —
- **Reporter:** — · **Assignee:** Dheeraj Gangwar (Android dev)
- **Project:** Android VPN (AB)

## Description

**What problem are we trying to solve?** The server list is currently not localized —
all strings are in English regardless of the selected app language. For
non-English users this is an inconsistent experience: the server list content does not
match the rest of the localized UI.

**What is the solution?** Implement the Server List API V6 and enable translations of
the server list content. V6 supports localized data: server names
and related strings in the app's current locale language. The client fetches data from
the V6 endpoint (which includes localized strings) and renders it in the current locale.
All available app languages must be supported.

**Supporting documentation:**
- Server List V6 implementation — Confluence (BE): /wiki/spaces/BE/pages/1403289601/Server+list+API+v6
- Server list localisation process — Confluence (ENG): /wiki/spaces/ENG/pages/1357381666/ED+2+-+Server+List+Localisation

## Acceptance Criteria

**Use Case: Server List V6 Integration**
- **AC-1:** The application must integrate with Server List API V6.
- **AC-2:** Server list data must be retrieved using the V6 endpoint.
- **AC-3:** The server list response must include localized strings when available.

**Use Case: Language Detection**
- **AC-4:** The server list language must follow the user's selected application language.
- **AC-5:** The application must pass the current locale when requesting the server list if required by the API.
- **AC-6:** Supported languages must match the application's supported localization languages.
- **AC-7:** (Edge) If the requested language is not available in the API response, the application must fallback to English.

## Comments (substantive)

### Dheeraj Gangwar · 2026-05-18 — What's Done / Impacted / What to Test
**What's Done:** Server List v6 with localization support; localization by
device default language; translations from `server_list_localization.json` inside the APK.

**Impacted Areas:** Home screen — Fastest Location banner; Server List screen;
Connect Details screen; Connect Report screen; VPN Connected notifications.

**What to Test:**
- Server names in the FREE, VIP, HISTORY sections.
- Correct localized server name/alias on all screens and notifications.
- Correct country name (**ENGLISH**) and country code in customer support tickets.
- Behavior when localization data is missing/unavailable.
- Fallback for unsupported device languages.
- Offline behavior.
- Sorting, search, filter with localized names.
- A device language change refreshes the localized server names.

**App Checks (sanity):** App boot ✅; Ads ✅; Connection IKEv2/OpenVPN/Super/V2Ray ✅;
Purchase ✅; Premium connection IKEv2/OpenVPN/Super/V2Ray ✅.

### Oleksii Voinov · 2026-05-19 — bugs during testing
- Search works incorrectly in a different language (screenshot + video attached).
- No translation for Chinese.
- Question for PM: should the text on the page be translated (screenshot attached).

### Dheeraj Gangwar · 2026-05-19 — responses
- Localizing the country name on the Connection Report screen — in the next QA build.
- Change the device language via the device settings, not through Android Studio.
- Resolved: Server list search; localizing the country name on the Connect Report screen.

### Dheeraj Gangwar · 2026-06-09 / 06-11 — final builds
- Server list v6 implemented for mobile and Android TV.
- See related: AB-3288, AB-3285, AB-3286, AB-3289, AB-3304.

## Clarifications (Q&A) — take priority over the description

- The localization source = device default language; translations embedded in the APK
  (`server_list_localization.json`) _(source: Dheeraj, 2026-05-18)_.
- Country name in customer support — NOT translated, English + country code
  _(source: Dheeraj, 2026-05-18)_.

## Attachments

| File | Type | What it shows | Status |
| --- | --- | --- | --- |
| Screenshot 2026-05-19 at 14.17.39.png | image/png | screenshot for the bug (search/translation) | not read, requires manual review |
| Screen Recording 2026-05-19 at 14.27.17.mov | video/quicktime | recording of the search bug | not read, requires manual review |

## Related sources

- Confluence (BE): Server list API v6 — /wiki/spaces/BE/pages/1403289601 — description of the V6 endpoint and implementation (pull via getConfluencePage).
- Confluence (ENG): ED-2 Server List Localisation — /wiki/spaces/ENG/pages/1357381666 — the localization process.
- Confluence (AN): Localisation — the supported languages list (mentioned in the tests).
- Related tickets: AB-3288, AB-3285, AB-3286, AB-3289, AB-3304 (bugs/follow-ups).

## Gaps and notes (facts, no analysis)

- Components are not set.
- The PR/branch is not linked in the ticket fields (only Firebase QA builds are mentioned in the comments) — the code skills (5-7) will need it separately.
- 2 attachments not read (screenshot + video) — require manual review.
- In the description the list language follows the "selected application language" (AC-4), while in the dev comment it follows "device default language" (What's Done). The discrepancy is recorded, not resolved.
