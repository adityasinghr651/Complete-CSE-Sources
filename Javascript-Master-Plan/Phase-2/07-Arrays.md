# Topic 7: Arrays

## WHY
Real programs rarely deal with just one value — you need to hold a list of users, a list of scores, a list of cart items. Without arrays, you'd need a separate variable for every single item (`user1`, `user2`, `user3`...), which breaks down completely once the count is unknown or large. Arrays give you **one variable that holds many values, in order**.

## WHAT
An array is an ordered, indexed collection of values. In JavaScript, arrays give us indexed access to multiple values under one variable — but under the hood, an array is actually a special kind of **object** with numeric keys and automatic `length` tracking.

## MENTAL MODEL

Think of an array as a **row of numbered lockers**, starting at locker `0`:

```text
index:    0        1        2        3
       ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
value: │ "a"  │ │ "b"  │ │ "c"  │ │ "d"  │
       └──────┘ └──────┘ └──────┘ └──────┘

arr.length === 4
arr[0] === "a"
arr[3] === "d"
arr[4] === undefined  (no locker there)
```

Indexing starts at `0`, not `1` — this trips up almost everyone at first.

## SYNTAX

```javascript
const fruits = ["apple", "banana", "cherry"];

console.log(fruits[0]);       // "apple"
console.log(fruits.length);   // 3

fruits[1] = "blueberry";      // reassign an element — allowed even with const!
fruits.push("date");          // add to end
fruits.pop();                 // remove from end
fruits.unshift("mango");      // add to start
fruits.shift();               // remove from start

console.log(fruits);
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const nums = [10, 20, 30];
console.log(nums[1]); // 20
```

## MANUAL IMPLEMENTATION
Not something to build from scratch (arrays are a built-in structure), but a useful exercise: implement a few array-like operations manually **before** learning the built-in methods, so you understand what's happening underneath. For example, write your own loop that finds the max value in an array — you'll appreciate `Math.max(...arr)` or array methods (Phase 9) much more once you've done it by hand.

## PRACTICAL USE
- Storing a list of users fetched from an API
- Storing a list of cart items with quantities
- Storing a sequence of moves in a game
- Storing form validation error messages to display

## EDGE CASES

1. **`const` doesn't freeze array contents** — only the variable binding is locked:
```javascript
const arr = [1, 2, 3];
arr.push(4);       // fine — mutates the array, not the binding
console.log(arr);  // [1, 2, 3, 4]

arr = [5, 6];       // TypeError — can't reassign the binding itself
```

2. **Accessing an out-of-range index returns `undefined`, not an error**:
```javascript
const arr = [1, 2, 3];
console.log(arr[10]); // undefined — no crash
```

3. **Arrays are objects under the hood** — `typeof` gives a misleading answer:
```javascript
console.log(typeof [1, 2, 3]); // "object" — NOT "array"
console.log(Array.isArray([1, 2, 3])); // true — the correct way to check
```

4. **Sparse arrays / holes**:
```javascript
const arr = [1, , 3]; // middle element is a "hole"
console.log(arr[1]); // undefined
console.log(arr.length); // 3 — length still counts the hole
```

5. **`length` can be manually set** to truncate an array (rare, but good to know):
```javascript
const arr = [1, 2, 3, 4, 5];
arr.length = 2;
console.log(arr); // [1, 2]
```

## COMMON MISTAKES
- Assuming `const` prevents modifying array contents (it doesn't — it only locks reassignment of the variable)
- Off-by-one errors when looping (`arr.length` vs `arr.length - 1` for last index)
- Confusing `push`/`pop` (end of array) with `unshift`/`shift` (start of array)
- Checking array type with `typeof` instead of `Array.isArray()`

## DEBUGGING EXERCISE

```javascript
const scores = [85, 92, 78];

function addScore(arr, newScore) {
    arr = arr.push(newScore);
}

addScore(scores, 100);
console.log(scores);
```

> [!NOTE]
> *(Reason through it before scrolling: what does `scores` actually contain after this runs, and why might someone expect something different?)*
> 
> **Answer:** `scores` becomes `[85, 92, 78, 100]` — `push` **does** mutate the original array in place, so the addition works. But the *reassignment* `arr = arr.push(newScore)` inside the function is misleading/wrong: `push()` returns the **new length** (`4`), not the array. So inside the function, `arr` now points to `4`, not the array — but this reassignment only affects the local `arr` variable inside `addScore`, not the original `scores` variable outside (parameters are separate bindings that just initially point to the same array). The mutation via `.push()` is what actually changed `scores`; the `arr = ...` line is dead code that does nothing useful and reveals a misunderstanding of what `push()` returns.

## WHERE THIS APPEARS IN REAL SOFTWARE
- API responses are frequently arrays of objects (list of users, list of products) — Phase 9's array methods (`map`, `filter`) exist specifically to transform this kind of data for UI display
- Shopping cart implementations are arrays of item objects
- Rendering lists in any UI framework starts from an array

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Arrays
>    ↓
> Array methods (map/filter/reduce) — Phase 9
>    ↓
> React list rendering (`items.map(item => <li>{item.name}</li>)`)
> ```

## INTERVIEW QUESTION

**Q: Why does `typeof []` return `"object"`? How do you correctly check if a value is an array?**

*Answer out loud before checking anything — explain that arrays are objects with special numeric-index behavior, and mention `Array.isArray()`.*

---

## 💻 WRITE WITHOUT AI

1. Create an array of 5 numbers. Log the first, the last (using `.length`, not a hardcoded index), and the total count.
2. Write a function `getLast(arr)` that returns the last element of any array **without hardcoding an index number** — it must work for arrays of any length.
3. Use `push` and `pop` on an array and log it after each operation to confirm your understanding of which end each affects.
4. Write a function `findMax(arr)` that manually loops through an array of numbers and returns the largest one (don't use `Math.max` — the point is to practice the loop-and-compare pattern by hand).
5. (Harder) Write a function `removeDuplicates(arr)` that takes an array of numbers and returns a new array with duplicates removed — using only loops and a manually-built result array (no built-in dedup methods yet, since you haven't covered them). Test with `[1, 2, 2, 3, 4, 4, 4, 5]`.

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
