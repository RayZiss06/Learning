# Rest Syntax / Rest Parameters (`...`)

The **rest syntax** uses three dots:

```javascript
...
```

Rest syntax allows JavaScript to **collect multiple values into a single variable**.

The most common use is with function parameters:

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

Here:

```javascript
...numbers
```

collects all supplied arguments into an array called `numbers`.

The same `...` syntax is also used for **spread syntax**, but the meaning depends on where it appears.

```text
Spread → expands values
Rest   → collects values
```

---

# 1. Basic Rest Parameter

Consider:

```javascript
function showNumbers(...numbers) {
    console.log(numbers);
}

showNumbers(10, 20, 30);
```

Output:

```javascript
[10, 20, 30]
```

Conceptually:

```text
10, 20, 30
     ↓
...numbers
     ↓
[10, 20, 30]
```

Rest collects individual arguments into an array.

---

# 2. Why Rest Parameters Are Useful

Suppose we write:

```javascript
function add(a, b) {
    return a + b;
}
```

This function explicitly defines two parameters.

But what if we want to support:

```javascript
add(10);
add(10, 20);
add(10, 20, 30);
add(10, 20, 30, 40, 50);
```

We can use:

```javascript
function add(...numbers) {
    // ...
}
```

Now the function can accept any number of arguments.

---

# 3. Rest Parameters Create an Array

Example:

```javascript
function show(...values) {
    console.log(values);
    console.log(Array.isArray(values));
}

show(10, 20, 30);
```

Output:

```text
[10, 20, 30]
true
```

This is important:

```text
Rest parameters create a real Array.
```

Therefore array methods can be used directly.

---

# 4. Using Array Methods with Rest

Example:

```javascript
function double(...numbers) {
    return numbers.map(
        (number) => number * 2
    );
}

console.log(
    double(1, 2, 3)
);
```

Output:

```javascript
[2, 4, 6]
```

Because:

```javascript
numbers
```

is a normal array.

---

# 5. Sum Using Rest Parameters

Example:

```javascript
function sum(...numbers) {
    return numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
}
```

Call:

```javascript
console.log(
    sum(10, 20, 30, 40)
);
```

Output:

```text
100
```

Flow:

```text
sum(10, 20, 30, 40)
          ↓
...numbers
          ↓
[10, 20, 30, 40]
          ↓
reduce()
          ↓
100
```

---

# 6. Rest with Regular Parameters

Rest parameters can be used after normal parameters.

Example:

```javascript
function greet(
    greeting,
    ...names
) {
    console.log(greeting);
    console.log(names);
}
```

Call:

```javascript
greet(
    "Hello",
    "John",
    "Alice",
    "Bob"
);
```

Output:

```text
Hello
```

and:

```javascript
["John", "Alice", "Bob"]
```

Here:

```javascript
greeting
```

receives:

```text
Hello
```

and:

```javascript
...names
```

collects everything remaining.

---

# 7. Multiple Regular Parameters Before Rest

Example:

```javascript
function createTeam(
    teamName,
    leader,
    ...members
) {
    console.log(teamName);
    console.log(leader);
    console.log(members);
}
```

Call:

```javascript
createTeam(
    "Alpha",
    "John",
    "Alice",
    "Bob",
    "David"
);
```

Values:

```text
teamName → "Alpha"

leader → "John"

members → [
    "Alice",
    "Bob",
    "David"
]
```

---

# 8. Rest Parameter Must Be Last

This is valid:

```javascript
function test(
    first,
    ...remaining
) {
}
```

This is invalid:

```javascript
function test(
    ...remaining,
    last
) {
}
```

Why?

Because rest means:

```text
Collect everything remaining.
```

If another parameter existed after it, JavaScript would not know which argument should belong to that parameter.

---

# 9. Only One Rest Parameter

This is invalid:

```javascript
function test(
    ...first,
    ...second
) {
}
```

A parameter list can contain only one rest parameter, and it must appear last.

Correct:

```javascript
function test(
    first,
    ...remaining
) {
}
```

---

# 10. Rest Parameter with Zero Remaining Arguments

Example:

```javascript
function test(
    first,
    ...remaining
) {
    console.log(first);
    console.log(remaining);
}

test("John");
```

Output:

```text
John
```

and:

```javascript
[]
```

If there are no remaining arguments, rest produces an empty array.

---

# 11. Rest with Arrow Functions

Rest parameters work with arrow functions.

Example:

```javascript
const sum = (...numbers) => {
    return numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
};
```

Call:

```javascript
console.log(
    sum(10, 20, 30)
);
```

Output:

```text
60
```

---

# 12. Short Arrow Function with Rest

Example:

```javascript
const sum = (...numbers) =>
    numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
```

Call:

```javascript
sum(1, 2, 3, 4);
```

Result:

```text
10
```

This pattern is common in modern JavaScript.

---

# 13. Rest vs `arguments`

Before rest parameters, JavaScript functions could access supplied arguments using:

```javascript
arguments
```

Example:

```javascript
function show() {
    console.log(arguments);
}

show(10, 20, 30);
```

`arguments` contains the values passed to the function.

However:

```javascript
arguments
```

is **not a real Array**.

---

# 14. `arguments` Is Array-Like

Example:

```javascript
function show() {
    console.log(
        Array.isArray(arguments)
    );
}

show(10, 20, 30);
```

Output:

```text
false
```

`arguments` has indexed values and a `length`, but it is not an Array.

Therefore you cannot directly rely on normal array methods such as:

```javascript
arguments.map(...)
```

Rest parameters avoid this problem.

---

# 15. Rest Is a Real Array

Example:

```javascript
function show(...args) {
    console.log(
        Array.isArray(args)
    );
}

show(10, 20, 30);
```

Output:

```text
true
```

Therefore:

```javascript
args.map(...)
args.filter(...)
args.reduce(...)
args.find(...)
```

can be used directly.

---

# 16. Rest vs `arguments` Example

Using `arguments`:

```javascript
function double() {
    const numbers =
        Array.from(arguments);

    return numbers.map(
        (number) => number * 2
    );
}
```

Using rest:

```javascript
function double(...numbers) {
    return numbers.map(
        (number) => number * 2
    );
}
```

Rest parameters provide cleaner syntax.

---

# 17. Rest Works with Arrow Functions

Arrow functions do not create their own `arguments` object.

So this:

```javascript
const show = () => {
    console.log(arguments);
};
```

does not give the arrow function its own arguments.

Instead:

```javascript
const show = (...args) => {
    console.log(args);
};
```

Call:

```javascript
show(10, 20, 30);
```

Output:

```javascript
[10, 20, 30]
```

This is especially important with arrow functions.

---

# 18. Rest Parameters Can Have Names

The variable does not need to be called:

```javascript
args
```

or:

```javascript
numbers
```

Any valid identifier works.

Example:

```javascript
function greet(...people) {
    console.log(people);
}
```

Or:

```javascript
function collect(...items) {
    console.log(items);
}
```

The `...` gives the parameter rest behavior.

---

# 19. Rest with Different Data Types

Example:

```javascript
function collect(...values) {
    console.log(values);
}

collect(
    10,
    "Hello",
    true,
    null,
    {
        name: "John"
    }
);
```

Result:

```javascript
[
    10,
    "Hello",
    true,
    null,
    {
        name: "John"
    }
]
```

Rest can collect values of any type.

---

# 20. Rest with Array Destructuring

Rest syntax can also be used during array destructuring.

Example:

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const [
    first,
    ...remaining
] = numbers;
```

Now:

```javascript
console.log(first);
```

Output:

```text
10
```

And:

```javascript
console.log(remaining);
```

Output:

```javascript
[20, 30, 40]
```

---

# 21. Array Rest Mental Model

Example:

```javascript
const [
    first,
    ...rest
] = [
    1,
    2,
    3,
    4
];
```

Think:

```text
[1, 2, 3, 4]
 │  └─────┬─────┘
 ▼        ▼
first    rest

first = 1

rest = [
    2,
    3,
    4
]
```

Rest collects the remaining array elements.

---

# 22. Skipping Values with Rest

Example:

```javascript
const numbers = [
    10,
    20,
    30,
    40,
    50
];

