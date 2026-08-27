# Topic 16: Palindrome Detection

## PROBLEM
Given a string, determine whether it reads the same forwards and backwards.

```text
Input: "racecar"  → true
Input: "hello"    → false
```

## BRUTE-FORCE / SIMPLE THINKING
The most direct way to check this: **reverse the string, and compare it to the original.** If they're identical, it's a palindrome. This is simple and correct, but it's worth noticing it does more work than strictly necessary — it builds an entirely new reversed string just to compare.

## OBSERVATION
You don't actually need to reverse the whole string. You only need to check that **the first character matches the last, the second matches the second-to-last, and so on**, working inward. The moment you find a mismatch, you can stop immediately — no need to keep checking. This is the **two-pointer** approach: one pointer starts at the beginning, one at the end, and they move toward each other.

```text
"racecar"
 ↑     ↑
left  right
r == r ✓ → move both pointers inward

"racecar"
  ↑   ↑
 left right
a == a ✓ → move both pointers inward

"racecar"
   ↑ ↑
  left right
c == c ✓ → move both pointers inward

pointers meet/cross → done, it's a palindrome
```

## ALGORITHM (two-pointer approach)
1. Set `left = 0` and `right = string.length - 1`.
2. While `left < right`:
   - If `string[left] !== string[right]`, return `false` immediately.
   - Otherwise, increment `left` and decrement `right`.
3. If the loop finishes without returning `false`, return `true`.

## DRY RUN

Input: `"level"`

```text
left=0, right=4: 'l' vs 'l' → match → left=1, right=3
left=1, right=3: 'e' vs 'e' → match → left=2, right=2
left=2, right=2: left < right is now false → loop ends
```
Result: `true` — palindrome confirmed.

Input: `"hello"`

```text
left=0, right=4: 'h' vs 'o' → mismatch → return false immediately
```
Result: `false` — no need to check anything else.

## PSEUDOCODE

```text
function isPalindrome(str):
    left = 0
    right = length(str) - 1
    while left < right:
        if str[left] != str[right]:
            return false
        left = left + 1
        right = right - 1
    return true
```

## MY IMPLEMENTATION
Your turn — translate this into JavaScript, using bracket notation to access characters by index (strings support index access like arrays: `str[0]`). Get the loop condition (`left < right`, not `<=`) right — think about why `<=` would be wrong here (hint: think about the middle character of an odd-length string).

## COMPLEXITY

- **Time complexity:** **O(n)** — in the worst case (a true palindrome, or a mismatch only at the very end), you check roughly half the characters, which is still O(n) after dropping the constant factor.
- **Space complexity:** **O(1)** for the two-pointer approach — no extra string/array is created, just two index variables. Compare this to the brute-force "reverse and compare" approach, which is O(n) space because it builds a whole new reversed string.

This is a good concrete example of the same time complexity (O(n) either way) but a real space complexity difference between two valid approaches — a common interview follow-up ("can you do this without extra space?").

## EDGE CASES
- Empty string `""` — `left = 0`, `right = -1`, so `left < right` is false immediately, loop never runs, returns `true` (an empty string is trivially a palindrome — worth deciding and stating this explicitly, since it's a common edge-case question)
- Single character `"a"` — `left = 0`, `right = 0`, `left < right` is false, returns `true` immediately
- Case sensitivity — `"Racecar"` (capital R) would fail a naive character-by-character check against `"racecar"` unless you normalize case first (e.g., `.toLowerCase()` — Phase 10 territory, but worth flagging now since it's a very common real test case)
- Spaces/punctuation — `"race car"` or `"A man, a plan, a canal, Panama"` are famous "palindrome phrases" that require stripping non-letter characters first; this is beyond the basic version above.

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS**: full phrase-palindrome handling (stripping spaces/punctuation, case normalization) combines this topic with string methods from Phase 10, which you haven't covered yet. I'll flag it as a natural follow-up exercise once you reach Phase 10, rather than teach it now.

## OPTIMIZATION DISCUSSION
The two-pointer approach *is* the optimization over the naive "reverse and compare" method — same time complexity, but better space complexity (O(1) vs O(n)), and it can exit early on a mismatch rather than always doing the full reversal first. There isn't a way to do better than O(n) time, since in the worst case you must at least look at every character once to be sure (you can't confirm a palindrome without checking all the necessary pairs).

## WHERE THIS APPEARS IN REAL SOFTWARE
Palindrome checking itself is rarely a direct business requirement, but it's one of the most common **interview screening questions** precisely because it tests: string/array indexing, loop control, the two-pointer pattern (which generalizes to many other problems — sorted array searches, sliding window problems), and edge-case thinking (empty string, single character, case sensitivity). The two-pointer *technique* itself does show up in real code — e.g., efficiently checking for duplicate pairs in a sorted array, or comparing data from both ends of a structure.

## INTERVIEW QUESTION

**Q: Compare the "reverse and compare" approach to the "two-pointer" approach for palindrome checking. Do they have the same time complexity? What about space complexity?**

*Answer out loud before checking anything — both are O(n) time, but two-pointer is O(1) space vs O(n) space for reverse-and-compare (since reversing builds a new string).*

---

## 💻 WRITE WITHOUT AI

1. Implement `isPalindrome(str)` using the two-pointer approach from the pseudocode above. Test with `"racecar"` (true) and `"hello"` (false).
2. Test with an empty string `""` and a single character `"a"` — confirm both return `true`, and be ready to explain why in your own words.
3. Test with mixed case, e.g., `"Racecar"` — confirm your basic implementation correctly returns `false` (since it's case-sensitive by default), and write a comment noting what change (from Phase 10) would be needed to fix this.
4. Implement the **brute-force version** too — reverse the string and compare it to the original — as a second function `isPalindromeBruteForce(str)`. Confirm both versions agree on the same test cases.
5. (Harder) Write `isPalindromeRecursive(str)` — implement palindrome checking **recursively** instead of with a loop, combining this topic with Topic 15. Think about your base case (very short strings) and your recursive case (compare first/last characters, then recurse on the substring between them).

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
