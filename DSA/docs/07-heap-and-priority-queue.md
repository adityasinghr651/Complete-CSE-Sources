# 📚 Day 7: Heap & Priority Queue — Complete Mastery Guide (Enhanced Edition for Students)

---

Welcome! 👋 Having completed **Day 1 to 6**, we are now shifting to **Day 7: Heap & Priority Queue**. This guide provides **COMPREHENSIVE NOTES**, **DEFINITIONS**, **ALL ESSENTIAL POINTS for students**, and **100% working direct links** (LeetCode + CodeChef + Codeforces).

This guide includes everything you need:
✅ **Complete Definitions**
✅ **All Notes Making Points**
✅ **Step-by-Step Explanations**
✅ **Visual Diagrams**
✅ **Code Examples**
✅ **Interview Tips**
✅ **Working Links**

---

## SECTION 1: COMPLETE DEFINITIONS & CORE CONCEPTS (For Students)

### What is a Heap? 🤔

#### **Definition (Official):**

> **A Heap** is a **complete binary tree** data structure that satisfies the **heap property**:
> - In a **Max Heap**: Every parent node is **greater than or equal to** its children
> - In a **Min Heap**: Every parent node is **less than or equal to** its children

**Key Properties:**

| Property | Description | Visual |
|----------|-------------|--------|
| **Complete Binary Tree** | All levels full except last, last level fills left-to-right |  |
| **Heap Property** | Parent ≥ Children (Max) or Parent ≤ Children (Min) | See diagram below |
| **Array Implementation** | Can be stored in array without pointers | `arr[i]` children: `arr[2i+1]`, `arr[2i+2]` |

#### **Visual Diagrams:**

**Max Heap (Parent ≥ Children):**
```text
        100           ← Root (max)
       /   \
     70     50       ← Level 1 (both < 100)
    /  \   /
   30  40 20         ← Level 2 (all < parents)

Every parent ≥ its children ✓
```

**Min Heap (Parent ≤ Children):**
```text
        10           ← Root (min)
       /   \
     20     30       ← Level 1 (both > 10)
    /  \   /
   40  50 60         ← Level 2 (all > parents)

Every parent ≤ its children ✓
```

#### **Complete Binary Tree Explained:**

```text
FULL Tree (NOT complete):
        A
       / \
      B   C
     /
    D

❌ NOT complete: Right child of B missing, but D exists

COMPLETE Tree:
        A
       / \
      B   C
     / \
    D   E

✅ Complete: All levels full except last, last fills left-to-right
```

### What is a Priority Queue?

#### **Definition:**

> **A Priority Queue (PQ)** is an **abstract data type** where each element has a **priority**, and elements are served in **order of priority**:
> - **Highest priority** served first (in Max Heap)
> - **Lowest priority** served first (in Min Heap)

**Key Difference:**

| Heap | Priority Queue |
|------|----------------|
| **Concrete implementation** (tree/array) | **Abstract concept** (interface) |
| Data structure | Design pattern |
| Implemented with arrays/trees | Often implemented using Heap |

**Relationship:**
```text
Priority Queue = Interface (what you want)
Heap = Implementation (how you do it)

Java: PriorityQueue class ← uses Heap internally
C++: std::priority_queue ← uses std::vector + Heap
```

---

## SECTION 2: COMPLETE NOTES MAKING POINTS (For Students)

### 📝 **Essential Notes for Exam/Interviews**

#### **Point 1: Heap vs Other Data Structures**

| Feature | Array | Linked List | Stack | Queue | **Heap** |
|---------|-------|-------------|-------|-------|----------|
| Access | O(1) | O(n) | O(1) top | O(1) front | **O(1) min/max** |
| Insert | O(1) end | O(1) | O(1) | O(1) end | **O(log n)** |
| Delete | O(n) | O(n) | O(1) | O(1) front | **O(log n)** |
| Search | O(n) | O(n) | O(n) | O(n) | **O(n)** |
| Sorted? | No | No | No | No | **Partially sorted** |

**When to use Heap:**
- ✅ Need **min/max** frequently → O(1)
- ✅ Need **top-K elements** → Efficient
- ✅ **Priority-based scheduling** → Natural fit
- ❌ Need **full sorting** → Use Array/List + Sort
- ❌ Need **random access** → Use Array

