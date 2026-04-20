# Arrays and Strings - Interview Cheat Sheet

## What is an Array?

An array (list in Python) is a contiguous block of memory that stores elements sequentially. Because elements are stored contiguously, index-based access is O(1) - the computer can jump directly to any position using the base address plus an offset.

Python lists are dynamic arrays - they automatically resize when capacity is exceeded. When resizing occurs, Python allocates roughly double the space and copies all elements over, which is why append is O(1) amortized rather than always O(1).

---

## What is a String?

A string is an immutable sequence of characters. Immutable means once created, it cannot be modified in place - any operation that appears to modify a string (concatenation, replacement, slicing) creates a new string. This has important performance implications, especially in loops.

---

## Array vs String

| Feature              | Array (list)              | String (str)              |
|----------------------|---------------------------|---------------------------|
| Mutable              | Yes                       | No (immutable)            |
| Index access         | O(1)                      | O(1)                      |
| Search               | O(n)                      | O(n)                      |
| Append               | O(1) amortized            | O(n) - creates new string |
| Insert at middle     | O(n)                      | O(n)                      |
| Concatenation        | O(k) where k = added len  | O(n) - creates new string |
| Length               | O(1)                      | O(1)                      |
| Slicing              | O(k) where k = slice len  | O(k) where k = slice len  |

---

## Time Complexities

### Array (Python list)

| Operation                    | Time           | Notes                            |
|------------------------------|----------------|----------------------------------|
| Access by index              | O(1)           |                                  |
| Modify by index              | O(1)           |                                  |
| Append to end                | O(1) amortized | O(n) when resize triggered       |
| Pop from end                 | O(1)           |                                  |
| Insert at middle/front       | O(n)           | Shifts all following elements    |
| Delete at middle/front       | O(n)           | Shifts all following elements    |
| Search (in operator)         | O(n)           |                                  |
| Length                       | O(1)           |                                  |
| Slicing A[i:j]               | O(j - i)       |                                  |
| Sorting                      | O(n log n)     | Timsort                          |
| Reversing                    | O(n)           |                                  |

### String (Python str)

| Operation                    | Time           | Notes                            |
|------------------------------|----------------|----------------------------------|
| Access by index              | O(1)           |                                  |
| Search (in operator)         | O(n)           |                                  |
| Length                       | O(1)           |                                  |
| Concatenation (s + t)        | O(n)           | Creates new string each time     |
| Slicing s[i:j]               | O(j - i)       |                                  |
| Join (''.join(lst))          | O(n)           | Preferred for building strings   |
| Split                        | O(n)           |                                  |
| Replace                      | O(n)           |                                  |
| Strip / lower / upper        | O(n)           |                                  |

Space Complexity: O(n) for both.

---

## The String Concatenation Trap

Never build a string with `+` inside a loop. Each concatenation creates a new string and copies all characters, making the total cost O(n^2).

```python
# WRONG - O(n^2)
result = ''
for char in chars:
    result += char

# CORRECT - O(n)
result = ''.join(chars)
```

Always collect characters into a list, then join at the end.

---

## When to Use an Array

- You need index-based access
- You need to store and process a sequence of items
- Two pointer or sliding window problems
- Prefix sums
- Sorting-based problems
- Storing intermediate results

---

## Interview Thinking Pattern

Ask yourself:
- Can I sort the array to simplify the problem? -> Sorting often unlocks two pointer
- Can two pointers reduce O(n^2) to O(n)? -> One from each end, or fast/slow
- Can a prefix sum answer range queries in O(1)?
- Can I use extra space (hash map, set) to avoid a nested loop?
- Is the string problem a sliding window problem?

Common signals:
- "two sum", "three sum", "container with most water" -> Two pointer
- "subarray sum equals k", "range sum query" -> Prefix sum
- "longest substring without repeating", "minimum window substring" -> Sliding window
- "rotate array", "spiral matrix", "set matrix zeroes" -> In-place manipulation
- "anagram", "palindrome", "reverse words" -> String manipulation

---

## Common Patterns

### Two Pointer - Opposite Ends

```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        total = nums[left] + nums[right]
        if total == target:
            return [left, right]
        elif total < target:
            left += 1
        else:
            right -= 1
    return []
```

### Two Pointer - Fast and Slow (Remove Duplicates In-Place)

```python
def remove_duplicates(nums):
    if not nums:
        return 0
    slow = 0
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]
    return slow + 1
```

### Sliding Window - Fixed Size

```python
def max_sum_subarray(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum
```

### Sliding Window - Variable Size

