# Event Delegation

**Event Delegation** is a technique where instead of attaching event listeners to many child elements, we attach **one event listener to a common parent** and determine which child triggered the event.

It relies mainly on **event bubbling**.

Example:

```html
<ul id="users">
    <li>Alice</li>
    <li>Bob</li>
    <li>Charlie</li>
</ul>
```

Instead of:

```javascript
const items =
    document.querySelectorAll(
        "#users li"
    );

items.forEach((item) => {
    item.addEventListener(
        "click",
        () => {
            console.log(item.textContent);
        }
    );
});
```

we can use:

```javascript
const list =
    document.querySelector("#users");

list.addEventListener(
    "click",
    (event) => {
        if (
            event.target.matches("li")
        ) {
            console.log(
                event.target.textContent
            );
        }
    }
);
```

One listener handles clicks from all `<li>` elements.

---

# 1. Why Event Delegation Exists

Suppose we have:

```html
<ul id="products">
    <li>Keyboard</li>
    <li>Mouse</li>
    <li>Monitor</li>
    <li>Laptop</li>
</ul>
```

We could attach listeners individually:

```javascript
const products =
    document.querySelectorAll(
        "#products li"
    );

products.forEach((product) => {
    product.addEventListener(
        "click",
        () => {
            console.log(
                product.textContent
            );
        }
    );
});
```

Conceptually:

```text
Product 1 → Listener
Product 2 → Listener
Product 3 → Listener
Product 4 → Listener
```

With delegation:

```text
Product 1 ─┐
Product 2 ─┤
Product 3 ─┼──► Parent Listener
Product 4 ─┘
```

One parent handles the events.

---

# 2. Event Delegation Depends on Event Propagation

When an event occurs, it does not necessarily exist only on the clicked element.

DOM events move through an **event propagation path**.

The main phases are:

```text
1. Capturing Phase

2. Target Phase

3. Bubbling Phase
```

Understanding these phases is essential for understanding delegation.

---

# 3. Event Propagation

Consider:

```html
<body>
    <div id="container">
        <button id="button">
            Click Me
        </button>
    </div>
</body>
```

If the button is clicked, the event conceptually travels:

```text
Window
  │
  ▼
Document
  │
  ▼
HTML
  │
  ▼
Body
  │
  ▼
Container
  │
  ▼
Button
```

This is the path toward the target.

Then it can travel back upward:

```text
Button
  │
  ▼
Container
  │
  ▼
Body
  │
  ▼
HTML
  │
  ▼
Document
  │
  ▼
Window
```

---

# 4. The Three Event Phases

```text
Window
  │
  │
  ▼
Document
  │
  ▼
Body
  │
  ▼
Parent
  │
  ▼
Target
  │
  ▲
  │
Parent
  ▲
  │
Body
  ▲
  │
Document
  ▲
  │
Window
```

The downward journey is:

```text
Capturing
```

The actual element where the event originated is:

```text
Target
```

The upward journey is:

```text
Bubbling
```

---

# 5. Capturing Phase

During capturing, the event travels from ancestors toward the target.

```text
Window
   ↓
Document
   ↓
HTML
   ↓
Body
   ↓
Parent
   ↓
Target
```

Listeners do **not** normally run during capturing unless configured to do so.

Example:

```javascript
element.addEventListener(
    "click",
    handler,
    {
        capture: true
    }
);
```

Or:

```javascript
element.addEventListener(
    "click",
    handler,
    true
);
```

---

# 6. Target Phase

The target is the element where the event originated.

Example:

```html
<button id="save">
    Save
</button>
```

If the button itself is clicked:

```text
Target
=
button
```

The event reaches the target between capture and bubble traversal.

---

# 7. Bubbling Phase

After reaching the target, bubbling events propagate upward through ancestors.

```text
Button
   ↑
Parent
   ↑
Body
   ↑
HTML
   ↑
Document
   ↑
Window
```

This is what makes event delegation possible.

The parent can receive an event that originated from a descendant.

---

# 8. Event Bubbling Example

HTML:

```html
<div id="parent">
    <button id="child">
        Click Me
    </button>
</div>
```

JavaScript:

```javascript
const parent =
    document.querySelector(
        "#parent"
    );

const child =
    document.querySelector(
        "#child"
    );

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);
```

Click the button.

Output:

```text
Child
Parent
```

Why?

```text
Button receives event
      │
      ▼
Child listener executes
      │
      ▼
Event bubbles upward
      │
      ▼
Parent listener executes
```

---

# 9. Bubbling Through Multiple Ancestors

HTML:

```html
<div id="grandparent">
    <div id="parent">
        <button id="child">
            Click
        </button>
    </div>
</div>
```

Listeners:

```javascript
grandparent.addEventListener(
    "click",
    () => {
        console.log(
            "Grandparent"
        );
    }
);

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Click the button.

Typical bubbling order:

```text
Child
Parent
Grandparent
```

---

# 10. Capturing Example

```javascript
grandparent.addEventListener(
    "click",
    () => {
        console.log(
            "Grandparent"
        );
    },
    {
        capture: true
    }
);

parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    },
    {
        capture: true
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Conceptually:

```text
Grandparent
    ↓
Parent
    ↓
Child
```

Capturing listeners run while the event travels toward the target.

---

# 11. Capturing + Bubbling

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log(
            "Parent Capture"
        );
    },
    {
        capture: true
    }
);

