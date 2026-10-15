# Number of Longest Increasing Subsequences (LIS)

## Problem Statement

Given an integer array `nums`, return the number of **Longest Increasing Subsequences**.

A subsequence is strictly increasing if:

```text
nums[i1] < nums[i2] < nums[i3] ...
```

---

## Example 1

```text
Input:
[1,3,5,4,7]

Output:
2
```

### LIS Length

```text
4
```

### Longest Subsequences

```text
1 → 3 → 5 → 7

1 → 3 → 4 → 7
```

Count:

```text
2
```

---

## Example 2

```text
Input:
[2,2,2,2,2]

Output:
5
```

Each element alone forms an LIS:

```text
[2]
[2]
[2]
[2]
[2]
```

Count:

```text
5
```

---

# Intuition

Normal LIS asks:

```text
What is the maximum LIS length?
```

This problem asks:

```text
How many LIS of maximum length exist?
```

So along with LIS length, we must also track:

```text
How many ways can we achieve that length?
```

---

# DP Definition

### Length Array

```java
dp[i]
```

Represents:

```text
Length of LIS ending at index i
```

---

### Count Array

```java
cnt[i]
```

Represents:

```text
Number of LIS ending at index i
```

---

# Initialization

Every element alone forms an LIS.

```java
dp[i] = 1
cnt[i] = 1
```

---

# Transition

For every previous index:

```java
if(nums[j] < nums[i])
```

we try extending the subsequence.

---

## Case 1

Found a strictly longer LIS.

```java
dp[j] + 1 > dp[i]
```

Update:

```java
dp[i] = dp[j] + 1
cnt[i] = cnt[j]
```

Why?

Because all previous ways are replaced by this better length.

---

### Example

```text
1 → 3 → 5
```

Length:

```text
3
```

Only one way.

So:

```java
cnt[i] = cnt[j]
```

---

## Case 2

Found another LIS of same length.

```java
dp[j] + 1 == dp[i]
```

Then:

```java
cnt[i] += cnt[j]
```

Why?

Because we discovered additional ways to reach the same LIS length.

---

### Example

```text
1 → 3 → 5 → 7

1 → 3 → 4 → 7
```

Two different paths produce same LIS length.

So:

```java
cnt[7] = cnt[5] + cnt[4]
```

---

# Java Code

```java
import java.util.*;

public class Main {

    public static int findNumberOfLIS(int[] nums) {

        int n = nums.length;

        int[] dp = new int[n];
        int[] cnt = new int[n];

        Arrays.fill(dp, 1);
        Arrays.fill(cnt, 1);

        int maxi = 1;

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < i; j++) {

                if (nums[j] < nums[i]) {

                    // Better LIS found
                    if (dp[j] + 1 > dp[i]) {

                        dp[i] = dp[j] + 1;
                        cnt[i] = cnt[j];
                    }

                    // Another LIS of same length
                    else if (dp[j] + 1 == dp[i]) {

                        cnt[i] += cnt[j];
                    }
                }
            }

            maxi = Math.max(maxi, dp[i]);
        }

        int ans = 0;

        for (int i = 0; i < n; i++) {

            if (dp[i] == maxi) {
                ans += cnt[i];
            }
        }

        return ans;
    }

    public static void main(String[] args) {

        int[] nums = {1,3,5,4,7};

        System.out.println(findNumberOfLIS(nums));
    }
}
```

---

# Dry Run

### Input

```text
[1,3,5,4,7]
```

---

### DP Array

```text
Index : 0 1 2 3 4
Value : 1 3 5 4 7

dp    : 1 2 3 3 4
```

---

### Count Array

```text
cnt   : 1 1 1 1 2
```

Explanation:

```text
7 can be reached through:

1→3→5→7

and

1→3→4→7
```

Hence:

```text
cnt[4] = 2
```

---

# Visualization

```text
          7
         / \
        5   4
         \ /
          3
          |
          1
```

Two longest paths reach 7.

Answer:

```text
2
```

---

# Why Two Arrays?

### dp[]

Stores:

```text
Best length achieved
```

### cnt[]

Stores:

```text
How many ways that best length can be achieved
```

This combination lets us compute both:

```text
Longest Length
+
Number of Longest Length Subsequences
```

---

# Example 2

```text
Input:
[2,2,2,2,2]
```

Since no increasing pair exists:

```text
dp = [1,1,1,1,1]

cnt = [1,1,1,1,1]
```

Maximum Length:

```text
1
```

Total Ways:

```text
1+1+1+1+1 = 5
```

Answer:

```text
5
```

---

# Time Complexity

Nested loops:

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
cnt[]
```

---

# LIS Pattern Family

```text
LIS Pattern
│
├── Longest Increasing Subsequence
├── Print LIS
├── Largest Divisible Subset
├── Longest String Chain
├── Longest Bitonic Subsequence
├── Number of LIS ✅
└── Minimum Removals to Make Mountain Array
```

### Interview Trick

Whenever a problem asks:

```text
Longest sequence
AND
Number of ways
```

Think:

```text
DP Array  -> Best Length

Count Array -> Number of Ways
```

This is the standard DP counting pattern used in:

```text
Number of LIS
Number of Paths
Coin Change II
Distinct Subsequences
Target Sum
```
