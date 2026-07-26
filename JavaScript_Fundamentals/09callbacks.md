#  Callbacks

A **callback** is a function passed to another function so that the receiving function can execute it at an appropriate time.

Example:

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

Output:

```text
Hello John
```

Here:

```javascript
greet
```

is passed to:

```javascript
processUser()
```

and `processUser()` later calls it.

So `greet` is the **callback function**.

---

# 1. What Is a Callback?

A callback is simply:

> A function given to another function so the receiving function can invoke it.

Example:

```javascript
function sayHello() {
    console.log("Hello");
}

function execute(callback) {
    callback();
}

execute(sayHello);
```

Output:

```text
Hello
```

Here:

```text
sayHello
→ callback function

execute
→ function receiving the callback
```

---

# 2. Functions Are First-Class Values

Callbacks are possible because JavaScript treats functions as **first-class values**.

This means functions can be:

```text
Stored in variables
Passed as arguments
Returned from functions
Stored in objects
Stored in arrays
```

Example:

```javascript
function greet() {
    console.log("Hello");
}

const myFunction = greet;

myFunction();
```

Output:

```text
Hello
```

The function itself can be treated like a value.

---

# 3. Passing Functions as Arguments

Normal values can be passed to functions:

```javascript
function show(value) {
    console.log(value);
}

show(10);
```

Functions can also be passed:

```javascript
function greet() {
    console.log("Hello");
}

function execute(fn) {
    fn();
}

execute(greet);
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

the parameter:

```javascript
fn
```

references the same function.

---

# 4. Function Reference vs Function Call

This distinction is extremely important.

Consider:

```javascript
function greet() {
    console.log("Hello");
}
```

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

When passing a callback, we usually pass:

```javascript
execute(greet);
```

Not:

```javascript
execute(greet());
```

---

# 5. Why `greet` and `greet()` Are Different

Consider:

```javascript
function greet() {
    console.log("Hello");

    return 100;
}

function execute(callback) {
    console.log(callback);
}
```

This:

```javascript
execute(greet);
```

passes the function.

But:

```javascript
execute(greet());
```

first executes:

```javascript
greet()
```

which prints:

```text
Hello
```

and returns:

```text
100
```

So it becomes equivalent to:

```javascript
execute(100);
```

Therefore:

```text
greet
→ function reference

greet()
→ result of executing greet
```

This distinction becomes very important in React event handlers.

---

# 6. Basic Callback Example

```javascript
function calculate(a, b, operation) {
    return operation(a, b);
}

function add(a, b) {
    return a + b;
}

const result = calculate(10, 20, add);

console.log(result);
```

Output:

```text
30
```

Execution:

```text
calculate(10, 20, add)
        ↓
operation = add
        ↓
operation(10, 20)
        ↓
add(10, 20)
        ↓
30
```

---

# 7. Why Use Callbacks?

Without callbacks:

```javascript
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

function multiply(a, b) {
    return a * b;
}
```

Each operation exists separately.

With callbacks:

```javascript
function calculate(a, b, operation) {
    return operation(a, b);
}
```

Now:

```javascript
console.log(calculate(10, 5, add));      // 15
console.log(calculate(10, 5, subtract)); // 5
console.log(calculate(10, 5, multiply)); // 50
```

The main function controls:

```text
when/how the operation happens
```

while the callback controls:

```text
what operation happens
```

This makes functions reusable and configurable.

---

# 8. Higher-Order Functions

A function that:

```text
takes another function as an argument

OR

returns another function
```

is called a **higher-order function**.

Example:

```javascript
function execute(callback) {
    callback();
}
```

`execute()` is a higher-order function because it receives another function.

The callback:

```javascript
callback
```

is the function passed into it.

So:

```text
Higher-Order Function
→ receives/returns functions

Callback
→ function passed for later/in-context invocation
```

---

# 9. Named Callbacks

A callback can be a named function.

```javascript
function greet(name) {
    console.log(`Hello ${name}`);
}

function processUser(callback) {
    callback("John");
}

processUser(greet);
```

Output:

```text
Hello John
```

Named callbacks can be useful when:

```text
The function is reused
The function is large
You want a descriptive name
You want easier debugging
```

---

# 10. Anonymous Callbacks

A callback can also be created directly as an anonymous function.

```javascript
function processUser(callback) {
    callback("John");
}

processUser(function (name) {
    console.log(`Hello ${name}`);
});
```

Output:

```text
Hello John
```

The callback does not need a separately declared name.

---

# 11. Arrow Function Callbacks

Modern JavaScript commonly uses arrow functions as callbacks.

```javascript
function processUser(callback) {
    callback("John");
}

