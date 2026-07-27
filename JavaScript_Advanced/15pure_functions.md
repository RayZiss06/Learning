# Pure Functions

A **pure function** is a function that satisfies two fundamental rules:

1. **Same input → Same output**
2. **No side effects**

Example:

```javascript
function add(a, b) {
    return a + b;
}

add(2, 3); // 5
add(2, 3); // 5
add(2, 3); // 5
```

For the same inputs:

```text
2, 3
```

the result will always be:

```text
5
```

The function also does not modify anything outside itself.

Therefore:

```javascript
add()
```

is a **pure function**.

---

# 1. Core Idea

Think of a pure function like a mathematical function.

```text
Input
  │
  ▼
Function
  │
  ▼
Output
```

The output depends **only on the input**.

Example:

```javascript
const square = number =>
    number * number;
```

```javascript
square(5); // 25
square(5); // 25
square(5); // 25
```

Nothing else affects the result.

---

# 2. Two Rules of Pure Functions

A function is pure when:

```text
PURE FUNCTION
=============================

1. Same Input → Same Output

2. No Side Effects
```

Both conditions matter.

A function that satisfies only one is not necessarily pure.

---

# 3. Rule 1 — Same Input, Same Output

Example:

```javascript
function multiply(a, b) {
    return a * b;
}
```

Calling:

```javascript
multiply(5, 10);
```

will always return:

```text
50
```

assuming ordinary JavaScript number behavior.

The result depends only on:

```text
a
b
```

---

# 4. Deterministic Functions

A function whose output is determined by its inputs is called **deterministic**.

```javascript
function calculateTax(
    price,
    taxRate
) {
    return price * taxRate;
}
```

Example:

```javascript
calculateTax(
    100,
    0.18
);
```

always produces:

```text
18
```

for those inputs.

Pure functions are deterministic.

But deterministic behavior alone is not enough: a pure function must also avoid side effects.

---

# 5. Rule 2 — No Side Effects

A **side effect** occurs when a function interacts with or changes something outside its returned value.

Common side effects include:

```text
Modifying global variables

Mutating input objects

Mutating input arrays

Writing to files

Writing to a database

Making network requests

Updating the DOM

Logging to the console

Changing browser storage

Sending analytics

Sending emails

Starting timers
```

Side effects are not inherently bad.

Applications need side effects.

The important concept is knowing **where they occur** and keeping pure logic separate when practical.

---

# 6. Pure Function Example

```javascript
function add(a, b) {
    return a + b;
}
```

Why pure?

```text
Same inputs
    ↓
Same output

No external state read

No external state modified

No input mutation

No external interaction
```

---

# 7. Impure Function — Global Variable

```javascript
let taxRate = 0.18;

function calculateTax(price) {
    return price * taxRate;
}
```

At first:

```javascript
calculateTax(100);
```

returns:

```text
18
```

But:

```javascript
taxRate = 0.20;
```

Now:

```javascript
calculateTax(100);
```

returns:

```text
20
```

Same input:

```text
100
```

Different output:

```text
18
20
```

Therefore the function is impure because its result depends on external mutable state.

---

# 8. Making It Pure

Instead of:

```javascript
let taxRate = 0.18;

function calculateTax(price) {
    return price * taxRate;
}
```

pass the dependency explicitly:

```javascript
function calculateTax(
    price,
    taxRate
) {
    return price * taxRate;
}
```

Now:

```javascript
calculateTax(
    100,
    0.18
);
```

always returns:

```text
18
```

The dependency is visible through the function parameters.

---

# 9. Explicit Dependencies

Compare:

```javascript
function calculate(price) {
    return price * taxRate;
}
```

with:

```javascript
function calculate(
    price,
    taxRate
) {
    return price * taxRate;
}
```

The second version explicitly declares what it needs.

```text
Hidden Dependency

Function
   │
   └── External taxRate


Explicit Dependency

price ───────┐
             ▼
          Function
             ▲
taxRate ─────┘
```

Explicit dependencies generally make functions easier to:

```text
Understand
Test
Reuse
Debug
Predict
```

---

# 10. Reading Constants

Consider:

```javascript
const TAX_RATE = 0.18;

function calculateTax(price) {
    return price * TAX_RATE;
}
```

Strictly speaking, the function depends on something outside its arguments.

However, if `TAX_RATE` is truly immutable and stable, the function remains deterministic in practice.

For learning and designing highly reusable pure functions, prefer:

```javascript
function calculateTax(
    price,
    taxRate
) {
    return price * taxRate;
}
```

when the dependency is logically part of the computation.

---

# 11. Impure Function — Modifying Global State

```javascript
let count = 0;

function increment() {
    count++;
}
```

This function changes:

```javascript
count
```

outside itself.

Therefore it has a side effect.

Calling:

```javascript
increment();
```

changes application state.

---

# 12. Pure Alternative

Instead of:

```javascript
let count = 0;

function increment() {
    count++;
}
```

write:

```javascript
function increment(count) {
    return count + 1;
}
```

Usage:

```javascript
let count = 0;

count = increment(count);
```

The function itself:

```javascript
increment()
```

does not mutate external state.

It simply computes a new value.

---

# 13. Mutation

**Mutation** means changing existing data.

Example:

```javascript
const user = {
    name: "Alice",
    age: 25
};

user.age = 26;
```

The existing object was modified.

That is mutation.

---

# 14. Input Mutation Makes a Function Impure

```javascript
function updateAge(user) {
    user.age = 30;

    return user;
}
```

Usage:

```javascript
const user = {
    name: "Alice",
    age: 25
};

updateAge(user);
```

Now the original:

```javascript
user
```

has changed.

Result:

```javascript
{
    name: "Alice",
    age: 30
}
```

The function mutated its input.

Therefore it is impure.

---

# 15. Pure Object Update

Instead:

```javascript
function updateAge(
    user,
    age
) {
    return {
        ...user,
        age
    };
}
```

Usage:

```javascript
const user = {
    name: "Alice",
    age: 25
};

const updatedUser =
    updateAge(
        user,
        30
    );
```

Original:

