# Array Leaders ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an integer array `arr`, find and return all the **leaders** present in the array. 

An element is considered an **array leader** if it is strictly greater than or equal to all the elements to its right side. The rightmost element of the array is **always** a leader. The final results should match the relative horizontal order in which they appear in the original array.

---

## Examples

### Example 1

```text
Input: arr = [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
```

### Explanation

```text
- 17 is greater than all elements to its right (4, 3, 5, 2).
- 5 is greater than all elements to its right (2).
- 2 is the rightmost element, so it is implicitly a leader.
```

---

### Example 2

```text
Input: arr = [1, 2, 3, 4, 0]
Output: [4, 0]
```

---

### Example 3

```text
Input: arr = [10, 4, 2, 4, 1]
Output: [10, 4, 4, 1]
```

---

# Intuition

A brute-force approach would pick each element one by one and use a nested loop to check every single element to its right. However, this triggers a slow O(N²) quadratic runtime penalty.

To optimize the search down to a **single linear pass**, we can change our perspective and scan the array **backward from right to left**:
- The rightmost element (`arr[N-1]`) is always an array leader because there are no elements to its right. We anchor this value as our initial `max` tracking reference.
- As we step backward from index `N - 2` down to `0`:
  - We compare the current element `arr[i]` with our running `max`.
  - If `arr[i] >= max`, it means this element dominates everything to its right. It is a leader!
  - We record this element and update our running maximum tracker: `max = arr[i]`.
- **The Order Adjustment Step:** Since scanning backward collects our leaders in reverse order (from right to left), we simply invert or **reverse the results list** before returning it to restore the original chronological order.

---

# State Definition

The backward scanning pipeline tracks the local maximum constraint using a single register:

```java
int max = arr[n - 1]; // Running maximum suffix tracker register
```

---

# Recurrence / Structural State Condition

```text
Initialize max = arr[n - 1], append arr[n - 1] to results
For i from n - 2 down to 0:
    If arr[i] >= max:
        max = arr[i]
        append arr[i] to results
Reverse results
```

---

# Optimal Single-Pass Suffix-Maximum Solution

## Idea

We initialize our suffix tracking register using the rightmost element. We run a single backward traversal loop to extract dominating elements on the fly. Finally, we use a high-speed two-pointer list reversal step to restore the required horizontal order, minimizing space down to O(1) auxiliary dimensions.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Collections;

class Solution {
    public static ArrayList<Integer> leaders(int[] arr) {
        ArrayList<Integer> result = new ArrayList<>();
        
        // Guard Condition: Handle degenerate null or empty arrays cleanly
        if (arr == null || arr.length == 0) {
            return result;
        }

        int n = arr.length;

        // Step 1: The rightmost element is always an array leader implicitly
        int maxFromRight = arr[n - 1];
        result.add(maxFromRight);

        // Step 2: Traverse the array backward from right to left [n-2 down to 0]
        for (int i = n - 2; i >= 0; i--) {
            // If the active element is greater than or equal to the max suffix seen so far
            if (arr[i] >= maxFromRight) {
                maxFromRight = arr[i]; // Update the suffix maximum tracker
                result.add(arr[i]);    // Record the newly discovered leader
            }
        }

        // Step 3: Reverse the collected results list to restore original chronological order
        Collections.reverse(result);

        return result;
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
*Explanation:* The backward scanning loop processes each element exactly once, taking O(N) steps. Reversing the results collection list at the end takes linear time proportional to the number of leaders discovered (at most O(N)). Combining these consecutive phases keeps total execution runtime strictly linear relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the memory allocations required to store and return the final leaders list (which is an inherent output requirement), the algorithm evaluates suffix properties entirely in-place on the fly using a single primitive variable, preserving a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing suffix metrics, Monotonic sequences, and stream boundaries.

✅ **The Reverse Traversal Strategy:** When an array problem states that an operation on element `i` depends entirely on checking all items to its **right** side (suffix), look to invert your loop track to scan **from right to left**. Building up a running suffix maximum or prefix tracking state transforms an O(N²) quadratic comparison trap into a clean O(N) single-pass linear sweep.

✅ Directly drives real-world microarchitectures like **stock market breakout signal alerts** (identifying trading days where prices closed higher than all subsequent trading days within a temporal frame), graphics visibility culling passes, network traffic peaks analyzers, and streaming metric monitors.

### Similar Problems

- Find the Largest Element in an Array ([GeeksforGeeks](https://geeksforgeeks.com))
- Next Greater Element I ([LeetCode 496](https://leetcode.com))
- Daily Temperatures ([LeetCode 739](https://leetcode.com))
- Product of Array Except Self ([LeetCode 238](https://leetcode.com))
