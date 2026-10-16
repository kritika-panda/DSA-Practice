# Rotate Array Left by K Places ([GeeksforGeeks](https://geeksforgeeks.com) / [LeetCode 189 Variant](https://leetcode.com))

## Problem Statement

Given an integer array `arr` and a non-negative integer `k`, **left rotate** the array to the left by `k` places. 

Left rotating an array shifts every element `k` positions to the left. Elements that fall off the front boundary must wrap around systematically to populate the terminal tail slots. Modifications must be executed completely **in-place** directly on the input array with an optimal auxiliary space complexity of **O(1)**.

---

## Examples

### Example 1

```text
Input: arr =, k = 2
Output: [3, 4, 5, 1, 2]
```

### Explanation

```text
- Shift elements left by 2 positions.
- Elements 1 and 2 fall off the front and wrap around to the end of the array.
```

---

### Example 2

```text
Input: arr =, k = 3
Output: [10, 20, 30]
```

### Explanation

```text
Rotating an array of length 3 exactly 3 times returns the array to its original configuration.
```

---

# Intuition

A naive approach would use an auxiliary array of size `N`, copy elements to their new indices `(i - k + N) % N`, and overwrite the original array. This costs an expensive O(N) extra memory penalty.

To achieve a true **O(1) absolute constant auxiliary space** footprint, we can use the elegant **Triple-Reversal Design Pattern**. Left rotating an array by `k` places splits the array into two logical segments: the first `k` elements that need to move to the back, and the remaining `N - k` elements that shift to the front.

By running three distinct in-place segment reversals, the structural wrap-around occurs naturally:
1.  **Reverse the prefix segment** consisting of the first `k` elements (index `0` to `k - 1`).
2.  **Reverse the suffix segment** consisting of the remaining elements (index `k` to `N - 1`).
3.  **Reverse the entire array completely** (index `0` to `N - 1`).

---

# State Definition

To keep track of rotation boundaries and handle large `k` values dynamically:

```java
int n = arr.length;
k = k % n; // Modulo arithmetic eliminates redundant full-circle cycles
```

---

# Recurrence / Structural State Invariant

```text
1. reverse(arr, 0, k - 1)      // Reverse first k elements
2. reverse(arr, k, n - 1)      // Reverse remaining elements
3. reverse(arr, 0, n - 1)      // Reverse whole array
```

---

# Optimal Single-Pass Reversal Solution

## Idea

We simplify large rotation counts using modulo math. We then deploy a two-pointer `reverse` function three times consecutively. This rearranges all elements cleanly in-place without generating a duplicate copy of the array on the heap.

---

## Java Code

```java
class Solution {
    public void rotateLeft(int[] arr, int k) {
        // Guard Condition: An empty array or single-element array needs no rotation pass
        if (arr == null || arr.length <= 1 || k == 0) {
            return;
        }

        int n = arr.length;
        
        // Modulo Invariant: Eliminates redundant full structural loops 
        // (e.g., rotating an array of size 5 by 12 is equivalent to rotating by 12 % 5 = 2)
        k = k % n;
        if (k == 0) {
            return;
        }

        // Phase 1: Reverse the first k elements [0 to k-1]
        reverse(arr, 0, k - 1);

        // Phase 2: Reverse the remaining n-k elements [k to n-1]
        reverse(arr, k, n - 1);

        // Phase 3: Reverse the entire array completely [0 to n-1]
        reverse(arr, 0, n - 1);
    }

    // High-speed in-place two-pointer segment flipper
    private void reverse(int[] arr, int left, int right) {
        while (left < right) {
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
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
*Explanation:* The first reversal operations process `k` elements, the second processes `N - k` elements, and the final global reversal processes all `N` elements. This means every array element is swapped exactly twice total, bounding execution runtime strictly linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively flipping elements inline directly on the existing array references, the algorithm entirely bypasses extra heap copy blocks, maintaining a flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing circular array shifts and memory buffer displacements.

✅ **The Value of Modulo Compression:** Always apply a modulo reduction guard (`k = k % n`) upfront for cyclic rotation challenges. This reduces massive scaling rotation indexes (e.g., k = 10⁹) down to constant-time bounds, completely shielding your code from out-of-bounds array crashes.

✅ Directly drives real-world microarchitectures like **cyclic network ring buffer packet shifters**, bitwise rotation processors, video stream frame displacement matrices, and memory page offset manipulators.

### Similar Problems

- Rotate Array Right by K Places ([LeetCode 189](https://leetcode.com))
- Rotate List ([LeetCode 61](https://leetcode.com))
- Left Rotate Array by One ([GeeksforGeeks](https://geeksforgeeks.com))
- Reverse Words in a String ([LeetCode 151](https://leetcode.com))
