# Linear Algebra Review

Reference: Mermin Appendix A; supplemented by 2601 Ch. 3.

This is the working linear-algebra kit for quantum computing. The focus is on objects that appear repeatedly: inner products, adjoints, unitary and Hermitian matrices, eigenvalues, tensor products, and density matrices.

---

## 1. Complex Vector Spaces

A **complex vector space** $V$ has dimension $D = N + 1$ (we index from $0$ to $N$).

**Standard (computational) basis** of $V$:

$$\vert 0\rangle = \begin{pmatrix} 1 \\ 0 \\ \vdots \\ 0 
\end{pmatrix}, \quad

\vert 1\rangle = \begin{pmatrix} 0 \\ 1 \\ \vdots \\ 0 \end{pmatrix}, \quad \dots \quad
\vert N\rangle = \begin{pmatrix} 0 \\ \vdots \\ 0 \\ 1 \end{pmatrix}$$

These are $D$-dimensional column vectors.

**Dirac notation.** Vectors are called **kets**, written $\vert \alpha \rangle$. Any $\vert \alpha \rangle \in V$ can be expanded as

$$\vert \alpha \rangle = \sum_{x=0}^{N} \alpha_x \vert x \rangle = \begin{pmatrix} \alpha_0 \\ \alpha_1 \\ \vdots \\ \alpha_N \end{pmatrix}, \quad \alpha_x \in \mathbb{C}.$$

The numbers $\alpha_x$ are the **components** of $\vert \alpha \rangle$ in the standard basis.

**Linearity.** If $\vert \alpha \rangle, \vert \beta \rangle \in V$ and $a, b \in \mathbb{C}$, then $a\vert \alpha \rangle + b\vert \beta \rangle \in V$.

---

## 2. Inner Product

The **standard inner product** on $\mathbb{C}^D$ is defined by

$$\langle \phi \vert \psi \rangle := \sum_{j=0}^{N} \phi_j^* \psi_j.$$

Convention: **conjugate-linear in the first slot**, linear in the second (physics convention).

The **bra** $\langle \alpha \vert$ is the conjugate transpose (adjoint) of the ket:

$$\langle \alpha \vert = \vert \alpha \rangle^\dagger = (\alpha_0^*, \alpha_1^*, \dots, \alpha_N^*).$$

So $\langle \alpha \vert \beta \rangle = \alpha^\dagger \beta \in \mathbb{C}$.

**Norm.** $\lVert \psi \rVert^2 = \langle \psi \vert \psi \rangle = \sum_j \vert \psi_j \vert^2$.

**Orthonormality of the standard basis:**

$$\langle x \vert y \rangle = \delta_{xy}.$$

**Cauchy–Schwarz inequality:**

$$\vert \langle \phi \vert \psi \rangle \vert \leq \lVert \phi \rVert \cdot \lVert \psi \rVert.$$

Operational meaning: amplitudes are inner products, probabilities are squared magnitudes. Cauchy–Schwarz is why probabilities are bounded by 1.

---

## 3. Outer Products and Projectors

The **outer product** $\vert \psi \rangle \langle \phi \vert$ is a $D \times D$ matrix:

$$(\vert \psi \rangle \langle \phi \vert) \vert v \rangle = \vert \psi \rangle \langle \phi \vert v \rangle.$$

It maps any vector to a scalar multiple of $\vert \psi \rangle$.

**Rank-one projector.** If $\lVert \psi \rVert = 1$, define

$$\Pi_\psi := \vert \psi \rangle \langle \psi \vert.$$

Then $\Pi_\psi^\dagger = \Pi_\psi$ (Hermitian) and $\Pi_\psi^2 = \Pi_\psi$ (idempotent):

$$\Pi_\psi^2 = \vert \psi \rangle \underbrace{\langle \psi \vert \psi \rangle}_{=1} \langle \psi \vert = \Pi_\psi.$$

**Completeness relation.** For any orthonormal basis $\{\vert k \rangle\}$:

$$\sum_k \vert k \rangle \langle k \vert = I.$$

---

## 4. Linear Maps: Adjoint, Unitary, Hermitian

