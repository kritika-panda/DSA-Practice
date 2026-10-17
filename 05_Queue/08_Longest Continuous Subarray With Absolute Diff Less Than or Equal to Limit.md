# Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

**LeetCode 1438 - Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit**

## Problem Statement

Given an array of integers `nums` and an integer `limit`, return the size of the longest **non-empty** subarray such that the absolute difference between any two elements of this subarray is less than or equal to `limit`.

---

## Example

```java
int[] nums = {8, 2, 4, 7};
int limit = 4;

Solution sol = new Solution();
sol.longestSubarray(nums, limit); // Returns 2 
```

**Explanation:** 
- `[8]` with maximum absolute diff \(\vert{}8-8\vert{} = 0 \le 4\).
- `[4, 7]` with maximum absolute diff \(\vert{}4-7\vert{} = 3 \le 4\).
The longest valid subarray is `[4, 7]` (or `[2, 4]`), which has a length of 2.

---

# Intuition

The problem asks for the longest contiguous subarray where the condition holds true. This structural combination of tracking a contiguous range while maximizing size suggests a **Sliding Window** approach.

For any window `[left, right]`, the absolute difference between *any* two elements is simply the difference between the **maximum element** and the **minimum element** currently inside that window. If `max_val - min_val <= limit`, the window is valid, and we can expand it by moving `right` forward. If `max_val - min_val > limit`, the window becomes invalid, and we must shrink it by moving `left` forward until it becomes valid again.

To do this efficiently, we need a way to track the minimum and maximum values of a dynamically shifting window in better than \(O(N)\) linear time.

---

# Approach 1: Sliding Window + Monotonic Deques (Optimal)

## Data Structure

```java
Deque<Integer> maxDeque
Deque<Integer> minDeque
```

We use two double-ended queues (Deques) to maintain the indices (or values) of elements in the current window:
- `maxDeque`: Stores values in a strictly **decreasing** order (the maximum element is always at the front).
- `minDeque`: Stores values in a strictly **increasing** order (the minimum element is always at the front).

---

## Algorithm Steps

1. Expand the window by iterating `right` from `0` to `N - 1`.
2. For each element `nums[right]`, maintain the monotonic property of both deques by popping elements from the back that break the order, then append `nums[right]`.
3. Check if the condition is broken: `maxDeque.peekFirst() - minDeque.peekFirst() > limit`.
4. If it is broken, we shrink the window from the left:
   - If the element leaving the window (`nums[left]`) matches the front of either deque, poll it out.
   - Increment `left`.
5. Update the maximum window length found so far: `maxLength = Math.max(maxLength, right - left + 1)`.

---

# Dry Run

### Parameters
```java
nums =, limit = 4
```

### Execution Trace

| right | nums[right] | maxDeque (Front -> Back) | minDeque (Front -> Back) | Condition Checked | Action / Window | maxLength |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `0` | `8` | `[8]` | `[8]` | \(8 - 8 = 0 \le 4\) | Valid window `[8]` | `1` |
| `1` | `2` | `[8, 2]` | `[2]` | \(8 - 2 = 6 > 4\) | Invalid! Slide `left` past `8`. `left` becomes `1`. | `1` |
| `2` | `4` | `[4]` | `[2, 4]` | \(4 - 2 = 2 \le 4\) | Valid window `[2, 4]` | `2` |
| `3` | `7` | `[7]` | `[2, 4, 7]` | \(7 - 2 = 5 > 4\) | Invalid! Slide `left` past `2`. `left` becomes `2`. | `2` |

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int longestSubarray(int[] nums, int limit) {
        Deque<Integer> maxDeque = new LinkedList<>();
        Deque<Integer> minDeque = new LinkedList<>();
        
        int left = 0;
        int maxLength = 0;
        
        for (int right = 0; right < nums.length; right++) {
            // Maintain monotonic decreasing order for maxDeque
            while (!maxDeque.isEmpty() && maxDeque.peekLast() < nums[right]) {
                maxDeque.pollLast();
            }
            maxDeque.addLast(nums[right]);
            
            // Maintain monotonic increasing order for minDeque
            while (!minDeque.isEmpty() && minDeque.peekLast() > nums[right]) {
                minDeque.pollLast();
            }
            minDeque.addLast(nums[right]);
            
            // If window condition is broken, shrink it from the left
            while (maxDeque.peekFirst() - minDeque.peekFirst() > limit) {
                if (maxDeque.peekFirst() == nums[left]) {
                    maxDeque.pollFirst();
                }
                if (minDeque.peekFirst() == nums[left]) {
                    minDeque.pollFirst();
                }
                left++;
            }
            
            maxLength = Math.max(maxLength, right - left + 1);
        }
        
        return maxLength;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
Even though there are nested loops, every element in the array is added to and removed from each deque at most once. The `left` pointer moves from left to right at most `N` times across the entire runtime.

### Space Complexity

```java
O(N)
```
In the worst-case scenario, the deques can grow linearly to store all elements if the input array is strictly sorted.

---

# Approach 2: Sliding Window + TreeMap

## Observation

If we want a simpler implementation without tracking monotonic properties manually, we can swap the Deques out for a `TreeMap<Integer, Integer>`. The `TreeMap` acts as a self-balancing binary search tree that tracks elements currently inside the window alongside their occurrences (frequencies).

---

## Java Solution (Alternative)

```java
import java.util.*;

class SolutionTreeMap {
    public int longestSubarray(int[] nums, int limit) {
        TreeMap<Integer, Integer> windowMap = new TreeMap<>();
        int left = 0;
        int maxLength = 0;
        
        for (int right = 0; right < nums.length; right++) {
            windowMap.put(nums[right], windowMap.getOrDefault(nums[right], 0) + 1);
            
            // Check boundaries using firstKey() and lastKey()
            while (windowMap.lastKey() - windowMap.firstKey() > limit) {
                windowMap.put(nums[left], windowMap.get(nums[left]) - 1);
                if (windowMap.get(nums[left]) == 0) {
                    windowMap.remove(nums[left]);
                }
                left++;
            }
            
            maxLength = Math.max(maxLength, right - left + 1);
        }
        
        return maxLength;
    }
}
```

---

# Complexity Analysis (TreeMap Approach)

### Time Complexity

```java
O(N log N)
```
Each insertion, deletion, and boundary check (`firstKey`/`lastKey`) inside a `TreeMap` of size \(N\) takes \(O(\log N)\) time.

### Space Complexity

```java
O(N)
```
To store the elements and frequencies inside the active map boundaries.

---

# Comparison

| Strategy | Time Complexity | Space Complexity | Implementation Complexity | Worst-Case Performance |
|---|---|---|---|---|
| **Monotonic Deques** | $O(N)$ | $O(N)$ | High (Requires strict tuning) | Highly Optimal |
| **TreeMap** | $O(N \log N)$ | $O(N)$ | Low & Predictable | Slower due to Tree Overhead |

---

# Key Takeaways

- To track the dynamic bounds of a sliding array window without clearing it repeatedly, check the edges using **Monotonic Deques**.
- Elements that cannot become the new maximum or minimum within the lifespan of the current window are discarded early from the back of the deques.
- For a quick, readable alternative during quick prototyping interviews, look to **TreeMap tracking frequencies**.
- Core concepts:
  - Sliding Window
  - Monotonic Deque
  - Balanced Tree Structures (TreeMap)
- Expected interview preference: **Monotonic Deques Approach** due to its optimal linear $O(N)$ runtime.
