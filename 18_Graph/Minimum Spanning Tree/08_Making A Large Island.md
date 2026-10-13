# Making A Large Island

## Problem Statement

You are given an `n x n` binary matrix `grid`. You are allowed to change **at most one** `0` to be `1`.

Return *the size of the largest **island** in `grid` after changing at most one `0` to `1`*.

An **island** is a 4-directionally connected group of `1`s.

The overall runtime complexity must be strictly bounded by:

```text
O(n²)
```

*(where n is the number of rows and columns in the square grid)*

---

## Example 1

### Input

```text
grid = [,
  [0, 1]
]
```

### Output

```text
3
```

### Explanation

```text
Change any one '0' to '1' (for instance, the cell at (0, 1)). 
The grid becomes:
[,
  [0, 1]
]
The largest island now has a size of 3.
```

---

## Example 2

### Input

```text
grid = [,
  [1, 0]
]
```

### Output

```text
4
```

### Explanation

```text
Change the '0' at (1, 1) to '1'. The grid becomes completely full of '1's.
The size of the island becomes 4.
```

---

## Example 3

### Input

```text
grid = [,
  [1, 1]
]
```

### Output

```text
4
```

### Explanation

```text
There are no '0's to change; the largest island is already of size 4.
```

---

# Intuition

A naive approach would try changing each `0` to `1` one by one, and then running a full DFS/BFS to find the island size every single time. However, this creates an inefficient quadratic grid processing complexity of \(O(n^4)\), which will time out.

To solve this in optimal **linear time \(O(n^2)\)**, we precompute the sizes of all existing islands and uniquely label them:
1. **Island Component Mapping:** Scan the grid. When an unvisited land cell `1` is hit, trigger a Depth-First Search (DFS) to find every connected cell belonging to this specific island. 
   - We assign this island a unique integer **Island ID** starting from `2` (since `0` and `1` are already taken).
   - As we traverse, we mutate the grid cells in-place to this `islandId` and count the total number of cells inside it.
   - We store this size inside a `HashMap` mapping `islandId -> size`.
2. **Bridge Isolation Evaluation:** Scan the grid a second time looking exclusively for water cells `0`. If we change this specific `0` to a `1`, it can act as a **bridge** connecting up to 4 of its cardinal neighbors (Up, Down, Left, Right).
3. **Deduplication Summation:** For each `0` cell, look at its 4 adjacent neighbors. Collect their unique `islandId` values into a `HashSet`. Using a set prevents us from double-counting the same island if it happens to wrap around and touch multiple sides of our bridge cell.
4. Sum the sizes of all unique surrounding islands fetched from our map, add `1` (for the newly flipped bridge cell itself), and track the absolute maximum size found.

---

# State Definition

Let:

```text
Map<Integer, Integer> islandSizes = new HashMap<>(); // Maps islandId -> total cell size count
Set<Integer> uniqueNeighbors = new HashSet<>();      // Prevents double-counting wrapping components
```

---

# Recurrence / Expansion Condition

