# Max Consecutive Ones ([LeetCode 485](https://leetcode.com))

## Problem Statement

Given a binary array `nums`, return *the **maximum number of consecutive `1`s** in the array*.

---

## Examples

### Example 1

```text
Input: nums = [1,1,0,1,1,1]
Output: 3
```

### Explanation

```text
The first two digits are consecutive 1s. The last three digits are consecutive 1s.
The maximum number of consecutive 1s is 3.
```

---

### Example 2

```text
Input: nums = [1,0,1,1,0,1]
Output: 2
```

---

# Intuition

To find the longest streak of consecutive `1`s, we can perform a **single linear pass** while managing a resetting streak counter.

As we traverse the array from left to right:
1. **Encounter a `1`**: We increment our dynamic `currentCount` streak counter by 1.
2. **Encounter a `0`**: The consecutive sequence is broken. We compare `currentCount` with our global maximum tracker `maxCount` to preserve the highest score found so far (`maxCount = Math.max(maxCount, currentCount)`), and then **reset `currentCount` to 0**.
3. **Loop Termination**: Since the array might end on a long sequence of `1`s without hitting a trailing `0` to trigger a reset check, we must execute one final maximum comparison check right after the loop finishes.

---

# State Definition

The linear sliding scanner tracks continuous element states using two scalar counters:

```java
int maxCount = 0;     // Global tracker preserving the longest continuous streak achieved
int currentCount = 0; // Dynamic local counter tracking the active running sequence of 1s
```

---

# Recurrence / Structural Loop Condition

```text
For each num in nums:
    If num == 1:
        currentCount++
    Else:
        maxCount = max(maxCount, currentCount)
        currentCount = 0
Return max(maxCount, currentCount)
```

---

# Optimal Single-Pass Sequential Solution

## Idea

We configure a standard `for-each` loop to process the binary array. By running inline updates to our global maximum tracker inside the loop, we eliminate the need for a separate post-loop comparison block, resolving the streak safely inside a single pass.

---

## Java Code

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        // Guard Condition: An empty array contains exactly zero consecutive 1s
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int maxCount = 0;
        int currentCount = 0;

        // Core Step: Single forward scan over the data stream
        for (int num : nums) {
            if (num == 1) {
                currentCount++; // Continuous streak extends, increment local counter
                
                // Real-time synchronization prevents the terminal array trailing 1s edge case
                if (currentCount > maxCount) {
                    maxCount = currentCount;
                }
            } else {
                // Streak broken by a 0 -> flush and reset the active tracking accumulator
                currentCount = 0;
            }
        }

        return maxCount; // Return the absolute highest continuous sequence count achieved
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
*Explanation:* The tracking pointer sweeps sequentially across the N elements exactly once. Inside the loop, it processes basic constant-time O(1) integer comparisons and addition checks, bounding the overall execution timeline strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The calculations are evaluated completely in-place directly on the array records, using only two local primitive integer variable registers, preserving a completely flat constant space footprint.

---

# Key Takeaways

✅ Core baseline sliding window template for tracking active continuous sequences and contiguous streams.

✅ **The Trailing Element Edge Case:** A common trap when coding resetting counters is forgetting to handle arrays that end on an active target element (e.g., `[1, 0, 1, 1]`). If maximum checks are *only* executed when a `0` breaks the loop, trailing streaks are discarded. Updating `maxCount` in real-time inside the `num == 1` branch handles this flawlessly.

✅ Directly drives real-world microarchitectures like **network connection signal up-time logging blocks** (measuring the longest continuous millisecond window of high signal states), runs of consecutive successful transaction pulses, and run-length bitwise compression engines (RLE).

### Similar Problems

- Max Consecutive Ones III ([LeetCode 1004](https://leetcode.com))
- Consecutive Characters ([LeetCode 1446](https://leetcode.com))
- Longest Consecutive Sequence ([LeetCode 128](https://leetcode.com))
- Minimum Swaps to Group All 1's Together ([LeetCode 1151](https://leetcode.com))
