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
math:
---

<!-- @format -->
After an easy example of Shor Code, this article will introduce stablizer as a tool to build and analyse Quantum Error Correction Circuit.

# Why we need stablizer?

One key point of quantum error correction is detect whether the logical qubit is affected by error or not without measuring the qubit itself directly. Because measurement will destroy the quantum state and make it collapse. Then the qubit is not able to use any more. 

So here stablizers work. Stablizers build quantum entanglement between target qubits and detect qubits. Then by measuring the detect qubits, we can get some information about the target qubits' state without destroying them, which is similar to quantum teleportation.

# What is stablizer?

## Measurement by Pauli Operations

Consider the simple circuit:

