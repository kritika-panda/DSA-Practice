# Split Array Largest Sum

## Problem Statement

Given an integer array `nums` and an integer `k`, split `nums` into `k` non-empty contiguous subarrays such that the largest sum of any subarray is minimized.

Return the minimized largest sum of the split.

The overall run time complexity should be:

```text
O(n * log(sum(nums) - max(nums)))
```

---

## Examples

### Example 1

**Input**

```java
nums = [7, 2, 5, 10, 8]
k = 2
```

**Output**

```java
18
```

**Explanation**

There are four ways to split `nums` into two subarrays.
The best way is to split it into:

```java
[7, 2, 5] and [10, 8]
```

where the largest sum of the two subarrays is:

```java
max(14, 18) = 18
```

---

### Example 2

**Input**

```java
nums = [1, 2, 3, 4, 5]
k = 2
```

**Output**

```java
9
```

**Explanation**

The optimal way to split it is:

```java
[1, 2, 3] and [4, 5]
```

where the largest sum of the two subarrays is:

```java
max(6, 9) = 9
```

---

### Example 3

**Input**

```java
nums = [1, 4, 4]
k = 3
```

**Output**

```java
4
```

---

## Brute Force Approach

Generate all possible combinations of splitting the array into `k` contiguous subarrays using recursion or dynamic programming, and find the minimum possible largest subarray sum.

### Steps

1. Try placing `k - 1` partitions at all possible positions in the array.
2. Calculate the sum of each subarray for every partition scheme.
3. Track the maximum sum among subarrays for each combination, and return the minimum of these maximums.

### Complexity

```text
Time Complexity: O(n^(k-1))
Space Complexity: O(n)
```

However, this exponential time complexity will cause a Time Limit Exceeded (TLE) error for larger inputs. The problem requires a more optimal solution.

---

# Optimal Approach: Binary Search on Answer

## Key Idea

Instead of searching for partition locations directly, we binary search for the *value* of the minimized largest subarray sum itself.

The possible range for our answer lies between:

```text
Minimum limit = max(nums)
Maximum limit = sum(nums)
```

For a chosen midpoint value `mid`, we greedily check if we can split the array into `k` or fewer subarrays such that no subarray sum exceeds `mid`. 

When this condition is satisfied, `mid` is a feasible answer, and we try to look for a smaller maximum sum.

---

## Visual Understanding

Suppose:

```java
nums = [7, 2, 5, 10, 8]
k = 2
```

Search range boundaries:

```text
low = max(nums) = 10
high = sum(nums) = 32
```

Let's test `mid = 21` (midpoint of 10 and 32):

Greedily build subarrays without exceeding a sum of 21:

```text
Subarray 1: [7, 2, 5] -> Sum = 14 (adding 10 exceeds 21)
Subarray 2: [10, 8]   -> Sum = 18
```

Total subarrays needed = `2`.

Since `2 <= k`, a max sum of 21 is valid. We move our search window left to find a lower possible maximum.

---

## Partition Variables

Let:

```java
low = max element in nums
high = total sum of elements in nums
```

During each binary search step:

```java
mid = low + (high - low) / 2;
```

---

### Border Elements

A helper function passes through the array tracking:

```java
currentSubarraySum = 0
requiredSubarrays = 1
```

If adding an element to `currentSubarraySum` exceeds `mid`, we start a new subarray:

```java
requiredSubarrays++
currentSubarraySum = element
```

---

## Correct Partition Condition

```java
requiredSubarrays <= k
```

If true, `mid` is a valid capability limit, so we attempt to minimize it further.

---

## How to Move Binary Search

### Case 1

```java
requiredSubarrays <= k
```

The maximum target value `mid` is large enough to partition within `k` pieces.

Move left to minimize:

```java
high = mid;
```

---

### Case 2

```java
requiredSubarrays > k
```

The maximum target value `mid` is too small, requiring more than `k` subarrays.

Move right to increase threshold:

```java
low = mid + 1;
```

---

## Java Solution

```java
class Solution {

    public int splitArray(int[] nums, int k) {

        int maxVal = 0;
        int totalSum = 0;

        for (int num : nums) {
            maxVal = Math.max(maxVal, num);
            totalSum += num;
        }

        int low = maxVal;
        int high = totalSum;
        int ans = high;

        while (low <= high) {

            int mid = low + (high - low) / 2;

            if (canSplit(nums, k, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }

        return ans;
    }

    private boolean canSplit(int[] nums, int k, int maxLimit) {

        int subarraysCount = 1;
        int currentSum = 0;

        for (int num : nums) {
            if (currentSum + num > maxLimit) {
                subarraysCount++;
                currentSum = num;
                if (subarraysCount > k) {
                    return false;
                }
            } else {
                currentSum += num;
            }
        }

        return true;
    }
}
```

---

## Dry Run

### Input

```java
nums = [7, 2, 5, 10, 8]
k = 2
```

---

### Initial Values

```java
low = 10
high = 32
```

---

### Iteration 1

```java
mid = 21
```

Evaluate `canSplit(nums, 2, 21)`:
- `[7, 2, 5]` (Sum = 14)
- `[10, 8]` (Sum = 18)
- Subarrays count = 2.

Since `2 <= 2`, it is valid.

```java
ans = 21
high = 20
```

---

### Iteration 2

```java
low = 10
high = 20
mid = 15
```

Evaluate `canSplit(nums, 2, 15)`:
- `[7, 2, 5]` (Sum = 14)
- `[10]` (Sum = 10)
- `[8]` (Sum = 8)
- Subarrays count = 3.

Since `3 > 2`, it is invalid.

```java
low = 16
```

---

### Iteration 3

```java
low = 16
high = 20
mid = 18
```

Evaluate `canSplit(nums, 2, 18)`:
- `[7, 2, 5]` (Sum = 14)
- `[10, 8]` (Sum = 18)
- Subarrays count = 2.

Since `2 <= 2`, it is valid.

```java
ans = 18
high = 17
```

---

### Iteration 4

```java
low = 16
high = 17
mid = 16
```

Evaluate `canSplit(nums, 2, 16)`:
- `[7, 2, 5]` (Sum = 14)
- `[10]` (Sum = 10)
- `[8]` (Sum = 8)
- Subarrays count = 3.

Since `3 > 2`, it is invalid.

```java
low = 17
```

---

### Iteration 5

```java
low = 17
high = 17
mid = 17
```

Evaluate `canSplit(nums, 2, 17)`:
- `[7, 2, 5]` (Sum = 14)
- `[10]` (Sum = 10)
- `[8]` (Sum = 8)
- Subarrays count = 3.

Since `3 > 2`, it is invalid.

```java
low = 18
```

Loop terminates because `low > high`.

---

### Answer

```java
18
```

---

## Why Do We Search on the Sum Value?

The range of possible answers is bounded monotonically between the maximum element and the sum of elements. This monotonic structure allows us to discard half of the remaining sum possibilities at each comparison step.

Searching on this value range yields:

```text
O(n * log(sum(nums) - max(nums)))
```

which satisfies the optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(sum(nums) - max(nums)))
```

The binary search takes logarithmic iterations over the value scale, and each choice performs a linear pass `O(n)` to validate array splits.

---

### Space Complexity

```text
O(1)
```

No extra auxiliary memory blocks are allocated.

---

## Key Insight

Instead of optimizing the arrays' splits layout directly, we frame the problem inversely: checking if a target capacity threshold can be sustained across `k` groups.

```text
Time  : O(n * log(sum(nums) - max(nums)))
Space : O(1)
```

---

## Similar Problems

1. Capacity To Ship Packages Within D Days
2. Koko Eating Bananas
3. Book Allocation Problem
4. Painters Partition Problem
5. Aggressive Cows
6. Minimize Max Distance to Gas Station
