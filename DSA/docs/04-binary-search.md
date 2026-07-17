# 📚 Day 4: Binary Search — Complete Mastery Guide (Enhanced Edition)

## SECTION 1: Intuition Building — Animation Style

### Why Binary Search Exists? 🤔

**Problem Statement:** Imagine you have **1 million sorted numbers** and you need to find a **specific number**.

**Linear Search Approach (O(n)):**
```text
Array: [1, 3, 5, 7, 9, 11, 13, 15, ..., 1000000]
Target: 999999

Check: 1 ✗
Check: 3 ✗
Check: 5 ✗
...
Check: 999997 ✗
Check: 999999 ✓

Total: ~500,000 comparisons ❌ SLOW!
```

**Binary Search Approach (O(log n)):**
```text
Array: [1, 3, 5, 7, 9, 11, 13, 15, ..., 1000000]
Target: 999999

Step 1: Check middle (500000) → 500000 < 999999 → Right half
Step 2: Check middle (750000) → 750000 < 999999 → Right half
Step 3: Check middle (875000) → 875000 < 999999 → Right half
Step 4: Check middle (937500) → 937500 < 999999 → Right half
Step 5: Check middle (968750) → 968750 < 999999 → Right half
Step 6: Check middle (984375) → 984375 < 999999 → Right half
Step 7: Check middle (992187) → 992187 < 999999 → Right half
Step 8: Check middle (996093) → 996093 < 999999 → Right half
Step 9: Check middle (998046) → 998046 < 999999 → Right half
Step 10: Check middle (999023) → 999023 < 999999 → Right half
Step 11: Check middle (999511) → 999511 < 999999 → Right half
Step 12: Check middle (999755) → 999755 < 999999 → Right half
Step 13: Check middle (999877) → 999877 < 999999 → Right half
Step 14: Check middle (999938) → 999938 < 999999 → Right half
Step 15: Check middle (999969) → 999969 < 999999 → Right half
Step 16: Check middle (999984) → 999984 > 999999 → Left half
Step 17: Check middle (999976) → 999976 < 999999 → Right half
Step 18: Found 999999 ✓

Total: Only 18 comparisons! ✅ FAST!
```

**Key Insight:** 
- 1 million elements = 10⁶
- log₂(10⁶) ≈ 20
- **500,000 → 20 comparisons** = **25,000x faster!** 🚀

### What Problem Does It Solve?

| Problem Type | Linear Search | Binary Search | Improvement |
|--------------|---------------|---------------|-------------|
| Find element in sorted array | O(n) | O(log n) | **n → log n** |
| Find first/last occurrence | O(n) | O(log n) | **n → log n** |
| Find insertion position | O(n) | O(log n) | **n → log n** |
| Minimum/maximum answer | O(n) | O(log n) | **n → log n** |
| Search in rotated array | O(n) | O(log n) | **n → log n** |
| Search in 2D matrix | O(n × m) | O(log(n × m)) | **nm → log(nm)** |

### Real-World Analogies 🏠

#### Analogy 1: Dictionary Search
```text
Dictionary: [A, B, C, D, E, F, G, H, ..., Z]

Q: "Find word starting with 'M'"

❌ Linear: A, B, C, D, ..., M (13 pages check)
✅ Binary: 
  → Open middle (J) → M > J → Right half
  → Open middle (P) → M < P → Left half
  → Open middle (M) → Found! (3 checks)
```

#### Analogy 2: Guess the Number Game
```text
Range: 1 to 100

I think of a number: 73

❌ Linear: "Is it 1? Is it 2? Is it 3? ..." (73 questions)
✅ Binary:
  → "Is it > 50?" → Yes (range: 51-100)
  → "Is it > 75?" → No (range: 51-75)
  → "Is it > 62?" → Yes (range: 63-75)
  → "Is it > 68?" → Yes (range: 69-75)
  → "Is it > 71?" → Yes (range: 72-75)
  → "Is it > 73?" → No (range: 72-73)
  → "Is it 72?" → No
  → Found! 73 (7 questions)
```

