# 📚 Day 9: Linked List — Complete Mastery Guide (Enhanced Edition for Students)

## SECTION 1: COMPLETE DEFINITIONS & CORE CONCEPTS (For Students)

### What is a Linked List? 🤔

#### **Definition (Official):**

> **A Linked List** is a **linear data structure** where elements are stored in **nodes**, and each node contains:
> 1. **Data** (value)
> 2. **Pointer** (reference) to the **next node** in sequence
>
> Unlike arrays, linked list elements are **NOT stored at contiguous memory locations** - they are **scattered** and **connected via pointers**.

**Key Properties:**

| Property | Description | Why Important |
|----------|-------------|---------------|
| **Dynamic Size** | Size changes at runtime | No fixed capacity needed |
| **Non-Contiguous Memory** | Nodes scattered in memory | Flexible allocation |
| **Node Structure** | Each node = (data, next pointer) | Basic building unit |
| **Head Pointer** | First node tracked by `head` | Entry point to list |
| **Null Termination** | Last node's next = `null` | Marks end of list |
| **O(1) Insert/Delete** | At known position | Faster than array |
| **O(n) Access** | Must traverse | Slower than array |

**Visual Diagram:**

```text
Linked List:  [10] → [20] → [30] → [40] → null

Node Structure:
┌─────────────┐
│  Data: 10   │
│  Next: ───→ │  Points to next node
└─────────────┘

Memory Layout (Non-Contiguous):
Address 0x100: Node(10) → Address 0x250
Address 0x250: Node(20) → Address 0x300
Address 0x300: Node(30) → Address 0x400
Address 0x400: Node(40) → null

NOT like Array: [10, 20, 30, 40] (contiguous addresses)
```

**Comparison with Array:**

| Feature | Array | Linked List |
|---------|-------|-------------|
| **Memory** | Contiguous | Non-contiguous |
| **Size** | Fixed (compile-time) | Dynamic (runtime) |
| **Insert at End** | O(1) if space, O(n) if resize | O(1) with tail pointer |
| **Insert at Start** | O(n) (shift all) | O(1) |
| **Delete at Start** | O(n) (shift all) | O(1) |
| **Access by Index** | O(1) | O(n) (traverse) |
| **Search** | O(n) | O(n) |
| **Space Overhead** | O(n) | O(n) + O(n) pointers |

### Types of Linked Lists

| Type | Structure | Properties |
|------|-----------|------------|
| **Singly Linked List** | Node → next → next → ... → null | Each node has 1 pointer (next) |
| **Doubly Linked List** | prev → Node → next → ... | Each node has 2 pointers (prev + next) |
| **Circular Linked List** | Node → next → ... → first → Node | Last node points to first (no null) |
| **Sentinel Node** | dummy → head → ... → tail → null | Dummy node simplifies operations |

**Visual:**

```text
Singly Linked List:
 [10] → [20] → [30] → null

Doubly Linked List:
null ← [10] ↔ [20] ↔ [30] → null
       (prev)  (next)

Circular Linked List:
 [10] → [20] → [30]
 ↑             ↓
 └─────────────┘
(last points to first)
```

---

## SECTION 2: COMPLETE NOTES MAKING POINTS (For Students)

### 📝 **Essential Notes for Exam/Interviews**

#### **Point 1: Node Structure (The Building Block)**

**Definition:**

> **A Node** is the basic unit of linked list containing:
> 1. **Data field** (value)
> 2. **Pointer field** (reference to next node)

**Singly Linked Node:**

```java
class Node {
    int data;       // Data field
    Node next;      // Pointer to next node
    
    // Constructor
    Node(int data) {
        this.data = data;
        this.next = null;
    }
}
```

**Doubly Linked Node:**

```java
class Node {
    int data;       // Data field
    Node prev;      // Pointer to previous node
    Node next;      // Pointer to next node
    
    // Constructor
    Node(int data) {
        this.data = data;
        this.prev = null;
        this.next = null;
    }
}
```

**C++ Node:**

```cpp
struct Node {
    int data;       // Data field
    Node* next;     // Pointer to next node
    
    // Constructor
    Node(int val) {
        data = val;
        next = nullptr;
    }
};
```

**Key Terminology:**

