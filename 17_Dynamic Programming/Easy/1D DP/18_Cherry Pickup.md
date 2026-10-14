# 741. Cherry Pickup

🔗 **Problem Link:**  
https://leetcode.com/problems/cherry-pickup/

---

# Problem Statement

You are given an `n x n` grid where:

- `0` → Empty cell
- `1` → Cherry
- `-1` → Thorn (blocked cell)

You must:

1. Start at `(0,0)`
2. Reach `(n-1,n-1)`
3. Return back to `(0,0)`

Rules:

- You can only move:
  - Right or Down on the forward trip
  - Left or Up on the return trip
- A cherry can only be collected once.
- Cells containing `-1` cannot be visited.

Return the maximum number of cherries that can be collected.

---

# Why the Naive Approach Fails

A straightforward idea is:

```text
1. Find the best path from start → end
2. Remove collected cherries
3. Find the best path from end → start
```

This is incorrect because:

```text
The forward path affects
the return path.
```

A locally optimal forward path may prevent a globally optimal overall solution.

---

# Key Insight

Instead of thinking about:

```text
Person goes from
(0,0) → (n-1,n-1)

and then returns
(n-1,n-1) → (0,0)
```

Think of it as:

```text
Two people start simultaneously
at (0,0)

Both move only:
- Right
- Down

Until they reach
(n-1,n-1)
```

Both walkers represent:

```text
Forward Journey
+
Return Journey
```

---

# State Optimization

For two walkers:

```text
Walker A -> (x1, y1)
Walker B -> (x2, y2)
```

At any step:

```text
x1 + y1 = x2 + y2
```

because both have taken the same number of moves.

Therefore:

```text
y2 = x1 + y1 - x2
```

We don't need four dimensions.

---

## DP State

```text
dfs(x1, y1, x2)
```

represents:

```text
Maximum cherries collected when:

Walker A is at (x1, y1)
Walker B is at (x2, y2)
```

where

```text
y2 = x1 + y1 - x2
```

---

# Memoization (Top-Down DP)

## Recursive Choices

Both walkers can move:

```text
Down
or
Right
```

Possible combinations:

```text
1. A Down,  B Down
2. A Down,  B Right
3. A Right, B Down
4. A Right, B Right
```

We take the best among all four possibilities.

---

## Transition

```text
dfs(x1, y1, x2)

=
cherries collected at current cells

+

max(
    dfs(x1+1, y1,   x2+1),
    dfs(x1+1, y1,   x2),
    dfs(x1,   y1+1, x2+1),
    dfs(x1,   y1+1, x2)
)
```

---

# Code

```java
class Solution {

    public int cherryPickup(int[][] grid) {

        int n = grid.length;

        Integer[][][] memo =
                new Integer[n][n][n];

        return Math.max(
                0,
                dfs(grid, 0, 0, 0, memo)
        );
    }

    private int dfs(
            int[][] grid,
            int x1,
            int y1,
            int x2,
            Integer[][][] memo) {

        int y2 = x1 + y1 - x2;

        int n = grid.length;

        if (x1 >= n ||
            y1 >= n ||
            x2 >= n ||
            y2 >= n ||
            grid[x1][y1] == -1 ||
            grid[x2][y2] == -1) {

            return -1;
        }

        if (x1 == n - 1 &&
            y1 == n - 1) {

            return grid[x1][y1];
        }

        if (memo[x1][y1][x2] != null) {
            return memo[x1][y1][x2];
        }

        int max = Math.max(
                Math.max(
                        dfs(grid, x1 + 1, y1, x2 + 1, memo),
                        dfs(grid, x1 + 1, y1, x2, memo)
                ),
                Math.max(
                        dfs(grid, x1, y1 + 1, x2 + 1, memo),
                        dfs(grid, x1, y1 + 1, x2, memo)
                )
        );

        if (max == -1) {
            return memo[x1][y1][x2] = -1;
        }

        int cherries = grid[x1][y1];

        if (x1 != x2 || y1 != y2) {
            cherries += grid[x2][y2];
        }

        return memo[x1][y1][x2]
                = cherries + max;
    }
}
```

