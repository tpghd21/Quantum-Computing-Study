# Quantum Computing Study

A structured, ground-up study of quantum computing — from linear algebra through quantum algorithms to variational methods. Every topic is organized as theory → implementation → validation.

---

## Topics

- [Linear algebra review](00-linear-algebra/notes.md)
- [Quantum mechanics basics](01-quantum-mechanics/notes.md)
- Quantum computing basics — qubits, gates, circuits (TBD)
- Quantum protocols — QKD, dense coding, teleportation (TBD)
- Quantum algorithms — Deutsch, Bernstein–Vazirani, Grover, Simon, QFT / QPE / Shor (TBD)
- Quantum error correction (TBD)
- VQE (TBD)
- QAOA (TBD)
- Quantum Monte Carlo (TBD)

---

## How Each Algorithm Is Organized

Every algorithm folder follows a consistent 3-file structure:

```
deutsch/
├── theory.md             ← Derivation, circuit, complexity (from Mermin)
├── implementation.ipynb  ← Qiskit circuit + simulation (or equivalent .py)
└── validation.md         ← Correctness tests, shot statistics, reproducibility
```

The point of `validation.md` is to treat each algorithm not as a textbook exercise but as something that needs to be *tested*. Shots are not the state — validation prevents fooling ourselves with noisy outputs.

Each validation includes:

- **Known-answer tests** — brute-force / statevector comparison at small $n$
- **Shot-scaling behavior** — $1/\sqrt{N}$ convergence check
- **Fixed-seed reproducibility** — deterministic results across runs
- **Resource counts** — depth / 2Q gates

---

## Repository Structure

```
quantum-computing-study/
├── README.md
├── TEMPLATE.md
├── requirements.txt
│
├── 00-linear-algebra/
│   └── notes.md
├── 01-quantum-mechanics/
│   └── notes.md
├── 02-quantum-computing-basics/
│   └── notes.md
├── 03-quantum-protocols/
│   ├── notes.md
│   ├── qkd/
│   ├── dense-coding/
│   └── teleportation/
├── 04-quantum-algorithms/
│   ├── notes.md
│   ├── deutsch/
│   ├── bernstein-vazirani/
│   ├── simon/
│   ├── grover/
│   ├── qft/
│   ├── qpe/
│   └── shor/
├── 05-quantum-error-correction/      ← planned
├── 06-variational-algorithms/        ← planned
└── 07-quantum-monte-carlo/           ← planned
```

---

## Notation Conventions

| Symbol | Meaning |
|--------|---------|
| $\vert 0\rangle, \vert 1\rangle$ | Computational basis states (column vectors) |
| $\vert +\rangle, \vert -\rangle$ | Hadamard basis: $\frac{1}{\sqrt{2}}(\vert 0\rangle \pm \vert 1\rangle)$ |
| $\otimes$ | Tensor product |
| $\oplus$ | Modulo-2 (XOR) addition |
| $H, X, Y, Z$ | Hadamard and Pauli gates |
| CNOT | Controlled-NOT (control first, target second) |

**Qubit ordering**: Big-endian — $q_0$ is most significant.  
**Repo-wide convention**: Bitstrings are reported as $q_0 \dots q_{n-1}$ (left-to-right); Qiskit raw strings are reindexed when displayed.  
**Qiskit note**: Qiskit's output bitstring is ordered by classical register index (rightmost bit = `c[0]`). Implementation notebooks include an explicit bit-order sanity check where the two conventions diverge.  
**Controlled gates**: $C^U_{ct}$ — control $c$, target $t$.  
**Global phase**: Ignored unless explicitly relevant.  
**Measurement**: Computational basis unless stated otherwise. Shot data is a *sample* from the Born distribution, not the state itself.

---

## Two-Source Strategy

The study draws from two complementary sources:

| Mermin (primary textbook) | Supplementary (2601 + literature) |
|---|---|
| Qubit / gate / circuit basics (Ch. 1) | "Circuit is a pipeline" perspective |
| Entanglement, Bell states | Streaming measurement → decision |
| Deutsch / BV / Simon / Grover (Ch. 2–4) | validation.md: correctness test + shot variance |
| QFT / QPE / Shor (Ch. 3) | Phase estimation error/shot-count analysis |
| BB84 / dense coding / teleportation (Ch. 6) | Protocol as FSM |
| QEC introduction (Ch. 5) | Decoder contract + tail latency |
| — | VQE / QAOA / QNG / QFIM |
| — | Quantum Monte Carlo |

---

## Quickstart

Requires Python 3.11+.

```bash
pip install -r requirements.txt
jupyter lab
```

If Aer installation fails, see [Qiskit Aer install notes](https://qiskit.github.io/qiskit-aer/getting_started.html) for your platform.

---

## Tech Stack

- **Theory**: Mermin textbook + supplementary notes → Markdown
- **Implementation**: Python 3.11+, Qiskit 1.x
- **Simulation**: Qiskit Aer (statevector + shot-based)
- **Visualization**: Matplotlib

---

## References

- Mermin, N. D. *Quantum Computer Science: An Introduction*. Cambridge University Press, 2007.
- Nielsen, M. A. and Chuang, I. L. *Quantum Computation and Quantum Information*. Cambridge University Press, 2010.
- IBM Qiskit Textbook (API usage and circuit-construction patterns; results are re-implemented and validated independently in this repo).

### Supplementary reading (planned)

- Cho, G. *Geometry- and Topology-Informed QC: From States to Real-Time Control with FPGA Prototypes*. [arXiv:2601.09556](https://arxiv.org/abs/2601.09556), 2026.