parent.addEventListener(
    "click",
    () => {
        console.log(
            "Parent Bubble"
        );
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Click child.

Conceptually:

```text
Parent Capture
      ↓
Child
      ↓
Parent Bubble
```

This demonstrates the two directions of propagation.

---

# 12. `event.target`

`event.target` refers to the element where the event originated.

Example:

```javascript
parent.addEventListener(
    "click",
    (event) => {
        console.log(
            event.target
        );
    }
);
```

If a button inside the parent was clicked:

```text
event.target
=
button
```

even though the listener is attached to the parent.

This is fundamental to event delegation.

---

# 13. `event.currentTarget`

`event.currentTarget` refers to the element whose event listener is currently executing.

Example:

```javascript
parent.addEventListener(
    "click",
    (event) => {
        console.log(
            event.target
        );

        console.log(
            event.currentTarget
        );
    }
);
```

If a child button was clicked:

```text
event.target
=
button


event.currentTarget
=
parent
```

---

# 14. `target` vs `currentTarget`

Consider:

```html
<div id="parent">
    <button id="button">
        Save
    </button>
</div>
```

Listener:

```javascript
parent.addEventListener(
    "click",
    (event) => {
        console.log(
            event.target
        );

        console.log(
            event.currentTarget
        );
    }
);
```

Click button.

```text
event.target
────────────────────

Where did the event originate?

button


event.currentTarget
────────────────────

Which element's listener
is currently executing?

parent
```

This distinction is extremely important.

---

# 15. Basic Event Delegation

HTML:

```html
<ul id="menu">
    <li>Home</li>
    <li>Products</li>
    <li>About</li>
    <li>Contact</li>
</ul>
```

JavaScript:

```javascript
const menu =
    document.querySelector("#menu");

menu.addEventListener(
    "click",
    (event) => {
        if (
            event.target.matches("li")
        ) {
            console.log(
                event.target.textContent
            );
        }
    }
);
```

One listener handles all items.

---

# 16. Why We Check the Target

Without:

```javascript
event.target.matches("li")
```

the handler also runs if the user clicks the parent itself.

Example:

```javascript
menu.addEventListener(
    "click",
    (event) => {
        console.log(
            "Something clicked"
        );
    }
);
```

Clicking:

```text
<li>
```

works.

But clicking:

```text
<ul>
```

also triggers the listener.

So delegation usually needs logic to identify relevant descendants.

---

# 17. `matches()`

The method:

```javascript
element.matches(selector)
```

checks whether an element matches a CSS selector.

Example:

```javascript
event.target.matches(
    ".delete-button"
);
```

Returns:

```text
true
```

or:

```text
false
```

Example:

```javascript
if (
    event.target.matches(
        ".delete-button"
    )
) {
    console.log(
        "Delete clicked"
    );
}
```

---

# 18. Problem with `event.target.matches()`

Consider:

```html
<button class="delete-button">
    <span>
        Delete
    </span>
</button>
```

If the user clicks directly on the `<span>`:

```text
event.target
=
span
```

Then:

```javascript
event.target.matches(
    ".delete-button"
);
```

returns:

```text
false
```

even though the user clicked inside the delete button.

This is why `closest()` is often safer.

---

# 19. `closest()`

`closest(selector)` searches:

```text
Current element
      │
      ▼
Parent
      │
      ▼
Parent
      │
      ▼
...
```

until it finds an element matching the selector.

Example:

```javascript
const button =
    event.target.closest(
        ".delete-button"
    );
```

If the user clicked:

```html
<span>Delete</span>
```

inside:

```html
<button class="delete-button">
```

`closest()` finds the button.

---

# 20. Delegation with `closest()`

```javascript
container.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                ".delete-button"
            );

        if (!button) {
            return;
        }

        console.log(
            "Delete clicked"
        );
    }
);
```

This works whether the user clicks:

```text
button
```

or:

```text
icon inside button
```

or:

```text
span inside button
```

provided those elements are descendants of the button.

---

# 21. Important `closest()` Boundary Check

There is an important issue.

Suppose the delegated container is:

```javascript
const container =
    document.querySelector(
        "#container"
    );
```

You find:

```javascript
const button =
    event.target.closest(
        ".delete-button"
    );
```

For robust delegation, ensure the matched element belongs to the intended container.

Example:

```javascript
if (
    !button ||
    !container.contains(button)
) {
    return;
}
```

Then handle it.

---

# 22. Robust Delegation Pattern

```javascript
container.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                ".delete-button"
            );

        if (
            !button ||
            !container.contains(
                button
            )
        ) {
            return;
        }

        handleDelete(button);
    }
);
```

This is a useful real-world pattern.

---

# 23. Dynamic Elements

One major advantage of event delegation is support for elements created later.

Suppose:

```html
<ul id="users"></ul>
```

Listener:

```javascript
const users =
    document.querySelector(
        "#users"
    );

users.addEventListener(
    "click",
    (event) => {
        const item =
            event.target.closest(
                ".user"
            );

        if (!item) {
            return;
        }

        console.log(
            item.textContent
        );
    }
);
```

Later:

```javascript
const user =
    document.createElement(
        "li"
    );

user.className = "user";

user.textContent =
    "Alice";

users.append(user);
```

The new element works automatically.

No new event listener is required.

---

# 24. Why Dynamic Elements Work

The listener is attached to:

```text
Parent
```

not the children.

When a newly created child is clicked:

```text
New Child
    │
    ▼
Click
    │
    ▼
Bubble
    │
    ▼
Parent Listener
```

The parent already exists and already has the listener.

---

# 25. Direct Listeners and Dynamic Elements

Suppose:

```javascript
document
    .querySelectorAll(".user")
    .forEach((user) => {
        user.addEventListener(
            "click",
            handleUser
        );
    });
```

This only attaches listeners to elements returned **at that moment**.

If another `.user` is added later, it does not automatically inherit that listener.

You would need to attach one manually.

Delegation avoids that requirement.

---

# 26. Todo List Example

HTML:

```html
<ul id="todo-list">
    <li data-id="1">
        Learn JavaScript
        <button class="delete">
            Delete
        </button>
    </li>

    <li data-id="2">
        Learn React
        <button class="delete">
            Delete
        </button>
    </li>
</ul>
```

JavaScript:

```javascript
const todoList =
    document.querySelector(
        "#todo-list"
    );

todoList.addEventListener(
    "click",
    (event) => {
        const deleteButton =
            event.target.closest(
                ".delete"
            );

        if (!deleteButton) {
            return;
        }

        const todo =
            deleteButton.closest(
                "li"
            );

        console.log(
            "Deleting:",
            todo.dataset.id
        );
    }
);
```

One listener handles every delete button.

---

# 27. Multiple Actions with One Listener

Suppose each row has:

```html
<button data-action="edit">
    Edit
</button>

<button data-action="delete">
    Delete
</button>

<button data-action="view">
    View
</button>
```

One parent listener:

```javascript
container.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                "[data-action]"
            );

        if (!button) {
            return;
        }

        const action =
            button.dataset.action;

        if (action === "edit") {
            editItem();
        }

        if (action === "delete") {
            deleteItem();
        }

        if (action === "view") {
            viewItem();
        }
    }
);
```

---

# 28. Using `switch`

The previous example can be written:

```javascript
container.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                "[data-action]"
            );

        if (!button) {
            return;
        }

        switch (
            button.dataset.action
        ) {
            case "edit":
                editItem();
                break;

            case "delete":
                deleteItem();
                break;

            case "view":
                viewItem();
                break;
        }
    }
);
```

This pattern is common in tables, lists, menus, and dashboards.

---

# 29. `data-*` Attributes

HTML allows custom data attributes:

```html
<button
    data-action="delete"
    data-user-id="42"
>
    Delete
</button>
```

JavaScript:

```javascript
button.dataset.action;
```

returns:

```text
delete
```

And:

```javascript
button.dataset.userId;
```

returns:

```text
42
```

Note that dataset values are strings.

---

# 30. Dataset Naming

HTML:

```html
data-user-id="42"
```

JavaScript:

```javascript
element.dataset.userId
```

The hyphenated name becomes camelCase.

```text
data-user-id
      ↓
dataset.userId
```

Another example:

```html
data-product-category="books"
```

becomes:

```javascript
element.dataset
    .productCategory;
```

---

# 31. Table Event Delegation

HTML:

```html
<table id="users-table">
    <tbody>
        <tr data-id="1">
            <td>Alice</td>

            <td>
                <button
                    class="delete"
                >
                    Delete
                </button>
            </td>
        </tr>

        <tr data-id="2">
            <td>Bob</td>

            <td>
                <button
                    class="delete"
                >
                    Delete
                </button>
            </td>
        </tr>
    </tbody>
</table>
```

Delegation:

```javascript
const table =
    document.querySelector(
        "#users-table"
    );

table.addEventListener(
    "click",
    (event) => {
        const deleteButton =
            event.target.closest(
                ".delete"
            );

        if (!deleteButton) {
            return;
        }

        const row =
            deleteButton.closest(
                "tr"
            );

        console.log(
            row.dataset.id
        );
    }
);
```

This works for rows added later too.

---

# 32. Menu Delegation

HTML:

```html
<nav id="menu">
    <button data-page="home">
        Home
    </button>

    <button data-page="users">
        Users
    </button>

    <button data-page="settings">
        Settings
    </button>
</nav>
```

JavaScript:

```javascript
menu.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                "[data-page]"
            );

        if (!button) {
            return;
        }

        navigateTo(
            button.dataset.page
        );
    }
);
```

One listener handles all navigation buttons.

---

# 33. Event Delegation and Performance

You may hear:

> Event delegation is faster because one listener is always better than many listeners.

That is too simplistic.

Modern browsers can handle many listeners reasonably well.

The strongest benefits of delegation are often:

```text
Simpler listener management

Dynamic element support

Centralized event handling

Reduced repetitive setup

Convenient list/table handling
```

Performance can also improve in cases with very large numbers of elements, but do not treat delegation as a universal optimization.

---

# 34. Delegation Is Not Always Better

Suppose there are only:

```text
3 buttons
```

with completely unrelated behavior.

Direct listeners may be clearer:

```javascript
saveButton.addEventListener(
    "click",
    save
);

cancelButton.addEventListener(
    "click",
    cancel
);

printButton.addEventListener(
    "click",
    print
);
```

You do not need delegation simply because it exists.

Use it when it makes the event architecture simpler.

---

# 35. `stopPropagation()`

An event listener can call:

```javascript
event.stopPropagation();
```

This stops further propagation through the DOM propagation path.

Example:

```javascript
child.addEventListener(
    "click",
    (event) => {
        event.stopPropagation();

        console.log("Child");
    }
);
```

Parent:

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);
```

Click child.

Output:

```text
Child
```

The event does not continue to the parent listener through normal propagation.

---

# 36. Why `stopPropagation()` Can Break Delegation

Suppose:

```text
Child
  │
  ▼
Click
  │
  ▼
stopPropagation()
```

The event does not bubble to the delegated parent.

```text
Child
   │
   X
   │
Parent Listener
```

Therefore parent delegation may never receive the event.

This is an important debugging point.

---

# 37. Avoid Unnecessary `stopPropagation()`

Do not use:

```javascript
event.stopPropagation();
```

automatically.

It can interfere with:

```text
Event delegation
Analytics
Parent interactions
UI libraries
Other event listeners
```

Use it when stopping propagation is actually part of the desired behavior.

---

# 38. `stopImmediatePropagation()`

There is also:

```javascript
event.stopImmediatePropagation();
```

This is stronger.

It prevents:

```text
Further propagation
+
Other listeners for that event
on the same element
```

Example:

```javascript
button.addEventListener(
    "click",
    (event) => {
        event.stopImmediatePropagation();

        console.log("First");
    }
);

button.addEventListener(
    "click",
    () => {
        console.log("Second");
    }
);
```

The second listener will not execute after the first calls `stopImmediatePropagation()`.

---

# 39. `stopPropagation()` vs `stopImmediatePropagation()`

```text
stopPropagation()
================================

Stops propagation to
other elements in the
event path.

It does not generally stop
other listeners already
registered on the same
element.


stopImmediatePropagation()
================================

Stops propagation
AND

prevents later listeners
on the same element
from executing.
```

---

# 40. `preventDefault()`

This is different from stopping propagation.

```javascript
event.preventDefault();
```

prevents the browser's **default action** for an event when that action is cancelable.

Example:

```html
<a href="/dashboard">
    Dashboard
</a>
```

JavaScript:

```javascript
link.addEventListener(
    "click",
    (event) => {
        event.preventDefault();

        console.log(
            "Navigation prevented"
        );
    }
);
```

The click event can still propagate.

The default navigation is what gets prevented.

---

# 41. `preventDefault()` vs `stopPropagation()`

These are frequently confused.

```text
preventDefault()
================================

Prevent browser's default
action.


stopPropagation()
================================

Stop event propagation
through the DOM tree.
```

Example:

```javascript
event.preventDefault();
```

does **not** mean:

```text
Stop bubbling
```

And:

```javascript
event.stopPropagation();
```

does **not** mean:

```text
Prevent link navigation
or form submission
```

---

# 42. Both Can Be Used

If required:

```javascript
event.preventDefault();
event.stopPropagation();
```

But they solve separate problems.

Ask:

```text
Do I want to prevent
the browser behavior?

→ preventDefault()


Do I want to stop
event propagation?

→ stopPropagation()
```

---

# 43. Delegated Link Handling

HTML:

```html
<nav id="navigation">
    <a
        href="/home"
        data-route="/home"
    >
        Home
    </a>

    <a
        href="/users"
        data-route="/users"
    >
        Users
    </a>
</nav>
```

JavaScript:

```javascript
navigation.addEventListener(
    "click",
    (event) => {
        const link =
            event.target.closest(
                "a[data-route]"
            );

        if (!link) {
            return;
        }

        event.preventDefault();

        navigate(
            link.dataset.route
        );
    }
);
```

This resembles the basic idea behind client-side navigation handling.

---

# 44. Event Delegation with Forms

Suppose a container holds multiple forms:

```html
<div id="forms">
    <form data-type="login">
        ...
    </form>

    <form data-type="register">
        ...
    </form>
</div>
```

You can potentially delegate supported bubbling events such as `submit`:

```javascript
forms.addEventListener(
    "submit",
    (event) => {
        const form =
            event.target.closest(
                "form"
            );

        if (!form) {
            return;
        }

        event.preventDefault();

        console.log(
            form.dataset.type
        );
    }
);
```

---

# 45. Not Every Event Behaves the Same

Event delegation relies on propagation behavior.

Many common events bubble, including:

```text
click
input
change
submit
keydown
keyup
pointerdown
pointerup
```

But you should not assume every event bubbles identically.

Some events require different approaches.

---

# 46. `focus` and `blur`

Traditionally:

```text
focus
blur
```

do not bubble like `click`.

For delegation, related bubbling events are:

```text
focusin
focusout
```

Example:

```javascript
form.addEventListener(
    "focusin",
    (event) => {
        console.log(
            "Focused:",
            event.target
        );
    }
);
```

This can be useful for delegated form behavior.

---

# 47. Capturing Can Handle Some Non-Bubbling Cases

Because capturing happens before the target phase, some event-handling scenarios can use capture listeners.

Example:

```javascript
container.addEventListener(
    "focus",
    handleFocus,
    true
);
```

But for ordinary delegated focus handling, `focusin`/`focusout` are often easier to reason about when available.

---

# 48. Mouse Enter and Leave

Be careful with:

```text
mouseenter
mouseleave
```

Their propagation behavior differs from:

```text
mouseover
mouseout
```

If building delegated hover behavior, understand which event semantics you need rather than assuming all mouse events behave like `click`.

---

# 49. `event.bubbles`

You can inspect:

```javascript
event.bubbles
```

Example:

```javascript
element.addEventListener(
    "click",
    (event) => {
        console.log(
            event.bubbles
        );
    }
);
```

For a normal click event:

```text
true
```

This property tells you whether that event is configured to bubble.

---

# 50. `event.cancelable`

You can inspect:

```javascript
event.cancelable
```

This indicates whether the event's default action can be cancelled.

If an event is not cancelable, calling:

```javascript
event.preventDefault();
```

does not magically make it cancelable.

---

# 51. `event.defaultPrevented`

After:

```javascript
event.preventDefault();
```

you can inspect:

```javascript
event.defaultPrevented
```

Example:

```javascript
console.log(
    event.defaultPrevented
);
```

This indicates whether the event's default action has been prevented.

---

# 52. `event.eventPhase`

The event object can expose:

```javascript
event.eventPhase
```

representing the current propagation phase.

Conceptually:

```text
Capturing
Target
Bubbling
```

The DOM also exposes constants such as:

```javascript
Event.CAPTURING_PHASE
Event.AT_TARGET
Event.BUBBLING_PHASE
```

You rarely need these in normal application code, but they are useful for understanding propagation.

---

# 53. Event Listener Options

Instead of:

```javascript
element.addEventListener(
    "click",
    handler
);
```

you can provide options:

```javascript
element.addEventListener(
    "click",
    handler,
    {
        capture: false,
        once: false,
        passive: false
    }
);
```

Common options include:

```text
capture
once
passive
signal
```

---

# 54. `once`

Example:

```javascript
button.addEventListener(
    "click",
    () => {
        console.log(
            "Runs once"
        );
    },
    {
        once: true
    }
);
```

After the first execution, the listener is automatically removed.

Useful for one-time interactions.

---

# 55. `signal`

As covered with AbortController:

```javascript
const controller =
    new AbortController();

element.addEventListener(
    "click",
    handler,
    {
        signal:
            controller.signal
    }
);
```

Later:

```javascript
controller.abort();
```

removes the listener.

This can simplify lifecycle cleanup.

---

# 56. `passive`

A passive listener tells the browser that the listener will not call `preventDefault()`.

Example:

```javascript
element.addEventListener(
    "touchmove",
    handler,
    {
        passive: true
    }
);
```

Do not use:

```javascript
event.preventDefault();
```

inside a listener that you explicitly declared passive and expect it to cancel the default action.

Passive listeners are particularly relevant to scrolling-related interactions.

---

# 57. Event Delegation and Nested Elements

Suppose:

```html
<ul id="list">
    <li class="item">
        <div class="content">
            <span class="title">
                JavaScript
            </span>
        </div>
    </li>
</ul>
```

If the user clicks:

```text
span.title
```

then:

```javascript
event.target
```

is the span.

If you want the item:

```javascript
const item =
    event.target.closest(
        ".item"
    );
```

This is why `closest()` is so useful for real-world delegation.

---

# 58. Nested Delegated Components

Suppose:

```html
<div class="card">
    <button class="menu">
        ...
    </button>
</div>
```

The parent handles:

```text
Card click
```

while the button handles:

```text
Menu click
```

Be careful not to accidentally trigger card behavior when menu behavior should be treated differently.

Instead of immediately using `stopPropagation()`, the parent can often filter the event.

Example:

```javascript
container.addEventListener(
    "click",
    (event) => {
        if (
            event.target.closest(
                ".menu"
            )
        ) {
            return;
        }

        const card =
            event.target.closest(
                ".card"
            );

        if (!card) {
            return;
        }

        openCard(card);
    }
);
```

Filtering is often easier to compose than globally stopping propagation.

---

# 59. Delegation Scope

Do not automatically attach every delegated event to:

```javascript
document
```

Example:

```javascript
document.addEventListener(
    "click",
    handler
);
```

can work, but if the interaction belongs only to:

```text
User Table
```

attach the listener to the closest stable relevant ancestor:

```javascript
usersTable.addEventListener(
    "click",
    handler
);
```

This keeps event handling scoped.

---

# 60. Why Use the Closest Stable Parent?

Suppose:

```text
Document
  │
  ▼
Dashboard
  │
  ▼
Users Panel
  │
  ▼
Users Table
  │
  ▼
Row
  │
  ▼
Delete Button
```

If only the table contains relevant delete buttons, use:

```text
Users Table
```

as the delegated parent.

Benefits:

```text
Smaller event scope
Clearer code
Fewer unrelated checks
Better component boundaries
```

---

# 61. Delegation and DOM Replacement

Suppose child elements are repeatedly replaced:

```javascript
container.innerHTML =
    renderItems(items);
```

Direct child listeners attached to the old elements disappear with those elements.

But if the listener is attached to:

```javascript
container
```

the listener remains.

New children still bubble events to the same container.

This is another major benefit of delegation.

---

# 62. Example — Dynamic Product List

HTML:

```html
<div id="products"></div>
```

Listener:

```javascript
const products =
    document.querySelector(
        "#products"
    );

products.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                "[data-product-id]"
            );

        if (!button) {
            return;
        }

        addToCart(
            button.dataset.productId
        );
    }
);
```

Render later:

```javascript
products.innerHTML = `
    <button
        data-product-id="101"
    >
        Add Keyboard
    </button>

    <button
        data-product-id="102"
    >
        Add Mouse
    </button>
`;
```

No extra listeners are required.

---

# 63. Example — Delete Dynamic Row

```javascript
table.addEventListener(
    "click",
    (event) => {
        const button =
            event.target.closest(
                "[data-action='delete']"
            );

        if (!button) {
            return;
        }

        const row =
            button.closest("tr");

        row.remove();
    }
);
```

This continues to work for newly inserted rows.

---

# 64. Example — Toggle Item

```javascript
list.addEventListener(
    "click",
    (event) => {
        const item =
            event.target.closest(
                ".item"
            );

        if (!item) {
            return;
        }

        item.classList.toggle(
            "selected"
        );
    }
);
```

One handler controls every list item.

---

# 65. Example — Action Router

A scalable pattern:

```javascript
container.addEventListener(
    "click",
    (event) => {
        const actionElement =
            event.target.closest(
                "[data-action]"
            );

        if (!actionElement) {
            return;
        }

        const actions = {
            edit: editItem,
            delete: deleteItem,
            view: viewItem,
            duplicate:
                duplicateItem
        };

        const action =
            actions[
                actionElement
                    .dataset.action
            ];

        if (!action) {
            return;
        }

        action(actionElement);
    }
);
```

Now HTML determines the action:

```html
<button data-action="edit">
    Edit
