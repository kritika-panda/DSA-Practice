# Find Nth Root of an Integer

## Problem Statement

You are given two positive integers `n` and `m`. You need to find the **nth root** of `m`. If the nth root is an integer, return it; otherwise, return `-1`.

### Definition

The **nth root** of a number `m` is a value `x` such that:
[x^n = m]

---

## Examples

### Example 1

```text
Input: n = 2, m = 9
Output: 3
```

### Explanation

```text
3 raised to the power of 2 is 9 (3^2 = 9), so the 2nd (square) root of 9 is 3.
```

---

### Example 2

```text
Input: n = 3, m = 27
Output: 3
```

### Explanation

```text
3 raised to the power of 3 is 27 (3^3 = 27), so the 3rd (cube) root of 27 is 3.
```

---

### Example 3

```text
Input: n = 4, m = 69
Output: -1
```

### Explanation

```text
The 4th root of 69 is approximately 2.88, which is not an integer. Therefore, we return -1.
```

---

# Key Property: Monotonic Search Space

```text
If mid^n == m ---> Found the exact nth root integer. Return mid.
If mid^n > m  ---> mid is too large. Check left half for smaller integers.
If mid^n < m  ---> mid is too small. Check right half for larger integers.
```

The integer search space from `1` to `m` forms a strictly increasing sorted sequence. As a result, calculating (x^n) creates a monotonically increasing sequence. This structure makes the problem a perfect fit for **Binary Search**, allowing us to isolate the answer in (O(log m)) search steps.

---

# Intuition

We set our binary search bounds between `low = 1` and `high = m`. 

At each step inside the `while (low <= high)` loop:
1. Find the midpoint safely: `mid = low + (high - low) / 2`.
2. Evaluate (mid^n) relative to (m). 

### Preventing Integer Overflow

Computing (mid^n) naively using a standard loop or a utility like `Math.pow` can easily trigger arithmetic **integer overflow bugs** if (mid^n) exceeds `Integer.MAX_VALUE` or `Long.MAX_VALUE`. 

To prevent this, we write a dedicated helper function that performs multiplication step-by-step and returns:
* `1` if (mid^n == m)
* `2` if (mid^n > m) (stops multiplying early as soon as the value exceeds (m))
* `0` if (mid^n < m)

Based on what the helper function returns, we discard the invalid half of our search pool and narrow the window. If the loop ends and the pointers cross without an exact match, the root is not an integer. Return `-1`.

---

# Java Implementation

```java
class Solution {
    public int nthRoot(int n, int m) {
        int low = 1, high = m;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int midState = checkPower(mid, n, m);
            
            // Case 1: Exact integer nth root found
            if (midState == 1) {
                return mid;
            } 
            // Case 2: mid^n is strictly greater than m, search left half
            else if (midState == 2) {
                high = mid - 1;
            } 
            // Case 3: mid^n is strictly less than m, search right half
            else {
                low = mid + 1;
            }
        }
        
        // No exact integer nth root exists
        return -1;
    }
    
    // Helper function to safely calculate mid^n against limit m without overflow
    // Returns: 1 if mid^n == m, 2 if mid^n > m, 0 if mid^n < m
    private int checkPower(int mid, int n, int m) {
        long ans = 1;
        for (int i = 1; i <= n; i++) {
            ans = ans * mid;
            
            // Prune early if the product exceeds m to prevent overflow
            if (ans > m) {
                return 2;
            }
        }
        
        if (ans == m) {
            return 1;
        }
        return 0;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(n log m)) where (m) is the target value and (n) is the power exponent. The binary search window takes (O(log m)) operations to collapse. Inside each step, the `checkPower` helper function executes a loop running at most (n) times.
* **Space Complexity:** (O(1)) auxiliary space. The calculation executes entirely in-place, tracking search partitions with a few local primitive integer values.