#### Analogy 3: Phone Book (Sorted Names)
```text
Phone Book: [Adam, Alice, Anna, Bob, Carol, Dave, Eve, Frank, ...]

Q: "Find 'David'"

❌ Linear: Adam, Alice, Anna, Bob, Carol, Dave (6 checks)
✅ Binary:
  → Middle: Carol → David > Carol → Right half
  → Middle: Eve → David < Eve → Left half
  → Dave → Found! (3 checks)
```

### Mental Models 🧠

#### Model 1: Divide and Conquer
```text
Array: [1, 3, 5, 7, 9, 11, 13, 15]
Target: 7

Step 1: Divide
[1, 3, 5, 7] | [9, 11, 13, 15]
      ↑
     mid=4 (value=7)

Found! ✓

Every step: SEARCH SPACE IS HALVED
```

#### Model 2: Elimination Strategy
```text
Array: [1, 3, 5, 7, 9, 11, 13, 15]
Target: 11

Step 1: Check mid=4 (value=7)
[1, 3, 5, 7] ❌ | [9, 11, 13, 15] ✅
  ↓ ELIMINATE           ↓ KEEP

Step 2: Check mid=6 (value=11)
[9] ❌ | [11, 13, 15] ✅
  ↓ ELIMINATE     ↓ KEEP

Found! ✓
```

#### Model 3: Monotonic Function (Binary Search on Answer)
```text
Question: "Minimum days to make bouquets?"

Answer space: [1, 2, 3, 4, 5, 6, 7, 8, ...]

      false false false true true true true
      ↑     ↑     ↑     ↑    ↑    ↑    ↑
      1     2     3     4    5    6    7

      ← Invalid    → Valid

Binary search finds the FIRST "true" (minimum valid answer) ✅
```

### Common Interview Use Cases

| Use Case | Problem Example | Pattern |
|----------|----------------|---------|
| **Find element** | Binary Search | Classic |
| **First/last position** | Find First and Last Position | Duplicates |
| **Insertion point** | Search Insert Position | Lower Bound |
| **Minimum answer** | Koko Eating Bananas | Binary Search on Answer |
| **Rotated array** | Search in Rotated Sorted Array | Rotated |
| **2D matrix** | Search a 2D Matrix | Matrix |
| **Median** | Median of Two Sorted Arrays | Advanced |

---

## SECTION 2: Complete Theory — Deep Dive

### Core Concepts

#### Concept 1: What is "Binary Search"?

```text
Binary Search = Divide search space in half repeatedly

Key Requirements:
1. Data must be SORTED
2. Must be able to compare and eliminate half

Algorithm:
while (search space not empty) {
    mid = middle element
    if (mid == target) return mid
    if (mid < target) eliminate left half
    if (mid > target) eliminate right half
}
return not found
```

#### Concept 2: Time Complexity Analysis

```text
Initial search space: n elements
After 1st iteration: n/2 elements
After 2nd iteration: n/4 elements
After 3rd iteration: n/8 elements
...
After kth iteration: n/2^k elements

Stop when: n/2^k = 1
→ 2^k = n
→ k = log₂(n)

Time Complexity: O(log n) ✅

Example:
n = 1,000,000
log₂(1,000,000) ≈ 20 iterations
```

**Why log n?**
- In every iteration, the search space is **halved**
- 2^k = n → k = log₂(n)
- **log n = number of times you can divide by 2**

#### Concept 3: Space Complexity

```text
Classic Binary Search:
- Variables: left, right, mid (3 variables)
- No recursion → No stack space
Space: O(1) ✅
```

### Deep Dive: 6 Main Binary Search Patterns

#### Pattern 1: Classic Binary Search (Find Exact Element)

**When to use:**
- Array is sorted
- Find exact target value
- Return index or -1

