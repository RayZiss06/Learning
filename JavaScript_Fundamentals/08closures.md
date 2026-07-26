#  Closures

A **closure** is created when a function retains access to variables from its **lexical scope**, even when that function executes outside the scope where it was originally created.

Example:

```javascript
function outer() {
    const message = "Hello";

    function inner() {
        console.log(message);
    }

    return inner;
}

const greet = outer();

greet();
```

Output:

```text
Hello
```

Even though `outer()` has already finished executing, `inner()` can still access:

```javascript
message
```

This behavior is called a **closure**.

---

# 1. Closure Definition

A useful definition is:

> A closure is a function together with access to the lexical environment in which that function was created.

Consider:

```javascript
function outer() {
    const value = 10;

    function inner() {
        console.log(value);
    }

    return inner;
}
```

`inner()` was created inside `outer()`.

Therefore, it has access to:

```javascript
value
```

through lexical scope.

When `inner` is returned:

```javascript
const fn = outer();
```

and later executed:

```javascript
fn();
```

it can still access:

```javascript
value
```

Output:

```text
10
```

---

# 2. Lexical Scope Recap

Closures depend on **lexical scope**.

Lexical scope means:

> A function's accessible outer scopes are determined by where the function is defined in the source code.

Example:

```javascript
const globalValue = "Global";

function outer() {
    const outerValue = "Outer";

    function inner() {
        console.log(globalValue);
        console.log(outerValue);
    }

    inner();
}

outer();
```

Output:

```text
Global
Outer
```

`inner()` can access:

```text
inner scope
↓
outer scope
↓
global/module scope
```

This lookup path is the **scope chain**.

Closures build on this behavior.

---

# 3. Basic Closure Example

```javascript
function outer() {
    const name = "John";

    function inner() {
        console.log(name);
    }

    return inner;
}

const greet = outer();

greet();
```

Execution:

```text
outer()
    ↓
name = "John"
    ↓
inner() created
    ↓
inner returned
    ↓
outer() finishes
    ↓
greet references inner
    ↓
greet()
    ↓
inner accesses name
    ↓
"John"
```

The important part is:

```text
outer() has finished

BUT

inner() still has access to outer's lexical environment
```

---

# 4. Why Doesn't the Outer Variable Disappear?

Normally, local variables are only accessible inside their scope.

```javascript
function test() {
    const value = 10;
}

test();

console.log(value); // ReferenceError
```

But consider:

```javascript
function test() {
    const value = 10;

    return function () {
        console.log(value);
    };
}

const fn = test();

fn();
```

Output:

```text
10
```

The returned function still references `value`.

Because that lexical environment is still reachable through the returned function, the necessary data remains available.

Conceptually:

```text
fn
│
▼
returned function
│
▼
lexical environment
│
└── value = 10
```

---

# 5. Closures Are Created Through Lexical Scoping

Closures are not something you manually enable.

Whenever a function is created, it is associated with its surrounding lexical environment.

Example:

```javascript
function outer() {
    const value = 10;

    function inner() {
        return value;
    }

    console.log(inner());
}

outer();
```

`inner()` uses a closure over `value`, even though it executes before `outer()` finishes.

Closures are often most noticeable when an inner function:

- is returned
- is passed somewhere else
- runs later as a callback

because the outer function may already have finished.

---

# 6. Returning Functions

One of the easiest ways to observe closures is by returning a function.

```javascript
function createGreeting() {
    const message = "Hello";

    return function () {
        console.log(message);
    };
}

const greet = createGreeting();

greet();
```

Output:

```text
Hello
```

The returned function remembers access to:

```javascript
message
```

---

# 7. Closure with Parameters

Function parameters are local bindings too, so closures can retain access to them.

```javascript
function createGreeting(name) {
    return function () {
        console.log(`Hello ${name}`);
    };
}

const greetJohn = createGreeting("John");
const greetAlice = createGreeting("Alice");

greetJohn();
greetAlice();
```

Output:

```text
Hello John
Hello Alice
```

Each invocation of:

```javascript
createGreeting()
```

creates a new lexical environment.

Conceptually:

```text
createGreeting("John")
│
└── name = "John"
      ↑
      │
greetJohn


createGreeting("Alice")
│
└── name = "Alice"
      ↑
      │
greetAlice
```

---

# 8. Multiple Closure Instances

Each function invocation can create independent state.

```javascript
function createCounter() {
    let count = 0;

    return function () {
        count++;

        return count;
    };
}

const counter1 = createCounter();
const counter2 = createCounter();
```

Now:

```javascript
console.log(counter1()); // 1
console.log(counter1()); // 2
console.log(counter1()); // 3

console.log(counter2()); // 1
console.log(counter2()); // 2
```

`counter1` and `counter2` do not share the same `count`.

Conceptually:

```text
counter1
   │
   ▼
Environment A
count = 3


counter2
   │
   ▼
Environment B
count = 2
```

Each call to `createCounter()` creates a separate lexical environment.

---

# 9. Closures Preserve Bindings, Not Frozen Values

This is extremely important.

Closures do not take a permanent snapshot of a variable's value when the function is created.

They retain access to the **binding**.

Example:

```javascript
function outer() {
    let value = 10;

    function inner() {
        console.log(value);
    }

    value = 20;

    return inner;
}

const fn = outer();

fn();
```

Output:

```text
20
```

Not:

```text
10
```

Why?

`inner()` retains access to the `value` binding.

That binding was later changed to:

```text
20
```

So when `inner()` executes, it reads the current value of that binding.

Remember:

```text
Closure
→ retains access to bindings

Not
→ frozen snapshot of every value
```

This becomes very important when understanding loops and React stale closures.

---

# 10. Closures Can Modify Outer Variables

A closure can both read and modify variables from its outer lexical environment.

```javascript
function createCounter() {
    let count = 0;

    return function () {
        count++;

        console.log(count);
    };
}

const counter = createCounter();

counter();
counter();
counter();
```

Output:

```text
1
2
3
```

The `count` binding persists between calls because the returned function retains access to it.

---

# 11. Private State with Closures

Closures can be used to create state that cannot be directly accessed from outside.

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

Outside code cannot directly access:

```javascript
count
```

This fails:

```javascript
console.log(count); // ReferenceError
```

But the returned function can access it:

```javascript
console.log(counter()); // 1
console.log(counter()); // 2
```

This creates a form of encapsulated/private state.

---

# 12. Multiple Functions Sharing a Closure

Multiple functions can share the same lexical environment.

```javascript
function createCounter() {
    let count = 0;

    return {
        increment() {
            count++;
        },

        decrement() {
            count--;
        },

        getCount() {
            return count;
        }
    };
}
```

Usage:

```javascript
const counter = createCounter();

counter.increment();
counter.increment();

console.log(counter.getCount()); // 2

counter.decrement();

console.log(counter.getCount()); // 1
```

All three functions access the same:

```javascript
count
```

Conceptually:

```text
             ┌── increment()
             │
count = 1 ◄──┼── decrement()
             │
             └── getCount()
```

But outside code cannot directly access the local `count` binding.

---

# 13. Function Factories

Closures are commonly used to create specialized functions.

Example:

```javascript
function multiplyBy(multiplier) {
    return function (number) {
        return number * multiplier;
    };
}
```

Create specialized functions:

```javascript
const double = multiplyBy(2);
const triple = multiplyBy(3);
```

Usage:

```javascript
console.log(double(5)); // 10
console.log(triple(5)); // 15
```

Conceptually:

```text
multiplyBy(2)
    ↓
multiplier = 2
    ↓
returns function
    ↓
double()
```

And:

```text
multiplyBy(3)
    ↓
multiplier = 3
    ↓
returns function
    ↓
triple()
```

This pattern is called a **function factory**.

---

# 14. Arrow Functions and Closures

Closures work with arrow functions too.

```javascript
function multiplyBy(multiplier) {
    return (number) => number * multiplier;
}

const double = multiplyBy(2);

console.log(double(5)); // 10
```

Or:

```javascript
const multiplyBy = (multiplier) => {
    return (number) => number * multiplier;
};
```

Or more compactly:

```javascript
const multiplyBy = (multiplier) =>
    (number) => number * multiplier;
```

The closure behavior is the same.

---

# 15. Closures with Callbacks

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

After `processUser()` finishes, the callback runs later.

It can still access:

```javascript
username
```

Output after approximately one second:

```text
John
```

Why?

The callback was defined inside `processUser()`.

Therefore it closes over:

```javascript
username
```

---

# 16. Closures with `setTimeout`

Consider:

```javascript
function startTimer() {
    const message = "Timer finished";

    setTimeout(function () {
        console.log(message);
    }, 1000);
}

startTimer();
```

Execution:

```text
startTimer()
    ↓
message created
    ↓
callback created
    ↓
setTimeout receives callback
    ↓
startTimer() finishes
    ↓
later...
    ↓
callback executes
    ↓
message still accessible
```

This is one of the most common practical uses of closures.

---

