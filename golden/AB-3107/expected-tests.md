# AB-3107 — reference test set (Testomatio: Server list v6, 16)

Format: **Title** + `### Requirements` + `### Steps` + `### Expected result`.
Many tests are data-driven: they specify a concrete server JSON object and the expected render.

## Data-driven by the server object structure (boundary / equivalence)

- Country with value, city is empty → cluster: Country name; server: Country + number (ex. Germany - 1)
- Country with value, city with value → cluster: Country; server: City + number (ex. London - 1)
- Server has a type value → cluster: Country + type (ex. United Kingdom - 2G); server: City + number
- Verify behavior when Server object doesn't contain new values for Location {country name} → a server without a country is removed from the list
- Server has city that's not translated → city defaults to English (to be confirmed with PM)

## Translations by placement / user type

- Verify translations for the SL (free)
- Verify translations for the SL (VIP)
- Verify translations for the SL (History)
- Verify all other placements for translation except (SL screen) — Notification (free/vip), Widget, Main (Fastest location), Connection Detail
- Verify in Customer support the server is sent without translation (English by default)

## Fallback / edge / negative

- Verify if device lang is not supported in our app → English by default on all placements
- Verify the behavior while the device is offline → doesn't crash, uses the bundled translations file
- The app use translations after update → update from a previous version to v6, doesn't crash, translates

## Adjacent functionality (optimal location, search/sort)

- Optimal location flow only Country is filled
- Optimal Location flow Country + city are filled
- Verify that server sorting, searching, and filtering work correctly with localized names

## What this tells us about the decomposition

- Sparse AC (7 items) → 16 tests. The main source of expansion is the dev comment "What to Test" + Confluence + data-driven test design.
- Each combination of JSON fields (country/city/type filled/empty/absent) → a separate test (equivalence partitioning).
- Each placement where the server name is shown → a separate check.
- Negative/edge: offline, unsupported language, missing translation, update migration.
- The localization exception (customer support = English) is checked explicitly.