#### **Point 2: Time Complexity Table**

| Operation | Min Heap | Max Heap | Explanation |
|-----------|----------|----------|-------------|
| **Find Min/Max** | O(1) | O(1) | Root is always min/max |
| **Insert** | O(log n) | O(log n) | Add at end, bubble up |
| **Delete Min/Max** | O(log n) | O(log n) | Remove root, bubble down |
| **Delete Any** | O(log n) | O(log n) | Find, replace, bubble |
| **Build Heap** | O(n) | O(n) | Heapify from array |
| **Peek** | O(1) | O(1) | View root without removing |

**Why O(log n) for Insert/Delete?**

```text
Heap height = log₂(n)  (complete binary tree)

Insert:
1. Add at end (bottom) → O(1)
2. Bubble up to correct position → max height steps = log n
Total: O(log n)

Delete:
1. Remove root → O(1)
2. Replace with bottom element → O(1)
3. Bubble down to correct position → max height steps = log n
Total: O(log n)
```

#### **Point 3: Array Implementation (NO POINTERS!)**

**Key Concept:** Heap can be stored in **array** without tree pointers!

```text
Tree:
        0 (100)
       /   \
    1(70)  2(50)
    /  \   /
 3(30) 4(40) 5(20)

Array: [100, 70, 50, 30, 40, 20]
       Index: 0  1   2   3   4   5

Formulas:
- Parent of i: (i - 1) / 2
- Left child of i: 2*i + 1
- Right child of i: 2*i + 2
```

**Code Example (Java):**
```java
class MinHeap {
    int[] arr;
    int size;
    
    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }
    
    void swap(int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

#### **Point 4: Heap Operations Step-by-Step**

**INSERT Operation (Bubble Up):**

```text
Step 1: Add at end
[10, 20, 30] → Add 5 → [10, 20, 30, 5]

Step 2: Bubble up
Index 3 (value 5):
  Parent = (3-1)/2 = 1 (value 20)
  5 < 20 → Swap!
[10, 5, 30, 20]

  Index 1 (value 5):
  Parent = (1-1)/2 = 0 (value 10)
  5 < 10 → Swap!
[5, 10, 30, 20] ✓ Min Heap!

Time: O(log n)
```

**DELETE Min Operation (Bubble Down):**

```text
Step 1: Remove root, replace with last
[5, 10, 30, 20] → Remove 5 → [20, 10, 30]

Step 2: Bubble down
Index 0 (value 20):
  Left = 1 (value 10), Right = 2 (value 30)
  Smallest child = 10
  20 > 10 → Swap!
[10, 20, 30] ✓ Min Heap!

Time: O(log n)
```

#### **Point 5: BUILD HEAP (Heapify) - O(n) NOT O(n log n)!**

**Common Mistake:** Many think build heap is O(n log n)

**Reality:** Build heap is **O(n)**!

```text
Array: [4, 10, 3, 5, 1]

Heapify from bottom (index n/2 - 1 to 0):

i = 1 (value 10):
  Children: 3 (5), 4 (1)
  Smallest = 1
  10 > 1 → Swap!
[4, 1, 3, 5, 10]

i = 0 (value 4):
  Children: 1 (1), 2 (3)
  Smallest = 1
  4 > 1 → Swap!
[1, 4, 3, 5, 10]
  
  Index 1 (value 4):
  Children: 3 (5), 4 (10)
  4 < 5, 4 < 10 → Done!

[1, 4, 3, 5, 10] ✓ Min Heap!

Time: O(n) (Math proof: most nodes at bottom, O(1) each)
```

#### **Point 6: HEAP SORT**

**Algorithm:**
```text
1. Build Max Heap from array → O(n)
2. Repeat n times:
   a. Swap root (max) with last → O(1)
   b. Reduce heap size by 1 → O(1)
   c. Bubble down new root → O(log n)

Total: O(n) + n × O(log n) = O(n log n)
```

**Code:**
```java
void heapSort(int[] arr) {
    int n = arr.length;
    
    // Build Max Heap
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);
    
    // Extract elements
    for (int i = n - 1; i > 0; i--) {
        swap(arr[0], arr[i]);  // Move max to end
        heapify(arr, i, 0);    // Heapify reduced heap
    }
}

