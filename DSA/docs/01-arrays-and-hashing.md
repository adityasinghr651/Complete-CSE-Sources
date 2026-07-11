# Arrays and Hashing

## Overview

Arrays and Hashing form the fundamental backbone of Data Structures and Algorithms. 
- **Memory Foundation:** Arrays provide contiguous memory storage, allowing for O(1) random access.
- **Building Blocks:** Advanced topics like Dynamic Programming, Graphs, and Trees are inherently built on top of arrays.
- **O(1) Lookup Magic:** Hashing allows us to trade brute-force O(n) lookups for optimized O(1) access, a crucial technique for passing tight time limits.

### Real-world Analogy 🏠
Imagine looking for a specific shop in a massive market:
- **Array Approach**: Checking each shop one by one until you find it → **O(n)** (Slow!)
- **Hashing Approach**: Looking at a directory map at the entrance that tells you exactly where the shop is → **O(1)** (Fast!)

## Prerequisites
- Basic understanding of programming concepts (Variables, Loops, Functions).
- Familiarity with basic Java syntax.

## Learning Objectives
- Understand how contiguous memory allocation works.
- Master the internal workings of HashMaps and HashSets.
- Recognize common patterns: Frequency Counting, Duplicate Detection, and Index mapping.
- Learn how to optimize O(n²) brute-force solutions to O(n) using hashing.

## Difficulty & Estimated Study Time
- **Difficulty:** Easy to Medium
- **Estimated Study Time:** 5-7 Hours

---

## Theory & Core Concepts

### 1. Array
Arrays have a fixed size in Java and provide O(1) access time.
```java
// Declaration
int[] arr = new int[5];  // Fixed size
int[] arr2 = {1, 2, 3};  // Inline initialization

// Access
arr[0] = 10;  // O(1)
```
- **Search (unsorted):** O(n)
- **Insert at end:** O(1) amortized (for dynamic arrays)

### 2. HashMap
A key-value store that provides average O(1) lookup times.
```java
Map<Integer, Integer> map = new HashMap<>();
map.put(key, value);  
map.get(key);         
map.containsKey(key); 
```
- **Worst-case Time Complexity:** O(n) (occurs during severe hash collisions when keys map to the same bucket).

### 3. HashSet
A collection of unique elements. Perfect for duplicate detection.
```java
Set<Integer> set = new HashSet<>();
set.add(5);
set.contains(5);  // O(1)
```

### Tradeoffs
| Data Structure | Pros | Cons |
|---------------|------|------|
| **Array** | O(1) access, memory efficient | Fixed size, insertion/deletion is slow |
| **HashMap** | O(1) lookup, extremely flexible | Extra space overhead, collision handling |
| **HashSet** | O(1) duplicate check | No ordering, extra space overhead |

---

## Patterns & Templates

### How to Identify the Pattern
| Keyword in Problem Statement | Pattern | Data Structure |
|-----------------------------|---------|----------------|
| "Find two numbers that sum to target" | Two Sum | HashMap |
| "Check if a duplicate exists" | Duplicate Detection | HashSet |
| "Group things that are similar" | Grouping | HashMap (key = sorted string/signature) |
| "Count frequency of elements" | Frequency Counter | HashMap / Array of size 26 |
| "Longest consecutive sequence" | Sequence Building | HashSet |

### Template 1: HashMap Frequency Counter
```java
public void frequencyCounter(int[] arr) {
    Map<Integer, Integer> freq = new HashMap<>();
    
    // Step 1: Count frequencies
    for (int num : arr) {
        freq.put(num, freq.getOrDefault(num, 0) + 1);
    }
}
```
*Optimization Note: If you are counting lowercase English letters, always use an `int[] count = new int[26];` instead of a HashMap to achieve true O(1) space and avoid hashing overhead.*