**Adjoint.** For $A \in \mathbb{C}^{D \times D}$, the adjoint is $A^\dagger = (\bar{A})^T$ (conjugate transpose).

**Unitary.** $U$ is unitary if $U^\dagger U = I$ (equivalently $UU^\dagger = I$).

**Hermitian.** $H$ is Hermitian if $H^\dagger = H$.

**Key property of unitaries:** they preserve norms and inner products.

$$\lVert U\psi \rVert = \lVert \psi \rVert, \qquad \langle U\phi \vert U\psi \rangle = \langle \phi \vert \psi \rangle.$$

*Proof.* $\lVert U\psi \rVert^2 = \psi^\dagger U^\dagger U \psi = \psi^\dagger \psi = \lVert \psi \rVert^2$. Similarly for the inner product.

This is why quantum gates (unitary operators) preserve probability normalization.

### Concrete gate checks

**Pauli X:**

$$X = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix}, \quad X^\dagger = X, \quad X^2 = I.$$

Hence unitary ($X^\dagger X = I$) and Hermitian ($X^\dagger = X$).

**Hadamard:**

$$H = \frac{1}{\sqrt{2}} \begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix}.$$

Check: $H^\dagger H = \frac{1}{2}\begin{pmatrix}1&1\\1&-1\end{pmatrix}\begin{pmatrix}1&1\\1&-1\end{pmatrix} = \frac{1}{2}\begin{pmatrix}2&0\\0&2\end{pmatrix} = I$. Unitary and Hermitian.

---

## 5. Eigenvalues, Diagonalization, and Spectra

### Why eigenvalues appear in QC

Eigenvalues show up whenever you have:
- **Measurement**: observables are Hermitian; outcomes are eigenvalues.
- **Time evolution**: $e^{-itH}$ — spectral decomposition turns matrix exponentials into scalars.
- **Phase estimation / QPE**: eigenphases are what you measure.

### Spectral theorem

Every Hermitian matrix $A$ admits a **spectral decomposition**:

$$A = \sum_k \lambda_k \vert v_k \rangle \langle v_k \vert$$

where $\{\vert v_k \rangle\}$ is an orthonormal eigenbasis and $\lambda_k \in \mathbb{R}$.

For unitary matrices, the eigenvalues lie on the unit circle: $\lambda_k = e^{i\theta_k}$.

### Worked example: diagonalize $Z$

$$Z = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix}.$$

Eigenpairs: $(+1, \vert 0 \rangle)$ and $(-1, \vert 1 \rangle)$. Spectral decomposition:

$$Z = (+1)\vert 0 \rangle \langle 0 \vert + (-1)\vert 1 \rangle \langle 1 \vert.$$

### Worked example: diagonalize a general 2×2 Hermitian matrix

Let $A = \begin{pmatrix} 2 & 1+i \\ 1-i & 0 \end{pmatrix}$.

**Step 1 — eigenvalues.** Solve $\det(A - \lambda I) = 0$:

$$(2-\lambda)(-\lambda) - (1+i)(1-i) = \lambda^2 - 2\lambda - 2 = 0$$

so $\lambda = 1 \pm \sqrt{3}$.

**Step 2 — eigenvectors.** For $\lambda_+ = 1 + \sqrt{3}$, solve $(A - \lambda_+ I)v = 0$ to find the eigenvector, then normalize. Repeat for $\lambda_-$.

**Step 3 — spectral decomposition.** $A = \lambda_+ \vert v_+ \rangle \langle v_+ \vert + \lambda_- \vert v_- \rangle \langle v_- \vert$.

---

## 6. Tensor Products

### Definition

Given two vector spaces $V_1$ ($\dim D_1$) and $V_2$ ($\dim D_2$), the tensor product $V_1 \otimes V_2$ has dimension $D_1 D_2$.

For $\vert \alpha \rangle \in V_1$ and $\vert \beta \rangle \in V_2$:

$$\vert \alpha \rangle \otimes \vert \beta \rangle = \begin{pmatrix} \alpha_0 \vert \beta \rangle \\ \alpha_1 \vert \beta \rangle \\ \vdots \\ \alpha_{N_1} \vert \beta \rangle \end{pmatrix} = \begin{pmatrix} \alpha_0 \beta_0 \\ \vdots \\ \alpha_0 \beta_{N_2} \\ \alpha_1 \beta_0 \\ \vdots \\ \alpha_{N_1} \beta_{N_2} \end{pmatrix}$$

a $D_1 D_2$-dimensional column vector.

### Kronecker product (for matrices)

If $A \in \mathbb{C}^{m \times m}$ and $B \in \mathbb{C}^{n \times n}$:

$$A \otimes B = \begin{pmatrix} a_{11}B & \cdots & a_{1m}B \\ \vdots & \ddots & \vdots \\ a_{m1}B & \cdots & a_{mm}B \end{pmatrix} \in \mathbb{C}^{mn \times mn}.$$

### The wiring rule

$$(A \otimes B)(\vert a \rangle \otimes \vert b \rangle) = (A\vert a \rangle) \otimes (B\vert b \rangle).$$

This is the algebraic statement of "parallel operations on separate wires."

### Example: $H \otimes H$

$$H \otimes H = \frac{1}{2} \begin{pmatrix} 1 & 1 & 1 & 1 \\ 1 & -1 & 1 & -1 \\ 1 & 1 & -1 & -1 \\ 1 & -1 & -1 & 1 \end{pmatrix}.$$

### Example: $(X \otimes Z)(\vert 0 \rangle \otimes \vert 1 \rangle)$

By the wiring rule:

$$(X \otimes Z)(\vert 0 \rangle \otimes \vert 1 \rangle) = (X\vert 0 \rangle) \otimes (Z\vert 1 \rangle) = \vert 1 \rangle \otimes (-\vert 1 \rangle) = -\vert 11 \rangle.$$

---

## 7. Density Matrices and Mixed States

### Why density matrices?

A ket $\vert \psi \rangle$ describes a **pure state** — a system we have complete knowledge of. But in practice, quantum systems are almost never purely described by a single ket. Density matrices extend the formalism to handle two situations that kets alone cannot express:

**Classical ignorance.** Someone flips a coin: heads → prepare $\vert 0 \rangle$, tails → prepare $\vert 1 \rangle$, but doesn't tell you the result. The qubit is not in a superposition — it's in a **classical probabilistic mixture**:

$$\rho_{\text{mix}} = \tfrac{1}{2}\vert 0 \rangle \langle 0 \vert + \tfrac{1}{2}\vert 1 \rangle \langle 1 \vert = \tfrac{1}{2}I.$$

This is fundamentally different from the superposition $\vert + \rangle = \frac{1}{\sqrt{2}}(\vert 0 \rangle + \vert 1 \rangle)$. Both give 50/50 in the computational basis, but $\vert + \rangle$ yields a deterministic $+1$ outcome when measured in the X basis, while $\rho_{\text{mix}}$ gives 50/50 in every basis. The difference is **coherence** — the off-diagonal terms that $\rho_{\text{mix}}$ lacks.

**Entanglement.** This is the deeper reason. Consider the Bell state $\vert \Phi^+ \rangle = \frac{1}{\sqrt{2}}(\vert 00 \rangle + \vert 11 \rangle)$. The full two-qubit system is pure, but if you look at the first qubit alone (partial trace over B), you get $\rho_A = \frac{1}{2}I$ — a maximally mixed state. This is not ignorance about preparation; it is a fundamental consequence of entanglement: **a subsystem of a pure entangled state cannot be described by any ket**. Only a density matrix works.

In short: ket notation can only represent pure states, but real quantum systems involve noise, decoherence, and partial observation, all of which produce mixed states. The density matrix is the general-purpose description.

### Definition

A **density matrix** is $\rho \in \mathbb{C}^{D \times D}$ satisfying:

$$\rho^\dagger = \rho, \qquad \rho \succeq 0, \qquad \text{Tr}(\rho) = 1.$$

A state is **pure** if $\rho = \vert \psi \rangle \langle \psi \vert$ (rank one), equivalently $\text{Tr}(\rho^2) = 1$.

