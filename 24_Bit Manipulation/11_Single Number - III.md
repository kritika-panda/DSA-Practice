# Single Number III

## Problem Statement

Given an integer array `nums`, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once. You may return the answer in **any order**.

You must write an algorithm that runs in linear runtime complexity and uses only constant extra space.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums = [1, 2, 1, 3, 2, 5]
```

**Output**

```java
[3, 5]
```

**Explanation**

The numbers `3` and `5` appear once, while `1` and `2` appear twice. `[5, 3]` is also a completely valid answer.

---

### Example 2

**Input**

```java
nums = [-1, 0]
```

**Output**

```java
[-1, 0]
```

---

### Example 3

**Input**

```java
nums = [0, 1]
```

**Output**

```java
[0, 1]
```

---

## Brute Force Approach

Track the frequency of all integers using a Hash Map or a sorted list window, extracting the elements whose frequency equals `1`.

### Steps

1. Create a `HashMap<Integer, Integer>` to track frequencies.
2. Iterate through the `nums` array and log the count of each element.
3. Traverse the map keys, filter out the two entries with a count of `1`, place them into an array of size 2, and return it.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates dynamic space for unique array elements
```

While a map tracking pass satisfies linear execution boundaries, it violates the strict **O(1) constant extra space** requirement. We can resolve this optimally without extra allocations by using bitwise mask partitioning.

---

# Optimal Approach: XOR Separation via Rightmost Set Bit

## Key Idea

If we XOR all the numbers in the array together, all elements appearing twice cancel each other out to `0`. We are left with a combined global XOR snapshot of our two unique numbers: 

```text
xyXor = x ^ y
```

Since x ≠ y, `xyXor` is guaranteed to be non-zero, meaning it contains at least one set bit (`1`). A set bit here implies that at this specific position, the bit of x and the bit of y are **different** (one has a `1`, the other has a `0`).

We can use this distinct bit position to partition our elements into two separate groups:
1. **Group 1:** Elements that have a `1` at this specific bit position.
2. **Group 2:** Elements that have a `0` at this specific bit position.

### The Separation Step
We isolate the rightmost set bit of `xyXor` using the two's complement identity: `rightmostSetBit = xyXor & (-xyXor)`. 
We then loop through the `nums` array again and use this single-bit mask to segregate the elements into two buckets, tracking their cumulative XOR products independently:
- All duplicate elements will still fall into the exact same bucket and cancel each other out completely.
- x and y are guaranteed to fall into separate buckets, isolating them completely in-place.

---

## Visual Understanding

Suppose `nums = [1, 2, 1, 3, 2, 5]`:

1. **Global XOR Accumulation:**
   - `xyXor = 1 ^ 2 ^ 1 ^ 3 ^ 2 ^ 5 = 3 ^ 5 = 6` (`0110` in binary).

2. **Isolate Rightmost Set Bit:**
   - `rightmostSetBit = 6 & (-6) = 0110 & 1010 = 0010` (`2` in decimal).
   - This means x and y differ at bit position 1.

3. **Bucket Partition Sweep:**
   - Elements with bit 1 set: `[2, 3, 2]`. XORing them yields: `2 ^ 3 ^ 2 = 3`. (Isolates x = 3)
   - Elements with bit 1 unset: `[1, 1, 5]`. XORing them yields: `1 ^ 1 ^ 5 = 5`. (Isolates y = 5)

The two unique numbers are safely separated without extra memory allocations.

---

## Partition Variables

Let:

```java
int xyXor = 0;
for (int num : nums) xyXor ^= num;

int rightmostSetBit = xyXor & (-xyXor);
int[] result = new int[2];
```

---

### Border Elements

The expression securely handles minimum and maximum integer values. Negative bit patterns under Two's Complement encoding conform smoothly to the rightmost bit-isolation formula.

---

## Correct Partition Condition

The linear array pass segments elements into two independent buckets based on the single-bit mask condition:

```java
for (int num : nums) {
    if ((num & rightmostSetBit) == 0) {
        result[0] ^= num; // Accumulate first independent bucket
    } else {
        result[1] ^= num; // Accumulate second independent bucket
    }
}
```

---

## Java Solution

```java
class Solution {

    public int[] singleNumber(int[] nums) {
        // Step 1: XOR all elements together to get the combined (x ^ y) value
        int xyXor = 0;
        for (int num : nums) {
            xyXor ^= num;
        }

        // Step 2: Isolate the rightmost set bit of the combined XOR result
        // This bit position represents a point where x and y differ
        int rightmostSetBit = xyXor & (-xyXor);

        int[] result = new int[2];

        // Step 3: Partition the elements into two buckets using the isolated bit mask
        for (int num : nums) {
            if ((num & rightmostSetBit) == 0) {
                result[0] ^= num; // Bucket 0: bit is unset
            } else {
                result[1] ^= num; // Bucket 1: bit is set
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise Two-Pass Sweep** | **O(n)** | **O(1)** | Performs exactly two linear passes through the array while using constant primitive memory registers in-place. |

---

## Key Insight

Using a single-bit difference mask allows an array to be split into two independent XOR buckets in-place, causing duplicates to cancel out while isolating multiple unique values simultaneously.

```text
Time  : O(n) double pass execution
Space : O(1) absolute constant extra space
```

---

## Similar Problems

1. Single Number (136) - *One unique element, others appear twice*
2. Single Number II (137) - *One unique element, others appear three times*
3. Missing Number (268)
4. Find the Duplicate Number (287)
5. Hamming Distance (461)
