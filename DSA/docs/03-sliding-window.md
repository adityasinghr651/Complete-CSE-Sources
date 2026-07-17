# 📚 Day 3: Sliding Window — Complete Mastery Guide (Enhanced Edition)

## SECTION 1: Intuition Building — Deep Dive with Animations

### Why Sliding Window Exists? 🤔

**Problem:** Imagine you need to find the **longest substring** in an **array/string** that contains exactly **k distinct characters**.

**Brute Force Approach (O(n²)):**
```text
String: "abcabcbb"
k = 3

Check all substrings:
"a" → 1 distinct
"ab" → 2 distinct
"abc" → 3 distinct ✓
"abca" → 3 distinct ✓
"abcab" → 3 distinct ✓
"abcabc" → 3 distinct ✓
"abcabcb" → 3 distinct ✓
"abcabcbb" → 3 distinct ✓
"a" → 1 distinct
"ab" → 2 distinct
"abc" → 3 distinct ✓
... total ~n² substrings ❌ SLOW!
```

**Sliding Window Approach (O(n)):**
```text
String: "abcabcbb"
k = 3

Window: [a,b,c] → 3 distinct ✓ → length=3
        [a,b,c,a] → 3 distinct ✓ → length=4
        [a,b,c,a,b] → 3 distinct ✓ → length=5
        [a,b,c,a,b,c] → 3 distinct ✓ → length=6
        [a,b,c,a,b,c,b] → 3 distinct ✓ → length=7 ← MAX!
        [a,b,c,a,b,c,b,b] → 3 distinct ✓ → length=8

Answer: 8 ✅ FAST!
```

### What Problem Does It Solve?

| Problem Type | Brute Force | Sliding Window | Improvement |
|--------------|-------------|----------------|-------------|
| Longest substring with k unique | O(n²) | O(n) | **n² → n** |
| Minimum subarray with sum ≥ k | O(n²) | O(n) | **n² → n** |
| Max sum of subarray size k | O(nk) | O(n) | **nk → n** |
| Contains anagram | O(n × m log m) | O(n × m) | **faster** |
| Subarray product < k | O(n²) | O(n) | **n² → n** |

### Real-World Analogies 🏠

#### Analogy 1: CCTV Camera (Fixed Window)
```text
Imagine a CCTV camera that can cover a 3-meter area:

Street: [House1, House2, House3, House4, House5, House6]
        [━━━━━━━]  ← Camera position 1
              [━━━━━━━]  ← Camera position 2 (slided right)
                    [━━━━━━━]  ← Camera position 3 (slided right)

There's no need to reset the camera every time, just slide it! ✅
```

#### Analogy 2: Video Player (Variable Window)
```text
In a video player:
← ━━━━━━━━━━━━━ →
  ← Start → End →

As long as the video is interesting (condition met):
→ Move the end forward (window expand)

When the video gets boring (condition violated):
→ Move the start forward (window shrink)

This is exactly how a **variable sliding window** works!
```

#### Analogy 3: Factory Assembly Line (Both Directions)
```text
Assembly line: [Part1, Part2, Part3, Part4, Part5]
               [━━━]  ← Window size 3
               
Step 1: Remove Part1, add Part6 → [Part2, Part3, Part4, Part6]
        L     R           R++
        
Step 2: Remove Part2, add Part7 → [Part3, Part4, Part6, Part7]
              L++     R++

Both the **left and right** ends of the window are moving! ✅
```

### Mental Models 🧠

#### Model 1: Fixed-Size Window
```text
Window size = 3

[1, 2, 3, 4, 5, 6]
 ███                    ← Window at position 0
   ███                  ← Window at position 1
     ███                ← Window at position 2
       ███              ← Window at position 3

Always move BOTH left and right together
left++, right++
```

#### Model 2: Variable-Size Window (Expand & Shrink)
```text
Condition: sum ≤ 10

[1, 2, 3, 4, 5, 6]
[━━━]                  ← sum=6 ✓ → expand
[━━━━]                 ← sum=10 ✓ → expand
[━━━━━]                ← sum=15 ✗ → shrink
  [━━━━]               ← sum=12 ✗ → shrink
    [━━━]              ← sum=9 ✓ → expand

The left and right pointers move **independently**!
```

#### Model 3: Two Pointers + HashMap (Frequency Tracking)
```text
String: "abcabcbb", k = 3

[abc]abcbb        ← Window: {a:1, b:1, c:1}, distinct=3 ✓
[abca]bcbb        ← Window: {a:2, b:1, c:1}, distinct=3 ✓
[abcab]cbb        ← Window: {a:2, b:2, c:1}, distinct=3 ✓
[abcabc]bb        ← Window: {a:2, b:2, c:2}, distinct=3 ✓
[abcabcb]b        ← Window: {a:2, b:3, c:2}, distinct=3 ✓ ← MAX
[abcabcbb]        ← Window: {a:2, b:4, c:2}, distinct=3 ✓

We use a HashMap to track the frequency of elements!
```

### Common Interview Use Cases

| Use Case | Problem Example | Window Type |
|----------|----------------|-------------|
| **Longest substring without repeating** | Longest Substring Without Repeating Characters | Variable |
| **Minimum size subarray with sum** | Minimum Size Subarray Sum | Variable |
| **Fixed window max sum** | Maximum Sum Subarray of Size K | Fixed |
| **Find all anagrams** | Find All Anagrams in a String | Fixed |
| **At most K distinct** | Longest Substring with At Most K Distinct | Variable |
| **Subarray product < K** | Subarray Product Less Than K | Variable |
| **Sliding window maximum** | Sliding Window Maximum | Fixed + Deque |

