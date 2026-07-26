#  Destructuring

**Destructuring** is an ES6 feature that allows values from **arrays** or properties from **objects** to be extracted into variables using concise syntax.

Instead of:

```javascript
const user = {
    name: "John",
    age: 25
};

const name = user.name;
const age = user.age;
```

we can write:

```javascript
const {
    name,
    age
} = user;
```

For arrays, instead of:

```javascript
const numbers = [10, 20];

const first = numbers[0];
const second = numbers[1];
```

we can write:

```javascript
const [
    first,
    second
] = numbers;
```

The main idea is:

```text
Array
↓
Extract values by POSITION

Object
↓
Extract properties by NAME
```

---

# 1. Array Destructuring

Suppose:

```javascript
const numbers = [
    10,
    20,
    30
];
```

Traditional approach:

```javascript
const first = numbers[0];
const second = numbers[1];
const third = numbers[2];
```

With destructuring:

```javascript
const [
    first,
    second,
    third
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

```javascript
console.log(second);
```

Output:

```text
20
```

```javascript
console.log(third);
```

Output:

```text
30
```

---

# 2. Array Destructuring Uses Position

This is extremely important.

```javascript
const languages = [
    "JavaScript",
    "Python",
    "Java"
];

const [
    first,
    second,
    third
] = languages;
```

Result:

```text
first  → "JavaScript"
second → "Python"
third  → "Java"
```

The variable names do not need to match anything.

For example:

```javascript
const [
    x,
    y,
    z
] = languages;
```

Result:

```text
x → "JavaScript"
y → "Python"
z → "Java"
```

Array destructuring works according to **position**.

---

# 3. Destructuring Only Required Values

You do not need to destructure every element.

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const [
    first,
    second
] = numbers;
```

Result:

```text
first  → 10
second → 20
```

The remaining elements are simply not assigned.

---

# 4. Skipping Array Elements

Suppose:

```javascript
const languages = [
    "JavaScript",
    "Python",
    "Java"
];
```

We only want the first and third values.

```javascript
const [
    first,
    ,
    third
] = languages;
```

Result:

```text
first → "JavaScript"
third → "Java"
```

The empty position skips `"Python"`.

---

# 5. Skipping Multiple Elements

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
    ,
    fourth
] = numbers;
```

Result:

```text
first  → 10
fourth → 40
```

---

# 6. Missing Array Values

Example:

```javascript
const numbers = [
    10,
    20
];

const [
    first,
    second,
    third
] = numbers;
```

Result:

```text
first  → 10
second → 20
third  → undefined
```

If the corresponding element does not exist, the variable receives:

```javascript
undefined
```

---

# 7. Default Values in Array Destructuring

Default values can be provided.

```javascript
const numbers = [
    10,
    20
];

const [
    first,
    second,
    third = 30
] = numbers;
```

Result:

```text
first  → 10
second → 20
third  → 30
```

Because there was no third value.

---

# 8. Default Value Only Applies to `undefined`

Consider:

```javascript
const values = [
    null,
    undefined
];

const [
    first = "Default",
    second = "Default"
] = values;
```

Result:

```text
first  → null
second → "Default"
```

Why?

Defaults apply when the destructured value is:

```javascript
undefined
```

Not when it is:

```javascript
null
```

---

# 9. Swapping Variables

Without destructuring:

```javascript
let a = 10;
let b = 20;

const temp = a;

a = b;
b = temp;
```

With destructuring:

```javascript
let a = 10;
let b = 20;

[a, b] = [b, a];
```

Now:

```text
a → 20
b → 10
```

This is a common destructuring example.

---

# 10. Rest with Array Destructuring

Rest syntax can collect the remaining elements.

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

Result:

```text
first → 10
```

and:

```javascript
remaining
```

contains:

```javascript
[20, 30, 40]
```

Remember:

```text
...remaining
```

is **rest syntax** because it collects values.

---

# 11. Rest Must Be Last

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

Rest must be the final element in the destructuring pattern.

---

# 12. Nested Array Destructuring

Consider:

```javascript
const data = [
    10,
    [
        20,
        30
    ]
];
```

We can destructure it:

```javascript
const [
    first,
    [
        second,
        third
    ]
] = data;
```

Result:

```text
first  → 10
second → 20
third  → 30
```

The destructuring pattern mirrors the array structure.

---

# 13. Nested Array Mental Model

Given:

```javascript
const data = [
    10,
    [
        20,
        30
    ]
];
```

Pattern:

```javascript
const [
    first,
    [
        second,
        third
    ]
] = data;
```

Think:

```text
[
    10,
    [20, 30]
]
 │      │
 ▼      ▼
