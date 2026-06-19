# App Navigation Map (VPN Super, Android)

Distilled from the `Android-automation-test` Page Objects + Navigator. Purpose:
let the checklist (3) and test-case (4) skills write **concrete, navigable steps**
using real screen and element names, e.g. "Home → tap burger (Menu) → tap Settings
→ tap Always-On VPN", instead of vague "go to settings".

> Source of truth for the app itself is the product + the ticket. This map reflects
> what the automation framework knows — it may lag new screens. For a brand-new
> screen in a ticket, derive steps from the ticket/mockups and extend the map later.
> Regenerate with the `ab-app-map` skill when the framework changes.

## Canonical screens (Pages enum)

PREVIEW, MAIN_PAGE, PAYWALL_PAGE, CONGRATULATIONS_PAGE, SECOND_CONGRATULATIONS_PAGE,
EMBEDDED_PAYWALL, MENU, SERVER_LIST, LOCATION, HELP_AND_SUPPORT, EMAIL_US,
CONNECTION_DETAILS, CONNECTION_REPORT, SIGN_IN, LOG_IN, MY_ACCOUNT,
SPLIT_TUNNELLING, SETTINGS, CONNECTION_FAILED, ALLWAYSVPN, LOADING_SCREEN,
APP_NOT_RESPONDING.

## Entry flow (cold start)

Preview (accept privacy) → [Paywall] → Main screen. Onboarding order seen in tests:
Preview/agree → close paywall → notification permission → connect.

## Screen-by-screen (reach + key actions/elements)

### Main screen (MAIN_PAGE) — hub
Reach: app launch after onboarding; `navigateToMainScreen`.
Actions: tap Connect / tap Disconnect; tap Fastest Location (→ Server List);
tap burger Menu (→ Menu); tap Crown (→ Paywall); tap Browser (→ Embedded Browser);
tap Help (→ Help & Support); choose protocol / reconnect; tap Streaks icon
(active → StreaksDialog; no streak → NoStreakDialog); streak count label.
Surfaces here: CCPA popup, notification permission, embedded/MPF/finish-setup
banners, One-Account-All-Devices banner, secure snackbars (red/green).

### Menu (MENU) — burger
Reach: Main → tap burger.
Actions: My Account, Settings, Share, Widget, Wi-Fi Protection, Secure devices
(email/login/purchase windows), Help & Support, Location, Rate us, Facebook /
Instagram icons, notification toggle, back / X. Banners: create-account, MPF,
embedded, one-account.

### Settings (SETTINGS)
Reach: Menu → tap Settings.
Actions: Always-On VPN (→ ALLWAYSVPN), Split Tunnelling (→ SPLIT_TUNNELLING),
Privacy Notice, Terms of Service.

### Always-On VPN (ALLWAYSVPN)
Reach: Settings → tap Always-On VPN.
Actions: go to system settings, contact us; toggle on/off; warnings:
DisableAlwaysON, GrantVPNPermission, WarningPage.

### Split Tunnelling (SPLIT_TUNNELLING)
Reach: Settings → tap Split Tunnelling.
Actions: include all apps, per-app checkbox (e.g. Chrome), open settings.

### Wi-Fi Protection
Reach: Menu → tap Wi-Fi Protection.
Actions: enable/disable/toggle notifications.

### Widget
Reach: Menu → tap Widget.
Actions: add to home screen, cancel; from home screen: connect/disconnect, check
connected state, widget present/removed.

### My Account (MY_ACCOUNT)
Reach: Menu → tap My Account.
Actions: Free / Plan / Upgrade / Restore / Sign in / Sign out; plan rise popup;
states: free / VIP / MPF / logged-out.

### Secure all devices
Reach: Menu → Secure devices (email / login / purchase window).
Actions: click action, email-me, close.

### Server List (SERVER_LIST)
Reach: Main → tap Fastest Location, or Server List entry.
Actions: choose Tab (Free / VIP / History), tap Fastest, select server / cluster,
search, sort A-Z / Z-A / fastest, refresh, back; protocols via Protocols/Tabs.

### Connection screens
- Connection Detail (CONNECTION_DETAILS): when connected; validate connection, back, share.
- Connection Report (CONNECTION_REPORT): on disconnect; rate us, share, get premium,
  tap server name, back.
- Connection Failed (CONNECTION_FAILED): error → change server / report error.

### Paywall (PAYWALL_PAGE / EMBEDDED_PAYWALL)
Reach: Main → tap Crown; or app-start / connection / banners. Factory picks variant.
Actions: select plan (weekly/monthly/annual/3-month), continue, subscribe, proceed
with ADS, restore, terms, close; confirm up/downgrade alert.

### After-purchase
Congratulations (CONGRATULATIONS_PAGE): continue, create account, log in, maybe later.
Second congratulations (SECOND_CONGRATULATIONS_PAGE): enter email, continue, maybe later.
Native: AccountCreated (OK), ConfirmEmail (confirm/update email), UpgradeComplete.

### Help & Support (HELP_AND_SUPPORT) / Email Us (EMAIL_US)
Reach: Main → Help, or Menu → Help & Support.
Actions: Connection Issues, How-To & General, Accounts & Billing (each: sub-titles),
Email Us (select subject, send, send-logs checkbox), back, navigate to main menu.
Complain result: success / error (blue / no-internet).

### Location / Geolocation (LOCATION)
Reach: Menu → tap Location.
Elements: city, country, IP, latitude, longitude, postal, region; back, get location date.

### Sign up / Log in (SIGN_IN / LOG_IN)
Sign up: with password / magic link / OTP. Log in: email, password, forgot password,
sign in (magic link / OTP), close.

### Dialogs / popups
StreaksDialog (title/body/CTA/close), NoStreakDialog (Connect now), ActiveStreakDialog,
CCPADialog (OK), SomethingWentWrong (OK), DialogPreview (back/quit),
ManageSubscription, ServerListPopUp (OK/cancel), notification permission dialogs,
connection-request dialog (OK/cancel), GrantVPNPermission / ActionNeeded.

### Embedded Browser
Reach: Main → tap Browser. Actions: search, options list, share, back to VPN Super,
ConnectedAlert (accept/decline).

## How skills 3/4 should use this

- Resolve each step to a real screen + action above (use the verbs: tap Menu, tap
  Settings, choose Tab "VIP", select server, tap Crown, …).
- Write the navigation prefix explicitly (how the tester reaches the screen).
- If the ticket's screen/element is NOT in this map, write the step from the
  ticket/mockups and flag it as new (don't invent a fake navigation path here).