```python
def longest_substring_without_repeating(s):
    seen = {}
    left = 0
    max_len = 0
    for right, char in enumerate(s):
        if char in seen and seen[char] >= left:
            left = seen[char] + 1
        seen[char] = right
        max_len = max(max_len, right - left + 1)
    return max_len
```

### Prefix Sum

```python
def build_prefix(nums):
    prefix = [0] * (len(nums) + 1)
    for i, num in enumerate(nums):
        prefix[i + 1] = prefix[i] + num
    return prefix

# Range sum query [l, r] inclusive - O(1)
def range_sum(prefix, l, r):
    return prefix[r + 1] - prefix[l]
```

### Kadane's Algorithm - Maximum Subarray

```python
def max_subarray(nums):
    max_sum = curr_sum = nums[0]
    for num in nums[1:]:
        curr_sum = max(num, curr_sum + num)
        max_sum = max(max_sum, curr_sum)
    return max_sum
```

### Check Anagram

```python
from collections import Counter

def is_anagram(s, t):
    return Counter(s) == Counter(t)
```

### Check Palindrome

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

### Reverse Words in a String

```python
def reverse_words(s):
    return ' '.join(s.split()[::-1])
```

---

## Code Reference

### Array Operations

```python
A = [1, 2, 3]

# Append to end - O(1) amortized
A.append(5)
print(A)  # => [1, 2, 3, 5]

# Pop from end - O(1)
A.pop()
print(A)  # => [1, 2, 3]

# Insert at index (not at end) - O(n)
A.insert(2, 5)
print(A)  # => [1, 2, 5, 3]

# Modify element - O(1)
A[0] = 7
print(A)  # => [7, 2, 5, 3]

# Access element by index - O(1)
print(A[2])  # => 5

# Search - O(n)
if 7 in A:
    print(True)  # => True

# Length - O(1)
print(len(A))  # => 4

# Slicing - O(k)
print(A[1:3])  # => [2, 5]

# Reverse in place - O(n)
A.reverse()

# Sort in place - O(n log n)
A.sort()

# Sort descending
A.sort(reverse=True)

# Sorted (returns new list) - O(n log n)
B = sorted(A)

# List comprehension
squares = [x * x for x in range(5)]  # => [0, 1, 4, 9, 16]

# Initialize array of zeros
zeros = [0] * 5  # => [0, 0, 0, 0, 0]

# 2D array
grid = [[0] * cols for _ in range(rows)]
```

### String Operations

```python
s = 'hello'

# Concatenation - O(n), creates new string
b = s + 'z'
print(b)  # => helloz

# Search - O(n)
if 'f' in s:
    print(True)

# Access by index - O(1)
print(s[2])  # => l

# Length - O(1)
print(len(s))  # => 5

# Slicing - O(k)
print(s[1:4])  # => ell

# Reverse a string
print(s[::-1])  # => olleh

# Split into list of words - O(n)
words = 'hello world'.split()  # => ['hello', 'world']
parts = 'a,b,c'.split(',')     # => ['a', 'b', 'c']

# Join list into string - O(n) - preferred for building strings
result = '->'.join(['a', 'b', 'c'])  # => a->b->c

# Strip whitespace
s = '  hello  '.strip()  # => 'hello'

# Case conversion
print('Hello'.lower())  # => hello
print('Hello'.upper())  # => HELLO

# Check type of characters
'abc'.isalpha()   # => True
'123'.isdigit()   # => True
'abc123'.isalnum()# => True

# Replace
print('hello'.replace('l', 'r'))  # => herro

# Find index of substring - returns -1 if not found
print('hello'.find('ll'))  # => 2

# Count occurrences
print('hello'.count('l'))  # => 2

# String to list of chars (to mutate)
chars = list('hello')
chars[0] = 'j'
result = ''.join(chars)  # => jello
```

---

## Key Things to Remember

- Python lists are dynamic arrays - append is O(1) amortized, insert and delete at arbitrary positions are O(n)
- Strings are immutable - use `''.join(list)` to build strings in loops, never `+=`
- Slicing always creates a copy - it does not modify in place
- Two pointer reduces many O(n^2) brute force problems to O(n)
- Sliding window is two pointer applied to subarray or substring problems
- Prefix sum enables O(1) range sum queries after O(n) preprocessing
- Sorting the array first is often the key insight to unlock a two pointer or greedy solution
- For 2D grid problems, always initialize with `[[0] * cols for _ in range(rows)]` not `[[0] * cols] * rows` - the latter creates rows that all reference the same list
