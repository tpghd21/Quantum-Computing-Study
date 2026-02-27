# Quantum Computing Basics — Qubits, Gates, Circuits

Reference: Mermin Ch. 1; Nielsen & Chuang Ch. 4 (Quantum Circuits, Controlled Ops, Universality); supplemented by 2601 Ch. 8–10.

This note covers the circuit model of quantum computation: how gates act on qubits, how circuits compose, and the key identities used to simplify and compile them. The focus is operational — how to read, write, and manipulate quantum circuits.

---

## 1. Classical vs. Quantum: Cbits and Qubits

A **classical bit (Cbit)** lives in $\\{0, 1\\}$. An $n$-Cbit register is in one of $2^n$ definite states.

A **qubit** lives in $\mathbb{C}^2$ with $\vert\psi\rangle = \alpha\_0\vert 0\rangle + \alpha\_1\vert 1\rangle$ and $\vert\alpha\_0\vert^2 + \vert\alpha\_1\vert^2 = 1$. An $n$-qubit register lives in $\mathbb{C}^{2^n}$, able to be in a superposition of all $2^n$ basis states simultaneously.

Classical gates (AND, OR, NOT, XOR) are in general irreversible. Quantum gates are always **unitary** (hence reversible). This is a fundamental constraint of quantum mechanics.

The correspondence: classical reversible gates on $n$ bits $\leftrightarrow$ permutation matrices on $\mathbb{C}^{2^n}$ (a subset of unitaries).

---

## 2. Standard Single-Qubit Gates

### 2.1 The Pauli gates and identity

$$I = \begin{pmatrix} 1 & 0 \\\\ 0 & 1 \end{pmatrix}, \quad X = \begin{pmatrix} 0 & 1 \\\\ 1 & 0 \end{pmatrix}, \quad Y = \begin{pmatrix} 0 & -i \\\\ i & 0 \end{pmatrix}, \quad Z = \begin{pmatrix} 1 & 0 \\\\ 0 & -1 \end{pmatrix}.$$

$X$ is the quantum NOT (bit flip). $Z$ is the phase flip. $Y = iXZ$.

All Paulis satisfy $P^2 = I$ (each is its own inverse) and are both Hermitian and unitary.

### 2.2 The Hadamard gate

$$H = \frac{1}{\sqrt{2}}\begin{pmatrix} 1 & 1 \\\\ 1 & -1 \end{pmatrix}.$$

Creates superposition: $H\vert 0\rangle = \vert +\rangle$, $H\vert 1\rangle = \vert -\rangle$. Satisfies $H^2 = I$.

### 2.3 Phase gates

$$S = \begin{pmatrix} 1 & 0 \\\\ 0 & i \end{pmatrix}, \quad T = \begin{pmatrix} 1 & 0 \\\\ 0 & e^{i\pi/4} \end{pmatrix}.$$

Key relations: $S^2 = Z$, $T^2 = S$, $S^{\dagger} = S^{-1}$, $T^{\dagger} = T^{-1}$.

### 2.4 Rotation gates

$$R_X(\theta) = e^{-i\theta X/2} = \cos\frac{\theta}{2}\,I - i\sin\frac{\theta}{2}\,X = \begin{pmatrix} \cos\frac{\theta}{2} & -i\sin\frac{\theta}{2} \\\\ -i\sin\frac{\theta}{2} & \cos\frac{\theta}{2} \end{pmatrix},$$

$$R_Y(\theta) = e^{-i\theta Y/2} = \begin{pmatrix} \cos\frac{\theta}{2} & -\sin\frac{\theta}{2} \\\\ \sin\frac{\theta}{2} & \cos\frac{\theta}{2} \end{pmatrix}, \quad R_Z(\theta) = e^{-i\theta Z/2} = \begin{pmatrix} e^{-i\theta/2} & 0 \\\\ 0 & e^{i\theta/2} \end{pmatrix}.$$

On the Bloch sphere, $R_\alpha(\theta)$ rotates the Bloch vector by angle $\theta$ about the $\alpha$-axis.

