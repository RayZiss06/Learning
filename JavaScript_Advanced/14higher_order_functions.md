# Higher-Order Functions

A **Higher-Order Function (HOF)** is a function that does at least one of the following:

1. Accepts another function as an argument.
2. Returns another function.
3. Does both.

```text
Higher-Order Function

        Function
           │
           ▼
    ┌─────────────┐
    │     HOF     │
    └─────────────┘
           │
           ▼
        Function
```

In JavaScript, this is possible because **functions are first-class values**.

---

# 1. Basic Definition

A function that accepts another function:

```javascript
function execute(fn) {
    fn();
}
```

`execute()` is a higher-order function because:

```javascript
execute(fn);
```

receives a function.

---

A function that returns another function:

```javascript
function multiplyBy(number) {
    return function (value) {
        return value * number;
    };
}
```

`multiplyBy()` is also a higher-order function because it returns a function.

---

A function can do both:

```javascript
function transform(fn) {
    return function (value) {
        return fn(value);
    };
}
```

It:

```text
Accepts Function
      │
      ▼
     HOF
      │
      ▼
Returns Function
```

---

# 2. Why Higher-Order Functions Exist

Suppose we have:

```javascript
const numbers = [
    1,
    2,
    3,
    4,
    5
];
```

We want to double every number.

We could write:

```javascript
const result = [];

for (const number of numbers) {
    result.push(
        number * 2
    );
}
```

Later we want to triple them:

```javascript
const result = [];

for (const number of numbers) {
    result.push(
        number * 3
    );
}
```

The iteration logic is repeated.

Only this changes:

```text
number * 2
number * 3
```

A higher-order function lets us separate:

```text
HOW to iterate
```

from:

```text
WHAT to do
```

---

# 3. Separating Behavior

We can create:

```javascript
function transformArray(
    array,
    transform
) {
    const result = [];

    for (const value of array) {
        result.push(
            transform(value)
        );
    }

    return result;
}
```

Then:

```javascript
const double =
    value =>
        value * 2;

const triple =
    value =>
        value * 3;
```

Usage:

```javascript
transformArray(
    numbers,
    double
);

transformArray(
    numbers,
    triple
);
```

Now:

```text
Iteration Logic
      │
      ▼
transformArray()


Changing Behavior
      │
      ▼
Callback Function
```

This is the core power of higher-order functions.

---

# 4. First-Class Functions

Higher-order functions work because JavaScript treats functions as **first-class values**.

That means functions can be:

```text
Stored in variables

Passed as arguments

Returned from functions

Stored in arrays

Stored in objects

Assigned to properties
```

Example:

```javascript
function greet() {
    console.log("Hello");
}

const fn = greet;
```

Now:

```javascript
fn();
```

works.

---

# 5. Passing Functions as Arguments

Example:

```javascript
function execute(fn) {
    fn();
}

function greet() {
    console.log("Hello");
}

execute(greet);
```

Notice:

```javascript
execute(greet);
```

not:

```javascript
execute(greet());
```

Why?

Because:

```javascript
greet
```

means:

```text
Pass the function
```

while:

```javascript
greet()
```

means:

```text
Execute the function
and pass its result
```

---

# 6. Higher-Order Function vs Callback

Consider:

```javascript
function execute(fn) {
    fn();
}

function greet() {
    console.log("Hello");
}

execute(greet);
```

Here:

```text
execute
```

is the:

```text
Higher-Order Function
```

and:

```text
greet
```

is the:

```text
Callback
```

So:

```text
HOF
=
Function receiving/returning
another function


Callback
=
Function passed to another
function to be executed
```

They are related but not the same concept.

---

# 7. Returning Functions

Example:

```javascript
function multiplyBy(
    multiplier
) {
    return function (value) {
        return (
            value *
            multiplier
        );
    };
}
```

Usage:

```javascript
const double =
    multiplyBy(2);

const triple =
    multiplyBy(3);
```

Then:

```javascript
double(10);
```

returns:

```text
20
```

and:

```javascript
triple(10);
```

returns:

```text
30
```

`multiplyBy()` is a HOF because it returns functions.

---

# 8. HOF + Closure

Consider:

```javascript
function multiplyBy(
    multiplier
) {
    return function (value) {
        return (
            value *
            multiplier
        );
    };
}
```

The returned function remembers:

```javascript
multiplier
```

through a closure.

```text
multiplyBy(2)
      │
      ▼
multiplier = 2
      │
      ▼
Return Function
      │
      ▼
Closure remembers 2
```

So:

```javascript
const double =
    multiplyBy(2);
```

creates a reusable function.

Higher-order functions and closures often appear together.

---

# 9. Built-In Higher-Order Functions

JavaScript provides many methods that accept functions.

Important examples:

```javascript
map()

filter()

reduce()

forEach()

find()

findIndex()

some()

every()

sort()
```

These are higher-order methods because they accept callback functions.

---

# 10. `map()`

`map()` transforms each element and creates a **new array**.

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const doubled =
    numbers.map(
        number =>
            number * 2
    );
```

Result:

```javascript
[
    2,
    4,
    6,
    8
]
```

Flow:

```text
[1, 2, 3, 4]
      │
      ▼
     map
      │
      ▼
Callback for each item
      │
      ▼
[2, 4, 6, 8]
```

---

# 11. Why `map()` Is a HOF

Because:

```javascript
numbers.map(
    number =>
        number * 2
);
```

`map()` receives:

```javascript
number =>
    number * 2
```

which is a function.

Conceptually:

```javascript
map(callback);
```

Therefore:

```text
map
=
Higher-Order Function


callback
=
Function passed to map
```

---

# 12. `filter()`

`filter()` creates a new array containing elements that satisfy a condition.

```javascript
const numbers = [
    5,
    10,
    15,
    20
];

const result =
    numbers.filter(
        number =>
            number > 10
    );
```

Result:

```javascript
[
    15,
    20
]
```

The callback returns:

```text
true
→ Keep item

false
→ Remove item
```

---

# 13. `reduce()`

`reduce()` combines array values into a single accumulated result.

```javascript
const numbers = [
    10,
    20,
    30
];

