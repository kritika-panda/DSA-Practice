# Find the K-Beauty of a Number

## Problem Statement

The **k-beauty** of an integer `num` is defined as the number of substrings of `num` (when read as a string) that meet the following conditions:
1. It has a length of `k`.
2. It is a **divisor** of `num`.

Given integers `num` and `k`, return the **k-beauty** of `num`.

### Notes
* **Leading zeros** are allowed in substrings (e.g., `"04"` is treated as the integer `4`).
* **0 is not a divisor** of any value. If a substring converts to `0`, it must be skipped to avoid a division-by-zero error.
* A **substring** is a contiguous sequence of characters within a string.

---

## Examples

### Example 1

```text
Input: num = 240, k = 2
Output: 2
```

### Explanation

```text
The substrings of "240" with a length of 2 are:
- "24": 24 is a divisor of 240 (240 % 24 == 0). ✔
- "40": 40 is a divisor of 240 (240 % 40 == 0). ✔

Therefore, the k-beauty is 2.
```

---

### Example 2

```text
Input: num = 430043, k = 2
Output: 2
```

### Explanation

```text
The substrings of "430043" with a length of 2 are:
- "43": 43 is a divisor of 430043 (430043 % 43 == 0). ✔
- "30": 30 is not a divisor of 430043. ❌
- "00": Evaluates to 0, which is not a valid divisor. ❌
- "04": Evaluates to 4, which is not a divisor of 430043. ❌
- "43": 43 is a divisor of 430043 (430043 % 43 == 0). ✔

Therefore, the k-beauty is 2.
```

---

# Key Concept: Fixed-Size Sliding Window

```text
String:  [ 2  4 ] 0
Indices:   i   i+k
```

This problem represents a classic **fixed-size sliding window** or **substring generation** problem over strings. Instead of looking for dynamic bounds, the window size is tightly constrained to exactly `k` characters at all times.

---

# Intuition

Since we need to inspect the individual digits sequentially, converting the integer `num` into its string representation `s` simplifies character grouping.

We slide a window of size `k` across the string from index `0` up to `s.length() - k`:
1. Extract the current substring chunk of length `k`: `s.substring(i, i + k)`.
2. Parse the extracted string into an integer.
3. Check the mathematical division criteria:
   * Ensure the parsed integer is **not equal to 0** to safeguard against arithmetic faults.
   * Check if `num % parsed_integer == 0`.
4. If both checks pass, increment our valid configuration counter.

---

# Java Implementation

```java
class Solution {
    public int divisorSubstrings(int num, int k) {
        // Convert the number to a string to easily extract substrings
        String s = String.valueOf(num);
        int count = 0;
        
        // Loop through all starting positions of substrings of length k
        for (int i = 0; i <= s.length() - k; i++) {
            // Extract the substring and parse it into an integer
            int sub = Integer.parseInt(s.substring(i, i + k));
            
            // Check if the value is non-zero and divides the original number perfectly
            if (sub != 0 && num % sub == 0) {
                count++;
            }
        }
        return count;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N * k) where N is the total number of digits in `num`. The loop runs exactly \(N - k + 1\) times. Inside the loop, extracting the substring and parsing it into an integer scales with the length of the window (\(k\)).
* **Space Complexity:** O(N) or O(1) auxiliary space. Converting the integer to a string creates an object proportional to the number of digits \(N\). Beyond the string storage, the algorithm uses only a few integer pointers and tracking counters.