# 17. Closures with Event Handlers

Closures also appear in event handlers.

```javascript
function setupButton() {
    const message = "Button clicked";

    const button = document.querySelector("#myButton");

    button.addEventListener("click", () => {
        console.log(message);
    });
}

setupButton();
```

`setupButton()` finishes.

Later, the user clicks the button.

The callback can still access:

```javascript
message
```

because it forms a closure.

---

# 18. Closures with Event Handler Factories

Closures can create event handlers containing specific data.

```javascript
function createClickHandler(message) {
    return function () {
        console.log(message);
    };
}

const button = document.querySelector("#myButton");

button.addEventListener(
    "click",
    createClickHandler("Hello")
);
```

`createClickHandler("Hello")` returns a function that closes over:

```javascript
message = "Hello"
```

When the button is clicked later:

```text
Hello
```

is printed.

---

# 19. Closures in Loops

Closures and loops are an important combination.

Consider:

```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000);
}
```

Output:

```text
0
1
2
```

Why?

With `let` in a `for` loop, JavaScript provides a separate binding for `i` for each iteration when needed by closures.

Conceptually:

```text
Iteration 1
i = 0
callback → binding for 0

Iteration 2
i = 1
callback → binding for 1

Iteration 3
i = 2
callback → binding for 2
```

So each callback closes over a different iteration binding.

---

# 20. The Classic `var` Closure Problem

Now consider:

```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000);
}
```

Output:

```text
3
3
3
```

Why?

`var` is function-scoped.

There is only one shared `i` binding.

Conceptually:

```text
Single i binding

i = 0
↓
callback 1 closes over i

i = 1
↓
callback 2 closes over same i

i = 2
↓
callback 3 closes over same i

loop finishes
↓
i = 3

callbacks execute
↓
all read same i
↓
3
3
3
```

The callbacks do not store:

```text
0
1
2
```

as frozen values.

They all access the same binding.

---

# 21. Why `let` Fixes the Loop Problem

```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 1000);
}
```

Output:

```text
0
1
2
```

Because `let` creates per-iteration bindings for this loop behavior.

Conceptually:

```text
Callback 1
↓
i binding = 0

Callback 2
↓
i binding = 1

Callback 3
↓
i binding = 2
```

This is one reason `let` is preferred for loop counters.

---

# 22. Old Solution Using an IIFE

Before `let` became available, an IIFE was commonly used to create a new function scope for every iteration.

```javascript
for (var i = 0; i < 3; i++) {
    (function (value) {
        setTimeout(() => {
            console.log(value);
        }, 1000);
    })(i);
}
```

Output:

```text
0
1
2
```

Each IIFE invocation receives the current `i` as:

```javascript
value
```

and creates a separate lexical environment.

Modern JavaScript usually just uses:

```javascript
let
```

for this case.

---

# 23. Closures with Objects

Closures can retain access to objects too.

```javascript
function createUser() {
    const user = {
        name: "John",
        age: 25
    };

    return function () {
        return user;
    };
}

const getUser = createUser();

console.log(getUser());
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

The closure retains access to the `user` binding.

---

# 24. Closure over an Object Binding vs Object Mutation

Consider:

```javascript
function createUser() {
    const user = {
        name: "John"
    };

    return function () {
        console.log(user.name);
    };
}

const showUser = createUser();

showUser();
```

Output:

```text
John
```

If the object is mutated before the closure runs:

```javascript
function createUser() {
    const user = {
        name: "John"
    };

    const showUser = () => {
        console.log(user.name);
    };

    user.name = "Alice";

    return showUser;
}

const showUser = createUser();

showUser();
```

Output:

```text
Alice
```

The closure accesses the object through the captured binding, and the object itself has been mutated.

---

# 25. Closure State Is Not Global State

Consider:

```javascript
function createCounter() {
    let count = 0;

    return () => ++count;
}
```

Create two counters:

```javascript
const counterA = createCounter();
const counterB = createCounter();
```

Then:

```javascript
console.log(counterA()); // 1
console.log(counterA()); // 2

console.log(counterB()); // 1
```

Each counter has independent state.

Compare this with:

```javascript
let count = 0;

function increment() {
    return ++count;
}
```

Now all calls share one outer/global `count`.

Closures allow state to be associated with specific function instances rather than exposing one shared global variable.

---

# 26. Closures and Encapsulation

Consider:

```javascript
function createBankAccount(initialBalance) {
    let balance = initialBalance;

    return {
        deposit(amount) {
            balance += amount;
        },

        withdraw(amount) {
            if (amount > balance) {
                return false;
            }

            balance -= amount;

            return true;
        },

        getBalance() {
            return balance;
        }
    };
}
```

Usage:

```javascript
const account = createBankAccount(100);