```javascript
user;
```

remains:

```javascript
{
    name: "Alice",
    age: 25
}
```

New object:

```javascript
updatedUser;
```

is:

```javascript
{
    name: "Alice",
    age: 30
}
```

---

# 16. Immutability

**Immutability** means avoiding changes to existing values and instead creating new values.

Mutation:

```javascript
user.age = 30;
```

Immutable update:

```javascript
const updatedUser = {
    ...user,
    age: 30
};
```

Mental model:

```text
MUTATION

Original Object
      │
      ▼
   Modified
      │
      ▼
Same Object


IMMUTABLE UPDATE

Original Object
      │
      ├─────────────┐
      │             ▼
      │         New Object
      │
      ▼
Unchanged
```

---

# 17. Pure Array Function

Consider:

```javascript
function addItem(
    array,
    item
) {
    return [
        ...array,
        item
    ];
}
```

Usage:

```javascript
const numbers = [
    1,
    2,
    3
];

const updated =
    addItem(
        numbers,
        4
    );
```

Original:

```javascript
numbers;
```

remains:

```javascript
[
    1,
    2,
    3
]
```

New array:

```javascript
updated;
```

is:

```javascript
[
    1,
    2,
    3,
    4
]
```

---

# 18. Impure Array Function

```javascript
function addItem(
    array,
    item
) {
    array.push(item);

    return array;
}
```

`push()` modifies the original array.

Therefore:

```javascript
addItem()
```

mutates its input.

---

# 19. Common Mutating Array Methods

These methods mutate the original array:

```javascript
push()

pop()

shift()

unshift()

splice()

sort()

reverse()

fill()

copyWithin()
```

Example:

```javascript
const numbers = [
    3,
    1,
    2
];

numbers.sort();
```

`numbers` itself is changed.

---

# 20. Common Non-Mutating Array Operations

Common operations that can create new values include:

```javascript
map()

filter()

slice()

concat()

flat()

flatMap()

toSorted()

toReversed()

toSpliced()

with()
```

Example:

```javascript
const numbers = [
    1,
    2,
    3
];

const doubled =
    numbers.map(
        number =>
            number * 2
    );
```

Original:

```javascript
[
    1,
    2,
    3
]
```

New:

```javascript
[
    2,
    4,
    6
]
```

---

# 21. Non-Mutating Method Does Not Guarantee Purity

This:

```javascript
const result =
    users.map(
        user => {
            user.active = true;

            return user;
        }
    );
```

uses `map()`, but the callback mutates each object.

So:

```text
map()
```

does not automatically make code pure.

Better:

```javascript
const result =
    users.map(
        user => ({
            ...user,
            active: true
        })
    );
```

---

# 22. Shallow Copies

Spread syntax:

```javascript
const copy = {
    ...original
};
```

creates a **shallow copy**.

Consider:

```javascript
const user = {
    name: "Alice",
    address: {
        city: "New York"
    }
};
```

Then:

```javascript
const copy = {
    ...user
};
```

`copy` is new, but:

```javascript
copy.address
```

and:

```javascript
user.address
```

still reference the same nested object.

---

# 23. Nested Mutation Problem

```javascript
const user = {
    name: "Alice",
    address: {
        city: "New York"
    }
};

const updated = {
    ...user
};

updated.address.city =
    "Chicago";
```

Now:

```javascript
user.address.city;
```

is also:

```text
Chicago
```

because the nested `address` object was shared.

---

# 24. Immutable Nested Update

Instead:

```javascript
const updated = {
    ...user,

    address: {
        ...user.address,
        city: "Chicago"
    }
};
```

Now:

```text
user
```

keeps the old nested object.

`updated` receives a new nested object.

This pattern is very important for React state updates.

---

# 25. `structuredClone()`

For supported values, JavaScript provides:

```javascript
const copy =
    structuredClone(
        original
    );
```

This performs a deep clone for many structured data types.

However:

```text
Deep cloning everything
is not the default solution
for immutable updates.
```

It can be unnecessary and expensive.

Usually copy only the parts that need to change.

---

# 26. Randomness and Purity

Consider:

```javascript
function getRandomNumber() {
    return Math.random();
}
```

Calling:

```javascript
getRandomNumber();
```

can return different results each time.

Therefore it is not deterministic and is not pure.

---

# 27. Making Randomness Explicit

Instead of generating randomness inside your core logic:

```javascript
function chooseWinner(users) {
    const index =
        Math.floor(
            Math.random() *
            users.length
        );

    return users[index];
}
```

you can separate the random value:

```javascript
function chooseWinner(
    users,
    randomValue
) {
    const index =
        Math.floor(
            randomValue *
            users.length
        );

    return users[index];
}
```

Now the core function's result is determined by its inputs.

The random generation can happen outside:

```javascript
const winner =
    chooseWinner(
        users,
        Math.random()
    );
```

---

# 28. Date and Time

Consider:

```javascript
function getGreeting() {
    const hour =
        new Date().getHours();

    return hour < 12
        ? "Good morning"
        : "Good afternoon";
}
```

The result depends on the current time.

Same function call:

```javascript
getGreeting();
```

can return different values at different times.

Therefore it is not pure.

---

# 29. Pure Time-Based Logic

Pass time explicitly:

```javascript
function getGreeting(hour) {
    return hour < 12
        ? "Good morning"
        : "Good afternoon";
}
```

Now:

```javascript
getGreeting(9);
```

always returns:

```text
Good morning
```

and:

```javascript
getGreeting(15);
```

always returns:

```text
Good afternoon
```

---

# 30. Console Logging Is a Side Effect

```javascript
function add(a, b) {
    console.log(
        "Calculating..."
    );

    return a + b;
}
```

The calculation is deterministic, but:

```javascript
console.log()
```

interacts with the outside environment.

Therefore, under the strict definition, the function is impure.

---

# 31. DOM Manipulation Is a Side Effect

```javascript
function showMessage(
    message
) {
    document
        .querySelector("#message")
        .textContent =
            message;
}
```

This changes the DOM.

Therefore it is a side-effecting function.