const total =
    numbers.reduce(
        (
            accumulator,
            number
        ) =>
            accumulator +
            number,
        0
    );
```

Result:

```text
60
```

Conceptually:

```text
0 + 10 = 10

10 + 20 = 30

30 + 30 = 60
```

---

# 14. `forEach()`

`forEach()` executes a callback for every element.

```javascript
const users = [
    "Alice",
    "Bob",
    "Charlie"
];

users.forEach(
    user => {
        console.log(user);
    }
);
```

Unlike `map()`:

```text
map
→ Creates transformed array


forEach
→ Executes behavior
```

`forEach()` returns `undefined`.

---

# 15. `find()`

`find()` returns the first element satisfying the callback condition.

```javascript
const users = [
    {
        id: 1,
        name: "Alice"
    },
    {
        id: 2,
        name: "Bob"
    }
];

const user =
    users.find(
        user =>
            user.id === 2
    );
```

Result:

```javascript
{
    id: 2,
    name: "Bob"
}
```

---

# 16. `findIndex()`

`findIndex()` returns the index of the first matching element.

```javascript
const numbers = [
    10,
    20,
    30
];

const index =
    numbers.findIndex(
        number =>
            number === 20
    );
```

Result:

```text
1
```

If nothing matches:

```text
-1
```

---

# 17. `some()`

`some()` checks whether **at least one** element satisfies a condition.

```javascript
const numbers = [
    5,
    10,
    20
];

const hasLargeNumber =
    numbers.some(
        number =>
            number > 15
    );
```

Result:

```text
true
```

Think:

```text
Does ANY item match?
```

---

# 18. `every()`

`every()` checks whether **all** elements satisfy a condition.

```javascript
const numbers = [
    10,
    20,
    30
];

const allPositive =
    numbers.every(
        number =>
            number > 0
    );
```

Result:

```text
true
```

Think:

```text
Does EVERY item match?
```

---

# 19. `sort()`

`sort()` can receive a comparison function.

```javascript
const numbers = [
    20,
    5,
    100,
    10
];

numbers.sort(
    (a, b) =>
        a - b
);
```

Result:

```javascript
[
    5,
    10,
    20,
    100
]
```

The comparison callback controls ordering.

Note that `sort()` mutates the array it operates on.

If you need to preserve the original array:

```javascript
const sorted =
    [...numbers].sort(
        (a, b) =>
            a - b
    );
```

or, where supported:

```javascript
const sorted =
    numbers.toSorted(
        (a, b) =>
            a - b
    );
```

---

# 20. HOFs Allow Behavior Injection

Suppose:

```javascript
function process(
    value,
    operation
) {
    return operation(value);
}
```

We can inject different behaviors:

```javascript
const double =
    value =>
        value * 2;

const square =
    value =>
        value ** 2;

const negative =
    value =>
        -value;
```

Usage:

```javascript
process(
    10,
    double
);
```

returns:

```text
20
```

```javascript
process(
    10,
    square
);
```

returns:

```text
100
```

Same infrastructure:

```text
process()
```

Different behavior:

```text
double
square
negative
```

---

# 21. Strategy Pattern Idea

Higher-order functions can implement a simple **strategy pattern**.

Suppose:

```javascript
function calculatePrice(
    price,
    pricingStrategy
) {
    return pricingStrategy(
        price
    );
}
```

Strategies:

```javascript
const regularPrice =
    price =>
        price;

const tenPercentOff =
    price =>
        price * 0.9;

const twentyPercentOff =
    price =>
        price * 0.8;
```

Then:

```javascript
calculatePrice(
    100,
    twentyPercentOff
);
```

returns:

```text
80
```

The algorithm can be changed by supplying another function.

---

# 22. Functions as Configuration

Instead of configuration like:

```javascript
process(
    data,
    "uppercase"
);
```

and then:

```javascript
if (
    mode === "uppercase"
) {
    // ...
}
```

we can pass the actual behavior:

```javascript
process(
    data,
    toUpperCase
);
```

This often reduces conditional logic.

---

# 23. Example — Custom `map()`

We can implement our own map-like HOF:

```javascript
function customMap(
    array,
    callback
) {
    const result = [];

    for (let i = 0;
         i < array.length;
         i++) {

        result.push(
            callback(
                array[i],
                i,
                array
            )
        );
    }

    return result;
}
```

Usage:

```javascript
const numbers = [
    1,
    2,
    3
];

const doubled =
    customMap(
        numbers,
        number =>
            number * 2
    );
```

Result:

```javascript
[
    2,
    4,
    6
]
```

---

# 24. Example — Custom `filter()`

```javascript
function customFilter(
    array,
    predicate
) {
    const result = [];

    for (let i = 0;
         i < array.length;
         i++) {

        if (
            predicate(
                array[i],
                i,
                array
            )
        ) {
            result.push(
                array[i]
            );
        }
    }

    return result;
}
```

Usage:

```javascript
customFilter(
    [5, 10, 15],
    number =>
        number >= 10
);
```

Result:

```javascript
[
    10,
    15
]
```

---

# 25. Predicate Functions

A **predicate** is a function that returns a boolean value.

Example:

```javascript
const isAdult =
    age =>
        age >= 18;
```

It returns:

```text
true
or
false
```

Predicates are commonly used with:

```javascript
filter()

find()

some()

every()
```

---

# 26. Transformation Functions

A transformation function converts one value into another.

```javascript
const double =
    number =>
        number * 2;
```

Commonly used with:

```javascript
map()
```

Flow:

```text
Input
↓
Transform
↓
Output
```

---

# 27. Reducer Functions

A reducer combines:

```text
Previous accumulated result
+
Current value
```

into:

```text
New accumulated result
```

Example:

```javascript
const add =
    (total, value) =>
        total + value;
```

Then:

```javascript
numbers.reduce(
    add,
    0
);
```

---

# 28. Function Factories

A function that creates another function is often called a **function factory**.

```javascript
function createGreeting(
    greeting
) {
    return function (name) {
        return (
            `${greeting}, ${name}`
        );
    };
}
```

Create:

```javascript
const sayHello =
    createGreeting(
        "Hello"
    );