### Template 2: The "Two Sum" Pattern (Check before Put)
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        
        map.put(nums[i], i); // Store value -> index mapping
    }
    return new int[]{};
}
```

### Template 3: HashSet Duplicate Detection
```java
public boolean containsDuplicate(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        if (!set.add(num)) { // add() returns false if duplicate exists
            return true;
        }
    }
    return false; 
}
```

---

## Common Mistakes & Edge Cases

1. **Using the Same Element Twice (Two Sum):** Putting the current element in the HashMap *before* checking for the complement can cause the algorithm to use the same element twice. Always check `containsKey` first!
2. **Assuming Ordering in HashSets:** HashSets are inherently unordered. Never rely on iteration order.
3. **Ignoring Hash Collisions:** In theoretical questions, remember that HashMap operations are O(1) *average*, but O(n) *worst-case* due to collisions.
4. **Missing Edge Cases:** Always test your code against empty arrays `[]`, single elements `[1]`, and arrays with negative numbers.

---

## Interview Notes & Revision

### Company Focus
- **Google & Meta:** Heavily test HashMap optimizations. They want to see you natively convert O(n²) brute-force approaches into O(n) space-time tradeoffs.
- **Amazon & Microsoft:** Frequently ask Two Sum variations, Product of Array Except Self, and Trapping Rain Water.

### Interview Checklist
- [ ] Did I clarify constraints? (Can arrays be empty? Can numbers be negative?)
- [ ] Did I explain the brute-force O(n²) approach first?
- [ ] Did I explain the time and space complexity clearly?
- [ ] Is my code clean, modular, and lacking redundant variables?

---

## Contest Notes

### How to Think Under Pressure (Competitive Programming)
1. **30-Second Pattern Scan:** Identify keywords immediately. "Consecutive" -> HashSet. "Pairs" -> HashMap.
2. **Brute Force First:** Even if you know it will Time Limit Exceed (TLE), mentally map out the O(n²) logic. The optimized solution is usually just replacing the inner loop with a HashMap lookup.
3. **Handling Wrong Answers (WA):** If you get a WA, check edge cases immediately: empty arrays, negative numbers, all elements being the same.
4. **Handling Time Limit Exceeded (TLE):** If you get a TLE, your algorithm is likely O(n²). Switch to O(n) or O(n log n).

---

## Practice Checklist

### Easy

| # | Problem                    | Frequency | Importance |
| - | -------------------------- | --------- | ---------- |
| 1 | [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) | 9/10      | 9/10       |
| 2 | [Valid Anagram](https://leetcode.com/problems/valid-anagram/) | 8/10      | 8/10       |
| 3 | [Two Sum](https://leetcode.com/problems/two-sum/) | 10/10     | 10/10      |
| 4 | [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) | 7/10      | 7/10       |
| 5 | [Most Common Element](https://leetcode.com/problems/majority-element/) | 6/10      | 6/10       |
| 6 | [Unique Morse Code Words](https://leetcode.com/problems/unique-morse-code-words/) | 5/10      | 5/10       | 

### Medium

| #  | Problem                                  | Frequency | Importance |
| -- | ---------------------------------------- | --------- | ---------- |
| 1  | [Group Anagrams](https://leetcode.com/problems/group-anagrams/) | 9/10      | 10/10      |
| 2  | [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) | 8/10      | 9/10       |
| 3  | [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) | 8/10      | 9/10       |
| 4  | [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) | 9/10      | 10/10      |
| 5  | [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/) | 7/10      | 8/10       |
| 6  | [Valid Sudoku](https://leetcode.com/problems/valid-sudoku/) | 7/10      | 8/10       |
| 7  | [Merge Intervals](https://leetcode.com/problems/merge-intervals/) | 9/10      | 9/10       |
| 8  | [Maximum Subarray (Kadane's)](https://leetcode.com/problems/maximum-subarray/) | 10/10     | 10/10      |
| 9  | [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | 8/10      | 8/10       |
| 10 | [Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) | 7/10      | 7/10       |

### Hard

| # | Problem                     | Frequency | Importance |
| - | --------------------------- | --------- | ---------- |
| 1 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | 9/10      | 10/10      |
| 2 | [First Missing Positive](https://leetcode.com/problems/first-missing-positive/) | 8/10      | 9/10       |
| 3 | [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) | 9/10      | 10/10      |
| 4 | [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | 8/10      | 9/10       |

---

# Problems

## 1. Very Easy

### Contains Duplicate

**Platform:** LeetCode
**Difficulty:** Easy
**Companies:** Amazon, Google, Meta, Apple
**Pattern:** Duplicate Detection
**Prerequisites:** HashSet

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/contains-duplicate/)

#### Brute Force
Compare every element with every other element using two nested loops. Time Complexity: O(n²).

#### Optimized Solution
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for(int num : nums){
            if(!set.add(num)) return true;
        }
        return false;
    }
}
```

