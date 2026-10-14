# Frog Jump with K Distance

## Problem Statement

A frog is crossing a river. The river is divided into `N` stones, and the height of each stone is given in an array `heights[]`.

The frog starts at the first stone (`index 0`) and wants to reach the last stone (`index N-1`).

At any point, the frog can jump up to `K` distance. That is, from stone `i`, the frog can jump to any stone `i + 1`, `i + 2`, ..., `i + K`.

The cost of a jump from stone `i` to stone `j` is:

```text

|heights[i] - heights[j]|
```

Return the minimum total energy required for the frog to reach the last stone.

---

## Example 1

### Input

```text
heights = [10, 30, 40, 50, 20]
k = 3
```

### Output

```text
30
```

### Explanation

Optimal path:

```text
0 → 1 → 4

Cost =

|10 - 30| +
|30 - 20|

= 20 + 10
= 30
```

---

## Example 2

### Input

```text
heights = [10, 20, 10]
k = 1
```

### Output

```text
20
```

---

# Intuition

To reach stone `i`, the frog can come from any of the previous `K` stones: `i - 1`, `i - 2`, ..., `i - K`.

Therefore,

```text
Minimum Cost(i)
=
min(
    cost from i-1,
    cost from i-2,
    ...
    cost from i-K
)
```

This forms a Dynamic Programming problem extending the classic 1 or 2 steps configuration.

---

# Recursive Solution

## Recurrence

```text
f(i) = 
min(
    f(i-j) + |h[i] - h[i-j]|
)
for all 1 <= j <= K where (i - j) >= 0
```

### Base Case

```text
f(0) = 0
```

---

## Java Code (Recursion)

```java
class Solution {

    private int solve(int index, int[] heights, int k) {

        if (index == 0) {
            return 0;
        }

        int minSteps = Integer.MAX_VALUE;

        // Explore all possible jumps from 1 up to K
        for (int j = 1; j <= k; j++) {
            if (index - j >= 0) {
                int jumpCost = 
                        solve(index - j, heights, k) 
                        + Math.abs(heights[index] - heights[index - j]);
                
                minSteps = Math.min(minSteps, jumpCost);
            }
        }

        return minSteps;
    }

    public int frogJumpWithK(int[] heights, int k) {
        return solve(heights.length - 1, heights, k);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(K^N)
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

    private int solve(int index, int[] heights, int k, int[] dp) {

        if (index == 0) {
            return 0;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int minSteps = Integer.MAX_VALUE;

        for (int j = 1; j <= k; j++) {
            if (index - j >= 0) {
                int jumpCost = 
                        solve(index - j, heights, k, dp) 
                        + Math.abs(heights[index] - heights[index - j]);
                
                minSteps = Math.min(minSteps, jumpCost);
            }
        }

        return dp[index] = minSteps;
    }

    public int frogJumpWithK(int[] heights, int k) {
        int n = heights.length;
        int[] dp = new int[n];
        Arrays.fill(dp, -1);
        return solve(n - 1, heights, k, dp);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N × K)
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
    dp[i-j] + |h[i] - h[i-j]|
)
for all 1 <= j <= K where (i - j) >= 0
```

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    public int frogJumpWithK(int[] heights, int k) {
        int n = heights.length;
        int[] dp = new int[n];
        
        dp[0] = 0;

        for (int i = 1; i < n; i++) {
            int minSteps = Integer.MAX_VALUE;
            
            for (int j = 1; j <= k; j++) {
                if (i - j >= 0) {
                    int jumpCost = 
                            dp[i - j] 
                            + Math.abs(heights[i] - heights[i - j]);
                    
                    minSteps = Math.min(minSteps, jumpCost);
                }
            }
            dp[i] = minSteps;
        }

        return dp[n - 1];
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N × K)
Space Complexity: O(N)
```

---

# Space Optimization

## Observation

For computing:

```text
dp[i]
```

we need the previous `K` states (`dp[i-1]`, `dp[i-2]`, ..., `dp[i-K]`). We can use a fixed-size list or circular array of size `K` instead of the full size `N` array.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    public int frogJumpWithK(int[] heights, int k) {
        int n = heights.length;
        
        // Circular buffer or fixed tracker of size K to store the last K states
        int[] lastK = new int[k];
        lastK[0] = 0; // base case for index 0 mapped to buffer

        for (int i = 1; i < n; i++) {
            int minSteps = Integer.MAX_VALUE;

            for (int j = 1; j <= k; j++) {
                if (i - j >= 0) {
                    int jumpCost = 
                            lastK[(i - j) % k] 
                            + Math.abs(heights[i] - heights[i - j]);
                    
                    minSteps = Math.min(minSteps, jumpCost);
                }
            }
            // Update the circular buffer for the current step
            lastK[i % k] = minSteps;
        }

        return lastK[(n - 1) % k];
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N × K)
Space Complexity: O(K)
```

---

# Dry Run

```text
heights =, k = 3
```

### Initial

```text
lastK = [0, 0, 0]  (representing stone 0)
```

### i = 1

```text
j = 1: jumpCost = lastK[0] + |30 - 10| = 0 + 20 = 20
minSteps = 20
lastK[1] = 20
```

### i = 2

```text
j = 1: jumpCost = lastK[1] + |40 - 30| = 20 + 10 = 30
j = 2: jumpCost = lastK[0] + |40 - 10| = 0 + 30 = 30
minSteps = 30
lastK[2] = 30
```

### i = 3

```text
j = 1: jumpCost = lastK[2] + |50 - 40| = 30 + 10 = 40
j = 2: jumpCost = lastK[1] + |50 - 30| = 20 + 20 = 40
j = 3: jumpCost = lastK[0] + |50 - 10| = 0 + 40 = 40
minSteps = 40
lastK[3 % 3] = lastK[0] = 40
```

### i = 4

```text
j = 1: jumpCost = lastK[3 % 3] + |20 - 50| = 40 + 30 = 70
j = 2: jumpCost = lastK[2] + |20 - 40| = 30 + 20 = 50
j = 3: jumpCost = lastK[1] + |20 - 30| = 20 + 10 = 30
minSteps = 30
lastK[4 % 3] = lastK[1] = 30
```

### Answer

```text
lastK[4 % 3] = lastK[1] = 30
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
Jump from any stone (i - j) where 1 <= j <= K
```

## Recurrence

```text
f(i) =
min(
    f(i-j) + cost(i-j → i)
)
for all 1 <= j <= K
```

## Base Case

```text
f(0) = 0
```
