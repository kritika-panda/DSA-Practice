# Relative Sort Array

## Problem Statement

Given two arrays `arr1` and `arr2`, the elements of `arr2` are distinct, and all elements in `arr2` are also in `arr1`.

Sort the elements of `arr1` such that the relative ordering of items in `arr1` matches the ordering in `arr2`. Elements that do not appear in `arr2` should be placed at the end of `arr1` in **ascending** order.

The overall run time complexity should be:

```text
O(n * log(n) + m)
```

*(where n is the length of arr1 and m is the length of arr2, optimizable to O(n + m + max_val))*

---

## Examples

### Example 1

**Input**

```java
arr1 = [2, 3, 1, 3, 2, 4, 6, 7, 9, 2, 19]
arr2 = [2, 1, 4, 3, 9, 6]
```

**Output**

```java
[2, 2, 2, 1, 4, 3, 3, 9, 6, 7, 19]
```

**Explanation**

The elements `2, 1, 4, 3, 9, 6` appear in `arr2`, so they are sorted based on their relative positions in `arr2`.  
The remaining elements `7, 19` do not appear in `arr2`, so they are appended at the end in ascending order.

---

### Example 2

**Input**

```java
arr1 = [28, 6, 22, 8, 44, 17]
arr2 = [22, 28, 8, 6]
```

**Output**

```java
[22, 28, 8, 6, 17, 44]
```

---

### Example 3

**Input**

```java
arr1 = [4, 5, 4]
arr2 = [5]
```

**Output**

```java
[5, 4, 4]
```

---

## Brute Force Approach

Use a custom sorting comparator by mapping each element in `arr2` to its index value.

### Steps

1. Create a map or lookup table storing `(element -> index)` for all items in `arr2`.
2. Convert `arr1` to an object array type so a custom comparator can be used.
3. Define the sorting logic:
   - If both elements exist in the map, compare their map indices.
   - If one element exists in the map and the other does not, the one in the map comes first.
   - If neither element exists in the map, sort them in standard ascending numerical order.
4. Overwrite `arr1` with the sorted values.

### Complexity

```text
Time Complexity: O(n * log(n) + m)
Space Complexity: O(n + m)
```

While this meets the broad timeline, sorting objects causes unnecessary overhead. If the values in `arr1` span a reasonably bounded range, we can eliminate comparison sorting completely using a counting sort array.

---

# Optimal Approach: Counting Sort / Frequency Bucket Strategy

## Key Idea

Instead of comparing pairs of numbers, we can use a **Counting Sort** strategy since elements typically fall within a predictable integer boundary (e.g., \(0 \le \text{arr1}[i] \le 1000\)).

1. Track the frequency of every element in `arr1` using a fixed-size frequency frequency bucket array.
2. Iterate through `arr2` sequentially. For each element, look up its count in the frequency array and append it to our results exactly that many times. Set its count to 0.
3. Iterate through the frequency array from index `0` to the maximum value boundary. Any remaining non-zero frequencies represent elements that were missing from `arr2`. Append them to the results sequentially.

Because we walk the frequency buckets sequentially from index 0 to max, the elements not present in `arr2` are automatically appended in perfect ascending order.

---

## Visual Understanding

Suppose:

```java
arr1 = [2, 3, 1, 3, 2, 4, 6, 7, 9, 2, 19]
arr2 = [2, 1, 4, 3, 9, 6]
```

1. **Populate Frequency Bucket array (up to max element 19):**
   - `1 -> 1`, `2 -> 3`, `3 -> 2`, `4 -> 1`, `6 -> 1`, `7 -> 1`, `9 -> 1`, `19 -> 1`

2. **Process `arr2` sequentially:**
   - Element `2`: frequency is 3 -> Add `2, 2, 2`. Clear count.
   - Element `1`: frequency is 1 -> Add `1`. Clear count.
   - Element `4`: frequency is 1 -> Add `4`. Clear count.
   - Element `3`: frequency is 2 -> Add `3, 3`. Clear count.
   - Element `9`: frequency is 1 -> Add `9`. Clear count.
   - Element `6`: frequency is 1 -> Add `6`. Clear count.

