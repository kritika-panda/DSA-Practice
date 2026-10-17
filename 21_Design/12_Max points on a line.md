# Max Points on a Line ([LeetCode 149](https://leetcode.com))

## Problem Statement

You are given an array of `points` where `points[i] = [x_i, y_i]` represents a point on the **X-Y plane**. Return the **maximum number of points** that lie on the **same straight line**.

A straight line is defined by at least **two points**. Points are considered **collinear** if they all share the same slope relative to a fixed anchor point.

Implement the solution using a **slope frequency map** strategy:

- **`int maxPoints(int[][] points)`** — Given the list of 2D coordinate points, returns the maximum count of points that are collinear (lie on the same infinite line passing through the plane).

---

## Examples

### Example 1

```text
Input:  points = [[1,1],[2,2],[3,3]]
Output: 3
```

### Example 2

```text
Input:  points = [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
```

### Explanation

```text
Example 1:
Points [1,1], [2,2], and [3,3] all lie on the line y = x.
All 3 points are collinear → answer is 3.

Example 2:
Points [1,1], [2,3], [3,2], and [5,3] do NOT all align.
However, [1,1], [2,3], [3,5]... let us check:
The line through [1,1] and [3,2] extends to [5,3]:
  slope = (2-1)/(3-1) = 1/2  → [1,1], [3,2], [5,3] share slope 1/2 → 3 collinear points.
The line through [1,4], [2,3], [3,2], [4,1]:
  slope = (3-4)/(2-1) = -1   → 4 collinear points → answer is 4.
```

---

# Intuition

Checking every possible triplet of points to test collinearity would cost an impractical \(O(N^3)\) computational runtime penalty. To optimize this down to an efficient **\(O(N^2)\) anchor-slope sweep**, we can leverage a **Slope Frequency Map**:

1. **The Anchor-Slope Rule:** Fix each point \(A = (x_1, y_1)\) as an **anchor**. For every other point \(B = (x_2, y_2)\), compute the **reduced slope** of the line \(AB\):
   - The raw slope is \(\frac{\Delta Y}{\Delta X} = \frac{y_2 - y_1}{x_2 - x_1}\).
   - To avoid floating-point imprecision, represent the slope as a **fully reduced fraction** \(\frac{dy}{dx}\) by dividing both \(\Delta Y\) and \(\Delta X\) by their **GCD**, and normalizing the sign so that \(dx \geq 0\) (or a canonical sentinel is used for vertical lines).

2. **Slope Grouping:** All points sharing the same reduced slope relative to the current anchor \(A\) lie on a single line through \(A\). The maximum count of such points (plus 1 for the anchor itself) gives the number of collinear points on that line.

3. **Duplicate Handling:** Points identical to the anchor \(A\) share every possible slope. They must be counted as **bonus collinear additions** appended uniformly to every slope group originating from \(A\).

4. **Global Maximum Tracking:** By iterating through every point as an anchor and maintaining a slope frequency map for each anchor pass, we track and return the global maximum line size encountered across all \(N\) anchor sweeps.

By storing slope frequencies inside a high-speed hash map (`Map<String, Integer>` keyed by the canonical slope string `"dy/dx"`), we can evaluate all pairwise slopes relative to a fixed anchor in \(O(N)\) per anchor, producing an overall \(O(N^2)\) runtime with \(O(1)\) average-time map lookups.

---

# State Definition

The analytical slope-frequency pipeline tracks collinearity counts on the heap using a slope-keyed hash map per anchor iteration:

```java
int globalMax;                    // Tracks the maximum collinear count seen across all anchor passes
Map<String, Integer> slopeMap;    // Maps canonical slope string "dy/dx" -> Frequency count relative to current anchor
int duplicates;                   // Counts points identical to the current anchor (share all slopes)
```

---

# Recurrence / Structural Matching Condition

```text
Function maxPoints(points):
    If len(points) == 1 → return 1
    globalMax = 1

    For i in range(0, N):                        // Fix points[i] as the anchor
        slopeMap = {}
        duplicates = 0

        For j in range(i+1, N):                  // Evaluate every other point as a candidate
            dx = points[j][0] - points[i][0]
            dy = points[j][1] - points[i][1]

            If dx == 0 AND dy == 0:              // Duplicate point found
                duplicates++
                Continue

            g = GCD(abs(dx), abs(dy))
            dx /= g
            dy /= g

            // Normalize sign: keep dx positive (or dy positive when dx == 0)
            If dx < 0:
                dx = -dx
                dy = -dy

            key = dy + "/" + dx
            slopeMap[key] = slopeMap.getOrDefault(key, 0) + 1

        // Best line through anchor = max slope group + duplicates + 1 (anchor itself)
        localMax = duplicates + 1
        For count in slopeMap.values():
            localMax = max(localMax, count + duplicates + 1)

        globalMax = max(globalMax, localMax)

    Return globalMax
```

