#  Functions

Functions are reusable blocks of code designed to perform a task.

Instead of repeating the same logic:

```javascript
console.log("Hello John");
console.log("Hello Alice");
console.log("Hello Bob");
```

we can create a function:

```javascript
function greet(name) {
    console.log(`Hello ${name}`);
}

greet("John");
greet("Alice");
greet("Bob");
```

A function can:

- Accept input through parameters
- Execute logic
- Return a value
- Be stored in variables
- Be passed to other functions
- Be returned from other functions
- Be stored inside objects and arrays
- Be asynchronous

Functions are one of the most important concepts in JavaScript and React.

---

# 1. Function Declaration

A **function declaration** defines a named function using the `function` keyword.

Syntax:

```javascript
function functionName(parameters) {
    // code
}
```

Example:

```javascript
function greet() {
    console.log("Hello");
}

greet();
```

Output:

```text
Hello
```

The function does not execute when it is defined.

It executes when it is called:

```javascript
greet();
```

---

# 2. Calling a Function

Defining:

```javascript
function greet() {
    console.log("Hello");
}
```

Calling:

```javascript
greet();
```

A function can be called multiple times:

```javascript
greet();
greet();
greet();
```

Output:

```text
Hello
Hello
Hello
```

---

# 3. Parameters and Arguments

Functions can receive values.

```javascript
function greet(name) {
    console.log(`Hello ${name}`);
}

greet("John");
```

Here:

```text
name
↓
Parameter
```

```text
"John"
↓
Argument
```

## Parameter

A variable listed in the function definition.

```javascript
function greet(name) {
    // name is a parameter
}
```

## Argument

The actual value supplied when calling the function.

```javascript
greet("John");

// "John" is an argument
```

---

# 4. Multiple Parameters

Functions can have multiple parameters.

```javascript
function add(a, b) {
    console.log(a + b);
}

add(10, 20);
```

Output:

```text
30
```

Here:

```text
Parameters:
a
b

Arguments:
10
20
```

Arguments are matched to parameters by position.

```javascript
function introduce(name, age) {
    console.log(`${name} is ${age} years old`);
}

introduce("John", 25);
```

---

# 5. Missing Arguments

If an argument is not supplied, the corresponding parameter receives `undefined`.

```javascript
function greet(name) {
    console.log(name);
}

greet();
```

Output:

```text
undefined
```

Example:

```javascript
function add(a, b) {
    console.log(a);
    console.log(b);
}

add(10);
```

Output:

```text
10
undefined
```

---

# 6. Extra Arguments

JavaScript allows more arguments to be supplied than there are parameters.

```javascript
function greet(name) {
    console.log(name);
}

greet("John", 25, "Kolkata");
```

The named parameter receives:

```text
name = "John"
```

The extra arguments do not automatically get named parameters.

They can still be accessed through mechanisms such as:

```javascript
arguments
```

in non-arrow functions, or more commonly through **rest parameters**, which we will cover later.

---

# 7. Return Values

Functions can return values using:

```javascript
return
```

Example:

```javascript
function add(a, b) {
    return a + b;
}

const result = add(10, 20);

console.log(result); // 30
```

Conceptually:

```text
add(10, 20)

    ↓

return 30

    ↓

result = 30
```

This is different from:

```javascript
function add(a, b) {
    console.log(a + b);
}
```

The function prints the result but does not return it.

---

# 8. `console.log()` vs `return`

This distinction is important.

## `console.log()`

Displays something.

```javascript
function add(a, b) {
    console.log(a + b);
}

const result = add(10, 20);

console.log(result);
```

Output:

```text
30
undefined
```

Why?

Because:

```javascript
console.log(a + b);
```

prints `30`, but the function itself does not explicitly return a value.

---

## `return`

Returns a value to the caller.

```javascript
function add(a, b) {
    return a + b;
}

const result = add(10, 20);

console.log(result);
```

Output:

```text
30
```

Remember:

```text
console.log()
→ display a value

return
→ send a value back from the function
```

---

# 9. Functions Without `return`

If a function completes without executing a `return` statement, its result is:

```javascript
undefined
```

Example:

```javascript
function greet() {
    console.log("Hello");
}

const result = greet();

console.log(result);
```

Output:

```text
Hello
undefined
```

---

# 10. `return` Stops Function Execution

When JavaScript reaches `return`, the current function ends immediately.

