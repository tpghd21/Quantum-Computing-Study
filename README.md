# Quantum Computing Study

A structured, ground-up study of quantum computing — from linear algebra through quantum error correction.  
Each topic is organized as **theory → implementation → validation**, using Qiskit for all circuit implementations.

---

## Topics

| # | Topic | Theory | Implementation |
|---|-------|--------|----------------|
| 00 | Linear algebra review | [notes.md](00-linear-algebra/notes.md) | — |
| 01 | Quantum mechanics basics | [notes.md](01-quantum-mechanics/notes.md) | — |
| 02 | Quantum computing basics — qubits, gates, circuits | [notes.md](02-quantum-computing-basics/notes.md) | TBD |
| 03 | Quantum protocols — Bell states, dense coding, teleportation | [notes.md](03-quantum-protocols/notes.md) | [notebook](03-quantum-protocols/implementation.ipynb) |
| 04 | Quantum algorithms — Deutsch, BV, Simon, Grover, QFT / QPE / Shor | [notes.md](04-quantum-algorithms/notes.md) | TBD |
| 05 | Quantum error correction | TBD | TBD |

---

## How Each Algorithm Is Organized

Every algorithm folder follows a consistent 3-file structure:

```
grover/
├── theory.md             ← Derivation, circuit, complexity
├── implementation.ipynb  ← Qiskit circuit + Aer simulation
└── validation.md         ← Correctness tests, shot statistics, reproducibility
```

Each `validation.md` includes:

- **Known-answer tests** — statevector comparison at small $n$
- **Shot-scaling behavior** — $1/\sqrt{N}$ convergence check
- **Fixed-seed reproducibility** — deterministic results across runs
- **Resource counts** — circuit depth and 2-qubit gate count

---

## Repository Structure

```
quantum-computing-study/
├── README.md
├── requirements.txt
│
├── 00-linear-algebra/
│   └── notes.md
│
├── 01-quantum-mechanics/
│   └── notes.md
│
├── 02-quantum-computing-basics/
│   └── notes.md
│
├── 03-quantum-protocols/
│   ├── notes.md
│   └── implementation.ipynb
│
├── 04-quantum-algorithms/
│   ├── notes.md
│   ├── deutsch/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   ├── bernstein-vazirani/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   ├── simon/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   ├── grover/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   ├── qft/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   ├── qpe/
│   │   ├── theory.md
│   │   ├── implementation.ipynb
│   │   └── validation.md
│   └── shor/
│       ├── theory.md
│       ├── implementation.ipynb
│       └── validation.md
│
└── 05-quantum-error-correction/
    └── notes.md
```

---

## Notation Conventions

| Symbol | Meaning |
|--------|---------|
| $\vert 0\rangle, \vert 1\rangle$ | Computational basis states |
| $\vert +\rangle, \vert -\rangle$ | Hadamard basis: $\frac{1}{\sqrt{2}}(\vert 0\rangle \pm \vert 1\rangle)$ |
| $\otimes$ | Tensor product |
| $\oplus$ | XOR (modulo-2 addition) |
| $H, X, Y, Z$ | Hadamard and Pauli gates |
| CNOT | Controlled-NOT (control first, target second) |

**Qubit ordering**: Big-endian throughout — $q_0$ is the most significant bit.  
**Qiskit note**: Qiskit's output bitstring is little-endian (rightmost bit = `c[0]`). Notebooks include an explicit bit-order conversion wherever the two conventions diverge.  
**Measurement**: Computational basis unless stated otherwise.  
**Global phase**: Ignored unless explicitly relevant.

---

## Tech Stack

- **Theory**: Mermin + Nielsen–Chuang → Markdown notes
- **Implementation**: Python 3.11+, Qiskit 1.x
- **Simulation**: Qiskit Aer (statevector + shot-based)
- **Visualization**: Matplotlib

---

## References

- Mermin, N. D. *Quantum Computer Science: An Introduction*. Cambridge University Press, 2007.
- Nielsen, M. A. and Chuang, I. L. *Quantum Computation and Quantum Information*. Cambridge University Press, 2010.
