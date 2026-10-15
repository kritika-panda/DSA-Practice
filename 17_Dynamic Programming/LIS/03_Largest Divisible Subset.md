# Largest Divisible Subset

## Problem Statement

Given a set of distinct positive integers `nums`, return the largest subset such that:

```text
For every pair (a, b) in the subset:
a % b == 0 OR b % a == 0
```

If there are multiple solutions, return any one of them.

---

## Example 1

```text
Input:
nums = [1,2,3]

Output:
[1,2]
```

Explanation:

```text
2 % 1 = 0

Subset [1,3] is also valid.
```

---

## Example 2

```text
Input:
nums = [1,2,4,8]

Output:
[1,2,4,8]
```

---

# Intuition

This problem is very similar to **Longest Increasing Subsequence (LIS)**.

### Key Observation

If the array is sorted:

```text
1 2 4 8
```

and:

```text
nums[i] % nums[j] == 0
```

then `nums[i]` can extend the divisible subset ending at `j`.

Thus we can apply:

```text
LIS DP + Parent Tracking
```

to reconstruct the answer.

---

# Approach

### Step 1

Sort the array.

```text
[1,2,4,8]
```

---

### Step 2

Define:

```java
dp[i]
```

= length of largest divisible subset ending at index `i`.

---

### Step 3

Define:

```java
parent[i]
```

to reconstruct the subset later.

---

### Transition

For every previous element:

```java
if(nums[i] % nums[j] == 0)
```

then:

```java
dp[i] = max(dp[i], dp[j] + 1)
```

---

### Step 4

Track the index where the maximum subset ends.

---

### Step 5

Backtrack using the parent array.

---

# Java Code

```java
import java.util.*;

public class Main {

    public static List<Integer> largestDivisibleSubset(int[] nums) {

        int n = nums.length;

        Arrays.sort(nums);

        int[] dp = new int[n];
        int[] parent = new int[n];

        Arrays.fill(dp, 1);

        int maxLen = 1;
        int lastIndex = 0;

        for (int i = 0; i < n; i++) {

            parent[i] = i;

            for (int j = 0; j < i; j++) {

                if (nums[i] % nums[j] == 0 &&
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

        List<Integer> result = new ArrayList<>();

        while (parent[lastIndex] != lastIndex) {
            result.add(nums[lastIndex]);
            lastIndex = parent[lastIndex];
        }

        result.add(nums[lastIndex]);

        Collections.reverse(result);

        return result;
    }

    public static void main(String[] args) {

        int[] nums = {1, 2, 4, 8};

        System.out.println(largestDivisibleSubset(nums));
    }
}
```

---

# Dry Run

### Input

```text
[1,2,4,8]
```

### After Sorting

```text
[1,2,4,8]
```

---

### DP Construction

| Number | DP |
|----------|----------|
| 1 | 1 |
| 2 | 2 |
| 4 | 3 |
| 8 | 4 |

```text
dp = [1,2,3,4]
```

---

### Parent Array

```text
parent = [0,0,1,2]
```

Meaning:

```text
8 ← 4 ← 2 ← 1
```

---

### Backtracking

```text
8 → 4 → 2 → 1
```

Reverse:

```text
[1,2,4,8]
```

---

# Visualization

```text
1
|
2
|
4
|
8
```

Each number is divisible by its predecessor.

---

# Why Sorting Works?

Before sorting:

```text
8 1 4 2
```

checking divisibility chains becomes difficult.

After sorting:

```text
1 2 4 8
```

every valid chain naturally grows from smaller values to larger values.

This allows an LIS-style DP solution.

---

# Time Complexity

### Sorting

```text
O(N log N)
```

### DP

```text
O(N²)
```

---

### Total

```text
O(N²)
```

---

# Space Complexity

```text
O(N)
```

For:

```text
dp[]
parent[]
answer list
```

---

# Pattern Recognition

This problem belongs to the **LIS Pattern** family.

```text
LIS
│
├── Longest Increasing Subsequence
├── Print LIS
├── Longest String Chain
├── Largest Divisible Subset  ✅
└── Longest Bitonic Subsequence
```

### Trick for Interviews

Whenever you see:

```text
"Find the longest subset/sequence"
```

and a condition exists between two elements:

```text
a < b
a divides b
difference == k
```

think:

```text
Sort + LIS DP + Parent Tracking
```

first.