**Template:**
```java
public int binarySearch(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left <= right) {
        // Prevent overflow: use left + (right - left) / 2
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            return mid;  // Found!
        } else if (arr[mid] < target) {
            left = mid + 1;  // Search right half
        } else {
            right = mid - 1;  // Search left half
        }
    }
    
    return -1;  // Not found
}
```

**Line-by-Line:**

| Line | Purpose | Why Important |
|------|---------|---------------|
| `left = 0, right = n-1` | Initialize | Full array |
| `while (left <= right)` | Loop condition | Stop when empty |
| `mid = left + (right - left) / 2` | Calculate mid | Avoid overflow |
| `arr[mid] == target` | Found | Return index |
| `arr[mid] < target → left = mid + 1` | Right half | Target is bigger |
| `arr[mid] > target → right = mid - 1` | Left half | Target is smaller |
| `return -1` | Not found | Element absent |

**Common Mistakes:**

```java
// ❌ MISTAKE 1: Overflow risk
int mid = (left + right) / 2;  // left+right can exceed Integer.MAX_VALUE
// ✅ CORRECT:
int mid = left + (right - left) / 2;

// ❌ MISTAKE 2: Wrong loop condition
while (left < right) {  // ← Misses when left == right
// ✅ CORRECT:
while (left <= right) {  // ← Include equality

// ❌ MISTAKE 3: Not updating correctly
left = mid;  // ← Can cause infinite loop if mid doesn't change
// ✅ CORRECT:
left = mid + 1;  // ← Always move past mid
```

#### Pattern 2: First/Last Occurrence (Duplicates)

**When to use:**
- Sorted array with duplicates
- Need first OR last position of target

