---
title: Network Flow
date: 2025-01-02 11:11:58
tags: 
mathjax: true
---
# 1: Problem Description

As stated, given a network graph with a source node and a sink node, calculate the maximum flow in the network.

## 1.1: Input Format

The first line contains four positive integers: $$n,m,s,t$$, representing the number of nodes, the number of directed edges, the source node index, and the sink node index, respectively.

The following mm lines each contain three positive integers: $u_i,v_i,w_i​$, representing the i-th directed edge starting from uiui​, ending at $v_i$​, with a weight $w_i$​ (indicating the maximum flow capacity of the edge).

## 1.2: Output Format

A single line containing a single positive integer, which is the maximum flow in the network.

## 1.3: Example

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

# 2: Solution 1: Edmonds–Karp Algorithm

1. Find a path from the source ss to the sink tt where all edges have remaining capacity ($capacity−flow>0$). Such a path is called "connected." 
2. Determine the bottleneck capacity along this path as $Augment=min⁡(capacity−flow)$.
3. Add the $Augment$ to the maxflow result
4. Repeat the previous steps until the algorithm cannot find any connected path from s to t

## 2.1: Code (Part)
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
## 2.2: Time Complexity Analysis

+ The time complexity of finding augment path for one time is $O(m)$
+ The upper limit of times of Augument is $O(nm)$ 
+ So the time complexity of this algorithm is $O(nm^2)$

# 3: Solution 2: Dinic Algorithm

The Dinic algorithm works by dividing the graph into layers and repeatedly finding augmenting paths to increase the flow. Here’s the main idea:

1. __BFS Layering:__ First, perform a Breadth-First Search (BFS) to divide the graph into layers. The distance from a node uu to the source ss determines its layer. Remove edges where the flow is equal to or exceeds the capacity to create the "level graph" $G_L=(V,E_L)$.

2. __Blocking Flow:__ In the level graph $G_L$​, find a maximal flow that cannot be further augmented. This is called the blocking flow.

3. __Repeat:__ Continue finding and adding blocking flows to the total flow until no augmenting path exists between the source s and the sink t.

## 3.1: Key Optimization

While performing DFS to find blocking flows:

+ If you encounter a node u where all outgoing edges are already saturated, skip u.

+ Maintain a pointer for each node uu to keep track of which edge to explore next. This prevents revisiting edges unnecessarily and improves efficiency.

## 3.2: Code (Part)

