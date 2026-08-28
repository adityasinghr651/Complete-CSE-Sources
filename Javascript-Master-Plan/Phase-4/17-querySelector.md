# Topic 17: querySelector

## WHY
Up to now, all your code has run in the console with no visible page. Real applications need to **read and change what's on screen** — update text, respond to clicks, show/hide elements. Before you can change anything on a page, you first need a way to **find** the HTML element you want to work with. `querySelector` is that lookup tool.

## WHAT
`querySelector` lets JavaScript search the HTML document for an element matching a CSS selector, and returns a reference to it so you can read or modify it.

## THE BIG PICTURE FIRST

```text
JavaScript
   ↓
Browser
   ↓
HTML document (the .html file loaded)
   ↓
DOM tree (browser's in-memory object representation of that HTML)
   ↓
JavaScript selects/modifies nodes (this is what querySelector does — step 1: select)
   ↓
Browser re-renders the updated page
```

The **DOM (Document Object Model)** is the browser's live, in-memory tree representation of your HTML. When the browser loads `index.html`, it doesn't just display the raw text — it builds a tree of "node" objects that JavaScript can access and manipulate. `querySelector` is how you reach into that tree and grab a specific node.

```html
<body>
  <h1 id="title">Hello</h1>
  <p class="intro">Welcome</p>
  <button id="btn">Click me</button>
</body>
```

becomes, conceptually, this tree:

```text
body
 ├── h1#title
 ├── p.intro
 └── button#btn
```

## MENTAL MODEL

Think of `querySelector` as **CSS selector syntax used as a search query** — if you know how to target an element in a `.css` file, you already know how to find it in JavaScript.

## SYNTAX

```javascript
// By ID (use # like in CSS)
const title = document.querySelector("#title");

// By class (use . like in CSS)
const intro = document.querySelector(".intro");

// By tag name
const firstButton = document.querySelector("button");

// Combinators work too, same as CSS
const nested = document.querySelector("div.container > p");

// querySelectorAll — returns ALL matches, as a NodeList (not a live array, but array-like)
const allParagraphs = document.querySelectorAll("p");
```

## SMALLEST POSSIBLE EXAMPLE

```html
<body>
  <h1 id="title">Hello</h1>
  <script>
    const heading = document.querySelector("#title");
    console.log(heading); // logs the actual <h1> element
  </script>
</body>
```

## MANUAL IMPLEMENTATION
Not something to build — `querySelector` is a browser-provided API on the `document` object. The manual skill here is **reading CSS selector syntax fluently** and predicting exactly which element(s) a given selector will match, before running the code — since a wrong selector doesn't throw an error, it just silently returns `null` or an empty list (see edge cases).

## PRACTICAL USE
- Grabbing a form input to read its value on submit
- Grabbing a button to attach a click handler (Phase 5)
- Grabbing a container element to insert dynamically created content into (Topic 20)
- Grabbing an element to toggle a CSS class on (e.g., showing/hiding a modal)

## EDGE CASES

1. **No match returns `null`, not an error** — but using the result afterward without checking will throw:
```javascript
const missing = document.querySelector("#doesNotExist");
console.log(missing); // null
console.log(missing.textContent); // TypeError: Cannot read properties of null
```
This is the single most common DOM bug for beginners — always be sure the selector actually matches something before using the result, especially if the HTML might not have loaded yet (see edge case 3).

2. **`querySelector` returns only the FIRST match**, even if multiple elements match the selector:
```javascript
// <p>One</p><p>Two</p><p>Three</p>
document.querySelector("p"); // only <p>One</p>
```
Use `querySelectorAll` to get all matches.