const [
    first,
    ,
    ...remaining
] = numbers;
```

Result:

```text
first = 10
```

The second value:

```text
20
```

is skipped.

Then:

```javascript
remaining
```

becomes:

```javascript
[30, 40, 50]
```

---

# 23. Rest Must Be Last in Array Destructuring

Valid:

```javascript
const [
    first,
    ...remaining
] = numbers;
```

Invalid:

```javascript
const [
    ...remaining,
    last
] = numbers;
```

Again:

```text
Rest means collect everything remaining.
```

Therefore it must appear at the end of the destructuring pattern.

---

# 24. Object Rest

Rest syntax can also collect remaining object properties.

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...remaining
} = user;
```

Now:

```javascript
console.log(name);
```

Output:

```text
John
```

And:

```javascript
console.log(remaining);
```

Output:

```javascript
{
    age: 25,
    role: "Admin"
}
```

---

# 25. Object Rest Mental Model

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...rest
} = user;
```

Think:

```text
user
│
├── name: "John" ──→ name
│
├── age: 25 ───────┐
│                  │
└── role: "Admin" ─┤
                   ▼
                  rest
```

Result:

```javascript
name = "John"
```

and:

```javascript
rest = {
    age: 25,
    role: "Admin"
}
```

---

# 26. Removing a Property Using Object Rest

A common pattern is to exclude a property.

Example:

```javascript
const user = {
    id: 1,
    name: "John",
    password: "secret"
};

const {
    password,
    ...safeUser
} = user;
```

Result:

```javascript
safeUser = {
    id: 1,
    name: "John"
}
```

The `password` property was extracted separately, and the remaining properties were collected into `safeUser`.

---

# 27. Excluding Multiple Properties

Example:

```javascript
const user = {
    id: 1,
    name: "John",
    password: "secret",
    token: "abc123",
    role: "Admin"
};

const {
    password,
    token,
    ...publicUser
} = user;
```

Result:

```javascript
publicUser = {
    id: 1,
    name: "John",
    role: "Admin"
}
```

This pattern is useful when constructing a new object that omits selected properties.

---

# 28. Object Rest Creates a New Object

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...rest
} = user;

console.log(
    user === rest
);
```

Output:

```text
false
```

`rest` is a new object containing the remaining properties.

---

# 29. Object Rest Is Shallow

Like object spread, object rest does not deeply clone nested objects.

Example:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    },

    role: "Admin"
};

const {
    name,
    ...rest
} = user;
```

Check:

```javascript
console.log(
    user.address === rest.address
);
```

Output:

```text
true
```

The nested object is still shared.

---

# 30. Rest in Function Destructuring

Suppose:

```javascript
function showUser({
    name,
    ...details
}) {
    console.log(name);
    console.log(details);
}
```

Call:

```javascript
showUser({
    name: "John",
    age: 25,
    role: "Admin"
});
```

Output:

```text
John
```

and:

```javascript
{
    age: 25,
    role: "Admin"
}
```

The object parameter is destructured, and rest collects the remaining properties.

---

# 31. Array Destructuring in Function Parameters

Example:

```javascript
function process([
    first,
    ...remaining
]) {
    console.log(first);
    console.log(remaining);
}
```

Call:

```javascript
process([
    10,
    20,
    30,
    40
]);
```

Output:

```text
10
```

and:

```javascript
[20, 30, 40]
```

---

# 32. Rest and Spread Together

Rest and spread are often used together.

Example:

```javascript
function double(...numbers) {
    const doubled =
        numbers.map(
            (number) =>
                number * 2
        );

    console.log(...doubled);
}
```

Call:

```javascript
double(1, 2, 3);
```

Flow:

```text
1, 2, 3
   ↓
REST
   ↓
[1, 2, 3]
   ↓
map()
   ↓
[2, 4, 6]
   ↓
SPREAD
   ↓
