# Minimize Maximum Pair Sum in Array (LeetCode 1877)

## Problem Statement

The **pair sum** of a pair `(a, b)` is:

```text
a + b
```

You are given an integer array `nums` of even length `n`.

Pair up the elements such that:

```text
Every element belongs to exactly one pair.
```

Return the **minimum possible value** of the **maximum pair sum**.

---

## Examples

### Example 1

```java
Input: nums = [3,5,2,3]

Output: 7
```

### Explanation

Pairs:

```text
(3,3) = 6
(5,2) = 7
```

Maximum pair sum:

```text
max(6,7) = 7
```

Answer:

```text
7
```

---

### Example 2

```java
Input: nums = [3,5,4,2,4,6]

Output: 8
```

### Explanation

Optimal pairing:

```text
(2,6) = 8
(3,5) = 8
(4,4) = 8
```

Maximum pair sum:

```text
8
```

---

# Intuition

We want:

```text
Minimum possible maximum pair sum
```

If we pair:

```text
Largest + Largest
```

the pair sum becomes very large.

Example:

```text
[2,3,4,5,6]
```

Bad pairing:

```text
(6,5)=11
```

This increases the maximum pair sum.

---

Instead:

```text
Pair the smallest number with the largest number.
```

This balances every pair.

---

# Key Greedy Observation

After sorting:

```text
nums = [2,3,4,5,6,7]
```

Create pairs:

```text
(2,7)
(3,6)
(4,5)
```

Pair sums:

```text
9
9
9
```

Maximum:

```text
9
```

Perfectly balanced.

---

# Why Does This Work?

If two large elements are paired together:

```text
Large + Large
```

the maximum pair sum becomes huge.

To reduce the maximum:

```text
Largest element should be paired
with the smallest available element.
```

This is a classic balancing strategy.

---

# Greedy Approach

## Step 1

Sort the array.

```java
Arrays.sort(nums);
```

---

## Step 2

Use two pointers.

```java
left  = 0
right = n - 1
```

---

## Step 3

Pair:

```text
Smallest + Largest
```

Compute:

```java
nums[left] + nums[right]
```

Maintain:

```java
maxPairSum
```

---

## Step 4

Move pointers.

```java
left++;
right--;
```

Continue until pointers cross.

---

# Dry Run

## Input

```java
nums = [3,5,4,2,4,6]
```

---

### Sort

```text
[2,3,4,4,5,6]
```

---

### Pair 1

```text
2 + 6 = 8
```

Maximum:

```text
8
```

---

### Pair 2

```text
3 + 5 = 8
```

Maximum:

```text
8
```

---

### Pair 3

```text
4 + 4 = 8
```

Maximum:

```text
8
```

Answer:

```text
8
```

---

# Visualization

```text
Sorted Array

2 3 4 4 5 6
↑           ↑
|-----------|

Pair Sum = 8

  3 4 4 5
  ↑     ↑
  |-----|

Pair Sum = 8

    4 4
    ↑ ↑

Pair Sum = 8
```

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int minPairSum(int[] nums) {

        Arrays.sort(nums);

        int left = 0;
        int right = nums.length - 1;

        int maxPairSum = 0;

        while (left < right) {

            maxPairSum = Math.max(
                maxPairSum,
                nums[left] + nums[right]
            );

            left++;
            right--;
        }

        return maxPairSum;
    }
}
```

---

# Correctness Proof

Let:

```text
a1 ≤ a2 ≤ a3 ≤ ... ≤ an
```

Consider the largest element:

```text
an
```

To minimize the maximum pair sum:

```text
an should be paired with the smallest available element.
```

Otherwise:

```text
an + larger element
```

creates a bigger pair sum.

Repeating this argument:

```text
a1 ↔ an
a2 ↔ an-1
a3 ↔ an-2
...
```

Hence pairing smallest with largest always minimizes the maximum pair sum.

---

# Complexity Analysis

Let:

```text
N = nums.length
```

### Time Complexity

Sorting:

```text
O(N log N)
```

Two-pointer traversal:

```text
O(N)
```

Overall:

```text
O(N log N)
```

---

### Space Complexity

```text
O(1)
```

Ignoring sorting space.

---

# Why Not Pair Adjacent Elements?

Consider:

```text
[1,2,7,8]
```

Adjacent pairing:

```text
(1,2)=3
(7,8)=15
```

Maximum:

```text
15
```

---

Smallest-Largest pairing:

```text
(1,8)=9
(2,7)=9
```

Maximum:

```text
9
```

Much better.

---

# Pattern Recognition

Use this greedy strategy when:

```text
✓ Need to minimize maximum pair value
✓ Need optimal pairing
✓ Array length is even
✓ Balancing large and small values helps
✓ Pair formation is involved
```

---

# Similar Problems

1. Boats to Save People
2. Two Sum Closest
3. Assign Cookies
4. Array Partition I
5. Put Marbles in Bags
6. Divide Players Into Teams of Equal Skill
7. Bag of Tokens

---

# Takeaway

```text
To minimize the maximum pair sum:

1. Sort the array.
2. Pair smallest with largest.
3. Track the largest pair sum formed.
4. Return that value.
```

**Pattern:** Greedy + Sorting + Two Pointers

**Time Complexity:** O(N log N)

**Space Complexity:** O(1)
