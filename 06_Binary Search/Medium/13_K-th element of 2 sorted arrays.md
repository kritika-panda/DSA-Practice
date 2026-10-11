# K-th Element of Two Sorted Arrays

## Problem Statement

Given two sorted arrays `nums1` and `nums2` of sizes `m` and `n` respectively and an element `k`, return the element that would be at the `k`-th position of the combined sorted array.

The overall run time complexity should be:

```text
O(log(min(m, n)))
```

---

## Examples

### Example 1

**Input**

```java
nums1 = [2, 3, 6, 7, 9]
nums2 = [1, 4, 8, 10]
k = 5
```

**Output**

```java
6
```

**Explanation**

Merged array:

```java
[1, 2, 3, 4, 6, 7, 8, 9, 10]
```

5th element:

```java
6
```

---

### Example 2

**Input**

```java
nums1 = [10, 20, 30, 40]
nums2 = [15, 25, 35]
k = 4
```

**Output**

```java
25
```

**Explanation**

Merged array:

```java
[10, 15, 20, 25, 30, 35, 40]
```

4th element:

```java
25
```

---

### Example 3

**Input**

```java
nums1 = []
nums2 = [1, 2, 3]
k = 2
```

**Output**

```java
2
```

---

## Brute Force Approach

Merge both arrays up to the `k`-th element using two pointers.

### Steps

1. Maintain two pointers for both arrays.
2. Compare elements and pick the smaller one, incrementing the counter.
3. Stop when the counter reaches `k` and return the element.

### Complexity

```text
Time Complexity: O(k)
Space Complexity: O(1)
```

However, if `k` is close to `m + n`, this becomes linear. The problem requires a logarithmic solution.

---

# Optimal Approach: Binary Search on Partition

## Key Idea

Instead of gathering elements, we select a set of `k` elements distributed between the prefixes of both arrays.

We divide both arrays into:

```text
Left Half (Contains exactly k elements)
Right Half
```

such that:

```text
Number of elements from nums1 + Number of elements from nums2
=
k
```

and

```text
max(left side)
<=
min(right side)
```

When this condition is satisfied, the maximum element on the left side is our answer.

---

## Visual Understanding

Suppose:

```java
nums1 = [2, 3, 6, 7, 9]
nums2 = [1, 4, 8, 10]
k = 5
```

Total required elements on left side:

```text
5
```

A valid partition:

```text
nums1: [2, 3, 6] | [7, 9]

nums2: [1, 4] | [8, 10]
```

Left side elements:

```text
2 3 6 1 4 (Count = 5)
```

Right side elements:

```text
7 9 8 10
```

Check:

```text
max(left) = max(6, 4) = 6
min(right) = min(7, 8) = 7
```

Since `6 <= 7`, this is the correct partition. The answer is `max(left) = 6`.

---

## Partition Variables

Let:

```java
cut1 = partition in nums1
cut2 = partition in nums2
```

Since the left side must contain exactly `k` elements:

```java
cut2 = k - cut1;
```

---

### Border Elements

```java
l1 = nums1[cut1 - 1]
l2 = nums2[cut2 - 1]

r1 = nums1[cut1]
r2 = nums2[cut2]
```

Handle boundaries using:

```java
Integer.MIN_VALUE
Integer.MAX_VALUE
```

---

## Correct Partition Condition

```java
l1 <= r2
&&
l2 <= r1
```

If true, `Math.max(l1, l2)` gives the `k`-th element.

---

## How to Move Binary Search

### Case 1

```java
l1 > r2
```

We picked too many elements from `nums1`.

Move left:

```java
high = cut1 - 1;
```

---

### Case 2

```java
l2 > r1
```

We picked too few elements from `nums1`.

Move right:

```java
low = cut1 + 1;
```

---

## Java Solution

```java
class Solution {

    public long kthElement(int[] nums1, int[] nums2, int k) {

        int m = nums1.length;
        int n = nums2.length;

        if (m > n) {
            return kthElement(nums2, nums1, k);
        }

        // Define Binary Search boundaries safely based on k
        int low = Math.max(0, k - n);
        int high = Math.min(k, m);

        while (low <= high) {

            int cut1 = low + (high - low) / 2;
            int cut2 = k - cut1;

            int l1 = (cut1 == 0)
                    ? Integer.MIN_VALUE
                    : nums1[cut1 - 1];

            int l2 = (cut2 == 0)
                    ? Integer.MIN_VALUE
                    : nums2[cut2 - 1];

            int r1 = (cut1 == m)
                    ? Integer.MAX_VALUE
                    : nums1[cut1];

            int r2 = (cut2 == n)
                    ? Integer.MAX_VALUE
                    : nums2[cut2];

            if (l1 <= r2 && l2 <= r1) {
                return Math.max(l1, l2);
            }

            else if (l1 > r2) {
                high = cut1 - 1;
            }

            else {
                low = cut1 + 1;
            }
        }

        return -1;
    }
}
```

---

## Dry Run

### Input

```java
nums1 = [2, 3, 6, 7, 9]
nums2 = [1, 4, 8, 10]
k = 5
```

Since `nums1` is larger, the algorithm swaps them internally:

```java
nums1 = [1, 4, 8, 10]
nums2 = [2, 3, 6, 7, 9]
```

---

### Initial Values

```java
m = 4
n = 5

low = max(0, 5 - 5) = 0
high = min(5, 4) = 4
```

---

### Iteration 1

```java
cut1 = 2
cut2 = 3
```

```java
l1 = 4
r1 = 8

l2 = 6
r2 = 7
```

Check:

```java
l1 <= r2
4 <= 7 (True)

l2 <= r1
6 <= 8 (True)
```

Valid partition reached immediately.

---

### Answer

```java
max(l1, l2)
=
max(4, 6)
=
6
```

---

## Why Do We Search on the Smaller Array?

Binary search boundary ranges are optimized to ensure `cut2` never falls out of bounds (`0 <= cut2 <= n`). Operating on the smaller array scales the search space efficiently.

Searching on the smaller array gives:

```text
O(log(min(m,n)))
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(log(min(m, n)))
```

Binary Search handles the search boundaries on the smaller array.

---

### Space Complexity

```text
O(1)
```

No extra space is used.

---

## Key Insight

The `k`-th element is the edge selector between the left side elements and the right side elements once:

```java
max(left half)
<=
min(right half)
```

We map `cut1` elements from array 1, meaning `k - cut1` elements must come from array 2.

```text
Time  : O(log(min(m,n)))
Space : O(1)
```

---

## Similar Problems

1. Median of Two Sorted Arrays
2. Median of a Data Stream
3. Find Peak Element
4. Search in Rotated Sorted Array
5. Split Array Largest Sum
6. K-th Smallest Element in a Sorted Matrix
