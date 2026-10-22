# Koko Eating Bananas

## Problem Statement

A monkey named Koko loves to eat bananas. There are `n` piles of bananas, where the (i^{th}) pile has `piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas from any other pile during this hour.

Koko likes to eat slowly but still wants to finish eating all the bananas before the guards return.

Return the **minimum integer speed `k`** such that she can eat all the bananas within `h` hours.

---

## Examples

<br>

### Example 1

<br>

```text
Input: piles = [3,6,7,11], h = 8
Output: 4
```

<br>

### Explanation

<br>

```text
If eating speed k = 3:
total hours = ceil(3/3) + ceil(6/3) + ceil(7/3) + ceil(11/3)
            = 1 + 2 + 3 + 4
            = 10 hours (> 8)

If eating speed k = 4:
total hours = ceil(3/4) + ceil(6/4) + ceil(7/4) + ceil(11/4)
            = 1 + 2 + 2 + 3
            = 8 hours (<= 8)

Therefore, the minimum integer eating speed is 4.
```

<br>

---

<br>

### Example 2

<br>

```text
Input: piles = [30,11,23,4,20], h = 5
Output: 30
```

<br>

### Explanation

<br>

```text
Since h equals the number of piles, Koko must eat at least the size of the largest pile each hour to finish on time.

Largest pile = 30

If k < 30, the pile with 30 bananas would require more than 1 hour, causing the total time to exceed 5 hours.

Therefore, the minimum eating speed is 30.
```

<br>

---

<br>

### Example 3

<br>

```text
Input: piles = [30,11,23,4,20], h = 6
Output: 23
```

<br>

### Explanation

<br>

```text
If eating speed k = 22:
total hours = ceil(30/22) + ceil(11/22) + ceil(23/22) + ceil(4/22) + ceil(20/22)
            = 2 + 1 + 2 + 1 + 1
            = 7 hours (> 6)

If eating speed k = 23:
total hours = ceil(30/23) + ceil(11/23) + ceil(23/23) + ceil(4/23) + ceil(20/23)
            = 2 + 1 + 1 + 1 + 1
            = 6 hours (<= 6)

Therefore, the minimum integer eating speed is 23.
```

---

# Key Property: Monotonicity of Total Time

```text
As eating speed k increases ---> The total hours required to finish all bananas monotonically decreases.
```

This inverse monotonic relationship allows us to frame the problem as **Binary Search on Answers**. Instead of checking speed values sequentially from 1 upwards, we can pinpoint the minimum optimal speed by splitting the potential velocity range in half at each step.

---

# Intuition

### Step 1: Define the Search Bounds
* **Minimum speed boundary (`low`):** `1` banana per hour (she must eat at least something).
* **Maximum speed boundary (`high`):** The maximum value found in `piles`. Eating any faster than the largest pile size provides no benefit, as Koko cannot move to a new pile within the same hour window.

### Step 2: Binary Search Mechanics
We look for the leftmost valid speed parameter using a standard binary search iteration:
1. Find the midpoint speed candidate: `mid = low + (high - low) / 2`.
2. Compute the total time Koko would spend finishing all piles at this speed.
3. **Case 1: `totalHours <= h` (Valid Speed)**
   * Koko can finish on time at this speed. We record `mid` as a valid candidate.
   * Since we want to find the *minimum* possible eating speed, we try to optimize by searching the left half: `high = mid - 1`.
4. **Case 2: `totalHours > h` (Invalid Speed)**
   * Koko is eating too slowly and cannot finish before the guards return. The required speed must be strictly faster. We shift our lower search boundary to explore the right half: `low = mid + 1`.

---

# Java Implementation

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int low = 1;
        int high = 0;
        
        // Find the maximum pile size to establish our upper search boundary
        for (int pile : piles) {
            high = Math.max(high, pile);
        }
        
        int ans = high;
        
        // Binary search on the velocity answer space
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Check if the current speed allows Koko to finish within h hours
            if (calculateHours(piles, mid) <= h) {
                ans = mid;         // Record valid candidate speed
                high = mid - 1;    // Try to find a slower valid speed to the left
            } else {
                low = mid + 1;     // Speed is too slow, look for faster speeds to the right
            }
        }
        
        return ans;
    }
    
    // Helper method to calculate total hours needed for a given eating speed k
    private long calculateHours(int[] piles, int k) {
        long totalHours = 0;
        for (int pile : piles) {
            // Integer formula for ceiling division: ceil(pile / k)
            totalHours += (pile + k - 1) / k;
        }
        return totalHours;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(N log(max(piles)))) where (N) is the total number of piles. The binary search window collapses in (O(log(max(text{piles})))) iterations. In each iteration step, the `calculateHours` helper executes a linear sweep across all (N) piles.
* **Space Complexity:** (O(1)) auxiliary space. The entire binary search mechanism executes in-place, tracking ranges with a few primitive local integer and long variables.
