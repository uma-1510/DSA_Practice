# Heaps - Interview Cheat Sheet

## What is a Heap?

A heap is a complete binary tree that satisfies the heap property:

- **Min Heap**: every parent node is less than or equal to its children. The minimum element is always at the root.
- **Max Heap**: every parent node is greater than or equal to its children. The maximum element is always at the root.

A complete binary tree means all levels are fully filled except possibly the last, which is filled from left to right.

---

## Underlying Data Structure

A heap is stored as an array, not as a linked tree. The tree structure is implied by index arithmetic:

```
For a node at index i:
  Parent:       (i - 1) // 2
  Left child:   2 * i + 1
  Right child:  2 * i + 2
```

Example array `[-4, 0, 1, 3, 2, 5, 10, 8, 12, 9]` represents:

```
          -4
        /     \
       0       1
      / \     / \
     3   2   5  10
    / \ / \
   8  12 9
```

---

## Key Properties

- Root is always the min (min heap) or max (max heap)
- Peek at min/max is O(1) - just read index 0
- Not fully sorted - only the heap property is guaranteed
- Python's `heapq` module implements a **min heap by default**
- There is no built-in max heap in Python - negate values to simulate one

---

## Time and Space Complexities

| Operation            | Time        | Notes                              |
|----------------------|-------------|------------------------------------|
| Heapify (build heap) | O(n)        | More efficient than n pushes       |
| Push (insert)        | O(log n)    | Sifts up to restore heap property  |
| Pop (extract min)    | O(log n)    | Sifts down to restore heap property|
| Peek at min          | O(1)        | A[0]                               |
| Heap Sort            | O(n log n)  | n pops each costing O(log n)       |
| Build from scratch   | O(n log n)  | n individual pushes                |
| heappushpop          | O(log n)    | Push then pop, more efficient      |
| Search               | O(n)        | Heap gives no search guarantee     |

Space Complexity: O(1) for heapify in-place, O(n) for heap sort with new list.

---

## When to Use a Heap

- You need repeated access to the min or max element
- You need the K largest or K smallest elements
- You need a priority queue
- Merging K sorted lists
- Scheduling / task processing by priority
- Dijkstra's shortest path algorithm
- Median finding with two heaps

---

## Interview Thinking Pattern

Ask yourself:
- Do I need the min or max repeatedly? -> Heap
- Do I need the K largest/smallest? -> Min heap of size K (for K largest)
- Do I need to process items by priority? -> Heap as priority queue
- Is the problem about merging sorted structures? -> Heap

Common signals:
- "K closest", "K most frequent", "K largest", "K smallest"
- "find the median", "running median"
- "merge K sorted lists/arrays"
- Any greedy problem that needs the current best choice

---

## Common Patterns

### K Largest Elements (use min heap of size K)

```python
import heapq

def k_largest(nums, k):
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)  # Remove the smallest
    return heap  # All elements are among the K largest
```

### K Smallest Elements (use max heap of size K, negate values)

```python
def k_smallest(nums, k):
    heap = []
    for num in nums:
        heapq.heappush(heap, -num)
        if len(heap) > k:
            heapq.heappop(heap)
    return [-x for x in heap]
```

### K Most Frequent Elements

```python
from collections import Counter
import heapq

def k_most_frequent(nums, k):
    count = Counter(nums)
    heap = []
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)
    return [num for freq, num in heap]
```

### Merge K Sorted Lists

```python
def merge_k_sorted(lists):
    heap = []
    result = []
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))  # (value, list_index, element_index)
    while heap:
        val, list_i, elem_i = heapq.heappop(heap)
        result.append(val)
        if elem_i + 1 < len(lists[list_i]):
            next_val = lists[list_i][elem_i + 1]
            heapq.heappush(heap, (next_val, list_i, elem_i + 1))
    return result
```

### Running Median (Two Heaps)

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.lower = []  # max heap (negate values) - stores lower half
        self.upper = []  # min heap - stores upper half

    def add_num(self, num):
        heapq.heappush(self.lower, -num)
        heapq.heappush(self.upper, -heapq.heappop(self.lower))
        if len(self.upper) > len(self.lower):
            heapq.heappush(self.lower, -heapq.heappop(self.upper))

    def find_median(self):
        if len(self.lower) > len(self.upper):
            return -self.lower[0]
        return (-self.lower[0] + self.upper[0]) / 2
