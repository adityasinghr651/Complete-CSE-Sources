# Topic 19: textContent

## WHY
Most of the time, you just want to display or read **plain text** inside an element — a username, a score, a status message — with no HTML structure involved at all. Using `innerHTML` for this works, but it's slower (the browser has to parse the string as HTML even though there's nothing to parse) and, more importantly, **unsafe** if the text ever comes from user input or an external source. `textContent` solves both problems.

## WHAT
`textContent` gets or sets the **plain text** inside an element, treating whatever you assign to it as literal text — never as HTML markup to be parsed.

## MENTAL MODEL

Think of `textContent` as a "no HTML parsing allowed" mode:

```html
<div id="box"></div>
```

```javascript
const box = document.querySelector("#box");
box.textContent = "<strong>Bold</strong>";
```

Result on the page: the literal text `<strong>Bold</strong>` is displayed **as visible text**, tags and all — it is NOT rendered as bold. Compare to `innerHTML`, which would have actually bolded it.

```text
innerHTML = "<strong>Bold</strong>"   →   Bold   (rendered, bold text)
textContent = "<strong>Bold</strong>" →   <strong>Bold</strong>   (shown literally, as text)
```

## SYNTAX

```javascript
const heading = document.querySelector("#heading");

// Reading
console.log(heading.textContent);

// Setting — always treated as plain text, never parsed as HTML
heading.textContent = "Hello, world!";

// Even "HTML-looking" strings are safe
heading.textContent = "<script>alert('hi')</script>"; // displayed literally, NOT executed
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const p = document.querySelector("p");
p.textContent = "Just plain text";
```

## MANUAL IMPLEMENTATION
Not a primitive to build — it's a browser API property, same category as `innerHTML`. The manual skill here is **deciding which one to use** for a given situation — that judgment call matters more than the syntax itself, since both are one-line assignments.

## PRACTICAL USE
- Displaying a username, score, or any dynamic plain-text value
- Safely rendering any text that originated from user input (form fields, comments, chat messages) — this is the default-safe choice
- Updating a status/error message in the UI
- Reading the visible text of an element for validation or logic (e.g., checking what a button currently says)

## EDGE CASES

1. **`textContent` includes text from ALL descendant elements, concatenated**, even hidden ones (`display: none`) — unlike the visually-focused `innerText` (a separate, less standard property not on your syllabus):
```html
<div id="box">
  Hello <span style="display:none">Hidden</span> World
</div>
```
```javascript
console.log(box.textContent); // "Hello Hidden World" — includes the hidden span's text
```

2. **Setting `textContent` wipes out any existing child elements**, replacing them with a single text node — same "destroys everything inside" behavior as `innerHTML`, just without the HTML-parsing step:
```javascript
box.textContent = "New text"; // any <span>, <b>, etc. previously inside box are gone
```

3. **HTML entities are NOT interpreted**:
```javascript
p.textContent = "5 &lt; 10"; // displays literally as "5 &lt; 10", NOT "5 < 10"
```
(With `innerHTML`, `&lt;` *would* be interpreted and rendered as `<`.)

4. **Reading `textContent` on an empty element returns an empty string `""`, not `null`** — different from `querySelector`'s "no match" behavior:
```javascript
const empty = document.querySelector("#emptyDiv");
console.log(empty.textContent); // "" — not null, assuming the element itself exists
```

## COMMON MISTAKES
- Using `textContent` when you actually need to insert real HTML structure (it will just show tags as literal text — a very confusing bug for beginners the first time they hit it)
- Using `innerHTML` for plain text when `textContent` would be simpler, faster, and safer
- Forgetting that assigning to `textContent` deletes any existing child elements, not just replaces "the text part"
- Confusing `textContent` with the similarly-named but different `innerText` (not covered here — `innerText` is layout-aware and slower; `textContent` is the standard, syllabus-covered choice)

## DEBUGGING EXERCISE

```html
<div id="message"></div>
<script>
  const message = document.querySelector("#message");
  const username = "<b>Aditya</b>";
  message.textContent = `Welcome, ${username}!`;
</script>
```

> [!NOTE]
> *(Reason through it: what actually displays on the page, and why might someone be confused if they expected bold text?)*
> 
> **Answer:** No error — the page displays the literal text `Welcome, <b>Aditya</b>!`, including the visible `<b>` and `</b>` tags as plain characters, NOT bolded text. This is correct, expected `textContent` behavior — it never interprets any part of the string as HTML, regardless of what the string contains or where it came from. If the intent was genuinely to render `Aditya` in bold, `innerHTML` would be needed instead (with awareness of the XSS tradeoff from Topic 18) — but if `username` were untrusted user input, `textContent` is exactly the *safe*, correct choice here, and the "bug" is really just a mismatch between intent and tool choice, not textContent malfunctioning.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Displaying any user-generated content safely (comments, usernames, chat messages, search queries echoed back to the user) — `textContent` is the default-safe tool for this
- Updating dynamic status text (loading states, error messages, counters) throughout an app
- A general engineering rule of thumb: **default to `textContent`; only reach for `innerHTML` when you specifically need to insert markup**, and even then, prefer `createElement` (next topic) for anything beyond trivial, trusted strings

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> textContent (safe-by-default text rendering)
>    ↓
> React's default JSX text rendering (`<p>{username}</p>` automatically escapes content, behaving like textContent, not innerHTML, unless you explicitly opt out)
> ```

## INTERVIEW QUESTION

**Q: Why is `textContent` considered safer than `innerHTML` when displaying user-provided data? Give a concrete scenario where using `innerHTML` instead would create a vulnerability.**

*Answer out loud before checking anything — connect back to the XSS example from Topic 18 (e.g., a comment field where a user submits `<img src=x onerror=.../>`).*

---

## 💻 WRITE WITHOUT AI

1. Create a `<p id="greeting"></p>` and set its `textContent` to a personalized greeting using a variable and a template literal.
2. Set an element's `textContent` to a string that looks like HTML (e.g., `"<em>fake tag</em>"`) and confirm on the actual rendered page that it shows up as literal text, not styled/italicized.
3. Create a `<div>` with some nested child elements already in the HTML (e.g., a `<span>` inside it). Read its `textContent` and log it — confirm it concatenates all the nested text together with no tags.
4. Set that same div's `textContent` to a new plain string, then inspect the page (or log `div.innerHTML` afterward) to confirm the original child `<span>` element is now completely gone, not just its text changed.
5. (Harder) Write a small function `safeDisplay(element, userInput)` that always uses `textContent` (never `innerHTML`) to display a value, specifically as a defensive habit — then deliberately call it with a "malicious-looking" string containing a fake `<script>` tag, and confirm nothing executes and the tag shows up as harmless visible text.

*(Reply with what you built/observed, or where you get stuck, before I give hints or a reference solution).*
