# Linked Lists - Interview Cheat Sheet

## What is a Linked List?

A linked list is a linear data structure where elements (nodes) are stored in sequence, but unlike arrays, they are not stored in contiguous memory. Each node holds a value and a pointer to the next node (and previous node in a doubly linked list).

The list is accessed through the head node. You must traverse from the head to reach any element - there is no index-based access.

---

## Singly vs Doubly Linked List

| Feature              | Singly Linked List       | Doubly Linked List              |
|----------------------|--------------------------|---------------------------------|
| Pointers per node    | next only                | next and prev                   |
| Traversal            | Forward only             | Forward and backward            |
| Space per node       | Less                     | More                            |
| Insert at beginning  | O(1)                     | O(1)                            |
| Insert at end        | O(n) without tail ptr    | O(1) with tail pointer          |
| Delete a node        | O(n) (need predecessor)  | O(1) if node reference is known |
| Common use cases     | Stacks, simple lists     | Deques, LRU cache               |

---

## Linked List vs Array

| Feature        | Array        | Linked List         |
|----------------|--------------|---------------------|
| Access by index| O(1)         | O(n)                |
| Search         | O(n)         | O(n)                |
| Insert at head | O(n)         | O(1)                |
| Insert at tail | O(1) amort.  | O(1) with tail ptr  |
| Insert middle  | O(n)         | O(n) to find + O(1) |
| Delete         | O(n)         | O(n) to find + O(1) |
| Memory         | Contiguous   | Scattered           |
| Cache friendly | Yes          | No                  |

---

## Time Complexities

| Operation              | Singly         | Doubly         |
|------------------------|----------------|----------------|
| Access by index        | O(n)           | O(n)           |
| Search                 | O(n)           | O(n)           |
| Insert at head         | O(1)           | O(1)           |
| Insert at tail         | O(1)*          | O(1)*          |
| Insert in middle       | O(n)           | O(n)           |
| Delete at head         | O(1)           | O(1)           |
| Delete at tail         | O(n)           | O(1)*          |
| Delete in middle       | O(n)           | O(1)*          |
| Traversal              | O(n)           | O(n)           |

*Requires keeping a tail pointer. Doubly linked list delete in middle is O(1) only if you already have a reference to the node.

Space Complexity: O(n) for n nodes.

---

## When to Use a Linked List

- You need frequent insertions or deletions at the head or tail
- You do not need random index access
- Implementing stacks, queues, or deques from scratch
- LRU cache (doubly linked list + hash map)
- Problems involving pointer manipulation (reversal, cycle detection, merging)

---

## Interview Thinking Pattern

Ask yourself:
- Can I solve this with two pointers (fast/slow)? -> Cycle detection, middle of list, Nth from end
- Do I need to reverse part of the list? -> In-place pointer manipulation
- Do I need O(1) insert/delete at both ends? -> Doubly linked list
- Is there a cycle? -> Floyd's algorithm (fast and slow pointer)

Common signals:
- "detect cycle", "find middle", "reverse linked list"
- "merge two sorted lists", "remove Nth node from end"
- "palindrome linked list", "reorder list"
- "LRU cache"

---

## Common Patterns

### Two Pointer - Find Middle of List

```python
def find_middle(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # slow is at the middle
```

### Two Pointer - Detect Cycle (Floyd's Algorithm)