account.deposit(50);

console.log(account.getBalance()); // 150

account.withdraw(30);

console.log(account.getBalance()); // 120
```

Outside code cannot directly access the local binding:

```javascript
balance
```

It must interact through:

```text
deposit()
withdraw()
getBalance()
```

This demonstrates encapsulation using closures.

---

# 27. Closures and Higher-Order Functions

A higher-order function can return a closure.

```javascript
function greaterThan(limit) {
    return function (number) {
        return number > limit;
    };
}

const greaterThan10 = greaterThan(10);
```

Now:

```javascript
console.log(greaterThan10(5));  // false
console.log(greaterThan10(20)); // true
```

`greaterThan10` closes over:

```javascript
limit = 10
```

---

# 28. Closures with Array Methods

Closures frequently appear in:

```javascript
map()
filter()
reduce()
forEach()
```

Example:

```javascript
function filterGreaterThan(limit, numbers) {
    return numbers.filter((number) => {
        return number > limit;
    });
}
```

The callback:

```javascript
(number) => {
    return number > limit;
}
```

has its own parameter:

```javascript
number
```

and accesses:

```javascript
limit
```

from the outer function.

That callback forms a closure over `limit`.

Usage:

```javascript
const numbers = [5, 10, 15, 20];

console.log(filterGreaterThan(10, numbers));
```

Output:

```javascript
[15, 20]
```

---

# 29. Closures with Multiple Outer Variables

A closure can access multiple variables from surrounding scopes.

```javascript
function createUser(name) {
    const role = "User";

    return function greet(message) {
        console.log(`${message} ${name} - ${role}`);
    };
}

const greetJohn = createUser("John");

greetJohn("Hello");
```

Output:

```text
Hello John - User
```

The inner function has:

```text
message
→ its own parameter

name
→ outer function parameter

role
→ outer function variable
```

---

# 30. Nested Closures

Closures can exist across multiple nested scopes.

```javascript
function outer(a) {
    return function middle(b) {
        return function inner(c) {
            return a + b + c;
        };
    };
}
```

Usage:

```javascript
const step1 = outer(10);
const step2 = step1(20);
const result = step2(30);

console.log(result); // 60
```

Inside `inner()`:

```text
c → inner scope

b → middle scope

a → outer scope
```

Through the lexical scope chain:

```text
inner
↓
middle
↓
outer
```

---

# 31. Currying Introduction

The previous pattern resembles a technique called **currying**.

Instead of:

```javascript
function add(a, b, c) {
    return a + b + c;
}

add(10, 20, 30);
```

we can structure functions as:

```javascript
const add = (a) => (b) => (c) => a + b + c;
```

Usage:

```javascript
add(10)(20)(30);
```

Result:

```text
60
```

Each function closes over values from previous function calls.

Currying is a broader functional-programming concept, but closures make it possible.

---

# 32. Closures and Memory

Closures can keep data reachable as long as the closure itself remains reachable.

Example:

```javascript
function createHandler() {
    const largeData = new Array(1000000).fill("data");

    return function () {
        console.log(largeData.length);
    };
}

const handler = createHandler();
```

Because `handler` references the lexical environment containing `largeData`, that data may remain reachable while the closure is reachable.

This is normal and useful when the data is needed.

But unnecessarily retaining references can increase memory usage.

---

# 33. Closures Do Not Automatically Cause Memory Leaks

Closures themselves are not memory leaks.

They are a fundamental JavaScript feature.

A problem can occur when:

```text
A closure remains reachable
+
it retains data that is no longer needed
```

For example, a long-lived event listener might keep references to data.

```javascript
function setup() {
    const data = createLargeData();

    function handleClick() {
        console.log(data);
    }

    button.addEventListener("click", handleClick);
}
```

If the listener remains registered for a long time, `handleClick` remains reachable and may keep its required lexical data reachable too.

When appropriate, event listeners can be removed:

```javascript
button.removeEventListener("click", handleClick);
```

Memory management is handled by JavaScript's garbage collector, based largely on reachability.

---

# 34. Closures and Garbage Collection

Consider:

```javascript
function outer() {
    const a = 10;
    const b = 20;

    return function () {
        console.log(a);
    };
}
```

The returned function needs:

```javascript
a
```

The JavaScript engine is responsible for managing the underlying lexical environment and memory.

You should think in terms of:

```text
reachable data
vs
unreachable data
```

rather than assuming every variable from every outer scope must remain permanently in memory.

---

# 35. Common Misconception: Closure Means Returning a Function

Incorrect:

> A closure is when a function returns another function.

Returning a function is a common way to **observe and use** closures, but it is not the definition.

Example:

```javascript
function outer() {
    const value = 10;

    function inner() {
        console.log(value);
    }

    inner();
}

