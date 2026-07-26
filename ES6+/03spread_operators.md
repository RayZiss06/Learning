# Spread Syntax (`...`)

The **spread syntax** uses three dots:

```javascript
...
```

It allows an iterable such as an array or string to be **expanded into individual values**, and in object literals it allows an object's own enumerable properties to be copied into another object.

Basic examples:

```javascript
const numbers = [1, 2, 3];

console.log(...numbers);
```

Output:

```text
1 2 3
```

Object spread:

```javascript
const user = {
    name: "John",
    age: 25
};

const copy = {
    ...user
};
```

The `...` syntax is also used for **rest syntax**, but spread and rest perform different jobs.

```text
Spread
→ expands values

Rest
→ collects values
```

---

# 1. Basic Spread Syntax

Consider:

```javascript
const numbers = [10, 20, 30];
```

Normally:

```javascript
console.log(numbers);
```

Output:

```javascript
[10, 20, 30]
```

Using spread:

```javascript
console.log(...numbers);
```

Output:

```text
10 20 30
```

The array is expanded into individual values.

Conceptually:

```javascript
...numbers
```

becomes:

```javascript
10, 20, 30
```

in a context where individual values are expected.

---

# 2. Spread with Arrays

Suppose:

```javascript
const numbers = [1, 2, 3];
```

We can spread the array inside another array:

```javascript
const result = [
    ...numbers
];
```

Result:

```javascript
[1, 2, 3]
```

Conceptually:

```javascript
[
    ...numbers
]
```

becomes:

```javascript
[
    1,
    2,
    3
]
```

---

# 3. Copying an Array

Without spread:

```javascript
const original = [1, 2, 3];

const copy = original;
```

This does **not** create a new array.

Both variables reference the same array.

```javascript
console.log(
    original === copy
);
```

Output:

```text
true
```

Using spread:

```javascript
const original = [1, 2, 3];

const copy = [
    ...original
];
```

Now:

```javascript
console.log(
    original === copy
);
```

Output:

```text
false
```

A new array was created.

---

# 4. Why Array Copying Matters

Consider:

```javascript
const original = [1, 2, 3];

const copy = original;

copy.push(4);
```

Now:

```javascript
console.log(original);
```

Output:

```javascript
[1, 2, 3, 4]
```

Because:

```text
original ──┐
           ▼
       [1, 2, 3, 4]
           ▲
copy ──────┘
```

Both variables reference the same array.

---

# 5. Copying with Spread

Example:

```javascript
const original = [1, 2, 3];

const copy = [
    ...original
];

copy.push(4);
```

Now:

```javascript
console.log(original);
```

Output:

```javascript
[1, 2, 3]
```

And:

```javascript
console.log(copy);
```

Output:

```javascript
[1, 2, 3, 4]
```

Because two different arrays exist.

```text
original
   │
   ▼
[1, 2, 3]


copy
   │
   ▼
[1, 2, 3, 4]
```

---

# 6. Adding Elements with Spread

Suppose:

```javascript
const numbers = [2, 3, 4];
```

Add an element at the beginning:

```javascript
const result = [
    1,
    ...numbers
];
```

Result:

```javascript
[1, 2, 3, 4]
```

---

# 7. Adding Elements at the End

Example:

```javascript
const numbers = [1, 2, 3];

const result = [
    ...numbers,
    4
];
```

Result:

```javascript
[1, 2, 3, 4]
```

---

# 8. Adding Elements Anywhere

Example:

```javascript
const first = [1, 2];
const second = [4, 5];

const result = [
    ...first,
    3,
    ...second
];
```

Result:

```javascript
[1, 2, 3, 4, 5]
```

---

# 9. Combining Arrays

Suppose:

```javascript
const frontend = [
    "HTML",
    "CSS",
    "JavaScript"
];

const backend = [
    "Node.js",
    "Express"
];
```

Combine:

```javascript
const technologies = [
    ...frontend,
    ...backend
];
```

Result:

```javascript
[
    "HTML",
    "CSS",
    "JavaScript",
    "Node.js",
    "Express"
]
```

---

# 10. Traditional Array Combination