const sayWelcome =
    createGreeting(
        "Welcome"
    );
```

Usage:

```javascript
sayHello("Alice");

sayWelcome("Bob");
```

Function factories are higher-order functions.

---

# 29. Function Decorators

A **function decorator** takes a function and returns an enhanced/wrapped function.

Example:

```javascript
function withLogging(fn) {
    return function (...args) {
        console.log(
            "Calling function"
        );

        const result =
            fn.apply(
                this,
                args
            );

        console.log(
            "Function finished"
        );

        return result;
    };
}
```

Usage:

```javascript
function add(a, b) {
    return a + b;
}

const loggedAdd =
    withLogging(add);
```

Then:

```javascript
loggedAdd(5, 10);
```

---

# 30. Decorator Flow

```text
Original Function
       │
       ▼
Higher-Order Function
       │
       ▼
Wrapped Function
       │
       ▼
Extra Behavior
       +
Original Behavior
```

This pattern can add:

```text
Logging
Timing
Caching
Validation
Authorization
Retries
Error handling
Metrics
```

without rewriting the original function.

---

# 31. Timing Decorator

```javascript
function withTiming(fn) {
    return function (...args) {
        const start =
            performance.now();

        const result =
            fn.apply(
                this,
                args
            );

        const end =
            performance.now();

        console.log(
            `Execution: ${
                end - start
            } ms`
        );

        return result;
    };
}
```

Usage:

```javascript
const timedCalculation =
    withTiming(
        expensiveCalculation
    );
```

Now:

```javascript
timedCalculation();
```

automatically measures execution time.

---

# 32. Validation Wrapper

```javascript
function requirePositive(fn) {
    return function (value) {
        if (value < 0) {
            throw new Error(
                "Value must be positive"
            );
        }

        return fn(value);
    };
}
```

Original:

```javascript
function square(number) {
    return (
        number * number
    );
}
```

Wrap:

```javascript
const safeSquare =
    requirePositive(
        square
    );
```

Now:

```javascript
safeSquare(5);
```

works.

But:

```javascript
safeSquare(-5);
```

throws an error.

---

# 33. Authorization Wrapper

Conceptually:

```javascript
function requireRole(
    role,
    fn
) {
    return function (
        user,
        ...args
    ) {
        if (
            user.role !== role
        ) {
            throw new Error(
                "Unauthorized"
            );
        }

        return fn(
            user,
            ...args
        );
    };
}
```

This separates:

```text
Authorization logic
```

from:

```text
Business logic
```

Higher-order functions can therefore help implement reusable cross-cutting behavior.

---

# 34. Memoization Is a HOF Pattern

Previously we created:

```javascript
function memoize(fn) {
    const cache =
        new Map();

    return function (...args) {
        // ...
    };
}
```

Notice:

```text
memoize
```

accepts:

```text
Function
```

and returns:

```text
Function
```

Therefore memoization utilities are classic higher-order functions.

---

# 35. Debounce Is a HOF Pattern

A typical debounce utility:

```javascript
function debounce(
    fn,
    delay
) {
    let timeout;

    return function (...args) {
        clearTimeout(
            timeout
        );

        timeout =
            setTimeout(
                () =>
                    fn.apply(
                        this,
                        args
                    ),
                delay
            );
    };
}
```

It accepts:

```text
fn
```

and returns a wrapped function.

Therefore:

```text
debounce
=
Higher-Order Function
```

---

# 36. Throttle Is a HOF Pattern

Likewise:

```javascript
function throttle(
    fn,
    delay
) {
    let waiting = false;

    return function (...args) {
        if (waiting) {
            return;
        }

        fn.apply(
            this,
            args
        );

        waiting = true;

        setTimeout(
            () => {
                waiting = false;
            },
            delay
        );
    };
}
```

Again:

```text
Function
↓
throttle
↓
Wrapped Function
```

So throttle utilities are higher-order functions.

---

# 37. Currying Is Built on HOF Behavior

Previously:

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;
```

Each stage returns another function.

Therefore currying relies heavily on higher-order-function behavior.

```text
add(10)
↓
Function

(20)
↓
Function

(30)
↓
Result
```

---

# 38. HOF + Closures + Currying

These concepts connect:

```text
Higher-Order Function
        │
        ▼
Can return Function
        │
        ▼
Returned Function
can form Closure
        │
        ▼
Remember Configuration
        │
        ▼
Currying /
Function Factory /
Decorator
```

They are separate concepts, but frequently used together.

---

# 39. Function Composition

Higher-order functions can combine multiple functions.

Suppose:

```javascript
const double =
    x =>
        x * 2;

const add10 =
    x =>
        x + 10;
```

We want:

```text
Input
↓
double
↓
add10
↓
Output
```

We can create:

```javascript
function compose(
    first,
    second
) {
    return function (value) {
        return second(
            first(value)
        );
    };
}
```

Usage:

```javascript
const calculate =
    compose(
        double,
        add10
    );

calculate(5);
```

Result:

```text
20
```

Because:

```text
5
↓
double
↓
10
↓
add10
↓
20
```

---

# 40. Generic `pipe()`

```javascript
const pipe =
    (...functions) =>
    value =>
        functions.reduce(
            (result, fn) =>
                fn(result),
            value
        );
```

Functions:

```javascript
const double =
    x => x * 2;

const add10 =
    x => x + 10;

const square =
    x => x ** 2;
```

Create:

```javascript
const calculate =
    pipe(
        double,
        add10,
        square
    );
```

Then:

```javascript
calculate(5);
```

Flow:

```text
5
↓
double
↓
10
↓
add10
↓
20
↓
square
↓
400
```

`pipe()` is a higher-order function.

---

# 41. `compose()` vs `pipe()`

Conventionally:

```text
pipe
=
Left → Right
```

Example:

```javascript
pipe(
    double,
    add10,
    square
);
```

Flow:

```text
double
↓
add10
↓
square
```

`compose()` is commonly:

```text
Right → Left
```

Example:

```javascript
compose(
    square,
    add10,
    double
);
```

Flow:

```text
double
↓
add10
↓
square
```

