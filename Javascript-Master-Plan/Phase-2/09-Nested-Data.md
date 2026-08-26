# Topic 9: Nested Data

## WHY
Real-world data isn't flat. A user has an address, which has a city and zip code. An order has a list of items, each with its own properties. To model this accurately, you need **objects inside objects**, **arrays inside objects**, and **objects inside arrays** — combined freely. Nested data is how JavaScript represents anything with real structure, especially JSON from APIs (Phase 8).

## WHAT
Nested data means objects and arrays containing other objects and arrays, to any depth. There's no new syntax here — it's the same object/array syntax from Topics 7–8, just composed together.

## MENTAL MODEL

Think of nesting as **boxes inside boxes inside boxes**:

```text
user (object)
 ├── name: "Aditya"
 ├── address (object)
 │     ├── city: "Ramnagar"
 │     └── zip: "244715"
 └── hobbies (array)
       ├── [0]: "coding"
       └── [1]: "chess"
```

To reach a deeply nested value, you **chain accessors** matching the structure exactly — one `.` or `[]` per level:

```javascript
user.address.city        // object → object
user.hobbies[0]          // object → array
```

## SYNTAX

```javascript
const user = {
    name: "Aditya",
    address: {
        city: "Ramnagar",
        zip: "244715"
    },
    hobbies: ["coding", "chess"],
    orders: [
        { id: 1, total: 500 },
        { id: 2, total: 750 }
    ]
};

console.log(user.address.city);      // "Ramnagar"
console.log(user.hobbies[1]);        // "chess"
console.log(user.orders[0].total);   // 500

// Updating nested values
user.address.city = "Nainital";
user.orders[1].total = 800;
user.hobbies.push("reading");
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const data = { list: [1, 2, 3] };
console.log(data.list[1]); // 2
```

## MANUAL IMPLEMENTATION
Not a primitive to build, but the essential manual skill is **reading structure before writing accessor code**. Given any nested JSON blob, you should be able to sketch the box-diagram (like above) on paper and know exactly which combination of `.` and `[]` reaches any given value, before touching the keyboard.

## PRACTICAL USE
- API responses almost always nest: a user object containing an array of orders, each order containing an array of items
- Game state (`{ player: { position: { x, y }, inventory: [...] } }`)
- Form data with grouped fields (`{ personal: {...}, address: {...} }`)
- Configuration files with grouped settings

## EDGE CASES

1. **Accessing through a missing intermediate level throws**:
```javascript
const user = { name: "Aditya" };
console.log(user.address.city); // TypeError: Cannot read properties of undefined (reading 'city')
```
This is the single most common runtime error when working with real API data — always confirm intermediate levels exist, especially with data you don't fully control.

2. **Mutating nested objects/arrays affects the original**, even though the outer object is `const` — reference semantics apply at every level, not just the top:
```javascript
const original = { config: { theme: "dark" } };
const copy = original; // NOT a real copy — same reference
copy.config.theme = "light";
console.log(original.config.theme); // "light" — original changed too!
```

3. **Shallow copying (`{ ...obj }` or `Array.from`) only copies one level deep** — nested objects/arrays are still shared references:

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS** (spread syntax `{...obj}` for copying isn't explicitly on your list under Phase 2, though `Object.freeze()` in Phase 6 is related — I'll introduce spread properly if/when it's needed, or you can ask for it early since it's very commonly used).

4. **Arrays of objects require indexing THEN dot notation**, in the right order:
```javascript
const orders = [{ id: 1 }, { id: 2 }];
console.log(orders[0].id); // 1 — index first, then property
console.log(orders.id);    // undefined — arrays don't have an "id" property directly
```

## COMMON MISTAKES
- Chaining accessors in the wrong order (property before index, or vice versa)
- Assuming a nested property always exists and crashing on real-world/API data
- Believing that copying an outer object also creates independent copies of nested structures
- Getting lost in deeply nested structures without first sketching the shape on paper

## DEBUGGING EXERCISE

```javascript
const company = {
    name: "TechCorp",
    departments: [
        { name: "Engineering", employees: 50 },
        { name: "Sales", employees: 30 }
    ]
};

function getEmployeeCount(company, deptName) {
    for (let i = 0; i < company.departments.length; i++) {
        if (company.departments.i.name === deptName) {
            return company.departments.i.employees;
        }
    }
}

console.log(getEmployeeCount(company, "Sales"));
```

> [!NOTE]
> *(Reason through it before scrolling: syntax error, runtime error, or logical error? What's actually wrong?)*
> 
> **Answer:** No syntax error — this runs without crashing, but it's a **logical error** returning `undefined`. `company.departments.i` uses dot notation with the literal word `"i"` as a property name — but `departments` is an array, and there's no property called `i` on it (dot notation never evaluates a variable). It should be `company.departments[i]` (bracket notation, using the loop variable `i` as a dynamic index). Because `company.departments.i` is `undefined`, `.name` access on it would actually throw a `TypeError` (`Cannot read properties of undefined`) the moment it's evaluated — so in practice this crashes on the first iteration, not silently returns `undefined`. Good catch to walk through: it's a runtime error, not silent.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Every non-trivial API response (Phase 8) is nested JSON — a user with nested address/orders/preferences
- Redux/state-management patterns (not on your syllabus, just context) heavily nest application state
- Game state objects naturally nest (player → inventory → items → item properties)

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Nested objects/arrays
>    ↓
> Parsing API JSON responses (Phase 8)
>    ↓
> React props drilling nested data down through components
> ```

## INTERVIEW QUESTION

**Q: Given a nested object like `{ user: { orders: [{ id: 1, items: [...] }] } }`, how would you safely access a deeply nested value without crashing if a level is missing? What would you check before accessing it?**

*Answer out loud before checking anything — describe checking each intermediate level exists (e.g., with `if` checks) since optional chaining isn't part of your syllabus yet.*

---

## 💻 WRITE WITHOUT AI

1. Create a `person` object with a nested `address` object (`city`, `state`). Log the city using chained dot notation.
2. Create a `classroom` object with a `students` array of objects (`{ name, grade }`, at least 3 students). Log the name of the second student.
3. Write a function `findStudentGrade(classroom, name)` that loops through `classroom.students` and returns the grade of the student matching `name` (return `null` if not found).
4. Deliberately access a nested property that doesn't exist (e.g., `person.job.title` when `job` was never defined) and observe the exact error message. Write it down.
5. (Harder) Build a `company` object with a `departments` array, where each department has a `name` and an `employees` array of `{ name, salary }` objects. Write a function `getTotalPayroll(company, deptName)` that finds the matching department and manually sums the salaries of its employees using nested loops.

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
