# Count the Number of Set Bits (Hamming Weight)

## Problem Statement

Given a positive integer `n`, return the total number of **set bits** (`1`s) present in its binary representation. This count is also known as the **Hamming Weight** of the integer.

The overall runtime complexity should be bounded by:

```text
O(k) -> O(1) worst-case
```

*(where k is the total number of set bits, at most 32 for standard integers)*

---

## Examples

### Example 1

**Input**

```java
n = 11 // Binary: 1011
```

**Output**

```java
3
```

**Explanation**

The binary representation of `11` is `1011`. It contains three `1` bits, so the answer is `3`.

---

### Example 2

**Input**

```java
n = 128 // Binary: 10000000
```

**Output**

```java
1
```

**Explanation**

`128` is a power of 2, so its binary representation contains exactly one set bit.

---

### Example 3

**Input**

```java
n = 0 // Binary: 0000
```

**Output**

```java
0
```

---

## Brute Force Approach

Iterate through all 32 possible bit positions, shifting a mask to inspect every bit individually.

### Steps

1. Initialize a `count` variable to 0.
2. Run a loop from `0` to `31`.
3. In each iteration, check if the `i`-th bit is set using a mask: `(n & (1 << i)) != 0`.
4. If it is set, increment `count`.
5. Return the final `count`.

### Complexity

```text
Time Complexity: O(32) -> O(1) constant time
Space Complexity: O(1)
```

While this approach runs in constant time, it is inefficient because it **always** executes exactly 32 loops, even if the number is small or has very few set bits (e.g., `n = 1` takes 32 checks). We can optimize this using a greedy loop that only triggers for actual set bits.

---

# Optimal Approach: Brian Kernighan’s Algorithm

## Key Idea

We can count the set bits much faster using **Brian Kernighan’s Algorithm**. Instead of inspecting every bit position, this algorithm uses a clear bitwise trick to strip out the rightmost set bit in each step:

```text
n = n & (n - 1)
```

### The Mechanics
When we subtract 1 from an integer `n`, all the trailing 0-bits up to the rightmost 1-bit flip to 1, and the rightmost 1-bit itself flips to 0. 
- For example, if `n = 12` (`1100`), then `n - 1 = 11` (`1011`).

If we perform a bitwise `AND` between `n` and `n - 1`, all the bits before the rightmost 1-bit remain unchanged, while the rightmost 1-bit and everything after it get zeroed out completely. 

By running this expression inside a loop, **each loop cycle clears exactly one set bit**. The loop terminates instantly as soon as `n` becomes 0, meaning the total number of iterations matches the exact number of set bits `k`.

---

## Visual Understanding

Suppose `n = 11` (`1011` in binary):

- **Iteration 1:**
  - `n = 11` (`1011`), `n - 1 = 10` (`1010`)
  - `n & (n - 1) = 1011 & 1010 = 1010` (`10`). One bit cleared. `count = 1`.
- **Iteration 2:**
  - `n = 10` (`1010`), `n - 1 = 9` (`1001`)
  - `n & (n - 1) = 1010 & 1001 = 1000` (`8`). Second bit cleared. `count = 2`.
- **Iteration 3:**
  - `n = 8` (`1000`), `n - 1 = 7` (`0111`)
  - `n & (n - 1) = 1000 & 0111 = 0000` (`0`). Third bit cleared. `count = 3`.

`n` is now 0. The loop terminates immediately after 3 cycles instead of running 32 times.

---

## Partition Variables

Let:

```java
int count = 0;
```

---

### Border Elements

The algorithm functions seamlessly across all numeric boundaries, working safely up to the maximum constraints of standard 32-bit integer values.

---

## Correct Partition Condition

The evaluation loop updates the variable state actively until the clearing steps drive the integer value to a zero-point threshold:

```java
while (n > 0) {
    n = n & (n - 1);
    count++;
}
```

---

## Java Solution

```java
class Solution {

    public int countSetBits(int n) {
        int count = 0;

        // Brian Kernighan's Algorithm
        while (n > 0) {
            n = n & (n - 1); // Clears the absolute lowest set bit (rightmost 1)
            count++;        // Increment count for each bit cleared
        }

        return count;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Brian Kernighan Loop** | **O(k)** | **O(1)** | The loop runs exactly `k` times, where `k` is the number of active set bits inside `n`. |

---

## Key Insight

Clearing the lowest active set bit dynamically in each loop step allows the algorithm to skip over vast blocks of empty zeros completely, reducing lookups to match the exact density of set bits.

```text
Time  : O(k) linear relative to set bits count
Space : O(1) in-place register mutation
```

---

## Similar Problems

1. Number of 1 Bits / Hamming Weight (191)
2. Counting Bits (338)
3. Power of Two (231)
4. Check if the i-th Bit is Set or Not
5. Reverse Bits (190)