processUser((name) => {
    console.log(`Hello ${name}`);
});
```

Output:

```text
Hello John
```

This pattern appears constantly in modern JavaScript and React.

---

# 12. Callback with Parameters

The function receiving the callback decides what arguments to provide when invoking it.

```javascript
function calculate(a, b, callback) {
    return callback(a, b);
}

function multiply(x, y) {
    return x * y;
}

console.log(calculate(5, 4, multiply));
```

Output:

```text
20
```

Inside:

```javascript
calculate()
```

we call:

```javascript
callback(a, b);
```

which becomes:

```javascript
multiply(5, 4);
```

---

# 13. Callback Return Values

Callbacks can return values.

```javascript
function calculate(a, b, callback) {
    const result = callback(a, b);

    return result;
}

const result = calculate(10, 5, (a, b) => {
    return a + b;
});

console.log(result);
```

Output:

```text
15
```

The callback returns:

```text
15
```

to `calculate()`.

Then `calculate()` returns that result to the caller.

---

# 14. Multiple Callbacks

A function can receive multiple callbacks.

```javascript
function processNumber(number, onPositive, onNegative) {
    if (number >= 0) {
        onPositive(number);
    } else {
        onNegative(number);
    }
}

function handlePositive(number) {
    console.log(`${number} is positive`);
}

function handleNegative(number) {
    console.log(`${number} is negative`);
}

processNumber(10, handlePositive, handleNegative);
```

Output:

```text
10 is positive
```

Different callbacks can handle different situations.

---

# 15. Callbacks Can Be Synchronous or Asynchronous

Callbacks are often associated with asynchronous programming, but:

> A callback is not automatically asynchronous.

There are two common categories:

```text
Synchronous Callback

Asynchronous Callback
```

---

# 16. Synchronous Callbacks

A synchronous callback executes during the current function call before the receiving function returns.

Example:

```javascript
function execute(callback) {
    console.log("Before");

    callback();

    console.log("After");
}

execute(() => {
    console.log("Callback");
});
```

Output:

```text
Before
Callback
After
```

Execution:

```text
execute()
    ↓
"Before"
    ↓
callback()
    ↓
"Callback"
    ↓
"After"
```

Everything happens synchronously.

---

# 17. Array Methods Use Callbacks

Many JavaScript array methods use synchronous callbacks.

Common examples:

```javascript
forEach()
map()
filter()
find()
some()
every()
reduce()
sort()
```

Example:

```javascript
const numbers = [1, 2, 3];

numbers.forEach((number) => {
    console.log(number);
});
```

Output:

```text
1
2
3
```

The function:

```javascript
(number) => {
    console.log(number);
}
```

is a callback.

---

# 18. `forEach()` Callback

```javascript
const users = ["John", "Alice", "Bob"];

users.forEach((user) => {
    console.log(user);
});
```

Output:

```text
John
Alice
Bob
```

`forEach()` invokes the callback once for each array element.

Conceptually:

```text
forEach()
│
├── callback("John")
├── callback("Alice")
└── callback("Bob")
```

---

# 19. `map()` Callback

`map()` uses a callback to transform every element and creates a new array from the callback results.

```javascript
const numbers = [1, 2, 3];

const doubled = numbers.map((number) => {
    return number * 2;
});

console.log(doubled);
```

Output:

```javascript
[2, 4, 6]
```

Conceptually:

```text
1 → callback → 2
2 → callback → 4
3 → callback → 6
```

---

# 20. `filter()` Callback

`filter()` uses a callback to decide whether each element should be included.

```javascript
const numbers = [1, 2, 3, 4, 5];

const evenNumbers = numbers.filter((number) => {
    return number % 2 === 0;
});

console.log(evenNumbers);
```

Output:

```javascript
[2, 4]
```

The callback returns:

```text
true
→ keep element

false
→ exclude element
```

---

# 21. `find()` Callback

`find()` returns the first element for which the callback produces a truthy result.

```javascript
const users = [
    { id: 1, name: "John" },
    { id: 2, name: "Alice" },
    { id: 3, name: "Bob" }
];

const user = users.find((user) => {
    return user.id === 2;
});

console.log(user);
```

Output:

```javascript
{
    id: 2,
    name: "Alice"
}
```

---

# 22. `some()` Callback

`some()` checks whether at least one element satisfies a condition.

```javascript
const numbers = [1, 3, 5, 8];

const hasEvenNumber = numbers.some((number) => {
    return number % 2 === 0;
});

console.log(hasEvenNumber);
```

Output:

```text
true
```

---

# 23. `every()` Callback

`every()` checks whether all elements satisfy a condition.

```javascript
const numbers = [2, 4, 6];

const allEven = numbers.every((number) => {
    return number % 2 === 0;
});

console.log(allEven);
```

Output:

```text
true
```

---

# 24. `reduce()` Callback

`reduce()` uses a callback to combine array elements into a single accumulated result.

```javascript
const numbers = [1, 2, 3, 4];

