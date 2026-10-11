# Single Number II

## Problem Statement

Given an integer array `nums` where every element appears **three times** except for one, which appears **exactly once**. Find the single element and return it.

You must implement an algorithm with a linear runtime complexity and use only constant extra space.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums = [2, 2, 3, 2]
```

**Output**

```java
3
```

---

### Example 2

**Input**

```java
nums = [0, 1, 0, 1, 0, 1, 99]
```

**Output**

```java
99
```

---

### Example 3

**Input**

```java
nums = [-2, -2, 1, -2]
```

**Output**

```java
1
```

---

## Brute Force Approach

Track the frequency of every element using a standard Hash Map collection and return the unique item.

### Steps

1. Create a `HashMap<Integer, Integer>` to store counts.
2. Iterate through `nums` and log the count of each element.
3. Iterate through the map keys to find the entry with a value of `1` and return it.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // breaks the constraint because it allocates space for unique items
```

While a map satisfies the linear timeline, it violates the strict **O(1) constant extra space** requirement. We can resolve this optimally by tracking the state transitions of binary bits.

---

# Optimal Approach: Bitwise State Machine (Ones & Twos)

## Key Idea

We can track occurrences in **O(n) time and O(1) space** using a digital circuit logic simulation. 

Since each number is a 32-bit integer, an element appearing three times means each of its 1-bits will appear 3 times. If an element appears only once, its 1-bits will appear exactly 1 time. 

We can track the frequency of bits using two bitmasks:
- `ones`: Tracks bits that have appeared **one time** (modulo 3).
- `twos`: Tracks bits that have appeared **two times** (modulo 3).

When a new number `num` arrives, we update our state machine using bitwise operators:
1. `twos = twos | (ones & num)`: A bit moves to `twos` if it was already in `ones` and appears again in `num`.
2. `ones = ones ^ num`: A bit flips its state in `ones` using standard XOR parity.
3. `threes = ones & twos`: If a bit is set in both `ones` and `twos`, it means it has appeared 3 times.
4. `ones = ones & ~threes`, `twos = twos & ~threes`: We clear the bits that reached a count of 3 from both `ones` and `twos` to reset their modulo-3 counters to 0.

At the end of the full array pass, the `ones` variable will hold the exact bit configuration of the element that appeared exactly once.

---

## Visual Understanding

Suppose `nums = [2, 2, 3, 2]`. Let's look at the bit changes for the value `2` (`0010` in binary):

- **Element 1 (`num = 2`):**
  - `twos` remains `0000`.
  - `ones` becomes `0000 ^ 0010 = 0010` (Tracks 1st appearance).
- **Element 2 (`num = 2`):**
  - `twos` becomes `0000 | (0010 & 0010) = 0010` (Tracks 2nd appearance).
  - `ones` becomes `0010 ^ 0010 = 0000`.
- **Element 3 (`num = 3`):** Processes `3` (`0011`). Bits are logged independently.
- **Element 4 (`num = 2`):**
  - `twos` checks: `ones` doesn't have it, so `twos & ~threes` clears it to `0000`.
  - `ones` flips: `0000 ^ 0010 = 0010`. But since it matches a 3rd appearance check, it clears to `0000`.

The duplicate elements cycle back to 0 completely, leaving the unique value `3` safely intact inside `ones`.

---

## Partition Variables

Let:

```java
int ones = 0;
int twos = 0;
```

---

### Border Elements

The bitwise logic gates naturally preserve bit signs under Two's Complement encoding, handling negative numbers effortlessly without risk of numeric overflow.

---

## Correct Partition Condition

The masks are calculated and cleared inside a single forward loop pass over the array:

```java
for (int num : nums) {
    twos |= (ones & num);
    ones ^= num;
    int threes = ones & twos;
    ones &= ~threes;
    twos &= ~threes;
}
return ones;
```

---

## Java Solution

```java
class Solution {

    public int singleNumber(int[] nums) {
        int ones = 0; // Tracks bits appearing 1 time (mod 3)
        int twos = 0; // Tracks bits appearing 2 times (mod 3)

        for (int num : nums) {
            // Bit moves to 'twos' if it was already in 'ones'
            twos |= (ones & num);
            
            // Toggle 'ones' using XOR logic
            ones ^= num;
            
            // Identify bits that have appeared 3 times
            int threes = ones & twos;
            
            // Clear the 3rd appearance bits from both tracking masks
            ones &= ~threes;
            twos &= ~threes;
        }

        return ones;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **State Machine Loop** | **O(n)** | **O(1)** | Traverses the input array exactly once while reusing two static integer masks in-place. |

---

## Key Insight

Simulating a modulo-3 loop counter using bitwise state variables clears out triplet bit patterns automatically, allowing the unique element to be isolated in-place without sorting or memory allocation overhead.

```text
Time  : O(n) single pass execution
Space : O(1) absolute constant extra memory
```

---

## Similar Problems

1. Single Number (136) - *Elements appear twice except one*
2. Single Number III (260) - *Two elements appear once, others twice*
3. Missing Number (268)
4. Find the Duplicate Number (287)
5. Counting Bits (338)
