# Flood Fill

## Problem Statement

An image is represented by an `m x n` integer grid `image` where `image[i][j]` represents the pixel value of the image.

You are also given three integers `sr`, `sc`, and `color`. You should perform a **flood fill** on the image starting from the pixel `image[sr][sc]`.

To perform a **flood fill**, consider the starting pixel, plus any pixels connected **4-directionally** to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with `color`.

Return *the modified image after performing the flood fill*.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the image grid)*

---

## Example 1

### Input

```text
image = [,
 ,
  [1, 0, 1]
]
sr = 1, sc = 1, color = 2
```

### Output

```text
[,
 ,
  [2, 0, 1]
]
```

### Explanation

```text
From the center of the image with position (sr, sc) = (1, 1) (color = 1):
All pixels connected 4-directionally with the same color are colored with 2.
Note the bottom-right pixel is not colored 2 because it is not 4-directionally connected to the starting pixel group.
```

---

## Example 2

### Input

```text
image = [,
  [0, 0, 0]
]
sr = 0, sc = 0, color = 0
```

### Output

```text
[,
  [0, 0, 0]
]
```

### Explanation

```text
The starting pixel is already colored 0, so no changes are made to the image.
```

---

# Intuition

The image matrix can be interpreted as an undirected graph where each cell is a node, and its connections to 4-directionally adjacent cells of the **exact same starting color** represent edges. Performing a flood fill is equivalent to traversing a **Connected Component** in a grid and updating all its node values.

We can solve this problem elegantly using **Depth-First Search (DFS)** traversal:
1. **Initial Color Capture:** Record the original color of the starting pixel: `initialColor = image[sr][sc]`.
2. **Infinite Loop Guard:** If `initialColor == color`, the target area already matches the desired color. We can short-circuit and return the image instantly to prevent the DFS from spinning in an infinite recursive cycle.
3. **Traverse & Recolor:** Trigger a recursive DFS pass from `(sr, sc)`. At each valid cell, we mutate its color in-place to the new `color`.
4. **4-Directional Radiance:** The DFS automatically branches outwards to check the top, bottom, left, and right neighbors. It only claims a neighbor if it matches the `initialColor`.

---

# State Definition

The state transformations are managed directly inside the image grid by changing the color codes in-place:
```text
image[i][j] = color // Mutating matching color pixels to prevent infinite re-evaluation
```

---

# Recurrence / Expansion Condition

```text
For a cell (i, j) with color == initialColor, lookahead in 4 directions:
    dfs(i - 1, j) // Up
    dfs(i + 1, j) // Down
    dfs(i, j - 1) // Left
    dfs(i, j + 1) // Right
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By validating boundaries at the gate and modifying matching colors in-place, each pixel within the connected component is visited exactly once. This completes the color flooding operation inside a stable **O(m × n)** linear timeline.

---

## Java Code

```java
class Solution {

    public int[][] floodFill(int[][] image, int sr, int sc, int color) {
        if (image == null || image.length == 0) {
            return image;
        }

        int initialColor = image[sr][sc];

        // Guard: If the starting pixel already matches the target color, do nothing
        if (initialColor != color) {
            int m = image.length;
            int n = image[0].length;
            fill(sr, sc, image, m, n, initialColor, color);
        }

        return image;
    }

    private void fill(int i, int j, int[][] image, int m, int n, int initialColor, int color) {
        // Base case: Out of bounds or current cell doesn't match the original color block
        if (i < 0 || i >= m || j < 0 || j >= n || image[i][j] != initialColor) {
            return;
        }

        // Apply the new color in-place (serves implicitly as our visited marker)
        image[i][j] = color;

        // Radiate recursively to all 4 cardinal neighbors
        fill(i - 1, j,     image, m, n, initialColor, color); // Up
        fill(i + 1, j,     image, m, n, initialColor, color); // Down
        fill(i,     j - 1, image, m, n, initialColor, color); // Left
        fill(i,     j + 1, image, m, n, initialColor, color); // Right
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Where m is the number of rows and n is the number of columns. Every pixel is processed at most a constant number of times.
Space Complexity: O(m × n) -> In the worst case where every single pixel matches the initial color, the execution call stack can grow up to a depth of m × n.
```

---

# Dry Run

## Input

```text
image = [,
  [1, 0, 1]
], sr = 0, sc = 0, color = 2
```

---

### Initial State

```text
initialColor = image[0][0] = 1
initialColor (1) != color (2) -> Call fill(0, 0)
```

---

### DFS Execution Trace

```text
1. fill(0, 0) -> image[0][0] matches 1 -> changes to 2.
   - Recurse right -> fill(0, 1):
     - image[0][1] matches 1 -> changes to 2.
     - Recurse right -> fill(0, 2): image[0][2] is 0 != 1 -> returns immediately.
     - Recurse down -> fill(1, 1): image[1][1] is 0 != 1 -> returns immediately.
   - Recurse down -> fill(1, 0):
     - image[1][0] matches 1 -> changes to 2.
     - All surrounding directions from (1, 0) are now out of bounds, 0, or already 2.
2. The entire top-left connected component is fully flooded.

Returns modified image.
```

---

# Visualization

## Multi-Directional Flood Propagation

```text
   Initial Grid       Discover Center       Flooding Done
   ┌───┬───┬───┐       ┌───┬───┬───┐       ┌───┬───┬───┐
   │ 1 │ 1 │ 0 │       │ 2 │ 1 │ 0 │       │ 2 │ 2 │ 0 │
   ├───┼───┼───┤  ──►  ├───┼───┼───┤  ──►  ├───┼───┼───┤
   │ 1 │ 0 │ 1 │       │ 1 │ 0 │ 1 │       │ 2 │ 0 │ 1 │
   └───┴───┴───┘       └───┴───┴───┘       └───┴───┴───┘
   (Start at 0,0)      Matures to 2        Component unified
```

---

# Graph Paradigm Pattern

## Identify State

```text
Grid-based Connected Components recoloring utilizing in-place conditional DFS updates.
```

---

## Choices

```text
Traverse 4-directionally to claim adjacent cells matching the origin color signature.
```

---

## Key Takeaways

✅ Baseline 2D grid matrix connected components graph traversal problem.

✅ Mutating cell values dynamically inside the source container removes the requirement to allocate separate visited indicator matrices.

✅ Perfect computer science mirror of real-world tool engines like the **Paint Bucket tool** in graphic design suites or segmentation masks in computer vision pipelines.

✅ Common Graph Design Pattern:

```text
In-Place Matrix Value Overwriting for Connected Component Tracking
```

### Similar Problems

- Number of Islands (LeetCode 200)
- Max Area of Island (LeetCode 695)
- Surrounded Regions (LeetCode 130)
- Island Perimeter (LeetCode 463)
