# Next Permutation ([LeetCode 31](https://leetcode.com))

## Problem Statement

A **permutation** of an array of integers is an arrangement of its members into a sequence or linear order.
- For example, for `arr = [1,2,3]`, the following are all the permutations of `arr`: `[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]`.

The **next permutation** of an array of integers is the next lexicographically greater permutation of its integer. More formally, if all the permutations of the array are sorted in one list alphabetically or numerically, then the **next permutation** of that array is the permutation that follows it in the sorted list. 

If such an arrangement is not possible (i.e., the array is already sorted in descending order, representing the absolute largest state), the array must be rearranged as the **lowest possible order** (i.e., sorted in ascending order).

You must modify the array **in-place** directly on the input array using an optimal auxiliary space complexity of **O(1)**.

---

## Examples

### Example 1

```text
Input: nums = [1,2,3]
Output: [1,3,2]
```

---

### Example 2

```text
Input: nums = [3,2,1]
Output: [1,2,3]
```

### Explanation

```text
The array [3,2,1] is already in the absolute highest lexicographical order. 
It wraps back around to the smallest possible configuration by sorting in ascending order.
```

---

### Example 3

```text
Input: nums = [1,1,5]
Output: [1,5,1]
```

---

# Intuition

A brute-force approach would generate every single permutation, sort them lexicographically, locate the current arrangement, and return its successor. However, this triggers an impractical \(O(N \cdot N!)\) factorial runtime penalty.

To find the next lexicographically greater permutation inside a **single linear pass with O(1) constant auxiliary space**, we look for the localized point of structural change from right to left:

1.  **Locate the Pivot Breakpoint (`i`):** Scan backward from right to left to find the first element that is strictly smaller than its right neighbor (`nums[i] < nums[i + 1]`). This element `nums[i]` is our pivot. Everything to the right of `i` forms a monotonically decreasing suffix block, which is already at its maximum configuration. If no pivot is found, the entire array is decreasing; skip directly to Step 3.
2.  **Locate the Smallest Greater Element (`j`):** Scan backward from the tail of the array to find the first element `nums[j]` that is strictly larger than our pivot (`nums[j] > nums[i]`). Swap the elements at `nums[i]` and `nums[j]`. This minimally bumps up the value at position `i`.
3.  **Minimize the Tail Suffix:** Since everything to the right of `i` is still structurally locked inside a descending sequence, it represents the *largest possible configuration* for that suffix. To transition to the *smallest adjacent lexicographical option*, we reverse the entire suffix block starting from index `i + 1` up to `N - 1` to invert it into an ascending order sequence.

---

# State Definition

The analytical rearrangement pipeline tracks boundary markers entirely in-place across three pointer metrics:

```java
int n = nums.length;
int i = n - 2; // Scanning pointer searching for the horizontal pivot break node
```

---

# Recurrence / Structural State Condition

```text
1. Find pivot 'i' from tail: while i >= 0 AND nums[i] >= nums[i + 1] -> i--
2. If i >= 0:
       Find 'j' from tail: while nums[j] <= nums[i] -> j--
       Swap(nums[i], nums[j])
3. Reverse(nums, i + 1, n - 1)
```

---

# Optimal Single-Pass In-Place Rearrangement Solution

## Idea

The implementation executes a linear backward pass to anchor the change coordinate. By using specialized inline functions for element swapping and segment reversing, it handles edge-case wraps smoothly using true constant auxiliary storage space.

---

## Java Code

```java
class Solution {
    public void nextPermutation(int[] nums) {
        // Guard Condition: Arrays with less than two elements have no adjacent variations
        if (nums == null || nums.length <= 1) {
            return;
        }

        int n = nums.length;
        int i = n - 2;

        // Phase 1: Scan backward to find the first index 'i' where nums[i] < nums[i + 1]
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Phase 2: If a valid structural pivot breakpoint is located
        if (i >= 0) {
            int j = n - 1;
            // Scan backward to locate the first element strictly larger than our pivot
            while (nums[j] <= nums[i]) {
                j--;
            }
            // Swap the pivot with this smallest available greater element
            swap(nums, i, j);
        }

        // Phase 3: Reverse the descending suffix following index 'i' to minimize its value layout
        // Note: If i == -1 (entire array was descending), this cleanly reverses the whole array to ascending order.
        reverse(nums, i + 1, n - 1);
    }

    // High-speed array reference element swapper
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    // High-speed in-place two-pointer segment flipper
    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            swap(nums, start, end);
            start++;
            end--;
        }
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The backward scan for the pivot breakpoint takes at most N operations. The scan for element `j` takes at most N operations. Reversing the suffix partition scales bounded by N/2 swaps. Because each consecutive processing phase runs linearly without nested backtracking loops, total execution runtime is strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively rearranging existing values in-place directly inside the source array buffer using primitive local variables, the algorithm completely removes the need for proxy sorting collections, preserving a flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing lexicographical adjustments, array transpositions, and sequence reorderings.

✅ **The Suffix Inversion Rule:** Reversing the descending suffix block (`reverse(nums, i + 1, n - 1)`) is a brilliant optimization step. Because a descending sequence represents the local mathematical maximum configuration, reversing it instantly flips it into an ascending sequence (the local absolute minimum), generating the smallest adjacent greater sequence without triggering an expensive full sorting pass.

✅ Directly drives real-world microarchitectures like **combinatorial permutation dictionary enumerators**, hardware layout analyzers, brute-force cryptographic key stream selectors, optimization routing planners, and string anagram calculators.

### Similar Problems

- Permutations ([LeetCode 46](https://leetcode.com))
- Permutations II ([LeetCode 47](https://leetcode.com))
- Permutation Sequence ([LeetCode 60](https://leetcode.com))
- Next Greater Element I ([LeetCode 496](https://leetcode.com))
