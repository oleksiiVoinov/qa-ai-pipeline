# AB-3162 — reference test set (Testomatio: CleverTap, 30)

Format: **Title** + `### Requirements` + `### Steps` + `### Expected result`.
Pattern: each event = a test, each prop (or group) = a test, plus SDK behavior.

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
