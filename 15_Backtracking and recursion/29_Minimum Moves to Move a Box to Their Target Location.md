# Minimum Moves to Move a Box to Their Target Location ([LeetCode 1263](https://leetcode.com))

## Problem Statement

A storekeeper is in an `m x n` grid board and wants to push a box `'B'` to a target location `'T'`. 

The grid contains:
- `'S'`: The **storekeeper's** initial position.
- `'B'`: The **box's** initial position.
- `'T'`: The **target's** location.
- `'.'`: **Empty fields** that can be walked on.
- `'#'`: **Obstacles** that cannot be walked on.

Return *the **minimum number of pushes** required to move the box to the target location*. If it is impossible to move the box to the target, return `-1`.

### Key Rules
- The storekeeper can move **Up, Down, Left, or Right** to an adjacent empty cell.
- The storekeeper can **push** the box to an adjacent empty cell by moving into the box's current cell from the **exact opposite side** (e.g., to push the box Right, the keeper must stand on the Left side of the box).
- Every time the box shifts, it counts as **1 push move**. Regular storekeeper walking steps do not count toward the push total.
- Neither the storekeeper nor the box can step into obstacle cells `'#'`.

---

## Examples

### Example 1

```text
Input: grid = [["#","#","#","#","#","#"],
                ["#","T","#","#","#","#"],
                ["#",".",".","B",".","#"],
                ["#",".","#","#",".","#"],
                ["#",".",".",".","S","#"],
                ["#","#","#","#","#","#"]]
Output: 3
```

### Explanation

```text
The box needs to be pushed 3 times:
1. Pushing the box Left shifts it to column 2.
2. Pushing the box Up shifts it to row 1.
3. Pushing the box Left shifts it onto 'T'.
Regular storekeeper steps around obstacles to get into pushing position do not count.
```

---

# Intuition

This problem is a classic variation of the **Sokoban** puzzle game. Because we need to find the *minimum* number of pushes, it maps naturally onto a **Shortest Path Grid Search**. However, a standard 2D Breadth-First Search (BFS) tracking only the box's coordinates `(boxRow, boxCol)` is insufficient because the box can only move if the storekeeper can physically walk to the opposite side to push it.

Thus, a valid state must track a **4D coordinate tuple**: 
```text
State = (boxRow, boxCol, playerRow, playerCol)
```

Since we want to minimize the number of *box pushes* (cost = 1) rather than the player's walking steps (cost = 0), this can be modeled as a **0-1 Breadth-First Search** utilizing a **Deque (Double-Ended Queue)** or a Priority Queue (Dijkstra's Algorithm):
1. **Player Walks (Cost = 0):** If the player steps into an empty adjacent cell without moving the box, the push count doesn't increase. We push this new subproblem state to the **front** of our Deque.
2. **Player Pushes Box (Cost = 1):** If the player steps into the box's cell and the cell exactly behind the box in that same direction is empty, the box shifts. The push count increases by 1. We push this new state to the **back** of our Deque.
3. **Reachability Check:** To optimize operations, every time we evaluate a box push, we can run a mini nested 2D BFS or DFS to verify if the player can physically walk from their current position to the required pushing slot without hitting the box or obstacles.

---

# State Definition (Optimal 0-1 BFS Approach)

To trace our state transitions cleanly while preventing cyclic search paths:

```text
State matrix: boolean[M][N][M][N] visited
- visited[bR][bC][pR][pC] flags whether a specific combination of box position and player position has already been processed.
```

---

# Recurrence / Structural State Condition

```text
While deque is not empty:
    Pop current state (bR, bC, pR, pC, pushes)
    If (bR, bC) == Target -> Return pushes
    
    Explore 4 directions for player movement (next_pR, next_pC):
        If next_pR == bR AND next_pC == bC:
            // This is a push attempt
            calc next_bR = bR + dRow, next_bC = bC + dCol
            If next_bR, next_bC inside bounds AND not '#' AND state not visited:
                Mark visited
                Add to BACK of deque with (pushes + 1)
        Else if next_pR, next_pC inside bounds AND not '#' AND state not visited:
            // This is a regular walk step
            Mark visited
            Add to FRONT of deque with (pushes)
```

---

# Optimal 0-1 BFS Solution using a Deque

## Idea

The implementation tracks the box and keeper states simultaneously using a 4D visited matrix cache. By using a Deque, walk steps (cost 0) are prioritized immediately at the front, while push transitions (cost 1) wait at the back. This guarantees that the first time the box lands on the target coordinate, the path found is mathematically optimal.

---

