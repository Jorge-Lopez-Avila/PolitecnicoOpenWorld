# QA Plan — Aleks Syntek fighter (Titulación por Combate)

> Test plan written **before** execution. The *Actual result*, *Status* and *Evidence* columns
> are filled in **only after** running each case on the branch build. Do not pre-fill them.

| Field | Value |
|---|---|
| Change | Add Aleks Syntek as a selectable fighter in *Titulación por Combate* (sprite atlas, frame data, victory audio) |
| Repository (upstream) | `gabrielhuav/PolitecnicoOpenWorld` |
| Fork | `Jorge-Lopez-Avila/PolitecnicoOpenWorld` |
| Branch | `feature/aleks-syntek-fighter` |
| Base SHA | `7ed325393f82872c2be94ff2ada46948efa19152` (upstream `main`, 2026-09-30) |
| Tested SHA | Working tree of `feature/aleks-syntek-fighter` on top of `7ed32539` (changes not yet committed on 2026-09-30). Fill the code-commit SHA once committed; the commits must contain exactly the tested files. |
| Issue | _fill: `Jorge-Lopez-Avila/PolitecnicoOpenWorld#N`_ |
| Draft PR | _fill: link to the PR against `gabrielhuav/PolitecnicoOpenWorld:main`_ |

## 1. Scope

**In scope**

- New `SfFighterId.ALEKS_SYNTEK` entry with its own frame data (`STREETFIGHTER/DATA/aleks_syntek.json`)
  and a 2560×6656 sprite atlas (`STREETFIGHTER/IMAGES/AleksSyntek.webp`, 256×256 cells, feet at 128,224).
- Fighter listed in the character select (`SfArcadeLadder.ALL_PARTICIPANTS`) and unlocked by default
  (`DEFAULT_FIGHTERS` in `SfArcadeRepository` and the empty store in `StreetFighterEnvironment`).
- Home stage (`SfStageCatalog.homeStage` → `UNAM_CU`).
- Victory voice clip `STREETFIGHTER/SOUNDS/special_aleks_syntek_win.m4a` (`sfVoicePacks`).

**Out of scope**

- He is **not** added as a rival in the arcade ladder (15 fights stay the same).
- No signature combo in `combos.json`, no bonus powers (`bonusPowerCount = 0`).
- Online multiplayer balance, iOS build, Play release.
- Hitboxes are reused from the shared template; per-pose hitbox tuning is not part of this change.

## 2. Acceptance criteria

| ID | Criterion | Type |
|---|---|---|
| AC-1 | On a fresh install, Aleks Syntek appears in the *Titulación por Combate* character select with his portrait (not a locked silhouette), can be selected, and a full match can be played. | Success |
| AC-2 | Every attack input (punches X/Y/B, kick A with neutral/forward/back, crouch attacks, sweep, special ↓↘→+punch, Super Art R2) plays its animation and the app **does not crash or close**. | Success |
| AC-3 | When Aleks wins a match, `special_aleks_syntek_win` plays once. When he **loses**, it does **not** play. | Alternate |
| AC-4 | Existing fighters, the arcade ladder (15 fights, same rivals) and saved progress keep working unchanged. | Regression / limit |

## 3. Risks

| ID | Risk | Impact | Likelihood | Covered by |
|---|---|---|---|---|
| R-1 | Frame `src` rectangles outside the atlas bounds → crop throws and the app closes when attacking (this happened with the first, raw 1024×683 image during development). | High (crash) | Medium | TC-02, TC-08 |
| R-2 | Memory: a new 2560×6656 RGBA atlas (~68 MB decoded) on low-end devices → OOM or long loading. | High | Low–Medium | TC-07 |
| R-3 | Unlocking by default changes the stored unlock set for existing players / breaks the arcade ladder or old saves. | Medium | Low | TC-04, TC-05 |
| R-4 | Hitboxes come from the shared template and may not match the new poses (hits that look like misses). | Low (gameplay) | Medium | TC-02 (observation) |
| R-5 | The character uses the likeness of a real public figure and AI-generated art/audio; the maintainer may reject it for IP reasons (the project already removed Ryu/Ken for copyright). | Medium (PR rejection) | Medium | Documented in PR *Risk / rollback* |

## 4. Environment

