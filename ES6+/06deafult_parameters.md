#  Default Parameters

**Default parameters** allow a function parameter to automatically receive a predefined value when the caller does not provide a value or explicitly passes `undefined`.

Basic syntax:

```javascript
function greet(name = "Guest") {
    console.log(`Hello ${name}`);
}
```

Call:

```javascript
greet();
```

Output:

```text
Hello Guest
```

Call:

```javascript
greet("John");
```

Output:

```text
Hello John
```

The main idea:

```text
Argument provided
      ↓
Use argument

Argument missing / undefined
      ↓
Use default value
```

---

# 1. Why Default Parameters Exist

Consider:

```javascript
function greet(name) {
    console.log(`Hello ${name}`);
}

greet();
```

Output:

```text
Hello undefined
```

Because no argument was provided:

```text
name → undefined
```

With a default parameter:

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

---

# 2. Basic Syntax

```javascript
function functionName(
    parameter = defaultValue
) {
}
```

Example:

```javascript
function greet(name = "Guest") {
    return `Hello ${name}`;
}
```

Calls:

```javascript
greet();
```

Result:

```text
"Hello Guest"
```

And:

```javascript
greet("Alice");
```

Result:

```text
"Hello Alice"
```

---

# 3. Default Is Used When Argument Is Missing

Example:

```javascript
function multiply(
    number,
    multiplier = 2
) {
    return number * multiplier;
}
```

Call:

```javascript
multiply(10);
```

Result:

```text
20
```

Because:

```text
number     → 10
multiplier → 2
```

---

# 4. Provided Argument Overrides Default

Example:

```javascript
function multiply(
    number,
    multiplier = 2
) {
    return number * multiplier;
}
```

Call:

```javascript
multiply(10, 5);
```

Result:

```text
50
```

The supplied `5` replaces the default `2`.

---

# 5. Explicit `undefined` Uses Default

Example:

```javascript
function greet(name = "Guest") {
    console.log(name);
}

greet(undefined);
```

Output:

```text
Guest
```

Passing:

```javascript
undefined
```

causes the default parameter to be used.

---

# 6. `null` Does NOT Use Default

Example:

```javascript
function greet(name = "Guest") {
    console.log(name);
}

greet(null);
```

Output:

```text
null
```

This is extremely important:

```text
undefined → Default used

null → Default NOT used
```

`null` is an explicitly supplied value.

---

# 7. Other Falsy Values Do Not Trigger Defaults

Default parameters do not depend on truthiness.

Example:

```javascript
function show(value = "Default") {
    console.log(value);
}
```

Calls:

```javascript
show(0);
show("");
show(false);
show(null);
show(undefined);
```

Outputs:

```text
0

false
null
Default
```

Only `undefined` triggers the default.

Values such as:

```text
0
""
false
null
NaN
```

are still values and therefore do not cause the default to be used.

---

# 8. Multiple Default Parameters

Example:

```javascript
function createUser(
    name = "Guest",
    age = 18,
    role = "User"
) {
    console.log(
        name,
        age,
        role
    );
}
```

Call:

```javascript
createUser();
```

Output:

```text
Guest 18 User
```

All parameters use their defaults.

---

# 9. Overriding Multiple Defaults

```javascript
createUser(
    "John",
    25,
    "Admin"
);
```

Output:

```text
John 25 Admin
```

Every supplied argument overrides its corresponding default.

---

# 10. Some Default, Some Normal Parameters

Default parameters can be mixed with regular parameters.

```javascript
function greet(
    name,
    greeting = "Hello"
) {
    return `${greeting} ${name}`;
}
```

Call:

```javascript
greet("John");
```

Result:

```text
"Hello John"
```

Call:

```javascript
greet(
    "John",
    "Welcome"
);
```

Result:

```text
"Welcome John"
```

---

# 11. Default Parameters Do Not Have to Be Last

This is valid:

```javascript
function greet(
    greeting = "Hello",
    name
) {
    console.log(
        greeting,
        name
    );
}
```

But calling:

```javascript
greet("John");
```

means:

```text
greeting → "John"
name     → undefined
```

JavaScript matches arguments by position.

It does not skip parameters because they have defaults.

---

# 12. Skipping a Default Parameter

Suppose:

```javascript
function createUser(
    name = "Guest",
    age = 18,
    role = "User"
) {
    console.log(
        name,
        age,
        role
    );
}
```

We want:

```text
name → default
age  → 25
role → default
```

Use:

```javascript
createUser(
    undefined,
    25
);
```

Result:

```text
Guest 25 User
```

`undefined` tells JavaScript to use the default for that position.

---

# 13. ES5 Approach Before Default Parameters

Before ES6, code often looked like:

```javascript
function greet(name) {
    if (name === undefined) {
        name = "Guest";
    }

    console.log(name);
}
```

ES6:

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}
```

The ES6 version is shorter and communicates the function's default behavior directly in its parameter list.

---

# 14. Old `||` Default Pattern

Older JavaScript sometimes used:

```javascript
function greet(name) {
    name = name || "Guest";

    console.log(name);
}
```

This has an important problem.

Suppose:

```javascript
function setVolume(volume) {
    volume = volume || 50;

    console.log(volume);
}

setVolume(0);
```

Output:

```text
50
```

But we explicitly passed:

```text
0
```

Why?

Because `0` is falsy.

---

# 15. Default Parameters vs `||`

With a default parameter:

```javascript
function setVolume(
    volume = 50
) {
    console.log(volume);
}

setVolume(0);
```

Output:

```text
0
```

This is often the desired behavior.

Compare:

```text
Default Parameter
-----------------
undefined → default

0         → keep 0
false     → keep false
""        → keep ""
null      → keep null
```

While:

```text
value || default
----------------
undefined → default
null      → default
0         → default
false     → default
""        → default
NaN       → default
```

These are not equivalent.

---

# 16. Default Parameter Can Be an Expression

Defaults are not limited to literal values.

Example:

```javascript
function calculate(
    value = 10 * 2
) {
    console.log(value);
}

calculate();
```

Output:

```text
20
```

The expression is evaluated when needed.

---

# 17. Function Call as Default Value

Example:

```javascript
function getDefaultName() {
    return "Guest";
}

function greet(
    name = getDefaultName()
) {
    console.log(name);
}

greet();
```

Output:

```text
Guest
```

A function call can be used as a default expression.

---

# 18. Default Expressions Are Evaluated at Call Time

Consider:

```javascript
let count = 0;

function nextValue() {
    count++;
    return count;
}

function show(
    value = nextValue()
) {
    console.log(value);
}
```

Calls:

```javascript
show();
show();
show();
```

Outputs:

```text
1
2
3
```

The default expression is evaluated when the function is called and the parameter needs its default.

It is not permanently calculated when the function is defined.

---

# 19. Default Expression Is Not Evaluated When Argument Exists

Example:

```javascript
function getDefault() {
    console.log(
        "Default calculated"
    );

    return 10;
}

function show(
    value = getDefault()
) {
    console.log(value);
}
```

Call:

```javascript
show(20);
```

Output:

```text
20
```

`getDefault()` is not called because `value` already received `20`.

---

# 20. Earlier Parameters Can Be Used

A default parameter can reference an earlier parameter.

Example:

```javascript
function multiply(
    number,
    multiplier = number
) {
    return number * multiplier;
}
```

Call:

```javascript
multiply(5);
```

Result:

```text
25
```

Because:

```text
number     → 5
multiplier → number → 5
```

Therefore:

```text
5 × 5 = 25
```

---

# 21. Another Earlier Parameter Example

```javascript
function createUser(
    name,
    username = name
) {
    return {
        name,
        username
    };
}
```

Call:

```javascript
createUser("John");
```

Result:

```javascript
{
    name: "John",
    username: "John"
}
```

Call:

```javascript
createUser(
    "John",
    "john123"
);
```

Result:

```javascript
{
    name: "John",
    username: "john123"
}
```

---

# 22. Later Parameters Cannot Be Reliably Used Earlier

Do not write patterns such as:

```javascript
function test(
    a = b,
    b = 10
) {
}
```

Calling:

```javascript
test();
```

causes an error because `b` has not yet been initialized when JavaScript evaluates the default for `a`.

Think of parameters being initialized from left to right:

```text
a
↓
b
↓
c
```

Earlier parameters are available to later defaults.

Later parameters are not available to earlier defaults.

---

# 23. Default Arrays

A parameter can default to an array.

```javascript
function processItems(
    items = []
) {
    console.log(items);
}