void heapify(int[] arr, int n, int i) {
    int largest = i;
    int left = 2*i + 1;
    int right = 2*i + 2;
    
    if (left < n && arr[left] > arr[largest])
        largest = left;
    if (right < n && arr[right] > arr[largest])
        largest = right;
    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapify(arr, n, largest);
    }
}
```

**Time:** O(n log n) | **Space:** O(1) | **Stable:** No

#### **Point 7: PRIORITY QUEUE OPERATIONS (Java/C++)**

**Java:**
```java
import java.util.PriorityQueue;

// Min Queue (default)
PriorityQueue<Integer> minPQ = new PriorityQueue<>();
minPQ.add(5);        // O(log n)
minPQ.peek();        // O(1) → returns 5
minPQ.poll();        // O(log n) → removes 5

// Max Queue
PriorityQueue<Integer> maxPQ = new PriorityQueue<>((a, b) -> b - a);
maxPQ.add(5);        // O(log n)
maxPQ.peek();        // O(1) → returns 5
```

**C++:**
```cpp
#include <queue>

// Max Queue (default)
priority_queue<int> maxPQ;
maxPQ.push(5);       // O(log n)
maxPQ.top();         // O(1) → returns 5
maxPQ.pop();         // O(log n)

// Min Queue
priority_queue<int, vector<int>, greater<int>> minPQ;
minPQ.push(5);       // O(log n)
minPQ.top();         // O(1) → returns 5
```

#### **Point 8: WHEN TO USE HEAP (Interview Decision Tree)**

```text
QUESTION STARTS
    ↓
Need min/max frequently?
    ↓ YES → Use Heap ✅
    ↓ NO
Need top-K elements?
    ↓ YES → Use Heap ✅
    ↓ NO
Priority-based scheduling?
    ↓ YES → Use Heap ✅
    ↓ NO
Need full sorting?
    ↓ YES → Use Array + Sort ❌
    ↓ NO
Need random access?
    ↓ YES → Use Array ❌
    ↓ NO
