# Graphs

## Interview Thinking Pattern

Convert input to Adjacency List, then identify:

- Traversal? -> BFS / DFS
- Shortest path? -> BFS / Dijkstra
- Ordering? -> Topo Sort
- Grouping? -> Union-Find
- Use visited set ALWAYS

## Final Mental Model

- DFS = explore deeply
- BFS = explore layer-wise
- Heap = optimize choice (min/max)
- Union-Find = grouping
- Topo Sort = ordering with dependencies

---

## What is a Graph?

A graph is a collection of nodes (vertices) and edges (connections) that represent relationships between entities.

A graph can be:

- Directed (u -> v)
- Undirected (u <-> v)
- Weighted / Unweighted
- Cyclic / Acyclic (DAG)

---

## Graph Representations

### 1. Adjacency Matrix

A 2D matrix M[n][n] where M[u][v] = 1 means an edge exists.

- Pros: O(1) edge lookup
- Cons: Takes more space
- Space Complexity: O(V^2)
- Edge check: O(1)
- Iterate neighbors: O(V)

### 2. Adjacency List (Most Used)

A dict/list mapping node to list of neighbors.

- Pros: Space efficient, faster traversal
- Space Complexity: O(V + E)
- Edge check: O(deg(V))
- Iterate neighbors: O(deg(V))

---

## Graph Traversals

### 1. DFS (Depth First Search)

Go deep first.

- Recursive: uses call stack
- Iterative: uses explicit stack
- Time: O(V + E)
- Space: O(V)

### 2. BFS (Breadth First Search)

Level-by-level traversal using a queue.

- Use case: Shortest path in unweighted graph
- Time: O(V + E)
- Space: O(V)

---

## Key Graph Algorithms

### 1. Topological Sort (DAG only)

Ordering of nodes such that for every directed edge u -> v, u comes before v.

Approaches:
- BFS (Kahn's Algorithm)
- DFS (postorder)

- Time: O(V + E)
- Space: O(V)

### 2. Shortest Path

**Unweighted Graph:**
- BFS: O(V + E)

**Weighted Graph:**
- Dijkstra (min heap): O((V + E) log V), Space: O(V)

**With Negative Weights:**
- Bellman-Ford: O(V * E), Space: O(V)

### 3. Cycle Detection

- Directed Graph: DFS + recursion stack
- Undirected Graph: DFS + parent tracking
- Time: O(V + E)
- Space: O(V)

### 4. Minimum Spanning Tree (MST)

**Kruskal's Algorithm:**
- Uses Union-Find
- Time: O(E log E)

**Prim's Algorithm:**
- Uses min heap
- Time: O(E log V)

### 5. Union-Find (Disjoint Set)

Used for cycle detection and connected components.

- Find: O(alpha(N)) (almost constant)
- Union: O(alpha(N))

---

## Common Patterns

- Always use a visited/seen set to avoid cycles
- Graph = Tree + cycles + multiple paths
- Always clarify: Directed vs Undirected, Weighted vs Unweighted, Connected vs Disconnected

---

## Applications of Graphs

- Social networks (friends, followers)
- Maps / GPS (shortest path)
- Task scheduling (topological sort)
- Network routing
- Dependency resolution (build systems)
- Recommendation systems
- Web crawling

---

## Code

### Input: Array of Edges and Graph Setup

```python
# Array of Edges (Directed) [Start, End]
n = 8
A = [[0,1], [1,2], [0,3], [3,4], [3,6], [3,7], [4,2], [4,5], [5,2]]

print(A)
```

### Convert Array of Edges to Adjacency Matrix

```python
# Initialize n x n matrix with zeros
M = []
for i in range(n):
    M.append([0] * n)

# Represent each edge as 1
for u, v in A:
    M[u][v] = 1
    # If undirected, also add:
    # M[v][u] = 1
```

### Convert Array of Edges to Adjacency List

```python
from collections import defaultdict

D = defaultdict(list)
for u, v in A:
    D[u].append(v)
    # Indicates u -> v
    # D[v].append(u)  # Uncomment if undirected

print(D)
# defaultdict(list, {0: [1, 3], 1: [2], 3: [4, 6, 7], 4: [2, 5], 5: [2]})
```

### DFS Recursive - O(V + E)

```python
def dfs_recursive(node):
    print(node)
    for neighbor in D[node]:
        if neighbor not in seen:
            seen.add(neighbor)
            dfs_recursive(neighbor)

source = 0
seen = set()
seen.add(source)
dfs_recursive(source)
```

### DFS Iterative with Stack - O(V + E)

```python
source = 0
seen = set()
seen.add(source)
stack = [source]

while stack:
    node = stack.pop()
    print(node)
    for nei in D[node]:
        if nei not in seen:
            seen.add(nei)
            stack.append(nei)
```

### BFS with Queue - O(V + E)

```python
from collections import deque

source = 0
seen = set()
seen.add(source)
q = deque()
q.append(source)

while q:
    node = q.popleft()
    print(node)
    for nei in D[node]:
        if nei not in seen:
            seen.add(nei)
            q.append(nei)
```

### Graph Implemented with a Class

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.neighbors = []

    def __str__(self):
        return f'Node({self.value})'

    def display(self):
        connections = [node.value for node in self.neighbors]
        return f'{self.value} is connected to: {connections}'


A = Node('A')
B = Node('B')
C = Node('C')
D = Node('D')

A.neighbors.append(B)
B.neighbors.append(A)
C.neighbors.append(D)
D.neighbors.append(C)

print(A.display())  # A is connected to: ['B']
print(B.display())  # B is connected to: ['A']
print(C.display())  # C is connected to: ['D']
print(D.display())  # D is connected to: ['C']
```
