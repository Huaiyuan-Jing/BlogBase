---
title: 'Quantum Error Correction: Stablizer'
date:
  '[object Object]': null
tags: [
  Quantum Computing
]
categories: []
index_img: https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/Quantum-Error-Correction-Stablizer/circ1.png?raw=true
banner:
math: true
---

<!-- @format -->
After a simple example of Shor Code, this article will introduce stabilizer as a tool to build and analyze quantum error correction circuits.

# Why do we need stabilizers?

One key point of quantum error correction is to detect whether the logical qubit has been affected by errors or not without measuring the qubit directly. This is because measurement would destroy the quantum state and cause it to collapse, rendering the qubit unusable.

Stabilizers work by creating quantum entanglement between target qubits and ancilla (detector) qubits. By measuring the ancilla qubits, we can obtain information about the target qubits' state without destroying them—a mechanism similar to quantum teleportation.

# Measurement via Pauli Operations

First we need to understand the most basic component of stablizer.

Consider the following circuit:

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/Quantum-Error-Correction-Stablizer/circ1.png?raw=true)

Assume that $q_0$ is in an arbitrary quantum state. What information can we obtain by measuring $q_1$ in this circuit?

$$
\begin{align}
q_0 q_1 &= (a|0\rangle + b|1\rangle) \otimes |0\rangle \\\\
&\xrightarrow{\text{H}(1)} (a|0\rangle + b|1\rangle) \otimes |+\rangle \\\\
&\xrightarrow{\text{CNOT}(1,0)} \frac{1}{\sqrt{2}}(a|00\rangle + b|01\rangle + b|10\rangle + a|11\rangle) \\\\
&\xrightarrow{\text{H}(1)} \frac{1}{\sqrt{2}}\left[(a + b)|+\rangle \otimes |0\rangle + (a - b)|-\rangle \otimes |1\rangle\right]
\end{align}
$$

Thus, measuring $q_1$ corresponds to measuring $q_0$ in the $\lbrace|+\rangle, |-\rangle\rbrace$ basis instead of the $\lbrace|0\rangle, |1\rangle\rbrace$ basis. The key feature of this circuit is that it avoids direct measurement of $q_0$, allowing it to remain usable for subsequent computations.

From the perspective of Pauli Matrixs, we can get

$$
X = |+\rangle\langle+| - |-\rangle\langle-|
$$

therefore the projective measurement defined by X is:

$$
\lbrace∣+\rangle\langle+∣, ∣−\rangle\langle−∣\rbrace
$$

Similiarly, we get

$$
\begin{align}
&Y = |+i\rangle\langle+i| - |-i\rangle\langle-i| \\\\
&Z = |0\rangle\langle0| - |1\rangle\langle1|
\end{align}
$$

The measurements defined by $X, Y,$ and $Z,$ are viewed as pauli observables.

If we combine two pauli observables together:

$$
Z⊗Z=(∣0⟩⟨0∣−∣1⟩⟨1∣)⊗(∣0⟩⟨0∣−∣1⟩⟨1∣)=(∣00⟩⟨00∣+∣11⟩⟨11∣)−(∣01⟩⟨01∣+∣10⟩⟨10∣)
$$

If, for instance, we were to measure a $∣ϕ^+⟩$ Bell state nondestructively using this measurement, then we would be certain to obtain the outcome 0, and the state would be unchanged as a result of the measurement. In particular, the state would not collapse to $∣00⟩$ or $∣11⟩$.

# Definition of stablizer

An n-qubit stabilizer code is specified by a list of n-qubit Pauli operations, $P1,…,Pr$​. These operations are called stabilizer generators in this context, and they must satisfy the following three properties.

1. The stabilizer generators all commute with one another.

2. The stabilizer generators form a minimal generating set.

3. At least one quantum state vector is fixed by all of the stabilizer generators.

Now we can describe nine qubit shor code as:

$$
\begin{align}
Z⊗Z⊗I⊗I⊗&I⊗I⊗I⊗I⊗I \\\\
I⊗Z⊗Z⊗I⊗&I⊗I⊗I⊗I⊗I \\\\
I⊗I⊗I⊗Z⊗&Z⊗I⊗I⊗I⊗I \\\\
I⊗I⊗I⊗I⊗&Z⊗Z⊗I⊗I⊗I \\\\
I⊗I⊗I⊗I⊗&I⊗I⊗Z⊗Z⊗I \\\\
I⊗I⊗I⊗I⊗&I⊗I⊗I⊗Z⊗Z \\\\
X⊗X⊗X⊗X⊗&X⊗X⊗I⊗I⊗I \\\\
I⊗I⊗I⊗X⊗&X⊗X⊗X⊗X⊗X​
\end{align}
$$