```rust
#[derive(Clone)]
struct Edge {
    to: usize,
    nxt: usize,
    cap: i32,
    flow: i32,
}
struct MaxFlow {
    s: usize,
    t: usize,
    head: Vec<usize>,
    edge: Vec<Edge>,
    maxflow: isize,
    depth: Vec<usize>,
}
impl MaxFlow {
    /// Creates a new `MaxFlow` instance with `n` nodes, source node `s`, and sink node `t`.
    pub fn new(n: usize, s: usize, t: usize) -> Self {
        Self {
            s,
            t,
            head: vec![0x3f3f3f3f; n],
            edge: vec![],
            maxflow: 0,
            depth: vec![0; n],
        }
    }
    
    /// Adds a directed edge from `from` to `to` with capacity `cap` to the flow network.
    ///
    /// # Arguments
    ///
    /// `from`: The node from which the edge originates.
    /// `to`: The node to which the edge points.
    /// `cap`: The capacity of the edge.
    pub fn add_edge(&mut self, from: usize, to: usize, cap: i32) {
        let m = self.edge.len();
        self.edge.push(Edge {
            to,
            nxt: self.head[from],
            cap,
            flow: 0,
        });
        self.head[from] = m;
        self.edge.push(Edge {
            to: from,
            nxt: self.head[to],
            cap: 0,
            flow: 0,
        });
        self.head[to] = m + 1;
    }
    /// Performs a BFS traversal of the residual graph to mark static nodes and find a shortest path from the source `s` to the sink `t`.
    ///
    /// Returns `true` if a path is found, and `false` otherwise.
    fn bfs(&mut self) -> bool {
        let mut q = std::collections::VecDeque::new();
        q.push_back(self.s);
        self.depth.fill(0x3f3f3f3f);
        self.depth[self.s] = 0;
        while !q.is_empty() {
            let u = q.pop_front().unwrap();
            let mut e = self.head[u];
            while e != 0x3f3f3f3f {
                let v = self.edge[e].to;
                if self.edge[e].flow < self.edge[e].cap && self.depth[v] > self.depth[u] + 1 {
                    self.depth[v] = self.depth[u] + 1;
                    q.push_back(v);
                }
                e = self.edge[e].nxt;
            }
        }
        self.depth[self.t] != 0x3f3f3f3f
    }
    /// Performs a DFS traversal of the residual graph to find a path from node `u` to the sink `t`.
    ///
    /// Returns the maximum flow that can be pushed along the path.
    ///
    /// # Arguments
    ///
    /// `u`: The starting node of the path.
    /// `flow`: The maximum flow to be pushed along the path.
    /// `edges`: The vector of edges in the residual graph.
    /// `cur`: The vector of current edges for each node in the residual graph.
    fn dfs(&self, u: usize, flow: i32, edges: &mut Vec<Edge>, cur: &mut Vec<usize>) -> i32 {
        if u == self.t || flow == 0 {
            return flow;
        }
        let mut res = 0;
        let mut e = cur[u];
        while e != 0x3f3f3f3f {
            let v = edges[e].to;
            if self.depth[v] == self.depth[u] + 1 {
                let f = self.dfs(
                    v,
                    std::cmp::min(flow - res, edges[e].cap - edges[e].flow),
                    edges,
                    cur,
                );
                edges[e].flow += f;
                edges[e ^ 1].flow -= f;
                res += f;
                if res == flow {
                    break;
                }
            }
            e = self.edge[e].nxt;
            cur[u] = e;
        }
        res
    }
    /// Computes the maximum flow in the flow network using the Dinic's algorithm.
    ///
    /// This function repeatedly calls `bfs` to find a shortest augmenting path
    /// and `dfs` to augment the flow along the path. The maximum flow is
    /// accumulated in `maxflow` field.
    pub fn dinic(&mut self) {
        while self.bfs() {
            let mut cur = self.head.clone();
            let mut edge = self.edge.clone();
            self.maxflow += self.dfs(self.s, std::i32::MAX, &mut edge, &mut cur) as isize;
            self.edge = edge;
        }
    }
}
```
# 4: Soluiton 3: ISAP Algorithmn