That does **not** mean it is bad.

UI applications require DOM updates.

---

# 32. Network Requests Are Side Effects

```javascript
async function getUsers() {
    const response =
        await fetch(
            "/api/users"
        );

    return response.json();
}
```

`fetch()` interacts with an external system.

The same call may produce:

```text
Different data

Network error

Server error

Timeout

Different status
```

Therefore the function is not pure.

---

# 33. Database Operations Are Side Effects

```javascript
async function saveUser(user) {
    return database
        .insert(user);
}
```

This changes an external system.

Therefore it is side-effecting.

Again, applications need this behavior.

The goal is not:

```text
Remove all side effects
```

The goal is:

```text
Separate pure computation
from side effects
when useful.
```

---

# 34. Pure Core, Impure Shell

A useful architecture principle is:

```text
Impure Input
     │
     ▼
Pure Business Logic
     │
     ▼
Impure Output
```

Example:

```javascript
async function checkout(
    order
) {
    const taxRate =
        await getTaxRate();

    const total =
        calculateTotal(
            order,
            taxRate
        );

    await saveOrder(
        total
    );

    return total;
}
```

Here:

```text
getTaxRate()
→ Side Effect

calculateTotal()
→ Can Be Pure

saveOrder()
→ Side Effect
```

Keep the calculation separate from external interactions.

---

# 35. Pure Business Logic Example

```javascript
function calculateTotal(
    items,
    taxRate
) {
    const subtotal =
        items.reduce(
            (
                total,
                item
            ) =>
                total +
                item.price *
                item.quantity,
            0
        );

    return (
        subtotal +
        subtotal * taxRate
    );
}
```

If the inputs do not change during execution, this function:

```text
Reads no external state

Modifies no external state

Mutates no inputs

Produces output from inputs
```

Therefore it is pure.

---

# 36. Referential Transparency

A pure function call can be replaced by its result without changing program behavior.

Example:

```javascript
function add(a, b) {
    return a + b;
}

const result =
    add(2, 3) * 10;
```

Since:

```javascript
add(2, 3);
```

always means:

```text
5
```

we can conceptually replace it:

```javascript
const result =
    5 * 10;
```

The behavior stays the same.

This property is called **referential transparency**.

---

# 37. Why Referential Transparency Matters

It makes code easier to reason about.

If:

```javascript
calculateTotal(order);
```

always returns the same result for the same `order` value, you can mentally treat the call like a value.

```text
Input
↓
Known transformation
↓
Predictable output
```

This helps with:

```text
Testing
Caching
Refactoring
Debugging
Composition
Parallel reasoning
```

---

# 38. Pure Functions Are Easy to Test

Consider:

```javascript
function add(a, b) {
    return a + b;
}
```

Testing:

```javascript
console.assert(
    add(2, 3) === 5
);

console.assert(
    add(-1, 1) === 0
);
```

No setup is required.

No:

```text
Database

API

DOM

Clock

Random generator

Global state
```

is needed.

---

# 39. Impure Functions Are Harder to Test

Consider:

```javascript
async function calculatePrice(
    productId
) {
    const product =
        await database
            .getProduct(
                productId
            );

    const rate =
        await fetchTaxRate();

    return (
        product.price *
        (1 + rate)
    );
}
```

Testing this may require:

```text
Database mocks

Network mocks

Async setup

Failure simulations
```

Instead, separate:

```javascript
function calculatePrice(
    price,
    rate
) {
    return (
        price *
        (1 + rate)
    );
}
```

Then test business logic independently.

---

# 40. Pure Functions and Memoization

Suppose:

```javascript
function square(number) {
    return number * number;
}
```

Since:

```javascript
square(5);
```

always returns:

```text
25
```

we can safely cache:

```text
Input 5
→ Output 25
```

This makes pure functions natural candidates for memoization.

---

# 41. Memoizing Impure Functions Can Be Wrong

Consider:

```javascript
function getCurrentTime() {
    return Date.now();
}
```

If memoized:

```text
First call
→ 10:00

Cache

Later call
→ Still 10:00
```

That changes the expected behavior.

Memoization works best when output is determined by inputs.

---

# 42. Pure Functions and Parallel Execution

If two pure computations do not share mutable state:

```javascript
const a =
    calculateA(dataA);

const b =
    calculateB(dataB);
```

they are easier to reason about independently.

Shared mutable state introduces dependencies such as:

```text
Which function ran first?

Did another function change the data?

What value exists now?
```

Pure functions reduce these hidden interactions.

---

# 43. Pure Function Composition

Pure functions work well together.

```javascript
const double =
    number =>
        number * 2;

const add10 =
    number =>
        number + 10;
```

Then:

```javascript
const result =
    add10(
        double(5)
    );
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
```

Each stage is predictable.

---

# 44. Pure Array Pipeline

```javascript
const activeUsers =
    users
        .filter(
            user =>
                user.active
        )
        .map(
            user => ({
                ...user,
                status:
                    "Active"
            })
        );
```

Assuming the callbacks depend only on their inputs and do not mutate anything, the transformation is pure.

---

# 45. Pure vs Impure Callback

Pure callback:

```javascript
numbers.map(
    number =>
        number * 2
);
```

Impure callback:

```javascript
let total = 0;

numbers.forEach(
    number => {
        total += number;
    }
);
```

The callback changes external state:

```javascript
total
```

Therefore it has a side effect.

---

# 46. Prefer Returning Values

Instead of:

```javascript
let total;

function calculate(
    a,
    b
) {
    total = a + b;
}
```

prefer:

```javascript
function calculate(
    a,
    b
) {
    return a + b;
}

const total =
    calculate(
        10,
        20
    );
```

The second version makes data flow explicit.

---

# 47. Data Flow

Pure-function-oriented code tends to look like:

```text
Input
  │
  ▼
Function A
  │
  ▼
Value
  │
  ▼
Function B
  │
  ▼
Value
  │
  ▼
Function C
  │
  ▼
Output
```

Rather than:

```text
Function A ──┐
             ▼
       Shared State
             ▲
Function B ──┤
             ▲
Function C ──┘
```