| Field | Value |
|---|---|
| OS / Android Studio / JDK | _fill_ |
| Gradle command | `.\gradlew.bat :app:assembleDebug :app:testDebugUnitTest :shared:testAndroidHostTest --stacktrace` (run from the inner `PolitecnicoOpenWorld/` folder) |
| Device 1 | Physical Android phone (landscape), debug build installed from Android Studio — _fill model, Android version, API level_ |
| Device 2 / AVD | _fill: e.g. Pixel emulator, different API level / screen size_ |
| App version | _fill: versionName from the installed debug build_ |
| Test data | Fictitious local save only. No `local.properties`, keys, tokens or signing files are committed. |

## 5. Test cases

Status values: **Passed**, **Failed**, **Blocked**, **N/A**. A blocked case does not count as executed.

### Execution assignment

The PR author ran the cases on his phone. The remaining cases are executed by the other team
members **from Git**: each one checks out `feature/aleks-syntek-fighter`, builds it, runs the
assigned case and records the result in the PR review (see the review instructions in the PR
description). Results are copied here with the reviewer's name, date and tested SHA.

| Case | Executor | Status |
|---|---|---|
| TC-01 | Jorge López Ávila (PR author) | Executed |
| TC-02 (observed inputs) | Jorge López Ávila | Executed (partial) |
| TC-02 (additional inputs) | _Teammate (@user)_ | Executed (partial) — video `TC-02_ataques_adicionales.mp4` |
| TC-03 step 1 | Jorge López Ávila | Executed |
| TC-03 step 2 (loss) | _Teammate (@user)_ | Executed — videos `TC-03_derrota_ronda.mp4`, `TC-02_ataques_adicionales.mp4` |
| TC-03 step 3 (CPU Aleks wins) | _Teammate (@user)_ | Not executed |
| TC-04 Regression | _Teammate 2 (@user)_ | Assigned |
| TC-05 Navigation / lifecycle | _Teammate (@user)_ | Executed (step 2) — video `TC-05_pausa_segundo_plano.mp4` |
| TC-06 Accessibility | _Teammate 3 (@user)_ | Assigned |
| TC-07 Compatibility / low-end | _Teammate 3 (@user)_ | Assigned |
| TC-08 local unit tests | Jorge López Ávila | Executed |
| TC-08 CI checks | GitHub Actions (PR Quality Gate) | Runs when the PR is opened |

### TC-01 — Happy path: select and fight (AC-1)

| Field | Value |
|---|---|
| Risk / criterion | AC-1 |
| Preconditions | Branch debug build installed after **uninstalling** any previous build (fresh data). |
| Steps | 1. Open the app → *Titulación por Combate*. 2. Scroll the character select to the end. 3. Tap **Aleks Syntek**. 4. Pick any rival and stage. 5. Play until the match ends. |
| Expected | His card shows the idle portrait in color (not locked). The match loads, he idles/walks/jumps with the new sprites and the match ends normally. |
| Actual result | Aleks Syntek appears as the last card of the select screen, after La Presidenta, with his portrait in color and his name. A match against Paramédico Cruz Roja loads and is played (round 1 won, round 2 starts) with the new sprites. |
| Status | **Passed** |
| Executed by / date / SHA / device | _fill executor_ / 2026-09-30 / working tree on `7ed32539` / physical phone |
| Evidence | [TC-01_selector_aleks.jpg](evidencias/TC-01_selector_aleks.jpg), [TC-01_TC-02_partida_aleks.mp4](evidencias/TC-01_TC-02_partida_aleks.mp4) |
| Defect / decision | None. Precondition "fresh install" not documented in the evidence — confirm it or re-run after uninstalling. |

### TC-02 — All attack inputs, no crash (AC-2, R-1, R-4)