2, 4, 6
```

Output:

```text
2 4 6
```

---

# 33. Identifying Rest vs Spread

Consider:

```javascript
function sum(...numbers) {
}
```

This is:

```text
REST
```

because values are being collected.

Consider:

```javascript
sum(...numbers);
```

This is:

```text
SPREAD
```

because values are being expanded.

Same syntax:

```javascript
...
```

Different context.

---

# 34. Rest in Parameter vs Spread in Argument

This distinction is extremely important.

Function definition:

```javascript
function sum(...numbers) {
}
```

`...numbers` is:

```text
REST PARAMETER
```

Function call:

```javascript
sum(...numbers);
```

`...numbers` is:

```text
SPREAD ARGUMENT
```

Remember:

```text
Definition → Rest

Call → Spread
```

in this particular pattern.

---

# 35. Example Combining Both

```javascript
function sum(...numbers) {
    return numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
}

const values = [
    10,
    20,
    30
];

console.log(
    sum(...values)
);
```

Here:

```javascript
sum(...values)
```

uses **spread**.

```javascript
function sum(...numbers)
```

uses **rest**.

Flow:

```text
values
↓
[10, 20, 30]

SPREAD
↓
sum(10, 20, 30)

REST
↓
numbers = [10, 20, 30]

reduce()
↓
60
```

---

# 36. Rest with `filter()`

Example:

```javascript
const getEvenNumbers =
    (...numbers) =>
        numbers.filter(
            (number) =>
                number % 2 === 0
        );
```

Call:

```javascript
console.log(
    getEvenNumbers(
        1,
        2,
        3,
        4,
        5,
        6
    )
);
```

Output:

```javascript
[2, 4, 6]
```

---

# 37. Rest with `map()`

Example:

```javascript
const square =
    (...numbers) =>
        numbers.map(
            (number) =>
                number ** 2
        );
```

Call:

```javascript
square(2, 3, 4);
```

Result:

```javascript
[4, 9, 16]
```

---

# 38. Rest with `reduce()`

Example:

```javascript
const multiply =
    (...numbers) =>
        numbers.reduce(
            (total, number) =>
                total * number,
            1
        );
```

Call:

```javascript
multiply(2, 3, 4);
```

Result:

```text
24
```

---

# 39. Rest with `Math.max()`

Example:

```javascript
function maximum(...numbers) {
    return Math.max(
        ...numbers
    );
}
```

Call:

```javascript
maximum(
    10,
    50,
    20
);
```

Flow:

```text
10, 50, 20
     ↓
REST
     ↓
[10, 50, 20]
     ↓
SPREAD
     ↓
Math.max(10, 50, 20)
     ↓
50
```

Rest and spread are both being used.

---

# 40. Rest with Required Parameters

Suppose we want the first value to have a special meaning.

```javascript
function calculate(
    operation,
    ...numbers
) {
    if (operation === "sum") {
        return numbers.reduce(
            (total, number) =>
                total + number,
            0
        );
    }
}
```

Call:

```javascript
calculate(
    "sum",
    10,
    20,
    30
);
```

Result:

```text
60
```

Here:

```text
operation → "sum"

numbers → [10, 20, 30]
```

---

# 41. Rest with Default Parameters

Normal parameters before rest can have default values.

Example:

```javascript
function greet(
    greeting = "Hello",
    ...names
) {
    return names.map(
        (name) =>
            `${greeting} ${name}`
    );
}
```

Call:

```javascript
greet(
    undefined,
    "John",
    "Alice"
);
```

Result:

```javascript
[
    "Hello John",
    "Hello Alice"
]
```

The rest parameter itself cannot have a default initializer such as:

```javascript
function test(
    ...args = []
) {
}
```

That is invalid.

Rest already becomes an empty array when nothing remains.

---

# 42. Rest Parameter Cannot Have Default Value

Invalid:

```javascript
function test(
    ...args = []
) {
}
```

Rest parameters cannot have default initializers.

There is no need because:

```javascript
function test(...args) {
    console.log(args);
}