---

# Optimal Slope Frequency Map Solution

## Idea

We anchor each point in turn and build a **slope frequency map** recording how many other points share each reduced slope relative to the anchor. Duplicate points (zero distance to anchor) are tracked separately and added as a bonus to every line through the anchor. After exhausting all candidates for a given anchor, we compute the best line size locally and propagate the global maximum forward.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class Solution {

    public int maxPoints(int[][] points) {
        int n = points.length;

        // Edge case: a single point trivially forms a line of length 1
        if (n == 1) return 1;

        int globalMax = 1;

        // Outer loop: fix each point as the anchor of the current line sweep
        for (int i = 0; i < n; i++) {
            // Slope frequency map: canonical "dy/dx" string -> count of collinear points
            Map<String, Integer> slopeMap = new HashMap<>();
            int duplicates = 0; // Points identical to the anchor

            // Inner loop: evaluate all subsequent points relative to the anchor
            for (int j = i + 1; j < n; j++) {
                int dx = points[j][0] - points[i][0];
                int dy = points[j][1] - points[i][1];

                // Duplicate point: shares every slope with the anchor
                if (dx == 0 && dy == 0) {
                    duplicates++;
                    continue;
                }

                // Reduce the slope fraction to canonical form via GCD normalization
                int g = gcd(Math.abs(dx), Math.abs(dy));
                dx /= g;
                dy /= g;

                // Normalize sign: enforce dx >= 0 so that (-1/2) and (1/-2) map to the same key
                if (dx < 0) {
                    dx = -dx;
                    dy = -dy;
                }

                // Build the canonical slope key and update the frequency map
                String key = dy + "/" + dx;
                slopeMap.put(key, slopeMap.getOrDefault(key, 0) + 1);
            }

            // Compute the best line through this anchor:
            // = largest slope group + all duplicates + 1 (the anchor itself)
            int localMax = duplicates + 1; // Handles the case where all points are duplicates
            for (int count : slopeMap.values()) {
                localMax = Math.max(localMax, count + duplicates + 1);
            }

            globalMax = Math.max(globalMax, localMax);
        }

        return globalMax;
    }

    // Euclidean GCD helper for slope fraction reduction
    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
}
```

---

## Complexity Analysis

Let \(N\) be the total number of points in the input array.

### Time Complexity

- **`maxPoints` Method:** \(O(N^2)\) quadratic time. The outer anchor loop runs \(N\) iterations; the inner candidate loop runs up to \(N - 1\) iterations per anchor. GCD computation per pair costs \(O(\log(\min(\Delta X, \Delta Y)))\), bounded in practice by the coordinate range and treated as a small constant relative to \(N\). Inside the inner loop, slope key lookups and insertions into the hash map scale in \(O(1)\) average constant time, keeping the overall query timeline entirely quadratic.

---

### Space Complexity

- **Auxiliary Space Complexity:** \(O(N)\) memory allocations are required on the heap per anchor iteration to store slope frequency entries inside the `slopeMap`. In the worst case (all points exhibiting distinct slopes relative to the current anchor), the inner map holds up to \(O(N)\) entries. The map is rebuilt fresh for each anchor pass, so the peak memory usage at any single anchor frame remains \(O(N)\).

---

# Key Takeaways

✅ Core fundamental paradigm for 2D geometry line-detection problems, slope normalization via GCD, and hash-map frequency aggregation patterns.

✅ **The Anchor-Slope Normalization Trick:** Representing slopes as **fully reduced integer fractions** (instead of floating-point ratios) is the critical design choice. This eliminates floating-point precision errors entirely and maps every geometrically identical slope to a unique, collision-free string key — enabling exact collinearity grouping with \(O(1)\) average hash map operations.

✅ **Duplicate Point Awareness:** Treating zero-distance (duplicate) points as universal slope boosters — rather than crashing the GCD or division logic — is a subtle edge case that separates correct implementations from incorrect ones.

✅ Directly drives real-world microarchitectures like **LiDAR point-cloud lane-line detectors** (identifying road edge collinearity inside 3D spatial scan streams), optical character recognition alignment validators evaluating whether printed text glyphs sit on a common baseline, and seismic sensor array analyzers detecting linear wave propagation fronts across distributed grid coordinates.

### Similar Problems

- Detect Squares ([LeetCode 2013](https://leetcode.com))
- Minimum Area Rectangle ([LeetCode 939](https://leetcode.com))
- Number of Boomerangs ([LeetCode 447](https://leetcode.com))
- Line Reflection ([LeetCode 356](https://leetcode.com))
- Set Matrix Zeroes ([LeetCode 73](https://leetcode.com))
