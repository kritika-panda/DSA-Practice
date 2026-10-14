# Perfect Squares

## Problem Statement

Given an integer `n`, return the least number of perfect square numbers that sum to `n`.

A perfect square is an integer that is the square of an integer.

Example:

```text
1 = 1²
4 = 2²
9 = 3²
16 = 4²
25 = 5²
```

You may use the same perfect square multiple times.

---

## Example 1

### Input

```text
n = 12
```

### Output

```text
3
```

### Explanation

```text
12 = 4 + 4 + 4

= 2² + 2² + 2²
```

Minimum count:

```text
3
```

---

## Example 2

### Input

```text
n = 13
```

### Output

```text
2
```

### Explanation

```text
13 = 9 + 4

= 3² + 2²
```

Minimum count:

```text
2
```

---

# Understanding the Problem

For any number:

```text
n
```

we can choose any square:

```text
1, 4, 9, 16...
```

and reduce the problem.

Example:

```text
n = 12
```

Choices:

```text
12 - 1 = 11

12 - 4 = 8

12 - 9 = 3
```

The answer is:

```text
1 + minimum answer among all choices
```

---

# DP State

Let:

```text
f(n)
=
Minimum perfect squares required
to form n
```

---

# Recurrence Relation

Try every possible square.

```text
f(n)
=
1 + min(
        f(n-1),
        f(n-4),
        f(n-9),
        ...
    )
```

where:

```text
square <= n
```

---

# Base Case

```text
f(0) = 0
```

Reason:

```text
No number required to form 0.
```

---

# Recursive Solution

## Java Code

```java
class Solution {

    private int solve(int n) {

        if (n == 0) {
            return 0;
        }

        int answer = Integer.MAX_VALUE;

        for (int i = 1; i * i <= n; i++) {

            answer = Math.min(
                    answer,
                    1 + solve(n - i * i)
            );
        }

        return answer;
    }

    public int numSquares(int n) {
        return solve(n);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : Exponential
Space Complexity : O(N)
```

Lots of repeated calculations occur.

---

# Memoization (Top-Down DP)

## Idea

Store answers for every value of `n`.

Avoid solving the same subproblem again.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int n, int[] dp) {

        if (n == 0) {
            return 0;
        }

        if (dp[n] != -1) {
            return dp[n];
        }

        int answer = Integer.MAX_VALUE;

        for (int i = 1; i * i <= n; i++) {

            answer = Math.min(
                    answer,
                    1 + solve(n - i * i, dp)
            );
        }

        return dp[n] = answer;
    }

    public int numSquares(int n) {

        int[] dp = new int[n + 1];

        Arrays.fill(dp, -1);

        return solve(n, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N * √N)

Space Complexity : O(N)
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i]
=
Minimum perfect squares
required to form i
```

---

## Base Case

```text
dp[0] = 0
```

---

## Transition

For every number:

```text
i
```

try all possible squares:

```text
1², 2², 3²...
```

Formula:

```text
dp[i]
=
min(
    dp[i],
    1 + dp[i - square]
)
```

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    public int numSquares(int n) {

        int[] dp = new int[n + 1];

        Arrays.fill(dp, Integer.MAX_VALUE);

        dp[0] = 0;

        for (int i = 1; i <= n; i++) {

            for (int j = 1; j * j <= i; j++) {

                int square = j * j;

                dp[i] = Math.min(
                        dp[i],
                        1 + dp[i - square]
                );
            }
        }

        return dp[n];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N * √N)

Space Complexity : O(N)
```

---

# Dry Run

## Input

```text
n = 12
```

---

### Initialize

```text
dp[0] = 0
```

---

### i = 1

```text
1 = 1²

dp[1] = 1
```

---

### i = 2

```text
2 = 1 + 1

dp[2] = 2
```

---

### i = 3

```text
3 = 1 + 1 + 1

dp[3] = 3
```

---

### i = 4

```text
4 = 2²

dp[4] = 1
```

---

### i = 5

```text
5 = 4 + 1

dp[5] = 2
```

---

### i = 6

```text
6 = 4 + 1 + 1

dp[6] = 3
```

---

### i = 7

```text
7 = 4 + 1 + 1 + 1

dp[7] = 4
```

---

### i = 8

```text
8 = 4 + 4

dp[8] = 2
```

---

### i = 9

```text
9 = 3²

dp[9] = 1
```

---

### i = 10

```text
10 = 9 + 1

dp[10] = 2
```

---

### i = 11

```text
11 = 9 + 1 + 1

dp[11] = 3
```

---

### i = 12

```text
12 = 4 + 4 + 4

dp[12] = 3
```

---

## Final Answer

```text
3
```

---

# Visualization

For:

```text
n = 12
```

Possible decompositions:

```text
12 = 1+1+1+1+1+1+1+1+1+1+1+1
Count = 12

12 = 4+4+1+1+1+1
Count = 6

12 = 9+1+1+1
Count = 4

12 = 4+4+4
Count = 3 ✅
```

Minimum:

```text
3
```

---

# Relation to Coin Change

Perfect Squares is basically:

## Coin Change (Minimum Coins)

Coins:

```text
1
4
9
16
25
...
```

Target:

```text
n
```

Goal:

```text
Minimum coins needed
to reach target.
```

---

# DP Pattern

## State

```text
f(n)
=
Minimum squares needed
to make n
```

---

## Choices

Choose any square:

```text
1²
2²
3²
...
```

that does not exceed `n`.

---

## Recurrence

```text
f(n)
=
1 + min(
        f(n - 1²),
        f(n - 2²),
        f(n - 3²),
        ...
      )
```

---

## Base Case

```text
f(0) = 0
```

---

# Pattern Recognition

When you see:

```text
Minimum steps

Minimum coins

Minimum operations

Minimum jumps
```

Think:

```text
Possible DP problem
```

Common structure:

```text
answer
=
1 + min(smaller states)
```

---

# Similar Problems

## Fibonacci Pattern

- Fibonacci Number
- Climbing Stairs
- Min Cost Climbing Stairs

---

## Unbounded Knapsack Pattern

- Coin Change
- Perfect Squares
- Minimum Cost for Tickets
- Rod Cutting

---

## Minimum DP Problems

- Frog Jump
- Perfect Squares
- Coin Change
- Minimum Path Sum
- Edit Distance

---

# Key Takeaways

✅ Perfect Squares is a classic DP problem

✅ Can be viewed as a variation of Coin Change

✅ State:

```text
dp[i]
=
Minimum squares needed to form i
```

✅ Recurrence:

```text
dp[i]
=
min(
    1 + dp[i-square]
)
```

✅ Time Complexity:

```text
O(N * √N)
```

✅ Very important foundation problem before:

- Coin Change
- Minimum Cost Tickets
- Unbounded Knapsack
- Rod Cutting
- Integer Break
