# 📚 Day 6: Stack & Queue — Complete Mastery Guide (Enhanced Edition)

## SECTION 1: Intuition Building — Animation Style

### What are Stack & Queue? 🤔

#### Stack — LIFO (Last-In-First-Out)

```text
Stack = A stack of plates (like in a restaurant)

❌ Queue: Manually access each plate (O(n))
✅ Stack: Access only the top plate (O(1))

Stack Operations:
- PUSH: Add a plate to the top
- POP: Remove the top plate
- TOP: View the top plate (without removing)

Example:
[Push 1] → [1]
[Push 2] → [1, 2]
[Push 3] → [1, 2, 3]
[Pop]    → [1, 2]  (3 removed)
[Pop]    → [1]     (2 removed)
[Top]    → 1       (1 visible)
```

**Key:** **Last element is removed FIRST** → **LIFO**

#### Queue — FIFO (First-In-First-Out)

```text
Queue = A cinema ticket line

❌ Stack: The last person gets the ticket first (nonsense!)
✅ Queue: The first person gets the ticket first (correct!)

Queue Operations:
- ENQUEUE: Add a person to the end
- DEQUEUE: Remove a person from the front
- FRONT: View the front person (without removing)

Example:
[Enqueue A] → [A]
[Enqueue B] → [A, B]
[Enqueue C] → [A, B, C]
[Dequeue]   → [B, C]  (A removed)
[Dequeue]   → [C]     (B removed)
[Front]     → C       (C visible)
```

**Key:** **First element is removed FIRST** → **FIFO**

### What Problems Do They Solve?

| Problem Type | Stack | Queue | Why? |
|--------------|-------|-------|------|
| Parenthesis matching | ✅ | ❌ | LIFO helps find the closest match |
| Expression evaluation | ✅ | ❌ | Postfix/Prefix evaluation with stack |
| Undo/Redo | ✅ | ❌ | Last action is undone first |
| Browser history | ✅ | ❌ | Back = pop from stack |
| BFS traversal | ❌ | ✅ | FIFO allows level-by-level traversal |
| Task scheduling | ❌ | ✅ | Fair scheduling (FIFO) |
| Sliding window max | ❌ | ✅ | Monotonic queue |
| Depth-first search | ✅ | ❌ | Implicit stack recursion |

### Real-World Analogies 🏠

#### Analogy 1: Stack — Reversible Pen (Undo Feature)

```text
Text Editor:
1. Type "Hello" → Stack: ["Hello"]
2. Type " World" → Stack: ["Hello", "Hello World"]
3. Type "!" → Stack: ["Hello", "Hello World", "Hello World!"]

Click UNDO:
→ Stack.pop() → "Hello World" ✓
→ Stack.pop() → "Hello" ✓

With LIFO, the LAST action is undone first! ✅
```

#### Analogy 2: Stack — Browser Back Button

```text
Visited Pages:
Google → YouTube → Wikipedia → StackOverflow

Back Button:
→ Stack.pop() → Wikipedia ✓
→ Stack.pop() → YouTube ✓
→ Stack.pop() → Google ✓

The last visited page is returned to first! ✅
```

---

## SECTION 2: Complete Theory — Deep Dive

### Stack Operations

| Operation | Time | Description |
|-----------|------|-------------|
| `push(x)` | O(1) | Add element to top |
| `pop()` | O(1) | Remove top element |
| `top()` | O(1) | View top element |
| `isEmpty()` | O(1) | Check if empty |
| `isFull()` | O(1) | Check if full (array) |

**Space:** O(n) | **Time:** All O(1)

### Queue Operations

| Operation | Time | Description |
|-----------|------|-------------|
| `enqueue(x)` | O(1) | Add to end |
| `dequeue()` | O(1) | Remove from front |
| `front()` | O(1) | View front |
| `isEmpty()` | O(1) | Check if empty |

**Space:** O(n) | **Time:** All O(1)

### 5 Stack Patterns

#### Pattern 1: Parenthesis Matching

```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '[' || c == '{') {
            stack.push(c);
        } else {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    
    return stack.isEmpty();
}
```

#### Pattern 2: Next Greater Element

