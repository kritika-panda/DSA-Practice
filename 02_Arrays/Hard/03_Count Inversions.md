# Count Inversions in an Array ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an integer array `arr[]`, return the **total inversion count** of the array.

Two elements `arr[i]` and `arr[j]` form an **inversion** if:
- `i < j` (the element appears before the other in the sequence)
- `arr[i] > arr[j]` (the preceding element has a strictly larger value payload)

---

## Examples

### Example 1

```text
Input: arr = [2, 4, 1, 3, 5]
Output: 3
```

### Explanation

```text
The 3 inversions present inside this array sequence are:
1. (2, 1) -> index 0 < index 2 and 2 > 1
2. (4, 1) -> index 1 < index 2 and 4 > 1
3. (4, 3) -> index 1 < index 3 and 4 > 3
```

---

### Example 2

```text
Input: arr = [2, 3, 4, 5, 6]
Output: 0
```

### Explanation

```text
The array is already perfectly sorted in non-decreasing order, meaning zero inversions exist.
```

---

### Example 3

```text
Input: arr = [10, 10, 10]
Output: 0
```

### Explanation

```text
All elements are completely identical, so no structural element pairs satisfy the strict arr[i] > arr[j] inequality.
```

---

# Intuition

An inversion count measures **how far an array is from being perfectly sorted**. A sorted array has an inversion count of 0, while an array sorted in reverse order has a maximum inversion count of \(\frac{N(N-1)}{2}\).

- **The Brute-Force Trap:** A native nested double loop compares every pair of elements `(i, j)`, running in a sub-optimal \(O(N^2)\) quadratic time footprint. This will trigger a Time Limit Exceeded (TLE) error for array sizes up to \(N = 5 \times 10^5\).
- **Divide and Conquer Optimization:** We can optimize our lookups down to a fast **\(O(N \log N)\)** timeline by embedding our counter logic straight inside a modified **Merge Sort** sorting pipeline.
- **The Split Counting Invariant:** When we split an array into a `left` half and a `right` half, the total inversions can be broken down into three categories:
  \[\text{Total Inversions} = \text{Inversions(left)} + \text{Inversions(right)} + \text{Inversions(split during merge)}\]
- **The Merge Step Calculation:** Assume both the `left` and `right` arrays are already sorted individually. We maintain a pointer `i` for `left` and a pointer `j` for `right`.
  - If `left[i] <= right[j]`, the relative order is correct. We copy `left[i]` into our temporary workspace array and advance `i`.
  - If `left[i] > right[j]`, a structural inversion is detected! Because the `left` array is sorted, if `left[i]` is strictly greater than `right[j]`, **every subsequent element from index `i` up to the end of the `left` array boundary is also guaranteed to be strictly greater than `right[j]`**.
  - Thus, this single element `right[j]` instantly forms exactly **`(mid - i + 1)`** inversions against the remaining left elements. We add this value block to our running total counter, copy `right[j]` over, and advance `j`.

---

# State Definition

The divide-and-conquer processing tree tracks regional boundaries and inversion counts across two recursive variables:

```text
mergeSortAndCount(arr, temp, left, right):
- 'left' and 'right' anchor the inclusive subarray boundaries currently being evaluated.
- 'temp' acts as a shared pre-allocated buffer array to avoid recursive heap allocation garbage.
```

---

# Recurrence / Structural Math Condition

```text
If left >= right -> Return 0

int mid = left + (right - left) / 2
long count = mergeSortAndCount(arr, temp, left, mid)
           + mergeSortAndCount(arr, temp, mid + 1, right)
           + merge(arr, temp, left, mid, right)
Return count
```

---

# Optimal \(O(N \log N)\) Divide-and-Conquer Solution

## Idea

We allocate a single primitive `temp[]` array workspace to serve as our central merging buffer. We launch a recursive merge sort routine. As sorted subarrays are woven together, any right-side element that jumps ahead of a left-side element accumulates the entire remaining left-side slice count in \(O(1)\) constant time, eliminating slow linear index tracing.

---

## Java Code

