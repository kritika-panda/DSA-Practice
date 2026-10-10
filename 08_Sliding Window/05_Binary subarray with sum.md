# Number of Subarrays With Sum = Goal

## Problem Statement

You are given a binary array `nums` (containing only `0`s and `1`s) and an integer `goal`.

Return the number of **non-empty subarrays** of `nums` whose sum is exactly equal to `goal`.

A **subarray** is a contiguous part of the array.

---

## Examples

### Example 1

**Input**
```java
nums = [1, 0, 0, 1, 1, 0]
goal = 2
```

**Output**
```java
6
```

**Explanation**

There are `6` subarrays with sum exactly equal to `2`:

```java
[1, 0, 0, 1]
[0, 0, 1, 1]
[0, 1, 1]
[1, 1]
[1, 1, 0]
[0, 0, 1, 1, 0]
```

---

### Example 2

**Input**
```java
nums = [0, 0, 0, 0, 0, 0]
goal = 0
```

**Output**
```java
21
```

**Explanation**

All subarrays consisting only of `0`s will have sum `0`.

Total subarrays:

```text
n × (n + 1) / 2
= 6 × 7 / 2
= 21
```

---

## Approach: Sliding Window + At Most K

Instead of directly counting subarrays with sum exactly equal to `goal`, we use:

```text
Subarrays with sum exactly goal
=
Subarrays with sum ≤ goal
-
Subarrays with sum ≤ (goal - 1)
```

So,

```java
exact(goal) = atMost(goal) - atMost(goal - 1)
```

### Why does this work?

- `atMost(goal)` counts all subarrays whose sum is less than or equal to `goal`.
- `atMost(goal - 1)` counts all subarrays whose sum is less than `goal`.
- Subtracting them leaves only subarrays whose sum is exactly `goal`.

This technique works because the array contains only `0`s and `1`s, making the sliding window valid.

---

## Java Solution

```java
class Solution {

    // Function to calculate number of subarrays with sum exactly equal to goal
    public int numSubarraysWithSum(int[] nums, int goal) {
        return atMost(nums, goal) - atMost(nums, goal - 1);
    }

    // Helper method to count subarrays with sum at most k
    private int atMost(int[] nums, int k) {

        // No valid subarray for negative sum
        if (k < 0) return 0;

        int left = 0;
        int sum = 0;
        int count = 0;

        // Traverse array using right pointer
        for (int right = 0; right < nums.length; right++) {

            // Add current element to window sum
            sum += nums[right];

            // Shrink window if sum exceeds k
            while (sum > k) {
                sum -= nums[left];
                left++;
            }

            // Count all valid subarrays ending at right
            count += (right - left + 1);
        }

        return count;
    }
}
```

---

## Dry Run

### Input

```java
nums = [1, 0, 1]
goal = 2
```

### Step 1: Calculate `atMost(2)`

| Right | Window | Sum | New Subarrays | Count |
|---------|---------|---------|---------|---------|
| 0 | [1] | 1 | 1 | 1 |
| 1 | [1,0] | 1 | 2 | 3 |
| 2 | [1,0,1] | 2 | 3 | 6 |

```text
atMost(2) = 6
```

---

### Step 2: Calculate `atMost(1)`

| Right | Window | Sum | New Subarrays | Count |
|---------|---------|---------|---------|---------|
| 0 | [1] | 1 | 1 | 1 |
| 1 | [1,0] | 1 | 2 | 3 |
| 2 | Shrink → [0,1] | 1 | 2 | 5 |

```text
atMost(1) = 5
```

---

### Final Answer

```text
exactly(2)
= atMost(2) - atMost(1)
= 6 - 5
= 1
```

Subarray:

```java
[1, 0, 1]
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

The algorithm calls `atMost()` twice.

In each call:

- The `right` pointer moves from left to right once.
- The `left` pointer also moves only forward.
- Each element is processed at most twice.

Therefore:

```text
O(n) + O(n) = O(n)
```

---

### Space Complexity

```text
O(1)
```

Only a few integer variables are used:

- `left`
- `right`
- `sum`
- `count`

No extra data structures are required.

---

## Key Insight

For binary arrays:

```java
Subarrays with Sum = Goal
=
Subarrays with Sum ≤ Goal
-
Subarrays with Sum ≤ (Goal - 1)
```

Using a sliding window to count **at most K** subarrays allows us to find the exact count efficiently in **O(n)** time and **O(1)** space.