### 2.5 Gate summary table

| Gate | Matrix | Action on $\vert 0\rangle$ | Action on $\vert 1\rangle$ | Key property |
|---|---|---|---|---|
| $X$ | bit flip | $\vert 1\rangle$ | $\vert 0\rangle$ | $X^2 = I$ |
| $Z$ | phase flip | $\vert 0\rangle$ | $-\vert 1\rangle$ | $Z^2 = I$ |
| $H$ | superpose | $\vert +\rangle$ | $\vert -\rangle$ | $H^2 = I$ |
| $S$ | $\pi/2$ phase | $\vert 0\rangle$ | $i\vert 1\rangle$ | $S^2 = Z$ |
| $T$ | $\pi/4$ phase | $\vert 0\rangle$ | $e^{i\pi/4}\vert 1\rangle$ | $T^2 = S$ |

---

## 3. Circuit Model

A quantum circuit is a sequence of gates applied to a register of qubits. Two composition rules connect circuits to linear algebra.

### 3.1 Time order = matrix multiplication

If gate $U$ is applied first, then gate $V$:

$$\vert\psi\rangle \mapsto V(U\vert\psi\rangle) = (VU)\vert\psi\rangle.$$

Matrix product is **right-to-left** when reading gates in time order (left-to-right in the circuit diagram).

### 3.2 Parallel gates = tensor product

If qubit 0 gets gate $A$ and qubit 1 gets gate $B$ simultaneously:

$$U_{\mathrm{total}} = A \otimes B.$$

On product states: $(A \otimes B)(\vert v\rangle \otimes \vert w\rangle) = (A\vert v\rangle) \otimes (B\vert w\rangle)$.

### 3.3 Worked example

Circuit: $H$ on qubit 0, $X$ on qubit 1, applied to $\vert 00\rangle$.

$$(H \otimes X)\vert 00\rangle = (H\vert 0\rangle) \otimes (X\vert 0\rangle) = \vert +\rangle \otimes \vert 1\rangle = \frac{\vert 01\rangle + \vert 11\rangle}{\sqrt{2}}.$$

### 3.4 Circuit equality

Two circuits $C\_1, C\_2$ are equal if their unitary matrices are equal: $U(C\_1) = U(C\_2)$. In algorithmic contexts, we often accept equality up to global phase: $U(C\_1) = e^{i\theta}U(C\_2)$.

---

## 4. Two-Qubit Gates

**Ket convention (little-endian).** We write $\vert q_1 q_0\rangle$ — qubit 0 is the rightmost position. In tensor products, $A \otimes B$ means $A$ on qubit 1 (left) and $B$ on qubit 0 (right).

### 4.1 CNOT (Controlled-NOT)

**Notation.** $C^U\_{ij}$: controlled-$U$ with control qubit $i$, target qubit $j$. CNOT is $C^X\_{01}$ (control = 0, target = 1).

$$C^X_{01}\vert x, y\rangle = \vert x \oplus y,\, y\rangle, \quad x, y \in \\{0, 1\\},$$

where qubit 0 = $y$ (control, right) and qubit 1 = $x$ (target, left). When the control is 1, the target is flipped. It is its own inverse: $(C^X_{01})^2 = I$.

Matrix (basis order $\vert 00\rangle, \vert 01\rangle, \vert 10\rangle, \vert 11\rangle$):

$$C^X_{01} = \begin{pmatrix} 1 & 0 & 0 & 0 \\\\ 0 & 0 & 0 & 1 \\\\ 0 & 0 & 1 & 0 \\\\ 0 & 1 & 0 & 0 \end{pmatrix}.$$

**Decomposition as projector sum:**

$$C^X_{01} = I \otimes \vert 0\rangle\langle 0\vert + X \otimes \vert 1\rangle\langle 1\vert.$$

