# PHASE 1 — Java Language Foundation + Core Stdlib Toolkit
### (Parts 1, 2, 5, 6 fused: Foundation → String/Char/StringBuilder → Arrays)

## 1. Fast Foundation Recap

**Primitive vs Reference Types**
- Primitives (`int, long, double, float, char, boolean, byte, short`) store the actual value.
- Reference types (`String, int[], ArrayList<>, HashMap<>`, your own classes) store a reference to an object on the heap.
- Why it matters: passing an array/object to a method passes the reference. Mutating the object inside the method affects the original. Reassigning the parameter to a *new* object does not.

```java
void modify(int[] arr) {
    arr[0] = 99;        // affects caller's array
    arr = new int[5];   // does NOT affect caller
}
```

**Type Casting**
- Widening (implicit): `int → long → float → double`, safe.
- Narrowing (explicit): `double → int` truncates, doesn't round: `(int) 9.99 == 9`.
- Overflow trap: `int * int` can overflow before you ever store it in a `long`.
```java
long product = (long) a * b;   // correct
long bad = a * b;              // wrong if a*b overflows int first
```

**Wrapper Classes**
| What | Use |
|---|---|
| `Integer.parseInt(s)` | String → int |
| `String.valueOf(x)` | anything → String |
| `Integer.MAX_VALUE` / `MIN_VALUE` | sentinel bounds |
| `Long.MAX_VALUE` | bigger sentinel, overflow-safe init |
| `Integer.compare(a,b)` | -1/0/1, for comparators |

**Common mistake**: never use `==` on boxed `Integer`/`Long` — Java only caches -128..127, so `Integer a=200,b=200; a==b` is `false`. Use `.equals()` or unbox first.

**Math utilities**
- `Math.max/min(a,b)` — basic
- `Math.abs(x)` — careful, `Math.abs(Integer.MIN_VALUE)` overflows back to itself
- `Math.pow(a,b)` — returns `double`, cast if you need int/long
- `Math.sqrt(x)` — for prime checks, `i*i <= n` often avoids double-precision issues
- `Math.floorDiv/floorMod(a,b)` — correct negative modulo (`-7 % 3 == -1` in Java, but `Math.floorMod(-7,3) == 2`)

---

## 2. String — immutable, touched in nearly every problem

Every "modifying" call returns a *new* String. Repeated `+=` in a loop is O(n²) total — use `StringBuilder` instead.

| Method | Returns | Modifies original? | Example | DSA use | Mistake |
|---|---|---|---|---|---|
| `length()` | int | no | `"abc".length()→3` | loop bounds | confusing with `.size()` |
| `charAt(i)` | char | no | `"abc".charAt(1)→'b'` | char processing | off-by-one |
| `substring(i,j)` | String | no | `"hello".substring(1,3)→"el"` | window extraction | `j` is exclusive |
| `indexOf(str)` | int (-1 if absent) | no | `"hello".indexOf("l")→2` | first occurrence | forgetting to check -1 |
| `contains(str)` | boolean | no | — | existence check | O(n·m), avoid in hot loops |
| `equals(str)` | boolean | no | content compare | **always use, never `==`** | using `==` |
| `equalsIgnoreCase(str)` | boolean | no | — | anagram/word match | — |
| `startsWith`/`endsWith` | boolean | no | — | prefix/suffix, trie-like problems | — |
| `split(regex)` | String[] | no | `"a,b,,c".split(",")→[a,b,"",c]` | tokenizing | regex chars like `.` need escaping |
| `replace(a,b)` | String | no | literal replace | cleaning | confusing with regex `replaceAll` |
| `toCharArray()` | char[] | no | — | index-mutation-style logic | it's a copy, not a view |
| `trim()`/`strip()` | String | no | remove whitespace | cleaning input | — |
| `toLowerCase/toUpperCase()` | String | no | — | case-insensitive problems | — |

**`==` vs `.equals()` — the classic bug:**
```java
String a = "hello", b = "hello", c = new String("hello");
a == b       // true  (string pool)
a == c       // false (different heap object)
a.equals(c)  // true  (content) ← always use this
```

---

## 3. StringBuilder — mutable, O(1) amortized append

| Method | Returns | Modifies? | Use |
|---|---|---|---|
| `append(x)` | itself, chainable | yes | building output |
| `reverse()` | itself | yes | palindrome/digit-reversal |
| `setCharAt(i,c)` | void | yes | in-place edits |
| `deleteCharAt(i)` | itself | yes | backspace-style problems |
| `insert(i,x)` | itself | yes | mid-string insert |
| `toString()` | String | no | **always call at the end** |

