# Design Browser History (LeetCode 1472)

## Problem Statement

You have a browser with one tab and start on the homepage.

Implement the `BrowserHistory` class:

### BrowserHistory(String homepage)

Initializes the browser with the homepage.

---

### visit(String url)

Visits `url` from the current page.

After visiting a new page:

```text
All forward history is cleared.
```

---

### back(int steps)

Move `steps` pages back in history.

Return the current page after moving back.

If there are fewer than `steps` pages:

```text
Move as far back as possible.
```

---

### forward(int steps)

Move `steps` pages forward in history.

Return the current page after moving forward.

If there are fewer than `steps` pages:

```text
Move as far forward as possible.
```

---

## Example

### Input

```java
BrowserHistory browserHistory = new BrowserHistory("leetcode.com");

browserHistory.visit("google.com");
browserHistory.visit("facebook.com");
browserHistory.visit("youtube.com");

browserHistory.back(1);
browserHistory.back(1);

browserHistory.forward(1);

browserHistory.visit("linkedin.com");

browserHistory.forward(2);

browserHistory.back(2);

browserHistory.back(7);
```

### Output

```text
facebook.com
google.com
facebook.com
linkedin.com
google.com
leetcode.com
```

---

# Intuition

Browser navigation naturally behaves like:

```text
← Back
→ Forward
```

When:

```text
visit(newUrl)
```

all forward pages become invalid.

Example:

```text
A → B → C
        ↑
     Current
```

Back:

```text
A → B → C
    ↑
 Current
```

Now visit:

```text
D
```

History becomes:

```text
A → B → D
```

Page:

```text
C
```

is removed forever.

---

# Best Approach: Dynamic Array + Current Pointer

Maintain:

```java
List<String> history
```

and

```java
currentIndex
```

Example:

```text
history =

[leetcode, google, facebook]

current = 2
```

---

# Data Structure

```java
ArrayList<String> history
```

Store all pages.

```java
int current
```

Current page index.

```java
int last
```

Last valid page in history.

---

## Visualization

```text
Index

0          1          2
--------------------------------
leetcode | google | facebook
                     ↑
                  current
```

---

# Visit Operation

Suppose:

```text
history:

A B C D

current = B
```

Visit:

```text
E
```

Forward history:

```text
C D
```

must be removed.

Result:

```text
A B E
```

---

# Approach

### visit(url)

```text
Move current forward
Store URL
Update last valid index
```

---

### back(steps)

Move:

```text
current = max(0, current - steps)
```

Return current page.

---

### forward(steps)

Move:

```text
current = min(last, current + steps)
```

Return page.

---

# Dry Run

## Initial

```java
BrowserHistory("leetcode.com")
```

History:

```text
[leetcode]
```

Current:

```text
0
```

---

## visit("google.com")

```text
[leetcode, google]
```

Current:

```text
1
```

---

## visit("facebook.com")

```text
[leetcode, google, facebook]
```

Current:

```text
2
```

---

## back(1)

```text
current = 1
```

Page:

```text
google.com
```

---

## forward(1)

```text
current = 2
```

Page:

```text
facebook.com
```

---

## visit("linkedin.com")

Forward history cleared.

```text
[leetcode, google, facebook, linkedin]
```

Current:

```text
3
```

---

# Java Solution (Optimal)

```java
import java.util.*;

class BrowserHistory {

    private List<String> history;
    private int current;
    private int last;

    public BrowserHistory(String homepage) {

        history = new ArrayList<>();

        history.add(homepage);

        current = 0;
        last = 0;
    }

    public void visit(String url) {

        current++;

        if (current < history.size()) {
            history.set(current, url);
        } else {
            history.add(url);
        }

        last = current;
    }

    public String back(int steps) {

        current = Math.max(
            0,
            current - steps
        );

        return history.get(current);
    }

    public String forward(int steps) {

        current = Math.min(
            last,
            current + steps
        );

        return history.get(current);
    }
}
```

---

# Why Does Visit Work?

Suppose:

```text
History:

A B C D

current = 1
      ↑
      B
```

Visit:

```text
E
```

We overwrite:

```text
A B E D
```

and set:

```text
last = E
```

Effectively:

```text
A B E
```

Only valid history remains.

---

# Alternative Solution Using Doubly Linked List

A browser naturally behaves like:

```text
Previous ↔ Current ↔ Next
```

A doubly linked list can support:

```text
back()
forward()
visit()
```

all in:

```text
O(1)
```

per move.

However, the ArrayList solution is simpler and is the intended LeetCode solution.

---

# Correctness Proof

Let:

```text
current
```

represent the index of the current webpage.

### Back

```text
Move left by steps.
```

Cannot go below:

```text
0
```

thus returns the correct page.

---

### Forward

```text
Move right by steps.
```

Cannot exceed:

```text
last
```

thus returns the correct page.

---

### Visit

When visiting a new page:

```text
All forward history must disappear.
```

Updating:

```text
last = current
```

ensures no forward page beyond