Read as: project qubit 0 (right factor) onto $\vert 0\rangle$ → do $I$ on qubit 1; onto $\vert 1\rangle$ → do $X$ on qubit 1. This generalizes: $C^U_{01} = I \otimes \vert 0\rangle\langle 0\vert + U \otimes \vert 1\rangle\langle 1\vert$.

**$C^X\_{01}$ in the $\vert\pm\rangle$ basis (N&C Exercise 4.20).** Using $\vert\pm\rangle = (\vert 0\rangle \pm \vert 1\rangle)/\sqrt{2}$, the roles swap: $\vert+\rangle\vert+\rangle \to \vert+\rangle\vert+\rangle$, $\vert-\rangle\vert+\rangle \to \vert-\rangle\vert+\rangle$, $\vert+\rangle\vert-\rangle \to \vert-\rangle\vert-\rangle$, $\vert-\rangle\vert-\rangle \to \vert+\rangle\vert-\rangle$. In this basis, qubit 1 (left) controls a phase flip on qubit 0 (right) — control and target are swapped! This is because $(H \otimes H)\,C^X\_{01}\,(H \otimes H) = C^X\_{10}$, which follows from $HXH = Z$.

### 4.2 CZ (Controlled-Z)

$$C^Z_{01} = \mathrm{diag}(1, 1, 1, -1) = I \otimes \vert 0\rangle\langle 0\vert + Z \otimes \vert 1\rangle\langle 1\vert.$$

$C^Z$ is symmetric: $C^Z_{01} = C^Z_{10}$, so it does not matter which qubit is called control or target.

**Key compilation identity:**

$$(H \otimes I)\,C^X_{01}\,(H \otimes I) = C^Z_{01}.$$

$H$ conjugates qubit 1 (target = left factor), turning its $X$ action into $Z$.

### 4.3 SWAP

$$\mathrm{SWAP}\vert a, b\rangle = \vert b, a\rangle.$$

$$\mathrm{SWAP} = \begin{pmatrix} 1 & 0 & 0 & 0 \\\\ 0 & 0 & 1 & 0 \\\\ 0 & 1 & 0 & 0 \\\\ 0 & 0 & 0 & 1 \end{pmatrix}.$$

SWAP can be decomposed into three controlled-NOTs:

$$\mathrm{SWAP} = C^X_{01} \cdot C^X_{10} \cdot C^X_{01}.$$

This is important for hardware where only nearest-neighbor two-qubit gates are available.

---

## 5. Controlled Gates (General)

A **controlled-$U$** gate applies $U$ to the target qubit if and only if the control qubit is $\vert 1\rangle$:

$$C^U_{01} = I \otimes \vert 0\rangle\langle 0\vert + U \otimes \vert 1\rangle\langle 1\vert.$$

Explicitly for 1-qubit $U = \begin{pmatrix} a & b \\\\ c & d \end{pmatrix}$, with basis order $\vert 00\rangle, \vert 01\rangle, \vert 10\rangle, \vert 11\rangle$:

$$C^U_{01} = \begin{pmatrix} 1 & 0 & 0 & 0 \\\\ 0 & a & 0 & b \\\\ 0 & 0 & 1 & 0 \\\\ 0 & c & 0 & d \end{pmatrix}.$$

$C^X\_{01}$ is the special case $U = X$. $C^Z\_{01}$ is $U = Z$.

The **Toffoli gate** $C^2(X)$ is a 3-qubit gate: two controls, one target. It flips the target only if both controls are 1. It is classically universal for reversible computation.

**Building $C^U$ from $C^X$ (N&C Corollary 4.2).** Any single-qubit $U$ can be decomposed as $U = e^{i\alpha} AXBXC$ where $ABC = I$. This gives an explicit $C^U$ circuit: apply $C$ to target, then $C^X$, then $B$, then $C^X$, then $A$, and a controlled phase $e^{i\alpha}$ on the control. Total cost: 2 $C^X$ gates + 4 single-qubit gates.

---

## 6. Bell State Creation — Worked Example

The circuit $I \otimes H$ ($H$ on qubit 0) followed by $C^X_{01}$ is the standard Bell-state generator.

