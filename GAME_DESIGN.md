# VANGUARD: DESERTFALL — Game Design Document

**Developer:** FourgeAI LABS · **Platform:** Android (target device: Pixel 10 Pro XL) · **Version:** 0.4.0-beta (versionCode 6) — full 5-mission campaign (§10) + visual polish (§11) · **Release prep:** §12
**Hard rules:** 100% **offline** campaign (the app does not even request the INTERNET permission) · **full Bluetooth AND wired gamepad support** on Android phones · no Windows/Legion Go focus.

---

## 1. The game

Alt-history Arizona. A techno-fascist Empire won its war with dieselpunk super-weapons and occupies the
US Southwest. Its comm towers watch every street. You are **Cole**, a former telecom lineman turned
resistance fighter — the one person who can climb those towers, splice into the Empire's uplink, and
turn its own eyes against it. Your handler is **Mara Voss**. The setting and the chrono-engine twist
come from Eric's sci-fi manuscript *The Vanguard Paradox*.

**Inspirations, fused deliberately:**
- **Wolfenstein** — dual-wield energy (roadmap), super-soldiers/mechs, secret desert labs, over-the-top bosses.
- **Call of Duty** — tight gunplay, cinematic scripted campaign, set-pieces.
- **Battlefield** — large objective maps, destructibility, combined arms (roadmap Conquest mode).
- **Spider-Man** — momentum traversal: the **Vanguard Tether**, a grapple rig that zips you to towers and rooftops.
- **Desert racing** — armoured **'94 Suburban K1500** convoy/escape missions (**implemented, basic, in Mission 2**).

## 2. What v0.1 actually contains (vertical slice)

- **Mission 1 — "First Tower"** (Surprise, AZ): intro cutscene → fight to the comm tower → hack the
  uplink terminal → defend against a wave → outro cutscene → mission-complete screen.
  Campaign completion is saved locally (SharedPreferences) on the device.
- **Mission 2 — "Iron Convoy"** (the western road + Empire substation): intro cutscene (6 lines, Mara
  briefing Cole by the Suburban) → **driving segment** → on-foot substation finale → outro cutscene
  (4 lines) → mission-complete screen (checkpoints, generators, kills/deaths/time). Unlocked from the
  mission list when Mission 1 is complete (long-press a locked mission to playtest it anyway);
  completion saved separately (`mission2Complete`).
  - **Driving (new in v0.2):** chase-cam driving of the armoured '94 Suburban — accelerate/brake/reverse,
    steering, speed-sensitive engine audio, a desert road (z 30 → -185) with **4 checkpoints in order**,
    **3 destructible blockade barriers** (smash through at speed, or they stop you), 2 roadside
    **auto-turrets** that damage the rig, solid-rock/building collision (thud + slow), rig health with
    respawn at the last checkpoint.
  - **On-foot finale:** park in the pad, exit, fight a substation garrison including the new **Heavy
    super-soldier** (bigger model, 320 HP, slower, 9-damage hits, deep cannon sound), pick up the
    **Empire Heavy Repeater** (third weapon: 55 damage/hit, slow fire, 40-round mag, distinct sound),
    **plant charges on 2 generators** (clear the garrison, hold USE 3 s, defend an 8 s countdown each,
    reinforcement waves incl. another Heavy), then reach the **extraction pad**. Death respawns you at
    the extraction pad; generators already blown stay blown.
