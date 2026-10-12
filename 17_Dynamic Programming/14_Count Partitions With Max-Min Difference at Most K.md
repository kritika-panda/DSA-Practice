# Count Partitions With Max-Min Difference at Most K

## Problem Statement

Given a 0-indexed integer array `nums` and an integer `k`, return *the number of ways to partition the array into **contiguous sub-arrays** such that for each sub-array, the difference between its maximum element and its minimum element is **at most** `k`*.

Since the answer can be very large, return it **modulo** 10⁹ + 7.

A **partition** of an array means splitting the array into contiguous segments such that every element belongs to exactly one segment.

The overall runtime complexity must be:

```text
O(N log N) or O(N)
```

*(where N is the total number of elements inside the nums array)*

---

## Example 1

### Input

```text
nums = [1, 3, 5, 2]
k = 2
```

### Output

```text
3
```

### Explanation

```text
The valid contiguous partitions are:
1., [3], [5], [2] -> max-min for each is 0 <= 2 (Valid!)
2., [5], [2] -> [1, 3] diff is 2 <= 2; others are 0 <= 2 (Valid!)
3., [3, 5], [2] -> [3, 5] diff is 2 <= 2; others are 0 <= 2 (Valid!)

Note: Including [1, 3, 5] is invalid because max-min is 5 - 1 = 4 > 2.
Total valid partitions: 3
```

---

## Example 2

### Input

```text
nums = [1, 2, 10, 5, 6]
k = 5
```

### Output

```text
1
```

### Explanation

```text
The only valid partition is, [10], [5, 6]. 
Any segment grouping 10 with 1 or 2 creates a difference > 5.
```

---

# Intuition

At any given element `i`, we want to find out how many valid partitions can end at this position. A partition ending at index `i` is formed by taking a valid contiguous trailing sub-array `nums[j...i]` and appending it to an existing valid partition that ended at index `j - 1`.

For `nums[j...i]` to be a valid segment, its condition is:
```text
Max(nums[j...i]) - Min(nums[j...i]) <= k
```

If we find the leftmost index `L[i]` such that for all `j` from `L[i]` to `i`, the sub-array `nums[j...i]` is valid, then the total unique ways to partition up to index `i` is the sum of all valid partitions ending before these valid segments:

```text
dp[i] = Sum( dp[j - 1] ) for all L[i] <= j <= i
```

Since the constraint \(\max - \min \le k\) is monotonic with respect to a fixed right endpoint (as you expand the segment to the left, the maximum can only increase and the minimum can only decrease, making the difference expand), we can maintain the valid left-boundary `L[i]` efficiently using a sliding window with **monotonic deques**. A running prefix sum can then optimize the range sum query from O(N) down to O(1).

---

# State Definition

Let:

```text
dp[i]
=
Total number of valid partitions for the prefix 
sub-array ending exactly at index i
```

---

# Recurrence

```text
dp[i] = Sum( dp[j - 1] )  for all L[i] <= j <= i
```

Using a prefix sum array `pref` where `pref[x] = dp[0] + dp[1] + ... + dp[x-1]`, this transforms to:
```text
dp[i] = pref[i] - pref[L[i]]
```
*(with appropriate modulo arithmetic handling)*

---

# Recursive Solution with Caching (Memoization)

## Base Cases

### Reached Starting Anchor Boundary

```text
index < 0

return 1
```

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    private final int MOD = 1000000007;

    private int solve(int i, int[] nums, int k, int[] dp) {
        if (i < 0) {
            return 1;
        }

        if (dp[i] != -1) {
            return dp[i];
        }

        long ways = 0;
        int maxVal = nums[i];
        int minVal = nums[i];

        // Track backwards to explore all valid segment cuts
        for (int j = i; j >= 0; j--) {
            maxVal = Math.max(maxVal, nums[j]);
            minVal = Math.min(minVal, nums[j]);

            if (maxVal - minVal <= k) {
                ways = (ways + solve(j - 1, nums, k, dp)) % MOD;
            } else {
                break; // Monotonic block breach: further left extensions will only expand difference
            }
        }

        return dp[i] = (int) ways;
    }

    public int countPartitions(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, -1);
        return solve(n - 1, nums, k, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²) -> Due to worst-case backtracking sweeps on flat/uniform arrays
