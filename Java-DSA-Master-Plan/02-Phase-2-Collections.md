# PHASE 2 — Collections Toolkit + DSA Implementation Patterns
### (Parts 2 + 3 + 4 fused: HashMap, HashSet, ArrayList, ArrayDeque, PriorityQueue, TreeMap/TreeSet — each tied to what it's for)

This is the part that actually fixes your stated bottleneck: "I know I need to track something, but I don't know which Java tool gets me there." Every structure below comes with the decision rule for *when* you reach for it.

---

## 1. The mental map

```text
Collection
│
├── List
│   ├── ArrayList   — dynamic array, index access
│   └── LinkedList  — rarely needed for DSA interviews, skip unless asked
│
├── Set
│   ├── HashSet     — existence check, no order
│   └── TreeSet     — existence check, sorted order
│
├── Queue
│   ├── ArrayDeque      — stack AND queue (use this, not java.util.Stack)
│   └── PriorityQueue   — always gives you min (or max) element
│
└── Map
    ├── HashMap   — key→value, O(1) avg, no order
    └── TreeMap   — key→value, sorted by key, O(log n)
```

---

## 2. ArrayList — dynamic array

| Method | Returns | Modifies? | Use |
|---|---|---|---|
| `add(x)` | boolean | yes | append |
| `add(i, x)` | void | yes | insert at index — O(n) shift |
| `get(i)` | T | no | O(1) random access |
| `set(i, x)` | T (old val) | yes | overwrite at index |
| `remove(i)` | T (removed val) | yes | **index-based** remove — O(n) shift |
| `remove(Object o)` | boolean | yes | **value-based** remove — trap below |
| `size()` | int | no | loop bound |
| `contains(x)` | boolean | no | O(n) linear scan |
| `isEmpty()` | boolean | no | — |

**Classic trap**: `list.remove(2)` on a `List<Integer>` removes the element **at index 2**, not the value `2`. To remove the value, box it: `list.remove(Integer.valueOf(2))`.

**Decision rule**: need index-based random access and mostly append at the end? → ArrayList. Need frequent insert/delete in the middle? Usually still fine with ArrayList for interview-sized inputs — LinkedList's theoretical advantage rarely matters here and its overhead often makes it slower in practice.

---

## 3. HashMap — key → value, O(1) average

| Method | Returns | Modifies? | Use |
|---|---|---|---|
| `put(k, v)` | old value or null | yes | insert/update |
| `get(k)` | value or null | no | lookup — **NPE risk if you auto-unbox a null** |
| `getOrDefault(k, def)` | value or def | no | lookup with fallback — avoids null checks |
| `containsKey(k)` | boolean | no | existence check |
| `containsValue(v)` | boolean | no | O(n) — rarely what you want |
| `remove(k)` | old value or null | yes | delete |
| `putIfAbsent(k, v)` | old value or null | yes | insert only if key missing |
| `keySet()` | Set\<K\> (view) | no | iterate keys |
| `values()` | Collection\<V\> (view) | no | iterate values |
| `entrySet()` | Set\<Map.Entry\<K,V\>\> | no | iterate key+value together — preferred over `keySet()` + `get()` in a loop |
| `merge(k, v, fn)` | new value | yes | combine on collision — see below |

**Frequency counting — the pattern you'll use constantly:**
```java
Map<Integer, Integer> freq = new HashMap<>();
for (int x : nums) freq.put(x, freq.getOrDefault(x, 0) + 1);
```
Same thing with `merge`:
```java
freq.merge(x, 1, Integer::sum);
```

**Iterating key+value together (preferred style):**
```java
for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
    int key = e.getKey();
    int val = e.getValue();
}
```

**Common mistake**: calling `.get(k)` on a missing key returns `null`, and if you assign that to a primitive `int`, you get a `NullPointerException` on auto-unboxing. Use `getOrDefault` instead.

**Decision rule**: "I need to count occurrences / group things by a key / check O(1) avg lookup by key" → HashMap.

---

## 4. HashSet — existence check, no duplicates, no order

| Method | Returns | Modifies? | Use |
|---|---|---|---|
| `add(x)` | boolean (false if already present) | yes | insert — the return value itself is useful for "have I seen this before" |
| `contains(x)` | boolean | no | O(1) avg existence check |
| `remove(x)` | boolean | yes | delete |
| `size()` | int | no | — |

**Visited-tracking pattern (graphs, BFS/DFS):**
```java
Set<Integer> visited = new HashSet<>();
if (!visited.contains(node)) {
    visited.add(node);
    // process
}
```

**Duplicate-detection pattern using add()'s return value:**
```java
Set<Integer> seen = new HashSet<>();
for (int x : nums) {
    if (!seen.add(x)) return true;  // add() returns false if x was already there
}
return false;
```

**Decision rule**: "I need existence/duplicate check, order doesn't matter" → HashSet.

---

## 5. TreeMap / TreeSet — sorted versions

Same method sets as HashMap/HashSet (`put/get/add/contains/remove`), but backed by a red-black tree → O(log n) per operation instead of O(1) average, and iteration happens in **sorted key/element order**.

