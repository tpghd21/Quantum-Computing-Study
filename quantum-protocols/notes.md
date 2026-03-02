# Quantum Protocols — Teleportation, Superdense Coding, and More

Reference: Mermin Ch. 6; Nielsen & Chuang Ch. 1.3.7 (Teleportation), 2.3 (Superdense), 2.6 (Bell/CHSH); supplemented by 2601 Ch. 17, 30.

These three protocols demonstrate that entanglement is a physical resource that changes what communication is possible. They share a common control pattern: resource entanglement → short Clifford circuit → measurement → classical feed-forward. This same pattern reappears in fault-tolerant quantum computing (Pauli-frame tracking).

---

## 1. Prerequisites: Bell States and Bell Measurement

### 1.1 The four Bell states

$$\vert\Phi^+\rangle = \frac{\vert 00\rangle + \vert 11\rangle}{\sqrt{2}}, \quad \vert\Phi^-\rangle = \frac{\vert 00\rangle - \vert 11\rangle}{\sqrt{2}},$$

$$\vert\Psi^+\rangle = \frac{\vert 01\rangle + \vert 10\rangle}{\sqrt{2}}, \quad \vert\Psi^-\rangle = \frac{\vert 01\rangle - \vert 10\rangle}{\sqrt{2}}.$$

These four states form an orthonormal basis for $\mathbb{C}^4$. Any two-qubit state can be expanded in the Bell basis.

### 1.2 Bell measurement (decode circuit)

The decoder circuit $D = (I \otimes H) \cdot C^X_{01}$ ($C^X$ then $H$ on qubit 0) maps Bell states to computational basis states:

$$D\vert\Phi^+\rangle = \vert 00\rangle, \quad D\vert\Phi^-\rangle = \vert 01\rangle, \quad D\vert\Psi^+\rangle = \vert 10\rangle, \quad D\vert\Psi^-\rangle = \vert 11\rangle.$$

So a Bell measurement is just $C^X_{01}$ followed by $H$ on qubit 0 (right), then computational-basis measurement.

### 1.3 Paulis permute the Bell basis

Starting from $\vert\Phi^+\rangle$ and applying a Pauli to the first qubit:

| Operation | Result |
|---|---|
| $I \otimes I$ | $\vert\Phi^+\rangle$ |
| $Z \otimes I$ | $\vert\Phi^-\rangle$ |
| $X \otimes I$ | $\vert\Psi^+\rangle$ |
| $XZ \otimes I$ | $\vert\Psi^-\rangle$ (up to global phase) |

This means a single-qubit Pauli on one half of a Bell pair can reach any of the four orthogonal Bell states. Both superdense coding and teleportation rely on this fact.

---

## 2. No-Cloning Theorem

**Theorem.** There is no quantum operation that takes an unknown state $\vert\psi\rangle$ and produces two perfect copies $\vert\psi\rangle\vert\psi\rangle$ for all $\vert\psi\rangle$.

*Proof sketch.* Suppose a unitary $U$ clones: $U(\vert\psi\rangle\vert 0\rangle) = \vert\psi\rangle\vert\psi\rangle$ for all $\vert\psi\rangle$. For two states $\vert\psi\rangle, \vert\phi\rangle$:

$$\langle\psi\vert\phi\rangle = \langle\psi, 0\vert\phi, 0\rangle = \langle\psi,\psi\vert\phi,\phi\rangle = \langle\psi\vert\phi\rangle^2.$$

So $\langle\psi\vert\phi\rangle \in \\{0, 1\\}$, which means any two states must be either identical or orthogonal. Contradiction for non-orthogonal states. $\square$

**Why this matters.** No-cloning is the physics that makes QKD secure: an eavesdropper cannot copy quantum signals without disturbing them.

---

## 3. Quantum Teleportation

### 3.1 Goal

Move an unknown qubit state $\vert\psi\rangle = \alpha\vert 0\rangle + \beta\vert 1\rangle$ from Alice to Bob using:
- 1 shared Bell pair ($\vert\Phi^+\rangle$)
- 2 classical bits
- Local operations only (no quantum channel needed after the Bell pair is distributed)