first   [
        second,
        third
        ]
```

---

# 14. Destructuring Function Return Arrays

Functions can return arrays.

```javascript
function getCoordinates() {
    return [
        10,
        20
    ];
}
```

Instead of:

```javascript
const coordinates =
    getCoordinates();

const x = coordinates[0];
const y = coordinates[1];
```

we can write:

```javascript
const [
    x,
    y
] = getCoordinates();
```

Result:

```text
x → 10
y → 20
```

---

# 15. React `useState()` Uses Array Destructuring

A common React pattern is:

```javascript
const [
    count,
    setCount
] = useState(0);
```

Conceptually, `useState()` returns two values in an array-like pair:

```javascript
[
    currentState,
    setterFunction
]
```

So:

```javascript
const [
    count,
    setCount
] = useState(0);
```

means:

```text
First returned value
↓
count

Second returned value
↓
setCount
```

This is **array destructuring**.

---

# 16. Why React State Variables Can Have Any Name

Because array destructuring uses position.

For example:

```javascript
const [
    username,
    setUsername
] = useState("");
```

or:

```javascript
const [
    count,
    setCount
] = useState(0);
```

React does not require these exact variable names.

This would technically work:

```javascript
const [
    x,
    y
] = useState(0);
```

But meaningful names are much better.

---

# 17. Object Destructuring

Consider:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};
```

Traditional approach:

```javascript
const name = user.name;
const age = user.age;
const role = user.role;
```

Using destructuring:

```javascript
const {
    name,
    age,
    role
} = user;
```

Result:

```text
name → "John"
age  → 25
role → "Admin"
```

---

# 18. Object Destructuring Uses Property Names

Unlike arrays, objects are destructured by **property name**.

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    name,
    age
} = user;
```

JavaScript looks for properties named:

```text
name
age
```

in the object.

---

# 19. Object Property Order Does Not Matter

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    role,
    name,
    age
} = user;
```

This still works.

Result:

```text
role → "Admin"
name → "John"
age  → 25
```

Because object destructuring uses property names, not position.

---

# 20. Array vs Object Destructuring

Array:

```javascript
const [
    first,
    second
] = array;
```

Uses:

```text
POSITION
```

Object:

```javascript
const {
    name,
    age
} = object;
```

Uses:

```text
PROPERTY NAME
```

This distinction is fundamental.

---

# 21. Extracting Only Required Object Properties

Suppose:

```javascript
const user = {
    id: 1,
    name: "John",
    age: 25,
    role: "Admin",
    active: true
};
```

If we only need:

```text
name
role
```

we can write:

```javascript
const {
    name,
    role
} = user;
```

There is no need to destructure every property.

---

# 22. Missing Object Properties

Example:

```javascript
const user = {
    name: "John"
};

const {
    name,
    age
} = user;
```

Result:

```text
name → "John"
age  → undefined
```

Because `age` does not exist.

---

# 23. Default Values in Object Destructuring

Example:

```javascript
const user = {
    name: "John"
};

const {
    name,
    role = "User"
} = user;
```

Result:

```text
name → "John"
role → "User"
```

The default is used because `role` is `undefined`.

---

# 24. Existing Property Overrides Default

Example:

```javascript
const user = {
    name: "John",
    role: "Admin"
};

const {
    name,
    role = "User"
} = user;
```

Result:

```text
name → "John"
role → "Admin"
```

The default is ignored because a value already exists.

---

# 25. `undefined` vs `null` with Defaults

Example:

```javascript
const user = {
    name: "John",
    age: undefined,
    role: null
};

const {
    age = 25,
    role = "User"
} = user;
```

