# Find the Smallest Divisor Given a Threshold

## Problem Statement

Given an array of integers `nums` and an integer `threshold`, we will choose a positive integer `divisor`, divide all the array by it, and sum the results of the division. Find the **smallest divisor** such that the result sum is **less than or equal to** `threshold`.

Each result of the division is **rounded to the nearest integer greater than or equal to that element** (i.e., ceiling division: `Math.ceil(nums[i] / divisor)`).

It is guaranteed that there will be an answer.

---

## Examples

### Example 1

```text
Input: nums = [1,2,5,9], threshold = 6
Output: 5
```

### Explanation

```text
If divisor = 1:
sum = ceil(1/1) + ceil(2/1) + ceil(5/1) + ceil(9/1)
    = 1 + 2 + 5 + 9
    = 17 (> 6)

If divisor = 4:
sum = ceil(1/4) + ceil(2/4) + ceil(5/4) + ceil(9/4)
    = 1 + 1 + 2 + 3
    = 7 (> 6)

If divisor = 5:
sum = ceil(1/5) + ceil(2/5) + ceil(5/5) + ceil(9/5)
    = 1 + 1 + 1 + 2
    = 5 (<= 6)

Therefore, the smallest divisor that satisfies the condition is 5.
```

---

### Example 2

```text
Input: nums = [44,22,33,11,1], threshold = 5
Output: 44
```

### Explanation

```text
Since threshold equals the number of elements in the array (5), every term in the sum must contribute exactly 1.

To achieve this:
ceil(num / divisor) = 1 for every element.

Therefore, the divisor must be at least as large as the maximum element.

max(nums) = 44

If divisor = 43:
sum = ceil(44/43) + ceil(22/43) + ceil(33/43) + ceil(11/43) + ceil(1/43)
    = 2 + 1 + 1 + 1 + 1
    = 6 (> 5)

If divisor = 44:
sum = ceil(44/44) + ceil(22/44) + ceil(33/44) + ceil(11/44) + ceil(1/44)
    = 1 + 1 + 1 + 1 + 1
    = 5 (<= 5)

Therefore, the smallest valid divisor is 44.
```

---

# Key Property: Monotonicity of the Division Sum

```text
As Divisor increases ---> The Sum of Division Results monotonically decreases.
```

This inverse monotonic relationship lets us bypass checking every possible divisor sequentially. Instead, we can apply **Binary Search on Answers** to isolate the smallest valid divisor inside a bounded search space in `O(N × log(max(nums)))` time.

---

# Intuition

### Step 1: Define the Search Bounds

* **Minimum possible divisor (`low`)**: `1` (since the divisor must be a positive integer).
* **Maximum possible divisor (`high`)**: `max(nums)` (dividing by anything larger than the largest number does not further reduce the ceiling-division sum).
### Why Do We Use `max(nums)` as the Upper Bound?

        We choose **`max(nums)` as the upper bound** because once the divisor becomes greater than or equal to the largest element in         the array, every element contributes exactly `1` to the sum (`ceil(num / divisor) = 1`). At that point, the sum reaches its           minimum possible value, which is `nums.length`, and increasing the divisor further cannot reduce the sum any more.                    Therefore, all divisors larger than `max(nums)` produce the same result as `max(nums)` and can never be the smallest valid            answer. This guarantees that the required divisor must lie within the range **`[1, max(nums)]`**, making `max(nums)` the              optimal upper bound for binary search.
        
        ### Example 1
        
        ```text
        nums = [1, 2, 5, 9]
        max(nums) = 9
        
        divisor = 9
        ceil(1/9) + ceil(2/9) + ceil(5/9) + ceil(9/9)
        = 1 + 1 + 1 + 1
        = 4
        
        divisor = 10
        = 1 + 1 + 1 + 1
        = 4
        
        divisor = 100
        = 1 + 1 + 1 + 1
        = 4
        ```
        
        Notice that once the divisor reaches `9`, increasing it further does not change the sum.
        
        ### Example 2
        
        ```text
        nums = [8]
        max(nums) = 8
        
        divisor = 8   -> ceil(8/8)   = 1
        divisor = 9   -> ceil(8/9)   = 1
        divisor = 100 -> ceil(8/100) = 1
        ```
        
        Again, every divisor greater than `8` behaves exactly the same as `8`.
        
        ### Key Observation
        
        ```text
        For any num:
        
        if divisor >= num
        then ceil(num / divisor) = 1
        ```
        
        Since every array element becomes `1` when the divisor is at least the maximum element, searching beyond `max(nums)` is unnecessary.
        
        ### Binary Search Range
        
        ```java
        int low = 1;
        int high = max(nums);
        ```
        
        This ensures that:
        
        - The answer is guaranteed to be within the search space.
        - No unnecessary divisors are considered.
        - The binary search range remains as small and efficient as possible.

