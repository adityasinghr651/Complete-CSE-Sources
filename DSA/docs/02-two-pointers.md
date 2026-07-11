# 02. Two Pointers

## Overview

The Two Pointers technique is one of the most frequently used optimization strategies in FAANG interviews. It is fundamentally used to search for pairs, subarrays, or specific conditions within **sorted arrays** or **strings**, allowing us to reduce brute-force O(n²) time complexity down to a highly optimized O(n).

### Real-world Analogy 🏠
**Scenario 1: Bookshelf Search (Opposite Ends)**
Imagine a bookshelf where books are sorted alphabetically [A, B, C, D, E, F, G, H, I, J].
- **Question:** Are 'A' and 'J' both on the shelf?
- **Brute Force:** Check every single book one by one (10 checks).
- **Two Pointers:** Look at the left end for 'A' (index 0) and the right end for 'J' (index 9). Done in 2 checks!

**Scenario 2: Race Track (Fast & Slow Pointers)**
On a race track, there are two runners: a slow runner (1 step/sec) and a fast runner (2 steps/sec).
- **Question:** Is there a loop (cycle) on the track?
- **Solution:** If the fast runner eventually catches up to and laps the slow runner, a cycle exists. This is the foundation of linked list cycle detection.

## Prerequisites
- Basic array and string manipulation.
- Understanding of while loops and index management.
- Familiarity with O(n) and O(n²) time complexity concepts.

## Learning Objectives
- Understand why and how the Two Pointers technique reduces time complexity.
- Master the 4 core patterns: Opposite Ends, Fast & Slow, Parallel Pointers, and Left Fixed + Two Pointers.
- Learn to immediately recognize problem triggers (e.g., "sorted array", "find pair", "palindrome").
- Differentiate between Two Pointers and Sliding Window techniques.

## Difficulty & Estimated Study Time
- **Difficulty:** Medium
- **Estimated Study Time:** 6-8 Hours

---

## Theory & Core Concepts

### Why does it work on Sorted Arrays?
In Java, "pointers" in this context are actually **array indices** (e.g., `left = 0`, `right = arr.length - 1`). 
When an array is sorted, we can make mathematical guarantees. For example, if `arr[left] + arr[right] > target`, we know that keeping `right` the same and moving `left` forward will only result in an even *larger* sum. Therefore, we can safely decrement `right` without missing any valid solutions.

### Tradeoffs Comparison
| Approach | Time | Space | When to Use |
|----------|------|-------|-------------|
| **Brute Force (Nested Loops)** | O(n²) | O(1) | Small n (< 100) |
| **Two Pointers (Sorted)** | O(n) | O(1) | The array is already sorted ✅ |
| **HashMap** | O(n) | O(n) | The array is unsorted |
| **Binary Search** | O(log n) | O(1) | Searching for a single exact element |

---

## Patterns & Templates

### Pattern 1: Opposite Ends (Converging)
Used for finding pairs in sorted arrays, checking palindromes, or reversing arrays in-place.
```java
public int[] oppositeEnds(int[] nums, int target) {
    int left = 0;                           
    int right = nums.length - 1;            
    
    while (left < right) {                  
        int currentSum = nums[left] + nums[right];  
        
        if (currentSum == target) {         
            return new int[]{left, right};  
        } else if (currentSum < target) {   
            left++; // Move right to increase sum
        } else {                            
            right--; // Move left to decrease sum
        }
    }
    return new int[]{};                     
}
```

### Pattern 2: Fast & Slow (Runner Technique)
Used for cycle detection in linked lists or removing duplicates in-place.
```java
public int removeDuplicates(int[] nums) {
    int slow = 0;  // Position for next unique element
    
    for (int fast = 0; fast < nums.length; fast++) {
        if (nums[fast] != nums[slow]) {
            slow++;                      
            nums[slow] = nums[fast];     
        }
    }
    return slow + 1;
}
```

