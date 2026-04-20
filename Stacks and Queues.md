# Stacks and Queues - Interview Cheat Sheet

## What is a Stack?

A stack is a linear data structure that follows Last In First Out (LIFO) order. The last element added is the first one removed. Think of a stack of plates - you add and remove from the top only.

In Python, a stack is implemented using a plain list. All core operations are O(1).

## What is a Queue?

A queue is a linear data structure that follows First In First Out (FIFO) order. The first element added is the first one removed. Think of a line at a checkout - you join at the back and leave from the front.

In Python, a queue is implemented using `collections.deque`. Do not use a plain list for queues - `list.pop(0)` is O(n) because it shifts all elements. `deque.popleft()` is O(1).

---

## Stack vs Queue

| Feature         | Stack                  | Queue                    |
|-----------------|------------------------|--------------------------|
| Order           | LIFO                   | FIFO                     |
| Add element     | Push to top            | Enqueue to back          |
| Remove element  | Pop from top           | Dequeue from front       |
| Peek            | Top element            | Front element            |
| Python type     | list                   | collections.deque        |
| Common use      | DFS, backtracking,     | BFS, scheduling,         |
|                 | undo, parsing          | level-order traversal    |

---

## Time Complexities

### Stack (Python list)

| Operation        | Time  |
|------------------|-------|
| Push (append)    | O(1)  |
| Pop              | O(1)  |
| Peek (stk[-1])   | O(1)  |
| Search           | O(n)  |
| Check if empty   | O(1)  |

### Queue (collections.deque)

| Operation           | Time  |
|---------------------|-------|
| Enqueue (append)    | O(1)  |
| Dequeue (popleft)   | O(1)  |
| Peek front (q[0])   | O(1)  |
| Peek back (q[-1])   | O(1)  |
| Search              | O(n)  |
| Check if empty      | O(1)  |

Space Complexity: O(n) for both.

---

## When to Use a Stack

- DFS (iterative implementation uses an explicit stack)
- Backtracking problems
- Matching brackets / parentheses
- Undo / redo operations
- Evaluating expressions (postfix, infix)
- Monotonic stack problems (next greater element, largest rectangle)
- Call stack simulation

## When to Use a Queue

- BFS (level-order traversal)
- Processing items in the order they arrive
- Sliding window problems (deque as monotonic queue)
- Task scheduling
- Producer-consumer problems

---

## Interview Thinking Pattern

Ask yourself:
- Do I need to process the most recently seen item first? -> Stack
- Do I need to process items in the order I saw them? -> Queue
- Do I need to track minimums or maximums in a window? -> Monotonic deque
- Is the problem about matching or nesting (brackets, tags)? -> Stack
- Is the problem about levels or shortest path? -> Queue (BFS)

Common signals:
- "valid parentheses", "decode string", "evaluate expression" -> Stack
- "level order traversal", "shortest path", "word ladder" -> Queue
- "next greater element", "largest rectangle in histogram" -> Monotonic stack
- "sliding window maximum/minimum" -> Monotonic deque

---

## Common Patterns

### Valid Parentheses

```python
def is_valid(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    for char in s:
        if char in mapping:
            top = stack.pop() if stack else '#'
            if mapping[char] != top:
                return False
        else:
            stack.append(char)
    return not stack
```

### Monotonic Stack - Next Greater Element

```python
def next_greater(nums):
    result = [-1] * len(nums)
    stack = []  # Stores indices
    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            idx = stack.pop()
            result[idx] = num
        stack.append(i)
    return result
```

### Monotonic Deque - Sliding Window Maximum

```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()  # Stores indices, decreasing order of values
    result = []
    for i, num in enumerate(nums):
        # Remove indices outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        # Remove indices whose values are smaller than current
        while dq and nums[dq[-1]] < num:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
```

### BFS with Queue

```python
from collections import deque

def bfs(graph, source):
    seen = set([source])
    q = deque([source])
    while q:
        node = q.popleft()
        print(node)
        for neighbor in graph[node]:
            if neighbor not in seen:
                seen.add(neighbor)
                q.append(neighbor)
```

### Iterative DFS with Stack

```python
def dfs(graph, source):
    seen = set([source])
    stack = [source]
    while stack:
        node = stack.pop()
        print(node)
        for neighbor in graph[node]:
            if neighbor not in seen:
                seen.add(neighbor)
                stack.append(neighbor)
```

### Min Stack - O(1) getMin

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []  # Tracks current min at each state

    def push(self, val):
        self.stack.append(val)
        min_val = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(min_val)

    def pop(self):
        self.stack.pop()
        self.min_stack.pop()

    def top(self):
        return self.stack[-1]

    def get_min(self):
        return self.min_stack[-1]
```

---

## Code Reference

### Stack

```python
stk = []
print(stk)
# => []

# Push to top - O(1)
stk.append(5)
stk.append(4)
stk.append(3)
print(stk)
# => [5, 4, 3]

# Pop from top - O(1)
x = stk.pop()
print(x)    # => 3
print(stk)  # => [5, 4]

# Peek at top - O(1)
print(stk[-1])  # => 4

# Check if stack is non-empty - O(1)
if stk:
    print(True)  # => True

# Check if stack is empty
if not stk:
    print("empty")
```

### Queue

```python
from collections import deque

q = deque()
print(q)
# => deque([])

# Enqueue to back - O(1)
q.append(5)
q.append(6)
q.append(7)
print(q)
# => deque([5, 6, 7])

# Dequeue from front - O(1)
q.popleft()  # => 5
print(q)
# => deque([6, 7])

# Peek front and back - O(1)
print(q[0])   # => 6
print(q[-1])  # => 7

# Check if queue is non-empty - O(1)
if q:
    print(True)
```

### Deque as Both Stack and Queue

```python
# deque supports O(1) operations on both ends
from collections import deque

dq = deque()

# Add to both ends
dq.append(1)       # Add to right
dq.appendleft(0)   # Add to left
print(dq)          # => deque([0, 1])

# Remove from both ends
dq.pop()           # Remove from right -> 1
dq.popleft()       # Remove from left  -> 0
```

---

## Key Things to Remember

- Use `list` for stacks - `append` and `pop` are both O(1)
- Use `deque` for queues - never use `list.pop(0)` as it is O(n)
- `deque` supports O(1) operations on both ends, making it usable as both a stack and a queue
- Monotonic stack maintains elements in strictly increasing or decreasing order - pop whenever the order is violated
- Monotonic deque is the go-to for sliding window min/max problems
- The min stack pattern (auxiliary stack tracking minimums) gives O(1) getMin at the cost of O(n) extra space
- When converting recursive DFS to iterative, replace the call stack with an explicit stack
- BFS always uses a queue - the queue enforces level-by-level processing