### Step 2: Binary Search Mechanics

We look for the leftmost valid divisor using a binary search loop:

1. Find the midpoint divisor:

   ```text
   mid = low + (high - low) / 2
   ```

2. Compute the ceiling-division sum for `mid` across all elements.

3. **Case 1: sum <= threshold (Valid Divisor)**

   ```text
   Current divisor works.
   Record it as a potential answer.
   Try finding an even smaller valid divisor by moving left.
   ```

   ```java
   high = mid - 1;
   ```

4. **Case 2: sum > threshold (Invalid Divisor)**

   ```text
   Current divisor is too small.
   The division results are too large, causing the sum to exceed the threshold.
   Move right to try a larger divisor.
   ```

   ```java
   low = mid + 1;
   ```

---

# Java Implementation

```java
class Solution {
    public int findAnswer(int mid,int[] nums)
    {
        int val=0;
        for(int i=0;i<nums.length;i++)
        {
            //val+=(int)Math.ceil((double)nums[i]/mid);
            val += (nums[i] + mid - 1) / mid;       //even better which avoids floating point operations.
        }
        return val;
    }
    public int smallestDivisor(int[] nums, int threshold) {
        int n=nums.length;
        int low=1;
        int high = nums[0];
        for (int num : nums) 
            high = Math.max(high, num);
        int res=0;
        int rangeStart=nums[0];
        int rangeEnd=nums[n-1];
        while(low<=high)
        {
            int mid=low+(high-low)/2;
            int val=findAnswer(mid,nums);
            if(val>threshold)
                low=mid+1;
            else
            {
                res=mid;
                high=mid-1;
            }
        }
        return res;
    }
}
```

# Why Do We Add `b - 1` in the Formula?

The key question is:

> Why do we add exactly `b - 1` and not `b`, `b - 2`, or some other number?

Let's discover it step by step.

---

## Goal

We want:

```text
ceil(a / b)
```

using integer division.

Suppose:

```text
a = 7
b = 3
```

Normal integer division:

```text
7 / 3 = 2
```

But we want:

```text
ceil(7/3) = 3
```

So we need a way to force the answer to become `3`.

---

## What Is the Smallest Amount We Can Add?

Current number:

```text
7
```

To make integer division return `3`, the numerator must become at least:

```text
3 × 3 = 9
```

So we need:

```text
7 + x = 9

x = 2
```

Notice:

```text
2 = b - 1
```

because:

```text
3 - 1 = 2
```

---

## Another Example

```text
a = 8
b = 3
```

Current result:

```text
8 / 3 = 2
```

Need:

```text
3
```

To reach the next multiple of 3:

```text
8 + 1 = 9
```

Need to add:

```text
1
```

But we cannot know the remainder beforehand.

If we always add:

```text
b - 1 = 2
```

then:

```text
8 + 2 = 10

10 / 3 = 3
```

Still correct.

---

## Why Not Add `b`?

Suppose:

```text
a = 9
b = 3
```

Actual answer:

```text
ceil(9 / 3) = 3
```

If we add `b`:

```text
(9 + 3) / 3

= 12 / 3

= 4
```