→ Consider other DS (Tree, Graph, etc)
```

---

## SECTION 3: 5 MAIN HEAP PATTERNS (For Students)

### Pattern 1: Kth Largest/Smallest Element

**When to use:**
- Find Kth largest/smallest in array
- Streaming data (Kth largest at any time)

**Template:**
```java
public int kthLargest(int[] nums, int k) {
    // Min Heap of size k
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    
    for (int num : nums) {
        pq.offer(num);
        if (pq.size() > k) {
            pq.poll();  // Remove smallest
        }
    }
    
    return pq.peek();  // Kth largest (smallest in min heap of size k)
}
```

**Time:** O(n log k) | **Space:** O(k)

### Pattern 2: Top K Frequent Elements

**When to use:**
- Find most frequent K elements
- Word frequency analysis

**Template:**
```java
public List<String> topKFrequent(String[] words, int k) {
    // Count frequency
    Map<String, Integer> freq = new HashMap<>();
    for (String word : words)
        freq.put(word, freq.getOrDefault(word, 0) + 1);
    
    // Max Heap by frequency
    PriorityQueue<String> pq = new PriorityQueue<>(
        (a, b) -> freq.get(b) - freq.get(a)
    );
    pq.addAll(freq.keySet());
    
    // Get top k
    List<String> result = new ArrayList<>();
    for (int i = 0; i < k && !pq.isEmpty(); i++)
        result.add(pq.poll());
    
    return result;
}
```

**Time:** O(n log k) | **Space:** O(n)

### Pattern 3: Merge K Sorted Lists

**When to use:**
- Merge k sorted arrays
- Find kth smallest in merged list

**Template:**
```java
public int[] mergeKSortedLists(int[][] lists) {
    // Min Heap of (value, listIndex, elementIndex)
    PriorityQueue<int[]> pq = new PriorityQueue<>(
        (a, b) -> a[0] - b[0]
    );
    
    // Add first element from each list
    for (int i = 0; i < lists.length; i++)
        pq.offer(new int[]{lists[i][0], i, 0});
    
    // Merge
    List<Integer> result = new ArrayList<>();
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        result.add(curr[0]);
        
        int listIdx = curr[1], elemIdx = curr[2];
        if (elemIdx + 1 < lists[listIdx].length)
            pq.offer(new int[]{lists[listIdx][elemIdx + 1], listIdx, elemIdx + 1});
    }
    
    return result.stream().mapToInt(i -> i).toArray();
}
```

**Time:** O(n log k) | **Space:** O(k)

### Pattern 4: Meeting Rooms II (Scheduling)

**When to use:**
- Minimum rooms for meetings
- Resource allocation

**Template:**
```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals.length == 0) return 0;
    
    // Sort by start time
    Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
    
    // Min Heap of end times
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    pq.offer(intervals[0][1]);
    
    for (int i = 1; i < intervals.length; i++) {
        if (intervals[i][0] >= pq.peek()) {
            pq.poll();  // Room free, reuse
        }
        pq.offer(intervals[i][1]);  // Add new meeting
    }
    
    return pq.size();  // Rooms needed
}
```

**Time:** O(n log n) | **Space:** O(n)

### Pattern 5: Sliding Window Median

**When to use:**
- Median of sliding window
- Streaming median

**Template (Two Heaps):**
```java
public double[] slidingWindowMedian(int[] nums, int k) {
    // Max Heap (smaller half) + Min Heap (larger half)
    PriorityQueue<Integer> small = new PriorityQueue<>((a, b) -> b - a);
    PriorityQueue<Integer> large = new PriorityQueue<>();
    
    // ... (balance heaps, extract median)
    return new double[0];
}
```

**Time:** O(n log k) | **Space:** O(k)

---

## SECTION 4: PROBLEM PROGRESSION — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Last Stone Weight**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/last-stone-weight/
- **Company Tags**: Google, Amazon
- **Frequency**: 7/10

**Problem**: Smash stones, get minimum remaining weight

**Solution (Max Heap):**
```java
public int lastStoneWeight(int[] stones) {
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
    
    for (int stone : stones) pq.offer(stone);
    
    while (pq.size() > 1) {
        int y = pq.poll();  // Largest
        int x = pq.poll();  // Second largest
        if (y > x) pq.offer(y - x);
    }
    
    return pq.isEmpty() ? 0 : pq.peek();
}
```

**Time:** O(n log n) | **Space:** O(n)

---

#### Level 2: Easy

#### **2. Kth Largest Element in a Stream**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/kth-largest-element-in-a-stream/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 8/10

**Solution (Min Heap of size K):**
```java
class KthLargest {
    PriorityQueue<Integer> pq;
    int k;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        pq = new PriorityQueue<>();
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) pq.poll();
        }
    }
    
    public int add(int val) {
        pq.offer(val);
        if (pq.size() > k) pq.poll();
        return pq.peek();
    }
}
```

**Time:** O(log k) per add | **Space:** O(k)

---

#### **3. K Weakest Rows in a Matrix**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 6/10

**Solution:**
```java
public int[] kWeakestRows(int[][] matrix, int k) {
    PriorityQueue<int[]> pq = new PriorityQueue<>(
        (a, b) -> a[1] == b[1] ? a[0] - b[0] : a[1] - b[1]
    );
    
    for (int i = 0; i < matrix.length; i++) {
        int strength = countOnes(matrix[i]);
        pq.offer(new int[]{i, strength});
    }
    
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
        result[i] = pq.poll()[0];
    
    return result;
}

private int countOnes(int[] row) {
    int count = 0;
    for (int val : row) {
        if (val == 1) count++;
        else break;
    }
    return count;
}
```

---

#### Level 3: Medium

#### **4. Kth Largest Element in an Array**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/kth-largest-element-in-an-array/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Min Heap):**
```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    
    for (int num : nums) {
        pq.offer(num);
        if (pq.size() > k) pq.poll();
    }
    
    return pq.peek();
}
```

**Alternative (QuickSelect - O(n)):**
```java
public int findKthLargest(int[] nums, int k) {
    return quickSelect(nums, 0, nums.length - 1, nums.length - k);
}

