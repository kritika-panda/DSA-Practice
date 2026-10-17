# Merge Two Sorted Arrays Without Extra Space ([LeetCode 88 Variant](https://leetcode.com) / [GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given two sorted arrays `arr1[]` of size `n` and `arr2[]` of size `m`. Your task is to **merge them into a single sorted sequence in-place** without using any extra auxiliary data structures. 

After completing the merge, `arr1[]` must contain the absolute smallest `n` elements in non-decreasing order, and `arr2[]` must contain the remaining `m` elements in non-decreasing order.

---

## Examples

### Example 1

```text
Input: n = 4, arr1[] = , m = 5, arr2[] = 
Output: arr1[] = , arr2[] = 
```

### Explanation

```text
After consolidating all elements together, the smallest 4 elements (1, 2, 3, 5) 
settle in arr1[], and the remaining 5 larger items populate arr2[] in sorted order.
```

---

### Example 2

```text
Input: n = 2, arr1[] = , m = 3, arr2[] = 
Output: arr1[] = , arr2[] = 
```

---

# Intuition

A naive approach would allocate a secondary array of size `n + m`, weave elements together using standard linear pointers, and then slice the items back into the original arrays. However, this violates the critical constraint to execute mutations **in-place with O(1) constant auxiliary space**.

To solve this problem optimally inside a linear pass, we can use the **Two-Pointer Grid Boundary Swap** strategy:
- Since both input arrays are already sorted independently, the absolute largest elements of `arr1[]` reside at its terminal tail boundary, and the absolute smallest elements of `arr2[]` reside at its initial front boundary.
- We can anchor a pointer `left` at the end of `arr1[]` (`n - 1`) and a pointer `right` at the start of `arr2[]` (`0`).
- We systematically compare `arr1[left]` with `arr2[right]`:
  - If `arr1[left] > arr2[right]`, it means these elements are sitting on the wrong side of the global sorted threshold boundary. We **swap them in-place**, and then move our tracking cursors inward (`left--`, `right++`).
  - If `arr1[left] <= arr2[right]`, it signifies that the elements have achieved equilibrium across the boundary line. Because both arrays are sorted, if `arr1[left]` is already smaller than or equal to `arr2[right]`, all preceding elements in `arr1[]` are guaranteed to be smaller than all subsequent elements in `arr2[]`. We can **short-circuit and break out of the loop early**.
- **The Final Sorting Sweep:** Once the boundary swapping phase finishes, the correct *sets* of elements have settled into their respective arrays. However, their internal positional arrangements might be disordered from the swaps. We simply run a standard \(O(N \log N + M \log M)\) sorting pass on both arrays individually to restore perfect non-decreasing monotonic tracks.

---

# State Definition

The multi-array tracking pipeline coordinates boundary evaluations using two boundary index cursors:

```java
int left = n - 1;  // Pointer tracking the largest elements at the tail of arr1
int right = 0;     // Pointer tracking the smallest elements at the front of arr2
```

---

# Recurrence / Structural Squeezing Condition

```text
While left >= 0 AND right < m:
    If arr1[left] > arr2[right]:
        Swap(arr1[left], arr2[right])
        left--, right++
    Else:
        Break (Equilibrium achieved)
Sort(arr1)
Sort(arr2)
```

---

# Optimal Multi-Pointer Boundary Swap Solution

## Idea

We configure a single convergence loop that steps inward from both edges. By swapping mismatched boundary elements directly across arrays and calling optimized native library sort functions afterwards, we finish the merge without allocating extra memory matrices on the heap.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public static void merge(int[] arr1, int[] arr2, int n, int m) {
        // Guard Condition: If either array boundary is empty, no merging is needed
        if (arr1 == null || arr2 == null || n == 0 || m == 0) {
            return;
        }

        // Initialize boundary pointers
        int left = n - 1; // Anchored at the absolute maximum of arr1
        int right = 0;    // Anchored at the absolute minimum of arr2

        // Phase 1: Swapping misaligned cross-boundary element pairs
        while (left >= 0 && right < m) {
            if (arr1[left] > arr2[right]) {
                // Elements are out of order globally -> execute an in-place swap operation
                int temp = arr1[left];
                arr1[left] = arr2[right];
                arr2[right] = temp;

                // Shift tracking cursors inward to evaluate subsequent elements
                left--;
                right++;
            } else {
                // Equilibrium state achieved -> short-circuit the loop early!
                // All remaining items in arr1 are guaranteed to be <= all remaining items in arr2
                break;
            }
        }

        // Phase 2: Restore internal array sorting monotonic invariants
        Arrays.sort(arr1);
        Arrays.sort(arr2);
    }
}
```

---

## Complexity Analysis

Let N be the total length of `arr1[]` and M be the total length of `arr2[]`.

### Time Complexity

```text
Time Complexity: O((N + M) log(N + M))
```
*Explanation:* The cross-boundary swapping loop executes a maximum of \(\min(N, M)\) times, running in linear time. The final sorting pass utilizes Java's highly optimized primitive Dual-Pivot Quicksort, which costs \(O(N \log N)\) for `arr1[]` and \(O(M \log M)\) for `arr2[]`. Combining these sequential operations bounds total time parameters cleanly.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. By mutatively exchanging element block addresses directly across existing array heap pointers, the algorithm completely removes the need for helper collections, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing dual-stream mergers, boundary grid balancing, and multi-pointer matrix partitions.

✅ **The Boundary Equilibrium Principle:** The ability to `break` out of our cross-boundary loop early is a vital optimization choice. Because both arrays start sorted, the moment the trailing maximum of the first array is smaller than or equal to the leading minimum of the second array, a perfect structural wall is formed. This allows us to skip parsing the remainder of the dataset entirely.

✅ Directly drives real-world microarchitectures like **relational database disk block consolidators** (merging sorted index segments from different disk pages in-place before executing writing flushes), cache table buffers organizers, telemetric multi-stream mergers, and sorting networks.

### Similar Problems

- Merge Sorted Array ([LeetCode 88](https://leetcode.com))
- Squares of a Sorted Array ([LeetCode 977](https://leetcode.com))
- Interval List Intersections ([LeetCode 986](https://leetcode.com))
- Sort Colors / Dutch National Flag ([LeetCode 75](https://leetcode.com))
