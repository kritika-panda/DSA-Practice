# Accounts Merge

## Problem Statement

Given a list of accounts where:

```java
accounts[i] = [name, email1, email2, ...]
```

Merge accounts belonging to the same person.

Two accounts belong to the same person if:

```text
They share at least one common email.
```

Return the merged accounts in the format:

```java
[name, sorted_emails...]
```

---

## Example

### Input

```java
[
 ["John","johnsmith@mail.com","john_newyork@mail.com"],
 ["John","johnsmith@mail.com","john00@mail.com"],
 ["Mary","mary@mail.com"],
 ["John","johnnybravo@mail.com"]
]
```

### Output

```java
[
 ["John",
  "john00@mail.com",
  "john_newyork@mail.com",
  "johnsmith@mail.com"],

 ["Mary",
  "mary@mail.com"],

 ["John",
  "johnnybravo@mail.com"]
]
```

---

# Key Observation

The names are NOT sufficient for merging.

Example:

```text
John
John
```

may represent:

```text
Same Person
or
Different People
```

---

The actual identifier is:

```text
Email
```

If two accounts share an email:

```text
They belong to the same person.
```

---

# Graph Interpretation

Consider:

```text
Account 0:
a b c

Account 1:
c d

Account 2:
x y
```

Since:

```text
c
```

is common between Account 0 and Account 1,

they belong to the same component.

```text
Account0 ---- Account1

Account2
```

This is a classic:

```text
Connected Components
```

problem.

---

# Why DSU?

We need to:

```text
Merge accounts dynamically
```

when they share emails.

This is exactly:

```text
Union-Find / DSU
```

---

# Intuition

Treat every:

```text
Account Index
```

as a node.

Example:

```text
0
1
2
3
```

If an email already appeared before:

```text
Union(currentAccount,
      previousAccount)
```

---

# Example

```java
[
 ["John","a","b"],
 ["John","b","c"],
 ["Mary","x"]
]
```

---

### Process Account 0

```text
a -> 0
b -> 0
```

Map:

```text
a : 0
b : 0
```

---

### Process Account 1

```text
b already exists
```

Therefore:

```text
Union(1,0)
```

Store:

```text
c -> 1
```

---

### Process Account 2

```text
x -> 2
```

No merge.

---

Final Components:

```text
{0,1}

{2}
```

---

# Approach

## Step 1

Create DSU for:

```text
n accounts
```

---

## Step 2

Maintain:

```java
Map<String,Integer>
```

```text
email -> account index
```

---

## Step 3

For every email:

### Email not seen before

Store:

```java
emailToNode.put(email, accountIndex);
```

---

### Email already seen

Merge accounts:

```java
unionBySize(
    currentAccount,
    existingAccount
);
```

---

## Step 4

After processing all emails,

all connected accounts belong to the same parent.

---

## Step 5

Group emails by parent.

```java
parent -> emails
```

---

## Step 6

Sort emails.

Add account name.

Return answer.

---

# Dry Run

Input:

```java
[
 ["John","a","b"],
 ["John","b","c"],
 ["Mary","x"]
]
```

---

### Email Mapping

```text
a -> 0
b -> 0
```

---

Processing Account 1

```text
b already belongs to 0
```

Union:

```text
0 and 1
```

Store:

```text
c -> 1
```

---

Processing Account 2

```text
x -> 2
```

---

DSU Structure

```text
Parent(0)=0

Parent(1)=0

Parent(2)=2
```

---

Group Emails

```text
0 -> a b c

2 -> x
```

---

Answer

```java
[
 ["John","a","b","c"],
 ["Mary","x"]
]
```

---

# DSU Solution

## DSU Class

