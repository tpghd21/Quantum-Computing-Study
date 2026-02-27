# Quantum Mechanics Basics

Reference: Mermin Ch. 1 & Appendix B; Nielsen & Chuang Ch. 2 (Density Operator, Schmidt, Partial Trace, Purification); supplemented by 2601 Ch. 5, 9.

This note collects the quantum-mechanical rules needed before we discuss circuits and algorithms. The goal is operational: what is a state, how does it evolve, and what happens when we measure.

---

## 1. States and the Qubit

A **classical bit** (Cbit) has two states: $0$ or $1$.

A **qubit** (quantum bit) can be in any linear superposition of $\vert 0\rangle$ and $\vert 1\rangle$:

$$\vert \psi \rangle = \alpha_0 \vert 0\rangle + \alpha_1 \vert 1\rangle = \begin{pmatrix} \alpha_0 \\\\ \alpha_1 \end{pmatrix}, \quad \alpha_0, \alpha_1 \in \mathbb{C}.$$

The amplitudes satisfy the **normalization constraint**:

$$\langle \psi \vert \psi \rangle = \vert \alpha_0 \vert^{2} + \vert \alpha_1 \vert^{2} = 1.$$

For $n$ qubits, the state lives in $\mathbb{C}^{2^n}$:

$$\vert \psi \rangle_n = \sum_{x=0}^{2^n - 1} \alpha_x \vert x \rangle_n, \quad \sum_x \vert \alpha_x \vert^{2} = 1.$$

The numbers $\alpha_x$ are **probability amplitudes**. They are complex numbers, not probabilities.

---

## 2. Global Phase and Projective Equivalence

**Global phase invariance.** The states $\vert \psi \rangle$ and $e^{i\theta}\vert \psi \rangle$ are physically identical for any $\theta \in \mathbb{R}$.

*Proof.* For any measurement basis $\\{\vert x \rangle\\}$,

$$\vert \langle x \vert e^{i\theta} \psi \rangle \vert^{2} = \vert e^{i\theta} \vert^{2} \vert \langle x \vert \psi \rangle \vert^{2} = \vert \langle x \vert \psi \rangle \vert^{2}.$$

All measurement statistics agree, so no experiment can distinguish the two. $\square$

**Relative phase matters.** While global phase is unobservable, the *relative* phase between components affects future interference and hence future measurement probabilities after additional gates.

**Degrees of freedom.** A qubit state $(\alpha_0, \alpha_1) \in \mathbb{C}^2$ has 4 real parameters. Normalization removes 1 and global phase removes 1, leaving **2 real degrees of freedom**. This matches the surface of a sphere ($S^2$).

The true state space of pure qubit states is the **complex projective line** $\mathbb{C}P^1$, which is diffeomorphic to $S^2$ (the Bloch sphere).

---

## 3. The Bloch Sphere

Every pure qubit state can be written (up to global phase) as

$$\vert \psi(\theta, \phi) \rangle = \cos\frac{\theta}{2}\vert 0\rangle + e^{i\phi}\sin\frac{\theta}{2}\vert 1\rangle$$

where $\theta \in [0, \pi]$ and $\phi \in [0, 2\pi)$.

This state corresponds to a unique point on the **Bloch sphere** with Bloch vector

$$\vec{r} = (\sin\theta\cos\phi, \, \sin\theta\sin\phi, \, \cos\theta).$$

Key points on the sphere:

| State | $\theta$ | $\phi$ | Bloch vector |
|---|---|---|---|
| $\vert 0\rangle$ | $0$ | — | $(0, 0, 1)$ |
| $\vert 1\rangle$ | $\pi$ | — | $(0, 0, -1)$ |
| $\vert +\rangle = \frac{1}{\sqrt{2}}(\vert 0\rangle + \vert 1\rangle)$ | $\pi/2$ | $0$ | $(1, 0, 0)$ |
| $\vert -\rangle = \frac{1}{\sqrt{2}}(\vert 0\rangle - \vert 1\rangle)$ | $\pi/2$ | $\pi$ | $(-1, 0, 0)$ |
| $\frac{1}{\sqrt{2}}(\vert 0\rangle + i\vert 1\rangle)$ | $\pi/2$ | $\pi/2$ | $(0, 1, 0)$ |
| $\frac{1}{\sqrt{2}}(\vert 0\rangle - i\vert 1\rangle)$ | $\pi/2$ | $3\pi/2$ | $(0, -1, 0)$ |

**Density matrix form.** Any qubit state (pure or mixed) can be written as

