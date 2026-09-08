# Quantum Randomness → Music

**A Schrödinger's Gambit experiment.**

## 1. Overview

This experiment builds a quantum circuit, measures it on a quantum simulator, and uses the resulting measurement outcomes as the stochastic input to a deterministic musical composition algorithm. The output is a MIDI file (and, when possible, rendered audio) that you can inspect, play, and reason about, note by note, all the way back to the quantum bits that produced it.

This is **not** a random-music generator that merely mentions quantum computing. The bitstream driving the primary melody is produced by an actual quantum circuit executed on `qiskit-aer`'s `AerSimulator`. Everything downstream — the note mapping, the MIDI encoding, the audio rendering — is a documented, deterministic classical algorithm.

## 2. Motivation

Quantum measurement produces genuinely discrete, non-deterministic-in-principle outcomes (in the idealized, noise-free case they arise from fundamental quantum probability, not from a classical pseudorandom algorithm). This project asks a simple question: can those outcomes be used as a meaningful, inspectable stochastic input to a creative process, in a way that is scientifically honest about what is and is not quantum?

## 3. Scientific Concept

A qubit starting in |0⟩, after a Hadamard gate, is in the superposition:

H|0⟩ = (|0⟩ + |1⟩) / √2

Measuring this qubit yields 0 or 1 with probability 0.5 each, in the ideal case. Repeating this over many qubits and many circuit executions produces a stream of classical bits. Once measured, these bits are ordinary classical data — nothing about them remains quantum after collapse. This notebook is explicit about that boundary throughout.

## 4. Quantum Circuit

The circuit is deliberately minimal:

|0> ── H ── Measure
|0> ── H ── Measure
...


`NUM_QUBITS` qubits are each placed into superposition and measured in a single circuit execution (one "shot"). The circuit contains no entangling gates; each qubit is measured independently by design, which keeps the source of randomness easy to reason about.

## 5. Measurement Process

The circuit is executed on `AerSimulator` with `memory=True`, which returns the individual outcome of every shot (not just aggregated counts). Each shot yields one `NUM_QUBITS`-bit string. Multiple shots are concatenated to build a bitstream of any required length. This is the only stage in the whole pipeline that is quantum.

## 6. Bitstream Generation

`generate_quantum_bits(num_bits, num_qubits)`:

1. Computes how many shots are needed to cover `num_bits`.
2. Builds and transpiles the Hadamard circuit.
3. Executes it with `memory=True`.
4. Concatenates the per-shot bitstrings and trims to exactly `num_bits`.
5. Returns the bitstream plus metadata (shots executed, backend used, etc.) for transparency.

If quantum bit generation fails for any reason, the notebook raises a clear error. It never silently substitutes classical randomness.

## 7. Musical Mapping

Every note consumes exactly 12 quantum-measured bits, split as follows:

| Bits | Count | Range | Parameter |
|---|---|---|---|
| 0–2   | 3 | 0–7 | Scale degree (wraps automatically into extra octaves) |
| 3–4   | 2 | 0–3 | Octave offset |
| 5–6   | 2 | 0–3 | Duration (index into a fixed duration list) |
| 7–9   | 3 | 0–7 | Velocity (scaled into a configured range) |
| 10–11 | 2 | 0–3 | Rest flag |

This mapping lives in one place — the `BIT_ALLOCATION` dictionary and the `quantum_bits_to_music_parameters` function — so the code and this table cannot drift apart. The scale-degree wrap rule (`degree % len(scale)`, with `degree // len(scale)` extra octaves) works for scales of any length, from pentatonic (5 notes) to chromatic (12 notes).

## 8. Musical Constraints

To keep the output musically coherent rather than arbitrary:

- A single configurable scale and root note are used for the whole melody (default: C major).
- Octave range is limited and configurable (`OCTAVE_BASE`, `OCTAVE_SPAN`).
- Durations are drawn from a small, fixed, musically sensible set (`NOTE_DURATION_OPTIONS`).
- Velocity is bounded to a configured, non-extreme range.
- Rests occur at a controlled, approximate rate (`REST_THRESHOLD_VALUE`), not on every note.

## 9. Installation

Run this in the first code cell of the notebook (also embedded there):

```bash
pip install -q "qiskit>=1.0.0" "qiskit-aer>=0.13.0" pylatexenc midiutil
apt-get -qq update
apt-get -qq install -y fluidsynth fluid-soundfont-gm

```

## Possible Extensions

- Quantum-derived harmony: use additional qubits to select chords rather than single notes.
- Multiple quantum registers driving multiple simultaneous instruments or voices.
- Entangled qubits producing deliberately correlated melodic voices.
- Parameterized quantum gates used as controllable musical transformation knobs.
- Quantum walks used to drive melodic contour or movement over time.
- QAOA-style optimization applied to rhythmic pattern selection.
- VQE-style optimization applied to musical parameter tuning.
- Real QPU execution for a live, hardware-driven composition session.
- Deliberately using hardware noise as a controlled source of musical texture.
- Quantum-randomness-driven generative ambient or background music.
- Synchronized quantum-driven audio and visual generation.

These are not implemented in this notebook; they are documented here as natural next steps for the [Schrödinger's Gambit](www.schrodingersgambit.com) project.