Result:

```text
age  → 25
role → null
```

Again:

```text
Default values apply to undefined.

They do not replace null.
```

---

# 26. Renaming Object Properties

Suppose:

```javascript
const user = {
    name: "John",
    age: 25
};
```

We want the `name` property stored in a variable called:

```javascript
username
```

Use:

```javascript
const {
    name: username,
    age
} = user;
```

Now:

```text
username → "John"
age      → 25
```

---

# 27. Renaming Does Not Create the Original Variable

Example:

```javascript
const {
    name: username
} = user;
```

This creates:

```javascript
username
```

It does **not** create a variable named:

```javascript
name
```

So:

```javascript
console.log(username);
```

works.

But:

```javascript
console.log(name);
```

would not refer to a destructured `name` variable from this statement.

---

# 28. Rename + Default Value

You can rename and provide a default simultaneously.

```javascript
const user = {};

const {
    name: username = "Guest"
} = user;
```

Result:

```text
username → "Guest"
```

Syntax:

```javascript
propertyName: variableName = defaultValue
```

Example:

```javascript
const {
    role: userRole = "User"
} = user;
```

---

# 29. Object Rest Destructuring

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...details
} = user;
```

Result:

```text
name → "John"
```

and:

```javascript
details
```

contains:

```javascript
{
    age: 25,
    role: "Admin"
}
```

Here:

```javascript
...details
```

is **rest syntax**.

---

# 30. Excluding Properties

Object rest is useful for constructing an object without selected properties.

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

Now:

```javascript
safeUser
```

is:

```javascript
{
    id: 1,
    name: "John"
}
```

The original object is not modified.

---

# 31. Excluding Multiple Properties

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
{
    id: 1,
    name: "John",
    role: "Admin"
}
```

---

# 32. Nested Object Destructuring

Consider:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York",
        country: "USA"
    }
};
```

Traditional:

```javascript
const city =
    user.address.city;

const country =
    user.address.country;
```

Destructuring:

```javascript
const {
    address: {
        city,
        country
    }
} = user;
```

Result:

```text
city    → "New York"
country → "USA"
```

---

# 33. Important Nested Destructuring Detail

Consider:

```javascript
const {
    address: {
        city
    }
} = user;
```

This creates:

```javascript
city
```

But it does **not** create a variable called:

```javascript
address
```

`address` is being used to navigate to the nested object.

If you also need `address`, you can destructure it separately or structure your code accordingly.

---

# 34. Nested Destructuring with Rename

Example:

```javascript
const user = {
    profile: {
        name: "John"
    }
};

const {
    profile: {
        name: username
    }
} = user;
```

Result:

```text
username → "John"
```

---

# 35. Nested Destructuring with Default Values

Suppose:

```javascript
const user = {
    profile: {}
};
```

We can write:

```javascript
const {
    profile: {
        name = "Guest"
    }
} = user;
```

Result:

```text
name → "Guest"
```

---

# 36. Missing Nested Object Problem

Consider:

```javascript
const user = {};
```

This can fail:

```javascript
const {
    profile: {
        name
    }
} = user;
```

because:

```javascript
user.profile
```

is:

```javascript
undefined
```

and JavaScript cannot destructure `name` from `undefined`.

---

# 37. Default Nested Object

We can provide a default object:

```javascript
const user = {};

