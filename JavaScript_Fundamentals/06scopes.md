#  Scope

**Scope** determines where variables, functions, and other identifiers can be accessed in a program.

In simple terms:

> Scope defines the visibility and accessibility of variables.

Example:

```javascript
function greet() {
    const message = "Hello";

    console.log(message); // accessible
}

greet();

console.log(message); // ReferenceError
```

`message` exists inside the function's scope and cannot be accessed outside it.

---

# 1. Types of Scope in JavaScript

The main scopes you should understand are:

```text
JavaScript Scope
│
├── Global Scope
├── Function Scope
├── Block Scope
└── Module Scope
```

To understand how these scopes interact, we also need:

```text
Lexical Scope
Scope Chain
Variable Shadowing
```

---

# 2. Global Scope

A variable declared outside functions and blocks at the top level of a classic script is in the top-level/global scope of that script.

```javascript
const username = "John";

function greet() {
    console.log(username);
}

greet();
```

Output:

```text
John
```

The function can access `username` because inner scopes can access variables from their outer scopes.

Another example:

```javascript
const appName = "My App";

function showAppName() {
    console.log(appName);
}

showAppName();
```

---

# 3. Global Variables

Variables in global scope can potentially be accessed from many parts of the program.

```javascript
let count = 0;

function increment() {
    count++;
}

function display() {
    console.log(count);
}

increment();
display();
```

Output:

```text
1
```

Both functions can access:

```javascript
count
```

because it exists in an outer scope.

However, excessive use of global mutable variables can make applications harder to understand because many parts of the program can modify shared state.

---

# 4. Browser Global Object

In browsers, the traditional global object is:

```javascript
window
```

For example:

```javascript
console.log(window);
```

However, top-level declarations do not all become `window` properties.

In a classic browser script:

```javascript
var name = "John";
```

typically creates:

```javascript
window.name
```

But:

```javascript
let age = 25;
const city = "Kolkata";
```

do not create:

```javascript
window.age
window.city
```

even though they are still top-level bindings in that script.

Also, JavaScript modules have their own module scope, which we will cover later.

---

# 5. `globalThis`

Modern JavaScript provides:

```javascript
globalThis
```

as a standardized way to access the global object across environments.

```javascript
console.log(globalThis);
```

Depending on the environment, the global object may historically be represented by things such as:

```text
Browser → window

Web Worker → self

Node.js → global
```

`globalThis` provides a common standard interface.

---

# 6. Function Scope

Variables declared inside a function cannot normally be accessed outside that function.

```javascript
function greet() {
    const message = "Hello";

    console.log(message);
}

greet();
```

Output:

```text
Hello
```

But:

```javascript
console.log(message);
```

outside the function produces:

```text
ReferenceError
```

Conceptually:

```text
Global Scope
│
└── greet()
    │
    └── Function Scope
        └── message
```

---

# 7. Each Function Creates Its Own Scope

Consider:

```javascript
function first() {
    const message = "First";

    console.log(message);
}

function second() {
    const message = "Second";

    console.log(message);
}

first();
second();
```

Output:

```text
First
Second
```

There is no conflict because each `message` belongs to a different function scope.

Conceptually:

```text
Global Scope
│
├── first()
│   └── message = "First"
│
└── second()
    └── message = "Second"
```

---

# 8. `var` Is Function-Scoped

One of the most important differences between `var`, `let`, and `const` concerns scope.

`var` is **function-scoped**.

```javascript
function test() {
    var message = "Hello";

    console.log(message);
}

test();
```

Outside:

```javascript
console.log(message); // ReferenceError
```

But blocks behave differently.

```javascript
function test() {
    if (true) {
        var message = "Hello";
    }

    console.log(message);
}

test();
```

Output:

```text
Hello
```

The `if` block did not create a separate scope for `var`.

Conceptually:

```text
Function Scope
│
├── if block
│   └── var message
│
└── message still accessible here
```

---

# 9. Block Scope

A **block** is generally code enclosed by:

```javascript
{
    // block
}
```

Examples include:

```javascript
if (...) {
}
```

```javascript
for (...) {
}
```

```javascript
while (...) {
}
```

and standalone blocks:

```javascript
{
    // block
}
```

`let` and `const` are block-scoped.

---

# 10. `let` Is Block-Scoped

