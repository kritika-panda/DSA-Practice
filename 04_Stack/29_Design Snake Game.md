# Design Snake Game

## Problem Statement

Design a Snake Game that is played on a device with:

```text
width × height
```

grid.

The snake starts at:

```text
(0,0)
```

Food appears sequentially.

Implement:

```java
SnakeGame(int width, int height, int[][] food)
```

and

```java
int move(String direction)
```

Return:

```text
Current score after the move
```

or

```text
-1 if game over
```

---

## Example

```java
SnakeGame game =
new SnakeGame(
        3,
        2,
        new int[][]{
                {1,2},
                {0,1}
        }
);

game.move("R"); // 0
game.move("D"); // 0
game.move("R"); // 1
game.move("U"); // 1
game.move("L"); // 2
game.move("U"); // -1
```

---

# Requirements

### Snake Movement

```text
U → Up
D → Down
L → Left
R → Right
```

---

### Food Consumption

If snake reaches food:

```text
Increase score
Increase snake length
Do not remove tail
```

---

### Normal Move

```text
Move head forward
Remove tail
Length remains same
```

---

### Game Over Conditions

#### Boundary Collision

```text
row < 0
row >= height
col < 0
col >= width
```

---

#### Self Collision

```text
Head touches any part of snake body
```

---

# Intuition

To support every move efficiently we need:

```text
Add head      → O(1)
Remove tail   → O(1)
Check collision → O(1)
```

The best combination is:

```text
Deque + HashSet
```

---

# Data Structures

## Deque

Stores snake body.

```java
Deque<Integer> snake
```

Structure:

```text
Head
 ↓
[7, 5, 2]
        ↑
      Tail
```

Operations:

```text
Add Head     → offerFirst()
Remove Tail  → pollLast()
```

Both:

```text
O(1)
```

---

## HashSet

Stores occupied cells.

```java
Set<Integer> occupied
```

Used for:

```text
Fast collision detection
```

Lookup:

```text
O(1)
```

---

# Cell Encoding

Instead of storing:

```java
(row, col)
```

store:

```java
row * width + col
```

Example:

```text
width = 5

Cell:
(2,3)

Encoded:

2 * 5 + 3

= 13
```

---

# Algorithm

## Step 1

Fetch current head.

```java
int head = snake.peekFirst();
```

---

## Step 2

Decode position.

```java
row = head / width
col = head % width
```

---

## Step 3

Move according to direction.

```text
U → row--
D → row++
L → col--
R → col++
```

---

## Step 4

Check boundary collision.

If outside grid:

```java
return -1;
```

---

## Step 5

Generate new head position.

```java
newHead = row * width + col;
```

---

## Step 6

Check food.

If food exists:

```text
Grow snake
Do not remove tail
Increase score
```

Otherwise:

```text
Remove tail
```

---

## Step 7

Check self collision.

```java
if(occupied.contains(newHead))
    return -1;
```

---

## Step 8

Insert new head.

Return current score.

---

# Java Code

```java
import java.util.*;

class SnakeGame {

    private int width;
    private int height;

    private int[][] food;
    private int foodIndex;

    private Deque<Integer> snake;
    private Set<Integer> occupied;

    public SnakeGame(
            int width,
            int height,
            int[][] food) {

        this.width = width;
        this.height = height;
        this.food = food;

        snake = new LinkedList<>();
        occupied = new HashSet<>();

        snake.offerFirst(0);
        occupied.add(0);

        foodIndex = 0;
    }

    public int move(String direction) {

        int head = snake.peekFirst();

        int row = head / width;
        int col = head % width;

        switch (direction) {

            case "U":
                row--;
                break;

            case "D":
                row++;
                break;

            case "L":
                col--;
                break;

            case "R":
                col++;
                break;
        }

        // Boundary collision

        if (row < 0 || row >= height ||
            col < 0 || col >= width) {
            return -1;
        }

        int newHead = row * width + col;

        // Food found

        if (foodIndex < food.length &&
            row == food[foodIndex][0] &&
            col == food[foodIndex][1]) {

            foodIndex++;

        } else {

            int tail = snake.pollLast();
            occupied.remove(tail);
        }

        // Self collision

        if (occupied.contains(newHead)) {
            return -1;
        }

        snake.offerFirst(newHead);
        occupied.add(newHead);

        return foodIndex;
    }
}
```

---

# Dry Run

## Input

```text
Width  = 3
Height = 2

Food = [[1,2],[0,1]]
```

Initial Snake:

```text
[(0,0)]
```

Score:

```text
0
```

---

## Move R

```text
(0,0) → (0,1)
```

Snake:

```text
[(0,1)]
```

Score:

```text
0
```

---

## Move D

```text
(0,1) → (1,1)
```

Snake:

```text
[(1,1)]
```

Score:

```text
0
```

---

## Move R

Food found:

```text
(1,2)
```

Snake:

```text
[(1,2),(1,1)]
```

Score:

```text
1
```

---

## Move U

```text
(1,2) → (0,2)
```

Snake:

```text
[(0,2),(1,2)]
```

Score:

```text
1
```

---

## Move L

Food found:

```text
(0,1)
```

Snake:

```text
[(0,1),(0,2),(1,2)]
```

Score:

```text
2
```

---

# Important Edge Case

Consider:

```text
Head → A → B → Tail
```

Suppose next move goes into:

```text
Tail's position
```

This is valid because:

```text
Tail leaves simultaneously.
```

Therefore:

```java
Remove Tail
Then Check Collision
```

must be the order.

---

# Visualization

```text
Initial

S . .
. . .

Move R

. S .
. . .

Move D

. . .
. S .

Move R (Food)

. . .
. B H
```

Where:

```text
H = Head
B = Body
```

---

# Why Deque + HashSet?

| Operation | Deque | HashSet |
|------------|--------|---------|
| Add Head | O(1) | O(1) |
| Remove Tail | O(1) | O(1) |
| Collision Check | ❌ | O(1) |
| Maintain Body Order | O(1) | ❌ |

Using both gives:

```text
O(1) per move
```

---

# Time Complexity

### move()

```text
O(1)
```

All operations:

```text
HashSet lookup
Insert
Delete
Deque operations
```

are constant time.

---

# Space Complexity

Let:

```text
L = current snake length
```

Deque:

```text
O(L)
```

HashSet:

```text
O(L)
```

Total:

```text
O(L)
```

---

# Pattern Recognition

```text
Design Pattern
│
├── Design Snake Game ✅
├── Design Tic Tac Toe
├── Design Browser History
├── Design Circular Queue
├── Design Authentication Manager
├── Design Hit Counter
└── Design Underground System
```

### Interview Trick

Whenever a design problem requires:

```text
Add at one end
Remove at another end
Fast membership lookup
```

Think:

```text
Deque
+
HashSet
```

For Snake Game, the key insight is:

```text
Deque maintains body order.

HashSet provides O(1)
self-collision detection.
```