```java
class Solution {
    public static long inversionCount(int[] arr) {
        // Guard Condition: Arrays with less than two elements cannot contain pairs
        if (arr == null || arr.length <= 1) {
            return 0;
        }

        int n = arr.length;
        // Shared workspace buffer to eliminate memory allocations inside the recursive stack
        int[] temp = new int[n];

        // Launch recursive divide-and-conquer merge sort tracking pass
        return mergeSortAndCount(arr, temp, 0, n - 1);
    }

    private static long mergeSortAndCount(int[] arr, int[] temp, int left, int right) {
        long count = 0;

        // Base Case: Subarray containing 1 or 0 elements has zero internal inversions
        if (left < right) {
            int mid = left + (right - left) / 2;

            // Step 1: Accumulate total inversions residing strictly in the left half segment
            count += mergeSortAndCount(arr, temp, left, mid);

            // Step 2: Accumulate total inversions residing strictly in the right half segment
            count += mergeSortAndCount(arr, temp, mid + 1, right);

            // Step 3: Merge both sorted halves together and count split cross-inversions
            count += merge(arr, temp, left, mid, right);
        }

        return count;
    }

    private static long merge(int[] arr, int[] temp, int left, int mid, int right) {
        int i = left;    // Traversal tracking cursor scanning the sorted left subarray
        int j = mid + 1; // Traversal tracking cursor scanning the sorted right subarray
        int k = left;    // Insertion cursor writing elements into the temporary workspace buffer
        
        long swaps = 0;

        // Weave elements from both subarrays together in ascending order
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                // Inversion Detected: arr[i] > arr[j]
                temp[k++] = arr[j++];
                
                // Since the left subarray is sorted, if arr[i] > arr[j], then all remaining 
                // elements from the active cursor index 'i' up to 'mid' are also > arr[j].
                // We add the entire remaining slice block count to our running register.
                swaps += (mid - i + 1);
            }
        }

        // Copy over any leftover items remaining in the left subarray block
        while (i <= mid) {
            temp[k++] = arr[i++];
        }

        // Copy over any leftover items remaining in the right subarray block
        while (j <= right) {
            temp[k++] = arr[j++];
        }

        // Overwrite the original array references with sorted values from our buffer workspace
        for (i = left; i <= right; i++) {
            arr[i] = temp[i];
        }

        return swaps; // Return split inversions located across this merge boundary layer
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `arr`.

### Time Complexity

```text
Time Complexity: O(N log N)
```
*Explanation:* The structural framework matches standard Merge Sort. The recursive state tree splits the search space in half at each depth level, resulting in a stack height of \(\log N\). At each of these levels, the linear merging loops sweep across a maximum total of \(N\) array nodes. The runtime scales strictly inside stable \(O(N \log N)\) parameters across all data profiles.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* The algorithm requires a single auxiliary array `temp` of size \(N\) to assemble sorted subarrays during the merge pass. The recursive framework call stack consumes up to \(O(\log N)\) space to store function activation frames, locking our auxiliary memory footprint to a linear upper bound of \(O(N)\).

---

# Key Takeaways

✅ High-tier fundamental algorithm representing the peak of divide-and-conquer optimizations and sorting loop mutations.

✅ **The Block Addition Rule:** The optimization line `swaps += (mid - i + 1);` transforms the time profile. Instead of manually scanning forward to match every individual trailing element item-by-item, using the sorted mathematical property of the left subarray allows us to count an entire block of inversions instantly in \(O(1)\) constant time.

✅ Directly drives real-world microarchitectures like **recommendation sorting engines ranking list correlation metrics** (measuring how closely a user's ranked preferences match a global baseline), calculating Kendall's Tau distance profiles, sorting stability metric generators, and data streaming error checkers.

### Similar Problems

- Global and Local Inversions ([LeetCode 775](https://leetcode.com))
- Reverse Pairs ([LeetCode 493](https://leetcode.com))
- Count of Smaller Numbers After Self ([LeetCode 315](https://leetcode.com))
- Merge Sorted Array ([LeetCode 88](https://leetcode.com))

<FollowUp>
Let me know which advanced divide-and-conquer sequence tracker or array sorting mutation puzzle you want to structure next:

