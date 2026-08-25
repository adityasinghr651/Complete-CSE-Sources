# Topic 5: Loops

## WHY
A huge amount of programming is "do this thing for every item in a collection" or "keep doing this until a condition is met." Without loops, you'd have to write out the same line of code manually for every repetition — impossible once the number of repetitions is unknown or large (e.g., "for every user in the database").

## WHAT
A loop repeats a block of code either a fixed number of times, while a condition holds, or once per item in a collection. JavaScript's main loop forms: `for`, `while`, `do...while`, and `for...of` / `for...in`.

## MENTAL MODEL

Think of a loop as a **repeating cycle with an exit condition**:

```text
   ┌─────────────────────┐
   │  check condition    │◄────────┐
   └─────────────────────┘         │
        │ true         │ false     │
        ▼              ▼           │
   run loop body    exit loop      │
        │                          │
        └──────────────────────────┘
             (update, go check again)
```

## SYNTAX

```javascript
// Classic for — you control init, condition, update
for (let i = 0; i < 5; i++) {
    console.log(i); // 0,1,2,3,4
}

// while — condition checked before each run
let count = 0;
while (count < 3) {
    console.log(count);
    count++;
}

// do...while — body runs at least once, condition checked after
let n = 0;
do {
    console.log(n);
    n++;
} while (n < 3);

// for...of — iterate over VALUES of an iterable (arrays, strings)
const fruits = ["apple", "banana"];
for (const fruit of fruits) {
    console.log(fruit);
}

// for...in — iterate over KEYS of an object (avoid on arrays)
const user = { name: "Aditya", age: 21 };
for (const key in user) {
    console.log(key, user[key]);
}
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
for (let i = 0; i < 3; i++) {
    console.log(i);
}
// 0
// 1
// 2
```

## MANUAL IMPLEMENTATION
Loops are a language primitive, but a very useful manual exercise is **dry-running** — tracing the value of each loop variable, iteration by iteration, on paper, before you run the code. This is exactly what you'll do for Bubble/Selection/Insertion sort in Phase 3, so building this habit now pays off directly.

## PRACTICAL USE
- Iterating over a list of users to render a UI list
- Summing values in an array (before you learn `reduce()` in Phase 9, this is done manually with a loop)
- Repeating a request/retry a fixed number of times
- Building game loops (Phase 12) — though those use a different repeating mechanism (`requestAnimationFrame`), the "repeat until a condition" concept is the same

## EDGE CASES

1. **Infinite loops** — forgetting to update the condition variable:
```javascript
let i = 0;
while (i < 5) {
    console.log(i);
    // forgot i++ — this never stops, will crash/freeze
}
```

2. **Off-by-one errors** — extremely common:
```javascript
const arr = [10, 20, 30];
for (let i = 0; i <= arr.length; i++) { // <= instead of <
    console.log(arr[i]); // last iteration: arr[3] is undefined
}
```

3. **`for...in` on arrays** iterates over indices as **strings**, and can include unexpected inherited properties in rare cases — this is why `for...of` or array methods (Phase 9) are preferred for arrays:
```javascript
const arr = [10, 20, 30];
for (const i in arr) {
    console.log(typeof i); // "string", not "number" — can bite you if you do math on it
}
```

4. **`break` and `continue`**:
```javascript
for (let i = 0; i < 5; i++) {
    if (i === 2) continue; // skip this iteration
    if (i === 4) break;    // stop the loop entirely
    console.log(i);
}
// 0, 1, 3
```

## COMMON MISTAKES
- Forgetting to increment/update the loop variable → infinite loop
- Off-by-one errors with `<` vs `<=`
- Using `for...in` on arrays instead of `for...of`
- Modifying the array you're looping over while looping over it (causes skipped elements or bugs) — a subtle one worth just being aware of now
- Redeclaring the loop variable name inside the loop body, shadowing it accidentally

## DEBUGGING EXERCISE

```javascript
function sumArray(arr) {
    let total = 0;
    for (let i = 0; i <= arr.length; i++) {
        total += arr[i];
    }
    return total;
}

console.log(sumArray([1, 2, 3]));
```

> [!NOTE]
> *(Reason through it: syntax error, runtime error, or logical error? What does this actually return, and why?)*
> 
> **Answer:** No crash, but a logical error. `i <= arr.length` means the loop runs one extra time with `i = 3`, where `arr[3]` is `undefined`. `total += undefined` makes `total` become `NaN` for the rest of the loop. Result: `NaN`, not `6`. This is a classic off-by-one bug — should be `i < arr.length`.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Rendering lists of data (products, comments, search results) — under the hood, something is looping over an array
- Batch processing (retry logic, processing queued jobs)
- Data validation across multiple fields
- Game loops and animation frames (Phase 12) rely on the same "repeat with a condition" idea, just driven by the browser's rendering clock instead of a plain loop

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> for/while loops
>    ↓
> Array methods (map/filter/reduce) — Phase 9, these are loops with more specific intent
>    ↓
> React list rendering (.map() over data to produce UI elements)
> ```

## INTERVIEW QUESTION

**Q: What's the difference between `for...of` and `for...in`? Why is `for...in` generally discouraged for arrays?**

*Answer out loud before checking anything. Cover: values vs keys, and the "string index" gotcha above.*

---

## 💻 WRITE WITHOUT AI

1. Write a `for` loop that prints numbers 1 to 10.
2. Write a `while` loop that prints only even numbers from 2 to 20.
3. Write a function `sumArray(arr)` using a `for` loop (get the bounds right — no off-by-one). Test with `[1,2,3,4]` and confirm the sum is `10`.
4. Write a loop over the string `"hello"` using `for...of`, printing each character on its own line.
5. (Harder) Write a loop that prints numbers 1–20, but skips multiples of 3 (`continue`) and stops entirely once it hits 15 (`break`). Predict the full output on paper before running it.

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