$$\rho = \frac{1}{2}(I + \vec{r} \cdot \vec{\sigma})$$

where $\vec{\sigma} = (X, Y, Z)$ are the Pauli matrices. Pure states satisfy $\Vert \vec{r} \Vert = 1$ (surface); mixed states have $\Vert \vec{r} \Vert < 1$ (interior of the Bloch ball).

**Operational meaning of the Bloch vector.** Each coordinate is an expectation value:

$$x = \mathrm{Tr}(\rho X), \quad y = \mathrm{Tr}(\rho Y), \quad z = \mathrm{Tr}(\rho Z).$$

---

## 4. Pauli Matrices

$$X = \begin{pmatrix} 0 & 1 \\\\ 1 & 0 \end{pmatrix}, \quad Y = \begin{pmatrix} 0 & -i \\\\ i & 0 \end{pmatrix}, \quad Z = \begin{pmatrix} 1 & 0 \\\\ 0 & -1 \end{pmatrix}.$$

Key properties:

$$X^2 = Y^2 = Z^2 = I, \quad \mathrm{Tr}(X) = \mathrm{Tr}(Y) = \mathrm{Tr}(Z) = 0.$$

Each Pauli is both Hermitian ($P^{\dagger} = P$) and unitary ($P^{\dagger}P = I$), with eigenvalues $\pm 1$.

**Anticommutation.** For distinct Paulis $\sigma_i, \sigma_j$:

$$\sigma_i \sigma_j + \sigma_j \sigma_i = 0 \quad (i \neq j).$$

**Products.**

$$XY = iZ, \quad YZ = iX, \quad ZX = iY.$$

(Cyclic: $X \to Y \to Z \to X$ with factor $i$.)

**Bloch-sphere action.** Each Pauli implements a $\pi$-rotation about its axis:

$$X: (x, y, z) \mapsto (x, -y, -z), \quad Y: (x, y, z) \mapsto (-x, y, -z), \quad Z: (x, y, z) \mapsto (-x, -y, z).$$

---

## 5. Measurement and the Born Rule

### 5.1 Computational-basis measurement

Given an $n$-qubit state $\vert \psi \rangle = \sum_x \alpha_x \vert x \rangle$, measuring in the computational basis yields outcome $x$ with probability

$$p(x) = \vert \alpha_x \vert^{2} = \vert \langle x \vert \psi \rangle \vert^{2}.$$

This is the **Born rule**. Since the state is normalized, $\sum_x p(x) = 1$.

**Post-measurement state.** After obtaining outcome $x$, the state collapses to $\vert x \rangle$. Measurement is **irreversible** — information about the amplitudes is lost.

### 5.2 Single-qubit example

For $\vert \psi \rangle = \alpha_0 \vert 0\rangle + \alpha_1 \vert 1\rangle$:

$$p(0) = \vert \alpha_0 \vert^{2}, \quad p(1) = \vert \alpha_1 \vert^{2}.$$

After measuring outcome $x$, the state is $\vert x \rangle$.

### 5.3 Measuring an observable

An **observable** is a Hermitian operator $O$ with spectral decomposition $O = \sum_k \lambda_k \Pi_k$, where $\lambda_k$ are the eigenvalues and $\Pi_k$ are orthogonal projectors.

Measuring $O$ on state $\vert \psi \rangle$:
- Outcome $\lambda_k$ occurs with probability $p(k) = \langle \psi \vert \Pi_k \vert \psi \rangle = \mathrm{Tr}(\rho \Pi_k)$.
- Post-measurement state: $\vert \psi \rangle \mapsto \Pi_k \vert \psi \rangle / \sqrt{p(k)}$.

**Expectation value:**

$$\langle O \rangle = \langle \psi \vert O \vert \psi \rangle = \mathrm{Tr}(\rho O) = \sum_k \lambda_k \, p(k).$$

**Remark: non-orthogonal states cannot be perfectly distinguished (N&C Box 2.3).** If $\langle\psi_1\vert\psi_2\rangle \neq 0$, no measurement can identify $\vert\psi_1\rangle$ vs $\vert\psi_2\rangle$ with certainty. The non-zero overlap guarantees that any measurement outcome compatible with $\vert\psi_1\rangle$ also has non-zero probability for $\vert\psi_2\rangle$. This underpins no-cloning and BB84 security.

### 5.4 What measurement discards

Consider $\vert \psi_\phi \rangle = \frac{1}{\sqrt{2}}(\vert 0\rangle + e^{i\phi}\vert 1\rangle)$. A $Z$-measurement gives

