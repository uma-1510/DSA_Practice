# Graphs Interview Cheat Sheet

## Interview Thinking Pattern

### Core Strategy
- Convert input → Adjacency List
- Identify problem type:
  - Traversal → BFS / DFS
  - Shortest Path → BFS / Dijkstra
  - Ordering → Topological Sort
  - Grouping → Union-Find
- Always use a visited set

---

## Final Mental Model

- DFS → Explore deeply
- BFS → Explore level by level
- Heap → Optimize choice (min/max)
- Union-Find → Grouping / Connected components
- Topological Sort → Ordering with dependencies

---

## What is a Graph?

A graph is a collection of:
- Nodes (Vertices)
- Edges (Connections)

It represents relationships between entities.

### Types of Graphs
- Directed (u → v)
- Undirected (u ↔ v)
- Weighted / Unweighted
- Cyclic / Acyclic (DAG)

---

## Graph Representations

### 1. Adjacency Matrix

A 2D matrix `M[n][n]`:
- `M[u][v] = 1` → edge exists

#### Complexity
- Space: O(V²)
- Edge check: O(1)
- Iterate neighbors: O(V)

#### Pros
- Fast edge lookup

#### Cons
- High space usage

---

### 2. Adjacency List (Most Used)

Representation:
node → [neighbors]


#### Complexity
- Space: O(V + E)
- Edge check: O(deg(V))
- Iterate neighbors: O(deg(V))

#### Pros
- Space efficient
- Fast traversal

---

## Graph Traversals

## 1. DFS (Depth First Search)

- Go deep first
- Uses recursion or stack

### Complexity
- Time: O(V + E)
- Space: O(V)

### Recursive DFS
```python
def dfs_recursive(node):
    print(node)
    for neighbor in D[node]:
        if neighbor not in seen:
            seen.add(neighbor)
            dfs_recursive(neighbor)

source = 0
seen = set([source])
dfs_recursive(source)


### Iterative DFS

source = 0
seen = set([source])
stack = [source]

while stack:
    node = stack.pop()
    print(node)

    for nei in D[node]:
        if nei not in seen:
            seen.add(nei)
            stack.append(nei)
