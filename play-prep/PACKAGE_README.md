# Play Internal-Testing Prep Package — Vanguard: Desertfall 0.4.0-beta

Prepared 2026-10-27. Scope: **internal testing only** — no production, no closed testing,
no GitHub push, and **no upload has been attempted** (upload is the next phase).

## The verified beta candidate AAB
| Item | Value |
|---|---|
| Path | `~/workspace/vanguard-desertfall/app/build/outputs/bundle/release/app-release.aab` |
| Size | **5,528,622 bytes** (~5.3 MiB) |
| SHA-256 | `f0ec3a09994d836809d6819490065964d33d9e56d02e45c87220a608ae82250e` |
| Package | `com.fourgeailabs.vanguarddesertfall` |
| Version | versionCode **6** · versionName **0.4.0-beta** |
| SDK | minSdk **29** · targetSdk **36** · label "Vanguard: Desertfall" |
| Permissions | **Zero** (verified — no `uses-permission` lines in badging) |
| Signature | `jarsigner -verify` → **jar verified**; signer `CN=FourgeAI LABS, OU=DevForge Pro, O=FourgeAI LABS, L=Surprise, ST=Arizona, C=US` (the Desertfall upload key below) |

**Name-resolved resource survival — verified inside the AAB** (this was the critical risk:
`AudioEngine`/`GameRenderer` resolve these by string name via `getIdentifier`, so shrinking
could have silently stripped them; release build has minify OFF + shrinkResources OFF):

| Resource class | In source | **Inside the AAB** |
|---|---|---|
| Voice lines `voice_*.mp3` | 51 | **51 ✓** |
| Synthesized SFX `*.wav` | 38 | **38 ✓** |
| Procedural textures `tex_*.png` | 6 | **6 ✓** |

## Signing (the durable deliverable)
- Keystore: `~/workspace/keystores/desertfall/desertfall-upload-key.jks` — **fresh RSA 2048,
  true JKS storetype**, alias **`desertfall`**, created 2026-10-27, valid
  **Oct 28, 2026 → Oct 20, 2056 UTC** (~30 years)
- Cert **SHA-1:** `02:CA:95:E2:21:FF:11:12:99:42:8B:D6:DA:34:87:09:5A:2D:61:5A`
- Cert **SHA-256:** `EE:60:7C:7C:07:03:96:AA:76:B8:4C:3C:BE:CC:18:CF:28:92:07:3F:70:F4:D6:7D:6D:D5:FF:53:4E:1E:1D:E4`
- Public cert exported beside it: `upload_certificate.pem` (needed if Play ever asks for the
  upload-key certificate)
- Passwords exist **only** in `desertfall-keystore-passwords.txt` (chmod 600) in that folder.
  They are in no project file, no document, and no chat.
- Wiring: `app/build.gradle.kts` reads that restricted properties file at build time (path
  overridable via env var `DESERTFALL_SIGNING_PROPERTIES`) and applies it to the release build
  only. Debug builds are untouched by it (verified: `assembleDebug` still green).
- ⚠️ **Back up `~/workspace/keystores/desertfall/` (keystore + passwords file) somewhere safe.**
  Losing it means a Play upload-key reset wait later. Never commit it to any repo.
- Gotcha recorded for posterity: the first generated key was PKCS12 wearing a `.jks` name and
  bundle signing rejected the distinct key password ("final block not properly padded"); it was
  destroyed unused and replaced with a real JKS, then proven by a jarsigner sign+verify test
  *before* the real build.

## ⚠️ Mandatory rebuild before upload — gore pass
Eric directed (2026-10-27) that Desertfall is a **Mature war game**: a **blood/gore +
war-scale combat pass runs right after this prep, before the beta upload**. Therefore:
1. The AAB above is **proof the pipeline works end-to-end** (signing → bundling → verification),
   **not** the upload artifact.
2. After the gore pass: rebuild `:app:bundleRelease` (same key, same version unless that pass
   bumps it), then **re-run this exact verification** (jarsigner, badging, resource counts,
   SHA-256) and update this file's table.
3. `CONTENT_RATING.md` and `LISTING.md` are already written for the **target** (post-gore) build.

## Package contents
| File | What it is |
|---|---|
| `LISTING.md` | Title, short + full description, category — written for the target Mature build |
| `RELEASE_NOTES.md` | Internal-testing release notes (plain, honest beta framing) + known limits |
| `DATA_SAFETY.md` | Data-safety answers mapped to Play's form (collects nothing, shares nothing) |
| `CONTENT_RATING.md` | IARC questionnaire **draft** for the target build (blood/gore declared) — Eric confirms at upload |
| `PRIVACY.md` | Complete privacy policy text (no data collection), ready to host |
| `TESTERS.md` | Internal tester recommendation: Eric only; Alexis is his call |

## Exact next steps for the upload phase
1. Gore/war-scale pass lands → rebuild + re-verify the AAB (above) → update this README.
2. Host `PRIVACY.md` at a public URL (see stop-points) and keep the URL for app creation.
3. In Play Console (Eric signed in): create app **Vanguard: Desertfall**, package
   `com.fourgeailabs.vanguarddesertfall`, game, free.
4. Complete the app-content declarations using this package: privacy policy URL, data safety
   (DATA_SAFETY.md), content rating questionnaire (CONTENT_RATING.md — Mature answers),
   target audience (adults, per the Mature rating), ads = none.
5. Store listing from LISTING.md (plus icon/screenshots — screenshots still need capturing
   from a running build on a device; none exist yet).
6. Internal testing track: create release, upload the re-verified AAB, paste RELEASE_NOTES.md,
   add testers from TESTERS.md, roll out to **internal testing only**.
7. Share the opt-in link with testers; Eric installs on his Pixel 10 Pro XL and playtests.

## Stop-points that need Eric personally
- **Play Console sign-in and first-time app creation** under his developer account.
- **Privacy policy hosting choice** — Play requires a public URL; where to host it
  (e.g. a FourgeAI LABS page) is his call. Nothing has been published anywhere by this prep.
- **Content rating confirmation** — the questionnaire answers in CONTENT_RATING.md are a draft;
  he submits them.
- **Tester list final call** — Eric only is the recommendation; adding Alexis (or anyone) is his
  decision, in the console.
- Anything involving payment, identity verification, or developer-account changes — none are
  part of this package and none were touched.
