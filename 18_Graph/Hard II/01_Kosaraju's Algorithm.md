# Kosaraju's Algorithm

## Problem Statement

Given a directed graph with `V` vertices and `E` edges, find all the **Strongly Connected Components (SCCs)** inside the graph. 

A **Strongly Connected Component** of a directed graph is a maximal subgraph where every vertex is reachable from every other vertex within that subgraph.

The graph is represented using an adjacency list format, where `adj[i]` contains a list of all vertices that node `i` points to.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of directed edges)*

---

## Example

### Input

```text
V = 5
adj = [
  0 -> [2, 3],
  1 -> [0],
  2 -> [1],
  3 -> [4],
  4 -> []
]
```

### Output

```text
3
```

### Explanation

```text
The graph contains exactly 3 strongly connected components:
1. Subgraph {0, 1, 2}: You can reach any node from any other node within this loop (0 → 3? Wait, 0 → 2 → 1 → 0).
2. Subgraph {3}: Node 3 can reach node 4 but cannot be reached back.
3. Subgraph {4}: Node 4 is a terminal dead-end with no outgoing edges.

Total SCC count = 3.
```

---

# Intuition

If we run a standard DFS on a directed graph, the traversal order depends heavily on our starting node. If we start at a node that can reach everything, a single DFS pass will consume the entire graph, masking the hidden independent components. 

To isolate SCCs cleanly, **Kosaraju's Algorithm** uses a clever three-step inversion trick based on **Topological Finishing Times** and **Graph Transposition**:

1. **Step 1 (Order by Finishing Time):** Run a standard DFS across the graph. When a node's recursive paths are completely exhausted, push it onto a `Stack`. The node that finishes absolute last floats to the top of the stack. This mimics a topological dependency ordering.
2. **Step 2 (Graph Transposition):** Create a reversed clone of the graph (`transpose`), where every directed edge `u → v` is flipped to point backward: `v → u`. 
   - *Why do this?* Inverting the edges completely neutralizes paths that leak *out* of an SCC to other downstream components, while safely preserving the internal cyclic loops inside the SCC itself.
3. **Step 3 (Reversed DFS Processing):** Pop nodes from our finishing time stack one by one. If a node is unvisited in our transposed graph, it represents the absolute root source of a remaining component. We launch a second DFS pass over the transposed graph starting from this node. This pass will map out and consume exactly one isolated SCC without leaking into any other nodes.

---

# State Definition

Let:

```text
boolean[] visited = new boolean[V];        // Bitmask array tracking visited status variables
Stack<Integer> finishStack = new Stack<>(); // Stack collecting nodes sorted by recursive finishing time
List<List<Integer>> transpose;             // Adjacency matrix structure containing inverted edge directions
```

---

# Recurrence / Structural Phase Conditions

```text
Phase 1: Run DFS(u) -> push to stack upon frame exit
Phase 2: Build Transpose -> flip all edges (u -> v变成 v -> u)
Phase 3: While stack is not empty:
            u = stack.pop()
            if u is unvisited -> launch transposeDFS(u) -> complete 1 SCC tracking frame
```

---

# Optimal Structural Strongly Connected Components Solution

## Idea

By breaking the search down into an ordering sweep, an in-place edge inversion block, and a final reversed component flood pass, each vertex and directional edge is processed exactly twice. This resolves all SCC parameters cleanly within an optimal **O(V + E)** timeline.

---

## Java Code (Three-Pass Kosaraju Paradigm)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Stack;

class Solution {