### 3.2 Setup

Three qubits: $A$ (Alice's unknown input), $A'$ (Alice's half of Bell pair), $B$ (Bob's half).

Initial state:

$$\vert\psi\rangle_A \otimes \vert\Phi^+\rangle_{A'B} = (\alpha\vert 0\rangle + \beta\vert 1\rangle)_A \otimes \frac{1}{\sqrt{2}}(\vert 00\rangle + \vert 11\rangle)_{A'B}.$$

### 3.3 Circuit

1. Alice applies $C^X_{A,A'}$ (control = $A$, target = $A'$).
2. Alice applies $H$ on qubit $A$.
3. Alice measures both $A$ and $A'$, getting classical bits $(m_1, m_2)$.
4. Alice sends $(m_1, m_2)$ to Bob over a classical channel.
5. Bob applies $Z^{m_1}X^{m_2}$ to qubit $B$.

### 3.4 Algebra (the teleportation identity)

After step 1 ($C^X$) and step 2 ($H$), the three-qubit state becomes:

$$\vert\Psi_{\mathrm{pre}}\rangle = \frac{1}{2}\sum_{m_1, m_2 \in \\{0,1\\}} \vert m_1 m_2\rangle_{AA'} \otimes \big(X^{m_2}Z^{m_1}\vert\psi\rangle\big)_B.$$

Expanding — Bob's state for each measurement outcome:

| $(m_1, m_2)$ | Bob holds | Correction |
|---|---|---|
| $(0, 0)$ | $\alpha\vert 0\rangle + \beta\vert 1\rangle = I\vert\psi\rangle$ | $I$ |
| $(0, 1)$ | $\alpha\vert 1\rangle + \beta\vert 0\rangle = X\vert\psi\rangle$ | $X$ |
| $(1, 0)$ | $\alpha\vert 0\rangle - \beta\vert 1\rangle = Z\vert\psi\rangle$ | $Z$ |
| $(1, 1)$ | $\alpha\vert 1\rangle - \beta\vert 0\rangle = XZ\vert\psi\rangle$ | $ZX$ |

After Bob's correction $Z^{m_1}X^{m_2}$, he recovers $\vert\psi\rangle$ exactly.

### 3.5 Key observations

**No FTL communication (N&C 2.4.3 analysis).** Without the 2 classical bits, Bob's qubit is maximally mixed ($\rho\_B = I/2$) regardless of $\vert\psi\rangle$. This can be shown rigorously using the reduced density operator: before Alice's measurement, $\rho\_B = \mathrm{Tr}\_{12}(\vert\Psi\_{\mathrm{pre}}\rangle\langle\Psi\_{\mathrm{pre}}\vert) = I/2$. The classical bits are essential — they travel at most at the speed of light.

**No cloning.** Alice's original qubit is destroyed by the measurement. The state is *moved*, not copied.

**Pauli frame.** In practice, the correction $Z^{m_1}X^{m_2}$ can be tracked in software rather than applied physically. This is called **Pauli-frame tracking** and is standard in fault-tolerant architectures.

**Resource interpretation (N&C 1.3.7).** Teleportation shows that 1 shared EPR pair + 2 classical bits $\geq$ 1 qubit of quantum communication. Conversely, superdense coding shows 1 shared EPR pair + 1 qubit of quantum communication $\geq$ 2 classical bits. These resource trade-offs are foundational to quantum information theory and connect to quantum error-correction (N&C Ch. 10) and entanglement distillation (N&C Ch. 12).

---

## 4. Superdense Coding

### 4.1 Goal

Send 2 classical bits by transmitting only 1 qubit, given a pre-shared Bell pair.

This is the "dual" of teleportation: teleportation uses 2 classical bits to move 1 qubit; superdense coding uses 1 qubit to send 2 classical bits.

### 4.2 Protocol

Alice and Bob share $\vert\Phi^+\rangle_{AB}$.

**Encoding.** To send bits $(b\_1, b\_2) \in \\{0,1\\}^2$, Alice applies $U\_{b\_1 b\_2} = Z^{b\_1}X^{b\_2}$ to her qubit $A$:

| $(b_1, b_2)$ | Alice's operation | Resulting Bell state |
|---|---|---|
| $(0, 0)$ | $I$ | $\vert\Phi^+\rangle$ |
| $(1, 0)$ | $Z$ | $\vert\Phi^-\rangle$ |
| $(0, 1)$ | $X$ | $\vert\Psi^+\rangle$ |
| $(1, 1)$ | $XZ$ | $\vert\Psi^-\rangle$ |

Alice then sends her qubit to Bob.

**Decoding.** Bob now holds both qubits. He performs a Bell measurement ($C^X_{01}$ then $H$ on qubit 0, then measure). Since the four Bell states are orthogonal, Bob recovers $(b\_1, b\_2)$ with certainty.

### 4.3 Why it works

A shared Bell pair provides access to four orthogonal global states via local operations on one qubit. Without entanglement, one qubit can only reliably carry 1 bit (Holevo bound for pure states in a fixed basis).

**Security against interception (N&C Exercise 2.70).** If Eve intercepts Alice's qubit in transit, she holds only one half of an entangled pair. For any positive operator $E$ (any measurement Eve might try), $\langle\psi\vert E \otimes I\vert\psi\rangle$ takes the *same value* for all four Bell states. Eve's reduced state is always $I/2$ regardless of Alice's encoding — she gains zero information about the message.

### 4.4 Resource accounting

| | Teleportation | Superdense coding |
|---|---|---|
| Pre-shared | 1 Bell pair | 1 Bell pair |
| Quantum channel | 0 qubits | 1 qubit |
| Classical channel | 2 bits | 0 bits |
| Net result | Move 1 qubit | Send 2 classical bits |

---

## 5. Quantum Key Distribution (brief)

QKD is a quantum communication protocol. It illustrates measurement disturbance and no-cloning.

**BB84.** Alice sends random qubits in $Z$ or $X$ basis. Bob measures in a random basis. They publicly compare bases, keep matching rounds (sifting), check error rate (QBER), and post-process to a secret key. Security rests on: (1) non-orthogonal states cannot be perfectly distinguished, and (2) any measurement that extracts information must disturb the state.

---

## 6. Common Pattern: Measure → Branch → Correct

All three protocols share a control-flow structure:

$$\mathrm{Entangled\ resource} \to \mathrm{Clifford\ circuit} \to \mathrm{Measure\ (classical\ bits)} \to \mathrm{Conditional\ correction}$$

| Protocol | "Measure" | "Branch / Correct" |
|---|---|---|
| Teleportation | Alice measures $(m\_1, m\_2)$ | Bob applies $Z^{m\_1}X^{m\_2}$ |
| Superdense coding | Bob performs Bell measurement | Readout directly gives $(b_1, b_2)$ |

---

## 7. Quick Reference

| Protocol | Resource | Sends | Receives | Key identity |
|---|---|---|---|---|
| Teleportation | 1 Bell pair + 2 cbits | 1 unknown qubit (destroyed) | 1 qubit (recovered) | $\vert\Psi\_{\mathrm{pre}}\rangle = \frac{1}{2}\sum \vert m\_1 m\_2\rangle \otimes X^{m\_2}Z^{m\_1}\vert\psi\rangle$ |
| Superdense coding | 1 Bell pair + 1 qubit | 2 classical bits | 2 classical bits | Paulis permute Bell basis |
| No-cloning | — | — | — | $\langle\psi\vert\phi\rangle = \langle\psi\vert\phi\rangle^2 \implies$ contradiction |
| CHSH | 2 parties, 2 settings each | — | — | Classical $\leq 2$, QM $= 2\sqrt{2}$ |

---

[← Back to README](../README.md)