- **Mission 3 — "The Mesa Lab"** (new in v0.3): intro cutscene (6 lines, Mara + Cole at the mesa
  approach — the first time the **chrono-engine / Vanguard Paradox thread appears in the story**) →
  on-foot desert **approach** (tether anchors on watch-post roofs and the mesa ledge; a patrol of
  troopers + the first **robot hound** guarding the blast door) → **breach** the blast door (clear the
  patrol, hold USE) → **lab interior** inside the mesa (dark steel/teal palette vs the desert sunset,
  divider-wall corridors with doorway chokepoints, stage-triggered packs: room 1, room 2, and a core
  chamber guard with a Heavy) → **overload the chrono core** (clear the guard, hold USE 3 s, defend a
  20 s countdown against two waves of hounds + troopers while the fog itself pulses violet harder and
  harder) → the core **explodes** → **escape** back out to extraction against a **90 s timer**, with
  **time-echo ghosts** (Cole's own recorded past positions, drawn as teal figures) flickering behind
  you → outro cutscene (4 lines: Cole heard his own voice saying things he hasn't said yet — the
  timeline noticed them). Unlocked when Mission 2 is complete (same long-press playtest override);
  completion saved separately (`mission3Complete`). Fail states never soft-lock: death respawns at the
  current beat's doorstep; an expired escape timer snaps Cole back to the core chamber with the clock
  reset.
  - **New enemy — robot hound (v0.3):** fast (5.6 m/s vs a trooper's 2.7), fragile (45 HP), **no gun** —
    it charges and **bites** (10 damage; bite cadence rebalanced to **1.15 s in v0.3.1** with a
    **1.3 m knockback** on Cole — see §9). Modelled from boxes as a hound
    silhouette: low long body, spine stripe, head with a red eye slit, four legs, tail, trot bob.
    Distinct synthesized sounds: metallic bark, servo whir, bite snap, winding-down destruction.
  - **Chrono presentation (honest scope):** the "time coming apart" effect is (a) the fog/clear colour
    pulsing violet, driven by `chronoPulseLevel` as the overload builds and through the escape,
    (b) the core cube spinning/pulsing teal→violet, (c) time-echo ghost figures during the escape,
    (d) a proximity chrono-hum loop and periodic chrono-pulse booms. There is no shader-level time
    distortion — that's a roadmap item for the chrono-remix missions this story now seeds.
- **Real-time 3D FPS** (OpenGL ES 3.0, native Kotlin, zero third-party dependencies): desert arena with
  mesas, buildings/crates for cover, road, and a full comm tower (legs, platform, cabin, mast, pulsing
  beacon — red for Empire, green once captured). Sunset palette, directional lighting, distance fog.
- **Combat:** hitscan Vanguard Rifle (30-round mag, automatic) + Scattergun (5-pellet, 6-shell),
  reload, melee, muzzle flash, recoil, hit-flash enemies, enemy soldiers that advance, shoot back
  (positional audio), damage the player; health, death and respawn.
- **Vanguard Tether (v1):** aim at the tower platform or a rooftop anchor (cyan markers) and press the
  tether control — you zip to it with an arc. Real working traversal, ready to grow into full swinging.
- **Simple collision** (player vs. solid world boxes), gravity/jump, world bounds, cover blocks shots.

## 3. Controls

### Gamepad (Bluetooth or wired USB — standard Android mapping)
| Input | Action |
|---|---|
| Left stick | Move |
| Right stick (AXIS_Z / AXIS_RZ, RX/RY fallback) | Look |
| Right trigger / BUTTON_R2 | Fire |
| Left trigger / BUTTON_L2 | Aim (slower move, steadier) |
| A | Jump · advance dialogue |
| B | Melee |
| X | Reload · **hold at the tower terminal to hack** · advance dialogue |
| Y | Switch weapon |
| LB / RB | Vanguard Tether |
| Start / Select | Pause |
| D-pad (hat or buttons) | Move (alt) |
| Mode | Skip cutscene |

**Driving (Mission 2, gamepad):** Right trigger — accelerate · Left trigger — brake / reverse ·
Left stick X — steer (left stick Y also drives as a fallback) · X (hold, stopped in the parking pad) — get out.
On foot by the rig: hold X to get in.

The game detects the pad from generic motion/key events and toasts its device name on first input.
A keyboard fallback (WASD, Space, R, F, E, Esc) exists for emulators/Chromebooks (W/S drive, A/D steer).

### Touch (fallback, always available)
- Left thumb: virtual stick (move / steer). Right thumb drag: look.
- On-screen buttons: **FIRE** (hold), **JUMP**, **TETHER**, **RLD**, **USE** (hold to hack / plant /
  get in & out of the rig), **SWAP**, pause (❚❚ top bar).
- Driving: **GAS** and **BRKE** pedal buttons (hold) + **◀ ▶** steer buttons (hold).

## 4. Audio design — "sounds must be accurate to type"

**Engine (`AudioEngine.kt`):** SoundPool for low-latency SFX + two looping MediaPlayers (ambience/music).
**Positional/directional audio:** world sounds (enemy gunfire, deaths, explosions) are played with
distance falloff *and* stereo pan computed from the source's bearing relative to the player's facing —
a shot on your left sounds on your left, and quieter with distance.
**Buses:** Master / Music / SFX / Voice sliders in Settings, all persisted.

**All v0.1 sounds are original and synthesized in code** (`tools/generate_sounds.py` → `res/raw/*.wav`).
No copyrighted or ripped game audio, no downloads. Each weapon has a distinct synthesized signature:
- *Vanguard Rifle* — sharp mid crack + low thump (0.16 s).
- *Scattergun* — bigger, longer, lower boom (0.32 s) so the two are unmistakable.
- *Enemy rifle* — thinner, higher, quieter — you can tell incoming fire from your own.
- Plus: reload (3 mechanical clicks), empty click, hitmarker tick, hurt thud, enemy death sweep,
  explosion, footstep taps, tether zip (rising sweep), hack beeps, objective-complete arpeggio,
  UI click, radio static between dialogue lines.
- *Ambience:* seamless desert-wind loop. *Music:* an original tense D-minor loop (drone + pulse +
  sparse line) generated to loop on the bar.
- *Mission 2 additions (same generator, same rules):* **engine loop** — a seamless 2 s synthesized V8
  rumble played through a dedicated player whose **playback rate rises with road speed** (0.75×–1.6×),
  so the rig audibly revs; checkpoint chime, barrier smash, vehicle collision thud, generator alarm
  (while a planted charge counts down), a bigger 1.5 s generator explosion (positional, 90 m range),
  the Heavy's deep cannon shot, and the Heavy Repeater's punchy zap-crack — each weapon/enemy still
  sounds distinct from every other.
- *Mission 3 additions (same generator, same rules):* **hound bark** (two metallic clank-barks),
  **hound servo whir** (rising mechanical sweep, positional — you hear it coming), **hound bite**
  (snap + thump), **hound destruction** (servo whine winding down + clank), **lab alarm** (alternating
  two-tone during the core overload), **blast door** (hydraulic hiss into a clunk), **chrono pulse**
  (deep downward time-boom with a high shimmer), and the **chrono core hum** — a seamless 2 s loop
  (50/100/1200 Hz, all whole cycles) on its own player whose **volume tracks your proximity** to the
  unexploded core. The core explosion reuses Mission 2's big positional explosion at 90 m range.

**Honest status:** these are *placeholder-quality synthesized* sounds and music that prove the full
system end-to-end. A professional release replaces them with recorded/f Foley weapon sounds, a composed
score, and mastered mixes (see §7). Voice acting shipped in v0.2.1 as generated voices (see §5).

## 5. Cutscenes & dialogue

**System (`Dialogue.kt` + GameWorld cutscene state + GameRenderer camera):** fully data-driven.
A cutscene = a list of `DialogueLine(id, speaker, text)` + a list of camera `CameraShot`s.
Authoring a new cutscene is adding data, not new engine code.

- **Mission 1 intro cutscene** — in-engine, scripted gliding camera, Cole and Mara present in the scene
  as characters, a real 6-line conversation (the tower, the plan, the risk), tower reveal shot.
- **Mission 1 outro cutscene** — at the captured tower, 3-line conversation, camera pulls up the mast
  to the now-green beacon.
- **Mission 2 intro cutscene** — 6-line Mara/Cole conversation at the staging point, with the Suburban
  reveal and the road ahead; **Mission 2 outro** — 4 lines at the extraction pad as the substation
  burns (renderer draws a flickering explosion glow over the substation once a generator has blown).
- **Mission 3 intro cutscene** — 6-line Mara/Cole conversation at dawn on the mesa approach, mesa-face
  reveal shot; Mara is in person here, then guides the lab run from the captured tower (her §5 voice
  is the same generated voice throughout). **Mission 3 outro** — 4 lines at extraction: the core's
  destruction echoed Cole's own voice back at him saying things he hasn't said yet, and Mara names it —
  *the timeline noticed us* — the story seed for chrono-remix missions.
- Lines auto-advance on reading time **or voice length, whichever is longer** (auto-advance is
  `max(reading time, voice duration + 0.4 s)`, so a spoken line is never cut off), or press
  **tap / A / X** to continue; **SKIP ▸▸** (touch button / gamepad Mode) skips. Manual advance,
  skip, cutscene end and quit-to-menu all stop the current voice line first — voices never overlap
  and never leak into gameplay. Subtitles show speaker name + line; subtitles can be turned off
  in Settings.
- **Voice acting (v0.2.1 — GENERATED voices; v0.3 extends to all 29 lines):** every cutscene line is spoken.
  *(v0.3.1: `m2i5` re-rendered — the TTS input spells "'94 K1500" phonetically as "ninety-four K
  fifteen hundred" so Mara says it correctly; the subtitle in `Dialogue.kt` is unchanged. New file:
  13.37 s, mean −26.9 dB / peak −7.4 dB, loudness-matched to the rest of the set.)*
  - **MARA VOSS** — voice **"Heroic Bugle"** (`avocado_v2:Caitlin`): confident, powerful,
    American, middle-aged female — the calm, commanding handler.
  - **COLE** — voice **"Bold Flag"** (`avocado_v2:Twenty`): heroic, bold, youthful, energetic
    American male — the determined protagonist.
  - Coverage: M1 intro 6 lines, M1 outro 3, M2 intro 6, M2 outro 4, **M3 intro 6, M3 outro 4** =
    **29 lines, ≈3.6 minutes of dialogue, 1.63 MB total** as `res/raw/voice_<id>.mp3`, played by `AudioEngine.playVoice(id)`
    on the Voice bus (Settings > Voice volume, live-adjustable during playback). Voices are
    in-person conversations — no radio filtering (only the pre-existing soft static blip as a
    line opens). Line text is synthesized **verbatim** from `Dialogue.kt`.
  - **Re-casting / re-recording later:** each line keeps its stable ID (e.g. `m1i1`). Generate or
    record a new take and replace `res/raw/voice_<id>.mp3` under the same resource name — the
    engine resolves voice files by name (any audio format), reads the real duration at runtime,
    and adjusts cutscene timing automatically. No code change. Human recordings (Eric, or hired
    actors at release) drop in exactly the same way.

## 6. Presentation (v0.1)

Polished main menu (title treatment, campaign CONTINUE that starts the next unfinished mission, a
**mission select list** with per-mission completion ✓ / lock 🔒 state, CONTROLS, SETTINGS), per-mission
briefing screens, an objective HUD that speaks each phase's language (DRIVE → checkpoints/rig health →
GENERATORS 0/2 → DEFEND THE CHARGE countdown → EXTRACT; Mission 3: APPROACH → BREACH THE LAB →
REACH THE CHRONO CORE → OVERLOAD defend countdown → ESCAPE timer → EXTRACT), driving speed readout in the HUD,
mission briefing/loading screen, clean HUD (objective, HP, weapon/ammo, hack progress, crosshair,
context messages), pause menu, mission-complete screen with stats, consistent sunset-desert art
direction and UI accent (orange on dark), adaptive launcher icon (V + tower mast), proper app name.
**Settings (v0.3.1)** also include a **DIFFICULTY** selector (STORY / NORMAL / VETERAN — §9) and a
**CAMERA SHAKE** toggle, both persisted with the rest.
**Honest status:** this is a clean, cohesive *vertical-slice* presentation built from framework UI and
coloured-geometry art — not final production art (see §7 for what "professional release" still needs).

## 7. Professional Production Roadmap

**Near (v0.2–v0.5):**
- Textured PBR-style materials + real 3D models (Blender pipeline) replacing coloured boxes; skybox,
  dust particles, heat shimmer; character models with skeletal animation (idle/walk/fire/death, lip-flap for cutscenes).
- Tether v2: physics swing with momentum, wall-run, mid-air redirect.
- Dual-wield, ADS zoom, sprint/slide, grenades. Enemy roster so far: trooper, **Heavy super-soldier
  (Mission 2)**, **robot hound (Mission 3)** — next: a flying drone or a chrono-phased trooper variant.
- **Mission 3 shipped in v0.3** (mesa lab, robot hound, chrono core, timed escape). Next missions:
  Phoenix airport convoy; Hoover Dam mech boss. **Suburban driving shipped in Mission 2 (basic)** —
  next for the rig: mounted gun, chase AI vehicles, damage modelling, dust/skid feel.
- Recorded weapon Foley + footsteps per surface; composed adaptive music (explore/combat layers);
  optional **human voice re-recording** for Mara/Cole using the line-ID drop-in system (generated
  voices shipped in v0.2.1); mix/master pass.

**Later (v1.0 direction):**
- **Conquest mode (Battlefield pillar):** large map, capture points, vehicles, destructible cover/buildings, bot squads (offline-first; any online play is a separate, optional design decision — campaign stays offline).
- Chrono-remix missions (the Paradox engine replays cleared missions with altered layouts/enemies) —
  **now story-seeded by Mission 3's outro** (the timeline noticed them; Cole's time-echoes are already
  a gameplay visual in the M3 escape). Shader-level time distortion and echo-enemies belong here.
  Mech boss fights (Hoover Dam), dual-wield super-weapons.