const total = numbers.reduce((accumulator, number) => {
    return accumulator + number;
}, 0);

console.log(total);
```

Output:

```text
10
```

The callback receives:

```text
accumulator
current element
```

Conceptually:

```text
0 + 1 = 1
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10
```

---

# 25. Callback Arguments from Array Methods

Many array methods provide more than just the current element.

Example:

```javascript
const users = ["John", "Alice", "Bob"];

users.forEach((user, index, array) => {
    console.log(user);
    console.log(index);
    console.log(array);
});
```

The callback can receive:

```text
Current Element
Index
Array
```

For `forEach()`:

```javascript
callback(element, index, array)
```

You only need to declare the parameters you actually use.

---

# 26. Asynchronous Callbacks

An asynchronous callback executes later, after some asynchronous operation or scheduled task.

Example:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Callback");
}, 1000);

console.log("End");
```

Output:

```text
Start
End
Callback
```

Not:

```text
Start
Callback
End
```

The callback is scheduled to run later.

---

# 27. `setTimeout()` Callback

```javascript
setTimeout(() => {
    console.log("Hello");
}, 2000);
```

Here:

```javascript
() => {
    console.log("Hello");
}
```

is the callback.

`setTimeout()` receives:

```text
callback
delay
```

Conceptually:

```text
setTimeout(callback, 2000)
```

After the timer condition is satisfied, the callback becomes eligible to execute according to the JavaScript runtime's event-loop scheduling.

---

# 28. `setTimeout()` Does Not Pause JavaScript

Consider:

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 2000);

console.log("C");
```

Output:

```text
A
C
B
```

JavaScript does not sit and wait at:

```javascript
setTimeout()
```

for two seconds.

Instead:

```text
console.log("A")
↓
setTimeout schedules callback
↓
console.log("C")
↓
current synchronous work finishes
↓
callback runs later when eligible
```

---

# 29. `setTimeout(..., 0)` Does Not Mean Immediate

Consider:

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

Output:

```text
A
C
B
```

Even with:

```text
0 milliseconds
```

the callback does not interrupt the currently executing synchronous code.

It runs later when the runtime can process it.

The deeper mechanism involves:

```text
Call Stack
Host APIs
Task Queue
Event Loop
```

We will cover these concepts in asynchronous JavaScript.

---

# 30. Event Callbacks

Browser events commonly use callbacks.

```javascript
const button = document.querySelector("#myButton");

button.addEventListener("click", () => {
    console.log("Button clicked");
});
```

The callback is:

```javascript
() => {
    console.log("Button clicked");
}
```

It does not execute immediately.

It executes when the relevant event occurs.

---

# 31. Event Object in Callbacks

Event callbacks receive information about the event.

```javascript
button.addEventListener("click", (event) => {
    console.log(event);
});
```

The browser provides the:

```javascript
event
```

object when it invokes the callback.

Example:

```javascript
button.addEventListener("click", (event) => {
    console.log(event.target);
});
```

So the code registering the callback does not need to manually create the event object.

---

# 32. Custom Callback Example

Callbacks are not limited to built-in JavaScript APIs.

We can create our own callback-based functions.

```javascript
function fetchUser(id, callback) {
    const user = {
        id,
        name: "John"
    };

    callback(user);
}

fetchUser(1, (user) => {
    console.log(user.name);
});
```

Output:

```text
John
```

Note:

```javascript
fetchUser()
```

in this example is synchronous.

Naming a function `fetchUser` does not automatically make it asynchronous.

---

# 33. Callback Execution Is Controlled by the Receiver

Consider:

```javascript
function execute(callback) {
    callback();
}
```

The receiving function decides:

```text
Whether callback is called
When callback is called
How many times callback is called
What arguments callback receives
```

Example:

```javascript
function repeat(callback) {
    callback();
    callback();
    callback();
}

repeat(() => {
    console.log("Hello");
});
```

Output:

```text
Hello
Hello
Hello
```

This is an important property of callback-based APIs.

---

# 34. A Callback May Never Be Called

```javascript
function process(value, callback) {
    if (value > 0) {
        callback(value);
    }
}
```

Usage:

```javascript
process(-10, (value) => {
    console.log(value);
});
```

The callback never executes because:

```javascript
value > 0
```

is false.

So passing a callback does not guarantee it will execute unless the API's contract guarantees that behavior.

---

# 35. Callback May Be Called Multiple Times

```javascript
function processItems(items, callback) {
    for (const item of items) {
        callback(item);
    }
}

