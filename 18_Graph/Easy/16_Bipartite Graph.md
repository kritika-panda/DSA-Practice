# Is Graph Bipartite?

## Problem Statement

Given an adjacency list `graph` representing an undirected graph with `V` vertices, return `true` *if and only if it is **bipartite***.

A graph is **bipartite** if its vertices can be partitioned into two independent sets, **Set A** and **Set B**, such that every edge in the graph connects a vertex in Set A to a vertex in Set B. In other words, no two adjacent vertices can belong to the same set.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges)*

---

## Example 1

### Input

```text
graph = [[1,3],[0,2],[1,3],[0,2]]
```

### Output

```text
true
```

### Explanation

```text
The graph looks like this:
0 — 1
│   │
3 — 2
We can color the vertices into two colors:
Set A (Color 1): {0, 2}
Set B (Color -1): {1, 3}
Every edge connects a node from Set A to a node from Set B. Thus, it is bipartite.
```

---

## Example 2

### Input

```text
graph = [[1,2,3],[0,2],[0,1,3],[0,2]]
```

### Output

```text
false
```

### Explanation

```text
The graph contains a triangle path: 0 — 1 — 2 — 0. 
It is impossible to partition these 3 vertices into 2 independent sets without two adjacent nodes sharing a set.
```

---

# Intuition

A graph is bipartite if and only if it can be **2-colored** continuously. This means we can color every node using one of two colors (e.g., `1` or `-1`) such that any two adjacent nodes have completely opposite colors.

We can model this using a **Breadth-First Search (BFS)** or **Depth-First Search (DFS)** graph coloring pass:
1. **Color Array Configuration:** Maintain an integer array `color` initialized to `0` for all vertices, indicating they are currently uncolored.
2. **Component Sweep:** Since the graph might be disconnected, we loop through all vertices from `0` to `V - 1`. If we strike an uncolored node (`0`), we initialize its color to `1` and push it onto a BFS queue.
3. **Alternating Propagation:** While the queue is active, we pop a node `curr`. We inspect all of its neighbors:
   * **If a neighbor is uncolored (`0`):** We paint it with the **exact opposite color** of the parent (`-color[curr]`) and push it onto the queue.
   * **If a neighbor is already colored:** We verify if its color matches the parent's color (`color[neighbor] == color[curr]`). If they are identical, our 2-coloring constraint is broken—meaning the graph contains an odd-length cycle and is **not bipartite**.
4. If the entire graph is colored successfully without a collision, it is bipartite.

---

# State Definition

Let:

```text
int[] color = new int[V]; // State array: 0 = Uncolored, 1 = Color A, -1 = Color B
Queue<Integer> queue = new LinkedList<>(); // BFS queue driving the propagation
```

---

# Recurrence / Expansion Condition

```text
For each neighbor of curr:
    If color[neighbor] == 0:
        color[neighbor] = -color[curr]
        queue.offer(neighbor)
    Else if color[neighbor] == color[curr]:
        Collision detected! (Return false)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By processing nodes through a queue-driven BFS and validating color states in-place, each vertex and edge is evaluated a constant number of times. This checks the bipartite property inside a highly optimized **O(V + E)** timeline.

---

## Java Code

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public boolean isBipartite(int[][] graph) {
        int v = graph.length;
        
        // Track states: 0 = uncolored, 1 = Color A, -1 = Color B
        int[] color = new int[v];

        // Step 1: Handle potential disconnected sub-graphs by sweeping all vertices
        for (int i = 0; i < v; i++) {
            if (color[i] == 0) {
                
                // Launch BFS step
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                color[i] = 1; // Assign initial default seed color

                // Step 2: Propagate alternating colors across the component
                while (!queue.isEmpty()) {
                    int curr = queue.poll();

                    for (int neighbor : graph[curr]) {
                        // Case A: Neighbor has not been colored yet -> Paint with inverted color
                        if (color[neighbor] == 0) {
                            color[neighbor] = -color[curr];
                            queue.offer(neighbor);
                        } 
                        // Case B: Neighbor shares the same color as current node -> Collision!
                        else if (color[neighbor] == color[curr]) {
                            return false; 
                        }
                    }
                }
            }
        }

        return true;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> The BFS visits every vertex exactly once and scans all edge configurations across adjacency records once.
Space Complexity: O(V)     -> Storage required for the color state matrix array and the tracking memory footprint of the BFS queue.
```

---

# Dry Run

## Input

```text
graph = [[1,3],[0,2],[1,3],[0,2]]
```

---

### BFS Coloration Trace

```text
1. i = 0 -> uncolored. queue =, color[0] = 1.
2. Pop 0: neighbors are 1 and 3.
   - color[1] == 0 -> color[1] = -color[0] = -1. queue = [1]
   - color[3] == 0 -> color[3] = -color[0] = -1. queue = [1, 3]
3. Pop 1: neighbors are 0 and 2.
   - color[0] (1) != color[1] (-1) -> Valid.
   - color[2] == 0 -> color[2] = -color[1] = 1. queue = [3, 2]
4. Pop 3: neighbors are 0 and 2.
   - color[0] (1) != color[3] (-1) -> Valid.
   - color[2] (1) != color[3] (-1) -> Valid.
5. Pop 2: neighbors checked safely. Queue empties cleanly.

Returns true.
```

---

# Visualization

## 2-Color Alternating Graph Map

```text
   (0) [Color: 1] ────────────────── (1) [Color: -1]
         │                                  │
         │                                  │
         │                                  │
   (3) [Color: -1] ───────────────── (2) [Color: 1]
   
   Edges only bridge between alternate color sets. No intra-set links exist.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Graph partition checks utilizing 2-color state validation across BFS/DFS exploration sweeps.
```

---

## Choices

```text
Paint uncolored neighbors with opposite markers. Catch odd-cycle color overlaps to flag failures.
```

---

## Key Takeaways

✅ Core fundamental graph-theory partition analysis model.

✅ A graph is bipartite if and only if it **does not contain any odd-length cycles** (like a triangle or pentagon loop).

✅ Serves as the structural platform used for **matching problems** (such as job assignment matching, stable marriages, or recommendation engines mapping users directly to products).

✅ Common Graph Design Pattern:

```text
Chromatic 2-Color Graph Network Validation via BFS Alternation
```

### Similar Problems

- Possible Bipartition (LeetCode 886)
- Shortest Path to Get All Keys (LeetCode 864)
- Open the Lock (LeetCode 752)
- Clone Graph (LeetCode 133)