```javascript
function test() {
    console.log("Before");

    return;

    console.log("After");
}

test();
```

Output:

```text
Before
```

This never executes:

```javascript
console.log("After");
```

because execution already left the function.

---

# 11. Early Return

Because `return` stops the function, it is commonly used to exit early.

```javascript
function greetUser(user) {
    if (!user) {
        return;
    }

    console.log(`Hello ${user.name}`);
}
```

If `user` is falsy:

```javascript
greetUser(null);
```

the function exits immediately.

This avoids unnecessary nesting.

Instead of:

```javascript
function greetUser(user) {
    if (user) {
        console.log(`Hello ${user.name}`);
    }
}
```

larger functions often use:

```javascript
function greetUser(user) {
    if (!user) {
        return;
    }

    // remaining logic
}
```

This pattern is often called a **guard clause**.

---

# 12. Default Parameters

Parameters can have default values.

```javascript
function greet(name = "Guest") {
    console.log(`Hello ${name}`);
}

greet();
```

Output:

```text
Hello Guest
```

If an argument is supplied:

```javascript
greet("John");
```

Output:

```text
Hello John
```

Default parameters are used when the argument is `undefined`.

```javascript
function greet(name = "Guest") {
    console.log(name);
}

greet(undefined); // Guest
greet(null);      // null
greet("");        // ""
```

Important:

```text
undefined → default used

null      → null is used
""        → empty string is used
0         → 0 is used
false     → false is used
```

---

# 13. Function Expression

A function can be created and assigned to a variable.

```javascript
const greet = function () {
    console.log("Hello");
};
```

This is called a **function expression**.

Call it normally:

```javascript
greet();
```

Output:

```text
Hello
```

A function expression can also have parameters:

```javascript
const add = function (a, b) {
    return a + b;
};

console.log(add(10, 20)); // 30
```

---

# 14. Function Declaration vs Function Expression

## Function Declaration

```javascript
function greet() {
    console.log("Hello");
}
```

## Function Expression

```javascript
const greet = function () {
    console.log("Hello");
};
```

One important difference involves hoisting.

This works:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Function declarations are hoisted in a way that allows this call before the declaration appears in the source.

But this does not work:

```javascript
greet();

const greet = function () {
    console.log("Hello");
};
```

It throws a `ReferenceError` because `greet` is accessed before the `const` declaration is initialized.

We will cover the exact behavior under:

```text
Hoisting
Temporal Dead Zone
```

---

# 15. Named Function Expressions

A function expression can also have its own name.

```javascript
const greet = function sayHello() {
    console.log("Hello");
};
```

Call it using:

```javascript
greet();
```

The internal name:

```javascript
sayHello
```

is available inside the function itself, but normally not in the surrounding scope.

Named function expressions can be useful for:

- Recursion
- Debugging
- Stack traces

---

# 16. Anonymous Functions

A function without a name is called an anonymous function.

```javascript
function () {
    console.log("Hello");
}
```

By itself, this syntax is not a valid standalone function declaration.

Anonymous functions are commonly used as expressions:

```javascript
const greet = function () {
    console.log("Hello");
};
```

or as callbacks:

```javascript
setTimeout(function () {
    console.log("Hello");
}, 1000);
```

---

# 17. Arrow Functions

Arrow functions provide a shorter syntax for function expressions.

Traditional function expression:

```javascript
const greet = function () {
    console.log("Hello");
};
```

Arrow function:

```javascript
const greet = () => {
    console.log("Hello");
};
```

Both can be called normally:

```javascript
greet();
```

---

# 18. Arrow Functions with Parameters

Multiple parameters:

```javascript
const add = (a, b) => {
    return a + b;
};
```

One parameter:

```javascript
const greet = (name) => {
    console.log(`Hello ${name}`);
};
```

When there is exactly one simple parameter, parentheses are optional:

```javascript
const greet = name => {
    console.log(`Hello ${name}`);
};
```

Both are valid:

```javascript
name => {}
```

```javascript
(name) => {}
```

Many codebases keep parentheses for consistency.

---

# 19. Arrow Function Implicit Return

If an arrow function contains a single expression, we can omit:

```text
{}
return
```

Instead of:

```javascript
const add = (a, b) => {
    return a + b;
};
```

we can write:

```javascript
const add = (a, b) => a + b;
```

Example:

```javascript
const double = number => number * 2;

console.log(double(5)); // 10
```

This is called an **implicit return**.

---

# 20. Explicit vs Implicit Return

## Explicit Return

```javascript
const add = (a, b) => {
    return a + b;
};
```

## Implicit Return

```javascript
const add = (a, b) => a + b;
```

Both return the same result.

But once `{}` creates a block body:

```javascript
const add = (a, b) => {
    a + b;
};
```

the function does **not** return the expression automatically.

```javascript
console.log(add(10, 20)); // undefined
```

You need:

```javascript
const add = (a, b) => {
    return a + b;
};
```

---

# 21. Returning Objects from Arrow Functions

Consider:

```javascript
const createUser = () => {
    name: "John"
};
```

This does not return the object as intended because `{}` is interpreted as the function's block body.

To implicitly return an object, wrap the object literal in parentheses:

```javascript
const createUser = () => ({
    name: "John"
});
```

Now:

```javascript
console.log(createUser());
```

Result:

```javascript
{
    name: "John"
}
```

This pattern appears frequently in array transformations and React.

---

# 22. Regular Functions vs Arrow Functions

Arrow functions are not simply shorter regular functions.

They have important behavioral differences.

```text
Regular Function
→ has its own `this` depending on how it is called
→ has its own `arguments`
→ can be used as a constructor with `new`
→ has a `prototype` property when constructible

Arrow Function
→ does not create its own `this`
→ does not have its own `arguments`
→ cannot be used as a constructor with `new`
→ does not have a constructor-style `prototype`
```

Example:

```javascript
function regularFunction() {
    console.log(arguments);
}

regularFunction(10, 20);
```

A regular function has access to its own:

```javascript
arguments
```

An arrow function does not:

```javascript
const arrowFunction = () => {
    // no own arguments object
};
```

Arrow functions instead commonly use rest parameters:

```javascript
const arrowFunction = (...args) => {
    console.log(args);
};

arrowFunction(10, 20);
```

Output:

```javascript
[10, 20]
```

The exact `this` behavior will be covered in the dedicated **`this` keyword** section.

---

# 23. Functions Are First-Class Values

JavaScript functions are **first-class values**.

This means functions can be treated like other values.

They can be:

```text
Stored in variables
Passed as arguments
Returned from functions
Stored in objects
Stored in arrays
```

Example:

```javascript
const greet = function () {
    console.log("Hello");
};
```

Here the function is stored in:

```javascript
greet
```

---

# 24. Assigning a Function to Another Variable

```javascript
function greet() {
    console.log("Hello");
}

const anotherFunction = greet;

anotherFunction();
```

Output:

```text
Hello
```

Notice:

```javascript
const anotherFunction = greet;
```

not:

```javascript
const anotherFunction = greet();
```

The first assigns the function itself.

The second calls the function and assigns its return value.

---

# 25. Function Reference vs Function Call

This distinction is extremely important.

## Function Reference

```javascript
greet
```

means:

```text
the function itself
```

## Function Call

```javascript
greet()
```

means:

```text
execute the function now
```

Example:

```javascript
function greet() {
    return "Hello";
}

const a = greet;
const b = greet();
```

Here:

```text
a → function

b → "Hello"
```

This becomes very important with callbacks and React event handlers.

---

# 26. Passing Functions as Arguments

Because functions are values, we can pass a function into another function.

```javascript
function greet() {
    console.log("Hello");
}

function execute(fn) {
    fn();
}

execute(greet);
```

Output:

```text
Hello
```

Here:

```javascript
greet
```

is passed as a value.

Inside:

```javascript
execute()
```

it becomes:

```javascript
fn
```

and then:

```javascript
fn();
```

executes it.

---

# 27. Callback Functions

A function passed to another function so that the receiving function can invoke it is commonly called a **callback function**.

```javascript
function greet(name) {
    console.log(`Hello ${name}`);
}

function processUser(callback) {
    const username = "John";

    callback(username);
}

processUser(greet);
```

Here:

```javascript
greet
```

is the callback.

And:

```javascript
processUser
```

receives the callback and decides when/how to invoke it.

Callbacks are extremely common in JavaScript.

Examples include:

```javascript
setTimeout()
addEventListener()
map()
filter()
forEach()
```

Callbacks will get their own dedicated section later.

---

# 28. Inline Callback Functions

Instead of defining the callback separately:

```javascript
function greet() {
    console.log("Hello");
}

setTimeout(greet, 1000);
```

we can pass a function expression directly:

```javascript
setTimeout(function () {
    console.log("Hello");
}, 1000);
```

Or an arrow function:

```javascript
setTimeout(() => {
    console.log("Hello");
}, 1000);
```

---

# 29. Higher-Order Functions

A **higher-order function** is a function that does at least one of the following:

- Takes one or more functions as arguments
- Returns a function

Example:

```javascript
function execute(callback) {
    callback();
}
```

`execute()` is a higher-order function because it receives another function.

Array methods such as:

```javascript
map()
filter()
reduce()
forEach()
```

are higher-order functions because they receive callback functions.

---

# 30. Returning Functions

A function can return another function.

```javascript
function createGreeting() {
    return function () {
        console.log("Hello");
    };
}
```

Usage:

```javascript
const greet = createGreeting();

greet();
```

Output:

```text
Hello
```

Conceptually:

```text
createGreeting()

        ↓

returns function

        ↓

greet = returned function

        ↓

greet()

        ↓

"Hello"
```

We can also write:

```javascript
function createGreeting() {
    return () => {
        console.log("Hello");
    };
}
```

This concept becomes very important for:

```text
Closures
Higher-order functions
React patterns
```

---

# 31. Functions Stored in Objects

Functions can be properties of objects.

```javascript
const user = {
    name: "John",

    greet: function () {
        console.log("Hello");
    }
};

user.greet();
```

When a function is used as an object property and called through the object, it is commonly called a **method**.

Modern method syntax:

```javascript
const user = {
    name: "John",

    greet() {
        console.log("Hello");
    }
};
```

Usage:

```javascript
user.greet();
```

The relationship between methods and `this` will be covered later.

---

# 32. Functions Stored in Arrays

Functions can also be stored inside arrays.

```javascript
const operations = [
    function () {
        console.log("First");
    },

    function () {
        console.log("Second");
    }
];

operations[0]();
operations[1]();
```

Output:

```text
First
Second
```

Again, this is possible because functions are values.

---

# 33. Rest Parameters

Rest parameters allow a function to collect multiple arguments into an array.

Syntax:

```javascript
function functionName(...args) {
    // ...
}
```

Example:

```javascript
function sum(...numbers) {
    console.log(numbers);
}

sum(10, 20, 30);
```

Output:

```javascript
[10, 20, 30]
```

`numbers` is a real array.

Therefore, array methods can be used:

```javascript
function sum(...numbers) {
    return numbers.reduce((total, number) => total + number, 0);
}

console.log(sum(10, 20, 30)); // 60
```

We will cover `reduce()` properly under Arrays.

---

# 34. Normal Parameters with Rest Parameters

Rest parameters can be combined with normal parameters.

```javascript
function userInfo(name, ...skills) {
    console.log(name);
    console.log(skills);
}

userInfo("John", "JavaScript", "React", "Node.js");
```

Output:

```text
John

["JavaScript", "React", "Node.js"]
```

The rest parameter must be last.

Valid:

```javascript
function test(a, ...rest) {}
```

Invalid:

```javascript
function test(...rest, a) {}
```

---

# 35. Rest Parameters vs Spread Syntax

Both use:

```javascript
...
```

but their purpose depends on context.

## Rest

Collect values:

```javascript
function sum(...numbers) {
    console.log(numbers);
}

sum(1, 2, 3);
```

Result:

```javascript
[1, 2, 3]
```

Conceptually:

```text
1, 2, 3

   ↓ collect

[1, 2, 3]
```

## Spread

Expand values:

```javascript
const numbers = [1, 2, 3];

console.log(...numbers);
```

Conceptually:

```text
[1, 2, 3]

   ↓ expand

1, 2, 3
```

Quick rule:

```text
Rest
→ collect many values into one

Spread
→ expand one iterable/object into individual elements/properties
```

Spread and rest will get deeper coverage under ES6+.

---

# 36. The `arguments` Object

Regular non-arrow functions have access to an `arguments` object.

```javascript
function showArguments() {
    console.log(arguments);
}

showArguments(10, 20, 30);
```

`arguments` contains the arguments passed to the function.

Values can be accessed by index:

```javascript
function showArguments() {
    console.log(arguments[0]);
    console.log(arguments[1]);
}

showArguments("John", 25);
```