$$p(0) = \frac{1}{2}, \quad p(1) = \frac{1}{2}$$

independent of $\phi$. The $Z$-measurement **cannot see relative phase**.

However, applying $H$ first and then measuring $Z$ (equivalent to measuring $X$) gives

$$p(0) = \frac{1 + \cos\phi}{2}, \quad p(1) = \frac{1 - \cos\phi}{2}.$$

Phase becomes visible after a basis change. This is the key insight behind the **H–phase–H** pattern used throughout quantum algorithms.

---

## 6. Unitary Evolution

**Postulate.** Between measurements, a closed quantum system evolves by a unitary operator:

$$\vert \psi \rangle \mapsto U \vert \psi \rangle, \quad U^{\dagger}U = I.$$

Unitarity guarantees that normalization is preserved: $\langle \psi \vert U^{\dagger}U \vert \psi \rangle = \langle \psi \vert \psi \rangle = 1$.

**Connection to Hamiltonians.** Continuous-time evolution is governed by the Schrödinger equation:

$$\frac{d}{dt}\vert \psi(t)\rangle = -iH(t)\vert \psi(t)\rangle$$

where $H(t)$ is a Hermitian Hamiltonian. For time-independent $H$, the solution is

$$\vert \psi(t)\rangle = e^{-iHt}\vert \psi(0)\rangle.$$

The operator $U(t) = e^{-iHt}$ is unitary because $H$ is Hermitian. In circuit language, each gate is a unitary; a circuit is a product of unitaries applied in sequence.

### 6.1 Rotation gates

Given the Pauli identity $\sigma^2 = I$, the rotation operators have clean closed forms:

$$R_\alpha(\theta) = e^{-i\theta\sigma_\alpha/2} = \cos\frac{\theta}{2}\,I - i\sin\frac{\theta}{2}\,\sigma_\alpha, \quad \alpha \in \\{X, Y, Z\\}.$$

On the Bloch sphere, $R_\alpha(\theta)$ rotates the Bloch vector by angle $\theta$ about the $\alpha$-axis.

**Explicit matrices:**

$$R_X(\theta) = \begin{pmatrix} \cos\frac{\theta}{2} & -i\sin\frac{\theta}{2} \\\\ -i\sin\frac{\theta}{2} & \cos\frac{\theta}{2} \end{pmatrix}, \quad R_Y(\theta) = \begin{pmatrix} \cos\frac{\theta}{2} & -\sin\frac{\theta}{2} \\\\ \sin\frac{\theta}{2} & \cos\frac{\theta}{2} \end{pmatrix}, \quad R_Z(\theta) = \begin{pmatrix} e^{-i\theta/2} & 0 \\\\ 0 & e^{i\theta/2} \end{pmatrix}.$$

### 6.2 General axis-angle rotation

For unit vector $\hat{n} = (n_x, n_y, n_z)$:

$$U(\hat{n}, \theta) = \cos\frac{\theta}{2}\,I - i\sin\frac{\theta}{2}\,(\hat{n}\cdot\vec{\sigma}).$$

This follows from $(\hat{n}\cdot\vec{\sigma})^2 = I$ (the Pauli anticommutation relations force all cross terms to cancel).

### 6.3 The Hadamard gate

$$H = \frac{1}{\sqrt{2}}\begin{pmatrix} 1 & 1 \\\\ 1 & -1 \end{pmatrix}.$$

Key conjugation identities:

$$HXH = Z, \quad HZH = X, \quad HYH = -Y, \quad H^2 = I.$$

Bloch-sphere interpretation: $H$ is a $\pi$-rotation about the axis $\frac{1}{\sqrt{2}}(\hat{x} + \hat{z})$, which swaps the $x$ and $z$ axes.

**Measurement equivalence.** Measuring $Z$ after applying $H$ is equivalent to measuring $X$:

$$\langle Z \rangle_{H\vert\psi\rangle} = \mathrm{Tr}(H\rho H^{\dagger} Z) = \mathrm{Tr}(\rho\, H^{\dagger}ZH) = \mathrm{Tr}(\rho X) = \langle X \rangle_{\vert\psi\rangle}.$$

### 6.4 Euler decomposition

Any single-qubit unitary $U \in SU(2)$ can be decomposed as

$$U = R_Z(\alpha)\,R_Y(\beta)\,R_Z(\gamma)$$