const {
    profile: {
        name = "Guest"
    } = {}
} = user;
```

Result:

```text
name → "Guest"
```

Here:

```javascript
profile = {}
```

provides an empty object if `profile` is `undefined`.

---

# 38. Function Parameter Destructuring

Suppose:

```javascript
const user = {
    name: "John",
    age: 25
};
```

Traditional:

```javascript
function showUser(user) {
    console.log(user.name);
    console.log(user.age);
}
```

Using parameter destructuring:

```javascript
function showUser({
    name,
    age
}) {
    console.log(name);
    console.log(age);
}
```

Call:

```javascript
showUser(user);
```

Output:

```text
John
25
```

---

# 39. Why Parameter Destructuring Is Useful

Instead of repeatedly writing:

```javascript
user.name
user.age
user.role
user.email
```

we can write:

```javascript
function showUser({
    name,
    age,
    role,
    email
}) {
    // use variables directly
}
```

This is especially useful when a function only needs selected properties.

---

# 40. Parameter Destructuring with Defaults

Example:

```javascript
function greet({
    name,
    role = "User"
}) {
    console.log(
        `${name} - ${role}`
    );
}
```

Call:

```javascript
greet({
    name: "John"
});
```

Output:

```text
John - User
```

---

# 41. Default Object Parameter

Consider:

```javascript
function greet({
    name = "Guest"
}) {
    console.log(name);
}
```

Calling:

```javascript
greet();
```

would fail because JavaScript attempts to destructure `undefined`.

A safer version when the argument itself is optional is:

```javascript
function greet({
    name = "Guest"
} = {}) {
    console.log(name);
}
```

Now:

```javascript
greet();
```

Output:

```text
Guest
```

---

# 42. Array Parameter Destructuring

Functions can destructure array parameters.

Example:

```javascript
function showCoordinates([
    x,
    y
]) {
    console.log(x);
    console.log(y);
}
```

Call:

```javascript
showCoordinates([
    10,
    20
]);
```

Output:

```text
10
20
```

---

# 43. Nested Parameter Destructuring

Example:

```javascript
function showUser({
    name,

    address: {
        city
    }
}) {
    console.log(name);
    console.log(city);
}
```

Call:

```javascript
showUser({
    name: "John",

    address: {
        city: "New York"
    }
});
```

Output:

```text
John
New York
```

---

# 44. Destructuring Function Return Objects

Suppose:

```javascript
function getUser() {
    return {
        name: "John",
        age: 25,
        role: "Admin"
    };
}
```

Instead of:

```javascript
const user = getUser();

const name = user.name;
const role = user.role;
```

we can write:

```javascript
const {
    name,
    role
} = getUser();
```

Result:

```text
name → "John"
role → "Admin"
```

---

# 45. Destructuring Function Return Arrays

Example:

```javascript
function getValues() {
    return [
        10,
        20
    ];
}

const [
    first,
    second
] = getValues();
```

Result:

```text
first  → 10
second → 20
```

---

# 46. Array Return vs Object Return

Suppose:

```javascript
function getPosition() {
    return [
        10,
        20
    ];
}
```

Usage:

```javascript
const [
    x,
    y
] = getPosition();
```

Names can be anything because array destructuring uses position.

For an object:

```javascript
function getUser() {
    return {
        name: "John",
        age: 25
    };
}
```

Usage:

```javascript
const {
    name,
    age
} = getUser();
```

Property names matter unless you explicitly rename them.

---

# 47. Destructuring with `let`

Destructuring does not require `const`.

Example:

```javascript
let [
    first,
    second
] = [
    10,
    20
];
```

Or:

```javascript
let {
    name,
    age
} = {
    name: "John",
    age: 25
};
```

Use `const` when the variables will not be reassigned, and `let` when they will.

---

# 48. Reassigning Array Variables with Destructuring

Example:

```javascript
let first;
let second;

[first, second] = [
    10,
    20
];
```

Now:

```text
first  → 10
second → 20
```

This is destructuring assignment rather than a declaration.

---

# 49. Reassigning Object Variables

Suppose:

```javascript
let name;
let age;
```

You might think this works:

```javascript
{
    name,
    age
} = user;
```

But JavaScript can interpret `{}` as a block.

Use parentheses:

```javascript
({
    name,
    age
} = user);
```

Now the destructuring assignment works.

This applies when assigning to existing variables without `const`, `let`, or `var`.

---

# 50. Computed Property Destructuring

Property names can be computed.

Example:

```javascript
const key = "name";

const user = {
    name: "John",
    age: 25
};

const {
    [key]: value
} = user;
```

Result:

```text
value → "John"
```

JavaScript evaluates:

```javascript
key
```

which contains:

```text
"name"
```

and extracts that property.

---

# 51. Destructuring Dynamic Property into Named Variable

Example:

```javascript
const property = "role";

const user = {
    name: "John",
    role: "Admin"
};