</button>

<button data-action="delete">
    Delete
</button>
```

---

# 66. Common Mistake — Using Only `event.target`

Suppose:

```html
<button class="delete">
    <svg>
        ...
    </svg>
</button>
```

User clicks the SVG.

Then:

```text
event.target
=
svg
```

So:

```javascript
event.target.matches(
    ".delete"
);
```

fails.

Use:

```javascript
event.target.closest(
    ".delete"
);
```

when nested content is possible.

---

# 67. Common Mistake — Forgetting to Filter

Bad:

```javascript
container.addEventListener(
    "click",
    () => {
        deleteItem();
    }
);
```

Now every click inside the container deletes something.

Delegation requires determining:

```text
What was actually clicked?
```

Use:

```text
matches()
closest()
dataset
```

or other appropriate checks.

---

# 68. Common Mistake — Delegating Too High

Attaching everything to:

```javascript
document
```

can create a giant event router full of unrelated logic.

Prefer:

```text
Closest stable ancestor
that owns the interaction.
```

---

# 69. Common Mistake — Delegating Too Low

Suppose children are frequently replaced.

If you attach the listener to an element that itself gets replaced:

```text
Old Parent
↓
Removed
↓
Listener disappears
```

Attach it to a **stable ancestor** that remains in the DOM.

---

# 70. Common Mistake — Assuming Every Event Bubbles

Do not assume all DOM events behave like `click`.

Check the event's propagation behavior.

Useful property:

```javascript
event.bubbles
```

And remember alternatives such as:

```text
focusin
focusout
```

for delegated focus behavior.

---

# 71. Common Mistake — Overusing `stopPropagation()`

Using it everywhere can break parent delegation and other event systems.

Prefer filtering when possible.

Use propagation control only when it matches the intended behavior.

---

# 72. Common Mistake — Confusing `target` and `currentTarget`

Inside a delegated listener:

```text
target
=
descendant that originated event


