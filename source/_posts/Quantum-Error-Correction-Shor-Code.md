---
title: 'Quantum Error Correction: Shor Code' 
date:
  '[object Object]': null
tags: [
  Quantum Computing
]
categories: []
description:
index_img: https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fwww.researchgate.net%2Fprofile%2FQin-Yanyuan%2Fpublication%2F351126547%2Ffigure%2Ffig2%2FAS%3A11431281175987241%401689967787116%2FQuantum-error-correction-A-Single-error-code-B-The-shor-code.png&f=1&nofb=1&ipt=b51c1c5fffeb2f704a56e5f6b700a9cc86d519a91303db78f8b1f44b22f0cea4&ipo=images
banner:
sticky:
mermaid:
katex:
mathjax:
topic:
author: Huaiyuan Jing
references:
comments:
indexing:
breadcrumb:
leftbar:
rightbar:
h1:
type:
math: true
---
Quantum error correction algorithms are a core technology in the field of quantum computing, designed to address the issue of quantum bits (qubits) being highly susceptible to environmental interference, which can lead to information loss or errors. Due to the characteristics of quantum systems, such as superposition and quantum entanglement, quantum information is extremely fragile, and even minor noise or phase shifts can compromise the accuracy of computations. Quantum error correction works by designing special encoding schemes to distribute the information of a single qubit across multiple physical qubits, enabling the detection and correction of errors without destroying the quantum state. 

For example, classical quantum error correction codes include the Shor Code, Steane Code, and Surface Code. These algorithms can identify and repair bit-flip errors, phase-flip errors, or errors involving both, thereby providing a solid foundation for building reliable large-scale quantum computers. This article will introduce the architecture of the Shor Code.

# Bit Flip

A bit-flip error is one of the most common types of errors in quantum computing. It refers to the unintended flipping of a qubit's state from $ |0\rangle $ to $ |1\rangle $, or from $ |1\rangle $ to $ |0\rangle $. This type of error is analogous to a bit flip in classical computing but, in the context of quantum computing, it requires error-correcting codes to detect and correct the error in order to preserve the integrity of quantum information.

## Correction Circuit

+ First, a logical qubit is represented using three physical qubits through the use of controlled-X (CX) gates. The CX gate works as follows: if the control qubit's state is $ |1\rangle $, it flips the value of the target qubit. In the setup described, this means that if $ q_0 $ (the control qubit) is in the state $ |1\rangle $, the corresponding target qubits, $ q_1 $ and $ q_2 $, will have their states flipped. This process encodes the logical qubit into a redundant form using three physical qubits, providing resilience against bit-flip errors.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/9fafab4b-f7f8-4815-8500-599576ae1f1f.png?raw=true)

+ If a bit-flip error occurs in one of the three physical qubits representing the logical qubit:

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/45b58c05-d21f-4a36-9f33-671196ff3b3a.png?raw=true)

+ Check the parity of adjacent qubits pairwise using CX gates.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d7264dce-dfce-4b61-b271-6fa0cbc19d94.png?raw=true)

+ Using $q_3$​ and $q_4$​'s values, the location of the bit-flip error can be determined:

| $q_3q_4$ |  Err  |
|------|-------|
|  00  |  N/A  |
|  10  |  q0   |
|  11  |  q1   |
|  01  |  q2   |

In the example above, the measurement result of $q_3q_4$​ is 11, which indicates that a bit-flip occurred on $q_1$.

+ Based on the corresponding encoding, apply a corrective flip at the specified position. In this case, an $X$-gate is applied to $q_1$​.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/42e3e338-4727-4dd9-ab54-b5114bb509d5.png?raw=true)

## Accuracy Calculate

Since this basic circuit uses only three qubits, it can tolerate at most one bit-flip error. If more than one bit-flip occurs, the circuit cannot correct the errors accurately. Assuming the probability of a bit-flip error for a single physical qubit is $ p $, the number of bit-flip errors in this circuit follows a $ Binary(3, p) $ distribution.

The probability of a logical qubit experiencing a bit-flip error is $ p^3 + 3p^2(1 - p) $, which is the sum of the probabilities of exactly three bit-flip errors and exactly two bit-flip errors.

If we plot these probabilities, the graph shows that for $ p < 0.5 $, this logical qubit provides a lower error rate compared to a single physical qubit.

![Graph of error probabilities](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/image_2025-01-08_10-14-29.png?raw=true)

## Test

Experiment with whether this circuit works as designed by testing it through simulating bit-flip errors by manually adding X-gates at predetermined positions.

