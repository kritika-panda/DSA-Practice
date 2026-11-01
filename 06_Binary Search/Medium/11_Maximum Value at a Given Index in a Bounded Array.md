# Maximum Value at a Given Index in a Bounded Array

## Problem Statement

You are given three positive integers: `n`, `index`, and `maxSum`. You want to construct an array `nums` (0-indexed) of `n` positive integers where:

1. `nums[i] > 0` for all `0 <= i < n`.
2. `nums[i] - nums[i-1] <= 1` for all `1 <= i < n` (the absolute difference between adjacent elements is at most 1).
3. The sum of all elements in `nums` does not exceed `maxSum`.
4. `nums[index]` is maximized.

Return the maximum value of `nums[index]` that can be achieved.

The overall run time complexity should be:

```text
O(log(maxSum))
```

---

## Examples

### Example 1

**Input**

```java
n = 4
index = 2
maxSum = 6
```

**Output**

```java
2
```

**Explanation**

The array `[1, 2, 2, 1]` satisfies all conditions:
- All elements are positive integers.
- The absolute difference between adjacent elements is at most 1.
- The sum of elements is `1 + 2 + 2 + 1 = 6`, which is `<= maxSum`.

The value at index 2 is `2`, which is maximized. Changing it to `3` would require an array like `[1, 2, 3, 2]`, whose sum is `8 > 6`.

---

### Example 2

**Input**

```java
n = 6
index = 1
maxSum = 10
```

**Output**

```java
3
```

**Explanation**

The optimal array is `[2, 3, 2, 1, 1, 1]` with a sum of `10`. The value at index 1 is `3`.

---

### Example 3

**Input**

```java
n = 3
index = 0
maxSum = 8
```

**Output**

```java
3
```

---

## Brute Force Approach

Iteratively try increasing the value at the target index by 1 starting from 1, and simulate building the minimal valid array surrounding it until the total sum exceeds `maxSum`.

### Steps

1. Start with an array filled with 1s.
2. Increment the target index, and dynamically increment its neighbors to maintain the difference constraint of at most 1.
3. Compute the sum at each level.
4. Stop when the accumulated sum exceeds `maxSum`, and return the previous valid value.

### Complexity

```text
Time Complexity: O(maxSum)
Space Complexity: O(1)
```

This approach will result in a Time Limit Exceeded (TLE) error for large values of `maxSum`. The problem requires a more optimized solution.

---

# Optimal Approach: Binary Search on Answer

## Key Idea

Instead of building the array linearly, we can use Binary Search directly on the target *value* at the given `index`. 

The range of possible values for `nums[index]` is bounded by:

```text
low = 1
high = maxSum
```

For a chosen target value `mid` at the specified `index`, we greedily calculate the **minimum possible sum** required to form the rest of the array. To minimize the total sum, elements must decrease by exactly 1 for each step moving away from `index` until they reach the baseline value of `1`.

If the calculated minimum sum is less than or equal to `maxSum`, then `mid` is a valid candidate value, and we attempt to look for a larger maximum peak.

---

## Visual Understanding

Suppose:

```java
n = 4
index = 2
maxSum = 6
```

Let's test if the value `mid = 3` can be placed at index 2.
To minimize the sum, the array must slope down symmetrically:

```text
Index:   0   1   2   3
Value:  [1,  2,  3,  2]
```

- Left side of index 2 needs 2 elements: `[2, 1]` (sum = 3).
- Right side of index 2 needs 1 element: `[2]` (sum = 2).
- Element at index 2 itself: `3`.

Total required sum = `3 + 2 + 3 = 8`.  
Since `8 > 6`, a value of `3` is invalid. We narrow our search window downward.

---

## Partition Variables

Let:

```java
low = 1
high = maxSum
```

During each binary search step:

```java
long mid = low + (high - low) / 2;
```

---

### Border Elements

The minimum sum formula handles two scenarios for both the left and right sides of the target index:
1. **The slope reaches 1 before hitting the boundary:** The elements form an arithmetic progression down to 1, and the remaining elements are all 1s.
2. **The slope hits the boundary before reaching 1:** The elements form a truncated arithmetic progression that never reaches 1.

Using the mathematical arithmetic sum formula $S_n = \frac{n}{2}(a_1 + a_n)$, we can calculate the exact sum in $O(1)$ time.

---

## Correct Partition Condition

```java
if (calculateMinSum(mid, index, n) <= maxSum) {
    ans = mid;
    low = mid + 1;
} else {
    high = mid - 1;
}
```