- Performance targets: **60 fps locked, 120 fps mode on Pixel 10 Pro XL** (dynamic resolution, baked
  lighting, object pooling), full QA pass (device matrix, controller matrix — Xbox/PS/8BitDo wired+BT,
  accessibility: remapping, colour-blind HUD — difficulty options **shipped in v0.3.1, §9**),
  Play Store release prep.

**What separates v0.1 from a shippable professional release, honestly:** production art/models/animation,
recorded Foley audio + human voice acting (generated voices shipped in v0.2.1), a composed score, more missions, enemy variety/AI polish, the
remaining game-feel work (camera shake + hit-stop **shipped in v0.3.1**; particles still to come),
extensive on-device QA, and store/compliance work. v0.1 is
the playable proof that the whole machine — engine, combat, tether, mission flow, audio, cutscenes,
menus, gamepad — works end to end.

## 8. Tech notes

- Native Kotlin + OpenGL ES 3.0, AGP 9.1.1 (built-in Kotlin), Gradle 9.3.1, minSdk 29, targetSdk 36.
- **Zero third-party dependencies** — framework Activity + GLSurfaceView only. Nothing to phone home,
  nothing to break offline.
- Code is split so each piece is small and readable: `GameWorld` (logic), `GameRenderer` (drawing),
  `MainActivity` (screen/input/menus), `AudioEngine` (sound), `Dialogue` (cutscene data),
  `InputState`/`GameSettings` (shared state).