A state is **mixed** if $\text{Tr}(\rho^2) < 1$.

### Example: mixture vs. superposition

$$\rho_{\text{mix}} = \tfrac{1}{2}\vert 0 \rangle \langle 0 \vert + \tfrac{1}{2}\vert 1 \rangle \langle 1 \vert = \tfrac{1}{2}I, \qquad \rho_+ = \vert + \rangle \langle + \vert = \tfrac{1}{2}\begin{pmatrix}1&1\\1&1\end{pmatrix}.$$

Both give $p(0) = p(1) = 1/2$ in the computational basis, but they differ by off-diagonal terms (coherence):

$$\text{Tr}(\rho_{\text{mix}}^2) = \tfrac{1}{2} \quad (\text{mixed}), \qquad \text{Tr}(\rho_+^2) = 1 \quad (\text{pure}).$$

$\text{Tr}(\rho^2)$ quantifies purity: it detects whether off-diagonal coherence is present. A fully mixed state ($\rho = \frac{1}{D}I$) gives the minimum $\text{Tr}(\rho^2) = 1/D$; a pure state gives the maximum $\text{Tr}(\rho^2) = 1$.

### Expectation values

For observable $O$ and state $\rho$:

$$\langle O \rangle = \text{Tr}(\rho O).$$

### Partial trace (basic)

For a bipartite state $\rho_{AB}$, the reduced state on $A$ is $\rho_A = \text{Tr}_B(\rho_{AB})$.

**Example:** $\vert \Phi^+ \rangle = \frac{1}{\sqrt{2}}(\vert 00 \rangle + \vert 11 \rangle)$.

$$\rho_{AB} = \frac{1}{2}\begin{pmatrix}1&0&0&1\\0&0&0&0\\0&0&0&0\\1&0&0&1\end{pmatrix}, \qquad \rho_A = \text{Tr}_B(\rho_{AB}) = \frac{1}{2}I.$$

The marginal of a maximally entangled state is maximally mixed.

---

## 8. Born Rule in Coordinates

Let $\vert \psi \rangle = \alpha \vert 0 \rangle + \beta \vert 1 \rangle$ with $\vert \alpha \vert^2 + \vert \beta \vert^2 = 1$. Measurement in the computational basis:

$$p(0) = \vert \langle 0 \vert \psi \rangle \vert^2 = \vert \alpha \vert^2, \qquad p(1) = \vert \langle 1 \vert \psi \rangle \vert^2 = \vert \beta \vert^2.$$

This is inner products and squaring — nothing more.

---

## Quick Reference

| Object | Notation | Key property |
|--------|----------|-------------|
| State vector | $\vert \psi \rangle \in \mathbb{C}^D$ | Column vector |
| Dual vector | $\langle \psi \vert = \vert \psi \rangle^\dagger$ | Row vector (conjugate transpose) |
| Inner product | $\langle \phi \vert \psi \rangle$ | Amplitude; $\vert \cdot \vert^2$ gives probability |
| Norm | $\lVert \psi \rVert^2 = \langle \psi \vert \psi \rangle$ | Must equal 1 for valid states |
| Outer product | $\vert \psi \rangle \langle \phi \vert$ | $D \times D$ matrix |
| Projector | $\Pi = \vert \psi \rangle \langle \psi \vert$ | $\Pi^2 = \Pi$, $\Pi^\dagger = \Pi$ |
| Unitary | $U^\dagger U = I$ | Norm-preserving; gates |
| Hermitian | $H^\dagger = H$ | Real eigenvalues; observables |
| Density matrix | $\rho \succeq 0$, $\text{Tr}(\rho)=1$ | Mixed states |
| Tensor product | $A \otimes B$ | Multi-qubit systems |
| Expectation | $\text{Tr}(\rho O)$ | Observable average |

---

## References

- Mermin, N. D. *Quantum Computer Science*, Appendix A.
- Cho, G. *Geometry- and Topology-Informed QC*, Chapter 3 ("Crash Course: Linear Algebra You Actually Need").
