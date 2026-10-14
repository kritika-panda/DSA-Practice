# Subset Sum Equal To Target

## Problem Statement

Given an array `arr[]` of size `n` and an integer `target`.

Determine whether there exists a subset whose sum is exactly equal to `target`.

Return:

```text
true  -> if such subset exists
false -> otherwise
```

---

## Example 1

```text
Input:
arr = [1,2,3,4]
target = 4

Output:
true

Explanation:

Subset {4} exists.
```

---

## Example 2

```text
Input:
arr = [3,34,4,12,5,2]
target = 9

Output:
true

Explanation:

Subset {4,5} gives sum 9.
```

---

## Example 3

```text
Input:
arr = [2,4,6]
target = 5

Output:
false
```

---

# Intuition

For every element, we have two choices:

```text
Take it
OR
Not Take it
```

Example:

```text
arr = [1,2,3]

Target = 3

At index 0

             1
          /     \
      Take     Not Take
```

This naturally leads to recursion.

---

# DP State

Define:

```java
dp[index][target]
```

Meaning:

```text
Can we form "target"
using elements from index 0...index ?
```

Return:

```text
true  -> possible
false -> not possible
```

---

# Recurrence

## Not Take

```java
notTake =
solve(index-1,target)
```

---

## Take

Possible only when:

```java
arr[index] <= target
```

```java
take =
solve(index-1,target-arr[index])
```

---

## Transition

```java
dp[index][target] =
take || notTake
```

---

# Approach 1: Recursion

## Code

```java
class Solution {

    static boolean isSubsetSum(int arr[], int target) {

        return solve(arr.length - 1, target, arr);
    }

    static boolean solve(int index,
                         int target,
                         int[] arr) {

        if (target == 0)
            return true;

        if (index == 0)
            return arr[0] == target;

        boolean notTake =
                solve(index - 1, target, arr);

        boolean take = false;

        if (arr[index] <= target) {

            take = solve(
                    index - 1,
                    target - arr[index],
                    arr
            );
        }

        return take || notTake;
    }
}
```

---

## Complexity

```text
Time  : O(2^N)

Space : O(N)
```

---

# Approach 2: Memoization

## Code

```java
class Solution {

    static Boolean[][] dp;

    static boolean isSubsetSum(int arr[], int target) {

        dp = new Boolean[arr.length][target + 1];

        return solve(
                arr.length - 1,
                target,
                arr
        );
    }

    static boolean solve(int index,
                         int target,
                         int[] arr) {

        if (target == 0)
            return true;

        if (index == 0)
            return arr[0] == target;

        if (dp[index][target] != null)
            return dp[index][target];

        boolean notTake =
                solve(index - 1, target, arr);

        boolean take = false;

        if (arr[index] <= target) {

            take = solve(
                    index - 1,
                    target - arr[index],
                    arr
            );
        }

        return dp[index][target]
                = take || notTake;
    }
}
```

---

## Complexity

```text
Time  : O(N × Target)

Space : O(N × Target) + O(N)
```

---

# Approach 3: Tabulation

## Base Cases

### Target 0

```java
dp[i][0] = true
```

because empty subset always forms sum 0.

---

### First Element

```java
dp[0][arr[0]] = true
```

if within range.

---

## Code

```java
class Solution {

    static boolean isSubsetSum(int arr[], int k) {

        int n = arr.length;

        boolean[][] dp =
                new boolean[n][k + 1];

        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }

        if (arr[0] <= k) {
            dp[0][arr[0]] = true;
        }

        for (int index = 1;
             index < n;
             index++) {

            for (int target = 1;
                 target <= k;
                 target++) {

                boolean notTake =
                        dp[index - 1][target];

                boolean take = false;

                if (arr[index] <= target) {

                    take =
                            dp[index - 1]
                              [target - arr[index]];
                }

                dp[index][target] =
                        take || notTake;
            }
        }

        return dp[n - 1][k];
    }
}
```

---

## Complexity

```text
Time  : O(N × Target)

Space : O(N × Target)
```

---

# Approach 4: Space Optimized

## Observation

Current row depends only on:

```text
Previous Row
```

So use:

```java
prev[]
curr[]
```

---

## Code

```java
class Solution {

    static boolean isSubsetSum(int arr[], int k) {

        int n = arr.length;

        boolean[] prev =
                new boolean[k + 1];

        prev[0] = true;

        if (arr[0] <= k) {
            prev[arr[0]] = true;
        }

        for (int index = 1;
             index < n;
             index++) {

            boolean[] curr =
                    new boolean[k + 1];

            curr[0] = true;

            for (int target = 1;
                 target <= k;
                 target++) {

                boolean notTake =
                        prev[target];

                boolean take = false;

                if (arr[index] <= target) {

                    take =
                            prev[target - arr[index]];
                }

                curr[target] =
                        take || notTake;
            }

            prev = curr;
        }

        return prev[k];
    }
}
```

---

## Complexity

```text
Time  : O(N × Target)

Space : O(Target)
```

---

# Further Optimization (Single Array)

Since we only need the previous row:

Traverse target in reverse order.

---

## Code

```java
class Solution {

    static boolean isSubsetSum(int arr[], int target) {

        boolean[] dp =
                new boolean[target + 1];

        dp[0] = true;

        for (int num : arr) {

            for (int t = target;
                 t >= num;
                 t--) {

                dp[t] = dp[t]
                        || dp[t - num];
            }
        }

        return dp[target];
    }
}
```

---

## Why Reverse Traversal?

Suppose:

```text
arr = [2]
target = 4
```

If we iterate forward:

```text
dp[2] becomes true

Then dp[4]
uses updated dp[2]
```

which means:

```text
2 is used twice
```

Not allowed in Subset Sum.

Reverse traversal guarantees:

```text
Each element is used only once.
```

---

# Dry Run

```text
arr = [1,2,3]
target = 5
```

Initial:

```text
dp = [T,F,F,F,F,F]
```

After 1:

```text
[T,T,F,F,F,F]
```

After 2:

```text
[T,T,T,T,F,F]
```

After 3:

```text
[T,T,T,T,T,T]
```

```text
dp[5] = true
```

Answer:

```text
true
```

---

# Pattern Recognition

Whenever you see:

```text
Pick / Not Pick
Subset
Target Sum
Can Form Sum?
```

Think:

```text
Subset Sum DP
```

---

# DP Formula

```java
dp[index][target] =
dp[index-1][target]
||
dp[index-1][target-arr[index]];
```

if

```java
arr[index] <= target
```

otherwise

```java
dp[index][target]
=
dp[index-1][target];
```

---

# Complexity Summary

| Approach | Time | Space |
|-----------|----------|----------|
| Recursion | O(2^N) | O(N) |
| Memoization | O(N × Target) | O(N × Target) |
| Tabulation | O(N × Target) | O(N × Target) |
| Space Optimized | O(N × Target) | O(Target) |
| Single Array DP | O(N × Target) | O(Target) |

✅ **Interview Preferred:** Single Array DP

```java
for (int num : arr) {
    for (int target = k; target >= num; target--) {
        dp[target] |= dp[target - num];
    }
}
```