processItems([1, 2, 3], (item) => {
    console.log(item);
});
```

Output:

```text
1
2
3
```

The callback executes once for every item.

This is exactly the kind of pattern used by array methods.

---

# 36. Callbacks and Closures

Callbacks frequently form closures.

```javascript
function processUser() {
    const username = "John";

    setTimeout(() => {
        console.log(username);
    }, 1000);
}

processUser();
```

The callback:

```javascript
() => {
    console.log(username);
}
```

closes over:

```javascript
username
```

So even after `processUser()` finishes, the callback can access the relevant lexical environment when it runs later.

This connects the previous topic:

```text
Closures
```

with:

```text
Callbacks
```

---

# 37. Callback vs Closure

These are different concepts.

## Callback

```text
A function passed to another function
for that function/API to invoke.
```

## Closure

```text
A function together with access to
its lexical environment.
```

A callback can also be a closure.

Example:

```javascript
function outer() {
    const value = 10;

    setTimeout(() => {
        console.log(value);
    }, 1000);
}
```

The arrow function is:

```text
Callback
→ because it is passed to setTimeout

Closure
→ because it accesses value from outer scope
```

The same function can therefore be both.

---

# 38. Error-First Callbacks

Traditional Node.js APIs commonly use **error-first callbacks**.

Pattern:

```javascript
function callback(error, result) {
    // ...
}
```

Convention:

```text
First argument
→ error

Second argument
→ successful result
```

Example conceptually:

```javascript
readData((error, data) => {
    if (error) {
        console.error(error);
        return;
    }

    console.log(data);
});
```

If something fails:

```text
error → Error object
data → unavailable
```

If successful:

```text
error → null
data → result
```

This pattern is common in older and callback-based Node.js APIs.

Modern JavaScript also heavily uses:

```text
Promises
async/await
```

---

# 39. Callback Hell

Nested asynchronous callbacks can become difficult to read.

Example:

```javascript
getUser(userId, (user) => {
    getOrders(user.id, (orders) => {
        getOrderDetails(orders[0].id, (details) => {
            processPayment(details, (result) => {
                console.log(result);
            });
        });
    });
});
```

Structure:

```text
getUser
    ↓
    getOrders
        ↓
        getOrderDetails
            ↓
            processPayment
```

This deeply nested pattern is commonly called:

```text
Callback Hell
```

or:

```text
Pyramid of Doom
```

---

# 40. Why Callback Hell Is a Problem

Deeply nested callback code can make it harder to:

```text
Read control flow
Handle errors
Reuse logic
Maintain code
Debug code
Reason about execution order
```

Example:

```javascript
operation1((result1) => {
    operation2(result1, (result2) => {
        operation3(result2, (result3) => {
            operation4(result3, (result4) => {
                // ...
            });
        });
    });
});
```

This was one motivation for wider use of:

```text
Promises
async/await
```

---

# 41. Reducing Callback Nesting with Named Functions

Instead of:

```javascript
getUser(1, (user) => {
    getOrders(user.id, (orders) => {
        console.log(orders);
    });
});
```

we could separate logic:

```javascript
function handleOrders(orders) {
    console.log(orders);
}

function handleUser(user) {
    getOrders(user.id, handleOrders);
}

getUser(1, handleUser);
```

This can reduce nesting.

However, for many asynchronous workflows, Promises and `async/await` provide more structured composition.

---

# 42. Inversion of Control

Callbacks introduce an important idea called **inversion of control**.

When we write:

```javascript
someFunction(callback);
```

we give another function/API control over invoking our callback.

It may control:

```text
When it executes
How many times it executes
What arguments it receives
Whether it executes at all
```

For built-in APIs this behavior is defined by their contract.

For third-party callback APIs, your code depends on that API honoring its documented behavior.

This concept is one reason asynchronous abstractions such as Promises can be useful.

---

# 43. Callback vs Promise

Callback style:

```javascript
getUser(1, (error, user) => {
    if (error) {
        console.error(error);
        return;
    }

    console.log(user);
});
```

Promise style:

```javascript
getUser(1)
    .then((user) => {
        console.log(user);
    })
    .catch((error) => {
        console.error(error);
    });
```

Promises provide an object representing the eventual completion or failure of an asynchronous operation.

They make chaining and centralized error propagation easier than many deeply nested callback designs.

Promises will be covered separately in asynchronous JavaScript.

---

# 44. Callback vs `async/await`

Promise-based code:

```javascript
getUser(1)
    .then((user) => {
        return getOrders(user.id);
    })
    .then((orders) => {
        console.log(orders);
    })
    .catch((error) => {
        console.error(error);
    });