const {
    [property]: selectedValue
} = user;
```

Result:

```text
selectedValue → "Admin"
```

This is useful when the property name is determined dynamically.

---

# 52. Destructuring with Rest and Spread Together

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...details
} = user;
```

Here:

```javascript
...details
```

is **rest**.

Now:

```javascript
const updatedUser = {
    name,
    ...details,
    active: true
};
```

Here:

```javascript
...details
```

is **spread**.

Same syntax, different jobs.

---

# 53. Rest + Spread Flow

```javascript
const {
    name,
    ...details
} = user;

const copy = {
    ...details
};
```

Think:

```text
user
 │
 │ REST
 ▼
details
 │
 │ SPREAD
 ▼
copy
```

Rest:

```text
collects
```

Spread:

```text
expands
```

---

# 54. React Props Without Destructuring

Suppose:

```jsx
function User(props) {
    return (
        <div>
            <h1>
                {props.name}
            </h1>

            <p>
                {props.age}
            </p>
        </div>
    );
}
```

This works.

But React components commonly use object destructuring.

---

# 55. React Props Destructuring

```jsx
function User({
    name,
    age
}) {
    return (
        <div>
            <h1>{name}</h1>
            <p>{age}</p>
        </div>
    );
}
```

Usage:

```jsx
<User
    name="John"
    age={25}
/>
```

The component receives a props object conceptually like:

```javascript
{
    name: "John",
    age: 25
}
```

Then object destructuring extracts the properties.

---

# 56. Destructuring Props Inside Component

Another approach:

```jsx
function User(props) {
    const {
        name,
        age
    } = props;

    return (
        <div>
            <h1>{name}</h1>
            <p>{age}</p>
        </div>
    );
}
```

Both approaches are valid.

---

# 57. Default Prop Values with Destructuring

Example:

```jsx
function User({
    name = "Guest",
    role = "User"
}) {
    return (
        <p>
            {name} - {role}
        </p>
    );
}
```

If:

```jsx
<User />
```

then:

```text
name → "Guest"
role → "User"
```

These defaults apply when the corresponding prop value is `undefined`.

---

# 58. React Props with Rest

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

Here:

```javascript
...props
```

in:

```javascript
{
    children,
    ...props
}
```

is rest.

It collects all remaining props.

Then:

```jsx
<button {...props}>
```

uses spread.

---

# 59. React Hook Array Destructuring

One of the most common React examples:

```javascript
const [
    count,
    setCount
] = useState(0);
```

This is simply JavaScript array destructuring.

Similarly:

```javascript
const [
    user,
    setUser
] = useState(null);
```

There is no special destructuring syntax created by React.

It is standard JavaScript.

---

# 60. React Context Destructuring

You may encounter:

```javascript
const {
    user,
    logout
} = useContext(AuthContext);
```

If `useContext(AuthContext)` returns an object such as:

```javascript
{
    user,
    logout
}
```

then object destructuring extracts those values.

---

# 61. API Response Destructuring

Suppose:

```javascript
const response = {
    status: 200,

    data: {
        id: 1,
        name: "John"
    }
};
```

We can extract:

```javascript
const {
    status,
    data
} = response;
```

Or nested properties:

```javascript
const {
    data: {
        id,
        name
    }
} = response;
```

Result:

```text
id   → 1
name → "John"
```

---

# 62. Destructuring in Loops

Suppose:

```javascript
const users = [
    {
        name: "John",
        age: 25
    },
    {
        name: "Alice",
        age: 30
    }
];
```

Instead of:

```javascript
for (const user of users) {
    console.log(
        user.name,
        user.age
    );
}
```

we can destructure directly:

```javascript
for (
    const {
        name,
        age
    } of users
) {
    console.log(
        name,
        age
    );
}
```

---

# 63. Destructuring in `map()`

Example:

```javascript
const users = [
    {
        name: "John",
        age: 25
    },
    {
        name: "Alice",
        age: 30
    }
];
```

Without destructuring:

```javascript
users.map(
    (user) => user.name
);
```

With destructuring:

```javascript
users.map(
    ({ name }) => name
);
```

Result:

```javascript
[
    "John",
    "Alice"
]
```

