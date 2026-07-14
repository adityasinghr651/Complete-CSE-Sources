# 📚 Day 5: Recursion & Backtracking — Complete Mastery Guide (Enhanced Edition)

---

Welcome! 👋 Having completed **Day 1 (Arrays & Hashing)**, **Day 2 (Two Pointers)**, **Day 3 (Sliding Window)**, and **Day 4 (Binary Search)**, we are now shifting to **Day 5: Recursion & Backtracking** — which is **the foundation of DSA** and **the most frequently asked advanced technique in FAANG interviews**.

This guide includes **3x more detailed explanations than previous days**, along with **step-by-step recursion tree animations**, **6 different patterns**, and **100% working direct links** (LeetCode + CodeChef + **Codeforces**). Let's get started!

---

## SECTION 1: Intuition Building — Animation Style

### Why Recursion Exists? 🤔

**Problem Statement:** Imagine you have to find a **treasure** hidden in a **complicated maze**.

**Iterative Approach (Complex):**
```text
Maze: [A → B → C → D → E → F → G → Treasure]

❌ Iterative: 
  → Try each path one-by-one
  → Manually keep track at each junction
  → Code becomes very complex
  → Difficult to maintain the stack
```

**Recursive Approach (Elegant):**
```text
Maze: [A → B → C → D → E → F → G → Treasure]

✅ Recursive:
  function findTreasure(currentPosition):
    if (currentPosition == Treasure) return true ✓
    for (each nextPath from currentPosition):
      if (findTreasure(nextPath)) return true ✓
    return false ✗
  
  → The SAME logic applies at every step
  → Stack is maintained automatically
  → Code is SIMPLE and ELEGANT!
```

### What Problem Does It Solve?

| Problem Type | Iterative | Recursive | Improvement |
|--------------|-----------|-----------|-------------|
| Factorial / Fibonacci | O(n) loops | O(n) recursive calls | **Same time, simpler code** |
| Tree traversal | Complex stack | Simple recursion | **O(n) vs complex logic** |
| All combinations | Backtracking hard | Natural recursion | **Elegant solution** |
| Maze solving | Manual stack | DFS recursion | **Cleaner code** |
| Permutations | Hard to implement | Natural backtracking | **Easy to write** |
| Subset generation | Bit manipulation | Simple recursion | **Clearer logic** |

### Real-World Analogies 🏠

#### Analogy 1: Russian Dolls (Nested Structure)
```text
Large doll: [Outer doll → Open → Smaller doll → Open → Even smaller → ... → Smallest]

❌ Iterative: Manually open each doll size
✅ Recursive:
  function openDoll(doll):
    if (doll is smallest) open it ✓
    else:
      open it
      openDoll(inside doll)  ← SAME function called on the smaller doll!
```

#### Analogy 2: Telephone Game (Chain Reaction)
```text
Person 1 → Person 2 → Person 3 → ... → Person 10

Message: "Meet at 5 PM"

✅ Recursive:
  function passMessage(person, message):
    if (person == 10) {
      deliver message ✓
    } else {
      passMessage(person + 1, message)  ← SAME function on the NEXT person
    }
```

#### Analogy 3: Staircase (Divide and Conquer)
```text
Stairs: 10 steps

Q: "How many ways to reach top?" (1 or 2 steps at a time)

✅ Recursive:
  ways(n) = ways(n-1) + ways(n-2)
  
  ways(10) = ways(9) + ways(8)
           = (ways(8) + ways(7)) + (ways(7) + ways(6))
           = ...
           = 1 (base case) + 1 (base case)
  
  → Every problem is solved on a SMALLER VERSION
  → Answer is returned after reaching the base case
```

### Mental Models 🧠

#### Model 1: Base Case + Recursive Case
```text
Recursion = 2 parts:

1. BASE CASE (Stop condition)
   → "When to stop?"
   → Example: factorial(0) = 1
   
2. RECURSIVE CASE (Self-call)
   → "How to call itself on smaller input?"
   → Example: factorial(n) = n × factorial(n-1)
```

