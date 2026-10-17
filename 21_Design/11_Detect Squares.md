\# Detect Squares ([LeetCode 2013](https://leetcode.com))

## Problem Statement

You are given a stream of points on a 2D plane. Design an algorithm that can add new points from the stream and count the number of ways to form **axis-aligned squares** with a given query point.

An axis-aligned square has pieces of equal length and its edges are completely parallel or perpendicular to the X-axis and Y-axis.

Implement the `DetectSquares` class:
- `DetectSquares()` Initializes the object with an empty 2D plane directory.
- `void add(int[] point)` Adds a new point `point = [x, y]` to the data stream. **Duplicate points are allowed** and should be treated as distinct entities.
- `int count(int[] point)` Counts the number of ways to form **axis-aligned squares** using the previously added points where the query point `point = [x, y]` acts as one of the four corner vertices.

---

## Examples

### Example 1

```text
Input:
["DetectSquares", "add", "add", "add", "count", "count", "add", "count"]
[[], [[3, 1]], [[11, 1]], [[11, 7]], [[11, 1]], [[14, 8]], [[11, 2]], [[11, 1]]]

Output:
[null, null, null, null, 1, 0, null, 2]
```

### Explanation

```text
DetectSquares detectSquares = new DetectSquares();
detectSquares.add([3, 1]);
detectSquares.add([11, 1]);
detectSquares.add([11, 7]);
detectSquares.count([11, 1]); // return 1. You can form one square with points [3,1], [11,1], [11,7], and query point [11,1].
detectSquares.count([14, 8]); // return 0. The query point cannot form any square.
detectSquares.add([11, 2]);   // Duplicate point added.
detectSquares.count([11, 1]); // return 2. Forming squares can choose between the two [11, 2] points.
```

---

# Intuition

Searching through all combinations of points blindly to find squares would cost an impractical \(O(N^3)\) computational runtime penalty. 

To optimize this down to an efficient **linear \(O(P_{\text{diagonal}})\) query pass**, we can leverage a **Frequency Coordinate Directory**:
1. **The Diagonal Rule:** A query point \(Q = (x_1, y_1)\) and a diagonal corner point \(D = (x_2, y_2)\) uniquely define exactly **one** axis-aligned square. For \(D\) to form a valid diagonal, it must satisfy two structural rules:
   - It cannot share the same coordinate lane as the query point (\(x_1 \neq x_2\) and \(y_1 \neq y_2\)).
   - The horizontal distance must equal the vertical distance, satisfying the square aspect ratio property:
     \[\Delta X == \Delta Y \implies \vert{}x_1 - x_2\vert{} == \vert{}y_1 - y_2\vert{}\]
2. **Corner Multiplications:** Once a valid diagonal partner \(D = (x_2, y_2)\) is located, the locations of the remaining two corner nodes are locked in automatically:
   - Corner 3 (\(C_3\)) must sit at: \((x_1, y_2)\)
   - Corner 4 (\(C_4\)) must sit at: \((x_2, y_1)\)
3. **Combinatorial Counts:** Since points can duplicate, the total combinations of ways to build a square using these specific vertices matches the product of their individual occurrence counts:
   \[\text{Ways} = \text{Count}(D) \times \text{Count}(C_3) \times \text{Count}(C_4)\]

By storing points inside a high-speed nested tracking map directory (`Map<Integer, Map<Integer, Integer>>` tracking `X -> Y -> Count`), we can loop through only the points sharing a column/row or treat coordinates as pairs, look up diagonal bounds, and multiply node counts instantly in \(O(1)\) average time.

---

# State Definition

The analytical streaming pipeline tracks coordinate frequencies on the heap using a nested map directory or flattened 2D frequency array matrix bounds:

```java
Map<Integer, Map<Integer, Integer>> pointCounts; // Maps X-coordinate -> Y-coordinate -> Frequency count
List<int[]> pointsList;                          // Flat register tracking all unique/added point entities
```

---

# Recurrence / Structural Matching Condition

```text
Function add(x, y):
    pointCounts[x][y]++
    pointsList.add([x, y])

Function count(qX, qY):
    totalSquares = 0
    For [pX, pY] in pointsList:
        If abs(qX - pX) != abs(qY - pY) OR qX == pX OR qY == pY -> Continue
        
        // Downstream corners are structurally locked
        int countD = pointCounts[pX][pY]
        int countC3 = pointCounts[qX][pY]
        int countC4 = pointCounts[pX][qY]
        
        totalSquares += countD * countC3 * countC4
    Return totalSquares
```

