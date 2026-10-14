# Minimum Platforms Required for a Railway

## Problem Statement

Given two arrays `arr[]` and `dep[]` representing the **arrival** and **departure** times of all trains that reach a railway station, find the **minimum number of platforms** required for the railway station so that no train has to wait.

An arrival time means a train enters a platform, and a departure time means a train completely leaves a platform. If two trains occupy the station at the same time (e.g., one arrives at the exact same time another departs), a platform overlap occurs, and they **cannot** share the same platform at that instant—an additional platform must be allocated.

The overall runtime complexity must be strictly bounded by:

```text
O(N log N)
```

*(where N is the total number of trains)*

---

## Examples

### Example 1

```java
Input: arr = [900, 940, 950, 1100, 1500, 1800]
       dep = [910, 1200, 1120, 1130, 1900, 2000]
Output: 3
```

### Explanation

```text
Chronological timeline visualization:
- 09:00 → Train 1 arrives. Platforms needed = 1.
- 09:10 → Train 1 departs. Platforms needed = 0.
- 09:40 → Train 2 arrives. Platforms needed = 1.
- 09:50 → Train 3 arrives. Platforms needed = 2.
- 11:00 → Train 4 arrives. Platforms needed = 3.  ◄── Max Platforms Reached!
- 11:20 → Train 3 departs. Platforms needed = 2.
- 11:30 → Train 4 departs. Platforms needed = 1.
- 12:00 → Train 2 departs. Platforms needed = 0.
...
Maximum simultaneous trains at any instant = 3.
```

---

### Example 2

```java
Input: arr = [900, 1230, 1300]
       dep = [1000, 1230, 1300]
Output: 2
```

### Explanation

```text
- 09:00 → Train 1 arrives. (Platforms = 1)
- 10:00 → Train 1 departs. (Platforms = 0)
- 12:30 → Train 2 arrives AND departs simultaneously. 
  Because departure transitions cannot bypass an arriving train at the exact same edge, 
  platforms jump to 2 at that instant. 

Maximum platforms = 2
```

---

# Intuition

This problem can be structurally rephrased as finding the **maximum number of overlapping intervals** at any point in time. 

Instead of tracking which specific train occupies which platform, we can view the problem as a **global chronological timeline sweep**:
- When any train **arrives**, it claims a platform. The total count of active platforms increases by 1.
- When any train **departs**, it vacates a platform. The total count of active platforms decreases by 1.
- By breaking the pairs apart and sorting both the `arr` and `dep` arrays independently, we can process arrivals and departures in perfect chronological order using **Two Pointers**. 
- Tracking the maximum value that our active platform counter reaches during this chronological sweep gives us the exact answer.

---

# Greedy Observation

Consider independent sorted arrival and departure streams:

```text
Arrivals   : 09:00, 09:40, 09:50
Departures : 09:10, 11:20, 12:00
```

We do *not* need to know which arrival matches which departure. We only need to check what event happens next on the clock:

```text
09:00 (Arrival)   → Platforms = 1
09:10 (Departure) → Platforms = 0
09:40 (Arrival)   → Platforms = 1
09:50 (Arrival)   → Platforms = 2  ◄── Max concurrently active trains
```

Sorting both tracks independently lets us map the global congestion peak effortlessly.

---

# Approach: Independent Sorting + Two Pointers

## Algorithm

1. Sort the arrival array `arr` in ascending order.
2. Sort the departure array `dep` in ascending order.
3. Initialize:
   - `i = 1` → Pointer tracking the next arrival event (start at 1 since index 0 is processed as the initial seed).
   - `j = 0` → Pointer tracking the next departure event.
   - `platformCount = 1` → Running count of active platforms.
   - `maxPlatforms = 1` → Tracks the peak of the running count.
4. While `i < n` and `j < n`:
   - If `arr[i] <= dep[j]`:
     - An arrival occurs before (or exactly at) the next departure.
     - Increment `platformCount`.
     - Advance arrival pointer `i++`.
   - Otherwise (`arr[i] > dep[j]`):
     - A departure occurs next.
     - Decrement `platformCount`.
     - Advance departure pointer `j++`.
   - Update `maxPlatforms = Math.max(maxPlatforms, platformCount)`.
5. Return `maxPlatforms`.

---

# Dry Run

```java
arr = [900, 940, 950]
dep = [910, 1200, 1120]
```