processItems();
```

Output:

```javascript
[]
```

This is useful when the function expects an array.

---

# 24. Default Objects

A parameter can default to an object.

```javascript
function createUser(
    user = {}
) {
    console.log(user);
}

createUser();
```

Output:

```javascript
{}
```

This is especially useful with destructuring.

---

# 25. Object Destructuring with Defaults

Suppose:

```javascript
function showUser({
    name = "Guest",
    role = "User"
}) {
    console.log(
        name,
        role
    );
}
```

Call:

```javascript
showUser({
    name: "John"
});
```

Output:

```text
John User
```

Here:

```text
name → "John"
role → default "User"
```

---

# 26. Problem with Missing Object Argument

Consider:

```javascript
function showUser({
    name = "Guest"
}) {
    console.log(name);
}
```

Calling:

```javascript
showUser();
```

causes an error.

Why?

Because JavaScript effectively tries to destructure:

```javascript
undefined
```

as an object.

---

# 27. Default Object Parameter

Use:

```javascript
function showUser({
    name = "Guest"
} = {}) {
    console.log(name);
}
```

Now:

```javascript
showUser();
```

Output:

```text
Guest
```

There are two defaults here.

First:

```javascript
= {}
```

handles the entire argument being `undefined`.

Then:

```javascript
name = "Guest"
```

handles the `name` property being `undefined`.

---

# 28. Understanding the Two Defaults

Consider:

```javascript
function showUser({
    name = "Guest"
} = {}) {
}
```

Case 1:

```javascript
showUser();
```

The parameter gets:

```javascript
{}
```

Then:

```javascript
name
```

is missing, so:

```text
name → "Guest"
```

Case 2:

```javascript
showUser({});
```

The object exists, but `name` is missing:

```text
name → "Guest"
```

Case 3:

```javascript
showUser({
    name: "John"
});
```

Result:

```text
name → "John"
```

---

# 29. Configuration Object Pattern

Default parameters are commonly used with configuration objects.

```javascript
function connect({
    host = "localhost",
    port = 3000,
    secure = false
} = {}) {
    console.log(
        host,
        port,
        secure
    );
}
```

Call:

```javascript
connect();
```

Result:

```text
localhost 3000 false
```

Call:

```javascript
connect({
    port: 8080
});
```

Result:

```text
localhost 8080 false
```

Only the provided option changes.

---

# 30. Why Configuration Objects Are Useful

Compare:

```javascript
connect(
    "localhost",
    3000,
    false,
    5,
    true
);
```

It can be difficult to understand what each value represents.

An object is clearer:

```javascript
connect({
    host: "localhost",
    port: 3000,
    secure: false
});
```

Combined with defaults:

```javascript
connect({
    secure: true
});
```

This can be much easier to read and maintain.

---

# 31. Default Array Destructuring

Example:

```javascript
function showCoordinates([
    x = 0,
    y = 0
] = []) {
    console.log(
        x,
        y
    );
}
```

Call:

```javascript
showCoordinates();
```

Output:

```text
0 0
```

Call:

```javascript
showCoordinates([
    10
]);
```

Output:

```text
10 0
```

---

# 32. Arrow Functions with Default Parameters

Default parameters work normally with arrow functions.

```javascript
const greet = (
    name = "Guest"
) => {
    return `Hello ${name}`;
};
```

Call:

```javascript
greet();
```

Result:

```text
"Hello Guest"
```

---

# 33. Short Arrow Function

```javascript
const multiply = (
    number,
    multiplier = 2
) => number * multiplier;
```

Call:

```javascript
multiply(10);
```

Result:

```text
20
```

---

# 34. Parentheses with Arrow Functions

A single arrow-function parameter without a default can omit parentheses:

```javascript
const greet =
    name => `Hello ${name}`;
