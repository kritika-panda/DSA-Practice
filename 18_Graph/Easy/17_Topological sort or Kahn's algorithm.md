# Topological Sort (Kahn's Algorithm)

## Problem Statement

Given a Directed Acyclic Graph (DAG) with `V` vertices and `E` edges, return an array containing a **Topological Sort** of the vertices.

A **Topological Sort** of a directed graph is a linear ordering of its vertices such that for every directed edge `u → v`, vertex `u` comes before vertex `v` in the ordering. If the graph contains any cycles, a topological sort is mathematically impossible.

The graph is represented using an adjacency list format where `adj[i]` contains a list of all vertices that node `i` points to.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges)*

---

## Example 1

### Input

```text
V = 4, E = 3
adj = [
  0 ->,
  1 ->,
  2 ->,
  3 -> []
]
```

### Output

```text
[0, 1, 2, 3]
```

### Explanation

```text
The linear dependencies dictate:
- 0 must come before 1 and 2.
- 1 and 2 must come before 3.
Valid orderings include [0, 1, 2, 3] or.
```

---

## Example 2

### Input

```text
V = 3, E = 2
adj = [
  0 ->,
  1 ->,
  2 -> []
]
```

### Output

```text
[0, 1, 2]
```

---

# Intuition

**Kahn's Algorithm** solves topological sorting using a **Breadth-First Search (BFS)** strategy driven by tracking node **In-Degrees** (the number of incoming edges pointing to a node).

The core philosophy revolves around a simple rule: **If a node has an in-degree of 0, it has no remaining dependencies and can be processed immediately.**

1. **In-Degree Setup:** Calculate the initial in-degree configuration for every vertex across the entire graph.
2. **Seed the Queue:** Find all vertices with an initial `inDegree == 0` and push them onto a BFS queue. These serve as our baseline starting points.
3. **Dependency Pruning Loop:** While the queue is active, pop a node `curr` and append it directly to our topological results array. For each neighbor that `curr` points to:
   * Decrement its incoming dependency counter: `inDegree[neighbor]--`.
   * **The Zero-Dependency Trigger:** If the neighbor's in-degree drops cleanly to `0`, it means all of its prerequisite parent components have been fully processed. Push it straight onto our queue.
4. **Cycle Detection Check:** If our finished result list contains fewer than `V` vertices, it means the remaining nodes were trapped in a cycle (where in-degrees could never fall to 0). In this scenario, return an empty array or flag a cycle.

---

# State Definition

Let:

```text
int[] inDegree = new int[V];               // Tracks active incoming dependencies per vertex
Queue<Integer> queue = new LinkedList<>(); // Holds nodes with 0 remaining constraints
int[] topoOrder = new int[V];              // Array capturing the final sorted sequence
```

---

# Recurrence / Pointer Transition Condition

```text
For each neighbor of curr popped from the queue:
    inDegree[neighbor]--
    If inDegree[neighbor] == 0:
        queue.offer(neighbor)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By pre-calculating the dependency array boundaries and processing elements using a linear FIFO queue sweep, each node and edge connection is processed exactly once. This resolves all scheduling priorities cleanly within an optimal **O(V + E)** timeline.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int[] topoSort(int V, ArrayList<ArrayList<Integer>> adj) {
        int[] inDegree = new int[V];
        
        // Step 1: Compute the initial in-degree boundaries for all vertices
        for (int u = 0; i < V; u++) {
            for (int v : adj.get(u)) {
                inDegree[v]++;
            }
        }

        // Step 2: Seed the BFS queue with all nodes containing 0 initial prerequisites
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] topoOrder = new int[V];
        int index = 0;

        // Step 3: Process the queue and structurally prune dependencies
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            topoOrder[index++] = curr; // Record node into the final sequence tracking frame

            // Iterate through outgoing neighboring connections
            for (int neighbor : adj.get(curr)) {
                inDegree[neighbor]--; // Decrement prerequisite constraint
                
                // If all parent constraints are cleared, unlock the neighbor node
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 4: Verify if a cycle truncated the graph completion pass
        if (index != V) {
            return new int[0]; // Empty array flags an invalid cyclical graph condition
        }

        return topoOrder;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of vertices and E is the number of edges. Initial in-degree counting takes O(V + E) time, and the queue processing visits each node and decrements each edge reference exactly once.
Space Complexity: O(V)     -> Storage allocations used for the in-degree tracking array, the BFS queue buffer, and the final output array.
```

---

# Dry Run

## Input

```text
V = 3, adj = [0 ->, 1 ->, 2 -> []]
```

---

### Execution Trace

```text
1. Compute In-Degrees: inDegree = [0, 1, 1]
2. Seed Queue: Node 0 has inDegree == 0 -> queue = [0]
3. Loop 1:
   - Pop 0 -> topoOrder[0] = 0. Index = 1.
   - Neighbor 1: inDegree[1] drops from 1 to 0 -> queue = [1]
4. Loop 2:
   - Pop 1 -> topoOrder[1] = 1. Index = 2.
   - Neighbor 2: inDegree[2] drops from 1 to 0 -> queue = [2]
5. Loop 3:
   - Pop 2 -> topoOrder[2] = 2. Index = 3.
   - No neighbors. Queue empty.

Returns [0, 1, 2] (Valid topological ordering)
```

---

# Visualization

## In-Degree Dependency Reduction Track

```text
  [In: 0]           [In: 1]           [In: 1]
    (0) ─────────────► (1) ───────────► (2)
     │                  │
   Popped             Unlocked
  [In: 0]            [In: 0]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Topological sequence compilation utilizing multi-source in-degree counting across a linear BFS pipeline.
```

---

## Choices

```text
Eject dependency counts across neighboring vertices. Push nodes into the active stream the moment constraints reach zero.
```

---

## Key Takeaways

✅ Core fundamental graph sorting algorithm designed specifically for directed acyclic structures.

✅ Kahn's algorithm contains built-in cycle validation safeguards: if the final array size is less than `V`, a loop cycle deadlock is caught.

✅ Standard real-world model used inside **package managers** (such as `npm install` or `pip` resolving package dependency chains), automated **build frameworks** (like `Make` or `Gradle` scheduling tasks), and compiler spreadsheet graph engines.

✅ Common Graph Design Pattern:

```text
Dependency Resolution via Monotonic In-Degree Pruning Loops
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Course Schedule II (LeetCode 210)
- Alien Dictionary (LeetCode 269)
- Minimum Height Trees (LeetCode 310)
- Sequence Reconstruction (LeetCode 444)