private int quickSelect(int[] nums, int left, int right, int target) {
    if (left == right) return nums[left];
    
    int pivotIdx = partition(nums, left, right);
    if (pivotIdx == target) return nums[pivotIdx];
    else if (pivotIdx < target) return quickSelect(nums, pivotIdx + 1, right, target);
    else return quickSelect(nums, left, pivotIdx - 1, target);
}

private int partition(int[] nums, int left, int right) {
    int pivot = nums[right], i = left;
    for (int j = left; j < right; j++) {
        if (nums[j] < pivot) {
            int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
            i++;
        }
    }
    int temp = nums[i]; nums[i] = nums[right]; nums[right] = temp;
    return i;
}
```

**Time:** O(n log k) heap, O(n) QuickSelect | **Space:** O(k) / O(1)

---

#### **5. Top K Frequent Elements**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/top-k-frequent-elements/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution:**
```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (int num : nums)
        freq.put(num, freq.getOrDefault(num, 0) + 1);
    
    PriorityQueue<Integer> pq = new PriorityQueue<>(
        (a, b) -> freq.get(b) - freq.get(a)
    );
    pq.addAll(freq.keySet());
    
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
        result[i] = pq.poll();
    
    return result;
}
```

**Time:** O(n log k) | **Space:** O(n)

---

#### **6. K Closest Points to Origin**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/k-closest-points-to-origin/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 9/10

**Solution:**
```java
public int[][] kClosest(int[][] points, int k) {
    PriorityQueue<int[]> pq = new PriorityQueue<>(
        (a, b) -> dist(b) - dist(a)  // Max Heap by distance
    );
    
    for (int[] point : points) {
        pq.offer(point);
        if (pq.size() > k) pq.poll();
    }
    
    int[][] result = new int[k][2];
    for (int i = 0; i < k; i++)
        result[i] = pq.poll();
    
    return result;
}

private int dist(int[] p) { return p[0]*p[0] + p[1]*p[1]; }
```

**Time:** O(n log k) | **Space:** O(k)

---

#### **7. Meeting Rooms II**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/meeting-rooms-ii/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Min Heap of end times):**
```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals.length == 0) return 0;
    
    Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    pq.offer(intervals[0][1]);
    
    for (int i = 1; i < intervals.length; i++) {
        if (intervals[i][0] >= pq.peek()) pq.poll();
        pq.offer(intervals[i][1]);
    }
    
    return pq.size();
}
```

**Time:** O(n log n) | **Space:** O(n)

---

#### Level 4: Hard

#### **8. Find Median from Data Stream**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/find-median-from-data-stream/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Two Heaps):**
```java
class MedianFinder {
    PriorityQueue<Integer> small;  // Max Heap
    PriorityQueue<Integer> large;  // Min Heap
    
    public MedianFinder() {
        small = new PriorityQueue<>((a, b) -> b - a);
        large = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        small.offer(num);
        large.offer(small.poll());
        
        if (large.size() > small.size()) small.offer(large.poll());
    }
    
    public double findMedian() {
        if (small.size() == large.size())
            return (small.peek() + large.peek()) / 2.0;
        else
            return small.peek();
    }
}
```

**Time:** O(log n) per add | **Space:** O(n)

---

#### **9. Sliding Window Maximum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/sliding-window-maximum/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Max Heap):**
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[0] - a[0]);
    
    for (int i = 0; i < n; i++) {
        pq.offer(new int[]{nums[i], i});
        
        if (i >= k - 1) {
            while (pq.peek()[1] < i - k + 1) pq.poll();
            result[i - k + 1] = pq.peek()[0];
        }
    }
    
    return result;
}
```