Output:

```text
John
25
```

However, `arguments` is **array-like**, not a real Array.

Modern JavaScript often prefers rest parameters:

```javascript
function showArguments(...args) {
    console.log(args);
}
```

Now:

```javascript
args
```

is a real array.

Arrow functions do not have their own `arguments` object.

---

# 37. Primitive Arguments

JavaScript passes arguments **by value**.

With primitives:

```javascript
function changeValue(value) {
    value = 100;
}

let number = 10;

changeValue(number);

console.log(number); // 10
```

Conceptually:

```text
number = 10

     ↓ value copied

value = 10

value = 100

number remains 10
```

Changing the parameter does not change the original variable.

---

# 38. Object Arguments

Objects require careful understanding.

```javascript
function changeUser(user) {
    user.name = "Alice";
}

const person = {
    name: "John"
};

changeUser(person);

console.log(person.name); // Alice
```

Why did the original object change?

Because JavaScript still passed a value, but that value was an **object reference**.

Conceptually:

```text
person ──────┐
             ↓
        { name: "John" }
             ↑
user ────────┘
```

Both reference values point to the same object.

Changing:

```javascript
user.name
```

mutates that shared object.

---

# 39. Reassigning an Object Parameter

Consider:

```javascript
function replaceUser(user) {
    user = {
        name: "Alice"
    };
}

const person = {
    name: "John"
};

replaceUser(person);

console.log(person.name); // John
```

Why didn't `person` change?

Because:

```javascript
user = {
    name: "Alice"
};
```

only reassigns the local parameter.

Conceptually:

Before:

```text
person ───┐
          ↓
      { name: "John" }
          ↑
user ─────┘
```

After reassignment:

```text
person ─────► { name: "John" }

user ───────► { name: "Alice" }
```

This is why saying:

```text
JavaScript passes objects by reference
```

is technically misleading.

A better statement is:

> JavaScript is pass-by-value. For objects, the value being passed is a reference to the object.

---

# 40. Function Scope

Variables declared inside a function are scoped to that function/block according to their declaration type.

```javascript
function test() {
    const message = "Hello";

    console.log(message);
}

test();
```

But:

```javascript
console.log(message);
```

outside the function produces:

```text
ReferenceError
```

Example:

```javascript
const globalValue = "Global";

function test() {
    const localValue = "Local";

    console.log(globalValue);
    console.log(localValue);
}

test();
```

The function can access the outer variable:

```javascript
globalValue
```

but outside code cannot access:

```javascript
localValue
```

Scope will get its own dedicated section next.

---

# 41. Lexical Scope Introduction

Functions can access variables from the scope where they were defined.

```javascript
const name = "John";

function greet() {
    console.log(name);
}

greet();
```

Output:

```text
John
```

Nested functions can access variables from outer functions:

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

This behavior is called **lexical scoping**.

It is the foundation of closures.

We will cover:

```text
Scope
Lexical Environment
Scope Chain
Closures
```

in dedicated sections.

---

# 42. Pure Functions

A **pure function** has two important characteristics:

1. The same inputs produce the same output.
2. It does not cause observable side effects.

Example:

```javascript
function add(a, b) {
    return a + b;
}
```

Calling:

```javascript
add(10, 20);
```

always returns:

```text
30
```

The function also does not modify external state.

---

# 43. Impure Functions

A function is impure if its result depends on changing external state or it causes side effects.

Example:

```javascript
let total = 0;

function addToTotal(value) {
    total += value;
}
```

This modifies:

```javascript
total
```

outside the function.

Another example:

```javascript
function randomNumber() {
    return Math.random();
}
```

The same input does not guarantee the same output.

Common side effects include:

```text
Changing external variables
Mutating objects outside the function
Writing to storage
Making network requests
Updating the DOM
Logging to the console
```

Side effects are not inherently bad.

Real applications need side effects.

The important concept is knowing where they occur and controlling them appropriately.

This becomes particularly important in React.

---

# 44. IIFE — Immediately Invoked Function Expression

An IIFE is a function expression that executes immediately after it is created.

Example:

```javascript
(function () {
    console.log("Hello");
})();
```

Output:

```text
Hello
```

Arrow version:

```javascript
(() => {
    console.log("Hello");
})();
```

Conceptually:

```text
Create function
      ↓
Immediately call it
```

