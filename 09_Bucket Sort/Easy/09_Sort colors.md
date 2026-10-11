# Sort Colors

## Problem Statement

Given an array `nums` with `n` objects colored red, white, or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums =
```

**Output**

```java
```

---

### Example 2

**Input**

```java
nums =
```

**Output**

```java
```

---

### Example 3

**Input**

```java
nums =
```

**Output**

```java
```

---

## Brute Force Approach

Use a standard comparison-based sorting algorithm (like Merge Sort or Quick Sort) or count the occurrences of each color and overwrite the array.

### Steps

1. Traverse the array once to count the total number of 0s, 1s, and 2s.
2. Iterate through the array again and overwrite the initial positions with the appropriate count of 0s, followed by 1s, and finally 2s.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(1)
```

While counting sort runs in linear time, it requires **two passes** over the array. The problem can be optimized to sort the array completely in a **single pass**.

---

# Optimal Approach: Dutch National Flag Algorithm

## Key Idea

We can achieve a single-pass linear time complexity using the **Dutch National Flag Algorithm** by maintaining three pointers to partition the array into three color zones:

```text
[0 ... low-1]    -> Zone for 0 (Red)
[low ... mid-1]  -> Zone for 1 (White)
[mid ... high]   -> Unprocessed elements
[high+1 ... n-1] -> Zone for 2 (Blue)
```

We initialize `low = 0`, `mid = 0`, and `high = n - 1`. We iterate through the array using the `mid` pointer and evaluate its value:
- If `nums[mid] == 0`: Swap `nums[low]` and `nums[mid]`, then increment both `low` and `mid`.
- If `nums[mid] == 1`: No swap needed. Just increment `mid`.
- If `nums[mid] == 2`: Swap `nums[mid]` and `nums[high]`, then decrement `high`. Do **not** increment `mid` yet, as the new element swapped from `high` is unprocessed.

---

## Visual Understanding

Suppose:

```java
nums =
```

Initial State: `low = 0`, `mid = 0`, `high = 5`.

- **Step 1 (`nums[mid] == 2`):** Swap `nums[0]` and `nums[5]`. Array becomes: ``. Decrement `high = 4`.
- **Step 2 (`nums[mid] == 0`):** Swap `nums[0]` and `nums[0]`. Increment `low = 1`, `mid = 1`.
- **Step 3 (`nums[mid] == 0`):** Swap `nums[1]` and `nums[1]`. Increment `low = 2`, `mid = 2`.
- **Step 4 (`nums[mid] == 1`):** No swap. Increment `mid = 3`.
- **Step 5 (`nums[mid] == 1`):** No swap. Increment `mid = 4`.
- **Step 6 (`nums[mid] == 0`):** Swap `nums[2]` and `nums[4]`. Array becomes: ``. Increment `low = 3`, `mid = 5`.

Loop terminates because `mid > high` (`5 > 4`).

---

## Partition Variables

Let:

```java
int low = 0;
int mid = 0;
int high = nums.length - 1;
```

---

### Border Elements

The moving boundary conditions keep the three tracking sections isolated:

```java
while (mid <= high)
```

---

## Correct Partition Condition

The value layout stabilizes into the three distinct sub-segments when the `mid` scanning pointer completely passes the `high` indicator limit.

---

## How to Move Binary Search

*(Note: This optimal method swaps out a value range search for a 3-way in-place pointer partitioning framework to satisfy the single-pass linear time constraint).*

---

## Java Solution

```java
class Solution {

    public void sortColors(int[] nums) {

        if (nums == null || nums.length <= 1) {
            return;
        }

        int low = 0;
        int mid = 0;
        int high = nums.length - 1;

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } 
            else if (nums[mid] == 1) {
                mid++;
            } 
            else { // nums[mid] == 2
                swap(nums, mid, high);
                high--;
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
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

### Initial State

```java
low = 0, mid = 0, high = 5
```

---

### Step Execution

- **mid = 0 (`nums[0] = 2`):** Swap `nums[0]` and `nums[5]`. Array = ``. `high = 4`.
- **mid = 0 (`nums[0] = 0`):** Swap `nums[0]` and `nums[0]`. `low = 1`, `mid = 1`.
- **mid = 1 (`nums[1] = 0`):** Swap `nums[1]` and `nums[1]`. `low = 2`, `mid = 2`.
- **mid = 2 (`nums[2] = 1`):** No swap. `mid = 3`.
- **mid = 3 (`nums[3] = 1`):** No swap. `mid = 4`.
- **mid = 4 (`nums[4] = 0`):** Swap `nums[2]` and `nums[4]`. Array = ``. `low = 3`, `mid = 5`.

Loop ends since `mid (5) > high (4)`.

---

### Answer

```java
```

---

## Why Do We Use Three Pointers?

By classifying elements dynamically into three relative fields, elements are repositioned directly into their correct color segments with a single look. We avoid the overhead of re-scanning indices or calculating secondary arrays completely.

This partition tracking model yields:

```text
O(n)
```

which satisfies the optimal constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

The algorithm performs a single pass over the array. Every step moves the `mid` pointer closer to `high` or shrinks `high`, processing the entire array in a maximum of `n` operations.

---

### Space Complexity

```text
O(1)
```

The sorting takes place in-place using variable references. No additional memory segments are created.

---

## Key Insight

Using three boundary indicators allows us to maintain sorted subsets seamlessly on both left and right edges simultaneously while traversing the array in a single sweep.

```text
Time  : O(n)
Space : O(1)
```

---

## Similar Problems

1. Move Zeroes (283)
2. Remove Element (27)
3. Partition Array According to Given Pivot (2161)
4. Sort Array By Parity (905)
5. Dutch National Flag Problem
