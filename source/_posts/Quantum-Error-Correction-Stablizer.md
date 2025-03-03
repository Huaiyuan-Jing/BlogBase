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
After an easy example of Shor Code, this article will introduce stablizer as a tool to build and analyse Quantum Error Correction Circuit.

# Why we need stablizer?

One key point of quantum error correction is detect whether the logical qubit is affected by error or not without measuring the qubit itself directly. Because measurement will destroy the quantum state and make it collapse. Then the qubit is not able to use any more. 

So here stablizers work. Stablizers build quantum entanglement between target qubits and detect qubits. Then by measuring the detect qubits, we can get some information about the target qubits' state without destroying them, which is similar to quantum teleportation.

# What is stablizer?

## Measurement by Pauli Operations

Consider the simple circuit:

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/Quantum-Error-Correction-Stablizer/circ1.png?raw=true)

Assume $q_0$ is an arbitrary state, what information can we get by measuring $q_1$ in this circuit?

$$
\begin{align}
q_0q_1 = (a|0\rangle + b|1\rangle) \otimes |0\rangle \\\\
\xrightarrow{h(1)} (a|0\rangle + b|1\rangle) \otimes |+\rangle \\\\
\xrightarrow{cx(1, 0)} \frac{\sqrt{2}}{2}(a|00\rangle + b|01\rangle + b|10\rangle + a|11\rangle) \\\\
\xrightarrow{h(1)} \frac{\sqrt{2}}{2}[(a + b)|+\rangle \otimes |0\rangle + (a - b)|-\rangle \otimes |1\rangle]
\end{align} 
$$

So measuring $q_1$ is actually measuring $q_0$ with base $\lbrace|+\rangle, |-\rangle\rbrace$ instead of base $\lbrace|0\rangle, |1\rangle\rbrace$. The key point of this circuit is it does not measure on $q_0$ directly, so $q_0$ is still able to use in the following computation.