The exact API depends on the implementation/library.

---

# 42. Generic `compose()`

```javascript
const compose =
    (...functions) =>
    value =>
        functions.reduceRight(
            (result, fn) =>
                fn(result),
            value
        );
```

Usage:

```javascript
const calculate =
    compose(
        square,
        add10,
        double
    );
```

Then:

```javascript
calculate(5);
```

becomes:

```text
5
↓
double
↓
10
↓
add10
↓
20
↓
square
↓
400
```

---

# 43. Array Processing Pipeline

Suppose:

```javascript
const users = [
    {
        name: "Alice",
        age: 25,
        active: true
    },
    {
        name: "Bob",
        age: 16,
        active: true
    },
    {
        name: "Charlie",
        age: 30,
        active: false
    }
];
```

We want:

```text
Active Users
↓
Adults
↓
Names
```

Using HOFs:

```javascript
const names =
    users
        .filter(
            user =>
                user.active
        )
        .filter(
            user =>
                user.age >= 18
        )
        .map(
            user =>
                user.name
        );
```

Result:

```javascript
[
    "Alice"
]
```

---

# 44. Extracting Reusable Predicates

Instead of inline callbacks:

```javascript
const isActive =
    user =>
        user.active;

const isAdult =
    user =>
        user.age >= 18;

const getName =
    user =>
        user.name;
```

Then:

```javascript
const names =
    users
        .filter(isActive)
        .filter(isAdult)
        .map(getName);
```

This can improve readability when the functions represent meaningful reusable concepts.

---

# 45. Declarative Programming

Compare:

```javascript
const result = [];

for (let i = 0;
     i < users.length;
     i++) {

    if (
        users[i].active &&
        users[i].age >= 18
    ) {
        result.push(
            users[i].name
        );
    }
}
```

with:

```javascript
const result =
    users
        .filter(isActive)
        .filter(isAdult)
        .map(getName);
```

The second style is more **declarative**.

It emphasizes:

```text
WHAT should happen
```

rather than manually describing:

```text
HOW iteration should happen
```

---

# 46. Imperative vs Declarative

Imperative:

```javascript
const result = [];

for (const number of numbers) {
    if (number > 10) {
        result.push(
            number * 2
        );
    }
}
```

Declarative:

```javascript
const result =
    numbers
        .filter(
            number =>
                number > 10
        )
        .map(
            number =>
                number * 2
        );
```

Higher-order functions are a major tool for declarative JavaScript.

---

# 47. Event Listeners Are HOF-Based APIs

Example:

```javascript
button.addEventListener(
    "click",
    handleClick
);
```

`addEventListener()` accepts a function:

```javascript
handleClick
```

to execute later.

So callback-based APIs are another major place where HOF concepts appear.

---

# 48. Timers

```javascript
setTimeout(
    () => {
        console.log(
            "Finished"
        );
    },
    1000
);
```

`setTimeout()` receives a function.

Likewise:

```javascript
setInterval(
    callback,
    1000
);
```

These APIs use functions as values.

---

# 49. Promise Methods

Promise APIs also use higher-order patterns:

```javascript
fetch("/api/users")
    .then(
        response =>
            response.json()
    )
    .then(
        users =>
            console.log(users)
    )
    .catch(
        error =>
            console.error(error)
    );
```

Methods such as:

```javascript
then()

catch()

finally()
```

receive callback functions.

---

# 50. Async HOF Example

We can create:

```javascript
async function processAsync(
    value,
    operation
) {
    return await operation(
        value
    );
}
```

Usage:

```javascript
async function getUser(id) {
    // ...
}

const user =
    await processAsync(
        10,
        getUser
    );
```

A HOF can work with synchronous or asynchronous functions.

---

# 51. Retry Wrapper

A useful HOF pattern:

```javascript
function withRetry(
    fn,
    maxAttempts
) {
    return async function (
        ...args
    ) {
        let lastError;

        for (
            let attempt = 1;
            attempt <= maxAttempts;
            attempt++
        ) {
            try {
                return await fn.apply(
                    this,
                    args
                );
            } catch (error) {
                lastError =
                    error;
            }
        }

        throw lastError;
    };
}
```

Usage:

```javascript
const fetchWithRetry =
    withRetry(
        fetchData,
        3
    );
```

Now retry behavior is reusable.

---

# 52. Error Handling Wrapper

```javascript
function withErrorHandling(
    fn
) {
    return async function (
        ...args
    ) {
        try {
            return await fn.apply(
                this,
                args
            );
        } catch (error) {
            console.error(
                error
            );

            throw error;
        }
    };
}
```

Then:

```javascript
const safeFetch =
    withErrorHandling(
        fetchData
    );
```

Again:

```text
Original Function
↓
Wrapper HOF
↓
Enhanced Function
```

---

# 53. Combining HOFs

Because decorators return functions, wrappers can be combined.

Suppose:

```javascript
const enhancedFetch =
    withLogging(
        withRetry(
            fetchData,
            3
        )
    );
```

Conceptually:

```text
fetchData
   │
   ▼
withRetry
   │
   ▼
Retrying Function
   │
   ▼
withLogging
   │
   ▼
Logged + Retrying Function
```

This is a powerful pattern.

But excessive nesting can become difficult to read, which is where composition utilities can help.

---

# 54. Middleware

Middleware systems heavily use higher-order functions.

Conceptually:

```javascript
const middleware =
    next =>
    request => {
        console.log(
            "Before"
        );

        const result =
            next(request);

        console.log(
            "After"
        );

        return result;
    };
```

The middleware receives:

```text
next function
```

and returns:

```text
new function
```

Therefore it is a HOF.

---

# 55. Middleware Chain

Conceptually:

```text
Request
  │
  ▼
Logging Middleware
  │
  ▼
Authentication Middleware
  │
  ▼
Validation Middleware
  │
  ▼
Handler
  │
  ▼
Response
```

Each layer can wrap the next function.

This pattern appears in:

```text
Server frameworks

State management

HTTP processing

Logging

Authorization

Error handling
```

---

# 56. Dependency Injection with HOFs

Suppose:

```javascript
function createUserService(
    database
) {
    return {
        getUser(id) {
            return database
                .findUser(id);
        }
    };
}
```

`createUserService()` receives configuration/dependencies and returns behavior.

Another form:

```javascript
const getUser =
    database =>
    id =>
        database.findUser(
            id
        );
```

This combines:

```text
HOF
+
Closure
+
Currying
+
Dependency Injection
```

---

# 57. React Connection

React makes extensive use of functions as values.

Examples include:

```jsx
<button
    onClick={handleClick}
>
    Save
</button>
```

Passing a callback:

```jsx
<UserList
    onSelect={handleSelect}
/>
```

Array rendering:

```jsx
users.map(
    user => (
        <User
            key={user.id}
            user={user}
        />
    )
);
```

Functional state updates:

```javascript
setCount(
    previous =>
        previous + 1
);
```

Understanding higher-order functions makes many React patterns feel natural.

---

# 58. Functional State Update

Later in React you will see:

```javascript
setCount(
    previousCount =>
        previousCount + 1
);
```

Instead of passing a value:

```javascript
setCount(10);
```

we pass a function.

Conceptually React can call:

```javascript
updater(previousState);
```

This is another example of APIs accepting behavior as functions.

---

# 59. HOFs and Immutability

Array HOFs often work well with immutable transformations.

Example:

```javascript
const doubled =
    numbers.map(
        number =>
            number * 2
    );
```

`map()` creates a new array rather than changing the original array.

Similarly:

```javascript
const adults =
    users.filter(
        user =>
            user.age >= 18
    );
```

creates another array.

This style is common in React.

---

# 60. But HOF Does Not Mean Immutable

A higher-order function can still mutate data.

Example:

```javascript
function process(
    object,
    callback
) {
    callback(object);
}
```

Then:

```javascript
process(
    user,
    user => {
        user.name = "Bob";
    }
);
```

The callback mutates the object.

Therefore:

```text
Higher-Order Function
≠
Automatically Immutable
```

---

# 61. HOF Does Not Mean Pure

Example:

```javascript
function execute(fn) {
    fn();
}
```

And:

```javascript
execute(
    () => {
        console.log(
            "Hello"
        );
    }
);
```

This has a side effect.

Therefore:

```text
Higher-Order Function
≠
Pure Function
```

HOF describes function structure, not purity.

---

# 62. HOF Does Not Mean Async

A HOF can be:

```text
Synchronous
or
Asynchronous
```

Example synchronous:

```javascript
numbers.map(
    number =>
        number * 2
);
```

Async-related:

```javascript
promise.then(
    result =>
        process(result)
);
```

Higher-order functions are independent of asynchronous programming.

---

# 63. HOF Does Not Mean Callback Only

A common misconception is:

```text
HOF
=
Function accepting callback
```

That's incomplete.

A function is higher-order if it:

```text
Accepts Function

OR

Returns Function

OR

Both
```

Example:

```javascript
function createFunction() {
    return function () {
        console.log("Hello");
    };
}
```

This is a HOF even though it takes no callback.

---

# 64. HOF and `this`

Wrappers can accidentally lose `this`.

Consider:

```javascript
const user = {
    name: "Alice",

    greet() {
        return this.name;
    }
};
```

A wrapper:

```javascript
function wrapper(fn) {
    return function (...args) {
        return fn(...args);
    };
}
```

may not preserve the desired method context.

A common wrapper pattern is:

```javascript
function wrapper(fn) {
    return function (...args) {
        return fn.apply(
            this,
            args
        );
    };
}
```

This forwards the wrapper's dynamic `this`.

Understanding `this` remains important when designing generic HOF utilities.

---

# 65. Forwarding Arguments

Generic wrappers usually need:

```javascript
...args
```

Example:

```javascript
function withLogging(fn) {
    return function (...args) {
        console.log(
            "Arguments:",
            args
        );

        return fn.apply(
            this,
            args
        );
    };
}
```

This allows wrapping functions with varying numbers of arguments.

---

# 66. Preserving Return Values

Wrong:

```javascript
function withLogging(fn) {
    return function (...args) {
        console.log(
            "Calling"
        );

        fn(...args);
    };
}
```

Suppose:

```javascript
function add(a, b) {
    return a + b;
}
```

The wrapper returns:

```text
undefined
```

because it forgot:

```javascript
return
```

Correct:

```javascript
return fn.apply(
    this,
    args
);
```

Generic wrappers should preserve the original function's behavior unless intentionally changing it.

---

# 67. Preserving Errors

Suppose:

```javascript
function wrapper(fn) {
    return function (...args) {
        try {
            return fn.apply(
                this,
                args
            );
        } catch (error) {
            console.error(error);
        }
    };
}
```

This silently changes behavior because errors no longer propagate.

If that is not intended:

```javascript
catch (error) {
    console.error(error);
    throw error;
}
```

A HOF wrapper must be designed carefully.

---

# 68. Sync vs Async Wrappers

Suppose:

```javascript
function withLogging(fn) {
    return function (...args) {
        console.log("Start");

        const result =
            fn.apply(
                this,
                args
            );

        console.log("End");

        return result;
    };
}
```

If `fn` returns a Promise:

```text
Start
End
Async work finishes later
```

So `"End"` does not mean asynchronous work completed.

For async behavior:

```javascript
function withAsyncLogging(fn) {
    return async function (...args) {
        console.log("Start");

        try {
            return await fn.apply(
                this,
                args
            );
        } finally {
            console.log("End");
        }
    };
}
```

---

# 69. Common Mistake — Calling Instead of Passing

Wrong:

```javascript
setTimeout(
    greet(),
    1000
);
```

This executes `greet()` immediately.

Correct:

```javascript
setTimeout(
    greet,
    1000
);
```

or:

```javascript
setTimeout(
    () => greet(),
    1000
);
```

Remember:

```text
greet
→ Function


greet()
→ Function result
```

---

# 70. Common Mistake — Forgetting Return in `map()`

Wrong:

```javascript
const doubled =
    numbers.map(
        number => {
            number * 2;
        }
    );
```

Result:

```javascript
[
    undefined,
    undefined,
    undefined
]
```

Correct:

```javascript
const doubled =
    numbers.map(
        number => {
            return number * 2;
        }
    );
```

or:

```javascript
const doubled =
    numbers.map(
        number =>
            number * 2
    );
```

---

# 71. Common Mistake — Using `map()` for Side Effects

Example:

```javascript
users.map(
    user => {
        console.log(user);
    }
);
```

If you only want a side effect, `forEach()` communicates the intent better:

```javascript
users.forEach(
    user => {
        console.log(user);
    }
);
```

Use:

```text
map
→ Transform


forEach
→ Perform action
```

---

# 72. Common Mistake — Mutating Inside `map()`

Avoid:

```javascript
const result =
    users.map(
        user => {
            user.active = true;

            return user;
        }
    );
```

This mutates the original user objects.

Prefer:

```javascript
const result =
    users.map(
        user => ({
            ...user,
            active: true
        })
    );
```

Now new objects are created.

---

# 73. Common Mistake — Missing Initial Value in `reduce()`

Example:

```javascript
numbers.reduce(
    (total, number) =>
        total + number
);
```

This can work for non-empty arrays, but behavior changes because the first element becomes the initial accumulator.

For totals, often prefer:

```javascript
numbers.reduce(
    (total, number) =>
        total + number,
    0
);
```

It also handles an empty array predictably.

---

# 74. Common Mistake — Overusing HOF Chains

This may be elegant:

```javascript
data
    .filter(...)
    .map(...)
    .filter(...)
    .map(...)
    .reduce(...);
```

But long chains can:

```text
Become difficult to debug

Create intermediate arrays

Hide expensive operations

Reduce readability
```

Higher-order functions are tools, not a requirement.

Sometimes a loop is clearer or more efficient.

---

# 75. Intermediate Arrays

Consider:

```javascript
numbers
    .filter(
        number =>
            number > 10
    )
    .map(
        number =>
            number * 2
    );
```

`filter()` creates an array.

Then `map()` creates another array.

For normal application data, this is often perfectly fine.

For huge performance-sensitive datasets, the extra passes and allocations may matter.

Do not optimize prematurely, but understand the behavior.

---

# 76. Common Mistake — Creating Unnecessary Functions

Example:

```javascript
users.map(
    user =>
        getName(user)
);
```

If `getName` already has the correct callback signature:

```javascript
users.map(
    getName
);
```

can be simpler.

But be careful: array callbacks receive additional arguments such as index and array, so directly passing a function is only correct when its signature/behavior is compatible.

---

# 77. The `parseInt` Trap

A classic example:

```javascript
[
    "10",
    "10",
    "10"
].map(parseInt);
```

Many expect:

```javascript
[
    10,
    10,
    10
]
```

But `map()` passes:

```text
value
index
array
```

while `parseInt()` accepts:

```text
string
radix
```

So the index gets passed as the radix.

Safer:

```javascript
[
    "10",
    "10",
    "10"
].map(
    value =>
        parseInt(
            value,
            10
        )
);
```

or:

```javascript
[
    "10",
    "10",
    "10"
].map(Number);
```

Important lesson:

> When passing functions directly as callbacks, understand the arguments the HOF supplies.

---

# 78. Callback Signatures

For many array methods:

```javascript
array.map(
    (
        value,
        index,
        array
    ) => {
        // ...
    }
);
```

Similarly:

```javascript
filter()
forEach()
find()
some()
every()
```

provide callback arguments.

You do not have to use all of them.

Example:

```javascript
numbers.map(
    number =>
        number * 2
);
```

uses only the first.

---

# 79. Higher-Order Functions and Abstraction

Suppose multiple functions contain:

```text
Validate
↓
Log
↓
Execute
↓
Measure
↓
Handle Error
```

Instead of duplicating those behaviors, HOFs can abstract them into wrappers.

Example:

```javascript
const enhanced =
    withLogging(
        withValidation(
            operation
        )
    );
```

This can separate concerns.

But abstraction should make code easier to maintain, not merely more abstract.

---

# 80. HOF Decision Guide

Consider a HOF when:

```text
Behavior changes but
structure stays the same.

You need reusable wrappers.

You need to create
configured functions.

You need function
composition.

You want to separate
cross-cutting concerns.

You are building callback-
based APIs.
```

Use a normal function when:

```text
There is no function behavior
to pass or return.

The abstraction adds
no practical value.

The HOF makes the code
harder to understand.
```

---

# 81. Interview Question — What Is a Higher-Order Function?

A higher-order function is a function that accepts one or more functions as arguments, returns a function, or both.

---

# 82. Interview Question — Why Can JavaScript Have HOFs?

Because functions are first-class values.

They can be:

```text
Assigned

Passed

Returned

Stored
```

like other values.

---

# 83. Interview Question — HOF vs Callback?

A higher-order function receives or returns functions.

A callback is a function passed into another function for that function/API to invoke.

Example:

```javascript
numbers.map(
    number =>
        number * 2
);
```

Here:

```text
map
→ HOF


number => number * 2
→ Callback
```

---

# 84. Interview Question — Is `map()` a HOF?

Yes.

Because it accepts a callback function.

The callback transforms each array element and `map()` creates a new array from the returned values.

---

# 85. Interview Question — Is `filter()` a HOF?

Yes.

It accepts a predicate function that determines which elements should remain in the new array.

---

# 86. Interview Question — Is `reduce()` a HOF?

Yes.

It accepts a reducer callback that determines how values are accumulated.

---

# 87. Interview Question — Is `setTimeout()` a HOF?

Conceptually yes: it accepts a function to execute later.

```javascript
setTimeout(
    callback,
    delay
);
```

---

# 88. Interview Question — Can a Function Be HOF Without Accepting a Function?

Yes.

If it returns a function.

```javascript
function createMultiplier(
    multiplier
) {
    return value =>
        value * multiplier;
}
```

This is a higher-order function.

---

# 89. Interview Question — Are All Callbacks HOFs?

No.

A callback is the function being passed.

The function receiving the callback is the HOF.

Example:

```javascript
function greet() {}

function execute(fn) {
    fn();
}
```

