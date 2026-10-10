# Max Consecutive Ones III

## Problem Statement

Given a binary array `nums` and an integer `k`, return the **maximum number of consecutive 1's** in the array if you can flip at most `k` 0's.

---

## Example

### Input

```text
nums = [1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0]
k = 2
```

### Output

```text
6
```

### Explanation

```text
By flipping exactly two 0's at strategic positions, the array can become:
[1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1]
                ^  ^ (Flipped numbers from 0 to 1)

The longest consecutive sequence of 1's spans from index 5 to 10.
Length = 6.
```

---

# Key Concept: Sliding Window

```text
[ Window Start (l) ... Window End (r) ]
```

Instead of tracking individual flips, this problem transforms into finding the **longest subarray that contains at most `k` zeros**. We maintain a dynamic sliding window bounded by a left pointer `l` and a right pointer `r` to compute this efficiently.

---

# Intuition

We traverse the array using the right pointer `r`. As `r` expands the window forward, we count the number of zeros encountered using a tracker `c`.

### Case 1: Zeros count is within limit
As long as `c <= k`, the window is valid. We continuously calculate the active window size as `r - l + 1` and update our global maximum count (`maxi`).

---

### Case 2: Zeros count exceeds limit
The moment `c > k`, the current window becomes invalid because it contains more zeros than we are allowed to flip. 

To resolve this, we contract the window from the left by shifting the pointer `l` forward. If the element leaving the window at index `l` is a `0`, we decrement our zero tracker (`c--`). We repeat this contraction until the number of zeros inside the active window drops back down to `k`.

---

# Visualization

Tracking pointers on `nums = [1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0]` with `k = 2`:

```text
1. Expand window (r goes 0 to 2): All 1's. c = 0, maxi = 3.
2. At r = 3 (nums[3] = 0): Zero encountered. c = 1, maxi = 4.
3. At r = 4 (nums[4] = 0): Another zero. c = 2, maxi = 5.
4. At r = 5 (nums[5] = 0): Third zero! c = 3 (Invalid since c > k).
   - Shrink from left: l moves from 0 to 3 to eject a zero.
   - When l moves past index 3, nums[3] was 0 -> c becomes 2. 
   - New valid window boundary establishes at l = 4.

Window positions visual snapshot when r hits index 10:
nums = [1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0]
                       l=5          r=10
Active valid window size = 10 - 5 + 1 = 6.
```

---

# Java Implementation

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int l = 0, maxi = 0, c = 0;
        int r = 0;
        int n = nums.length;
        
        while (r < n) {
            // Include the current element in the window
            if (nums[r] == 0) {
                c++;
            }
            
            // If zero count exceeds k, shrink the window from the left
            while (c > k) {
                if (nums[l] == 0) {
                    c--;
                }
                l++;
            }
            
            // Track the maximum consecutive 1's window size found so far
            maxi = Math.max(maxi, r - l + 1);
            r++;
        }
        
        return maxi;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the `nums` array. Although there is a nested `while` loop, the left pointer `l` and the right pointer `r` both only travel across each array element at most once.
* **Space Complexity:** O(1) auxiliary space because we only maintain a few integer pointer indices and zero counter variables without allocating any dynamic collections.