```

But with a default:

```javascript
const greet =
    (name = "Guest") =>
        `Hello ${name}`;
```

Parentheses are required around a default parameter.

---

# 35. Defaults with Template Literals

Example:

```javascript
function greet(
    name = "Guest",
    greeting = "Hello"
) {
    return `${greeting}, ${name}!`;
}
```

Call:

```javascript
greet();
```

Result:

```text
"Hello, Guest!"
```

Call:

```javascript
greet(
    "John",
    "Welcome"
);
```

Result:

```text
"Welcome, John!"
```

---

# 36. Defaults with Objects

Example:

```javascript
function createUser(
    name = "Guest",
    settings = {
        theme: "light"
    }
) {
    return {
        name,
        settings
    };
}
```

Call:

```javascript
createUser();
```

Result:

```javascript
{
    name: "Guest",
    settings: {
        theme: "light"
    }
}
```

---

# 37. Default Object Is Created When Needed

Consider:

```javascript
function createSettings(
    settings = {}
) {
    return settings;
}
```

Each call that uses the default receives a newly evaluated default object.

```javascript
const first =
    createSettings();

const second =
    createSettings();

console.log(
    first === second
);
```

Output:

```text
false
```

The object literal `{}` is evaluated separately for each call.

This differs from languages where mutable default arguments may be shared between calls.

---

# 38. Default Array Is Also Created Per Call

Example:

```javascript
function createList(
    items = []
) {
    items.push("JavaScript");

    return items;
}
```

Call:

```javascript
console.log(
    createList()
);
```

Output:

```javascript
["JavaScript"]
```

Call again:

```javascript
console.log(
    createList()
);
```

Output:

```javascript
["JavaScript"]
```

Not:

```javascript
[
    "JavaScript",
    "JavaScript"
]
```

Each default `[]` is created when needed for that invocation.

---

# 39. Default Parameter with Callback

Example:

```javascript
function process(
    value,
    callback = value => value
) {
    return callback(value);
}
```

Call:

```javascript
process(10);
```

Result:

```text
10
```

The default callback simply returns the value.

Call:

```javascript
process(
    10,
    value => value * 2
);
```

Result:

```text
20
```

---

# 40. Default Callback Function

Another example:

```javascript
function execute(
    callback = () => {
        console.log(
            "Default action"
        );
    }
) {
    callback();
}
```

Call:

```javascript
execute();
```

Output:

```text
Default action
```

Call:

```javascript
execute(() => {
    console.log(
        "Custom action"
    );
});
```

Output:

```text
Custom action
```

---

# 41. Required Parameter Pattern

JavaScript does not have built-in required function parameters.

But a default expression can be used to simulate them.

```javascript
function required() {
    throw new Error(
        "Parameter is required"
    );
}

function createUser(
    name = required()
) {
    return {
        name
    };
}
```

Calling:

```javascript
createUser();
```

throws:

```text
Error: Parameter is required
```

Calling:

```javascript
createUser("John");
```

works normally.

This is a useful pattern to understand, although explicit validation is often clearer in larger application code.

---

# 42. Default Parameter Scope

Default parameters have their own parameter initialization scope.

For example:

```javascript
let value = 10;

function show(
    number = value
) {
    let value = 20;

    console.log(number);
}

show();
```

Output:

```text
10
```

The default expression does not use the `value` declared later inside the function body.

---

# 43. Earlier Parameter Scope

Example:

```javascript
function test(
    a,
    b = a
) {
    console.log(
        a,
        b
    );
}

