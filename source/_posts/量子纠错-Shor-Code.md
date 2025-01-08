---
title: '量子纠错: Shor Code' 
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
---
量子纠错算法是量子计算领域的核心技术之一，旨在应对量子比特（qubits）易受环境干扰导致信息丢失或出错的问题。由于量子系统的特性，如叠加态和量子纠缠，使得量子信息极其脆弱，任何微小的噪声或相位偏移都可能破坏计算的准确性。量子纠错通过设计特殊的编码方案，将一个量子比特的信息分布到多个物理量子比特上，从而检测和纠正错误，而不会破坏量子态。例如，经典的量子纠错码包括肖尔码（Shor Code）、斯坦恩码（Steane Code）和表面码（Surface Code）。这些算法能够识别和修复比特翻转（bit-flip）错误、相位翻转（phase-flip）错误或两者同时发生的错误，为构建可靠的大规模量子计算机提供了坚实的基础。这篇文章将介绍Shor Code的架构.

# Bit Flip

bit flip error（比特翻转错误）是最常见的一种错误类型，它指的是一个量子比特的状态意外从 ∣0⟩ 翻转为 ∣1⟩，或者从 ∣1⟩ 翻转为 ∣0⟩。这种错误类似于经典计算中比特的翻转，但在量子计算中需要通过纠错编码来检测和修正，以保护量子信息的完整性。

## Correction Circuit

+ 首先将一个逻辑比特用三个物理比特表示

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/45b58c05-d21f-4a36-9f33-671196ff3b3a.png?raw=true)

+ 三个逻辑比特中的一个出现了比特翻转错误

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/45b58c05-d21f-4a36-9f33-671196ff3b3a.png?raw=true)

+ 通过cx检查两两相邻的qubit的奇偶性,