| Keyword | Meaning | Example |
|---------|---------|---------|
| **Node** | Individual element | `Node node = new Node(10)` |
| **Head** | First node in list | `head = node` |
| **Tail** | Last node in list | `tail.next = null` |
| **Next** | Pointer to next node | `node.next = nextNode` |
| **Prev** | Pointer to prev node | `node.prev = prevNode` |
| **Null** | End of list marker | `tail.next = null` |
| **Pointer** | Reference/address | `Node* ptr = &node` |
| **Reference** | Object reference | `Node ref = node` |
| **Traversal** | Going through nodes | `while (curr != null)` |

#### **Point 2: TIME COMPLEXITY TABLE**

| Operation | Singly Linked | Doubly Linked | Array | Explanation |
|-----------|---------------|---------------|-------|-------------|
| **Access by Index** | O(n) | O(n) | O(1) | Must traverse |
| **Search** | O(n) | O(n) | O(n) | Linear scan |
| **Insert at Start** | O(1) | O(1) | O(n) | No shifting needed |
| **Insert at End** | O(n)* | O(n)* | O(1) | *With tail pointer: O(1) |
| **Insert at Middle** | O(n) | O(n) | O(n) | Traverse + insert |
| **Delete at Start** | O(1) | O(1) | O(n) | No shifting needed |
| **Delete at End** | O(n)* | O(n)* | O(1) | *With tail: O(1) for doubly |
| **Delete at Middle** | O(n) | O(n) | O(n) | Traverse + delete |
| **Update by Index** | O(n) | O(n) | O(1) | Traverse + update |

*Note: With **tail pointer**, insert/delete at end becomes O(1)*

**Why O(n) for Access?**

```text
List:  [10] → [20] → [30] → [40]

Get index 3 (value 40):
curr = head (10)
curr = curr.next (20)     step 1
curr = curr.next (30)     step 2
curr = curr.next (40)     step 3

Steps = index = n
Time: O(n)
```

**Why O(1) for Insert at Start?**

```text
Insert 5 at start:
newNode = new Node(5)
newNode.next = head
head = newNode

Steps: 3 operations
Time: O(1)
```

#### **Point 3: KEY OPERATIONS (ALL FUNCTIONS)**

**All Essential Functions:**

| Function | Purpose | Java Code | C++ Code | Time |
|----------|---------|-----------|----------|------|
| **createNode** | Create new node | `new Node(data)` | `new Node(data)` | O(1) |
| **insertAtStart** | Add at beginning | `insertAtStart(data)` | `insertAtStart(data)` | O(1) |
| **insertAtEnd** | Add at end | `insertAtEnd(data)` | `insertAtEnd(data)` | O(n) / O(1)* |
| **insertAtPos** | Add at position | `insertAtPos(pos, data)` | `insertAtPos(pos, data)` | O(n) |
| **deleteAtStart** | Remove first | `deleteAtStart()` | `deleteAtStart()` | O(1) |
| **deleteAtEnd** | Remove last | `deleteAtEnd()` | `deleteAtEnd()` | O(n) / O(1)* |
| **deleteAtPos** | Remove at position | `deleteAtPos(pos)` | `deleteAtPos(pos)` | O(n) |
| **search** | Find element | `search(data)` | `search(data)` | O(n) |
| **get** | Get by index | `get(index)` | `get(index)` | O(n) |
| **update** | Update by index | `update(index, data)` | `update(index, data)` | O(n) |
| **size** | Count nodes | `size()` | `size()` | O(1) |
| **isEmpty** | Check empty | `isEmpty()` | `isEmpty()` | O(1) |
| **clear** | Remove all | `clear()` | `clear()` | O(n) |
| **traverse** | Print all | `traverse()` | `traverse()` | O(n) |
| **reverse** | Reverse list | `reverse()` | `reverse()` | O(n) |
| **middle** | Find middle | `middle()` | `middle()` | O(n) |

*With tail pointer

#### **Point 4: STANDARD OPERATIONS (Step-by-Step)**

**INSERTION at Start:**

```text
Step 1: Create new node
newNode = new Node(5)

Step 2: Link new node to current head
newNode.next = head

Step 3: Update head to new node
head = newNode

Before:  [10] → [20] → [30] → null
After:   [5] → [10] → [20] → [30] → null

Java Code:
public void insertAtStart(int data) {
    Node newNode = new Node(data);
    newNode.next = head;
    head = newNode;
}

C++ Code:
void insertAtStart(int data) {
    Node* newNode = new Node(data);
    newNode->next = head;
    head = newNode;
}

Time: O(1)
```

**INSERTION at End:**

```text
Step 1: Create new node
newNode = new Node(40)

Step 2: Traverse to last node
curr = head
while (curr.next != null) { curr = curr.next; }

Step 3: Link last node to new node
curr.next = newNode

Before:  [10] → [20] → [30] → null
After:   [10] → [20] → [30] → [40] → null

Java Code:
public void insertAtEnd(int data) {
    Node newNode = new Node(data);
    
    if (head == null) {
        head = newNode;
        return;
    }
    
    Node curr = head;
    while (curr.next != null) {
        curr = curr.next;
    }
    
    curr.next = newNode;
}

C++ Code:
void insertAtEnd(int data) {
    Node* newNode = new Node(data);
    
    if (head == nullptr) {
        head = newNode;
        return;
    }
    
    Node* curr = head;
    while (curr->next != nullptr) {
        curr = curr->next;
    }
    
    curr->next = newNode;
}

Time: O(n)
```

**DELETE at Start:**

```text
Step 1: Check if list empty
if (head == null) return null;

Step 2: Store current head
Node temp = head;

Step 3: Move head to next
head = head.next;

Step 4: Return deleted node
return temp;

Before:  [10] → [20] → [30] → null
After:   [20] → [30] → null
Deleted: 10

Java Code:
public Node deleteAtStart() {
    if (head == null) return null;
    
    Node temp = head;
    head = head.next;
    
    return temp;
}

C++ Code:
Node* deleteAtStart() {
    if (head == nullptr) return nullptr;
    
    Node* temp = head;
    head = head->next;
    
    return temp;
}

Time: O(1)
```

**DELETE at End:**

```text
Step 1: Check if list empty or single node
if (head == null) return null;
if (head.next == null) { head = null; return head; }

Step 2: Traverse to second-last node
Node curr = head;
while (curr.next.next != null) { curr = curr.next; }

Step 3: Remove last node
curr.next = null;

Before:  [10] → [20] → [30] → null
After:   [10] → [20] → null
Deleted: 30

Java Code:
public Node deleteAtEnd() {
    if (head == null) return null;
    if (head.next == null) { head = null; return head; }
    
    Node curr = head;
    while (curr.next.next != null) {
        curr = curr.next;
    }
    
    Node temp = curr.next;
    curr.next = null;
    
    return temp;
}

C++ Code:
Node* deleteAtEnd() {
    if (head == nullptr) return nullptr;
    if (head->next == nullptr) { head = nullptr; return head; }
    
    Node* curr = head;
    while (curr->next->next != nullptr) {
        curr = curr->next;
    }
    
    Node* temp = curr->next;
    curr->next = nullptr;
    
    return temp;
}

Time: O(n)
```

**SEARCH for Element:**

```text
Step 1: Start from head
Node curr = head;

Step 2: Traverse until found or null
while (curr != null) {
    if (curr.data == target) return curr;
    curr = curr.next;
}

Step 3: Return null if not found
return null;

Java Code:
public Node search(int target) {
    Node curr = head;
    
    while (curr != null) {
        if (curr.data == target) {
            return curr;
        }
        curr = curr.next;
    }
    
    return null;
}

C++ Code:
Node* search(int target) {
    Node* curr = head;
    
    while (curr != nullptr) {
        if (curr->data == target) {
            return curr;
        }
        curr = curr->next;
    }
    
    return nullptr;
}

Time: O(n)
```

**GET by Index:**

```text
Step 1: Check invalid index
if (index < 0) return null;

Step 2: Start from head
Node curr = head;

Step 3: Traverse to index
for (int i = 0; i < index && curr != null; i++) {
    curr = curr.next;
}

Step 4: Return node at index
return curr;

Java Code:
public Node get(int index) {
    if (index < 0) return null;
    
    Node curr = head;
    
    for (int i = 0; i < index && curr != null; i++) {
        curr = curr.next;
    }
    
    return curr;
}

C++ Code:
Node* get(int index) {
    if (index < 0) return nullptr;
    
    Node* curr = head;
    
    for (int i = 0; i < index && curr != nullptr; i++) {
        curr = curr->next;
    }
    
    return curr;
}

Time: O(n)
```

**UPDATE at Index:**

```text
Step 1: Get node at index
Node node = get(index);

Step 2: Check if node exists
if (node == null) return false;

Step 3: Update data
node.data = newData;

Step 4: Return success
return true;

Java Code:
public boolean update(int index, int newData) {
    Node node = get(index);
    
    if (node == null) return false;
    
    node.data = newData;
    return true;
}

Time: O(n)
```