### Pattern 3: Parallel Pointers (Two Arrays)
Used for merging sorted arrays or finding intersections.
```java
public int[] mergeSortedArrays(int[] A, int[] B) {
    int i = 0, j = 0, k = 0;
    int[] result = new int[A.length + B.length];
    
    while (i < A.length && j < B.length) {
        if (A[i] < B[j]) {
            result[k++] = A[i++];
        } else {
            result[k++] = B[j++];
        }
    }
    
    // Copy remaining elements
    while (i < A.length) result[k++] = A[i++];
    while (j < B.length) result[k++] = B[j++];
    
    return result;
}
```

### Pattern 4: Left Fixed + Two Pointers (3Sum Style)
Used for finding triplets (3Sum) or quadruplets (4Sum).
```java
// Step 1: Sort the array (O(n log n))
Arrays.sort(nums);

// Step 2: Fix the first element
for (int i = 0; i < nums.length - 2; i++) {
    // Skip duplicates to prevent duplicate triplets
    if (i > 0 && nums[i] == nums[i - 1]) continue;
    
    // Step 3: Use opposite ends for the remainder
    int left = i + 1, right = nums.length - 1;
    while (left < right) {
        // Evaluate and adjust pointers...
    }
}
```

---

## Common Mistakes & Edge Cases

1. **Incorrect Initial Right Pointer:** Setting `int right = nums.length;` instead of `nums.length - 1` will cause an `ArrayIndexOutOfBoundsException`.
2. **Wrong Loop Condition:** Using `while (left <= right)` when searching for two distinct elements. The pointers should never meet/overlap if you need a distinct pair. Use `while (left < right)`.
3. **Moving Both Pointers Blindly:** Never do `left++; right--;` simultaneously unless you have successfully processed a pair and need to continue searching for *more* pairs.
4. **Integer Overflow in 4Sum:** When summing 4 integers, the result can exceed the 32-bit integer limit. Always cast to `long` before summing (e.g., `long sum = (long) nums[i] + nums[j] + nums[left] + nums[right];`).

---

## Interview Notes & Revision

### Company Focus
- **Google & Meta:** Highly emphasize **3Sum** and **Container With Most Water**. These problems perfectly test your ability to recognize patterns and optimize O(n²) algorithms into O(n).
- **Amazon & Microsoft:** Expect foundational problems like **Two Sum II**, **Move Zeroes**, and **Merge Sorted Arrays**.

### Two Pointers vs Sliding Window
| Aspect | Two Pointers | Sliding Window |
|--------|--------------|----------------|
| **Movement** | Pointers move independently (usually towards each other) | Window expands/contracts dynamically |
| **Condition** | Fixed condition (e.g., exact sum = target) | Dynamic condition (e.g., at most K unique) |

---

## Contest Notes

### How to Think Under Pressure
1. **30-Second Pattern Recognition:**
   - Is it about arrays/strings?
   - Is it sorted (or can it be sorted within O(n log n) time limits)?
   - Do you need to find pairs/triplets? → **Two Pointers (Opposite Ends)**
   - Need to merge two arrays? → **Two Pointers (Parallel)**
2. **Verbalize the Brute Force:** In interviews, always explain the O(n²) nested loop approach first. Then state: *"Since the array is sorted, I can use two pointers to reduce this to O(n) by eliminating redundant scans."*

---

## Practice Checklist

### Easy

