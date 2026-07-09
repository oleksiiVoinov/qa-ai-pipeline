# AB-3162 — reference test set (Testomatio: CleverTap, 30)

Format (updated): title = `TC-REQ-N.M [Component] [Type] <action — outcome>`, then
`### Requirements (system setup)` + `### Pre-conditions (user/device)` + `### Steps`
+ `### Expected result` + `Type/Priority`. Full card shape in `templates/test-cases.md`.
Pattern: each event = a test, each prop (or group) = a test, plus SDK behavior.
Events coverage rule: ≤20 → one test per event; >20 → group by screen (5-6), list all
names in Expected. Always add an `[E2E]` group; apply `standards/android-test-checklist.md`.

## Example cards in the new format (built only from this ticket's data)

```
#### TC-REQ-4.1 [Event] paywall_viewed fires with screen_name + source
**Traceability:** TC-REQ-4.1 → REQ-4.1 → REQ-4
**Source:** Custom Events table

### Requirements (system setup)
- CleverTap SDK initialized (feature enabled for the country)
- Firebase DebugView: `adb shell setprop debug.firebase.analytics.app com.free.vpn.super.hotspot.open`

### Pre-conditions (user / device state)
- User: Free, logged-out

### Steps
1. Open a screen that shows the paywall

### Expected result
- `paywall_viewed` fires once with params `screen_name`, `source`

**Type:** Functional · **Priority:** Normal
```

```
#### TC-REQ-2.1 [SDK] [Error] Ipinfo blocked — SDK does not initialize, no crash
**Traceability:** TC-REQ-2.1 → REQ-2.1 → REQ-2
**Source:** SDK init / country AC

### Requirements (system setup)
- Charles Proxy: block `ipinfo` endpoint (return error / timeout)

### Pre-conditions (user / device state)
- User: Free · fresh launch

### Steps
1. Launch the app with the ipinfo endpoint blocked

### Expected result
- The SDK does not initialize; the app does not crash

**Type:** Negative · **Priority:** High
```

## SDK init / feature flag / country (suite 16ea5673)

- Sdk take info from IpLocation (SDK enabled for a specific country → init succeeds, pulls from IpLocation)
- Couldn't initialize the country (block Ipinfo → the SDK does not initialize, no crashes)

## Send behavior (idempotency)

- User properties are sent only 1 time on the app boot
- Event is sent only if triggered

## Custom Events — each = one test (suite afc157df)

- paywall_viewed (screen_name, source)
- vpn_connection_success (server_country, protocol)
- vpn_disconnected (session_duration_seconds)
- vpn_connection_failed (server_country, protocol, error_reason)
- vpn_connection_drop (server_country, protocol, session_duration_seconds)
- support_form_submitted (subject)
- onboarding_step_completed (step_name, step_index)
- deep_link_opened (source, destination_screen)

## User Properties — each/group = one test (suite ca69675c)

- Country changed (user_country_changed, country_changed_at, user_previous_country)
- Ipinfo (Country ISO-2, City)
- Subscription information (status, type, date, platform, auto_renew_status)
- Subscription (cancelled) → auto_renew_status=cancelled
- Subscription (grace_period) → auto_renew_status=grace_period
- connection_successful_count / connection_fail_count / post_upgrade_connections_count
- last_connected_at / last_known_location / location_changed
- esim_compatible / split_tunneling_enabled / always_on_enabled / wifi_protection_enabled / widget_enabled
- account_created / devices in create account (devices_count, has_ios/windows/mac/androidtv_device)

## What this tells us about the decomposition

- A large events/props table → 1 test per entity (literally by the prop/event name).
- Behavioral AC (idempotency, init by country, silent fail) → separate tests.
- Test titles are often = the event/prop name (the ticket terminology is preserved verbatim).
- Some tests are marked "need more info / TO DO" — raw requirements are NOT invented, but left as stubs.