Before spread, you might use:

```javascript
const result =
    first.concat(second);
```

With spread:

```javascript
const result = [
    ...first,
    ...second
];
```

Both create a new combined array.

Spread is often more flexible because you can easily insert additional values:

```javascript
const result = [
    ...first,
    "Something",
    ...second
];
```

---

# 11. Spread Does Not Remove Duplicates

Example:

```javascript
const first = [1, 2, 3];
const second = [3, 4, 5];

const result = [
    ...first,
    ...second
];
```

Result:

```javascript
[1, 2, 3, 3, 4, 5]
```

Spread simply expands values.

It does not automatically remove duplicates.

---

# 12. Removing Duplicates with `Set`

Spread can be combined with `Set`.

Example:

```javascript
const numbers = [
    1,
    2,
    2,
    3,
    3,
    4
];

const unique = [
    ...new Set(numbers)
];
```

Result:

```javascript
[1, 2, 3, 4]
```

Here:

```javascript
new Set(numbers)
```

removes duplicate values.

Then:

```javascript
[...set]
```

converts the set back into an array.

---

# 13. Spread with Strings

Strings are iterable.

Therefore:

```javascript
const language = "JavaScript";

const letters = [
    ...language
];
```

Result:

```javascript
[
    "J",
    "a",
    "v",
    "a",
    "S",
    "c",
    "r",
    "i",
    "p",
    "t"
]
```

---

# 14. String Spread into Function Arguments

Example:

```javascript
console.log(..."ABC");
```

Conceptually:

```javascript
console.log(
    "A",
    "B",
    "C"
);
```

Output:

```text
A B C
```

---

# 15. Spread with Function Arguments

Suppose:

```javascript
function add(a, b, c) {
    return a + b + c;
}
```

And:

```javascript
const numbers = [
    10,
    20,
    30
];
```

Instead of:

```javascript
add(
    numbers[0],
    numbers[1],
    numbers[2]
);
```

we can use:

```javascript
add(...numbers);
```

Conceptually:

```javascript
add(...numbers);
```

becomes:

```javascript
add(10, 20, 30);
```

Result:

```text
60
```

---

# 16. Spread with `Math.max()`

`Math.max()` expects individual arguments.

Example:

```javascript
Math.max(
    10,
    50,
    20
);
```

Output:

```text
50
```

But suppose:

```javascript
const numbers = [
    10,
    50,
    20
];
```

This does not give the desired result:

```javascript
Math.max(numbers);
```

Instead:

```javascript
Math.max(...numbers);
```

Conceptually:

```javascript
Math.max(
    10,
    50,
    20
);
```

Output:

```text
50
```

---

# 17. Spread with `Math.min()`

Example:

```javascript
const numbers = [
    10,
    50,
    20
];

const minimum =
    Math.min(...numbers);
```

Result:

```text
10
```

---

# 18. Spread with Function Parameters

Example:

```javascript
function createUser(
    name,
    age,
    role
) {
    return {
        name,
        age,
        role
    };
}

const data = [
    "John",
    25,
    "Admin"
];

const user =
    createUser(...data);
```

Result:

```javascript
{
    name: "John",
    age: 25,
    role: "Admin"
}
```

---

# 19. Spread with Objects

Object spread allows properties from one object to be copied into another object.

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const copy = {
    ...user
};
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

---

# 20. Copying Objects

Without spread:

```javascript
const user = {
    name: "John"
};

const copy = user;
```

Check:

```javascript
console.log(
    user === copy
);
```

Output:

```text
true
```

Both reference the same object.

Using spread:

```javascript
const user = {
    name: "John"
};

const copy = {
    ...user
};
```

Check:

```javascript
console.log(
    user === copy
);
```

Output:

```text
false
```

A new object was created.

---

# 21. Updating a Copied Object

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const updatedUser = {
    ...user,
    age: 26
};
```

Result:

```javascript
{
    name: "John",
    age: 26
}
```

Original:

```javascript
console.log(user);
```

remains:

```javascript
{
    name: "John",
    age: 25
}
```

---

# 22. Property Override Order

This is extremely important.

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const updated = {
    ...user,
    age: 30
};
```