#### **Point 5: ADVANCED OPERATIONS (Interview Focused)**

**REVERSE a Linked List (3 Methods):**

**Method 1: Iterative (3 Pointers)**

```text
Step 1: Initialize pointers
prev = null, curr = head, next = null

Step 2: Traverse and reverse
while (curr != null) {
    next = curr.next;      // Save next
    curr.next = prev;      // Reverse pointer
    prev = curr;           // Move prev
    curr = next;           // Move curr
}

Step 3: Update head
head = prev;

Before:  [10] → [20] → [30] → null
After:   null ← [10] ← [20] ← [30]
head = [30] → [20] → [10] → null

Java Code:
public void reverse() {
    Node prev = null;
    Node curr = head;
    Node next = null;
    
    while (curr != null) {
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    
    head = prev;
}

C++ Code:
void reverse() {
    Node* prev = nullptr;
    Node* curr = head;
    Node* next = nullptr;
    
    while (curr != nullptr) {
        next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    
    head = prev;
}

Time: O(n) | Space: O(1)
```

**Method 2: Recursive**

```text
recursiveReverse(curr):
    if (curr == null || curr.next == null) {
        head = curr;
        return;
    }
    
    recursiveReverse(curr.next);
    curr.next.next = curr;
    curr.next = null;

Java Code:
public void reverseRecursive() {
    if (head == null) return;
    reverseHelper(head);
}

private Node reverseHelper(Node curr) {
    if (curr == null || curr.next == null) {
        head = curr;
        return curr;
    }
    
    Node newHead = reverseHelper(curr.next);
    curr.next.next = curr;
    curr.next = null;
    
    return newHead;
}

Time: O(n) | Space: O(n) (recursion stack)
```

**Method 3: Stack**

```text
Step 1: Push all nodes to stack
Stack<Node> stack;
Node curr = head;
while (curr != null) {
    stack.push(curr);
    curr = curr.next;
}

Step 2: Pop and rebuild
head = stack.pop();
curr = head;
while (!stack.isEmpty()) {
    curr.next = stack.pop();
    curr = curr.next;
}
curr.next = null;

Time: O(n) | Space: O(n)
```

**FIND Middle Node (2 Pointer Trick):**

```text
Step 1: Initialize 2 pointers
slow = head, fast = head

Step 2: Traverse
while (fast != null && fast.next != null) {
    slow = slow.next;        // Move 1 step
    fast = fast.next.next;   // Move 2 steps
}

Step 3: Return slow
return slow;  // Middle node

Before:  [10] → [20] → [30] → [40] → [50]
        slow=10, fast=10
        slow=20, fast=30
        slow=30, fast=50
        slow=40, fast=null
After: middle = [30]

For even length:  [10] → [20] → [30] → [40]
middle = [20] (first middle) or [30] (second middle)

Java Code:
public Node findMiddle() {
    Node slow = head;
    Node fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    return slow;
}

C++ Code:
Node* findMiddle() {
    Node* slow = head;
    Node* fast = head;
    
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
    }
    
    return slow;
}

Time: O(n) | Space: O(1)
```

**CHECK Cycle (Loop Detection):**

```text
Step 1: Initialize 2 pointers
slow = head, fast = head

Step 2: Traverse
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
    
    if (slow == fast) {
        return true;  // Cycle found!
    }
}

Step 3: No cycle
return false;

With cycle:  [10] → [20] → [30] → [40]
                           ↑         ↓
                           └─────────┘
slow=10, fast=10
slow=20, fast=30
slow=30, fast=20
slow=40, fast=40 → MATCH! Cycle found

Java Code:
public boolean hasCycle() {
    Node slow = head;
    Node fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast) {
            return true;
        }
    }
    
    return false;
}

C++ Code:
bool hasCycle() {
    Node* slow = head;
    Node* fast = head;
    
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        
        if (slow == fast) {
            return true;
        }
    }
    
    return false;
}

Time: O(n) | Space: O(1)
```

**MERGE Two Sorted Lists:**