3. **Running your script before the HTML below it exists** — if your `<script>` tag is in the `<head>` (or otherwise runs before the body's elements are parsed), `querySelector` will find nothing yet, because those DOM nodes don't exist in the tree at that point:
```html
<head>
  <script>
    const title = document.querySelector("#title"); // null — #title doesn't exist yet!
  </script>
</head>
<body>
  <h1 id="title">Hello</h1>
</body>
```
This is exactly why script tags are commonly placed at the **end of `<body>`**, or run after a `DOMContentLoaded` event.

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS**: `DOMContentLoaded` and script-loading strategies (`defer`, `async`) aren't explicitly on your syllabus, but the "script tag placement" issue above is directly relevant to every DOM exercise you'll do, so I'm flagging the *symptom* now. If you want, I can briefly cover `DOMContentLoaded` when we hit event handling in Phase 5, since it's genuinely useful — your call.

4. **`querySelectorAll` returns a NodeList, not a real array** — it has `.forEach()` but NOT `.map()`/`.filter()` directly (those are true array methods, covered in Phase 9):
```javascript
const items = document.querySelectorAll("li");
items.forEach(item => console.log(item)); // works
items.map(item => item.textContent); // TypeError: items.map is not a function
```

## COMMON MISTAKES
- Forgetting `#` for IDs or `.` for classes (writing `querySelector("title")` instead of `querySelector("#title")` — this searches for a `<title>` *tag*, not an element with `id="title"`)
- Not checking for `null` before using the result
- Placing `<script>` in `<head>` without deferring it, then being confused why elements aren't found
- Assuming `querySelectorAll` behaves exactly like a real array

## DEBUGGING EXERCISE

```html
<body>
  <script>
    const button = document.querySelector("#submitBtn");
    button.textContent = "Clicked!";
  </script>
  <button id="submitBtn">Submit</button>
</body>
```

> [!NOTE]
> *(Reason through it: syntax error, runtime error, or logical error? What actually happens when this page loads?)*
> 
> **Answer:** Runtime error — `TypeError: Cannot set properties of null (setting 'textContent')`. The `<script>` tag runs **before** the `<button>` element below it has been parsed into the DOM, so `document.querySelector("#submitBtn")` returns `null` at that point. Calling `.textContent = "..."` on `null` throws immediately. The fix: move the `<script>` tag to *after* the button in the HTML (or at the very end of `<body>`), so the DOM node exists by the time the script runs.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Every piece of vanilla JS that reads or updates a webpage starts with selecting an element
- Form handling (Phase 11) always begins with selecting the form/input elements
- Even in frameworks like React (which you'll reach later), the underlying browser still ultimately uses DOM nodes — React just manages that layer for you, but understanding raw `querySelector` demystifies what's happening underneath

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> querySelector (manual DOM lookup)
>    ↓
> React's virtual DOM (abstracts this away — you rarely call querySelector directly in React)
>    ↓
> useRef in React (the rare cases where you DO need direct DOM access)
> ```

## INTERVIEW QUESTION

**Q: What's the difference between `querySelector` and `querySelectorAll`? What does each return when there's no match, and how would you defensively guard against that?**

*Answer out loud before checking anything — cover: single element vs NodeList, `null` vs empty NodeList (not `null`!) as the "no match" result, and an `if (element)` check as the guard.*

---

## 💻 WRITE WITHOUT AI

For these, create a real `.html` file (with a `<script>` tag, placed correctly at the end of `<body>`) — you need an actual page to select elements from.

1. Create an HTML page with an `<h1 id="heading">` and a `<script>` that selects it by ID and logs it to the console.
2. Add a `<p class="description">` and select it by class. Log its `textContent` (you'll formally cover `textContent` next topic, but you can peek at reading it here).
3. Add three `<li>` elements inside a `<ul>`. Use `querySelectorAll("li")` and loop over the result with `.forEach()`, logging each item.
4. Deliberately select an element that doesn't exist (`#nothingHere`) and log the result — confirm it's `null`, then write an `if` check that logs `"Element not found"` instead of crashing when you try to use it.
5. (Harder) Move your `<script>` tag to the `<head>` (without `defer`) and try selecting an element that comes later in the `<body>`. Confirm you get `null` and understand exactly why — then move the script back to fix it.

*(Reply with what you built/observed, or where you get stuck, before I give hints or a reference solution).*