Result:

```javascript
{
    name: "John",
    age: 30
}
```

Why?

Because:

```javascript
age: 30
```

appears **after**:

```javascript
...user
```

Later properties overwrite earlier properties with the same key.

---

# 23. Spread Order Matters

Compare:

```javascript
const user = {
    name: "John",
    age: 25
};
```

### Example 1

```javascript
const updated = {
    ...user,
    age: 30
};
```

Result:

```javascript
{
    name: "John",
    age: 30
}
```

### Example 2

```javascript
const updated = {
    age: 30,
    ...user
};
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

Because:

```javascript
...user
```

comes later and overwrites:

```javascript
age: 30
```

with:

```javascript
age: 25
```

---

# 24. Merging Objects

Example:

```javascript
const personalInfo = {
    name: "John",
    age: 25
};

const accountInfo = {
    email: "john@example.com",
    role: "Admin"
};

const user = {
    ...personalInfo,
    ...accountInfo
};
```

Result:

```javascript
{
    name: "John",
    age: 25,
    email: "john@example.com",
    role: "Admin"
}
```

---

# 25. Merging Objects with Duplicate Keys

Example:

```javascript
const first = {
    name: "John",
    role: "User"
};

const second = {
    role: "Admin",
    active: true
};

const result = {
    ...first,
    ...second
};
```

Result:

```javascript
{
    name: "John",
    role: "Admin",
    active: true
}
```

Because:

```javascript
...second
```

comes later.

Therefore:

```text
Later property wins.
```

---

# 26. Adding New Properties

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const updatedUser = {
    ...user,
    role: "Admin",
    active: true
};
```

Result:

```javascript
{
    name: "John",
    age: 25,
    role: "Admin",
    active: true
}
```

---

# 27. Removing Properties with Spread and Destructuring

Spread itself does not directly remove properties.

But object destructuring with rest syntax can be used.

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    password: "secret"
};

const {
    password,
    ...safeUser
} = user;
```

Now:

```javascript
console.log(safeUser);
```

Output:

```javascript
{
    name: "John",
    age: 25
}
```

Here:

```javascript
...safeUser
```

is **rest syntax**, not spread syntax.

We will cover rest separately.

---

# 28. Spread Creates a Shallow Copy

This is one of the most important concepts.

Consider:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};
```

Copy:

```javascript
const copy = {
    ...user
};
```

At the top level:

```javascript
user !== copy
```

But the nested object is still shared:

```javascript
user.address === copy.address
```

Output:

```text
true
```

---

# 29. Shallow Copy Mental Model

Original:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};
```

After:

```javascript
const copy = {
    ...user
};
```

Think:

```text
user
│
├── name: "John"
│
└── address ─────┐
                 ▼
             {
               city: "New York"
             }
                 ▲
                 │
copy             │
│                │
├── name: "John" │
└── address ─────┘
```

The outer objects are different.

The nested `address` object is shared.

---

# 30. Shallow Copy Problem

Example:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};

const copy = {
    ...user
};

copy.address.city =
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

Why?

Because both objects reference the same nested `address` object.

---

# 31. Copying Nested Objects

If you need to update the nested object immutably:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York",
        country: "USA"
    }
};

const updatedUser = {
    ...user,

    address: {
        ...user.address,
        city: "Chicago"
    }
};
```

Now:

```javascript
console.log(
    user.address.city
);
```

Output:

```text
New York
```

And:

```javascript
console.log(
    updatedUser.address.city
);
```

Output:

```text
Chicago
```

---

# 32. Nested Spread Mental Model

Example:

```javascript
const updatedUser = {
    ...user,

    address: {
        ...user.address,
        city: "Chicago"
    }
};
```

Think:

```text
Create new user object
        ↓
Copy user properties
        ↓
Create new address object
        ↓
Copy address properties
        ↓
Replace city
        ↓
Assign new address to new user
```

Now:

```text
user
 ↓
old address


updatedUser
 ↓
new address
```

---

# 33. Spread Is Not Deep Cloning

This:

```javascript
const copy = {
    ...original
};
```

does **not** recursively clone every nested object.