```text
Step 1: Create dummy node
Node dummy = new Node(0);
Node curr = dummy;

Step 2: Compare and merge
while (list1 != null && list2 != null) {
    if (list1.data <= list2.data) {
        curr.next = list1;
        list1 = list1.next;
    } else {
        curr.next = list2;
        list2 = list2.next;
    }
    curr = curr.next;
}

Step 3: Attach remaining
if (list1 != null) curr.next = list1;
if (list2 != null) curr.next = list2;

Step 4: Return merged
return dummy.next;

List1:  [1] → [3] → [5]
List2:  [2] → [4] → [6]

Merged:  [1] → [2] → [3] → [4] → [5] → [6]

Java Code:
public Node mergeSorted(Node list1, Node list2) {
    Node dummy = new Node(0);
    Node curr = dummy;
    
    while (list1 != null && list2 != null) {
        if (list1.data <= list2.data) {
            curr.next = list1;
            list1 = list1.next;
        } else {
            curr.next = list2;
            list2 = list2.next;
        }
        curr = curr.next;
    }
    
    if (list1 != null) curr.next = list1;
    if (list2 != null) curr.next = list2;
    
    return dummy.next;
}

Time: O(n + m) | Space: O(1)
```

**REMOVE Nth Node from End:**

```text
Step 1: Initialize 2 pointers
slow = head, fast = head

Step 2: Move fast n steps ahead
for (int i = 0; i < n; i++) {
    fast = fast.next;
}

Step 3: If fast is null, remove head
if (fast == null) {
    return head.next;
}

Step 4: Move both until fast at end
while (fast.next != null) {
    slow = slow.next;
    fast = fast.next;
}

Step 5: Remove nth node
slow.next = slow.next.next;

List:  [1] → [2] → [3] → [4] → [5], n = 2
slow=1, fast=1
slow=1, fast=3 (move fast 2 steps)
slow=3, fast=5 (move both until fast at end)
Remove: slow.next = slow.next.next → [3].next = [5]

Result:  [1] → [2] → [3] → [5]

Java Code:
public Node removeNthFromEnd(Node head, int n) {
    Node slow = head;
    Node fast = head;
    
    for (int i = 0; i < n; i++) {
        fast = fast.next;
    }
    
    if (fast == null) {
        return head.next;
    }
    
    while (fast.next != null) {
        slow = slow.next;
        fast = fast.next;
    }
    
    slow.next = slow.next.next;
    
    return head;
}

Time: O(n) | Space: O(1)
```

#### **Point 6: DUMMY NODE TECHNIQUE (Simplifies Code)**

**Definition:**

> **A Dummy Node** is a placeholder node at the start of linked list that simplifies operations by **avoiding special cases** for head manipulation.

**Why Use Dummy?**

```text
Without Dummy:
- Special case: if (head == null)
- Special case: if (operation at head)
- Complex code

With Dummy:
- No special case for head
- Uniform code for all positions
- Simplifies logic significantly
```

---

