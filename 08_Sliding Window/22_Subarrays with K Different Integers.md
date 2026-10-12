# Subarrays with K Different Integers

## Problem Statement

Given an integer array `nums` and an integer `k`, return *the number of **good subarrays** of `nums`*.

A **good subarray** is a subarray that contains exactly `k` different integers.
- For example, `[1, 2, 3]` has `3` different integers: `1`, `2`, and `3`.

A **subarray** is a **contiguous** part of an array.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the total number of elements inside the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [1, 2, 1, 2, 3]
k = 2
```

**Output**

```java
7
```

**Explanation**

Subarrays formed with exactly 2 different integers:
`[1, 2]`, `[2, 1]`, `[1, 2]`, `[2, 3]`, `[1, 2, 1]`, `[2, 1, 2]`, `[1, 2, 1, 2]`.

---

### Example 2

**Input**

```java
nums = [1, 2, 1, 3, 4]
k = 3
```

**Output**

```java
3
```

**Explanation**

Subarrays formed with exactly 3 different integers:
`[1, 2, 1, 3]`, `[2, 1, 3]`, `[1, 3, 4]`.

---

## Brute Force Approach

Generate every possible contiguous subarray using nested loops, calculate the count of distinct elements inside each subarray using a set, and increment a global counter whenever the distinct count equals exactly `k`.

### Steps

1. Initialize `goodSubarraysCount = 0`.
2. Run an outer loop `i` from `0` to `n - 1` to mark the starting index of the subarray.
3. Run an inner loop `j` from `i` to `n - 1` to mark the ending index of the subarray.
4. Maintain a `HashSet<Integer>` dynamically inside the inner loop to collect numbers from `nums[j]`.
5. If `set.size() == k`, increment `goodSubarraysCount`. If `set.size() > k`, break the inner loop early.
6. Return the total count.

### Complexity

```text
Time Complexity: O(n^2) // due to checking all pairs of indices
Space Complexity: O(n) to hold unique elements in the set window
```

Evaluating all combinations takes quadratic time, causing standard execution timeouts for large input sets. We can optimize this process down to **linear time** by transforming the exact constraint using standard sliding windows.

---

# Optimal Approach: At Most K Transformation via Sliding Window

## Key Idea

Counting subarrays with *exactly* `k` distinct integers directly within a single sliding window is tricky because the window size doesn't grow or shrink monotonically with a strict equivalence constraint. 

However, we can simplify the problem using a clever mathematical transformation:

\[\text{Exactly}(K) = \text{AtMost}(K) - \text{AtMost}(K - 1)\]

Calculating the number of subarrays with **at most `k` distinct integers** is a classic sliding window problem that can be solved in a single clean pass. 
1. **The Counting Formula:** For any valid sliding window `[left, right]` containing at most `k` distinct integers, the total number of valid subarrays ending exactly at index `right` is equal to the length of the window: `right - left + 1`.
2. **Dynamic Frequency Array:** We use a frequency tracking array `counts` (or a `HashMap`) and a `distinct` counter to manage our active window footprint.
3. **Window Expansion & Contraction:**
   - Expand the window by sliding `right` forward and recording the incoming element.
   - If `distinct > k`, the window has breached our constraint. Move `left` forward, decrementing counts, until `distinct` drops back down to `k`.
   - Accumulate the sliding window lengths into a sum.
4. Run this helper tracking logic twice: once for `atMost(nums, k)` and once for `atMost(nums, k - 1)`. The difference between the two results yields the exact match count in optimal linear time.

---

## Visual Understanding

Suppose `nums = [1, 2, 1, 2, 3]` and `k = 2`:

Let's look at how `atMost(nums, 2)` grows. Initialize `left = 0`, `sum = 0`:
- **right = 0 (`1`):** Distinct count = 1 <= 2. Subarrays ending here = `0 - 0 + 1 = 1`. (`[1]`). `sum = 1`.
- **right = 1 (`2`):** Distinct count = 2 <= 2. Subarrays ending here = `1 - 0 + 1 = 2`. (`[2], [1, 2]`). `sum = 1 + 2 = 3`.
- **right = 2 (`1`):** Distinct count = 2 <= 2. Subarrays ending here = `2 - 0 + 1 = 3`. (`[1], [2, 1], [1, 2, 1]`). `sum = 3 + 3 = 6`.
- **right = 3 (`2`):** Distinct count = 2 <= 2. Subarrays ending here = `3 - 0 + 1 = 4`. `sum = 6 + 4 = 10`.
- **right = 4 (`3`):** Distinct count becomes 3, which is > 2! Shrink left pointer forward. `left` moves past `0` and `1` until distinct is 2 \(\implies\) `[2, 3]` at `left = 3`. Valid length = `4 - 3 + 1 = 2`. `sum = 10 + 2 = 12`.

Total for `atMost(k=2)` = 12. 
If we run `atMost(k=1)`, the sum evaluates to 5 (counting only single-element runs like `[1], [2], [1], [2], [3]`).
\[\text{Exactly}(2) = 12 - 5 = 7\]

The exact count of 7 is isolated cleanly without complex multi-pointer branching.

---

## Partition Variables

Let:

```java
int[] counts = new int[nums.length + 1]; // Pre-allocated array fits elements up to n securely
int distinct = 0;
int left = 0;
long totalSubarrays = 0;
```

---

### Border Elements

The sliding window contraction loop drops frequencies cleanly and manages distinct state flags dynamically as the left pointer marches forward:

```java
counts[nums[left]]--;
if (counts[nums[left]] == 0) {
    distinct--;
}
left++;
```

---

## Correct Partition Condition

The overall problem calculation evaluates the subtraction difference layout between the two independent function passes:

```java
return atMostK(nums, k) - atMostK(nums, k - 1);
```

---

## Java Solution

```java
class Solution {