Likewise:

```javascript
const copy = [
    ...original
];
```

does not recursively clone nested arrays or objects.

Spread performs a:

```text
Shallow Copy
```

---

# 34. Nested Array Example

```javascript
const original = [
    [1, 2],
    [3, 4]
];

const copy = [
    ...original
];
```

Check:

```javascript
console.log(
    original === copy
);
```

Output:

```text
false
```

But:

```javascript
console.log(
    original[0] === copy[0]
);
```

Output:

```text
true
```

The nested arrays are shared.

---

# 35. Modifying Nested Array

Example:

```javascript
const original = [
    [1, 2],
    [3, 4]
];

const copy = [
    ...original
];

copy[0].push(100);
```

Now:

```javascript
console.log(original);
```

Output:

```javascript
[
    [1, 2, 100],
    [3, 4]
]
```

Again, spread only copied the outer array.

---

# 36. Deep Copying

For supported values, modern JavaScript provides:

```javascript
structuredClone()
```

Example:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};

const copy =
    structuredClone(user);
```

Now:

```javascript
console.log(
    user.address === copy.address
);
```

Output:

```text
false
```

`structuredClone()` performs a deep clone for many built-in structured data types.

---

# 37. `structuredClone()` Is Not Universal

`structuredClone()` cannot clone every possible JavaScript value.

For example, functions cannot normally be cloned with it.

Example:

```javascript
const object = {
    greet() {
        console.log("Hello");
    }
};
```

Trying to structured-clone such an object can throw a `DataCloneError`.

So choose cloning techniques based on the data being handled.

---

# 38. Spread vs `Object.assign()`

Before object spread became common:

```javascript
const copy =
    Object.assign({}, user);
```

With spread:

```javascript
const copy = {
    ...user
};
```

Both can create a shallow copy.

Spread is often easier to read.

---

# 39. Merging with `Object.assign()`

Example:

```javascript
const result =
    Object.assign(
        {},
        first,
        second
    );
```

Spread equivalent:

```javascript
const result = {
    ...first,
    ...second
};
```

Both use shallow property copying.

---

# 40. Spread with Iterables

Array/function-call spread works with iterable values.

Common iterables include:

```text
Array
String
Set
Map
TypedArray
```

Example:

```javascript
const set =
    new Set([1, 2, 3]);

const array = [
    ...set
];
```

Result:

```javascript
[1, 2, 3]
```

---

# 41. Spread with `Set`

Example:

```javascript
const set = new Set([
    "JavaScript",
    "React",
    "Node.js"
]);

const technologies = [
    ...set
];
```

Result:

```javascript
[
    "JavaScript",
    "React",
    "Node.js"
]
```

---

# 42. Spread with `Map`

A `Map` is iterable.

Example:

```javascript
const map = new Map([
    ["name", "John"],
    ["age", 25]
]);

const entries = [
    ...map
];
```

Result:

```javascript
[
    ["name", "John"],
    ["age", 25]
]
```

Each map entry becomes an array:

```javascript
[key, value]
```

---

# 43. Object Spread Is Different from Iterable Spread

Arrays and strings can be spread into arrays because they are iterable.

Example:

```javascript
[..."ABC"]
```

Result:

```javascript
["A", "B", "C"]
```

Plain objects are not normally iterable.

Therefore:

```javascript
const user = {
    name: "John"
};

const result = [
    ...user
];
```

throws an error.

But object spread:

```javascript
const result = {
    ...user
};
```

works because object spread copies enumerable own properties.

---

# 44. Object Spread with Arrays

Arrays are objects with enumerable index properties.

Therefore:

```javascript
const numbers = [
    10,
    20,
    30
];

const object = {
    ...numbers
};
```

Result:

```javascript
{
    0: 10,
    1: 20,
    2: 30
}
```

The array indexes become property keys.

---

# 45. Object Spread with Strings

Example:

```javascript
const object = {
    ..."ABC"
};
```

Result:

```javascript
{
    0: "A",
    1: "B",
    2: "C"
}
```

The string's indexed characters become properties.

---

# 46. Spread and `null` / `undefined`

In modern JavaScript, object spread can safely ignore `null` and `undefined`.

Example:

```javascript
const result = {
    ...null,
    ...undefined,
    name: "John"
};
```

Result:

```javascript
{
    name: "John"
}
```

However, array spread requires an iterable.

This fails:

```javascript
const result = [
    ...null
];
```

because `null` is not iterable.

---

# 47. Conditional Object Spread

A useful pattern:

```javascript
const isAdmin = true;

