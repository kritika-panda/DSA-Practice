# Longest Bitonic Subsequence (LBS)

## Problem Statement

Given an array `nums`, find the length of the **Longest Bitonic Subsequence**.

A Bitonic Subsequence is a sequence that:

```text
First increases
Then decreases
```

Both parts must be strictly monotonic.

---

## Example 1

```text
Input:
[1, 11, 2, 10, 4, 5, 2, 1]

Output:
6
```

### Longest Bitonic Subsequence

```text
1 → 2 → 10 → 4 → 2 → 1
```

Length = 6

---

## Example 2

```text
Input:
[1, 2, 5, 3, 2]

Output:
5
```

### Bitonic Subsequence

```text
1 → 2 → 5 → 3 → 2
```

Length = 5
```

---

# Intuition

A Bitonic Sequence consists of:

```text
Increasing Part
+
Decreasing Part
```

Example:

```text
1 2 5 3 1
```

At peak element:

```text
      5
     / \
    /   \
  1 2   3 1
```

So for every index `i`, we need:

```text
LIS ending at i
+
LDS starting at i
```

---

# Key Observation

For every index:

```java
lis[i]
```

= Longest Increasing Subsequence ending at `i`

and

```java
lds[i]
```

= Longest Decreasing Subsequence starting from `i`

Then:

```java
Bitonic Length at i
=
lis[i] + lds[i] - 1
```

Why `-1`?

Because peak element is counted twice.

---

# Visualization

Consider:

```text
1 11 2 10 4 5 2 1
```

### LIS

```text
Index : 0 1 2 3 4 5 6 7
Value : 1 11 2 10 4 5 2 1

LIS   : 1  2 2  3 3 4 2 1
```

---

### LDS

```text
LDS   : 1  5 2  4 3 3 2 1
```

---

### Combine

```text
LIS + LDS - 1
```

| Index | Value | LIS | LDS | Bitonic |
|---------|---------|---------|---------|---------|
| 0 | 1 | 1 | 1 | 1 |
| 1 | 11 | 2 | 5 | 6 |
| 2 | 2 | 2 | 2 | 3 |
| 3 | 10 | 3 | 4 | 6 |
| 4 | 4 | 3 | 3 | 5 |
| 5 | 5 | 4 | 3 | 6 |
| 6 | 2 | 2 | 2 | 3 |
| 7 | 1 | 1 | 1 | 1 |

Answer:

```text
6
```

---

# Approach

### Step 1

Compute LIS ending at every index.

```java
lis[i]
```

---

### Step 2

Compute LDS starting at every index.

```java
lds[i]
```

This is simply LIS from right to left.

---

### Step 3

For every index:

```java
ans = max(ans, lis[i] + lds[i] - 1);
```

---

# Java Code

```java
import java.util.*;

public class Main {

    public static int longestBitonicSubsequence(int[] nums) {

        int n = nums.length;

        int[] lis = new int[n];
        int[] lds = new int[n];

        Arrays.fill(lis, 1);
        Arrays.fill(lds, 1);

        // LIS

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < i; j++) {

                if (nums[j] < nums[i]) {

                    lis[i] = Math.max(
                            lis[i],
                            lis[j] + 1
                    );
                }
            }
        }

        // LDS

        for (int i = n - 1; i >= 0; i--) {

            for (int j = n - 1; j > i; j--) {

                if (nums[j] < nums[i]) {

                    lds[i] = Math.max(
                            lds[i],
                            lds[j] + 1
                    );
                }
            }
        }

        int ans = 0;

        for (int i = 0; i < n; i++) {

            ans = Math.max(
                    ans,
                    lis[i] + lds[i] - 1
            );
        }

        return ans;
    }

    public static void main(String[] args) {

        int[] nums =
                {1,11,2,10,4,5,2,1};

        System.out.println(
                longestBitonicSubsequence(nums)
        );
    }
}
```

---

# Dry Run

### Input

```text
1 11 2 10 4 5 2 1
```

### LIS

```text
1 2 2 3 3 4 2 1
```

### LDS

```text
1 5 2 4 3 3 2 1
```

### Peak at 5

```text
LIS = 4
LDS = 3

Bitonic = 4 + 3 - 1
        = 6
```

---

# Why Subtract 1?

For the peak element:

```text
Increasing Side -> counts peak
Decreasing Side -> counts peak
```

Example:

```text
1 2 5 3 1
```

```text
LIS = 3
LDS = 3
```

Peak:

```text
5
```

counted twice.

Therefore:

```java
lis[i] + lds[i] - 1
```

---

# Edge Case

Some platforms (e.g., Striver DP Sheet) require:

```text
At least one increasing element
AND
At least one decreasing element
```

So ignore positions where:

```java
lis[i] == 1 || lds[i] == 1
```

Modified calculation:

```java
if (lis[i] > 1 && lds[i] > 1) {
    ans = Math.max(ans, lis[i] + lds[i] - 1);
}
```

---

# Time Complexity

Computing LIS:

```text
O(N²)
```

Computing LDS:

```text
O(N²)
```

Total:

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
lis[]
lds[]
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
├── Longest Bitonic Subsequence ✅
├── Number of LIS
└── Minimum Removals to Make Mountain Array
```

### Interview Trick

Whenever a problem asks:

```text
Increase then decrease
Peak element
Mountain Array
Bitonic Sequence
```

Think immediately:

```text
LIS from Left
+
LIS from Right (LDS)
+
Combine Results
```

This is the core pattern behind all Bitonic and Mountain Array problems.
