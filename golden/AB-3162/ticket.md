# AB-3162 — Implement Phase-1 (SDK, Events, User Props) [CleverTap]

- **Type:** Task | **Priority:** Medium | **Status:** Done | **Label:** Blue_App
- **URL:** https://superunlimited.atlassian.net/browse/AB-3162
- **Testomatio suite:** CleverTap (a2652429) — 30 tests
- **Source of truth for events/props:** Google Sheet (link in the AC)

## Acceptance Criteria (verbatim, key points)

**SDK Implementation**
- The CleverTap SDK is integrated and initializes successfully.
- Initialization gated behind a Firebase feature flag of the format `{ "enabled": boolean, "whitelistedCountries": [] }`. enabled=false → the SDK does not initialize; enabled=true → filter by countries (an empty list = all).
- The country source is the user's real IP at app boot (IP info).
- If the country does not resolve at boot → the SDK does not initialize.
- CleverTap initializes asynchronously on every boot, without blocking startup.
- Re-initialization on every cold start (not from the background).
- If initialization fails → the app fails silently and continues a normal startup.

**User Properties & Events**
- Props and events are sent on every boot per the defined trigger.
- Events — in real time on boot; props — updated and sent on every boot from fresh client data.
- Idempotency: props are sent on every boot even without changes; events — only when the trigger fires.
- On a send error — silent fail, without blocking startup and without a retry in this cycle.

## Custom Events (from the ticket table)

paywall_viewed (screen_name, source), vpn_connection_success (server_country, protocol),
vpn_disconnected (session_duration_seconds), vpn_connection_failed (server_country="", protocol, error_reason),
vpn_connection_drop (server_country, protocol, session_duration_seconds), support_form_submitted (subject).
Additional from the comments: purchase_completed (plan_name), deep_link_opened, onboarding_step_completed.

## User Properties (categories from the table)

- Location: Country, City, last_known_location, user_country_changed, country_changed_at, user_previous_country
- Subscription: subscription_status, subscription_type, subscription_date, subscription_platform, auto_renew_status
- VPN Usage: connection_successful_count, post_upgrade_connections_count, last_connected_at, connection_fail_count
- Device: has_androidtv_device, esim_compatible, devices_count, has_ios_device, has_windows_device, has_mac_device
- Features: wifi_protection_enabled, widget_enabled, split_tunneling_enabled, always_on_enabled
- Account: account_created, is_vip_user
- App: user_id, app_package_name, app_version_name, app_version_code

## Clarification from the comments

- Profile updates are instantiated immediately when a value changes, like custom events.