The first is generally easier to reason about.

---

# 48. Pure Functions and React

This concept becomes important in React.

A component conceptually behaves like:

```text
Props + State
     │
     ▼
 Component
     │
     ▼
     UI
```

For the same:

```text
Props
State
```

the rendering logic should produce the same UI description.

This is why React encourages pure rendering.

---

# 49. Pure React Component Concept

Conceptually:

```jsx
function Greeting({
    name
}) {
    return (
        <h1>
            Hello {name}
        </h1>
    );
}
```

Given:

```jsx
<Greeting
    name="Alice"
/>
```

the component's render calculation consistently produces:

```text
Hello Alice
```

The component does not need to modify external data during rendering.

---

# 50. Impure Render Logic

Avoid patterns conceptually like:

```javascript
let renderCount = 0;

function Component() {
    renderCount++;

    // render...
}
```

The rendering process modifies external state.

React may render components more than once in development or for rendering work, so render logic should not rely on side effects.

Side effects belong in appropriate mechanisms such as event handlers or effects.

---

# 51. React State Mutation

Suppose:

```javascript
const user = {
    name: "Alice",
    age: 25
};
```

Avoid:

```javascript
user.age = 26;

setUser(user);
```

Prefer:

```javascript
setUser({
    ...user,
    age: 26
});
```

Why?

Because React state should generally be treated as immutable.

Creating a new reference helps React and your code reason about changes predictably.

---

# 52. React Functional State Update

Example:

```javascript
setCount(
    previousCount =>
        previousCount + 1
);
```

The updater:

```javascript
previousCount =>
    previousCount + 1
```

is naturally written as a pure function.

Input:

```text
previousCount
```

Output:

```text
nextCount
```

It does not need to mutate the previous value.

---

# 53. Reducers

A reducer commonly follows:

```text
Current State
+
Action
      │
      ▼
   Reducer
      │
      ▼
New State
```

Example:

```javascript
function reducer(
    state,
    action
) {
    switch (
        action.type
    ) {
        case "increment":
            return {
                ...state,
                count:
                    state.count + 1
            };

        default:
            return state;
    }
}
```

The reducer returns new state instead of mutating existing state.

---

# 54. Impure Reducer

Avoid:

```javascript
function reducer(
    state,
    action
) {
    if (
        action.type ===
        "increment"
    ) {
        state.count++;

        return state;
    }

    return state;
}
```

This mutates:

```javascript
state
```

A reducer should generally compute the next state from the current state and action without mutating the existing state.

---

# 55. Pure Reducer

```javascript
function reducer(
    state,
    action
) {
    if (
        action.type ===
        "increment"
    ) {
        return {
            ...state,

            count:
                state.count + 1
        };
    }

    return state;
}
```

Flow:

```text
Old State
    │
    ▼
Reducer
    │
    ▼
New State


Old State
remains unchanged.
```

---

# 56. Pure Function with Objects

Consider:

```javascript
function renameUser(
    user,
    newName
) {
    return {
        ...user,
        name: newName
    };
}
```

Input:

```javascript
{
    name: "Alice",
    age: 25
}
```

Output:

```javascript
{
    name: "Bob",
    age: 25
}
```

Original input remains unchanged.

---

# 57. Pure Function with Arrays

Add:

```javascript
function addItem(
    items,
    item
) {
    return [
        ...items,
        item
    ];
}
```

Remove:

```javascript
function removeItem(
    items,
    id
) {
    return items.filter(
        item =>
            item.id !== id
    );
}
```

Update:

```javascript
function updateItem(
    items,
    updatedItem
) {
    return items.map(
        item =>
            item.id ===
            updatedItem.id
                ? updatedItem
                : item
    );
}
```

These patterns will appear frequently in React.

---

# 58. Updating One Property

```javascript
function updateEmail(
    user,
    email
) {
    return {
        ...user,
        email
    };
}
```

This is generally preferable to:

```javascript
function updateEmail(
    user,
    email
) {
    user.email = email;

    return user;
}
```

because the latter mutates the input object.

---

# 59. Removing Object Property Immutably

```javascript
function removePassword(
    user
) {
    const {
        password,
        ...safeUser
    } = user;

    return safeUser;
}
```

The original object remains unchanged.

---

# 60. Sorting Without Mutation

Mutating:

```javascript
function sortNumbers(
    numbers
) {
    return numbers.sort(
        (a, b) =>
            a - b
    );
}
```

This changes the input array.

Better:

```javascript
function sortNumbers(
    numbers
) {
    return [
        ...numbers
    ].sort(
        (a, b) =>
            a - b
    );
}
```

Or with modern JavaScript:

```javascript
function sortNumbers(
    numbers
) {
    return numbers.toSorted(
        (a, b) =>
            a - b
    );
}
```

---

# 61. Reversing Without Mutation

Mutating:

```javascript
numbers.reverse();
```

Immutable approach:

```javascript
const reversed =
    [...numbers]
        .reverse();
```

Modern JavaScript:

```javascript
const reversed =
    numbers.toReversed();
```

---

# 62. Replacing an Array Element

Suppose:

```javascript
const numbers = [
    10,
    20,
    30
];
```

Instead of:

```javascript
numbers[1] = 100;
```

use:

```javascript
const updated =
    numbers.with(
        1,
        100
    );
```

Result:

```javascript
[
    10,
    100,
    30
]
```

Original:

```javascript
[
    10,
    20,
    30
]
```

remains unchanged.

---

# 63. Removing Array Elements

Mutating:

```javascript
items.splice(
    index,
    1
);
```

Immutable:

```javascript
const updated =
    items.filter(
        (_, currentIndex) =>
            currentIndex !==
            index
    );
```

or:

```javascript
const updated =
    items.toSpliced(
        index,
        1
    );
```

---

# 64. Pure Function with Validation

```javascript
function validateAge(age) {
    return (
        Number.isInteger(age) &&
        age >= 18
    );
}
```

Examples:

```javascript
validateAge(25);
// true

validateAge(15);
// false
```

This is easy to:

```text
Test
Compose
Reuse
```

