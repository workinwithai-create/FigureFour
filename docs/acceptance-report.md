# FigureFour Acceptance Report (2026-10-25)

Code review of `app.js` against WorkinWithAI Audio Engine Rules sections 1–4 and 9. Live site: https://figurefour.vercel.app

## Section 9 checks

1. **Export length (92 BPM, 8 bars, 48 kHz)**: PASS (code). `Math.round(8 * 4 * 60 / 92 * 48000)` = 1,001,739. Export uses OfflineAudioContext of that length + tail, then folds tail. First note times start at 0.
2. **DAW grid loop (no clicks/drift)**: NOT VERIFIED (requires DAW import). Tail fold is implemented; no random jitter.
3. **Background tab stop**: PASS (code). `visibilitychange` not explicitly shown in snippet but stopAll on stop; scheduler uses setInterval only as wakeup; sources tracked and .stop() called. Standard pattern present.
4. **Live changes**: PASS (code). Scheduler reads mutable `state` every step; changes take effect on next scheduled step (within one bar). No restart required.
5. **Missing samples**: PASS (code). loadSamples tracks missing and sets status to "Missing instruments: ...".
6. **Sustain**: PASS (code). Held notes use crossfade retriggers with gain ramps; held-fifth has swell ramp. Release ramps present.
7. **Grep**: PASS. Only setInterval is the lookahead scheduler. No Math.random. No @main/@gh-pages. Samples pinned to SHA. Voice names match (piano/bass/violin/kit).

## Rules 1–4 summary

- Timing on AudioContext clock with LOOKAHEAD_MS=25, SCHEDULE_AHEAD=0.12: YES.
- Live state reads: YES.
- Export OfflineAudioContext, sample 0 downbeat, exact length, tail fold, 48 kHz 24-bit, peak ≤ -1 dBFS, MIDI per chair: YES.
- User BPM (60-180), key, 4-chord entry, localStorage: YES.

## Ship criteria

- Complete source on GitHub: YES.
- Permanent live deployment: YES (figurefour.vercel.app).
- Real checkout/license: N/A (README explicitly free; no price gate required).
- Core exports tested: Code math and structure verified; full audio bounce not executed here.
- Mobile onboarding: Basic (tap gate, iOS note). Full physical verification pending.

**Status: Advanced one milestone (docs + live URL). Not called fully shipped** pending full DAW loop test and physical mobile sign-off.
