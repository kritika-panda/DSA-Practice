# Longest Increasing Subsequence (LIS)

## Problem Statement

Given an integer array `nums`, return the length of the **Longest Increasing Subsequence**.

A subsequence is formed by deleting some or no elements without changing the order of the remaining elements.

An increasing subsequence satisfies:

```text
nums[i1] < nums[i2] < nums[i3] < ...
```

---

## Example 1

```text
Input:
nums = [10,9,2,5,3,7,101,18]

Output:
4
```

Explanation:

```text
[2,3,7,101]
```

Length:

```text
4
```

---

## Example 2

```text
Input:
nums = [0,1,0,3,2,3]

Output:
4
```

---

## Example 3

```text
Input:
nums = [7,7,7,7,7]

Output:
1
```

---

# Intuition

For every element:

```text
Pick it
or
Skip it
```

If we pick the current element:

```text
The next selected element
must be greater than it.
```

Therefore we need to remember:

```text
current index
previous selected index
```

---

# Recursive Formulation

Define:

```text
f(index, prevIndex)
```

Meaning:

```text
Length of LIS starting
from index

where previous chosen element
is prevIndex
```

---

## Choices

### Not Pick

```text
f(index + 1, prevIndex)
```

### Pick

Allowed only if:

```text
prevIndex == -1

OR

nums[index] > nums[prevIndex]
```

Then:

```text
1 + f(index + 1, index)
```

---

## Recurrence

```text
max(pick, notPick)
```

---

# Memoization Solution

```java
class Solution {

    public int lengthOfLIS(int[] nums) {

        int n = nums.length;

        int[][] dp =
            new int[n][n + 1];

        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(
                0,
                -1,
                nums,
                dp);
    }

    private int solve(
            int index,
            int prevIndex,
            int[] nums,
            int[][] dp) {

        if (index == nums.length)
            return 0;

        if (dp[index][prevIndex + 1] != -1)
            return dp[index][prevIndex + 1];

        int notPick =
            solve(index + 1,
                  prevIndex,
                  nums,
                  dp);

        int pick = 0;

        if (prevIndex == -1 ||
            nums[index] >
            nums[prevIndex]) {

            pick = 1 +
                solve(index + 1,
                      index,
                      nums,
                      dp);
        }

        return dp[index][prevIndex + 1]
                = Math.max(
                    pick,
                    notPick);
    }
}
```

---

# Tabulation Solution

## DP State

```text
dp[index][prev+1]
```

Meaning:

```text
LIS length starting
from index

when previous chosen
element is prev
```

---

## Code

```java
class Solution {

    public int lengthOfLIS(int[] nums) {

        int n = nums.length;

        int[][] dp =
            new int[n + 1][n + 1];

        for (int index = n - 1;
             index >= 0;
             index--) {

            for (int prev =
                    index - 1;
                 prev >= -1;
                 prev--) {

                int notPick =
                    dp[index + 1]
                      [prev + 1];

                int pick = 0;

                if (prev == -1 ||
                    nums[index] >
                    nums[prev]) {

                    pick =
                        1 +
                        dp[index + 1]
                          [index + 1];
                }

                dp[index][prev + 1] =
                    Math.max(
                        pick,
                        notPick);
            }
        }

        return dp[0][0];
    }
}
```

---

# Classic O(N²) DP Solution

This is the most commonly asked interview approach.

## DP Definition

```text
dp[i]
```

Meaning:

```text
Length of LIS ending at index i
```

Initially:

```text
Every element itself
forms LIS of length 1
```

```java
dp[i] = 1
```

---

## Transition

For every previous index:

```text
j < i
```

If:

```text
nums[j] < nums[i]
```

Then:

```java
dp[i] =
max(dp[i],
    1 + dp[j]);
```

---

## Code

```java
class Solution {

    public int lengthOfLIS(int[] nums) {

        int n = nums.length;

        int[] dp = new int[n];

        Arrays.fill(dp, 1);

        int ans = 1;

        for (int i = 0; i < n; i++) {

            for (int j = 0;
                 j < i;
                 j++) {

                if (nums[j] < nums[i]) {

                    dp[i] = Math.max(
                            dp[i],
                            1 + dp[j]);
                }
            }

            ans = Math.max(ans, dp[i]);
        }

        return ans;
    }
}
```

---

# Printing the LIS

```java
class Solution {

    public List<Integer> longestIncreasingSubsequence(
            int[] nums) {

        int n = nums.length;

        int[] dp = new int[n];
        int[] parent = new int[n];

        Arrays.fill(dp, 1);

        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }

        int maxLen = 1;
        int lastIndex = 0;

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < i; j++) {

                if (nums[j] < nums[i] &&
                    dp[j] + 1 > dp[i]) {

                    dp[i] = dp[j] + 1;
                    parent[i] = j;
                }
            }

            if (dp[i] > maxLen) {
                maxLen = dp[i];
                lastIndex = i;
            }
        }

        List<Integer> lis =
                new ArrayList<>();

        while (parent[lastIndex]
                != lastIndex) {

            lis.add(nums[lastIndex]);
            lastIndex =
                parent[lastIndex];
        }

        lis.add(nums[lastIndex]);

        Collections.reverse(lis);

        return lis;
    }
}
```

---

# Optimal Solution (Binary Search)

## Key Idea

Maintain a list:

```text
tails
```

where:

```text
tails[i]
=
smallest possible ending value
of an increasing subsequence
having length i+1
```

For each number:

### If larger than last element

```text
Append it
```

### Otherwise

```text
Replace lower_bound position
```

---

## Code

```java
class Solution {

    public int lengthOfLIS(int[] nums) {

        List<Integer> tails =
                new ArrayList<>();

        for (int num : nums) {

            int index =
                Collections.binarySearch(
                        tails,
                        num);

            if (index < 0)
                index = -(index + 1);

            if (index ==
                tails.size()) {

                tails.add(num);
            } else {

                tails.set(index,
                          num);
            }
        }

        return tails.size();
    }
}
```

---

# Dry Run

```text
nums =
[10,9,2,5,3,7,101,18]
```

Process:

```text
10  -> [10]

9   -> [9]

2   -> [2]

5   -> [2,5]

3   -> [2,3]

7   -> [2,3,7]

101 -> [2,3,7,101]

18  -> [2,3,7,18]
```

Length:

```text
4
```

Answer:

```text
4
```

---

# Why Binary Search Works?

Important:

```text
tails is NOT necessarily
the actual LIS
```

It only stores:

```text
Best possible ending values
for each LIS length.
```

Smaller ending values are better because:

```text
They provide more opportunities
for future extension.
```

---

# Pattern Recognition

Whenever you see:

```text
Longest Increasing Sequence

Strictly Increasing

Maximum Length Subsequence

Increasing Order
```

Think:

```text
✅ LIS
```

Approaches:

```text
Recursion + DP      -> O(N²)

Tabulation          -> O(N²)

Binary Search LIS   -> O(N log N)
```

---

# Complexity Analysis

## Memoization

```text
Time  : O(N²)
Space : O(N²)
```

## Tabulation

```text
Time  : O(N²)
Space : O(N²)
```

## Classic DP

```text
Time  : O(N²)
Space : O(N)
```

## Binary Search (Optimal)

```text
Time  : O(N log N)
Space : O(N)
```

---

# LIS Family of Problems

```text
Longest Increasing Subsequence
            ↓

Print LIS
            ↓

Number of LIS
            ↓

Longest Bitonic Subsequence
            ↓

Largest Divisible Subset
            ↓

Longest String Chain
            ↓

Russian Doll Envelopes
```

All are derived from the LIS pattern.