```java
class DisjointSet {

    int[] parent;
    int[] size;

    DisjointSet(int n) {

        parent = new int[n];
        size = new int[n];

        for(int i=0;i<n;i++) {

            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUPar(int node) {

        if(node == parent[node])
            return node;

        return parent[node]
                = findUPar(parent[node]);
    }

    void unionBySize(int u, int v) {

        int pu = findUPar(u);
        int pv = findUPar(v);

        if(pu == pv)
            return;

        if(size[pu] < size[pv]) {

            parent[pu] = pv;
            size[pv] += size[pu];
        }
        else {

            parent[pv] = pu;
            size[pu] += size[pv];
        }
    }
}
```

---

## Main Solution

```java
class Solution {

    public List<List<String>> accountsMerge(
            List<List<String>> accounts) {

        int n = accounts.size();

        DisjointSet ds =
                new DisjointSet(n);

        Map<String,Integer> emailToNode =
                new HashMap<>();

        for(int i=0;i<n;i++) {

            for(int j=1;
                j<accounts.get(i).size();
                j++) {

                String email =
                        accounts.get(i).get(j);

                if(!emailToNode.containsKey(email)) {

                    emailToNode.put(email, i);
                }
                else {

                    ds.unionBySize(
                            i,
                            emailToNode.get(email)
                    );
                }
            }
        }

        ArrayList<String>[] mergedMail =
                new ArrayList[n];

        for(int i=0;i<n;i++) {

            mergedMail[i] =
                    new ArrayList<>();
        }

        for(Map.Entry<String,Integer> entry :
                emailToNode.entrySet()) {

            String email = entry.getKey();

            int node = entry.getValue();

            int parent =
                    ds.findUPar(node);

            mergedMail[parent].add(email);
        }

        List<List<String>> ans =
                new ArrayList<>();

        for(int i=0;i<n;i++) {

            if(mergedMail[i].size() == 0)
                continue;

            Collections.sort(
                    mergedMail[i]
            );

            List<String> temp =
                    new ArrayList<>();

            temp.add(
                    accounts.get(i).get(0)
            );

            temp.addAll(
                    mergedMail[i]
            );

            ans.add(temp);
        }

        return ans;
    }
}
```

---

# Complexity Analysis

Let:

```text
N = Number of Accounts

M = Total Emails
```

---

### DSU Operations

```text
M unions/finds

O(M × α(N))
```

where:

```text
α(N)
```

is Inverse Ackermann Function.

Practically:

```text
≈ O(M)
```

---

### Sorting Emails

Suppose total emails:

```text
M
```

Sorting:

```text
O(M log M)
```

---

### Total Complexity

```text
Time Complexity:

O(M log M)

Space Complexity:

O(M + N)
```

---

# Why DSU Works?

Consider:

```text
Account A shares email with B

Account B shares email with C
```

Then:

```text
A and C
```

must also belong to the same person.

This is exactly:

```text
Connected Components
```

DSU efficiently finds and merges such components.

---

# Visualization

```text
Account0
   |
 john@mail

Account1
   |
 john@mail

Account1
   |
 abc@mail

Account2
   |
 abc@mail
```

Graph:

```text
0 ---- 1 ---- 2
```

All belong to:

```text
One Connected Component
```

Thus:

```text
One Merged Account
```

---

# Interview Pattern

Whenever the question says:

```text
Merge Groups

Common Identifier

Shared Email

Connected Components

Dynamic Grouping
```

Think:

```text
DSU (Union Find)
```

---

# Similar DSU Problems

```text
Accounts Merge

Number of Islands II

Most Stones Removed

Making A Large Island

Kruskal's MST

Redundant Connection
```

---

# Interview Takeaways

### Core Idea

```text
Email = Connection

Account = Node
```

---

### Merge Condition

```java
Same Email
⇒ Union Accounts
```

---

### Data Structures

```java
HashMap<String,Integer>

DSU

ArrayList<String>[]
```

---

### Complexity

```text
Time  : O(M log M)

Space : O(M + N)
```

---

### Most Important Insight

```text
Accounts Merge is NOT a string problem.

It is a Connected Components problem
where emails act as edges between accounts.

Connected Components
=
DSU
```
