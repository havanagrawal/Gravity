---
layout: post
title:  "Python Reference Sheet for Interviews"
date:   2019-10-11 11:13:00 -0700
category: ["learning"]
---

A quick reference for coding interviews in Python, along with a few exercises.

Note: This is a non-exhaustive and ad-hoc post that I created for a peer.

1. [Collections](#collections)
2. [Graphs](#graphs)
    1. [Representations](#representations)
    2. [Algorithms](#algorithms)
3. [Miscellaneous](#miscellaneous)
4. [Exercises](#exercises)

## Collections

1. For transformations and filters, use **list comprehensions**:
  ```python
nums = [-4, -2, 1, 5, 9]
squares = [i**2 for i in nums]
```

2. For iterating through a collection of tuples that are record-like, i.e. fixed format, use **tuple-unpacking**:
  ```python
records = [
      ("Steve", 10),
      ("Okoye", 5),
      ("Thor", 15),
      ("Steve", 9),
      ("Steve", 2)
]
for name, votes in records:
    # do something
```

3. Use sets when the desired output has unique elements:
  ```python
candidates = {name for name, _ in records}
```

4. Use **generators** for collections that are iterated through only once/are expensive to hold in memory at once:
  ```python
def memory_heavy(database_conn):
  # Loads 1 million records into memory
  all_records = [database_conn.get(k) for k in range(1000000)]
  # Makes a list of 1 million scores
  scores = [score for name, score in all_records]
  return max(scores)
  ```
  vs
  ```python
def memory_light(database_conn):
  # all_records has the type `generator`
  # It is lazily evaluated, i.e. each item is fetched only on use
  all_records = (database_conn.get(k) for k in range(1000000))
  # Similarly, scores is also lazily evaluated
  # If you were to use
  # [score for name, votes in all_records]
  # It would generate the entire list of 1 million items in memory
  scores = (score for name, score in all_records)
  return max(scores)
```
  Remember that generators can be iterated through only once.
5. Use **memoization** for recursive functions that may be queried more than once. For example, if you need to find the number of permutations of `n` distinct boys and `m` distinct girls in `n` boy slots and `m` girl slots respectively, you need `n! * m!`.
  ```python
def no_of_permutations(n: int, m: int) -> int:
      _max = max(m, n) + 1
      factorial = [1] * _max
      for i in range(2, _max):
          factorial[i] = factorial[i - 1] * i
      return factorial[m] * factorial[n]
```

6. If you are unable to come up with the memoized version, you can leverage the `@lru_cache` decorator:
  ```python
from functools import lru_cache

@lru_cache()
def factorial(n: int) -> int:
    if n == 0 or n == 1:
        return 1
    return n * factorial(n - 1)
```
  This will compute the factorial for each `n` only once.
7. Use `zip` to iterate through two collections in lock-step:
  ```python
names = ["Peter", "Carl", "Emma"]
votes = [20, 10, 50]
for name, vote in zip(names, votes):
      print(f"{name} received {vote} votes")
```

## Sorting

Use `sorted` along with the `key` and `reverse` args to leverage the most out of Python's built-in sorting capabilities.

**Sorting by number of votes**
```python
# Assuming records have the format (name, total_votes, timestamp)
def sort_by_votes(records):
    # key is a function that accepts a single element
    # and returns the parameter which you wish to use for sorting
    return sorted(records, key=lambda x: x[1])
  ```

**Sorting by timestamp, with latest record first**
  ```python
def sort_by_timestamp(records):
    return sorted(records, key=lambda x: x[2], reverse=True)
```

**Sorting by property**

Suppose `records` was a list of elements of the type `VoteRecord`

```python
class VoteRecord:
    def __init__(self, name, total_votes, timestamp):
        self._name = name
        self._total_votes = total_votes
        self._timestamp = timestamp
```

then your key can be more descriptive:

```python
sorted(records, key=lambda x: x._total_votes)
```

If you have a function that extracts some property from the element, you can use it directly
```python
def name_length(record):
    return len(record._name)

# Sorts records by increasing length of name
sorted(records, key=name_length)
```

**Multi-key sorting a.k.a breaking ties**

Tuples also have a natural sorting order, which you can leverage to sort collections based on more than one property.

For example, this sorts the records in ascending order of votes, breaking ties by ascending order of name
```python
sorted(records, key=lambda row:(row._total_votes, row._name))
```


## Graphs

Assume that the input is of the format `List[Tuple[Int, Int, Int]]`, i.e. a list of tuples where each tuple is of the format (src, dest, weight). This is flexible enough to all four kinds of the Punnett square of graphs (directed/undirected * weighted/unweighted)

The total number of nodes in the graph can either be specified, or derived as:

```python
def number_of_nodes(tuples: List[Tuple[Int, Int, Int]]) -> int:
    srcs = (src for src, _, _ in tuples)
    dests = (dest for _, dest, _ in tuples)
    return max(max(srcs), max(dests))
```

### Representations

There are two popular formats for dealing with graphs:

#### Adjacency Matrix

```python
def make_adjacency_matrix(
      tuples: List[Tuple[Int, Int, Int]],
      n: int
    ) -> List[List[Int]]:
    """
    Arguments
    ---------
    tuples: A list of graph records of the format (src, dest, weight)
    n:      The total number of nodes in the graph

    Returns
    -------
    matrix: A 2-D matrix where matrix[src][dest] is equal to
            the weight of the edge from src to dest
    """
    # [[math.inf]*n]*n does not work because arrays are pass-by-reference
    # So the same internal array reference is copied n times
    # which is not what we want
    matrix = [[math.inf]*n for _ in range(n)]

    for src, dest, weight in tuples:
        matrix[src][dest] = weight

    return matrix
```

The advantage this offers is that you can query for the weight between two nodes in `O(1)`.

#### Adjacency List

```python
def make_adjacency_list(
      tuples: List[Tuple[Int, Int, Int]],
      n: int
    ) -> Dict[Int, List[Tuple[Int, Int]]]:
    """
    Arguments
    ---------
    tuples:   A list of graph records of the format (src, dest, weight)
    n:        The total number of nodes in the graph

    Returns
    -------
    adj_list: A dict where adj_list[src] is a list of tuples (dest, weight)
    """
    adj_list = defaultdict(list)

    for src, dest, weight in tuples:
      adj_list[src].append((dest, weight))

    return adj_list
```

The advantage is that you can get all the neighbors of a node in `O(1)`. This is particularly useful when the graph is sparse, and a adjacency matrix would be mostly empty (count(`math.inf`) ~= n<sup>2</sup>)

### Algorithms

We use a [`deque`](https://docs.python.org/3.3/library/collections.html#collections.deque) i.e. a double-ended queue that serves as a stack as well as a queue, depending on how you use it.

We assume an unweighted graph for these.

#### BFS

```python
from collections import deque

def bfs(adj_list, start):
  queue = deque([start])
  visited = set()

  while queue:
    # Dequeue the next node to visit
    current = queue.popleft()

    # If we have already processed this node, skip
    if current in visited:
      continue

    # Do something with the current node
    # You could print it, add it to a data structure, perform some mutation on it
    process(current)

    # Add before we see adjacent nodes
    # This is important because a node may have an edge to itself
    # And we want to avoid cycles
    visited.add(current)

    # all nodes reachable from the current node that have not been visited
    # Note that this is a generator
    to_visit = (
      node
      for node in adj_list[current]
      if node not in visited
    )

    # Add all these nodes to the queue
    queue.extend(to_visit)

    # Note that the queue may contain duplicate elements, but it is guaranteed
    # that we process each node only once because of the visited set
```

#### DFS

```python
from collections import deque

def dfs(adj_list, start):
  stack = deque([start])
  visited = set()

  while stack:
    # Pop the next node to visit
    # Note that this is the only difference from bfs
    current = stack.pop()

    if current in visited:
      continue

    process(current)
    visited.add(current)

    to_visit = (
      node
      for node in adj_list[current]
      if node not in visited
    )

    stack.extend(to_visit)
```

Note how using an adjacency list makes these algorithms fairly simple. If we were using an adjacency matrix, the `to_visit` step would need an extra condition:
```
to_visit = (
  node
  for node in range(n)
  if (
    adj_matrix[current][node] and
    i not in visited
  )
)
```
This is not just extra code, it also has higher complexity: finding adjacent nodes becomes an `O(n)` operation.

## Miscellaneous

1. Python `math` provides:
    1. `fabs`: Absolute value
    2. `gcd`: Greatest Common Divisor (LCM can be derived as `(m*n)/math.gcd(m, n)`)
    3. `math.sqrt`, `math.factorial`: Self-explanatory
    3. `math.log`, `math.log2`, `math.log10` : Natural, base-2 and base-10 log
    4. `math.degrees` and `math.radians`: Convert radians to degrees, and degrees to radians, respectively
2. Python `itertools` provides:
    1. `combinations(iterable, r)`: Combinations of size `r` from `iterable` in lexicographic order.
       `combinations(["cat"], 2)` would result in `[("c", "a"), ("c", "t"), ("a", "t")]`
    2. `permutations` is similar
    3. `product(iterable1, iterable2)` results in a cross product. So `product("AB", "CD")` would give `[('A', 'C'), ('A', 'D'), ('B', 'C'), ('B', 'D')]`
3. Use the `join` method on strings to concatenate iterables into strings:
  ```python
"".join(["c", "a", "t"]) # gives "cat"
".".join(["10", "0", "0", "1"]) # gives "10.0.0.1"
  ```
4. Instead of `5 < a and a < 100`, you can use `5 < a < 100` which evaluates to the same thing.

### Exercises

1. Given a list of numbers, print their GCD and LCM:
  ```python
gcd_and_lcm([10, 6, 12]) # (2, 60)
gcd_and_lcm([18, 12, 32, 5]) # (1, 1440)
```
1. Given a string `s`, generate all possible subsequences of length `k`:
  ```python
subsequences("cats", 2) # ["ca", "ct", "cs", "at", "as", "ts"]
subsequences("cats", 3) # ["cat", "cas", "ats"]
```
1. Given a graph in the above canonical format, find the length of the shortest path from a given node `src` to a node `dest`
1. Given integers `n` and `k`, print every `k`th Tribonacci number up to (and including)`n`, where the Tribonacci relation is given by:
  {% raw %}
$$T_1 = 1$$

$$T_2 = 1$$

$$T_3 = 2$$

$$T_n = T_{n-1} + T_{n-2} + T_{n-3}$$
{% endraw %}
  Note that $T_0$ is not defined.
  ```python
tribonacci(n=15, k=5) # [7, 147, 3136]
  ```