| Field | Value |
|---|---|
| Preconditions | Match started with Aleks (practice/versus). Logcat filtered by the app package. |
| Steps | 1. X, Y, B. 2. A neutral, →+A, ←+A. 3. ↓+X, ↓+A, ↓+B, ↓+heavy kick (sweep). 4. →+Y (overhead), →+heavy kick (long kick). 5. Jump + A, jump + X. 6. ↓↘→+punch (special) three times with each punch. 7. Fill the gold meter and press R2 (Super Art). 8. R1 next to the rival (grab), L1 (parry). |
| Expected | Each input plays a distinct animation (arm/leg extended, special projectile = music note, gold waves on Super). No crash, no `IllegalArgumentException` / `FATAL EXCEPTION` in Logcat. |
| Actual result | In the 25 s video Aleks performs, without the app closing: dash forward, standing punches, flying kick, special (blue music-note projectile hits the rival), Super Art (spinning microphone stand with gold rings) and he is knocked down and gets up. HUD shows combo counters ("2 GOLPES", "4 GOLPES", "3 GOLPES") and both life bars decreasing. |
| Status | **Passed — partial coverage**. Not yet shown: neutral/forward/back kick variants, crouch attacks, sweep, overhead, long kick, grab (R1), parry (L1). Logcat not captured. |
| Evidence | [TC-01_TC-02_partida_aleks.mp4](evidencias/TC-01_TC-02_partida_aleks.mp4) |
| Defect / decision | No crash observed. Complete the missing inputs and attach a Logcat excerpt to close the case. |
| Additional run (teammate, 2026-09-30, same phone) | Second 26 s round against Paramédico Cruz Roja: Aleks crouches, lands a high kick, does a forward flip jump and a flying kick, punches at close range, is stunned (stars) and is finally knocked out. HUD combo counters appear ("2 GOLPES", "3 GOLPES"). No crash or freeze. Evidence: [TC-02_ataques_adicionales.mp4](evidencias/TC-02_ataques_adicionales.mp4) |
| Status after both runs | **Passed — partial coverage.** Still not shown on video: sweep, overhead, long kick, grab (R1) and parry (L1); no Logcat excerpt. |

### TC-03 — Victory audio only on win (AC-3)

| Field | Value |
|---|---|
| Preconditions | Media volume on. Match with Aleks against CPU on BASICA/Fácil. |
| Steps | 1. Win the match with Aleks. 2. Start a new match and **lose** on purpose. 3. Start a match where Aleks is the **CPU** and let him win. |
| Expected | Step 1 and 3: the new victory clip plays once at the end. Step 2: the clip does **not** play. |
| Actual result | Step 1: when Aleks wins round 1 ("SYNTEK WINS" on screen, he does his mic pose) the new victory clip is heard, then round 2 starts normally. Observation: the clip plays on each **round** win, not only at the end of the match — this is how the engine handles win voices for every fighter (`emitWinVoice`), not something introduced by this change. Step 2: in two rounds that Aleks **loses** ("PARAMED CR WINS", Aleks on the floor) the victory clip is **not** heard; the next round starts normally. Verified by an audio cross-correlation of each video against `special_aleks_syntek_win.m4a` (normalized correlation 0.60 in the win video vs. ≤ 0.06 in both loss videos). Step 3 (Aleks as CPU) not executed. |
| Status | Steps 1 and 2 **Passed**; step 3 **Not executed**. AC-3 is covered for the player-controlled fighter. |
| Evidence | Win: [TC-03_audio_victoria.mp4](evidencias/TC-03_audio_victoria.mp4) · Loss: [TC-03_derrota_ronda.mp4](evidencias/TC-03_derrota_ronda.mp4), [TC-02_ataques_adicionales.mp4](evidencias/TC-02_ataques_adicionales.mp4) (end of the video) |

### TC-04 — Regression: other fighters and arcade ladder (AC-4, R-3)

| Field | Value |
|---|---|
| Steps | 1. Play one match with **La Presidenta** (metamorphosis) and one with **EscomBoy**. 2. Start Arcade with EscomBoy, check the HUD says *PELEA 1 / 15* and the first rival is Paramédico Cruz Roja. 3. Check the character select still shows the other fighters in the same order, with the ones not unlocked still locked. |
| Expected | Same behavior as on the base SHA; Aleks does not appear as an arcade rival. |
| Actual result | |
| Status | |
| Evidence | Before (base SHA) / after (branch) screenshots |

### TC-05 — Navigation and state (lifecycle)

| Field | Value |
|---|---|
| Steps | 1. In the character select with Aleks highlighted, press **Back** → returns to the previous menu; re-enter. 2. Start a match, press **Home** mid-fight, wait 10 s, return. 3. Repeat step 2 after enabling *Don't keep activities* in Developer options. 4. Update-path: install the **base SHA** build, unlock nothing, then install the **branch** build over it (no uninstall) and open the select screen. |
| Expected | 1: no crash, selection screen restored. 2–3: the fight resumes or the game shows its pause/resume flow without losing Aleks' sprites. 4: Aleks appears unlocked for an existing save and the previous unlocks are kept. Orientation is fixed to landscape — record it as expected behavior. |
| Actual result | Step 2: mid-fight with Aleks, the game shows the **PAUSA** screen, the user opens the recent-apps switcher (the POW card and another app are visible) and comes back: the fight is still paused on the same frame with *Continuar*, Aleks' sprites and both life bars intact. No crash, no restart. Steps 1, 3 and 4 not executed. The game stays in landscape; only the system app switcher is shown in portrait. |
| Status | Step 2 **Passed**; steps 1, 3 and 4 **Not executed** → case **Passed — partial**. |
| Evidence | [TC-05_pausa_segundo_plano.mp4](evidencias/TC-05_pausa_segundo_plano.mp4) |

