# Min Cost Climbing Stairs

## Problem Statement

You are given an integer array `cost` where:

```text
cost[i]
```

is the cost of stepping on the `i-th` stair.

Once you pay the cost, you can either climb:

- 1 step
- 2 steps

You can start from:

```text
index 0
or
index 1
```

Return the minimum cost to reach the top of the floor.

The top is beyond the last index.

---

## Example 1

### Input

```text
cost = [10,15,20]
```

### Output

```text
15
```

### Explanation

```text
Start at index 1

Pay 15

Jump directly to top

Total Cost = 15
```

---

## Example 2

### Input

```text
cost = [1,100,1,1,1,100,1,1,100,1]
```

### Output

```text
6
```

### Explanation

Optimal path:

```text
1 → 1 → 1 → 1 → 1 → 1

Total = 6
```

---

# Understanding the Problem

To reach stair `i`, we can come from:

```text
i - 1
or
i - 2
```

The cost of stepping on stair `i` must be paid.

This directly forms a Dynamic Programming problem.

---

# State Definition

Let:

```text
f(i)
=
Minimum cost required to reach stair i
```

---

# Recurrence Relation

To reach stair `i`:

```text
f(i) =
cost[i] +
min(
    f(i-1),
    f(i-2)
)
```

---

## Base Cases

```text
f(0) = cost[0]
f(1) = cost[1]
```

---

## Final Answer

The top can be reached from:

```text
n-1
or
n-2
```

Therefore:

```text
answer =
min(
    f(n-1),
    f(n-2)
)
```

---

# Recursive Solution

## Java Code

```java
class Solution {

    private int solve(int index, int[] cost) {

        if (index == 0) {
            return cost[0];
        }

        if (index == 1) {
            return cost[1];
        }

        return cost[index]
                + Math.min(
                        solve(index - 1, cost),
                        solve(index - 2, cost)
                );
    }

    public int minCostClimbingStairs(int[] cost) {

        int n = cost.length;

        return Math.min(
                solve(n - 1, cost),
                solve(n - 2, cost)
        );
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(2^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Store answers for already solved indices.

Avoid recomputation.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int index, int[] cost, int[] dp) {

        if (index == 0) {
            return cost[0];
        }

        if (index == 1) {
            return cost[1];
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        return dp[index] =
                cost[index]
                + Math.min(
                        solve(index - 1, cost, dp),
                        solve(index - 2, cost, dp)
                );
    }

    public int minCostClimbingStairs(int[] cost) {

        int n = cost.length;

        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        return Math.min(
                solve(n - 1, cost, dp),
                solve(n - 2, cost, dp)
        );
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N) + O(N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i]
=
Minimum cost required to reach stair i
```

---

## Base Cases

```text
dp[0] = cost[0]
dp[1] = cost[1]
```

---

## Transition

```text
dp[i]
=
cost[i]
+
min(dp[i-1], dp[i-2])
```

---

## Java Code

```java
class Solution {

    public int minCostClimbingStairs(int[] cost) {

        int n = cost.length;

        int[] dp = new int[n];

        dp[0] = cost[0];
        dp[1] = cost[1];

        for (int i = 2; i < n; i++) {

            dp[i] =
                    cost[i]
                    + Math.min(
                            dp[i - 1],
                            dp[i - 2]
                    );
        }

        return Math.min(
                dp[n - 1],
                dp[n - 2]
        );
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N)
```

---

# Space Optimized Solution

## Observation

For calculating:

```text
dp[i]
```

we only need:

```text
dp[i-1]
dp[i-2]
```

Thus the DP array can be removed.

---

## Java Code

```java
class Solution {

    public int minCostClimbingStairs(int[] cost) {

        int n = cost.length;

        int prev2 = cost[0];
        int prev1 = cost[1];

        for (int i = 2; i < n; i++) {

            int current =
                    cost[i]
                    + Math.min(prev1, prev2);

            prev2 = prev1;
            prev1 = current;
        }

        return Math.min(prev1, prev2);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(1)
```

---

# Dry Run

## Input

```text
cost = [10,15,20]
```

### Initial

```text
prev2 = 10
prev1 = 15
```

---

### i = 2

```text
current
=
20 + min(15,10)

=
30
```

Update:

```text
prev2 = 15
prev1 = 30
```

---

### Final Answer

```text
min(prev1, prev2)

=
min(30,15)

=
15
```

---

# Visual Representation

```text
Cost

Index:  0   1   2
        |   |   |
Cost : 10  15  20
```

```text
dp[0] = 10

dp[1] = 15

dp[2] = 20 + min(10,15)
      = 30
```

```text
Answer

min(dp[2], dp[1])

=
min(30,15)

=
15
```

---

# DP Pattern

## State

```text
f(i)
=
Minimum cost to reach stair i
```

---

## Choices

```text
Reach i from:

1. i - 1
2. i - 2
```

---

## Recurrence

```text
f(i)
=
cost[i]
+
min(
    f(i-1),
    f(i-2)
)
```

---

## Base Cases

```text
f(0) = cost[0]
f(1) = cost[1]
```

---

## Final Answer

```text
min(
    f(n-1),
    f(n-2)
)
```

Because the top can be reached from either of the last two stairs.

---

# Key Takeaways

✅ Classic DP problem

✅ Very similar to Fibonacci

✅ Introduces:
- State Definition
- Recurrence Relation
- Memoization
- Tabulation
- Space Optimization

✅ Pattern Used:

```text
1-Step + 2-Step DP
```

✅ Similar Problems:
- Fibonacci Number
- Frog Jump
- House Robber
- Climbing Stairs
- Decode Ways
