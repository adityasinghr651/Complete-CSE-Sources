# PHASE 1 — Java Language Foundation + Core Stdlib Toolkit
### (Parts 1, 2, 5, 6 fused: Foundation → String/Char/StringBuilder → Arrays)

Welcome to Phase 1. The goal here is to learn the **20% of Java syntax** that solves **80% of LeetCode/DSA problems**. We aren't building enterprise apps yet; we are building speed and fluency in a blank editor.

---

## 1. Fast Foundation Recap

### Primitive vs Reference Types

**The Concept:**
Java has two types of variables. Primitives hold the actual value. Reference types (Objects, Arrays) hold a "pointer" (reference) to where the data lives in memory (the Heap). 

**Basic Syntax:**
```java
// Primitives (int, long, double, char, boolean)
int x = 5;

// References (String, Arrays, Custom Classes)
int[] arr = new int[]{1, 2, 3}; 
```

**DSA Application (Why it matters in interviews):**
When you pass a primitive to a function, it passes a *copy*. When you pass an array to a function, it passes the *reference*. If you mutate the array inside the function, it changes the original!

```java
public void modifyArray(int[] nums) {
    nums[0] = 99; // This WILL change the caller's array!
}
```

> [!WARNING]
> **Common Pitfall**: `int * int` can overflow before you ever store it in a `long`.
> ```java
> long bad = a * b;            // Wrong: overflows as an int first!
> long good = (long) a * b;    // Correct: casts 'a' to long before multiplying
> ```

---

## 2. Strings & Characters

### String Basics
**The Concept:** Strings in Java are **immutable**. You cannot change them once created. Every time you do `s += "a"`, Java creates a brand new String. 

**Basic Syntax & Core Methods:**
```java
String s = "hello";

int len = s.length();                 // 5 (Notice the parentheses!)
char first = s.charAt(0);             // 'h'
String sub = s.substring(1, 4);       // "ell" (Start is inclusive, End is exclusive)
char[] arr = s.toCharArray();         // ['h', 'e', 'l', 'l', 'o']
```

> [!WARNING]
> **Common Pitfall**: Never use `==` to compare Strings. `==` checks if they are the exact same object in memory. ALWAYS use `.equals()`.
> ```java
> if (s1.equals(s2)) { ... } // CORRECT
> ```

### StringBuilder
**The Concept:** Because Strings are immutable, building a string in a loop with `+=` is incredibly slow (O(N²)). `StringBuilder` is mutable and fast (O(N)).

**DSA Application (Building a string dynamically):**
```java
// E.g., Reversing a string or filtering characters
StringBuilder sb = new StringBuilder();

for (char c : "hello".toCharArray()) {
    sb.append(c);
}

// Convert back to String at the very end
String result = sb.toString(); 
```

### Character Math
**The Concept:** Under the hood, `char` is just a number (its ASCII value). You can do math on them!

**DSA Application (Character Indexing):**
This is the #1 trick for Anagram/Palindrome/String array problems. You can map lowercase letters `'a'` to `'z'` directly to indices `0` to `25`.
```java
char c = 'd';
int index = c - 'a'; // 'd' (100) - 'a' (97) = 3
// You can now use `index` in an array: freq[index]++
```

**Converting Digit Char to Int:**
```java
char digit = '7';
int val = digit - '0'; // 7 (integer)
```

---

## 3. Arrays

**The Concept:** Fixed-size, contiguous blocks of memory. Arrays are objects in Java, so they are Reference Types.

**Basic Syntax:**
```java
int[] nums = new int[5];                 // [0, 0, 0, 0, 0]
int[] explicit = {1, 2, 3};              // [1, 2, 3]
int[][] grid = new int[3][4];            // 3 rows, 4 columns
```

**DSA Application (Common Operations):**
```java
// 1. Sort an array (O(N log N))
Arrays.sort(nums);

// 2. Fill an array with a default value (Crucial for DP / Memoization)
Arrays.fill(nums, -1);

// 3. True Array Copy (Don't just do int[] copy = nums; that copies the reference!)
int[] trueCopy = Arrays.copyOf(nums, nums.length);
```

### The 2D Array Traversal Pattern
You will write this loop constantly for Graph and Matrix problems:
```java
int[][] grid = { {1, 2}, {3, 4} };

for (int r = 0; r < grid.length; r++) {
    for (int c = 0; c < grid[0].length; c++) {
        System.out.println(grid[r][c]);
    }
}
```

---

## 4. Synthesis Pattern: The Two-Pointer Palindrome

Let's tie it all together into a blank-editor FAANG pattern.

**The Problem**: Check if a string is a palindrome.
**The Tools Needed**: `String.length()`, `String.charAt()`.

```java
public boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1; // Last index
    
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

## 5. Synthesis Pattern: The Anagram Frequency Array

**The Problem**: Check if String `b` is an anagram of String `a`.
**The Tools Needed**: `String.toCharArray()`, `char` math (`c - 'a'`).

```java
public boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    
    // We only have 26 lowercase English letters
    int[] freq = new int[26];
    
    // Increment for string a
    for (char c : a.toCharArray()) {
        freq[c - 'a']++; 
    }
    
    // Decrement for string b
    for (char c : b.toCharArray()) {
        freq[c - 'a']--;
    }
    
    // If they are anagrams, every bucket should be back to 0
    for (int count : freq) {
        if (count != 0) return false;
    }
    
    return true;
}
```

---

## 🚀 Next Steps
Can you open a blank editor right now and write the `isPalindrome` and `isAnagram` methods purely from memory without looking at this sheet? Try it! That is how you build the mental model.
