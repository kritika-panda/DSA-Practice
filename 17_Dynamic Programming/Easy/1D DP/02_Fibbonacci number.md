# Fibonacci Number

## Problem Statement

The Fibonacci sequence is defined as:

```text
F(0) = 0
F(1) = 1
```

For every `n >= 2`:

```text
F(n) = F(n - 1) + F(n - 2)
```

Given an integer `n`, return the `n-th` Fibonacci number.

---

## Example 1

### Input

```text
n = 2
```

### Output

```text
1
```

### Explanation

```text
F(2) = F(1) + F(0)
     = 1 + 0
     = 1
```

---

## Example 2

### Input

```text
n = 3
```

### Output

```text
2
```

### Explanation

```text
F(3) = F(2) + F(1)
     = 1 + 1
     = 2
```

---

## Example 3

### Input

```text
n = 4
```

### Output

```text
3
```

### Explanation

```text
F(4)
= F(3) + F(2)
= 2 + 1
= 3
```

---

# Intuition

To calculate:

```text
F(n)
```

we need:

```text
F(n-1)
F(n-2)
```

This naturally leads to a recursive solution.

---

# Recursive Solution

## Recurrence

```text
F(n) = F(n-1) + F(n-2)
```

### Base Cases

```text
F(0) = 0
F(1) = 1
```

---

## Java Code

```java
class Solution {

    public int fib(int n) {

        if (n <= 1) {
            return n;
        }

        return fib(n - 1) + fib(n - 2);
    }
}
```

---

## Recursion Tree

For:

```text
fib(5)
```

```text
                 fib(5)
               /        \
          fib(4)       fib(3)
         /     \       /     \
    fib(3) fib(2) fib(2) fib(1)
      ...
```

Notice that the same subproblems are solved repeatedly.

Example:

```text
fib(3)
fib(2)
```

appear multiple times.

---

## Complexity Analysis

```text
Time Complexity : O(2^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Store already computed Fibonacci values.

Whenever the same state appears again, use the stored answer.

---

## DP State

```text
dp[i] = Fibonacci value of i
```

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int n, int[] dp) {

        if (n <= 1) {
            return n;
        }

        if (dp[n] != -1) {
            return dp[n];
        }

        return dp[n] =
                solve(n - 1, dp)
                + solve(n - 2, dp);
    }

    public int fib(int n) {

        int[] dp = new int[n + 1];

        Arrays.fill(dp, -1);

        return solve(n, dp);
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

## Idea

Build the DP array from smaller answers.

---

## DP Definition

```text
dp[i] = Fibonacci value at index i
```

### Base Cases

```text
dp[0] = 0
dp[1] = 1
```

### Transition

```text
dp[i] = dp[i-1] + dp[i-2]
```

---

## Java Code

```java
class Solution {

    public int fib(int n) {

        if (n <= 1) {
            return n;
        }

        int[] dp = new int[n + 1];

        dp[0] = 0;
        dp[1] = 1;

        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
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

# Space Optimization

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

Therefore, the entire DP array is unnecessary.

---

## Java Code

```java
class Solution {

    public int fib(int n) {

        if (n <= 1) {
            return n;
        }

        int prev2 = 0;
        int prev1 = 1;

        for (int i = 2; i <= n; i++) {

            int current = prev1 + prev2;

            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
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
n = 5
```

### Initial State

```text
prev2 = 0
prev1 = 1
```

---

### i = 2

```text
current = 1 + 0 = 1

prev2 = 1
prev1 = 1
```

---

### i = 3

```text
current = 1 + 1 = 2

prev2 = 1
prev1 = 2
```

---

### i = 4

```text
current = 2 + 1 = 3

prev2 = 2
prev1 = 3
```

---

### i = 5

```text
current = 3 + 2 = 5

prev2 = 3
prev1 = 5
```

---

### Answer

```text
5
```

---

# DP Pattern

## State

```text
f(i)
=
Fibonacci value at index i
```

---

## Choices

```text
Take contribution from:

1. f(i-1)
2. f(i-2)
```

---

## Recurrence

```text
f(i) = f(i-1) + f(i-2)
```

---

## Base Cases

```text
f(0) = 0
f(1) = 1
```

---

# Visualization

```text
Index : 0  1  2  3  4  5  6  7

Value : 0  1  1  2  3  5  8 13
```

```text
0 + 1 = 1
1 + 1 = 2
1 + 2 = 3
2 + 3 = 5
3 + 5 = 8
5 + 8 = 13
```

---

# Key Takeaways

✅ First DP problem everyone should learn

✅ Introduces:
- Recursion
- Memoization
- Tabulation
- Space Optimization

✅ Foundation for:
- Climbing Stairs
- House Robber
- Coin Change
- Longest Increasing Subsequence
- Dynamic Programming in general

---

# Complexity Comparison

| Approach | Time Complexity | Space Complexity |
|----------|----------------|------------------|
| Recursion | O(2^N) | O(N) |
| Memoization | O(N) | O(N) |
| Tabulation | O(N) | O(N) |
| Space Optimized | O(N) | O(1) |

---

# Interview Follow-Up

Can Fibonacci be solved in:

```text
O(log N)
```

Answer:

✅ Yes

Using:

- Matrix Exponentiation
- Fast Doubling Method

These are advanced techniques used when `n` is very large.
