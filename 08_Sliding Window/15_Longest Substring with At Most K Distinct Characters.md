# Longest Substring with K Unique Characters

## Problem Statement

Given a string `s` and an integer `k`, find the length of the **longest substring** that contains **exactly** `k` distinct characters. If there is no such substring, return `-1`.

*(Note: The provided implementation adapts the "at most k" strategy to locate the longest window matching exactly `k` unique characters).*

---

## Examples

### Example 1

```text
Input: s = "aababbcaacc", k = 2
Output: 6
```

### Explanation

```text
The longest substring with exactly two distinct characters is "aababb".
The length of this substring is 6.
```

---

### Example 2

```text
Input: s = "abcddefg", k = 3
Output: 4
```

### Explanation

```text
The longest substring with exactly three distinct characters is "bcdd" (containing 'b', 'c', and 'd').
The length of this substring is 4.
```

---

# Key Concept: Sliding Window with Latest Index Map

```text
[ Window Start (l) ... Window End (r) ]
```

Instead of tracking frequency counts, we can store the **most recent index** where each unique character was seen inside a HashMap. This layout allows us to locate character tracking boundaries instantly whenever the unique character threshold is breached.

---

# Intuition

We traverse the string from left to right using the `right` pointer `r`. 

At every step as `r` moves forward:
1. Save or overwrite the current index of the character: `hm.put(s.charAt(r), r)`.

### Case 1: Map size matches K
If `hm.size() == k`, the current window contains exactly `k` distinct characters. We calculate the size of this window as `r - l + 1` and update our global maximum tracking variable `maxi`.

---

### Case 2: Map size exceeds K
The moment `hm.size() > k`, a new unique character has broken our constraint. 

To bring the unique character count back down to `k`, we must completely eject the character whose last-seen occurrence is furthest to the left.
1. Find the minimum value inside the map: `min = Collections.min(hm.values())`.
2. Evict that character completely from the map: `hm.remove(s.charAt(min))`.
3. Shift the left boundary `l` past that evicted position: `l = min + 1`.
4. Recalculate the window size and update `maxi`.

---

# Visualization

Tracking pointers on `s = "aababbcaacc"` with `k = 2`:

```text
1. Expand window (r goes 0 to 4): Process characters 'a' and 'b'.
   - At r = 4: hm = {'a': 3, 'b': 4}. size == 2 -> maxi = max(-1, 4-0+1) = 5.

2. At r = 5 ('c'): Unique count increases!
   - hm = {'a': 3, 'b': 4, 'c': 5}. size is 3 (Invalid!).
   - Find minimum index value: min(3, 4, 5) = 3 (Character 'a').
   - Remove 'a' from map. Shift left boundary: l = 3 + 1 = 4.
   - hm becomes {'b': 4, 'c': 5}. Valid!
   - New Window size = 5 - 4 + 1 = 2. maxi remains 5.

Visual sequence of the longest valid window segment:
l=0       r=5
[aababb]cbaacc  (Unique characters = {'a', 'b'}, length = 6)
```

---

# Java Implementation

```java
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int longestKSubstr(String s, int k) {
        int l = 0;
        int r = 0;
        int n = s.length();
        
        // HashMap to store the latest index position of each unique character
        Map<Character, Integer> hm = new HashMap<>();
        int maxi = -1;

        while (r < n) {
            // Update or store the latest index of the current character
            hm.put(s.charAt(r), r);
            
            // Condition 1: Exactly k unique characters are inside the window
            if (hm.size() == k) {
                maxi = Math.max(maxi, r - l + 1);
            } 
            // Condition 2: Window exceeds k unique characters, shrink from left
            else if (hm.size() > k) {
                // Find the character with the earliest last-seen index position
                int min = Collections.min(hm.values());
                
                // Evict that character from the map to bring unique size back down
                hm.remove(s.charAt(min));
                
                // Jump the left pointer directly past the removed character's location
                l = min + 1;
                
                // Update maximum valid length matching the threshold limit
                maxi = Math.max(maxi, r - l + 1);
            }
            r++;
        }

        return maxi;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N * K) where N is the length of the string `s` and K is the number of distinct characters allowed. In the worst-case scenario, finding the minimum index using `Collections.min()` scans all elements inside the HashMap. Since the map size is strictly capped at K + 1, this scan takes O(K) time per step.
* **Space Complexity:** O(K) auxiliary space. The HashMap holds at most K + 1 character entries at any point before triggering an eviction cycle.
