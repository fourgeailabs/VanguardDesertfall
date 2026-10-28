# Data Safety Declaration Prep — Vanguard: Desertfall

*Note (2026-10-27): Eric's direction that Desertfall is a Mature war game (blood/gore pass
landing before upload — see CONTENT_RATING.md) changes nothing in this sheet: content is not
data. The app still collects and shares nothing.

This is the accurate basis for the Play Console **Data safety** form. It is simple because the
app is built to have nothing to declare.

## Verified facts about the app (0.4.0-beta)
- Package `com.fourgeailabs.vanguarddesertfall`, minSdk 29, targetSdk 36
- **Zero permissions** requested in the manifest (no INTERNET — verified in the shipped build)
- **No third-party SDKs at all** — no analytics, no crash reporting, no ads SDK, no social SDK
  (zero third-party dependencies; Android framework + Kotlin standard library only)
- **No accounts, no sign-in, no server** — the campaign is 100% offline by construction
- **No ads, no in-app purchases**
- The only data the app stores is **game progress and settings** (mission completion flags,
  volume/sensitivity/difficulty preferences) in on-device SharedPreferences. It never leaves
  the device — there is no network code capable of sending it anywhere.

## Mapped answers for Play's Data safety sections

| Play section | Answer |
|---|---|
| Does your app collect or share any of the required user data types? | **No** |
| Data collected (every category: location, personal info, financial, health, messages, photos, audio, files, calendar, contacts, app activity, web browsing, app info/performance, device IDs) | **None collected** |
| Data shared with third parties | **None shared** |
| Is all user data encrypted in transit? | Not applicable — no data is transmitted (no network permission) |
| Can users request data deletion? | Not applicable — no data leaves the device; users can clear everything via Android Settings → Apps → Vanguard: Desertfall → Clear storage, or by uninstalling |
| Independent security review / security practices | No external review; security posture is structural (offline, no permissions, no SDKs) |

## Notes for whoever fills the form at upload
- Answer from this sheet verbatim; do NOT guess additional data types "to be safe" — Play treats
  the declaration as a binding statement and the app genuinely collects nothing.
- If a future version ever adds crash reporting, analytics, or any online feature, this sheet
  must be updated **before** that version is uploaded.
