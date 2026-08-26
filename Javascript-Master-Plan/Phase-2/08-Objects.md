# Topic 8: Objects

## WHY
Arrays are great for ordered lists of similar things, but real-world data usually has **named properties**, not just positions — a user has a name, an age, an email. Using an array (`user[0]` = name, `user[1]` = age) would be unreadable and fragile. Objects let you store data as **key-value pairs**, where each value has a meaningful label.

## WHAT
An object is an unordered collection of key-value pairs. Keys (also called properties) are strings (or Symbols), and values can be anything — numbers, strings, arrays, functions, even other objects.

## MENTAL MODEL

Think of an object as a **labeled filing cabinet**, not numbered lockers:

```text
        ┌─────────────────────────┐
        │  user                   │
        │  ┌───────┬─────────────┐│
        │  │ name  │ "Aditya"    ││
        │  ├───────┼─────────────┤│
        │  │ age   │ 21          ││
        │  ├───────┼─────────────┤│
        │  │ email │ "a@x.com"   ││
        │  └───────┴─────────────┘│
        └─────────────────────────┘
```

You retrieve values by **key name**, not position.

## SYNTAX

```javascript
const user = {
    name: "Aditya",
    age: 21,
    email: "a@x.com"
};

console.log(user.name);       // dot notation — "Aditya"
console.log(user["age"]);     // bracket notation — 21

user.age = 22;                // update existing property
user.city = "Ramnagar";       // add a new property
delete user.email;            // remove a property

console.log(user);
```

**Dot vs bracket notation** — dot notation requires a fixed, known key name. Bracket notation lets you use a **variable** or a key with special characters/spaces:

```javascript
const key = "name";
console.log(user[key]);       // "Aditya" — dynamic key lookup
console.log(user.key);        // undefined — this looks for a literal property called "key"
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const point = { x: 5, y: 10 };
console.log(point.x); // 5
```

## MANUAL IMPLEMENTATION
Not a build-from-scratch primitive, but a good manual exercise: given a real-world entity (a book, a movie, a student), design the object shape yourself — decide what keys it needs and what types the values should be, before writing any code. This "data modeling" instinct matters more in practice than syntax.

## PRACTICAL USE
- Representing a single user, product, or form's data
- Configuration objects (settings passed into a function)
- API responses are almost always JSON — which maps directly to JS objects (Phase 8 will make this concrete)
- Representing game entities (`{ x, y, health, speed }` — directly useful in Phase 12)

## EDGE CASES

1. **Accessing a non-existent property returns `undefined`, not an error**:
```javascript
const user = { name: "Aditya" };
console.log(user.age); // undefined — no crash
```

2. **Nested property access on `undefined` throws**:
```javascript
console.log(user.address.city); // TypeError: Cannot read properties of undefined
```
This is extremely common with API data — you'll want optional chaining (`user.address?.city`) later; flagging it now, not teaching it yet since it's not on your list. 

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS** (optional chaining `?.` — very useful, but I won't teach it in depth unless you want me to insert it; your syllabus doesn't list it).

3. **Objects compared with `===` compare reference, not contents**:
```javascript
const a = { x: 1 };
const b = { x: 1 };
console.log(a === b); // false — different objects in memory, even though contents match
console.log(a === a); // true — same reference
```

4. **`const` on an object allows mutating properties, just like arrays**:
```javascript
const obj = { count: 0 };
obj.count = 5; // fine
obj = {};      // TypeError — can't reassign the binding
```

5. **Keys are always strings internally**, even if you write them as numbers:
```javascript
const obj = { 1: "one" };
console.log(obj["1"]); // "one"
console.log(obj[1]);   // "one" — coerced to string automatically
```

## COMMON MISTAKES
- Using dot notation with a dynamic/variable key (should use bracket notation instead)
- Assuming `const` prevents modifying object properties (it doesn't)
- Comparing two objects with `===` and expecting content equality
- Accessing deeply nested properties without checking intermediate levels exist first, causing a `TypeError`

## DEBUGGING EXERCISE

```javascript
const settings = { theme: "dark", fontSize: 14 };

function updateSetting(obj, key, value) {
    obj[key] = value;
}

updateSetting(settings, "theme", "light");
console.log(settings.theme);

const key = "fontSize";
console.log(settings.key);
```

> [!NOTE]
> *(Reason through both logs before scrolling: what prints, and why might the second one surprise someone?)*
> 
> **Answer:** First log: `"light"` — the function mutates the object via bracket notation with a dynamic key, and since objects are passed by reference, the change is visible outside the function. Second log: `undefined` — `settings.key` uses **dot notation with the literal word `"key"`**, not the variable `key`'s value (`"fontSize"`). Dot notation never evaluates a variable as a key; you'd need `settings[key]` to get `14`.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Every API response you'll ever fetch (Phase 8) comes back as JSON, which parses directly into JS objects
- Configuration objects are everywhere (build tool configs, function options, app settings)
- Representing a single "record" — a user, a product, a game entity — is almost always an object

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Objects
>    ↓
> Props passed to React components (`<UserCard user={userObject} />`)
>    ↓
> Request/response bodies in Node.js/Express APIs
> ```

## INTERVIEW QUESTION

**Q: What's the difference between dot notation and bracket notation for accessing object properties? When would you be forced to use bracket notation?**

*Answer out loud before checking anything — cover the dynamic-key case and keys with special characters/spaces as your examples.*

---

## 💻 WRITE WITHOUT AI

1. Create an object representing a book with `title`, `author`, and `pages`. Log each property using dot notation.
2. Add a new property `isRead: false` to your book object after creating it, then update it to `true`. Log the object after each step.
3. Write a function `getProperty(obj, key)` that returns `obj[key]` — call it with a variable key (not hardcoded) to prove you understand why bracket notation is needed here.
4. Create two separate objects with identical contents (e.g., `{ x: 1 }` each) and compare them with `===`. Predict the result in a comment before running it.
5. (Harder) Create a `student` object with `name`, `grades` (an array of numbers), and write a function `getAverage(student)` that manually loops through `student.grades` and returns the average. This combines Topic 7 (arrays) and Topic 8 (objects) — a preview of Topic 9, Nested data.

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
