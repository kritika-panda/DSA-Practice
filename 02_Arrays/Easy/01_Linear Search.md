# Linear Search in an Array ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array `arr` and an integer `target`, write a function to search for the `target` element inside the array. If the `target` exists, return its **0-indexed position** (the first occurrence). If the element is not present in the array, return **`-1`**.

---

## Examples

### Example 1

```text
Input: arr =, target = 110
Output: 6
```

### Explanation

```text
The target value 110 is positioned at index 6 of the array.
```

---

### Example 2

```text
Input: arr =, target = 175
Output: -1
```

### Explanation

```text
The target element 175 is not present anywhere inside the array, so -1 is returned.
```

---

# Intuition

A **Linear Search** is the most basic, baseline sequential search algorithm. It makes zero assumptions about the data structure's configuration (unlike Binary Search, which strictly requires the data to be sorted up front).

The mechanism is completely straightforward:
- We initialize a tracking cursor at the absolute beginning of the array (index `0`).
- We step forward **one element at a time** from left to right.
- At each element `i`, we run a primitive equality comparison check: `arr[i] == target`.
- If a match is found, we short-circuit out of the lookup instantly and return the active index `i`.
- If our tracking cursor sweeps all the way to the terminal boundary of the array size without triggering a match, it proves the target does not exist, so we return `-1`.

---

# State Definition

The sequential lookup tracks progress using a single positional cursor variable:

```java
int i = 0; // Iteration index tracker variable scanning from front to back
```

---

# Recurrence / Structural Loop Condition

```text
For i from 0 to arr.length - 1:
    If arr[i] == target -> Return i
Return -1
```

---

# Optimal Single-Pass Sequential Solution

## Idea

We set up a standard linear `for` loop that sweeps across the array block. By incorporating an immediate short-circuit `return` statement right at the point of match verification, we guarantee minimal execution footprint using true constant auxiliary storage space.

---

## Java Code

```java
class Solution {
    public int search(int[] arr, int target) {
        // Guard Condition: An empty array cannot contain any target elements
        if (arr == null || arr.length == 0) {
            return -1;
        }

        // Core Step: Single forward sequential pass over the array timeline
        for (int i = 0; i < arr.length; i++) {
            // Check equality against the target element
            if (arr[i] == target) {
                return i; // Match located, short-circuit out instantly!
            }
        }

        // Entire collection space exhausted without a match
        return -1;
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `arr`.

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* In the best-case configuration where the first element is our target (`arr[0] == target`), the loop resolves in O(1) constant time. In the worst-case scenario where the target sits at the absolute tail or is missing entirely, the loop must check every single item. This yields a worst-case execution runtime that scales completely linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The sequential scan modifies no pointers and handles calculations entirely in-place, requiring only a single local integer variable, which maintains a completely flat constant space footprint.

---

# Key Takeaways

✅ Core baseline foundational template for un-ordered lookup operations in arrays.

✅ **The Sorting Dependency Invariant:** Linear Search is universally resilient because it requires **no pre-sorted data organization**. While algorithms like Binary Search scale much faster at an ultra-fast $O(\log N)$ logarithmic speed, they will return corrupt answers if the array is unsorted. For completely random, raw, or unsorted streaming datasets, Linear Search remains the absolute structural floor tool.

✅ Directly drives real-world microarchitectures like **un-indexed data stream filtering**, log text parsing search utilities, scanning raw hardware buffer packets, cache miss fallback searches, and basic array lookups.

### Similar Problems

- Binary Search ([LeetCode 704](https://leetcode.com))
- Find Target Indices After Sorting Array ([LeetCode 2089](https://leetcode.com))
- Search in Rotated Sorted Array ([LeetCode 33](https://leetcode.com))
- Search a 2D Matrix ([LeetCode 74](https://leetcode.com))