Example:

```javascript
if (true) {
    let message = "Hello";

    console.log(message);
}
```

Output:

```text
Hello
```

But:

```javascript
console.log(message);
```

outside the block produces:

```text
ReferenceError
```

Conceptually:

```text
Outer Scope
│
└── if Block
    └── message
```

`message` only exists in that block.

---

# 11. `const` Is Block-Scoped

`const` behaves similarly regarding scope.

```javascript
if (true) {
    const username = "John";

    console.log(username);
}
```

Outside:

```javascript
console.log(username); // ReferenceError
```

So:

```text
var
→ function-scoped

let
→ block-scoped

const
→ block-scoped
```

---

# 12. `var` vs `let` in Blocks

Consider:

```javascript
if (true) {
    var a = 10;
    let b = 20;
}

console.log(a); // 10
console.log(b); // ReferenceError
```

Why?

```text
var a
→ not scoped by the if block

let b
→ scoped by the if block
```

The same applies to `const`.

---

# 13. Scope in `for` Loops

Using `let`:

```javascript
for (let i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i); // ReferenceError
```

`i` belongs to the loop's block scope.

Using `var`:

```javascript
for (var i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i); // 3
```

Because `var` is not block-scoped.

If this code is inside a function, that `var i` belongs to the surrounding function scope.

---

# 14. Nested Scope

Scopes can exist inside other scopes.

```javascript
const globalValue = "Global";

function outer() {
    const outerValue = "Outer";

    function inner() {
        const innerValue = "Inner";

        console.log(globalValue);
        console.log(outerValue);
        console.log(innerValue);
    }

    inner();
}

outer();
```

Output:

```text
Global
Outer
Inner
```

Conceptually:

```text
Global Scope
│
├── globalValue
│
└── outer()
    │
    ├── outerValue
    │
    └── inner()
        │
        └── innerValue
```

The inner function can access variables from its outer scopes.

---

# 15. Outer Scope Cannot Access Inner Scope

Scope access works outward, not inward.

```javascript
function outer() {
    const outerValue = "Outer";

    function inner() {
        const innerValue = "Inner";
    }

    console.log(outerValue); // works
    console.log(innerValue); // ReferenceError
}
```

`outer()` cannot access variables declared inside `inner()`.

Think of scope like:

```text
Inner Scope
→ can look outward

Outer Scope
→ cannot look inward
```

---

# 16. Lexical Scope

JavaScript uses **lexical scoping**.

This means a function's access to surrounding variables is determined by **where the function is defined in the source code**, not where it is later called.

Example:

```javascript
const name = "Global";

function outer() {
    const name = "Outer";

    function inner() {
        console.log(name);
    }

    inner();
}

outer();
```

Output:

```text
Outer
```

`inner()` was defined inside `outer()`, so its surrounding lexical scope includes `outer()`.

---

# 17. Lexical Scope Depends on Definition Location

Consider:

```javascript
const name = "Global";

function printName() {
    console.log(name);
}

function test() {
    const name = "Local";

    printName();
}

test();
```

What is printed?

```text
Global
```

Not:

```text
Local
```

Why?

Because `printName()` was **defined in global scope**.

Its lexical environment is based on where it was defined:

```text
Global Scope
│
├── name = "Global"
│
├── printName()
│
└── test()
    └── name = "Local"
```

Calling `printName()` from inside `test()` does not make `test()` its lexical parent.

This is a very important concept.

---

# 18. Scope Chain

When JavaScript tries to resolve an identifier, it starts in the current scope.

If the identifier is not found, it searches the enclosing lexical scope.

Then the next enclosing scope.

This continues outward until the identifier is found or there are no more enclosing scopes.

Example:

```javascript
const a = 10;

function outer() {
    const b = 20;

    function inner() {
        const c = 30;

        console.log(a);
        console.log(b);
        console.log(c);
    }

    inner();
}

outer();
```

Inside `inner()`:

```text
Looking for c:

inner scope
↓
found c


Looking for b:

inner scope
↓
not found

outer scope
↓
found b


Looking for a:

inner scope
↓
not found

outer scope
↓
not found

global scope
↓
found a
```

This lookup path is called the **scope chain**.

---

# 19. Identifier Lookup

Consider:

```javascript
const username = "Global";

function outer() {
    const age = 25;

    function inner() {
        console.log(username);
    }

    inner();
}

outer();
```

JavaScript looks for `username`:

```text
inner scope
    ↓
not found

outer scope
    ↓
not found

global scope
    ↓
username found
```

Result:

```text
Global
```

---

# 20. ReferenceError

If JavaScript searches the entire available scope chain and cannot find the identifier:

```javascript
function test() {
    console.log(username);
}

test();
```

and `username` has never been declared in an accessible scope, JavaScript throws:

```text
ReferenceError
```

Conceptually:

```text
Current Scope
    ↓
not found

Outer Scope
    ↓
not found

Global Scope
    ↓
not found

ReferenceError
```

---

# 21. Variable Shadowing

A variable in an inner scope can have the same name as a variable in an outer scope.

```javascript
const name = "Global";

function test() {
    const name = "Local";

    console.log(name);
}

test();
```

Output:

```text
Local
```

The inner variable **shadows** the outer variable.

Outside:

```javascript
console.log(name);
```

Output:

```text
Global
```

Conceptually:

```text
Global Scope
│
├── name = "Global"
│
└── test()
    │
    └── name = "Local"
```

Inside `test()`:

```text
name → "Local"
```

Outside:

```text
name → "Global"
```

---

# 22. Shadowing in Nested Blocks

Example:

```javascript
let value = 10;

{
    let value = 20;

    console.log(value);
}

console.log(value);
```

Output:

```text
20
10
```

These are two different variables.

---

# 23. Multiple Levels of Shadowing

```javascript
const value = "Global";

function outer() {
    const value = "Outer";

    function inner() {
        const value = "Inner";

        console.log(value);
    }

    inner();

    console.log(value);
}

outer();

console.log(value);
```

Output:

```text
Inner
Outer
Global
```

Each scope resolves to its nearest matching declaration.

---

# 24. Shadowing Does Not Modify the Outer Variable

```javascript
let count = 10;

function test() {
    let count = 20;

    count++;

    console.log(count);
}

test();

console.log(count);
```

Output:

```text
21
10
```

The inner `count` is a different variable.

---

# 25. Modifying an Outer Variable

If no local variable shadows the outer variable, the function can modify the outer binding when that binding is mutable.

```javascript
let count = 10;

function increment() {
    count++;
}

increment();

console.log(count); // 11
```

JavaScript searches:

```text
increment scope
    ↓
count not found

outer/global scope
    ↓
count found
```

Therefore:

```javascript
count++;
```

modifies that outer variable.

---

# 26. Shadowing vs Reassignment

These are different concepts.

## Shadowing

```javascript
let count = 10;

function test() {
    let count = 20;
}
```

Two separate variables exist.

## Reassignment

```javascript
let count = 10;

function test() {
    count = 20;
}

test();

console.log(count); // 20
```

No local `count` was declared, so the outer variable was reassigned.

---

# 27. Illegal Shadowing

Some combinations of declarations are not allowed.

Example:

```javascript
let value = 10;

{
    var value = 20;
}
```

This produces:

```text
SyntaxError
```

Why?

Because `var` is not confined to the block and would conflict with the `let` binding in the surrounding scope.

However:

```javascript
var value = 10;

{
    let value = 20;
}
```

is valid.

The `let` belongs to a separate block scope.

---

# 28. Same-Scope Redeclaration

`let` cannot be redeclared in the same scope:

```javascript
let name = "John";
let name = "Alice";
```

Result:

```text
SyntaxError
```

Same with `const`:

```javascript
const name = "John";
const name = "Alice";
```

Result:

```text
SyntaxError
```

But `var` allows same-scope redeclaration:

```javascript
var name = "John";
var name = "Alice";

console.log(name); // Alice
```

This is one reason `let` and `const` provide safer declaration behavior.

---

# 29. Same Name in Different Scopes Is Valid

This is valid:

```javascript
const name = "John";

function test() {
    const name = "Alice";

    console.log(name);
}
```

Because the declarations belong to different scopes.

Likewise:

```javascript
{
    const value = 10;
}

{
    const value = 20;
}
```

Each block has its own binding.

---

# 30. Scope and `if`

```javascript
if (true) {
    const message = "Hello";
    let count = 10;

    console.log(message);
    console.log(count);
}
```