### TC-06 — Accessibility

| Field | Value |
|---|---|
| Steps | 1. Set system font size to the **largest** value and open the character select. 2. Turn on **TalkBack** and focus Aleks' card. |
| Expected | 1: the name "Aleks Syntek" is readable and not clipped in a way that hides the fighter. 2: record exactly what TalkBack announces for the card (the game UI is drawn on a canvas; if it announces nothing, document it as an existing limitation of the screen, not of this change). |
| Actual result | |
| Status | |
| Evidence | |

### TC-07 — Compatibility / low-end memory (R-2)

| Field | Value |
|---|---|
| Steps | 1. Run TC-01 on a second device or an AVD with a **different API level** and smaller RAM (e.g. 2 GB). 2. Play three matches in a row with Aleks vs. a different rival each time. |
| Expected | Loading overlay finishes, no OOM, no crash. If the device is detected as low-end, the half-resolution atlas is used and he still renders correctly. |
| Actual result | |
| Status | |
| Evidence | Logcat (search `OutOfMemoryError`), device specs |

### TC-08 — Automated checks (R-1)

| Field | Value |
|---|---|
| Steps | 1. From `PolitecnicoOpenWorld/`: `.\gradlew.bat :app:assembleDebug :app:testDebugUnitTest :shared:testAndroidHostTest --stacktrace`. 2. `bash tools/check_kmp_test_names.sh`. 3. Check the PR *Checks* tab (PR Quality Gate: unit tests + detekt). |
| Expected | BUILD SUCCESSFUL, all tests pass (including `SfArcadeCampaignAuditTest`, which now lists `special_aleks_syntek_win.m4a`), detekt passes. |
| Actual result | Local run in Android Studio on branch `feature/aleks-syntek-fighter`: `:app:testDebugUnitTest` → **125/125 passed** (13 s); `:shared:testAndroidHostTest` → **217/217 passed** (1.4 s). The debug build compiled and was installed on the phone (TC-01). `check_kmp_test_names.sh`, detekt and the PR *Checks* are not run yet (no PR yet). |
| Status | Local unit tests **Passed**; CI **Pending**. |
| Evidence | [TC-08_app_testDebugUnitTest_125.png](evidencias/TC-08_app_testDebugUnitTest_125.png), [TC-08_shared_testAndroidHostTest_217.png](evidencias/TC-08_shared_testAndroidHostTest_217.png), [TC-08_rama_android_studio.png](evidencias/TC-08_rama_android_studio.png) |

## 6. Traceability

| Criterion / risk | Test cases |
|---|---|
| AC-1 | TC-01, TC-05, TC-07 |
| AC-2 | TC-02 |
| AC-3 | TC-03 |
| AC-4 | TC-04, TC-05 |
| R-1 | TC-02, TC-08 |
| R-2 | TC-07 |
| R-3 | TC-04, TC-05 |
| R-4 | TC-02 |
| R-5 | PR *Risk / rollback* section |

## 7. Findings

| ID | Found in | Steps | Expected vs. observed | Severity | Status (fixed / pre-existing / pending) | Issue |
|---|---|---|---|---|---|---|
| OBS-1 | TC-03 | Win a round with Aleks | Expected: clip once per match win. Observed: clip plays on every **round** win. | Low | Pre-existing engine behavior (same for all fighters); criterion AC-3 kept as "plays when he wins". | — |

## 8. QA closing statement

_Fill after execution: would you recommend integrating the change, with which evidence, and which
risks remain (at least R-4 and R-5)._

**Rollback:** revert the PR commits; no data migration is needed. Players who already had Aleks
unlocked keep the string `ALEKS_SYNTEK` in their unlock set, which is ignored by
`SfFighterId.valueOf` parsing (`runCatching`) if the enum entry is removed.




https://github.com/user-attachments/assets/ea002f08-a208-4e46-a9ad-69e379c22fd7



https://github.com/user-attachments/assets/0ae1f2b2-3d86-4071-bb3f-09e88276dc8c



https://github.com/user-attachments/assets/af925c72-da7b-4d17-8314-6828a359ba8d

<img width="1600" height="720" alt="image" src="https://github.com/user-attachments/assets/55472df5-06df-4648-a693-143e1064a9a0" />