test(10);
```

Output:

```text
10 10
```

The earlier parameter `a` is available while initializing `b`.

---

# 44. `this` in Default Expressions

Default expressions can use the `this` value associated with the function call.

Example:

```javascript
const user = {
    name: "John",

    greet(
        name = this.name
    ) {
        console.log(name);
    }
};

user.greet();
```

Output:

```text
John
```

This is valid, but using complex defaults can make functions harder to understand, so keep defaults simple when possible.

---

# 45. `function.length` and Defaults

Every function has a:

```javascript
length
```

property.

It represents the number of parameters before the first one with a default value.

Example:

```javascript
function test(
    a,
    b,
    c
) {
}
```

```javascript
console.log(
    test.length
);
```

Output:

```text
3
```

---

# 46. `function.length` with Default Parameter

Example:

```javascript
function test(
    a,
    b = 10,
    c
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
1
```

Why?

The first default parameter is:

```javascript
b = 10
```

Only parameters before it are counted.

Therefore:

```text
a → counted
b → default starts here
c → not counted
```

---

# 47. Defaults at the End and `length`

Example:

```javascript
function test(
    a,
    b,
    c = 10
) {
}
```

Then:

```javascript
console.log(
    test.length
);
```

Output:

```text
2
```

Because:

```text
a → counted
b → counted
c → first default
```

---

# 48. Rest Parameter and `length`

Example:

```javascript
function test(
    a,
    b,
    ...rest
) {
}
```

Then:

```javascript
console.log(
    test.length
);
```

Output:

```text
2
```

The rest parameter is not counted.

---

# 49. Default + Rest Parameters

Example:

```javascript
function log(
    prefix = "INFO",
    ...messages
) {
    console.log(
        prefix,
        ...messages
    );
}
```

Call:

```javascript
log(
    "ERROR",
    "Something",
    "failed"
);
```

Output:

```text
ERROR Something failed
```

Rest must still be the final parameter.

---

# 50. Rest Parameters Cannot Have Defaults

This is invalid:

```javascript
function test(
    ...args = []
) {
}
```

Rest parameters cannot have default initializers.

There is no need because when no remaining arguments exist:

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

# 51. React Component Default Props with Parameters

Consider:

```jsx
function Button({
    text = "Click Me",
    disabled = false
}) {
    return (
        <button
            disabled={disabled}
        >
            {text}
        </button>
    );
}
```

Usage:

```jsx
<Button />
```

Results conceptually in:

```text
text     → "Click Me"
disabled → false
```

Usage:

```jsx
<Button
    text="Save"
    disabled={true}
/>
```

uses the supplied values.

---

# 52. Default Values in React Props

Example:

```jsx
function UserCard({
    name = "Guest",
    role = "User"
}) {
    return (
        <div>
            <h2>{name}</h2>
            <p>{role}</p>
        </div>
    );
}
```

This is simply JavaScript:

```text
Object destructuring
+
Default values
```

React is not introducing new syntax here.

---

# 53. `null` React Prop Does Not Use Default

Suppose:

```jsx
function User({
    name = "Guest"
}) {
    return <p>{name}</p>;
}
```

If:

```jsx
<User name={null} />
```

then:

```text
name → null
```

The default `"Guest"` is not applied.

Defaults apply to `undefined`, not `null`.

---

# 54. API Utility Example

```javascript
function fetchUsers({
    page = 1,
    limit = 20,
    sort = "name"
} = {}) {
    console.log(
        page,
        limit,
        sort
    );
}
```

Call:

```javascript
fetchUsers();
```

Result:

```text
1 20 name
```

Call:

```javascript
fetchUsers({
    page: 3,
    limit: 50
});
```

Result:

```text
3 50 name
```

This pattern is common for utility functions with optional configuration.

---

# 55. Pagination Example

```javascript
function paginate(
    items,
    page = 1,
    pageSize = 10
) {
    const start =
        (page - 1) * pageSize;

    return items.slice(
        start,
        start + pageSize
    );
}
```

Defaults make the function usable without requiring every optional argument.

---

# 56. Default Options Object

A useful pattern:

```javascript
function request({
    method = "GET",
    headers = {},
    body = null
} = {}) {
    // request logic
}
```

Now callers can provide only what they need:

```javascript
request();
```

or:

```javascript
request({
    method: "POST",
    body: {
        name: "John"
    }
});
```

---

# 57. Default Parameters vs Nullish Coalescing

Default parameter:

```javascript
function show(
    value = "Default"
) {
}
```

uses the default when:

```text
value === undefined
```

Nullish coalescing:

```javascript
const result =
    value ?? "Default";
```

uses the fallback when:

```text
value === undefined
OR
value === null
```

Therefore:

```text
Default parameter:
undefined → fallback
null      → keep null

??:
undefined → fallback
null      → fallback
```

We will cover `??` separately.

---

# 58. Default Parameters vs Logical OR

Consider:

```javascript
function test(
    value = 10
) {
    return value;
}
```

Compare:

```javascript
const result =
    value || 10;
```

and:

```javascript
const result =
    value ?? 10;
```

Behavior:

| Value | Default Parameter | `||` | `??` |
|---|---:|---:|---:|
| `undefined` | Default | Default | Default |
| `null` | `null` | Default | Default |
| `0` | `0` | Default | `0` |
| `false` | `false` | Default | `false` |
| `""` | `""` | Default | `""` |

These mechanisms solve related but different problems.

---

# 59. Common Mistake — Thinking `null` Uses Default

Wrong assumption:

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}

greet(null);
```

Actual output:

```text
null
```

Remember:

```text
undefined → default

null → null
```

---

# 60. Common Mistake — Thinking Falsy Means Missing

Example:

```javascript
function setVolume(
    volume = 50
) {
    console.log(volume);
}

setVolume(0);
```

Output:

```text
0
```

`0` is falsy, but it is not `undefined`.

Default parameters are not based on truthiness.

---

# 61. Common Mistake — Argument Position

Consider:

```javascript
function createUser(
    name = "Guest",
    age = 18
) {
}
```

Calling:

```javascript
createUser(25);
```

does not mean:

```text
age → 25
```

It means:

```text
name → 25
age  → 18
```

Arguments are assigned by position.

If you have many optional values, consider an options object.

---

# 62. Common Mistake — Missing Destructured Argument

This:

```javascript
function show({
    name
}) {
}

show();
```

throws because JavaScript attempts to destructure `undefined`.

If the entire argument is optional:

```javascript
function show({
    name = "Guest"
} = {}) {
}
```

---

# 63. Common Mistake — Referencing Later Parameter

Avoid:

```javascript
function test(
    a = b,
    b = 10
) {
}
```

Calling:

```javascript
test();
```

fails because `b` is not initialized when `a`'s default expression runs.

Use earlier parameters in later defaults:

```javascript
function test(
    a = 10,
    b = a
) {
}
```

---

# 64. Common Mistake — Confusing `||` with Defaults

This:

```javascript
function test(value) {
    value = value || 10;
}
```

is not equivalent to:

```javascript
function test(
    value = 10
) {
}
```

For example:

```javascript
test(0);
```

The `||` approach treats `0` as falsy.

The default parameter preserves `0`.

---

# 65. Common Mistake — Default Rest Parameter

Invalid:

```javascript
function test(
    ...args = []
) {
}
```

Correct:

```javascript
function test(
    ...args
) {
}
```

`args` already becomes an empty array if no remaining arguments are supplied.

---

# 66. Common Mistake — Overly Complex Defaults

Technically, you can write complex expressions:

```javascript
function calculate(
    value =
        getValue() *
        calculateFactor()
) {
}
```

But complicated defaults can make function behavior difficult to understand.

Prefer defaults that communicate obvious fallback behavior.

---

# 67. Interview Question 1

What is the output?

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}