This pattern is very common in React.

---

# 64. Destructuring in `filter()`

Example:

```javascript
const users = [
    {
        name: "John",
        active: true
    },
    {
        name: "Alice",
        active: false
    }
];
```

Use:

```javascript
const activeUsers =
    users.filter(
        ({ active }) => active
    );
```

This extracts `active` directly in the callback parameter.

---

# 65. Destructuring in `forEach()`

Example:

```javascript
users.forEach(
    ({ name, age }) => {
        console.log(
            name,
            age
        );
    }
);
```

Again, the callback parameter is an object, so it can be destructured immediately.

---

# 66. Array of Pairs

Suppose:

```javascript
const entries = [
    ["name", "John"],
    ["age", 25],
    ["role", "Admin"]
];
```

We can destructure each pair:

```javascript
for (
    const [
        key,
        value
    ] of entries
) {
    console.log(
        key,
        value
    );
}
```

Output:

```text
name John
age 25
role Admin
```

This pattern is common with:

```javascript
Object.entries()
```

and:

```javascript
Map
```

---

# 67. `Object.entries()` with Destructuring

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

for (
    const [
        key,
        value
    ] of Object.entries(user)
) {
    console.log(
        key,
        value
    );
}
```

Output:

```text
name John
age 25
```

Each entry is:

```javascript
[
    key,
    value
]
```

so array destructuring works naturally.

---

# 68. Common Mistake — Array Order

Consider:

```javascript
const values = [
    "John",
    25
];

const [
    age,
    name
] = values;
```

Result:

```text
age  → "John"
name → 25
```

JavaScript does not understand your intended meaning.

Array destructuring uses **position**.

---

# 69. Common Mistake — Wrong Object Property Name

Example:

```javascript
const user = {
    name: "John"
};

const {
    username
} = user;
```

Result:

```text
username → undefined
```

JavaScript looks for a property literally called:

```javascript
username
```

If you want to rename `name`:

```javascript
const {
    name: username
} = user;
```

---

# 70. Common Mistake — Confusing Rename Syntax

This:

```javascript
const {
    name: username
} = user;
```

does **not** mean:

```text
username property → name variable
```

It means:

```text
name property → username variable
```

Mental model:

```text
propertyName : variableName
```

---

# 71. Common Mistake — Nested Destructuring

Given:

```javascript
const {
    address: {
        city
    }
} = user;
```

Do not assume this creates:

```javascript
address
```

It only creates:

```javascript
city
```

unless `address` is separately assigned.

---

# 72. Common Mistake — Missing Nested Value

This may throw:

```javascript
const user = {};

const {
    address: {
        city
    }
} = user;
```

because `address` is `undefined`.

If appropriate, provide a default:

```javascript
const {
    address: {
        city
    } = {}
} = user;
```

---

# 73. Common Mistake — Defaults and `null`

Example:

```javascript
const user = {
    role: null
};

const {
    role = "User"
} = user;
```

Result:

```text
role → null
```

Not:

```text
"User"
```

Defaults only replace `undefined`.

---

# 74. Common Mistake — Object Assignment Without Parentheses

If variables already exist:

```javascript
let name;
let age;
```

This can be problematic:

```javascript
{
    name,
    age
} = user;
```

Use:

```javascript
({
    name,
    age
} = user);
```

The parentheses tell JavaScript to treat `{}` as an object destructuring pattern rather than a block.

---

# 75. Common Mistake — Rest Position

Wrong:

```javascript
const [
    ...rest,
    last
] = numbers;
```

Correct:

```javascript
const [
    first,
    ...rest
] = numbers;
```

Rest must be last.

---

# 76. Common Mistake — Thinking Destructuring Modifies Original Data

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    name
} = user;
```

This does not remove:

```javascript
name
```

from `user`.

The original remains:

```javascript
{
    name: "John",
    age: 25
}
```

Destructuring extracts/assigns values.

---

# 77. Common Mistake — Destructuring a Non-Iterable as an Array

This fails:

```javascript
const user = {
    name: "John"
};

const [
    name
] = user;
```

A plain object is not iterable.

Use object destructuring:

```javascript
const {
    name
} = user;
```

---

# 78. Common Mistake — Destructuring `null` or `undefined`

This fails:

```javascript
const {
    name
} = null;
```

And:

```javascript
const {
    name
} = undefined;
```

Likewise array destructuring requires an iterable:

```javascript
const [
    first
] = undefined;
```

Destructuring requires an appropriate source value.

---

# 79. Interview Question 1

What is the output?

```javascript
const [
    a,
    b
] = [
    10,
    20
];

console.log(a, b);
```

Answer:

```text
10 20
```

---

# 80. Interview Question 2

What is the output?

```javascript
const [
    first,
    ,
    third
] = [
    10,
    20,
    30
];

console.log(
    first,
    third
);
```

Answer:

```text
10 30
```

---

# 81. Interview Question 3

What is the output?

```javascript
const [
    a,
    b = 20
] = [
    10
];

console.log(a, b);
```

Answer:

```text
10 20
```

---

# 82. Interview Question 4

What is the output?

```javascript
const [
    value = 10
] = [
    null
];

console.log(value);
```

Answer:

```text
null
```

The default does not replace `null`.

---

# 83. Interview Question 5

What is the output?

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    age,
    name
} = user;

console.log(
    name,
    age
);
```

Answer:

```text
John 25
```

Object property order does not matter.

---

# 84. Interview Question 6

What is the output?

```javascript
const user = {
    name: "John"
};

const {
    name: username
} = user;

console.log(username);
```

Answer:

```text
John
```

---

# 85. Interview Question 7

What is the output?

```javascript
const user = {};

const {
    role = "User"
} = user;

console.log(role);
```

Answer:

```text
User
```

---

# 86. Interview Question 8

What is the output?

```javascript
const user = {
    role: null
};

const {
    role = "User"
} = user;

console.log(role);
```

Answer:

```text
null
```

---

# 87. Interview Question 9

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

# 88. Interview Question 10

What is the output?

```javascript
const user = {
    name: "John",
    age: 25,
    role: "Admin"
};

const {
    name,
    ...details
} = user;

console.log(details);
```

Answer:

```javascript
{
    age: 25,
    role: "Admin"
}
```

---

# 89. Interview Question 11

What is the output?

```javascript
let a = 10;
let b = 20;

[a, b] = [b, a];

console.log(a, b);
```

Answer:

```text
20 10
```

---

# 90. Interview Question 12

What variables are created?

```javascript
const {
    address: {
        city
    }
} = user;
```

Answer:

```text
city
```

The destructuring pattern does not create an `address` variable.

---

# 91. Interview Question 13

What is the output?

```javascript
const user = {
    profile: {
        name: "John"
    }
};

const {
    profile: {
        name: username
    }
} = user;

console.log(username);
```

Answer:

```text
John
```

---

# 92. Interview Question 14

What is happening here?

```javascript
const [
    count,
    setCount
] = useState(0);
```

Answer:

```text
Array destructuring
```

`useState()` returns a pair containing the current state value and a state setter function.

---

# 93. Interview Question 15

What is happening here?

```javascript
function User({
    name,
    age
}) {
}
```

Answer:

```text
Object destructuring
```

The props object is being destructured directly in the function parameter.

---

# 94. Interview Question 16

What is the output?

```javascript
const {
    name: username = "Guest"
} = {};

console.log(username);
```

Answer:

```text
Guest
```

The `name` property is missing, so the default value is assigned to `username`.

---

# 95. Interview Question 17

What is the output?

```javascript
const key = "name";

const user = {
    name: "John"
};

const {
    [key]: value
} = user;

console.log(value);
```

Answer:

```text
John
```

The property name is computed from `key`.

---

# 96. Interview Question 18

What happens here?

```javascript
const user = {};

const {
    profile: {
        name
    }
} = user;
```

Answer:

```text
TypeError
```

because `user.profile` is `undefined`, and JavaScript attempts to destructure `name` from it.

---

# 97. Destructuring Mental Model

Array:

```javascript
const [
    first,
    second
] = [
    10,
    20
];
```

Think:

```text
[10, 20]
 │    │
 ▼    ▼
