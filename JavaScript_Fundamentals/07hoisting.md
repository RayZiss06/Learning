#  Hoisting

**Hoisting** describes how JavaScript processes declarations before executing the code in a scope.

Because of this, some variables and functions can appear to be referenced before their declaration line.

Example:

```javascript
console.log(value);

var value = 10;
```

Output:

```text
undefined
```

But:

```javascript
console.log(value);

let value = 10;
```

produces:

```text
ReferenceError
```

And:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

works successfully.

Why do these behave differently?

To understand hoisting correctly, we need to understand:

```text
Declaration
Initialization
Assignment
Scope
Temporal Dead Zone
```

---

# 1. What Hoisting Actually Means

A common explanation is:

> JavaScript moves declarations to the top of their scope.

This can be useful as a beginner mental model, but JavaScript does **not literally rearrange your source code**.

A better mental model is:

> Before executing statements in a scope, JavaScript establishes bindings for declarations in that scope according to the rules for each declaration type.

Different declarations are initialized differently.

That is why:

```javascript
var
let
const
function
class
```

do not behave identically before their declaration lines.

---

# 2. Declaration, Initialization, and Assignment

These terms are important.

## Declaration

Creating a binding with a name:

```javascript
let age;
```

Here:

```text
age
```

has been declared.

---

## Initialization

Giving the binding its initial value.

For example:

```javascript
let age;
```

after the declaration executes, `age` has the value:

```javascript
undefined
```

---

## Assignment

Giving an already initialized variable a value:

```javascript
age = 25;
```

---

## Declaration with Initializer

Usually we combine them:

```javascript
let age = 25;
```

Conceptually:

```text
Declare age
    ↓
Initialize binding
    ↓
Evaluate 25
    ↓
Assign/store 25
```

The exact timing differs between `var`, `let`, and `const`, which explains their hoisting behavior.

---

# 3. `var` Hoisting

Consider:

```javascript
console.log(age);

var age = 25;
```

Output:

```text
undefined
```

Why doesn't this produce a `ReferenceError`?

Because the `var` binding exists before the statement execution reaches:

```javascript
var age = 25;
```

and it is initialized with:

```javascript
undefined
```

before normal statement execution begins.

A useful conceptual model is:

```javascript
var age;

console.log(age);

age = 25;
```

Output:

```text
undefined
```

Again, JavaScript does not literally rewrite the code this way.

It is only a mental model for understanding the behavior.

---

# 4. `var` Declaration vs Assignment

Consider:

```javascript
console.log(age);

var age = 25;

console.log(age);
```

Output:

```text
undefined
25
```

Conceptually:

```text
Before statement execution:

age → undefined
```

Then:

```javascript
console.log(age);
```

prints:

```text
undefined
```

Then execution reaches:

```javascript
var age = 25;
```

and the value becomes:

```text
25
```

So:

```text
var declaration
→ binding created and initialized with undefined before statement execution

assignment/initializer
→ value assigned when execution reaches that statement
```

---

# 5. `var` Inside Functions

Remember that `var` is function-scoped.

```javascript
function test() {
    console.log(value);

    var value = 10;

    console.log(value);
}

test();
```

Output:

```text
undefined
10
```

The `value` binding belongs to the entire `test()` function scope.

Conceptually:

```javascript
function test() {
    var value;

    console.log(value);

    value = 10;

    console.log(value);
}
```

---

# 6. `var` and Shadowing

Consider:

```javascript
var name = "Global";

function test() {
    console.log(name);

    var name = "Local";
}

test();
```

What is printed?

It is:

```text
undefined
```

Not:

```text
Global
```

Why?

Because the function contains its own `var name`.

Conceptually:

```javascript
var name = "Global";

function test() {
    var name; // local binding initialized with undefined

    console.log(name);

    name = "Local";
}
```

The local `name` shadows the global `name`.

So inside `test()`:

```text
name → local variable
```

and before its assignment:

```text
name → undefined
```

---

# 7. `let` Hoisting

A common misconception is:

> `let` is not hoisted.

That is not accurate.

`let` declarations are also processed before statement execution, but they are **not initialized in the same way as `var`**.

Consider:

```javascript
console.log(age);

let age = 25;
```

This produces:

```text
ReferenceError
```

The `age` binding exists for the scope, but it cannot be accessed before execution reaches its declaration.

The period where it cannot be accessed is called the:

```text
Temporal Dead Zone
```

or:

```text
TDZ
```

---

# 8. `const` Hoisting

`const` behaves similarly to `let`.

```javascript
console.log(age);

const age = 25;
```

Result:

```text
ReferenceError
```

The binding exists, but accessing it before its declaration is initialized is not allowed.

So:

```text
var
→ hoisted
→ initialized with undefined

let
→ hoisted
→ uninitialized before declaration executes
→ Temporal Dead Zone

const
→ hoisted
→ uninitialized before declaration executes
→ Temporal Dead Zone
```

---

# 9. Temporal Dead Zone — TDZ

The **Temporal Dead Zone** is the period from entering a scope until execution reaches the declaration that initializes a `let`, `const`, or class binding.

Example:

```javascript
{
    // TDZ for username starts

    console.log(username);

    let username = "John";

    // TDZ ends once declaration executes
}
```

The access:

```javascript
console.log(username);
```

throws:

```text
ReferenceError
```

Conceptually:

```text
Enter Block
    ↓
username binding exists
but is uninitialized
    ↓
Temporal Dead Zone
    ↓
let username = "John"
    ↓
initialized
    ↓
username can now be accessed
```

---

# 10. Why Is It Called "Temporal" Dead Zone?

Because it depends on **when execution reaches the declaration**, not simply where the declaration appears spatially.

Consider:

```javascript
{
    const getAge = () => age;

    let age = 25;

    console.log(getAge());
}
```

Output:

```text
25
```

The function refers to `age`, but it does not access `age` until:

```javascript
getAge()
```

is called.

By that time:

```javascript
let age = 25;
```

has already executed.

Compare:

```javascript
{
    const getAge = () => age;

    console.log(getAge());

    let age = 25;
}
```

This throws:

```text
ReferenceError
```

because `getAge()` tries to access `age` while `age` is still in the TDZ.

---

# 11. TDZ Is Scope-Based

Consider:

```javascript
let value = "Outer";

{
    console.log(value);

    let value = "Inner";
}
```

You might expect:

```text
Outer
```

But this produces:

```text
ReferenceError
```

Why?

Because this block has its own:

```javascript
let value
```

That inner declaration shadows the outer variable for the entire block.

Conceptually:

```text
Outer Scope
│
└── value = "Outer"

Block Scope
│
└── value
    ↓
    exists but is in TDZ
```

JavaScript finds the inner `value`.

It does not skip it and continue looking for the outer one just because the inner binding has not been initialized yet.

---

# 12. `typeof` and the TDZ

Normally, `typeof` can be used with an undeclared identifier without throwing an error:

```javascript
console.log(typeof something);
```

Output:

```text
undefined
```

But this is different:

```javascript
console.log(typeof age);

let age = 25;
```

This throws:

```text
ReferenceError
```

Why?

Because `age` is not undeclared.

The `age` binding exists, but it is currently in the Temporal Dead Zone.

So:

```text
typeof undeclaredIdentifier
→ "undefined"

typeof TDZVariable
→ ReferenceError
```

---

# 13. `let` After Declaration

Once execution reaches the declaration, the variable can be accessed.

```javascript
let age;

console.log(age);
```

Output:

```text
undefined
```

Why?

Because:

```javascript
let age;
```

has now executed.

The variable is initialized with:

```javascript
undefined
```

So distinguish:

```javascript
console.log(age);
let age;
```

from:

```javascript
let age;
console.log(age);
```

First:

```text
ReferenceError
```

Second:

```text
undefined
```

---

# 14. `const` Must Be Initialized

Unlike `let`, `const` requires an initializer in its declaration.

Invalid:

```javascript
const age;
```

Result:

```text
SyntaxError
```

Valid:

```javascript
const age = 25;
```

This is because a `const` binding cannot later be reassigned, so it must receive its initial value when declared.

---

# 15. Function Declaration Hoisting

Function declarations behave differently from variables.

Consider:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Output:

```text
Hello
```

