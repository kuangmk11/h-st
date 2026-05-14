# høst
![jord](doc/1.png)
![løv](doc/2.png)
![lys](doc/3.png)

**Høst** (Norwegian: "Harvest / Autumn") is a norns script combining a drone synth, a polyphonic synth, and an FX chain. Hardware: norns + 16×8 grid + arc.

---

## Focus Modes (Fokus)

Three layers, each controlling a different part of the engine. Focus determines what the arc encoders and screen display.

| # | Name | Meaning | Layer |
|---|------|---------|-------|
| 1 | **Jord** | Earth | Drone synth |
| 2 | **Løv** | Leaves | Poly synth |
| 3 | **Lys** | Light | FX chain |

---

## Norns Hardware

### Keys
| Key | Action |
|-----|--------|
| K2 | Switch to **Jord** |
| K3 | Switch to **Løv** |
| K2 + K3 held | Switch to **Lys** |

### Encoders
| Enc | Action |
|-----|--------|
| E1 | Cycle focus |
| E2 | FX gain (distortion drive) |
| E3 | Poly scale (envelope time scaling) |

---

## Grid Layout (16×8)

### Column 1 — Controls
| Row | Function |
|-----|----------|
| 1 | **Hold** toggle — sustain notes after release |
| 2 | **Loop** toggle — loop envelopes (tremolo) |
| 3 | Octave **3** (high) |
| 4 | Octave **2** (mid, default) |
| 5 | Octave **1** (low) |
| 6 | Focus → **Jord** |
| 7 | Focus → **Løv** |
| 8 | Focus → **Lys** |

### Columns 2–16 — Note Playing Area

Pitch layout uses a **5ths-based isometric mapping**:

```
note = 12 + x + 5 × (8 - y)
```

Moving right = +1 semitone. Moving up = +5 semitones (perfect fourth). Up to 4 notes tracked simultaneously in Lua; the engine supports up to 6 voices (oldest stolen first).

Active notes cast a diagonal shadow trail down-left on the grid. Held notes display at brightness 10.

### Hold Mode
Toggle with grid (1,1). When on, releasing a key does not stop the note — pressing the same key again toggles it off. Pressing the hold button again releases all held notes.

### Loop Mode
Toggle with grid (1,2). When on, the poly envelope runs as a repeating A-R-A-R pattern — a rhythmic gate/tremolo effect.

---

## Arc Encoders (by Focus)

### Jord (Drone)
| Enc | Param | Effect |
|-----|-------|--------|
| 1 | Klangfarge (timbre) | Morphs: sawtooth → sine → square |
| 2 | Støy (noise) | Adds pitch noise / pink noise crossfade |
| 3 | Terskel (bias) | LPG threshold — how much of the drone sounds |
| 4 | Frekvens (freq) | Drone pitch (0.2–2000 Hz exponential) |

### Løv (Poly)
| Enc | Param | Effect |
|-----|-------|--------|
| 1 | Klangfarge (timbre) | Morphs: sawtooth → sine → square |
| 2 | Støy (noise) | Adds pitch noise / pink noise crossfade |
| 3 | Terskel (bias) | LPG threshold — brightness/density |
| 4 | Kontur (shape) | Envelope contour: short attack → long attack → long release → short release |

### Lys (FX)
| Enc | Param | Effect |
|-----|-------|--------|
| 1 | Første (peak 1) | First SVF peak filter frequency (20–20kHz) |
| 2 | Andre (peak 2) | Second SVF peak filter frequency (20–20kHz) |
| 3 | Kropp (body) | Morphs FX chain: dry → filter → filter+delay → delay → dry (wraps) |
| 4 | Tid (time) | Delay time (0.01–2s) |

### Arc Button
| Action | Effect |
|--------|--------|
| Press | Toggle to/from **Lys** focus momentarily |

---

## FX Chain (Lys)

All audio passes through:
1. **Dual SVF peak filters** (peak1, peak2) — resonance scales with `body`
2. **Feedback delay** with LPF rolloff on the feedback path
3. **Soft distortion** — tanh saturation with gain compensation

The `body` parameter (arc E3 in Lys) sweeps through five modes: dry → filtered → filtered+delay → delayed → dry.

---

## Params Menu

| Section | Param | Notes |
|---------|-------|-------|
| HØST | Fokus | Jord / Løv / Lys |
| HØST | Hold? | Nei / Ja |
| Main | Volum | Master amp (0–2) |
| JORD | Volum | Drone amp |
| JORD | Klangfarge | Drone timbre |
| JORD | Støy | Drone noise |
| JORD | Terskel | Drone bias |
| JORD | Frekvens | Drone pitch |
| LØV | Volum | Poly amp |
| LØV | Klangfarge | Poly timbre |
| LØV | Støy | Poly noise |
| LØV | Terskel | Poly bias |
| LØV | Kontur | Poly envelope shape |
| LYS | Første | Filter peak 1 |
| LYS | Andre | Filter peak 2 |
| LYS | Kropp | FX body (mode) |
| LYS | Tid | Delay time |
| LYS | Resonans | Filter resonance max |
| LYS | Ekko | Delay feedback max |
| FORVITRING | Styrke | Distortion gain |
| NOTER | Vekst | Max attack time |
| NOTER | Forfall | Max release time |
| NOTER | Skala | Envelope scale (E3 on norns) |
| NOTER | Repeter? | Loop envelopes (grid 1,2) |

---

## State

Params are saved automatically on exit to `state.pset` and restored on load.

---

## Sequencer (WIP)

`chaos_event()` in `høst.lua` is a stub — defined but not yet started or implemented. It is the intended home for a generative sequencer. The closest working feature is **Loop mode** (grid 1,2 / `poly_loop`), which creates rhythmic envelope cycling on held notes.