```

---

## Code Reference

### Build Min Heap (Heapify)

```python
import heapq

# Time: O(n), Space: O(1) - in-place
A = [-4, 3, 1, 0, 2, 5, 10, 8, 12, 9]
heapq.heapify(A)
print(A)
# => [-4, 0, 1, 3, 2, 5, 10, 8, 12, 9]
```

### Heap Push (Insert)

```python
# Time: O(log n)
heapq.heappush(A, 4)
print(A)
# => [-4, 0, 1, 3, 2, 5, 10, 8, 12, 9, 4]
```

### Heap Pop (Extract Min)

```python
# Time: O(log n)
minn = heapq.heappop(A)
print(A, minn)
# => ([0, 2, 1, 3, 4, 5, 10, 8, 12, 9], -4)
```

### Peek at Min

```python
# Time: O(1)
print(A[0])  # => 1
```

### Heap Push Pop

```python
# Pushes item then pops min - more efficient than separate push + pop
# Time: O(log n)
heapq.heappushpop(A, 99)
print(A)
# => [1, 2, 5, 3, 4, 99, 10, 8, 12, 9]
```

### Heap Sort

```python
# Time: O(n log n), Space: O(n)
def heapsort(arr):
    heapq.heapify(arr)
    n = len(arr)
    new_list = [0] * n
    for i in range(n):
        minn = heapq.heappop(arr)
        new_list[i] = minn
    return new_list

heapsort([1, 3, 5, 7, 9, 2, 4, 6, 8, 0])
# => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Max Heap (Negate Values)

```python
# Python has no built-in max heap - negate all values to simulate one
B = [-4, 3, 1, 0, 2, 5, 10, 8, 12, 9]
n = len(B)
for i in range(n):
    B[i] = -B[i]
heapq.heapify(B)
print(B)
# [-12, -9, -10, -8, -2, -5, -1, -3, 0, 4]

largest = -heapq.heappop(B)
print(largest)  # => 12

heapq.heappush(B, -7)  # Insert 7 into max heap
print(B)
# => [-10, -9, -5, -8, -7, 4, -1, -3, 0, -2]
```

### Build Heap from Scratch

```python
# Time: O(n log n) - less efficient than heapify for static data
C = [-5, 4, 2, 1, 7, 0, 3]
heap = []
for x in C:
    heapq.heappush(heap, x)
    print(heap, len(heap))

# [-5] 1
# [-5, 4] 2
# [-5, 4, 2] 3
# [-5, 1, 2, 4] 4
# [-5, 1, 2, 4, 7] 5
# [-5, 1, 0, 4, 7, 2] 6
# [-5, 1, 0, 4, 7, 2, 3] 7
```

### Tuples on the Heap

```python
# Heap compares tuples lexicographically - first element is the priority
# Useful for (frequency, value), (distance, node), (cost, task) etc.
from collections import Counter

D = [5, 4, 3, 5, 4, 3, 5, 5, 4]
counter = Counter(D)
print(counter)
# => Counter({5: 4, 4: 3, 3: 2})

heap = []
for k, v in counter.items():
    heapq.heappush(heap, (v, k))  # (frequency, value) - min by frequency
print(heap)
# => [(2, 3), (4, 5), (3, 4)]
```

---

## Key Things to Remember

- Python `heapq` is a **min heap only** - negate values for max heap behavior
- `heapify` is O(n), not O(n log n) - use it over n individual pushes when building from a complete list
- Heap is not fully sorted - only the root is guaranteed to be min/max
- When pushing tuples, Python compares by the first element, then second, and so on
- If first elements are equal and the second element is not comparable (e.g. a custom object), it will raise an error - add a unique tiebreaker like an index: `(priority, index, item)`
- For K largest problems, maintain a **min heap of size K** - this keeps only the K largest seen so far
- For K smallest problems, maintain a **max heap of size K** (negate values)
- Two-heap pattern (one min, one max) is the go-to approach for median problems