Function declarations are available before their declaration line executes.

Conceptually:

```text
Before statement execution:

greet → function
```

Therefore:

```javascript
greet();
```

works.

---

# 16. Function Declaration Example

```javascript
console.log(add(10, 20));

function add(a, b) {
    return a + b;
}
```

Output:

```text
30
```

The function can be called before its declaration appears in the source.

---

# 17. Function Expression with `var`

Consider:

```javascript
greet();

var greet = function () {
    console.log("Hello");
};
```

This produces:

```text
TypeError
```

Why?

Because only the `var greet` binding is initialized before statement execution.

The function value is assigned later.

Conceptually:

```javascript
var greet;

greet();

greet = function () {
    console.log("Hello");
};
```

Before assignment:

```javascript
greet === undefined
```

So:

```javascript
greet();
```

is effectively trying to call:

```javascript
undefined();
```

which causes a `TypeError`.

---

# 18. Function Expression with `let`

```javascript
greet();

let greet = function () {
    console.log("Hello");
};
```

Result:

```text
ReferenceError
```

Why?

Because `greet` is in the Temporal Dead Zone until its declaration executes.

---

# 19. Function Expression with `const`

```javascript
greet();

const greet = function () {
    console.log("Hello");
};
```

Result:

```text
ReferenceError
```

Again:

```text
greet
→ binding exists
→ not initialized yet
→ TDZ
```

---

# 20. Arrow Function Hoisting

Arrow functions are expressions.

Example:

```javascript
const greet = () => {
    console.log("Hello");
};
```

The arrow function itself does not behave like a function declaration.

The behavior before this line depends on the variable declaration storing it.

With `const`:

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

Result:

```text
ReferenceError
```

Because:

```text
const greet
→ TDZ
```

---

# 21. Arrow Function with `var`

Consider:

```javascript
greet();

var greet = () => {
    console.log("Hello");
};
```

Result:

```text
TypeError
```

Conceptually:

```javascript
var greet;

greet();

greet = () => {
    console.log("Hello");
};
```

At the time of the call:

```javascript
greet === undefined
```

Therefore calling it fails.

---

# 22. Function Declaration vs Function Expression vs Arrow Function

## Function Declaration

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Works:

```text
Hello
```

---

## Function Expression with `var`

```javascript
greet();

var greet = function () {
    console.log("Hello");
};
```

Fails with:

```text
TypeError
```

because:

```text
greet → undefined
```

at the time of the call.

---

## Function Expression with `const`

```javascript
greet();

const greet = function () {
    console.log("Hello");
};
```

Fails with:

```text
ReferenceError
```

because:

```text
greet → TDZ
```

---

## Arrow Function with `const`

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

Fails with:

```text
ReferenceError
```

for the same reason.

---

# 23. Quick Function Hoisting Table

| Declaration | Before Declaration Line |
|---|---|
| `function greet() {}` | Function can be called |
| `var greet = function() {}` | `greet` is `undefined` |
| `let greet = function() {}` | TDZ → `ReferenceError` |
| `const greet = function() {}` | TDZ → `ReferenceError` |
| `var greet = () => {}` | `greet` is `undefined` |
| `let greet = () => {}` | TDZ → `ReferenceError` |
| `const greet = () => {}` | TDZ → `ReferenceError` |

---

# 24. Hoisting and Scope

Hoisting happens within the declaration's scope.

Consider:

```javascript
function test() {
    console.log(value);

    var value = 10;
}

test();
```

Output:

```text
undefined
```

But outside:

```javascript
console.log(value);
```

produces:

```text
ReferenceError
```

because `value` belongs to:

```text
test() function scope
```

Hoisting does not move the variable outside its scope.

---

# 25. Block Scope and Hoisting

Consider:

```javascript
{
    console.log(value);

    let value = 10;
}
```

The `value` binding belongs to the block.

Before the declaration executes:

```text
value → TDZ
```

Outside the block:

```javascript
console.log(value);
```

the binding does not exist in that outer scope.

So remember:

> Hoisting does not change scope.

---

# 26. `var` in Blocks

`var` is not block-scoped.

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

Because `value` belongs to the entire function scope.

This means:

```javascript
function test() {
    console.log(value);

    if (true) {
        var value = 10;
    }
}

test();
```

prints:

```text
undefined
```

Conceptually:

```javascript
function test() {
    var value;

    console.log(value);

    if (true) {
        value = 10;
    }
}
```

---

# 27. `let` in Blocks

Compare:

```javascript
function test() {
    console.log(value);

    if (true) {
        let value = 10;
    }
}
```

Here the `value` declared inside the `if` block is not in the scope of the outer `console.log`.

If no other `value` exists in an accessible outer scope, the result is:

```text
ReferenceError
```

The inner `let` is only relevant inside:

```javascript
{
    let value = 10;
}
```

---

# 28. Function Declaration Inside Another Function

```javascript
function outer() {
    inner();

    function inner() {
        console.log("Inner");
    }
}

outer();
```

Output:

```text
Inner
```

The `inner` function declaration is available within its containing function scope before its declaration line.

But:

```javascript
inner();
```

outside `outer()` does not work because:

```text
inner
```

belongs to `outer()`'s scope.

Again:

> Hoisting does not change scope.

---

# 29. Function Declarations Inside Blocks

Function declarations inside blocks have historically had complicated behavior across older JavaScript environments, especially outside strict mode.

Modern JavaScript code should not rely on confusing block-level function hoisting behavior.

Instead of writing code that depends on:

```javascript
if (true) {
    function greet() {
        console.log("Hello");
    }
}
```

and then attempting to use `greet` outside the block, keep the function use clearly within the intended scope.

For predictable modern code, block-scoped function expressions are also an option:

```javascript
if (true) {
    const greet = () => {
        console.log("Hello");
    };

    greet();
}
```

---

# 30. Class Hoisting

Class declarations have bindings that are created for their scope, but they cannot be accessed before their declaration is evaluated.

Consider:

```javascript
const user = new User();

class User {
    constructor() {
        this.name = "John";
    }
}
```

This produces:

```text
ReferenceError
```

The class binding is in the Temporal Dead Zone before the class declaration executes.

Correct:

```javascript
class User {
    constructor() {
        this.name = "John";
    }
}

const user = new User();
```

Classes will be covered in detail later.

---

# 31. Class Expressions

Classes can also be assigned to variables:

```javascript
const User = class {
    constructor(name) {
        this.name = name;
    }
};
```

Here the variable declaration rules also matter.

This:

```javascript
const user = new User();

const User = class {};
```

throws:

```text
ReferenceError
```

because the outer `User` binding created by `const` is in the TDZ.

---

# 32. Multiple `var` Declarations

`var` allows redeclaration:

```javascript
var value = 10;
var value = 20;

console.log(value);
```

Output:

```text
20
```

This also means:

```javascript
var value;

var value;

var value;
```

does not create three independent bindings in the same scope.

There is one `value` binding.

---

# 33. Function Declaration and `var` with Same Name

Consider:

```javascript
console.log(typeof greet);

var greet;

function greet() {
    console.log("Hello");
}
```

The function declaration provides the function value during declaration setup, and the bare:

```javascript
var greet;
```

does not overwrite that value with `undefined`.

So:

```javascript
typeof greet
```

is:

```text
function
```

However, later assignment can replace it:

```javascript
function greet() {
    console.log("Hello");
}

var greet = 10;

console.log(greet);
```

Output:

```text
10
```

The assignment occurs during normal execution.

---

# 34. Hoisting Does Not Mean Assignment Is Hoisted

Consider:

```javascript
console.log(value);

var value = getValue();

function getValue() {
    return 100;
}
```

Output:

```text
undefined
```

Even though `getValue()` is available, this assignment:

```javascript
value = getValue();
```

does not happen until execution reaches that statement.

Conceptually:

```text
Before execution:

value → undefined
getValue → function
```

Then:

```javascript
console.log(value);
```

prints:

```text
undefined
```

Then:

```javascript
value = getValue();
```

sets:

```text
value → 100
```

---

# 35. Hoisting and Closures

Consider:

```javascript
function outer() {
    return function inner() {
        console.log(value);
    };

    var value = 10;
}

const fn = outer();

fn();
```

Output:

```text
undefined
```