test();
```

Output:

```javascript
[]
```

---

# 43. Rest Parameter Cannot Have a Trailing Comma

This is invalid:

```javascript
function test(
    ...args,
) {
}
```

Because the rest parameter must be the final parameter and cannot be followed by a trailing comma.

Correct:

```javascript
function test(
    ...args
) {
}
```

---

# 44. Rest and Function `length`

A function's `length` property counts parameters before the first parameter with certain special forms such as defaults or rest.

Example:

```javascript
function test(
    a,
    b,
    ...rest
) {
}
```

Check:

```javascript
console.log(
    test.length
);
```

Output:

```text
2
```

The rest parameter is not included.

This is a more advanced detail but can appear in JavaScript interviews.

---

# 45. Rest vs `arguments` — Arrow Functions

Regular function:

```javascript
function show() {
    console.log(arguments);
}
```

Arrow function:

```javascript
const show = () => {
    // no own arguments object
};
```

Rest works with both:

```javascript
function regular(...args) {
    console.log(args);
}

const arrow = (...args) => {
    console.log(args);
};
```

Therefore rest parameters are generally preferred in modern JavaScript when you want a variable number of arguments.

---

# 46. Rest Makes Intent Explicit

Compare:

```javascript
function sum() {
    console.log(arguments);
}
```

with:

```javascript
function sum(...numbers) {
    console.log(numbers);
}
```

The second version immediately tells the reader:

```text
This function accepts any number
of values and collects them into numbers.
```

That improves readability.

---

# 47. Rest with Callback Functions

Example:

```javascript
function execute(
    callback,
    ...values
) {
    return callback(...values);
}
```

Call:

```javascript
const add = (
    a,
    b,
    c
) => a + b + c;

const result = execute(
    add,
    10,
    20,
    30
);
```

Result:

```text
60
```

Flow:

```text
execute(add, 10, 20, 30)
             │
             ▼
values = [10, 20, 30]
             │
             ▼
callback(...values)
             │
             ▼
add(10, 20, 30)
             │
             ▼
60
```

---

# 48. Rest in React Component Props

Rest syntax can be used when destructuring component props.

Example:

```jsx
function Button({
    children,
    ...props
}) {
    return (
        <button {...props}>
            {children}
        </button>
    );
}
```

Usage:

```jsx
<Button
    type="button"
    disabled={false}
    className="primary"
>
    Save
</Button>
```

Inside `Button`:

```text
children
→ "Save"

props
→ {
    type: "button",
    disabled: false,
    className: "primary"
}
```

Then:

```jsx
<button {...props}>
```

uses **spread** to pass those properties to the button.

---

# 49. Rest and Spread in React Props

Example:

```jsx
function Input({
    label,
    ...inputProps
}) {
    return (
        <label>
            {label}

            <input
                {...inputProps}
            />
        </label>
    );
}
```

Usage:

```jsx
<Input
    label="Email"
    type="email"
    name="email"
    required
/>
```

Rest:

```javascript
...inputProps
```

during destructuring collects:

```javascript
{
    type: "email",
    name: "email",
    required: true
}
```

Spread:

```jsx
<input {...inputProps} />
```

expands them into props.

---

# 50. Rest in React Event Helpers

Example:

```javascript
const logValues =
    (...values) => {
        console.log(values);
    };
```

Call:

```javascript
logValues(
    "click",
    10,
    true
);
```

Result:

```javascript
[
    "click",
    10,
    true
]
```

Rest is useful for utility functions that accept variable numbers of values.

---

# 51. Rest in Utility Functions

Example:

```javascript
const joinStrings =
    (separator, ...strings) =>
        strings.join(separator);
```

Call:

```javascript
joinStrings(
    " - ",
    "JavaScript",
    "React",
    "Node.js"
);
```

Result:

```text
JavaScript - React - Node.js
```

Here:

```text
separator → " - "

strings → [
    "JavaScript",
    "React",
    "Node.js"
]
```

---

# 52. Rest in Logging Functions

Example:

```javascript
function log(
    level,
    ...messages
) {
    console.log(
        `[${level}]`,
        ...messages
    );
}
```

Call:

```javascript
log(
    "INFO",
    "User",
    10,
    "logged in"
);
```

Output:

```text
[INFO] User 10 logged in
```

Rest collects the messages.

Spread sends them to `console.log()` as separate arguments.

---

# 53. Rest with Destructured Object Properties

Example:

```javascript
const response = {
    status: 200,
    data: {
        name: "John"
    },
    cached: false,
    duration: 120
};

