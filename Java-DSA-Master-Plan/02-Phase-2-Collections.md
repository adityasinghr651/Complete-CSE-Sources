# PHASE 2 — Collections Toolkit + DSA Implementation Patterns
### (Parts 2 + 3 + 4 fused: HashMap, HashSet, ArrayList, ArrayDeque, PriorityQueue)

This is the part that actually fixes the bottleneck: *"I know I need to track something, but I don't know which Java tool gets me there."*

Every structure below comes with the **Decision Rule** for *when* you reach for it in an interview.

---

## 1. ArrayList (Dynamic Array)

**The Concept:** A resizable array. Use this when you need index-based access but don't know the final size of the array ahead of time.

**Basic Syntax:**
```java
List<Integer> list = new ArrayList<>();
list.add(10);          // Append to end: O(1) amortized
list.set(0, 99);       // Update index 0 to 99: O(1)
int val = list.get(0); // Random access: O(1)
int len = list.size(); // Length (not .length!)
```

**DSA Application (Returning a List of answers):**
Often, LeetCode asks you to return a `List<Integer>`. You build it using an ArrayList.
```java
public List<Integer> findEvens(int[] nums) {
    List<Integer> evens = new ArrayList<>();
    for (int num : nums) {
        if (num % 2 == 0) {
            evens.add(num);
        }
    }
    return evens;
}
```

> [!WARNING]
> **Common Pitfall**: `list.remove(2)` on a `List<Integer>` removes the element **at index 2**, not the value `2`. To remove the value, you must box it: `list.remove(Integer.valueOf(2))`.

**Decision Rule:** Need index-based random access and mostly append at the end? → `ArrayList`.

---

## 2. HashMap (Key → Value Map)

**The Concept:** Stores key-value pairs. Offers O(1) average time complexity for lookups, insertions, and deletions.

**Basic Syntax:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("Alice", 25);
map.put("Bob", 30);

int age = map.get("Alice");           // 25
boolean hasBob = map.containsKey("Bob"); // true
map.remove("Alice");
```

**DSA Application 1: Frequency Counting Pattern**
You will use this pattern constantly. The `getOrDefault` method prevents NullPointerExceptions if the key doesn't exist yet.
```java
int[] nums = {1, 1, 2, 3, 1, 2};
Map<Integer, Integer> freq = new HashMap<>();

for (int num : nums) {
    // If num exists, get its count. Otherwise, default to 0. Then add 1.
    freq.put(num, freq.getOrDefault(num, 0) + 1);
}
```

**DSA Application 2: The "Two Sum" Tracking Pattern**
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>(); // val -> index
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (seen.containsKey(complement)) {
            return new int[] { seen.get(complement), i };
        }
        seen.put(nums[i], i);
    }
    return new int[] {};
}
```

**Decision Rule:** "I need to count occurrences" OR "I need O(1) lookup by a specific key" → `HashMap`.

---

## 3. HashSet (Unique Elements)

**The Concept:** A collection that contains no duplicates. Offers O(1) average time complexity for lookups and insertions.

**Basic Syntax:**
```java
Set<Integer> set = new HashSet<>();
set.add(5);
boolean exists = set.contains(5); // true
set.remove(5);
```

**DSA Application: Duplicate Detection Pattern**
The `.add()` method returns `false` if the element was *already* in the set! This is a powerful trick.
```java
public boolean containsDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();
    for (int num : nums) {
        if (!seen.add(num)) {
            return true; // add() failed, it was a duplicate!
        }
    }
    return false;
}
```

**Decision Rule:** "I need to check for existence/duplicates, and order doesn't matter" → `HashSet`.

---

## 4. ArrayDeque (Stack & Queue)

**The Concept:** A double-ended queue. **Do not use the legacy `Stack` class in Java.** Use `ArrayDeque` for both Stack (LIFO) and Queue (FIFO) behavior.

### As a Stack (LIFO)
**Basic Syntax:**
```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10); // push to top
stack.push(20);
int top = stack.pop(); // 20 (removes it)
int peek = stack.peek(); // 10 (looks at it)
```

**DSA Application (Valid Parentheses):**
```java
public boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(') stack.push(')');
        else if (c == '{') stack.push('}');
        else if (c == '[') stack.push(']');
        else if (stack.isEmpty() || stack.pop() != c) return false;
    }
    return stack.isEmpty();
}
```

### As a Queue (FIFO)
**Basic Syntax:**
```java
Deque<Integer> queue = new ArrayDeque<>();
queue.offer(10); // add to back (enqueue)
queue.offer(20);
int front = queue.poll(); // 10 (remove from front / dequeue)
```

**Decision Rule:** "I need LIFO (Last In First Out)" OR "I need FIFO (First In First Out)" → `ArrayDeque`.

---

## 5. PriorityQueue (Min/Max Heap)

**The Concept:** Always gives you the minimum (or maximum) element in O(1) time. Insertions take O(log N).

**Basic Syntax:**
```java
// Default is a Min-Heap (smallest element at the top)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// Max-Heap (largest element at the top)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

minHeap.offer(10);
minHeap.offer(5);
int smallest = minHeap.poll(); // 5
```

**DSA Application: Top K Elements Pattern**
How to find the Kth largest element in an array? Keep a Min-Heap of size K.
```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    
    for (int num : nums) {
        minHeap.offer(num);
        if (minHeap.size() > k) {
            minHeap.poll(); // Evict the smallest element seen so far
        }
    }
    
    // The top of the heap is the Kth largest overall!
    return minHeap.peek(); 
}
```

**Decision Rule:** "I need the min or max element repeatedly while more elements keep coming in" → `PriorityQueue`.

---

## 🚀 Next Steps
Can you open a blank editor and write the **Two Sum** (HashMap), **Contains Duplicate** (HashSet), and **Valid Parentheses** (ArrayDeque Stack) solutions from memory? Try it!
