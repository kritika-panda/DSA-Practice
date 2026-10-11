# Reverse Pairs

## Problem Statement

Given an integer array `nums`, return the number of **reverse pairs** in the array.

A reverse pair is a pair `(i, j)` where:
1. `0 <= i < j < nums.length`
2. `nums[i] > 2 * nums[j]`

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
```

**Output**

```java
2
```

**Explanation**

The two reverse pairs are:
- `(1, 4)` -> `nums = 3`, `nums = 1` -> `3 > 2 * 1` (True)
- `(2, 4)` -> `nums = 3`, `nums = 1` -> `3 > 2 * 1` (True)

---

### Example 2

**Input**

```java
nums =
```

**Output**

```java
3
```

**Explanation**

The three reverse pairs are:
- `(0, 3)` -> `nums = 2`, `nums = 0` -> `2 > 2 * 0` (True)
- `(1, 3)` -> `nums = 4`, `nums = 0` -> `4 > 2 * 0` (True)
- `(2, 3)` -> `nums = 3`, `nums = 0` -> `3 > 2 * 0` (True)

---

### Example 3

**Input**

```java
nums = [1]
```

**Output**

```java
0
```

---

## Brute Force Approach

Use two nested loops to check every possible pair `(i, j)` to see if it satisfies the reverse pair conditions.

### Steps

1. Run an outer loop with index `i` from `0` to `n - 1`.
2. Run an inner loop with index `j` from `i + 1` to `n - 1`.
3. For each pair, check if `nums[i] > 2L * nums[j]` (casting to `long` avoids integer overflow).
4. Count and return the total number of successful matches.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

For large arrays, an \(O(n^2)\) time complexity results in a Time Limit Exceeded (TLE) error. The problem requires a more optimized divide-and-conquer strategy.

---

# Optimal Approach: Merge Sort (Divide & Conquer)

## Key Idea

We can count the reverse pairs efficiently by modifying the standard **Merge Sort** routine. 

When dividing the array into two sorted halves (`left` and `right`), we can count reverse pairs before merging them. Because both the left and right subarrays are already sorted individually, we can use a **two-pointer approach**:

For each element `i` in the left subarray, we find how many elements `j` in the right subarray satisfy `nums[i] > 2L * nums[j]`. 
- If `nums[i] > 2L * nums[j]`, then because the left array is sorted, any subsequent element after `i` will *also* be greater than `2 * nums[j]`.
- This monotonic behavior allows us to advance the right pointer `j` without resetting it for the next `i`, completing the counting pass for both halves in linear time.

---

## Visual Understanding

Suppose we are counting pairs between two sorted subarrays:

```text
Left Subarray:  
Right Subarray: 
```

- **i = 0 (`nums[i] = 4`):** Check `j = 0 (val = 1)`. `4 > 2 * 1` is true. Move `j` to 1. Check `j = 1 (val = 3)`. `4 > 2 * 3` is false. Stop. Elements in right half smaller than `4/2` is `1` (element `1`). Add `j - mid - 1` = `1` to count.
- **i = 1 (`nums[i] = 6`):** Resume checking from `j = 1 (val = 3)`. `6 > 2 * 3` is false. Stop. Total valid right elements for `6` includes everything up to `j`, which is still `1`.
- **i = 2 (`nums[i] = 8`):** Resume checking from `j = 1 (val = 3)`. `8 > 2 * 3` is true. Move `j` to 2. Out of bounds. Total elements matching for `8` is `2` (elements `1` and `3`).

---

## Partition Variables

Let:

```java
int mid = low + (high - low) / 2;
int count = mergeSortAndCount(nums, low, mid) + mergeSortAndCount(nums, mid + 1, high);
```

---

### Border Elements

To handle values near the 32-bit limits cleanly without overflow errors, we perform comparisons using `long` typecasting boundaries:

```java
if ((long) nums[i] > 2 * (long) nums[j])
```

---

## Correct Partition Condition

The count update step tracks the width of elements in the right partition that fall below the half-value threshold of the current left element:

```java
count += (j - (mid + 1));
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range search with a divide-and-conquer Merge Sort layout to partition and count inverted element values in log-linear time).*