currentTarget
=
parent whose listener
is executing
```

Mixing these up causes many delegation bugs.

---

# 73. Common Mistake — Confusing `preventDefault()` and Propagation

```javascript
event.preventDefault();
```

does not stop bubbling.

```javascript
event.stopPropagation();
```

does not automatically prevent default browser behavior.

They solve separate problems.

---

# 74. Common Mistake — Forgetting Nested Click Targets

Buttons frequently contain:

```text
span
svg
img
icon
```

Therefore the actual target may not be the button.

Use:

```javascript
closest()
```

when appropriate.

---

# 75. Common Mistake — No Container Boundary Check

For robust reusable code:

```javascript
const match =
    event.target.closest(
        selector
    );

if (
    !match ||
    !container.contains(match)
) {
    return;
}
```

This ensures the match belongs to the intended delegation scope.

---

# 76. Common Mistake — Assuming Delegation Is Always Faster

Delegation can reduce listener count and simplify dynamic interfaces, but do not blindly rewrite every listener.

Code clarity and interaction boundaries matter.

---

# 77. Interview Question — What Is Event Delegation?

Event delegation is a technique where a listener is attached to a common ancestor instead of attaching separate listeners to multiple descendants.

The ancestor identifies the originating descendant when the event propagates to it.

---

# 78. Interview Question — How Does Event Delegation Work?

Usually through:

```text
Event Bubbling
+
event.target
+
matches()/closest()
```

The child generates the event.

The event bubbles to the parent.

The parent checks which descendant originated the interaction.

---

# 79. Interview Question — What Is Event Bubbling?

Event bubbling is the propagation phase where a bubbling event travels from the target upward through its ancestors.

```text
Target
  ↑