Result so far = `[2, 2, 2, 1, 4, 3, 3, 9, 6]`

3. **Collect remaining buckets linearly from 0 to 19:**
   - Remaining elements discovered at index `7` (count 1) and index `19` (count 1).
   - Append them to results -> `7, 19`.

Final Result = `[2, 2, 2, 1, 4, 3, 3, 9, 6, 7, 19]`.

---

## Partition Variables

Let:

```java
int[] frequency = new int[1001]; // Assuming constraints bound values up to 1000
int ansIdx = 0;
```

---

### Border Elements

The range of the counting array safely shields all values within the specified constraint scale:

```java
for (int num : arr1) {
    frequency[num]++;
}
```

---

## Correct Partition Condition

When clearing out the remaining elements not present in `arr2`:

```java
for (int i = 0; i < frequency.length; i++) {
    while (frequency[i] > 0) {
        ans[ansIdx++] = i;
        frequency[i]--;
    }
}
```

This naturally writes missing items into the tail section in non-decreasing order.

---

## How to Move Binary Search

*(Note: This optimal configuration swaps out standard binary search partitioning for a direct non-comparison frequency counting framework to meet linear runtime guarantees).*

---

## Java Solution

```java
class Solution {

    public int[] relativeSortArray(int[] arr1, int[] arr2) {

        int[] frequency = new int[1001]; // Adjust size dynamically or based on problem constraints
        
        // Step 1: Count occurrences of each number in arr1
        for (int num : arr1) {
            frequency[num]++;
        }

        int[] result = new int[arr1.length];
        int index = 0;

        // Step 2: Place elements of arr2 in relative order
        for (int num : arr2) {
            while (frequency[num] > 0) {
                result[index++] = num;
                frequency[num]--;
            }
        }

        // Step 3: Append remaining elements in ascending order
        for (int i = 0; i < frequency.length; i++) {
            while (frequency[i] > 0) {
                result[index++] = i;
                frequency[i]--;
            }
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
arr1 = [4, 5, 4]
arr2 = [5]
```

---

### Initial State

```java
frequency = {4=2, 5=1}
result = [0, 0, 0]
index = 0
```

---

### Step Execution

- **Process arr2 (Element 5):** `frequency[5] = 1`. `result[0] = 5`. `index = 1`. `frequency[5] = 0`.
- **Process Remaining Elements:**
  - Loop reaches `i = 4`. `frequency[4] = 2`.
  - `result[1] = 4`, `index = 2`, `frequency[4] = 1`.
  - `result[2] = 4`, `index = 3`, `frequency[4] = 0`.

Loop finishes scanning all slots up to 1000.

---

### Answer

```java
[5, 4, 4]
```

---

## Why Do We Use Counting Sort Buckets?

By tracking elements within a fixed frequency array, we can place items relative to `arr2` in constant time \(O(1)\) per copy. Scanning the counting array sequentially from index 0 guarantees that all remaining elements are automatically handled in ascending order, avoiding full comparison sort costs entirely.

This layout yields:

```text
O(n + m + max_val)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n + m + max_val)
```

Populating frequencies takes `O(n)` time. Positioning elements following `arr2` takes `O(m)` steps. The final recovery swipe across the tracking spectrum checks at most `max_val` (e.g., 1001) static bucket steps.

---

### Space Complexity

```text
O(max_val)
```

An extra array of size `max_val` (fixed at 1001 elements) is allocated to manage the frequency tallies.

---

## Key Insight

When sorting constraints demand custom ordering rules combined with partial ascending properties, utilizing the numerical value directly as a frequency array index simplifies the process into a non-comparison counting task.

```text
Time  : O(n + m + max_val)
Space : O(max_val)
```

---

## Similar Problems

1. Sort Colors (75)
2. Custom Sort String (791)
3. Intersection of Two Arrays (349)
4. Sort Array By Parity (905)
5. H-Index (274)
