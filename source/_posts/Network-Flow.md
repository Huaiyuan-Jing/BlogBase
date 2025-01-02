---
title: Network Flow
date: 2025-01-02 11:11:58
tags: 
mathjax: true
---
# 1 Problem Description

As stated, given a network graph with a source node and a sink node, calculate the maximum flow in the network.

## 1.1 Input Format

The first line contains four positive integers: $$n,m,s,t$$, representing the number of nodes, the number of directed edges, the source node index, and the sink node index, respectively.

The following mm lines each contain three positive integers: $u_i,v_i,w_i​$, representing the i-th directed edge starting from uiui​, ending at $v_i$​, with a weight $w_i$​ (indicating the maximum flow capacity of the edge).

## 1.2 Output Format

A single line containing a single positive integer, which is the maximum flow in the network.

## 1.3 Example

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

# 2 Solution 1: Edmonds–Karp Algorithm

1. Find a path from the source ss to the sink tt where all edges have remaining capacity ($capacity−flow>0$). Such a path is called "connected." 
2. Determine the bottleneck capacity along this path as $Augment=min⁡(capacity−flow)$.
3. Add the $Augment$ to the maxflow result
4. Repeat the previous steps until the algorithm cannot find any connected path from s to t

## 2.1 Code (Part)
```rust
.....
fn max_flow(&mut self, s: usize, t: usize) -> i32 {
    let mut flow = 0;

    loop {
        // Reset for BFS
        self.a.fill(0);
        let mut queue = VecDeque::new();
        queue.push_back(s);
        self.a[s] = INF;

        // Perform BFS
        while let Some(x) = queue.pop_front() {
            if let Some(edges) = self.graph.get(&x) {
                for &i in edges {
                    let edge = &self.edges[i];
                    if self.a[edge.to] == 0 && edge.cap > edge.flow {
                        self.p[edge.to] = Some(i);
                        self.a[edge.to] = min(self.a[x], edge.cap - edge.flow);
                        queue.push_back(edge.to);
                        if edge.to == t {
                            break;
                        }
                    }
                }
            }
        }

        if self.a[t] == 0 {
            break; // No more augmenting path found
        }

        // Augment flow along the path
        let mut u = t;
        while let Some(edge_index) = self.p[u] {
            let edge = &mut self.edges[edge_index];
            edge.flow += self.a[t];
            let reverse_edge_index = edge_index ^ 1;
            self.edges[reverse_edge_index].flow -= self.a[t];
            u = edge.from;
        }

        flow += self.a[t];
    }

    flow
}
.....
```
## 2.2 Time Complexity Analysis

+ The time complexity of finding augment path for one time is $O(m)$
+ The upper limit of times of Augument is $O(nm)$ 
+ So the time complexity of this algorithm is $O(nm^2)$

# 3 Solution 2: Dinic Algorithm

The Dinic algorithm works by dividing the graph into layers and repeatedly finding augmenting paths to increase the flow. Here’s the main idea:

1. __BFS Layering:__ First, perform a Breadth-First Search (BFS) to divide the graph into layers. The distance from a node uu to the source ss determines its layer. Remove edges where the flow is equal to or exceeds the capacity to create the "level graph" $G_L=(V,E_L)$.

2. __Blocking Flow:__ In the level graph $G_L$​, find a maximal flow that cannot be further augmented. This is called the blocking flow.

3. __Repeat:__ Continue finding and adding blocking flows to the total flow until no augmenting path exists between the source s and the sink t.

## 3.1 Key Optimization

While performing DFS to find blocking flows:

+ If you encounter a node u where all outgoing edges are already saturated, skip u.

+ Maintain a pointer for each node uu to keep track of which edge to explore next. This prevents revisiting edges unnecessarily and improves efficiency.
