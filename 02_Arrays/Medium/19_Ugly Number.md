# Ugly Number

## Problem Statement

An **ugly number** is a positive integer whose prime factors are limited to:

```java
2, 3, and 5
```

Given an integer `n`, return `true` if `n` is an ugly number, otherwise return `false`.

### Example 1

**Input:**

```java
n = 6
```

**Output:**

```java
true
```

**Explanation:**

```java
6 = 2 × 3
```

Both prime factors are among `2, 3, 5`.

---

### Example 2

**Input:**

```java
n = 1
```

**Output:**

```java
true
```

**Explanation:**

```java
1
```

has no prime factors, so it is considered an ugly number.

---

### Example 3

**Input:**

```java
n = 14
```

**Output:**

```java
false
```

**Explanation:**

```java
14 = 2 × 7
```

Prime factor `7` is not allowed.

---

# Intuition

An ugly number can contain only prime factors:

```java
2, 3, 5
```

Therefore:

- Continuously divide the number by `2` while possible.
- Continuously divide the number by `3` while possible.
- Continuously divide the number by `5` while possible.

If the final value becomes:

```java
1
```

then all prime factors were among `2, 3, 5`.

Otherwise, some other prime factor exists.

---

# Approach

1. If `n <= 0`, return `false`.
2. Divide by `2` repeatedly.
3. Divide by `3` repeatedly.
4. Divide by `5` repeatedly.
5. Check if the resulting number equals `1`.

---

# Dry Run

### Input

```java
n = 30
```

Divide by 2:

```java
30 → 15
```

Divide by 3:

```java
15 → 5
```

Divide by 5:

```java
5 → 1
```

Final value:

```java
1
```

Return:

```java
true
```

---

### Input

```java
n = 14
```

Divide by 2:

```java
14 → 7
```

Cannot divide by:

```java
3 or 5
```

Final value:

```java
7
```

Return:

```java
false
```

---

# Java Solution (Optimal)

```java
class Solution {
    public boolean isUgly(int n) {

        if (n <= 0) {
            return false;
        }

        int[] factors = {2, 3, 5};

        for (int factor : factors) {

            while (n % factor == 0) {
                n /= factor;
            }
        }

        return n == 1;
    }
}
```

---

# Alternative Implementation

```java
class Solution {
    public boolean isUgly(int n) {

        if (n <= 0) {
            return false;
        }

        while (n % 2 == 0) {
            n /= 2;
        }

        while (n % 3 == 0) {
            n /= 3;
        }

        while (n % 5 == 0) {
            n /= 5;
        }

        return n == 1;
    }
}
```

---

# Why Does This Work?

Suppose:

```java
n = 60
```

Prime factorization:

```java
60 = 2 × 2 × 3 × 5
```

Removing all occurrences of:

```java
2, 3, and 5
```

gives:

```java
1
```

Hence it is an ugly number.

For:

```java
14 = 2 × 7
```

Removing factor `2` leaves:

```java
7
```

Since `7 ≠ 1`, it is not ugly.

---

# Complexity Analysis

Let:

```java
n = given number
```

Each division reduces the number significantly.

- **Time Complexity:** `O(log n)`
- **Space Complexity:** `O(1)`

---

# Interview Follow-up: Ugly Number II

Instead of checking whether a number is ugly, find the:

```java
nth ugly number
```

Examples:

```java
1st  Ugly Number = 1
2nd  Ugly Number = 2
3rd  Ugly Number = 3
4th  Ugly Number = 4
5th  Ugly Number = 5
6th  Ugly Number = 6
...
```

This follow-up is typically solved using:

- Dynamic Programming
- Three Pointers (`i2`, `i3`, `i5`)

in **O(n)** time.

---

# Key Takeaways

- Ugly numbers contain only prime factors `2`, `3`, and `5`.
- Repeatedly divide by `2`, `3`, and `5`.
- If the remaining value becomes `1`, the number is ugly.
- Handle `n <= 0` separately.
- Optimal complexity is **O(log n)** time and **O(1)** space.
- Frequently asked as a prerequisite to **Ugly Number II**.