## SECTION 4: PROBLEM PROGRESSION — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Reverse Linked List**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/reverse-linked-list/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution (Iterative 3-Pointer):**
```java
public Node reverseList(Node head) {
    Node prev = null;
    Node curr = head;
    Node next = null;
    
    while (curr != null) {
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    
    return prev;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **2. Middle of the Linked List**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/middle-of-the-linked-list/
- **Company Tags**: Google, Amazon, Microsoft
- **Frequency**: 9/10

**Solution (2 Pointer Trick):**
```java
public Node middleNode(Node head) {
    Node slow = head;
    Node fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    return slow;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### Level 2: Easy

#### **3. Merge Two Sorted Lists**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/merge-two-sorted-lists/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution:**
```java
public Node mergeTwoLists(Node list1, Node list2) {
    Node dummy = new Node(0);
    Node curr = dummy;
    
    while (list1 != null && list2 != null) {
        if (list1.val <= list2.val) {
            curr.next = list1;
            list1 = list1.next;
        } else {
            curr.next = list2;
            list2 = list2.next;
        }
        curr = curr.next;
    }
    
    if (list1 != null) curr.next = list1;
    if (list2 != null) curr.next = list2;
    
    return dummy.next;
}
```

**Time**: O(n + m) | **Space**: O(1)

---

#### **4. Remove Linked List Elements**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/remove-linked-list-elements/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 7/10

**Solution:**
```java
public Node removeElements(Node head, int val) {
    Node dummy = new Node(0);
    dummy.next = head;
    Node curr = dummy;
    
    while (curr.next != null) {
        if (curr.next.val == val) {
            curr.next = curr.next.next;
        } else {
            curr = curr.next;
        }
    }
    
    return dummy.next;
}
```

---

#### Level 3: Medium

#### **5. Linked List Cycle**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/linked-list-cycle/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Cycle Detection):**
```java
public boolean hasCycle(Node head) {
    Node slow = head;
    Node fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast) {
            return true;
        }
    }
    
    return false;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **6. Remove Nth Node From End of List**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/remove-nth-node-from-end-of-list/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution:**
```java
public Node removeNthFromEnd(Node head, int n) {
    Node slow = head;
    Node fast = head;
    
    for (int i = 0; i < n; i++) {
        fast = fast.next;
    }
    
    if (fast == null) {
        return head.next;
    }
    
    while (fast.next != null) {
        slow = slow.next;
        fast = fast.next;
    }
    
    slow.next = slow.next.next;
    
    return head;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **7. Palindrome Linked List**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/palindrome-linked-list/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution:**
```java
public boolean isPalindrome(Node head) {
    if (head == null) return true;
    
    // Find middle
    Node middle = findMiddle(head);
    
    // Reverse second half
    Node secondHalf = reverseList(middle.next);
    middle.next = null;
    
    // Compare
    Node first = head;
    Node second = secondHalf;
    while (second != null) {
        if (first.val != second.val) return false;
        first = first.next;
        second = second.next;
    }
    
    return true;
}

private Node findMiddle(Node head) {
    Node slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### Level 4: Hard

#### **8. Copy List with Random Pointer**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/copy-list-with-random-pointer/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution:**
```java
public Node copyRandomList(Node head) {
    if (head == null) return null;
    
    // Step 1: Create interleaved list
    Node curr = head;
    while (curr != null) {
        Node newNode = new Node(curr.val);
        newNode.next = curr.next;
        curr.next = newNode;
        curr = newNode.next;
    }
    
    // Step 2: Set random pointers
    curr = head;
    while (curr != null) {
        if (curr.random != null) {
            curr.next.random = curr.random.next;
        }
        curr = curr.next.next;
    }
    
    // Step 3: Split lists
    Node newHead = head.next;
    curr = head;
    while (curr != null) {
        Node newNode = curr.next;
        curr.next = newNode.next;
        if (newNode.next != null) {
            newNode.next = newNode.next.next;
        }
        curr = curr.next;
    }
    
    return newHead;
}
```

**Time**: O(n) | **Space**: O(1)

---

### CodeChef Problems

#### **9. Linked List Operations**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/learn/course/data-structures/linked-lists (search "Linked List")
- **Rating**: 800

Practice insert, delete, traverse operations.

---

### Codeforces Problems

#### **10. Linked List Problem**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset (search "linked list 1000")
- **Rating**: 1000-1200

Practice reverse, cycle detection, merge problems.

---

## SECTION 5: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Easy (7 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Reverse Linked List | https://leetcode.com/problems/reverse-linked-list/ |
| 2 | Middle of Linked List | https://leetcode.com/problems/middle-of-the-linked-list/ |
| 3 | Merge Two Sorted Lists | https://leetcode.com/problems/merge-two-sorted-lists/ |
| 4 | Remove Linked List Elements | https://leetcode.com/problems/remove-linked-list-elements/ |
| 5 | Delete Node in List | https://leetcode.com/problems/delete-node-in-a-linked-list/ |
| 6 | Linked List Cycle II | https://leetcode.com/problems/linked-list-cycle-ii/ |
| 7 | Partition List | https://leetcode.com/problems/partition-list/ |

### Medium (8 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Linked List Cycle | https://leetcode.com/problems/linked-list-cycle/ |
| 2 | Remove Nth Node From End | https://leetcode.com/problems/remove-nth-node-from-end-of-list/ |
| 3 | Palindrome Linked List | https://leetcode.com/problems/palindrome-linked-list/ |
| 4 | Add Two Numbers | https://leetcode.com/problems/add-two-numbers/ |
| 5 | Rotate List | https://leetcode.com/problems/rotate-list/ |
| 6 | Swap Nodes in Pairs | https://leetcode.com/problems/swap-nodes-in-pairs/ |
| 7 | Linked List Random Node | https://leetcode.com/problems/linked-list-random-node/ |
| 8 | Split Linked List in Parts | https://leetcode.com/problems/split-linked-list-in-parts/ |

### Hard (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Copy List with Random Pointer | https://leetcode.com/problems/copy-list-with-random-pointer/ |
| 2 | Flatten a Multilevel List | https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/ |
| 3 | Reverse Nodes in K-Group | https://leetcode.com/problems/reverse-nodes-in-k-group/ |
| 4 | LRUCache | https://leetcode.com/problems/lru-cache/ |
| 5 | Merge K Sorted Lists | https://leetcode.com/problems/merge-k-sorted-lists/ |

---

## SECTION 6: Active Learning — 5 Questions

1. **Reverse Linked List** — LeetCode 206  
   [Link](https://leetcode.com/problems/reverse-linked-list/)

2. **Middle of Linked List** — LeetCode 876  
   [Link](https://leetcode.com/problems/middle-of-the-linked-list/)

3. **Merge Two Sorted** — LeetCode 21  
   [Link](https://leetcode.com/problems/merge-two-sorted-lists/)

4. **Linked List Cycle** — LeetCode 141  
   [Link](https://leetcode.com/problems/linked-list-cycle/)

5. **Remove Nth From End** — LeetCode 19  
   [Link](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 7: Revision Notes — CHEAT SHEET

### 📝 **Linked List Cheat Sheet**

| Concept | Java Code | C++ Code | Time |
|---------|-----------|----------|------|
| **Create Node** | `new Node(data)` | `new Node(data)` | O(1) |
| **Insert Start** | `insertAtStart(data)` | `insertAtStart(data)` | O(1) |
| **Insert End** | `insertAtEnd(data)` | `insertAtEnd(data)` | O(n) |
| **Delete Start** | `deleteAtStart()` | `deleteAtStart()` | O(1) |
| **Delete End** | `deleteAtEnd()` | `deleteAtEnd()` | O(n) |
| **Search** | `search(data)` | `search(data)` | O(n) |
| **Get Index** | `get(index)` | `get(index)` | O(n) |
| **Reverse** | `reverse()` | `reverse()` | O(n) |
| **Find Middle** | `findMiddle()` | `findMiddle()` | O(n) |
| **Has Cycle** | `hasCycle()` | `hasCycle()` | O(n) |
| **Merge Sorted** | `merge(list1, list2)` | `merge(list1, list2)` | O(n+m) |

**Key Patterns:**
```java
// 2 Pointer (slow/fast)
Node slow = head, fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}

// Dummy Node
Node dummy = new Node(0);
dummy.next = head;
Node curr = dummy;

// Reverse (3 pointers)
Node prev = null, curr = head, next = null;
while (curr != null) {
    next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
}
```

---

## SECTION 8: KEYWORDS & FUNCTIONS SUMMARY

### 🔑 **ALL KEYWORDS**

| Keyword | Type | Purpose |
|---------|------|---------|
| **Node** | Class/Struct | Basic unit |
| **head** | Variable | First node |
| **tail** | Variable | Last node |
| **next** | Field | Pointer to next |
| **prev** | Field | Pointer to prev (doubly) |
| **null** | Value | End marker |
| **curr** | Variable | Current node |
| **prev** | Variable | Previous node |
| **next** | Variable | Next node |
| **dummy** | Variable | Placeholder |
| **slow** | Variable | 1-step pointer |
| **fast** | Variable | 2-step pointer |

### ⚙️ **ALL FUNCTIONS**

| Function | Purpose | Time |
|----------|---------|------|
| `createNode(data)` | Create new node | O(1) |
| `insertAtStart(data)` | Add at beginning | O(1) |
| `insertAtEnd(data)` | Add at end | O(n) |
| `insertAtPos(pos, data)` | Add at position | O(n) |
| `deleteAtStart()` | Remove first | O(1) |
| `deleteAtEnd()` | Remove last | O(n) |
| `deleteAtPos(pos)` | Remove at position | O(n) |
| `search(data)` | Find element | O(n) |
| `get(index)` | Get by index | O(n) |
| `update(index, data)` | Update by index | O(n) |
| `size()` | Count nodes | O(1) |
| `isEmpty()` | Check empty | O(1) |
| `clear()` | Remove all | O(n) |
| `traverse()` | Print all | O(n) |
| `reverse()` | Reverse list | O(n) |
| `findMiddle()` | Find middle | O(n) |
| `hasCycle()` | Detect cycle | O(n) |
| `mergeSorted(list1, list2)` | Merge sorted lists | O(n+m) |
| `removeNthFromEnd(n)` | Remove Nth from end | O(n) |

---
