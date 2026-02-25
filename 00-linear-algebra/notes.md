# Linear Algebra Review

Reference: Mermin Appendix A; supplemented by 2601 Ch. 3.

This is the working linear-algebra kit for quantum computing. The focus is on objects that appear repeatedly: inner products, adjoints, unitary and Hermitian matrices, eigenvalues, tensor products, and density matrices.

---

## 1. Complex Vector Spaces

A **complex vector space** \(V\) has dimension \(D\). We index the standard basis by \(0,1,\dots,D-1\).

**Standard (computational) basis** of \(V\):

$$
|0\rangle =
\begin{bmatrix}
1\\
0\\
\vdots\\
0
\end{bmatrix},
\quad
|1\rangle =
\begin{bmatrix}
0\\
1\\
\vdots\\
0
\end{bmatrix},
\quad \dots \quad
|D-1\rangle =
\begin{bmatrix}
0\\
\vdots\\
0\\
1
\end{bmatrix}.
$$

These are \(D\)-dimensional column vectors.

**Dirac notation.** Vectors are called **kets**, written \(|\alpha\rangle\). Any \(|\alpha\rangle\in V\) can be expanded as

$$
|\alpha\rangle = \sum_{x=0}^{D-1} \alpha_x |x\rangle,
\qquad \alpha_x \in \mathbb{C}.
$$

In coordinates:

$$
|\alpha\rangle =
\begin{bmatrix}
\alpha_0\\
\alpha_1\\
\vdots\\
\alpha_{D-1}
\end{bmatrix}.
$$

The numbers \(\alpha_x\) are the **components** of \(|\alpha\rangle\) in the standard basis.

**Linearity.** If \(|\alpha\rangle,|\beta\rangle\in V\) and \(a,b\in\mathbb{C}\), then \(a|\alpha\rangle + b|\beta\rangle \in V\).

---

## 2. Inner Product

The **standard inner product** on \(\mathbb{C}^D\) is defined by

$$
\langle \phi | \psi \rangle := \sum_{j=0}^{D-1} \phi_j^* \psi_j.
$$

Convention: **conjugate-linear in the first slot**, linear in the second (physics convention).

The **bra** \(\langle \alpha|\) is the conjugate transpose (adjoint) of the ket:

$$
\langle \alpha| = |\alpha\rangle^\dagger = (\alpha_0^*, \alpha_1^*, \dots, \alpha_{D-1}^*).
$$

So \(\langle \alpha | \beta \rangle = \alpha^\dagger \beta \in \mathbb{C}\).

**Norm.**

$$
\|\psi\|^2 = \langle \psi|\psi\rangle = \sum_{j=0}^{D-1} |\psi_j|^2.
$$

**Orthonormality of the standard basis:**

$$
\langle x|y\rangle = \delta_{xy}.
$$

**Cauchy–Schwarz inequality:**

$$
|\langle \phi|\psi\rangle| \le \|\phi\|\,\|\psi\|.
$$

Operational meaning: amplitudes are inner products, probabilities are squared magnitudes. With normalization, Cauchy–Schwarz ensures probabilities stay bounded.

---

## 3. Outer Products and Projectors

The **outer product** \(|\psi\rangle\langle \phi|\) is a \(D\times D\) matrix:

$$
(|\psi\rangle\langle \phi|)\,|v\rangle = |\psi\rangle \langle \phi|v\rangle.
$$

It maps any vector to a scalar multiple of \(|\psi\rangle\).

**Rank-one projector.** If \(\|\psi\|=1\), define

$$
\Pi_\psi := |\psi\rangle\langle \psi|.
$$

Then \(\Pi_\psi^\dagger = \Pi_\psi\) (Hermitian) and \(\Pi_\psi^2 = \Pi_\psi\) (idempotent):

$$
\Pi_\psi^2
= |\psi\rangle\underbrace{\langle \psi|\psi\rangle}_{=1}\langle \psi|
= \Pi_\psi.
$$

**Completeness relation.** For any orthonormal basis \(\{|k\rangle\}\):

$$
\sum_k |k\rangle\langle k| = I.
$$

---

## 4. Linear Maps: Adjoint, Unitary, Hermitian

**Adjoint.** For \(A\in\mathbb{C}^{D\times D}\), the adjoint is \(A^\dagger = (\overline{A})^T\) (conjugate transpose).

**Unitary.** \(U\) is unitary if \(U^\dagger U = I\) (equivalently \(UU^\dagger = I\)).

**Hermitian.** \(H\) is Hermitian if \(H^\dagger = H\).

**Key property of unitaries:** they preserve norms and inner products.

$$
\|U\psi\| = \|\psi\|,
\qquad
\langle U\phi|U\psi\rangle = \langle \phi|\psi\rangle.
$$

*Proof.* \(\|U\psi\|^2 = \psi^\dagger U^\dagger U \psi = \psi^\dagger \psi = \|\psi\|^2\). Similarly for the inner product.

This is why quantum gates (unitary operators) preserve probability normalization.