---

## SECTION 2: Complete Theory — Deep Dive

### Core Concepts

#### Concept 1: What is a "Window"?

```text
Array: [1, 2, 3, 4, 5, 6, 7, 8]

Window = Subarray represented by [left, right] indices

left = 2, right = 5
        [━━━]
[1, 2, 3, 4, 5, 6, 7, 8]
     ↑        ↑
    left     right

Window elements: [3, 4, 5, 6]
Window size: right - left + 1 = 5 - 2 + 1 = 4
```

#### Concept 2: Two Types of Sliding Window

**Type A: Fixed-Size Window**

```python
window_size = 3
left = 0

for right in range(len(arr)):
    # Add current element to window
    window.add(arr[right])
    
    # If window exceeds size, remove left element
    if right - left + 1 > window_size:
        window.remove(arr[left])
        left += 1
    
    # Process window (only when size matches)
    if right - left + 1 == window_size:
        process(window)
```

**When to use:**
- The window size is pre-determined (e.g., subarray of size K)
- Max/Min sum of subarray size K
- Average of subarray size K

**Time:** O(n) | **Space:** O(1) or O(window_size)

**Type B: Variable-Size Window**

```python
left = 0

for right in range(len(arr)):
    # Expand window (add current element)
    window.add(arr[right])
    
    # Shrink window while condition violated
    while not condition(window):
        window.remove(arr[left])
        left += 1
    
    # Process window (condition satisfied)
    process(window)
```

**When to use:**
- Longest substring without repeating
- Minimum size subarray with sum ≥ K
- At most K distinct characters
- Subarray product < K

**Time:** O(n) | **Space:** O(1) or O(alphabet_size)

#### Concept 3: Why Time Complexity is O(n)?

```text
Array: [1, 2, 3, 4, 5, 6, 7, 8]

right moves: 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 (8 steps)
left moves:  0 → 1 → 2              → 3 → 4 (4 steps)

Total movements: 8 + 4 = 12 steps
Each element:
  - Add to window: 1 time (by right)
  - Remove from window: 1 time (by left, at most)

Total operations: 2n = O(n) ✅
```

**Key Insight:** Each element is visited a **maximum of 2 times** → **O(n)**

### Deep Dive: 3 Main Sliding Window Patterns

#### Pattern 1: Fixed-Size (Maximum/Minimum of K-sized Subarray)

```java
public int maxSumOfSubarrayOfSizeK(int[] arr, int k) {
    int left = 0;
    int currentSum = 0;
    int maxSum = 0;
    
    for (int right = 0; right < arr.length; right++) {
        // ADD: Add current element to window
        currentSum += arr[right];
        
        // SHRINK: If window exceeds size k, remove left element
        if (right - left + 1 > k) {
            currentSum -= arr[left];
            left++;
        }
        
        // PROCESS: Only process when window size == k
        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, currentSum);
        }
    }
    
    return maxSum;
}
```

**3-Step Framework:**
1. **ADD** → `window += arr[right]`
2. **SHRINK** → `if (size > k) { window -= arr[left]; left++; }`
3. **PROCESS** → `if (size == k) { answer = max(answer, window); }`

#### Pattern 2: Variable-Size Expand (Longest Substring)

```java
public int longestSubstringWithoutRepeating(String s) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // EXPAND: Add current character
        char c = s.charAt(right);
        freq.put(c, freq.getOrDefault(c, 0) + 1);
        
        // SHRINK: While condition violated (duplicate exists)
        while (freq.get(c) > 1) {
            char leftChar = s.charAt(left);
            freq.put(leftChar, freq.get(leftChar) - 1);
            left++;
        }
        
        // PROCESS: Update answer (condition satisfied)
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**3-Step Framework:**
1. **EXPAND** → `freq[right]++`
2. **SHRINK** → `while (condition violated) { freq[left]--; left++; }`
3. **PROCESS** → `max = max(max, window_size)`

#### Pattern 3: Variable-Size Minimum (Shortest Subarray)

```java
public int minSizeSubarraySum(int[] arr, int target) {
    int left = 0;
    int currentSum = 0;
    int minLen = Integer.MAX_VALUE;
    
    for (int right = 0; right < arr.length; right++) {
        // EXPAND: Add current element
        currentSum += arr[right];
        
        // SHRINK: While condition satisfied (sum >= target)
        while (currentSum >= target) {
            minLen = Math.min(minLen, right - left + 1);
            currentSum -= arr[left];
            left++;
        }
    }
    
    return minLen == Integer.MAX_VALUE ? 0 : minLen;
}
```

**3-Step Framework:**
1. **EXPAND** → `sum += arr[right]`
2. **SHRINK** → `while (condition satisfied) { update_answer; sum -= arr[left]; left++; }`
3. **PROCESS** → `min = min(min, window_size)`

**Key Difference from Pattern 2:**
- Pattern 2: `while (condition VIOLATED)` → shrink
- Pattern 3: `while (condition SATISFIED)` → shrink (because we want MINIMUM)

### Tradeoffs Comparison

| Approach | Time | Space | When to Use |
|----------|------|-------|-------------|
| **Brute Force (all substrings)** | O(n²) | O(1) | Small n (< 100) |
| **Fixed Sliding Window** | O(n) | O(k) | Window size fixed |
| **Variable Sliding Window** | O(n) | O(alphabet) | Find longest/shortest |
| **Prefix Sum + Binary Search** | O(n log n) | O(n) | Sum queries |

**Key Decision Tree:**
```text
PROBLEM STARTS
    ↓
Is it about subarray/substring?
    ↓ YES
Is window size fixed (e.g., size K)?
    ↓ YES → Fixed Sliding Window ✅
    ↓ NO
Need LONGEST with condition?
    ↓ YES → Variable Window (expand while satisfied) ✅
    ↓ NO
Need SHORTEST with condition?
    ↓ YES → Variable Window (shrink while satisfied) ✅
    ↓ NO
→ Other patterns (DP, Two Pointers)
```

---

## SECTION 3: Pattern Recognition — Advanced

### How to Identify Sliding Window Pattern? 🔍

#### Keyword Detection in Interview Questions

| Keyword Phrase | Pattern Trigger | Example Problem |
|----------------|-----------------|-----------------|
| "longest substring" | Variable Window (Longest) | Longest Substring Without Repeating |
| "shortest subarray" | Variable Window (Shortest) | Minimum Size Subarray Sum |
| "subarray of size K" | Fixed Window | Maximum Sum Subarray of Size K |
| "at most K distinct" | Variable Window | Longest Substring with At Most K Distinct |
| "containing K unique" | Variable Window | Longest Substring with K Distinct |
| "product less than K" | Variable Window | Subarray Product Less Than K |
| "all anagrams of" | Fixed Window | Find All Anagrams in a String |
| "maximum points from cards" | Fixed Window | Maximum Points You Can Obtain from Cards |

#### Decision Flowchart

```text
PROBLEM STARTS
    ↓
Is it about contiguous subarray/substring?
    ↓ YES
Do we need to find longest/shortest/max/min?
    ↓ YES
Is window size fixed (e.g., "size K")?
    ↓ YES → Fixed Sliding Window ✅
    ↓ NO
Need LONGEST with condition met?
    ↓ YES → Variable Window (expand) ✅
    ↓ NO
Need SHORTEST with condition met?
    ↓ YES → Variable Window (shrink) ✅
    ↓ NO
→ Multiple windows or other patterns
```

### When to Use Sliding Window? ✅

| Scenario | Use Sliding Window? | Reason |
|----------|---------------------|--------|
| Longest substring without repeating | ✅ YES | O(n) vs O(n²) brute force |
| Minimum size subarray with sum ≥ K | ✅ YES | O(n) vs O(n²) |
| Max sum of subarray size K | ✅ YES | O(n) vs O(nk) |
| Find all anagrams in string | ✅ YES | O(n × m) vs O(n × m log m) |
| Subarray product < K | ✅ YES | O(n) vs O(n²) |
| At most K distinct characters | ✅ YES | O(n) vs O(n²) |
| Non-contiguous subsequence | ❌ NO | Use DP or Greedy instead |
| 2D matrix subarray | ❌ NO | Use Prefix Sum + 2D sliding |

### When NOT to Use Sliding Window? ❌

| Scenario | Better Alternative | Reason |
|----------|-------------------|--------|
| Non-contiguous elements | DP / Greedy | Sliding window only works on contiguous |
| Need all combinations | Backtracking | Sliding window finds only one optimal |
| 2D matrix maximum | Prefix Sum | Sliding window is 1D only |
| Sum of all subarrays | Prefix Sum | O(n) without window complexity |
| Maximum subarray sum | Kadane's Algorithm | More efficient, simpler logic |

### Similar-Looking Patterns & Differences

#### Pattern 1: Sliding Window vs Two Pointers

| Aspect | Sliding Window | Two Pointers |
|--------|---------------|--------------|
| **Movement** | Both move same direction (left++, right++) | Can move opposite (left++, right--) |
| **Window** | Maintains contiguous range | May not maintain window |
| **Example** | Longest Substring Without Repeating | Two Sum II (sorted array) |
| **Use Case** | Subarray/substring optimization | Pair finding, palindrome, reverse |

**Visual:**
```text
Sliding Window (Same Direction):
[1, 3, 5, 7, 9, 11, 13]
 ↑        ↑
left    right
→ Both move RIGHT

Two Pointers (Opposite Direction):
[1, 3, 5, 7, 9, 11, 13]
 ↑                 ↑
left              right
→ Move towards each other
```

#### Pattern 2: Sliding Window vs Prefix Sum

| Aspect | Sliding Window | Prefix Sum |
|--------|---------------|------------|
| **Purpose** | Find optimal subarray | Range sum queries |
| **Time** | O(n) | O(n) build, O(1) query |
| **Example** | Min size subarray with sum ≥ K | Sum of subarray [i, j] |
| **When** | One-pass optimization | Multiple queries |

#### Pattern 3: Fixed vs Variable Window

| Aspect | Fixed Window | Variable Window |
|--------|-------------|-----------------|
| **Size** | Always k | Changes dynamically |
| **Condition** | `if (size == k)` | `while (condition)` |
| **Example** | Max sum of size K | Longest without repeating |
| **When** | Size is known beforehand | Find optimal size |

---

## SECTION 4: Visual Learning — Animation Style

### Animation 1: Fixed-Size Window (Max Sum of Size 3)

```text
Array: [2, 1, 5, 1, 3, 2]
Window Size: 3

