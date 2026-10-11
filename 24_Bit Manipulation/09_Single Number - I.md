# Single Number

## Problem Statement

Given a **non-empty** array of integers `nums`, every element appears *twice* except for one. Find that single one.

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
nums = [2, 2, 1]
```

**Output**

```java
1
```

---

### Example 2

**Input**

```java
nums = [4, 1, 2, 1, 2]
```

**Output**

```java
4
```

---

### Example 3

**Input**

```java
nums = [1]
```

**Output**

```java
1
```

---

## Brute Force Approach

Track occurrences using a Hash Map or a nested loop search to locate the unique element.

### Steps

1. Create a `HashMap<Integer, Integer>` to track frequencies.
2. Iterate through `nums` and log the count of each element.
3. Iterate through the map to find the entry with a frequency count of `1` and return its key.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // requires dynamic space for up to n/2 + 1 elements
```

While this satisfies the linear runtime constraint, it violates the strict **O(1) constant extra space** requirement. We can resolve this optimally without allocating any extra space by utilizing bitwise operators.

---

# Optimal Approach: Bitwise XOR Accumulation

## Key Idea

We can solve this problem in true **O(n) time and O(1) space** by taking advantage of the properties of the bitwise **XOR (`^`)** operator.

The XOR operator follows three core mathematical identities:
1. `x ^ 0 = x` (An element XORed with 0 stays unchanged)
2. `x ^ x = 0` (An element XORed with itself cancels out to 0)
3. `x ^ y = y ^ x` (The operation is commutative and associative, meaning order does not matter)

Because of these properties, if we initialize a variable to `0` and XOR it sequentially with every element in the array, **all elements that appear twice will pair up and cancel each other out to `0`**. 

The only element left standing at the end of the entire loop will be the unique single number.

---

## Visual Understanding

Suppose `nums = [4, 1, 2, 1, 2]`:

Initialize `result = 0`. Let's accumulate XOR operations across the array elements sequentially:

```text
result = 0 ^ 4 = 4
result = 4 ^ 1
result = 4 ^ 1 ^ 2
result = 4 ^ 1 ^ 2 ^ 1  => (1 ^ 1 cancels to 0) => 4 ^ 2 ^ 0 = 4 ^ 2
result = 4 ^ 2 ^ 2      => (2 ^ 2 cancels to 0) => 4 ^ 0 = 4
```

Because order does not affect the outcome, the duplicate values wipe each other out entirely, leaving the value `4` isolated.

---

## Partition Variables

Let:

```java
int result = 0;
```

---

### Border Elements

The XOR gate properties handle positive numbers, negative numbers, and zero seamlessly without any numerical overflow risks.

---

## Correct Partition Condition

The accumulator loops from left to right through the array elements in a single forward pass:

```java
for (int num : nums) {
    result ^= num;
}
return result;
```

---

## Java Solution

```java
class Solution {

    public int singleNumber(int[] nums) {
        int result = 0;

        // XORing all elements together cancels out the duplicates
        for (int num : nums) {
            result ^= num;
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
| **XOR Accumulator** | **O(n)** | **O(1)** | Traverses the input array exactly once while reusing a single integer register in-place. |

---

## Key Insight

Leveraging the self-canceling property of the XOR logic gate reduces a frequency tracking problem into a constant-space math accumulation pass.

```text
Time  : O(n) single pass execution
Space : O(1) absolute constant extra memory
```

---

## Similar Problems

1. Single Number II (137) - *Elements appear three times except one*
2. Single Number III (260) - *Two elements appear once, others twice*
3. Missing Number (268)
4. Find the Duplicate Number (287)
5. Swap Two Numbers