Why?

`value` exists in `outer()`'s scope and was initialized to `undefined`.

However, this line:

```javascript
value = 10;
```

never executes because:

```javascript
return
```

ends `outer()` first.

Conceptually:

```text
outer scope:

value → undefined

return inner

value = 10
↑
never reached
```

The returned function closes over that binding, whose value remains `undefined`.

---

# 36. Hoisting and Lexical Scope

Consider:

```javascript
var value = "Global";

function outer() {
    function inner() {
        console.log(value);
    }

    var value = "Outer";

    inner();
}

outer();
```

Output:

```text
Outer
```

When `inner()` runs, `outer`'s local `value` has already been assigned `"Outer"`.

Now move the call:

```javascript
var value = "Global";

function outer() {
    function inner() {
        console.log(value);
    }

    inner();

    var value = "Outer";
}

outer();
```

Output:

```text
undefined
```

Why?

The local `var value` exists for the whole `outer()` scope and shadows the global variable.

At the time `inner()` runs:

```text
outer value → undefined
```

The assignment:

```javascript
value = "Outer";
```

has not happened yet.

---

# 37. Hoisting with `let` and Lexical Scope

Compare:

```javascript
let value = "Global";

function outer() {
    function inner() {
        console.log(value);
    }

    inner();

    let value = "Outer";
}

outer();
```

Result:

```text
ReferenceError
```

Why?

The local:

```javascript
let value
```

shadows the global variable throughout `outer()`'s lexical scope.

But before:

```javascript
let value = "Outer";
```

executes, that local binding is in the TDZ.

So:

```text
inner()
↓
looks for value
↓
outer scope has value
↓
value is in TDZ
↓
ReferenceError
```

JavaScript does not skip the TDZ binding and use the global `value`.

---

# 38. Common Misconception: "`let` and `const` Are Not Hoisted"

Incorrect mental model:

```text
var → hoisted

let → not hoisted

const → not hoisted
```

Better mental model:

```text
var
→ binding established
→ initialized with undefined before statement execution

let
→ binding established
→ remains uninitialized until declaration executes
→ TDZ

const
→ binding established
→ remains uninitialized until declaration executes
→ TDZ
```

The fact that `let` and `const` produce `ReferenceError` before their declaration does not mean the bindings are absent.

The TDZ behavior itself demonstrates that JavaScript knows about the declaration.

---

# 39. Common Misconception: "Functions Are Always Hoisted"

This statement is too broad.

This works:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

because it is a **function declaration**.

But:

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

does not work.

And:

```javascript
greet();

const greet = function () {
    console.log("Hello");
};
```

does not work.

So distinguish:

```text
Function Declaration
vs
Function Expression
vs
Arrow Function Expression
```

---

# 40. Common Misconception: "Hoisting Moves Code"

JavaScript does not transform:

```javascript
console.log(value);

var value = 10;
```

into source code that literally becomes:

```javascript
var value;

console.log(value);

value = 10;
```

That rewritten version is only a teaching model.

A better understanding is:

```text
JavaScript establishes declarations/bindings for the scope
↓
then executes statements in order
```

---

# 41. Error Types Matter

Understanding hoisting also helps distinguish common errors.

## Undeclared Variable

```javascript
console.log(value);
```

when `value` does not exist in an accessible scope:

```text
ReferenceError
```

---

## `let` / `const` Before Declaration

```javascript
console.log(value);

let value = 10;
```

Result:

```text
ReferenceError
```

because of the TDZ.

---

## Calling `var` Function Expression Too Early

```javascript
greet();

var greet = function () {};
```

Result:

```text
TypeError
```

Why not `ReferenceError`?

Because `greet` exists.

Its value is:

```javascript
undefined
```

The error happens because JavaScript tries to call a non-function value.

So:

```text
Identifier doesn't resolve / TDZ access
→ ReferenceError

Identifier resolves, but value cannot be called
→ TypeError
```

---

# 42. Hoisting Interview Example 1

What is the output?

```javascript
console.log(a);

var a = 10;
```

Answer:

```text
undefined
```

Reason:

```text
a binding exists
↓
initialized with undefined
↓
console.log(a)
↓
undefined
↓
a = 10
```