first second
```

Object:

```javascript
const {
    name,
    age
} = user;
```

Think:

```text
user
│
├── name ──→ name
│
└── age ───→ age
```

The critical difference:

```text
Array
↓
POSITION

Object
↓
PROPERTY NAME
```

---

# 98. Destructuring Cheat Sheet

## Array Destructuring

```javascript
const [
    first,
    second
] = array;
```

## Skip Value

```javascript
const [
    first,
    ,
    third
] = array;
```

## Default Value

```javascript
const [
    value = "Default"
] = array;
```

## Rest

```javascript
const [
    first,
    ...rest
] = array;
```

## Swap

```javascript
[a, b] = [b, a];
```

## Nested Array

```javascript
const [
    first,
    [
        second,
        third
    ]
] = array;
```

## Object Destructuring

```javascript
const {
    name,
    age
} = user;
```

## Rename

```javascript
const {
    name: username
} = user;
```

## Default

```javascript
const {
    role = "User"
} = user;
```

## Rename + Default

```javascript
const {
    name: username = "Guest"
} = user;
```

## Object Rest

```javascript
const {
    name,
    ...details
} = user;
```

## Nested Object

```javascript
const {
    address: {
        city
    }
} = user;
```

## Function Parameter

```javascript
function show({
    name,
    age
}) {
}
```

## React Props

```jsx
function User({
    name,
    age
}) {
}
```

## React State

```javascript
const [
    state,
    setState
] = useState(initialValue);
```

---

# 99. Important Rules to Remember

```text
1. Destructuring was introduced in ES6.

2. Destructuring extracts values into variables.

3. Arrays are destructured by position.

4. Objects are destructured by property name.

5. Array variable names can be anything.

6. Object property names must match unless renamed.

7. Object property order does not matter.

8. Array position matters.

9. Array elements can be skipped using commas.

10. Missing values normally produce undefined.

11. Default values can be provided.

12. Defaults apply when the extracted value is undefined.

13. Defaults do not replace null.

14. Variables can be swapped using array destructuring.

15. Rest syntax can collect remaining array elements.

16. Rest syntax can collect remaining object properties.

17. Rest must appear last in its destructuring pattern.

18. Object properties can be renamed during destructuring.

19. Rename syntax is propertyName: variableName.

20. Renaming does not create a variable using the original property name.

21. Destructuring can be nested.

22. Nested patterns mirror the underlying data structure.

23. Missing nested objects can cause errors.

24. Defaults can be used for missing nested objects when appropriate.

25. Function parameters can be destructured.

26. Function return values can be destructured.

27. Array destructuring is heavily used with React hooks.

28. Object destructuring is heavily used with React props.

29. Destructuring can be used inside map(), filter(), and forEach() callbacks.

30. Destructuring can be used in for...of loops.

31. Object rest is useful for creating objects without selected properties.

32. Rest destructuring does not delete properties from the original object.

33. Destructuring itself does not mutate the original array or object.

34. Rest/spread copies are shallow where object references are involved.

35. Existing variables can be assigned using destructuring.

36. Object destructuring assignment to existing variables may require parentheses.

37. Computed property names can be destructured.

38. Destructuring is standard JavaScript, not a React-specific feature.
```

---

# 100. Most Important Concept

Remember this distinction:

```text
ARRAY DESTRUCTURING
===================

Position matters.

["John", 25]
    │      │
    ▼      ▼
  name    age
```

```javascript
const [
    name,
    age
] = [
    "John",
    25
];
```

Object destructuring:

```text
OBJECT DESTRUCTURING
====================

Property names matter.

{
    name: "John",
    age: 25
}
       │
       ▼
Find properties by name
```

```javascript
const {
    name,
    age
} = user;
```

And:

```text
REST
↓
Collect remaining values

SPREAD
↓
Expand values

DESTRUCTURING
↓
Extract values
```

These three concepts are frequently used together in modern JavaScript and React.

---

# 101. Destructuring in One Sentence

> Destructuring is an ES6 syntax that extracts values from arrays by position and properties from objects by name into variables, with support for defaults, renaming, nested patterns, and rest syntax.