❌ Wrong

We overshot.

So adding `b` is too much.

---

## Why Not Add `b - 2`?

Take:

```text
a = 7
b = 3
```

Add:

```text
b - 2 = 1
```

Then:

```text
(7 + 1) / 3

= 8 / 3

= 2
```

❌ Wrong

We didn't reach the next bucket.

So `b - 2` is not enough.

---

## Why `b - 1` Is Perfect

Consider any remainder:

```text
a = q × b + r
```

where:

```text
1 ≤ r ≤ b - 1
```

The largest possible remainder is:

```text
b - 1
```

If we always add:

```text
b - 1
```

then every non-zero remainder gets pushed across the next multiple of `b`.

Example with:

```text
b = 5
```

```text
11 = 2 × 5 + 1
12 = 2 × 5 + 2
13 = 2 × 5 + 3
14 = 2 × 5 + 4
```

Add:

```text
b - 1 = 4
```

```text
11 + 4 = 15
12 + 4 = 16
13 + 4 = 17
14 + 4 = 18
```

Integer divide by 5:

```text
15 / 5 = 3
16 / 5 = 3
17 / 5 = 3
18 / 5 = 3
```

All correctly become:

```text
ceil(11 / 5)
ceil(12 / 5)
ceil(13 / 5)
ceil(14 / 5)
```

---

## Visual Intuition

For:

```text
b = 4
```

Ceiling buckets:

```text
1  2  3  4  → 1
5  6  7  8  → 2
9 10 11 12  → 3
```

Adding:

```text
b - 1 = 3
```

shifts the numbers:

```text
1 → 4
2 → 5
3 → 6
4 → 7

5 → 8
6 → 9
7 → 10
8 → 11
```

Now integer division by 4 naturally gives:

```text
4 / 4  = 1
5 / 4  = 1
6 / 4  = 1
7 / 4  = 1

8 / 4  = 2
9 / 4  = 2
10 / 4 = 2
11 / 4 = 2
```

which matches the ceiling buckets perfectly.

---

## Mathematical Proof

Let:

```text
a = q × b + r
```

where:

```text
0 ≤ r < b
```

### Case 1: Exact Division (`r = 0`)

```text
(a + b - 1) / b

= (q × b + b - 1) / b

= q
```

So:

```text
ceil(a / b) = q
```

✅ Correct

---

### Case 2: Division Has a Remainder (`r > 0`)

```text
(a + b - 1) / b

= (q × b + r + b - 1) / b
```

Since:

```text
1 ≤ r ≤ b - 1
```

then:

```text
b ≤ r + b - 1 ≤ 2b - 2
```

Thus:

```text
(r + b - 1) / b = 1
```

using integer division.

Therefore:

```text
(q × b + r + b - 1) / b

= q + 1
```

which is exactly:

```text
ceil(a / b)
```

✅ Correct

---

## The One-Line Intuition

```text
b - 1 is the largest possible remainder.
```

Adding it guarantees that:

- Exact multiples stay in the same bucket.
- Every non-exact division gets pushed into the next bucket.

That is exactly what `ceil()` does.

Therefore:

```text
ceil(a / b)
=
(a + b - 1) / b
```

for all positive integers `a` and `b`.

---

## Interview Shortcut

Whenever you see:

```java
Math.ceil((double)a / b)
```

for positive integers, replace it with:

```java
(a + b - 1) / b
```

For example:

```java
sum += (num + divisor - 1) / divisor;
```

is equivalent to:

```java
sum += Math.ceil((double) num / divisor);
```

but uses only integer arithmetic.

### Complexity Analysis

* **Time Complexity:** (O(N log(max(nums)))) where (N) is the number of elements in `nums`. The binary search space takes (O(log(max(nums)))) iterations to collapse. In each iteration, we run a linear scan over all (N) elements inside the `calculateSum` helper function.
* **Space Complexity:** (O(1)) auxiliary space. All operational logic checks execute entirely in-place without using extra linear collections.