const {
    data,
    ...metadata
} = response;
```

Now:

```javascript
data
```

is:

```javascript
{
    name: "John"
}
```

And:

```javascript
metadata
```

is:

```javascript
{
    status: 200,
    cached: false,
    duration: 120
}
```

This can be useful when separating one important property from the rest.

---

# 54. Rest Does Not Mutate the Original Array

Example:

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const [
    first,
    ...rest
] = numbers;
```

Original:

```javascript
console.log(numbers);
```

Output:

```javascript
[1, 2, 3, 4]
```

Rest destructuring does not remove values from the original array.

---

# 55. Rest Does Not Mutate the Original Object

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    role,
    ...rest
} = user;
```

Original:

```javascript
console.log(user);
```

still contains:

```javascript
{
    name: "John",
    age: 25,
    role: "Admin"
}
```

Rest creates a new object containing the remaining properties.

---

# 56. Rest Destructuring Is Not `delete`

This:

```javascript
const {
    password,
    ...safeUser
} = user;
```

does not delete:

```javascript
password
```

from `user`.

It creates another object without that selected property.

Compare:

```javascript
delete user.password;
```

which modifies the original object.

These are different operations.

---

# 57. Common Mistake — Rest Not Last

Wrong:

```javascript
function test(
    ...args,
    value
) {
}
```

Correct:

```javascript
function test(
    value,
    ...args
) {
}
```

Rest must be last.

---

# 58. Common Mistake — Multiple Rest Parameters

Wrong:

```javascript
function test(
    ...first,
    ...second
) {
}
```

Correct:

```javascript
function test(
    first,
    ...remaining
) {
}
```

Only one rest parameter can appear in the parameter list.

---

# 59. Common Mistake — Confusing Rest and Spread

Example:

```javascript
function sum(...numbers) {
}
```

This is:

```text
REST
```

because it collects arguments.

Example:

```javascript
sum(...numbers);
```

This is:

```text
SPREAD
```

because it expands an iterable into arguments.

Do not identify `...` based only on the symbol.

Look at what it is doing.

---

# 60. Common Mistake — Thinking Rest Is Array-Only

Rest parameters create arrays:

```javascript
function test(...args) {
}
```

Array destructuring rest creates an array:

```javascript
const [
    first,
    ...rest
] = array;
```

But object destructuring rest creates an object:

```javascript
const {
    name,
    ...rest
} = object;
```

So the result depends on the context.

---

# 61. Common Mistake — Expecting Object Rest to Deep Clone

Example:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};

const {
    name,
    ...rest
} = user;
```

This:

```javascript
rest.address
```

still references the same nested address object.

Object rest performs shallow property copying.

---

# 62. Common Mistake — Modifying Nested Rest Data

Example:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};

const {
    name,
    ...rest
} = user;

rest.address.city =
    "Chicago";
```

Now:

```javascript
console.log(
    user.address.city
);
```

Output:

```text
Chicago
```

because the nested object is shared.

---

# 63. Common Mistake — Using `arguments` in Arrow Functions

Do not rely on:

```javascript
const sum = () => {
    console.log(arguments);
};
```

as the arrow's argument list.

Instead:

```javascript
const sum = (...numbers) => {
    console.log(numbers);
};
```

Arrow functions do not create their own `arguments` object.

---

# 64. Interview Question 1

What is the output?

```javascript
function show(...args) {
    console.log(args);
}

show(1, 2, 3);
```

Answer:

```javascript
[1, 2, 3]
```

---

# 65. Interview Question 2

What is the output?

```javascript
function show(
    first,
    ...rest
) {
    console.log(first);
    console.log(rest);
}

show(
    10,
    20,
    30,
    40
);
```

Answer:

```text
10
```

and:

```javascript
[20, 30, 40]
```

---

# 66. Interview Question 3

Is this valid?

```javascript
function test(
    ...args,
    value
) {
}
```

Answer:

```text
No.
```

A rest parameter must be the last parameter.

---

# 67. Interview Question 4

What is the output?

```javascript
const [
    first,
    ...rest
] = [
    1,
    2,
    3,
    4
];

