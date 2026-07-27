# Currying

**Currying** is a functional programming technique that transforms a function taking multiple arguments into a sequence of functions that each take one argument.

Normal function:

```javascript
function add(a, b, c) {
    return a + b + c;
}

add(10, 20, 30);
```

Curried function:

```javascript
function add(a) {
    return function (b) {
        return function (c) {
            return a + b + c;
        };
    };
}

add(10)(20)(30);
```

Both produce:

```text
60
```

The difference is **how the arguments are supplied**.

```text
Normal

add(10, 20, 30)


Curried

add(10)(20)(30)
```

---

# 1. Core Idea

A function:

```javascript
f(a, b, c)
```

can conceptually be transformed into:

```javascript
f(a)(b)(c)
```

Instead of receiving all arguments at once:

```text
a, b, c
   │
   ▼
Function
   │
   ▼
Result
```

currying creates a chain:

```text
a
│
▼
Function
│
▼
b
│
▼
Function
│
▼
c
│
▼
Result
```

---

# 2. Basic Currying Example

Normal function:

```javascript
function multiply(a, b) {
    return a * b;
}

console.log(
    multiply(5, 10)
);
```

Output:

```text
50
```

Curried version:

```javascript
function multiply(a) {
    return function (b) {
        return a * b;
    };
}

console.log(
    multiply(5)(10)
);
```

Output:

```text
50
```

---

# 3. What Happens Internally?

Consider:

```javascript
multiply(5)(10);
```

First:

```javascript
multiply(5);
```

returns:

```javascript
function (b) {
    return 5 * b;
}
```

Then:

```javascript
(10)
```

calls that returned function.

So:

```text
multiply(5)(10)

        │
        ▼

multiply(5)

        │
        ▼

function (b) {
    return 5 * b;
}

        │
        ▼

function(10)

        │
        ▼

5 * 10

        │
        ▼

50
```

---

# 4. Currying Uses Closures

Consider:

```javascript
function multiply(a) {
    return function (b) {
        return a * b;
    };
}
```

The inner function uses:

```javascript
a
```

even after the outer function has returned.

This works because of a **closure**.

```text
multiply(5)
     │
     ▼
a = 5
     │
     ▼
Returned Function
     │
     ▼
Remembers a
```

Then:

```javascript
multiply(5)(10);
```

becomes:

```text
Remembered a = 5
b = 10

5 * 10
=
50
```

---

# 5. Storing Intermediate Functions

We do not have to immediately write:

```javascript
multiply(5)(10);
```

We can store the intermediate function.

```javascript
const multiplyBy5 =
    multiply(5);
```

Now:

```javascript
multiplyBy5(10);
```

returns:

```text
50
```

And:

```javascript
multiplyBy5(20);
```

returns:

```text
100
```

And:

```javascript
multiplyBy5(100);
```

returns:

```text
500
```

This is one of the main practical ideas behind currying.

---

# 6. Reusable Specialized Functions

Suppose:

```javascript
function multiply(a) {
    return function (b) {
        return a * b;
    };
}
```

We can create:

```javascript
const double =
    multiply(2);

const triple =
    multiply(3);

const multiplyBy10 =
    multiply(10);
```

Usage:

```javascript
double(5);
```

returns:

```text
10
```

```javascript
triple(5);
```

returns:

```text
15
```

```javascript
multiplyBy10(5);
```

returns:

```text
50
```

The general function:

```text
multiply
```

produces specialized functions:

```text
multiply
   │
   ├── multiply(2)
   │       ↓
   │     double
   │
   ├── multiply(3)
   │       ↓
   │     triple
   │
   └── multiply(10)
           ↓
      multiplyBy10
```

---

# 7. Arrow Function Currying

The previous function:

```javascript
function multiply(a) {
    return function (b) {
        return a * b;
    };
}
```

can be written:

```javascript
const multiply =
    (a) =>
        (b) =>
            a * b;
```

Or more compactly:

```javascript
const multiply =
    a => b => a * b;
```

Usage:

```javascript
multiply(5)(10);
```

Output:

```text
50
```

---

# 8. Three Arguments

Normal:

```javascript
function add(a, b, c) {
    return a + b + c;
}
```

Curried:

```javascript
function add(a) {
    return function (b) {
        return function (c) {
            return a + b + c;
        };
    };
}
```

Arrow version:

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;
```

Usage:

```javascript
add(10)(20)(30);
```

Output:

```text
60
```

---

# 9. Intermediate Results

Consider:

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;
```

We can do:

```javascript
const add10 =
    add(10);
```

Now:

```text
add10
```

represents:

```javascript
b =>
    c =>
        10 + b + c;
```

Then:

```javascript
const add10And20 =
    add10(20);
```

Now it represents:

```javascript
c =>
    10 + 20 + c;
```

Finally:

```javascript
add10And20(30);
```

returns:

```text
60
```

---

# 10. Currying Mental Model

Think:

```text
Give one argument
        │
        ▼
Remember it
        │
        ▼
Return another function
        │
        ▼
Give next argument
        │
        ▼
Remember it
        │
        ▼
Return another function
        │
        ▼
Give final argument
        │
        ▼
Calculate result
```

Closures provide the "remember it" behavior.

---

# 11. Why Use Currying?

Currying can help with:

```text
Reusable specialized functions

Function configuration

Reducing repeated arguments

Function composition

Functional programming

Building reusable pipelines

Creating handlers

Dependency configuration
```

The biggest practical idea is:

```text
Configure once
↓
Reuse many times
```

---

# 12. Example — Tax Calculator

Suppose:

```javascript
function calculateTax(
    taxRate,
    amount
) {
    return (
        amount *
        taxRate
    );
}
```

Usage:

```javascript
calculateTax(
    0.18,
    100
);

calculateTax(
    0.18,
    500
);

calculateTax(
    0.18,
    1000
);
```

We repeatedly pass:

```text
0.18
```

Currying can remove that repetition.

---

# 13. Curried Tax Calculator

```javascript
const calculateTax =
    taxRate =>
    amount =>
        amount * taxRate;
```

Configure once:

