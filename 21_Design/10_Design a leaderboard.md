# Design a Leaderboard

**LeetCode 1244 - Design A Leaderboard**

## Problem Statement

Design a leaderboard system that supports:

```java
Leaderboard()
```

Initialize the leaderboard.

```java
void addScore(int playerId, int score)
```

Adds `score` to the player's score.

If the player does not exist, add them.

```java
int top(int K)
```

Return the sum of the top `K` players' scores.

```java
void reset(int playerId)
```

Reset the player's score to `0`.

---

## Example

```java
Leaderboard leaderboard = new Leaderboard();

leaderboard.addScore(1, 73);
leaderboard.addScore(2, 56);
leaderboard.addScore(3, 39);
leaderboard.addScore(4, 51);
leaderboard.addScore(5, 4);

leaderboard.top(1); // 73

leaderboard.reset(1);
leaderboard.reset(2);

leaderboard.addScore(2, 51);

leaderboard.top(3); // 141
```

---

# Intuition

We need to support:

```java
1. Update Player Score
2. Reset Player Score
3. Get Top K Scores
```

A simple HashMap can store player scores:

```java
playerId -> score
```

For `top(K)`:

- Collect all scores
- Use a max heap
- Extract K largest values

---

# Approach 1: HashMap + Max Heap

## Data Structure

```java
Map<Integer, Integer> scores
```

Stores:

```java
playerId -> totalScore
```

---

## addScore()

```java
score += newScore
```

---

## reset()

```java
score = 0
```

or remove player.

---

## top(K)

1. Put all scores into max heap.
2. Extract top K elements.
3. Return their sum.

---

# Dry Run

### Scores

```java
1 -> 73
2 -> 56
3 -> 39
4 -> 51
5 -> 4
```

### top(3)

Heap:

```java
73, 56, 51, 39, 4
```

Take:

```java
73 + 56 + 51
```

Answer:

```java
180
```

---

# Java Solution

```java
import java.util.*;

class Leaderboard {

    private Map<Integer, Integer> scores;

    public Leaderboard() {
        scores = new HashMap<>();
    }

    public void addScore(int playerId, int score) {

        scores.put(
            playerId,
            scores.getOrDefault(playerId, 0) + score
        );
    }

    public int top(int K) {

        PriorityQueue<Integer> maxHeap =
            new PriorityQueue<>(Collections.reverseOrder());

        maxHeap.addAll(scores.values());

        int sum = 0;

        while (K-- > 0 && !maxHeap.isEmpty()) {
            sum += maxHeap.poll();
        }

        return sum;
    }

    public void reset(int playerId) {
        scores.remove(playerId);
    }
}
```

---

# Complexity Analysis

### addScore()

```java
O(1)
```

### reset()

```java
O(1)
```

### top(K)

Building heap:

```java
O(N)
```

Extract K elements:

```java
O(K log N)
```

Overall:

```java
O(N + K log N)
```

---

# Approach 2: HashMap + TreeMap (Optimal)

## Observation

The bottleneck is:

```java
top(K)
```

We can maintain score frequencies.

### Data Structures

```java
HashMap<playerId, score>
TreeMap<score, frequency>
```

The TreeMap automatically keeps scores sorted.

---

## Example

```java
playerScores:

1 -> 73
2 -> 56
3 -> 39
```

```java
scoreFrequency:

39 -> 1
56 -> 1
73 -> 1
```

---

## top(K)

Traverse TreeMap from highest score.

```java
descendingMap()
```

and take K players.

---

# Java Solution (Optimal)

```java
import java.util.*;

class Leaderboard {

    private Map<Integer, Integer> playerScores;
    private TreeMap<Integer, Integer> scoreFrequency;

    public Leaderboard() {

        playerScores = new HashMap<>();
        scoreFrequency = new TreeMap<>();
    }

    public void addScore(int playerId, int score) {

        if (playerScores.containsKey(playerId)) {

            int oldScore = playerScores.get(playerId);

            scoreFrequency.put(
                oldScore,
                scoreFrequency.get(oldScore) - 1
            );

            if (scoreFrequency.get(oldScore) == 0) {
                scoreFrequency.remove(oldScore);
            }

            score += oldScore;
        }

        playerScores.put(playerId, score);

        scoreFrequency.put(
            score,
            scoreFrequency.getOrDefault(score, 0) + 1
        );
    }

    public int top(int K) {

        int sum = 0;

        for (Map.Entry<Integer, Integer> entry :
                scoreFrequency.descendingMap().entrySet()) {

            int score = entry.getKey();
            int freq = entry.getValue();

            int playersToTake = Math.min(K, freq);

            sum += playersToTake * score;

            K -= playersToTake;

            if (K == 0) {
                break;
            }
        }

        return sum;
    }

    public void reset(int playerId) {

        int score = playerScores.get(playerId);

        scoreFrequency.put(
            score,
            scoreFrequency.get(score) - 1
        );

        if (scoreFrequency.get(score) == 0) {
            scoreFrequency.remove(score);
        }

        playerScores.remove(playerId);
    }
}
```

---

# Why TreeMap is Better?

Suppose:

```java
100000 players
```

If `top(5)` is called frequently:

**Heap Approach**

```java
Create heap every time
```

Cost:

```java
O(N)
```

---

**TreeMap Approach**

Scores remain sorted continuously.

Cost:

```java
O(K + log N)
```

for most operations.

---

# Complexity Analysis (Optimal)

### addScore()

```java
O(log N)
```

### reset()

```java
O(log N)
```

### top(K)

```java
O(K + number of distinct scores)
```

Typically much faster than rebuilding a heap.

---

# Comparison

| Approach | addScore | reset | top(K) |
|-----------|----------|--------|---------|
| HashMap + Heap | O(1) | O(1) | O(N + K log N) |
| HashMap + TreeMap | O(log N) | O(log N) | O(K) approx |

---

# Key Takeaways

- Maintain player scores using a `HashMap`.
- For a straightforward solution, use a max heap during `top(K)`.
- For the optimal LeetCode solution, maintain:
  ```java
  HashMap<Player, Score>
  TreeMap<Score, Frequency>
  ```
- `TreeMap` keeps scores sorted at all times.
- This avoids rebuilding a heap every query.
- Core concepts:
  - HashMap
  - Heap
  - Ordered Map (TreeMap)
  - Frequency Counting
- Expected interview answer: **HashMap + TreeMap**.