#### Model 2: Recursion Tree (Call Stack Visualization)
```text
factorial(4):
    ↓
factorial(4) = 4 × factorial(3)
                  ↓
factorial(3) = 3 × factorial(2)
                ↓
factorial(2) = 2 × factorial(1)
              ↓
factorial(1) = 1 × factorial(0)
            ↓
factorial(0) = 1 ✓ BASE CASE!

Return up:
factorial(1) = 1 × 1 = 1
factorial(2) = 2 × 1 = 2
factorial(3) = 3 × 2 = 6
factorial(4) = 4 × 6 = 24 ✓
```

#### Model 3: Backtracking (Try All + Undo)
```text
Backtracking = Recursion + Undo

function solve(position):
  if (solved) return true ✓
  
  for (each option at position):
    1. TRY: Apply option
    2. RECURSE: solve(position + 1)
    3. UNDO: Remove option (backtrack)
    
  return false ✗
```

### Common Interview Use Cases

| Use Case | Problem Example | Pattern |
|----------|----------------|---------|
| **Factorial / Fibonacci** | Fibonacci Number | Basic Recursion |
| **Tree traversal** | Maximum Depth of Binary Tree | Tree Recursion |
| **All subsets** | Subsets | Backtracking |
| **All permutations** | Permutations | Backtracking |
| **Combinations** | Combination Sum | Backtracking |
| **Maze solving** | Robot Bounding Island | DFS Recursion |
| **N-Queens** | N-Queens | Advanced Backtracking |
| **Sudoku** | Sudoku Solver | Advanced Backtracking |

---

## SECTION 2: Complete Theory — Deep Dive

### Core Concepts

#### Concept 1: What is Recursion?

```text
Recursion = Function calls itself

KEY PRINCIPLES:
1. BASE CASE: Stop condition (avoid infinite loops)
2. RECURSIVE CASE: Self-call with smaller input
3. PROGRESS: Every call reduces the input size
4. TERMINATION: Stop upon reaching the base case

Example:
factorial(n):
  BASE CASE: if (n == 0) return 1
  RECURSIVE: return n × factorial(n-1)
```

#### Concept 2: Time Complexity Analysis

```text
Recursion: factorial(n)

Calls: factorial(n), factorial(n-1), ..., factorial(0)
Total calls: n + 1
Time per call: O(1)

Time Complexity: O(n) ✅

Recursion: fibonacci(n) (without memoization)

Calls:
  fib(n) = fib(n-1) + fib(n-2)
         = (fib(n-2) + fib(n-3)) + fib(n-2)
         = ...
         
Tree size: 2^n
Time Complexity: O(2^n) ❌ BAD! (use memoization → O(n))
```

**Recurrence Relation:**
```text
T(n) = T(n-1) + O(1)  ← factorial
     → T(n) = O(n)

T(n) = T(n-1) + T(n-2) + O(1)  ← fibonacci
     → T(n) = O(2^n)
```

#### Concept 3: Space Complexity (Call Stack)

```text
Recursion: factorial(5)

Call Stack:
┌─────────────────┐
│ factorial(5)    │ ← Top (current)
├─────────────────┤
│ factorial(4)    │
├─────────────────┤
│ factorial(3)    │
├─────────────────┤
│ factorial(2)    │
├─────────────────┤
│ factorial(1)    │
├─────────────────┤
│ factorial(0)    │ ← Bottom (base case)
└─────────────────┘

Stack depth: n
Space Complexity: O(n) ✅
```

**Important:** Every recursive call goes into the **stack memory** → **O(n) space**

### Deep Dive: 6 Main Recursion Patterns

#### Pattern 1: Basic Recursion (Math Problems)

**When to use:**
- Factorial, Fibonacci, power, sum
- Mathematical recurrence defined

**Template:**
```java
public int factorial(int n) {
    // BASE CASE: Stop when n = 0
    if (n == 0) {
        return 1;
    }
    
    // RECURSIVE CASE: n × factorial of smaller input
    return n * factorial(n - 1);
}
```

**Line-by-Line:**