Outside:

```javascript
console.log(message); // ReferenceError
console.log(count);   // ReferenceError
```

But:

```javascript
if (true) {
    var value = 10;
}

console.log(value);
```

In a classic script/global context, this `var` is not confined to the `if` block.

---

# 31. Scope and Loops

```javascript
for (let i = 0; i < 3; i++) {
    const message = `Iteration ${i}`;

    console.log(message);
}
```

Outside:

```javascript
console.log(i);       // ReferenceError
console.log(message); // ReferenceError
```

Both are block-scoped.

---

# 32. Scope and `try...catch`

The catch parameter is scoped to the `catch` block.

```javascript
try {
    throw new Error("Something went wrong");
} catch (error) {
    console.log(error.message);
}
```

Outside:

```javascript
console.log(error); // ReferenceError
```

---

# 33. Standalone Block Scope

Blocks do not need to belong to an `if` or loop.

```javascript
{
    const name = "John";

    console.log(name);
}
```

Outside:

```javascript
console.log(name); // ReferenceError
```

Standalone blocks can be useful when intentionally limiting variable scope.

---

# 34. Function Parameters Are Local Bindings

Function parameters belong to the function's scope.

```javascript
function greet(name) {
    console.log(name);
}

greet("John");
```

Inside:

```javascript
name
```

is available.

Outside:

```javascript
console.log(name);
```

does not access that parameter.

Conceptually:

```text
greet("John")
      ↓

Function Scope
│
└── name = "John"
```

---

# 35. Nested Functions

A nested function can access its outer function's variables.

```javascript
function outer() {
    const message = "Hello";

    function inner() {
        console.log(message);
    }

    inner();
}

outer();
```

Output:

```text
Hello
```

But the outer function cannot access variables declared inside the nested function:

```javascript
function outer() {
    function inner() {
        const secret = "Secret";
    }

    console.log(secret); // ReferenceError
}
```

---

# 36. Scope and Callbacks

Callbacks follow lexical scope like other functions.

```javascript
function processUser() {
    const username = "John";

    setTimeout(() => {
        console.log(username);
    }, 1000);
}

processUser();
```

Even though the callback runs later, it can still access:

```javascript
username
```

because the callback was defined inside `processUser()`.

This behavior is the foundation of **closures**.

We will cover it properly in the Closures section.

---

# 37. Scope vs Lifetime

Scope and lifetime are related but different concepts.

## Scope

Determines **where an identifier can be accessed**.

## Lifetime

Describes **how long the underlying data/binding remains relevant or reachable during execution**.

Consider:

```javascript
function createCounter() {
    let count = 0;

    return function () {
        count++;

        return count;
    };
}

const counter = createCounter();
```

Normally you might expect local function data to become unreachable after the function finishes.

However, the returned function still references:

```javascript
count
```

So the relevant lexical environment remains reachable.

```javascript
console.log(counter()); // 1
console.log(counter()); // 2
```

This is a **closure**.

So:

```text
Scope
→ where can I access this identifier?

Lifetime
→ how long does the relevant data remain reachable?
```

---

# 38. Module Scope

Modern JavaScript applications commonly use ES modules.

Example:

```javascript
// user.js

const username = "John";

export function getUsername() {
    return username;
}
```

`username` belongs to the module's scope.

It does not automatically become a global variable.

Another file can access exported values:

```javascript
import { getUsername } from "./user.js";

console.log(getUsername());
```

But it cannot directly access:

```javascript
username
```

unless that binding is exported.

Modules therefore provide natural isolation between files.

This is heavily used in React applications.

---

# 39. Script Scope vs Module Scope

Consider:

```javascript
const appName = "My App";
```

Inside an ES module, this binding belongs to that module.

```text
Module A
│
├── appName
└── functions
```

Another module:

```text
Module B
```

does not automatically receive access.

Values must be explicitly exported and imported:

```javascript
export const appName = "My App";
```

Then:

```javascript
import { appName } from "./app.js";
```

This makes dependencies explicit.

---

# 40. Scope and `var`

Remember:

```javascript
function test() {
    if (true) {
        var value = 10;
    }

    console.log(value);
}

test();
```

Output:

```text
10
```

Because `var` belongs to:

```text
test() function scope
```

not the `if` block.

---