    public int kosaraju(int V, List<List<Integer>> adj) {
        Stack<Integer> finishStack = new Stack<>();
        boolean[] visited = new boolean[V];

        // Step 1: Track finishing times using a DFS Post-Order stack placement loop
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                fillOrderDFS(i, adj, visited, finishStack);
            }
        }

        // Step 2: Create the Transposed (Reversed) Graph clone layout
        List<List<Integer>> transpose = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            transpose.add(new ArrayList<>());
        }
        
        for (int u = 0; u < V; u++) {
            for (int v : adj.get(u)) {
                // Flip direction from u -> v straight over to v -> u
                transpose.get(v).add(u);
            }
        }

        // Reset our visited tracking status registers for the second DFS sweep
        Arrays.fill(visited, false);
        int sccCount = 0;

        // Step 3: Extract nodes from finishing stack and map isolated components
        while (!finishStack.isEmpty()) {
            int curr = finishStack.pop();

            // If unvisited in the transposed layout, this is a brand new independent SCC root
            if (!visited[curr]) {
                sccCount++;
                // Flood fill the component; this pass cannot leak outside the active SCC bounds
                consumeComponentDFS(curr, transpose, visited);
            }
        }

        return sccCount;
    }

    private void fillOrderDFS(int curr, List<List<Integer>> adj, boolean[] visited, Stack<Integer> stack) {
        visited[curr] = true;

        for (int neighbor : adj.get(curr)) {
            if (!visited[neighbor]) {
                fillOrderDFS(neighbor, adj, visited, stack);
            }
        }

        // Post-Order Placement: Push onto stack after exploring all outbound branches
        stack.push(curr);
    }

    private void consumeComponentDFS(int curr, List<List<Integer>> transpose, boolean[] visited) {
        visited[curr] = true;

        for (int neighbor : transpose.get(curr)) {
            if (!visited[neighbor]) {
                consumeComponentDFS(neighbor, transpose, visited);
            }
        }
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of vertices and E is the number of directed edges. The first DFS pass takes O(V + E) time, transposing the graph costs O(V + E), and the final component collection pass evaluates vertices linearly, locking total runtime inside a strict O(V + E) boundary.
Space Complexity: O(V + E) -> Memory allocated to store the transposed graph adjacency lists, finishing stack buffers, and visited state trackers.
```

---

# Dry Run

## Input

```text
V = 3, adj = [0 -> [1], 1 -> [2], 2 -> [0]] (A directed 3-node loop cycle)
```

---

### Step-by-Step Traversal Trace

```text
1. Step 1 (Finishing Times DFS):
   - Start at 0 -> visits 1 -> visits 2. Node 2 has no further moves.
   - Stack pushes: 2 finishes -> 1 finishes -> 0 finishes. 
   - finishStack = (Top: 0)

2. Step 2 (Transposition):
   - Original: 0→1, 1→2, 2→0
   - Transposed: 1→0, 2→1, 0→2

3. Step 3 (Component Breakdown Sweep):
   - Pop 0 (unvisited): sccCount becomes 1. Launch consumeComponentDFS(0) on transpose graph.
   - 0 visits 2 -> 2 visits 1 -> 1 visits 0 (already visited). Whole loop consumed.
   - Stack pops 1 and 2: already marked visited, skip.

Final sccCount = 1. Returns 1.
```

---

# Visualization

## Graph Transposition Filtering

```text
    Original Directed Graph Layout:         Transposed Layout (Flipped Edges):
         ┌───► (1) ───┐   Leak                   ┌───◄ (1) ◄───┐   Blocked
         │             ▼   Path                  ▲             │   Path
        (0) ◄──────── (2) ───► (3)              (0) ────────► (2) ◄─── (3)
       [ Internal Cycle ]                     [ Internal Cycle Loop Intact ]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Strongly connected components segmentation solved via finishing-time ordering and reverse graph transposition filtering.
```

## Choices

```text
Compile exit order onto a post-order stack container. Invert directed edge signs. Pop elements to isolate component boundaries sequentially, blocking cross-component data leakage.
```

---

## Key Takeaways

✅ Advanced core directed graph component segmentation archetype.

✅ **The Transposition Inversion Property:** Flipping directional edges preserves internal cycle routes while transforming outgoing leak paths into incoming dead ends. This guarantees that a DFS pass on a transposed node isolates its local parent component securely.

✅ Alternative Strategy: **Tarjan's Algorithm** maps SCCs using low-link discovery tracking numbers within a single DFS sweep, saving transposition memory at the cost of managing complex tracker arrays.

✅ Direct real-world implementation engine inside **static code analyzers** to intercept circular library file dependencies, web crawler index clustering systems mapping mutual hyperlink rings, and social network community grouping applications.

✅ Common Graph Design Pattern:

```text
Directed Component Isolation via Graph Transposition and Post-Order Stack Traversal
```

### Similar Problems

- Critical Connections in a Network (Bridges - LeetCode 1192)
- Course Schedule (Cycle tracking - LeetCode 207)
- Find Eventual Safe States (LeetCode 802)
- Redundant Connection II (LeetCode 685)
