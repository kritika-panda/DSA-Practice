# m-Coloring Problem ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an undirected graph represented as an adjacency matrix `graph` of size `V x N` (where `V` is the number of vertices) and an integer `m`, determine if the graph can be colored using at most `m` colors such that **no two adjacent vertices share the same color**. 

Return `true` if a valid coloring configuration exists, otherwise return `false`.

---

## Examples

### Example 1

```text
Input: V = 4, m = 3
graph = [[0, 1, 1, 1],
         [1, 0, 1, 0],
         [1, 1, 0, 1],
         [1, 0, 1, 0]]
Output: true
```

### Explanation

```text
The graph forms a 4-node network. We can assign 3 colors across the vertices:
- Vertex 0: Color 1
- Vertex 1: Color 2
- Vertex 2: Color 3
- Vertex 3: Color 2
Symmetric checks confirm that no connected nodes share a matching color code.
```

---

### Example 2

```text
Input: V = 3, m = 2
graph = [[0, 1, 1],
         [1, 0, 1],
         [1, 1, 0]]
Output: false
```

### Explanation

```text
The graph forms a fully connected 3-node triangle (clique). 
It is mathematically impossible to color a triangle using only 2 colors 
without two adjacent nodes conflicting.
```

---

# Intuition

The m-Coloring problem is a classic constraint satisfaction challenge that maps onto a **State-Space Decision Tree**. For each vertex from `0` to `V - 1`, we must pick a color integer from `1` to `m`.

We can systematically explore the solution space using a **Depth-First Search (DFS) with Backtracking**:
1. **Sequential Vertex Assignment:** We process vertices one by one starting from node `0`.
2. **Constraint Validation Loop:** For the active vertex `v`, we loop through all possible colors `c` from `1` to `m`. Before assigning color `c`, we run an adjacency check: look at all neighbors of `v` in the graph; if any neighboring node is *already* colored `c`, this choice is invalid, so we skip it (Pruning).
3. **Recursive Exploration:** If color `c` is safe, we assign it to vertex `v` (`color[v] = c`) and move forward recursively to process the next vertex (`v + 1`).
4. **The Backtrack Reset:** If exploring downstream returns `false` (meaning the chosen color configuration leads to a dead end further down the line), we undo the assignment by resetting `color[v] = 0`. We then continue our loop to try the next color option.
5. **Base Case Victory:** If our vertex pointer successfully reaches `V`, every single node has been safely colored without conflict, so we return `true`.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorial search steps systematically inside a graph decision layout, we manage states via a dedicated 1D register:

```text
backtrack(v, colorArray):
- 'v' tracks our active position pointer index inside the vertex pool.
- 'colorArray' acts as a shared mutable array caching assigned color codes on-the-fly.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If v == V -> Return true (All vertices successfully colored)

Recursive Controlled Branching:
For c from 1 to m:
    If isSafeToColor(v, c, colorArray):
        colorArray[v] = c // Assign color
        If backtrack(v + 1, colorArray) -> Return true
        colorArray[v] = 0 // Backtrack step (Undo and clear state)

Return false
```

---

# Optimal Backtracking Solution

## Idea

We allocate a local primitive 1D `int[V]` array initialized to `0` to serve as our central coloring tracker workspace. We trigger our recursive descent from vertex 0. By incorporating a high-speed neighbor scan check, we prune out invalid colors before entering downstream recursive branches.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public boolean graphColoring(boolean[][] graph, int m, int V) {
        // Guard Condition: If there are no vertices, it is vacuously valid
        if (V == 0) {
            return true;
        }

        // 1D tracking array initialized to 0 (where 0 means the node is uncolored)
        int[] color = new int[V];

        // Launch recursive backtracking pass starting from vertex index 0
        return solveColoring(0, graph, m, V, color);
    }

    private boolean solveColoring(int v, boolean[][] graph, int m, int V, int[] color) {
        // Base Case: If the vertex cursor reaches V, a valid coloring configuration is finalized
        if (v == V) {
            return true;
        }

        // Controlled Branching: Attempt assigning every color code from 1 up to m
        for (int c = 1; c <= m; c++) {
            // Check neighbor constraints before altering the state variables
            if (isSafe(v, graph, color, c, V)) {
                color[v] = c; // Choice Step: Assign color code c to vertex v

                // Recurse deeper to attempt coloring the next subsequent node
                if (solveColoring(v + 1, graph, m, V, color)) {
                    return true; // Valid configuration confirmed, bubble up success!
                }

                // Backtrack Step: Clear cell choice to restore context alignment for the next branch
                color[v] = 0;
            }
        }

        return false; // All m choices exhausted for this node without success
    }

    // High-speed lookahead constraint validator checking adjacent node peer boundaries
    private boolean isSafe(int v, boolean[][] graph, int[] color, int c, int V) {
        for (int i = 0; i < V; i++) {
            // If an edge connection exists AND that adjacent node shares the target color code
            if (graph[v][i] && color[i] == c) {
                return false; // Conflict found, color code c cannot be used here
            }
        }
        return true; // No adjacent collisions, color is safe to apply
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(m^V) worst case
```
*Explanation:* For each of the `V` vertices, the algorithm attempts to test up to `m` color options. This creates a state-space configuration tree with an absolute upper bound size of \(O(m^V)\). However, because the lookahead constraint check continuously clips out conflicting branches early, the actual operational path footprint is highly optimized.

---

### Space Complexity

```text
Space Complexity: O(V) auxiliary space
```
*Explanation:* Highly optimal. The algorithm coordinates states in-place using a single 1D local `color` array of size `V`. Auxiliary memory usage scales linearly at O(V) to accommodate the maximum depth height of the recursive call stack.

---

# Key Takeaways

✅ Core fundamental paradigm for processing constraint satisfaction problems (CSPs) on networks and graphs.

✅ **The Value of Controlled Backtracking:** Instead of generating all possible color permutations and filtering them afterward, embedding adjacency validation checks directly into the branching statement (`if (isSafe(...))`) prunes out vast sub-trees of invalid options, turning an unmanageable problem into an optimized search.

✅ Directly drives real-world microarchitectures like **telecommunication cellular tower frequency allocators** (assigning distinct radio frequencies to overlapping signal cells to avoid interference), map layout drawing tools, university exam scheduling managers, and compiler register allocation passes (Chaitin's formulation).

✅ Common Architectural Paradigm:

```text
Vertex-Indexed Constraint Branching with Symmetric Trailing Unwind Clears
```

### Similar Problems

- N-Queens ([LeetCode 51](https://leetcode.com))
- Sudoku Solver ([LeetCode 37](https://leetcode.com))
- Word Search ([LeetCode 79](https://leetcode.com))
- Is Graph Bipartite? (Special case where m = 2 - [LeetCode 785](https://leetcode.com))
