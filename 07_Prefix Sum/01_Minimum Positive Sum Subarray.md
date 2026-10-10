# Minimum Positive Sum Subarray

## Problem Statement

You are given an integer array `nums` and two integers `l` and `r`.

Your task is to find the minimum sum of a subarray:

- Whose length is between `l` and `r` (inclusive).
- Whose sum is greater than `0`.

Return the minimum positive subarray sum.

If no such subarray exists, return `-1`.

> A subarray is a contiguous non-empty sequence of elements within an array.

---

## Examples

### Example 1

**Input**

```java
nums = [3, -2, 1, 4]
l = 2
r = 3
```

**Output**

```java
1
```

**Explanation**

Subarrays of length between `2` and `3` whose sum is positive:

```java
[3, -2]      -> 1
[1, 4]       -> 5
[3, -2, 1]   -> 2
[-2, 1, 4]   -> 3
```

The smallest positive sum is:

```java
1
```

from subarray:

```java
[3, -2]
```

---

### Example 2

**Input**

```java
nums = [-2, 2, -3, 1]
l = 2
r = 3
```

**Output**

```java
-1
```

**Explanation**

There is no subarray of length between `2` and `3` whose sum is greater than `0`.

Therefore:

```java
-1
```

is returned.

---

### Example 3

**Input**

```java
nums = [1, 2, 3, 4]
l = 2
r = 4
```

**Output**

```java
3
```

**Explanation**

Possible positive subarray sums:

```java
[1, 2]       -> 3
[2, 3]       -> 5
[3, 4]       -> 7
[1, 2, 3]    -> 6
[2, 3, 4]    -> 9
[1, 2, 3, 4] -> 10
```

The minimum positive sum is:

```java
3
```

from subarray:

```java
[1, 2]
```

---

## Approach: Sliding Window for Every Valid Length

For every subarray length from `l` to `r`:

1. Compute the sum of the first window.
2. Slide the window across the array.
3. Update the window sum efficiently by:
   - Removing the leftmost element.
   - Adding the new rightmost element.
4. Whenever the current sum is positive, update the minimum answer.

If no positive sum is found, return `-1`.

---

## Java Solution

```java
import java.util.List;

class Solution {

    public int minimumSumSubarray(List<Integer> nums, int l, int r) {

        int minSum = Integer.MAX_VALUE;

        for (int len = l; len <= r; len++) {

            int currSum = 0;

            // First window of size len
            for (int j = 0; j < len; j++) {
                currSum += nums.get(j);
            }

            if (currSum > 0) {
                minSum = Math.min(minSum, currSum);
            }

            int low = 0;
            int high = len;

            // Slide window
            while (high < nums.size()) {

                currSum -= nums.get(low);
                currSum += nums.get(high);

                low++;
                high++;

                if (currSum > 0) {
                    minSum = Math.min(minSum, currSum);
                }
            }
        }

        return minSum == Integer.MAX_VALUE ? -1 : minSum;
    }
}
```

---

## Dry Run

### Input

```java
nums = [3, -2, 1, 4]
l = 2
r = 3
```

---

### Length = 2

#### Window 1

```java
[3, -2]
```

Sum:

```text
1
```

```text
minSum = 1
```

---

#### Window 2

```java
[-2, 1]
```

Sum:

```text
-1
```

Ignore.

---

#### Window 3

```java
[1, 4]
```

Sum:

```text
5
```

```text
minSum = 1
```

---

### Length = 3

#### Window 1

```java
[3, -2, 1]
```

Sum:

```text
2
```

```text
minSum = 1
```

---

#### Window 2

```java
[-2, 1, 4]
```

Sum:

```text
3
```

```text
minSum = 1
```

---

### Final Answer

```text
1
```

---

## Why Sliding Window Works

For a fixed length:

```text
Next Window Sum
=
Current Window Sum
- Outgoing Element
+ Incoming Element
```

This allows moving the window in:

```text
O(1)
```

time instead of recomputing each subarray sum from scratch.

---

## Complexity Analysis

Let:

```text
n = nums.size()
```

and

```text
k = r - l + 1
```

be the number of lengths examined.

### Time Complexity

For each length between `l` and `r`, we scan the array once.

```text
O(n × (r - l + 1))
```

Worst case:

```text
O(n²)
```

---

### Space Complexity

```text
O(1)
```

Only a few variables are used:

```java
currSum
minSum
low
high
```

No extra data structures are required.

---

## Key Insight

For each valid subarray length:

```text
1. Calculate first window sum.
2. Use sliding window to generate remaining sums.
3. Track the smallest positive sum.
```

The answer is:

```text
Minimum Positive Sum
Among All Subarrays
Whose Length Lies In [l, r]
```

---

## Similar Problems

1. Maximum Sum Subarray of Size K
2. Sliding Window Maximum
3. Minimum Size Subarray Sum
4. Binary Subarrays With Sum
5. Subarray Sum Equals K
6. Maximum Average Subarray I
