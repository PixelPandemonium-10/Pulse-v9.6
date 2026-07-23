# PULSE — Adaptive Reaction Timing Instrument

[![Live Demo](https://pixelpandemonium-10.github.io/Pulse-v9.6/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A precision rhythm game with real-time statistical calibration.
Tap as the ring closes — the adaptive window scales to your personal
reaction-time distribution, maintaining challenge at the edge of your
ability.

[Play it now →](https://pixelpandemonium-10.github.io/Pulse-v9.6/)

---

## How It Works

The game maintains a running statistical model of your absolute timing
errors. On each hit, it:

1. **Transforms** |error| via variance-stabilizing log mapping:
   `y = 60 · ln(1 + |error| / 60)`
2. **Fits** a log-normal MLE over regime-tagged samples (slow/fast,
   with 60 ms hysteresis to prevent flicker)
3. **Sets the window** to the empirical 85th percentile of the last
   20 in-regime magnitudes, capped to [4.5%, 22%] of the beat cycle
4. **Adjusts BPM** based on window tightness — narrow window → faster,
   wide window → slower

Late responses (t ≥ 1.0 cycle) and extreme early taps
(&gt; 6% of cycle before window) are treated as censored observations
and excluded from spread estimation.

| Feature | Description |
|---------|-------------|
| Adaptive window | Empirical 85th percentile, n ≥ 10 gated |
| Regime system | Slow/fast with hysteresis, cache invalidation on switch |
| Live visualization | Gaussian PDF + timing-error histogram |
| Session feedback | Bias, σ_log, window size, regime, difficulty tier |

---

## Controls

| Input | Action |
|-------|--------|
| `Space` or tap | Register timing |
| `Esc` | End session early |

---

## Technical Notes

- **Single-file deployment.** All logic, styles, and assets inline.
  No build step, no external dependencies.
- **Canvas rendering.** Game loop, particle system, and background
  grid via 2D context with DPR scaling.
- **Responsive layout.** CSS Grid with breakpoint at 720 px.
- **Persistence.** Personal best stored in `localStorage`
  (`pulse3_best` key).

  ---

  ## Future Work

| Priority | Item | Notes |
|----------|------|-------|
| **Bug** | Fix `best` score persistence | `Math.max` assignment order issue — one-line fix |
| **Feature** | Web Audio API integration | Tone.js or native oscillator for beat cues |
| **Feature** | Cross-session statistics | Aggregate reaction times across sessions |
| **Feature** | Export session data | JSON/CSV of raw timing errors for external analysis |
| **Polish** | Reduced motion preference | Respect `prefers-reduced-motion` media query |

---


### Browser Requirements

ES2015+ (`Math.log1p`, `ResizeObserver`, `PointerEvent`). Modern
browsers only — Chrome, Firefox, Safari, Edge.

---

## Running Locally

```bash
git clone https://github.com/YOUR_USERNAME/pulse-game.git
cd pulse-game

# Option 1: open directly
open index.html        # macOS
xdg-open index.html    # Linux

# Option 2: local server (recommended for DPR/resize testing)
python3 -m http.server 8000
# Visit http://localhost:8000