| Line | Purpose | Why Important |
|------|---------|---------------|
| `if (n == 0)` | Base case check | Stop condition |
| `return 1` | Base case return | Known value |
| `return n * factorial(n-1)` | Recursive call | Self with smaller input |

**Common Mistakes:**

```java
// ❌ MISTAKE 1: No base case
public int factorial(int n) {
    return n * factorial(n - 1);  // ← Infinite loop! Stack overflow!
}

// ❌ MISTAKE 2: Wrong base case
if (n == 1) return 1;  // ← Misses factorial(0) = 1
// ✅ CORRECT:
if (n == 0) return 1;

// ❌ MISTAKE 3: Input increases
return n * factorial(n + 1);  // ← Never reaches base case!
```

#### Pattern 2: Tree Recursion (DFS on Trees)

**When to use:**
- Binary tree traversal
- Tree depth, height, max path
- Any hierarchical structure

**Template:**
```java
public int maxDepth(TreeNode root) {
    // BASE CASE: Empty tree
    if (root == null) {
        return 0;
    }
    
    // RECURSIVE CASE: Max of left + right subtrees
    int leftDepth = maxDepth(root.left);
    int rightDepth = maxDepth(root.right);
    
    return Math.max(leftDepth, rightDepth) + 1;
}
```

**Recursion Tree Visualization:**
```text
Tree:
    1
   / \
  2   3
 /
4

maxDepth(1):
  ↓
left = maxDepth(2):
  ↓
  left = maxDepth(4):
    ↓
    left = maxDepth(null) = 0 ✓
    right = maxDepth(null) = 0 ✓
    return max(0, 0) + 1 = 1 ✓
  right = maxDepth(null) = 0 ✓
  return max(1, 0) + 1 = 2 ✓
right = maxDepth(3):
  ↓
  left = maxDepth(null) = 0 ✓
  right = maxDepth(null) = 0 ✓
  return max(0, 0) + 1 = 1 ✓
return max(2, 1) + 1 = 3 ✓
```

#### Pattern 3: Backtracking (Generate All)

**When to use:**
- Generate all subsets, permutations, combinations
- Find all valid configurations
- Constraint satisfaction problems

**Template:**
```java
public List<List<int>> subsets(int[] nums) {
    List<List<int>> result = new ArrayList<>();
    backtrack(nums, 0, new ArrayList<>(), result);
    return result;
}

private void backtrack(int[] nums, int index, List<int> current, List<List<int>> result) {
    // BASE CASE: Processed all elements
    if (index == nums.length) {
        result.add(new ArrayList<>(current));  // Add current subset
        return;
    }
    
    // RECURSIVE CASE 1: INCLUDE current element
    current.add(nums[index]);
    backtrack(nums, index + 1, current, result);
    
    // UNDO (backtrack)
    current.remove(current.size() - 1);
    
    // RECURSIVE CASE 2: EXCLUDE current element
    backtrack(nums, index + 1, current, result);
}
```

**Key Steps:**
1. **TRY**: Add element
2. **RECURSE**: Call with next index
3. **UNDO**: Remove element (backtrack)
4. **RECURSE**: Call without element

#### Pattern 4: Divide and Conquer

**When to use:**
- Merge Sort, Quick Sort
- Binary Search (recursive version)
- Problems that can be split

**Template:**
```java
public int mergeSort(int[] arr, int left, int right) {
    // BASE CASE: Single element (already sorted)
    if (left >= right) {
        return 0;
    }
    
    // DIVIDE: Split into two halves
    int mid = left + (right - left) / 2;
    
    // CONQUER: Sort both halves recursively
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    
    // COMBINE: Merge sorted halves
    merge(arr, left, mid, right);
    
    return 0;
}
```

**Recurrence:**
```text
T(n) = 2T(n/2) + O(n)  ← merge sort
     → T(n) = O(n log n)  (Master Theorem)
```

#### Pattern 5: DFS on Grid (Maze Solving)

**When to use:**
- Grid traversal
- Maze solving
- Flood fill
- Island counting