Parent
  ↑
Grandparent
  ↑
Document
  ↑
Window
```

---

# 80. Interview Question — What Is Event Capturing?

Capturing is the propagation phase where the event travels from outer ancestors toward the target.

```text
Window
  ↓
Document
  ↓
Parent
  ↓
Target
```

Listeners can participate in this phase using:

```javascript
{
    capture: true
}
```

---

# 81. Interview Question — `target` vs `currentTarget`?

`event.target`:

```text
Element where the event
originated.
```

`event.currentTarget`:

```text
Element whose listener
is currently executing.
```

In delegation, they are often different.

---

# 82. Interview Question — Why Is `closest()` Useful?

Because the actual target may be a nested element.

Example:

```text
button.delete
    │
    ▼
svg
    │
    ▼
path ← clicked
```

`event.target` may be the path.

```javascript
event.target.closest(
    ".delete"
);
```

can still find the intended button.

---

# 83. Interview Question — Why Is Delegation Useful for Dynamic Elements?

Because the listener belongs to a stable ancestor.

New descendants automatically participate when their events bubble to that ancestor.

No listener needs to be attached individually to each newly created child.

---

# 84. Interview Question — `preventDefault()` vs `stopPropagation()`?

```text
preventDefault()
=
Prevent browser default action.


stopPropagation()
=
Stop further event propagation.
```

They are independent operations.

---

# 85. Interview Question — `stopPropagation()` vs `stopImmediatePropagation()`?

`stopPropagation()` stops further propagation through the event path.

`stopImmediatePropagation()` also prevents later listeners on the same element from executing.

---

# 86. Interview Question — Can Every Event Be Delegated?

Not with exactly the same bubbling approach.

Delegation depends on the event's propagation behavior.

Some events bubble naturally.

Others may require:

```text
Related bubbling event
Capturing
Different event type
Different architecture
```

---

# 87. Interview Question — What Are the Benefits of Delegation?

Common benefits:

```text
Fewer repetitive listeners

