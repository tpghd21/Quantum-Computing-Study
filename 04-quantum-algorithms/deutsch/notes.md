# Deutsch's Algorithm (Mermin Ch. 2)

> **Qubit ordering convention**: $|q_1 q_0\rangle$ — $q_0$ is the rightmost bit (LSB).
> Tensor product $A \otimes B$: $A$ acts on $q_1$, $B$ acts on $q_0$. Qiskit little-endian convention.
>
> **In this algorithm**: $q_1$ = input, $q_0$ = ancilla.

---

## Learning Goals
- State the Deutsch problem precisely (constant vs. balanced)
- Derive the circuit step by step
- Explain why 1 quantum query suffices where 2 classical queries are needed
- Trace the role of phase kickback in extracting global information

## Key Definitions & Notation

| Term | Meaning |
|------|---------|
| $f:\{0,1\} \to \{0,1\}$ | Boolean function on 1 bit |
| Constant | $f(0) = f(1)$ (both 0 or both 1) |
| Balanced | $f(0) \neq f(1)$ |
| $U_f$ | Oracle: $U_f\|q_1\rangle\|q_0\rangle = \|q_1\rangle\|q_0 \oplus f(q_1)\rangle$ |

**Oracle convention**: $U_f|x\rangle_1|y\rangle_0 = |x\rangle_1|y \oplus f(x)\rangle_0$ — input $x$ on $q_1$, ancilla $y$ on $q_0$. Oracle flips $q_0$ conditionally.

---

## The Problem

There are exactly 4 possible functions $f_j : \{0,1\} \to \{0,1\}$:

| $j$ | $f_j(0)$ | $f_j(1)$ | Type |
|-----|----------|----------|------|
| 0 | 0 | 0 | Constant |
| 1 | 0 | 1 | Balanced |
| 2 | 1 | 0 | Balanced |
| 3 | 1 | 1 | Constant |

**Task**: determine whether $f$ is constant or balanced.

**Classical**: must evaluate $f(0)$ AND $f(1)$ → **2 queries**.
**Quantum**: **1 query** suffices.

---

## Main Derivation

### Circuit

```
q₁: ─|0⟩── H ─── ┌────┐ ─── H ─── [measure] → result
                   │ Uf │
q₀: ─|1⟩── H ─── └────┘ ───────── (discarded)

(|q₁q₀⟩ ordering: q₁ = input, q₀ = ancilla)
```

### Step-by-step

**Step 0**: Initial state
$$|q_1 q_0\rangle = |0\rangle_1|1\rangle_0 = |01\rangle$$

**Step 1**: Apply $H_1 \otimes H_0$ (Hadamard on both qubits)
$$\frac{1}{\sqrt{2}}(|0\rangle + |1\rangle)_1 \otimes \frac{1}{\sqrt{2}}(|0\rangle - |1\rangle)_0 = |+\rangle_1|-\rangle_0$$

**Step 2**: Apply $U_f$

Key trick — **phase kickback**: when $q_0$ is in $|-\rangle$:
$$U_f|x\rangle_1|-\rangle_0 = (-1)^{f(x)}|x\rangle_1|-\rangle_0$$

So after $U_f$:
$$\frac{1}{\sqrt{2}}\left((-1)^{f(0)}|0\rangle + (-1)^{f(1)}|1\rangle\right)_1 \otimes |-\rangle_0$$

**Step 3**: Apply $H$ to $q_1$ (the input qubit)

Factor out $(-1)^{f(0)}$:
$$(-1)^{f(0)} \cdot \frac{1}{\sqrt{2}}\left(|0\rangle + (-1)^{f(0) \oplus f(1)}|1\rangle\right)_1$$

After $H_1$:
- If $f(0) \oplus f(1) = 0$ (constant): state is $\pm|0\rangle_1$ → **measure 0**
- If $f(0) \oplus f(1) = 1$ (balanced): state is $\pm|1\rangle_1$ → **measure 1**

**Step 4**: Measure $q_1$
$$
\text{Result} = f(0) \oplus f(1) = \begin{cases}
0 & \text{constant} \\
1 & \text{balanced}
\end{cases}
$$
---