greet();
```

Answer:

```text
Guest
```

---

# 68. Interview Question 2

What is the output?

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}

greet(undefined);
```

Answer:

```text
Guest
```

---

# 69. Interview Question 3

What is the output?

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}

greet(null);
```

Answer:

```text
null
```

---

# 70. Interview Question 4

What is the output?

```javascript
function test(
    value = 10
) {
    console.log(value);
}

test(0);
```

Answer:

```text
0
```

---

# 71. Interview Question 5

What is the output?

```javascript
function test(
    value = true
) {
    console.log(value);
}

test(false);
```

Answer:

```text
false
```

---

# 72. Interview Question 6

What is the output?

```javascript
function test(
    value = "Hello"
) {
    console.log(value);
}

test("");
```

Answer:

```text
```

An empty string is printed.

The default is not used.

---

# 73. Interview Question 7

What is the output?

```javascript
function multiply(
    a,
    b = a
) {
    return a * b;
}

console.log(
    multiply(5)
);
```

Answer:

```text
25
```

---

# 74. Interview Question 8

What happens?

```javascript
function test(
    a = b,
    b = 10
) {
}

test();
```

Answer:

```text
ReferenceError
```

`b` has not been initialized when the default for `a` is evaluated.

---

# 75. Interview Question 9

What is the output?

```javascript
function show({
    name = "Guest"
} = {}) {
    console.log(name);
}