---

# 65. Validation with Side Effects

Instead:

```javascript
function validateAge(age) {
    if (age < 18) {
        alert(
            "You must be 18"
        );

        return false;
    }

    return true;
}
```

Now validation and UI interaction are mixed.

Better separation:

```javascript
const valid =
    validateAge(age);

if (!valid) {
    alert(
        "You must be 18"
    );
}
```

Now:

```text
Validation
→ Pure logic

Alert
→ Side effect
```

---

# 66. Pure Function with Error Information

Instead of displaying errors inside validation:

```javascript
function validateUser(
    user
) {
    const errors = [];

    if (!user.name) {
        errors.push(
            "Name is required"
        );
    }

    if (user.age < 18) {
        errors.push(
            "Must be 18 or older"
        );
    }

    return errors;
}
```

Then:

```javascript
const errors =
    validateUser(user);
```

The caller decides whether to:

```text
Display them

Log them

Return them from API

Store them

Ignore them
```

This keeps business logic reusable.

---

# 67. Side Effects at the Boundary

A useful pattern:

```javascript
const errors =
    validateUser(user);

if (errors.length > 0) {
    displayErrors(errors);
}
```

Here:

```text
validateUser()
→ Pure


displayErrors()
→ Side Effect
```

This creates a clear boundary.

---

# 68. Pure Calculation + Impure Persistence

```javascript
const updatedUser =
    updateUser(
        currentUser,
        changes
    );

await saveUser(
    updatedUser
);
```

Where:

```text
updateUser()
→ Pure


saveUser()
→ Impure
```

This is a common real-world design.

---

# 69. Benefits of Pure Functions

Pure functions generally provide:

```text
Predictability

Easy testing

Easy debugging

Reusability

Composability

Explicit dependencies

Safer refactoring

Memoization opportunities

Reduced hidden coupling

Easier state management
```

---

# 70. Predictability

With:

```javascript
function calculate(
    a,
    b
) {
    return a + b;
}
```

you only need to know:

```text
a
b
```

to understand the result.

With:

```javascript
function calculate(a) {
    return (
        a +
        globalValue +
        getCurrentTime()
    );
}
```

you need to understand:

```text
a

globalValue

current time

when globalValue changes

who changes globalValue
```

Hidden dependencies increase complexity.

---

# 71. Debugging

Pure function:

```text
Wrong Output
    │
    ▼
Check Inputs
    │
    ▼
Check Function
```

Impure function:

```text
Wrong Output
    │
    ├── Inputs?
    ├── Global State?
    ├── Database?
    ├── Network?
    ├── Time?
    ├── Randomness?
    ├── Mutation?
    └── Execution Order?
```

Pure logic reduces the number of possible causes.

---

# 72. Reusability

A function:

```javascript
function calculateTotal(
    items,
    taxRate
) {
    // ...
}
```

can be reused in:

```text
Frontend

Backend

Tests

CLI

Batch job

Worker
```

because it does not require:

```text
DOM

Database

HTTP

Browser state
```

---

# 73. Composition

Small pure functions:

```javascript
const trim =
    value =>
        value.trim();

const lowercase =
    value =>
        value.toLowerCase();

const removeSpaces =
    value =>
        value.replaceAll(
            " ",
            ""
        );
```

can be composed:

```javascript
const username =
    removeSpaces(
        lowercase(
            trim(input)
        )
    );
```

Each transformation is independent and predictable.

---

# 74. Pure Functions and HOFs

A higher-order function can itself be pure.

Example:

```javascript
function multiplyBy(
    multiplier
) {
    return value =>
        value * multiplier;
}
```

Given the same:

```javascript
multiplier
```

it creates equivalent behavior.

The returned function also calculates output from its inputs without side effects.

---

# 75. Pure Functions and Currying

```javascript
const calculateTax =
    taxRate =>
    price =>
        price * taxRate;
```

Create:

```javascript
const tax18 =
    calculateTax(0.18);
```

Then:

```javascript
tax18(100);
```

returns:

```text
18
```

Currying works especially well with pure functions because configuration and computation remain predictable.

---

# 76. Pure Functions and Closures

Closures can still be pure if the captured values do not change.

Example:

```javascript
function multiplyBy(
    multiplier
) {
    return value =>
        value * multiplier;
}
```

The returned function closes over:

```javascript
multiplier
```

but does not modify it.

Therefore:

```javascript
const double =
    multiplyBy(2);

double(10);
```

is predictable.

---

# 77. Closure with Mutable State

Contrast:

```javascript
function createCounter() {
    let count = 0;

    return function () {
        return ++count;
    };
}
```

Calling:

```javascript
const counter =
    createCounter();

counter(); // 1
counter(); // 2
counter(); // 3
```

Same arguments:

```text
No arguments
```

Different outputs:

```text
1
2
3
```

because the function depends on mutable captured state.

Therefore the returned counter function is impure.

---

# 78. Pure Does Not Mean No Variables

This is pure:

```javascript
function calculate(
    price,
    quantity
) {
    const subtotal =
        price * quantity;

    const discount =
        subtotal * 0.1;

    return (
        subtotal -
        discount
    );
}
```

Local variables are perfectly fine.

The important question is whether the function:

```text
Depends on hidden mutable state

or

Changes observable external state
```

---

# 79. Local Mutation

This can still be pure:

```javascript
function createSequence(
    length
) {
    const result = [];

    for (
        let i = 0;
        i < length;
        i++
    ) {
        result.push(i);
    }

    return result;
}
```

`result` is mutated internally.

But it was created inside the function and is not observable until returned.

Given the same input, the function produces equivalent output and does not modify external state.

Therefore internal local mutation does not automatically make a function impure.

---

# 80. Important Distinction

Do not memorize:

```text
Mutation = Always Impure
```

More accurately:

```text
Observable mutation of
external/shared/input state
=
Side Effect
```

A function can mutate private local data during computation while still behaving purely externally.

Example:

```javascript
function doubleAll(
    numbers
) {
    const result = [];

    for (
        const number
        of numbers
    ) {
        result.push(
            number * 2
        );
    }

    return result;
}
```

