# Topic 6: Template Literals

## WHY
Before template literals, building strings with variables meant messy string concatenation with `+`, which gets hard to read fast — especially with multiple variables or multi-line text. Template literals solve this by letting you embed variables and expressions directly inside a string.

## WHAT
A template literal is a string wrapped in **backticks** (`` ` ``) instead of quotes, which supports:
- **Interpolation** — embedding variables/expressions with `${...}`
- **Multi-line strings** without special characters

## MENTAL MODEL

Think of a template literal as a string with **fill-in-the-blank slots**:

```text
`Hello, ${name}! You are ${age} years old.`
         └──┬──┘              └──┬──┘
         slot filled          slot filled
         with variable        with variable
         value at runtime     value at runtime
```

JavaScript evaluates whatever is inside `${ }`, converts it to a string, and drops it into place.

## SYNTAX

```javascript
const name = "Aditya";
const age = 21;

// Old way — concatenation
const oldMsg = "Hello, " + name + "! You are " + age + " years old.";

// Template literal
const newMsg = `Hello, ${name}! You are ${age} years old.`;

// Multi-line, no \n needed
const multiLine = `Line one
Line two
Line three`;

// Expressions inside ${} — not just variables
const price = 100;
const tax = 0.18;
console.log(`Total: ${price + price * tax}`);
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const x = 5;
console.log(`x is ${x}`); // "x is 5"
```

## MANUAL IMPLEMENTATION
Not a primitive to build, but a useful manual exercise: take a few of your earlier concatenation-based logs (from Topics 1–5) and **rewrite them as template literals** — that's the actual muscle you're building.

## PRACTICAL USE
- Building dynamic UI text ("You have 3 items in your cart")
- Constructing URLs with dynamic parts (`` `https://api.example.com/users/${userId}` ``)
- Generating HTML strings dynamically (Phase 4 — DOM manipulation will use this constantly)
- Logging formatted debug messages

## EDGE CASES

1. **`${}` can hold any expression, not just a variable name** — including function calls and ternaries:
```javascript
const age = 20;
console.log(`Status: ${age >= 18 ? "adult" : "minor"}`);
```

2. **Nesting template literals** works but gets hard to read fast — use sparingly:
```javascript
const name = "Aditya";
console.log(`Hi ${ `${name}` }`); // valid, but unnecessary here
```

3. **Objects inside `${}` don't auto-format nicely**:
```javascript
const user = { name: "Aditya" };
console.log(`User: ${user}`); // "User: [object Object]" — not what you want
console.log(`User: ${user.name}`); // "User: Aditya" — correct
```

4. **Backticks are NOT interchangeable with regular quotes for `${}` to work** — this only works inside backticks:
```javascript
console.log("Value: ${x}"); // literally prints "Value: ${x}" — no interpolation
console.log(`Value: ${x}`); // interpolates correctly
```

## COMMON MISTAKES
- Using regular quotes (`'` or `"`) and expecting `${}` to interpolate — it silently doesn't (no error, just wrong output).
- Forgetting `.property` access when interpolating an object, getting `[object Object]`.
- Overusing nested template literals, hurting readability instead of helping it.

## DEBUGGING EXERCISE

```javascript
const item = "coffee";
const price = 4.5;
const message = "You bought ${item} for $${price}";
console.log(message);
```

> [!NOTE]
> *(Reason through it: syntax error, runtime error, or logical error? What actually prints?)*
> 
> **Answer:** No error. This prints the literal text `You bought ${item} for $${price}` with no interpolation at all, because it uses double quotes (`"`) instead of backticks. `${}` only has special meaning inside backtick strings — inside regular quotes it's just plain text. This is a logical error that's easy to miss since nothing crashes.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Constructing API endpoint URLs with dynamic IDs
- Generating dynamic class names or inline styles (very common pattern once you hit React)
- Building readable log messages and error messages with contextual data
- SQL query building in backend code (though production code typically uses parameterized queries instead of raw template literals for security — flagging this as a real-world consideration, not something to implement now)

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Template literals
>    ↓
> Dynamic JSX text in React (`Hello, {name}` — similar idea, different syntax)
>    ↓
> Constructing fetch() URLs dynamically (Phase 8)
> ```

## INTERVIEW QUESTION

**Q: What are template literals, and what two problems do they solve compared to string concatenation with `+`?**

*Answer out loud before checking anything. Cover: readability/interpolation, and multi-line string support.*

---

## 💻 WRITE WITHOUT AI

1. Rewrite this concatenation as a template literal: `"My name is " + name + " and I am " + age + " years old."`
2. Write a template literal that embeds a calculation directly, e.g., print `` `The total is ${price * quantity}` `` with real variables.
3. Create a multi-line template literal that formats a small "receipt" — item name, price, and a total line — across 3 lines, no `\n` needed.
4. Deliberately write a string using regular quotes with `${}` inside it, run it, and observe/explain in a comment why it doesn't interpolate.
5. (Harder) Write a template literal that uses a ternary inside `${}` to print `"Pass"` or `"Fail"` based on a `score` variable compared to a passing threshold (e.g., 40).

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
