# Minimum Number of Days to Make m Bouquets

## Problem Statement

You are given an integer array `bloomDay`, an integer `m`, and an integer `k`.

You want to make `m` bouquets. To make a bouquet, you need to use `k` **adjacent flowers** from the garden.

The garden consists of `n` flowers, where the (i^th) flower will bloom on the `bloomDay[i]` and then can be used in **exactly one** bouquet.

Return the **minimum number of days** you need to wait to be able to make `m` bouquets from the garden. If it is impossible to make `m` bouquets, return `-1`.

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