Historically, IIFEs were often used to create private scopes before modern module systems and block-scoped declarations became common.

You may still encounter them in existing JavaScript code.

---

# 45. Recursion

Recursion occurs when a function calls itself.

Example:

```javascript
function countdown(number) {
    if (number <= 0) {
        return;
    }

    console.log(number);

    countdown(number - 1);
}

countdown(3);
```

Output:

```text
3
2
1
```

Execution:

```text
countdown(3)
    ↓
countdown(2)
    ↓
countdown(1)
    ↓
countdown(0)
    ↓
return
```

---

# 46. Base Case in Recursion

Recursive functions need a condition that stops recursion.

This is called the **base case**.

```javascript
if (number <= 0) {
    return;
}
```

Without it:

```javascript
function test() {
    test();
}

test();
```

the function continues calling itself until the call stack limit is exceeded, typically causing an error such as:

```text
RangeError: Maximum call stack size exceeded
```

---

# 47. Function Call Stack

When functions call other functions, JavaScript tracks active function calls using the **call stack**.

Example:

```javascript
function first() {
    second();
}

function second() {
    third();
}

function third() {
    console.log("Hello");
}

first();
```

Conceptually:

```text
first()
   ↓
second()
   ↓
third()
```

Call stack:

```text
| third  |
| second |
| first  |
```

When `third()` finishes:

```text
| second |
| first  |
```

Then `second()` finishes:

```text
| first |
```

Then `first()` finishes:

```text
empty
```

Understanding the call stack becomes important when learning:

```text
Recursion
Callbacks
Promises
async/await
Event Loop
```

---

# 48. Functions and Hoisting

Function declarations can be called before their declaration appears:

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

Function expressions using `const` cannot be accessed before initialization:

```javascript
greet();

const greet = function () {
    console.log("Hello");
};
```

This produces a `ReferenceError`.

The same applies to arrow functions assigned to `const`:

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

Also a `ReferenceError`.

We will cover exactly why under **Hoisting**.

---

# 49. Functions and `this`

Regular functions and arrow functions handle `this` differently.

Example:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Output:

```text
John
```

But arrow functions do not create their own `this`.

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

This does **not** make `this` refer to `user`.

Do not remember:

```text
this = object where function is written
```

That is incorrect.

For regular functions, `this` generally depends on **how the function is called**.

For arrow functions, `this` is resolved lexically from the surrounding scope.

This deserves its own section and will be covered under:

```text
this Keyword
```

---

# 50. React Event Handler: Reference vs Call

A very important React example:

```jsx
function handleClick() {
    console.log("Clicked");
}

return (
    <button onClick={handleClick}>
        Click
    </button>
);
```

Here:

```jsx
onClick={handleClick}
```

passes the function.

It does not execute it immediately.

Compare:

```jsx
onClick={handleClick()}
```

This calls `handleClick()` while rendering and passes its return value as the handler.

Usually this is not what you want.

Remember:

```text
handleClick
→ function reference

handleClick()
→ function call
```

---

# 51. Passing Arguments to React Event Handlers

Suppose:

```javascript
function deleteUser(id) {
    console.log(id);
}
```

This:

```jsx
<button onClick={deleteUser(10)}>
    Delete
</button>
```

calls the function during rendering.

Instead:

```jsx
<button onClick={() => deleteUser(10)}>
    Delete
</button>
```

Now React receives a function:

```javascript
() => deleteUser(10)
```

When the click occurs, that function executes:

```javascript
deleteUser(10);
```

This callback pattern appears constantly in React.

---

# 52. Functions as React Components

A React function component is fundamentally a JavaScript function.

```jsx
function Welcome() {
    return <h1>Hello</h1>;
}
```

Or:

```jsx
const Welcome = () => {
    return <h1>Hello</h1>;
};
```

Or using implicit return:

```jsx
const Welcome = () => <h1>Hello</h1>;
```

The function returns JSX describing the UI.

---

# 53. Props Are Function Inputs

Conceptually, React props are input to a component function.

```jsx
function Welcome(props) {
    return <h1>Hello {props.name}</h1>;
}
```

Usage:

```jsx
<Welcome name="John" />
```

Conceptually:

```text
Component
    ↓
function Welcome(props)

Input
    ↓
props

Output
    ↓
JSX
```

Later, destructuring makes this cleaner:

```jsx
function Welcome({ name }) {
    return <h1>Hello {name}</h1>;
}
```

---

# 54. Functions and Array Methods in React

Suppose:

```javascript
const users = ["John", "Alice", "Bob"];
```

React frequently uses callback functions with `map()`:

```jsx
return (
    <ul>
        {users.map((user) => (
            <li key={user}>{user}</li>
        ))}
    </ul>
);
```

The callback:

```javascript
(user) => (
    <li key={user}>{user}</li>
)
```

is a function passed to:

```javascript
map()
```

Understanding:

```text
Functions
Callbacks
Arrow functions
Return values
```

is therefore essential before React.

---

# 55. Common Function Mistakes

## Mistake 1: Forgetting `return`

Incorrect:

```javascript
function add(a, b) {
    a + b;
}

console.log(add(10, 20)); // undefined
```

Correct:

```javascript
function add(a, b) {
    return a + b;
}
```

---

## Mistake 2: Arrow Function Block Without `return`

Incorrect:

```javascript
const add = (a, b) => {
    a + b;
};
```

Correct:

```javascript
const add = (a, b) => {
    return a + b;
};
```

Or:

```javascript
const add = (a, b) => a + b;
```

---

## Mistake 3: Returning an Object Incorrectly

Incorrect:

```javascript
const createUser = () => {
    name: "John"
};
```

Correct:

```javascript
const createUser = () => ({
    name: "John"
});
```

---

## Mistake 4: Function Reference vs Function Call

```javascript
greet;
```

is not the same as:

```javascript
greet();
```

Remember:

```text
greet   → function value/reference
greet() → execute function
```

---

## Mistake 5: Assuming Arrow Functions Have Their Own `this`

They do not.

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

The arrow function does not receive `user` as its `this`.

---

## Mistake 6: Saying Objects Are Passed by Reference

A common explanation is:

```text
Primitives → passed by value
Objects    → passed by reference
```

This is misleading.

The more accurate model is:

```text
JavaScript arguments are passed by value.

Primitive:
→ primitive value copied

Object:
→ reference value copied
→ both references can point to the same object
```

---

# 56. Quick Revision

```text
JavaScript Functions

Function Declaration
function greet() {}

Function Expression
const greet = function () {};

Arrow Function
const greet = () => {};

Parameter
function greet(name) {}
               ↑

Argument
greet("John");
       ↑

Return
function add(a, b) {
    return a + b;
}

Default Parameter
function greet(name = "Guest") {}

Rest Parameter
function sum(...numbers) {}

Callback
function passed to another function

Higher-Order Function
function that receives and/or returns functions

IIFE
(function () {})();

Recursion
function calling itself
```

---

# 57. Function Forms

### Function Declaration

```javascript
function add(a, b) {
    return a + b;
}
```

### Function Expression

```javascript
const add = function (a, b) {
    return a + b;
};
```

### Arrow Function

```javascript
const add = (a, b) => {
    return a + b;
};
```

### Arrow Function with Implicit Return

```javascript
const add = (a, b) => a + b;
```

### Returning an Object

```javascript
const createUser = () => ({
    name: "John"
});
```

---

# 58. Core Concepts to Remember

```text
Functions are first-class values.

Functions can:
→ be assigned to variables
→ be passed as arguments
→ be returned from functions
→ be stored in objects
→ be stored in arrays

functionName
→ function itself

functionName()
→ execute function

return
→ sends a value back
→ ends current function execution

Arrow functions
→ shorter function-expression syntax
→ no own `this`
→ no own `arguments`
→ cannot be constructors

Rest parameters
→ collect arguments into an array

JavaScript arguments
→ always passed by value
→ object values contain references to objects
```

---

# 59. React-Relevant Concepts

The function concepts that will appear constantly in React are:

```text
Function Components
Arrow Functions
Callbacks
Higher-Order Functions
Function References
Event Handlers
Returning JSX
Functions passed as props
Array callbacks
Closures
Lexical Scope
Pure Functions
```

Examples:

```jsx
const App = () => {
    return <h1>Hello</h1>;
};
```

```jsx
<button onClick={handleClick}>
    Click
</button>
```

```jsx
<button onClick={() => deleteUser(id)}>
    Delete
</button>
```

```jsx
{users.map((user) => (
    <User key={user.id} user={user} />
))}
```

These are ordinary JavaScript function concepts being used by React.