for some $\alpha, \beta, \gamma \in \mathbb{R}$. This is the SU(2) Euler-angle decomposition. It underlies gate synthesis in hardware: two fixed rotation axes suffice to generate any single-qubit gate (up to global phase).

---

## 7. Superposition vs. Classical Mixture

This distinction is central to understanding what makes quantum computing different from classical probabilistic computing.

**Coherent superposition:**

$$\vert + \rangle = \frac{\vert 0\rangle + \vert 1\rangle}{\sqrt{2}}.$$

**Classical mixture** (same $Z$-measurement statistics):

$$\rho_{\mathrm{mix}} = \frac{1}{2}\vert 0\rangle\langle 0\vert + \frac{1}{2}\vert 1\rangle\langle 1\vert = \frac{I}{2}.$$

Both give $p(0) = p(1) = 1/2$ under a $Z$-measurement. But they are physically different.

**Distinguishing test.** Apply $H$ and then measure $Z$:

$$H\vert +\rangle = \vert 0\rangle \implies p(0) = 1 \quad \mathrm{(deterministic)},$$

$$H\rho_{\mathrm{mix}}H^{\dagger} = \frac{I}{2} \implies p(0) = \frac{1}{2} \quad \mathrm{(still random)}.$$

$\vert +\rangle$ sits on the Bloch sphere surface (pure state). $\rho_{\mathrm{mix}}$ sits at the center (maximally mixed). A rotation moves surface points but leaves the center fixed.

**Takeaway.** Superposition involves *coherence* (off-diagonal terms in $\rho$). A classical mixture has no coherence. Only coherent states can interfere.

**Purity test (N&C Theorem 2.5).** $\rho$ is a valid density operator iff $\mathrm{Tr}(\rho) = 1$ and $\rho \geq 0$. Pure states satisfy $\mathrm{Tr}(\rho^2) = 1$; mixed states satisfy $\mathrm{Tr}(\rho^2) < 1$. In the density-operator picture, evolution is $\rho \mapsto U\rho U^{\dagger}$ and measurement probability is $p(m) = \mathrm{Tr}(\Pi_m \rho)$.

---

## 8. Interference

Interference is the single most important phenomenon behind quantum speedups.

**Core principle.** Probabilities never interfere. Amplitudes interfere, and probabilities are squared magnitudes of amplitudes.

### 8.1 The interference identity

If an amplitude for outcome $x$ is the sum of two contributions $\alpha_x = a + b$, then

$$p(x) = \vert a + b \vert^{2} = \vert a \vert^{2} + \vert b \vert^{2} + 2\,\mathrm{Re}(\bar{a}b).$$

The cross term $2\,\mathrm{Re}(\bar{a}b)$ is the **interference term**. It can be positive (constructive), negative (destructive), or zero.

### 8.2 Worked example: the minus-sign trick

$$\vert 0\rangle \xrightarrow{H} \frac{\vert 0\rangle + \vert 1\rangle}{\sqrt{2}} \xrightarrow{Z} \frac{\vert 0\rangle - \vert 1\rangle}{\sqrt{2}} \xrightarrow{H} \vert 1\rangle.$$

So $HZH\vert 0\rangle = \vert 1\rangle$ deterministically. The $Z$ gate inserts a relative phase ($-1$ on $\vert 1\rangle$), which flips constructive interference into destructive interference on the final recombination.

### 8.3 Phase-to-amplitude conversion (H–phase–H)

The circuit $\vert 0\rangle \to H \to R_Z(\theta) \to H \to$ measure $Z$ produces

$$p(0) = \frac{1 + \cos\theta}{2}, \quad p(1) = \frac{1 - \cos\theta}{2}.$$

This is the smallest example of the central algorithmic pattern: **convert an invisible phase into a visible probability bias**. Virtually every quantum algorithm relies on some version of this.

---

## 9. Composite Systems

If system $A$ has state space $\mathcal{H}_A$ and system $B$ has state space $\mathcal{H}_B$, the composite system has state space

$$\mathcal{H}_{AB} = \mathcal{H}_A \otimes \mathcal{H}_B.$$

For two qubits: $$\mathcal{H}_A \cong \mathbb{C}^2$, $\mathcal{H}_B \cong \mathbb{C}^2$, so $\mathcal{H}_{AB} \cong \mathbb{C}^4.$$

A **product state** is $\vert \psi \rangle_A \otimes \vert \phi \rangle_B$. A state that cannot be written this way is **entangled**.

**Example (Bell state):**

