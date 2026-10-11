# Minimum Bit Flips to Convert Number

## Problem Statement

A **bit flip** of a number consists of choosing a bit in the binary representation and flipping it from `0` to `1` or from `1` to `0`.

Given two integers `start` and `goal`, return *the **minimum number of bit flips** to convert `start` to `goal`*.

The overall runtime complexity must be strictly bounded by:

```text
O(k) -> O(1) worst-case
```

*(where k is the total number of mismatched bits, at most 32 for standard integers)*

---

## Examples

### Example 1

**Input**

```java
start = 10 // Binary: 1010
goal = 7   // Binary: 0111
```

**Output**

```java
3
```

**Explanation**

The binary representations of `10` and `7` are:
- `start` = `1010`
- `goal`  = `0111`

We need to flip the bits at positions 0, 1, and 3 to convert `10` to `7`. This requires `3` bit flips.

---

### Example 2

**Input**

```java
start = 3 // Binary: 011
goal = 4  // Binary: 100
```

**Output**

```java
3
```

**Explanation**

The binary representations are `011` and `100`. All three bit positions differ, so it requires `3` bit flips.

---

### Example 3

**Input**

```java
start = 10
goal = 10
```

**Output**

```java
0
```

---

## Brute Force Approach

Iterate through all 32 possible bit positions, extracting and comparing the bits of both numbers one by one.

### Steps

1. Initialize a `flipCount` variable to 0.
2. Run a loop from `0` up to `31`.
3. In each iteration, isolate the `i`-th bit of both numbers:
   - `bitStart = (start >> i) & 1`
   - `bitGoal = (goal >> i) & 1`
4. If `bitStart != bitGoal`, increment `flipCount`.
5. Return the final `flipCount`.

### Complexity

```text
Time Complexity: O(32) -> O(1) constant time
Space Complexity: O(1)
```

Although this runs in constant time, it is inefficient because it **always** executes exactly 32 loops, even if the numbers are identical or have very few mismatches. We can optimize this by leveraging the bit-clearing properties of XOR and Brian Kernighan's Algorithm.

---

# Optimal Approach: XOR + Brian Kernighan’s Bit Clearing

## Key Idea

We can solve this problem optimally in two steps by combining the properties of **XOR (`^`)** and **Brian Kernighan's Algorithm**:

1. **Isolate Mismatched Bits (XOR):** The bitwise XOR operator returns a `1` if and only if the two input bits are **different**. By executing `mismatch = start ^ goal`, we generate a new integer where each set bit (`1`) represents an exact position where `start` and `goal` differ. The problem then simplifies to counting the number of set bits in `mismatch`.
2. **Count Set Bits Efficiently:** Instead of looping 32 times, we use Brian Kernighan's expression `mismatch = mismatch & (mismatch - 1)` inside a loop. Each cycle clears exactly one rightmost set bit. The loop terminates instantly as soon as `mismatch` becomes 0.

---

## Visual Understanding

Suppose `start = 10` (`1010`) and `goal = 7` (`0111`):

1. **Apply Bitwise XOR:**
   ```text
     1010  (start = 10)
   ^ 0111  (goal = 7)
     ----
     1101  (mismatch = 13 -> 3 set bits)
   ```

2. **Run Brian Kernighan's Algorithm on `13` (`1101`):**
   - **Cycle 1:** `1101 & 1100 = 1100` (`12`). First bit cleared. `count = 1`.
   - **Cycle 2:** `1100 & 1011 = 1000` (`8`). Second bit cleared. `count = 2`.
   - **Cycle 3:** `1000 & 0111 = 0000` (`0`). Third bit cleared. `count = 3`.

The loop terminates after exactly 3 steps.

---

## Partition Variables

Let:

```java
int mismatch = start ^ goal;
int count = 0;
```

---

### Border Elements

The XOR operation handles all numeric boundaries seamlessly, operating safely within the maximum capacity bounds of 32-bit signed integers.

---

## Correct Partition Condition

The counting loop processes updates dynamically until the bit-clearing steps reduce the mismatch register to zero:

```java
while (mismatch > 0) {
    mismatch = mismatch & (mismatch - 1);
    count++;
}
```

---

## Java Solution

```java
class Solution {

    public int minBitFlips(int start, int goal) {
        // Step 1: Isolate the mismatched bits using bitwise XOR
        int mismatch = start ^ goal;
        int count = 0;

        // Step 2: Count the set bits using Brian Kernighan's Algorithm
        while (mismatch > 0) {
            mismatch = mismatch & (mismatch - 1); // Clears the rightmost set bit
            count++;                              // Increment count for each bit cleared
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
| **XOR + Bit Clearing** | **O(k)** | **O(1)** | The loop executes exactly `k` times, where `k` is the number of mismatched bits between `start` and `goal`. |

---

## Key Insight

Using an XOR gate isolates mismatches into a single binary mask instantly, and Brian Kernighan's trick clears those active bits dynamically to bypass empty zero blocks completely.

```text
Time  : O(k) linear relative to mismatched bits
Space : O(1) in-place register mutation
```

---

## Similar Problems

1. Number of 1 Bits / Hamming Weight (191)
2. Counting Bits (338)
3. Single Number (136)
4. Toggle the i-th Bit
5. Hamming Distance (461)
