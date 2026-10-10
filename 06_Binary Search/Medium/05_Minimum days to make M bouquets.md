# Minimum Number of Days to Make m Bouquets

## Problem Statement

You are given an integer array `bloomDay`, an integer `m`, and an integer `k`.

You want to make `m` bouquets. To make a bouquet, you need to use `k` **adjacent flowers** from the garden.

The garden consists of `n` flowers, where the (i^{th}) flower will bloom on the `bloomDay[i]` and then can be used in **exactly one** bouquet.

Return the **minimum number of days** you need to wait to be able to make `m` bouquets from the garden. If it is impossible to make `m` bouquets, return `-1`.

---

## Examples

### Example 1

```text
Input: bloomDay =, m = 3, k = 1
Output: 3
```

### Explanation

```text
We need 3 bouquets of 1 flower each.
- Day 1: [1, _, _, _, _] -> 1 bouquet
- Day 2: [1, _, _, _, 2] -> 2 bouquets
- Day 3: [1, _, 3, _, 2] -> 3 bouquets
Therefore, the minimum number of days is 3.
```

---

### Example 2

```text
Input: bloomDay =, m = 3, k = 2
Output: -1
```

### Explanation

```text
We need 3 bouquets of 2 flowers each, meaning we need 3 * 2 = 6 flowers in total. 
Since the garden only has 5 flowers, it is impossible. Return -1.
```

---

### Example 3

```text
Input: bloomDay =, m = 2, k = 3
Output: 12
```

### Explanation

```text
We need 2 bouquets of 3 flowers each.
- Day 7: [7, 7, 7, 7, _, 7, 7] -> 1 bouquet formed from the first 3 flowers. The last 2 cannot form a bouquet of size 3.
- Day 12: [7, 7, 7, 7, 12, 7, 7] -> All flowers bloomed. We can pick indices [0,1,2] for the 1st bouquet and indices [4,5,6] or [3,4,5] for the 2nd.
Minimum days required = 12.
```

---

# Key Property: Monotonicity of Bouquet Count

```text
As the number of Days increases ---> The total bouquets we can form monotonically increases.
```

If we choose a specific day `D`, we can easily check how many bouquets can be formed using a greedy linear scan. If day `D` allows us to form at least `m` bouquets, then any day *after* `D` will also be valid. If it does not, then any day *before* `D` is definitely invalid. 

This directional, monotonic property allows us to apply **Binary Search on Answers** to isolate the absolute minimum day parameter in (O(N log(max(bloomDay})))) time.

---

# Intuition

### Step 1: Base Case Impossibility Check
Before running the search, check if `(long) m * k > bloomDay.length`. If the total required flowers exceed the available count, it is physically impossible to fulfill the bouquets. Return `-1` immediately.

### Step 2: Define the Search Bounds
* **Minimum day boundary (`low`):** `1` (or the minimum value inside `bloomDay`).
* **Maximum day boundary (`high`):** The maximum value inside `bloomDay` (on this day, all flowers have bloomed, yielding the maximum possible bouquets).

### Step 3: Binary Search Mechanics
We narrow the search pool down to the leftmost valid day candidate:
1. Find the midpoint day: `mid = low + (high - low) / 2`.
2. Compute the total number of bouquets we can form if we gather flowers on day `mid`.
3. **Case 1: `bouquets >= m` (Valid Day)**
   * Koko can make enough bouquets on this day. We record `mid` as a tentative answer.
   * Since we want to find the *earliest / minimum* day, we shift our search to the left half: `high = mid - 1`.
4. **Case 2: `bouquets < m` (Invalid Day)**
   * Not enough flowers have bloomed yet by day `mid`. We must wait longer. We shift our lower search boundary to the right half: `low = mid + 1`.

---

# Java Implementation

```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        // Prevent integer overflow during multiplication check
        long totalFlowersNeeded = (long) m * k;
        if (totalFlowersNeeded > bloomDay.length) {
            return -1;
        }

        int low = Integer.MAX_VALUE;
        int high = Integer.MIN_VALUE;

        // Establish the search boundaries based on array minimum and maximum elements
        for (int day : bloomDay) {
            low = Math.min(low, day);
            high = Math.max(high, day);
        }

        int ans = high;

        // Binary search on the answer space
        while (low <= high) {
            int mid = low + (high - low) / 2;

            // If we can make at least m bouquets on day 'mid'
            if (canMakeBouquets(bloomDay, mid, m, k)) {
                ans = mid;         // Record the valid candidate day
                high = mid - 1;    // Search left for an even earlier valid day
            } else {
                low = mid + 1;     // Not enough bouquets, search right for later days
            }
        }

        return ans;
    }

    // Helper method to count how many bouquets can be formed on a given day
    private boolean canMakeBouquets(int[] bloomDay, int day, int m, int k) {
        int bouquets = 0;
        int consecutiveFlowers = 0;

        for (int bloom : bloomDay) {
            // Check if the current flower has bloomed by the given day
            if (bloom <= day) {
                consecutiveFlowers++;
                // Once we collect k adjacent bloomed flowers, form a bouquet
                if (consecutiveFlowers == k) {
                    bouquets++;
                    consecutiveFlowers = 0; // Reset for the next bouquet
                }
            } else {
                // Adjacency is broken by an unbloomed flower, reset count
                consecutiveFlowers = 0;
            }

            // Early exit optimization if target m is met
            if (bouquets >= m) {
                return true;
            }
        }

        return bouquets >= m;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(N log(max(bloomDay) - min(bloomDay)))) where (N) is the number of elements in `bloomDay`. The binary search takes logarithmic time relative to the range of days. In each step, the `canMakeBouquets` helper function executes a linear scan over all (N) elements.
* **Space Complexity:** (O(1)) auxiliary space. All operations execute strictly in-place using primitive tracking variables.