---

## Java Solution

```java
import java.util.ArrayList;

class Solution {

    public int reversePairs(int[] nums) {
        if (nums == null || nums.length < 2) {
            return 0;
        }
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] nums, int low, int high) {
        if (low >= high) {
            return 0;
        }

        int mid = low + (high - low) / 2;
        int count = 0;

        // Step 1: Recursively count reverse pairs in left and right halves
        count += mergeSortAndCount(nums, low, mid);
        count += mergeSortAndCount(nums, mid + 1, high);

        // Step 2: Count reverse pairs across the split boundary
        count += countPairs(nums, low, mid, high);

        // Step 3: Merge both sorted halves together
        merge(nums, low, mid, high);

        return count;
    }

    private int countPairs(int[] nums, int low, int mid, int high) {
        int count = 0;
        int j = mid + 1;

        // Two-pointer sliding count technique
        for (int i = low; i <= mid; i++) {
            while (j <= high && (long) nums[i] > 2 * (long) nums[j]) {
                j++;
            }
            count += (j - (mid + 1));
        }
        
        return count;
    }

    private void merge(int[] nums, int low, int mid, int high) {
        ArrayList<Integer> temp = new ArrayList<>();
        int left = low;
        int right = mid + 1;

        // Standard merge tracking sort layout
        while (left <= mid && right <= high) {
            if (nums[left] <= nums[right]) {
                temp.add(nums[left++]);
            } else {
                temp.add(nums[right++]);
            }
        }

        while (left <= mid) {
            temp.add(nums[left++]);
        }

        while (right <= high) {
            temp.add(nums[right++]);
        }

        for (int i = low; i <= high; i++) {
            nums[i] = temp.get(i - low);
        }
    }
}
```

---

## Dry Run

### Input

```java
nums =
```

---

### Step Execution Traversal

- **Base splits down to final merge steps:** Let's look at the cross-boundary step where `left = ` and `right = `. `low = 0, mid = 1, high = 3`.
- **Counting Phase:**
  - **i = 0 (`nums = 1`):** `j = 2 (`nums = 3`)`. `1 > 2 * 3` (False). `count += (2 - 2) = 0`.
  - **i = 1 (`nums = 3`):** `j = 2 (`nums = 3`)`. `3 > 2 * 3` (False). `count += (2 - 2) = 0`.
- Let's trace an active mismatch pair, e.g., if a deeper layer split was `left = ` and `right = `:
  - **i = 0 (`nums = 3`):** `j = 1 (`nums = 1`)`. `3 > 2 * 1` holds true. `j` increments to `2`. Loop finishes. `count += (2 - 1) = 1`.

The recursive tree winds up completely aggregating all pair offsets.

---

### Answer

```java
2
```

---

## Why Is the Time Complexity Log-Linear?

The algorithm utilizes a divide-and-conquer model to split the structural problem scope into half segments repeatedly (`log n` levels). At each merge step layer, the two-pointer counting technique sweeps the subsegments linearly (`O(n)`), keeping the overall execution well within log-linear bounds.

This uniform partitioning model yields:

```text
O(n * log(n))
```

which satisfies the optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(n))
```

The merge sort framework splits the array across \(\log n\) recursive depth levels. At each level, the linear counting and sorting passes take \(O(n)\) time.

---

### Space Complexity

```text
O(n)
```

The merging sequence utilizes a temporary array collection list to rearrange elements dynamically during execution steps.

---

## Key Insight

Leveraging sorted subarray intervals allows us to count element pairs in linear time using a two-pointer sliding scan, avoiding the \(O(n^2)\) penalty of checking pairs from scratch.

```text
Time  : O(n * log(n)) runtime path
Space : O(n) auxiliary memory slots
```

---

## Similar Problems

1. Count of Smaller Numbers After Self (315)
2. Global and Local Inversions (775)
3. Beautiful Array (932)
4. Merge Sort Implementation
5. Count of Range Sum (327)