#### Dry Run
`nums = [1, 2, 3, 1]`
- i=0: set.add(1) → true, set={1}
- i=1: set.add(2) → true, set={1,2}
- i=2: set.add(3) → true, set={1,2,3}
- i=3: set.add(1) → false → Return `true`

#### Complexity
- **Time:** O(n)
- **Space:** O(n)

---

## 2. Easy

### Valid Anagram

**Platform:** LeetCode
**Difficulty:** Easy
**Companies:** Google, Amazon, Microsoft
**Pattern:** Frequency Counter
**Prerequisites:** ASCII values, Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/valid-anagram/)

#### Brute Force
Sort both strings and compare them. Time Complexity: O(n log n).

#### Optimized Solution
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) return false;
        
        int[] count = new int[26];
        for(char c : s.toCharArray()) count[c-'a']++;
        for(char c: t.toCharArray()) count[c-'a']--;
        
        for(int countValue : count){
            if(countValue != 0) return false;
        }
        return true;
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1) (Fixed size array of 26)

---

## 3. Medium

### Two Sum

**Platform:** LeetCode
**Difficulty:** Medium
**Companies:** Meta, Google, Amazon, Apple, Uber
**Pattern:** Hash Map
**Prerequisites:** Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/two-sum/)

#### Optimized Solution
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map = new HashMap<>();
        for(int i=0; i<nums.length; i++){
            int complement = target - nums[i];
            if(map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{};
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(n)

---

## 4. Hard

### Trapping Rain Water

**Platform:** LeetCode
**Difficulty:** Hard
**Companies:** Google, Meta, Amazon, Microsoft, Uber
**Pattern:** Two Pointers / Prefix Max
**Prerequisites:** Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/trapping-rain-water/)

#### Optimized Solution (Two Pointers)
```java
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        int water = 0;
        
        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) leftMax = height[left];
                else water += leftMax - height[left];
                left++;
            } else {
                if (height[right] >= rightMax) rightMax = height[right];
                else water += rightMax - height[right];
                right--;
            }
        }
        return water;
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1)

---

## 5. FAANG Questions

### Longest Consecutive Sequence

**Platform:** LeetCode
**Difficulty:** Medium (Often asked as Hard follow-up)
**Companies:** Google, Microsoft, Meta
**Pattern:** Sequence Building
**Prerequisites:** HashSet

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/longest-consecutive-sequence/)

#### Optimized Solution
```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);
        
        int longest = 0;
        for (int num : set) {
            // Only start counting if num-1 doesn't exist
            if (!set.contains(num - 1)) {
                int current = num;
                int length = 1;
                
                while (set.contains(current + 1)) {
                    current++;
                    length++;
                }
                longest = Math.max(longest, length);
            }
        }
        return longest;
    }
}
```

#### Complexity
- **Time:** O(n) (Each element is visited at most twice)
- **Space:** O(n)

---

## 6. Codeforces & CP

### A. Team

**Platform:** Codeforces
**Difficulty:** 800
**Pattern:** Counting
**Prerequisites:** Basic Arrays

#### Problem Statement Link
[Official Link](https://codeforces.com/problemset/problem/231/A)

*(Detailed solution and template placeholder)*

---

## 7. Mastery Test

### Product of Array Except Self

**Platform:** LeetCode
**Difficulty:** Medium
**Companies:** Meta, Amazon, Microsoft
**Pattern:** Prefix and Suffix Arrays
**Prerequisites:** Array Manipulation

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/product-of-array-except-self/)

*(Detailed solution and template placeholder)*
