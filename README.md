# Chord Progression Trainer

> **Status:** Pre-development — goals & tech stack only. No application code yet.
> **Name:** Working title. Final brand name pending screening and decision. All naming claims in this repo are provisional.

An Android app for learning chord progressions the way musicians actually acquire the skill: by ear, against real songs, with immediate feedback.

The target user is the person who plays an instrument but freezes when asked "what chords are in this song?" — and the goal is to become the person who hears a song once and can call the progression.

## Goals

### 1. Learn chord progressions by ear
The core learning loop. Progressions are taught as *functions in a key* (Roman numerals: I–V–vi–IV), not as isolated chord shapes, because function is what transfers from song to song.

### 2. Song reference library
Users can look up how a chord progression maps onto a specific popular song, or register their own songs (custom song entry) to attach a progression to it. A progression stops being abstract the moment it is anchored to a song the user already loves.

### 3. Song analysis mode
The app accepts a song (popular reference or user-registered custom song) and detects the chord progression, key, and Roman-numeral analysis. The output is a teaching artifact: the user sees the analysis *and* the reasoning, not just a transcription dump.

### 4. Sing-along / play-along mode with live chord evaluation
The user plays or sings; the app listens through the device microphone, detects the chords in real time, and evaluates whether each chord fits the expected progression — confirming correct moves and flagging out-of-progression chords as they happen. This closes the loop: hear it, name it, play it, get judged on it.

### Non-goals (v1)
- No music notation editor or sheet-music engraving
- No full multi-track transcription (single harmony focus)
- No social features
- No iOS port until the Android loop is proven

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| App | **Android native (Kotlin + Jetpack Compose)** | Single-platform focus for v1. Compose for fast iteration on the practice UI. |
| Architecture | MVVM + unidirectional data flow | Predictable state for realtime audio flows. |
| Audio DSP / chord detection | **Kotlin + Oboe (C++)** for low-latency mic capture; chord recognition via chromagram (pitch class profile) + template matching or a small on-device model | Chord detection must run on-device, offline-capable. ACR or cloud analysis stays out of the sing-along loop. |
| Song library data | Local Room database + bundled JSON seed | Offline-first. Sync later. |
| Backend (analysis of uploaded songs) | **Cloudflare Workers** (TypeScript) — owner's existing infra pattern | Pay-per-use, zero idle cost, matches the Talentalk/PropInsight deployment pattern. |
| Audio analysis engine (backend) | Python (librosa / madmom or chord-recognition models) invoked via Workers → container | Detection quality lives here; the Worker is the router. |
| Builds/CI | GitHub Actions → APK artifact | Same pipeline style as owner's existing repos. |

### Detection approach, phased
1. **Phase A — deterministic:** chromagram + chord-template matching (well-understood, zero training data, good enough for clean recordings and single-instrument input).
2. **Phase B — ML:** small on-device or Worker-side model for noisy/mixed audio, trained once Phase A generates labeled data from user corrections.

## Repository Layout (planned)

```
app/          # Android app (Kotlin, Compose) — not yet created
engine/       # Chord detection engine (Phase A chromagram first)
worker/       # Cloudflare Worker API (song analysis endpoint)
data/         # Seed progression/song JSON
docs/         # Design decisions, naming log
```

## Naming Log

Name candidates screened so far (full screening trail lives in the project thread, 2026-10-03). Clean survivors, pending decision:

`Kordzy` · `Chordka` · `Tonaltra` · `Solfaria` · `EarDecode` · `ChordCrypt` · `Tunetective` · `Nadaquest`

Screening rule (KLEDO lesson, 2026-09-23): every candidate must pass domain (RDAP), app-store, and web collision checks before it propagates into any deliverable.