{%gi 6 2-2-2%}

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d9c3ff0e-5556-4b29-8740-4df0e9c50cfc.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/76f9a782-3363-4f27-8c8f-763305a4802a.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/e0a0c764-909a-4ff1-8342-99f26597c7ee.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

{%endgi%}

It can be observed that, in the simulated tests, a bit-flip error occurring on any one of $ q_0 $, $ q_1 $, or $ q_2 $ does not affect the state of the current logical qubit.

# Phase Flip

Phase-flip error is another common type of error, which occurs when a quantum bit unintentionally has a $ Z $-gate applied to it ($ Z|0\rangle = |0\rangle $, $ Z|1\rangle = -|1\rangle $). This type of error cannot be detected using the bit-flip detection circuit described above. However, with a slight modification, the circuit can be adapted to detect phase-flip errors as well.

## Correction Circuit

+ Similar to the bit-flip correction circuit, a logical qubit is represented using three physical qubits, and a Hadamard gate is applied to each qubit. The purpose of using the Hadamard gate for encoding is that if a phase-flip occurs on any qubit, it will flip $ |+\rangle $ to $ |-\rangle $. Finally, by applying the Hadamard gate again for decoding, the state will return to $ |0\rangle $ and $ |1\rangle $. This way, the phase-flip error is transformed into a bit-flip error.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/41ff177e-2348-4be0-b561-467ab134d3f3.png?raw=true)

+ Use CX gates to check whether adjacent qubits are equal. Unlike the bit-flip detection circuit, in this case, a Hadamard gate is applied to the qubits being checked, and the direction of the CX gate is reversed.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d6146133-e052-4969-ad0a-a0e178114040.png?raw=true) 

We can use the following equation to understand how phase-flip error detection for a qubit is implemented.
$$
|- +\rangle \xrightarrow{expand} 
\frac{1}{2}(|00\rangle + |01\rangle - |10\rangle - |11\rangle) 
\xrightarrow{cx(0, 1)} \frac{1}{2}(|00\rangle + |11\rangle - |10\rangle - |01\rangle)
\xrightarrow{reorder} |- -\rangle
$$

It can be seen that, through the CX gate, the $ |-\rangle $ state from the detected position is propagated to the qubit at the detection location. This achieves a similar effect to the bit-flip error detection described earlier.

+ Similar to the bit-flip circuit, apply the corrective $ Z $-gate to the qubit at the position corresponding to the measurement result.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/8778f677-f027-47f7-bb13-c9f1d7ca98b1.png?raw=true)

+ Finally, decode the physically encoded qubits back to their original states using the Hadamard gate.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/7e19b997-e7cd-4c9c-abd0-7f162172c633.png?raw=true)

## Test

Experiment to check if the circuit works properly. Since Hadamard gates are used for encoding and decoding, any phase-flip that occurs in the middle will be converted into a bit-flip, which can then be detected through measurement.

{%gi 6 2-2-2%}

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/9cc9d438-c438-46ee-a2a5-3921aa4b18aa.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/c6e37afa-84e2-4ae7-9bdb-c88a9406de39.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/523bc553-fbe9-4f6c-97b6-edac3cf5d0c3.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

{%endgi%}

As shown in the diagram, if there is a phase-flip error on one of the three physical qubits, it can be corrected.

## Remain Problem

So far, circuits have been provided to handle both bit-flip and phase-flip errors, but the common issue is that each circuit can only handle one type of error and cannot correct the other type. The following will introduce a complete solution to handle both types of errors.

# Shor Code

If both bit-flip and phase-flip errors occur simultaneously on a logical qubit, the solution is naturally to nest both error-correction circuits. Specifically, the three physical qubits in the phase-flip correction circuit are each represented by three bit-flip correction circuits. Of course, the reverse can also be done. 

This approach results in the Shor Code, which can simultaneously handle both bit-flip and phase-flip errors.

## Correction Circuit

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/2e3b9efd-0e2e-4f8f-8113-b061bec9f8c3.png?raw=true)

## Accuracy Calculate

The accuracy of the Shor Code is the same as the previous two error-correction circuits, as it can correct any single bit-flip or phase-flip. Therefore, its error rate is given by:

$$1 - (1 - p) ^ 9 - 9 * p * (1 - p) ^ 8$$

where $p$ is the probability of a single physical qubit experiencing a bit-flip or phase-flip.

 ![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/image_2025-01-13_10-29-18.png?raw=true)

As seen in the graph, when $ p < 0.0323 $, the Shor Code performs better than a single physical qubit.