$$\vert 00\rangle \xrightarrow{I \otimes H} \frac{\vert 00\rangle + \vert 01\rangle}{\sqrt{2}} \xrightarrow{C^X_{01}} \frac{\vert 00\rangle + \vert 11\rangle}{\sqrt{2}} = \vert\Phi^+\rangle.$$

Step by step:
1. $H$ on qubit 0 (right): $\vert 0\rangle_0 \mapsto \vert +\rangle_0 = \frac{1}{\sqrt{2}}(\vert 0\rangle + \vert 1\rangle)$. Joint state: $\frac{1}{\sqrt{2}}(\vert 00\rangle + \vert 01\rangle)$.
2. $C^X_{01}$: $\vert 00\rangle \mapsto \vert 00\rangle$, and $\vert 01\rangle \mapsto \vert 11\rangle$ (control $y=1$ flips target $x$).
3. Result: $\frac{1}{\sqrt{2}}(\vert 00\rangle + \vert 11\rangle)$, a maximally entangled state.

Each qubit individually is maximally mixed ($\rho = I/2$) even though the joint state is pure. This is the hallmark of entanglement.

---

## 7. Circuit Identities

These identities are used constantly for circuit simplification and compilation.

### 7.1 Single-qubit identities

$$H^2 = I, \quad X^2 = Y^2 = Z^2 = I.$$

$$HXH = Z, \quad HZH = X, \quad HYH = -Y.$$

$$S^2 = Z, \quad T^2 = S.$$

### 7.2 Pauli propagation through $C^X_{01}$

Let $C = C^X_{01}$ (control = qubit 0 = right, target = qubit 1 = left). Then:

$$C(I \otimes X)C = X \otimes X, \qquad C(X \otimes I)C = X \otimes I,$$

$$C(I \otimes Z)C = I \otimes Z, \qquad C(Z \otimes I)C = Z \otimes Z.$$

**Mnemonic:** $C^X$ propagates $X$ **forward** from control to target, and $Z$ **backward** from target to control. ($I \otimes X = X\_0$ is on the control; $X \otimes I = X\_1$ is on the target.)

As commuting rules:

$(I \otimes X) \cdot C = C \cdot (X \otimes X)$ and $(Z \otimes I) \cdot C = C \cdot (Z \otimes Z)$,

while $X \otimes I$ and $I \otimes Z$ commute with $C^X_{01}$ unchanged.

### 7.3 Disjoint-support commutation

If gate $A$ acts on qubit set $S$ and gate $B$ acts on disjoint qubit set $T$ ($S \cap T = \varnothing$), then $AB = BA$. This allows parallelization: gates on different wires can execute simultaneously.

---

## 8. Universality

A gate set $\mathcal{G}$ is **universal** if any unitary can be approximated to arbitrary precision by circuits from $\mathcal{G}$.

**Standard universal set:** $\\{H, T, C^X\\}$.

What this means in practice:
- **Clifford gates** (generated by $H, S, C^X$) have rich algebraic structure but are efficiently classically simulable (Gottesman–Knill theorem). They are not universal alone.
- Adding the **$T$ gate** supplies the non-Clifford resource needed for universality.

**Euler decomposition (N&C Theorem 4.1, Z-Y decomposition).** Any single-qubit unitary $U$ can be written as $U = e^{i\alpha} R_Z(\beta)\,R_Y(\gamma)\,R_Z(\delta)$ for real $\alpha, \beta, \gamma, \delta$. This means two rotation axes suffice to reach any point on the Bloch sphere.

**N&C universality hierarchy (Ch. 4.5).** The universality proof proceeds in three steps:
1. Any $n$-qubit unitary → product of **two-level unitaries** (act non-trivially on only two basis states).
2. Any two-level unitary → single-qubit gates + $C^X$.
3. Arbitrary single-qubit gates → **approximated** by a discrete set like $\\{H, T\\}$.