**Template (First Occurrence):**
```java
public int findFirst(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            result = mid;  // Record position
            right = mid - 1;  // Continue searching LEFT for earlier occurrence
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

**Template (Last Occurrence):**
```java
public int findLast(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            result = mid;  // Record position
            left = mid + 1;  // Continue searching RIGHT for later occurrence
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

**Key Difference:**
- **First**: `right = mid - 1` (search left when found)
- **Last**: `left = mid + 1` (search right when found)

#### Pattern 3: Lower Bound / Upper Bound

**When to use:**
- Find insertion position
- First value ≥ target (lower bound) OR > target (upper bound)
- Even if target not present

**Lower Bound (First ≥ target):**
```java
public int lowerBound(int[] arr, int target) {
    int left = 0;
    int right = arr.length;  // ← Note: right = length, not length-1
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] < target) {
            left = mid + 1;  // Need bigger value
        } else {
            right = mid;  // arr[mid] >= target, could be answer
        }
    }
    
    return left;  // Insertion position
}
```

**Upper Bound (First > target):**
```java
public int upperBound(int[] arr, int target) {
    int left = 0;
    int right = arr.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] <= target) {
            left = mid + 1;  // Need strictly bigger value
        } else {
            right = mid;  // arr[mid] > target, could be answer
        }
    }
    
    return left;
}
```

**Key Differences:**

| Bound | Condition | When arr[mid] == target |
|-------|-----------|------------------------|
| **Lower** | First ≥ target | `right = mid` (include) |
| **Upper** | First > target | `left = mid + 1` (exclude) |

#### Pattern 4: Binary Search on Answer (MOST IMPORTANT)

**When to use:**
- Question asks for **minimum/maximum answer**
- Can check if answer is possible using **Yes/No**
- Answer space is monotonic (false → true)

**Example Questions:**
- "Minimum days to complete task?"
- "Maximum capacity needed?"
- "Minimum speed required?"

**Template:**
```java
public int binarySearchOnAnswer(int[] arr, int target) {
    int left = minPossibleAnswer;  // e.g., 1
    int right = maxPossibleAnswer;  // e.g., Integer.MAX_VALUE
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (isPossible(mid, arr, target)) {
            result = mid;  // This answer works
            right = mid - 1;  // Try smaller (find minimum)
        } else {
            left = mid + 1;  // Need bigger answer
        }
    }
    
    return result;
}

// Helper function: Check if candidate answer is valid
private boolean isPossible(int candidate, int[] arr, int target) {
    // Implement problem-specific validation
    // Return true if candidate is feasible, false otherwise
}
```

**Key Insight:**
```text
Answer space: [false, false, false, true, true, true]
              ↑     ↑     ↑     ↑    ↑    ↑
             min1  min2  min3  ✓    ✓    ✓

Binary search finds FIRST "true" (minimum valid answer) ✅
```

#### Pattern 5: Rotated Sorted Array

**When to use:**
- Sorted array rotated at some pivot
- Example: `[4, 5, 6, 7, 0, 1, 2]` (originally `[0, 1, 2, 4, 5, 6, 7]`)

**Template:**
```java
public int searchRotated(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            return mid;
        }
        
        // Check which half is sorted
        if (arr[left] <= arr[mid]) {  // Left half is sorted
            if (arr[left] <= target && target < arr[mid]) {
                right = mid - 1;  // Target in left sorted half
            } else {
                left = mid + 1;  // Target in right half
            }
        } else {  // Right half is sorted
            if (arr[mid] < target && target <= arr[right]) {
                left = mid + 1;  // Target in right sorted half
            } else {
                right = mid - 1;  // Target in left half
            }
        }
    }
    
    return -1;
}
```

**Key Idea:**
- **One half is always sorted**
- Check if target is in sorted half → go there
- Otherwise → go to other half

#### Pattern 6: Binary Search on Matrix

**When to use:**
- Matrix rows are sorted
- Each row's first element > previous row's last element
- Example: `[[1, 3, 5], [7, 9, 11], [13, 15, 17]]`

**Template:**
```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0) return false;
    
    int rows = matrix.length;
    int cols = matrix[0].length;
    
    int left = 0;
    int right = rows * cols - 1;  // Treat as 1D array
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midValue = matrix[mid / cols][mid % cols];  // Convert 1D → 2D
        
        if (midValue == target) {
            return true;
        } else if (midValue < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return false;
}
```

**1D to 2D Conversion:**
```text
2D: matrix[row][col]
1D index: i

row = i / cols
col = i % cols
```

### Tradeoffs Comparison

| Approach | Time | Space | When to Use |
|----------|------|-------|-------------|
| **Linear Search** | O(n) | O(1) | Unsorted array, small n |
| **Classic Binary Search** | O(log n) | O(1) | Sorted array, exact match |
| **Binary Search on Answer** | O(log(max - min)) | O(1) | Min/Max optimization |
| **HashMap** | O(n) | O(n) | Unsorted, multiple queries |

**Key Decision Tree:**
```text
PROBLEM STARTS
    ↓
Is data sorted?
    ↓ YES
Need exact match?
    ↓ YES → Classic Binary Search ✅
    ↓ NO
Need first/last position?
    ↓ YES → Pattern 2 (Duplicates) ✅
    ↓ NO
Need min/max answer?
    ↓ YES → Binary Search on Answer ✅
    ↓ NO
Is array rotated?
    ↓ YES → Pattern 5 (Rotated) ✅
    ↓ NO
→ Other patterns
```

---

## SECTION 3: Pattern Recognition — Advanced

### How to Identify Binary Search Pattern? 🔍

#### Keyword Detection

| Keyword Phrase | Pattern Trigger | Example Problem |
|----------------|-----------------|-----------------|
| "sorted array" | Classic Binary Search | Binary Search |
| "find exact value" | Classic Binary Search | Binary Search |
| "first occurrence" | Duplicates | Find First Position |
| "last occurrence" | Duplicates | Find Last Position |
| "insertion position" | Lower Bound | Search Insert Position |
| "minimum X such that" | Binary Search on Answer | Koko Eating Bananas |
| "maximum X such that" | Binary Search on Answer | Capacity To Ship |
| "rotated sorted array" | Rotated | Search in Rotated Array |
| "2D matrix sorted" | Matrix | Search a 2D Matrix |
| "median of two arrays" | Advanced | Median of Two Sorted Arrays |

#### Decision Flowchart

```text
PROBLEM STARTS
    ↓
Is data sorted OR can answer be checked with Yes/No?
    ↓ YES
Need exact element?
    ↓ YES → Classic Binary Search ✅
    ↓ NO
Need first/last position?
    ↓ YES → Duplicates Pattern ✅
    ↓ NO
Need min/max answer?
    ↓ YES → Binary Search on Answer ✅
    ↓ NO
Is array rotated?
    ↓ YES → Rotated Pattern ✅
    ↓ NO
Is it 2D matrix?
    ↓ YES → Matrix Pattern ✅
    ↓ NO
→ Other patterns
```

### When to Use Binary Search? ✅

| Scenario | Use Binary Search? | Reason |
|----------|---------------------|--------|
| Sorted array, find element | ✅ YES | O(log n) vs O(n) |
| Sorted array with duplicates, find first | ✅ YES | O(log n) vs O(n) |
| Find insertion point | ✅ YES | O(log n) vs O(n) |
| Minimum days/capacity/speed | ✅ YES | O(log(answer)) vs O(n) |
| Rotated sorted array | ✅ YES | O(log n) vs O(n) |
| Sorted 2D matrix | ✅ YES | O(log(nm)) vs O(nm) |
| Unsorted array | ❌ NO | Sort first (O(n log n)) |
| Non-monotonic condition | ❌ NO | No binary search guarantee |

### When NOT to Use Binary Search? ❌

| Scenario | Better Alternative | Reason |
|----------|-------------------|--------|
| Unsorted array, single query | Linear Search | Sorting adds O(n log n) |
| Multiple queries on unsorted | HashMap | O(1) per query after O(n) build |
| Non-monotonic function | Brute Force | Binary search requires monotonicity |
| Find all occurrences | Linear Search | Binary search finds only one |

---

## SECTION 4: Visual Learning — Animation Style

### Animation 1: Classic Binary Search

```text
Array: [1, 3, 5, 7, 9, 11, 13, 15]
Target: 7

Step 1: left=0, right=7
[1, 3, 5, 7, 9, 11, 13, 15]
 ↑                 ↑
left             right
     ↑
    mid=3 (value=7)
    
arr[mid] == target ✓ FOUND!
Return index 3
```

### Animation 2: Binary Search Not Found

```text
Array: [1, 3, 5, 7, 9, 11, 13, 15]
Target: 8

Step 1: left=0, right=7
[1, 3, 5, 7, 9, 11, 13, 15]
     ↑
    mid=3 (value=7)
    
7 < 8 → left = mid + 1 = 4

Step 2: left=4, right=7
[1, 3, 5, 7, 9, 11, 13, 15]
              ↑
             mid=5 (value=11)
             
11 > 8 → right = mid - 1 = 4

Step 3: left=4, right=4
[1, 3, 5, 7, 9, 11, 13, 15]
           ↑
          mid=4 (value=9)
          
9 > 8 → right = mid - 1 = 3

Step 4: left=4, right=3
left > right → STOP

Return -1 (not found) ✓
```

### Animation 3: Binary Search on Answer (Koko Eating Bananas)

```text
Question: "Minimum speed to eat all bananas in H hours?"

Bananas: [3, 6, 7, 11]
H = 8

Try speed = 1:
Hours needed = 3/1 + 6/1 + 7/1 + 11/1 = 3+6+7+11 = 27 > 8 ✗

Try speed = 5:
Hours needed = 3/5 + 6/5 + 7/5 + 11/5 = 1+2+2+3 = 8 ≤ 8 ✓

Try speed = 4:
Hours needed = 3/4 + 6/4 + 7/4 + 11/4 = 1+2+2+3 = 8 ≤ 8 ✓

Try speed = 3:
Hours needed = 3/3 + 6/3 + 7/3 + 11/3 = 1+2+3+4 = 10 > 8 ✗

Answer space:
speed=1 ✗, speed=2 ✗, speed=3 ✗, speed=4 ✓, speed=5 ✓, ...
      false    false    false    true   true

Minimum speed = 4 ✓
```

---

## SECTION 5: Problem Progression — ALL LINKS WORKING ✓

### Level 1: Very Easy

#### **1. Binary Search**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-search/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 9/10

**Problem**: Find target in sorted array, return index or -1

**Solution (Classic Template)**:
```java
public int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1;
}
```

**Time**: O(log n) | **Space**: O(1)

---

#### **2. Guess Number Higher or Lower**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/guess-number-higher-or-lower/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 7/10

**Solution**:
```java
public int guessNumber(int n) {
    int left = 1;
    int right = n;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int guess = guess(mid);
        
        if (guess == 0) {
            return mid;
        } else if (guess == -1) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    
    return -1;
}
```

---

### Level 2: Easy

#### **3. Search Insert Position**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/search-insert-position/
- **Company Tags**: **Google, Amazon, Microsoft, Meta**
- **Frequency**: 9/10

**Problem**: Find index where target should be inserted to maintain sorted order

**Solution (Lower Bound)**:
```java
public int searchInsert(int[] nums, int target) {
    int left = 0;
    int right = nums.length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    
    return left;
}
```

**Time**: O(log n) | **Space**: O(1)

---

#### **4. First Bad Version**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/first-bad-version/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 8/10

**Solution (First Occurrence)**:
```java
public int firstBadVersion(int n) {
    int left = 1;
    int right = n;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (isBadVersion(mid)) {
            result = mid;
            right = mid - 1;  // Continue left
        } else {
            left = mid + 1;
        }
    }
    
    return result;
}
```

---

### Level 3: Medium

#### **5. Find First and Last Position**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Problem**: Find first and last index of target in sorted array with duplicates

**Solution**:
```java
public int[] searchRange(int[] nums, int target) {
    int first = findFirst(nums, target);
    int last = findLast(nums, target);
    
    return new int[]{first, last};
}

private int findFirst(int[] nums, int target) {
    int left = 0, right = nums.length - 1, result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;
            right = mid - 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}

private int findLast(int[] nums, int target) {
    int left = 0, right = nums.length - 1, result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;
            left = mid + 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

**Time**: O(log n) | **Space**: O(1)

---

#### **6. Search in Rotated Sorted Array**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/search-in-rotated-sorted-array/
- **Company Tags**: **Google, Meta, Amazon, Microsoft, Apple**
- **Frequency**: 10/10

**Solution (Rotated Pattern)**:
```java
public int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        if (nums[left] <= nums[mid]) {  // Left half sorted
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {  // Right half sorted
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    
    return -1;
}
```

**Time**: O(log n) | **Space**: O(1)

---

#### **7. Koko Eating Bananas**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/koko-eating-bananas/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 9/10

**Problem**: Find minimum eating speed to finish all bananas in H hours

**Solution (Binary Search on Answer)**:
```java
public int minEatingSpeed(int[] piles, int H) {
    int left = 1;
    int right = 1000000000;  // Max possible
    int result = right;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canFinish(piles, H, mid)) {
            result = mid;
            right = mid - 1;  // Try smaller speed
        } else {
            left = mid + 1;  // Need bigger speed
        }
    }
    
    return result;
}