outer();
```

`inner()` still closes over `value`.

Nothing was returned.

Closures arise from lexical scoping, not specifically from `return`.

---

# 36. Common Misconception: Closure Copies Values

Incorrect:

> The closure copies the value when it is created.

Consider:

```javascript
function outer() {
    let value = 10;

    const inner = () => {
        console.log(value);
    };

    value = 20;

    return inner;
}

const fn = outer();

fn();
```

Output:

```text
20
```

The closure accesses the binding, not a frozen copy of `10`.

---

# 37. Common Misconception: Outer Function Must Finish First

A closure exists because of lexical scope regardless of whether the outer function has finished.

Example:

```javascript
function outer() {
    const value = 10;

    function inner() {
        console.log(value);
    }

    inner();
}

outer();
```

`inner()` has access to `value` through its closure even while `outer()` is still executing.

The "outer function has finished" examples are useful because they demonstrate that access can survive beyond the outer call.

---

# 38. Common Misconception: Closures Only Work One Level Up

A function can access variables through the entire lexical scope chain.

```javascript
const a = 10;

function first() {
    const b = 20;

    function second() {
        const c = 30;

        function third() {
            console.log(a + b + c);
        }

        return third;
    }

    return second();
}

const fn = first();

fn();
```

Output:

```text
60
```

`third()` can access:

```text
c
↓
b
↓
a
```

through its lexical environment chain.

---

# 39. Common Misconception: Closure Variables Are Public

Consider:

```javascript
function createCounter() {
    let count = 0;

    return () => ++count;
}

const counter = createCounter();
```

You cannot access:

```javascript
counter.count
```

and expect to get the local `count`.

The variable belongs to the lexical environment, not automatically to the function object's public properties.

```javascript
console.log(counter.count); // undefined
```

But:

```javascript
console.log(counter()); // 1
```

The function itself can access `count`.

---

# 40. Closure vs Scope

These concepts are related but different.

## Scope

Determines:

```text
Where can an identifier be accessed?
```

Example:

```javascript
function outer() {
    const value = 10;

    function inner() {
        console.log(value);
    }
}
```

Lexical scope tells us that `inner()` can access `value`.

---

## Closure

Describes the function retaining access to its lexical environment.

```javascript
function outer() {
    const value = 10;

    return function inner() {
        console.log(value);
    };
}

const fn = outer();

fn();
```

Even after the `outer()` call finishes, `fn` can still access the relevant binding.

So:

```text
Lexical Scope
→ determines what a function can access

Closure
→ function retains access to that lexical environment
```

---

# 41. Closure vs Global Variable

Without closure:

```javascript
let count = 0;

function increment() {
    count++;

    return count;
}
```

`count` is shared outer/global state.

Any code with access to the binding could modify it.

With closure:

```javascript
function createCounter() {
    let count = 0;

    return () => ++count;
}

const counter = createCounter();
```

`count` is encapsulated within that counter's lexical environment.

This allows independent instances:

```javascript
const counter1 = createCounter();
const counter2 = createCounter();
```

---

# 42. Closure vs Object Property

State can also be stored in objects:

```javascript
const counter = {
    count: 0,

    increment() {
        this.count++;

        return this.count;
    }
};
```

Here:

```javascript
count
```

is an object property.

With a closure:

```javascript
function createCounter() {
    let count = 0;

    return {
        increment() {
            return ++count;
        }
    };
}
```

`count` is a lexical variable rather than a public object property.

Both approaches are useful.

Closures are one mechanism for encapsulating state.

---

# 43. Practical Use Cases of Closures

Closures are commonly used for:

```text
Private / encapsulated state
Function factories
Callbacks
Event handlers
Timers
Higher-order functions
Partial application
Currying
Module patterns
Configuration
Memoization
React event handlers
React hooks
```

---

# 44. Configuration with Closures

Suppose multiple functions need the same configuration.

```javascript
function createLogger(prefix) {
    return function (message) {
        console.log(`[${prefix}] ${message}`);
    };
}
```

Create specialized loggers:

```javascript
const errorLogger = createLogger("ERROR");
const infoLogger = createLogger("INFO");
```

Usage:

```javascript
errorLogger("Something failed");
infoLogger("Application started");
```

Output:

```text
[ERROR] Something failed
[INFO] Application started
```

Each logger remembers its own:

```javascript
prefix
```

---

# 45. Partial Application with Closures

Closures can preconfigure some arguments.

```javascript
function multiply(a, b) {
    return a * b;
}
```

Instead, create:

```javascript
function createMultiplier(a) {
    return function (b) {
        return a * b;
    };
}
```

Then:

```javascript
const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(10)); // 20
console.log(triple(10)); // 30
```

The first argument is remembered by the closure.

---

# 46. Memoization Introduction

Closures can maintain a cache between function calls.

```javascript
function createSquareCalculator() {
    const cache = {};

    return function (number) {
        if (number in cache) {
            return cache[number];
        }

        const result = number * number;

        cache[number] = result;

        return result;
    };
}
```

Usage:

```javascript
const square = createSquareCalculator();