```javascript
const tax18 =
    calculateTax(0.18);
```

Then:

```javascript
tax18(100);
tax18(500);
tax18(1000);
```

Conceptually:

```text
calculateTax(0.18)
        │
        ▼
Remember taxRate
        │
        ▼
tax18(amount)
```

---

# 14. Example — Discount Calculator

```javascript
const discount =
    percentage =>
    price =>
        price -
        price * percentage;
```

Create:

```javascript
const tenPercentOff =
    discount(0.10);

const twentyPercentOff =
    discount(0.20);
```

Usage:

```javascript
tenPercentOff(100);
```

returns:

```text
90
```

```javascript
twentyPercentOff(100);
```

returns:

```text
80
```

We created reusable configured functions.

---

# 15. Example — Logger

Suppose:

```javascript
function log(
    level,
    message
) {
    console.log(
        `[${level}] ${message}`
    );
}
```

Usage:

```javascript
log(
    "INFO",
    "Server started"
);

log(
    "INFO",
    "User logged in"
);

log(
    "ERROR",
    "Database failed"
);
```

We repeatedly provide log levels.

---

# 16. Curried Logger

```javascript
const log =
    level =>
    message =>
        console.log(
            `[${level}] ${message}`
        );
```

Create specialized loggers:

```javascript
const info =
    log("INFO");

const error =
    log("ERROR");

const warning =
    log("WARNING");
```

Usage:

```javascript
info(
    "Server started"
);

info(
    "User logged in"
);

error(
    "Database failed"
);
```

Output:

```text
[INFO] Server started
[INFO] User logged in
[ERROR] Database failed
```

---

# 17. Configuration Pattern

Currying is useful when some arguments change rarely while others change frequently.

Suppose:

```javascript
function request(
    baseUrl,
    endpoint
) {
    return (
        baseUrl +
        endpoint
    );
}
```

Instead:

```javascript
const request =
    baseUrl =>
    endpoint =>
        baseUrl +
        endpoint;
```

Configure:

```javascript
const api =
    request(
        "https://api.example.com"
    );
```

Then:

```javascript
api("/users");

api("/products");

api("/orders");
```

The repeated configuration is stored through closure.

---

# 18. More Realistic API Configuration

```javascript
const createApi =
    baseUrl =>
    token =>
    endpoint =>
        fetch(
            `${baseUrl}${endpoint}`,
            {
                headers: {
                    Authorization:
                        `Bearer ${token}`
                }
            }
        );
```

Configure:

```javascript
const api =
    createApi(
        "https://api.example.com"
    );

const authenticatedApi =
    api("abc123");
```

Then:

```javascript
authenticatedApi(
    "/users"
);

authenticatedApi(
    "/products"
);
```

Conceptually:

```text
Base URL
   │
   ▼
Remember
   │
   ▼
Token
   │
   ▼
Remember
   │
   ▼
Endpoint
   │
   ▼
Request
```

---

# 19. Example — Permission Checker

Suppose:

```javascript
function hasPermission(
    role,
    permission
) {
    // ...
}
```

Curried:

```javascript
const hasPermission =
    role =>
    permission => {
        const permissions = {
            admin: [
                "read",
                "write",
                "delete"
            ],

            user: [
                "read"
            ]
        };

        return (
            permissions[
                role
            ]?.includes(
                permission
            ) ?? false
        );
    };
```

Configure:

```javascript
const adminCan =
    hasPermission(
        "admin"
    );

const userCan =
    hasPermission(
        "user"
    );
```

Usage:

```javascript
adminCan("delete");
```

returns:

```text
true
```

```javascript
userCan("delete");
```

returns:

```text
false
```

---

# 20. Example — Filter Functions

Suppose:

```javascript
const products = [
    {
        name: "Laptop",
        category:
            "electronics"
    },
    {
        name: "Mouse",
        category:
            "electronics"
    },
    {
        name: "Chair",
        category:
            "furniture"
    }
];
```

We can create:

```javascript
const byCategory =
    category =>
    product =>
        product.category ===
        category;
```

Then:

```javascript
const electronics =
    products.filter(
        byCategory(
            "electronics"
        )
    );
```

This works because:

```javascript
byCategory(
    "electronics"
);
```

returns a function suitable for `.filter()`.

---

# 21. What `filter()` Receives

This:

```javascript
byCategory(
    "electronics"
);
```

returns:

```javascript
product =>
    product.category ===
    "electronics";
```

So:

```javascript
products.filter(
    byCategory(
        "electronics"
    )
);
```

becomes conceptually:

```javascript
products.filter(
    product =>
        product.category ===
        "electronics"
);
```

Currying helps generate reusable callback functions.

---

# 22. Generic Property Matcher

We can generalize the previous example:

```javascript
const propertyEquals =
    property =>
    expectedValue =>
    object =>
        object[property] ===
        expectedValue;
```

Usage:

```javascript
const categoryEquals =
    propertyEquals(
        "category"
    );
```

Then:

```javascript
const electronics =
    categoryEquals(
        "electronics"
    );
```

Then:

```javascript
products.filter(
    electronics
);
```

---

# 23. Building Functions in Stages

```javascript
const propertyEquals =
    property =>
    expected =>
    object =>
        object[property] ===
        expected;
```

Stage 1:

```javascript
const statusEquals =
    propertyEquals(
        "status"
    );
```

Stage 2:

```javascript
const isActive =
    statusEquals(
        "active"
    );
```

Stage 3:

```javascript
users.filter(
    isActive
);
```

Mental model:

```text
Generic Function
      │
      ▼
Choose Property
      │
      ▼
Choose Value
      │
      ▼
Reusable Predicate
```

---

# 24. Currying vs Partial Application

These concepts are related but **not identical**.

Currying transforms:

```javascript
f(a, b, c)
```

into:

```javascript
f(a)(b)(c)
```

Each function normally receives one argument.

Partial application means fixing **some arguments** of an existing function to create another function.

Example:

```javascript
function multiply(a, b) {
    return a * b;
}
```

Partial application:

```javascript
const double =
    (value) =>
        multiply(
            2,
            value
        );
```

We fixed:

```text
a = 2
```

and created a function waiting for:

```text
b
```

---

# 25. Currying vs Partial Application

```text
CURRYING
================================

Transforms:

f(a, b, c)

into:

f(a)(b)(c)


Focus:
One argument per function.


PARTIAL APPLICATION
================================

Fixes some arguments.

Example:

f(a, b, c)

↓

g(b, c)

where a is already known.
```

Currying naturally enables partial application, but the terms should not be treated as synonyms.

---

# 26. Partial Application Example

Original:

```javascript
function calculate(
    tax,
    discount,
    price
) {
    return (
        price +
        price * tax -
        price * discount
    );
}
```

Partial application:

```javascript
const calculateWithTax =
    (tax) =>
    (discount, price) =>
        calculate(
            tax,
            discount,
            price
        );
```

Notice:

```javascript
(discount, price)
```

takes **two arguments**.

Therefore this is partial application, but not fully curried.

A fully curried form would be:

```javascript
const calculate =
    tax =>
    discount =>
    price =>
        price +
        price * tax -
        price * discount;
```

---

# 27. Currying and Function Composition

Currying becomes useful with function composition.

Suppose:

```javascript
const multiply =
    a =>
    b =>
        a * b;

const add =
    a =>
    b =>
        a + b;
```

Create:

```javascript
const double =
    multiply(2);

const add10 =
    add(10);
```

Now:

```javascript
add10(
    double(5)
);
```

Flow:

```text
5
│
▼
double
│
▼
10
│
▼
add10
│
▼
20
```

Small configured functions can be combined into larger transformations.

---

# 28. Simple `pipe()`

We can create:

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

Then:

```javascript
const double =
    value =>
        value * 2;

const add10 =
    value =>
        value + 10;

const square =
    value =>
        value * value;
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

Currying often makes functions easier to configure for pipelines like this.

---

# 29. Currying and Dependency Injection

Suppose a function needs a database dependency:

```javascript
async function getUser(
    database,
    userId
) {
    return database.findUser(
        userId
    );
}
```

Curried:

```javascript
const getUser =
    database =>
    userId =>
        database.findUser(
            userId
        );
```

Configure once:

```javascript
const getUserFromDatabase =
    getUser(database);
```

Then:

```javascript
getUserFromDatabase(1);

getUserFromDatabase(2);

getUserFromDatabase(3);
```

The dependency is injected once and reused.

---

# 30. Multiple Dependencies

```javascript
const createUserService =
    database =>
    logger =>
    async user => {
        logger(
            "Creating user"
        );

        return database
            .createUser(
                user
            );
    };
```

Configure:

```javascript
const withDatabase =
    createUserService(
        database
    );

const createUser =
    withDatabase(
        logger
    );
```

Then:

```javascript
createUser({
    name: "Alice"
});
```

This demonstrates staged configuration.

---

# 31. Event Handler Factory

Currying-like patterns are useful for creating event handlers.

```javascript
const handleAction =
    action =>
    event => {
        console.log(
            action,
            event.target
        );
    };
```

Usage:

```javascript
saveButton
    .addEventListener(
        "click",
        handleAction(
            "save"
        )
    );
```

`handleAction("save")` returns the actual event handler.

---

# 32. Multiple Event Handlers

```javascript
const handleAction =
    action =>
    event => {
        console.log(
            `${action}:`,
            event.target
        );
    };
```

Create:

```javascript
const handleSave =
    handleAction(
        "SAVE"
    );

const handleDelete =
    handleAction(
        "DELETE"
    );

const handleEdit =
    handleAction(
        "EDIT"
    );
```

Then:

```javascript
saveButton.addEventListener(
    "click",
    handleSave
);

deleteButton.addEventListener(
    "click",
    handleDelete
);
```

---

# 33. React Connection

Later in React, you may encounter:

```jsx
<button
    onClick={
        handleDelete(
            user.id
        )
    }
>
    Delete
</button>
```

where:

```javascript
const handleDelete =
    id =>
    event => {
        console.log(
            id,
            event
        );
    };
```

Here:

```javascript
handleDelete(id)
```

returns an event handler.

The same closure/currying ideas apply.

---

# 34. Currying and Validation

Suppose:

```javascript
const minLength =
    length =>
    value =>
        value.length >=
        length;
```

Create:

```javascript
const minPasswordLength =
    minLength(8);
```

Then:

```javascript
minPasswordLength(
    "password123"
);
```

returns:

```text
true
```

This creates reusable validation rules.

---

# 35. Validation Factory

```javascript
const greaterThan =
    minimum =>
    value =>
        value > minimum;
```

Create:

```javascript
const positive =
    greaterThan(0);

const over18 =
    greaterThan(18);
```

Usage:

```javascript
positive(10);
```

```text
true
```

```javascript
over18(15);
```

```text
false
```

---

# 36. Generic Comparison Functions

```javascript
const greaterThan =
    minimum =>
    value =>
        value > minimum;

const lessThan =
    maximum =>
    value =>
        value < maximum;

const equals =
    expected =>
    value =>
        value === expected;
```

Then:

```javascript
const positive =
    greaterThan(0);

const below100 =
    lessThan(100);

const isAdmin =
    equals("admin");
```

Small reusable predicates are common in functional programming.

---

# 37. Currying and `map()`

```javascript
const multiply =
    factor =>
    value =>
        value * factor;
