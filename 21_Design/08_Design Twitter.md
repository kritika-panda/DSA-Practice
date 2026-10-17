# Design Twitter

## Problem Statement

Design a simplified version of Twitter where users can:

- Post tweets
- Follow a user
- Unfollow a user
- Retrieve the 10 most recent tweets in their news feed

Implement the `Twitter` class:

```java
Twitter()
```

Initializes the object.

```java
void postTweet(int userId, int tweetId)
```

Compose a new tweet.

```java
List<Integer> getNewsFeed(int userId)
```

Retrieve the 10 most recent tweet IDs from the user's news feed.

The news feed should contain tweets posted by:

- The user himself
- Users that he follows

Tweets should be ordered from most recent to least recent.

```java
void follow(int followerId, int followeeId)
```

Follower follows a followee.

```java
void unfollow(int followerId, int followeeId)
```

Follower unfollows a followee.

---

# Example

### Input

```java
["Twitter",
 "postTweet",
 "getNewsFeed",
 "follow",
 "postTweet",
 "getNewsFeed",
 "unfollow",
 "getNewsFeed"]

[[],
 [1,5],
 [1],
 [1,2],
 [2,6],
 [1],
 [1,2],
 [1]]
```

### Output

```java
[null,null,[5],null,null,[6,5],null,[5]]
```

---

# Intuition

We need to support:

```java
1. Post Tweet
2. Follow / Unfollow
3. Get Latest 10 Tweets
```

Key observations:

- Tweets need chronological ordering.
- Every tweet gets a unique timestamp.
- News Feed combines tweets from multiple users.
- We only need the top 10 recent tweets.

This suggests using a:

```java
Max Heap (Priority Queue)
```

while merging tweet streams from followed users.

---

# Data Structures

## Follow Map

```java
Map<Integer, Set<Integer>>
```

Stores:

```java
follower -> followees
```

Example:

```java
1 -> {2,3}
```

---

## Tweet Map

```java
Map<Integer, List<Tweet>>
```

Stores all tweets of a user.

Example:

```java
1 -> [(5,time1),(13,time2)]
```

---

## Global Timestamp

```java
int timestamp
```

Increases whenever a new tweet is posted.

---

# Tweet Class

```java
class Tweet {

    int tweetId;
    int time;

    public Tweet(int tweetId, int time) {
        this.tweetId = tweetId;
        this.time = time;
    }
}
```

---

# Approach

## postTweet()

Add tweet to the user's tweet list.

```java
tweetMap[userId].add(tweet)
```

Increment timestamp.

---

## follow()

Add followee to follower's follow set.

```java
followMap[follower].add(followee)
```

---

## unfollow()

Remove followee from follow set.

```java
followMap[follower].remove(followee)
```

---

## getNewsFeed()

Collect tweets from:

```java
1. User himself
2. Every followee
```

Push tweets into a max heap ordered by:

```java
timestamp
```

Extract at most:

```java
10 tweets
```

---

# Dry Run

### Operations

```java
postTweet(1, 5)
```

Tweet list:

```java
1 -> [5]
```

---

```java
follow(1, 2)
```

```java
1 -> {2}
```

---

```java
postTweet(2, 6)
```

```java
2 -> [6]
```

---

```java
getNewsFeed(1)
```

Collect:

```java
User 1 => [5]
User 2 => [6]
```

Max Heap:

```java
6,5
```

Result:

```java
[6,5]
```

---

# Java Solution (LeetCode Accepted)

```java
import java.util.*;

class Twitter {

    private static class Tweet {

        int tweetId;
        int time;

        Tweet(int tweetId, int time) {
            this.tweetId = tweetId;
            this.time = time;
        }
    }

    private Map<Integer, Set<Integer>> followMap;
    private Map<Integer, List<Tweet>> tweetMap;

    private int timestamp;

    public Twitter() {

        followMap = new HashMap<>();
        tweetMap = new HashMap<>();

        timestamp = 0;
    }

    public void postTweet(int userId, int tweetId) {

        tweetMap
            .computeIfAbsent(
                userId,
                k -> new ArrayList<>());

        tweetMap.get(userId)
                .add(new Tweet(tweetId, timestamp++));

    }

    public List<Integer> getNewsFeed(int userId) {

        PriorityQueue<Tweet> maxHeap =
            new PriorityQueue<>(
                (a, b) -> b.time - a.time);

        if (tweetMap.containsKey(userId)) {
            maxHeap.addAll(tweetMap.get(userId));
        }

        Set<Integer> followees =
            followMap.getOrDefault(
                userId,
                new HashSet<>());

        for (int followee : followees) {

            if (tweetMap.containsKey(followee)) {
                maxHeap.addAll(tweetMap.get(followee));
            }
        }

        List<Integer> feed = new ArrayList<>();

        int count = 10;

        while (!maxHeap.isEmpty() &&
               count-- > 0) {

            feed.add(maxHeap.poll().tweetId);
        }

        return feed;
    }

    public void follow(
            int followerId,
            int followeeId) {

        followMap
            .computeIfAbsent(
                followerId,
                k -> new HashSet<>());

        followMap.get(followerId)
                 .add(followeeId);
    }

    public void unfollow(
            int followerId,
            int followeeId) {

        if (followMap.containsKey(followerId)) {
            followMap.get(followerId)
                     .remove(followeeId);
        }
    }
}
```

---

# Optimized Approach

The previous solution inserts **all tweets** into the heap.

For millions of tweets this becomes expensive.

### Better Idea

Store tweets as a linked list:

```java
Tweet
 ├─ tweetId
 ├─ time
 └─ next
```

For each user:

```java
latest tweet -> head
```

Put only the latest tweet of each user into the heap.

When one tweet is removed:

```java
push its previous tweet
```

This is exactly like:

```java
Merge K Sorted Lists
```

---

# Optimized Complexity

Let:

```java
F = number of followees
```

### News Feed

```java
O(10 log F)
```

instead of:

```java
O(total tweets)
```

---

# Interview Discussion

### Why Heap?

Need:

```java
most recent tweet first
```

Heap gives:

```java
O(log N)
```

retrieval of latest tweet.

---

### Why Store Timestamp?

Tweet IDs do not represent order.

Use:

```java
global timestamp
```

to maintain chronology.

---

### Why HashMap + HashSet?

```java
follow()
unfollow()
lookup
```

all become:

```java
O(1)
```

average.

---

# Complexity Analysis (Basic Solution)

### postTweet()

```java
O(1)
```

### follow()

```java
O(1)
```

### unfollow()

```java
O(1)
```

### getNewsFeed()

```java
O(T log T)
```

where:

```java
T = total collected tweets
```

---

# Complexity Analysis (Optimized Solution)

### postTweet()

```java
O(1)
```

### follow()

```java
O(1)
```

### unfollow()

```java
O(1)
```

### getNewsFeed()

```java
O(10 log F)
```

---

# Design Patterns & Concepts Tested

### Data Structures

```java
HashMap
HashSet
Priority Queue
Linked List
```

### Concepts

```java
Heap Merge
K-Way Merge
Feed Generation
Relationship Graph
```

---

# Key Takeaways

- Maintain a global timestamp for tweet ordering.
- Store follow relationships in a `HashSet`.
- Use a max heap to retrieve most recent tweets.
- News feed includes:
  - User's tweets
  - Followees' tweets
- Optimized solution uses a heap of only the latest tweets and performs a **K-way merge**.
- This is one of the most frequently asked design problems in coding interviews.