Dynamic element support

Centralized handling

Simpler cleanup

Works well for lists

Works well for tables

Works well for menus

Works well when child DOM
is frequently replaced
```

---

# 88. Interview Question — What Are the Drawbacks?

Possible drawbacks:

```text
More target-filtering logic

Nested target complexity

Propagation can be stopped

Not all events bubble equally

Handlers can become too broad

Large document-level routers
can become difficult to maintain
```

---

# 89. Interview Question — Why Not Delegate Everything to `document`?

Because the handler then receives events from a very broad scope.

It may require many unrelated checks and create poor component boundaries.

Prefer the closest stable ancestor that logically owns the interaction.

---

# 90. Interview Question — What Does `matches()` Do?

```javascript
element.matches(selector)
```

returns whether the element itself matches the CSS selector.

It does not search ancestors.

For ancestor searching, use:

```javascript
closest()
```

---

# 91. Interview Question — `matches()` vs `closest()`?

```text
matches(selector)
================================

Does THIS element match?


closest(selector)
================================

Does this element or one
of its ancestors match?
```

This distinction is especially important for nested button content.

---

# 92. Interview Question — What Are `focusin` and `focusout` Useful For?

They provide focus-related event behavior that can be convenient for delegation because they bubble.

This makes parent-level form handling easier than relying only on `focus` and `blur`.

---

# 93. Interview Question — What Is `event.bubbles`?

A Boolean indicating whether the event is configured to bubble.

```javascript
console.log(
    event.bubbles
);
```

This can help inspect propagation behavior.

---

# 94. Interview Question — What Is `event.defaultPrevented`?

It indicates whether the event's default action has been prevented.

Example:

```javascript
event.preventDefault();

