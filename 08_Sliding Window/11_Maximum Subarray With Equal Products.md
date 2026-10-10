# Longest Product Equivalent Subarray

## Problem Statement

You are given an array of positive integers `nums`. 

An array `arr` is called **product equivalent** if:
```text
prod(arr) == lcm(arr) * gcd(arr)
```
Where:
* `prod(arr)` is the product of all elements of `arr`.
* `gcd(arr)` is the Greatest Common Divisor (GCD) of all elements of `arr`.
* `lcm(arr)` is the Least Common Multiple (LCM) of all elements of `arr`.

Return the length of the **longest product equivalent subarray** of `nums`.

---

## Examples

### Example 1

```text
Input: nums = [1, 2, 1, 2, 1, 1, 1]
Output: 5
```

### Explanation

```text
The longest product equivalent subarray is [1, 2, 1, 1, 1] from index 2 to 6.
- prod([1, 2, 1, 1, 1]) = 2
- gcd([1, 2, 1, 1, 1]) = 1
- lcm([1, 2, 1, 1, 1]) = 2

Since 2 == 1 * 2, the condition holds. Its length is 5.
```

---

### Example 2

```text
Input: nums = [2, 3, 4, 5, 6]
Output: 3
```

### Explanation

```text
The longest product equivalent subarray is [3, 4, 5] from index 1 to 3.
- prod([3, 4, 5]) = 60
- gcd([3, 4, 5]) = 1
- lcm([3, 4, 5]) = 60

Since 60 == 1 * 60, the condition holds. Its length is 3.
```

---

### Example 3

```text
Input: nums = [1, 2, 3, 1, 4, 5, 1]
Output: 5
```

---

# Mathematical Property

For any two numbers (a) and (b), the identity (a times b = lcm}(a, b) times gcd}(a, b)) always holds. However, this property **does not automatically generalize** to arrays with more than two elements. 

An array of more than two elements is product equivalent only under very specific structural conditions (such as when elements are pairwise coprime, or when duplicates/ones do not disrupt the aggregate product equation). We evaluate each subarray dynamically to identify these valid windows.

---

# Intuition

We can check all possible subarrays using a **brute-force expansion strategy** with a nested loop configuration:
1. The outer loop fixes the starting index `i` of the subarray.
2. The inner loop expands the ending index `j` from `i` to the end of the array.

As `j` moves forward, we incrementally update `prod`, `gcd`, and `lcm` for the current segment `nums[i...j]`. If the updated states satisfy the product equation, we update our maximum tracking variable `ans`.

### Optimization Pruning
Since the product (`prod`) accumulates exponentially as elements are added, it can quickly overflow standard 64-bit signed integer (`long`) bounds. To prevent overflow bugs and redundant computations, we break early out of the inner loop if `prod` crosses a safety threshold of (10^{12}).

---

# Java Implementation

```java
class Solution {
    public int maxLength(int[] nums) {
        int n = nums.length;
        int ans = 0;

        // Anchor the starting point of the subarray
        for (int i = 0; i < n; i++) {
            long prod = 1;
            int g = 0;
            long l = 1;

            // Expand the end pointer to evaluate all sub-segments
            for (int j = i; j < n; j++) {
                prod *= nums[j];
                g = gcd(g, nums[j]);
                l = lcm(l, nums[j]);

                // Check if the product equivalence condition is met
                if (prod == (long) g * l) {
                    ans = Math.max(ans, j - i + 1);
                }

                // Prevent numeric overflow by pruning paths exceeding 10^12
                if (prod > 1e12) {
                    break;
                }
            }
        }
        return ans;
    }

    // Helper method to compute Greatest Common Divisor (Euclidean Algorithm)
    private int gcd(int a, int b) {
        if (a == 0) return b;
        return gcd(b % a, a);
    }

    // Helper method to compute Least Common Multiple
    private long lcm(long a, int b) {
        return a / gcd((int) a, b) * b;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(N^2 log(max(nums)))) where (N) is the total size of the input array. The nested double loops inspect all (O(N^2)) subarrays. Inside the inner execution layer, updating the `gcd` and `lcm` takes logarithmic time bounded by the maximum value in `nums`.
* **Space Complexity:** (O(1)) auxiliary space. The calculation maintains only a few primitive local variables for accumulation limits, bypassing any dynamic arrays or object allocations.