```java
public int[] nextGreater(int[] arr) {
    int n = arr.length;
    int[] result = new int[n];
    Stack<Integer> stack = new Stack<>();
    
    for (int i = n - 1; i >= 0; i--) {
        while (!stack.isEmpty() && stack.peek() <= arr[i]) {
            stack.pop();
        }
        result[i] = stack.isEmpty() ? -1 : stack.peek();
        stack.push(arr[i]);
    }
    
    return result;
}
```

#### Pattern 3: Celebrity Problem (Stack Reduction)

```java
public int findCelebrity(int n) {
    Stack<Integer> stack = new Stack<>();
    for (int i = 0; i < n; i++) stack.push(i);
    
    while (stack.size() >= 2) {
        int A = stack.pop();
        int B = stack.pop();
        if (knows(A, B)) stack.push(B);  // A knows B, A not celebrity
        else stack.push(A);  // A doesn't know B, B not celebrity
    }
    
    return stack.pop();
}
```

#### Pattern 4: Min Stack (O(1) getMin)

```java
class MinStack {
    Stack<Integer> stack;
    Stack<Integer> minStack;
    
    public void push(int x) {
        stack.push(x);
        if (minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        }
    }
    
    public void pop() {
        if (stack.peek().equals(minStack.peek())) {
            minStack.pop();
        }
        stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

#### Pattern 5: Sliding Window Maximum (Deque)

```java
public int[] slidingWindowMax(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new LinkedList<>();
    
    for (int i = 0; i < n; i++) {
        while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
            deque.pollFirst();
        }
        while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
            deque.pollLast();
        }
        deque.offerLast(i);
        
        if (i >= k - 1) {
            result[i - k + 1] = nums[deque.peekFirst()];
        }
    }
    
    return result;
}
```

### 4 Queue Patterns

#### Pattern 1: BFS (Level Order)

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        
        result.add(level);
    }
    
    return result;
}
```

#### Pattern 2: Circular Queue

```java
class CircularQueue {
    int[] queue;
    int head, tail, count, size;
    
    public CircularQueue(int k) {
        queue = new int[k];
        size = k;
        head = 0;
        tail = 0;
        count = 0;
    }
    
    public boolean enQueue(int value) {
        if (count == size) return false;
        queue[tail] = value;
        tail = (tail + 1) % size;
        count++;
        return true;
    }
    
    public boolean deQueue() {
        if (count == 0) return false;
        head = (head + 1) % size;
        count--;
        return true;
    }
    
    public int front() {
        return queue[head];
    }
    
    public boolean isEmpty() {
        return count == 0;
    }
}
```

#### Pattern 3: Task Scheduler

```java
public int leastInterval(int[] tasks, int n) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (int task : tasks) freq.put(task, freq.getOrDefault(task, 0) + 1);
    
    Queue<Integer> queue = new LinkedList<>();
    for (int f : freq.values()) queue.offer(f);
    
    int time = 0;
    while (!queue.isEmpty()) {
        List<Integer> batch = new ArrayList<>();
        int size = queue.size();
        
        for (int i = 0; i < size && i <= n; i++) {
            batch.add(queue.poll() - 1);
        }
        
        for (int f : batch) {
            if (f > 0) queue.offer(f);
        }
        
        time += queue.isEmpty() ? batch.size() : n + 1;
    }
    
    return time;
}
```

#### Pattern 4: First Unique Number

```java
class FirstUnique {
    Queue<Integer> queue = new LinkedList<>();
    Map<Integer, Integer> count = new HashMap<>();
    
    public FirstUnique(int[] nums) {
        for (int num : nums) add(num);
    }
    
    public int showFirstUnique() {
        while (!queue.isEmpty() && count.get(queue.peek()) > 1) {
            queue.poll();
        }
        return queue.isEmpty() ? -1 : queue.peek();
    }
    
    public void add(int value) {
        queue.offer(value);
        count.put(value, count.getOrDefault(value, 0) + 1);
    }
}
```

---

## SECTION 3: Problem Progression — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Valid Parentheses**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/valid-parentheses/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 10/10

**Solution**:
```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '[' || c == '{') {
            stack.push(c);
        } else {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    
    return stack.isEmpty();
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **2. Implement Stack using Queues**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/implement-stack-using-queues/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 7/10

**Solution**:
```java
class MyStack {
    Queue<Integer> q1 = new LinkedList<>();
    Queue<Integer> q2 = new LinkedList<>();
    
