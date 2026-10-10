# Longest Even-Odd Alternating Subarray Within Threshold

## Problem Statement

You are given a `0-indexed` integer array `nums` and an integer `threshold`.

Find the length of the longest subarray `nums[l...r]` that satisfies all of the following conditions:

1. `nums[l] % 2 == 0` (the subarray starts with an even number).
2. For every index `i` in the range `[l, r - 1]`:

   ```java
   nums[i] % 2 != nums[i + 1] % 2
   ```

   (adjacent elements must alternate between even and odd).
3. For every index `i` in the range `[l, r]`:

   ```java
   nums[i] <= threshold
   ```

Return the length of the longest such subarray.

> A subarray is a contiguous non-empty sequence of elements within an array.

---

## Examples

### Example 1

**Input**

```java
nums = [3,2,5,4]
threshold = 5
```

**Output**

```java
3
```

**Explanation**

Choose the subarray:

```java
[2,5,4]
```

- Starts with an even number (`2`)
- Alternates even → odd → even
- All elements are ≤ 5

Length:

```java
3
```

---

### Example 2

**Input**

```java
nums = [1,2]
threshold = 2
```

**Output**

```java
1
```

**Explanation**

Choose:

```java
[2]
```

It starts with an even number and satisfies all conditions.

Length:

```java
1
```

---

### Example 3

**Input**

```java
nums = [2,3,4,5]
threshold = 4
```

**Output**

```java
3
```

**Explanation**

Choose:

```java
[2,3,4]
```

- Starts with even (`2`)
- Alternates parity
- Every element is ≤ 4

Length:

```java
3
```

---

## Approach

We scan the array and attempt to build an alternating subarray.

### Key Observations

A valid subarray must:

- Begin with an even number.
- Contain only values ≤ `threshold`.
- Alternate parity at every step.

Whenever one of these conditions breaks, the current subarray ends and we search for a new valid starting point.

The variable `flag` indicates whether we are currently inside a valid alternating subarray.

---

## Algorithm

1. Traverse the array using pointer `j`.
2. If not currently building a subarray:
   - Check whether `nums[j]` is even and within threshold.
   - If yes, start a new subarray.
3. If already inside a valid subarray:
   - Verify:
     - Current number is within threshold.
     - Current and previous numbers have opposite parity.
   - If valid, extend the subarray.
   - Otherwise, reset and try starting again.
4. Continuously update the maximum length found.

---

## Java Solution

```java
class Solution {

    public int longestAlternatingSubarray(int[] nums, int th) {

        int n = nums.length;
        int maxi = 0;
        int i = 0;
        int j = 0;
        int flag = 0;

        while (j < n) {

            if (flag == 0) {

                if (nums[j] % 2 == 0 && nums[j] <= th) {
                    i = j;
                    maxi = Math.max(maxi, j - i + 1);
                    flag = 1;
                }

            } else {

                int x = nums[j - 1];
                int y = nums[j];
                int sum = x + y;

                if (sum % 2 != 0 && nums[j] <= th) {
                    maxi = Math.max(maxi, j - i + 1);
                } else {
                    flag = 0;
                    j--;
                }
            }

            j++;
        }

        return maxi;
    }
}
```

---

## Dry Run

### Input

```java
nums = [3,2,5,4]
threshold = 5
```

### Step 1

```text
j = 0
nums[0] = 3
```

- Not even
- Cannot start

```text
maxi = 0
```

---

### Step 2

```text
j = 1
nums[1] = 2
```

- Even
- ≤ threshold

Start new subarray:

```java
[2]
```

```text
maxi = 1
```

---

### Step 3

```text
j = 2
nums[2] = 5
```

Check parity:

```text
2 + 5 = 7 (odd)
```

Alternation is valid.

Subarray:

```java
[2,5]
```

```text
maxi = 2
```

---

### Step 4

```text
j = 3
nums[3] = 4
```

Check parity:

```text
5 + 4 = 9 (odd)
```

Alternation continues.

Subarray:

```java
[2,5,4]
```

```text
maxi = 3
```

---

### Final Answer

```text
3
```

---

## Why Does `x + y` Being Odd Work?

Two numbers have opposite parity when one is even and the other is odd.

Examples:

```text
Even + Odd = Odd
Odd + Even = Odd
```

Therefore:

```java
(x + y) % 2 != 0
```

means:

```java
x % 2 != y % 2
```

which verifies the alternating parity condition.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

- The array is traversed once.
- Each element is processed a constant number of times.

---

### Space Complexity

```text
O(1)
```

Only a few variables are used:

```java
i, j, maxi, flag
```

No extra data structures are required.

---

## Key Insight

A valid subarray must:

```text
1. Start with an even number.
2. Alternate parity at every position.
3. Contain only elements ≤ threshold.
```

Whenever any condition fails, restart the search from the current position.

This allows us to find the longest valid alternating subarray in:

```text
Time  : O(n)
Space : O(1)
```
