# Sort Colors / Sort an Array of 0s, 1s, and 2s ([LeetCode 75](https://leetcode.com) / [GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array `nums` with `n` objects colored red, white, or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function and inside a **single linear pass** using **O(1) constant auxiliary space**.

---

## Examples

### Example 1

```text
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

---

### Example 2

```text
Input: nums = [2,0,1]
Output: [0,1,2]
```

---

# Intuition

A naive approach would use a comparison sorting algorithm like Merge Sort or Quick Sort, which takes \(O(N \log N)\) time. Another straightforward option is a **Counting Sort** pass: loop through the array once to count the total number of 0s, 1s, and 2s, and then overwrite the array sequentially. However, Counting Sort requires two complete passes over the dataset.

To achieve a true **single-pass in-place solution**, we can deploy Dijkstra's famous **Dutch National Flag Algorithm**. This pattern uses a three-way partitioning mechanism tracked by three active pointers:
- `low`: Anchors the next available position for a `0` at the front of the array.
- `high`: Anchors the next available position for a `2` at the back of the array.
- `mid`: Serves as our active scanning cursor moving from left to right.

The core structural logic handles elements based on the value at `nums[mid]`:
1.  **Encounter a `0` (`nums[mid] == 0`)**: Swap `nums[mid]` with `nums[low]`. Since we know the item swapped from `low` is guaranteed to be a `1` (or same as `mid`), we can safely advance **both** pointers: `low++` and `mid++`.
2.  **Encounter a `1` (`nums[mid] == 1`)**: The element is already in its correct relative center zone. We simply advance our scanning cursor: `mid++`.
3.  **Encounter a `2` (`nums[mid] == 2`)**: Swap `nums[mid]` with `nums[high]`, and decrement `high--`. Crucially, we **do not** advance `mid` yet, because the element swapped from the back is completely uninspected and must be evaluated in the next iteration step.

---

# State Definition

The three-way partitioning pipeline tracks compaction zones using three local index cursors:

```java
int low = 0;        // Insertion boundary tracking 0 elements at the front
int mid = 0;        // Active scanning pointer sweeping across the data stream
int high = n - 1;   // Insertion boundary tracking 2 elements at the tail
```

---

# Recurrence / Structural Partitioning Condition

```text
While mid <= high:
    If nums[mid] == 0:
        Swap(nums[low], nums[mid])
        low++, mid++
    Else If nums[mid] == 1:
        mid++
    Else:
        Swap(nums[mid], nums[high])
        high--
```

---

# Optimal Single-Pass Dutch National Flag Solution

## Idea

We configure a single traversal loop controlled by our `mid` pointer. By grouping 0s to the front and pushing 2s to the back using high-speed inline element swaps, we automatically squeeze all 1s into the center zone inside a single pass.

---

## Java Code

```java
class Solution {
    public void sortColors(int[] nums) {
        // Guard Condition: Trivial arrays containing less than two items are already sorted
        if (nums == null || nums.length <= 1) {
            return;
        }

        int low = 0;
        int mid = 0;
        int high = nums.length - 1;

        // Core Step: Single forward pass three-way partitioning
        while (mid <= high) {
            if (nums[mid] == 0) {
                // Choice 1: Found a 0 -> Swap it forward into the low zone
                int temp = nums[low];
                nums[low] = nums[mid];
                nums[mid] = temp;
                
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                // Choice 2: Found a 1 -> Already in center alignment, just advance cursor
                mid++;
            } else {
                // Choice 3: Found a 2 -> Swap it backward into the high zone
                int temp = nums[mid];
                nums[mid] = nums[high];
                nums[high] = temp;
                
                // Decrement high. Note that we do NOT increment mid here because
                // the newly swapped element from high must be evaluated next.
                high--;
            }
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
*Explanation:* The `mid` pointer shifts rightward and the `high` pointer shifts leftward, narrowing the uninspected search space until they cross over. Every element is visited and swapped a maximum of a constant number of times inside a single pass, bounding execution runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively shifting data values completely in-place directly on the existing array references, the algorithm entirely bypasses extra heap allocation budgets or tracking tables, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing three-way partitioning, element grouping, and multi-pointer categorization tracks.

✅ **The Mid-Pointer Hold Rule:** In the Dutch National Flag algorithm, when you swap a `2` to the back (`nums[mid] == 2`), you **must not increment `mid`** in that same step. The item pulled from the back boundary is completely unknown—it could be a `0`, a `1`, or another `2`. Leaving `mid` stationary ensures this new element is processed accurately during the subsequent loop iteration.

✅ Directly drives real-world microarchitectures like **operating system memory page separators** (segregating kernel space, user space, and unallocated buffer sectors in a single stream pass), quicksort pivot routers, data packet classifiers, and stream filtering systems.

### Similar Problems

- Move Zeroes ([LeetCode 283](https://leetcode.com))
- Remove Element ([LeetCode 27](https://leetcode.com))
- Rearrange Array Elements by Sign ([LeetCode 2149](https://leetcode.com))
- Partition Array According to Given Pivot ([LeetCode 2161](https://leetcode.com))
