# Count the Number of Possible Triangles

**LeetCode 611 - Valid Triangle Number / GFG Classic**

## Problem Statement

Given an unsorted array of positive integers `nums`, return the **number of triplets** chosen from the array that can make triangles if we take them as side lengths.

For three side lengths \(a\), \(b\), and \(c\) to form a valid triangle, they must satisfy the **Triangle Inequality Theorem**:
* \(a + b > c\)
* \(a + c > b\)
* \(b + c > a\)

---

## Example

```java
int[] nums = {4, 6, 3, 7};

Solution sol = new Solution();
sol.findNumberOfTriangles(nums); // Returns 3
```

**Explanation:** The 3 valid combinations that form a triangle are:
1. `[3, 4, 6]` (since 3 + 4 > 6)
2. `[4, 6, 7]` (since 4 + 6 > 7)
3. `[3, 6, 7]` (since 3 + 6 > 7)

Note that `[3, 4, 7]` is invalid because 3 + 4 is not strictly greater than 7.

---

# Intuition

A naive brute-force approach would check all possible triplets using three nested loops, verifying the triangle inequalities for each combination. This costs \(\mathcal{O}(N^3)\) time, causing it to fail on large arrays.

We can optimize this problem significantly by **sorting the array** in ascending order first. 

When the array is sorted such that \(a \le b \le c\), the inequalities \(a + c > b\) and \(b + c > a\) are **always true**. The only condition we need to actively check is:
\[\text{If } a \le b \le c, \text{ then a valid triangle requires } a + b > c\]

By fixing the longest side \(c\) (at index `i`), we can use a **Two-Pointer** approach (`left` and `right`) to find pairs that satisfy \(nums[left] + nums[right] > nums[i]\) in linear time. 

---

# Approach: Sorting + Two-Pointer Optimization

## Data Structures

```java
int left, int right
```
Two moving index markers indicating the shortest side (`left`) and the middle side (`right`) relative to a fixed longest side (`i`).

---

## Algorithm Steps

1. Sort the array `nums` in ascending order.
2. Initialize `count = 0`.
3. Loop from the end of the array to index `2` using index variable `i`. This `nums[i]` represents our longest side \(c\).
4. For each fixed `i`, place two pointers: `left = 0` and `right = i - 1`.
5. While `left < right`:
   - Check if `nums[left] + nums[right] > nums[i]`:
     - If it **is**, it implies that all elements from index `left` to `right - 1` when added to `nums[right]` will also be strictly greater than `nums[i]` (because the array is sorted). 
     - Add `right - left` to `count` and shift `right` downward (`right--`) to test a smaller middle side.
     - If it **is not**, the sum is too small. Shift `left` upward (`left++`) to increase the sum.
6. Return `count` once the outer loop finishes.

---

# Dry Run

### Input
```java
nums = [4, 6, 3, 7] -> Sorted: [3, 4, 6, 7]
```

### Execution Trace

- **Initialization:** `count = 0`

| Outer Index `i` (\(c\)) | `left` (\(a\)) | `right` (\(b\)) | Condition (`nums[left] + nums[right] > nums[i]`) | Count Update | Action |
|---|---|---|---|---|---|
| `3` (val: `7`) | `0` (val: `3`) | `2` (val: `6`) | `3 + 6 = 9 > 7` ✅ Yes | `0 + (2 - 0) = 2` | Triplets: `[3,6,7]`, `[4,6,7]`. `right--` |
| `3` (val: `7`) | `0` (val: `3`) | `1` (val: `4`) | `3 + 4 = 7 > 7` ❌ No | `2` | Sum equal, not greater. `left++` |
| `3` (val: `7`) | `1` Loop exits | | | | `left == right`, terminate inner loop |
| | | | | | |
| `2` (val: `6`) | `0` (val: `3`) | `1` (val: `4`) | `3 + 4 = 7 > 6` ✅ Yes | `2 + (1 - 0) = 3` | Triplet: `[3,4,6]`. `right--` |
| `2` (val: `6`) | `0` Loop exits | | | | `left == right`, terminate inner loop |

### Result
```java
count = 3
```

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int findNumberOfTriangles(int[] nums) {
        if (nums == null || nums.length < 3) {
            return 0;
        }

        int n = nums.length;
        int count = 0;

        // Step 1: Sort the array to unlock structural monotonicity
        Arrays.sort(nums);

        // Step 2: Fix the longest side from right to left
        for (int i = n - 1; i >= 2; i--) {
            int left = 0;
            int right = i - 1;

            // Step 3: Run Two-Pointer scan for the remaining two sides
            while (left < right) {
                if (nums[left] + nums[right] > nums[i]) {
                    // If nums[left] + nums[right] > nums[i], then all elements 
                    // from index 'left' to 'right-1' paired with 'right' are also valid.
                    count += (right - left);
                    
                    // Move the right pointer downward to search for smaller combinations
                    right--;
                } else {
                    // Sum is too small, increase the lower boundary element
                    left++;
                }
            }
        }

        return count;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N²)
```
Sorting the array takes \(\mathcal{O}(N \log N)\) time. The outer loop runs \(N\) times, and for each iteration, the inner two-pointer sweep runs in linear \(\mathcal{O}(N)\) time since `left` and `right` converge on each other. Thus, the total complexity bound is \(\mathcal{O}(N \log N + N^2) = \mathcal{O}(N^2)\).

### Space Complexity

```java
O(1)
```
The algorithm uses inline sorting techniques and basic integer pointers, meaning no extra dynamic data structures are allocated. *(Note: depending on the language implementation, `Arrays.sort()` may utilize \(\mathcal{O}(\log N)\) stack space for quicksort variants).*

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Brute Force** | \(\mathcal{O}(N^3)\) | \(\mathcal{O}(1)\) | Simple to write, but hits an immediate TLE on larger sizes. |
| **Sorting + Two-Pointer** | \(\mathcal{O}(N^2)\) | \(\mathcal{O}(1)\) | **Highly optimal.** Sorting simplifies checking three conditions down to a single index lookup pattern. |

---

# Key Takeaways

- **Sort to Simplify Inequalities:** Sorting changes multi-variable inequality constraints into predictable, directional boundaries where only the edge constraint needs checking.
- **Range Calculations:** When `nums[left] + nums[right] > nums[i]` matches, the number of successful sub-pairs is extracted mathematically as `right - left` in \(\mathcal{O}(1)\) time, skipping explicit inner traversal loops.
- Core concepts:
  - Triangle Inequality Rules
  - Sorting Preprocessing
  - Symmetric Converging Two-Pointer Strategy
- Expected interview preference: **Sorting + Two-Pointer Approach**.