```text
execute
→ HOF

greet
→ Callback
```

---

# 90. Interview Question — HOF vs Closure?

Higher-order function describes:

```text
A function accepting
or returning functions.
```

Closure describes:

```text
A function retaining access
to its lexical environment.
```

Example:

```javascript
function multiplyBy(a) {
    return function (b) {
        return a * b;
    };
}
```

Here:

```text
multiplyBy
→ Higher-Order Function

Returned function
→ Closure over a
```

---

# 91. Interview Question — HOF vs Currying?

Higher-order functions are the broader concept.

Currying is a technique where:

```javascript
f(a, b, c)
```

becomes:

```javascript
f(a)(b)(c)
```

Currying relies on functions returning functions, so it uses higher-order-function behavior.

---

# 92. Interview Question — HOF vs Pure Function?

They describe different properties.

Higher-order:

```text
Accepts/returns functions.
```

Pure:

```text
Same input → same output
and no side effects.
```

A function can be:

```text
HOF + Pure

HOF + Impure

Normal + Pure

Normal + Impure
```

---

# 93. Interview Question — What Is a Function Decorator?

A function decorator is a HOF that accepts a function and returns another function that adds or modifies behavior.

Example:

```javascript
const logged =
    withLogging(original);
```

Common uses:

```text
Logging
Caching
Timing
Retry
Authorization
Validation
```

---

# 94. Interview Question — What Is Function Composition?

Function composition combines smaller functions so the output of one becomes the input of another.

```text
Input
↓
Function A
↓
Function B
↓
Function C
↓
Output
```

HOFs such as `pipe()` and `compose()` can construct these pipelines.

---

# 95. Interview Question — `map()` vs `forEach()`?

`map()`:

```text
Transforms elements

Returns a new array
```

`forEach()`:

```text
Executes callback for
each element

Returns undefined
```

Use `map()` when you need the transformed array.

Use `forEach()` when the goal is performing an action for each element.

---

# 96. Interview Question — `some()` vs `every()`?

```text
some()
====================

Does at least one match?

ANY


every()
====================

Do all match?

ALL
```

Both return booleans.

---

# 97. Interview Question — Why Pass Functions Instead of Strings?

Instead of:

```javascript
process(
    data,
    "double"
);
```

requiring:

```javascript
switch (mode) {
    // ...
}
```

you can pass:

```javascript
process(
    data,
    double
);
```

This makes behavior extensible without forcing the processing function to know every possible operation.

---

# 98. Interview Question — What Problems Can HOFs Cause?

Potential issues include:

```text
Too much abstraction

Deep wrapper nesting

Difficult stack traces

Lost this context

Forgotten return values

Unexpected callback arguments

Mutation inside callbacks

Extra allocations

Hard-to-follow pipelines
```

Use them where they improve the design.

---

# 99. Output Question

```javascript
function applyOperation(
    value,
    operation
) {
    return operation(value);
}

const double =
    number =>
        number * 2;

console.log(
    applyOperation(
        10,
        double
    )
);
```

Output:

```text
20
```

Because:

```text
applyOperation
↓
Receives double
↓
double(10)
↓
20
```

---

# 100. Output Question

```javascript
function createAdder(
    number
) {
    return function (
        value
    ) {
        return (
            value +
            number
        );
    };
}

const add10 =
    createAdder(10);

console.log(
    add10(5)
);

console.log(
    add10(20)
);
```

Output:

```text
15
30
```

`createAdder()` is a HOF and the returned function forms a closure over `number`.

---

# 101. Output Question

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const result =
    numbers
        .filter(
            number =>
                number % 2 === 0
        )
        .map(
            number =>
                number * 10
        );

console.log(result);
```

Output:

```javascript
[
    20,
    40
]
```

Flow:

```text
[1, 2, 3, 4]

↓ filter even

[2, 4]

↓ multiply by 10

[20, 40]
```

---

# 102. Output Question

```javascript
const values = [
    10,
    20,
    30
];

const result =
    values.reduce(
        (
            total,
            value
        ) =>
            total + value,
        100
    );

console.log(result);
```

Output:

```text
160
```

Flow:

```text
Initial = 100

100 + 10 = 110

110 + 20 = 130

130 + 30 = 160
```

---

# 103. Practical Example

Suppose:

```javascript
const products = [
    {
        name: "Laptop",
        price: 1200,
        active: true
    },
    {
        name: "Mouse",
        price: 50,
        active: true
    },
    {
        name: "Old Keyboard",
        price: 30,
        active: false
    }
];
```

Create:

```javascript
const isActive =
    product =>
        product.active;

const getPrice =
    product =>
        product.price;

const add =
    (total, price) =>
        total + price;
```

Then:

```javascript
const total =
    products
        .filter(isActive)
        .map(getPrice)
        .reduce(
            add,
            0
        );
```

Flow:

```text
Products
↓
Active products
↓
Prices
↓
Sum
↓
$1,250
```

This is a good example of small functions working with higher-order APIs.

---

# 104. Higher-Order Functions Cheat Sheet

```text
HIGHER-ORDER FUNCTION
================================

A function that:

Accepts Function

OR

Returns Function

OR

Both


WHY POSSIBLE?
================================

JavaScript functions are
first-class values.


EXAMPLE
================================

function execute(fn) {
    fn();
}

execute
→ HOF

fn
→ Callback


RETURNS FUNCTION
================================

function multiplyBy(a) {
    return b => a * b;
}


BUILT-IN HOFs
================================

map
filter
reduce
forEach
find
findIndex
some
every
sort


MAP
================================

Transform elements.

Returns new array.


FILTER
================================

Select elements.

Returns new array.


REDUCE
================================

Accumulate values.


FOREACH
================================

Perform action for each item.


FIND
================================

Find first matching item.


SOME
================================

Any item matches?


EVERY
================================

All items match?


FUNCTION FACTORY
================================

HOF returning configured
functions.


DECORATOR
================================

Function
↓
Wrapper
↓
Enhanced Function


COMPOSITION
================================

Small Functions
↓
Combined Behavior


PIPE
================================

Left → Right


