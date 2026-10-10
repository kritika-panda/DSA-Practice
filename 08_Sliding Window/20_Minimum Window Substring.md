# Minimum Window Substring

## Problem Statement

Given two strings `s` and `t` of lengths `m` and `n` respectively, return the **minimum window substring** of `s` such that every character in `t` (**including duplicates**) is included in the window. If there is no such substring, return the empty string `""`.

The test cases are generated such that the answer is **unique**.

---

## Examples

### Example 1

```text
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
```

### Explanation

```text
The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t. 
While "ADOBEC" also contains all required characters, its length is 6, which is larger than "BANC" (length 4).
```

---

### Example 2

```text
Input: s = "a", t = "a"
Output: "a"
```

### Explanation

```text
The entire string s contains all required characters and represents the minimum window.
```

---

### Example 3

```text
Input: s = "a", t = "aa"
Output: ""
```

### Explanation

```text
Both 'a's from string t must be included in the window. 
Since s only contains a single 'a', it is impossible to satisfy the condition, so an empty string is returned.
```

---

# Key Concept: Sliding Window with Match Tracking

```text
[ Window Start (left) ... Window End (right) ] ---> Shrink from left when valid
```

To find the minimum length window substring efficiently, we use a **two-pointer sliding window** technique paired with two frequency trackers:
1. `need`: A frequency dictionary mapping the required counts of characters present in `t`.
2. `window`: A frequency dictionary mapping the characters present in our current active sliding window.

We track matching conditions using a `have` counter, which increments only when a character's frequency inside the active window completely meets its required threshold count in `need`. This lets us avoid scanning the entire frequency map to verify validity at each step.

---

# Intuition

We expand the window using the `right` pointer to locate a valid match configuration:

### Phase 1: Expansion (Looking for a valid window)
We add `s.charAt(right)` to our `window` frequency map. If this character is required by `t` and its frequency count in `window` exactly matches its required count in `need`, we increment `have`.

---

### Phase 2: Contraction (Minimizing the window)
The moment `have == needCount` (where `needCount` is the number of unique characters in `t`), the current window is valid. 
1. We calculate the window's size: `right - left + 1`.
2. If this size is smaller than our historical minimum (`minLen`), we update `minLen` and store the `start` index of this substring.
3. We try to optimize the window by ejecting the character at `left` (`window.put(leftChar, count - 1)`) and moving the `left` pointer forward (`left++`).
4. If ejecting `leftChar` causes its count to drop below the required threshold in `need`, the window becomes invalid, so we decrement `have` and resume the expansion phase.

---

# Java Implementation

```java
import java.util.*;

class Solution {
    public String minWindow(String s, String t) {
        // If s is shorter than t, it's impossible to form a valid window
        if (s.length() < t.length()) return "";

        // Map to store character frequencies required by string t
        Map<Character, Integer> need = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        // Map to store character frequencies inside the current window
        Map<Character, Integer> window = new HashMap<>();
        int have = 0, needCount = need.size();
        int left = 0, minLen = Integer.MAX_VALUE;
        int start = 0;

        // Expand the window using the right pointer
        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            window.put(c, window.getOrDefault(c, 0) + 1);

            // Increment have only when the current character meets its target frequency
            if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) {
                have++;
            }

            // Shrink the window from the left as long as the window remains valid
            while (have == needCount) {
                // Record the minimum window details found so far
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    start = left;
                }

                char leftChar = s.charAt(left);
                window.put(leftChar, window.get(leftChar) - 1);
                
                // If a required character's frequency falls below the target, break validity
                if (need.containsKey(leftChar) && window.get(leftChar) < need.get(leftChar)) {
                    have--;
                }
                left++;
            }
        }

        // Return the minimum substring, or an empty string if no valid window was found
        return minLen == Integer.MAX_VALUE ? "" : s.substring(start, start + minLen);
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(M + N) where M is the length of string `s` and N is the length of string `t`. Populating the `need` map takes O(N) time. During the sliding window loop, both the `left` and `right` pointers move strictly forward from `0` to `M`, meaning each character in `s` is processed at most twice.
* **Space Complexity:** O(M + N) auxiliary space. In the worst-case scenario, the `need` and `window` maps store entries proportional to the number of unique characters in both strings.
