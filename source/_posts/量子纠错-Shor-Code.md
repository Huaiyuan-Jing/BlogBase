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
math: true
---
量子纠错算法是量子计算领域的核心技术之一,旨在应对量子比特(qubits)易受环境干扰导致信息丢失或出错的问题。由于量子系统的特性, 如叠加态和量子纠缠, 使得量子信息极其脆弱, 任何微小的噪声或相位偏移都可能破坏计算的准确性。量子纠错通过设计特殊的编码方案, 将一个量子比特的信息分布到多个物理量子比特上, 从而检测和纠正错误, 而不会破坏量子态。例如, 经典的量子纠错码包括肖尔码 (Shor Code), 斯坦恩码 (Steane Code) 和表面码 (Surface Code). 这些算法能够识别和修复比特翻转 (bit-flip) 错误、相位翻转 (phase-flip) 错误或两者同时发生的错误, 为构建可靠的大规模量子计算机提供了坚实的基础。这篇文章将介绍Shor Code的架构.

# Bit Flip

bit flip error (比特翻转错误) 是最常见的一种错误类型，它指的是一个量子比特的状态意外从 $|0\rangle$ 翻转为 $|1\rangle$，或者从 $|1\rangle$ 翻转为 $|0\rangle$。这种错误类似于经典计算中比特的翻转，但在量子计算中需要通过纠错编码来检测和修正，以保护量子信息的完整性。

## Correction Circuit

+ 首先通过cx将一个逻辑比特用三个物理比特表示 (cx为control-x, 效果为如果控制位的qubit的值为1, 则翻转对应的qubit, 在下图中表现为如果q0为$|1\rangle$, 对应翻转q1和q2的值)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/9fafab4b-f7f8-4815-8500-599576ae1f1f.png?raw=true)

+ 假设三个逻辑比特中的一个出现了比特翻转错误

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/45b58c05-d21f-4a36-9f33-671196ff3b3a.png?raw=true)

+ 通过cx检查两两相邻的qubit的奇偶性

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d7264dce-dfce-4b61-b271-6fa0cbc19d94.png?raw=true)

+ 这样根据q3,q4的值,就可以推导出比特翻转发生的位置: 

| q3q4 |  Err  |
|------|-------|
|  00  |  N/A  |
|  10  |  q0   |
|  11  |  q1   |
|  01  |  q2   |

在上面的案例里, q3q4 measure的结果是11, 也就是说q1出现了比特翻转.

+ 根据对应的编码对指定的位置进行反向翻转, 在上面的案例里就是在q1上应用X门

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/42e3e338-4727-4dd9-ab54-b5114bb509d5.png?raw=true)

## Accuracy Calculate

因为这个最基本的电路只有三个比特, 所以它只能容忍一个比特的翻转错误, 超过一个比特就无法正确的纠错了. 假设一个物理比特的出现翻转错误的概率是p, 也就是说这个电路出现翻转错误的比特的数量~$Binary(3, p)$. 那么这个逻辑比特的出现翻转错误的概率就是$p^3 + 3 * p ^ 2 * (1 - p)$, 即出现三个翻转错误和两个翻转错误的概率之和. 如果绘制出这两个概率的曲线, 可以看到:

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/image_2025-01-08_10-14-29.png?raw=true)

在p < 0.5时, 这个Logical Bit能给我们带来更低的错误率.

## Test

实验一下这个电路能否按照设计的工作, 测试通过在预定位置手动添加X门的方式来模拟bit flip error.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d9c3ff0e-5556-4b29-8740-4df0e9c50cfc.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/76f9a782-3363-4f27-8c8f-763305a4802a.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/e0a0c764-909a-4ff1-8342-99f26597c7ee.png?raw=true)

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/a531af4b-85af-4bab-ad57-b0bc44aa41c1.png?raw=true)

可以看见, 在模拟测试下, q0, q1, q2三个比特中的任意一个比特发生bit flip error并不会影响当前逻辑比特的状态.

# Phase Flip

phase flip error相位反转错误是另一种常见的错误类型, 可以表示为一个量子比特意外被添加了一个Z门 ($Z|0> = |0>, Z|1> = -|1>$).
这种错误用上面的的比特翻转的检测电路无法检测到, 但稍加修改就可以在phase flip error上应用.

## Correction Circuit

+ 与bit flip correction circuit一样, 用三个物理比特表示一个逻辑比特, 并且为每个比特添加一个Hadamard门

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/41ff177e-2348-4be0-b561-467ab134d3f3.png?raw=true)

+ 使用cx检测相邻qubit是否相等, 与bit flip的电路不同的是, 这里对检测的qubit添加了Hadamard门并且翻转了cx的方向.

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/d6146133-e052-4969-ad0a-a0e178114040.png?raw=true) 

我们可以用下面这个式子来理解对于一个比特的phase flip error检测是如何实现的
$$
|-+\rangle \xrightarrow{expand} 
|00\rangle + |01\rangle - |10\rangle - |11\rangle 
\xrightarrow{cx(0, 1)} |00\rangle + |11\rangle - |10\rangle - |01\rangle
\xrightarrow{reorder} |- -\rangle
$$

可以看见, 通过cx门, 被检测位置的$|-\rangle$被传播到了检测位置的比特上, 这样就实现了与上面bit flip检测类似的效果

+ 在measure结果对应的位置的比特上应用修复的Z门

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/8778f677-f027-47f7-bb13-c9f1d7ca98b1.png?raw=true)

+ 最后把用Hadamard门转换的物理比特解码为原本的状态

![](https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/%E9%87%8F%E5%AD%90%E7%BA%A0%E9%94%99-Shor-Code/7e19b997-e7cd-4c9c-abd0-7f162172c633.png?raw=true)

## Test

实验电路是否正常工作, 与bit flip circuit不同的是, phase flip circuit把物理比特的初始状态通过Hadamard Gate编译再解码, 这样在中间发生的phase flip将会被转换为bit flip并可以通过measure检测.



## Remain Problem

到目前为止, bit flip error和phase flip error都有对应的电路解决了, 但共有的问题就在于任意一种电路都没法处理另一种类型的错误. 而下面就会介绍完备的解决方案.
