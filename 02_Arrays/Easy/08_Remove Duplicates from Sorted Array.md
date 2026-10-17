# Remove Duplicates from Sorted Array ([LeetCode 26](https://leetcode.com))

## Problem Statement

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates **in-place** such that each unique element appears only once. The relative order of the elements should be kept the same. Then return *the number of unique elements in `nums`*.

Consider the number of unique elements of `nums` to be `k`. To get accepted, you need to do the following things:
1. Modify the array `nums` such that the first `k` elements of `nums` contain the unique elements in the order they were present in `nums` initially. The remaining elements of `nums` are not important, as well as the size of `nums`.
2. Return `k`.

---

## Examples

### Example 1

```text
Input: nums = [1,1,2]
Output: 2, nums = [1,2,_]
```

### Explanation

```text
Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.
It does not matter what you leave beyond the returned k (hence they are underscored).
```

---

### Example 2

```text
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
```

### Explanation

```text
Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
```

---

# Intuition

Since the input array is already **sorted**, all duplicate values are guaranteed to sit adjacent to each other in a contiguous block. This property allows us to optimize the operation into a single pass without any expensive lookup tracking collections.

We can apply the **Two-Pointer (Read/Write) Design Pattern**:
- We initialize a `writePointer` at index `1`. This pointer tracks the next available slot where a newly discovered unique element should be written. (The absolute first element at index `0` is always implicitly unique).
- We use a `readPointer` starting from index `1` to scan the rest of the array sequentially.
- At each index, we compare the element at `readPointer` with the element right before it (`nums[readPointer - 1]`).
- If they do not match (`nums[readPointer] != nums[readPointer - 1]`), we have crossed a value boundary, signifying a new unique element.
- We copy this unique element forward: `nums[writePointer] = nums[readPointer]`, and then increment the `writePointer`.
- When the loop finishes, the array from index `0` up to `writePointer - 1` contains strictly unique elements, and `writePointer` holds the exact value of `k`.

---

# State Definition

The sequential compaction pipeline tracks unique boundary transitions using a single placement anchor:

```java
int writePointer = 1; // Tracks the destination index for the next unique element placement
```

---

# Recurrence / Structural State Condition

```text
For readPointer from 1 to nums.length - 1:
    If nums[readPointer] != nums[readPointer - 1]:
        nums[writePointer] = nums[readPointer]
        writePointer++
Return writePointer
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We configure a single linear traversal loop starting from index 1. When a unique element is verified by looking back one step, we copy it inline directly to the destination slot, optimizing auxiliary space down to absolute constant dimensions.

---

## Java Code

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        // Guard Condition: An empty array contains zero unique elements
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // The absolute first element at index 0 is always unique by default
        int writePointer = 1;

        // Core Step: Single linear scan pass lookback check
        for (int readPointer = 1; readPointer < nums.length; readPointer++) {
            // Compare the active element with its immediate predecessor
            if (nums[readPointer] != nums[readPointer - 1]) {
                // New unique element boundary detected -> write it to our placement anchor
                nums[writePointer] = nums[readPointer];
                
                // Advance the unique element placement pointer forward
                writePointer++;
            }
        }

        // Return total count of unique elements, matching the count k
        return writePointer;
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
*Explanation:* The `readPointer` sweeps sequentially from index 1 up to N - 1 exactly once. Inside the loop, it performs basic constant-time O(1) comparison and data value assignment operations, bounding the overall execution timeline strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively overriding duplicate slots inline directly on the existing array references, the algorithm entirely bypasses extra heap allocation budgets or tracking tables, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core baseline template for in-place array filtering, element compaction, and stream deduplication tasks.

✅ **The Lookback Validation Rule:** Comparing `nums[readPointer]` with `nums[readPointer - 1]` is highly strategic for sorted tracks. It allows us to pinpoint the exact transition edge of a unique block instantly, removing the need to compare against the `writePointer` slot directly.

✅ Directly drives real-world microarchitectures like **database storage compression filters** (deduplicating runs of identical keys in sorted storage indexes in-place before flashing data pages to disk), real-time signal stream deduplicators, transaction ledger processors, and file system diff consolidators.

### Similar Problems

- Remove Duplicates from Sorted Array II (Allowing up to two duplicates - [LeetCode 80](https://leetcode.com))
- Move Zeroes ([LeetCode 283](https://leetcode.com))
- Remove Element ([LeetCode 27](https://leetcode.com))
- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