# 41. Scope and `let`

```javascript
function test() {
    if (true) {
        let value = 10;

        console.log(value);
    }

    console.log(value);
}
```

The second `console.log()` causes:

```text
ReferenceError
```

because `value` belongs only to the `if` block.

---

# 42. Scope and `const`

Same block-scoping behavior:

```javascript
function test() {
    if (true) {
        const value = 10;

        console.log(value);
    }

    console.log(value);
}
```

Outside the block:

```text
ReferenceError
```

---

# 43. Scope Does Not Mean Immutability

`const` being block-scoped is unrelated to object mutation.

```javascript
{
    const user = {
        name: "John"
    };

    user.name = "Alice";

    console.log(user.name);
}
```

Output:

```text
Alice
```

`const` means the binding cannot be reassigned:

```javascript
user = {};
```

would fail.

Scope answers:

```text
Where can `user` be accessed?
```

`const` reassignment rules answer:

```text
Can the `user` binding be assigned another value?
```

These are separate concepts.

---

# 44. Scope and Closures

Consider:

```javascript
function outer() {
    const message = "Hello";

    return function inner() {
        console.log(message);
    };
}

const greet = outer();

greet();
```

Output:

```text
Hello
```

Even though:

```javascript
outer()
```

has finished execution, the returned `inner()` function still has access to the lexical environment where it was created.

This is a closure.

Understanding scope is therefore required before understanding closures.

---

# 45. Scope and Hoisting

Scope tells us **where** a declaration is available.

Hoisting concerns **how declarations behave within that scope before their declaration line is executed**.

Example:

```javascript
function test() {
    console.log(value);

    var value = 10;
}
```

This behaves differently from:

```javascript
function test() {
    console.log(value);

    let value = 10;
}
```

The difference involves:

```text
Hoisting
Initialization
Temporal Dead Zone
```

These concepts belong to the next dedicated topic: **Hoisting**.

---

# 46. Common Scope Mistake: Accessing Block Variables Outside

Incorrect:

```javascript
if (true) {
    const user = "John";
}

console.log(user);
```

Result:

```text
ReferenceError
```

Correct:

```javascript
const user = "John";

if (true) {
    console.log(user);
}
```

---

# 47. Common Scope Mistake: Confusing `var` with `let`

```javascript
for (var i = 0; i < 3; i++) {
    // ...
}

console.log(i); // 3
```

Compared with:

```javascript
for (let i = 0; i < 3; i++) {
    // ...
}

console.log(i); // ReferenceError
```

Remember:

```text
var
→ function scope

let
→ block scope

const
→ block scope
```

---

# 48. Common Scope Mistake: Accidental Shadowing

```javascript
let count = 10;

function increment() {
    let count = 0;

    count++;
}

increment();

console.log(count); // 10
```

If the intention was to modify the outer `count`, declaring another:

```javascript
let count
```

inside the function prevents that.

The inner variable shadows the outer variable.

---

# 49. Common Scope Mistake: Thinking Call Location Changes Scope

```javascript
const name = "Global";

function greet() {
    console.log(name);
}

function test() {
    const name = "Local";

    greet();
}

test();
```

Output:

```text
Global
```

Not:

```text
Local
```

Because lexical scope depends on:

```text
where the function was defined
```

not:

```text
where the function was called
```

---

# 50. React Component Scope

React function components are JavaScript functions, so normal JavaScript scope rules apply.

```jsx
function UserProfile() {
    const username = "John";

    return <h1>{username}</h1>;
}
```

`username` belongs to that particular component function execution.

It cannot be accessed from another function simply because both are React components.

```jsx
function UserProfile() {
    const username = "John";

    return <h1>{username}</h1>;
}

function Dashboard() {
    return <h1>{username}</h1>;
}
```

`Dashboard()` cannot access `username`.

---

# 51. React Component and Outer Scope

A component can access variables from an outer lexical scope.

```jsx
const appName = "My App";

function Header() {
    return <h1>{appName}</h1>;
}
```

Because:

```text
Header Scope
    ↓
Outer Module Scope
    ↓
appName found
```

This is ordinary lexical scoping.

---

# 52. React Event Handlers and Scope

```jsx
function User() {
    const username = "John";

    function handleClick() {
        console.log(username);
    }

    return (
        <button onClick={handleClick}>
            Show User
        </button>
    );
}
```