COMPOSE
================================

Usually Right → Left


HOF + CLOSURE
================================

Returned functions can
remember configuration.


HOF + CURRYING
================================

Functions return functions
as arguments are supplied.


HOF + CALLBACK
================================

HOF receives callback.

Callback is executed by HOF.


COMMON USES
================================

Array processing
Events
Promises
Timers
Validation
Logging
Retries
Caching
Debouncing
Throttling
Middleware
React
Function composition
Dependency injection
```

---

# 105. Important Rules to Remember

```text
1. A higher-order function accepts or returns functions.

2. It may do both.

3. JavaScript supports HOFs because functions are first-class values.

4. Functions can be stored in variables.

5. Functions can be passed as arguments.

6. Functions can be returned from functions.

7. A callback is not the same as a HOF.

8. The receiver is the HOF.

9. The passed function is the callback.

10. map() is a HOF.

11. filter() is a HOF.

12. reduce() is a HOF.

13. forEach() is a HOF.

14. find() is a HOF.

15. findIndex() is a HOF.

16. some() is a HOF.

17. every() is a HOF.

18. sort() accepts a comparator function.

19. map() transforms elements.

20. map() returns a new array.

21. filter() selects elements.

22. filter() returns a new array.

23. reduce() accumulates values.

24. forEach() is commonly used for actions/side effects.

25. some() checks whether any item matches.

26. every() checks whether all items match.

27. find() returns the first matching item.

28. HOFs can separate behavior from iteration/infrastructure.

29. HOFs enable behavior injection.

30. HOFs can reduce repeated logic.

31. HOFs can create reusable abstractions.

32. Functions returning functions are HOFs.

33. Function factories are HOFs.

34. Function decorators are HOFs.

35. Memoization utilities are HOFs.

36. Debounce utilities are HOFs.

37. Throttle utilities are HOFs.

38. Retry wrappers can be HOFs.

39. Logging wrappers can be HOFs.

40. Validation wrappers can be HOFs.

41. Authorization wrappers can be HOFs.

42. Middleware frequently uses HOFs.

43. Currying relies on HOF behavior.

44. Closures often appear with HOFs.

45. Currying and HOFs are not identical concepts.

46. Closures and HOFs are not identical concepts.

47. Callbacks and HOFs are not identical concepts.

48. HOFs do not automatically make functions pure.

49. HOFs do not automatically make code immutable.

50. HOFs can be synchronous.

51. HOFs can work with asynchronous functions.

52. Promise APIs use callback functions extensively.

53. Event APIs use callback functions extensively.

54. Timer APIs use callback functions.

55. React relies heavily on functions as values.

56. Functional state updates pass functions.

57. HOFs support declarative programming.

58. Declarative code focuses on what should happen.

59. Imperative code describes how it should happen.

60. map/filter chains may create intermediate arrays.

61. Intermediate arrays are usually fine unless performance matters.

62. Do not optimize ordinary array operations prematurely.

63. Long HOF chains can reduce readability.

64. A loop may sometimes be clearer.

65. A loop may sometimes be more efficient.

66. Passing fn is different from calling fn().

67. fn passes the function.

68. fn() passes its returned value.

69. Understand callback signatures.

70. Array callbacks can receive value, index, and array.

71. Directly passing functions can sometimes cause argument mismatches.

72. map(parseInt) is a classic callback-signature trap.

73. Preserve return values when creating wrappers.

74. Preserve this when required.

75. Rest parameters help generic wrappers forward arguments.

76. apply() can forward arguments while preserving this.

77. Async wrappers must understand Promise behavior.

78. A synchronous wrapper may finish before async work completes.

79. Wrappers can intentionally modify behavior.

80. Wrapper behavior should be explicit.

81. HOFs can implement strategy-like patterns.

82. Functions can represent interchangeable behavior.

83. Functions can replace some conditional configuration.

84. Composition combines functions.

85. pipe() commonly executes left to right.

86. compose() commonly executes right to left.

87. HOFs make function composition possible.

88. Small reusable functions can form pipelines.

89. Predicates return booleans.

90. Predicates work naturally with filter/find/some/every.

91. Transformation functions work naturally with map.

92. Reducers work naturally with reduce.

93. Function factories can capture configuration through closures.

94. HOFs are foundational to functional JavaScript.

95. HOFs are also common outside functional programming.

96. HOFs are not automatically better than normal functions.

97. Avoid abstractions that add complexity without benefit.

98. Prefer meaningful callback/function names when logic is reused.

99. Keep callbacks small when possible.

100. Think of HOFs as functions that operate on behavior represented by other functions.
```

---

# Higher-Order Functions in One Sentence

> A higher-order function is a function that accepts functions, returns functions, or both, allowing behavior itself to be passed around, configured, wrapped, composed, and reused.

---

# Final Mental Model

### Accepting a Function

```text
Callback
   │
   ▼
┌─────────────┐
│     HOF     │
└──────┬──────┘
       │
       ▼
Execute Callback
       │
       ▼
Result
```

Example:

```javascript
numbers.map(callback);
```

---

### Returning a Function

```text
Configuration
      │
      ▼
┌─────────────┐
│     HOF     │
└──────┬──────┘
       │
       ▼
Return Function
       │
       ▼
Reusable Behavior
```

Example:

```javascript
const double =
    multiplyBy(2);
```

---

### Accepting + Returning

```text
Original Function
       │
       ▼
┌───────────────┐
│      HOF      │
└───────┬───────┘
        │
        ▼
Wrapped Function
        │
        ▼
Enhanced Behavior
```

Example:

```javascript
const cached =
    memoize(fn);

const debounced =
    debounce(fn, 300);

const logged =
    withLogging(fn);
```

The key idea is:

```text
In JavaScript,

FUNCTIONS
ARE VALUES.

Therefore we can:

Pass behavior
Return behavior
Store behavior
Configure behavior
Wrap behavior
Compose behavior
Reuse behavior
```

That single idea explains:

```text
Callbacks
Higher-Order Functions
Closures
Currying
Memoization
Debouncing
Throttling
Array Methods
Middleware
Function Composition
Many React Patterns
```
