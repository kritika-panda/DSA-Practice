# Find Eventual Safe States

## Problem Statement

There is a directed graph of `V` vertices with labels from `0` to `V - 1`. The graph is represented by a 0-indexed 2D integer array `graph` where `graph[i]` is an array of nodes adjacent to vertex `i`, meaning there is a directed edge from vertex `i` to each node in `graph[i]`.

A node is a **terminal node** if there are no outgoing edges. A node is a **safe node** if every possible path starting from that node leads to a terminal node (or another safe node).

Return *an array containing all the **safe nodes** of the graph*. The answer should be sorted in **ascending order**.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges)*

---

## Example 1

### Input

```text
graph = [[1,2],[2,3],[5],[0],[5],[],[]]
```

### Output

```text
[2,4,5,6]
```

### Explanation

```text
The graph illustration:
- Nodes 5 and 6 are terminal nodes since they have no outgoing edges.
- Every path starting at nodes 2, 4, 5, and 6 leads to either node 5 or 6.
- Nodes 0, 1, and 3 are part of a cycle (0 -> 1 -> 3 -> 0), so paths starting from them can loop infinitely.
```

---

## Example 2

### Input

```text
graph = [[1,2,3,4],[1,2],[3,4],[0,4],[]]
```

### Output

```text
[4]
```

### Explanation

```text
Only node 4 is a terminal node, and every path starting from node 4 leads to node 4 itself (since it has no choices). All other nodes can lead into a cycle.
```

---

# Intuition

This problem can be elegantly solved by looking at its inversion. A node is **unsafe** if it is either part of a directed cycle or can reach a directed cycle. Conversely, any node that **cannot reach a cycle** is guaranteed to be a safe node.

We can track down these cycle-free paths efficiently using **DFS with 3-State Graph Coloration**:
*   **State 0 (Unvisited):** The node hasn't been explored yet.
*   **State 1 (Visiting / Unsafe):** The node is actively open on the current recursive DFS path stack. If we look ahead and encounter another node in State 1, we have discovered a back-edge loop cycle! This node is unsafe.
*   **State 2 (Safe / Processed):** The node and all its downstream branches have been fully processed and verified to have zero cyclic paths.

By caching these states directly inside an integer status tracker array, we skip redundant evaluations. If a node is fully validated as State 2, any upstream node pointing to it can trust it as a safe link.

---

# State Definition

Let:

```text
int[] state = new int[V]; // 0 = Unvisited, 1 = Visiting (Unsafe), 2 = Safe
List<Integer> safeNodes = new ArrayList<>(); // Collection to aggregate results
```

---

# Recurrence / State Transition Condition

```text
For each neighbor of current node:
    If state[neighbor] == 1 -> Cycle encountered (current node is unsafe, return false)
    If state[neighbor] == 0 and !dfs(neighbor) -> Downstream path hits a cycle (return false)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By checking nodes through a 3-state tracking array and executing a single DFS sweep across unvisited vertices, each node and edge connection is processed exactly once. This gathers all safe nodes in an optimal **O(V + E)** timeline.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    // State indicators for graph coloring
    private static final int UNVISITED = 0;
    private static final int VISITING = 1;
    private static final int SAFE = 2;

    public List<Integer> eventualSafeNodes(int[][] graph) {
        int v = graph.length;
        int[] state = new int[v];
        List<Integer> result = new ArrayList<>();

        // Step 1: Evaluate every node in the graph sequentially
        for (int i = 0; i < v; i++) {
            if (dfs(i, graph, state)) {
                result.add(i); // Node is safe; add to our sorted list
            }
        }

        return result;
    }

    private boolean dfs(int curr, int[][] graph, int[] state) {
        // If already processed, return true if it was marked completely SAFE
        if (state[curr] != UNVISITED) {
            return state[curr] == SAFE;
        }

        // Mark the current node as VISITING (active on current path)
        state[curr] = VISITING;

        // Step 2: Traverse all outgoing directed paths
        for (int neighbor : graph[curr]) {
            // If neighbor is active on stack (VISITING) or leads to a cycle, curr is unsafe
            if (state[neighbor] == VISITING || !dfs(neighbor, graph, state)) {
                return false; 
            }
        }

        // Step 3: All paths from this node are clean; upgrade state to SAFE
        state[curr] = SAFE;
        return true;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> The DFS process visits every vertex and traverses each directed edge at most once due to state caching memoization.
Space Complexity: O(V)     -> Memory allocated for the state tracker array and the maximum recursion stack depth frames.
```

---

# Dry Run

## Input

```text
graph = [[1], [2], [0, 3], []] // Cycle: 0 -> 1 -> 2 -> 0, and 2 -> 3 (Terminal)
```

---

### DFS Execution Trace

```text
1. i = 0: state[0] = VISITING. Recurse to 1 -> state[1] = VISITING. Recurse to 2 -> state[2] = VISITING.
2. At node 2, check neighbor 0: state[0] == VISITING -> Cycle Detected!
   - dfs(2) returns false. state[2] stays VISITING (implicitly marking it unsafe).
   - dfs(1) returns false. state[1] stays VISITING.
   - dfs(0) returns false. state[0] stays VISITING.
3. i = 1, i = 2: state != UNVISITED, returns false immediately.
4. i = 3: state[3] = VISITING. No neighbors (terminal!). 
   - Loop completes -> state[3] updates to SAFE. Returns true. Add 3 to result.

Final sorted result array: [3]
```

---

# Visualization

## Cycle Extraction Layer

```text
   (0) ──► (1) ──► (2) ──► (3) [Terminal / Safe]
    ▲               │
    └───────────────┘
  [Unsafe Cycle Loop Block]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Directed cycle screening utilizing 3-color vertex classifications to filter out reachable unsafe nodes.
```

---

## Choices

```text
Skip processed frames. Flag nodes as unsafe upon hitting an open stack back-edge. Seal safe pathways on clean exits.
```

---

## Key Takeaways

✅ Advanced variation of directed graph cycle detection.

✅ Can also be solved using **Kahn's Algorithm (In-Degree BFS)** by completely reversing the edge directions (`v → u`). Nodes that end up with an out-degree of 0 in the reversed graph can be peeled away safely step-by-step.

✅ Models real-world system engines like **unresolvable circular pointer detection** in garbage collection or tracking safe states in finite state automation networks.

✅ Common Graph Design Pattern:

```text
Terminal State Convergence via 3-Color DFS Memoization
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Course Schedule II (LeetCode 210)
- All Paths from Source Lead to Destination (LeetCode 1059)
- Redundant Connection II (LeetCode 685)