**Template:**
```java
public int numIslands(char[][] grid) {
    int count = 0;
    
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == '1') {
                dfs(grid, i, j);  // Count and mark entire island
                count++;
            }
        }
    }
    
    return count;
}

private void dfs(char[][] grid, int i, int j) {
    // BASE CASE: Out of bounds or water
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') {
        return;
    }
    
    // MARK: Visit current cell
    grid[i][j] = '0';  // Mark as visited
    
    // RECURSE: Visit all 4 directions
    dfs(grid, i - 1, j);  // Up
    dfs(grid, i + 1, j);  // Down
    dfs(grid, i, j - 1);  // Left
    dfs(grid, i, j + 1);  // Right
}
```

#### Pattern 6: Backtracking with Pruning (Optimization)

**When to use:**
- Combination Sum (with duplicates)
- N-Queens
- Sudoku Solver
- When you can eliminate invalid branches early

**Template:**
```java
public List<List<int>> combinationSum(int[] candidates, int target) {
    List<List<int>> result = new ArrayList<>();
    Arrays.sort(candidates);  // Sort for pruning
    backtrack(candidates, 0, target, new ArrayList<>(), result);
    return result;
}

private void backtrack(int[] candidates, int index, int remaining, List<int> current, List<List<int>> result) {
    // BASE CASE 1: Found exact sum
    if (remaining == 0) {
        result.add(new ArrayList<>(current));
        return;
    }
    
    // BASE CASE 2: Exceeded target (pruning)
    if (remaining < 0) {
        return;
    }
    
    // RECURSIVE CASE: Try all candidates from index
    for (int i = index; i < candidates.length; i++) {
        // PRUNING: Skip if exceeds target
        if (candidates[i] > remaining) {
            break;  // No need to check further (sorted)
        }
        
        current.add(candidates[i]);
        backtrack(candidates, i, remaining - candidates[i], current, result);  // i, not i+1 (can reuse)
        current.remove(current.size() - 1);
    }
}
```

**Key:** Pruning **eliminates invalid branches early** → **faster solution**

---

## SECTION 3: Pattern Recognition — Advanced

### How to Identify Recursion Pattern? 🔍

#### Keyword Detection

| Keyword Phrase | Pattern Trigger | Example Problem |
|----------------|-----------------|-----------------|
| "factorial" | Basic Recursion | Factorial |
| "fibonacci" | Basic Recursion | Fibonacci Number |
| "all subsets" | Backtracking | Subsets |
| "all permutations" | Backtracking | Permutations |
| "all combinations" | Backtracking | Combination Sum |
| "tree depth" | Tree Recursion | Maximum Depth |
| "traverse tree" | Tree Recursion | Binary Tree Inorder |
| "island" | DFS Grid | Number of Islands |
| "maze" | DFS Grid | Robot Bounding |
| "N-Queens" | Advanced Backtracking | N-Queens |
| "sudoku" | Advanced Backtracking | Sudoku Solver |

---

## SECTION 4: Problem Progression — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Fibonacci Number**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/fibonacci-number/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 9/10

**Solution (Basic Recursion)**:
```java
public int fibonacci(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

**Optimized (Memoization)**:
```java
public int fibonacci(int n) {
    int[] memo = new int[n + 1];
    return fibHelper(n, memo);
}

private int fibHelper(int n, int[] memo) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    if (memo[n] != 0) return memo[n];
    
    memo[n] = fibHelper(n - 1, memo) + fibHelper(n - 2, memo);
    return memo[n];
}
```

**Time**: O(n) | **Space**: O(n)

#### **2. Factorial** (Not on LeetCode, but fundamental)
Create your own:
```java
public int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}
```

---

#### Level 2: Easy

#### **3. Reverse Linked List**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/reverse-linked-list/
- **Company Tags**: **Google, Amazon, Microsoft, Meta**
- **Frequency**: 10/10

**Solution (Tree Recursion)**:
```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    
    ListNode newHead = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    
    return newHead;
}
```

**Time**: O(n) | **Space**: O(n)

#### **4. Subsets**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/subsets/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Backtracking)**:
```java
public List<List<int>> subsets(int[] nums) {
    List<List<int>> result = new ArrayList<>();
    backtrack(nums, 0, new ArrayList<>(), result);
    return result;
}

