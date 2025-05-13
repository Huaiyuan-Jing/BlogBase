---
title: 'FTQC量子资源计数器'
tags: [
  Quantum Computing
]
index_img: https://github.com/Huaiyuan-Jing/BlogBase/blob/main/source/_posts/FTQC%E9%87%8F%E5%AD%90%E8%B5%84%E6%BA%90%E8%AE%A1%E6%95%B0%E5%99%A8/ChatGPT%20Image%202025%E5%B9%B45%E6%9C%8813%E6%97%A5%2015_26_54.png?raw=true
banner:
math: true
---

# 为什么需要FTQC资源计数器

在NISQ(Noisy Intermediate-scale Quantum)设备上，衡量一个算法消耗的资源是非常直观的。通过算法使用的门的数量，物理比特的数量，电路的深度，物理硬件的各种错误率与保真度就可以大致的评估一个算法。但在FTQC(Fault Tolerant Quantum Computing)上事情却复杂一些。最关键的不同在于FTQC使用纠错码的方式通过设置更多冗余的物理比特来降低错误率，具体的细节可以参考之前的博文。

但对应的，在FTQC上，量子电路使用的比特数量是编码后的逻辑比特的数量而非真实的物理比特数量。对于每一个逻辑比特，FTQC都要使用数十到数百不定的物理比特来编码它。再加上有一些特殊的操作，比如对于surface code来说是T gate,每一个都需要额外的物理比特来实现。这让FTQC上的资源统计与评估变得没有那么直观。所以在FTQC上，我们需要一套特殊的机制来实现对于算法使用资源的衡量。