```

With `async/await`:

```javascript
async function loadData() {
    try {
        const user = await getUser(1);
        const orders = await getOrders(user.id);

        console.log(orders);
    } catch (error) {
        console.error(error);
    }
}
```

`async/await` is built on Promises.

It often makes asynchronous workflows look closer to normal sequential code.

But callbacks are still fundamental because they appear everywhere in JavaScript.

---

# 45. Promises Still Use Callbacks

Even when using Promises, callbacks do not disappear.

Example:

```javascript
fetch("/api/users")
    .then((response) => {
        return response.json();
    })
    .then((users) => {
        console.log(users);
    })
    .catch((error) => {
        console.error(error);
    });
```

These functions:

```javascript
(response) => { ... }

(users) => { ... }

(error) => { ... }
```

are callbacks passed to:

```text
then()
catch()
```

So understanding callbacks remains essential.

---

# 46. `async/await` Does Not Eliminate Callbacks Either

Even in an `async` function:

```javascript
async function loadUsers() {
    const response = await fetch("/api/users");
    const users = await response.json();

    users.forEach((user) => {
        console.log(user.name);
    });
}
```

This:

```javascript
(user) => {
    console.log(user.name);
}
```

is still a callback.

Callbacks are a general JavaScript function pattern, not just an old asynchronous technique.

---

# 47. Common Mistake: Calling Instead of Passing

Incorrect:

```javascript
button.addEventListener("click", handleClick());
```

This executes:

```javascript
handleClick()
```

immediately and passes its return value to `addEventListener`.

Usually you want:

```javascript
button.addEventListener("click", handleClick);
```

Now the browser receives the function and can call it when the event occurs.

---

# 48. When You Need to Pass Arguments

Suppose:

```javascript
function handleClick(id) {
    console.log(id);
}
```

This is usually incorrect for an event listener:

```javascript
button.addEventListener("click", handleClick(10));
```

because:

```javascript
handleClick(10)
```

executes immediately.

Instead, wrap it in another function:

```javascript
button.addEventListener("click", () => {
    handleClick(10);
});
```

Now:

```text
() => {
    handleClick(10);
}
```

is the callback.

When the click occurs, that callback executes and then calls:

```javascript
handleClick(10)
```

---

# 49. Callback Wrapper Pattern

This pattern appears frequently:

```javascript
() => someFunction(value)
```

Why?

Because:

```javascript
someFunction(value)
```

means:

```text
execute now
```

while:

```javascript
() => someFunction(value)
```

means:

```text
create a function that will execute someFunction(value) later
```

Example:

```javascript
setTimeout(() => {
    greet("John");
}, 1000);
```

instead of:

```javascript
setTimeout(greet("John"), 1000);
```

---

# 50. Common Mistake: Expecting an Async Callback Result Immediately

Consider:

```javascript
function getValue() {
    setTimeout(() => {
        return 10;
    }, 1000);
}

const result = getValue();

console.log(result);
```

Output:

```text
undefined
```

Why?

The `return 10` belongs to the callback:

```javascript
() => {
    return 10;
}
```

It does **not** return from:

```javascript
getValue()
```

`getValue()` finishes before the timeout callback executes.

This is a fundamental asynchronous programming concept.

---

# 51. Returning from a Callback

Consider:

```javascript
function process(callback) {
    const result = callback();

    console.log(result);
}

process(() => {
    return 100;
});
```

Output:

```text
100
```

This works because the callback is executed synchronously:

```javascript
const result = callback();
```

But:

```javascript
function process() {
    setTimeout(() => {
        return 100;
    }, 1000);
}
```

the callback's `return` value is not automatically returned from `process()`.

Always ask:

```text
Which function does this return belong to?
```

---

# 52. Common Mistake: Assuming All Callbacks Run Later

This callback runs immediately during `map()`:

```javascript
const result = [1, 2, 3].map((number) => {
    return number * 2;
});
```

This callback runs later:

```javascript
setTimeout(() => {
    console.log("Hello");
}, 1000);
```

So:

```text
Callback
≠
Asynchronous
```

Callbacks can be:

```text
Synchronous
or
Asynchronous
```

depending on the API receiving them.

---

# 53. Common Mistake: Forgetting `return` in `map()`

Incorrect:

```javascript
const numbers = [1, 2, 3];

const doubled = numbers.map((number) => {
    number * 2;
});

console.log(doubled);
```

Output:

```javascript
[undefined, undefined, undefined]
```

Why?

The callback does not return anything.

Correct:

```javascript
const doubled = numbers.map((number) => {
    return number * 2;
});
```

Or:

```javascript
const doubled = numbers.map(
    (number) => number * 2
);
```

Output:

```javascript
[2, 4, 6]
```

---

# 54. Common Mistake: `forEach()` Return Value

Consider:

```javascript
const numbers = [1, 2, 3];

const result = numbers.forEach((number) => {
    return number * 2;
});

