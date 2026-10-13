# Number of Distinct Islands

## Problem Statement

Given an `m x n` 2D binary grid `grid` representing a map of `1`s (land) and `0`s (water), return *the number of **distinct** islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are completely surrounded by water.

Two islands are considered **distinct** if and only if one island cannot be rotated or translated to match the other. In this specific variation, we only consider **translations** (shifts without rotations or reflections). This means two islands are distinct if their geometric shapes or traversal contours are completely different.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the grid)*

---

## Example 1

### Input

```text
grid = [
  [1, 1, 0, 0, 0],
  [1, 1, 0, 0, 0],
  [0, 0, 0, 1, 1],
  [0, 0, 0, 1, 1]
]
```

### Output

```text
1
```

### Explanation

```text
There are two islands in the grid: 
- Top-left 2x2 island
- Bottom-right 2x2 island

Since one can be perfectly translated (shifted) to match the other, they are identical shapes.
Total unique/distinct islands = 1.
```

---

## Example 2

### Input

```text
grid = [
  [1, 1, 0, 1, 1],
  [1, 0, 0, 0, 0],
  [0, 0, 0, 0, 1],
  [1, 1, 0, 1, 1]
]
```

### Output

```text
3
```

---

# Intuition

This problem expands on the classic *Number of Islands* problem. Instead of just counting the total number of connected components, we need to uniquely identify and deduplicate the **geometric shape signature** of each island.

Since we only care about translations, we can uniquely identify an island's shape by recording its **exact directional traversal path signature** during a Depth-First Search (DFS).
* As the DFS moves in a direction, we append a unique character representation tracking that choice (e.g., `'U'` for Up, `'D'` for Down, `'L'` for Left, `'R'` for Right).
* **The Backtracking Boundary Marker:** Crucially, we must also append a terminal backtrack marker character (e.g., `'B'`) when backtracking out of a recursive frame. Without logging the backtrack steps, two structurally different islands could generate identical path prefixes and cause a false collision.
* By serializing this path into a string and adding it to a global `HashSet`, duplicate island shapes are automatically filtered out.

---

# State Definition

The unique geometric shapes are aggregated inside a tracking set:
```text
Set<String> uniqueIslands = new HashSet<>(); // Collects distinct traversal signatures
```

---

# Recurrence / Directional Transition Condition

```text
For an active island cell (i, j), append direction, recurse, then append backtrack:
    path.append('U'); dfs(i - 1, j); path.append('B'); // Up
    path.append('D'); dfs(i + 1, j); path.append('B'); // Down
    path.append('L'); dfs(i, j - 1); path.append('B'); // Left
    path.append('R'); dfs(i, j + 1); path.append('B'); // Right
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By scanning every cell in the grid, sinking land cells in-place to mark them as visited, and tracking path directions inside a `StringBuilder`, each island's structure is serialized linearly. The total runtime scales bounded explicitly inside **O(m × n)** complexity.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

class Solution {

    public int numDistinctIslands(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int m = grid.length;
        int n = grid[0].length;
        Set<String> uniqueIslands = new HashSet<>();

        // Step 1: Scan every cell in the 2D grid matrix
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                
                // If an unvisited land cell '1' is found, start capturing its shape signature
                if (grid[i][j] == 1) {
                    StringBuilder pathSignature = new StringBuilder();
                    // Pass an initial anchor trigger character 'O' to designate origin start
                    pathSignature.append('O');
                    
                    // Step 2: Flood-sink the island while serializing its structural layout contours
                    exploreAndSerialize(i, j, grid, m, n, pathSignature);
                    
                    // Add the serialized path string to our deduplication set
                    uniqueIslands.add(pathSignature.toString());
                }
            }
        }

        // Step 3: The size of the set matches the count of unique shapes
        return uniqueIslands.size();
    }

    private void exploreAndSerialize(int i, int j, int[][] grid, int m, int n, StringBuilder path) {
        // Base case: Out of matrix bounds or cell is water '0'
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == 0) {
            return;
        }

        // Sink the current land cell in-place to act as our visited marker
        grid[i][j] = 0;

        // Step 4: Branch 4-directionally, logging paths and tracking backtracks explicitly
        if (i - 1 >= 0 && grid[i - 1][j] == 1) {
            path.append('U');
            exploreAndSerialize(i - 1, j, grid, m, n, path);
            path.append('B');
        }
        if (i + 1 < m && grid[i + 1][j] == 1) {
            path.append('D');
            exploreAndSerialize(i + 1, j, grid, m, n, path);
            path.append('B');
        }
        if (j - 1 >= 0 && grid[i][j - 1] == 1) {
            path.append('L');
            exploreAndSerialize(i, j - 1, grid, m, n, path);
            path.append('B');
        }
        if (j + 1 < n && grid[i][j + 1] == 1) {
            path.append('R');
            exploreAndSerialize(i, j + 1, grid, m, n, path);
            path.append('B');
        }
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Every grid cell is processed at most a constant number of times during the scan and shape creation pass.
Space Complexity: O(m × n) -> For storing the path signature strings inside the HashSet and tracking deep island branches on the recursion stack.
```

---

# Dry Run

## Input

```text
grid = [
  [1, 1, 0],
  [0, 0, 0],
  [1, 1, 0]
]
```

---

### Execution Serialization Trace

```text
1. i = 0, j = 0 -> Hit 1. IslandCount triggers. 
   - pathSignature = "O"
   - Right move available to (0,1) -> Append 'R', recurse to (0,1). path = "OR"
   - At (0,1), no further paths -> function frame ends, returns up appending 'B'. path = "ORB"
2. Final string added to uniqueIslands set = "ORB".

3. Grid loops past 0s until i = 2, j = 0 -> Hit 1.
   - pathSignature = "O"
   - Right move available to (2,1) -> Append 'R', recurse to (2,1). path = "OR"
   - frame ends, appends 'B'. path = "ORB"
4. HashSet intercepts "ORB" as a duplicate key collision and drops it.

Set size = 1. Returns 1.
```

---

# Visualization

## Path-Signature Deduplication

```text
  Island Structure A                 Island Structure B
     ┌───┬───┐                          ┌───┬───┐
     │ 1 │ 1 │                          │ 1 │ 0 │
     └───┼───┘                          └───┼───┤
         │                              │ 1 │ 1 │
      Path: "ORB"                       └───┴───┘
                                     Path: "ODRB"
  
  Unique strings ensure distinct geometries are mapped correctly without collisions.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Shape-serialized connected components tracking utilizing path-signature string token hashing.
```

---

## Choices

```text
Traverse 4-directionally to capture relative geometry moves. Append backtracking codes to insulate contours.
```

---

## Key Takeaways

✅ Advanced extension of grid connected component graph processing.

✅ Adding backtracking signals (`'B'`) is mathematically mandatory to avoid path signature overlap collisions among asymmetric or un-mirrored structural pairs.

✅ Universal concept modeling layout used in **Optical Character Recognition (OCR)**, canvas pattern matching, or image shape classification.

✅ Common Graph Design Pattern:

```text
Canonical Serialization of DFS Graph Contours for Structural Deduplication
```

### Similar Problems

- Number of Islands (LeetCode 200)
- Max Area of Island (LeetCode 695)
- Number of Distinct Islands II (With Rotations/Reflections - LeetCode 711)
- Flood Fill (LeetCode 733)
