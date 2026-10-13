# Most Stones Removed with Same Row or Column

## Problem Statement

On a 2D plane, we place `n` stones at some integer coordinate points. Each coordinate point may have at most one stone.

A stone can be removed if it shares either **the same row or the same column** with another stone that has not been removed yet.

Given a 2D array `stones` of length `n` where `stones[i] = [ri, ci]` represents the location of the `i`-th stone, return *the **maximum possible number of stones** that can be removed*.

The overall runtime complexity must be strictly bounded by:

```text
O(n · α(n))
```

*(where n is the total number of stones, and α is the Inverse Ackermann function)*

---

## Example 1

### Input

```text
stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
```

### Output

```text
5
```

### Explanation

```text
One way to remove 5 stones is:
1. Remove stone [2,2] because it shares the same row as [2,1].
2. Remove stone [2,1] because it shares the same column as [0,1].
3. Remove stone [1,2] because it shares the same row as [1,0].
4. Remove stone [1,0] because it shares the same column as [0,0].
5. Remove stone [0,1] because it shares the same row as [0,0].
Stone [0,0] cannot be removed because it is the last stone left in its connected component.
Total stones removed = 5.
```

---

## Example 2

### Input

```text
stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
```

### Output

```text
3
```

---

## Example 3

### Input

```text
stones = [[0,0]]
```

### Output

```text
0
```

---

# Intuition

This problem can be elegantly transformed into a graph theory problem: **Find the connected components of stones.**

If two stones share the same row or the same column, they are connected. In any connected component of size `K`, we can always find a valid sequence to remove stones one by one until exactly **one stone remains** to anchor the component. This means if a component has `K` stones, we can remove `K - 1` stones from it.

If the entire graph of `n` stones is divided into `C` isolated connected components:
```text
Max Stones Removed 
= Sum over all components of (Component_Size - 1)
= Total Stones (n) - Total Connected Components (C)
```

To manage these row and column connections dynamically across separate coordinates, we can use a **Disjoint Set Union (DSU / Union-Find)** data structure. To map rows and columns into a single 1D DSU coordinate space without overlapping indices, we use a simple bitwise or mathematical **index transformation offsetting trick**: 
```text
Transformed Column Index = col + 10001
```
*(Since coordinate values range from 0 to 10000, adding 10001 guarantees that columns will never collide with row index slots).*

---

# State Definition

Let:

```text
Map<Integer, Integer> parent = new HashMap<>(); // DSU parent pointer map tracking dynamic coordinates
int components = 0;                             // Global tracking register for unique connected components
```

---

# Recurrence / Structural Condition Logic

```text
For each stone (row, col):
    Union(row, col + 10001)

Return total_stones - dsu.components
```

---

# Optimal Structural Graph Solution

## Idea

By processing rows and columns straight into an optimized DSU framework with **Path Compression**, components are established on-the-fly. This skips expensive pairwise matrix allocations entirely, resolving the deduplication logic in near-linear **O(n · α(n))** time.

---

## Java Code (Disjoint Set Union Architecture)

```java
import java.util.HashMap;
import java.util.Map;

class Solution {

    // High-performance Disjoint Set Union (DSU) engine using a HashMap for dynamic sizing
    private static class UnionFind {
        private final Map<Integer, Integer> parent;
        private int components;

        public UnionFind() {
            this.parent = new HashMap<>();
            this.components = 0;
        }

        // Find operation with recursive Path Compression optimization
        public int find(int i) {
            // Lazy initialization: if a coordinate is encountered for the first time, it is its own root
            if (!parent.containsKey(i)) {
                parent.put(i, i);
                components++; // Increment total components pool count
            }
            if (parent.get(i) == i) {
                return i;
            }
            // Compress the path to the root directly
            parent.put(i, find(parent.get(i)));
            return parent.get(i);
        }

        // Union operation connecting two coordinates together
        public void union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);

            if (rootI != rootJ) {
                parent.put(rootI, rootJ);
                components--; // Unified one component gap successfully
            }
        }

        public int getComponentsCount() {
            return this.components;
        }
    }

    public int removeStones(int[][] stones) {
        if (stones == null || stones.length == 0) {
            return 0;
        }

        UnionFind dsu = new UnionFind();

        // Step 1: Iterate through stones and link row and column coordinates
        for (int[] stone : stones) {
            int row = stone[0];
            // Offset columns by 10001 to prevent index collision space overlaps with rows
            int col = stone[1] + 10001; 
            
            dsu.union(row, col);
        }

        // Step 2: Maximum stones removed = Total Stones - Connected Components Count
        return stones.length - dsu.getComponentsCount();
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(n · α(n)) -> Where n is the total number of stones. Processing each stone takes near-constant O(α(n)) amortized time due to DSU path compression.
Space Complexity: O(n)          -> Allocated storage space to map the active row and column node elements inside the DSU parent hash map.
```

---

# Dry Run

## Input

```text
stones = [[0,0],[0,1],[1,0]]
```

---

### DSU Execution Trace

```text
Initial DSU State: components = 0, parent map = {}

1. Process stone [0,0]:
   - union(0, 10001) -> Creates root 0 and root 10001. Merges them. 
     components pool changes: 0 + 2 - 1 = 1. parent = {0->10001, 10001->10001}

2. Process stone [0,1]:
   - union(0, 10002) -> find(0) matches 10001. Creates root 10002. Merges them.
     components pool changes: 1 + 1 - 1 = 1. parent = {0->10001, 10001->10002, 10002->10002}

3. Process stone [1,0]:
   - union(1, 10001) -> find(10001) matches 10002. Creates root 1. Merges them.
     components pool changes: 1 + 1 - 1 = 1.

Loop ends. All items are compressed safely inside 1 global connected component.
Stones removed = Total (3) - Components (1) = 2.

Returns 2.
```

---

# Visualization

## Row-Column Connected Components

```text
     Stone Coordinate Map Layout:          DSU Structural Equivalency Grouping:
         col 0   col 1
  row 0   (S1) ─── (S2)                            (row 0) ─── (col 0) ─── (row 1)
           │                                                      │
  row 1   (S3)                                                 (col 1)
                                           All linked together as 1 component.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Graph structural grouping solved via Disjoint Set Union (DSU) multi-variable component reduction.
```

## Choices

```text
Offset multi-dimensional grid attributes to unify coordinates onto a single 1D element axis. Combine roots to track components, extracting the stone-removal potential natively.
```

---

## Key Takeaways

✅ Advanced graph-theory component isolation problem disguised as a 2D matrix puzzle.

✅ **The Offset Matrix Pattern:** Shifting columns dynamically using an upper-bound constant scalar (`col + 10001`) lets you treat different grid attributes (rows and columns) as uniform nodes inside a standard 1D DSU data stream.

✅ Direct real-world implementation engine inside **database row-deduplication utilities** (collapsing duplicate records sharing specific identical keys), routing tracking layers, and constraint grouping configurations.

✅ Common Graph Design Pattern:

```text
Attribute-Offset Graph Structuring via Disjoint Set Union Components Tracking
```

### Similar Problems

- Redundant Connection (LeetCode 684)
- Number of Provinces (LeetCode 547)
- Graph Valid Tree (LeetCode 261)
- Number of Operations to Make Network Connected (LeetCode 1319)