---

# Optimal Coordinate Mapping Solution

## Idea

We save point coordinates to a central registry list to drive our search loop, while simultaneously maintaining a nested frequency directory map to enable immediate lookups. During queries, we treat each recorded node as a potential diagonal corner candidate, checking edge ratios and multiplying relative frequencies instantly.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class DetectSquares {
    // Nested frequency map directory tracking: X_coordinate -> Y_coordinate -> Count
    private final Map<Integer, Map<Integer, Integer>> pointCounts;
    // Flat register holding all added point items to streamline our loop passes
    private final List<int[]> pointsList;

    public DetectSquares() {
        this.pointCounts = new HashMap<>();
        this.pointsList = new ArrayList<>();
    }
    
    public void add(int[] point) {
        int x = point[0];
        int y = point[1];

        // Ensure the inner row map exists for the active X column track
        this.pointCounts.putIfAbsent(x, new HashMap<>());
        Map<Integer, Integer> yCounts = this.pointCounts.get(x);
        yCounts.put(y, yCounts.getOrDefault(y, 0) + 1);

        // Record the point reference to our flat traversal tracker list
        this.pointsList.add(point);
    }
    
    public int count(int[] point) {
        int qX = point[0];
        int qY = point[1];
        int totalSquares = 0;

        // Core Step: Loop through all points treating each as a potential diagonal vertex
        for (int[] p : this.pointsList) {
            int pX = p[0];
            int pY = p[1];

            // Invariant Filter A: Skip if points share matching axes or fail square aspect ratios
            // |qX - pX| == |qY - pY| ensures the horizontal and vertical side lengths are identical
            if (Math.abs(qX - pX) != Math.abs(qY - pY) || qX == pX || qY == pY) {
                continue;
            }

            // Look up the frequency counts of the remaining two corner nodes
            // Corner 3 sits at (qX, pY) and Corner 4 sits at (pX, qY)
            int countC3 = this.getPointCount(qX, pY);
            int countC4 = this.getPointCount(pX, qY);

            // Accumulate combinatorial counts based on corner combinations
            if (countC3 > 0 && countC4 > 0) {
                totalSquares += countC3 * countC4;
            }
        }

        return totalSquares;
    }

    // High-speed constant-time frequency lookup utility helper
    private int getPointCount(int x, int y) {
        if (!this.pointCounts.containsKey(x)) {
            return 0;
        }
        return this.pointCounts.get(x).getOrDefault(y, 0);
    }
}
```

---

## Complexity Analysis

Let \(N\) be the total number of points added to the data stream up to the active runtime frame.

### Time Complexity

- **`add` Method:** \(O(1)\) constant time. Inserting entries and updating scalar counts inside nested hash directories evaluates instantly.
- **`count` Method:** \(O(N)\) linear time. The search sweep iterates across the \(N\) point coordinates recorded inside our tracking collection list. Inside the loop, looking up missing corner frequencies scales in \(O(1)\) average constant time, keeping the query timeline entirely linear.

---

### Space Complexity

- **Auxiliary Space Complexity:** \(O(N)\) memory allocations are required on the heap to store coordinate pairs concurrently inside the traversal list tracker and frequency directory mappings.

---

# Key Takeaways

✅ Core fundamental paradigm for processing 2D grid geometry lookups, data stream aggregations, and hash coordinate maps.

✅ **The Diagonal Vertex Anchoring Trick:** Identifying that a square can be uniquely determined by just *one diagonal pairing* is the key optimization choice here. By treating every element in our collection as a diagonal node option and calculating the remaining two corner positions via fixed transformations, we turn a heavy multi-variable combinatorics problem into a single fast linear pass.

✅ Directly drives real-world microarchitectures like **computer vision object bounding box trackers** (detecting symmetrical rectangular features inside pixel streams), geographic mapping layers evaluating intersecting spatial coordinates grid structures, and radar signal analyzers.

### Similar Problems

- Max Points on a Line ([LeetCode 149](https://leetcode.com))
- Minimum Area Rectangle ([LeetCode 939](https://leetcode.com))
- Number of Valid Triplets in a Grid
- Set Matrix Zeroes ([LeetCode 73](https://leetcode.com))