The input remains unchanged.

---

# 81. Pure Functions Can Throw Errors

Consider:

```javascript
function divide(a, b) {
    if (b === 0) {
        throw new Error(
            "Cannot divide by zero"
        );
    }

    return a / b;
}
```

For the same input:

```javascript
divide(10, 0);
```

the same error behavior occurs.

Throwing does not inherently make the function impure.

Purity is about deterministic behavior and observable side effects.

---

# 82. Pure Functions Can Return Objects

Example:

```javascript
function createUser(
    name,
    age
) {
    return {
        name,
        age
    };
}
```

Each call creates a different object reference:

```javascript
createUser(
    "Alice",
    25
);
```

But the returned values have equivalent content for the same inputs.

Purity does not require the same object reference.

---

# 83. Object Identity vs Value

```javascript
const a =
    createUser(
        "Alice",
        25
    );

const b =
    createUser(
        "Alice",
        25
    );
```

Then:

```javascript
a === b;
```

is:

```text
false
```

because they are separate objects.

But:

```text
a

and

b
```

contain equivalent data.

Pure functions may create new object references.

---

# 84. Pure Functions Can Call Pure Functions

```javascript
function subtotal(
    price,
    quantity
) {
    return (
        price *
        quantity
    );
}

function total(
    price,
    quantity,
    taxRate
) {
    const amount =
        subtotal(
            price,
            quantity
        );

    return (
        amount +
        amount * taxRate
    );
}
```

If:

```text
subtotal()
```

is pure and `total()` introduces no side effects or hidden dependencies, `total()` remains pure.

---

# 85. Calling an Impure Function

Consider:

```javascript
function calculate(value) {
    return (
        value +
        Math.random()
    );
}
```

Even though `calculate()` itself does not mutate anything, it calls:

```javascript
Math.random()
```

whose result changes independently of the input.

Therefore:

```javascript
calculate(10);
```

is not deterministic.

---

# 86. Pure vs Impure Summary

```text
PURE
================================

Same input
→ Same output

No observable side effects

No hidden mutable dependencies

Does not mutate inputs

Does not modify shared state


IMPURE
================================

May depend on:

Global mutable state
Current time
Randomness
Network
Database
External environment

May modify:

Inputs
Globals
DOM
Storage
Database
External systems
```

---

# 87. Side Effects Are Necessary

A real application needs to:

```text
Fetch data

Save data

Update UI

Log information

Send requests

Read files

Write files

Use browser storage

Send notifications
```

These are side effects.

Therefore the goal is not:

```text
100% Pure Application
```

Instead:

```text
Pure logic
+
Controlled side effects
```

---

# 88. Functional Core, Imperative Shell

Another name for this design idea is:

```text
Functional Core
Imperative Shell
```

Conceptually:

```text
┌─────────────────────────┐
│     Impure Shell        │
│                         │
│  API / DB / UI / Files  │
│          │              │
│          ▼              │
│   ┌───────────────┐     │
│   │  Pure Core    │     │
│   │               │     │
│   │ Business Logic│     │
│   │ Validation    │     │
│   │ Calculation   │     │
│   │ Transformation│     │
│   └───────────────┘     │
│          │              │
│          ▼              │
│     Side Effects        │
│                         │
└─────────────────────────┘
```

This is a useful architecture principle beyond JavaScript.

---

# 89. Common Mistake — Assuming `const` Means Immutable

```javascript
const user = {
    name: "Alice"
};
```

This prevents:

```javascript
user = {};
```

but does **not** prevent:

```javascript
user.name = "Bob";
```

`const` prevents reassignment of the variable binding.

It does not make the object immutable.

---

# 90. `Object.freeze()`

```javascript
const user =
    Object.freeze({
        name: "Alice"
    });
```

Now modifying the top-level property is prevented according to JavaScript's freeze semantics.

However:

```javascript
Object.freeze()
```

is shallow.

Nested objects are not automatically deeply frozen.

---

# 91. Common Mistake — Spread Means Deep Copy

Wrong assumption:

```javascript
const copy = {
    ...original
};
```

means:

```text
Everything deeply copied
```

It does not.

Spread creates a shallow copy.

Nested references remain shared unless copied separately.

---

# 92. Common Mistake — `map()` Means Pure

Wrong:

```javascript
users.map(
    user => {
        user.active = true;
        return user;
    }
);
```

`map()` creates a new array, but the callback mutated the existing objects.

Purity depends on the complete behavior, not the method name.

---

# 93. Common Mistake — Pure Means One-Line Function

This:

```javascript
const add =
    (a, b) =>
        a + b;
```

is pure.

But this can also be pure:

```javascript
function calculateOrder(
    items,
    discountRate,
    taxRate
) {
    const subtotal =
        items.reduce(
            (
                total,
                item
            ) =>
                total +
                item.price *
                item.quantity,
            0
        );

    const discount =
        subtotal *
        discountRate;

    const taxable =
        subtotal -
        discount;

    const tax =
        taxable *
        taxRate;

    return {
        subtotal,
        discount,
        tax,
        total:
            taxable + tax
    };
}
```

Purity has nothing to do with function length.

---

# 94. Common Mistake — Pure Means Arrow Function

Both can be pure:

```javascript
function add(a, b) {
    return a + b;
}
```

and:

```javascript
const add =
    (a, b) =>
        a + b;
```

Arrow functions are syntax.

Purity is behavior.

---

# 95. Common Mistake — Pure Means Functional Programming Only

Pure functions are important in functional programming, but they are useful everywhere:

```text
React

Node.js

Backend services

Validation

Calculations

Data transformations

Reducers

Business logic

Testing

Utilities
```

You can use pure functions without writing a fully functional-programming-style application.

---

# 96. Common Mistake — All External Reads Are Equal

Consider:

```javascript
const RATE = 0.18;

function calculate(price) {
    return price * RATE;
}
```

versus:

```javascript
let rate = 0.18;

function calculate(price) {
    return price * rate;
}
```

The second has mutable external state.

The first uses a stable constant.

When reasoning about purity, ask:

```text
Can this dependency change
without appearing in the
function inputs?
```

Explicit parameters are still often better for reusable business logic.

---

# 97. Common Mistake — Side Effects Are Bad

Side effects are not bad.

Uncontrolled, hidden, and scattered side effects are difficult to reason about.

A useful goal:

```text
Side Effects
      │
      ▼
Explicit
Controlled
Isolated
Easy to locate
```

---

# 98. Interview Question — What Is a Pure Function?

A pure function is a function that:

```text
Produces the same result
for the same inputs

AND

Has no observable side effects
```

---

# 99. Interview Question — What Is a Side Effect?

A side effect is an observable interaction with state or systems outside the function's returned value.

Examples:

```text
Mutating global state

Mutating inputs

Updating DOM

Network requests

Database writes

File writes

Logging

Storage changes
```

---

# 100. Interview Question — Why Are Pure Functions Useful?

Because they are generally:

```text
Predictable

Easy to test

Easy to debug

Reusable

Composable

Memoizable

Easy to reason about
```

---

# 101. Interview Question — Is `Math.random()` Pure?

No.

```javascript
Math.random();
```

can produce a different result each time without different explicit inputs.

Therefore it is nondeterministic.

---

# 102. Interview Question — Is `Date.now()` Pure?

No.

```javascript
Date.now();
```

depends on current time.

Its result changes even though no arguments are supplied.

---

# 103. Interview Question — Is `console.log()` a Side Effect?

Yes.

It performs observable output outside the function's returned value.

---

# 104. Interview Question — Is `map()` Pure?

The method itself returns a new array rather than mutating the source array.

But the overall operation is only pure if the callback is pure and does not mutate referenced data or interact with external state.

Example:

```javascript
numbers.map(
    number =>
        number * 2
);
```

can be pure.

This:

```javascript
numbers.map(
    number => {
        console.log(number);

        return number * 2;
    }
);
```

contains a side effect.

---

# 105. Interview Question — Is `sort()` Pure?

`Array.prototype.sort()` mutates the array.

```javascript
numbers.sort();
```

Therefore using it directly on an input array introduces mutation.

Use:

```javascript
numbers.toSorted();
```

or copy first:

```javascript
[...numbers].sort();
```

when you need an immutable transformation.

---

# 106. Interview Question — Does `const` Make Objects Immutable?

No.

```javascript
const user = {
    name: "Alice"
};

user.name = "Bob";
```

is valid.

`const` prevents reassignment of `user`, not mutation of the object.

---

# 107. Interview Question — Can Pure Functions Use Local Variables?

Yes.

```javascript
function calculate(a, b) {
    const total =
        a + b;

    return total * 2;
}
```

Local variables do not violate purity.

---

# 108. Interview Question — Can Pure Functions Create Objects?

Yes.

```javascript
function createUser(name) {
    return {
        name
    };
}
```

Creating new values is fine.

The function does not need to return the same object reference.

---

# 109. Interview Question — Can a Pure Function Throw?

Yes, if the thrown result is deterministic from its inputs and no side effect is introduced.

Example:

```javascript
function divide(a, b) {
    if (b === 0) {
        throw new Error(
            "Division by zero"
        );
    }

    return a / b;
}
```

---

# 110. Interview Question — Why Does React Care About Purity?

Predictable rendering depends on components calculating UI from their inputs without performing uncontrolled side effects during rendering.

Purity also helps with:

```text
State updates

Reducers

Memoization

Rendering optimization

Testing

Debugging
```

---

# 111. Output Question

```javascript
let value = 10;

function calculate(number) {
    return number + value;
}

console.log(
    calculate(5)
);

value = 20;

console.log(
    calculate(5)
);
```

Output:

```text
15
25
```

Same explicit input:

```text
5
```

Different output.

Therefore:

```javascript
calculate()
```

is impure because it depends on mutable external state.

---

# 112. Output Question

```javascript
function update(user) {
    return {
        ...user,
        active: true
    };
}

const user = {
    name: "Alice",
    active: false
};

const updated =
    update(user);

console.log(
    user.active
);

console.log(
    updated.active
);
```

Output:

```text
false
true
```

The original object was not mutated.

---

# 113. Output Question

```javascript
function addItem(
    items,
    item
) {
    items.push(item);

    return items;
}

const numbers = [
    1,
    2
];

const result =
    addItem(
        numbers,
        3
    );

console.log(numbers);
```

Output:

```javascript
[
    1,
    2,
    3
]
```

The input was modified.

Therefore the function is impure.

---

# 114. Output Question

```javascript
function addItem(
    items,
    item
) {
    return [
        ...items,
        item
    ];
}

const numbers = [
    1,
    2
];

const result =
    addItem(
        numbers,
        3
    );

console.log(numbers);

console.log(result);
```

Output:

```javascript
[
    1,
    2
]

[
    1,
    2,
    3
]
```

Original data remains unchanged.

---

# 115. Pure Function Checklist

Before calling a function pure, ask:

```text
1. Does the same input
   always produce the
   same output?

2. Does it avoid changing
   external state?

3. Does it avoid mutating
   its inputs?

4. Does it avoid depending
   on mutable external state?

5. Does it avoid external
   interactions?

6. Are its dependencies
   explicit?

7. Can I test it without
   setting up external systems?
```

If yes, it is likely pure.

---

# 116. Pure Functions Cheat Sheet

