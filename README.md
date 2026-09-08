# Quantum Randomness → Music

**Can a quantum measurement become a melody?**  
This project implements a complete pipeline that transforms measurement outcomes from a quantum circuit into a musical composition. The source of randomness is a set of qubits in superposition, measured to produce classical bits that are then mapped to notes, durations, and dynamics.

## Overview

- **Quantum Circuit**: `NUM_QUBITS` qubits each prepared with a Hadamard gate and measured.
- **Bitstream**: Each measurement yields a bitstring; repeated shots generate a list of bitstrings.
- **Musical Mapping**: Each bitstring is parsed into:
  - **Rest** (1 bit)
  - **Scale degree** (3 bits → 0–6)
  - **Octave** (2 bits → 0–3)
  - **Duration** (2 bits → one of four lengths)
  - **Velocity** (3 bits → 0–7 scaled to MIDI velocity)
- **Output**: A melody rendered as MIDI and optionally as audio (WAV) via FluidSynth.

All randomness originates from the quantum circuit (simulated), not from Python's `random` module (except in the optional comparison section).

## Motivation

This experiment is part of the **Schrödinger's Gambit** project, which explores creative applications of quantum computing. By sonifying quantum measurement outcomes, we make abstract quantum behaviour perceptible and show that quantum processes can be a source of stochastic variation for art.

## Scientific Concept

- **Superposition**: A qubit in |+⟩ = (|0⟩+|1⟩)/√2 gives 0 or 1 with equal probability upon measurement.
- **Hadamard Gate**: Creates the equal superposition.
- **Measurement**: Collapses the state to a classical bit.
- **Simulated vs Hardware**: The notebook uses a simulator (Qiskit Aer) by default; running on real hardware is optional.

## Installation

### Google Colab

1. Open [Google Colab](https://colab.research.google.com/).
2. Upload the notebook `quantum_randomness_music.ipynb` or open it from GitHub.
3. Run the installation cell (Cell 5) – it installs all required packages.
4. Execute the cells sequentially.

### Local (if desired)

- Install Python 3.8+ and the packages listed in Cell 5.
- Install FluidSynth (`sudo apt install fluidsynth`) for audio conversion.
- Run the notebook in Jupyter.

## How to Run

1. Configure parameters in **Cell 7** (quantum bits, tempo, scale, etc.).
2. Run all cells from top to bottom.
3. The primary melody is saved as `outputs/quantum_melody_01.mid` and `outputs/quantum_melody_01.wav`.
4. Additional melodies are saved as `quantum_melody_02.mid`, etc.
5. A classical comparison melody is saved as `classical_melody.mid`.

## Output Files

All generated files are placed in the `outputs/` directory:

- `quantum_melody_01.mid` – primary MIDI
- `quantum_melody_01.wav` – audio (if rendering succeeded)
- `quantum_melody_02.mid`, ... – additional melodies
- `classical_melody.mid` – comparison melody

## Simulator vs Real Hardware

- **Simulator**: Uses Qiskit Aer to perform exact calculations of the ideal circuit. Fast, reproducible (with a seed), and free.
- **Real Hardware**: To use an IBM Quantum device, replace the backend with `IBMQ.get_backend('ibmq_...)` after authenticating. Measurement outcomes will include noise, which can add an extra layer of variation.

## Limitations

- The quantum randomness is simulated; no physical quantum device is required.
- The number of notes is limited by `SHOTS` (default 32).
- The mapping from bits to music is arbitrary and can be changed.
- No quantum advantage or superior musical quality is claimed.
- The audio rendering depends on FluidSynth (works in Colab after installation).

## Future Extensions

- Entanglement to correlate voices.
- Quantum walks for melodic contours.
- Real‑time hardware integration.
- Use of quantum optimisation algorithms to shape musical structure.

## Project Context

This notebook is part of [Schrödinger's Gambit](https://schrodingersgambit.com) an open‑source collection of quantum‑inspired creative experiments. It demonstrates a principled way to use quantum measurement outcomes as a stochastic seed for art, while maintaining scientific integrity and educational clarity.