console.log(
    event.defaultPrevented
);
```

Typically:

```text
true
```

for a cancelable event whose default was successfully prevented.

---

# 95. Output Question — Bubbling

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Click child.

Output:

```text
Child
Parent
```

because the event runs at the child and bubbles to the parent.

---

# 96. Output Question — Stop Propagation

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log("Parent");
    }
);

child.addEventListener(
    "click",
    (event) => {
        event.stopPropagation();

        console.log("Child");
    }
);
```

Click child.

Output:

```text
Child
```

The event does not bubble to the parent listener.

---

# 97. Output Question — Target vs CurrentTarget

HTML:

```html
<div id="parent">
    <button id="child">
        Click
    </button>
</div>
```

JavaScript:

```javascript
parent.addEventListener(
    "click",
    (event) => {
        console.log(
            event.target.id
        );

        console.log(
            event.currentTarget.id
        );
    }
);
```

Click child.

Output:

```text
child
parent
```

---

# 98. Output Question — Capture and Bubble

```javascript
parent.addEventListener(
    "click",
    () => {
        console.log(
            "Parent Capture"
        );
    },
    true
);

parent.addEventListener(
    "click",
    () => {
        console.log(
            "Parent Bubble"
        );
    }
);

child.addEventListener(
    "click",
    () => {
        console.log("Child");
    }
);
```

Click child.

Conceptual output:

```text
Parent Capture
Child
Parent Bubble
```

---

# 99. Event Delegation Pattern to Remember

```javascript
container.addEventListener(
    "click",
    (event) => {
        const target =
            event.target.closest(
                "[data-action]"
            );

        if (
            !target ||
            !container.contains(target)
        ) {
            return;
        }

        const action =
            target.dataset.action;

        // Handle action
    }
);
```

This pattern gives you:

```text
One parent listener

Nested element support

Dynamic child support

Selector filtering

Scoped event handling
```

---

# 100. Event Delegation Mental Model

```text
             Parent
               │
               │ Listener
               │
      ┌────────┼────────┐
      │        │        │
      ▼        ▼        ▼
    Child    Child    Child
      A        B        C
               │
               │ Click
               ▼
          Event Target
               │
               ▼
            Bubble
               │
               ▼
             Parent
               │
               ▼
         Listener Runs
               │
               ▼
        Inspect target
               │
               ▼
      Find intended child
               │
               ▼
          Handle action
```

---

# 101. Propagation Mental Model