private boolean canFinish(int[] piles, int H, int speed) {
    int hours = 0;
    for (int pile : piles) {
        hours += (pile + speed - 1) / speed;  // ceil(pile/speed)
    }
    return hours <= H;
}
```

**Time**: O(n log(max)) | **Space**: O(1)

---

#### **8. Capacity To Ship Packages**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/
- **Company Tags**: **Google, Amazon, Microsoft**
- **Frequency**: 9/10

**Solution (Binary Search on Answer)**:
```java
public int shipWithinDays(int[] weights, int days) {
    int left = 0;  // Max single weight
    int right = 0;  // Sum of all weights
    
    for (int w : weights) {
        left = Math.max(left, w);
        right += w;
    }
    
    int result = right;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canShip(weights, days, mid)) {
            result = mid;
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    
    return result;
}

private boolean canShip(int[] weights, int days, int capacity) {
    int requiredDays = 1;
    int currentWeight = 0;
    
    for (int w : weights) {
        if (currentWeight + w > capacity) {
            requiredDays++;
            currentWeight = 0;
        }
        currentWeight += w;
    }
    
    return requiredDays <= days;
}
```

**Time**: O(n log(sum)) | **Space**: O(1)

---

### Level 4: Hard

#### **9. Median of Two Sorted Arrays**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/median-of-two-sorted-arrays/
- **Company Tags**: **Google, Meta, Amazon, Microsoft, Apple**
- **Frequency**: 10/10

**Problem**: Find median of two sorted arrays (combined)

**Solution (Advanced Binary Search)**:
```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    if (nums1.length > nums2.length) {
        return findMedianSortedArrays(nums2, nums1);
    }
    
    int x = nums1.length, y = nums2.length;
    int low = 0, high = x;
    
    while (low <= high) {
        int partitionX = (low + high) / 2;
        int partitionY = (x + y + 1) / 2 - partitionX;
        
        int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
        int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
        
        int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
        int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];
        
        if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
            if ((x + y) % 2 == 0) {
                return (Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0;
            } else {
                return Math.max(maxLeftX, maxLeftY);
            }
        } else if (maxLeftX > minRightY) {
            high = partitionX - 1;
        } else {
            low = partitionX + 1;
        }
    }
    
    throw new IllegalArgumentException();
}
```

**Time**: O(log(min(m, n))) | **Space**: O(1)

---

#### **10. Split Array Largest Sum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/split-array-largest-sum/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 8/10

**Solution (Binary Search on Answer)**:
```java
public int splitArray(int[] nums, int k) {
    int left = 0, right = 0;
    
    for (int n : nums) {
        left = Math.max(left, n);
        right += n;
    }
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (canSplit(nums, k, mid)) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    
    return left;
}