Extra useful methods on both:
| Method | Returns | Use |
|---|---|---|
| `firstKey()` / `lastKey()` (TreeMap) | K | smallest/largest key |
| `first()` / `last()` (TreeSet) | E | smallest/largest element |
| `floorKey(k)` / `ceilingKey(k)` | K or null | largest key ≤ k / smallest key ≥ k |
| `lowerKey(k)` / `higherKey(k)` | K or null | strictly less/greater |
| `floor(x)` / `ceiling(x)` (TreeSet) | E or null | same idea, no key/value |

**Decision rule**: "I need sorted keys/elements, or the closest value to X, or repeatedly need min/max but also want ordered iteration" → TreeMap/TreeSet. If you don't need sorted order, HashMap/HashSet is faster — don't reach for Tree* by default.

---

## 6. ArrayDeque — stack AND queue in one class

**Use `ArrayDeque` for both stack and queue behavior. Do not use the legacy `java.util.Stack` class** — it's synchronized (slower) and considered a design mistake in the JDK itself.

| Method | Behaves as | Returns | Use |
|---|---|---|---|
| `push(x)` | stack push | void | add to front |
| `pop()` | stack pop | E, throws if empty | remove from front |
| `peek()` | stack/queue peek | E or null | look without removing |
| `offer(x)` / `offerLast(x)` | queue enqueue | boolean | add to back |
| `poll()` / `pollFirst()` | queue dequeue | E or null | remove from front |
| `addFirst(x)` / `addLast(x)` | deque insert | void | explicit both-ends insert |
| `pollFirst()` / `pollLast()` | deque remove | E or null | explicit both-ends remove, **null-safe** (no exception on empty) |
| `isEmpty()` | — | boolean | loop condition |

**As a stack (LIFO)** — valid parentheses, monotonic stack:
```java
Deque<Character> stack = new ArrayDeque<>();
stack.push(c);
char top = stack.pop();
```

**As a queue (FIFO)** — BFS:
```java
Deque<Integer> queue = new ArrayDeque<>();
queue.offer(start);
while (!queue.isEmpty()) {
    int node = queue.poll();
    // process, then queue.offer(neighbor)
}
```

**Monotonic stack pattern** (next greater element, etc.) — keep the stack's values in increasing/decreasing order, popping while the new element breaks the invariant:
```java
Deque<Integer> stack = new ArrayDeque<>(); // stores indices
for (int i = 0; i < nums.length; i++) {
    while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
        int idx = stack.pop();
        result[idx] = nums[i]; // nums[i] is the "next greater" for nums[idx]
    }
    stack.push(i);
}
```

**Decision rule**: "I need LIFO" or "I need FIFO" → ArrayDeque either way, just use the matching method names.

---

## 7. PriorityQueue — always gives you the min (or max)

Backed by a binary heap. Default is a **min-heap** (smallest element at the head).

| Method | Returns | Modifies? | Use |
|---|---|---|---|
| `offer(x)` / `add(x)` | boolean | yes | insert — O(log n) |
| `poll()` | smallest (or null if empty) | yes | remove+return the min — O(log n) |
| `peek()` | smallest (or null) | no | look without removing — O(1) |
| `remove(x)` | boolean | yes | remove a specific value — O(n), rarely needed |
| `size()` | int | no | — |

**Min-heap (default):**
```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

**Max-heap (reverse comparator):**
```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
```

**Custom objects (e.g., pairs, or `int[]` for graph edges) need a Comparator:**
```java
// Dijkstra-style: int[]{node, distance}, ordered by distance
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
```

**Top-K pattern** (kth largest — keep a min-heap of size K):
```java
PriorityQueue<Integer> heap = new PriorityQueue<>();
for (int x : nums) {
    heap.offer(x);
    if (heap.size() > k) heap.poll(); // evict the smallest, keeping the k largest
}
return heap.peek(); // kth largest
```

**Common mistake**: forgetting the default is a min-heap when the problem wants the max — you'll get wrong answers silently (no crash, just wrong output) if you don't pass a comparator.

**Decision rule**: "I need the min or max element repeatedly, while more elements keep coming in" → PriorityQueue.

---

## 8. Comparator — for TreeMap/TreeSet ordering and PriorityQueue/sort ordering

```java
// Lambda form (preferred in interviews):
Comparator<int[]> byDistance = (a, b) -> a[1] - b[1];

// Sort a List of custom pairs by second value descending:
list.sort((a, b) -> b[1] - a[1]);

// Multi-key sort: by first value asc, then second value desc:
list.sort((a, b) -> a[0] != b[0] ? a[0] - b[0] : b[1] - a[1]);
```
**Mistake**: `a - b` on `int[]` elements overflows if values are near `Integer.MIN_VALUE`/`MAX_VALUE` — use `Integer.compare(a, b)` to be safe in general, though `a-b` is fine for typical interview-sized inputs.

---

## 9. Updated "I need to..." → tool table

| I need to... | Use |
|---|---|
| Count occurrences | HashMap |
| Check existence / duplicates, no order | HashSet |
| Sorted keys, or closest-value queries | TreeMap / TreeSet |
| Min or max repeatedly, elements keep arriving | PriorityQueue |
| LIFO | ArrayDeque (`push`/`pop`) |
| FIFO (BFS) | ArrayDeque (`offer`/`poll`) |
| Custom sort order | Comparator lambda |
| Index-based random access, append at end | ArrayList |
| Iterate a map's keys and values together | `entrySet()` |