## 9. Polish changelog — v0.3.1 (no new content; fixes, feel, balance)

**Bug fixes**
- **Input leak (known bug from v0.2):** a jump/reload press queued *while driving* was never
  consumed in driving mode and fired once when Cole stepped out of the Suburban. Fixed
  systematically: `InputState.clearQueued()` drops all one-shot queues + look deltas, and
  `GameWorld.transitionClear()` calls it at **every** major transition — mission start (×3),
  cutscene start (×3 outro triggers) and end (`finishCutscene`), vehicle enter **and** exit,
  on-foot respawn, rig-destruction respawn, and M3 escape snap-back. `MainActivity.quitToMenu()`
  clears too. Held state (sticks/fire/interact) is intentionally untouched — devices re-assert it.
- **Enemy wall collision (known limit from v0.3):** enemies used to ignore solid boxes and clip
  through lab divider walls/corners. They now get the player's minimum-push XZ push-out
  (`resolveEnemyCollisions`, per-type radius/height, floor-slab and overhead rules), so they slide
  along walls and funnel through doorways. **Honest limit: this is collision + LOS, not navmesh
  pathfinding** — an enemy whose straight line to Cole is fully walled off will hug the wall until
  the line opens, and spawn points were checked to not start inside geometry.
- **Enemy fire now respects walls:** before v0.3.1, a ranged enemy's damage roll happened
  regardless of intervening walls (their *sound* was positional, the damage was not blocked).
  `losBlocked()` (XZ segment vs gun-height-straddling solid boxes) now gates damage for troopers,
  Heavies **and** Mission 2 turrets — cover works both ways, matching the player's shot rays.
  Point-blank attacks (≤1.5 m) are exempt by design: adjacent is adjacent.

