# Evaluate Division

## Problem Statement

You are given an array of variable pairs `equations` and an array of real numbers `values`, where `equations[i] = [Ai, Bi]` and `values[i]` represent the equation \(A_i / B_i = \text{values}[i]\). Each \(A_i\) or \(B_i\) is a string that represents a single variable.

You are also given some queries, where `queries[j] = [Cj, Dj]` represents the \(j\)-th query where you must find the value of \(C_j / D_j\).

Return *the answers to all queries*. If a single answer cannot be determined, return `-1.0`.

Note: The input is always valid. You may assume that evaluating the queries will not result in division by zero and that there is no contradiction.

The overall runtime complexity must be strictly bounded by:

```text
O((E + Q) * V)
```

*(where E is the number of equations, Q is the number of queries, and V is the number of unique variables)*

---

## Example 1

### Input

```text
equations = [["a","b"],["b","c"]]
values = [2.0,3.0]
queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
```

### Output

```text
[6.0, 0.5, -1.0, 1.0, -1.0]
```

### Explanation

```text
Given: a / b = 2.0, b / c = 3.0
Queries:
1. a / c = (a / b) * (b / c) = 2.0 * 3.0 = 6.0
2. b / a = 1 / (a / b) = 1 / 2.0 = 0.5
3. a / e = Variable 'e' is hidden/missing, so -1.0
4. a / a = Variable 'a' is known, so a / a = 1.0
5. x / x = Variable 'x' is unvisited/unknown, so -1.0
```

---

## Example 2

### Input

```text
equations = [["a","b"],["b","c"],["bc","cd"]]
values = [1.5,2.5,5.0]
queries = [["a","c"],["c","b"],["bc","cd"],["cd","bc"]]
```

### Output

```text
[3.75, 0.4, 5.0, 0.2]
```

---

# Intuition

This problem can be elegantly modeled as a **Directed, Weighted Graph** verification task:
*   Each variable represents a **Node** (vertex).
*   Each equation \(A / B = v\) represents a **Directed Edge** from node \(A\) to node \(B\) with a weight of \(v\).
*   Crucially, this implies the inverse relationship holds for free: a **Directed Edge** from node \(B\) to node \(A\) with a weight of \(1 / v\).

Finding the value of a query \(C / D\) is equivalent to **finding a path from node \(C\) to node \(D\)** and multiplying the weights of all edges along that path:

\[\frac{A}{C} = \frac{A}{B} \times \frac{B}{C}\]

We can search for this path using **Depth-First Search (DFS)** paired with an unvisited tracking `HashSet` to prevent spinning in cycles. If either variable in a query is missing from our graph pool, we short-circuit and flag it as unreachable.

---

# State Definition

Let:

```text
Map<String, Map<String, Double>> graph = new HashMap<>(); // Adjacency map of weights
Set<String> visited = new HashSet<>();                     // Traversal cycle defense track
```

---

# Recurrence / State Transition Condition

```text
For each neighbor of current node:
    If neighbor is not visited:
        result = dfs(neighbor, target)
        if result != -1.0:
            return current_edge_weight * result
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By pre-building the network relations inside a nested `HashMap` structure, we establish all forward and inverse paths instantly. For each of the \(Q\) queries, we run a localized DFS traversal that touches at most \(V\) nodes and \(E\) edges, bounding the overall execution matrix cleanly inside **O((E + Q) * V)** timelines.

---

## Java Code

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

class Solution {

    public double[] calcEquation(int[][] equations, double[] values, int[][] queries) {
        // Fast fallback placeholder if types map out abstractly
        return new double[0];
    }

    public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
        // Step 1: Build the directed, weighted adjacency map graph framework
        Map<String, Map<String, Double>> graph = new HashMap<>();

        for (int i = 0; i < equations.length; i++) {
            String u = equations[i][0];
            String v = equations[i][1];
            double val = values[i];

            graph.computeIfAbsent(u, k -> new HashMap<>()).put(v, val);
            graph.computeIfAbsent(v, k -> new HashMap<>()).put(u, 1.0 / val); // Log inverse ratio
        }

        double[] results = new double[queries.length];

        // Step 2: Process each query independently using targeted DFS paths
        for (int i = 0; i  visited = new HashSet<>();
                results[i] = dfs(src, dest, graph, visited);
            }
        }

        return results;
    }

    private double dfs(String curr, String target, Map<String, Map<String, Double>> graph, Set<String> visited) {
        // Base case match found! Terminal path anchor reached
        if (curr.equals(target)) {
            return 1.0;
        }

        visited.add(curr); // Register current variable node on active path tracker
        Map<String, Double> neighbors = graph.get(curr);

        // Step 3: Traverse adjacent outgoing node edges
        for (Map.Entry<String, Double> neighbor : neighbors.entrySet()) {
            String nextNode = neighbor.getKey();
            double edgeWeight = neighbor.getValue();

            if (!visited.contains(nextNode)) {
                double subResult = dfs(nextNode, target, graph, visited);
                
                // If a valid connection down the line was found, chain product weights back up
                if (subResult != -1.0) {
                    return edgeWeight * subResult;
                }
            }
        }

        return -1.0; // Dead end hit; target variable unreachable along this path
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O((E + Q) * V) -> Graph building takes linear O(E) steps. For each of the Q queries, the DFS traversal searches through at most V variable nodes in the worst case.
Space Complexity: O(E + V) -> Storage footprint for the adjacency tracking maps and unvisited HashSet bounds.
```

---

# Dry Run

## Input

```text
equations = [["a","b"],["b","c"]], values = [2.0, 3.0]
query = ["a", "c"]
```

---

### Initial Graph Compilation

```text
graph Map state:
"a" -> {"b" -> 2.0}
"b" -> {"a" -> 0.5, "c" -> 3.0}
"c" -> {"b" -> 0.333}
```

---

### DFS Execution Trace for ["a", "c"]

```text
1. dfs("a", "c") -> visited = {"a"}
2. Explore neighbor "b" -> dfs("b", "c") -> visited = {"a", "b"}
3. Explore neighbor "c" -> dfs("c", "c") -> target matched! Returns 1.0
4. Backtrack to "b": returns edgeWeight ("b"->"c" = 3.0) * 1.0 = 3.0
5. Backtrack to "a": returns edgeWeight ("a"->"b" = 2.0) * 3.0 = 6.0
Query resolves to 6.0.
```

---

# Visualization

## Variable Dependency Factor Graph

```text
       2.0               3.0
  (a) ────► (b) ───► (c)
   ◄────     ◄───
    0.5              0.333
    
  Evaluating a / c follows path links forward: 2.0 * 3.0 = 6.0
```

---

# Design Pattern

## Identify State

```text
Equations processing reframed into path-finding searches across Directed, Weighted Graph Networks.
```

---

## Choices

```text
Chain multiplications forward across matching edges. Invert step values for backward hops. Prune cyclic loops.
```

---

## Key Takeaways

✅ Advanced mathematical transformation question mapping system relations into path algorithms.

✅ Demonstrates a classical operational engine utilized inside database spreadsheet systems for **Cell Dependency Resolution** or compiling metric conversions (e.g., currency arbitrage tracking matrices).

✅ Adjacency maps ensure variable validation tracks stay lean, isolating network components inside sub-linear bounds.

✅ Common Graph Design Pattern:

```text
Multiplicative Path Resolution via Directed Edge-Weight Product Chaining
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Network Delay Time (LeetCode 743)
- Reconstruct Itinerary (LeetCode 332)
- Clone Graph (LeetCode 133)
