# Topic 18: innerHTML

## WHY
Once you've selected an element with `querySelector`, you'll often want to **change what's inside it** — swap out text, insert new HTML structure, build a list dynamically. `innerHTML` is one of the primary tools for reading or replacing the content inside an element.

## WHAT
`innerHTML` is a property on a DOM element that gets or sets the HTML markup **inside** that element, as a string. Setting it re-parses that string as HTML and replaces the element's children.

## MENTAL MODEL

Think of `innerHTML` as **the raw HTML source code living between an element's opening and closing tags**, exposed as a readable/writable string:

```html
<div id="box">
  <p>Hello <b>world</b></p>
</div>
```

```javascript
const box = document.querySelector("#box");
console.log(box.innerHTML);
// "\n  <p>Hello <b>world</b></p>\n"
```

Setting it replaces everything inside:

```javascript
box.innerHTML = "<h2>New content</h2>";
// <div id="box"><h2>New content</h2></div>
```

## SYNTAX

```javascript
const container = document.querySelector("#container");

// Reading
console.log(container.innerHTML);

// Setting — replaces ALL existing children
container.innerHTML = "<p>New paragraph</p>";

// Appending (careful — this re-parses everything, see edge cases)
container.innerHTML += "<p>Another paragraph</p>";
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const box = document.querySelector("#box");
box.innerHTML = "<strong>Bold text</strong>";
```

## MANUAL IMPLEMENTATION
Not a primitive to build — it's a browser API property. The relevant manual exercise: practice **building HTML strings using template literals** (Topic 6) and assigning them to `innerHTML`, since this combination is extremely common in real code — e.g., `container.innerHTML = \`<li>${item.name}</li>\`;`.

## PRACTICAL USE
- Rendering a list of items fetched from an API into a `<ul>`
- Injecting a formatted message or error into a designated container
- Building small UI fragments dynamically (a card, a table row) using template literals
- Clearing a container's content (`container.innerHTML = ""`)

## EDGE CASES

1. **`innerHTML +=` destroys and rebuilds everything, including event listeners and current state** — this is a subtle, important gotcha:
```javascript
container.innerHTML += "<p>New item</p>";
```
This isn't really "appending" in a lightweight sense — it's actually: read the current HTML as a string, concatenate the new string, and **reparse the entire thing from scratch**, replacing all existing child nodes. Any event listeners previously attached directly to those children are lost, and any focus/selection state in inputs is reset. For anything beyond trivial cases, `createElement`/`appendChild` (Topic 20) is the safer choice.

2. **Security risk: XSS (Cross-Site Scripting)** — if you ever set `innerHTML` using untrusted content (e.g., raw user input from a form, or unsanitized API data), a malicious user could inject a `<script>` tag or event-handler attribute that executes arbitrary JavaScript in your page's context:
```javascript
const userInput = "<img src=x onerror='alert(1)'>";
container.innerHTML = userInput; // executes attacker's code!
```
This is a real, well-known security consideration — not something to over-engineer around at your current stage, but you should know it exists and that `textContent` (next topic) is the safe alternative when you're just displaying plain text.

3. **Malformed HTML strings get auto-corrected/parsed leniently** by the browser, sometimes producing unexpected structure — e.g., unclosed tags may get auto-closed in ways you didn't intend.

4. **Setting `innerHTML = ""` is a common, valid way to clear an element's contents**, but it also destroys any listeners attached to children, same as edge case 1.

## COMMON MISTAKES
- Using `innerHTML +=` in a loop expecting cheap "appending," not realizing the whole subtree gets destroyed and rebuilt every iteration (this becomes a real performance problem with large lists, and a functional bug if any child had event listeners)
- Using `innerHTML` to insert plain text when `textContent` (next topic) would be safer and slightly faster
- Directly injecting unsanitized user input via `innerHTML`, opening an XSS vulnerability
- Forgetting that setting `innerHTML` reflows/re-renders the affected part of the page — doing this excessively in a loop causes real performance issues

## DEBUGGING EXERCISE

```html
<ul id="list"></ul>
<script>
  const list = document.querySelector("#list");
  const items = ["Apple", "Banana", "Cherry"];

  for (let i = 0; i < items.length; i++) {
    list.innerHTML += `<li>${items[i]}</li>`;
  }
</script>
```

> [!NOTE]
> *(Reason through it: what's inefficient or risky about this approach, given what you just learned about `innerHTML +=`?)*
> 
> **Answer:** It works, but on every single loop iteration, the browser: (1) reads the *entire* current `innerHTML` string, (2) concatenates the new `<li>`, and (3) **completely destroys and re-parses the whole `<ul>` subtree** from scratch — even the `<li>` elements that were already correctly in place from previous iterations. For 3 items this is invisible, but for a list of thousands of items (e.g., rendering a large API response), this becomes a real performance problem — each iteration does more work than the last, since it's re-parsing an ever-growing string. It's also fragile: if any of those `<li>` elements had event listeners attached individually, they'd be wiped out on every iteration. The better approach (Topic 20 — `createElement`) builds nodes without this repeated destroy-and-rebuild cost.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Quick prototyping and small dynamic content updates commonly use `innerHTML` for simplicity
- Production apps are careful about `innerHTML` with user-generated content specifically because of XSS — sanitization libraries or safer APIs are used when displaying untrusted content
- Frameworks like React deliberately avoid raw `innerHTML` by default for exactly this reason (their `dangerouslySetInnerHTML` prop name is a deliberate, explicit warning label about the risk)

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> innerHTML (direct HTML string injection)
>    ↓
> React's JSX rendering (handles escaping/safety automatically, by default)
>    ↓
> dangerouslySetInnerHTML (React's explicit opt-in when you truly need raw HTML — named that way as a warning)
> ```

## INTERVIEW QUESTION

**Q: What's a security risk of setting `innerHTML` with untrusted content? What's the safer alternative when you just need to display plain text?**

*Answer out loud before checking anything — name XSS explicitly, and mention `textContent` as the safe choice for plain text (next topic — you'll cover exactly why it's safe).*

---

## 💻 WRITE WITHOUT AI

1. Create an HTML page with an empty `<div id="output">`. Use `innerHTML` to insert a paragraph with some text.
2. Build an HTML string using a template literal that includes a variable (e.g., a name), and assign it to an element's `innerHTML`.
3. Create a `<ul id="fruitList"></ul>` and use a loop with `innerHTML +=` to add 3 `<li>` items from an array of fruit names — get it working first.
4. Deliberately try setting `innerHTML` to a string containing a script-like payload, e.g. `"<img src=x onerror=\"console.log('XSS ran')\">"`, into a test div, and observe in your console that the injected code actually executes. (This is for understanding the risk hands-on, in your own sandboxed test page — not for use anywhere real.)
5. (Harder) Rewrite exercise 3, but instead of `innerHTML +=` inside the loop, **build the full HTML string first** (concatenate all `<li>` pieces into one string via a loop or accumulator variable), and set `innerHTML` **once** at the end. Compare: does the visible result look identical? Why is this version better, even though it still technically uses `innerHTML` the same way at its core?

*(Reply with what you built/observed, or where you get stuck, before I give hints or a reference solution).*