**Alternative (Monotonic Queue - O(n)):**
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new LinkedList<>();
    
    for (int i = 0; i < n; i++) {
        while (!deque.isEmpty() && deque.peekFirst() < i - k + 1)
            deque.pollFirst();
        while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i])
            deque.pollLast();
        deque.offerLast(i);
        
        if (i >= k - 1) result[i - k + 1] = nums[deque.peekFirst()];
    }
    
    return result;
}
```

**Time:** O(n log k) heap, O(n) queue | **Space:** O(k)

---

### CodeChef Problems

#### **10. Heap Sort**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/learn/course/stacks-and-queues (search "Heap Sort")
- **Rating**: 1000

**Solution:**
See Heap Sort code in Pattern 6 above.

---

### Codeforces Problems

#### **11. Code Monk Heaps**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/blog/entry/50314
- **Rating**: 1000-1400
- **Tags**: Heap, Priority Queue

5 problems with partial solutions.

---

## SECTION 5: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Easy (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Last Stone Weight | https://leetcode.com/problems/last-stone-weight/ |
| 2 | Kth Largest Element in Stream | https://leetcode.com/problems/kth-largest-element-in-a-stream/ |
| 3 | K Weakest Rows in Matrix | https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/ |
| 4 | Smallest Number in Infinite Set | https://leetcode.com/problems/smallest-number-in-infinite-set/ |
| 5 | Kth Smallest Number in Multiplication Table | https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/ |

### Medium (10 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Kth Largest Element in Array | https://leetcode.com/problems/kth-largest-element-in-an-array/ |
| 2 | Top K Frequent Elements | https://leetcode.com/problems/top-k-frequent-elements/ |
| 3 | K Closest Points to Origin | https://leetcode.com/problems/k-closest-points-to-origin/ |
| 4 | Meeting Rooms II | https://leetcode.com/problems/meeting-rooms-ii/ |
| 5 | Kth Smallest Element in Sorted Matrix | https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/ |
| 6 | Maximum Subsequence Score | https://leetcode.com/problems/maximum-subsequence-score/ |
| 7 | Total Cost to Hire K Workers | https://leetcode.com/problems/total-cost-to-hire-k-workers/ |
| 8 | Furthest Building You Can Reach | https://leetcode.com/problems/farthest-building-you-can-reach/ |
| 9 | Minimum Cost to Connect Sticks | https://leetcode.com/problems/minimum-cost-to-connect-sticks/ |
| 10 | Find K Closest Elements | https://leetcode.com/problems/find-k-closest-elements/ |

### Hard (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Find Median from Data Stream | https://leetcode.com/problems/find-median-from-data-stream/ |
| 2 | Sliding Window Maximum | https://leetcode.com/problems/sliding-window-maximum/ |
| 3 | Merge K Sorted Lists | https://leetcode.com/problems/merge-k-sorted-lists/ |
| 4 | Reorganize String | https://leetcode.com/problems/reorganize-string/ |
| 5 | Task Scheduler | https://leetcode.com/problems/task-scheduler/ |

---

## SECTION 6: Active Learning — 5 Questions

1. **Last Stone Weight** — LeetCode 1046  
   [Link](https://leetcode.com/problems/last-stone-weight/)

2. **Kth Largest in Stream** — LeetCode 703  
   [Link](https://leetcode.com/problems/kth-largest-element-in-a-stream/)

3. **Kth Largest in Array** — LeetCode 215  
   [Link](https://leetcode.com/problems/kth-largest-element-in-an-array/)

4. **Top K Frequent** — LeetCode 347  
   [Link](https://leetcode.com/problems/top-k-frequent-elements/)

5. **Find Median** — LeetCode 295  
   [Link](https://leetcode.com/problems/find-median-from-data-stream/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 7: Revision Notes — CHEAT SHEET

### 📝 **Heap & Priority Queue Cheat Sheet**

| Concept | Formula/Code | Time |
|---------|--------------|------|
| **Parent** | `(i - 1) / 2` | O(1) |
| **Left Child** | `2*i + 1` | O(1) |
| **Right Child** | `2*i + 2` | O(1) |
| **Insert** | `pq.offer(x)` | O(log n) |
| **Delete** | `pq.poll()` | O(log n) |
| **Peek** | `pq.peek()` | O(1) |
| **Build Heap** | Heapify from n/2-1 | O(n) |
| **Heap Sort** | Build + Extract n times | O(n log n) |

**Java:**
```java
PriorityQueue<Integer> minPQ = new PriorityQueue<>();
PriorityQueue<Integer> maxPQ = new PriorityQueue<>((a, b) -> b - a);
```

**C++:**
```cpp
priority_queue<int> maxPQ;
priority_queue<int, vector<int>, greater<int>> minPQ;
```

---
