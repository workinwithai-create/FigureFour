# FigureFour — Acceptance report (Audio Engine Rules §9)

Date: 2026-10-25 · Checked commit: `63719f9` base + this docs commit (supersedes the earlier code-review-only report in 63719f9) · Live: https://figurefour.vercel.app

**Method.** Code review with file:line references and greps. Runtime tests ran the repo locally (`python3 -m http.server`) in headless Chromium through Playwright, with the real pinned FluidR3 samples loaded from jsDelivr. The exported WAV and MIDI were parsed in Python. Before testing, the live deploy was compared byte-for-byte with the repo (`app.js`, `index.html`, `styles.css` are identical; `/` returns HTTP 200).

Legend: **RT** = runtime-tested · **CR** = code review only.

## Section 9 checks

| # | Check | Result | Mode | Evidence |
|---|---|---|---|---|
| 1a | 92 BPM, 8 bars, 48 kHz → exactly 1,001,739 samples | **PASS** | RT + CR | The parsed WAV has `samples=1001739, sr=48000`. The app status reads "WAV 1001739 samples · expected 1001739". Math: `app.js:438` `Math.round(bars * 4 * 60 / state.bpm * SR)` |
| 1b | First transient within 1 ms of sample 0 | **PASS** | RT | The first sample above 1% FS is sample 16 (0.33 ms) |
| 2 | DAW import, loop ×4, no clicks or drift | **NOT VERIFIED** | CR | No DAW on the box. Supporting evidence: the length is exact (above) and the tail folds back into the loop start at `app.js:456-461` (`loop[i-length] += tail`) |
| 3 | Hide the tab → stops cleanly, no stacked notes | **PASS** | RT (simulated) | Hidden state was simulated with `visibilitychange`: `timerId` went from set to `null`, `active.length` = 0. Code at `app.js:575-576` → `stopAll()` (`app.js:266-273`). A real 30-second background wait was not run |
| 4 | Changes heard within one bar, no restart | **PASS (code)** | CR | `scheduleStep` rebuilds `eventsFor(state)` on every 16th step (`app.js:240-253`). The scheduler reads `state.bpm` live (`app.js:260`). Listening was not tested |
| 5 | Block the sample host → UI names missing instruments | **PASS** | RT | jsDelivr was blocked with a Playwright route. Status read "Missing instruments: piano, bass, violin, kit" (`app.js:345-347`) |
| 6 | Longest pad sustains at slowest tempo | **NOT VERIFIED** | CR | held-fifth is 16 steps with a swell (`app.js:165`). Playback and envelope at BPM 60 were not measured |
| 7a | No `@main` / `@gh-pages` | **PASS** | CR | `grep -nE "@main\|@gh-pages" app.js index.html` → no matches. Samples are pinned at `app.js:2` `PreEight@d58301e4a494555f411a2afbc448b724136eee76` |
| 7b | No `Math.random` in export path | **PASS** | CR | `grep -n Math.random` → no matches anywhere |
| 7c | Voice names match samples | **PASS** | CR | Chairs piano/bass/violin/kit map to `samples/{piano,bass,violin}/*.mp3` and `drums/{kick,snare,hihat}.mp3` (`app.js:9-13, 327-330`). All 31 files loaded |

## Additional checks requested

| Check | Result | Mode | Evidence |
|---|---|---|---|
| Scheduler on the audio clock | **PASS** | CR | Notes are timed from `ctx.currentTime` lookahead (`app.js:258`, `nextStepTime`), with `src.start(t)` at `app.js:233`. `setInterval(scheduler, 25)` (`app.js:294`) only wakes the lookahead loop and does not time notes. `requestAnimationFrame` is UI-only (playhead) |
| Length math + tail fold | **PASS** | RT + CR | See 1a. Offline render length = loop + 1.6 s tail (`app.js:440`), and the tail folds into the loop head (`app.js:456-461`) |
| Peak ≤ −1 dBFS | **PASS** | RT + CR | Measured peak 0.3899 FS (−8.2 dBFS) on the default recipe. Limiter at `app.js:462-466` scales to `10^(-1/20)` if exceeded. The clamp branch was not triggered at runtime |
| WAV 48 kHz / 24-bit | **PASS** | RT | Parsed fmt chunk: 2 ch, 48000 Hz, 24 bits (`app.js:414,417`) |
| MIDI export | **PASS** | RT | Downloaded a 1,484-byte `MThd` file with 4 `MTrk` chunks (tempo track + chairs) |
| Samples pinned to commit SHA | **PASS** | CR | `app.js:2` (see 7a) |
| No unused auth/db | **PASS** | CR | `grep -i "supabase\|firebase\|auth"` → none. Only `localStorage` is used, for settings |
| `.gitignore` has `.env*` and `.vercel` | **PASS (fixed in this commit)** | CR | The repo had no `.gitignore`. Added one with `.env*`, `.vercel` |
| No committed `.env` | **PASS** | CR | `git ls-files \| grep -i env` → none |

## Ship criteria

| Criterion | Status |
|---|---|
| Source on GitHub | ✅ workinwithai-create/FigureFour (main) |
| Live deploy | ✅ https://figurefour.vercel.app returns 200 and serves code identical to the repo |
| Free (no license gate) | ✅ README: "This build is free (no license gate)." |
| Exports tested | ✅ WAV + MIDI runtime-tested in headless Chromium |
| Mobile onboarding verified on a real device | ❌ **NOT VERIFIED**: no physical phone was available |
| §9 #2 (DAW loop) and #6 (pad sustain) | ⚠️ NOT VERIFIED |

**Status: not fully shipped. Real-device mobile check pending.** The DAW loop test (#2) and the pad sustain listen (#6) are also still open.