    public void push(int x) {
        q2.offer(x);
        while (!q1.isEmpty()) q2.offer(q1.poll());
        Queue<Integer> temp = q1; q1 = q2; q2 = temp;
    }
    
    public int pop() {
        return q1.poll();
    }
    
    public int top() {
        return q1.peek();
    }
    
    public boolean empty() {
        return q1.isEmpty();
    }
}
```

---

#### Level 2: Easy

#### **3. Daily Temperatures**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/daily-temperatures/
- **Company Tags**: Google, Amazon, Microsoft
- **Frequency**: 9/10

**Solution (Next Greater Element)**:
```java
public int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] result = new int[n];
    Stack<Integer> stack = new Stack<>();
    
    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
            int prev = stack.pop();
            result[prev] = i - prev;
        }
        stack.push(i);
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **4. Number of Islands**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/number-of-islands/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (BFS with Queue)**:
```java
public int numIslands(char[][] grid) {
    if (grid == null || grid.length == 0) return 0;
    
    int count = 0;
    int rows = grid.length, cols = grid[0].length;
    
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == '1') {
                bfs(grid, i, j, rows, cols);
                count++;
            }
        }
    }
    
    return count;
}

private void bfs(char[][] grid, int i, int j, int rows, int cols) {
    Queue<int[]> queue = new LinkedList<>();
    queue.offer(new int[]{i, j});
    grid[i][j] = '0';
    
    int[][] dirs = {{-1,0},{1,0},{0,-1},{0,1}};
    
    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        for (int[] dir : dirs) {
            int ni = cell[0] + dir[0], nj = cell[1] + dir[1];
            if (ni >= 0 && ni < rows && nj >= 0 && nj < cols && grid[ni][nj] == '1') {
                grid[ni][nj] = '0';
                queue.offer(new int[]{ni, nj});
            }
        }
    }
}
```

**Time**: O(rows × cols) | **Space**: O(min(rows, cols))

---

#### Level 3: Medium

