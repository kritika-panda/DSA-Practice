# Sqrt(x)

## Problem Statement

Given a non-negative integer `x`, return the square root of `x` rounded down to the nearest integer. The returned integer should be **non-negative** as well.

You must not use any built-in exponent function or operator. For example, do not use `pow(x, 0.5)` in C++ or `x ** 0.5` in Python.

---

## Examples

### Example 1

```text
Input: x = 4
Output: 2
```

### Explanation

```text
The square root of 4 is 2, so we return 2.
```

---

### Example 2

```text
Input: x = 8
Output: 2
```

### Explanation

```text
The square root of 8 is 2.82842..., and since we round it down to the nearest integer, 2 is returned.
```

---

# Key Property: Monotonic Search Space

```text
If mid * mid <= x ---> mid is a potential square root candidate. Check right half for larger integers.
If mid * mid > x  ---> mid is too large. Check left half for smaller integers.
```

The values of integers from `1` to `x / 2` form a strictly increasing sorted sequence. The squares of these integers also increase monotonically. 

This enables us to use **Binary Search** over the integer range to locate the square root value in \(O(\log x)\) time instead of checking integers sequentially.

---

# Intuition

### Step 1: Base Case Trimming
If `x < 2`, the square root of `x` is `x` itself (since \(\sqrt{0} = 0\) and \(\sqrt{1} = 1\)). We can return `x` immediately.

### Step 2: Binary Search in Range
For any integer \(x \ge 4\), its square root is mathematically guaranteed to be less than or equal to `x / 2`. We can restrict our search window between `low = 1` and `high = x / 2`. 

At each step inside the loop:
1. Find the midpoint safely to prevent integer overflow: `mid = low + (high - low) / 2`.
2. Compute `mid * mid`. We must cast the multiplication to a `long` datatype to prevent integer overflow bugs.
3. **Case A: `(long) mid * mid <= x`**
   * The square of `mid` is less than or equal to `x`. This makes `mid` a valid potential answer candidate.
   * We record it (`ans = mid`) and shift our lower search boundary to check for a larger qualifying integer in the right half: `low = mid + 1`.
4. **Case B: `(long) mid * mid > x`**
   * The square of `mid` exceeds `x`. This element and everything to its right are too large. We shift our upper boundary to explore the left half: `high = mid - 1`.

When the loop terminates, `ans` contains the largest integer whose square does not exceed `x`.

---

# Java Implementation

```java
class Solution {
    public int mySqrt(int x) {
        // Base Case: Square root of 0 is 0, and square root of 1 is 1
        if (x < 2) return x;
        
        int low = 1, high = x / 2, ans = 0;
        
        while (low <= high) {
            // Safe midpoint calculation to avoid integer overflow
            int mid = low + (high - low) / 2;
            
            // Cast product to long to protect against integer overflow
            if ((long) mid * mid <= x) {
                ans = mid;       // Record the current best floor candidate
                low = mid + 1;   // Search the right half for a larger valid integer
            } else {
                high = mid - 1;  // Search the left half for smaller values
            }
        }
        
        return ans;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log x) or O(log N) where N is the value of `x`. The binary search space between `1` and `x / 2` is cut exactly in half at every iteration step, ensuring a clean logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. All operational logic runs strictly in-place using only basic primitive iteration tracking variables.