**Difficulty (Settings → DIFFICULTY, persisted; default NORMAL)**
NORMAL is exactly the tuned baseline below — existing players feel no change. All scaling flows
through `GameSettings` helpers so troopers, Heavies, hounds and turrets scale consistently:

| Lever | STORY | NORMAL | VETERAN |
|---|---|---|---|
| Enemy damage (shots / melee / bite / turret) | ×0.6 | ×1.0 | ×1.3 |
| Enemy hit chance (55% base / 50% turret) | −15 pts | base | +15 pts |
| Enemy fire cooldown | ×1.25 (slower) | ×1.0 | ×0.85 (faster) |
| Hound bite cadence | ×1.4 (slower) | ×1.0 | ×0.85 |

**Game feel**
- **Camera shake** (Settings toggle, default ON): decayed shake on big explosions (generator 0.8,
  chrono core 1.0), player hurt (0.28–0.35), barrier smashes and hard vehicle impacts (0.25–0.3),
  applied to the first-person and chase cameras. Cutscene cameras are exempt — conversations stay calm.
- **Damage vignette:** radial red edge-glow flashes when Cole takes damage and pulses while HP < 30
  (the HP text already turned red below 30; now the screen edges agree).
- **Hit-stop on kill:** ~45 ms at 12% timescale, scoped strictly to on-foot PLAY — ticked in real
  time so cutscene voice timing, driving and timers are unaffected.
- **Kill confirmation:** the hitmarker sound gains a visual partner — crosshair pulses orange and
  grows on a hit, flips to a larger red ✕ on a kill (from `hitPulse`/`killPulse` in the HUD snapshot).