---

# 43. Hoisting Interview Example 2

What happens?

```javascript
console.log(a);

let a = 10;
```

Answer:

```text
ReferenceError
```

Reason:

```text
a exists
↓
a is in TDZ
↓
cannot access yet
```

---

# 44. Hoisting Interview Example 3

What happens?

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Answer:

```text
Hello
```

The function declaration is available before its source declaration line.

---

# 45. Hoisting Interview Example 4

What happens?

```javascript
greet();

var greet = function () {
    console.log("Hello");
};
```

Answer:

```text
TypeError
```

At the time of the call:

```javascript
greet === undefined
```

---

# 46. Hoisting Interview Example 5

What happens?

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

Answer:

```text
ReferenceError
```

`greet` is in the TDZ.

---

# 47. Hoisting Interview Example 6

What is the output?

```javascript
var value = 10;

function test() {
    console.log(value);

    var value = 20;

    console.log(value);
}

test();
```

Answer:

```text
undefined
20
```

Why?

The local `var value` shadows the global `value`.

Conceptually:

```javascript
var value = 10;

function test() {
    var value;

    console.log(value);

    value = 20;

    console.log(value);
}
```

---

# 48. Hoisting Interview Example 7

What happens?

```javascript
let value = 10;

function test() {
    console.log(value);

    let value = 20;
}

test();
```

Answer:

```text
ReferenceError
```

Why?

The local `let value` shadows the outer variable but remains in the TDZ until its declaration executes.

---

# 49. Hoisting Interview Example 8

What is the output?

```javascript
var a = 1;

function test() {
    console.log(a);

    if (false) {
        var a = 2;
    }
}

test();
```

Answer:

```text
undefined
```

Even though the block never executes:

```javascript
if (false)
```

the `var a` declaration belongs to the entire function scope.

Conceptually:

```javascript
function test() {
    var a;

    console.log(a);

    if (false) {
        a = 2;
    }
}
```

The local `a` shadows the global `a`.

---

# 50. Hoisting Interview Example 9

What happens?

```javascript
{
    console.log(value);

    const value = 10;
}
```

Answer:

```text
ReferenceError
```

The block-scoped `value` is in the TDZ.

---

# 51. Hoisting Interview Example 10

What is the output?

```javascript
console.log(typeof value);

var value = 10;
```

Answer:

```text
undefined
```

Note that `typeof` returns the string:

```text
"undefined"
```

because the `var` binding has already been initialized to `undefined`.

Compare:

```javascript
console.log(typeof value);

let value = 10;
```

which throws:

```text
ReferenceError
```

because `value` is in the TDZ.

---

# 52. Should We Rely on Hoisting?

Even though this works:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

you should write code in a clear and predictable structure.

For variables, avoid patterns like:

```javascript
console.log(value);

var value = 10;
```

Prefer declaring variables before using them:

```javascript
const value = 10;

console.log(value);
```

Modern JavaScript generally favors:

```text
const
↓
let
↓
var only when there is a specific reason
```

Understanding hoisting is still essential because it explains JavaScript behavior and helps when reading existing code.

---

# 53. React Relevance

In React, components and helper functions are often written using function declarations or arrow functions.

Function declaration:

```jsx
function App() {
    return <h1>Hello</h1>;
}
```

Arrow function:

```jsx
const App = () => {
    return <h1>Hello</h1>;
};
```

These are not identical regarding hoisting.

This can work:

```jsx
const element = <App />;

function App() {
    return <h1>Hello</h1>;
}
```

because `App` is a function declaration.

But JavaScript code that accesses a `const` component before its initialization will fail:

```javascript
console.log(App);

const App = () => {
    return "Hello";
};
```

Result:

```text
ReferenceError
```

The same JavaScript rules apply inside React code.

---

# 54. React Helper Functions

Consider:

```jsx
function App() {
    return (
        <button onClick={handleClick}>
            Click
        </button>
    );

    function handleClick() {
        console.log("Clicked");
    }
}
```

The function declaration binding exists within the `App()` execution, so referring to `handleClick` earlier in the function body is valid.

However:

```jsx
function App() {
    const button = (
        <button onClick={handleClick}>
            Click
        </button>
    );

    const handleClick = () => {
        console.log("Clicked");
    };

    return button;
}
```

This works because `handleClick` is not actually read until the JSX expression:

```jsx
onClick={handleClick}
```

is evaluated, and in this exact code that evaluation occurs while creating `button`, **before** `handleClick` is initialized.

Therefore this version actually throws a:

```text
ReferenceError
```

Correct ordering:

```jsx
function App() {
    const handleClick = () => {
        console.log("Clicked");
    };

    return (
        <button onClick={handleClick}>
            Click
        </button>
    );
}
```

The important point is that JSX does not change JavaScript's TDZ rules.

---

# 55. Quick Comparison

| Declaration | Scope | Before Declaration | Initial State |
|---|---|---|---|
| `var` | Function | Accessible | `undefined` |
| `let` | Block | TDZ | Uninitialized |
| `const` | Block | TDZ | Uninitialized |
| Function Declaration | Function/block depending on context | Callable in its scope | Function |
| Class Declaration | Block | TDZ | Uninitialized |

---

# 56. Function Hoisting Comparison

```text
function greet() {}
→ callable before declaration
```

```text
var greet = function() {}
→ greet is undefined before assignment
→ calling it causes TypeError
```

```text
let greet = function() {}
→ TDZ before declaration
→ ReferenceError
```

```text
const greet = function() {}
→ TDZ before declaration
→ ReferenceError
```

```text
var greet = () => {}
→ greet is undefined before assignment
→ calling it causes TypeError
```

```text
let greet = () => {}
→ TDZ before declaration
→ ReferenceError
```

```text
const greet = () => {}
→ TDZ before declaration
→ ReferenceError
```

---

# 57. Hoisting Mental Model

A useful mental model is:

```text
Enter Scope
    ↓
JavaScript establishes declarations/bindings
    ↓
Different declarations get different initialization behavior
    ↓
Statements execute in source order
```

For `var`:

```text
Enter Scope
    ↓
var binding created
    ↓
initialized with undefined
    ↓
statements execute
    ↓
assignment happens when reached
```

For `let` / `const`:

```text
Enter Scope
    ↓
binding established
    ↓
uninitialized
    ↓
TDZ
    ↓
declaration executes
    ↓
binding initialized
    ↓
can be accessed normally
```

For function declarations:

```text
Enter Scope
    ↓
function binding established
    ↓
function value available
    ↓
statements execute
```

---

# 58. Core Concepts to Remember

```text
Hoisting does NOT mean JavaScript literally moves code.

Declarations are processed before normal statement execution.

var
→ binding created
→ initialized with undefined
→ assignment happens later

let
→ binding created
→ uninitialized until declaration executes
→ TDZ before declaration

const
→ binding created
→ uninitialized until declaration executes
→ TDZ before declaration
→ must have an initializer

Function Declaration
→ function available before declaration line

Function Expression
→ behavior depends on variable declaration

Arrow Function
→ expression
→ behavior depends on variable declaration

Class Declaration
→ binding exists
→ TDZ before declaration executes
```

---

# 59. Most Important Distinction

Do not memorize:

```text
var is hoisted
let and const are not hoisted
```

Instead remember:

```text
var
→ hoisted + initialized with undefined

let
→ hoisted + uninitialized + TDZ

const
→ hoisted + uninitialized + TDZ

function declaration
→ hoisted with function value available

class
→ hoisted binding + TDZ
```

This explains the actual observable behavior much better.

---

# 60. Scope + Hoisting Together

Scope answers:

```text
Where does this binding exist?
```

Hoisting answers:

```text
What is the state of that binding before execution reaches its declaration?
```

Example:

```javascript
function test() {
    console.log(value);

    var value = 10;
}
```

Scope:

```text
value belongs to test() function scope
```

Hoisting:

```text
value is initialized with undefined before statement execution
```

Result:

```text
undefined
```

Compare:

```javascript
function test() {
    console.log(value);

    let value = 10;
}
```

Scope:

```text
value belongs to the relevant block/function-body block scope
```

Hoisting:

```text
value is uninitialized and in TDZ
```

Result:

```text
ReferenceError
```