console.log(rest);
```

Answer:

```javascript
[2, 3, 4]
```

---

# 68. Interview Question 5

What is the output?

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...rest
} = user;

console.log(rest);
```

Answer:

```javascript
{
    age: 25,
    role: "Admin"
}
```

---

# 69. Interview Question 6

What is the output?

```javascript
function test(...args) {
    console.log(
        Array.isArray(args)
    );
}

test(1, 2, 3);
```

Answer:

```text
true
```

Rest parameters create a real array.

---

# 70. Interview Question 7

What is the output?

```javascript
function test() {
    console.log(
        Array.isArray(arguments)
    );
}

test(1, 2, 3);
```

Answer:

```text
false
```

`arguments` is array-like, not a real Array.

---

# 71. Interview Question 8

Which `...` is rest and which is spread?

```javascript
function sum(...numbers) {
    return numbers.reduce(
        (a, b) => a + b,
        0
    );
}

const values = [
    10,
    20,
    30
];

sum(...values);
```

Answer:

```javascript
function sum(...numbers)
```

uses:

```text
REST
```

while:

```javascript
sum(...values)
```

uses:

```text
SPREAD
```

---

# 72. Interview Question 9

What is the output?

```javascript
function test(
    first,
    ...rest
) {
    console.log(rest);
}

test(10);
```

Answer:

```javascript
[]
```

There are no remaining arguments.

---

# 73. Interview Question 10

What is the output?

```javascript
const {
    password,
    ...user
} = {
    name: "John",
    age: 25,
    password: "secret"
};

console.log(user);
```

Answer:

```javascript
{
    name: "John",
    age: 25
}
```

---

# 74. Interview Question 11

Does this remove `password` from the original object?

```javascript
const {
    password,
    ...safeUser
} = user;
```

Answer:

```text
No.
```

It creates a new object containing the remaining properties.

The original object is not modified.

---

# 75. Interview Question 12

What is the output?

```javascript
const original = {
    nested: {
        value: 10
    },
    name: "John"
};

const {
    name,
    ...rest
} = original;

console.log(
    original.nested ===
        rest.nested
);
```

Answer:

```text
true
```

Object rest performs shallow copying.

---

# 76. Interview Question 13

What is the output?

```javascript
const sum =
    (...numbers) =>
        numbers.reduce(
            (total, number) =>
                total + number,
            0
        );

console.log(
    sum(1, 2, 3, 4)
);
```

Answer:

```text
10
```

---

# 77. Interview Question 14

What is the output?

```javascript
function test(
    a,
    b,
    ...rest
) {
}

console.log(
    test.length
);
```

Answer:

```text
2
```

The rest parameter is not counted in the function's `length`.

---

# 78. Interview Question 15

Can a rest parameter have a default value?

Example:

```javascript
function test(
    ...args = []
) {
}
```

Answer:

```text
No.
```

Rest parameters cannot have default initializers.

If no arguments remain, the rest parameter already becomes:

```javascript
[]
```

---

# 79. Rest vs Spread Comparison

| Feature                       | Rest     | Spread               |
| ----------------------------- | -------- | -------------------- |
| Syntax                        | `...`    | `...`                |
| Main purpose                  | Collect  | Expand               |
| Function definition           | ✅        | ❌                    |
| Function call                 | ❌        | ✅                    |
| Array destructuring           | ✅        | ❌                    |
| Object destructuring          | ✅        | ❌                    |
| Array literal                 | ❌        | ✅                    |
| Object literal                | ❌        | ✅                    |
| Variable arguments            | Collects | Expands              |
| Result in function parameters | Array    | Individual arguments |

The key distinction is:

```text
Rest takes many values and collects them.

Spread takes a collection and expands it.
```

---

# 80. Rest Mental Model

Function:

```javascript
function sum(...numbers) {
}
```

Think:

```text
sum(10, 20, 30)
       │
       ▼
10, 20, 30
       │
       │ REST
       ▼
[10, 20, 30]
       │
       ▼
numbers
```

Array destructuring:

```javascript
const [
    first,
    ...rest
] = numbers;
```

Think:

```text
[1, 2, 3, 4]
 │  └────┬────┘
 ▼       ▼
first   rest

first = 1

rest = [2, 3, 4]
```

Object destructuring:

```javascript
const {
    name,
    ...rest
} = user;
```

Think:

```text
{
    name,
    age,
    role
}
   │
   ▼

name → name

age + role
    ↓
   rest
```

---

# 81. Spread and Rest Mental Model

The easiest way to remember them:

```text
REST

10, 20, 30
     ↓
     ...
     ↓
[10, 20, 30]

Many → One container
```

```text
SPREAD

[10, 20, 30]
     ↓
     ...
     ↓
10, 20, 30

One container → Many values
```

Therefore:

```text
REST = Collect

SPREAD = Expand
```

---

# 82. Rest Cheat Sheet

### Function Rest Parameter

```javascript
function test(...args) {
}
```

### Arrow Function

```javascript
const test =
    (...args) => {
    };
```

### Regular + Rest Parameters

```javascript
function test(
    first,
    ...rest
) {
}
```

### Array Destructuring

```javascript
const [
    first,
    ...rest
] = array;
```

### Object Destructuring

```javascript
const {
    property,
    ...rest
} = object;
```

### Exclude Property

```javascript
const {
    password,
    ...safeUser
} = user;
```

### Rest + Spread

```javascript
function test(...args) {
    console.log(...args);
}
```

---

# 83. Important Rules to Remember

```text
1. Rest syntax uses ...

2. Rest collects multiple values.

3. Rest parameters collect function arguments into an array.

4. Rest parameters create real arrays.

5. Array methods can be used directly on rest parameters.

6. Rest allows functions to accept variable numbers of arguments.

7. Normal parameters can appear before a rest parameter.

8. A rest parameter must be last.

9. Only one rest parameter can appear in a parameter list.

10. A rest parameter cannot have a default initializer.

11. If nothing remains, a rest parameter becomes [].

12. Rest works with regular functions.

13. Rest works with arrow functions.

14. Arrow functions do not create their own arguments object.

15. Rest is generally preferred over arguments for variable arguments.

16. arguments is array-like but is not an Array.

17. Rest can be used in array destructuring.

18. Array rest collects remaining elements into an array.

19. Rest can be used in object destructuring.

20. Object rest collects remaining properties into a new object.

21. Object rest performs shallow copying.

22. Nested objects may still be shared.

23. Rest destructuring does not mutate the original object or array.

24. Object rest can be used to create an object excluding selected properties.

25. Excluding a property using rest does not delete it from the original object.

26. Rest and spread use the same ... syntax.

27. Rest collects.

28. Spread expands.

29. The meaning of ... depends on its context.

30. In function definitions, ...args is a rest parameter.

31. In function calls, ...array is spread syntax.

32. Rest and spread are frequently used together.

33. Rest syntax is heavily used with destructuring.

34. Rest/spread patterns are common in React props and state-related code.

35. Always think about whether ... is collecting or expanding values.
```

---

# 84. Most Important Concept

If you remember only one thing:

```text
REST = COLLECT
SPREAD = EXPAND
```

Example:

```javascript
function sum(...numbers) {
    return numbers.reduce(
        (a, b) => a + b,
        0
    );
}

const values = [
    10,
    20,
    30
];

sum(...values);
```

Flow:

```text
values
│
▼
[10, 20, 30]
│
│ SPREAD
▼
10, 20, 30
│
▼
sum(10, 20, 30)
│
│ REST
▼
numbers = [10, 20, 30]
│
▼
60
```

Same symbol:

```javascript
...
```

Opposite jobs:

```text
Spread → Container → Values

Rest → Values → Container
```

---

# 85. Rest Syntax in One Sentence

> Rest syntax (`...`) collects multiple remaining values into a single array or object, making it useful for variable function arguments, destructuring, and modern JavaScript patterns.
