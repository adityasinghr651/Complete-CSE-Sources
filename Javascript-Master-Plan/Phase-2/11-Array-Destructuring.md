# Topic 11: Array Destructuring

## WHY
Without destructuring, pulling multiple values out of an array means repetitive, verbose index-based code (`arr[0]`, `arr[1]`, `arr[2]`...). Destructuring lets you **unpack array values directly into named variables in one line**, which is both shorter and clearer about intent.

## WHAT
Array destructuring is a syntax that extracts values from an array (by position) into individual variables in a single statement.

## MENTAL MODEL

Think of it as **matching a shape** — the variables on the left mirror the positions in the array on the right:

```text
const [a, b, c] = [10, 20, 30];
       │   │   │      │    │    │
       └───┼───┼──────┘    │    │
           └───┼───────────┘    │
               └────────────────┘

a = 10, b = 20, c = 30 — matched by position, not name
```

## SYNTAX

```javascript
const arr = [10, 20, 30];

// Basic destructuring
const [a, b, c] = arr;
console.log(a, b, c); // 10 20 30

// Skipping elements with commas
const [first, , third] = arr;
console.log(first, third); // 10 30

// Default values (used if the position is undefined)
const [x, y, z, w = 100] = arr;
console.log(w); // 100 — arr[3] doesn't exist, so default kicks in

// Swapping variables — a classic use case
let p = 1, q = 2;
[p, q] = [q, p];
console.log(p, q); // 2 1

// Rest pattern — collect remaining elements
const [head, ...rest] = [1, 2, 3, 4];
console.log(head); // 1
console.log(rest); // [2, 3, 4]
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const [first, second] = [100, 200];
console.log(first); // 100
```

## MANUAL IMPLEMENTATION
Not something to build from scratch — it's syntax sugar over indexed access. The manual exercise is **rewriting your own earlier code** (from Topics 7–9) that used `arr[0]`, `arr[1]` style access, converting it to destructuring, to build the reflex.

## PRACTICAL USE
- Unpacking coordinate pairs (`const [x, y] = position;`)
- Extracting multiple return values from a function that returns an array (since functions can only officially `return` one value, returning an array and destructuring it is the common workaround)
- Swapping variables cleanly without a temporary variable
- Working with `Object.entries()` results — each entry is a `[key, value]` array

## EDGE CASES

1. **Destructuring more variables than array elements gives `undefined`** for the extras, not an error:
```javascript
const [a, b, c] = [1, 2];
console.log(c); // undefined
```

2. **Default values only apply when the value is `undefined`** — not for `null` or other falsy values:
```javascript
const [a = 10] = [undefined];
console.log(a); // 10 — default used

const [b = 10] = [null];
console.log(b); // null — default NOT used, null is a real value here

const [c = 10] = [0];
console.log(c); // 0 — default NOT used, 0 is a real value
```

3. **Destructuring works on any iterable, not just arrays** — including strings:
```javascript
const [firstChar, secondChar] = "hello";
console.log(firstChar, secondChar); // "h" "e"
```

4. **Nested array destructuring**:
```javascript
const [a, [b, c]] = [1, [2, 3]];
console.log(a, b, c); // 1 2 3
```

## COMMON MISTAKES
- Assuming destructuring matches by name instead of position (that's object destructuring's behavior — different pattern, not covered under this topic but worth noting exists)
- Forgetting that skipped positions use empty commas (`[a, , c]`), which is easy to miscount in longer arrays
- Expecting default values to override `null` (they don't — only `undefined` triggers a default)
- Using destructuring on something that isn't iterable (e.g., a plain object) and getting a `TypeError`

## DEBUGGING EXERCISE

```javascript
function getMinMax(arr) {
    let min = Math.min(...arr);
    let max = Math.max(...arr);
    return [min, max];
}

const [max, min] = getMinMax([5, 2, 8, 1]);
console.log(`Min: ${min}, Max: ${max}`);
```

> [!NOTE]
> *(Reason through it before scrolling: syntax error, runtime error, or logical error? What actually prints?)*
> 
> **Answer:** No error — this runs, but prints **swapped** values: `"Min: 1, Max: 8"` — wait, let's be precise. `getMinMax` returns `[min, max]` = `[1, 8]`. The caller destructures it as `const [max, min] = ...`, so **positionally**, `max` gets `1` (the actual min) and `min` gets `8` (the actual max) — the variable *names* on the left don't have to match the *meaning* of the values, only the *position* matters. So the log prints `"Min: 8, Max: 1"` — completely backwards from what the variable names suggest, purely because of destructuring order mismatch. This is a real, sneaky bug class: destructuring gives you no protection against mismatched variable naming vs. actual return order.

## WHERE THIS APPEARS IN REAL SOFTWARE
- React's `useState` hook returns a `[value, setterFunction]` array, which you destructure: `const [count, setCount] = useState(0)` — you'll use this exact pattern constantly once you reach React
- Functions returning multiple related values (min/max, success/error tuples) commonly use this pattern
- Iterating over `Object.entries(obj)` gives `[key, value]` pairs, unpacked via destructuring in a loop

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Array destructuring
>    ↓
> React useState/useReducer hook patterns (`const [state, setState] = useState(...)`)
>    ↓
> Iterating Map entries or Object.entries() in loops
> ```

## INTERVIEW QUESTION

**Q: What happens if you destructure an array into more variables than it has elements? What about fewer? And when do default values in destructuring actually apply?**

*Answer out loud before checking anything — cover the `undefined`-only default trigger explicitly, since that's the most commonly missed detail.*

---

## 💻 WRITE WITHOUT AI

1. Destructure `const arr = [1, 2, 3]` into `a, b, c` and log all three.
2. Destructure only the first and third elements of a 4-element array, skipping the second (use the comma-skip syntax).
3. Swap two variables `let p = "hello", q = "world"` using array destructuring (no temporary variable).
4. Write a function `divide(a, b)` that returns `[quotient, remainder]` as an array. Destructure the result when calling it and log both values clearly labeled.
5. (Harder) Use the rest pattern to write a function `getFirstAndRest(arr)` that destructures the first element and "the rest" in its parameter list directly (i.e., destructure inside the function signature, not the body), returning both as a formatted string.

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