#### **5. Min Stack**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/min-stack/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution**:
```java
class MinStack {
    Stack<Integer> stack;
    Stack<Integer> minStack;
    
    public void push(int x) {
        stack.push(x);
        if (minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        }
    }
    
    public void pop() {
        if (stack.peek().equals(minStack.peek())) {
            minStack.pop();
        }
        stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

**Time**: All O(1) | **Space**: O(n)

---

#### **6. Sliding Window Maximum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/sliding-window-maximum/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Monotonic Queue)**:
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new LinkedList<>();
    
    for (int i = 0; i < n; i++) {
        while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
            deque.pollFirst();
        }
        while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
            deque.pollLast();
        }
        deque.offerLast(i);
        
        if (i >= k - 1) {
            result[i - k + 1] = nums[deque.peekFirst()];
        }
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(k)

---

#### **7. Binary Tree Level Order Traversal**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (BFS)**:
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        
        result.add(level);
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(n)

---

#### Level 4: Hard

#### **8. Trapping Rain Water**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/trapping-rain-water/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Two Stacks)**:
```java
public int trap(int[] height) {
    int n = height.length;
    if (n == 0) return 0;
    
    Stack<Integer> left = new Stack<>(), right = new Stack<>();
    int maxLeft = 0, maxRight = 0;
    
    for (int i = 0; i < n; i++) {
        maxLeft = Math.max(maxLeft, height[i]);
        left.push(maxLeft);
    }
    
    for (int i = n - 1; i >= 0; i--) {
        maxRight = Math.max(maxRight, height[i]);
        right.push(maxRight);
    }
    
    int water = 0;
    for (int i = 0; i < n; i++) {
        water += Math.min(left.get(i), right.peek()) - height[i];
        right.pop();
    }
    
    return water;
}
```

**Time**: O(n) | **Space**: O(n)

---

### CodeChef Problems

#### **9. Remove Duplicates (Stack)**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/learn/course/stacks-and-queues/LSTACKS/problems/STACK02
- **Rating**: 800

**Problem**: Remove adjacent duplicate characters using stack

**Solution**:
```java
public String removeDuplicates(String s) {
    Stack<Character> stack = new Stack<>();
    
    for (char c : s.toCharArray()) {
        if (!stack.isEmpty() && stack.peek() == c) {
            stack.pop();
        } else {
            stack.push(c);
        }
    }
    
    StringBuilder result = new StringBuilder();
    for (char c : stack) result.append(c);
    
    return result.toString();
}
```

---

### Codeforces Problems

#### **10. Plug-in (Stack)**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset/problem/81/A
- **Rating**: 1000
- **Tags**: Stack

**Problem**: Remove adjacent duplicate pairs

**Solution**:
```java
public String plugIn(String s) {
    Stack<Character> stack = new Stack<>();
    
    for (char c : s.toCharArray()) {
        if (!stack.isEmpty() && stack.peek() == c) {
            stack.pop();
        } else {
            stack.push(c);
        }
    }
    
    StringBuilder result = new StringBuilder();
    for (char c : stack) result.append(c);
    
    return result.toString();
}
```

---

## SECTION 4: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Stack Problems (10)

| # | Problem | Link |
|---|---------|------|
| 1 | Valid Parentheses | https://leetcode.com/problems/valid-parentheses/ |
| 2 | Min Stack | https://leetcode.com/problems/min-stack/ |
| 3 | Daily Temperatures | https://leetcode.com/problems/daily-temperatures/ |
| 4 | Trapping Rain Water | https://leetcode.com/problems/trapping-rain-water/ |
| 5 | Largest Rectangle in Histogram | https://leetcode.com/problems/largest-rectangle-in-histogram/ |
| 6 | Remove Duplicate Letters | https://leetcode.com/problems/remove-duplicate-letters/ |
| 7 | Evaluate Reverse Polish Notation | https://leetcode.com/problems/evaluate-reverse-polish-notation/ |
| 8 | Decode String | https://leetcode.com/problems/decode-string/ |
| 9 | Asteroid Collision | https://leetcode.com/problems/asteroid-collision/ |
| 10 | Sum of Subarray Minimums | https://leetcode.com/problems/sum-of-subarray-minimums/ |

### Queue Problems (10)

| # | Problem | Link |
|---|---------|------|
| 1 | Number of Islands | https://leetcode.com/problems/number-of-islands/ |
| 2 | Binary Tree Level Order | https://leetcode.com/problems/binary-tree-level-order-traversal/ |
| 3 | Sliding Window Maximum | https://leetcode.com/problems/sliding-window-maximum/ |
| 4 | Course Schedule | https://leetcode.com/problems/course-schedule/ |
| 5 | Find the Writer | https://leetcode.com/problems/find-the-winner-of-the-circular-game/ |
| 6 | Implement Queue using Stacks | https://leetcode.com/problems/implement-queue-using-stacks/ |
| 7 | First Unique Number | https://leetcode.com/problems/first-unique-number/ |
| 8 | Farthest Building You Can Reach | https://leetcode.com/problems/farthest-building-you-can-reach/ |
| 9 | Task Scheduler | https://leetcode.com/problems/task-scheduler/ |
| 10 | Rotting Oranges | https://leetcode.com/problems/rotting-oranges/ |

---

## SECTION 5: Active Learning — 5 Questions

1. **Valid Parentheses** — LeetCode 20  
   [Link](https://leetcode.com/problems/valid-parentheses/)

2. **Daily Temperatures** — LeetCode 739  
   [Link](https://leetcode.com/problems/daily-temperatures/)

3. **Min Stack** — LeetCode 155  
   [Link](https://leetcode.com/problems/min-stack/)

4. **Sliding Window Maximum** — LeetCode 239  
   [Link](https://leetcode.com/problems/sliding-window-maximum/)

5. **Number of Islands** — LeetCode 200  
   [Link](https://leetcode.com/problems/number-of-islands/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 6: Revision Notes

### 📝 Stack & Queue Cheat Sheet

| Pattern | When | Template Key |
|---------|------|--------------|
| **Parenthesis** | Valid brackets | `push(open), pop(close), check match` |
| **Next Greater** | Nearest bigger element | `iterate backwards, stack.peek() <= arr[i]` |
| **Min Stack** | O(1) getMin | `2 stacks: main + min` |
| **BFS** | Level order, shortest path | `queue.poll(), add children` |
| **Monotonic Queue** | Sliding window max | `deque.pollLast() <= current` |

---
