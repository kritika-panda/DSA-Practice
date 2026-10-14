# Decode Ways

## Problem Statement

A message containing letters from:

```text
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

has been encoded as a string containing digits.

Given a string `s`, return the number of ways to decode it.

A leading zero is invalid.

---

## Example 1

### Input

```text
s = "12"
```

### Output

```text
2
```

### Explanation

```text
"12"

1 2  -> AB
12   -> L
```

Total ways:

```text
2
```

---

## Example 2

### Input

```text
s = "226"
```

### Output

```text
3
```

### Explanation

```text
2 2 6 -> B B F
22 6  -> V F
2 26  -> B Z
```

Total:

```text
3
```

---

## Example 3

### Input

```text
s = "06"
```

### Output

```text
0
```

### Explanation

```text
0 cannot be decoded
```

---

# Intuition

At every position, we have two choices:

### Choice 1

Decode one digit.

Example:

```text
"123"

Take:
1

Remaining:
"23"
```

---

### Choice 2

Decode two digits.

Example:

```text
"123"

Take:
12

Remaining:
"3"
```

But the two-digit number must lie between:

```text
10 and 26
```

---

# State Definition

Let:

```text
f(i)
=
Number of ways to decode substring
starting from index i
```

---

# Recurrence

## Single Digit

If:

```text
s[i] != '0'
```

then:

```text
f(i+1)
```

ways are possible.

---

## Double Digit

If:

```text
10 <= number <= 26
```

then:

```text
f(i+2)
```

ways are possible.

---

## Formula

```text
f(i)
=
f(i+1)
+
f(i+2)
```

whenever both choices are valid.

---

# Recursive Solution

## Base Cases

### Reached End

```text
index == n

return 1
```

Why?

Because we found one valid decoding.

---

### Invalid Zero

```text
s[index] == '0'

return 0
```

---

## Java Code

```java
class Solution {

    private int solve(int index, String s) {

        if (index == s.length()) {
            return 1;
        }

        if (s.charAt(index) == '0') {
            return 0;
        }

        int ways = solve(index + 1, s);

        if (index + 1 < s.length()) {

            int number =
                    (s.charAt(index) - '0') * 10
                    + (s.charAt(index + 1) - '0');

            if (number >= 10 && number <= 26) {
                ways += solve(index + 2, s);
            }
        }

        return ways;
    }

    public int numDecodings(String s) {
        return solve(0, s);
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

Store answers for each index.

Avoid recomputing overlapping subproblems.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int index, String s, int[] dp) {

        if (index == s.length()) {
            return 1;
        }

        if (s.charAt(index) == '0') {
            return 0;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int ways = solve(index + 1, s, dp);

        if (index + 1 < s.length()) {

            int number =
                    (s.charAt(index) - '0') * 10
                    + (s.charAt(index + 1) - '0');

            if (number >= 10 && number <= 26) {
                ways += solve(index + 2, s, dp);
            }
        }

        return dp[index] = ways;
    }

    public int numDecodings(String s) {

        int[] dp = new int[s.length()];

        Arrays.fill(dp, -1);

        return solve(0, s, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity : O(N)
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i]
=
Number of ways to decode substring
starting from index i
```

---

## Base Case

```text
dp[n] = 1
```

This represents:

```text
Empty string
```

which has exactly one valid decoding.

---

## Transition

### Single Digit

```text
dp[i] += dp[i+1]
```

---

### Double Digit

If:

```text
10 <= value <= 26
```

then:

```text
dp[i] += dp[i+2]
```

---

## Java Code

```java
class Solution {

    public int numDecodings(String s) {

        int n = s.length();

        int[] dp = new int[n + 1];

        dp[n] = 1;

        for (int i = n - 1; i >= 0; i--) {

            if (s.charAt(i) == '0') {
                continue;
            }

            dp[i] = dp[i + 1];

            if (i + 1 < n) {

                int number =
                        (s.charAt(i) - '0') * 10
                        + (s.charAt(i + 1) - '0');

                if (number >= 10 && number <= 26) {
                    dp[i] += dp[i + 2];
                }
            }
        }

        return dp[0];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity : O(N)
```

---

# Space Optimized Solution

## Observation

We only need:

```text
dp[i+1]
dp[i+2]
```

while calculating:

```text
dp[i]
```

Therefore we can optimize to O(1) space.

---

## Java Code

```java
class Solution {

    public int numDecodings(String s) {

        int n = s.length();

        int next1 = 1; // dp[n]
        int next2 = 0; // dp[n+1]

        for (int i = n - 1; i >= 0; i--) {

            int current = 0;

            if (s.charAt(i) != '0') {

                current = next1;

                if (i + 1 < n) {

                    int number =
                            (s.charAt(i) - '0') * 10
                            + (s.charAt(i + 1) - '0');

                    if (number >= 10 && number <= 26) {
                        current += next2;
                    }
                }
            }

            next2 = next1;
            next1 = current;
        }

        return next1;
    }
}
```

---

# Dry Run

## Input

```text
s = "226"
```

---

### Initialize

```text
dp[3] = 1
```

---

### i = 2

```text
"6"

dp[2] = dp[3]

= 1
```

---

### i = 1

```text
"26"

Single digit:
dp[2] = 1

Two digit:
dp[3] = 1

dp[1] = 2
```

---

### i = 0

```text
"226"

Single digit:
dp[1] = 2

Two digit:
dp[2] = 1

dp[0] = 3
```

---

### Answer

```text
3
```

---

# Visualization

## String

```text
"226"
```

```text
Index : 0   1   2
Digit : 2   2   6
```

Possible Decodings:

```text
2 2 6  -> B B F

22 6   -> V F

2 26   -> B Z
```

Total:

```text
3
```

---

# DP Pattern

## State

```text
f(i)
=
Number of ways to decode
substring from i onwards
```

---

## Choices

```text
1. Take one digit

2. Take two digits
```

---

## Recurrence

```text
f(i)
=
f(i+1)
+
f(i+2)
```

when both choices are valid.

---

## Base Cases

```text
f(n) = 1

f(i) = 0
if s[i] == '0'
```

---

# Common Edge Cases

## Case 1

```text
s = "0"
```

Output:

```text
0
```

---

## Case 2

```text
s = "06"
```

Output:

```text
0
```

---

## Case 3

```text
s = "10"
```

Output:

```text
1
```

```text
10 -> J
```

---

## Case 4

```text
s = "100"
```

Output:

```text
0
```

---

## Case 5

```text
s = "27"
```

Output:

```text
1
```

```text
2 7 -> BG

27 is not valid because
27 > 26
```

---

# Key Takeaways

✅ Classic DP counting problem

✅ Similar structure to Fibonacci

✅ At every index:
- Take 1 digit
- Take 2 digits

✅ Important handling of:
- Leading zeros
- Invalid two-digit numbers

✅ Common Interview Pattern:

```text
Count Number of Ways
```

### Similar Problems

- Climbing Stairs
- Coin Change II
- Target Sum
- Word Break
- Partition Equal Subset Sum
- Distinct Subsequences
