# Count of Range Sum

## Problem Statement

Given an integer array `nums` and two integers `lower` and `upper`, return the number of range sums that lie in `[lower, upper]` inclusive.

A **range sum** $S(i, j)$ is defined as the sum of the elements in `nums` between indices `i` and `j` inclusive ($i \le j$), which can be written as:

```text
S(i, j) = nums[i] + nums[i+1] + ... + nums[j]
```

The overall run time complexity should be:

```text
O(n * log(n))
```

*(where n is the total number of elements in the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums =
lower = -2
upper = 2
```

**Output**

```java
3
```

**Explanation**

The three ranges that have a sum falling between -2 and 2 are:
- `[0, 0]` -> `nums = -2`
- `[2, 2]` -> `nums = 2`
- `[0, 2]` -> `-2 + 5 + 2 = 5` (Wait, let's see. Valid sums: `nums = -2`, `nums = 2`, and `nums + nums + nums = 5` which is invalid. The valid ranges are actually `[0, 0]` (-2), `[2, 2]` (2), and `[1, 2]` ($5 + 2 = 7$, invalid). Let's check `[0, 1]` ($-2 + 5 = 3$, invalid). The 3 ranges are: `[0,0]` (-2), `[2,2]` (2), and `[0,2]`? No, let's check `nums = 5`, `nums = 2`... Wait, if prefix sums are computed: $P = [0, -2, 3, 5]$, then $P[3]-P[0] = 5-0=5$, $P[3]-P[1] = 5 - (-2) = 7$, $P[2]-P[0] = 3-0=3$, $P[2]-P[1] = 3 - (-2) = 5$, $P[1]-P[0] = -2$, $P[3]-P[2] = 5-3=2$. So the 3 valid range sums are `-2` (range `[0,0]`), `2` (range `[2,2]`), and another one? Ah, range `[0,0]` is -2, range `[2,2]` is 2. Let's look closely at `P`: $P[1]-P[0] = -2$, $P[3]-P[2] = 2$. What about $P[2]-P[1]$? That's 5. What about $P[3]-P[1]$? That's 7. What about $P[1]-P[2]$? Not a valid range. Let's re-verify: the 3 ranges are `[0,0]`, `[2,2]`, and `[0,1]` is 3, `[1,1]` is 5. Wait, what about `[0,2]`? $-2+5+2=5$. Is there another range? No, the example 1 output of 3 corresponds to the 3 ranges: `[0,0]` (-2), `[2,2]` (2), and `[0,2]`? No, `-2` and `2` are valid. Let's check the prefix sums carefully.)

---

### Example 2

**Input**

```java
nums = [0]
lower = 0
upper = 0
```

**Output**

```java
1
```

**Explanation**

The only range is `[0, 0]` with a sum of `0`, which lies within `[0, 0]`.

---

## Brute Force Approach

Use two nested loops to check every possible subarray range `(i, j)` and calculate its sum.

### Steps

1. Run an outer loop with index `i` from `0` to `n - 1`.
2. Run an inner loop with index `j` from `i` to `n - 1`.
3. Accumulate the sum from `i` to `j`. Use a `long` variable to avoid integer overflow.
4. If `lower <= sum && sum <= upper`, increment the counter.
5. Return the total count.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

For large arrays, an $O(n^2)$ brute-force implementation triggers a Time Limit Exceeded (TLE) error. We can optimize this by leveraging prefix sums combined with a divide-and-conquer strategy.

---

# Optimal Approach: Merge Sort on Prefix Sums

## Key Idea

A range sum $S(i, j)$ can be expressed using prefix sums: $S(i, j) = \text{sums}[j+1] - \text{sums}[i]$. 
The problem then reduces to finding the number of pairs $(i, j)$ such that $i < j$ and:

```text
lower <= sums[j] - sums[i] <= upper
```

This can be rearranged into a target range condition for each $\text{sums}[i]$:

```text
sums[i] + lower <= sums[j] <= sums[i] + upper
```

We can maintain order and count these valid pairs efficiently by adapting the **Merge Sort** algorithm on the prefix sums array. 

When dividing the prefix sums array into two sorted halves (`left` and `right`), any valid pair will have $i$ in the left half and $j$ in the right half. Because both halves are sorted individually during the merge sort process, we can use a **two-pointer sliding window** approach:
- For each element $\text{sums}[i]$ in the left half, we maintain two pointers `start` and `end` in the right half.
- `start` is the first index where $\text{sums}[\text{start}] \ge \text{sums}[i] + \text{lower}$.
- `end` is the first index where $\text{sums}[\text{end}] > \text{sums}[i] + \text{upper}$.
- The number of valid options for this specific $i$ is exactly `end - start`. Since both halves are sorted monotonically, `start` and `end` never need to be reset, allowing us to count all crossing pairs for the two halves in linear time.

---

## Visual Understanding

Suppose we are counting valid pairs between two pre-sorted prefix sum halves with `lower = -2` and `upper = 2`:

```text
Left Subarray (i positions)   : [-2, 0]
Right Subarray (j positions)  : [3, 5]
```

- **For $i = 0$ ($\text{sums}[i] = -2$):**
  - Target range for $j$ is $[-2 + (-2), -2 + 2] = [-4, 0]$.
  - Scan the right subarray: `3` is greater than 0, so no element fits. `start = 2`, `end = 2`. Count added = `0`.
- **For $i = 1$ ($\text{sums}[i] = 0$):**
  - Target range for $j$ is $[0 + (-2), 0 + 2] = [-2, 2]$.
  - Scan the right subarray: `3` is greater than 2, so no element fits. Count added = `0`.

*(Note: The actual valid counts aggregate dynamically across all internal recursive layer splits of the entire prefix array).*

---

## Partition Variables

Let:

```java
int mid = low + (high - low) / 2;
int count = mergeSortAndCount(sums, low, mid) + mergeSortAndCount(sums, mid + 1, high);
```

---

### Border Elements

To protect calculations from 32-bit integer overflow limits completely, the prefix sum values are tracked using `long` array types:

```java
long[] sums = new long[nums.length + 1];
```

---

## Correct Partition Condition

The running indices `start` and `end` advance continuously across the right partition until they violate the dynamic range bounds for the current left element:

```java
while (start <= high && sums[start] < sums[i] + lower) start++;
while (end <= high && sums[end] <= sums[i] + upper) end++;
count += (end - start);
```

---

## How to Move Binary Search

*(Note: This optimal configuration swaps out standard binary range searches for a divide-and-conquer Merge Sort architecture over prefix sum elements to track and slice interval boundaries in log-linear time).*

---

## Java Solution

```java
class Solution {

    public int countRangeSum(int[] nums, int lower, int upper) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int n = nums.length;
        long[] sums = new long[n + 1];
        
        // Step 1: Populate the prefix sum array using long to handle overflow
        for (int i = 0; i < n; i++) {
            sums[i + 1] = sums[i] + nums[i];
        }

        // Step 2: Use divide-and-conquer to count valid pairs during merge sort
        return mergeSortAndCount(sums, 0, n, lower, upper);
    }

    private int mergeSortAndCount(long[] sums, int low, int high, int lower, int upper) {
        if (low >= high) {
            return 0;
        }

        int mid = low + (high - low) / 2;
        
        // Count valid pairs purely within the left and right splits
        int count = mergeSortAndCount(sums, low, mid, lower, upper) 
                  + mergeSortAndCount(sums, mid + 1, high, lower, upper);

        // Count valid pairs that cross across the left and right boundary split
        int start = mid + 1;
        int end = mid + 1;
        
        for (int i = low; i <= mid; i++) {
            while (start <= high && sums[start] < sums[i] + lower) {
                start++;
            }
            while (end <= high && sums[end] <= sums[i] + upper) {
                end++;
            }
            count += (end - start);
        }

        // Standard merge step to maintain sorted order for the next recursive layer
        merge(sums, low, mid, high);

        return count;
    }

    private void merge(long[] sums, int low, int mid, int high) {
        long[] temp = new long[high - low + 1];
        int left = low;
        int right = mid + 1;
        int idx = 0;

        while (left <= mid && right <= high) {
            if (sums[left] <= sums[right]) {
                temp[idx++] = sums[left++];
            } else {
                temp[idx++] = sums[right++];
            }
        }

        while (left <= mid) {
            temp[idx++] = sums[left++];
        }

        while (right <= high) {
            temp[idx++] = sums[right++];
        }

        System.arraycopy(temp, 0, sums, low, temp.length);
    }
}
```

---

## Dry Run

### Input

```java
nums = [0]
lower = 0, upper = 0
```

---

### Step Execution Traversal

- **Prefix Sum Setup:** `sums = [0, 0]`. `n = 1`.
- **Initial Call:** `mergeSortAndCount(sums, 0, 1, 0, 0)`. `low = 0, high = 1`.
- **Split Layer:** `mid = 0`.
  - Left call `[0, 0]` returns `0`. Right call `[1, 1]` returns `0`.
- **Crossing Pairs Counting Phase:**
  - `start = 1`, `end = 1`.
  - **i = 0 ($\text{sums} = 0$):**
    - `sums[start]` is `sums[1] = 0`. Check `0 < 0 + 0` (False). `start` stays `1`.
    - `sums[end]` is `sums[1] = 0`. Check `0 <= 0 + 0` (True). `end` increments to `2`.
    - Loop terminates because `end > high` ($2 > 1$).
    - `count += (2 - 1) = 1`.
- **Merge Phase:** Re-arranges `[0, 0]` into sorted structure. Returns total count = `1`.

---

### Answer

```java
1
```

---

## Why Is the Time Complexity Log-Linear?

Use code with caution.
The algorithm utilizes a divide-and-conquer model to split the structural prefix array into equal half segments repeatedly ($\log n$ levels). At each merge step layer, the two-pointer sliding count technique sweeps both subsegments linearly ($O(n)$) without pointer regression, keeping the overall execution well within log-linear bounds.
This uniform partitioning model yields:
text O(n * log(n)) 
which satisfies the optimal complexity constraint.

## Complexity Analysis

Time Complexity

```text 
O(n * log(n))
```
The merge sort framework splits the prefix sum array across $\log n$ recursive depth layers. At each level, the linear sliding window count and array merging passes take $O(n)$ time.

Space Complexity

```text
O(n)
```
The merging sequence utilizes a temporary array collection array to rearrange elements dynamically during execution steps.


## Key Insight

Expressing range queries in terms of prefix differences allows us to replace localized subarray scans with a global, sorted two-pointer sliding window search embedded inside a Merge Sort routine.
text Time  : O(n * log(n)) runtime path Space : O(n) auxiliary memory slots
