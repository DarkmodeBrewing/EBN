# EBN — Electronic Beat Notation
## Specification v0.1

© Lars Larsson, 2026  
Licensed under CC BY 4.0
---

## 1. Purpose

EBN (Electronic Beat Notation) is a lightweight, text-based notation system for
sketching **EBM / Industrial** music without requiring a DAW.

It is designed to:
- Work on phones and low-friction tools
- Map directly to step sequencers
- Separate composition from sound design
- Favor clarity, repetition, and discipline

Non-goals:
- Full musical notation
- MIDI export
- DAW- or synth-specific features
- Expressive performance nuance

---

## 2. Core Time Model

- Music is divided into **bars**
- Each bar is independent
- Each bar contains **16 steps** (16th notes)
- Step numbers always reset per bar

> Steps are always numbered `1..16`  
> There are no global step counters (no 17..32, etc.)

---

## 3. Required Header Fields

Every EBN block MUST declare the following fields:

```
Time: 4/4
Steps: 16
Bars: N
```

Rules:
- `Time` is informational only (v0.1 assumes 4/4)
- `Steps` is fixed to 16 in v0.1
- `Bars` defines how many bar blocks follow

---

## 4. Bars

Bars are declared explicitly and sequentially:

```
Bar 1:
Bar 2:
...
```

Each bar may define:
- Percussion events
- Melodic / tonal events
- Silence
- References to other bars
- Overrides of referenced content

Bars are processed independently.

---

## 5. Step Notation

### 5.1 Explicit steps

```
Kick = [1, 5, 9, 13]
```

Defines events on specific steps.

### 5.2 Ranges

```
Hat = [1..16]
```

Defines events spanning a continuous range of steps.

### 5.3 Ranges with step size

```
Hat = [1..16 step 2]
```

Defines repeating events with a fixed interval.

---

## 6. Event Semantics

EBN v0.1 defines three event semantics.

### 6.1 hit

- One-shot event
- Default if no semantic is specified

```
Snare = hit [5, 13]
```

Equivalent to:

```
Snare = [5, 13]
```

### 6.2 sustain

- Continuous event across multiple steps

```
Bass = sustain [1..16]
```

### 6.3 silence

- Explicit absence of sound
- Used for intentional gaps or endings

```
Silence = [15, 16]
```

---

## 7. Instrument Definitions

### 7.1 Percussion (flat instruments)

Flat instruments define events directly:

```
Kick  = [1, 5, 9, 13]
Snare = [5, 13]
Hat   = [1..16 step 2]
```

### 7.2 Melodic / Tonal instruments (keyed)

Tonal instruments define pitch-to-event mappings:

```
Lead = {
  F2: sustain [1..8],
  G2: sustain [9..16]
}
```

Rules:
- Keys are note names with octave numbers
- Each key defines its own event semantics
- Overlapping ranges are allowed and intentional

---

## 8. Bar Referencing (DRY Rule)

Bars may reference earlier bars to avoid repetition.

### 8.1 Simple reference

```
Bar 2:
  ref: Bar 1
```

This copies all content from `Bar 1`.

### 8.2 Reference with overrides

```
Bar 2:
  ref: Bar 1
  change:
    HatAccent = []
```

Rules:
- Referenced content is copied conceptually
- `change` overrides or removes specific elements
- Empty lists remove events

---

## 9. Comments

Comments are allowed anywhere and are ignored by parsers.

```
// Builds pressure
G2: sustain [1..16]
```

---

## 10. Optional Metadata

Optional metadata may appear before bar definitions.

```
BPM: 90
Style: Industrial EBM
Mood: Controlled panic
Status: DRAFT | CONFIRMED | EXPERIMENTAL
```

Metadata has no semantic meaning in v0.1.

---

## 11. DAW Mapping Assumptions

EBN assumes the following defaults:

- 1 bar = 16-step loop
- Mono signals by default
- No swing unless explicitly stated
- No velocity, probability, or modulation in v0.1
- Sound design and mixing occur after notation

---

## 12. Canonical Example

```
### Untitled Song — Intro

BPM: 90
Time: 4/4
Steps: 16
Bars: 2

Bar 1:
  Kick  = [1, 5, 9, 13]
  Snare = [5, 13]
  Hat   = [1..16 step 2]
  HatAccent = [1, 9]

  Lead = {
    F2: sustain [1..8],
    G2: sustain [9..16]
  }

Bar 2:
  ref: Bar 1
  change:
    HatAccent = []
```

---

## 13. Versioning

- v0.x → experimental, breaking changes allowed
- v1.0 → stable, backwards-compatible

---

## 14. Design Philosophy

EBN is intentionally rigid.

Constraint is not a limitation — it is the aesthetic.
