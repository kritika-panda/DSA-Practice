# All Paths from Source Lead to Destination

## Problem Statement

Given the `edges` of a directed graph where `edges[i] = [u, v]` indicates a directed edge from node `u` to node `v`, along with a `source` node and a `destination` node, determine whether **all paths** starting from `source` eventually end at `destination`.

Specifically, return `true` if and only if:
* The graph contains at least one path from `source` to `destination`.
* If a path starts at `source`, it cannot become trapped in an infinite loop (no cycles reachable from `source`).
* Every path starting at `source` must terminate *only* at `destination` (meaning `destination` itself must have an out-degree of 0, and no other reachable node can be a dead end).

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges in the graph)*

---

## Example 1

### Input

```text
n = 3
edges = [[0,1],[0,2]]
source = 0
destination = 2
```

### Output

```text
false
```

### Explanation

```text
Path 1: 0 → 2 (Ends at destination).
Path 2: 0 → 1 (Trapped at node 1, which is a dead end and not the destination).
Therefore, not all paths lead to the destination.
```

---

## Example 2

### Input

```text
n = 4
edges = [[0,1],[0,3],[1,2],[2,1]]
source = 0
destination = 3
```

### Output

```text
false
```

### Explanation

```text
Path 1: 0 → 3 (Valid).
Path 2: 0 → 1 → 2 → 1 → 2 ... (Trapped in an infinite loop cycle between 1 and 2).
Therefore, return false.
```

---

## Example 3

### Input

```text
n = 3
edges = [[0,1],[1,2]]
source = 0
destination = 2
```

### Output

```text
true
```

---

# Intuition

This problem can be effectively solved by using **Depth-First Search (DFS) graph traversal with State Tracking Coloration**. 

To verify if all paths starting from a node reach the destination, we must inspect every outgoing edge. During our recursive walk, there are three primary failure triggers that we must catch:
1. **The Dead-End Failure:** If we hit a node that has no outgoing edges (out-degree == 0), this node **must** be the `destination`. If it is any other node, a path has terminated prematurely at an incorrect location—return `false`.
2. **The Destination Validation:** The `destination` node itself **must not have any outgoing edges**. If it does, a path could pass through the destination and wander off into a dead end or a loop somewhere else.
3. **The Cycle Failure:** If we revisit a node that is currently active on our active recursion stack, we have detected a directed cycle. A path can loop here infinitely and will never terminate—return `false`.

To manage these checks efficiently without repetitive re-evaluation, we can track three color states for each node:
*   **0 (White - Unvisited):** The node has not been explored yet.
*   **1 (Gray - Visiting):** The node is currently active on the recursive DFS call stack. Re-encountering a Gray node flags a cycle immediately.
*   **2 (Black - Fully Processed):** The node and all paths stemming from it have been completely validated as safe and leading to the destination. We can safely short-circuit and return `true` immediately on Black nodes.

---

# State Definition

Let:

```text
List<Integer>[] graph = new ArrayList[n]; // Adjacency list representation
int[] state = new int[n];                 // State array: 0=Unvisited, 1=Visiting, 2=Processed
```

---

# Recurrence / State Transition Condition

```text
For each neighbor of curr:
    If state[neighbor] == 1 -> Cycle detected (return false)
    If state[neighbor] == 0 and !dfs(neighbor) -> Downstream failure (return false)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By wrapping graph components inside an adjacency list format and applying 3-state tracking coloration, each node and edge is examined exactly once. This allows the cycle detection and destination confirmation validations to run in optimal **O(V + E)** time.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    // State Constants for 3-Color Graph Coloring Algorithm
    private static final int UNVISITED = 0;
    private static final int VISITING = 1;
    private static final int PROCESSED = 2;

    public boolean leadsToDestination(int n, int[][] edges, int source, int destination) {
        // Step 1: Build the Adjacency List representing the directed graph environment
        List<Integer>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }
        for (int[] edge : edges) {
            graph[edge[0]].add(edge[1]);
        }

        // Fast-fail: If the destination node has outgoing paths, it cannot be a terminal anchor
        if (!graph[destination].isEmpty()) {
            return false;
        }

        int[] state = new int[n];

        // Step 2: Trigger DFS tracking starting from our designated source node
        return dfs(source, destination, graph, state);
    }

    private boolean dfs(int curr, int dest, List<Integer>[] graph, int[] state) {
        // Condition A: If the node is fully processed and validated before, short-circuit
        if (state[curr] == PROCESSED) {
            return true;
        }

        // Condition B: If the node is currently active on our recursion stack, a cycle is struck
        if (state[curr] == VISITING) {
            return false;
        }

        // Condition C: If we hit a dead end, it must be the destination node itself
        if (graph[curr].isEmpty()) {
            return curr == dest;
        }

        // Mark current node as active on the call stack
        state[curr] = VISITING;

        // Step 3: Recursively evaluate all outgoing neighborhood pathways
        for (int neighbor : graph[curr]) {
            if (!dfs(neighbor, dest, graph, state)) {
                return false; // Downstream tracking failure intercepted
            }
        }

        // Backtrack: Mark the node as completely processed and validated
        state[curr] = PROCESSED;
        return true;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of nodes (vertices) and E is the number of edges. DFS traverses every vertex and edge at most once.
Space Complexity: O(V + E) -> For storing the adjacency list graph structure and the tracking state array array sizes.
```

---

# Dry Run

## Input

```text
n = 3, edges = [[0,1],[0,2]], source = 0, destination = 2
```

---

### Initial State

```text
graph: 0 -> [1, 2], 1 -> [], 2 -> []
state = [0, 0, 0]
```

---

### DFS Execution Trace

```text
1. dfs(0) -> state[0] = VISITING (1)
2. Explore neighbor 1 -> dfs(1):
   - graph[1] is empty (dead end)
   - check: 1 == dest (2) is FALSE!
   - dfs(1) returns false.
3. Outgoing branch evaluation fails at node 0 loop layer.
Returns: false
```

---

# Visualization

## Graph Component Verification State

```text
   (0) ─── Visiting Stack
   / \
  ▼   ▼
 (1)  (2) ─── Valid Terminal Destination Anchor (Out-Degree = 0)
  │
 Dead End (Mismatched Failure Endpoint)
```

---

# Design Pattern

## Identify State

```text
Directed graph path validation utilizing a 3-color DFS graph coloring loop.
```

---

## Choices

```text
Prune processed paths (Black). Flag cycles instantly on active lines (Gray). Validate all dead ends.
```

---

## Key Takeaways

✅ Standard graph-theory state validation compilation design pattern.

✅ Demonstrates a core architectural algorithm model used inside compiler optimization layers for **Dead Code Elimination** or static program link parsing chains.

✅ Color array caching caps redundant subtree parsing loops, processing graph networks inside highly stable linear parameters.

✅ Common Graph Design Pattern:

```text
Reachable Path Validation via 3-Color DFS Cycle Interception
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Find Eventual Safe States (LeetCode 802)
- Reconstruct Itinerary (LeetCode 332)
- Graph Valid Tree (LeetCode 261)