Space Complexity: O(N) recursion stack memory frames
```

---

# Optimal Tabulation Solution (Sliding Window + Monotonic Deque)

## Idea

To solve this in strict O(N) linear time, we optimize the search for the left boundary `L[i]` and the range sum tracking.
1. **Monotonic Deques:** We maintain two deques—`maxDeque` (descending) and `minDeque` (ascending)—to track the indices of the maximum and minimum elements in our active sliding window.
2. **Left Pointer Contraction:** As we expand the right pointer `i`, if `nums[maxDeque.peekFirst()] - nums[minDeque.peekFirst()] > k`, the window is invalid. We advance our left boundary pointer `L` forward and pop out-of-bounds indices from the deques until the window constraint becomes valid again.
3. **Prefix Sum Accumulator:** Instead of looping from `L` to `i` to sum up values, we maintain a running prefix sum of the `dp` array, enabling O(1) interval evaluations.

---

## Java Code

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int countPartitions(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int n = nums.length;
        int MOD = 1000000007;

        // dp[i] stores valid partitions for prefix ending at index i-1 (1-indexed for offset safety)
        int[] dp = new int[n + 1];
        int[] pref = new int[n + 2];

        // Base case initialization
        dp[0] = 1; 
        pref[1] = 1; // pref[x] tracks sum of dp[0...x-1]

        Deque<Integer> maxDeque = new ArrayDeque<>();
        Deque<Integer> minDeque = new ArrayDeque<>();

        int left = 0; // Left boundary index pointer

        for (int i = 0; i < n; i++) {
            // Maintain max deque (descending)
            while (!maxDeque.isEmpty() && nums[maxDeque.peekLast()] <= nums[i]) {
                maxDeque.pollLast();
            }
            maxDeque.offerLast(i);

            // Maintain min deque (ascending)
            while (!minDeque.isEmpty() && nums[minDeque.peekLast()] >= nums[i]) {
                minDeque.pollLast();
            }
            minDeque.offerLast(i);

            // Slide left boundary forward if max-min difference breaches constraint k
            while (!maxDeque.isEmpty() && !minDeque.isEmpty() && 
                   (nums[maxDeque.peekFirst()] - nums[minDeque.peekFirst()] > k)) {
                left++;
                // Evict indices that have fallen out of the active left window boundary
                if (maxDeque.peekFirst() < left) maxDeque.pollFirst();
                if (minDeque.peekFirst() < left) minDeque.pollFirst();
            }

            // Transition: dp[i + 1] = Sum(dp[left...i])
            // Using our prefix array tracker: pref[i + 1] - pref[left]
            long currentWays = (pref[i + 1] - pref[left] + MOD) % MOD;
            dp[i + 1] = (int) currentWays;

            // Update prefix sum array for the next loop tracking pass
            pref[i + 2] = (pref[i + 1] + dp[i + 1]) % MOD;
        }

        return dp[n];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N) -> Each index is pushed and popped from deques at most once
Space Complexity : O(N) -> Allocated space for deques and dp/prefix arrays
```

---

# Dry Run

```text
nums =, k = 2
```

### Initial State

```text
dp[0] = 1, pref[1] = 1
left = 0
```

### i = 0 (num = 1)

```text
maxDeque =, minDeque = [0]
Diff = 1 - 1 = 0 <= 2 (Valid)
dp[1] = pref[1] - pref[0] = 1 - 0 = 1
pref[2] = pref[1] + dp[1] = 1 + 1 = 2
```

### i = 1 (num = 3)

```text
maxDeque =, minDeque = [0, 1]
Diff = 3 - 1 = 2 <= 2 (Valid)
dp[2] = pref[2] - pref[0] = 2 - 0 = 2
pref[3] = pref[2] + dp[2] = 2 + 2 = 4
```

### i = 2 (num = 5)

```text
maxDeque =, minDeque = [0, 1, 2]
Diff = 5 - 1 = 4 > 2 (Invalid!)
Contract Window: left increments -> left = 1. maxDeque=[2], minDeque=[1, 2]
Check Diff: max(5) - min(3) = 2 <= 2 (Valid!)
dp[3] = pref[3] - pref[1] = 4 - 1 = 3
pref[4] = pref[3] + dp[3] = 4 + 3 = 7
```

### i = 3 (num = 2)

```text
maxDeque =, minDeque = [3] (index 1, 2 popped out)
Diff = 5 - 2 = 3 > 2 (Invalid!)
Contract Window: left increments -> left = 2. maxDeque=[2], minDeque=[3]
Check Diff: max(5) - min(2) = 3 > 2 (Invalid!)
Contract Window: left increments -> left = 3. maxDeque=[3], minDeque=[3] (index 2 popped out)
Check Diff: max(2) - min(2) = 0 <= 2 (Valid!)
dp[4] = pref[4] - pref[3] = 7 - 4 = 3
```

### Answer

```text
dp[4] = 3
```

---

# DP Pattern

## Identify State

```text
f(i)
=Total unique partition combinations for the array prefix ending at index i
```

## Choices

```text 
Pick a split cut index j such that max(nums[j...i]) - min(nums[j...i]) <= k 
```

## Recurrence

```text 
f(i) = Sum( f(j - 1) ) for all valid split cuts 
```

## Base Cases

```text 
f(-1) = 1 (An empty prefix represents exactly 1 baseline configuration anchor)
```

## Key Takeaways

✅ Advanced 1D DP partition optimization structure combining sliding windows with monotonic data queues.
✅ Reframing the dynamic range sum lookups into a 1D prefix-sum differences framework eliminates linear lookup tracking scans.
✅ Deque index validation checks handle monotonicity breaks cleanly, processing array states in strict linear time O(N).
✅ Common Interview Pattern:
```text 
Contiguous Array Partitioning with Range Extremes Constraints 
```


## Similar Problems

• Partition Array for Maximum Sum (LeetCode 1043)
• Continuous Subarrays (LeetCode 2762)
• Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit (LeetCode 1438)
• Partition Array Into Three Parts With Equal Sum (LeetCode 1013)