## Why It Works: Phase Kickback

The ancilla $q_0$ in $|-\rangle$ is an eigenstate of $X$ with eigenvalue $-1$. When $f(x) = 1$, the oracle applies $X$ to $q_0$, which kicks back a phase $(-1)$ onto the input register $q_1$.

This converts function value information (stored in $q_0$) into phase information on $q_1$ (which $H$ can extract).

---

## Complexity Analysis

### What is Query Complexity?

In this context, "complexity" means **query complexity**: treating $f$ as a black box (oracle), we count **how many times we must call $f$** to solve the problem. We ignore internal gate counts or time complexity — only oracle calls matter.

### Classical: Why 2 queries is optimal

**Deterministic**: We have zero prior information about $f$. After seeing only $f(0)$, we cannot distinguish constant from balanced:
- If $f(0) = 0$: could be $f_0$ (constant) or $f_1$ (balanced).
- If $f(0) = 1$: could be $f_2$ (balanced) or $f_3$ (constant).

In either case, one query leaves two possibilities alive. We need **both** $f(0)$ and $f(1)$ to determine $f(0) \oplus f(1)$, so **2 is a tight lower bound**.

**Probabilistic (bounded-error)**: Even with randomized strategies, a single query gives success probability no better than $1/2$ (equivalent to guessing). Certainty requires 2 queries.

### Quantum: Why 1 query suffices

Phase kickback is the key. A single oracle call encodes **global information** into relative phases: $(-1)^{f(0)}|0\rangle + (-1)^{f(1)}|1\rangle$. The Hadamard gate converts this phase difference into a computational basis distinction.

Classically, one query returns the value of $f(0)$ **or** $f(1)$ — a single local fact. Quantum superposition + interference extracts the **global property** $f(0) \oplus f(1)$ directly.

### Summary

| | Classical (deterministic) | Classical (randomized) | Quantum |
|---|--------------------------|----------------------|---------|
| Query complexity | $2$ (tight) | $2$ (for certainty) | $\mathbf{1}$ (exact) |
| Speedup | — | — | 2× (constant factor) |

The constant-factor speedup has limited practical significance, but Deutsch's algorithm is the **first proof** that quantum interference can extract information that is classically inaccessible in fewer queries.

---

## Worked Example

Let $f(x) = x$ (balanced, $f_1$).

1. $|q_1 q_0\rangle = |01\rangle$
2. $H^{\otimes 2}$: $|+\rangle_1|-\rangle_0$
3. $U_f$: $(-1)^0|0\rangle_1|-\rangle_0 + (-1)^1|1\rangle_1|-\rangle_0 = \frac{1}{\sqrt{2}}(|0\rangle - |1\rangle)_1|-\rangle_0 = |-\rangle_1|-\rangle_0$
4. $H$ on $q_1$: $|1\rangle_1$
5. Measure $q_1$: **1** → balanced ✓

---

## Qiskit Implementation Note

In Qiskit, `QuantumCircuit(2)` creates q[0] and q[1]:
- `q[0]` = $q_0$ (ancilla, bottom wire, LSB)
- `q[1]` = $q_1$ (input, top wire)
- `qc.x(0)` prepares ancilla $q_0$ to $|1\rangle$
- `qc.h([0, 1])` applies Hadamard to both
- Oracle: `qc.cx(1, 0)` for $f(x) = x$ (CNOT with $q_1$ = control, $q_0$ = target)
- `qc.h(1)` then `qc.measure(1, 0)` measures the input qubit

---

## Common Mistakes & Pitfalls
- **Phase kickback is not obvious**: $q_0$ "does nothing" visually, but it's essential for the phase to appear on $q_1$
- **Global phase is irrelevant**: the $(-1)^{f(0)}$ factor disappears on measurement
- **This is NOT parallel evaluation**: the quantum computer doesn't "evaluate $f(0)$ and $f(1)$ simultaneously" — it extracts $f(0) \oplus f(1)$ via interference

---

## References
- Mermin, Ch. 2 (Deutsch problem)
- Deutsch, D. Proc. R. Soc. Lond. A 400 (1985)