console.log(result);
```

Output:

```text
undefined
```

`forEach()` ignores callback return values for building a result and itself returns `undefined`.

If you want to transform an array:

```javascript
const result = numbers.map((number) => {
    return number * 2;
});
```

Result:

```javascript
[2, 4, 6]
```

---

# 55. React Uses Callbacks Everywhere

Callbacks are fundamental in React.

You will see them in:

```text
Event handlers
State setters
Array rendering
Callback props
useEffect
useMemo
useCallback
Promise handlers
Form handling
Custom hooks
```

Understanding callbacks before React is extremely important.

---

# 56. React Event Handler

```jsx
function App() {
    function handleClick() {
        console.log("Clicked");
    }

    return (
        <button onClick={handleClick}>
            Click
        </button>
    );
}
```

Here:

```javascript
handleClick
```

is passed to React as a callback.

React invokes it when the click event occurs.

---

# 57. React: Reference vs Call

Correct:

```jsx
<button onClick={handleClick}>
    Click
</button>
```

This passes:

```javascript
handleClick
```

to React.

Incorrect when you intend it to run only on click:

```jsx
<button onClick={handleClick()}>
    Click
</button>
```

This executes:

```javascript
handleClick()
```

during rendering and uses its return value as the `onClick` prop.

This is one of the most common beginner React mistakes.

---

# 58. React Event Handler with Arguments

Suppose:

```javascript
function deleteUser(id) {
    console.log(id);
}
```

Incorrect:

```jsx
<button onClick={deleteUser(user.id)}>
    Delete
</button>
```

because it executes during rendering.

Correct:

```jsx
<button
    onClick={() => {
        deleteUser(user.id);
    }}
>
    Delete
</button>
```

Or:

```jsx
<button onClick={() => deleteUser(user.id)}>
    Delete
</button>
```

Now React receives a callback.

When clicked:

```text
callback executes
↓
deleteUser(user.id)
```

---

# 59. React Callback Props

A parent component can pass a callback to a child component.

```jsx
function Parent() {
    function handleSave() {
        console.log("Saved");
    }

    return <Child onSave={handleSave} />;
}
```

Child:

```jsx
function Child({ onSave }) {
    return (
        <button onClick={onSave}>
            Save
        </button>
    );
}
```

Flow:

```text
Parent
│
│ creates handleSave
│
▼
passes callback
│
▼
Child
│
│ button clicked
│
▼
onSave()
│
▼
handleSave()
```

This is a fundamental React pattern.

---

# 60. React Child-to-Parent Communication

Props normally flow:

```text
Parent
↓
Child
```

A child can communicate an event or value back to its parent by invoking a callback prop supplied by the parent.

Parent:

```jsx
function Parent() {
    function handleUserSelected(user) {
        console.log(user);
    }

    return (
        <UserList
            onUserSelected={handleUserSelected}
        />
    );
}
```

Child:

```jsx
function UserList({ onUserSelected }) {
    const user = {
        id: 1,
        name: "John"
    };

    return (
        <button
            onClick={() => {
                onUserSelected(user);
            }}
        >
            Select
        </button>
    );
}
```

Flow:

```text
Parent creates callback
↓
callback passed through props
↓
Child receives callback
↓
event occurs
↓
Child invokes callback
↓
Parent's function runs with supplied data
```

---

# 61. React `map()` Callbacks

Rendering lists uses callbacks frequently.

```jsx
function UserList({ users }) {
    return (
        <ul>
            {users.map((user) => {
                return (
                    <li key={user.id}>
                        {user.name}
                    </li>
                );
            })}
        </ul>
    );
}
```

The function:

```javascript
(user) => {
    return ...
}
```

is a callback passed to:

```javascript
map()
```

---

# 62. React Functional State Update Callback

React state setters can accept an updater function.

```jsx
setCount((previousCount) => {
    return previousCount + 1;
});
```

Or:

```jsx
setCount((previousCount) => previousCount + 1);
```

The updater function is given the previous/pending state and returns the next state.

This is useful when the next state depends on the previous state.

Example:

```jsx
function handleClick() {
    setCount((count) => count + 1);
}
```

---

# 63. React `useEffect` Callback

Example:

```jsx
useEffect(() => {
    console.log("Component rendered");
});
```

The function passed to:

```javascript
useEffect()
```

is an effect callback.

With dependencies:

```jsx
useEffect(() => {
    console.log(userId);
}, [userId]);
```

React controls when the effect callback runs according to effect semantics and the dependency list.

We will cover this properly when studying React Hooks.

---

# 64. React Effect Cleanup Callback

An effect callback can return a cleanup function.

```jsx
useEffect(() => {
    const handleResize = () => {
        console.log("Resized");
    };

    window.addEventListener("resize", handleResize);

    return () => {
        window.removeEventListener(
            "resize",
            handleResize
        );
    };
}, []);
```

There are two functions involved:

```text
Effect function
↓
passed to useEffect