After sorting:

```text
arr = [900, 940, 950]
dep = [910, 1120, 1200]
```

### Initial State

```text
platformCount = 1, maxPlatforms = 1
i = 1, j = 0
```

---

### Step 1

```text
arr[i] (940) vs dep[j] (910)
940 > 910 → Departure event happens first!

platformCount-- → 0
j++ → 1
```

---

### Step 2

```text
arr[i] (940) vs dep[j] (1120)
940 <= 1120 → Arrival event happens first!

platformCount++ → 1
i++ → 2
maxPlatforms = max(1, 1) = 1
```

---

### Step 3

```text
arr[i] (950) vs dep[j] (1120)
950 <= 1120 → Arrival event happens first!

platformCount++ → 2
i++ → 3
maxPlatforms = max(1, 2) = 2
```

The arrival loop `i` completes. The remaining departures just clear out the station.

Answer:

```text
2
```

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int findPlatform(int[] arr, int[] dep, int n) {
        // Step 1: Sort both arrays independently to track chronological events
        Arrays.sort(arr);
        Arrays.sort(dep);

        // Seed with the absolute first arrival train event
        int platformCount = 1;
        int maxPlatforms = 1;

        int i = 1; // Pointer for arrivals
        int j = 0; // Pointer for departures

        // Step 2: Two-pointer chronological sweep pass
        while (i < n && j < n) {
            // If a train arrives before or exactly as another departs,
            // we must allocate an additional platform layout block
            if (arr[i] <= dep[j]) {
                platformCount++;
                i++;
            } 
            // A train leaves the station completely, freeing up a platform
            else {
                platformCount--;
                j++;
            }

            // Record the peak congestion metric found
            maxPlatforms = Math.max(maxPlatforms, platformCount);
        }

        return maxPlatforms;
    }
}
```

---

# Optimized Understanding

This problem belongs to the **Interval Sweeping** class of algorithms. 

Sorting the arrival and departure times separately transforms a complex 2D interval overlap search into a 1D event stream. We don't care *which* train leaves; we only care that *some* train has vacated a platform, which decrements our resource allocation counter.

---

# Correctness Proof

### Greedy Choice

We process events in strict chronological order. Suppose we do not sort the departures and instead try to match arrivals to their specific departure slots. 

If Train A arrives at 9:00 and leaves at 11:00, and Train B arrives at 9:30 and leaves at 10:00, sorting departures processes the 10:00 vacancy before checking any arrivals past that point. 

Failing to process that 10:00 vacancy as early as possible would cause us to overestimate the number of platforms needed at later times. Therefore, strict chronological processing is always optimal.

---

### Optimal Substructure

After sorting both streams, evaluating the event at `min(arr[i], dep[j])` leaves us with a subproblem of size $N - 1$ containing the remaining events. Resolving each event step optimally guarantees a globally optimal maximum value tracker.

---

# Complexity Analysis

Let:

```text
N = total number of trains
```

### Time Complexity

Sorting `arr` and `dep`:

```text
O(N log N) + O(N log N) = O(N log N)
```

Two-Pointer Sweep:

```text
O(N)
```

Overall:

```text
O(N log N)
```

---

### Space Complexity

```text
O(1)
```

The algorithm operates completely in-place on the inputs, requiring no auxiliary storage lists or priority queue structures.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Interval Sweeping + Two Pointers
```

Common indicators:

- Finding peak concurrency or overlap boundaries in a timeline
- Tracking resource capacities across simultaneous events
- Decoupling start/end intervals to run a linear two-pointer sweep

---

# Similar Problems

1. Meeting Rooms II (LeetCode 253) - *Identical structure using a Min-Heap/Sorting*
2. Non-overlapping Intervals (LeetCode 435)
3. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
4. Car Pooling (LeetCode 1094)
5. N Meetings in One Room

---

# Takeaway

```text
Sort both arrival and departure arrays independently.

Use two pointers to step through events chronologically.

Increment platforms on arrivals, decrement on departures,
and track the peak value reached.
```

**Pattern:** Greedy + Interval Sweeping + Two Pointers

<FollowUp>
We can now review another high-frequency interval or greedy timeline problem. Let me know if you would like to generate the raw markdown code for **Meeting Rooms II (LeetCode 253)** or **Job Sequencing Problem with Deadlines**!
</FollowUp>
