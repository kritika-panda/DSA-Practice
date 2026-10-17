# Majority Element ([LeetCode 169](https://leetcode.com))

## Problem Statement

Given an array `nums` of size `n`, return *the **majority element***.

The majority element is the element that appears **more than \(\lfloor n / 2 \rfloor\)** times. You may assume that the majority element always exists in the array.

**Constraints & Performance Targets:**
- Time Complexity: strictly bounded by **O(N)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: nums = [3,2,3]
Output: 3
```

---

### Example 2

```text
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

---

# Intuition

A straightforward approach would use a `HashMap` to count the frequencies of all elements, then scan the map for the entry exceeding n / 2. However, managing a hash directory introduces an O(N) extra memory penalty on the heap. Another option is sorting the array, where the majority element is guaranteed to land at index \(\lfloor n / 2 \rfloor\) because it spans over half the array. However, sorting takes an expensive \(O(N \log N)\) time footprint.

To fulfill both strict targets—**O(N) time and O(1) space**—we can deploy **Boyer-Moore's Voting Algorithm**:
- Think of the array as a political voting process where the majority element is a dominant party, and all other unique elements represent rival parties.
- We maintain a running `candidate` variable and a scalar balance `count` tracker.
- As we loop through the array, we process votes sequentially:
  1. If `count == 0`, it means all previous votes have cancelled each other out completely. We immediately anchor the active element as our new `candidate`.
  2. If the current element matches `candidate`, it belongs to the same party, so we increment our balance: `count++`.
  3. If it does not match, it represents a rival vote, so we decrement our balance: `count--`.
- Because the true majority element appears strictly more than half the time, it will mathematically survive all rival cancellations, remaining anchored inside our `candidate` variable at the terminal tail boundary of the pass.

---

# State Definition

The voting allocation pipeline tracks the dominant entity states using two scalar registers:

```java
int candidate = 0; // Register caching the active majority element candidate
int count = 0;     // Balance register tracking active vote weights
```

---

# Recurrence / Structural Loop Condition

```text
For each num in nums:
    If count == 0 -> candidate = num
    
    If num == candidate -> count++
    Else -> count--
Return candidate
```

---

# Optimal Single-Pass Boyer-Moore Voting Solution

## Idea

We configure a single sequential loop. When our vote balance tracker drops to zero, we swap our candidate focus on the fly. By incrementing or decrementing the balance register based on match conditions, we isolate the majority element cleanly inside a single pass.

---

## Java Code

```java
class Solution {
    public int majorityElement(int[] nums) {
        // Guard Condition: Trivial datasets return their baseline elements directly
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int candidate = 0;
        int count = 0;

        // Core Step: Single forward pass Boyer-Moore voting sweep
        for (int num : nums) {
            // If the balance register hits zero, anchor a new candidate focus
            if (count == 0) {
                candidate = num;
            }

            // Accumulate vote weights or execute rival path cancellations
            if (num == candidate) {
                count++; // Vote matches candidate party -> increment weight
            } else {
                count--; // Rival vote encountered -> execute cancellation
            }
        }

        // Since the problem statement guarantees the majority element always exists, 
        // the remaining candidate is mathematically guaranteed to be the correct answer.
        return candidate;
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
*Explanation:* The tracking pointer sweeps sequentially across the N elements exactly once. Inside the block, it performs simple constant-time O(1) scalar equality checks and integer arithmetic operations, bounding execution runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The algorithm coordinates streaming metrics completely in-place directly on the array values using only two local integer registers, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Core baseline template for stream consensus tracking, frequency balancing, and streaming data compression reduction checks.

✅ **The Cancellation Invariant:** Boyer-Moore works because any majority element that appears strictly more than half the time (\(> n/2\)) will always have more votes than all other elements combined. Even in the absolute worst-case distribution where all rival numbers team up to cancel out the majority element sequentially, the majority element will still hold at least one remaining vote at the end.

✅ Directly drives real-world microarchitectures like **fault-tolerant distributed consensus layers** (e.g., voting filters across redundant system nodes to isolate a majority verified outcome), network data packet stream routers, database stream aggregators, and data signal filters.

### Similar Problems

- Majority Element II (Finding elements appearing $> n/3$ times - [LeetCode 229](https://leetcode.com))
- Check If a Number Is Majority Element in a Sorted Array ([LeetCode 1150](https://leetcode.com))
- Single Number ([LeetCode 136](https://leetcode.com))
- Find the Duplicate Number ([LeetCode 287](https://leetcode.com))