$$\vert \Phi^{+}\rangle = \frac{\vert 00\rangle + \vert 11\rangle}{\sqrt{2}}.$$

This is entangled: there are no single-qubit states $\vert a \rangle, \vert b \rangle$ such that $\vert \Phi^{+}\rangle = \vert a \rangle \otimes \vert b \rangle$.

**Quick test (two qubits).** Write $\vert \Psi \rangle = a\vert 00\rangle + b\vert 01\rangle + c\vert 10\rangle + d\vert 11\rangle$ and form the coefficient matrix $M = \begin{pmatrix} a & b \\\\ c & d \end{pmatrix}$. Then

$\vert \Psi \rangle$ is entangled $\iff \det(M) = ad - bc \neq 0$.

For $\vert \Phi^{+}\rangle$: $ad - bc = \frac{1}{2} \neq 0$. Entangled.

### Schmidt Decomposition

The determinant test works for two qubits. The **Schmidt decomposition** is the general structural theorem for bipartite pure states.

**Theorem.** For any pure state $\vert\Psi\rangle \in \mathcal{H}_A \otimes \mathcal{H}_B$ with $\dim \mathcal{H}_A = d_A$ and $\dim \mathcal{H}_B = d_B$, there exist orthonormal sets $\\{\vert u_k\rangle\\} \subset \mathcal{H}_A$ and $\\{\vert v_k\rangle\\} \subset \mathcal{H}_B$ and non-negative real numbers $\lambda_k \geq 0$ such that

$$\vert\Psi\rangle = \sum_{k=1}^{r} \lambda_k \vert u_k\rangle \otimes \vert v_k\rangle, \quad \sum_k \lambda_k^2 = 1,$$

where $r \leq \min(d_A, d_B)$ is the **Schmidt rank**.

*Proof idea.* Write the state as $\vert\Psi\rangle = \sum_{ij} M_{ij}\vert i\rangle\vert j\rangle$ and form the coefficient matrix $M$ (a $d_A \times d_B$ matrix). Take the SVD: $M = U\Sigma V^{\dagger}$. The Schmidt coefficients $\lambda_k$ are the singular values, and $\vert u_k\rangle, \vert v_k\rangle$ are the corresponding columns of $U$ and $V$. $\square$

**Remark (economy of representation).** A general state in $\mathcal{H}_A \otimes \mathcal{H}_B$ requires $d_A \cdot d_B$ amplitudes in the computational basis. The Schmidt decomposition expresses the same state using only $r \leq \min(d_A, d_B)$ terms. For two qubits: the computational basis needs 4 terms, but the Schmidt form needs **at most 2**. For an $n$-qubit system split into two halves of $n/2$ qubits each: the computational basis needs $2^n$ terms, but the Schmidt form needs at most $2^{n/2}$ terms — an exponential reduction.

**Entanglement criterion.**

$\vert\Psi\rangle$ is a product state $\iff r = 1$.

If $r \geq 2$, the state is entangled. The Schmidt coefficients quantify *how entangled* the state is.

**Reduced density matrices.** From the Schmidt decomposition, the reduced states are immediately:

$$\rho_A = \mathrm{Tr}_B(\vert\Psi\rangle\langle\Psi\vert) = \sum_k \lambda_k^2 \vert u_k\rangle\langle u_k\vert, \quad \rho_B = \sum_k \lambda_k^2 \vert v_k\rangle\langle v_k\vert.$$

Both subsystems share the same spectrum $\\{\lambda_k^2\\}$. For a product state ($r = 1$), both $\rho_A$ and $\rho_B$ are pure. For an entangled state ($r \geq 2$), both are mixed.

**Worked example.** $\vert\Phi^+\rangle = \frac{1}{\sqrt{2}}(\vert 00\rangle + \vert 11\rangle)$ is already in Schmidt form with $\lambda_1 = \lambda_2 = \frac{1}{\sqrt{2}}$, $\vert u_1\rangle = \vert v_1\rangle = \vert 0\rangle$, $\vert u_2\rangle = \vert v_2\rangle = \vert 1\rangle$. Schmidt rank $r = 2$ (entangled). Reduced state: $\rho_A = \frac{1}{2}\vert 0\rangle\langle 0\vert + \frac{1}{2}\vert 1\rangle\langle 1\vert = \frac{I}{2}$ (maximally mixed).