const user = {
    name: "John",

    ...(isAdmin && {
        role: "Admin"
    })
};
```

Result:

```javascript
{
    name: "John",
    role: "Admin"
}
```

If:

```javascript
isAdmin = false;
```

then the additional property is not included.

---

# 48. Clearer Conditional Spread

Another approach:

```javascript
const user = {
    name: "John",

    ...(isAdmin
        ? { role: "Admin" }
        : {})
};
```

This explicitly spreads one of two objects.

---

# 49. Spread in React State

This is one of the most important React uses.

Suppose:

```javascript
const [user, setUser] =
    useState({
        name: "John",
        age: 25
    });
```

You generally should not mutate:

```javascript
user.age = 26;
```

Instead create a new object:

```javascript
setUser({
    ...user,
    age: 26
});
```

This creates a new state object.

---

# 50. Why Spread Matters in React

React state updates commonly rely on replacing objects/arrays rather than mutating existing state.

Instead of:

```javascript
user.age = 26;
setUser(user);
```

use:

```javascript
setUser({
    ...user,
    age: 26
});
```

Conceptually:

```text
Old state
   │
   ▼
{ name, age: 25 }


Spread
   │
   ▼
Create new object
   │
   ▼
Override age
   │
   ▼
{ name, age: 26 }
```

---

# 51. Safer React State Update

When new state depends on previous state, prefer the functional updater form:

```javascript
setUser((previousUser) => ({
    ...previousUser,
    age: 26
}));
```

This ensures the update uses the latest state value React provides.

---

# 52. Updating Nested React State

Suppose:

```javascript
const [user, setUser] =
    useState({
        name: "John",

        address: {
            city: "New York",
            country: "USA"
        }
    });
```

To update `city`:

```javascript
setUser((previousUser) => ({
    ...previousUser,

    address: {
        ...previousUser.address,
        city: "Chicago"
    }
}));
```

Both the outer object and nested object being changed are copied.

---

# 53. Adding Items to React Array State

Suppose:

```javascript
const [users, setUsers] =
    useState([
        "John",
        "Alice"
    ]);
```

Add:

```javascript
setUsers((previousUsers) => [
    ...previousUsers,
    "Bob"
]);
```

Result:

```javascript
[
    "John",
    "Alice",
    "Bob"
]
```

---

# 54. Adding Item at Beginning

```javascript
setUsers((previousUsers) => [
    "Bob",
    ...previousUsers
]);
```

Result:

```javascript
[
    "Bob",
    "John",
    "Alice"
]
```

---

# 55. Updating Array Objects in React

Suppose:

```javascript
const users = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Alice"
    }
];
```

Update user `2`:

```javascript
const updatedUsers =
    users.map((user) =>
        user.id === 2
            ? {
                ...user,
                name: "Bob"
            }
            : user
    );
```

Result:

```javascript
[
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Bob"
    }
]
```

Here spread copies the object that needs modification.

---

# 56. Spread with React Props

Suppose:

```javascript
const user = {
    name: "John",
    age: 25
};
```

Instead of:

```jsx
<User
    name={user.name}
    age={user.age}
/>
```

you can write:

```jsx
<User {...user} />
```

Conceptually:

```jsx
<User
    name="John"
    age={25}
/>
```

This is called spreading props.

---

# 57. Use Prop Spreading Carefully

This:

```jsx
<Component {...props} />
```

can be convenient.

But excessive prop spreading can make it harder to see what a component receives.

Sometimes explicit props are clearer:

```jsx
<User
    name={user.name}
    age={user.age}
/>
```

Choose based on readability and component design.

---

# 58. Spread and Immutability

Spread is commonly used to support **immutable update patterns**.

Mutation:

```javascript
const user = {
    name: "John"
};