If the required sum is valid, `mid` is a feasible peak. We then search the right half for a higher peak.

---

## How to Move Binary Search

### Case 1

```text
requiredSum <= maxSum
```

The target peak `mid` is sustainable within our `maxSum` budget constraint.

Move right to look for a larger peak value:

```java
low = mid + 1;
```

---

### Case 2

```text
requiredSum > maxSum
```

The target peak `mid` requires too large of a sum budget.

Move left to downscale the target value:

```java
high = mid - 1;
```

---

## Java Solution

```java
class Solution {

    public int maxValue(int n, int index, int maxSum) {

        long low = 1;
        long high = maxSum;
        long ans = 1;

        while (low <= high) {

            long mid = low + (high - low) / 2;

            if (getMinSum(mid, index, n) <= maxSum) {
                ans = mid;
                low = mid + 1; // Try to find a larger peak value
            } else {
                high = mid - 1; // Element peak is too high
            }
        }

        return (int) ans;
    }

    private long getMinSum(long peak, int index, int n) {
        
        long sum = peak;

        // Calculate left side sum
        long leftCount = index;
        sum += getSideSum(peak, leftCount);

        // Calculate right side sum
        long rightCount = n - 1 - index;
        sum += getSideSum(peak, rightCount);

        return sum;
    }

    private long getSideSum(long peak, long count) {
        
        if (count == 0) return 0;

        long sum = 0;
        
        // Scenario 1: Slope reaches 1 before hitting the array boundary
        if (count <= peak - 1) {
            long start = peak - 1;
            long end = peak - count;
            sum += ((start + end) * count) / 2;
            sum += (count - (start - end + 1)); // Padding remaining spots with 1s
        } 
        // Scenario 2: Slope hits the boundary before reaching 1
        else {
            long start = peak - 1;
            long end = 1;
            long elementsCount = peak - 1;
            sum += ((start + end) * elementsCount) / 2;
            sum += (count - elementsCount); // Padding the rest with 1s
        }

        return sum;
    }
}
```

---

## Dry Run

### Input

```java
n = 4
index = 2
maxSum = 6
```

---

### Initial Values

```java
low = 1
high = 6
ans = 1
```

---

### Iteration 1

```java
mid = 3
```

Evaluate `getMinSum(3, 2, 4)`:
- `leftCount = 2`. Since `2 <= 2`, it forms a full slope: `((2 + 1) * 2) / 2 = 3`.
- `rightCount = 1`. Since `1 <= 2`, it forms a truncated slope: `((2 + 2) * 1) / 2 = 2`.
- `totalSum = 3 + 3 + 2 = 8`.

Since `8 > 6` (Invalid):

```java
high = 3 - 1 = 2
```

---

### Iteration 2

```java
low = 1
high = 2
mid = 1
```

Evaluate `getMinSum(1, 2, 4)`:
- `leftCount = 2`, `rightCount = 1`.
- Slices contain purely 1s since peak is 1.
- `totalSum = 1 + 1 + 1 + 1 = 4`.

Since `4 <= 6` (Valid):

```java
ans = 1
low = 1 + 1 = 2
```

---

### Iteration 3

```java
low = 2
high = 2
mid = 2
```

Evaluate `getMinSum(2, 2, 4)`:
- `leftCount = 2`. `peak - 1 = 1`. Elements: `[1]`, padded with one `1` -> `1 + 1 = 2`.
- `rightCount = 1`. `peak - 1 = 1`. Elements: `[1]` -> `1`.
- `totalSum = 2 (left) + 2 (peak) + 1 (right) = 5`.

Since `5 <= 6` (Valid):

```java
ans = 2
low = 2 + 1 = 3
```

Loop terminates because `low > high` (`3 > 2`).

---

### Answer

```java
2
```

---

## Why Do We Use Binary Search on the Value Space?

The maximum element peak value changes monotonically with respect to the total sum budget required. If a peak value `v` is impossible to sustain within `maxSum`, any peak value greater than `v` is also guaranteed to be impossible. This property enables us to perform a Binary Search on the answer space.

Calculating the sum mathematically takes $O(1)$ time, yielding a total time complexity of:

```text
O(log(maxSum))
```

which satisfies the optimization constraints.

---

## Complexity Analysis

### Time Complexity

```text
O(log(maxSum))
```

The binary search cuts the value search space between `1` and `maxSum` in half at each step. In each step, the boundary check function executes a mathematical formula in O(1) time.

---

### Space Complexity

```text
O(1)
```