`handleClick()` can access:

```javascript
username
```

because it was defined inside the same component function execution.

Conceptually:

```text
User()
│
├── username
│
└── handleClick()
       ↓
       can access username
```

This relationship becomes especially important when we study closures and React hooks.

---

# 53. React Callbacks and Lexical Scope

```jsx
function UserList() {
    const prefix = "User:";

    const users = ["John", "Alice"];

    return (
        <ul>
            {users.map((user) => (
                <li key={user}>
                    {prefix} {user}
                </li>
            ))}
        </ul>
    );
}
```

The callback passed to `map()` can access:

```javascript
prefix
```

because of lexical scope.

It can also access:

```javascript
user
```

because `user` is the callback parameter.

Conceptually:

```text
UserList Scope
│
├── prefix
├── users
│
└── map callback
    │
    └── user
```

Inside the callback:

```text
user   → callback scope
prefix → outer component scope
```

---

# 54. Scope and React Modules

React applications commonly have files such as:

```text
src/
├── App.jsx
├── components/
│   ├── Header.jsx
│   └── User.jsx
└── utils/
    └── formatName.js
```

Each ES module has its own top-level scope.

Example:

```javascript
// formatName.js

function formatName(name) {
    return name.toUpperCase();
}

export default formatName;
```

Another module:

```javascript
import formatName from "./utils/formatName.js";
```

Code does not automatically become globally accessible just because it exists in another file.

---

# 55. Scope Hierarchy

A useful mental model:

```text
Module / Global Scope
│
├── variable
│
├── function
│   │
│   ├── Function Scope
│   │   │
│   │   ├── variable
│   │   │
│   │   └── Block
│   │       │
│   │       └── block-scoped variable
│   │
│   └── Nested Function
│       │
│       └── nested variable
│
└── another function
```

Inner scopes can search outward through their lexical scope chain.

Outer scopes cannot directly search inward.

---

# 56. Scope Lookup Mental Model

Given:

```javascript
const a = 1;

function first() {
    const b = 2;

    function second() {
        const c = 3;

        console.log(a + b + c);
    }

    second();
}

first();
```

Inside `second()`:

```text
Need c
↓
second scope
↓
found


Need b
↓
second scope
↓
not found
↓
first scope
↓
found


Need a
↓
second scope
↓
not found
↓
first scope
↓
not found
↓
outer/global scope
↓
found
```

Result:

```text
6
```

---

# 57. Quick Revision

```text
JavaScript Scope

Global / Top-Level Scope
→ outermost scope of the current script/environment

Function Scope
→ created by functions
→ var is function-scoped

Block Scope
→ created by blocks for let/const
→ let is block-scoped
→ const is block-scoped

Module Scope
→ top-level bindings belong to the ES module
→ not automatically global
```

---

# 58. `var`, `let`, and `const` Scope

```text
var
→ function-scoped
→ not block-scoped

let
→ block-scoped

const
→ block-scoped
```

Example:

```javascript
function test() {
    if (true) {
        var a = 10;
        let b = 20;
        const c = 30;
    }

    console.log(a); // 10

    console.log(b); // ReferenceError
    console.log(c); // ReferenceError
}
```

---

# 59. Core Concepts to Remember

```text
Scope
→ determines where identifiers are accessible

Lexical Scope
→ determined by where code/functions are defined

Scope Chain
→ JavaScript searches from current scope outward

Shadowing
→ inner declaration hides outer declaration with same name

Function Scope
→ functions create scopes
→ var follows function scope

Block Scope
→ let and const follow block scope

Module Scope
→ ES modules have isolated top-level bindings

Inner scope
→ can access outer scope

Outer scope
→ cannot directly access inner scope
```

Most importantly:

```text
Lexical scope is determined by definition location,
not call location.
```

---

# 60. Scope vs Related Concepts

Do not mix these concepts:

```text
Scope
→ Where can the identifier be accessed?

Hoisting
→ How declarations behave before their declaration line executes

Closure
→ A function retaining access to its lexical environment

Lifetime / Reachability
→ How long data remains reachable during execution

this
→ A separate mechanism whose value depends on function type
   and, for regular functions, how the function is called
```

These concepts interact, but they are not the same thing.