**Solovay-Kitaev theorem.** Approximation to accuracy $\varepsilon$ costs only $O(\log^c(1/\varepsilon))$ gates from a universal discrete set — polylogarithmic overhead.

**Compilation** is the constructive task: given a target unitary $U$, produce a circuit over the hardware gate set with acceptable approximation error, depth, and two-qubit gate count.

---

## 9. Circuit Depth and Hardware Constraints

### 9.1 Depth vs. gate count

**Circuit depth** is the minimum number of time steps when gates on disjoint wires execute in parallel. Two circuits can have the same gate count but different depth. Depth matters because noise accumulates with time.

### 9.2 Connectivity constraints

Real hardware has a **connectivity graph** $G = (V, E)$: two-qubit gates can only act on edges $(i, j) \in E$. If a circuit needs a gate between non-adjacent qubits, the compiler inserts SWAP networks, increasing depth.

### 9.3 Why simplification matters

Circuit simplification is not aesthetic — it is an **error mitigation** step. Fewer gates (especially fewer two-qubit gates) means less accumulated noise, shorter circuit time, and higher success probability.

---

## 10. Measurement in Circuits

Measurement in the computational basis produces a classical bit from each qubit. In Qiskit notation, the measurement result goes to a classical register.

For $n$ qubits in state $\vert\psi\rangle = \sum_x \alpha_x \vert x\rangle$:
- Outcome $x$ with probability $p(x) = \vert\alpha_x\vert^2$ (Born rule).
- Post-measurement state: $\vert x\rangle$.
- Measurement is irreversible.

An $n$-qubit measurement in the computational basis can be realized by applying 1-qubit measurements to each of the $n$ qubits individually.

**Mid-circuit measurement** creates a hybrid quantum–classical dataflow: the measurement outcome (a classical bit) can be used to condition subsequent gates.

---

## 11. Quick Reference

| Concept | Rule |
|---|---|
| Ket convention | $\vert q_1 q_0\rangle$ (qubit 0 = rightmost); $A \otimes B$: $A$ on q1, $B$ on q0 |
| Time order | $V$ after $U$ $\implies$ matrix $VU$ (right-to-left) |
| Parallel gates | $A$ on qubit 1, $B$ on qubit 0 $\implies$ $A \otimes B$ |
| $C^X_{01}$ (CNOT) | $\vert x,y\rangle \mapsto \vert x \oplus y, y\rangle$ (ctrl=q0=$y$, tgt=q1=$x$) |
| $C^X$ in $\vert\pm\rangle$ basis | Control/target swap: $(H \otimes H) C^X_{01} (H \otimes H) = C^X_{10}$ |
| $C^Z$ | $(H \otimes I)\,C^X\_{01}\,(H \otimes I) = C^Z\_{01}$; symmetric ($C^Z\_{01} = C^Z\_{10}$) |
| $C^U$ (general) | $C^U_{01} = I \otimes \vert 0\rangle\langle 0\vert + U \otimes \vert 1\rangle\langle 1\vert$ |
| $C^U$ decomposition | $U = e^{i\alpha}AXBXC$, $ABC=I$ → 2 $C^X$ gates (N&C) |
| SWAP | $C^X_{01} \cdot C^X_{10} \cdot C^X_{01}$ |
| $X\_0$ through $C^X\_{01}$ | $C(I \otimes X)C = X \otimes X$ (propagates to target) |
| $Z\_1$ through $C^X\_{01}$ | $C(Z \otimes I)C = Z \otimes Z$ (propagates back to control) |
| Universal set | $\\{H, T, C^X\\}$ |
| Euler / Z-Y decomp | $U = e^{i\alpha} R_Z(\beta)\,R_Y(\gamma)\,R_Z(\delta)$ (N&C Thm 4.1) |
| Universality hierarchy | Two-level → single+$C^X$ → discrete (N&C 4.5) |
| Solovay-Kitaev | $O(\log^c(1/\varepsilon))$ gates for $\varepsilon$-approximation |
| Circuit depth | Min time steps with parallel execution |

---

[← Back to README](../README.md)