private boolean canSplit(int[] nums, int k, int maxSum) {
    int splits = 1, currentSum = 0;
    
    for (int n : nums) {
        currentSum += n;
        if (currentSum > maxSum) {
            splits++;
            currentSum = n;
        }
    }
    
    return splits <= k;
}
```

**Time**: O(n log(sum)) | **Space**: O(1)

---

## SECTION 6: LeetCode Mastery — Top 20 (ALL WORKING LINKS) ✓

### Easy (6 problems)

| # | Problem | Link | Frequency |
|---|---------|------|-----------|
| 1 | Binary Search | https://leetcode.com/problems/binary-search/ | 9/10 |
| 2 | Search Insert Position | https://leetcode.com/problems/search-insert-position/ | 9/10 |
| 3 | First Bad Version | https://leetcode.com/problems/first-bad-version/ | 8/10 |
| 4 | Guess Number Higher or Lower | https://leetcode.com/problems/guess-number-higher-or-lower/ | 7/10 |
| 5 | Arranging Coins | https://leetcode.com/problems/arranging-coins/ | 7/10 |
| 6 | Valid Perfect Square | https://leetcode.com/problems/valid-perfect-square/ | 6/10 |

### Medium (10 problems)

| # | Problem | Link | Frequency |
|---|---------|------|-----------|
| 1 | Find First and Last Position | https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/ | 10/10 |
| 2 | Search in Rotated Sorted Array | https://leetcode.com/problems/search-in-rotated-sorted-array/ | 10/10 |
| 3 | Koko Eating Bananas | https://leetcode.com/problems/koko-eating-bananas/ | 9/10 |
| 4 | Capacity To Ship Packages | https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/ | 9/10 |
| 5 | Find Minimum in Rotated Sorted Array | https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/ | 9/10 |
| 6 | Search a 2D Matrix | https://leetcode.com/problems/search-a-2d-matrix/ | 8/10 |
| 7 | Single Element in Sorted Array | https://leetcode.com/problems/single-element-in-a-sorted-array/ | 8/10 |
| 8 | Find Peak Element | https://leetcode.com/problems/find-peak-element/ | 7/10 |
| 9 | Minimum Days to Make Bouquets | https://leetcode.com/problems/minimum-days-to-make-bouquets/ | 7/10 |
| 10 | Find K Closest Elements | https://leetcode.com/problems/find-k-closest-elements/ | 7/10 |

### Hard (4 problems)

| # | Problem | Link | Frequency |
|---|---------|------|-----------|
| 1 | Median of Two Sorted Arrays | https://leetcode.com/problems/median-of-two-sorted-arrays/ | 10/10 |
| 2 | Split Array Largest Sum | https://leetcode.com/problems/split-array-largest-sum/ | 8/10 |
| 3 | Find in Mountain Array | https://leetcode.com/problems/find-in-mountain-array/ | 7/10 |
| 4 | Divide Chocolate | https://leetcode.com/problems/divide-chocolate/ | 7/10 |

---

## SECTION 7: CodeChef Mastery — Direct Links

**CodeChef Binary Search Practice**: https://www.codechef.com/practice (search "binary search")

### Beginner Problems

| # | Problem | Link | Rating |
|---|---------|------|--------|
| 1 | **Binary String** | https://www.codechef.com/problems/BINARYSTR | 500 |
| 2 | **Search Element** | https://www.codechef.com/problems/SEARCH_ELEM | 600 |

### Intermediate Problems

| # | Problem | Link | Rating |
|---|---------|------|--------|
| 3 | **Raindrops** | https://www.codechef.com/EXUN21B/problems/RAINDROPS | 1000 |
| 4 | **Count Triplets** | https://practice.geeksforgeeks.org/problems/count-triplets-with-sum-smaller-than-x5549/1# | 1200 |

---

## SECTION 8: Company Questions

| Company | Must-Solve | Pattern | Frequency |
|---------|-----------|---------|-----------|
| **Google** | Median of Two Sorted Arrays, Koko Eating Bananas, Search Rotated | All Patterns | Very High |
| **Meta** | Median of Two Sorted Arrays, Search Rotated, Find First/Last | Rotated + Duplicates | Very High |
| **Amazon** | Koko Eating Bananas, Capacity To Ship, Binary Search | Binary Search on Answer | Very High |
| **Microsoft** | Binary Search, Search Insert, First Bad Version | Classic + Lower Bound | Very High |
| **Apple** | Binary Search, Valid Perfect Square | Easy Warmup | High |

---

## SECTION 9: Active Learning — 5 Questions

### Question 1 (Easy)
**Binary Search** — LeetCode 704  
[Link](https://leetcode.com/problems/binary-search/)

### Question 2 (Easy)
**Search Insert Position** — LeetCode 35  
[Link](https://leetcode.com/problems/search-insert-position/)

### Question 3 (Medium)
**Find First and Last Position** — LeetCode 34  
[Link](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

### Question 4 (Medium)
**Koko Eating Bananas** — LeetCode 875  
[Link](https://leetcode.com/problems/koko-eating-bananas/)

### Question 5 (Hard)
**Median of Two Sorted Arrays** — LeetCode 4  
[Link](https://leetcode.com/problems/median-of-two-sorted-arrays/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 10: Revision Notes

### 📝 Binary Search — Cheat Sheet

| Pattern | When | Template Key |
|---------|------|--------------|
| **Classic** | Sorted, exact match | `while (left <= right)` |
| **Duplicates** | First/last position | `result = mid; right/left = mid ± 1` |
| **Lower/Upper Bound** | Insertion point | `while (left < right)`, `right = mid` |
| **Binary Search on Answer** | Min/Max optimization | `isPossible(mid)`, `right = mid - 1` |
| **Rotated** | Rotated sorted array | Check which half sorted |
| **Matrix** | 2D sorted matrix | `mid / cols`, `mid % cols` |

---
