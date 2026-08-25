# Topic 2: Functions

## WHY
Without functions, you'd repeat the same logic over and over wherever you need it. If you needed to fix a bug in that logic, you'd have to fix it in every place it was copied. Functions let you **name a piece of logic once, and reuse it** — this is the foundation of writing maintainable code.

## WHAT
A function is a reusable block of code that takes input (parameters), does something, and optionally returns output. JavaScript has several ways to write one.

## MENTAL MODEL

Think of a function like a **machine**:

```text
   input(s)
      │
      ▼
 ┌─────────┐
 │ FUNCTION│   (does something with the input)
 └─────────┘
      │
      ▼
   output (return value)
```

You give it raw material (arguments), it processes them (the function body), and it hands back a result (the `return` value) — or hands back nothing (`undefined`) if there's no `return`.

## SYNTAX

There are three common ways to write a function:

```javascript
// 1. Function declaration
function add(a, b) {
    return a + b;
}

// 2. Function expression
const subtract = function(a, b) {
    return a - b;
};

// 3. Arrow function
const multiply = (a, b) => {
    return a * b;
};

// Arrow function shorthand (implicit return, single expression)
const divide = (a, b) => a / b;
```

> [!IMPORTANT]
> **Key distinction to know now:** **Function declarations are hoisted** (usable before they appear in the file); **function expressions and arrow functions are not** — they follow the `let`/`const` rules from Topic 1.

## SMALLEST POSSIBLE EXAMPLE

```javascript
function greet() {
    return "Hello";
}

console.log(greet()); // "Hello"
```

Note the two separate steps: **defining** the function (`function greet() {...}`) vs **calling** it (`greet()`). A very common beginner confusion is forgetting the `()` to actually call it — `console.log(greet)` just prints the function's source, it doesn't run it.

## MANUAL IMPLEMENTATION
Not applicable in the "build it from scratch" sense — functions are a language primitive. The manual exercise here is understanding **parameters vs arguments**:

- **Parameter**: the name in the function definition (`a`, `b` above) — a placeholder.
- **Argument**: the actual value you pass in when calling (`add(3, 4)` → `3` and `4` are arguments).

## PRACTICAL USE
- Validating a form input (`validateEmail(input)`)
- Calculating a total price (`calculateTotal(cartItems)`)
- Formatting a date for display (`formatDate(rawDate)`)
- Handling a button click (the function you pass to `addEventListener`)

## EDGE CASES

1. **Missing arguments become `undefined`**, not an error:
```javascript
function add(a, b) {
    return a + b;
}
console.log(add(5)); // NaN  (5 + undefined)
```

2. **Extra arguments are silently ignored**:
```javascript
add(1, 2, 3, 4); // extra args (3, 4) do nothing unless you use `arguments` or rest params
```

3. **No `return` statement means the function returns `undefined`**:
```javascript
function logSum(a, b) {
    console.log(a + b); // prints, but doesn't return
}
const result = logSum(2, 3); // logs 5
console.log(result); // undefined
```

4. **Arrow functions with implicit return need care with objects**:
```javascript
const makeUser = (name) => { name: name }; // WRONG — {} is parsed as a block, not an object
const makeUserFixed = (name) => ({ name: name }); // correct — wrap in parens
```

## COMMON MISTAKES
- Forgetting `return` and expecting a value to come out
- Confusing calling a function (`greet()`) with referencing it (`greet`)
- Not realizing arrow functions don't have their own `this` (we'll hit this hard in Phase 6 — OOP)
- Assuming function declarations and expressions behave identically regarding hoisting

## DEBUGGING EXERCISE

What's wrong here? Reason through it before scrolling down mentally.

```javascript
function calculateArea(width, height) {
    width * height;
}

const area = calculateArea(5, 10);
console.log(`Area is ${area}`);
```

> [!NOTE]
> *(Think about: syntax error? runtime error? logical error? What will `area` actually be, and why?)*
> 
> **Answer:** No syntax error — it runs fine. But there's no `return`, so `calculateArea` returns `undefined`. `area` will be `undefined`, and the log will print `"Area is undefined"`. This is a **logical error**, the most dangerous kind because JS gives you no warning.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Every API handler, every button click, every data transformation is a function
- Codebases are essentially trees of small functions calling other small functions — breaking logic into well-named functions is core to readable code
- Pure functions (same input → same output, no side effects) are preferred in many codebases because they're easier to test and reason about

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> JavaScript functions
>    ↓
> React components (a component is just a function that returns UI)
>    ↓
> Node.js request handlers
>    ↓
> Express route controllers
> ```

## INTERVIEW QUESTION

**Q: What's the difference between a function declaration and a function expression? Why does it matter?**

*Answer out loud before checking anything. Cover: hoisting behavior, and a practical consequence (e.g., you can call a declared function before its line in the file, but not an expression-based one).*

---

## 💻 WRITE WITHOUT AI

1. Write a function `isEven(number)` that returns `true` if a number is even, `false` otherwise. Test it with `4` and `7`.
2. Write a function `greetUser(name)` using arrow function syntax that returns `"Hello, " + name`. Call it and log the result.
3. Write a function `square(n)` that returns `n * n`. Call it *without* the parentheses (`console.log(square)`) and observe what prints — write a comment explaining why.
4. Write a function `sumAll(a, b, c)` and deliberately call it with only two arguments. Predict the output before running, then verify.
5. (Harder) Write a function `makePerson(name, age)` using an arrow function with implicit return that returns an **object** `{ name, age }`. Get the object-literal-in-arrow-function syntax right without me showing you first.

*(Reply with your attempts (or where you're stuck) before checking any reference).*
