# Frog Jump

## Problem Statement

A frog is crossing a river. The river is divided into `N` stones, and the height of each stone is given in an array `heights[]`.

The frog starts at the first stone (`index 0`) and wants to reach the last stone (`index N-1`).

At any point, the frog can jump:

- From stone `i` to stone `i + 1`
- From stone `i` to stone `i + 2`

The cost of a jump from stone `i` to stone `j` is:

```text
|heights[i] - heights[j]|
```

Return the minimum total energy required for the frog to reach the last stone.

---

## Example 1

### Input

```text
heights = [30,10,60,10,60,50]
```

### Output

```text
40
```

### Explanation

Optimal path:

```text
0 → 2 → 4 → 5

Cost =
|30 - 60| +
|60 - 60| +
|60 - 50|

= 30 + 0 + 10
= 40
```

---

## Example 2

### Input

```text
heights = [10,20,30,10]
```

### Output

```text
20
```

---

# Intuition

To reach stone `i`, the frog can come from:

1. Stone `i - 1`
2. Stone `i - 2`

Therefore,

```text
Minimum Cost(i)
=
min(
    cost from i-1,
    cost from i-2
)
```

This directly forms a Dynamic Programming problem.

---

# Recursive Solution

## Recurrence

```text
f(i) =
min(
    f(i-1) + |h[i] - h[i-1]|,
    f(i-2) + |h[i] - h[i-2]|
)
```

### Base Case

```text
f(0) = 0
```

---

## Java Code (Recursion)

```java
class Solution {

    private int solve(int index, int[] heights) {

        if (index == 0) {
            return 0;
        }

        int left =
                solve(index - 1, heights)
                + Math.abs(heights[index] - heights[index - 1]);

        int right = Integer.MAX_VALUE;

        if (index > 1) {
            right =
                    solve(index - 2, heights)
                    + Math.abs(heights[index] - heights[index - 2]);
        }

        return Math.min(left, right);
    }

    public int frogJump(int[] heights) {
        return solve(heights.length - 1, heights);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(2^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Store already computed states in a DP array.

Avoid solving the same subproblem repeatedly.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int index, int[] heights, int[] dp) {

        if (index == 0) {
            return 0;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int left =
                solve(index - 1, heights, dp)
                + Math.abs(heights[index] - heights[index - 1]);

        int right = Integer.MAX_VALUE;

        if (index > 1) {
            right =
                    solve(index - 2, heights, dp)
                    + Math.abs(heights[index] - heights[index - 2]);
        }

        return dp[index] = Math.min(left, right);
    }

    public int frogJump(int[] heights) {

        int n = heights.length;

        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        return solve(n - 1, heights, dp);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N) + O(N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## Idea

Build answers from smaller subproblems.

---

## DP Definition

```text
dp[i] = minimum energy required to reach stone i
```

### Base Case

```text
dp[0] = 0
```

### Transition

```text
dp[i] =
min(
    dp[i-1] + |h[i] - h[i-1]|,
    dp[i-2] + |h[i] - h[i-2]|
)
```

---

## Java Code

```java
class Solution {

    public int frogJump(int[] heights) {

        int n = heights.length;

        int[] dp = new int[n];
        dp[0] = 0;

        for (int i = 1; i < n; i++) {

            int left =
                    dp[i - 1]
                    + Math.abs(heights[i] - heights[i - 1]);

            int right = Integer.MAX_VALUE;

            if (i > 1) {
                right =
                        dp[i - 2]
                        + Math.abs(heights[i] - heights[i - 2]);
            }

            dp[i] = Math.min(left, right);
        }

        return dp[n - 1];
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N)
```

---

# Space Optimization

## Observation

For computing:

```text
dp[i]
```

we only need:

```text
dp[i-1]
dp[i-2]
```

Therefore, the entire DP array is unnecessary.

---

## Java Code

```java
class Solution {

    public int frogJump(int[] heights) {

        int n = heights.length;

        int prev = 0;
        int prev2 = 0;

        for (int i = 1; i < n; i++) {

            int left =
                    prev
                    + Math.abs(heights[i] - heights[i - 1]);

            int right = Integer.MAX_VALUE;

            if (i > 1) {
                right =
                        prev2
                        + Math.abs(heights[i] - heights[i - 2]);
            }

            int current = Math.min(left, right);

            prev2 = prev;
            prev = current;
        }

        return prev;
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(1)
```

---

# Dry Run

```text
heights = [30,10,60,10,60,50]
```

### Initial

```text
prev  = 0
prev2 = 0
```

### i = 1

```text
left  = 0 + |10 - 30| = 20
right = INF

current = 20

prev2 = 0
prev = 20
```

### i = 2

```text
left  = 20 + |60 - 10| = 70
right = 0 + |60 - 30| = 30

current = 30

prev2 = 20
prev = 30
```

### i = 3

```text
left  = 30 + |10 - 60| = 80
right = 20 + |10 - 10| = 20

current = 20

prev2 = 30
prev = 20
```

### i = 4

```text
left  = 20 + |60 - 10| = 70
right = 30 + |60 - 60| = 30

current = 30

prev2 = 20
prev = 30
```

### i = 5

```text
left  = 30 + |50 - 60| = 40
right = 20 + |50 - 10| = 60

current = 40
```

### Answer

```text
40
```

---

# DP Pattern

## Identify State

```text
f(i)
=
Minimum energy required to reach stone i
```

## Choices

```text
Jump from:
1. i - 1
2. i - 2
```

## Recurrence

```text
f(i) =
min(
    f(i-1) + cost(i-1 → i),
    f(i-2) + cost(i-2 → i)
)
```

## Base Case

```text
f(0) = 0
```

---

# Follow-Up: Frog Jump with K Distance

Instead of only 1 or 2 jumps, the frog can jump up to `K` stones.

### Recurrence

```text
f(i) =
min(
    f(i-j) + |h[i] - h[i-j]|
)

for all 1 <= j <= K
```

### Complexity

```text
Time Complexity : O(N × K)
Space Complexity: O(N)
```

This is the natural extension of the classic Frog Jump DP problem.
