# Largest Number

## Problem Statement

Given a list of non-negative integers `nums`, arrange them such that they form the largest number and return it.

Since the result may be very large, return it as a string.

### Example 1

**Input:**

```java
nums = [10,2]
```

**Output:**

```java
"210"
```

### Example 2

**Input:**

```java
nums = [3,30,34,5,9]
```

**Output:**

```java
"9534330"
```

### Example 3

**Input:**

```java
nums = [0,0]
```

**Output:**

```java
"0"
```

---

# Intuition

A normal descending sort does not work.

Consider:

```java
[3, 30]
```

If sorted numerically:

```java
30, 3
```

Result:

```java
303
```

But:

```java
3, 30
```

Produces:

```java
330
```

which is larger.

So for any two numbers `a` and `b`, we need to determine whether:

```java
ab > ba
```

If yes, place `a` before `b`.

---

# Key Observation

For two numbers:

```java
a = "3"
b = "30"
```

Compare:

```java
"330"
```

with

```java
"303"
```

Since:

```java
"330" > "303"
```

place:

```java
3 before 30
```

---

# Approach (Custom Sorting)

1. Convert all integers into strings.
2. Sort strings using a custom comparator:
   - Compare `(b + a)` and `(a + b)`.
   - If `(b + a)` is larger, place `b` first.
3. Concatenate all strings.
4. Handle edge case:
   - If the largest element is `"0"`, return `"0"`.

---

# Dry Run

### Input

```java
nums = [3,30,34,5,9]
```

Convert to strings:

```java
["3","30","34","5","9"]
```

Sorting comparisons:

```java
"9"+"5" = "95"
"5"+"9" = "59"
```

⇒ 9 comes before 5

```java
"34"+"3" = "343"
"3"+"34" = "334"
```

⇒ 34 comes before 3

Sorted array:

```java
["9","5","34","3","30"]
```

Concatenate:

```java
"9534330"
```

---

# Java Solution (Optimal)

```java
import java.util.*;

class Solution {
    public String largestNumber(int[] nums) {

        String[] arr = new String[nums.length];

        for (int i = 0; i < nums.length; i++) {
            arr[i] = String.valueOf(nums[i]);
        }

        Arrays.sort(arr, (a, b) -> (b + a).compareTo(a + b));

        if (arr[0].equals("0")) {
            return "0";
        }

        StringBuilder result = new StringBuilder();

        for (String num : arr) {
            result.append(num);
        }

        return result.toString();
    }
}
```

---

# Example Walkthrough

### Input

```java
nums = [10,2]
```

Compare:

```java
"210"
```

and

```java
"102"
```

Since:

```java
210 > 102
```

Sorted order:

```java
["2","10"]
```

Result:

```java
"210"
```

---

# Why Check for Leading Zero?

Consider:

```java
nums = [0,0,0]
```

After sorting:

```java
["0","0","0"]
```

Concatenation gives:

```java
"000"
```

Expected:

```java
"0"
```

Hence:

```java
if (arr[0].equals("0"))
    return "0";
```

---

# Complexity Analysis

### Sorting

Let:

- `n` = number of elements
- `k` = average number of digits

Custom comparison takes:

```java
O(k)
```

Sorting takes:

```java
O(n log n)
```

Overall:

```java
Time Complexity: O(n log n * k)
```

```java
Space Complexity: O(n)
```

---

# Why This Works?

For every pair `(a, b)`:

- If `ab > ba`, placing `a` before `b` always contributes to a larger final number.
- Sorting all elements according to this rule guarantees the globally largest concatenated number.

---

# Key Takeaways

- Numerical sorting is incorrect.
- Compare concatenations:
  - `a + b`
  - `b + a`
- Use a custom comparator to sort strings.
- Handle the all-zero case separately.
- Time Complexity: **O(n log n × k)**
- One of the most important custom-sorting interview problems asked by Amazon, Google, Microsoft, and Meta.