| # | Problem | Frequency | Importance |
| - | -------------------------- | --------- | ---------- |
| 1 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | 9/10 | 9/10 |
| 2 | [Reverse String](https://leetcode.com/problems/reverse-string/) | 8/10 | 8/10 |
| 3 | [Move Zeroes](https://leetcode.com/problems/move-zeroes/) | 9/10 | 10/10 |
| 4 | [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) | 7/10 | 7/10 |
| 5 | [Rotate Array](https://leetcode.com/problems/rotate-array/) | 8/10 | 8/10 |

### Medium

| # | Problem | Frequency | Importance |
| -- | ---------------------------------------- | --------- | ---------- |
| 1 | [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | 10/10 | 10/10 |
| 2 | [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | 9/10 | 10/10 |
| 3 | [3Sum](https://leetcode.com/problems/3sum/) | 10/10 | 10/10 |
| 4 | [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) | 8/10 | 9/10 |
| 5 | [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) | 8/10 | 9/10 |
| 6 | [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/) | 8/10 | 9/10 |

### Hard

| # | Problem | Frequency | Importance |
| - | --------------------------- | --------- | ---------- |
| 1 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | 9/10 | 10/10 |
| 2 | [4Sum](https://leetcode.com/problems/4sum/) | 8/10 | 9/10 |
| 3 | [Next Permutation](https://leetcode.com/problems/next-permutation/) | 8/10 | 9/10 |

---

# Problems

## 1. Very Easy

### Valid Palindrome

**Platform:** LeetCode
**Difficulty:** Easy
**Companies:** Apple, Google, Microsoft
**Pattern:** Opposite Ends
**Prerequisites:** String Manipulation

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/valid-palindrome/)

#### Brute Force
Clean the string by removing non-alphanumeric characters, allocate a new string, reverse it, and compare it with the cleaned string. Time: O(n), Space: O(n).

#### Optimized Solution
```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0;
        int right = s.length() - 1;
        
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                left++;
            }
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                right--;
            }
            
            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1)

---

## 2. Easy

### Move Zeroes

**Platform:** LeetCode
**Difficulty:** Easy
**Companies:** Microsoft, Google, Amazon, Meta, Apple
**Pattern:** Fast & Slow Pointers
**Prerequisites:** Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/move-zeroes/)

#### Optimized Solution
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int slow = 0;  // Position for next non-zero
        
        // Fast scans array, slow places non-zeros
        for (int fast = 0; fast < nums.length; fast++) {
            if (nums[fast] != 0) {
                nums[slow] = nums[fast];
                slow++;
            }
        }
        
        // Fill remaining positions with 0
        while (slow < nums.length) {
            nums[slow] = 0;
            slow++;
        }
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1)

---

## 3. Medium

### Two Sum II - Input Array Is Sorted

**Platform:** LeetCode
**Difficulty:** Medium
**Companies:** Google, Amazon, Microsoft, Meta
**Pattern:** Opposite Ends
**Prerequisites:** Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

#### Optimized Solution
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            
            if (sum == target) {
                return new int[]{left + 1, right + 1};  // 1-indexed!
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{};
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1)

---

## 4. Hard

### Trapping Rain Water

**Platform:** LeetCode
**Difficulty:** Hard
**Companies:** Google, Meta, Amazon, Microsoft, Apple, Uber
**Pattern:** Two Pointers (Opposite Ends)
**Prerequisites:** Arrays

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/trapping-rain-water/)

#### Optimized Solution
```java
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        int water = 0;
        
        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    water += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    water += rightMax - height[right];
                }
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

### Container With Most Water

**Platform:** LeetCode
**Difficulty:** Medium
**Companies:** Google, Meta, Amazon, Microsoft, Uber
**Pattern:** Opposite Ends
**Prerequisites:** Greedy

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/container-with-most-water/)

#### Optimized Solution
```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int maxArea = 0;
        
        while (left < right) {
            int width = right - left;
            int h = Math.min(height[left], height[right]);
            maxArea = Math.max(maxArea, h * width);
            
            // Always move the shorter line!
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return maxArea;
    }
}
```

#### Complexity
- **Time:** O(n)
- **Space:** O(1)

---

## 6. Codeforces & CP

### Difference Pairs

**Platform:** CodeChef
**Difficulty:** 500
**Pattern:** Two Pointers
**Prerequisites:** Basic Implementation

#### Problem Statement Link
[Official Link](https://www.codechef.com/practice/course/two-pointers/POINTERF/problems/PREP68)

*(Detailed solution and template placeholder)*

---

## 7. Mastery Test

### 3Sum

**Platform:** LeetCode
**Difficulty:** Medium
**Companies:** Google, Meta, Amazon, Microsoft, Apple
**Pattern:** Left Fixed + Two Pointers
**Prerequisites:** Sorting

#### Problem Statement Link
[Official Link](https://leetcode.com/problems/3sum/)

*(Detailed solution and template placeholder)*
