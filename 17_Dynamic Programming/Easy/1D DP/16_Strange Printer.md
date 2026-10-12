# Strange Printer

## Problem Statement

There is a strange printer with the following two special properties:
1. The printer can only print a sequence of **the same character** each time.
2. At each turn, the printer can print new characters starting from any position and ending at any position, and will cover the original existing characters.

Given a string `s`, return *the **minimum number of turns** the printer needs to print it*.

The overall runtime complexity must be:

```text
O(N³)
```

*(where N is the length of the string s)*

---

## Example 1

### Input

```text
s = "aaabbb"
```

### Output

```text
2
```

### Explanation

```text
1. Print "aaaaaa" (Turn 1)
2. Print "bbb" starting from index 3 -> "aaabbb" (Turn 2)
```

---

## Example 2

### Input

```text
s = "aba"
```

### Output

```text
2
```

### Explanation

```text
1. Print "aaa" (Turn 1)
2. Print "b" at index 1 -> "aba" (Turn 2)
```

---

# Intuition

This problem can be viewed as an **Interval Dynamic Programming** task. We want to find the minimum turns required to print the substring `s[i...j]`.

Let's look at the baseline transition cases:
1. **Case A (Isolated Right Character Match):** If the character at the start matches the character at the end (`s[i] == s[j]`), then the turn used to print the character `s[i]` could have simply been elongated to cover `s[j]` at the same time for free. Any overlapping overwrites in between are covered by subsequent turns. Thus:
   ```text
   f(i, j) = f(i, j - 1)
   ```
2. **Case B (Interval Partitioning Cuts):** If they don't match, or to find a more optimal path through intermediate identical segments, we can split the interval `[i, j]` into two subproblems at an arbitrary split cut index `k` (where `i <= k < j`). We minimize across all possible split partitions:
   ```text
   f(i, j) = min( f(i, k) + f(k + 1, j) ) for all i <= k < j
   ```

---

# State Definition

Let:

```text
f(i, j)
=
The minimum number of turns the printer needs 
to print the substring slice s[i...j]
```

---

# Recurrence

```text
If s[i] == s[j] -> f(i, j) = f(i, j - 1)
Else            -> f(i, j) = min( f(i, k) + f(k + 1, j) ) for all i <= k < j
```

---

# Recursive Solution

## Base Cases

### Empty or Single Character

```text
i > j  -> return 0
i == j -> return 1
```

---

## Java Code

```java
class Solution {

    private int solve(int i, int j, String s) {
        if (i > j) return 0;
        if (i == j) return 1;

        // Base strategy: assume worst case split cut first
        int minTurns = solve(i, j - 1, s) + 1;

        // Explore all possible interval split paths
        for (int k = i; k < j; k++) {
            if (s.charAt(k) == s.charAt(j)) {
                int currentTurns = solve(i, k, s) + solve(k + 1, j - 1, s);
                minTurns = Math.min(minTurns, currentTurns);
            }
        }

        return minTurns;
    }

    public int strangePrinter(String s) {
        if (s == null || s.length() == 0) return 0;
        return solve(0, s.length() - 1, s);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(3^N) -> Exponential partition branching combinations
Space Complexity: O(N) recursion stack memory frames
```

---

# Memoization (Top-Down DP)

## Idea

Store intermediate substring range calculations inside a 2D cache matrix array `dp[n][n]` to bypass duplicate interval operations.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int i, int j, String s, int[][] dp) {
        if (i > j) return 0;
        if (i == j) return 1;

        if (dp[i][j] != 0) {
            return dp[i][j];
        }

        // Base worst-case default step
        int minTurns = solve(i, j - 1, s, dp) + 1;

        // Optimization: optimize if characters match along internal split partitions
        for (int k = i; k < j; k++) {
            if (s.charAt(k) == s.charAt(j)) {
                int currentTurns = solve(i, k, dp, s) + solve(k + 1, j - 1, dp, s); // internal compression shortcut
                minTurns = Math.min(minTurns, currentTurns);
            }
        }

        return dp[i][j] = minTurns;
    }

    private int solve(int i, int k, int[][] dp, String s) {
        return solve(i, k, s, dp);
    }

    public int strangePrinter(String s) {
        if (s == null || s.length() == 0) return 0;
        int n = s.length();
        int[][] dp = new int[n][n];
        return solve(0, n - 1, s, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N³) -> N² states x N split operations per state
Space Complexity : O(N²) memory cache + O(N) call stack
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i][j] = minimum printer turns required to generate the substring s[i...j]
```

---

## Base Case

```text
dp[i][i] = 1
```

---

## Transition

Intervals must be constructed by incrementing step lengths progressively from `1` up to `n - 1`:

```text
For a given length gap:
dp[i][j] = dp[i][j - 1] + 1
If s[i] == s[j], check compression paths: dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k + 1][j - 1])
```

---

## Java Code

```java
class Solution {

    public int strangePrinter(String s) {
        if (s == null || s.length() == 0) return 0;
        int n = s.length();
        int[][] dp = new int[n][n];

        // Base case single characters
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        // Build states from bottom rows up (or processing by length size chunks)
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                
                // Initialize with worst case (print s[j] as an independent separate turn)
                dp[i][j] = dp[i][j - 1] + 1;

                // Attempt to minimize via structural partitioning cuts
                for (int k = i; k < j; k++) {
                    if (s.charAt(k) == s.charAt(j)) {
                        int current = dp[i][k] + (k + 1 <= j - 1 ? dp[k + 1][j - 1] : 0);
                        dp[i][j] = Math.min(dp[i][j], current);
                    }
                }
            }
        }

        return dp[0][n - 1];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N³)
Space Complexity : O(N²)
```

---

## Why Can't This Be Absolutely Space-Optimized to O(1) or O(N)?

Unlike linear 1D recurrence patterns (like *Climbing Stairs* or *House Robber*), where values depend only on a fixed historical step tail, **Interval DP grid cell lookups expand dynamically across diagonal spans**. 

To compute a subproblem length `len`, you simultaneously require values from multiple variable-length split cuts distributed all over the internal matrix cells (`dp[i][k]` and `dp[k+1][j-1]`). Because updates cannot be mapped linearly onto a single static row or column buffer without corrupting upcoming required paths, an **O(N²)** matrix layout remains mandatory.

---

# DP Pattern

## Identify State

```text
f(i, j)
=
Minimum turns required to print string segment s[i...j]
```

---

## Choices

```text
Split the string at any index k (where i <= k < j) to leverage matching character overlaps
```

---

## Recurrence

```text
f(i, j) = min( f(i, j-1) + 1, min( f(i, k) + f(k+1, j-1) ) for s[k] == s[j] )
```

---

## Base Cases

```text
f(i, i) = 1
f(i > j) = 0
```

---

# Key Takeaways

✅ Advanced prototypical Interval Dynamic Programming structure.

✅ Character matching optimizations (`s[k] == s[j]`) act as dynamic shortcut compression bridges, allowing the printer turns to expand forward without costing extra overhead.

✅ Standard matrix length-based loop tabulation ensures all dependency blocks underneath are computed safely before wider intervals query them.

✅ Common Interview Pattern:

```text
Substring Range Optimization with Multi-Split Combinatorial Subproblems
```

### Similar Problems

- Remove Boxes (LeetCode 546)
- Burst Balloons (LeetCode 312)
- Minimum Cost to Merge Stones (LeetCode 1000)
- Valid Palindrome III (LeetCode 1216)
