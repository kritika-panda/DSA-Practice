# Find the MST Weight

## Problem Statement

Given a connected, weighted, undirected graph with `V` vertices and `E` edges, find the **sum of weights of the Minimum Spanning Tree (MST)**.

---

## Example

```text
      2
  0 ------ 1
  |      / |
6 |    /   | 3
  |  /     |
  |/       |
  2 ------ 3
      1
```

Edges:

```text
0-1 = 2
0-2 = 6
1-2 = 3
1-3 = 8
2-3 = 1
```

---

### MST

Choose:

```text
2-3 = 1
0-1 = 2
1-2 = 3
```

Total Weight:

```text
1 + 2 + 3 = 6
```

Answer:

```text
6
```

---

# Intuition

The problem asks:

```text
Connect all vertices
with minimum total edge weight.
```

This is the exact definition of:

```text
Minimum Spanning Tree (MST)
```

The two famous approaches are:

```text
1. Prim's Algorithm
2. Kruskal's Algorithm
```

For finding only the MST weight, Prim's Algorithm is commonly used.

---

# Prim's Algorithm

## Idea

Start from any node.

Repeatedly choose:

```text
Minimum weight edge
that connects an unvisited node.
```

Similar to Dijkstra's Algorithm.

---

# Approach

Maintain:

```java
PriorityQueue<int[]>
```

Each element:

```text
(weight, node)
```

---

### Initially

```text
Node 0
Weight = 0
```

Insert:

```text
(0,0)
```

---

### While PQ is not Empty

Take minimum weight edge.

If node already visited:

```text
Skip
```

Else:

```text
Add weight to MST
Mark node visited
Push all neighbors
```

---

# Dry Run

Graph:

```text
0 --2-- 1
|      / |
6    3   8
|  /     |
2 --1-- 3
```

---

### Start

```text
PQ = [(0,0)]

MST Weight = 0
```

---

### Pick Node 0

```text
Weight += 0
```

Push:

```text
(2,1)
(6,2)
```

---

### Pick Node 1

```text
Weight += 2
```

MST:

```text
2
```

Push:

```text
(3,2)
(8,3)
```

---

### Pick Node 2

```text
Weight += 3
```

MST:

```text
5
```

Push:

```text
(1,3)
```

---

### Pick Node 3

```text
Weight += 1
```

Final MST Weight:

```text
6
```

---

# Java Solution (Prim's Algorithm)

```java
import java.util.*;

class Solution {

    static class Pair {

        int weight;
        int node;

        Pair(int weight, int node) {
            this.weight = weight;
            this.node = node;
        }
    }

    static int spanningTree(int V,
                            ArrayList<ArrayList<ArrayList<Integer>>> adj) {

        PriorityQueue<Pair> pq =
                new PriorityQueue<>(
                        (a, b) -> a.weight - b.weight
                );

        boolean[] visited = new boolean[V];

        pq.offer(new Pair(0, 0));

        int mstWeight = 0;

        while (!pq.isEmpty()) {

            Pair curr = pq.poll();

            int wt = curr.weight;
            int node = curr.node;

            if (visited[node])
                continue;

            visited[node] = true;

            mstWeight += wt;

            for (ArrayList<Integer> edge : adj.get(node)) {

                int adjNode = edge.get(0);
                int edgeWeight = edge.get(1);

                if (!visited[adjNode]) {

                    pq.offer(
                        new Pair(
                                edgeWeight,
                                adjNode
                        )
                    );
                }
            }
        }

        return mstWeight;
    }
}
```

---

# Complexity Analysis

Let:

```text
V = Vertices
E = Edges
```

### Time Complexity

For every edge insertion:

```text
O(log V)
```

Total:

```text
O(E log V)
```

---

### Space Complexity

Visited Array:

```text
O(V)
```

Priority Queue:

```text
O(E)
```

Overall:

```text
O(V + E)
```

---

# Why Does Prim's Work?

At every step:

```text
Choose the minimum edge
that connects MST
to an unvisited node.
```

Due to the **Cut Property** of MST:

```text
The minimum edge crossing any cut
always belongs to the MST.
```

Hence Prim's Algorithm is guaranteed to produce the Minimum Spanning Tree.

---

# Alternative: Kruskal's Algorithm

Steps:

```text
1. Sort all edges.
2. Pick smallest edge.
3. Use DSU to avoid cycles.
4. Continue until V-1 edges chosen.
```

Complexity:

```text
O(E log E)
```

---

# Interview Takeaways

### Recognition Pattern

```text
Connect all nodes
+
Minimum Cost
=
MST
```

### Algorithms

```text
Prim's Algorithm
Kruskal's Algorithm
```

### Most Asked GFG Problem

```text
Minimum Spanning Tree
(GFG)
```

Expected Solution:

```text
Prim's Algorithm using Priority Queue
```

### Complexity

```text
Time  : O(E log V)

Space : O(V + E)
```