```text
PURE FUNCTION
================================

Same Input
→ Same Output

AND

No Side Effects


PURE
================================

function add(a, b) {
    return a + b;
}


IMPURE
================================

let value = 10;

function add(a) {
    return a + value;
}


SIDE EFFECTS
================================

Global mutation
Input mutation
DOM updates
Network requests
Database writes
File operations
Logging
Storage updates


DETERMINISTIC
================================

Output is determined
by inputs.


IMMUTABILITY
================================

Do not modify existing
shared/input data.

Create new values.


OBJECT UPDATE
================================

{
    ...object,
    property: newValue
}


ARRAY ADD
================================

[
    ...array,
    item
]


ARRAY REMOVE
================================

array.filter(...)


ARRAY UPDATE
================================

array.map(...)


SORT
================================

array.toSorted(...)

or

[...array].sort(...)


REVERSE
================================

array.toReversed()

or

[...array].reverse()


NESTED OBJECT
================================

{
    ...object,

    nested: {
        ...object.nested,
        value: newValue
    }
}


REFERENTIAL TRANSPARENCY
================================

Function call can be
replaced by its result
without changing behavior.


PURE CORE
================================

Input / External Systems
        │
        ▼
     Side Effect
        │
        ▼
     Pure Logic
        │
        ▼
     Side Effect
        │
        ▼
External System / UI


BENEFITS
================================

Predictable
Testable
Reusable
Composable
Debuggable
Memoizable
Explicit dependencies


REACT
================================

Props + State
     │
     ▼
Render Calculation
     │
     ▼
UI


REDUCER
================================

Old State
+
Action
   │
   ▼
Reducer
   │
   ▼
New State
```

---

# 117. Important Rules to Remember

```text
1. Pure functions produce the same output for the same inputs.

2. Pure functions have no observable side effects.

3. Both conditions are important.

4. Pure functions are deterministic.

5. Hidden mutable dependencies can break purity.

6. Global mutable variables can break purity.

7. Current time can break determinism.

8. Randomness can break determinism.

9. Network requests are side effects.

10. Database operations are side effects.

11. DOM updates are side effects.

12. Logging is technically a side effect.

13. File operations are side effects.

14. Browser storage operations are side effects.

15. Input mutation makes functions impure.

16. Shared-state mutation creates side effects.

17. Immutability means avoiding changes to existing shared/input data.

18. Immutable updates create new values.

19. Object spread can create shallow object copies.

20. Array spread can create shallow array copies.

21. Spread does not perform deep cloning.

22. Nested structures may need nested copies.

23. const does not make objects immutable.

24. Object.freeze() is shallow.

25. map() returns a new array.

26. filter() returns a new array.

27. sort() mutates the original array.

28. reverse() mutates the original array.

29. push() mutates the original array.

30. pop() mutates the original array.

31. splice() mutates the original array.

32. toSorted() returns a sorted copy.

33. toReversed() returns a reversed copy.

34. toSpliced() provides an immutable splice-like operation.

35. with() can replace an array element without mutating the original.

36. A non-mutating array method does not guarantee the callback is pure.

37. Pure functions can use local variables.

38. Pure functions can create objects.

39. Pure functions can create arrays.

40. Pure functions can call other pure functions.

41. Pure functions can throw deterministic errors.

42. Internal local mutation does not automatically break purity.

43. Observable external/shared mutation is the important issue.

44. Pure functions are easier to test.

45. Pure functions are easier to debug.

46. Pure functions are easier to reuse.

47. Pure functions are easier to compose.

48. Pure functions are natural candidates for memoization.

49. Memoizing impure functions may produce incorrect behavior.

50. Explicit dependencies improve predictability.

51. Passing time as input can make time-based logic pure.

52. Passing random values as input can make core logic deterministic.

53. Side effects are necessary in real applications.

54. Side effects are not inherently bad.

55. Hidden and uncontrolled side effects are harder to maintain.

56. Separate computation from side effects where practical.

57. Functional Core / Imperative Shell is a useful architecture pattern.

58. Business calculations are good candidates for pure functions.

59. Validation logic is a good candidate for pure functions.

60. Data transformations are good candidates for pure functions.

61. Persistence is inherently side-effecting.

62. Network communication is inherently side-effecting.

63. UI updates are side-effecting.

64. Referential transparency is strongly associated with pure functions.

65. Pure function calls can be reasoned about like values.

66. Pure functions reduce hidden coupling.

67. Pure functions reduce dependence on execution order.

68. Closures can be pure.

69. Closures over mutable state may be impure.

70. Higher-order functions can be pure.

71. Curried functions can be pure.

72. Arrow functions are not automatically pure.

73. Normal functions can be pure.

74. Function length has nothing to do with purity.

75. Functional programming does not own the concept of purity.

76. Pure functions are useful in ordinary JavaScript applications.

77. React rendering should remain predictable.

78. React state should generally be treated as immutable.

79. Avoid directly mutating React state.

80. Functional state updater functions should compute next state from previous state.

81. Reducers should avoid mutating existing state.

82. Reducers commonly return new state.

83. Pure render calculations improve predictability.

84. Side effects should not be performed arbitrarily during React rendering.

85. Event handlers are appropriate places for many user-triggered side effects.

86. Effects handle synchronization with external systems in React.

87. Pure functions make unit testing simpler.

88. Pure functions usually require less mocking.

89. Pure functions make refactoring safer.

90. Pure functions make business logic portable.

91. Do not assume const means immutable.

92. Do not assume spread means deep copy.

93. Do not assume map() means pure.

94. Do not assume arrow functions mean pure.

95. Do not assume all mutation is externally observable.

96. Do not assume side effects are bad.

97. Identify side effects explicitly.

98. Isolate side effects where useful.

99. Keep core calculations predictable.

100. Think: inputs in, output out.
```

---

# Pure Functions in One Sentence

> A pure function computes its result entirely from its inputs, produces the same result for the same inputs, and does not cause observable side effects.

---

# Final Mental Model

```text
PURE FUNCTION

Input
  │
  ▼
┌─────────────────┐
│                 │
│    Function     │
│                 │
└────────┬────────┘
         │
         ▼
       Output


Same Input
    │
    ▼
Same Output


Nothing outside
is unexpectedly changed.
```

Compare with:

```text
IMPURE FUNCTION

       Global State
            │
            ▼
Input ──► Function ──► Output
            │
            ├──► DOM
            │
            ├──► Database
            │
            ├──► Network
            │
            └──► Shared State
```

The most useful principle to remember:

```text
PURE LOGIC
======================

Data In
   │
   ▼
Calculate
   │
   ▼
Data Out


SIDE EFFECTS
======================

External World
   │
   ▼
Read / Write / Update
```

Real applications need both.

Design them so you know **which is which**.