user.name = "Alice";
```

Immutable-style update:

```javascript
const updatedUser = {
    ...user,
    name: "Alice"
};
```

Now:

```text
user
→ old object

updatedUser
→ new object
```

---

# 59. Spread Does Not Make Data Immutable

Important:

Spread does not prevent objects from being modified.

Example:

```javascript
const user = {
    name: "John"
};

const copy = {
    ...user
};

copy.name = "Alice";
```

This is allowed.

Spread simply creates a new shallow object.

It does not freeze it.

---

# 60. `Object.freeze()` Is Different

Example:

```javascript
const user =
    Object.freeze({
        name: "John"
    });
```

This relates to preventing changes to the object itself.

Spread:

```javascript
const copy = {
    ...user
};
```

means:

```text
Create a shallow copy
```

These are different concepts.

Also, `Object.freeze()` itself is shallow unless nested values are frozen separately.

---

# 61. Spread vs Rest

Both use:

```javascript
...
```

But they do opposite things.

### Spread

```javascript
const numbers = [
    1,
    2,
    3
];

console.log(
    ...numbers
);
```

Conceptually:

```text
[1, 2, 3]
      ↓
1, 2, 3
```

Spread expands.

### Rest

```javascript
function sum(
    ...numbers
) {
}
```

Conceptually:

```text
1, 2, 3
      ↓
[1, 2, 3]
```

Rest collects.

---

# 62. Spread vs Rest Mental Model

Remember:

```text
SPREAD

Container
   ↓
...values
   ↓
Individual values
```

```text
REST

Individual values
   ↓
...values
   ↓
Container
```

Same syntax:

```javascript
...
```

Different context.

---

# 63. Spread Example

```javascript
const numbers = [
    1,
    2,
    3
];

const copy = [
    ...numbers
];
```

Here:

```javascript
...numbers
```

is spread because it **expands** `numbers`.

---

# 64. Rest Example

```javascript
const [
    first,
    ...remaining
] = [
    1,
    2,
    3,
    4
];
```

Result:

```javascript
first === 1
```

and:

```javascript
remaining
```

is:

```javascript
[2, 3, 4]
```

Here:

```javascript
...remaining
```

collects the remaining values.

Therefore it is rest syntax.

---

# 65. Common Mistake — Thinking Spread Deep Copies

Wrong assumption:

```javascript
const copy = {
    ...original
};
```

means:

```text
Everything recursively copied.
```

It does not.

Correct:

```text
Spread creates a shallow copy.
```

Nested objects may still be shared.

---

# 66. Common Mistake — Wrong Override Order

Suppose:

```javascript
const user = {
    name: "John",
    role: "User"
};
```

You want:

```text
role = Admin
```

Correct:

```javascript
const updated = {
    ...user,
    role: "Admin"
};
```

Potential mistake:

```javascript
const updated = {
    role: "Admin",
    ...user
};
```

Result:

```text
role = User
```

because the later spread overwrites the earlier property.

---

# 67. Common Mistake — Mutating Before Spreading

Bad pattern:

```javascript
user.age = 30;

const updated = {
    ...user
};
```

The original object was already mutated.

Better:

```javascript
const updated = {
    ...user,
    age: 30
};
```

Now the original remains unchanged.

---

# 68. Common Mistake — Array Spread on Plain Object

Wrong:

```javascript
const user = {
    name: "John"
};

const array = [
    ...user
];
```

A plain object is not iterable.

This throws a:

```text
TypeError
```

Use object spread:

```javascript
const copy = {
    ...user
};
```

---

# 69. Common Mistake — Assuming Spread Removes Duplicates

Example:

```javascript
const result = [
    ...[1, 2],
    ...[2, 3]
];
```

Result:

```javascript
[1, 2, 2, 3]
```

If uniqueness is required:

```javascript
const result = [
    ...new Set([
        1,
        2,
        2,
        3
    ])
];
```

Result:

```javascript
[1, 2, 3]
```

---

# 70. Common Mistake — Overusing Spread

Suppose:

```javascript
const numbers = [
    1,
    2,
    3
];
```

If you only need to read:

```javascript
numbers[0]
```

there is no reason to create:

```javascript
const copy = [
    ...numbers
];
```

Spread creates a new container when used for copying.

Use it when the copy or expansion is actually needed.

---

# 71. Interview Question 1

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

console.log(...numbers);
```

