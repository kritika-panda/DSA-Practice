# Set the Rightmost Unset Bit

## Problem Statement

Given a positive integer `n`, set the **rightmost unset bit** (the first `0` bit from the right) to `1`. If there are no unset bits in the number (i.e., the number is of the form \(2^k - 1\) and consists entirely of `1`s), return the number unchanged.

The overall runtime complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
n = 6 // Binary: 0110
```

**Output**

```java
7 // Binary: 0111
```

**Explanation**

The binary representation of `6` is `0110`. 
- The rightmost unset bit (`0`) is at position 0.
- Setting it to `1` transforms the binary string into `0111`, which equals `7`.

---

### Example 2

**Input**

```java
n = 10 // Binary: 1010
```

**Output**

```java
11 // Binary: 1011
```

**Explanation**

The binary representation of `10` is `1010`. The rightmost `0` is at position 0. Flipping it to `1` yields `1011` (`11`).

---

### Example 3

**Input**

```java
n = 7 // Binary: 0111
```

**Output**

```java
7 // Binary: 0111
```

**Explanation**

The binary representation of `7` consists entirely of set bits (`0111`). Since there are no nested internal unset bits within its active span, the value remains unchanged.

---

## Brute Force Approach

Iterate through the bit positions from right to left starting from index 0, locate the first bit that is `0`, and set it using a mask.

### Steps

1. Run a loop with index `i` from `0` to `31`.
2. Check if the `i`-th bit is unset using a bitwise AND mask: `(n & (1 << i)) == 0`.
3. If the bit is found to be `0`:
   - Set it to `1` using a bitwise OR mask: `n = n | (1 << i)`.
   - Break the loop and return `n`.
4. If the loop completes without finding an unset bit, return `n`.

### Complexity

```text
Time Complexity: O(32) -> O(1) constant time
Space Complexity: O(1)
```

While this runs in constant time asymptotically, the loop forces multiple checking paths. We can complete this task in a **single CPU clock cycle** using a clever arithmetic-bitwise combination.

---

# Optimal Approach: Bitwise OR with `n + 1`

## Key Idea

We can set the rightmost unset bit instantly using the expression:

```text
n | (n + 1)
```

### The Mechanics
When we add `1` to an integer `n`, look at what happens to its binary representation:
- The addition forces a carry-over that passes through all trailing `1` bits, flipping them from `1` to `0`.
- The carry-over stops the exact moment it hits the **rightmost unset bit** (`0`), turning that `0` into a `1`.
- Any bits higher than this rightmost unset bit remain completely unchanged.

For example, if `n = 6` (`0110`):
- `n + 1 = 7` (`0111`).

If we perform a bitwise `OR` (`|`) between `n` and `n + 1`, we combine their set bits:
- The higher bits stay exactly as they were.
- The trailing bits that flipped to `0` in `n + 1` are restored to `1` because they were `1` in our original `n`.
- The rightmost unset bit—which became `1` in `n + 1`—is safely locked into a `1` state.

This solves the entire problem in a single step. If `n` has no unset bits (e.g., `7` or `0111`), `n + 1` becomes `8` (`1000`). Performing `7 | 8` results in `15` (`1111`), which changes the number. To protect against this edge case, we can use an inline condition to ensure numbers of the form \(2^k - 1\) are left untouched.

---

## Visual Understanding

Suppose `n = 6` (`0110` in binary):

1. Compute `n + 1`: `6 + 1 = 7` (`0111` in binary).
2. Apply a bitwise OR operation:
   ```text
     0110  (n = 6)
   | 0111  (n + 1 = 7)
     ----
     0111  (Result is 7)
   ```

The rightmost unset bit at index 0 was flipped to `1` cleanly.

---

## Partition Variables

Let:

```java
int nextNum = n + 1;
```

---

### Border Elements

To handle edge cases where a number consists entirely of `1`s (like `7` or `15`), we can use the power-of-two check variant `((n + 1) & n) == 0` to identify them early and skip alterations.

---

## Correct Partition Condition

The complete bitwise expression transforms the target index threshold instantly:

```java
return ((n + 1) & n) == 0 ? n : n | (n + 1);
```

---

## Java Solution

```java
class Solution {

    public int setRightmostUnsetBit(int n) {
        // If n consists entirely of 1s (e.g., 7 -> 0111), n + 1 is a power of 2 (8 -> 1000).
        // In that case, ((n + 1) & n) will be exactly 0, and we return n unchanged.
        if (((n + 1) & n) == 0) {
            return n;
        }

        // Otherwise, a bitwise OR with (n + 1) sets the rightmost unset bit
        return n | (n + 1);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise OR Expression** | **O(1)** | **O(1)** | Executes as a single-cycle low-level operation directly within the ALU hardware. |

---

## Key Insight

Leveraging the natural arithmetic carry-over behavior of `n + 1` targets the first available `0` bit automatically, avoiding sequential bit scans entirely.

```text
Time  : O(1) single clock cycle step
Space : O(1) in-place register mutation
```

---

## Similar Problems

1. Clear the Rightmost Set Bit (`n & (n - 1)`)
2. Isolate the Rightmost Set Bit (`n & (-n)`)
3. Check if a Number is a Power of Two
4. Toggle the i-th Bit
5. Check if the i-th Bit is Set or Not