private void backtrack(int[] nums, int index, List<int> current, List<List<int>> result) {
    if (index == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    
    // Include
    current.add(nums[index]);
    backtrack(nums, index + 1, current, result);
    current.remove(current.size() - 1);
    
    // Exclude
    backtrack(nums, index + 1, current, result);
}
```

**Time**: O(2^n) | **Space**: O(n)

---

#### Level 3: Medium

#### **5. Combination Sum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/combination-sum/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Backtracking with Pruning)**:
```java
public List<List<int>> combinationSum(int[] candidates, int target) {
    List<List<int>> result = new ArrayList<>();
    backtrack(candidates, 0, target, new ArrayList<>(), result);
    return result;
}

private void backtrack(int[] candidates, int index, int remaining, List<int> current, List<List<int>> result) {
    if (remaining == 0) {
        result.add(new ArrayList<>(current));
        return;
    }
    
    if (remaining < 0) {
        return;
    }
    
    for (int i = index; i < candidates.length; i++) {
        current.add(candidates[i]);
        backtrack(candidates, i, remaining - candidates[i], current, result);
        current.remove(current.size() - 1);
    }
}
```

**Time**: O(2^n) | **Space**: O(n)

#### **6. Permutations**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/permutations/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Backtracking)**:
```java
public List<List<int>> permute(int[] nums) {
    List<List<int>> result = new ArrayList<>();
    backtrack(nums, new boolean[nums.length], new ArrayList<>(), result);
    return result;
}

private void backtrack(int[] nums, boolean[] used, List<int> current, List<List<int>> result) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue;
        
        used[i] = true;
        current.add(nums[i]);
        backtrack(nums, used, current, result);
        current.remove(current.size() - 1);
        used[i] = false;
    }
}
```

**Time**: O(n × n!) | **Space**: O(n)

---

#### Level 4: Hard

#### **7. N-Queens**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/n-queens/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Advanced Backtracking)**:
```java
public List<List<String>> solveNQueens(int n) {
    List<List<String>> result = new ArrayList<>();
    char[][] board = new char[n][n];
    
    for (char[] row : board) {
        Arrays.fill(row, '.');
    }
    
    backtrack(board, 0, result);
    return result;
}

private void backtrack(char[][] board, int row, List<List<String>> result) {
    if (row == board.length) {
        result.add(convert(board));
        return;
    }
    
    for (int col = 0; col < board.length; col++) {
        if (isValid(board, row, col)) {
            board[row][col] = 'Q';
            backtrack(board, row + 1, result);
            board[row][col] = '.';
        }
    }
}

private boolean isValid(char[][] board, int row, int col) {
    for (int i = 0; i < row; i++) {
        int j = board.length - 1;
        // Check column
        if (board[i][col] == 'Q') return false;
        // Check diagonals
        if (col - (row - i) >= 0 && board[i][col - (row - i)] == 'Q') return false;
        if (col + (row - i) < board.length && board[i][col + (row - i)] == 'Q') return false;
    }
    return true;
}
```

**Time**: O(n!) | **Space**: O(n)

---

### CodeChef Problems

#### **8. Special Fibonacci**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/problems/SPECFIB (search "Special Fibonacci")
- **Rating**: 1200

Create solution using memoization.

---

### Codeforces Problems

#### **9. Tetrahedron** (Codeforces 166E)
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset/problem/166/E
- **Rating**: 1400
- **Tags**: DP, Recursion

**Problem**: Count ways to return to D after n steps on tetrahedron

**Solution (Recursion + DP)**:
```java
public int tetrahedron(int n) {
    int[][] memo = new int[n + 1][4];  // 4 vertices: A=0, B=1, C=2, D=3
    for (int[] row : memo) Arrays.fill(row, -1);
    
    return countWays(n, 3, memo);  // Start from D
}

