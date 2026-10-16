# Left Rotate Array by One ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array `arr`, write a function to **left rotate** the array by exactly **one position**. 

Left rotating an array shifts every element one position to the left, and the absolute first element of the array moves to the absolute last position. Modifications must be executed completely **in-place** directly on the input array.

---

## Examples

### Example 1

```text
Input: arr = [1, 2, 3, 4, 5]
Output: [2, 3, 4, 5, 1]
```

### Explanation

```text
- Element 1 at index 0 shifts to the end (index 4).
- Elements 2, 3, 4, 5 shift left by one slot.
```

---

### Example 2

```text
Input: arr = [10, 20, 30]
Output: [20, 30, 10]
```

---

# Intuition

To shift elements left by one position, we cannot simply run a loop doing `arr[i] = arr[i + 1]`. If we do that starting from the front, the first element `arr[0]` gets overwritten and completely erased from memory before it can bubble down to the tail.

To execute this transition cleanly in a **single linear pass with zero extra storage arrays**:
1. Save the value of the absolute first element (`arr[0]`) into a temporary scalar storage variable (`temp`).
2. Run a loop shifting all subsequent elements from index `1` up to `N - 1` exactly one position to the left: `arr[i - 1] = arr[i]`.
3. Once the shifting pass completes, write the saved `temp` value into the final terminal position slot of the array (`arr[N - 1]`).

---

# State Definition

The inline shift operations track states using a single primitive variable cache:

```java
int temp = arr[0]; // Temporary variable holding the first element before it gets overwritten
```

---

# Recurrence / Structural Loop Condition

```text
temp = arr[0]
For i from 1 to arr.length - 1:
    arr[i - 1] = arr[i]
arr[arr.length - 1] = temp
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We isolate the first element inside a local registry. We then run a simple linear traversal loop shifting elements leftwards. Finally, we link our isolated data value back to the tail boundary, executing the left rotation using O(1) auxiliary storage space.

---

## Java Code

```java
class Solution {
    public void rotateLeftByOne(int[] arr) {
        // Guard Condition: An empty array or single-element array needs no rotation pass
        if (arr == null || arr.length <= 1) {
            return;
        }

        int n = arr.length;

        // Step 1: Cache the absolute first element to protect it from being overwritten
        int temp = arr[0];

        // Step 2: Run a single pass shifting all elements leftward by one slot position
        for (int i = 1; i < n; i++) {
            arr[i - 1] = arr[i];
        }

        // Step 3: Place the cached first element at the absolute terminal tail boundary
        arr[n - 1] = temp;
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
*Explanation:* The array shifting loop processes exactly N - 1 elements linearly. Inside the block, it performs simple constant-time O(1) primitive reference assignments, bounding the total execution runtime strictly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively adjusting elements inline directly inside the source array structure, the algorithm completely removes the need for helper proxy arrays, preserving a flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing sequence displacements and circular array shifts.

✅ **The Overwrite Guard Pattern:** When performing in-place stream transformations where values shift sequentially into neighbor positions, identify the "victim node" that will be overwritten first. Isolating it upfront into a temporary primitive register variable guarantees zero data loss during array adjustments.

✅ Directly drives real-world microarchitectures like **operating system round-robin scheduling queues** (shifting the active task process to the back of the line after its execution quantum terminates), streaming cyclic buffers, text ticker display wrappers, and byte-shifting filters.

### Similar Problems

- Rotate Array by K Positions ([LeetCode 189](https://leetcode.com))
- Rotate List ([LeetCode 61](https://leetcode.com))
- Shift 2D Grid ([LeetCode 1260](https://leetcode.com))
- Zero Filled Subarray Summary Metrics