1. Perform a BFS from t to s and mark the depth (why it's from t to s will be explained later).

2. Perform a DFS from s to t, similar to Dinic's algorithm. However, after finishing processing a node, if the flow passed from the previous node exceeds the used capacity of the current node (for the current depth), the current node is considered "wasted" for the remaining path. In this case, increment its depth by 1. If a gap (a depth level with no nodes) appears, terminate the algorithm.

3. If step 2 does not terminate the algorithm, repeat step 2.

## 4.1: Optimization:

    Compare to Dinic Algorithm, ISAP doesnt require to run bfs several times.

## 4.2: Code (Part)
```rust
mod maxflow {
    #[derive(Clone)]
    struct Edge {
        to: usize,
        nxt: usize,
        cap: i32,
        flow: i32,
    }
    pub struct Graph {
        n: usize,
        s: usize,
        t: usize,
        head: Vec<usize>,
        edge: Vec<Edge>,
        pub maxflow: isize,
        depth: Vec<usize>,
        gap: Vec<usize>,
    }
    impl Graph {
        /// Creates a new `MaxFlow` instance with `n` nodes, source node `s`, and sink node `t`.
        pub fn new(n: usize, s: usize, t: usize) -> Self {
            Self {
                n,
                s,
                t,
                head: vec![0x3f3f3f3f; n],
                edge: vec![],
                maxflow: 0,
                depth: vec![0x3f3f3f3f; n],
                gap: vec![0; n * 2],
            }
        }
    }
    /// Adds a directed edge from `from` to `to` with capacity `cap` to the flow network.
    ///
    /// # Arguments
    ///
    /// `from`: The node from which the edge originates.
    /// `to`: The node to which the edge points.
    /// `cap`: The capacity of the edge.
    pub fn add_edge(g: &mut Graph, from: usize, to: usize, cap: i32) {
        let m = g.edge.len();
        g.edge.push(Edge {
            to,
            nxt: g.head[from],
            cap,
            flow: 0,
        });
        g.head[from] = m;
        g.edge.push(Edge {
            to: from,
            nxt: g.head[to],
            cap: 0,
            flow: 0,
        });
        g.head[to] = m + 1;
    }
    /// Performs a BFS traversal of the residual graph to mark static nodes and find a shortest path from the source `s` to the sink `t`.
    ///
    /// Returns `true` if a path is found, and `false` otherwise.
    fn generate_depth(g: &mut Graph) {
        g.depth = vec![0x3f3f3f3f; g.n];
        let mut q = std::collections::VecDeque::new();
        q.push_back(g.t);
        g.depth[g.t] = 0;
        g.gap[0] = 1;
        while !q.is_empty() {
            let u = q.pop_front().unwrap();
            let mut e = g.head[u];
            while e != 0x3f3f3f3f {
                let v = g.edge[e].to;
                if g.depth[v] != 0x3f3f3f3f {
                    e = g.edge[e].nxt;
                    continue;
                }
                g.depth[v] = g.depth[u] + 1;
                g.gap[g.depth[v]] += 1;
                q.push_back(v);
                e = g.edge[e].nxt;
            }
        }
    }
    /// Performs a DFS traversal of the residual graph to find a path from node `u` to the sink `t`.
    ///
    /// Returns the maximum flow that can be pushed along the path.
    ///
    /// # Arguments
    ///
    /// `u`: The starting node of the path.
    /// `flow`: The maximum flow to be pushed along the path.
    /// `edges`: The vector of edges in the residual graph.
    /// `cur`: The vector of current edges for each node in the residual graph.
    fn dfs(g: &mut Graph, u: usize, flow: i32, cur: &mut Vec<usize>) -> i32 {
        if u == g.t || flow == 0 {
            return flow;
        }
        let mut used = 0;
        let mut e = cur[u];
        while e != 0x3f3f3f3f {
            let v = g.edge[e].to;
            if g.depth[v] == g.depth[u] - 1 {
                let f = dfs(
                    g,
                    v,
                    std::cmp::min(flow - used, g.edge[e].cap - g.edge[e].flow),
                    cur,
                );
                g.edge[e].flow += f;
                g.edge[e ^ 1].flow -= f;
                used += f;
                if used == flow {
                    return used;
                }
            }
            e = g.edge[e].nxt;
            cur[u] = e;
        }
        g.gap[g.depth[u]] -= 1;
        if g.gap[g.depth[u]] == 0 {
            g.depth[g.s] = g.n + 1;
        }
        g.depth[u] += 1;
        g.gap[g.depth[u]] += 1;
        used
    }
    /// Computes the maximum flow in the flow network using the Dinic's algorithm.
    ///
    /// This function repeatedly calls `bfs` to find a shortest augmenting path
    /// and `dfs` to augment the flow along the path. The maximum flow is
    /// accumulated in `maxflow` field.
    pub fn isap(g: &mut Graph) {
        generate_depth(g);
        while g.depth[g.s] < g.n {
            let mut cur = g.head.clone();
            g.maxflow += dfs(g, g.s, std::i32::MAX, &mut cur) as isize;
        }
    }
}
```