Cleanup function
↓
returned by effect function
```

React invokes the cleanup at the appropriate lifecycle points.

This combines:

```text
Callbacks
Closures
Functions returning functions
```

---

# 65. React Callbacks and Closures

Consider:

```jsx
function User({ user }) {
    const handleClick = () => {
        console.log(user.id);
    };

    return (
        <button onClick={handleClick}>
            View
        </button>
    );
}
```

`handleClick` is:

```text
Callback
→ passed as event handler

Closure
→ accesses user from the surrounding render
```

One function can be both.

---

# 66. Callback Execution Mental Model

Consider:

```javascript
function calculate(a, b, callback) {
    return callback(a, b);
}

function add(a, b) {
    return a + b;
}

calculate(10, 20, add);
```

Think:

```text
add
│
│ passed as value
▼

calculate(10, 20, add)
│
├── a = 10
├── b = 20
└── callback = add
        │
        ▼
callback(a, b)
        │
        ▼
add(10, 20)
        │
        ▼
30
```

---

# 67. Callback + Closure Mental Model

```javascript
function createProcessor(multiplier) {
    return function process(numbers, callback) {
        numbers.forEach((number) => {
            callback(number * multiplier);
        });
    };
}
```

Usage:

```javascript
const doubleProcessor = createProcessor(2);

doubleProcessor([1, 2, 3], (result) => {
    console.log(result);
});
```

Output:

```text
2
4
6
```

Here:

```text
process
→ closure over multiplier

forEach function
→ callback

result handler
→ callback passed to process
```

Several JavaScript concepts can work together in the same code.

---

# 68. Callback Interview Example 1

What is the output?

```javascript
function execute(callback) {
    console.log("A");

    callback();

    console.log("B");
}

execute(() => {
    console.log("C");
});
```

Answer:

```text
A
C
B
```

The callback executes synchronously between the two logs.

---

# 69. Callback Interview Example 2

What is the output?

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

Answer:

```text
A
C
B
```

The timer callback runs later after the current synchronous execution completes and the runtime schedules the queued task.

---

# 70. Callback Interview Example 3

What happens?

```javascript
function greet() {
    console.log("Hello");
}

function execute(callback) {
    callback();
}

execute(greet());
```

Execution first calls:

```javascript
greet()
```

Output:

```text
Hello
```

Since `greet()` has no explicit return value, it returns:

```javascript
undefined
```

So `execute` receives:

```javascript
undefined
```

Then:

```javascript
callback();
```

tries to call `undefined`.

Result:

```text
TypeError
```

Correct:

```javascript
execute(greet);
```

---

# 71. Callback Interview Example 4

What is the output?

```javascript
const numbers = [1, 2, 3];

const result = numbers.map((number) => {
    return number * 10;
});

console.log(result);
```

Answer:

```javascript
[10, 20, 30]
```

`map()` calls the callback for each element and builds a new array from the returned values.

---

# 72. Callback Interview Example 5

What is the output?

```javascript
function execute(value, callback) {
    return callback(value);
}

const result = execute(5, (number) => {
    return number * number;
});

console.log(result);
```

Answer:

```text
25
```

Execution:

```text
value = 5
↓
callback(5)
↓
5 * 5
↓
25
```

---

# 73. Callback Interview Example 6

What is the output?

```javascript
function process(callback) {
    callback(10);
    callback(20);
}

process((value) => {
    console.log(value);
});
```

Answer:

```text
10
20
```

A callback can be invoked multiple times.

---

# 74. Callback Interview Example 7

What is the output?

```javascript
const value = 10;

function execute(callback) {
    const value = 20;

    callback();
}

function showValue() {
    console.log(value);
}

execute(showValue);
```

Answer:

```text
10
```

Why?

`showValue()` was defined in the outer scope where:

```javascript
value = 10
```

Callbacks follow normal lexical scope rules.

Calling `showValue()` inside `execute()` does not make `execute()` its lexical parent.

---

# 75. Callback Interview Example 8

What is the output?

```javascript
function execute() {
    const value = 20;

    const callback = () => {
        console.log(value);
    };

    callback();
}

execute();
```

Answer:

```text
20
```

The callback was created inside `execute()` and closes over its `value`.

---

# 76. Callback Interview Example 9

What is the output?

```javascript
const numbers = [1, 2, 3];

const result = numbers.filter((number) => {
    return number > 1;
});

console.log(result);
```

Answer:

```javascript
[2, 3]
```

The callback determines which values remain.

---

# 77. Callback Interview Example 10

What is the output?

```javascript
function outer(callback) {
    console.log("Start");

    callback();

    console.log("End");
}