```text
                 WINDOW
                   │
                   │
             CAPTURING
                   │
                   ▼
                DOCUMENT
                   │
                   ▼
                  BODY
                   │
                   ▼
                 PARENT
                   │
                   ▼
                  CHILD
                   │
                TARGET
                   │
                   ▼
                 PARENT
                   │
                   ▼
                  BODY
                   │
                   ▼
                DOCUMENT
                   │
                   ▼
                 WINDOW

                BUBBLING
```

---

# 102. Event Delegation Cheat Sheet

```text
EVENT DELEGATION
================================

One parent listener handles
events from multiple children.


CORE MECHANISM
================================

Event bubbling


TARGET
================================

event.target

Where event originated.


CURRENT TARGET
================================

event.currentTarget

Element whose listener
is currently running.


MATCHES
================================

element.matches(selector)

Check current element.


CLOSEST
================================

element.closest(selector)

Search current element
and ancestors.


BUBBLING
================================

Target → Ancestors


CAPTURING
================================

Ancestors → Target


STOP PROPAGATION
================================

event.stopPropagation()


STOP IMMEDIATE
================================

event.stopImmediatePropagation()


PREVENT DEFAULT
================================

event.preventDefault()


DYNAMIC ELEMENTS
================================

Delegation works because
the stable parent owns
the listener.


DATA ATTRIBUTES
================================

data-user-id="42"

↓

element.dataset.userId


COMMON USES
================================

Lists
Tables
Menus
Todo apps
Dynamic buttons
Dashboards
Navigation
Forms


BEST PRACTICE
================================

Delegate to the closest
stable parent that owns
the interaction.
```

---

# 103. Important Rules to Remember

```text
1. Event delegation usually relies on bubbling.

2. Attach one listener to a common stable ancestor.

3. event.target identifies where the event originated.

4. event.currentTarget identifies whose listener is executing.

5. target and currentTarget are often different during delegation.

6. Event propagation has capturing, target, and bubbling phases.

7. Capturing moves toward the target.

8. Bubbling moves from the target toward ancestors.

9. addEventListener() uses bubbling behavior by default.

10. capture: true registers a capture-phase listener.

11. matches() checks the element itself.

12. closest() checks the element and its ancestors.

13. closest() is useful when buttons contain spans, SVGs, or icons.

14. Delegated handlers should filter irrelevant events.

15. A boundary check can keep matches inside the intended container.

16. Dynamic descendants work without new listeners.

17. Replacing child DOM does not remove a listener attached to a stable parent.

18. data-* attributes are useful for delegated actions.

19. dataset exposes data attributes.

20. Hyphenated data names become camelCase in dataset.

21. stopPropagation() stops further propagation.

22. stopPropagation() can prevent delegated parents from receiving events.

23. stopImmediatePropagation() also blocks later listeners on the same element.

24. preventDefault() prevents a browser default action when possible.

25. preventDefault() does not stop bubbling.

26. stopPropagation() does not automatically prevent default behavior.

27. Do not overuse stopPropagation().

28. Filtering is often preferable to stopping propagation.

29. Not every event bubbles the same way.

30. focus and blur require special consideration for delegation.

31. focusin and focusout are useful bubbling alternatives.

32. Mouse events differ in propagation semantics.

33. event.bubbles tells whether an event bubbles.

34. event.cancelable tells whether its default can be cancelled.

35. event.defaultPrevented indicates whether default behavior was prevented.

36. Event listeners can use capture.

37. Event listeners can use once.

38. Event listeners can use passive.

39. Event listeners can use AbortSignal.

40. Delegation is useful for large dynamic lists.

41. Delegation is useful for tables.

42. Delegation is useful for menus.

43. Delegation is useful for dynamically rendered interfaces.

44. Delegation can simplify listener cleanup.

45. Delegation is not automatically better for every UI.

46. Direct listeners are perfectly valid for simple unrelated controls.

47. Do not automatically delegate everything to document.

48. Prefer the closest stable ancestor.

49. Do not delegate to an ancestor that will itself be replaced.

50. Understand event propagation instead of memorizing delegation syntax.
```

---

# Event Delegation in One Sentence

> Event delegation handles events from multiple or dynamic descendants through a listener on a common ancestor, usually by relying on event bubbling and identifying the originating element with `event.target` or `closest()`.

---

# Final Mental Model

Without delegation:

```text
Parent

├── Child A
│      └── Listener
│
├── Child B
│      └── Listener
│
├── Child C
│      └── Listener
│
├── Child D
│      └── Listener
│
└── Child E
       └── Listener
```

With delegation:

```text
             Parent
                │
             Listener
                │
     ┌──────────┼──────────┐
     │          │          │
     ▼          ▼          ▼
   Child      Child      Child
     A          B          C

                 +
                 
             New Child
                 D

                 │
                 ▼

       No new listener needed
```

When Child B is clicked:

```text
Child B
   │
   │ click
   ▼
Target
   │
   │ bubble
   ▼
Parent
   │
   ▼
Parent Listener
   │
   ▼
event.target
   │
   ▼
Child B
   │
   ▼
Handle Child B
```

Remember the core relationship:

```text
CAPTURING

Parent
  │
  ▼
Child


TARGET

Child


BUBBLING

Child
  │
  ▼
Parent


DELEGATION

Child
  │
  │ Bubble
  ▼
Parent Listener
  │
  ▼
Identify Child
  │
  ▼
Handle Event
```