show();
```

Answer:

```text
Guest
```

---

# 76. Interview Question 10

What is the output?

```javascript
function show({
    name = "Guest"
} = {}) {
    console.log(name);
}

show({
    name: null
});
```

Answer:

```text
null
```

The default does not replace `null`.

---

# 77. Interview Question 11

What is the output?

```javascript
function test(
    a,
    b = 10,
    c
) {
}

console.log(
    test.length
);
```

Answer:

```text
1
```

Only parameters before the first default parameter are counted.

---

# 78. Interview Question 12

What is the output?

```javascript
function test(
    a,
    b,
    c = 10
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

---

# 79. Interview Question 13

What is the output?

```javascript
function createList(
    list = []
) {
    list.push(1);

    return list;
}

console.log(
    createList()
);

console.log(
    createList()
);
```

Answer:

```text
[1]
[1]
```

Each invocation gets a newly created default array.

---

# 80. Interview Question 14

What is the output?

```javascript
function calculate(
    a = 10,
    b = a * 2
) {
    return b;
}

console.log(
    calculate()
);
```

Answer:

```text
20
```

---

# 81. Interview Question 15

What is the output?

```javascript
function calculate(
    a = 10,
    b = a * 2
) {
    return b;
}

console.log(
    calculate(5)
);
```

Answer:

```text
10
```

Because:

```text
a → 5
b → a * 2 → 10
```

---

# 82. Interview Question 16

What is the difference?

```javascript
function one(
    value = 10
) {
}
```

vs:

```javascript
function two(value) {
    value = value || 10;
}
```

Answer:

```text
one()
uses the default only for undefined.

two()
uses the fallback for any falsy value.
```

So:

```javascript
one(0);
```

keeps:

```text
0
```

while the `||` expression inside:

```javascript
two(0);
```

produces:

```text
10
```

---

# 83. Interview Question 17

Is this valid?

```javascript
const greet =
    (name = "Guest") =>
        `Hello ${name}`;
```

Answer:

```text
Yes.
```

Arrow functions support default parameters.

---

# 84. Interview Question 18

Is this valid?

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

---

# 85. Default Parameter Mental Model

Consider:

```javascript
function greet(
    name = "Guest"
) {
}
```

Call:

```javascript
greet();
```

Think:

```text
Argument
   ↓
undefined
   ↓
Default available?
   ↓
YES
   ↓
"Guest"
   ↓
name
```

But:

```javascript
greet(null);
```

Think:

```text
Argument
   ↓
null
   ↓
Is it undefined?
   ↓
NO
   ↓
Keep null
```

---

# 86. Default + Destructuring Mental Model

```javascript
function createUser({
    name = "Guest",
    role = "User"
} = {}) {
}
```

Call:

```javascript
createUser();
```

Flow:

```text
Argument
↓
undefined

Default parameter
↓
{}

Destructure
↓
name → undefined
role → undefined

Property defaults
↓
name → "Guest"
role → "User"
```

Call:

```javascript
createUser({
    name: "John"
});
```

Flow:

```text
Argument
↓
{
    name: "John"
}

Destructure
↓
name → "John"
role → undefined

Default
↓
role → "User"
```

---

# 87. Default Parameters Cheat Sheet

## Basic Default

```javascript
function greet(
    name = "Guest"
) {
}
```

## Multiple Defaults

```javascript
function createUser(
    name = "Guest",
    age = 18
) {
}
```

## Earlier Parameter

```javascript
function multiply(
    a,
    b = a
) {
}
```

## Expression

```javascript
function test(
    value = 10 * 2
) {
}
```

## Function Call

```javascript
function test(
    value = getDefault()
) {
}
```

## Default Array

```javascript
function test(
    items = []
) {
}
```

## Default Object

```javascript
function test(
    options = {}
) {
}
```

## Destructuring

```javascript
function test({
    name = "Guest"
}) {
}
```

## Safe Optional Object

```javascript
function test({
    name = "Guest"
} = {}) {
}
```

## Arrow Function

```javascript
const greet =
    (name = "Guest") =>
        `Hello ${name}`;
```

## Rest

```javascript
function test(
    value = 10,
    ...rest
) {
}
```

---

# 88. Important Rules to Remember

```text
1. Default parameters were introduced in ES6.

2. They provide fallback values for function parameters.

3. A default is used when an argument is omitted.

4. A default is used when undefined is explicitly passed.

5. null does not trigger a default parameter.

6. 0 does not trigger a default.

7. false does not trigger a default.

8. "" does not trigger a default.

9. NaN does not trigger a default.

10. Default parameters are not based on truthiness.

11. Supplied arguments override defaults.

12. Multiple parameters can have defaults.

13. Default parameters do not have to be last.

14. Arguments are still assigned by position.

15. undefined can be passed to skip a defaulted position.

16. Default values can be expressions.

17. Default values can call functions.

18. Default expressions are evaluated when needed during a function call.

19. They are not evaluated when a non-undefined argument is supplied.

20. Later defaults can reference earlier parameters.

21. Earlier defaults should not reference later parameters.

22. Defaults can be arrays.

23. Defaults can be objects.

24. Default array/object literals are evaluated per invocation when needed.

25. Defaults work with arrow functions.

26. Defaults work with destructuring.

27. Destructured properties can have their own defaults.

28. An entire destructured parameter can also have a default.

29. `({ name = "Guest" } = {})` handles an omitted object argument safely.

30. Defaults can be callbacks.

31. Default expressions can be used to simulate required parameters.

32. `function.length` stops counting at the first parameter with a default.

33. Rest parameters are not counted in function.length.

34. Rest parameters cannot have default initializers.

35. Default parameters and `||` are not equivalent.

36. Default parameters and `??` are not equivalent.

37. `||` falls back for all falsy values.

38. `??` falls back for null and undefined.

39. Default parameters fall back only for undefined.

40. Default parameters are common with configuration objects, utilities, APIs, and React props.
```

---

# 89. Most Important Concept

Remember:

```text
DEFAULT PARAMETERS CARE ABOUT undefined
```

Example:

```javascript
function test(
    value = "Default"
) {
    console.log(value);
}
```

Results:

```javascript
test();
```

```text
Default
```

```javascript
test(undefined);
```

```text
Default
```

```javascript
test(null);
```

```text
null
```

```javascript
test(0);
```

```text
0
```

```javascript
test(false);
```

```text
false
```

```javascript
test("");
```

```text
""
```

Mental model:

```text
undefined
    │
    ▼
Use Default


null / 0 / false / ""
    │
    ▼
Keep Supplied Value
```

---

# 90. Default Parameters in One Sentence

> Default parameters allow JavaScript functions to assign fallback values to parameters when the corresponding argument is missing or `undefined`, while preserving explicitly supplied values such as `null`, `0`, `false`, and empty strings.

