# Capacity To Ship Packages Within D Days

## Problem Statement

A conveyor belt has packages that must be shipped from one port to another within `days` days. The (i^{th}) package on the conveyor belt has a weight denoted by `weights[i]`. Each day, we load the ship with packages on the conveyor belt (in the order given by `weights`). We may not load more weight than the maximum weight capacity of the ship.

Return the **least weight capacity** of the ship that will result in all the packages on the conveyor belt being shipped within `days` days.

---

## Examples

### Example 1

```text
Input: weights =, days = 5
Output: 15
```

### Explanation

```text
A ship capacity of 15 is the minimum to ship all packages in 5 days like this:
- Day 1: 1, 2, 3, 4, 5 (total weight = 15)
- Day 2: 6, 7 (total weight = 13)
- Day 3: 8 (total weight = 8)
- Day 4: 9 (total weight = 9)
- Day 5: 10 (total weight = 10)

Note that the packages must be shipped in the given order. 
A lower capacity would force packages onto subsequent days, breaching the 5-day limit.
```

---

### Example 2

```text
Input: weights =, days = 3
Output: 6
```

### Explanation

```text
A ship capacity of 6 allows shipping in 3 days:
- Day 1: 3, 2 (weight = 5)
- Day 2: 2, 4 (weight = 6)
- Day 3: 1, 4 (weight = 5)
```

---

### Example 3

```text
Input: weights =, days = 4
Output: 3
```

---

# Key Property: Monotonicity of Shipping Duration

```text
As Ship Capacity increases ---> The total number of Days required to ship all packages monotonically decreases.
```

If we choose a specific weight capacity `C`, we can easily check how many days it will take to ship all the packages using a greedy linear scan. If capacity `C` allows us to finish within the required `days`, then any capacity *larger* than `C` will also be valid. If it does not, then any capacity *smaller* than `C` is definitely invalid.

This inverse monotonic property allows us to apply **Binary Search on Answers** to isolate the absolute minimum acceptable capacity in (O(N log(sum text{weights} - max(text{weights})))) time.

---

# Intuition

### Step 1: Define the Search Bounds
* **Minimum possible capacity (`low`):** The maximum single package weight in `weights` (e.g., `10` in Example 1). The ship must be at least as strong as the heaviest item, otherwise that item could never be loaded.
* **Maximum possible capacity (`high`):** The sum of all elements in `weights` (e.g., `55` in Example 1). With this capacity, the ship can transport every single package simultaneously on Day 1.

### Step 2: Binary Search Mechanics
We look for the leftmost valid capacity candidate using a standard binary search iteration:
1. Find the midpoint capacity candidate: `mid = low + (high - low) / 2`.
2. Compute the total days required to deliver all packages if the ship's limit is `mid`.
3. **Case 1: `requiredDays <= days` (Valid Capacity)**
   * The ship can finish on time at this capacity. We record `mid` as a tentative answer.
   * Since we want to find the *minimum* possible capacity, we try to optimize by searching the left half: `high = mid - 1`.
4. **Case 2: `requiredDays > days` (Invalid Capacity)**
   * The ship is too small and takes too many days to clear the conveyor belt. The required capacity must be strictly larger. We shift our lower search boundary to explore the right half: `low = mid + 1`.

---

# Java Implementation

```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int low = 0;
        int high = 0;

        // Establish the search boundaries:
        // low is the maximum single weight; high is the sum of all weights
        for (int w : weights) {
            low = Math.max(low, w);
            high += w;
        }

        int ans = high;

        // Binary search on the capacity answer space
        while (low <= high) {
            int mid = low + (high - low) / 2;

            // Check if the current capacity capacity clears the load within the day limit
            if (getDaysRequired(weights, mid) <= days) {
                ans = mid;         // Record the valid candidate capacity
                high = mid - 1;    // Search left for an even smaller valid capacity
            } else {
                low = mid + 1;     // Capacity too small, search right for larger limits
            }
        }

        return ans;
    }

    // Helper method to calculate the number of days required for a given ship capacity
    private int getDaysRequired(int[] weights, int capacity) {
        int days = 1;
        int currentLoad = 0;

        for (int w : weights) {
            // If adding the current package exceeds the daily capacity limit,
            // ship out the current load and move the package to the next day
            if (currentLoad + w > capacity) {
                days++;
                currentLoad = 0;
            }
            currentLoad += w;
        }

        return days;
    }
}
```

### Complexity Analysis

* **Time Complexity:** (O(N log(sum (weights) - max(weights)))) where (N) is the number of packages in the `weights` array. The binary search window takes logarithmic time relative to the weight span. In each step, the `getDaysRequired` helper function executes a linear scan over all (N) elements.
* **Space Complexity:** (O(1)) auxiliary space. The calculation runs entirely in-place, tracking ranges using only a few primitive local variables.
