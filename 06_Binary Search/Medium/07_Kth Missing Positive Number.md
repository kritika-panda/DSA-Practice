# Kth Missing Positive Number

## Problem Statement

Given an array `arr` of positive integers sorted in a **strictly increasing order**, and an integer `k`, return the **(k^{th}) positive integer** that is missing from this array.

---

## Examples

### Example 1

```text
Input: arr =, k = 5
Output: 9
```

### Explanation

```text
The missing positive integers are [1, 5, 6, 8, 9, 10, 12, 13, ...].
The 5th missing positive integer is 9.
```

---

### Example 2

```text
Input: nums =, k = 2
Output: 6
```

### Explanation

```text
The missing positive integers are [5, 6, 7, ...].
The 2nd missing positive integer is 6.
```

---

# Key Property: Calculating Missing Numbers In-Place

```text
Missing Numbers up to index i = arr[i] - (i + 1)
```

In a perfectly complete array with no missing elements, the value at index `i` should be exactly `i + 1` (e.g., index `0` holds `1`, index `1` holds `2`, etc.). 

If the array is elements like `[2, 3, 4, 7, 11]`, we can compute exactly how many positive integers are missing up to any index `i` by subtracting its ideal value from its actual value.

```text
Indices:          0   1   2   3   4
Values:          
Ideal Values:     1   2   3   4   5
Missing Count:    1   1   1   3   6  <--- Monotonically Increasing Array
```

Because the missing number counts increase monotonically, we can use **Binary Search** to find the exact segment where the (k^{th}) missing number resides in (O(log N)) time.

---

# Intuition

We search for the first index where the count of missing numbers is greater than or equal to `k`. We maintain two pointers, `low = 0` and `high = arr.length - 1`.

At each step inside the loop:
1. Find the midpoint: `mid = low + (high - low) / 2`.
2. Compute the missing numbers count up to `mid`: `missingCount = arr[mid] - (mid + 1)`.
3. **Case 1: `missingCount < k`**
   * The (k^{th}) missing number lies strictly to the right of `mid`. We shift our lower search boundary: `low = mid + 1`.
4. **Case 2: `missingCount >= k`**
   * The (k^{th}) missing number lies to the left of `mid` (or could be bounded by it). We contract our upper search boundary: `high = mid - 1`.

### Math Derivation for the Return Value
When the binary search loop finishes, the `high` pointer will cross below `low`, and the target segment will sit between `high` and `low`. 
The total missing numbers up to index `high` is given by `arr[high] - (high + 1)`. 
To find the remaining balance needed to reach `k`, we compute: `k - (arr[high] - (high + 1))`.
Adding this balance back to the actual number at `arr[high]` yields:
```text
Result = arr[high] + k - arr[high] + high + 1
       = k + low (since low == high + 1 at the end of the loop)
```

---

# Java Implementation

```java
class Solution {
    public int findKthPositive(int[] arr, int k) {
        int low = 0;
        int high = arr.length - 1;

        // Binary search to find the index segment containing the kth missing number
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int missingCount = arr[mid] - (mid + 1);

            // If the number of missing integers up to mid is less than k, look right
            if (missingCount < k) {
                low = mid + 1;
            } 
            // Otherwise, look left
            else {
                high = mid - 1;
            }
        }

        // At the end of the loop, low is equal to high + 1
        // The mathematical derivation simplifies down to returning low + k
        return low + k;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(log N)) where (N) is the total length of the `arr` array. The remaining search pool is cut exactly in half at every iteration step, ensuring a clean logarithmic execution profile.
* **Space Complexity:** (O(1)) auxiliary space. The calculation runs entirely in-place using only primitive pointer variables.
