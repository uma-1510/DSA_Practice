# Hash Tables - Interview Cheat Sheet

## What is a Hash Function?

A hash function takes an input (key) and converts it into an integer index used to store the value in an underlying array. The goal is to distribute keys uniformly across the array to minimize collisions.

Properties of a good hash function:
- Deterministic: same input always produces same output
- Fast to compute: O(1) average
- Uniform distribution: minimizes clustering and collisions

Example (conceptual):
```
hash("greg") -> 2 -> store at index 2 in array
```

---

## What is a Hash Table?

A hash table is a data structure that maps keys to values using a hash function. It is the underlying mechanism behind both hash maps and hash sets.

Internally it uses an array. The hash function converts the key into an array index, and the value is stored at that index.

### Collision Handling

When two keys hash to the same index, a collision occurs. Two common strategies:

- **Chaining**: Each array slot holds a linked list of all key-value pairs that hash to that index.
- **Open Addressing**: On collision, probe for the next available slot (linear probing, quadratic probing, double hashing).

Python's built-in dict and set use open addressing with pseudo-random probing.

---

## Hash Map vs Hash Set

| Feature         | Hash Map (dict)          | Hash Set (set)            |
|-----------------|--------------------------|---------------------------|
| Stores          | Key-value pairs          | Keys only                 |
| Lookup          | O(1) average             | O(1) average              |
| Use case        | Count, group, map data   | Membership, deduplication |
| Duplicate keys  | Not allowed              | Not allowed               |
| Python type     | dict / defaultdict       | set                       |

---

## Time Complexities

| Operation        | Average | Worst Case |
|------------------|---------|------------|
| Insert           | O(1)    | O(n)       |
| Lookup / Search  | O(1)    | O(n)       |
| Delete           | O(1)    | O(n)       |
| Iteration        | O(n)    | O(n)       |
| Construction     | O(n)    | O(n)       |

Worst case O(n) occurs when all keys collide into the same slot (degenerate case). In practice with a good hash function this is extremely rare.

Space Complexity: O(n) where n is the number of keys stored.

---

## Underlying Data Structure

- Built on top of a dynamic array
- Load factor = (number of elements) / (array size)
- When load factor exceeds a threshold (typically 0.75), the table resizes (rehashes all elements) -> O(n) amortized
- Rehashing doubles the array size and reinserts all elements

---

## Interview Thinking Pattern

Ask yourself:
- Do I need to count occurrences? -> Counter / defaultdict(int)
- Do I need to group items? -> defaultdict(list)
- Do I need fast membership checks? -> set
- Do I need to track seen elements? -> set
- Do I need to map one thing to another? -> dict
- Do I need a default value to avoid KeyError? -> defaultdict

Common signals in a problem that hint at hash usage:
- "two sum", "find duplicates", "group anagrams", "count frequency"
- "find if X exists", "longest substring without repeating"
- Any problem asking for O(1) lookup

---

## Common Patterns

### Frequency Count
```python
from collections import Counter
freq = Counter("aabbcc")
# Counter({'a': 2, 'b': 2, 'c': 2})
```

### Grouping
```python
from collections import defaultdict
groups = defaultdict(list)
words = ["eat", "tea", "tan", "ate", "nat", "bat"]
for w in words:
    key = tuple(sorted(w))
    groups[key].append(w)
# Groups anagrams together
```

### Tracking Seen Elements (Two Sum pattern)
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

### Sliding Window + Set
```python
def longest_unique_substring(s):
    seen = set()
    left = 0
    max_len = 0
    for right in range(len(s)):
        while s[right] in seen:
            seen.remove(s[left])
            left += 1
        seen.add(s[right])
        max_len = max(max_len, right - left + 1)
    return max_len
```

---

## Applications of Hash Tables

- Database indexing (fast record lookup)
- Caching / memoization (store computed results)
- Counting word/character frequencies
- Detecting duplicates
- Implementing sets
- Graph adjacency lists
- Symbol tables in compilers
- DNS lookup (domain -> IP)
- Anagram detection and grouping
- Substring problems (sliding window)

---

## Code Reference

### Hash Sets

```python
s = set()
print(s)

# Add item - O(1)
s.add(1)
s.add(2)
s.add(3)
print(s)

# Lookup - O(1)
if 1 not in s:
    print(True)

# Remove item - O(1)
s.remove(3)
print(s)

# Construct set from string - O(n)
string = 'aaaaaaabbbbbbbbbbbccccccccceeeeeeeee'
sett = set(string)
print(sett)  # {'a', 'b', 'c', 'e'}

# Loop over set - O(n)
for x in s:
    print(x)
```

### Hash Maps (Dictionaries)

```python
d = {'greg': 1, 'steve': 2, 'rob': 3}
print(d)

# Add key-value pair - O(1)
d['arsh'] = 4
print(d)

# Check for key presence - O(1)
if 'greg' in d:
    print(True)

# Access value by key - O(1)
print(d['greg'])

# Safe access with default (avoids KeyError)
print(d.get('unknown', 0))  # Returns 0 if key not found

# Loop over key-value pairs - O(n)
for key, val in d.items():
    print(f'key {key}: val {val}')

# Loop over keys only - O(n)
for key in d.keys():
    print(key)

# Loop over values only - O(n)
for val in d.values():
    print(val)

# Delete a key - O(1)
del d['rob']

# Pop a key with default - O(1)
val = d.pop('steve', None)
```

### defaultdict

```python
from collections import defaultdict

# Default list - useful for grouping
default_list = defaultdict(list)
default_list['a'].append(1)   # No KeyError
print(default_list[2])        # Returns [] instead of KeyError

# Default int - useful for counting
default_int = defaultdict(int)
for char in "hello":
    default_int[char] += 1
print(default_int)  # {'h': 1, 'e': 1, 'l': 2, 'o': 1}

# Default set - useful for unique grouping
default_set = defaultdict(set)
default_set['group'].add('item1')
```

### Counter

```python
from collections import Counter

string = 'aaaaaaabbbbbbbbbbbccccccccceeeeeeeee'
counter = Counter(string)
print(counter)
# Counter({'b': 11, 'e': 9, 'c': 9, 'a': 7})

# Most common elements
print(counter.most_common(2))
# [('b', 11), ('e', 9)]

# Counter from a list
c = Counter([1, 1, 2, 3, 3, 3])
print(c)  # Counter({3: 3, 1: 2, 2: 1})

# Arithmetic on counters
c1 = Counter('aab')
c2 = Counter('abc')
print(c1 + c2)  # Counter({'a': 3, 'b': 2, 'c': 1})
print(c1 - c2)  # Counter({'a': 1})
```

---

## Key Things to Remember

- dict and set in Python are both hash tables internally
- Keys must be hashable: strings, numbers, tuples are hashable; lists and dicts are not
- Insertion order is preserved in Python dicts from Python 3.7+
- set has no guaranteed order
- Use defaultdict to avoid KeyError when accessing missing keys
- Use Counter when you need frequency counts with extra utilities like most_common()
- Always think about whether a visited set or frequency map can reduce your time complexity from O(n^2) to O(n)