console.log(square(5)); // 25
console.log(square(5)); // 25
```

The `cache` remains accessible to the returned function between calls.

This technique is called **memoization**.

Real memoization implementations may need more careful cache-key and memory-management strategies, but closures provide the underlying ability to retain the cache.

---

# 47. Module Pattern with Closures

Before ES modules became standard, closures were commonly used to create module-like structures.

```javascript
const counter = (() => {
    let count = 0;

    function increment() {
        count++;
    }

    function getCount() {
        return count;
    }

    return {
        increment,
        getCount
    };
})();
```

Usage:

```javascript
counter.increment();
counter.increment();

console.log(counter.getCount()); // 2
```

But:

```javascript
counter.count
```

does not expose the local variable.

Modern applications generally use ES modules, but this pattern is useful for understanding how closures can provide encapsulation.

---

# 48. React and Closures

Closures are extremely important in React because React applications use functions heavily.

Consider:

```jsx
function UserProfile() {
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

because it was created inside that component function execution.

The event handler forms a closure over the variables it references from that render's lexical environment.

---

# 49. React Event Handler Closure

Consider:

```jsx
function User({ user }) {
    const handleClick = () => {
        console.log(user.id);
    };

    return (
        <button onClick={handleClick}>
            View User
        </button>
    );
}
```

`handleClick` closes over:

```javascript
user
```

from the component render where that handler was created.

When the user clicks later, the handler can access that `user`.

---

# 50. Closures Inside `map()` in React

Consider:

```jsx
function UserList({ users }) {
    return (
        <ul>
            {users.map((user) => (
                <li key={user.id}>
                    <button
                        onClick={() => {
                            console.log(user.id);
                        }}
                    >
                        View
                    </button>
                </li>
            ))}
        </ul>
    );
}
```

Each callback created during `map()` has access to that callback invocation's:

```javascript
user
```

Therefore clicking a button can use the corresponding user's ID.

Conceptually:

```text
User A
↓
handler closes over User A

User B
↓
handler closes over User B

User C
↓
handler closes over User C
```

---

# 51. React State and Closures

Consider:

```jsx
function Counter() {
    const [count, setCount] = useState(0);

    function handleClick() {
        console.log(count);
    }

    return (
        <button onClick={handleClick}>
            {count}
        </button>
    );
}
```

`handleClick` is created during a render and has access to the `count` value from that render's lexical environment.

When React renders the component again, a new execution occurs and new functions may be created that close over the values from that newer render.

A useful mental model is:

```text
Render 1
count = 0
handler 1 → sees count from Render 1

Render 2
count = 1
handler 2 → sees count from Render 2

Render 3
count = 2
handler 3 → sees count from Render 3
```

This concept becomes very important with asynchronous callbacks and hooks.

---

# 52. Stale Closures in React

Consider:

```jsx
function Counter() {
    const [count, setCount] = useState(0);

    function handleClick() {
        setTimeout(() => {
            console.log(count);
        }, 3000);
    }

    return (
        <button onClick={handleClick}>
            Count: {count}
        </button>
    );
}
```

Suppose `handleClick` is invoked during a render where:

```text
count = 2
```

The timeout callback created by that handler closes over the `count` binding/value associated with that render.

If React later renders the component with:

```text
count = 5
```

before the timer executes, that previously created callback can still log:

```text
2
```

because it belongs to the earlier render's lexical environment.

This is often described as a **stale closure** when the programmer expected the callback to observe newer state.

---

# 53. Why React Stale Closures Happen

A React function component executes again when React renders it.

Conceptually:

```text
Render 1

Counter()
count = 0

creates:
handler A
```

Later:

```text
Render 2

Counter()
count = 1

creates:
handler B
```

These are separate function executions with separate lexical environments.

So:

```text
handler A → Render 1 values

handler B → Render 2 values
```

Closures do not automatically switch their lexical environment to the newest render.

This is ordinary JavaScript closure behavior, not something unique to JavaScript syntax inside React.

---

# 54. Closures with `setState`

Suppose we want to update state based on previous state.

A potentially problematic pattern when multiple queued updates depend on the same captured value is:

```jsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

All three expressions may use the same `count` value from the current render.

For updates based on previous state, React provides functional updater syntax:

```jsx
setCount((previousCount) => previousCount + 1);
setCount((previousCount) => previousCount + 1);
setCount((previousCount) => previousCount + 1);
```

Here React supplies the appropriate previous state to each updater.

This is especially important when state updates depend on earlier state.

---

# 55. Closures and `useEffect`

Closures are also important with effects.

Example:

```jsx
useEffect(() => {
    console.log(count);
}, [count]);
```

The effect callback is created during a render and closes over values from that render.

Dependencies tell React when the effect should be scheduled again with a callback from a newer render.

This is one reason dependency arrays matter.

We will cover the exact rules properly when studying React Hooks.

---

# 56. Closure Does Not Mean "Remember the Latest Value"

This is especially important for React.

A closure does not mean:

```text
Always fetch the newest variable from any future function execution.
```

It means:

```text
Retain access to the lexical environment
where this function was created.
```

Normal JavaScript:

```javascript
function createFunction(value) {
    return () => console.log(value);
}

const first = createFunction(10);
const second = createFunction(20);

first();  // 10
second(); // 20
```

`first` does not suddenly start seeing:

```text
20
```

because another invocation happened later.

Each invocation has its own environment.

---

# 57. Closure Execution Mental Model

Given:

```javascript
function outer() {
    let count = 0;

    return function () {
        count++;

        return count;
    };
}

const counter = outer();
```

Think:

```text
outer()
│
├── count = 0
│
└── creates function
        │
        └── references count

function returned
        │
        ▼
counter
        │
        ▼
closure keeps relevant environment reachable
        │
        ▼
count = 0
```

Then:

```javascript
counter();
```

changes:

```text
count = 1
```

Then:

```javascript
counter();
```

changes:

```text
count = 2
```

The same lexical binding is being accessed across those calls.

---

# 58. Closure Interview Example 1

What is the output?

```javascript
function outer() {
    let value = 10;

    return function () {
        console.log(value);
    };
}

const fn = outer();

fn();
```

Answer:

```text
10
```

The returned function closes over `value`.

---

# 59. Closure Interview Example 2

What is the output?

```javascript
function outer() {
    let value = 10;

    const inner = () => {
        console.log(value);
    };

    value = 20;

    return inner;
}

const fn = outer();

fn();
```

Answer:

```text
20
```

The closure retains access to the binding, not a frozen copy of `10`.

---

# 60. Closure Interview Example 3

What is the output?

```javascript
function createCounter() {
    let count = 0;

    return () => ++count;
}

const a = createCounter();
const b = createCounter();

console.log(a());
console.log(a());
console.log(b());
console.log(a());
console.log(b());
```

Answer:

```text
1
2
1
3
2
```

Why?

```text
a → its own count

b → its own count
```

---

# 61. Closure Interview Example 4

What is the output?

```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 0);
}
```

Output:

```text
3
3
3
```

All callbacks share the same function-scoped `i` binding.

When the callbacks run, the loop has completed and:

```text
i = 3
```

---

# 62. Closure Interview Example 5

What is the output?

```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i);
    }, 0);
}
```

Output:

```text
0
1
2
```

Each iteration has its own relevant `i` binding.

---

# 63. Closure Interview Example 6

What is the output?

```javascript
function outer(x) {
    return function (y) {
        return x + y;
    };
}