### Concrete gate checks

**Pauli \(X\):**

$$
X =
\begin{bmatrix}
0 & 1\\
1 & 0
\end{bmatrix},
\quad
X^\dagger = X,
\quad
X^2 = I.
$$

Hence unitary (\(X^\dagger X = I\)) and Hermitian (\(X^\dagger = X\)).

**Hadamard:**

$$
H = \frac{1}{\sqrt{2}}
\begin{bmatrix}
1 & 1\\
1 & -1
\end{bmatrix}.
$$

Check:

$$
H^\dagger H
= \frac{1}{2}
\begin{bmatrix}
1 & 1\\
1 & -1
\end{bmatrix}
\begin{bmatrix}
1 & 1\\
1 & -1
\end{bmatrix}
=
\frac{1}{2}
\begin{bmatrix}
2 & 0\\
0 & 2
\end{bmatrix}
= I.
$$

So \(H\) is unitary and Hermitian.

---

## 5. Eigenvalues, Diagonalization, and Spectra

### Why eigenvalues appear in QC

Eigenvalues show up whenever you have:
- **Measurement**: observables are Hermitian; outcomes are eigenvalues.
- **Time evolution**: \(e^{-itH}\) — spectral decomposition turns matrix exponentials into scalars.
- **Phase estimation / QPE**: eigenphases are what you measure.

### Spectral theorem (finite-dimensional)

Every Hermitian matrix \(A\) admits a **spectral decomposition**:

$$
A = \sum_k \lambda_k |v_k\rangle\langle v_k|,
$$

where \(\{|v_k\rangle\}\) is an orthonormal eigenbasis and \(\lambda_k\in\mathbb{R}\).

For unitary matrices, the eigenvalues lie on the unit circle: \(\lambda_k = e^{i\theta_k}\).

### Worked example: diagonalize \(Z\)

$$
Z =
\begin{bmatrix}
1 & 0\\
0 & -1
\end{bmatrix}.
$$

Eigenpairs: \((+1, |0\rangle)\) and \((-1, |1\rangle)\). Spectral decomposition:

$$
Z = (+1)|0\rangle\langle 0| + (-1)|1\rangle\langle 1|.
$$

### Worked example: diagonalize a general \(2\times 2\) Hermitian matrix

Let

$$
A =
\begin{bmatrix}
2 & 1+i\\
1-i & 0
\end{bmatrix}.
$$

**Step 1 — eigenvalues.** Solve \(\det(A-\lambda I)=0\):

$$
(2-\lambda)(-\lambda) - (1+i)(1-i) = \lambda^2 - 2\lambda - 2 = 0,
$$

so \(\lambda = 1 \pm \sqrt{3}\).

**Step 2 — eigenvectors.** For \(\lambda_+ = 1+\sqrt{3}\), solve \((A-\lambda_+ I)v=0\) to find an eigenvector, then normalize. Repeat for \(\lambda_-\).

**Step 3 — spectral decomposition.**

$$
A = \lambda_+ |v_+\rangle\langle v_+| + \lambda_- |v_-\rangle\langle v_-|.
$$

---

## 6. Tensor Products

### Definition

Given two vector spaces \(V_1\) (\(\dim D_1\)) and \(V_2\) (\(\dim D_2\)), the tensor product \(V_1\otimes V_2\) has dimension \(D_1D_2\).

For \(|\alpha\rangle\in V_1\) and \(|\beta\rangle\in V_2\), the tensor product \(|\alpha\rangle\otimes|\beta\rangle\) is a \(D_1D_2\)-dimensional column vector.

### Kronecker product (for matrices)

If \(A\in\mathbb{C}^{m\times m}\) and \(B\in\mathbb{C}^{n\times n}\):

$$
A\otimes B =
\begin{bmatrix}
a_{11}B & \cdots & a_{1m}B\\
\vdots & \ddots & \vdots\\
a_{m1}B & \cdots & a_{mm}B
\end{bmatrix}
\in \mathbb{C}^{mn\times mn}.
$$

### The wiring rule

$$
(A\otimes B)(|a\rangle\otimes|b\rangle) = (A|a\rangle)\otimes(B|b\rangle).
$$

This is the algebraic statement of “parallel operations on separate wires.”

### Example: \(H\otimes H\)

$$
H\otimes H
=
\frac{1}{2}
\begin{bmatrix}
1 & 1 & 1 & 1\\
1 & -1 & 1 & -1\\
1 & 1 & -1 & -1\\
1 & -1 & -1 & 1
\end{bmatrix}.
$$

### Example: \((X\otimes Z)(|0\rangle\otimes|1\rangle)\)

By the wiring rule:

$$
(X\otimes Z)(|0\rangle\otimes|1\rangle)
= (X|0\rangle)\otimes(Z|1\rangle)
= |1\rangle\otimes(-|1\rangle)
= -|11\rangle.
$$

---

## 7. Density Matrices and Mixed States

### Why density matrices?

A ket \(|\psi\rangle\) describes a **pure state**. Density matrices extend the formalism to handle:

