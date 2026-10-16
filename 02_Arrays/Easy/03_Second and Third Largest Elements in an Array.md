# Second and Third Largest Elements in an Array

## Problem Statement

Given an integer array `arr`, find and return both the **second largest** and the **third largest** distinct elements present inside the array. If a unique second or third largest element does not exist (due to insufficient distinct elements), return a baseline fallback boundary marker (like **`-1`** or **`Integer.MIN_VALUE`**).

---

## Examples

### Example 1

```text
Input: arr = [12, 35, 1, 10, 34, 1]
Output: Second Largest = 34, Third Largest = 12
```

### Explanation

```text
The unique sorted elements in descending order are: 35 > 34 > 12 > 10 > 1.
The second largest distinct element is 34, and the third largest distinct element is 12.
```

---

### Example 2

```text
Input: arr = [10, 10, 10]
Output: Second Largest = -1, Third Largest = -1
```

### Explanation

```text
There is only 1 distinct element (10). Runner-up positions do not exist, returning -1.
```

---

# Intuition

A naive approach would sort the array in descending order or collect unique items into a balanced tree set. However, sorting takes an expensive **\(O(N \log N)\)** time footprint and sets consume extra heap memory.

To solve this in a **single linear pass with O(1) constant auxiliary space**, we maintain three independent tracking registers (`first`, `second`, `third`) initialized to the lowest possible integer value. As we scan through each element `num` in the array, we bubble values down the rank matrix dynamically:
1. **New Maximum Located (`num > first`):** The old first cascades to second, the old second cascades to third, and `first` absorbs `num`.
2. **New Runner-Up Located (`first > num > second`):** The old second cascades to third, and `second` absorbs `num`.
3. **New Second Runner-Up Located (`second > num > third`):** `third` absorbs `num`.
4. **Duplicate Safeguard Check:** If `num == first`, `num == second`, or `num == third`, we skip the calculation entirely to ensure our registers track strictly **distinct (unique)** rank values.

---

# State Definition

The multi-register linear scanning pipeline tracks the unique largest hierarchies using three primitive registers:

```java
long first = Long.MIN_VALUE;  // Global distinct maximum tracker register
long second = Long.MIN_VALUE; // Global distinct second maximum tracker register
long third = Long.MIN_VALUE;  // Global distinct third maximum tracker register
```

---

# Recurrence / Cascading Rank Conditions

```text
For each num in arr:
    If num == first OR num == second OR num == third -> Continue (Skip duplicates)
    
    If num > first:
        third = second
        second = first
        first = num
    Else If num > second:
        third = second
        second = num
    Else If num > third:
        third = num
```

---

# Optimal Single-Pass Cascading Solution

## Idea

We initialize three tracking variables to 64-bit minimum bounds to handle edge cases where the input array contains standard `Integer.MIN_VALUE` literals. We run a single sequential pass, shifting values down the hierarchy chain cleanly.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    // Structural container block to return dual output parameters cleanly
    public static class RankResult {
        int secondLargest;
        int thirdLargest;

        RankResult(int secondLargest, int thirdLargest) {
            this.secondLargest = secondLargest;
            this.thirdLargest = thirdLargest;
        }

        @Override
        public String toString() {
            return "Second Largest = " + secondLargest + ", Third Largest = " + thirdLargest;
        }
    }

    public static RankResult findRankedElements(int[] arr) {
        if (arr == null || arr.length < 2) {
            return new RankResult(-1, -1);
        }

        // Use 64-bit long limits to comfortably shield against Integer.MIN_VALUE data flags
        long first = Long.MIN_VALUE;
        long second = Long.MIN_VALUE;
        long third = Long.MIN_VALUE;

        // Core Step: Single forward pass hierarchy cascade
        for (int num : arr) {
            // Distinct Filter Guard: Explicitly skip duplicates to isolate strictly unique ranks
            if (num == first || num == second || num == third) {
                continue;
            }

            if (num > first) {
                third = second;   // Old second falls to third position
                second = first;   // Old first falls to second position
                first = num;      // New absolute maximum anchored
            } else if (num > second) {
                third = second;   // Old second falls to third position
                second = num;     // New unique second anchored
            } else if (num > third) {
                third = num;      // New unique third anchored
            }
        }

        // Map long register placeholders back to problem specification requirements
        int finalSecond = (second == Long.MIN_VALUE) ? -1 : (int) second;
        int finalThird = (third == Long.MIN_VALUE) ? -1 : (int) third;

        return new RankResult(finalSecond, finalThird);
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
*Explanation:* The pointer sweeps through each item of the array exactly once. At each element, it performs at most a fixed set of three primitive comparison statements, locking total execution runtime strictly linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The state mutations and value cascading loops run entirely inside local primitive tracking registers, completely avoiding any helper sorting structures, queues, or arrays, preserving a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing multi-state stream tracks and stream hierarchy reductions.

✅ **The Cascading Drop Effect:** When maintaining multiple sequential max tracking variables, remember that updating a higher tier variable **always requires cascading the older historical value down to the next runner-up position** (`third = second; second = first;`). Omitting the cascade will discard the historical runner-up values, corrupting the solution.

✅ Directly drives real-world microarchitectures like **leaderboard ranking display engines** tracking top distinct player scoring tiers, network packet performance tracking, database telemetry sorting modules, and real-time streaming data analytics.

### Similar Problems

- Find the Largest Element in an Array ([GeeksforGeeks](https://geeksforgeeks.com))
- Third Maximum Number ([LeetCode 414](https://leetcode.com))
- Kth Largest Element in an Array ([LeetCode 215](https://leetcode.com))
- Top K Frequent Elements ([LeetCode 347](https://leetcode.com))