---

# Avoiding Double Counting

Suppose both walkers stand on:

```text
(x, y)
```

Then there is only one cherry.

Wrong:

```text
grid[x][y] + grid[x][y]
```

Correct:

```java
if (x1 != x2 || y1 != y2) {
    cherries += grid[x2][y2];
}
```

This ensures the same cherry is not counted twice.

---

# Invalid States

Immediately reject:

```text
Out of bounds
OR
Thorn (-1)
```

```java
if (x1 >= n ||
    y1 >= n ||
    x2 >= n ||
    y2 >= n ||
    grid[x1][y1] == -1 ||
    grid[x2][y2] == -1)
{
    return -1;
}
```

---

# Base Case

When Walker A reaches:

```text
(n-1, n-1)
```

Walker B must also be at:

```text
(n-1, n-1)
```

because both have taken exactly the same number of steps.

Return:

```java
grid[n - 1][n - 1]
```

---

# Dry Run

## Input

```text
grid =

[
 [0,1,-1],
 [1,0,-1],
 [1,1, 1]
]
```

---

### Step 1

Both start:

```text
A(0,0)
B(0,0)
```

Cherries:

```text
0
```

---

### Step 2

Possible moves:

```text
A ↓
B ↓

A ↓
B →

A →
B ↓

A →
B →
```

DFS explores all four combinations.

---

### Step 3

Memoization stores:

```text
memo[x1][y1][x2]
```

so repeated states are computed only once.

---

### Final Result

```text
5
```

---

# Why 3D DP Works

At first glance the state seems:

```text
(x1, y1, x2, y2)
```

which would require:

```text
O(n⁴)
```

states.

But:

```text
x1 + y1 = x2 + y2
```

Therefore:

```text
y2 = x1 + y1 - x2
```

and one dimension is eliminated.

State becomes:

```text
(x1, y1, x2)
```

Resulting in:

```text
O(n³)
```

states.

---

# Complexity Analysis

### Number of States

```text
(x1, y1, x2)
```

Each ranges from:

```text
0 → n-1
```

Therefore:

```text
O(n³)
```

states.

---

### Time Complexity

Each state explores:

```text
4 transitions
```

Constant work.

```text
O(n³)
```

---

### Space Complexity

Memoization table:

```text
O(n³)
```

Recursion stack:

```text
O(n)
```

Total:

```text
O(n³)
```

---

# Visual Representation

```text
Forward Trip

(0,0)
   ↓
   ↓
(n-1,n-1)

Return Trip

(n-1,n-1)
   ↑
   ↑
(0,0)
```

Converted into:

```text
Walker A

(0,0)
   ↓ →
   ↓ →
(n-1,n-1)

Walker B

(0,0)
   ↓ →
   ↓ →
(n-1,n-1)
```

Both moving simultaneously.

---

# Key Takeaways

| Observation | Benefit |
|------------|----------|
| Convert round trip into two walkers | Removes forward/return dependency |
| Both walkers take same number of steps | Derive `y2` from other coordinates |
| State becomes `(x1,y1,x2)` | Reduce from O(n⁴) to O(n³) |
| Memoization prevents recomputation | Efficient DP |
| Avoid double-counting shared cells | Correct cherry collection |

---

# Pattern Recognition

Whenever you see:

```text
Go from A → B
Then return from B → A

Maximize total reward
```

try transforming it into:

```text
Two people moving together
from A → B
```

This is a classic:

```text
DP on Two Paths
```

pattern and appears in advanced grid DP problems.

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Memoization (3D DP) | O(n³) | O(n³) |

✅ **Optimal Approach:** 3D Memoization DP using the "Two Walkers" transformation.
