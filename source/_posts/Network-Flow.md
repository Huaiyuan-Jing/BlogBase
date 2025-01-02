---
title: Network Flow
date: 2025-01-02 11:11:58
tags:
---
# Problem Description

As stated, given a network graph with a source node and a sink node, calculate the maximum flow in the network.

## Input Format

The first line contains four positive integers: $n,m,s,t,$ representing the number of nodes, the number of directed edges, the source node index, and the sink node index, respectively.

The following mm lines each contain three positive integers: $u_i,v_i,w_i​$, representing the ii-th directed edge starting from uiui​, ending at vivi​, with a weight wiwi​ (indicating the maximum flow capacity of the edge).

## Output Format

A single line containing a single positive integer, which is the maximum flow in the network.

## Example

### Input:
```
4 5 4 3
4 2 30
4 3 20
2 3 20
2 1 30
1 3 30
```
### Output
```
50
```

