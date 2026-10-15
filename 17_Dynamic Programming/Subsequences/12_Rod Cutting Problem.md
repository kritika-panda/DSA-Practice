# Rod Cutting Problem

## Problem Statement

Given a rod of length `N` and an array `price[]` where:

```text
price[i]
```

represents the selling price of a rod piece of length:

```text
i + 1
```

Find the maximum obtainable value by cutting the rod into pieces and selling them.

You may cut the rod:

```text
Any number of times
```

or

```text
Not cut it at all.
```

---

## Example

```text
Input:

N = 5

price = [2,5,7,8,10]

Length: 1 2 3 4 5
Price : 2 5 7 8 10
```

Possible cuts:

```text
5
Profit = 10

2 + 3
Profit = 5 + 7 = 12

1 + 2 + 2
Profit = 2 + 5 + 5 = 12
```

Maximum Profit:

```text
12
```

Output:

```text
12
```

---

# Intuition

Each rod length can be chosen:

```text
Unlimited times
```

Example:

```text
Length 2 piece
```

can be used repeatedly:

```text
2 + 2 + 2 + ...
```

Therefore:

```text
Rod Cutting
=
Unbounded Knapsack
```

---

# Mapping to Unbounded Knapsack

```text
Rod Piece Length  -> Weight

Price             -> Profit

Rod Length N      -> Capacity
```

Example:

```text
Length = [1,2,3,4,5]

Price  = [2,5,7,8,10]

Rod Length = 5
```

becomes:

```text
Weight = [1,2,3,4,5]

Profit = [2,5,7,8,10]

Capacity = 5
```

---

# Recursive Formulation

Define:

```text
f(index, length)
```

Meaning:

```text
Maximum profit obtainable
using pieces 0...index
to form rod length 'length'
```

---

## Not Take

```text
f(index - 1, length)
```

---

## Take

Current rod piece length:

```java
rodLength = index + 1
```

If:

```text
rodLength <= length
```

then:

```text
price[index]
+
f(index,
  length - rodLength)
```

Notice:

```text
index remains same
```

because pieces can be reused.

---

## Recurrence

```text
max(take, notTake)
```

---

# Base Case

When:

```text
index == 0
```

Only length-1 piece remains.

We can use it:

```text
length times
```

Profit:

```java
length * price[0]
```

```java
if(index == 0)
    return length * price[0];
```

---

# Memoization Solution

```java
class Solution {

    public int cutRod(int[] price, int n) {

        int[][] dp =
            new int[n][n + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(
                n - 1,
                n,
                price,
                dp);
    }

    private int solve(int index,
                      int length,
                      int[] price,
                      int[][] dp) {

        if (index == 0) {

            return length * price[0];
        }

        if (dp[index][length] != -1)
            return dp[index][length];

        int notTake =
            solve(index - 1,
                  length,
                  price,
                  dp);

        int take = Integer.MIN_VALUE;

        int rodLength = index + 1;

        if (rodLength <= length) {

            take =
                price[index]
                + solve(
                    index,
                    length - rodLength,
                    price,
                    dp);
        }

        return dp[index][length]
                = Math.max(
                    take,
                    notTake);
    }
}
```

---

# Tabulation Solution

## DP State

```text
dp[i][len]
```

Meaning:

```text
Maximum profit using
rod pieces 1...(i+1)
for total rod length len
```

---

## Initialization

```java
for (int len = 0;
     len <= n;
     len++) {

    dp[0][len] =
        len * price[0];
}
```

---

## Code

```java
class Solution {

    public int cutRod(int[] price, int n) {

        int[][] dp =
            new int[n][n + 1];

        for (int len = 0;
             len <= n;
             len++) {

            dp[0][len] =
                len * price[0];
        }

        for (int i = 1; i < n; i++) {

            int rodLength = i + 1;

            for (int len = 0;
                 len <= n;
                 len++) {

                int notTake =
                        dp[i - 1][len];

                int take =
                        Integer.MIN_VALUE;

                if (rodLength <= len) {

                    take =
                        price[i]
                        + dp[i]
                            [len - rodLength];
                }

                dp[i][len] =
                    Math.max(
                        take,
                        notTake);
            }
        }

        return dp[n - 1][n];
    }
}
```

---

# Space Optimized Solution

```java
class Solution {

    public int cutRod(int[] price, int n) {

        int[] dp =
            new int[n + 1];

        for (int len = 0;
             len <= n;
             len++) {

            dp[len] =
                len * price[0];
        }

        for (int i = 1; i < n; i++) {

            int rodLength = i + 1;

            for (int len = rodLength;
                 len <= n;
                 len++) {

                dp[len] = Math.max(
                    dp[len],
                    price[i]
                    + dp[len - rodLength]
                );
            }
        }

        return dp[n];
    }
}
```

---

# Most Optimal 1D DP

```java
class Solution {

    public int cutRod(int[] price, int n) {

        int[] dp =
            new int[n + 1];

        for (int len = 0;
             len <= n;
             len++) {

            dp[len] =
                len * price[0];
        }

        for (int piece = 2;
             piece <= n;
             piece++) {

            for (int len = piece;
                 len <= n;
                 len++) {

                dp[len] = Math.max(
                    dp[len],
                    price[piece - 1]
                    + dp[len - piece]
                );
            }
        }

        return dp[n];
    }
}
```

---

# Dry Run

```text
N = 5

price = [2,5,7,8,10]
```

Possible choices:

```text
5            -> 10

2 + 3        -> 12

1 + 2 + 2    -> 12

1 + 1 + 1 + 1 + 1 -> 10
```

Maximum:

```text
12
```

Answer:

```text
12
```

---

# Why is this Unbounded Knapsack?

When we cut a rod piece of length:

```text
3
```

we can still cut another piece of length:

```text
3
```

again.

Therefore:

```text
Take case:

price[i]
+
f(index,
  remainingLength)
```

Notice:

```text
index remains same
```

This is exactly the Unbounded Knapsack pattern.

---

# Pattern Recognition

Whenever you see:

```text
Cut rod

Maximum revenue

Infinite usage of piece lengths

Repeated selection allowed
```

Think:

```text
✅ Unbounded Knapsack
```

Mapping:

```text
Piece Length -> Weight

Price        -> Profit

Rod Length   -> Capacity
```

---

# Relation with Previous Problems

```text
0/1 Knapsack
        ↓
max()
index - 1 after take

Unbounded Knapsack
        ↓
max()
same index after take

Minimum Coins
        ↓
min()
same index after take

Coin Change II
        ↓
count ways
same index after take

Rod Cutting
        ↓
max()
same index after take
```

---

# Complexity Analysis

### Memoization

```text
Time  : O(N²)

Space : O(N²)
```

### Tabulation

```text
Time  : O(N²)

Space : O(N²)
```

### Optimal 1D DP

```text
Time  : O(N²)

Space : O(N)
```

Where:

```text
N = length of rod
```