```python
def has_cycle(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

### Two Pointer - Find Cycle Start

```python
def cycle_start(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # No cycle
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    return slow  # Cycle start node
```

### Two Pointer - Nth Node from End

```python
def remove_nth_from_end(head, n):
    dummy = SinglyNode(0)
    dummy.next = head
    slow = fast = dummy
    for _ in range(n + 1):
        fast = fast.next
    while fast:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next
```

### Reverse a Linked List (Iterative)

```python
def reverse(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    return prev  # New head
```

### Reverse a Linked List (Recursive)

```python
def reverse_recursive(head):
    if not head or not head.next:
        return head
    new_head = reverse_recursive(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

### Merge Two Sorted Lists

```python
def merge_sorted(l1, l2):
    dummy = SinglyNode(0)
    curr = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    curr.next = l1 if l1 else l2
    return dummy.next
```

### Check Palindrome

```python
def is_palindrome(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    # Reverse second half
    prev = None
    while slow:
        next_node = slow.next
        slow.next = prev
        prev = slow
        slow = next_node
    # Compare both halves
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True
```

---

## Code Reference

### Singly Linked List

```python
class SinglyNode:
    def __init__(self, val, next=None):
        self.val = val
        self.next = next

    def __str__(self):
        return str(self.val)

# Build list: 1 -> 2 -> 3 -> 4
Head = SinglyNode(1)
A = SinglyNode(2)
B = SinglyNode(3)
C = SinglyNode(4)
Head.next = A
A.next = B
B.next = C
```

### Traversal - O(n)

```python
curr = Head
while curr:
    print(curr)
    curr = curr.next
```

### Display

```python
def display(head):
    curr = head
    elements = []
    while curr:
        elements.append(str(curr.val))
        curr = curr.next
    print('->'.join(elements))

display(Head)
# => 1->2->3->4
```

### Search - O(n)

```python
def search(head, val):
    curr = head
    while curr:
        if val == curr.val:
            return True
        curr = curr.next
    return False

search(Head, 2)  # => True
```

### Insert at Head (Singly) - O(1)

```python
def insert_at_beginning(head, val):
    new_node = SinglyNode(val, next=head)
    return new_node  # New head

Head = insert_at_beginning(Head, 0)
display(Head)
# => 0->1->2->3->4
```

### Insert at Tail (Singly) - O(n) without tail pointer

```python
def insert_at_end(head, val):
    new_node = SinglyNode(val)
    if not head:
        return new_node
    curr = head
    while curr.next:
        curr = curr.next
    curr.next = new_node
    return head

Head = insert_at_end(Head, 5)
display(Head)
# => 0->1->2->3->4->5
```

### Delete a Node by Value (Singly) - O(n)

```python
def delete(head, val):
    if not head:
        return None
    if head.val == val:
        return head.next  # New head
    curr = head
    while curr.next:
        if curr.next.val == val:
            curr.next = curr.next.next
            return head
        curr = curr.next
    return head  # Val not found
```

---

### Doubly Linked List

```python
class DoublyNode:
    def __init__(self, val, next=None, prev=None):
        self.val = val
        self.next = next
        self.prev = prev

    def __str__(self):
        return str(self.val)

head = tail = DoublyNode(1)
```

### Insert at Beginning (Doubly) - O(1)

```python
def insert_at_beginning(head, tail, val):
    new_node = DoublyNode(val, next=head)
    head.prev = new_node
    return new_node, tail

head, tail = insert_at_beginning(head, tail, 3)
display(head)
# => 3->1
```

### Insert at End (Doubly) - O(1) with tail pointer

```python
def insert_at_end(head, tail, val):
    new_node = DoublyNode(val, prev=tail)
    tail.next = new_node
    return head, new_node

head, tail = insert_at_end(head, tail, 7)
display(head)
# => 3->1->7
```

### Delete at Head (Doubly) - O(1)

```python
def delete_at_beginning(head, tail):
    if not head:
        return None, None
    if head == tail:
        return None, None
    new_head = head.next
    new_head.prev = None
    return new_head, tail
```

### Delete at Tail (Doubly) - O(1) with tail pointer

```python
def delete_at_end(head, tail):
    if not tail:
        return None, None
    if head == tail:
        return None, None
    new_tail = tail.prev
    new_tail.next = None
    return head, new_tail
```

---

## Dummy Node Trick

Many linked list problems become cleaner with a dummy (sentinel) node at the head. It eliminates edge cases for operations at the head.

```python
dummy = SinglyNode(0)
dummy.next = head
# ... manipulate list using dummy.next as reference
return dummy.next  # Return real head
```

Use this whenever deletion or insertion at the head complicates the logic.

---

## Key Things to Remember

- Always check for `None` before accessing `.next` or `.val`
- Use a dummy node to simplify edge cases involving the head
- Always track the tail pointer if you need O(1) insert at end
- Fast and slow pointer (Floyd's) solves cycle detection, middle finding, and Nth from end
- Reversing a list in-place requires tracking `prev`, `curr`, and `next_node` simultaneously
- Doubly linked list gives O(1) delete if you have a direct node reference - this is why it is used in LRU cache
- Drawing out the pointer changes before coding saves significant debugging time in interviews
