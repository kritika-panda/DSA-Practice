# Find the Smallest Divisor Given a Threshold

## Problem Statement

Given an array of integers `nums` and an integer `threshold`, we will choose a positive integer `divisor`, divide all the array by it, and sum the results of the division. Find the **smallest divisor** such that the result sum is **less than or equal to** `threshold`.

Each result of the division is **rounded to the nearest integer greater than or equal to that element** (i.e., ceiling division: `Math.ceil(nums[i] / divisor)`).

It is guaranteed that there will be an answer.

---

## Examples

### Example 1

```text
Input: nums =, threshold = 6
Output: 5
```

### Explanation

```text
If divisor = 1: sum = 1 + 2 + 5 + 9 = 17 (> 6)
If divisor = 4: sum = ceil(1/4) + ceil(2/4) + ceil(5/4) + ceil(9/4) = 1 + 1 + 2 + 3 = 7 (> 6)
If divisor = 5: sum = ceil(1/5) + ceil(2/5) + ceil(5/5) + ceil(9/5) = 1 + 1 + 1 + 2 = 5 (<= 6)
Therefore, the smallest divisor that satisfies the condition is 5.
```

---

### Example 2

```text
Input: nums =, threshold = 5
Output: 44
```

---

# Key Property: Monotonicity of the Division Sum

```text
As Divisor increases ---> The Sum of Division Results monotonically decreases.
```

This inverse monotonic relationship lets us bypass checking every possible divisor sequentially. Instead, we can apply **Binary Search on Answers** to isolate the smallest valid divisor inside a bounded search space in (O(N log(max(text{nums})))) time.

---

# Intuition

### Step 1: Define the Search Bounds
* **Minimum possible divisor (`low`):** `1` (since the divisor must be a positive integer).
* **Maximum possible divisor (`high`):** The maximum element in `nums` (dividing by any number larger than (max(text{nums})) will result in a sum equal to the number of elements in the array, which won't change the outcome further).

### Step 2: Binary Search Mechanics
We look for the leftmost valid divisor using a binary search loop:
1. Find the midpoint divisor: `mid = low + (high - low) / 2`.
2. Compute the ceiling division sum for `mid` across all array elements.
3. **Case 1: `sum <= threshold` (Valid Divisor)**
   * The current `mid` is a valid candidate. We record it as our tentative answer.
   * Since we want the *smallest* possible divisor, we try to optimize by searching the left half: `high = mid - 1`.
4. **Case 2: `sum > threshold` (Invalid Divisor)**
   * The sum is too large, meaning `mid` is too small to compress the values sufficiently. The true answer must be strictly larger. We move right: `low = mid + 1`.

---

# Java Implementation

```java
class Solution {
    public int smallestDivisor(int[] nums, int threshold) {
        int low = 1;
        int high = 0;
        
        // Find the maximum value in nums to establish the upper bound
        for (int num : nums) {
            high = Math.max(high, num);
        }
        
        int ans = high;
        
        // Binary search on the answer space
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Check if the current divisor satisfies the threshold condition
            if (calculateSum(nums, mid) <= threshold) {
                ans = mid;         // Record the valid candidate
                high = mid - 1;    // Look left for a smaller valid divisor
            } else {
                low = mid + 1;     // Look right for larger values
            }
        }
        
        return ans;
    }
    
    // Helper method to compute the ceiling sum for a given divisor
    private int calculateSum(int[] nums, int divisor) {
        int sum = 0;
        for (int num : nums) {
            // Efficient way to compute ceiling division without floating-point math
            sum += (num + divisor - 1) / divisor;
        }
        return sum;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(N log(max(nums)))) where (N) is the number of elements in `nums`. The binary search space takes (O(log(max(text{nums})))) iterations to collapse. In each iteration, we run a linear scan over all (N) elements inside the `calculateSum` helper function.
* **Space Complexity:** (O(1)) auxiliary space. All operational logic checks execute entirely in-place without using extra linear collections.