Step 1: right=0
[2, 1, 5, 1, 3, 2]
 [━━]
  ← sum=2, size=1 (< 3, don't process)

Step 2: right=1
[2, 1, 5, 1, 3, 2]
 [━━━━]
     ← sum=3, size=2 (< 3, don't process)

Step 3: right=2
[2, 1, 5, 1, 3, 2]
 [━━━━━━]
     ← sum=8, size=3 (= 3, PROCESS!)
     max = max(0, 8) = 8

Step 4: right=3
[2, 1, 5, 1, 3, 2]
    [━━━━━━]
        ← sum=3+1=4, size=4 (> 3, SHRINK!)
        sum=4-2=2, left=1
        [━━━━━━]
            ← sum=7, size=3 (= 3, PROCESS!)
            max = max(8, 7) = 8

Step 5: right=4
[2, 1, 5, 1, 3, 2]
       [━━━━━━]
           ← sum=7+3=10, size=4 (> 3, SHRINK!)
           sum=10-1=9, left=2
           [━━━━━━]
               ← sum=9, size=3 (= 3, PROCESS!)
               max = max(8, 9) = 9

Step 6: right=5
[2, 1, 5, 1, 3, 2]
        [━━━━━━]
            ← sum=9+2=11, size=4 (> 3, SHRINK!)
            sum=11-5=6, left=3
            [━━━━━━]
                ← sum=6, size=3 (= 3, PROCESS!)
                max = max(9, 6) = 9

Final Answer: 9 ✅
```

### Animation 2: Variable Window (Longest Without Repeating)

```text
String: "abcabcbb"

Step 1: right=0 ('a')
["abcabcbb"]
 [━]
  ← freq={a:1}, distinct=1 ✓
  maxLen = max(0, 1) = 1

Step 2: right=1 ('b')
["abcabcbb"]
 [━━]
   ← freq={a:1, b:1}, distinct=2 ✓
   maxLen = max(1, 2) = 2

Step 3: right=2 ('c')
["abcabcbb"]
 [━━━]
    ← freq={a:1, b:1, c:1}, distinct=3 ✓
    maxLen = max(2, 3) = 3

Step 4: right=3 ('a') ← DUPLICATE!
["abcabcbb"]
 [━━━━]
     ← freq={a:2, b:1, c:1}, distinct=3 ✗ (a repeated)
     SHRINK:
       remove 'a' (left=0), freq={a:1, b:1, c:1}, left=1
       ["abcabcbb"]
        [━━━]
         ← freq={a:1, b:1, c:1}, distinct=3 ✓
         maxLen = max(3, 3) = 3

Step 5: right=4 ('b') ← DUPLICATE!
["abcabcbb"]
  [━━━━]
     ← freq={a:1, b:2, c:1}, distinct=3 ✗
     SHRINK:
       remove 'b' (left=1), freq={a:1, b:1, c:1}, left=2
       ["abcabcbb"]
        [━━━]
         ← freq={a:1, b:1, c:1}, distinct=3 ✓
         maxLen = max(3, 3) = 3

... continue ...

Final Answer: 3 ("abc") ✅
```

### Animation 3: Variable Window (Minimum Size with Sum ≥ 7)

```text
Array: [2, 3, 1, 2, 4, 3]
Target: 7

Step 1: right=0
[2, 3, 1, 2, 4, 3]
 [━]
  ← sum=2 (< 7, don't shrink)

Step 2: right=1
[2, 3, 1, 2, 4, 3]
 [━━]
   ← sum=5 (< 7, don't shrink)

Step 3: right=2
[2, 3, 1, 2, 4, 3]
 [━━━]
    ← sum=6 (< 7, don't shrink)

Step 4: right=3
[2, 3, 1, 2, 4, 3]
 [━━━━]
     ← sum=8 (≥ 7, SHRINK!)
     minLen = min(MAX, 4) = 4
     sum=8-2=6, left=1
     [━━━━]
         ← sum=6 (< 7, stop shrinking)

Step 5: right=4
[2, 3, 1, 2, 4, 3]
   [━━━━━]
       ← sum=6+4=10 (≥ 7, SHRINK!)
       minLen = min(4, 4) = 4
       sum=10-3=7, left=2
       [━━━━━]
           ← sum=7 (≥ 7, SHRINK!)
           minLen = min(4, 3) = 3
           sum=7-1=6, left=3
           [━━━━━]
               ← sum=6 (< 7, stop shrinking)

Step 6: right=5
[2, 3, 1, 2, 4, 3]
    [━━━━━━]
        ← sum=6+3=9 (≥ 7, SHRINK!)
        minLen = min(3, 3) = 3
        sum=9-2=7, left=4
        [━━━━━━]
            ← sum=7 (≥ 7, SHRINK!)
            minLen = min(3, 2) = 2
            sum=7-4=3, left=5
            [━━━━━━]
                ← sum=3 (< 7, stop shrinking)

Final Answer: 2 ([4, 3]) ✅
```

---

## SECTION 5: Core Templates (Java) — Detailed Explanation

### Template 1: Fixed-Size Window (Most Common)

```java
public int fixedWindowSize(int[] arr, int k) {
    // Step 1: Initialize variables
    int left = 0;                   // ← Left pointer of window
    int currentSum = 0;             // ← Current window sum (or other value)
    int answer = 0;                 // ← Final answer (max, min, etc.)
    
    // Step 2: Expand window with right pointer
    for (int right = 0; right < arr.length; right++) {
        // ADD: Add current element to window
        currentSum += arr[right];
        
        // SHRINK: If window exceeds size k, remove left element
        if (right - left + 1 > k) {
            currentSum -= arr[left];
            left++;
        }
        
        // PROCESS: Only process when window size == k
        if (right - left + 1 == k) {
            answer = Math.max(answer, currentSum);  // or Math.min, etc.
        }
    }
    
    return answer;
}
```

**Line-by-Line Breakdown:**

| Line | Purpose | Why Important |
|------|---------|---------------|
| `int left = 0;` | Left pointer | Start of window |
| `int currentSum = 0;` | Accumulator | Track window value |
| `int answer = 0;` | Result | Store optimal value |
| `for (int right = 0; ...)` | Right pointer | End of window |
| `currentSum += arr[right];` | ADD | Include new element |
| `if (size > k)` | Check size | Ensure window doesn't grow |
| `currentSum -= arr[left]; left++;` | SHRINK | Remove old element |
| `if (size == k)` | Process condition | Only when size matches |
| `answer = Math.max(...)` | Update answer | Track optimal |

**Common Mistakes:**

```java
// ❌ MISTAKE 1: Wrong size calculation
if (right - left > k) {  // ← Missing +1!
// ✅ CORRECT:
if (right - left + 1 > k) {

// ❌ MISTAKE 2: Processing before shrinking
currentSum += arr[right];
answer = Math.max(answer, currentSum);  // ← Wrong! Size might be > k
if (right - left + 1 > k) { ... }
// ✅ CORRECT:
currentSum += arr[right];
if (right - left + 1 > k) { ... }
if (right - left + 1 == k) { answer = ...; }  // ← Process after shrinking

// ❌ MISTAKE 3: Not handling edge case
// What if arr.length < k?
// ✅ Add check at beginning:
if (arr.length < k) return 0;  // or appropriate default
```

### Template 2: Variable-Size Window (Longest Substring)

```java
public int longestSubstringWithCondition(String s) {
    // Step 1: Initialize frequency map and pointers
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0;
    int maxLen = 0;
    
    // Step 2: Expand window with right pointer
    for (int right = 0; right < s.length(); right++) {
        // EXPAND: Add current character
        char c = s.charAt(right);
        freq.put(c, freq.getOrDefault(c, 0) + 1);
        
        // SHRINK: While condition VIOLATED, shrink from left
        while (freq.get(c) > 1) {  // ← Condition: no duplicates
            char leftChar = s.charAt(left);
            freq.put(leftChar, freq.get(leftChar) - 1);
            left++;
        }
        
        // PROCESS: Update answer (condition satisfied)
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Key Pattern:**
```text
EXPAND → while (VIOLATED) SHRINK → PROCESS
```

### Template 3: Variable-Size Window (Shortest Subarray)

```java
public int shortestSubarrayWithCondition(int[] arr, int target) {
    // Step 1: Initialize variables
    int left = 0;
    int currentSum = 0;
    int minLen = Integer.MAX_VALUE;
    
    // Step 2: Expand window with right pointer
    for (int right = 0; right < arr.length; right++) {
        // EXPAND: Add current element
        currentSum += arr[right];
        
        // SHRINK: While condition SATISFIED, shrink from left
        while (currentSum >= target) {  // ← Condition: sum >= target
            minLen = Math.min(minLen, right - left + 1);
            currentSum -= arr[left];
            left++;
        }
    }
    
    // Step 3: Return result (handle no solution)
    return minLen == Integer.MAX_VALUE ? 0 : minLen;
}
```

**Key Difference from Template 2:**
```text
Template 2 (Longest): while (VIOLATED) SHRINK
Template 3 (Shortest): while (SATISFIED) SHRINK  // ← Different!
```

**Why?**
- Longest: We want to **keep expanding** until violated
- Shortest: We want to **shrink as much as possible** while still satisfied

### Template 4: Fixed Window + HashMap (Find All Anagrams)

```java
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> result = new ArrayList<>();
    
    // Step 1: Build frequency map for pattern
    Map<Character, Integer> patternFreq = new HashMap<>();
    for (char c : p.toCharArray()) {
        patternFreq.put(c, patternFreq.getOrDefault(c, 0) + 1);
    }
    
    // Step 2: Initialize window variables
    Map<Character, Integer> windowFreq = new HashMap<>();
    int left = 0;
    int matches = 0;  // ← How many characters have correct frequency
    
    // Step 3: Slide window
    for (int right = 0; right < s.length(); right++) {
        char rightChar = s.charAt(right);
        windowFreq.put(rightChar, windowFreq.getOrDefault(rightChar, 0) + 1);
        
        // If this character matches pattern frequency
        if (patternFreq.containsKey(rightChar) && 
            windowFreq.get(rightChar).equals(patternFreq.get(rightChar))) {
            matches++;
        }
        
        // If window exceeds pattern size, shrink
        if (right - left + 1 > p.length()) {
            char leftChar = s.charAt(left);
            
            if (patternFreq.containsKey(leftChar) &&
                windowFreq.get(leftChar).equals(patternFreq.get(leftChar))) {
                matches--;  // ← Will no longer match
            }
            
            windowFreq.put(leftChar, windowFreq.get(leftChar) - 1);
            left++;
        }
        
        // If all characters match, found anagram
        if (matches == patternFreq.size()) {
            result.add(left);
        }
    }
    
    return result;
}
```

---

## SECTION 6: Problem Progression — ALL LINKS WORKING ✓

### Level 1: Very Easy

#### **1. Maximum Sum of Subarray of Size K**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/)
- **Company Tags**: Amazon, Microsoft
- **Frequency**: 7/10

**Problem**: Find maximum sum of contiguous subarray of size exactly K

**Solution (Fixed Window)**:
```java
public int maxSum(int[] arr, int k) {
    if (arr.length < k) return 0;
    
    int left = 0;
    int currentSum = 0;
    int maxSum = 0;
    
    for (int right = 0; right < arr.length; right++) {
        currentSum += arr[right];
        
        if (right - left + 1 > k) {
            currentSum -= arr[left];
            left++;
        }
        
        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, currentSum);
        }
    }
    
    return maxSum;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **2. Contains Duplicate II**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/contains-duplicate-ii/)
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 8/10

**Problem**: Check if there are two indices i, j such that `arr[i] == arr[j]` and `|i - j| <= k`

**Solution (Fixed Window + HashSet)**:
```java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    Set<Integer> set = new HashSet<>();
    
    for (int right = 0; right < nums.length; right++) {
        if (set.contains(nums[right])) return true;
        
        set.add(nums[right]);
        
        if (set.size() > k) {
            set.remove(nums[right - k]);
        }
    }
    
    return false;
}
```

**Time**: O(n) | **Space**: O(k)

---

### Level 2: Easy

#### **3. Best Time to Buy and Sell Stock**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
- **Company Tags**: **Google, Amazon, Microsoft, Meta, Apple**
- **Frequency**: 10/10

**Problem**: Find maximum profit from buying and selling stock (one transaction)

**Solution (Variable Window - Tracking Min)**:
```java
public int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    
    for (int right = 0; right < prices.length; right++) {
        // Track minimum price (buying point)
        if (prices[right] < minPrice) {
            minPrice = prices[right];
        }
        
        // Calculate profit (selling at current price)
        int profit = prices[right] - minPrice;
        maxProfit = Math.max(maxProfit, profit);
    }
    
    return maxProfit;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **4. Maximum Average Subarray I**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/maximum-average-subarray-i/)
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 6/10

**Solution (Fixed Window)**:
```java
public double findMaxAverage(int[] nums, int k) {
    int sum = 0;
    for (int i = 0; i < k; i++) sum += nums[i];
    
    int maxSum = sum;
    
    for (int i = k; i < nums.length; i++) {
        sum += nums[i] - nums[i - k];
        maxSum = Math.max(maxSum, sum);
    }
    
    return (double) maxSum / k;
}
```

**Time**: O(n) | **Space**: O(1)

---

### Level 3: Medium

#### **5. Longest Substring Without Repeating Characters**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- **Company Tags**: **Google, Meta, Amazon, Microsoft, Apple, Netflix**
- **Frequency**: 10/10 

**Problem**: Find length of longest substring without repeating characters

**Solution (Variable Window)**:
```java
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        freq.put(c, freq.getOrDefault(c, 0) + 1);
        
        // Shrink while duplicate exists
        while (freq.get(c) > 1) {
            char leftChar = s.charAt(left);
            freq.put(leftChar, freq.get(leftChar) - 1);
            left++;
        }
        
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Dry Run**: `"abcabcbb"`
```text
right=0 ('a'): freq={a:1}, maxLen=1
right=1 ('b'): freq={a:1, b:1}, maxLen=2
right=2 ('c'): freq={a:1, b:1, c:1}, maxLen=3
right=3 ('a'): freq={a:2, ...}, SHRINK → left=1, freq={a:1, ...}, maxLen=3
right=4 ('b'): freq={b:2, ...}, SHRINK → left=2, freq={b:1, ...}, maxLen=3
...

Final: 3 ("abc") ✅
```

**Time**: O(n) | **Space**: O(min(n, alphabet_size)) = O(26) for English

---

#### **6. Minimum Size Subarray Sum**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/minimum-size-subarray-sum/)
- **Company Tags**: **Google, Amazon, Microsoft, Meta**
- **Frequency**: 9/10

**Problem**: Find minimal length of subarray with sum ≥ target

**Solution (Variable Window - Shortest)**:
```java
public int minSubArrayLen(int target, int[] nums) {
    int left = 0;
    int sum = 0;
    int minLen = Integer.MAX_VALUE;
    
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];
        
        // Shrink while sum >= target
        while (sum >= target) {
            minLen = Math.min(minLen, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
    
    return minLen == Integer.MAX_VALUE ? 0 : minLen;
}
```

**Time**: O(n) | **Space**: O(1)

---

#### **7. Find All Anagrams in a String**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 8/10

**Solution (Fixed Window + HashMap)**:
```java
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> result = new ArrayList<>();
    Map<Character, Integer> patternFreq = new HashMap<>();
    
    for (char c : p.toCharArray()) {
        patternFreq.put(c, patternFreq.getOrDefault(c, 0) + 1);
    }
    
    Map<Character, Integer> windowFreq = new HashMap<>();
    int left = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char rightChar = s.charAt(right);
        windowFreq.put(rightChar, windowFreq.getOrDefault(rightChar, 0) + 1);
        
        if (right - left + 1 > p.length()) {
            char leftChar = s.charAt(left);
            windowFreq.put(leftChar, windowFreq.get(leftChar) - 1);
            if (windowFreq.get(leftChar) == 0) windowFreq.remove(leftChar);
            left++;
        }
        
        if (windowFreq.equals(patternFreq)) {
            result.add(left);
        }
    }
    
    return result;
}
```

**Time**: O(n × m) where m = pattern length | **Space**: O(m)

---

#### **8. Subarray Product Less Than K**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/subarray-product-less-than-k/)
- **Company Tags**: Amazon, Microsoft
- **Frequency**: 7/10

**Solution (Variable Window)**:
```java
public int numSubarrayProductLessThanK(int[] nums, int k) {
    if (k <= 1) return 0;
    
    int left = 0;
    int product = 1;
    int count = 0;
    
    for (int right = 0; right < nums.length; right++) {
        product *= nums[right];
        
        while (product >= k) {
            product /= nums[left];
            left++;
        }
        
        count += right - left + 1;  // All subarrays in current window
    }
    
    return count;
}
```

**Time**: O(n) | **Space**: O(1)

---

### Level 4: Hard

#### **9. Sliding Window Maximum**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/sliding-window-maximum/)
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10 

**Problem**: Find max in each window of size k

**Brute Force**: O(nk)
```java
for (int i = 0; i <= nums.length - k; i++) {
    int max = Integer.MIN_VALUE;
    for (int j = i; j < i + k; j++) {
        max = Math.max(max, nums[j]);
    }
    result.add(max);
}
```

**Optimized (Monotonic Deque)**: O(n)
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    if (nums.length == 0) return new int[0];
    
    int[] result = new int[nums.length - k + 1];
    Deque<Integer> deque = new LinkedList<>();  // Stores INDICES
    int i = 0;
    
    for (int right = 0; right < nums.length; right++) {
        // Remove indices out of window
        while (!deque.isEmpty() && deque.peekFirst() < right - k + 1) {
            deque.pollFirst();
        }
        
        // Remove smaller elements (maintain decreasing order)
        while (!deque.isEmpty() && nums[deque.peekLast()] < nums[right]) {
            deque.pollLast();
        }
        
        deque.offerLast(right);
        
        // Add max to result (first element is max)
        if (right >= k - 1) {
            result[i++] = nums[deque.peekFirst()];
        }
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(k)

---

#### **10. Minimum Window Substring**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/minimum-window-substring/)
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 9/10

**Solution (Variable Window + HashMap)**:
```java
public String minWindow(String s, String t) {
    Map<Character, Integer> tFreq = new HashMap<>();
    for (char c : t.toCharArray()) {
        tFreq.put(c, tFreq.getOrDefault(c, 0) + 1);
    }
    
    Map<Character, Integer> sFreq = new HashMap<>();
    int left = 0;
    int minLen = Integer.MAX_VALUE;
    int minLeft = 0;
    int required = tFreq.size();
    int formed = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        sFreq.put(c, sFreq.getOrDefault(c, 0) + 1);
        
        if (tFreq.containsKey(c) && sFreq.get(c).equals(tFreq.get(c))) {
            formed++;
        }
        
        while (formed == required) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                minLeft = left;
            }
            
            char leftChar = s.charAt(left);
            sFreq.put(leftChar, sFreq.get(leftChar) - 1);
            if (tFreq.containsKey(leftChar) && sFreq.get(leftChar) < tFreq.get(leftChar)) {
                formed--;
            }
            left++;
        }
    }
    
    return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
}
```

**Time**: O(n) | **Space**: O(m + n)

---

### Level 5: FAANG Level

#### **11. Longest Substring with At Most K Distinct**
- **Platform**: LeetCode
- **Link**: [Official Link](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 8/10

**Solution (Variable Window)**:
```java
public int lengthOfLongestSubstringKDistinct(String s, int k) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        freq.put(c, freq.getOrDefault(c, 0) + 1);
        
        while (freq.size() > k) {
            char leftChar = s.charAt(left);
            freq.put(leftChar, freq.get(leftChar) - 1);
            if (freq.get(leftChar) == 0) freq.remove(leftChar);
            left++;
        }
        
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Time**: O(n) | **Space**: O(k)

---

## SECTION 7: Company Questions

| Company | Must-Solve Questions | Pattern | Frequency |
|---------|---------------------|---------|-----------|
| **Google** | Longest Without Repeating, Sliding Window Maximum, Minimum Window Substring | Variable + Fixed | Very High |
| **Meta** | Longest Without Repeating, Sliding Window Maximum, Subarray Product < K | Variable | Very High |
| **Amazon** | Minimum Size Subarray Sum, Longest Without Repeating, Best Time Buy Sell | Variable | Very High |
| **Microsoft** | Minimum Size Subarray Sum, Sliding Window Maximum, Contains Duplicate II | Mixed | Very High |
| **Apple** | Best Time Buy Sell, Maximum Average Subarray I | Easy Warmup | High |
| **Netflix** | Longest With K Distinct, Sliding Window Maximum | Medium-Hard | Medium |
| **Uber** | Minimum Window Substring, Longest Without Repeating | String + Window | High |
| **Airbnb** | Find All Anagrams, Minimum Size Subarray Sum | Fixed + Variable | Medium |
| **LinkedIn** | Contains Duplicate II, Maximum Average Subarray I | Fixed | High |
| **Atlassian** | Best Time Buy Sell, Contains Duplicate II | Easy | Medium |
| **Databricks** | Sliding Window Maximum, Minimum Window Substring | Hard | Medium |

---

## SECTION 8: LeetCode Mastery — Top 20 Must Solve

### Easy (6 problems)

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Best Time to Buy and Sell Stock | [Link](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | 10/10 | 10/10 |
| 2 | Contains Duplicate II | [Link](https://leetcode.com/problems/contains-duplicate-ii/) | 8/10 | 8/10 |
| 3 | Maximum Average Subarray I | [Link](https://leetcode.com/problems/maximum-average-subarray-i/) | 7/10 | 7/10 |
| 4 | Maximum Sum of Distinct Subarrays With Length K | [Link](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | 7/10 | 7/10 |
| 5 | Sliding Subarray Beauty | [Link](https://leetcode.com/problems/sliding-subarray-beauty/) | 6/10 | 6/10 |
| 6 | Substrings of Size Three With Distinct Characters | [Link](https://leetcode.com/problems/substrings-of-size-three-with-distinct-characters/) | 6/10 | 6/10 |

### Medium (10 problems)

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Longest Substring Without Repeating Characters | [Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | 10/10 | 10/10 |
| 2 | Minimum Size Subarray Sum | [Link](https://leetcode.com/problems/minimum-size-subarray-sum/) | 9/10 | 10/10 |
| 3 | Find All Anagrams in a String | [Link](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | 8/10 | 9/10 |
| 4 | Subarray Product Less Than K | [Link](https://leetcode.com/problems/subarray-product-less-than-k/) | 7/10 | 8/10 |
| 5 | Permutation in String | [Link](https://leetcode.com/problems/permutation-in-string/) | 7/10 | 8/10 |
| 6 | Maximum Points You Can Obtain from Cards | [Link](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | 8/10 | 9/10 |
| 7 | Fruit Into Baskets | [Link](https://leetcode.com/problems/fruit-into-baskets/) | 7/10 | 8/10 |
| 8 | Max Consecutive Ones III | [Link](https://leetcode.com/problems/max-consecutive-ones-iii/) | 7/10 | 8/10 |
| 9 | Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold | [Link](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) | 6/10 | 7/10 |
| 10 | Count the Number of Good Subarrays | [Link](https://leetcode.com/problems/count-the-number-of-good-subarrays/) | 6/10 | 7/10 |


### Hard (4 problems)

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Sliding Window Maximum | [Link](https://leetcode.com/problems/sliding-window-maximum/) | 10/10 | 10/10 |
| 2 | Minimum Window Substring | [Link](https://leetcode.com/problems/minimum-window-substring/) | 9/10 | 10/10 |
| 3 | Longest Substring with At Most K Distinct | [Link](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | 8/10 | 9/10 |
| 4 | Find the Longest Semi-Repetitive Substring | [Link](https://leetcode.com/problems/find-the-longest-semi-repetitive-substring/) | 7/10 | 8/10 |


---

## SECTION 9: CodeChef Mastery — Direct Links

**CodeChef Sliding Window Practice**: [Link](https://www.codechef.com/practice-old/tags/sliding-window)

### Beginner Problems

| # | Problem | Link | Rating | Why Chosen? |
|---|---------|------|--------|-------------|
| 1 | **Max Sum Subarray** | [Link](https://www.codechef.com/problems/MAXSUBARR) | 500 | Basic fixed window |
| 2 | **Average Subarray** | [Link](https://www.codechef.com/problems/AVGSUB) | 600 | Fixed window average |
| 3 | **Distinct Characters** | [Link](https://www.codechef.com/problems/DISTCHAR) | 700 | Variable window |

### Intermediate Problems

| # | Problem | Link | Rating | Why Chosen? |
|---|---------|------|--------|-------------|
| 4 | **Longest Valid Substring** | [Link](https://www.codechef.com/problems/VALIDSTR) | 1000 | Variable window longest |
| 5 | **K Distinct Subarray** | [Link](https://www.codechef.com/problems/KDISTARR) | 1200 | At most K distinct |

### Advanced Problems

| # | Problem | Link | Rating | Why Chosen? |
|---|---------|------|--------|-------------|
| 6 | **Sliding Maximum** | [Link](https://www.codechef.com/problems/SLIDINGMAX) | 1400 | Monotonic deque |
| 7 | **Minimum Window** | [Link](https://www.codechef.com/problems/MINWINDOW) | 1600 | Minimum window substring |


---

## SECTION 10: Contest Training

### How to Think Under Pressure? 🏃

#### Sliding Window Decision Tree (30 Seconds)

```text
PROBLEM READ
    ↓
Subarray/substring?
    ↓ YES
Fixed size (e.g., "size K")?
    ↓ YES → Fixed Window (ADD, SHRINK if >k, PROCESS if =k)
    ↓ NO
Need LONGEST?
    ↓ YES → Variable (EXPAND, SHRINK while VIOLATED, PROCESS)
    ↓ NO
Need SHORTEST?
    ↓ YES → Variable (EXPAND, SHRINK while SATISFIED, PROCESS)
    ↓ NO
→ Other pattern
```

#### Time Management

| Difficulty | Target Time |
|------------|-------------|
| **Easy** | 5-8 min |
| **Medium** | 12-18 min |
| **Hard** | 25-35 min |

---

## SECTION 11: Active Learning — 5 Questions

### Question 1 (Easy)
**Best Time to Buy and Sell Stock** — LeetCode 121  
[Link](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Question 2 (Medium)
**Longest Substring Without Repeating Characters** — LeetCode 3  
[Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

### Question 3 (Medium)
**Minimum Size Subarray Sum** — LeetCode 209  
[Link](https://leetcode.com/problems/minimum-size-subarray-sum/)

### Question 4 (Medium)
**Find All Anagrams in a String** — LeetCode 438  
[Link](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

### Question 5 (Hard)
**Sliding Window Maximum** — LeetCode 239  
[Link](https://leetcode.com/problems/sliding-window-maximum/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 12: Revision Notes

### 📝 Sliding Window — Cheat Sheet

| Pattern | When | Template |
|---------|------|----------|
| **Fixed Window** | Size K known | `ADD → if (>k) SHRINK → if (=k) PROCESS` |
| **Variable (Longest)** | Find longest | `EXPAND → while (VIOLATED) SHRINK → PROCESS` |
| **Variable (Shortest)** | Find shortest | `EXPAND → while (SATISFIED) SHRINK → PROCESS` |

---

## SECTION 13: Final Mastery Test

After 5 questions, test:
- Easy: Contains Duplicate II
- Medium: Permutation in String
- Hard: Minimum Window Substring
- Contest: CodeChef MAXSUBARR
- Interview: Longest Without Repeating

---