Answer:

```text
1 2 3
```

---

# 72. Interview Question 2

What is the output?

```javascript
const original = [
    1,
    2,
    3
];

const copy = [
    ...original
];

console.log(
    original === copy
);
```

Answer:

```text
false
```

Spread created a new outer array.

---

# 73. Interview Question 3

What is the output?

```javascript
const first = [
    1,
    2
];

const second = [
    3,
    4
];

const result = [
    ...first,
    ...second
];

console.log(result);
```

Answer:

```javascript
[1, 2, 3, 4]
```

---

# 74. Interview Question 4

What is the output?

```javascript
const user = {
    name: "John",
    age: 25
};

const updated = {
    ...user,
    age: 30
};

console.log(updated.age);
```

Answer:

```text
30
```

The later property overrides the spread property.

---

# 75. Interview Question 5

What is the output?

```javascript
const user = {
    name: "John",
    age: 25
};

const updated = {
    age: 30,
    ...user
};

console.log(updated.age);
```

Answer:

```text
25
```

`...user` comes later and overwrites `age`.

---

# 76. Interview Question 6

What is the output?

```javascript
const original = {
    nested: {
        value: 10
    }
};

const copy = {
    ...original
};

console.log(
    original.nested
        === copy.nested
);
```

Answer:

```text
true
```

Spread performs a shallow copy.

---

# 77. Interview Question 7

What is the output?

```javascript
const original = {
    nested: {
        value: 10
    }
};

const copy = {
    ...original
};

copy.nested.value = 100;

console.log(
    original.nested.value
);
```

Answer:

```text
100
```

The nested object is shared.

---

# 78. Interview Question 8

What is the output?

```javascript
const numbers = [
    10,
    50,
    20
];

console.log(
    Math.max(...numbers)
);
```

Answer:

```text
50
```

---

# 79. Interview Question 9

What is the output?

```javascript
const letters = [
    ..."ABC"
];

console.log(letters);
```

Answer:

```javascript
["A", "B", "C"]
```

Strings are iterable.

---

# 80. Interview Question 10

What is the output?

```javascript
const numbers = [
    1,
    2,
    2,
    3
];

const unique = [
    ...new Set(numbers)
];

console.log(unique);
```

Answer:

```javascript
[1, 2, 3]
```

---

# 81. Interview Question 11

What happens?

```javascript
const user = {
    name: "John"
};

const values = [
    ...user
];
```

Answer:

```text
TypeError
```

A plain object is not iterable and therefore cannot be spread into an array this way.

---

# 82. Interview Question 12

What is the output?

```javascript
const numbers = [
    10,
    20,
    30
];

const object = {
    ...numbers
};

console.log(object);
```

Answer:

```javascript
{
    0: 10,
    1: 20,
    2: 30
}
```

---

# 83. Interview Question 13

What is the output?

```javascript
const first = {
    a: 1,
    b: 2
};

const second = {
    b: 100,
    c: 3
};

const result = {
    ...first,
    ...second
};

console.log(result);
```

Answer:

```javascript
{
    a: 1,
    b: 100,
    c: 3
}
```

The later `b` value wins.

---

# 84. Interview Question 14

What is the output?

```javascript
const original = [
    [1, 2]
];

const copy = [
    ...original
];

copy[0].push(3);

console.log(original);
```

Answer:

```javascript
[
    [1, 2, 3]
]
```

The inner array was not cloned.

---

# 85. Interview Question 15

Is this spread or rest?

```javascript
const copy = [
    ...numbers
];
```

Answer:

```text
Spread
```

because the values of `numbers` are expanded.

---

# 86. Interview Question 16

Is this spread or rest?

```javascript
function sum(
    ...numbers
) {
}
```

Answer:

```text
Rest
```

because multiple arguments are collected into one array.

---

# 87. Spread Mental Model