```java
StringBuilder sb = new StringBuilder();
for (char c : "hello".toCharArray()) sb.append(c);
String result = sb.toString();  // don't forget this
```
Ignore `StringBuffer` — it's just the thread-safe (slower) version, irrelevant here.

---

## 4. Character — char-level logic

| Method | Use |
|---|---|
| `Character.isLetter(c)` | filter non-letters |
| `Character.isDigit(c)` | validate/parse |
| `Character.isLetterOrDigit(c)` | combined check |
| `Character.isWhitespace(c)` | manual tokenizing |
| `Character.toLowerCase/toUpperCase(c)` | anagram checks |
| `c - '0'` (idiom) | digit char → int value, e.g. `'7'-'0'==7` — **memorize this** |
| `c - 'a'` (idiom) | letter → 0-25 index, e.g. `'c'-'a'==2` |

```java
int[] freq = new int[26];
for (char c : s.toCharArray()) freq[c - 'a']++;
```

---

## 5. String/Char → DSA patterns

**Palindrome (two-pointer):**
```java
boolean isPalindrome(String s) {
    int i = 0, j = s.length() - 1;
    while (i < j) {
        if (s.charAt(i) != s.charAt(j)) return false;
        i++; j--;
    }
    return true;
}
```

**Anagram (frequency array):**
```java
boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    int[] freq = new int[26];
    for (char c : a.toCharArray()) freq[c - 'a']++;
    for (char c : b.toCharArray()) freq[c - 'a']--;
    for (int f : freq) if (f != 0) return false;
    return true;
}
```

For general (non-lowercase-only) frequency counting → `HashMap<Character,Integer>` (Phase 2). Sliding window over strings also gets full treatment in Phase 2.

---

## 6. Arrays — fixed-size, themselves objects

```java
int[] a = new int[5];              // all zeros
int[] b = {1, 2, 3};               // literal
int[][] grid = new int[3][4];      // 3x4, all zeros
int[][] jagged = new int[3][];
jagged[0] = new int[]{1, 2};
```

| Method | Returns | Modifies? | Use | Time |
|---|---|---|---|---|
| `Arrays.sort(arr)` | void | yes, in place | nearly every sort-dependent problem | O(n log n) |
| `Arrays.fill(arr,val)` | void | yes | init DP/memo arrays | O(n) |
| `Arrays.copyOf(arr,len)` | new array | no | resize/pad | O(n) |
| `Arrays.copyOfRange(arr,i,j)` | new array | no | sub-array slice | O(n) |
| `Arrays.equals(a,b)` | boolean | no | content compare — never `==` | O(n) |
| `Arrays.binarySearch(arr,key)` | int (or negative) | no | array must be sorted first | O(log n) |
| `Arrays.asList(arr)` | fixed-size List view | — | trap: `.add()` throws |
| `Arrays.toString(arr)` | String | no | debugging (2D: `deepToString`) |

**Mistakes**: `arr1==arr2` compares references, not contents. `int[] copy = original;` copies the *reference*, not the array — use `Arrays.copyOf` or `.clone()`.

**2D traversal:**
```java
for (int r = 0; r < grid.length; r++)
    for (int c = 0; c < grid[0].length; c++) {
        // grid[r][c]
    }
```

**int[] ↔ List<Integer> trap:**
```java
// Arrays.asList(intArray) gives List<int[]> of size 1 — NOT what you want!
List<Integer> list = new ArrayList<>();
for (int x : arr) list.add(x);

int[] back = list.stream().mapToInt(Integer::intValue).toArray();
```

---

## 7. Quick reference — "I need to..." → tool

- Compare string content → `.equals()`, never `==`
- Build a string in a loop → `StringBuilder`
- Digit char → int → `c - '0'`
- Letter → 0-25 index → `c - 'a'`
- Sort an array → `Arrays.sort(arr)`
- Init a DP/memo array → `Arrays.fill(arr, val)`
- Real array copy → `Arrays.copyOf` / `.clone()`
- Fast search on sorted array → `Arrays.binarySearch`
- String → int → `Integer.parseInt`
- Min/max sentinel → `Integer.MAX_VALUE`/`MIN_VALUE`
- Avoid overflow in a product → cast one operand to `long` first