const add10 = outer(10);

console.log(add10(5));
```

Answer:

```text
15
```

The returned function closes over:

```javascript
x = 10
```

Then receives:

```javascript
y = 5
```

Result:

```text
10 + 5 = 15
```

---

# 64. Closure Interview Example 7

What is the output?

```javascript
let value = 10;

function createFunction() {
    return () => {
        console.log(value);
    };
}

const fn = createFunction();

value = 20;

fn();
```

Output:

```text
20
```

Why?

The closure accesses the outer `value` binding.

That same binding was changed to `20`.

Compare this with:

```javascript
function createFunction(value) {
    return () => {
        console.log(value);
    };
}

const fn = createFunction(10);

fn();
```

Here the closure references the parameter binding created for that invocation.

---

# 65. Closure Interview Example 8

What is the output?

```javascript
function outer() {
    let count = 0;

    return {
        increment() {
            count++;
        },

        getCount() {
            return count;
        }
    };
}

const counter = outer();

counter.increment();
counter.increment();

console.log(counter.getCount());
```

Answer:

```text
2
```

Both methods share the same lexical environment containing `count`.

---

# 66. Closures and `var`, `let`, `const`

Closures can capture bindings declared using any of:

```javascript
var
let
const
```

Example:

```javascript
function outer() {
    var a = 10;
    let b = 20;
    const c = 30;

    return function () {
        console.log(a, b, c);
    };
}