**Schmidt number is invariant under local unitaries (N&C 2.5).** If $\vert\psi\rangle = \sum_i \lambda_i \vert i_A\rangle\vert i_B\rangle$ then $(U \otimes I)\vert\psi\rangle = \sum_i \lambda_i (U\vert i_A\rangle)\vert i_B\rangle$ has the same Schmidt coefficients. This invariance makes the Schmidt number a useful entanglement measure.

**Remark (N&C Exercise 2.77).** The Schmidt decomposition is specific to *bipartite* systems. For three or more parties, a state like $\vert GHZ\rangle = \frac{1}{\sqrt{2}}(\vert 000\rangle + \vert 111\rangle)$ does have a Schmidt-like form, but a generic tripartite state cannot be written as $\sum_i \lambda_i \vert i_A\rangle\vert i_B\rangle\vert i_C\rangle$.

### Partial trace and the reduced density operator (N&C 2.4.3)

The **partial trace** over system $B$ is defined on product operators by

$$\mathrm{Tr}_B(\vert a_1\rangle\langle a_2\vert \otimes \vert b_1\rangle\langle b_2\vert) = \vert a_1\rangle\langle a_2\vert \, \langle b_2\vert b_1\rangle,$$

extended by linearity. The **reduced density operator** for system $A$ is $\rho_A = \mathrm{Tr}_B(\rho_{AB})$.

**Physical justification (N&C Box 2.6).** For any observable $M$ on system $A$, measurement averages are the same whether computed from $\rho_A$ or $\rho_{AB}$: $\mathrm{Tr}(M\rho_A) = \mathrm{Tr}((M \otimes I_B)\rho_{AB})$. The partial trace is the *unique* operation with this property.

---

## 10. The Postulates — Summary

Here are the four rules that define quantum mechanics for finite-dimensional systems (the framework used in quantum computing).

**Postulate 1 (State space).** The state of a quantum system is described by a unit vector $\vert \psi \rangle$ in a complex Hilbert space $\mathcal{H}$ (or more generally, a density matrix $\rho$ with $\rho \succeq 0$ and $\mathrm{Tr}(\rho) = 1$). States differing only by a global phase are physically identical.

**Postulate 2 (Evolution).** A closed system evolves by a unitary operator: $\vert \psi \rangle \mapsto U\vert \psi \rangle$.

**Postulate 3 (Measurement).** A projective measurement is described by orthogonal projectors $\\{\Pi_k\\}$ with $\sum_k \Pi_k = I$. Outcome $k$ with probability $p(k) = \langle\psi\vert \Pi_k\vert\psi\rangle$, post-measurement state $\Pi_k\vert\psi\rangle / \sqrt{p(k)}$.

**Postulate 4 (Composite systems).** The state space of a composite system is the tensor product of the component state spaces: $\mathcal{H}_{AB} = \mathcal{H}_A \otimes \mathcal{H}_B$.

Everything else — Bloch sphere geometry, interference, entanglement, Born rule, expectation values — follows from these four postulates plus the linear algebra from the previous notes.

---

## 11. Quick Reference

| Object | Definition | Role |
|---|---|---|
| $\vert \psi \rangle \in \mathbb{C}^{2^n}$ | Unit vector | Pure state |
| $\rho \succeq 0$, $\mathrm{Tr}(\rho)=1$ | Density matrix | Pure or mixed state |
| $U^{\dagger}U = I$ | Unitary operator | Time evolution / gate |
| $O^{\dagger} = O$ | Hermitian operator | Observable |
| $p(x) = \vert\langle x \vert \psi\rangle\vert^{2}$ | Born rule | Measurement probability |
| $\langle O \rangle = \mathrm{Tr}(\rho O)$ | Expectation value | Average measurement outcome |
| $e^{i\theta}\vert\psi\rangle \sim \vert\psi\rangle$ | Global phase | Physically unobservable |
| $\vert a+b\vert^{2} = \vert a\vert^{2} + \vert b\vert^{2} + 2\,\mathrm{Re}(\bar{a}b)$ | Interference identity | Constructive / destructive |
| $\mathcal{H}_{AB} = \mathcal{H}_A \otimes \mathcal{H}_B$ | Tensor product | Composite systems |
| $ad - bc \neq 0$ | Determinant test | Entanglement (2 qubits) |
| $\vert\Psi\rangle = \sum_k \lambda_k \vert u_k\rangle\vert v_k\rangle$ | Schmidt decomposition | $r$ terms instead of $d_A d_B$; $r=1$ iff product |
| $\rho_A = \mathrm{Tr}_B(\rho_{AB})$ | Partial trace | Reduced density operator (N&C) |

---

[← Back to README](../README.md)