```

Then:

```javascript
const numbers = [
    1,
    2,
    3,
    4
];
```

Use:

```javascript
numbers.map(
    multiply(2)
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

Because:

```javascript
multiply(2)
```

returns:

```javascript
value =>
    value * 2;
```

which is directly usable as a callback.

---

# 38. Currying and `filter()`

```javascript
const greaterThan =
    minimum =>
    value =>
        value > minimum;
```

Then:

```javascript
const numbers = [
    5,
    10,
    15,
    20
];
```

Use:

```javascript
numbers.filter(
    greaterThan(10)
);
```

Result:

```javascript
[
    15,
    20
]
```

---

# 39. Currying and `reduce()`

Suppose:

```javascript
const addTax =
    taxRate =>
    total =>
    amount =>
        total +
        amount +
        amount * taxRate;
```

Configure:

```javascript
const addTax18 =
    addTax(0.18);
```

Then:

```javascript
const total =
    prices.reduce(
        addTax18,
        0
    );
```

The curried function allows us to configure the tax rate before using the resulting function as the reducer.

---

# 40. Generic Formatter

```javascript
const format =
    prefix =>
    suffix =>
    value =>
        `${prefix}${value}${suffix}`;
```

Configure:

```javascript
const formatCurrency =
    format("$")("");
```

Usage:

```javascript
formatCurrency(100);
```

Output:

```text
$100
```

Another:

```javascript
const percentage =
    format("")("%");
```

Usage:

```javascript
percentage(75);
```

Output:

```text
75%
```

---

# 41. Generic Message Builder

```javascript
const createMessage =
    type =>
    source =>
    message =>
        `[${type}] [${source}] ${message}`;
```

Configure:

```javascript
const apiError =
    createMessage(
        "ERROR"
    )(
        "API"
    );
```

Then:

```javascript
apiError(
    "Request failed"
);
```

Output:

```text
[ERROR] [API] Request failed
```

---

# 42. Automatic Currying

So far we manually wrote:

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;
```

But we can create a function that converts ordinary functions into curried functions.

Original:

```javascript
function add(a, b, c) {
    return a + b + c;
}
```

We want:

```javascript
const curriedAdd =
    curry(add);
```

Then:

```javascript
curriedAdd(1)(2)(3);
```

---

# 43. Basic `curry()` Implementation

```javascript
function curry(fn) {
    return function curried(
        ...args
    ) {
        if (
            args.length >=
            fn.length
        ) {
            return fn.apply(
                this,
                args
            );
        }

        return function (
            ...nextArgs
        ) {
            return curried.apply(
                this,
                [
                    ...args,
                    ...nextArgs
                ]
            );
        };
    };
}
```

Usage:

```javascript
function add(a, b, c) {
    return a + b + c;
}

const curriedAdd =
    curry(add);

console.log(
    curriedAdd(1)(2)(3)
);
```

Output:

```text
6
```

---

# 44. What Is `fn.length`?

Functions have a:

```javascript
function.length
```

property representing the number of parameters before the first one with a default value or rest parameter.

Example:

```javascript
function add(a, b, c) {
    return a + b + c;
}

console.log(
    add.length
);
```

Output:

```text
3
```

Our curry utility uses this as an indication of how many arguments are expected.

---

# 45. How Automatic Currying Works

For:

```javascript
curriedAdd(1)(2)(3);
```

First:

```text
args = [1]

1 < 3

Return function
```

Then:

```text
args = [1, 2]

2 < 3

Return function
```

Then:

```text
args = [1, 2, 3]

3 >= 3

Execute original function
```

Result:

```text
6
```

---

# 46. Flexible Curry Utility

The implementation above can also allow:

```javascript
curriedAdd(
    1,
    2,
    3
);
```

or:

```javascript
curriedAdd(
    1,
    2
)(3);
```

or:

```javascript
curriedAdd(
    1
)(
    2,
    3
);
```

or:

```javascript
curriedAdd(
    1
)(
    2
)(
    3
);
```

All can produce:

```text
6
```

This is more flexible than strict one-argument-at-a-time currying.

---

# 47. Strict Currying

Strict currying conceptually means:

```javascript
f(a)(b)(c)
```

Each function accepts exactly one argument.

A utility that accepts:

```javascript
f(a, b)(c)
```

is commonly called a curry helper in JavaScript libraries, although it supports grouped argument application as well.

Know the conceptual distinction.

---

# 48. Limitations of `fn.length`

Consider:

```javascript
function test(
    a,
    b = 10,
    c
) {}
```

Then:

```javascript
test.length;
```

is:

```text
1
```

because default parameters affect `.length`.

Also:

```javascript
function test(
    ...args
) {}
```

has:

```javascript
test.length;
```

equal to:

```text
0
```

So automatic currying based on `.length` has limitations.

---

# 49. Currying Functions with Optional Arguments

Suppose:

```javascript
function createUser(
    name,
    role = "user"
) {
    // ...
}
```

Automatically determining:

```text
When are all arguments supplied?
```

becomes ambiguous because some arguments are optional.

For these situations, explicit curried functions are often easier to understand.

---

# 50. Currying and Argument Order

Argument order becomes very important.

Suppose:

```javascript
const filterBy =
    value =>
    property =>
    object =>
        object[property] ===
        value;
```

Usage becomes:

```javascript
filterBy(
    "active"
)(
    "status"
)(
    user
);
```

Maybe this is less natural than:

```javascript
const filterBy =
    property =>
    value =>
    object =>
        object[property] ===
        value;
```

Usage:

```javascript
filterBy(
    "status"
)(
    "active"
)(
    user
);
```

Design argument order based on reuse.

---

# 51. Stable Arguments First

A useful guideline is:

> Put arguments that change less frequently earlier.

Suppose:

```text
API Base URL
```

rarely changes, while:

```text
Endpoint
```

changes frequently.

Then:

```javascript
const request =
    baseUrl =>
    endpoint =>
        // ...
```

makes sense.

Configure:

```javascript
const api =
    request(baseUrl);
```

Then repeatedly call:

```javascript
api(endpoint);
```

---

# 52. Argument Order Example

Less useful:

```javascript
const request =
    endpoint =>
    baseUrl =>
        // ...
```

If base URL is constant but endpoint changes constantly, you cannot conveniently configure the base URL once.

Better:

```javascript
const request =
    baseUrl =>
    endpoint =>
        // ...
```

Think:

```text
Configuration arguments
        ↓
Earlier


Changing data
        ↓
Later
```

---

# 53. Currying and Reusability

Without currying:

```javascript
calculateTax(
    0.18,
    price1
);

calculateTax(
    0.18,
    price2
);

calculateTax(
    0.18,
    price3
);
```

With currying:

```javascript
const tax18 =
    calculateTax(0.18);

tax18(price1);

tax18(price2);

tax18(price3);
```

We move from:

```text
Repeat configuration
```

to:

```text
Configure once
Reuse many times
```

---

# 54. Currying and Readability

Currying can improve readability when specialized functions have meaningful names.

Example:

```javascript
const isAdult =
    greaterThanOrEqual(18);

const isExpensive =
    greaterThan(1000);

const isAdmin =
    equals("admin");
```

Then:

```javascript
users.filter(
    isAdult
);
```

can be easier to understand than repeating inline logic everywhere.

---

# 55. Currying Can Also Hurt Readability

Compare:

```javascript
calculate(
    config,
    user,
    order,
    currency,
    options
);
```

with:

```javascript
calculate(config)
    (user)
    (order)
    (currency)
    (options);
```

The second version is not automatically better.

Currying should provide some benefit such as:

```text
Reuse
Configuration
Composition
Function generation
```

Do not curry functions just to make syntax more "functional."

---

# 56. Currying Is Not About Shorter Code

This:

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;
```

is shorter than:

```javascript
function add(a, b, c) {
    return a + b + c;
}
```

But shortening syntax is **not the purpose**.

The purpose is changing the function's structure so intermediate functions can be reused.

---

# 57. Currying and Function Factories

A function factory creates functions.

Example:

```javascript
const createMultiplier =
    multiplier =>
    value =>
        value * multiplier;
```

Then:

```javascript
const double =
    createMultiplier(2);
```

This pattern is also naturally curried.

Currying and function factories frequently overlap.

---

# 58. Currying and Closures vs Currying

A closure is a language behavior:

```text
Function remembers
its lexical environment.
```

Currying is a function transformation/design technique:

```text
f(a, b)
↓
f(a)(b)
```

Currying commonly **uses closures**, but:

```text
Closure
≠
Currying
```

Not every closure is currying.

---

# 59. Example of Closure Without Currying

```javascript
function counter() {
    let count = 0;

    return function () {
        return ++count;
    };
}
```

This uses a closure.

But it is not currying because we are not transforming:

```text
f(a, b, c)
```

into:

```text
f(a)(b)(c)
```

---

# 60. Higher-Order Functions vs Currying

Higher-order function:

```text
Takes a function
and/or
returns a function.
```

Curried functions return functions until enough arguments have been supplied.

Therefore curried functions involve higher-order behavior.

But not every higher-order function is curried.

Example:

```javascript
function execute(fn) {
    return fn();
}
```

This is a higher-order function.

It is not currying.

---

# 61. Currying vs Chaining

This:

```javascript
user
    .setName("Alice")
    .setRole("admin")
    .save();
```

is **method chaining**.

This:

```javascript
createUser(
    "Alice"
)(
    "admin"
)(
    true
);
```

is currying.

They may visually look like sequences, but they are different concepts.

---

# 62. Currying vs Nested Functions

A function containing another function is not automatically curried.

Example:

```javascript
function outer() {
    function inner() {
        console.log(
            "Hello"
        );
    }

    inner();
}
```

This is nested functions.

Currying specifically concerns staged argument application.

---

# 63. Currying vs Callback

Example:

```javascript
button.addEventListener(
    "click",
    handleClick
);
```

`handleClick` is a callback.

A curried function can **create** a callback:

```javascript
button.addEventListener(
    "click",
    handleAction("save")
);
```

Here:

```javascript
handleAction("save")
```

returns the callback.

Currying and callbacks are separate concepts that can work together.

---

# 64. Currying and Pure Functions

Currying is common in functional programming, where pure functions are frequently preferred.

Example:

```javascript
const add =
    a =>
    b =>
        a + b;
```

This is pure:

```text
Same inputs
→
Same output
```

Pure curried functions are easy to:

```text
Reuse
Compose
Test
Reason about
```

But currying itself does not guarantee purity.

---

# 65. Curried Function with Side Effect

```javascript
const log =
    prefix =>
    message =>
        console.log(
            prefix,
            message
        );
```

This is curried.

But:

```javascript
console.log()
```

is a side effect.

Therefore:

```text
Curried
does not mean
Pure
```

---

# 66. Currying and Testing

Suppose:

```javascript
const validateLength =
    minimum =>
    value =>
        value.length >=
        minimum;
```

Create:

```javascript
const minimum8 =
    validateLength(8);
```

Testing becomes simple:

```javascript
console.assert(
    minimum8(
        "12345678"
    ) === true
);

console.assert(
    minimum8(
        "123"
    ) === false
);
```

Specialized functions can be tested independently.

---

# 67. Currying and Configuration

Suppose:

```javascript
const createFormatter =
    locale =>
    options =>
    value =>
        new Intl.NumberFormat(
            locale,
            options
        ).format(value);
```

Configure:

```javascript
const usFormatter =
    createFormatter(
        "en-US"
    );
```

Then configure further:

```javascript
const usdFormatter =
    usFormatter({
        style: "currency",
        currency: "USD"
    });
```

Then:

```javascript
usdFormatter(1000);
```

produces a formatted currency string such as:

```text
$1,000.00
```

This demonstrates staged configuration.

---

# 68. Currying and Middleware Concepts

Middleware often uses nested function structures.

For example:

```javascript
const middleware =
    config =>
    next =>
    action => {
        console.log(
            config
        );

        return next(
            action
        );
    };
```

Usage conceptually:

```text
Configuration
      │
      ▼
Next Function
      │
      ▼
Action
      │
      ▼
Process
```

You may encounter patterns like this in state management and server frameworks.

---

# 69. Middleware Example

```javascript
const logger =
    prefix =>
    next =>
    action => {
        console.log(
            prefix,
            action
        );

        return next(
            action
        );
    };
```

Configure:

```javascript
const appLogger =
    logger("[APP]");
```

Then middleware infrastructure can provide:

```text
next
```

and later:

```text
action
```

Currying fits situations where different arguments become available at different stages.

---

# 70. Currying and Different Timing

Sometimes arguments are not available simultaneously.

Example:

```text
Application Startup

Database becomes available
        │
        ▼
Configure database


Request arrives later
        │
        ▼
User ID becomes available


Handler executes
```

Curried functions can naturally represent those stages:

```javascript
const getUser =
    database =>
    userId =>
        database.findUser(
            userId
        );
```

---

# 71. Practical Example — Role-Based Validation

```javascript
const canPerform =
    permissions =>
    role =>
    action =>
        permissions[
            role
        ]?.includes(
            action
        ) ?? false;
```

Configure permissions:

```javascript
const permissions = {
    admin: [
        "read",
        "write",
        "delete"
    ],

    editor: [
        "read",
        "write"
    ],

    viewer: [
        "read"
    ]
};
```

Then:

```javascript
const checkPermission =
    canPerform(
        permissions
    );
```

Then:

```javascript
const adminCan =
    checkPermission(
        "admin"
    );
```

Then:

```javascript
adminCan(
    "delete"
);
```

returns:

```text
true
```

---

# 72. Practical Example — Query Builder

```javascript
const where =
    property =>
    operator =>
    value =>
        `${property} ${operator} ${value}`;
```

Usage:

```javascript
const ageWhere =
    where("age");

const ageGreaterThan =
    ageWhere(">");

ageGreaterThan(18);
```

Result:

```text
age > 18
```

This is a simplified educational example. Real SQL should use parameterized queries rather than constructing untrusted values into query strings.

---

# 73. Practical Example — URL Builder

```javascript
const createUrl =
    baseUrl =>
    resource =>
    id =>
        `${baseUrl}/${resource}/${id}`;
```

Configure:

```javascript
const apiUrl =
    createUrl(
        "https://api.example.com"
    );

const userUrl =
    apiUrl("users");
```

Then:

```javascript
userUrl(42);
```

returns:

```text
https://api.example.com/users/42
```

---

# 74. Practical Example — Selector Generator

```javascript
const getProperty =
    property =>
    object =>
        object[property];
```

Create:

```javascript
const getName =
    getProperty("name");

const getEmail =
    getProperty("email");
```

Usage:

```javascript
const user = {
    name: "Alice",
    email:
        "alice@example.com"
};

getName(user);
```

returns:

```text
Alice
```

---

# 75. Mapping with Property Selectors

Given:

```javascript
const users = [
    {
        name: "Alice"
    },
    {
        name: "Bob"
    },
    {
        name: "Charlie"
    }
];
```

Use:

```javascript
users.map(
    getProperty("name")
);
```

Result:

```javascript
[
    "Alice",
    "Bob",
    "Charlie"
]
```

This demonstrates how curried function factories integrate nicely with array methods.

---

# 76. Practical Example — Sorting

```javascript
const compareBy =
    property =>
    (a, b) => {
        if (
            a[property] <
            b[property]
        ) {
            return -1;
        }

        if (
            a[property] >
            b[property]
        ) {
            return 1;
        }

        return 0;
    };
```

Then:

```javascript
users.sort(
    compareBy("name")
);
```

Technically, the returned comparator takes two arguments:

```javascript
(a, b)
```

so this is better described as a function factory / partial-application pattern rather than strict full currying.

This distinction is worth understanding.

---

# 77. Currying Does Not Require Arrow Functions

This:

```javascript
const add =
    a =>
    b =>
        a + b;
```

is convenient.

But currying works with normal functions:

```javascript
function add(a) {
    return function (b) {
        return a + b;
    };
}
```

Arrow functions are syntax.

Currying is the concept.

---

# 78. Currying Does Not Mean Calling Immediately

You can:

```javascript
add(10)(20);
```

or:

```javascript
const add10 =
    add(10);

const result =
    add10(20);
```

The second form often demonstrates the practical value more clearly.

---

# 79. Common Mistake — Confusing Currying with Calling Multiple Functions

This:

```javascript
a();
b();
c();
```

is not currying.

Currying requires:

```text
One function
returns another function
that receives another
part of the argument set.
```

---

# 80. Common Mistake — Calling a Non-Curried Function Like a Curried Function

```javascript
function add(a, b) {
    return a + b;
}

add(5)(10);
```

`add(5)` produces:

```text
NaN
```

because `b` is `undefined`, and then JavaScript attempts to call that result as a function.

The original function must be designed or transformed to support currying.

---

# 81. Common Mistake — Forgetting to Return the Inner Function

Wrong:

```javascript
function multiply(a) {
    function inner(b) {
        return a * b;
    }
}
```

Then:

```javascript
multiply(5);
```

returns:

```text
undefined
```

Correct:

```javascript
function multiply(a) {
    return function (b) {
        return a * b;
    };
}
```

Each stage must return the next function until the final result is produced.

---

# 82. Common Mistake — Forgetting the Final Return

Wrong:

```javascript
const add =
    a =>
    b => {
        a + b;
    };
```

This returns:

```text
undefined
```

because the block body needs an explicit `return`.

Correct:

```javascript
const add =
    a =>
    b => {
        return a + b;
    };
```

or:

```javascript
const add =
    a =>
    b =>
        a + b;
```

---

# 83. Common Mistake — Over-Currying

Do not turn:

```javascript
function add(a, b) {
    return a + b;
}
```

into a curried function if the application always has both arguments at the same time and there is no reuse or composition benefit.

Simple code is often better.

---

# 84. Common Mistake — Poor Argument Order

Suppose the frequently changing value comes first:

```javascript
const request =
    endpoint =>
    baseUrl =>
        // ...
```

You cannot conveniently configure the stable base URL first.

Design argument order according to:

```text
Stable / Configuration
        ↓
Earlier


Dynamic / Data
        ↓
Later
```

when that fits the use case.

---

# 85. Common Mistake — Calling Instead of Passing

Suppose:

```javascript
const isGreaterThan =
    minimum =>
    value =>
        value > minimum;
```

Correct:

```javascript
numbers.filter(
    isGreaterThan(10)
);
```

Because:

```javascript
isGreaterThan(10)
```

returns the callback.

Do not accidentally write something that invokes the returned predicate without the value it needs.

---

# 86. Common Mistake — Assuming Currying Improves Performance

Currying is primarily about:

```text
Function structure
Reusability
Composition
Configuration
```

It does not inherently make calculations faster.

In fact, it may create additional function objects and calls.

Use it for architectural benefits, not automatic performance optimization.

---

# 87. Common Mistake — Calling Every Nested Function "Currying"

Example:

```javascript
function outer() {
    return function () {
        return function () {
            return 10;
        };
    };
}
```

This contains nested returned functions, but without staged arguments it is not a useful example of currying.

Currying is about transforming multi-argument computation into staged argument application.

---

# 88. Common Mistake — Currying Everything

Functional programming techniques are tools, not rules.

Use currying where it improves:

```text
Reuse
Composition
Configuration
Readability
Testing
```

Avoid it when it makes straightforward code harder to understand.

---

# 89. Interview Question — What Is Currying?

Currying is the transformation of a function that takes multiple arguments into a sequence of functions that each take one argument.

```javascript
f(a, b, c)
```

becomes:

```javascript
f(a)(b)(c)
```

---

# 90. Interview Question — Why Is Currying Useful?

It enables:

```text
Partial configuration

Reusable specialized functions

Function composition

Reduced repeated arguments

Function factories

Staged dependency injection
```

---

# 91. Interview Question — How Does Currying Work in JavaScript?

Usually through functions returning functions and closures retaining previously supplied arguments.

Example:

```javascript
const add =
    a =>
    b =>
        a + b;
```

When:

```javascript
add(5);
```

runs, the returned function remembers:

```text
a = 5
```

through closure.

---

# 92. Interview Question — Currying vs Partial Application?

Currying:

```javascript
f(a, b, c)
```

becomes:

```javascript
f(a)(b)(c)
```

Partial application fixes some arguments and returns a function expecting the remaining arguments.

Example:

```javascript
const double =
    value =>
        multiply(
            2,
            value
        );
```

Currying can enable partial application, but the concepts are different.

---

# 93. Interview Question — Currying vs Closure?

Closure is the language mechanism that lets a function remember its lexical environment.

Currying is a function design/transformation technique.

Curried functions commonly rely on closures.

```text
Closure
=
Mechanism


Currying
=
Technique
```

---

# 94. Interview Question — Currying vs Higher-Order Functions?

Higher-order functions accept functions or return functions.

Curried functions return functions as arguments are supplied.

Therefore curried functions involve higher-order behavior, but not every higher-order function is curried.

---

# 95. Interview Question — Currying vs Function Composition?

Currying prepares functions for staged configuration.

Composition combines functions.

Example:

```text
Currying

multiply(2)
↓
double


Composition

double
+
add10
+
square
↓
Pipeline
```

They often work together.

---

# 96. Interview Question — Is Currying Built into JavaScript?

JavaScript supports the features needed to implement currying:

```text
First-class functions
Closures
Higher-order functions
Rest parameters
```

But JavaScript does not automatically curry ordinary functions.

You implement the pattern yourself or use a library.

---

# 97. Interview Question — Does Currying Require Arrow Functions?

No.

Both are valid:

```javascript
function add(a) {
    return function (b) {
        return a + b;
    };
}
```

and:

```javascript
const add =
    a =>
    b =>
        a + b;
```

Arrow functions simply make curried syntax compact.

---

# 98. Interview Question — Why Does Argument Order Matter?

Because earlier arguments are easier to preconfigure.

If:

```text
Configuration
```

is supplied first, you can create reusable functions that later accept changing data.

Example:

```javascript
const filterBy =
    property =>
    value =>
    object =>
        // ...
```

---

# 99. Interview Question — What Is a Function Factory?

A function factory returns functions configured using supplied values.

Example:

```javascript
const multiply =
    factor =>
    value =>
        value * factor;
```

Then:

```javascript
const double =
    multiply(2);
```

Currying naturally supports function-factory patterns.

---

# 100. Interview Question — Can Curried Functions Have Side Effects?

Yes.

Currying does not guarantee purity.

Example:

```javascript
const logger =
    level =>
    message =>
        console.log(
            level,
            message
        );
```

This is curried but has a side effect.

---

# 101. Interview Question — When Should Currying Be Avoided?

Avoid it when:

```text
All arguments are always
available together.

No intermediate function
will be reused.

It makes code harder
to understand.

The team is unfamiliar
with the pattern and there
is little practical benefit.

A normal function is clearer.
```

---

# 102. Output Question

```javascript
const multiply =
    a =>
    b =>
        a * b;

const double =
    multiply(2);

console.log(
    double(5)
);

console.log(
    double(10)
);
```

Output:

```text
10
20
```

Because:

```text
multiply(2)
↓
Returns function
remembering 2
```

---

# 103. Output Question

```javascript
const add =
    a =>
    b =>
    c =>
        a + b + c;

const first =
    add(10);

const second =
    first(20);

console.log(
    second(30)
);
```

Output:

```text
60
```

Flow:

```text
10
↓
Remember

20
↓
Remember

30
↓
Calculate

10 + 20 + 30
=
60
```

---

# 104. Output Question

```javascript
const greet =
    greeting =>
    name =>
        `${greeting}, ${name}`;

const hello =
    greet("Hello");

console.log(
    hello("Alice")
);

console.log(
    hello("Bob")
);
```

Output:

```text
Hello, Alice
Hello, Bob
```

The greeting was configured once.

---

# 105. Output Question

```javascript
const greaterThan =
    minimum =>
    value =>
        value > minimum;

const values = [
    5,
    10,
    15,
    20
];

console.log(
    values.filter(
        greaterThan(10)
    )
);
```

Output:

```javascript
[
    15,
    20
]
```

Because:

```javascript
greaterThan(10)
```

returns:

```javascript
value =>
    value > 10;
```

---

# 106. Write a Curried Function

Normal:

```javascript
function calculate(
    a,
    b,
    c
) {
    return (
        a * b + c
    );
}
```

Curried:

```javascript
const calculate =
    a =>
    b =>
    c =>
        a * b + c;
```

Usage:

```javascript
calculate(5)(10)(20);
```

Result:

```text
70
```

---

# 107. Practical Pattern to Remember

```javascript
const createFunction =
    configuration =>
    data => {
        // use configuration
        // with changing data
    };
```

Configure once:

```javascript
const configuredFunction =
    createFunction(
        configuration
    );
```

Reuse:

```javascript
configuredFunction(
    data1
);

configuredFunction(
    data2
);

configuredFunction(
    data3
);
```

This is one of the most useful ways to think about currying.

---

# 108. Currying Mental Model

```text
Normal Function

┌─────────────────────┐
│                     │
│   f(a, b, c)        │
│                     │
└──────────┬──────────┘
           │
           ▼
         Result


Curried Function

      f(a)
       │
       ▼
Remember a
       │
       ▼
Function(b)
       │
       ▼
Remember b
       │
       ▼
Function(c)
       │
       ▼
Use a + b + c
       │
       ▼
     Result
```

---

# 109. Currying vs Partial Application Cheat Sheet

```text
CURRYING
================================

f(a, b, c)

↓

f(a)(b)(c)


Each stage receives
an argument.


PARTIAL APPLICATION
================================

f(a, b, c)

Fix:

a = 10

↓

g(b, c)


Some arguments are fixed.

Remaining function may
still take multiple arguments.


RELATIONSHIP
================================

Currying makes partial
application natural.

But they are not
the same concept.
```

---

# 110. Currying Cheat Sheet

```text
CURRYING
================================

Transform:

f(a, b, c)

into:

f(a)(b)(c)


CORE MECHANISM
================================

Functions returning functions
+
Closures


EXAMPLE
================================

const add =
    a =>
    b =>
        a + b;

add(5)(10);

→ 15


INTERMEDIATE FUNCTION
================================

const add5 =
    add(5);

add5(10);
add5(20);


WHY USE IT?
================================

Configure once

Reuse many times

Create specialized functions

Reduce repeated arguments

Function composition

Dependency configuration

Callback factories


CLOSURE
================================

Previous arguments are
remembered by returned
functions.


ARROW FUNCTIONS
================================

Useful syntax for currying.

Not required.


PARTIAL APPLICATION
================================

Fix some arguments.

Related to currying,
but not identical.


ARGUMENT ORDER
================================

Stable configuration
usually earlier.

Changing data
usually later.


COMMON USES
================================

Validators
Loggers
Formatters
Filters
Mappers
Event handlers
API configuration
Dependency injection
Middleware
Functional pipelines


DO NOT
================================

Curry everything.

Use it only when the
structure provides value.
```

---

# 111. Important Rules to Remember

```text
1. Currying transforms multi-argument functions into staged function calls.

2. f(a, b, c) becomes f(a)(b)(c).

3. Each curried stage typically accepts one argument.

4. Each stage returns another function until enough arguments exist.

5. The final stage returns the actual result.

6. Currying commonly relies on closures.

7. Closures remember previously supplied arguments.

8. Curried functions involve higher-order-function behavior.

9. Not every higher-order function is curried.

10. Not every closure is currying.

11. Nested functions are not automatically currying.

12. Arrow functions are convenient for currying.

13. Arrow functions are not required.

14. Intermediate curried functions can be stored.

15. Intermediate functions can become specialized reusable functions.

16. multiply(2) can create double().

17. Configuration can be supplied once.

18. Changing data can be supplied later.

19. Stable arguments are often placed earlier.

20. Frequently changing arguments are often placed later.

21. Argument order strongly affects reusability.

22. Currying works well with map().

23. Currying works well with filter().

24. Currying can help create callbacks.

25. Currying can help create validators.

26. Currying can help create loggers.

27. Currying can help configure APIs.

28. Currying can help with dependency injection.

29. Currying can appear in middleware patterns.

30. Currying works well with function composition.

31. Currying and partial application are related.

32. Currying and partial application are not identical.

33. Partial application fixes some arguments.

34. A partially applied function may still accept multiple arguments.

35. Strict currying normally supplies one argument per stage.

36. JavaScript does not automatically curry normal functions.

37. A curry utility can transform compatible functions.

38. Function.length can help determine expected argument count.

39. Function.length has limitations.

40. Default parameters affect function.length.

41. Rest parameters affect function.length.

42. Optional arguments complicate automatic currying.

43. Currying does not guarantee pure functions.

44. Curried functions can still have side effects.

45. Currying does not inherently improve performance.

46. Currying can create additional function calls.

47. Currying is primarily about function structure.

48. Currying can improve reusability.

49. Currying can improve composition.

50. Currying can reduce repeated configuration.

51. Currying can improve readability when specialized functions have meaningful names.

52. Currying can hurt readability when overused.

53. Do not curry functions merely because you can.

54. A normal function is better when all arguments naturally arrive together.

55. Currying is especially useful when arguments arrive at different stages.

56. Function factories and currying often overlap.

57. Event-handler factories often use currying-like structures.

58. React code can use functions that return event handlers.

59. Functional libraries often make heavy use of currying.

60. Understand the reason for currying rather than memorizing f(a)(b)(c).
```

---

# Currying in One Sentence

> Currying transforms a function that accepts multiple arguments into a sequence of functions that receive those arguments in stages, allowing earlier arguments to configure reusable functions for later calls.

---

# Final Mental Model

Normal function:

```text
       a
       │
       ├─────┐
       │     │
       b     │
       │     │
       ├─────┤
       │     ▼
       c → Function
             │
             ▼
           Result
```

Curried:

```text
a
│
▼
Function
│
├── Remember a
│
▼
b
│
▼
Function
│
├── Remember a
├── Remember b
│
▼
c
│
▼
Function
│
▼
Use a, b, c
│
▼
Result
```

Practical idea:

```text
General Function
       │
       ▼
Provide Configuration
       │
       ▼
Specialized Function
       │
       ├── Data 1 → Result
       │
       ├── Data 2 → Result
       │
       └── Data 3 → Result
```

Remember:

```javascript
const multiply =
    a =>
    b =>
        a * b;

const double =
    multiply(2);

double(5);
double(10);
double(20);
```

Think:

```text
Currying
=
Give arguments gradually

Closure
=
Remember earlier arguments

Partial Application
=
Reuse a function with some
arguments already fixed

Composition
=
Combine small functions
into larger behavior
```
