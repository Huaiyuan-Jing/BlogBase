---
title: 'Quantum Error Correction: Stablizer'
date:
  '[object Object]': null
tags: [
  Quantum Computing
]
categories: []
index_img:
banner:
math: true
---

<!-- @format -->
After a simple example of Shor Code, this article will introduce stabilizer as a tool to build and analyze quantum error correction circuits.

# Why do we need stabilizers?

One key point of quantum error correction is to detect whether the logical qubit has been affected by errors or not without measuring the qubit directly. This is because measurement would destroy the quantum state and cause it to collapse, rendering the qubit unusable.

Stabilizers work by creating quantum entanglement between target qubits and ancilla (detector) qubits. By measuring the ancilla qubits, we can obtain information about the target qubits' state without destroying them—a mechanism similar to quantum teleportation.

# What is a stabilizer?

## Measurement via Pauli Operations

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