```text
For each water cell (i, j) == 0:
    Collect unique islandIds from (i-1, j), (i+1, j), (i, j-1), (i, j+1)
    potentialSize = 1 + sum( islandSizes.get(id) )
    maxSize = max(maxSide, potentialSize)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By identifying island component boundaries during a primary initialization sweep and evaluating bridge combinations using constant-time set filtering on a second pass, each grid element is queried a constant number of times. This resolves the matrix expansion puzzle cleanly inside **O(n²)** timelines.

---

## Java Code

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

class Solution {

    public int largestIsland(int[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }

        int n = grid.length;
        Map<Integer, Integer> islandSizes = new HashMap<>();
        int islandId = 2; // Island IDs start at 2 to avoid collision with 0 and 1
        int maxIsland = 0;

        // Step 1: Discover all islands, label them uniquely, and cache their sizes
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int size = getIslandSizeDFS(i, j, grid, n, islandId);
                    islandSizes.put(islandId, size);
                    maxIsland = Math.max(maxIsland, size); // Track largest natural island
                    islandId++;
                }
            }
        }

        // Step 2: Evaluate each water cell '0' as a potential bridge location
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    Set<Integer> uniqueNeighbors = new HashSet<>();
                    
                    // Collect neighboring island IDs from all 4 cardinal directions
                    if (i > 0) uniqueNeighbors.add(grid[i - 1][j]);
                    if (i < n - 1) uniqueNeighbors.add(grid[i + 1][j]);
                    if (j > 0) uniqueNeighbors.add(grid[i][j - 1]);
                    if (j < n - 1) uniqueNeighbors.add(grid[i][j + 1]);

                    int potentialSize = 1; // Count the flipped bridge cell itself
                    
                    for (int id : uniqueNeighbors) {
                        if (id > 1) { // Ignore 0 (water) and 1 (unprocessed land markers)
                            potentialSize += islandSizes.get(id);
                        }
                    }
                    
                    maxIsland = Math.max(maxIsland, potentialSize);
                }
            }
        }

        return maxIsland;
    }

    private int getIslandSizeDFS(int i, int j, int[][] grid, int n, int islandId) {
        // Base case: Out of bounds or cell is not land '1'
        if (i < 0 || i >= n || j < 0 || j >= n || grid[i][j] != 1) {
            return 0;
        }

        // Label the cell with our current unique island ID in-place
        grid[i][j] = islandId;

        // Radiate 4-directionally to pull the full size sum
        return 1 + getIslandSizeDFS(i - 1, j,     grid, n, islandId)  // Up
                 + getIslandSizeDFS(i + 1, j,     grid, n, islandId)  // Down
                 + getIslandSizeDFS(i,     j - 1, grid, n, islandId)  // Left
                 + getIslandSizeDFS(i,     j + 1, grid, n, islandId); // Right
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(n²) -> Where n is the size of the square grid. The grid is scanned exactly twice. Each cell is visited a constant number of times across both loops.
Space Complexity: O(n²) -> Memory footprint utilized to maintain the islandSizes map and the recursive call stack depth during dense grid traversals.
```

---

# Dry Run

## Input

```text
grid = [,
  [0, 1]
]
```

---

### Step 1: Pre-mapping Component Pass

```text
- Scan hits (0, 0) == 1. islandId = 2. DFS turns it to 2. Size = 1. Map: {2 -> 1}
- Scan hits (1, 1) == 1. islandId = 3. DFS turns it to 3. Size = 1. Map: {2 -> 1, 3 -> 1}
Grid layout looks like:
[,
  [0, 3]
]
```

---

### Step 2: Bridge Evaluation Pass

```text
- Evaluate water cell at (0, 1):
  Neighbors: Left cell (2), Down cell (3) -> uniqueNeighbors = {2, 3}
  potentialSize = 1 + islandSizes.get(2) + islandSizes.get(3) = 1 + 1 + 1 = 3.
  maxIsland updates to 3.

Returns 3.
```

---

# Visualization

## Component Mapping and Bridge Bridging

```text
  Initial Grid      Component Map IDs        Bridge Placed at (0,1)
   ┌───┬───┐           ┌───┬───┐                ┌───┬───┐
   │ 1 │ 0 │           │ 2 │ 0 │                │ 2 │ B │ ◄── Bridge joins 
   ├───┼───┤    ──►    ├───┼───┤         ──►    ├───┼───┤     Islands 2 and 3
   │ 0 │ 1 │           │ 0 │ 3 │                │ 0 │ 3 │
   └───┴───┘           └───┴───┘                └───┴───┘
                      Sizes: {2:1, 3:1}         Total Size: 1 + 1 + 1 = 3
```

---

# Graph Paradigm Pattern

## Identify State

```text
Grid-based connected components pre-calculation paired with local neighborhood combination expansion.
```

## Choices

```text
Isolate independent islands via numerical coloration flags. Query adjacent boundaries using a HashSet container to combine disjoint metrics cleanly.
```

---

## Key Takeaways

✅ Advanced grid-based graph processing optimization.

✅ **The Identification ID Shift:** Transitioning the island markers from `1` straight to progressive identifiers like `2`, `3`, etc., allows the algorithm to map component boundaries safely in-place without the high memory allocation cost of an external coordinates tracker layout.

✅ Universal concept tracking components inside **image dilation networks**, mapping utility grid fallback links, and processing partition structures.

✅ Common Graph Design Pattern:

```text
Connected Component Serialization and Boundary Bridge Optimization
```

