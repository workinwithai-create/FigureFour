# FigureFour

Four-bar melodic figure desk for bedroom producers and AI-music finishers.

The loop has chords and a pocket. It has no signature line. Session players write a short repeating figure (FluidR3 violin) so the section finally has something memorable to finish around.

Live samples only — FluidR3 acoustic grand, upright bass, violin, and acoustic kit from the PreEight sample bank pinned to commit `d58301e4a494555f411a2afbc448b724136eee76`.

No oscillators. Audio never leaves the tab.

## Distinct from yesterday and the rest of the line

| Tool | Job |
| --- | --- |
| TagFour | Four-bar last-line tag after the money chorus |
| LiftTwo | Two-bar pre-hook lift |
| PreEight | Eight-bar musical pre-chorus |
| CallFour | Call-and-response after a sung line |
| StabFour | Horn stabs on the chorus |
| HoleFour | Stop-time turnaround |
| **FigureFour** | Four bars that put a signature melodic figure on the pocket you already have |

Yesterday's ships covered form transitions and tags. FigureFour is the missing motif so a stuck loop has a line to finish the song around.

## Play

- A · Pocket (chords, bass, kit)
- B · Figure (same pocket + violin figure)
- Change recipe, mutes, BPM, key, or chords while playing — takes effect on the next step.

## Export

WAV (48 kHz, 24-bit stereo, first downbeat at sample 0, tail folded for seamless loop) and matching MIDI (one chair per track, tempo and time signature set).

At 92 BPM and 4 bars the loop is exactly 500,870 samples (`Math.round(4 * 4 * 60 / 92 * 48000)`). For the 8-bar acceptance check, set Bars to 8.

## Pricing recommendation

**One-time $19** on Gumroad or Lemon Squeezy. This is a finite desk, not a recurring studio. Optional $9 add-on inside Forge Pass ($24/mo). Do not put the core tool behind a monthly meter.

Best place to sell: Gumroad (quick indie tool audience) + a pinned X demo next to the Forge line. Mirror on Lemon Squeezy for Merchant of Record.

This build is free (no license gate). If priced later, add a Lemon Squeezy key check or WorkinWithAI entitlement.

## Stack

Static HTML/CSS/JS. Deploy on Vercel team `release-forge`.

## Acceptance checks (section 9)

1. Export at 92 BPM, 8 bars, 48 kHz → exactly 1,001,739 samples; first transient within 1 ms of sample 0.
2. Import the WAV at bar 1 of a 92 BPM DAW session and loop 4 times → no clicks at seam, no drift.
3. Play, switch tab for 30 s, return → stopped cleanly, no stacked notes.
4. Change recipe, mutes, BPM while playing → heard within one bar, no restart.
5. Block sample host, reload → UI names missing instruments.
6. Hold longest pad (held-fifth recipe) at slowest tempo → sustains to end of note.
7. Grep: no `@main` or `@gh-pages`, no `Math.random` in export path, voice names match samples.

## License

MIT. workinwithai-create.