**Classical ignorance.** A fair coin prepares \(|0\rangle\) or \(|1\rangle\) with probability \(1/2\):

$$
\rho_{\text{mix}}
= \tfrac{1}{2}|0\rangle\langle 0|
+ \tfrac{1}{2}|1\rangle\langle 1|
= \tfrac{1}{2}I.
$$

This differs from the superposition \(|+\rangle=\tfrac{1}{\sqrt{2}}(|0\rangle+|1\rangle)\). Both give 50/50 in the computational basis, but \(|+\rangle\) is deterministic in the \(X\) basis while \(\rho_{\text{mix}}\) remains 50/50 in every basis. The difference is **coherence** (off-diagonal terms).

**Entanglement.** For the Bell state \(|\Phi^+\rangle=\tfrac{1}{\sqrt{2}}(|00\rangle+|11\rangle)\), the joint state is pure, but the reduced state of either qubit is maximally mixed: \(\rho_A=\tfrac{1}{2}I\). A subsystem of a pure entangled state cannot be described by a ket.

### Definition

A **density matrix** is \(\rho\in\mathbb{C}^{D\times D}\) satisfying:

$$
\rho^\dagger = \rho,
\qquad
\rho \succeq 0,
\qquad
\operatorname{Tr}(\rho)=1.
$$

A state is **pure** if \(\rho = |\psi\rangle\langle\psi|\) (rank one), equivalently \(\operatorname{Tr}(\rho^2)=1\).

A state is **mixed** if \(\operatorname{Tr}(\rho^2)<1\).

### Example: mixture vs. superposition

$$
\rho_{\text{mix}} = \tfrac{1}{2}I,
\qquad
\rho_+ = |+\rangle\langle +|
= \tfrac{1}{2}
\begin{bmatrix}
1 & 1\\
1 & 1
\end{bmatrix}.
$$

Purity check:

$$
\operatorname{Tr}(\rho_{\text{mix}}^2) = \tfrac{1}{2},
\qquad
\operatorname{Tr}(\rho_+^2) = 1.
$$

In general, \(\operatorname{Tr}(\rho^2)\) detects coherence: the maximally mixed state \(\rho=\tfrac{1}{D}I\) gives \(\operatorname{Tr}(\rho^2)=\tfrac{1}{D}\), while a pure state gives \(1\).

### Expectation values

For observable \(O\) and state \(\rho\):

$$
\langle O\rangle = \operatorname{Tr}(\rho O).
$$

### Partial trace (basic)

For a bipartite state \(\rho_{AB}\), the reduced state on \(A\) is \(\rho_A=\operatorname{Tr}_B(\rho_{AB})\).

**Example:** \(|\Phi^+\rangle=\tfrac{1}{\sqrt{2}}(|00\rangle+|11\rangle)\).

$$
\rho_{AB}
=
\frac{1}{2}
\begin{bmatrix}
1 & 0 & 0 & 1\\
0 & 0 & 0 & 0\\
0 & 0 & 0 & 0\\
1 & 0 & 0 & 1
\end{bmatrix},
\qquad
\rho_A = \operatorname{Tr}_B(\rho_{AB}) = \tfrac{1}{2}I.
$$

---

## 8. Born Rule in Coordinates

Let \(|\psi\rangle = \alpha |0\rangle + \beta |1\rangle\) with \(|\alpha|^2 + |\beta|^2 = 1\). Measurement in the computational basis:

$$
p(0) = |\langle 0|\psi\rangle|^2 = |\alpha|^2,
\qquad
p(1) = |\langle 1|\psi\rangle|^2 = |\beta|^2.
$$

This is inner products and squaring — nothing more.

---

## Quick Reference

- State vector: \(|\psi\rangle\in\mathbb{C}^D\) (column vector)
- Dual vector: \(\langle \psi| = |\psi\rangle^\dagger\) (row vector)
- Inner product: \(\langle \phi|\psi\rangle\) (amplitude; \(|\cdot|^2\) gives probability)
- Norm: \(\|\psi\|^2=\langle\psi|\psi\rangle\) (equals 1 for valid pure states)
- Outer product: \(|\psi\rangle\langle\phi|\) (\(D\times D\) matrix)
- Projector: \(\Pi=|\psi\rangle\langle\psi|\) (\(\Pi^2=\Pi\), \(\Pi^\dagger=\Pi\))
- Unitary: \(U^\dagger U = I\) (norm-preserving; gates)
- Hermitian: \(H^\dagger=H\) (real eigenvalues; observables)
- Density matrix: \(\rho\succeq 0\), \(\operatorname{Tr}(\rho)=1\) (mixed states)
- Tensor product: \(A\otimes B\) (multi-qubit systems)
- Expectation: \(\operatorname{Tr}(\rho O)\) (observable average)

---

## References

- Mermin, N. D. *Quantum Computer Science*, Appendix A.
- Cho, G. *Geometry- and Topology-Informed QC*, Chapter 3 (“Crash Course: Linear Algebra You Actually Need”).