    public int subarraysWithKDistinct(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k == 0) {
            return 0;
        }
        
        // Transform the exact constraint into two simpler "at most" sliding window subproblems
        return atMostK(nums, k) - atMostK(nums, k - 1);
    }

    private int atMostK(int[] nums, int k) {
        // Pre-allocated frequency tracker array based on problem input constraints (1 <= nums[i] <= nums.length)
        int[] counts = new int[nums.length + 1];
        int distinct = 0;
        int left = 0;
        int totalSubarrays = 0;

        // Slide the right boundary pointer forward to expand our window
        for (int right = 0; right < nums.length; right++) {
            // If this is the first time we see this integer in our active window, increment distinct
            if (counts[nums[right]] == 0) {
                distinct++;
            }
            counts[nums[right]]++;

            // Step 2: Contract the window from the left if distinct elements cross above k
            while (distinct > k) {
                counts[nums[left]]--;
                // If a number's frequency hits 0, it has completely left our active window scope
                if (counts[nums[left]] == 0) {
                    distinct--;
                }
                left++; // Advance the left boundary
            }

            // Step 3: The window [left, right] contains at most k distinct elements.
            // The number of valid subarrays ending exactly at index 'right' is equal to the window length.
            totalSubarrays += (right - left + 1);
        }

        return totalSubarrays;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **At Most K Sliding Window** | **O(n)** | **O(n)** | The helper function is invoked twice. Inside each pass, both the `right` and `left` pointers march forward through the array at most once, performing O(1) constant-time array updates. The frequency tracker array allocates space matching input boundaries. |

---

## Key Insight

Transforming the strict "exactly K" equivalence constraint into the difference of two monotonic "at most K" subproblems allows window lengths to be accumulated cleanly, tracking distinct subarrays in optimal linear time.

```text
Time  : O(n) single pass linear sliding complexity
Space : O(n) pre-allocated primitive tracking array allocation
```

---

## Similar Problems

1. Longest Substring Without Repeating Characters (3)
2. Subarray Sum Equals K (560)
3. Count Number of Nice Subarrays (1248)
4. Binary Subarrays With Sum (930)
5. Fruit Into Baskets (904)
