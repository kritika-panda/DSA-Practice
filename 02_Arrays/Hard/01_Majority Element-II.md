# Majority Element II ([LeetCode 229](https://leetcode.com))

## Problem Statement

Given an integer array `nums` of size `n`, find all elements that appear **more than \(\lfloor n / 3 \rfloor\)** times.

**Constraints & Performance Targets:**
- Time Complexity: strictly bounded by **O(N)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: nums = [3,2,3]
Output: [3]
```

---

### Example 2

```text
Input: nums = [1]
Output: [1]
```

---

### Example 3

```text
Input: nums = [1,2]
Output: [1,2]
```

---

# Intuition

A simple approach would use a `HashMap` to record frequencies, but this violates the O(1) space constraint. Sorting the array takes \(O(N \log N)\) time, which violates our linear time objective.

To hit both performance targets—**O(N) time and O(1) space**—we deploy the **Boyer-Moore Majority Vote Algorithm generalized for K elements**:
- Mathematically, an array can contain **at most two** elements that appear strictly more than \(\lfloor n / 3 \rfloor\) times. Why? If there were three such elements, each appearing more than n/3 times, their combined frequency would exceed 3 × (n/3) = n, which is impossible.
- Therefore, we maintain **two independent candidate registers** (`candidate1`, `candidate2`) and their respective balance tracking weight registers (`count1`, `count2`).
- As we loop through the array, we process votes sequentially across three logical choice checks:
  1. If the active element matches an existing candidate, we increment its balance weight (`count1++` or `count2++`).
  2. If a tracking weight register drops to zero, the candidate party has been neutralized. We immediately anchor the active element as that register's new focus.
  3. If the element matches neither candidate and both counts are non-zero, it acts as a rival vote that **cancels out one vote from both candidates simultaneously** (`count1--`, `count2--`).
- **The Validation Verification Step:** Unlike *Majority Element I*, where a majority candidate (> n/2) is guaranteed to win if it exists, a > n/3 threshold means candidates can survive the cancellation phase without actually clearing the target quota (e.g., in `[1, 2, 3, 4]`, 3 and 4 could survive but appear only once). Thus, we must perform a **second linear validation pass** to count the exact frequencies of our two survivors, adding them to our output only if they cross the strict \(\lfloor n / 3 \rfloor\) boundary.

---

# State Definition

The multi-candidate consensus pipeline tracks potential dominant elements using four primitive registers:

```java
int candidate1 = 0, candidate2 = 0; // Registers caching the active top two majority contenders
int count1 = 0, count2 = 0;         // Balance registers tracking vote weights
```

---

# Recurrence / Structural Loop Condition

```text
Phase 1 (Voting):
For each num in nums:
    If num == candidate1 -> count1++
    Else If num == candidate2 -> count2++
    Else If count1 == 0 -> candidate1 = num, count1 = 1
    Else If count2 == 0 -> candidate2 = num, count2 = 1
    Else -> count1--, count2--

Phase 2 (Validation):
Reset counters to 0. Recount occurrences of candidate1 and candidate2.
Append candidates exceeding n / 3 to results.
```

---

# Optimal Single-Pass Boyer-Moore Dual Voting Solution

## Idea

The implementation pairs a dual-candidate tracking system loop with an immediate secondary validation verification pass. By using inline condition branching instead of collections, it drops memory allocation overhead to absolute zero.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> result = new ArrayList<>();
        
        // Guard Condition: Handle empty or null arrays cleanly
        if (nums == null || nums.length == 0) {
            return result;
        }

        // Initialize our dual candidates and balance weight tracking registers
        int candidate1 = 0, candidate2 = 1; // Seed with distinct initial footprints
        int count1 = 0, count2 = 0;

        // Phase 1: Run the generalized Boyer-Moore dual voting cancellation sweep
        for (int num : nums) {
            if (num == candidate1) {
                count1++; // Vote matches candidate 1 -> extend weight
            } else if (num == candidate2) {
                count2++; // Vote matches candidate 2 -> extend weight
            } else if (count1 == 0) {
                candidate1 = num; // Candidate 1 neutralized -> anchor new focus
                count1 = 1;
            } else if (count2 == 0) {
                candidate2 = num; // Candidate 2 neutralized -> anchor new focus
                count2 = 1;
            } else {
                // Rival vote encountered -> execute a simultaneous double cancellation step
                count1--;
                count2--;
            }
        }

        // Phase 2: Mandatory validation verification pass
        count1 = 0;
        count2 = 0;
        for (int num : nums) {
            if (num == candidate1) {
                count1++;
            } else if (num == candidate2) {
                count2++;
            }
        }

        int targetQuota = nums.length / 3;
        if (count1 > targetQuota) {
            result.add(candidate1);
        }
        if (count2 > targetQuota) {
            result.add(candidate2);
        }

        return result; // Return the verified collection of elements appearing > n/3 times
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
*Explanation:* Phase 1 performs a single linear sweep across the array elements to identify potential candidates. Phase 2 performs a second linear validation scan to count the exact frequencies of the two survivors. Running these consecutive un-nested loops keeps total execution runtime strictly linear relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the memory blocks needed to collect and return the final results list (which is an inherent problem criteria requirement), the algorithm coordinates tracking completely inside a few primitive local integer registers, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Advanced core template for multi-candidate stream consensus tracking, frequency balancing, and real-time analytics reduction.

✅ **The Order of Voting Operations Rule:** When implementing a multi-candidate Boyer-Moore vote, your candidate matching checks (`num == candidate1` and `num == candidate2`) **must execute before** your zero-weight assignment checks (`count1 == 0` or `count2 == 0`). If you swap this hierarchy, when a count drops to zero, the active candidate can accidentally clone into both registers simultaneously, corrupting the tracking logic completely.

✅ Directly drives real-world microarchitectures like **distributed system consensus modules** (e.g., verifying multi-party node agreement weights where multiple top tiers are monitored), telemetry threshold filters, load balancers tracking dominant stream clusters, and voting systems.

### Similar Problems

- Majority Element ([LeetCode 169](https://leetcode.com))
- Check If a Number Is Majority Element in a Sorted Array ([LeetCode 1150](https://leetcode.com))
- Top K Frequent Elements ([LeetCode 347](https://leetcode.com))
- Find the Duplicate Number ([LeetCode 287](https://leetcode.com))