**Controller disconnect auto-pause**
If a gamepad that has actually sent input this session is removed during PLAY, the game pauses and
toasts "Controller disconnected — game paused" (`InputManager.InputDeviceListener`, registered in
`onCreate`, unregistered in `onDestroy`, main-thread handler). Caveats: only pads that have sent
input are tracked (a pad that was merely plugged in won't trigger it), and removal is detected per
device ID — unplugging a USB hub drops all its devices and the first match pauses the game.

**Voice fix** — `m2i5` re-rendered with phonetic TTS input ("ninety-four K fifteen hundred") for the
"'94 K1500" line only; subtitle unchanged. QA: 13.37 s (was 13.51 s), mean −26.9 dB / peak −7.4 dB,
in family with the rest of the set (cf. `m1i1` −26.7 dB). Same voice (`avocado_v2:Caitlin`).

**Balance table (NORMAL difficulty) — before → after, with rationale**

| System | Before (v0.3.0) | After (v0.3.1) | Why |
|---|---|---|---|
| Hound bite cadence | 0.85 s | **1.15 s** (× difficulty bite scale) | 10 dmg every 0.85 s ≈ 100 HP in ~8.5 s of contact — a death sentence with no counterplay. 1.15 s ≈ 13 s, still lethal if you stand there. |
| Hound bite knockback | none — Cole stays pinned in bite range | **1.3 m push** (wall-resolved) | The missing reaction window: a bite now buys space to shoot, tether, or run. The hound's 5.6 m/s closes it again fast — it stays scary. |
| Enemy/turret damage vs walls | applied with no wall check | **blocked by `losBlocked()`** | Cover must work both ways; previously "cover" only blocked the player's incoming *sound* positioning, not the damage. |
| Difficulty | none | **STORY / NORMAL / VETERAN** (above) | Accessibility + replay lever; NORMAL = this table, so no stealth retuning rides along with it. |
| Trooper damage 5 / 55% hit / 1.4–2.2 s cycle | — | **unchanged (checked)** | ≈1.7 effective DPS at range vs 100 HP + respawn — fair pressure, not an outlier. |
| Heavy 320 HP / 9 dmg | — | **unchanged (checked)** | The tank fantasy is the point; Repeater (55/hit ≈ 6 shots) and Scattergun exist as its answers. |
| Hound 45 HP / 5.6 m/s | — | **unchanged (checked)** | One rifle burst or one Scattergun blast; speed is its identity. Only its bite cadence was the outlier. |
| Weapon damage (Rifle 34 · Scatter 14×5 · Repeater 55) | — | **unchanged (checked)** | TTKs feel right against 100/320/45 HP pools; no outlier to fix. |
| M2 turret (4 dmg, 50%, 1.3 s) | — | **unchanged baseline** (now difficulty-scaled + LOS-gated) | ≈1.5 DPS vs 100 rig HP across a ~30 s exposure window — pressure, not a wall. |
| M3 escape timer 90 s | — | **unchanged (checked)** | ~200 m at 6.2 m/s ≈ 33 s of pure running; 90 s leaves room for fights and a wrong turn. Generous, not broken. |

**What v0.3.1 is NOT:** no new missions, weapons, or enemies; no navmesh/pathfinding (see collision
limit above); no playtest data — every number here is code-reasoned from the v0.3.0 baselines, and
nothing in this pass has run on a physical device yet.


## 10. Campaign completion — Missions 4 & 5 (v1.0.0 code, shipped inside the 0.4.0-beta build)

The full five-mission campaign is implemented end-to-end in code and compiles
clean. Per Eric's 2026-10-27 priority change, the BETA leads with Missions 1–3;
Missions 4–5 ride along as a bonus because they were substantially complete and
compile-green — they have NOT been playtested on a device, and their balance is
code-reasoned only, same honesty standard as everything else here.

**Campaign structure:** M1 First Tower → M2 Iron Convoy → M3 The Mesa Lab →
M4 Phoenix Down → M5 The Paradox Engine (finale). Unlock chain + saves
`mission1Complete`…`mission5Complete`; CONTINUE walks to the next unfinished
mission and, once M5 is done, becomes "CAMPAIGN COMPLETE ✓ — REPLAY MISSION 1"
(no dead button). Locked missions can be long-pressed to playtest.

**Mission 4 — "Phoenix Down" (Battlefield pillar).** Suburban insertion drive
reusing M2 driving (2 checkpoints, 1 smashable blockade, park pad), then an
on-foot capture phase in a dusk city map: THREE comm relays (A west, B east,
C north) capturable in ANY order — stand in the zone, hold USE 3 s to splice
(hostiles within 18 m block it), then hold the zone through a 12 s defend timer
against a spawned wave (leaving pauses the timer). Mix of troopers, hounds,
Heavies, and 2 destroyable turrets (60 HP via the shot ray); tether anchors on
rooftops. Extraction at the rig pad. Intro 6 lines (m4i1–m4i6), outro 4 lines
(m4o1–m4o4): capturing Phoenix's relays blinds the Empire regionally and reveals
the Paradox Engine's true location — a dam citadel.

**Mission 5 — "The Paradox Engine" (finale).** Dam citadel: approach, plaza,
citadel wall (gate gap), Engine chamber. **BOSS — SIEGE WALKER:** a multi-box
mech (legs, hull, cannon) with STAGED weak points — phase 1: two power nodes
(140 HP each) on shoulder pylons; phase 2: exposed core (500 HP); total pool
780 HP. Hull shots deal no damage (armour) with a periodic hint. Telegraphs:
the cannon charges ~1.6 s (charge sound, growing muzzle glow, ground blast
marker locked where Cole stood when the charge began) then an AoE blast
(30 dmg × difficulty scale within 5.2 m) — dodgeable by design; slow advance
(0.9 m/s, halts at 20 m), point-blank stomp aura. Adds spawn on each node death
and on phase 2. Boss damage persists through Cole's death (no healing). HUD
boss bar: "SIEGE WALKER" + segmented bar. After the Walker: Engine guard,
hold USE 3 s to overload, 20 s defend, explosion with violet chrono pulse +
time-echo ghosts, 90 s escape to extraction with the M3 no-soft-lock snap-back.
Outro 6 lines (m5o1–m5o6): Empire comms collapse across Arizona, the chrono-echo
thread pays off (Cole's future echo warned/saved him), Mara closes hopeful with
ONE sequel hook (the Engine's last pulse threw an untraceable signal past the
horizon). Then a skippable CREDITS screen (FourgeAI LABS; a game by Eric Collins
& The Doctor; design/code/story, generated voices, synthesized music/SFX) and
the campaign-complete menu state.

**Voices:** all 22 new lines generated with the established cast and tool —
Mara `avocado_v2:Caitlin`, Cole `avocado_v2:Twenty`, `/opt/hatch/bin/tts`,
verbatim from `Dialogue.kt`, QA'd per line (duration + levels). Game total:
**51 voiced lines** (29 from M1–M3 + 22 new), all packaged in the APK
(`res/raw/voice_<id>.mp3`). QA note: `voice_m5i6.mp3` and `voice_m5o1.mp3`
share size/duration (66,277 B / 8.28 s) — verified NOT duplicates (different
MD5, different RMS profiles, different speakers); it's a CBR-encoding
coincidence.

**New synthesized sounds** (via `tools/generate_sounds.py`): `relay_online`,
`walker_charge` (1.6 s), `walker_cannon`, `walker_step`, `node_break`.

**Chrono Remix: SKIPPED — honestly.** No Remix toggle ships. It was optional,
and faking a half-tested variant of M1 risked the beta's stability; it is a
post-launch roadmap item, not a shipped feature.

## 11. Visual Polish — 0.4.0-beta (the beta candidate)

Goal: kill the "flat untextured prototype" look in Missions 1–3 (the beta
content) inside the existing Kotlin + OpenGL ES 3.0 box engine, zero new
dependencies, 60 fps plausible (one cube mesh, texture binds only on change,
no per-pixel pipelines).

**Procedural textures** (`tools/generate_textures.py`, PIL, seeded; authored as
bright luminance detail ~0.55–1.0 so the shader multiplies them by each box's
art-directed colour — palette stays, flatness goes): `tex_sand`, `tex_rock`
(strata), `tex_concrete` (panels/seams), `tex_metal` (brushed/riveted),
`tex_lab` (steel grid/vents), `tex_asphalt` — 256×256, in `res/raw/`, loaded by
`GameRenderer.loadTextures()` with REPEAT wrap + mipmaps; any load failure
falls back to a 1×1 white texture per slot (never black). The fragment shader
maps them **triplanar-lite**: UVs from local world-size coords (4 m tile),
dominant world-normal axis — no UV attribute needed. Material per box is chosen
by `matFor()`, a size+colour heuristic (ground→sand/asphalt, mesas→rock,
lab steel→lab, grey structures→metal, roads→asphalt, default concrete).
Entities stay on the white texture (their detail is geometry, below).

**Sky + light:** a dedicated sky program — gradient zenith/horizon + sun disc
and glow from world view direction, drawn first each frame (rotation-only
view, depth writes off). Per-mission grading completed: M1 sunset, M2 golden
road (warmer fog + sky), M3 teal lab, M4 deeper dusk, M5 violet dusk; the
chrono pulse now also bends the sky horizon violet. **Blob shadows**: one flat
translucent dark box under the player (fades with jump height), every enemy,
the Suburban, cutscene persons, and the Siege Walker — ground contact, no
floaty actors.

**Particles** (`GameWorld` pool, cap 260, oldest culled; renderer draws fading,
shrinking cubes, depth-write off): muzzle sparks, hit sparks + dust, death
bursts (hound = machinery sparks), barrier-smash debris, generator/core/engine
explosion fire + smoke, boss weak-point destruction, cannon blast dust, cyan
tether trail, and ambient desert dust motes drifting outdoors.

**Model detail (extra boxes, trivial counts):** troopers/Heavies get glowing
visor slits, chest armour panels, belt stripe (trooper), and leg boxes with a
walk-swing approximation + walk bob; the hound already had panel/eye detail
and a trot bob. The Suburban gets side/rear glass, grille, headlights, and
hubcaps (it already had wheels/plough/armour stripe). The M1 comm tower got
cross-bracing bars, a signal dish, and a crossarm. Lab consoles get blinking
indicator lights; time-echo ghosts are now translucent (alpha 0.5), as are all
particle/glow draws via the new `uAlpha` path.

**Animation life:** the gun viewmodel now bobs/sways scaled by Cole's actual
planar movement speed and kicks back on fire (from the `muzzleFlash` timer);
it also gained a barrel, sight post + orange front dot, and the
resistance-orange stripe, textured as metal. Enemy walk bob/leg swing and the
hound trot are renderer-side sine approximations.

**UI/art direction:** menu title letter-spacing + an accent rule under the
title block; briefing panels get the same sunset gradient + accent rule as the
menu. HUD accent language was already consistent (orange accent, panel
backgrounds) and is unchanged.

**Honest remaining visual limits:** it is still a box-geometry engine — no
skeletal animation (walk cycles are sine approximations), no real shadow
mapping (blob shadows only), no normal maps or PBR (single directional light +
fog), textures are 256² luminance detail, not albedo art. Everything above is
verified IN CODE and by a clean compile; **nothing has been rendered on Eric's
Pixel 10 Pro XL yet**, so how much better it actually looks — and the real
frame rate — are unverified until the beta is on his phone. Performance risk is
assessed LOW: draw calls grew modestly (particles ≤260 small cubes worst case,
~6 extra boxes per enemy), texture binds are change-only, and there are no
per-pixel passes.

## 12. Release prep — internal-testing beta (2026-10-27)

- **Signing:** a fresh RSA 2048 upload key was created for Desertfall (alias `desertfall`,
  ~30-year validity, cert SHA-256 `EE:60:7C:7C:07:03:96:AA:76:B8:4C:3C:BE:CC:18:CF:28:92:07:3F:
  70:F4:D6:7D:6D:D5:FF:53:4E:1E:1D:E4`, SHA-1 `02:CA:95:E2:21:FF:11:12:99:42:8B:D6:DA:34:87:09:
  5A:2D:61:5A`; true JKS storetype — the first key generated was PKCS12-in-a-.jks, which failed
  bundle signing with distinct store/key passwords, and was replaced before any use) at
  `~/workspace/keystores/desertfall/` — **outside the
  project**, following the PrismTune/NeuroPath keystore pattern. Passwords live only in the
  chmod-600 `desertfall-keystore-passwords.txt` beside the keystore; **no password or secret is
  in this project or this document**. `app/build.gradle.kts` reads that restricted properties
  file at build time (path overridable via the `DESERTFALL_SIGNING_PROPERTIES` env var) and
  signs the release build with it; debug builds are unaffected.
- **Name-resolved resource safety:** all voice lines (`voice_<id>.mp3`, 51), synthesized SFX
  (38 WAVs) and procedural textures (6 PNGs) are loaded by *name* at runtime via
  `getIdentifier`, so the release build has **minify OFF and resource shrinking OFF**
  (documented in `build.gradle.kts`). The release AAB is verified after every build by counting
  those resource classes inside the bundle — see `play-prep/PACKAGE_README.md` for the verified
  counts, AAB size and SHA-256 for the current candidate.
- **Play prep package:** `play-prep/` holds the internal-testing listing, release notes,
  data-safety answers (zero permissions, no data collected/shared, no ads/IAP), content-rating
  draft (**Mature target** — a blood/gore pass lands before upload, see
  `play-prep/CONTENT_RATING.md`), privacy policy text, and tester recommendations.
- **Rebuild note:** per Eric's 2026-10-27 direction, the gore/war-scale pass runs after this
  prep and **before** upload; the uploaded AAB will be a rebuild of this same version with the
  same signing key, re-verified the same way. This prep's AAB proves the signing + bundling +
  verification pipeline end-to-end.
