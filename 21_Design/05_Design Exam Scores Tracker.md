# Design Exam Scores Tracker

## Problem Statement

Design a data structure that tracks exam scores and efficiently returns the average score of the latest `k` exams.

Implement the `ExamScoresTracker` class:

```java
ExamScoresTracker(int capacity)
```

Initializes the tracker with a maximum capacity.

```java
void addScore(int score)
```

Adds a new exam score.

If the tracker is full, remove the oldest score before adding the new one.

```java
double getAverage()
```

Returns the average of all current scores.

```java
int getLatestScore()
```

Returns the most recently added score.

```java
int getTotalExams()
```

Returns the number of scores currently stored.

---

## Example

### Input

```java
ExamScoresTracker tracker = new ExamScoresTracker(3);

tracker.addScore(80);
tracker.addScore(90);
tracker.addScore(70);

tracker.getAverage();
tracker.addScore(100);

tracker.getAverage();
```

### Output

```java
80.0
86.67
```

### Explanation

Initially:

```java
[80, 90, 70]
```

Average:

```java
(80 + 90 + 70) / 3 = 80
```

After adding:

```java
100
```

Oldest score:

```java
80
```

is removed.

Tracker becomes:

```java
[90, 70, 100]
```

Average:

```java
(90 + 70 + 100) / 3 = 86.67
```

---

# Intuition

We need:

- Fast insertion of scores.
- Fast removal of the oldest score.
- Fast calculation of average.

A simple queue is ideal because:

```java
First In → First Out (FIFO)
```

The oldest score is always removed first.

To avoid recalculating the sum every time:

- Maintain a running sum.
- Update it during insertion and deletion.

---

# Approach

Use:

```java
Queue<Integer> scores
long sum
int capacity
```

### Adding a Score

If the queue is full:

```java
remove oldest score
subtract from sum
```

Then:

```java
add new score
update sum
```

### Getting Average

```java
average = sum / currentSize
```

---

# Dry Run

### Capacity = 3

```java
addScore(80)
```

Queue:

```java
[80]
```

Sum:

```java
80
```

---

```java
addScore(90)
```

Queue:

```java
[80,90]
```

Sum:

```java
170
```

---

```java
addScore(70)
```

Queue:

```java
[80,90,70]
```

Sum:

```java
240
```

Average:

```java
240 / 3 = 80
```

---

```java
addScore(100)
```

Queue full.

Remove:

```java
80
```

Queue:

```java
[90,70]
```

Sum:

```java
160
```

Add:

```java
100
```

Queue:

```java
[90,70,100]
```

Sum:

```java
260
```

Average:

```java
260 / 3 = 86.67
```

---

# Java Solution

```java
import java.util.*;

class ExamScoresTracker {

    private Queue<Integer> scores;
    private int capacity;
    private long sum;

    public ExamScoresTracker(int capacity) {
        this.capacity = capacity;
        this.scores = new LinkedList<>();
        this.sum = 0;
    }

    public void addScore(int score) {

        if (scores.size() == capacity) {
            sum -= scores.poll();
        }

        scores.offer(score);
        sum += score;
    }

    public double getAverage() {

        if (scores.isEmpty()) {
            return 0.0;
        }

        return (double) sum / scores.size();
    }

    public int getLatestScore() {

        if (scores.isEmpty()) {
            return -1;
        }

        int latest = -1;

        for (int score : scores) {
            latest = score;
        }

        return latest;
    }

    public int getTotalExams() {
        return scores.size();
    }
}
```

---

# Optimized Version for O(1) Latest Score

Instead of traversing the queue, maintain:

```java
int latestScore
```

during insertion.

```java
private int latestScore;

public void addScore(int score) {

    if (scores.size() == capacity) {
        sum -= scores.poll();
    }

    scores.offer(score);
    sum += score;
    latestScore = score;
}

public int getLatestScore() {
    return scores.isEmpty() ? -1 : latestScore;
}
```

---

# Complexity Analysis

### addScore()

```java
Time Complexity: O(1)
```

### getAverage()

```java
Time Complexity: O(1)
```

### getLatestScore()

```java
Time Complexity: O(1)
```

### Space Complexity

```java
O(capacity)
```

---

# Follow-up: Circular Queue Implementation

For stricter interview requirements:

- Use a circular array instead of `LinkedList`.
- Maintain:
  - `front`
  - `rear`
  - `size`
  - `sum`

This reduces object overhead while keeping all operations in:

```java
O(1)
```

---

# Key Takeaways

- The problem is essentially a **fixed-size moving window of scores**.
- Queue naturally handles removal of the oldest score.
- Maintain a running sum for constant-time average calculation.
- Track the latest score separately for `O(1)` access.
- All major operations run in **O(1)** time.
- Tests understanding of **Queue Design**, **Sliding Window**, and **Data Stream Processing** concepts.
