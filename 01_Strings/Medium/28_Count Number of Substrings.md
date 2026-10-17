# Count Number of Substrings

**GFG / Classic Advanced Interview Problem - Substrings with K Distinct Characters**

## Problem Statement

Given a string `s` of lowercase alphabets and an integer `k`, return the **total number of substrings** that contain **exactly \(k\) distinct characters**.

A **substring** is a contiguous sequence of characters within a string.

---

## Example

```java
String s = "abaaca";
int k = 1;

Solution sol = new Solution();
sol.countSubstrings(s, k); // Returns 7
```

**Explanation:** The substrings containing exactly `1` distinct character are:
`"a"` (at index 0), `"b"` (at index 1), `"a"` (at index 2), `"aa"` (indices 2-3), `"a"` (at index 3), `"c"` (at index 4), and `"a"` (at index 5). 
Therefore, the total count is `7`.

---

# Intuition

A brute force approach would look at every possible substring by using two nested loops, counting the distinct characters using a hash set or a frequency array. This costs \(O(N^2)\) time, which will result in a Time Limit Exceeded (TLE) error for large strings.

To optimize this to linear time, we can think about a **Sliding Window (Two-Pointer)** approach. However, standard sliding window techniques excel at finding substrings with **at most** \(k\) distinct characters, because expanding the window monotonically increases or keeps the distinct character count the same. Finding **exactly** \(k\) distinct characters directly via a single sliding window is notoriously tricky because shrinking the window doesn't always cleanly match an equality constraint.

We can solve this elegantly using a standard mathematical trick:

\[\text{Count}(\text{Exactly } k) = \text{Count}(\text{At Most } k) - \text{Count}(\text{At Most } k - 1)\]

By creating a helper function that counts substrings with **at most** \(k\) distinct characters using a dynamic sliding window, we can run it twice to compute our exact boundary delta in \(O(N)\) time.

---

# Approach: Sliding Window Delta Optimization

## Data Structure

```java
int[] freq = new int[26]
```
An integer array tracking character frequencies inside the current window, replacing an expensive Map since the input is strictly limited to lowercase English letters (`26` elements).

---

## Algorithm Steps

1. Create a helper function `countAtMost(s, k)` which calculates the number of substrings containing **at most** \(k\) distinct characters.
2. Inside `countAtMost`:
   - Initialize two pointers `left = 0` and `right = 0`, a `distinctCount = 0`, and a `totalSubstrings = 0`.
   - Slide `right` from `0` to `N - 1`:
     - Add `s.charAt(right)` to the frequency array. If its frequency becomes `1`, increment `distinctCount`.
     - While `distinctCount > k`, shrink the window from the left: decrement the frequency of `s.charAt(left)`. If its frequency drops to `0`, decrement `distinctCount`. Move `left` forward.
     - The number of valid substrings ending at `right` is given by the window size: `right - left + 1`. Add this directly to `totalSubstrings`.
3. In the main function, return `countAtMost(s, k) - countAtMost(s, k - 1)`.

---

# Dry Run

### Input
```java
s = "aba", k = 2
```

### Execution Trace for `countAtMost(s, 2)`

- **Initialization:** `left = 0`, `distinctCount = 0`, `total = 0`, `freq = [0, ..., 0]`

| `right` | `s[right]` | updated `freq` | `distinctCount` | `while(distinct > k)` | Substrings Added (`right - left + 1`) | `total` |
|---|---|---|---|---|---|---|
| `0` | `'a'` | `{'a': 1}` | `1` | Skip (`1 <= 2`) | `0 - 0 + 1 = 1` (`["a"]`) | `1` |
| `1` | `'b'` | `{'a': 1, 'b': 1}` | `2` | Skip (`2 <= 2`) | `1 - 0 + 1 = 2` (`["b", "ab"]`) | `3` |
| `2` | `'a'` | `{'a': 2, 'b': 1}` | `2` | Skip (`2 <= 2`) | `2 - 0 + 1 = 3` (`["a", "ba", "aba"]`) | `6` |

* `countAtMost(s, 2) = 6`

### Execution Trace for `countAtMost(s, 1)`
- Following the same logic, it yields `3` (valid substrings are `["a"]`, `["b"]`, `["a"]`).

### Result
```java
ans = countAtMost(s, 2) - countAtMost(s, 1) -> 6 - 3 = 3 // Valid exactly 2 distinct: ["ab", "ba", "aba"]
```

---

# Java Solution

```java
class Solution {
    public int countSubstrings(String s, int k) {
        return countAtMost(s, k) - countAtMost(s, k - 1);
    }

    // Helper function to count substrings with AT MOST k distinct characters
    private int countAtMost(String s, int k) {
        if (k < 0) return 0;
        
        int n = s.length();
        int left = 0;
        int distinctCount = 0;
        int totalSubstrings = 0;
        int[] freq = new int[26];

        for (int right = 0; right < n; right++) {
            int rightCharIdx = s.charAt(right) - 'a';
            
            if (freq[rightCharIdx] == 0) {
                distinctCount++;
            }
            freq[rightCharIdx]++;

            // Shrink the window if distinct characters exceed k
            while (distinctCount > k) {
                int leftCharIdx = s.charAt(left) - 'a';
                freq[leftCharIdx]--;
                if (freq[leftCharIdx] == 0) {
                    distinctCount--;
                }
                left++;
            }

            // All substrings ending at 'right' from any index between 'left' and 'right' are valid
            totalSubstrings += (right - left + 1);
        }

        return totalSubstrings;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
The helper function `countAtMost` is called twice. In each call, both the `right` and `left` pointers traverse the string of length \(N\) at most once. This creates an \(O(2N) = O(N)\) structural flow, which scales strictly linearly.

### Space Complexity

```java
O(1)
```
The frequency tracker array size is bounded by the alphabet size (`26`), which acts as an invariant \(O(1)\) auxiliary constant, consuming zero scaling heap space.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Brute Force** (nested loops) | \(O(N^2)\) | \(O(N)\) or \(O(1)\) | Simple to write, but hits a strict TLE on performance arrays. |
| **Sliding Window Delta** (Optimal) | \(O(N)\) | \(O(1)\) | Elegant reduction pattern converting a hard "exact" problem into manageable "at most" intervals. |

---

# Key Takeaways

- **The Exact-to-At-Most Formula:** Whenever you encounter a sliding window requirement for **exactly \(K\)** items, immediately consider solving for **at most \(K\)** and subtracting **at most \(K-1\)**.
- **Window Count Contribution:** The expression `right - left + 1` counts the number of valid intervals *ending* at the current `right` pointer. It is the cornerstone calculation for linear-time combination sweeps.
- Core concepts:
  - Sliding Window / Two Pointers
  - Character Frequency Arrays
  - Combinatorial Window Mathematics
- Expected interview preference: **Sliding Window Delta Approach**.