const fn = outer();

fn();
```

Output:

```text
10 20 30
```

The important differences between `var`, `let`, and `const` concern things such as:

```text
Scope
Hoisting
TDZ
Reassignment
Per-iteration loop bindings
```

Closure itself is based on lexical access.

---

# 67. Quick Revision

```text
Closure
→ function + access to its lexical environment

Lexical Scope
→ determines what outer bindings the function can access

Closure retains access to:
→ variables
→ parameters
→ outer function bindings
→ relevant lexical environments

Closures are commonly visible when:
→ function is returned
→ callback executes later
→ event handler executes later
→ timer executes later
```

---

# 68. Core Closure Example

```javascript
function outer() {
    let value = 10;

    return function inner() {
        return value;
    };
}

const fn = outer();

console.log(fn()); // 10
```

Mental model:

```text
outer()
│
├── value = 10
│
└── inner()
       │
       └── uses value
              │
              ▼
       lexical environment

outer() finishes
       │
       ▼
inner still reachable through fn
       │
       ▼
relevant environment remains reachable
       │
       ▼
value remains accessible
```

---

# 69. Important Rules to Remember

```text
1. JavaScript uses lexical scope.

2. Functions can access bindings from their outer lexical scopes.

3. A closure retains access to the lexical environment where the function was created.

4. Closures do not require returning a function.

5. Returning functions simply makes closure behavior easy to observe.

6. Closures retain access to bindings, not frozen snapshots of values.

7. Different outer-function invocations create separate lexical environments.

8. Multiple inner functions from the same invocation can share the same environment.

9. Closures can provide encapsulated/private state.

10. Callbacks, timers, and event handlers commonly use closures.

11. `var` loop callbacks can share one binding.

12. `let` in a for loop can provide per-iteration bindings.

13. Closures can keep referenced data reachable.

14. Closures themselves are not memory leaks.

15. React event handlers and callbacks rely heavily on closures.
```

---

# 70. Scope + Hoisting + Closure

These three concepts connect directly.

## Scope

```text
Where can the identifier be accessed?
```

## Hoisting

```text
What is the declaration's state before execution
reaches its declaration line?
```

## Closure

```text
How can a function retain access to its lexical
environment when used elsewhere or later?
```

Example:

```javascript
function createCounter() {
    let count = 0;

    return function () {
        return ++count;
    };
}
```

### Scope

```text
count belongs to createCounter()'s lexical environment.
```

### Hoisting

```text
The let binding is established for the scope but remains
in the TDZ until its declaration executes.
```

### Closure

```text
The returned function retains access to count after
createCounter() returns.
```

Understanding these together gives you the foundation for a large part of JavaScript's function behavior.

---

# 71. React-Relevant Closure Summary

React uses closures everywhere.

### Event Handler

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

`handleClick` closes over:

```text
user
```

---

### Array Callback

```jsx
users.map((user) => (
    <User key={user.id} user={user} />
));
```

The callback has access to variables from its surrounding lexical environment.

---

### State

```jsx
function Counter() {
    const [count, setCount] = useState(0);

    const handleClick = () => {
        console.log(count);
    };

    return (
        <button onClick={handleClick}>
            {count}
        </button>
    );
}
```

The handler belongs to a particular render's lexical environment.

---

### Functional State Update

When new state depends on previous state:

```jsx
setCount((previousCount) => previousCount + 1);
```

This avoids relying on a potentially stale captured `count` for that update calculation.

---

### Effects

```jsx
useEffect(() => {
    console.log(count);
}, [count]);
```

Effect callbacks also close over values from the render in which they were created.

Understanding closures will make these React concepts much easier:

```text
Event Handlers
useState
useEffect
useCallback
useMemo
Custom Hooks
Dependency Arrays
Stale Closures
```