outer(() => {
    console.log("Inside");
});
```

Answer:

```text
Start
Inside
End
```

Again, callbacks are not inherently asynchronous.

---

# 78. Callback vs Higher-Order Function

Consider:

```javascript
function greet() {
    console.log("Hello");
}

function execute(callback) {
    callback();
}

execute(greet);
```

Roles:

```text
greet
→ callback

execute
→ higher-order function
```

Why?

```text
greet
→ passed to another function

execute
→ accepts another function
```

A function can sometimes be both depending on how it is used.

---

# 79. Callback vs Event Handler

An event handler is commonly a callback registered for an event.

```javascript
button.addEventListener("click", handleClick);
```

Here:

```javascript
handleClick
```

is:

```text
Event Handler
+
Callback
```

"Event handler" describes its purpose.

"Callback" describes the pattern of passing it to another API for invocation.

---

# 80. Callback vs Promise vs `async/await`

```text
Callback
│
├── Function passed to another function/API
│
├── Can be synchronous
│
└── Can be asynchronous
│
▼
Promise
│
├── Represents eventual completion/failure
│
├── Supports chaining
│
└── Uses callbacks with then/catch/finally
│
▼
async/await
│
├── Syntax built on Promises
│
└── Makes many asynchronous workflows easier to read
```

These are related concepts, but not replacements for understanding callbacks.

---

# 81. Quick Revision

```text
Callback
→ function passed to another function/API
→ receiving code invokes it

Function Reference
→ greet

Function Call
→ greet()

Higher-Order Function
→ accepts or returns functions

Synchronous Callback
→ executes during current operation

Asynchronous Callback
→ executes later

Callbacks appear in:
→ Array methods
→ Timers
→ Events
→ Promises
→ Node.js APIs
→ React
```

---

# 82. Important Rules to Remember

```text
1. Functions are first-class values in JavaScript.

2. Functions can be passed as arguments.

3. A function passed for another function/API to invoke is a callback.

4. A function receiving/returning functions is a higher-order function.

5. greet is a function reference.

6. greet() executes the function.

7. Callbacks are NOT automatically asynchronous.

8. map(), filter(), forEach(), etc. use synchronous callbacks.

9. Timers and event handlers commonly use callbacks that execute later.

10. The receiving API determines when/how a callback is invoked.

11. Callbacks can receive arguments.

12. Callbacks can return values.

13. A callback can execute zero, one, or many times depending on the API.

14. Callbacks follow normal lexical scope rules.

15. Callbacks can form closures.

16. Deep asynchronous callback nesting can create callback hell.

17. Promises help structure asynchronous workflows.

18. async/await is built on Promises.

19. Promises still use callbacks.

20. React relies heavily on callbacks.
```

---

# 83. Scope + Hoisting + Closures + Callbacks

These concepts now connect.

## Scope

```text
Where can an identifier be accessed?
```

## Hoisting

```text
What is a declaration's state before execution
reaches its declaration line?
```

## Closure

```text
How does a function retain access to its
lexical environment?
```

## Callback

```text
How can a function be passed to another
function/API for invocation?
```

Example:

```javascript
function createHandler(message) {
    return function handleClick() {
        console.log(message);
    };
}

const handler = createHandler("Hello");

setTimeout(handler, 1000);
```

Here:

```text
createHandler
│
├── message belongs to its scope
│
└── handleClick closes over message
        │
        ▼
handler
        │
        ▼
passed to setTimeout
        │
        ▼
handler becomes callback
        │
        ▼
runs later
        │
        ▼
still accesses message
```

So:

```text
Scope
+
Closure
+
Callback
```

all work together.

---

# 84. React-Relevant Callback Summary

Callbacks are one of the most important JavaScript concepts for React.

### Event Handler

```jsx
<button onClick={handleClick}>
    Click
</button>
```

```text
handleClick
→ callback
```

---

### Event Handler with Argument

```jsx
<button onClick={() => deleteUser(user.id)}>
    Delete
</button>
```

```text
Arrow function
→ callback

deleteUser(user.id)
→ executed when callback runs
```

---

### Callback Prop

```jsx
<Child onSave={handleSave} />
```

```text
Parent
↓
passes callback
↓
Child
↓
invokes callback
```

---

### List Rendering

```jsx
users.map((user) => (
    <User
        key={user.id}
        user={user}
    />
));
```

```text
(user) => ...
→ map callback
```

---

### State Updater

```jsx
setCount((count) => count + 1);
```

```text
(count) => count + 1
→ updater callback
```

---

### Effect

```jsx
useEffect(() => {
    console.log("Effect");
}, []);
```

The effect function is passed to React for React to invoke according to effect semantics.

Understanding callbacks now will make these React topics significantly easier:

```text
Props
Events
State
useState
useEffect
useCallback
useMemo
Custom Hooks
Async Data Fetching
Form Handling
```
