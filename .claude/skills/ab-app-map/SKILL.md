---
name: ab-app-map
description: >-
  (Re)generate the app navigation map (standards/app-navigation-map.md) from the
  Android-automation-test Page Objects + Navigator. Opt-in maintenance skill — run
  it when the automation framework changes, NOT as part of a ticket run. Triggers:
  "regenerate app map", "update navigation map", "rebuild app-navigation-map".
---

# Skill — Regenerate App Navigation Map

Maintenance utility. Builds/refreshes `standards/app-navigation-map.md`, the screen/
element/navigation reference that skills 3 and 4 use to write concrete steps.

## Activation

Opt-in only. Run when explicitly asked to (re)generate the map, or after the
automation framework's Page Objects change. It is NOT part of `shiva-run` or any
normal ticket run.

## Access

Needs the `Android-automation-test` repo. If not connected — request the folder
then (lazy), wait for it, read-only. `git fetch` first if possible.

## Process

1. Read the `Pages` enum (`.../pages/multiplatform/Pages.java`) — canonical screen list.
2. For each Page Object under `.../pages/`, extract:
   - the screen it represents (class + folder area: mainPage, menu, serverList,
     connection, paywall, helpAndSupportPage, dialogs, …);
   - its public methods = user actions / navigation (e.g. `tapMenu`, `chooseTab`,
     `selectServer`, `tapCrown`) and the elements they imply.
3. From `Navigator.detectPage` + cross-page `tap*`/`navigate*` methods, derive how
   each screen is reached (navigation edges).
4. Write `standards/app-navigation-map.md`: canonical screens, entry flow, then
   per-screen "Reach via" + key actions/elements, plus a short "how skills 3/4 use this".

## Hard rules

- Use real screen/class names and method-derived action names — don't invent UI.
- Mark areas the framework doesn't cover as gaps; never fabricate navigation.
- This skill only writes the map file; it doesn't run tests or touch the product repo.

## Model

Sonnet, effort Medium, temperature 0.