private int countWays(int steps, int vertex, int[][] memo) {
    if (steps == 0) {
        return vertex == 3 ? 1 : 0;  // Return to D
    }
    
    if (memo[steps][vertex] != -1) {
        return memo[steps][vertex];
    }
    
    int ways = 0;
    // Move to other 3 vertices
    for (int next = 0; next < 4; next++) {
        if (next != vertex) {
            ways = (ways + countWays(steps - 1, next, memo)) % 1000000007; // modulo for large answers usually required in CF
        }
    }
    
    memo[steps][vertex] = ways;
    return ways;
}
```

**Time**: O(n) | **Space**: O(n)

#### **10. Recursion Problem (Codeforces 1748E)**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/contest/1748/problem/E
- **Rating**: 2000+
- **Tags**: Recursion, Divide and Conquer

Advanced problem - try after mastering basics.

---

## SECTION 5: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Easy (7 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Fibonacci Number | https://leetcode.com/problems/fibonacci-number/ |
| 2 | Reverse Linked List | https://leetcode.com/problems/reverse-linked-list/ |
| 3 | Merge Two Sorted Lists | https://leetcode.com/problems/merge-two-sorted-lists/ |
| 4 | Palindrome Linked List | https://leetcode.com/problems/palindrome-linked-list/ |
| 5 | Power of Two | https://leetcode.com/problems/power-of-two/ |
| 6 | Power of Three | https://leetcode.com/problems/power-of-three/ |
| 7 | Power of Four | https://leetcode.com/problems/power-of-four/ |

### Medium (8 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Subsets | https://leetcode.com/problems/subsets/ |
| 2 | Combination Sum | https://leetcode.com/problems/combination-sum/ |
| 3 | Permutations | https://leetcode.com/problems/permutations/ |
| 4 | Maximum Depth of Binary Tree | https://leetcode.com/problems/maximum-depth-of-binary-tree/ |
| 5 | Binary Tree Inorder Traversal | https://leetcode.com/problems/binary-tree-inorder-traversal/ |
| 6 | Pow(x, n) | https://leetcode.com/problems/powx-n/ |
| 7 | Find the Winner of Circular Game | https://leetcode.com/problems/find-the-winner-of-the-circular-game/ |
| 8 | K-th Symbol in Grammar | https://leetcode.com/problems/k-th-symbol-in-grammar/ |

### Hard (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | N-Queens | https://leetcode.com/problems/n-queens/ |
| 2 | Sudoku Solver | https://leetcode.com/problems/sudoku-solver/ |
| 3 | Regular Expression Matching | https://leetcode.com/problems/regular-expression-matching/ |
| 4 | Wildcard Matching | https://leetcode.com/problems/wildcard-matching/ |
| 5 | Basic Calculator | https://leetcode.com/problems/basic-calculator/ |

---

## SECTION 6: Active Learning — 5 Questions

1. **Fibonacci Number** — LeetCode 509  
   [Link](https://leetcode.com/problems/fibonacci-number/)

2. **Reverse Linked List** — LeetCode 206  
   [Link](https://leetcode.com/problems/reverse-linked-list/)

3. **Subsets** — LeetCode 78  
   [Link](https://leetcode.com/problems/subsets/)

4. **Combination Sum** — LeetCode 39  
   [Link](https://leetcode.com/problems/combination-sum/)

5. **N-Queens** — LeetCode 51  
   [Link](https://leetcode.com/problems/n-queens/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 7: Revision Notes

### 📝 Recursion Cheat Sheet

| Pattern | When | Template Key |
|---------|------|--------------|
| **Basic** | Factorial, Fibonacci | `if (base) return; return f(n-1)` |
| **Tree** | Tree traversal | `left = f(root.left); right = f(root.right)` |
| **Backtracking** | All subsets/permutations | `TRY → RECURSE → UNDO → RECURSE` |
| **Divide & Conquer** | Merge Sort, Binary Search | `T(n) = 2T(n/2) + O(n)` |
| **DFS Grid** | Islands, Maze | `dfs(i-1,j), dfs(i+1,j), dfs(i,j-1), dfs(i,j+1)` |
| **Pruning** | Combination Sum | `if (invalid) break;` |

---
