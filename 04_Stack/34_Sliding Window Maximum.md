# Sliding Window Maximum (LeetCode 239)

## Problem Statement

You are given an array of integers `nums`, there is a sliding window of size `k` which is moving from the very left of the array to the very right. You can only see the `k` numbers in the window. Each time the sliding window moves right by one position.

Return *the max sliding window*.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the nums array)*

---

## Examples

### Example 1

```java
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

### Explanation

```text
Window position                Max
------------------------       ---
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

---

### Example 2

```java
Input: nums =, k = 1
Output: [1]
```

---

# Intuition

A naive approach would evaluate the maximum element inside each window by scanning all k elements, costing an inefficient \(O(N \times k)\) runtime. 

To solve this problem in optimal **linear time**, we use a **Monotonic Decreasing Deque (Double-Ended Queue)** that stores indices:
- We want the elements tracked by our deque to be sorted in a strictly decreasing order from front to back. This ensures that the absolute maximum element of the active window always sits securely right at the **front** of the deque.
- As the window slides to index `i`, we do two things:
  1. **Evict Out-of-Bounds Indices:** We check if the index sitting at the front of the deque has fallen out of the active window bounds (`deque.peekFirst() <= i - k`). If it has, we pop it off from the front.
  2. **Prune Smaller Candidates:** Any element inside the deque that is **less than or equal to** the incoming element `nums[i]` is useless because `nums[i]` is both larger and will stay in the window longer. We greedily pop these elements from the **back** of the deque.
- After maintaining these properties, the front of the deque holds the index of the maximum element for the current window.

---

# State Definition

The queue tracking framework relies on a double-ended queue storing array indices:

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] result = new int[n - k + 1];
        Deque<Integer> deque = new ArrayDeque<>(); // Double-ended queue tracking indices monotonic-decreasingly
    }
}
```

---

# Recurrence / Stack Contract Condition

```java
for (int i = 0; i < nums.length; i++) {
    // 1. Remove indices that have slid out of the active window boundary
    if (!deque.isEmpty() && deque.peekFirst() <= i - k) {
        deque.pollFirst();
    }
    // 2. Maintain monotonic decreasing order: pop smaller values from the back
    while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
        deque.pollLast();
    }
    // 3. Insert current element index
    deque.offerLast(i);
    // 4. If window has reached size k, record the maximum value from the front
    if (i >= k - 1) {
        result[i - k + 1] = nums[deque.peekFirst()];
    }
}
```

---

# Optimal Monotonic Deque Solution

## Idea

By utilizing a double-ended queue to maintain a sliding snapshot of array indices sorted decreasingly, elements that lose future utility are pruned from both ends in constant time. This bypasses complete window re-scans, completing the layout in a single linear **O(N)** pass.

---

## Java Code

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k <= 0) {
            return new int[0];
        }

        int n = nums.length;
        int[] result = new int[n - k + 1];
        // Deque stores array indices in a strictly monotonic decreasing value order
        Deque<Integer> deque = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            // Step 1: Evict the front index if it has fallen out of the active sliding window
            if (!deque.isEmpty() && deque.peekFirst() <= i - k) {
                deque.pollFirst();
            }

            // Step 2: Prune smaller elements from the back because they cannot be the maximum
            while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
                deque.pollLast();
            }

            // Step 3: Append the current element's index onto the back of the deque
            deque.offerLast(i);

            // Step 4: Once the window has hit size k, capture the maximum element from the front
            if (i >= k - 1) {
                result[i - k + 1] = nums[deque.peekFirst()];
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array of length N is scanned exactly once. Each index is appended to and removed from the deque at most once across the entire runtime pass. The amortized cost per element is O(1), locking total time complexity strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(k)
```
*Explanation:* The auxiliary memory utilized by the deque never overshoots the window capacity constraint, storing at most k indices concurrently.

---

# Key Takeaways

✅ High-tier fundamental template for high-performance streaming sliding window metrics.

✅ **Why Store Indices Instead of Values?** Storing indices inside the deque instead of direct numbers is critical because indices carry dual structural meaning—they allow us to query the actual values (`nums[index]`) while providing an instant way to check if an element has fallen out of bounds (`index <= i - k`).

✅ Universally used inside **network rate-limiting throttlers** (e.g., monitoring maximum packet traffic drops inside sliding time windows), financial telemetry streaming engines, and analytics streaming layers.

✅ Common Architectural Paradigm:

```text
Sliding Boundary Index Pruning via Amortized Deque Reductions
```

### Similar Problems

- Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit (LeetCode 1438)
- Sliding Window Median (LeetCode 480)
- Shortest Subarray with Sum at Least K (LeetCode 862)
- Next Greater Element I (LeetCode 496)
