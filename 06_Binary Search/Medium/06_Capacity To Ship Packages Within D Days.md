# Capacity To Ship Packages Within D Days

## Problem Statement

A conveyor belt has packages that must be shipped from one port to another within `days` days. The (i^{th}) package on the conveyor belt has a weight denoted by `weights[i]`. Each day, we load the ship with packages on the conveyor belt (in the order given by `weights`). We may not load more weight than the maximum weight capacity of the ship.

Return the **least weight capacity** of the ship that will result in all the packages on the conveyor belt being shipped within `days` days.

---

## Examples
## Example 1

```text
weights = [1,2,3,4,5,6,7,8,9,10]
days = 5
```

### Search Space

- Minimum capacity = max(weights) = 10
- Maximum capacity = sum(weights) = 55

```text
Capacity Range: [10 ... 55]
```

### Check Capacity = 15

```text
Day 1: 1 + 2 + 3 + 4 + 5 = 15
Day 2: 6 + 7 = 13
Day 3: 8
Day 4: 9
Day 5: 10

Total Days Needed = 5 ✅
```

Since 15 works, try a smaller capacity.

### Check Capacity = 14

```text
Day 1: 1 + 2 + 3 + 4 = 10
Day 2: 5 + 6 = 11
Day 3: 7
Day 4: 8
Day 5: 9
Day 6: 10

Total Days Needed = 6 ❌
```

Need more than 5 days, so capacity is too small.

### Answer

```text
Minimum Capacity = 15
```

---

## Example 2

```text
weights = [3,2,2,4,1,4]
days = 3
```

### Search Space

```text
minCapacity = 4
maxCapacity = 16
```

### Check Capacity = 6

```text
Day 1: 3 + 2 = 5
Day 2: 2 + 4 = 6
Day 3: 1 + 4 = 5

Total Days = 3 ✅
```

Try smaller capacity.

### Check Capacity = 5

```text
Day 1: 3 + 2 = 5
Day 2: 2
Day 3: 4 + 1 = 5
Day 4: 4

Total Days = 4 ❌
```

Need more than 3 days.

### Answer

```text
Minimum Capacity = 6
```

---

## Example 3

```text
weights = [1,2,3,1,1]
days = 4
```

### Search Space

```text
minCapacity = 3
maxCapacity = 8
```

### Check Capacity = 3

```text
Day 1: 1 + 2 = 3
Day 2: 3
Day 3: 1 + 1 = 2

Total Days = 3 ✅
```

Since we need at most 4 days, capacity 3 works.

### Answer

```text
Minimum Capacity = 3
```

---

# Binary Search Visualization

```text
weights = [3,2,2,4,1,4]
days = 3

Search Range = [4 ... 16]

mid = 10
Can ship in 2 days ✅
Move Left

[4 ... 9]

mid = 6
Can ship in 3 days ✅
Move Left

[4 ... 5]

mid = 4
Can ship in 5 days ❌
Move Right

[5 ... 5]

mid = 5
Can ship in 4 days ❌
Move Right

Answer = 6
```

---

# Edge Case 1: Only One Day

```text
weights = [5,4,3,2,1]
days = 1
```

Everything must be shipped together.

```text
Capacity = 5 + 4 + 3 + 2 + 1 = 15
```

✅ Answer:

```text
15
```

---

# Edge Case 2: Days = Number of Packages

```text
weights = [5,4,3,2,1]
days = 5
```

Ship one package per day.

```text
Capacity = max(weights) = 5
```

✅ Answer:

```text
5
```

---

# Greedy Check Function Example

For:

```text
weights = [3,2,2,4,1,4]
capacity = 6
```

### Simulation

```text
Day 1:
Load = 0

Take 3 → Load = 3
Take 2 → Load = 5

Next weight = 2
5 + 2 > 6

Start Day 2
Load = 2

Take 4
2 + 4 = 6

Next weight = 1
6 + 1 > 6

Start Day 3
Load = 1

Take 4
1 + 4 = 5
```

```text
Days Needed = 3
```

✅ Capacity 6 is feasible.

---

# Key Observation

If a capacity works:

```text
Capacity = X ✅
Capacity = X + 1 ✅
Capacity = X + 2 ✅
...
```

If a capacity fails:

```text
Capacity = X ❌
Capacity = X - 1 ❌
Capacity = X - 2 ❌
...
```

This forms a monotonic pattern:

```text
FFFFFTTTTT
     ^
 First Valid Capacity
```

Where:

- F = Cannot ship within D days
- T = Can ship within D days

Since the answer space is sorted in this way, we apply **Binary Search on Answer** to find the **minimum valid capacity**.



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