## Java Code

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    // 4D Coordinate Container Block tracking the game state on the heap
    private static class State {
        int bR, bC; // Box Row and Column
        int pR, pC; // Player Row and Column
        int pushes; // Cumulative push move counter register

        State(int bR, int bC, int pR, int pC, int pushes) {
            this.bR = bR;
            this.bC = bC;
            this.pR = pR;
            this.pC = pC;
            this.pushes = pushes;
        }
    }

    public int minPushBox(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;

        int startBR = -1, startBC = -1;
        int startPR = -1, startPC = -1;
        int targetR = -1, targetC = -1;

        // Step 1: Scan the grid to extract coordinates for all primary entities
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 'B') {
                    startBR = r; startBC = c;
                } else if (grid[r][c] == 'S') {
                    startPR = r; startPC = c;
                } else if (grid[r][c] == 'T') {
                    targetR = r; targetC = c;
                }
            }
        }

        // 4D state cache to track visited configurations and prevent infinite loop cycles
        boolean[][][][] visited = new boolean[rows][cols][rows][cols];
        
        // Deque structure to implement 0-1 BFS state prioritization
        Deque<State> deque = new ArrayDeque<>();
        
        State initial = new State(startBR, startBC, startPR, startPC, 0);
        deque.addFirst(initial);
        visited[startBR][startBC][startPR][startPC] = true;

        int[] dRow = {1, -1, 0, 0};
        int[] dCol = {0, 0, 1, -1};

        // Step 2: Launch 0-1 Breadth-First Search
        while (!deque.isEmpty()) {
            State curr = deque.pollFirst();

            // Success Boundary Condition: Box has successfully landed on target
            if (curr.bR == targetR && curr.bC == targetC) {
                return curr.pushes;
            }

            // Explore all 4 cardinal directions for player movement
            for (int i = 0; i < 4; i++) {
                int nextPR = curr.pR + dRow[i];
                int nextPC = curr.pC + dCol[i];

                // Check grid boundaries and obstacle blocks for keeper walking steps
                if (nextPR < 0 || nextPR >= rows || nextPC < 0 || nextPC >= cols || grid[nextPR][nextPC] == '#') {
                    continue;
                }

                // Collision Check: If the player walks directly into the box, this triggers a PUSH attempt
                if (nextPR == curr.bR && nextPC == curr.bC) {
                    int nextBR = curr.bR + dRow[i];
                    int nextBC = curr.bC + dCol[i];

                    // Check bounds and ensure the space behind the box is empty to receive the push
                    if (nextBR >= 0 && nextBR < rows && nextBC >= 0 && nextBC < cols && grid[nextBR][nextBC] != '#') {
                        if (!visited[nextBR][nextBC][nextPR][nextPC]) {
                            visited[nextBR][nextBC][nextPR][nextPC] = true;
                            // Push event has cost = 1 -> Append to the BACK of the deque
                            deque.addLast(new State(nextBR, nextBC, nextPR, nextPC, curr.pushes + 1));
                        }
                    }
                } else {
                    // Regular walking step event has cost = 0 -> Priority insert to the FRONT of the deque
                    if (!visited[curr.bR][curr.bC][nextPR][nextPC]) {
                        visited[curr.bR][curr.bC][nextPR][nextPC] = true;
                        deque.addFirst(new State(curr.bR, curr.bC, nextPR, nextPC, curr.pushes));
                    }
                }
            }
        }

        return -1; // Target location structurally unachievable
    }
}
```

---

## Complexity Analysis

Let M × N be the total grid matrix dimensions size.

### Time Complexity

```text
Time Complexity: O(M² × N²)
```
*Explanation:* The state space size is bounded securely by the 4D configuration profile of the box position pairs multiplied by the player position pairs, yielding M × N × M × N = M² × N² possible states. Since each state configuration is added to and processed from our 0-1 BFS Deque exactly once in constant O(1) time, the total execution runtime complexity remains strictly polynomial.

---

### Space Complexity

```text
Space Complexity: O(M² × N²)
```

```text
Explanation: Auxiliary storage space scales proportionally to accommodate the 4D boolean tracking cache matrix grid visited and the memory allocations needed to hold active state tuples concurrently inside the search Deque workspace buffer.

Key Takeaways

✅ Advanced graph search problem representing the golden standard for 0-1 Shortest Path state architectures.
✅ The 0-1 BFS Deque Invariant: When a graph problem features varying edge weights restricted to 0 and 1, standard queue-based BFS fails, and full Dijkstra's algorithm adds unnecessary sorting latency. Using a Deque to prioritize cost-0 updates at the front and cost-1 updates at the back maintains strict queue monotonicity using efficient constant time modifications.
✅ Directly drives real-world microarchitectures like industrial warehouse automated guided vehicles (AGVs) navigating obstacles to push containers, robotic path routers, and game physics solvers.
```
