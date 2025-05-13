---
title: 'FTQC量子资源计数器'
tags: [
  Quantum Computing
]
index_img:
banner:
math: true
---

# 为什么需要FTQC资源计数器

在NISQ(Noisy Intermediate-scale Quantum)设备上，衡量一个算法消耗的资源是非常直观的。通过算法使用的门的数量，物理比特的数量，电路的深度，物理硬件的各种错误率与保真度就可以大致的评估一个算法。但在FTQC(Fault Tolerant Quantum Computing)上事情却复杂一些。最关键的不同在于FTQC使用纠错码的方式通过设置更多冗余的物理比特来降低错误率，具体的细节可以参考之前的博文。