Array:

```javascript
const numbers = [
    1,
    2,
    3
];

const result = [
    0,
    ...numbers,
    4
];
```

Think:

```text
numbers
   │
   ▼
[1, 2, 3]
   │
   │ spread
   ▼
1, 2, 3
   │
   ▼
[0, 1, 2, 3, 4]
```

Object:

```javascript
const user = {
    name: "John",
    age: 25
};

const updated = {
    ...user,
    age: 30
};
```

Think:

```text
user properties
      │
      ▼
name: John
age: 25
      │
      │ spread
      ▼
new object
      │
      ▼
age: 30 overrides age: 25
      │
      ▼
{
    name: "John",
    age: 30
}
```

---

# 88. Spread Cheat Sheet

### Copy Array

```javascript
const copy = [
    ...array
];
```

### Combine Arrays

```javascript
const combined = [
    ...first,
    ...second
];
```

### Add Beginning

```javascript
const result = [
    newItem,
    ...array
];
```

### Add End

```javascript
const result = [
    ...array,
    newItem
];
```

### Function Arguments

```javascript
functionName(...array);
```

### Maximum

```javascript
Math.max(...numbers);
```

### Copy Object

```javascript
const copy = {
    ...object
};
```

### Update Object

```javascript
const updated = {
    ...object,
    property: newValue
};
```

### Merge Objects

```javascript
const result = {
    ...first,
    ...second
};
```

### Update Nested Object

```javascript
const updated = {
    ...object,

    nested: {
        ...object.nested,
        value: newValue
    }
};
```

### Remove Duplicates

```javascript
const unique = [
    ...new Set(array)
];
```

### String to Array

```javascript
const letters = [
    ...string
];
```

---

# 89. Important Rules to Remember

```text
1. Spread syntax uses ...

2. Spread expands values.

3. Arrays can be spread into arrays.

4. Iterables can be spread into function arguments.

5. Strings are iterable and can be spread.

6. Sets are iterable and can be spread.

7. Maps are iterable and can be spread.

8. Plain objects cannot be spread into arrays because they are not iterable.

9. Object spread copies own enumerable properties.

10. Spread can create shallow array copies.

11. Spread can create shallow object copies.

12. Spread does not create deep copies.

13. Nested objects may remain shared.

14. Nested arrays may remain shared.

15. Multiple arrays can be combined using spread.

16. Multiple objects can be merged using spread.

17. Later object properties overwrite earlier properties with the same key.

18. Spread can pass array elements as function arguments.

19. Spread works well with Math.max() and Math.min().

20. Spread can convert iterable values such as Set into arrays.

21. Spread combined with Set can remove duplicate primitive values.

22. Object spread and iterable spread operate in different contexts.

23. Spread is heavily used for immutable update patterns.

24. Spread is heavily used in React state updates.

25. Spread is used to add items to array state without mutating the original array.

26. Spread is used to update object state without mutating the original object.

27. Nested state updates may require spreading each changed level.

28. Props can be spread into React components.

29. Excessive prop spreading can reduce readability.

30. Spread does not make an object immutable.

31. Spread and Object.freeze() solve different problems.

32. Spread and rest use the same ... syntax.

33. Spread expands values.

34. Rest collects values.

35. The meaning of ... depends on where it appears.
```

---

# 90. Most Important Concept

Remember:

```text
Spread = Expand
```

Array:

```javascript
const numbers = [
    1,
    2,
    3
];

const result = [
    ...numbers
];
```

Conceptually:

```text
[1, 2, 3]
     ↓
   spread
     ↓
 1, 2, 3
     ↓
new array
```

Object:

```javascript
const updated = {
    ...user,
    age: 30
};
```

Conceptually:

```text
user properties
      ↓
    spread
      ↓
new object
      ↓
override age
```

And always remember:

```text
Spread copy
    ≠
Deep copy
```

Spread creates a **shallow copy**.

---

# 91. Spread Syntax in One Sentence

> Spread syntax (`...`) expands values from iterables or copies properties from objects into a new context, making it especially useful for combining data, creating shallow copies, passing function arguments, and performing immutable-style updates.