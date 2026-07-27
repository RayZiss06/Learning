# Immutability

**Immutability** means that once a value or data structure exists, we avoid modifying that existing value directly.

Instead of changing existing data:

```javascript
const user = {
    name: "Alice",
    age: 25
};

user.age = 26;
```

we create a new value:

```javascript
const updatedUser = {
    ...user,
    age: 26
};
```

Now:

```javascript
console.log(user.age);
// 25

console.log(updatedUser.age);
// 26
```

The original object remains unchanged.

---

# 1. Mutation vs Immutability

## Mutation

Mutation means changing existing data.

```javascript
const user = {
    name: "Alice",
    age: 25
};

user.age = 26;
```

The same object has been modified.

```text
Before

user
 │
 ▼
{
    name: "Alice",
    age: 25
}


After

user
 │
 ▼
{
    name: "Alice",
    age: 26
}
```

---

## Immutable Update

Instead:

```javascript
const updatedUser = {
    ...user,
    age: 26
};
```

Now:

```text
user
 │
 ▼
{
    name: "Alice",
    age: 25
}


updatedUser
 │
 ▼
{
    name: "Alice",
    age: 26
}
```

Two separate objects exist.

---

# 2. Why Immutability Matters

Immutability makes state changes easier to reason about.

Instead of:

```text
Object
  │
  ▼
Changed
  │
  ▼
Changed Again
  │
  ▼
Changed Again
```

we can think in terms of versions:

```text
State V1
   │
   ▼
State V2
   │
   ▼
State V3
```

Each version represents a particular state of the data.

This helps with:

* Predictability
* Debugging
* State management
* Change detection
* Memoization
* Undo/redo
* Testing
* React rendering

---

# 3. JavaScript Is Not an Immutable Language

JavaScript does **not** make objects and arrays immutable by default.

```javascript
const user = {
    name: "Alice"
};

user.name = "Bob";
```

This is valid.

Arrays can also be mutated:

```javascript
const numbers = [
    1,
    2,
    3
];

numbers.push(4);
```

JavaScript allows mutation.

Immutability is usually a **programming strategy** we deliberately follow.

---

# 4. Primitive Values

JavaScript primitive types include:

```text
string
number
boolean
undefined
null
bigint
symbol
```

Primitive values themselves are immutable.

Example:

```javascript
let name = "Alice";

name.toUpperCase();

console.log(name);
```

Output:

```text
Alice
```

`toUpperCase()` did not modify the existing string.

It returned a new string.

```javascript
const upperName =
    name.toUpperCase();

console.log(upperName);
```

Output:

```text
ALICE
```

---

# 5. Primitive Reassignment Is Not Mutation

Consider:

```javascript
let count = 10;

count = 20;
```

The number `10` was not modified into `20`.

Instead:

```text
Before

count
  │
  ▼
 10


After

count
  │
  ▼
 20
```

The variable binding was changed to another primitive value.

---

# 6. Reference Values

Objects and arrays are reference values.

Example:

```javascript
const user = {
    name: "Alice"
};
```

Conceptually:

```text
user
 │
 │ reference
 ▼

Memory
┌─────────────────┐
│ name: "Alice"   │
└─────────────────┘
```

The variable stores a reference to the object.

This becomes extremely important when discussing mutation.

---

# 7. Multiple References to the Same Object

```javascript
const user = {
    name: "Alice"
};

const anotherUser = user;
```

Now:

```text
user ──────────┐
               ▼
           Object
               ▲
anotherUser ───┘
```

Both variables reference the **same object**.

Therefore:

```javascript
anotherUser.name = "Bob";
```

also affects:

```javascript
user.name;
```

Output:

```text
Bob
```

---

# 8. Assignment Does Not Copy Objects

This:

```javascript
const user2 = user1;
```

does **not** create another object.

It copies the reference.

Example:

```javascript
const user1 = {
    name: "Alice"
};

const user2 = user1;

user2.name = "Bob";

console.log(
    user1.name
);
```

Output:

```text
Bob
```

Because:

```text
user1 ──┐
        ▼
      Object
        ▲
user2 ──┘
```

---

# 9. Reference Equality

Consider:

```javascript
const user1 = {
    name: "Alice"
};

const user2 = {
    name: "Alice"
};
```

Then:

```javascript
console.log(
    user1 === user2
);
```

Output:

```text
false
```

Even though their contents look identical, they are different objects.

```text
user1
 │
 ▼
Object A


user2
 │
 ▼
Object B
```

Therefore:

```javascript
user1 !== user2;
```

---

# 10. Same Reference Equality

```javascript
const user1 = {
    name: "Alice"
};

const user2 = user1;
```

Then:

```javascript
console.log(
    user1 === user2
);
```

Output:

```text
true
```

Because both variables reference the same object.

---

# 11. Why Reference Equality Matters

Suppose:

```javascript
const oldState = {
    count: 10
};

const newState = oldState;

newState.count = 20;
```

Then:

```javascript
oldState === newState;
```

is:

```text
true
```

Even though the object's content changed.

A system checking only:

```javascript
oldState === newState
```

cannot tell that you intended to create a new state version.

This is one reason immutable updates are important in state management.

---

# 12. Immutable Object Update

Instead:

```javascript
const oldState = {
    count: 10
};

const newState = {
    ...oldState,
    count: 20
};
```

Now:

```javascript
oldState === newState;
```

is:

```text
false
```

And:

```javascript
oldState.count;
// 10

newState.count;
// 20
```

This gives us:

```text
Old Reference
      │
      ▼
Old State


New Reference
      │
      ▼
New State
```

---

# 13. Spread Syntax for Objects

A common immutable update technique:

```javascript
const user = {
    name: "Alice",
    age: 25,
    active: true
};

const updatedUser = {
    ...user,
    age: 26
};
```

The spread:

```javascript
...user
```

copies the object's enumerable own properties into a new object.

Then:

```javascript
age: 26
```

overwrites the copied `age`.

---

# 14. Property Order Matters

This:

```javascript
const updatedUser = {
    ...user,
    age: 30
};
```

means the new `age` wins.

But:

```javascript
const updatedUser = {
    age: 30,
    ...user
};
```

means `user.age` can overwrite `30`.

Example:

```javascript
const user = {
    age: 25
};

const updated = {
    age: 30,
    ...user
};

console.log(
    updated.age
);
```

Output:

```text
25
```

Later properties overwrite earlier properties.

---

# 15. `Object.assign()`

Another way to create a shallow copy:

```javascript
const updatedUser =
    Object.assign(
        {},
        user,
        {
            age: 26
        }
    );
```

This is similar to:

```javascript
const updatedUser = {
    ...user,
    age: 26
};
```

Spread syntax is generally more readable for ordinary object updates.

---

# 16. Immutable Array Add

Mutating:

```javascript
numbers.push(4);
```

Immutable:

```javascript
const updated = [
    ...numbers,
    4
];
```

Example:

```javascript
const numbers = [
    1,
    2,
    3
];

const updated = [
    ...numbers,
    4
];
```

Result:

```text
numbers
[1, 2, 3]

updated
[1, 2, 3, 4]
```

---

# 17. Add to Beginning

Mutating:

```javascript
numbers.unshift(0);
```

Immutable:

```javascript
const updated = [
    0,
    ...numbers
];
```

---

# 18. Remove an Array Item

Suppose:

```javascript
const users = [
    {
        id: 1,
        name: "Alice"
    },
    {
        id: 2,
        name: "Bob"
    },
    {
        id: 3,
        name: "Charlie"
    }
];
```

Remove user `2`:

```javascript
const updatedUsers =
    users.filter(
        user =>
            user.id !== 2
    );
```

The original array remains unchanged.

---

# 19. Update an Array Item

```javascript
const updatedUsers =
    users.map(
        user =>
            user.id === 2
                ? {
                    ...user,
                    name: "Robert"
                }
                : user
    );
```

Important:

Only the changed object receives a new reference.

Unchanged objects are reused.

---

# 20. Structural Sharing

The previous example demonstrates **structural sharing**.

Suppose:

```text
Original Array

[
    User A,
    User B,
    User C
]
```

Only `User B` changes.

We do not need:

```text
New User A
New User B
New User C
```

Instead:

```text
Old Array
│
├── User A ──────────┐
├── User B           │
└── User C ───────┐  │
                  │  │
New Array         │  │
│                 │  │
├─────────────────┘  │
├── New User B       │
└────────────────────┘
```

The new array reuses unchanged objects.

This is called **structural sharing**.

---

# 21. Why Structural Sharing Matters

Imagine an object containing:

```text
100,000 records
```

If one record changes, copying every nested object would be wasteful.

Instead:

```text
New Root
   │
   ├── Old Unchanged Data
   ├── Old Unchanged Data
   ├── New Changed Data
   └── Old Unchanged Data
```

Only the path that changed needs new references.

This reduces:

* Memory usage
* Allocation
* Copying
* Processing

while maintaining immutable semantics.

---

# 22. Shallow Copy

Consider:

```javascript
const user = {
    name: "Alice",

    address: {
        city: "Kolkata"
    }
};
```

Now:

```javascript
const copy = {
    ...user
};
```

We created a new top-level object.

Therefore:

```javascript
copy === user;
```

is:

```text
false
```

But:

```javascript
copy.address ===
user.address;
```

is:

```text
true
```

because spread performs a **shallow copy**.

---

# 23. Shallow Copy Mental Model

```text
user
 │
 ▼
┌────────────────────┐
│ name: "Alice"      │
│ address ───────────────┐
└────────────────────┘   │
                         ▼
                    Address Object


copy
 │
 ▼
┌────────────────────┐
│ name: "Alice"      │
│ address ───────────────┘
└────────────────────┘
```

`user` and `copy` are separate objects.

But their `address` properties reference the same nested object.

---

# 24. Shallow Copy Mutation Bug

```javascript
const copy = {
    ...user
};

copy.address.city =
    "Delhi";
```

Now:

```javascript
console.log(
    user.address.city
);
```

Output:

```text
Delhi
```

Why?

Because:

```javascript
copy.address ===
user.address;
```

is:

```text
true
```

---

# 25. Immutable Nested Object Update

To change the nested city:

```javascript
const updatedUser = {
    ...user,

    address: {
        ...user.address,
        city: "Delhi"
    }
};
```

Now:

```javascript
user.address.city;
// Kolkata

updatedUser.address.city;
// Delhi
```

And:

```javascript
user === updatedUser;
// false
```

```javascript
user.address ===
updatedUser.address;
// false
```

Both changed levels received new references.

---

# 26. Deeply Nested Update

Suppose:

```javascript
const user = {
    id: 1,

    profile: {
        name: "Alice",

        address: {
            city: "Kolkata",
            country: "India"
        }
    }
};
```

Change only the city:

```javascript
const updatedUser = {
    ...user,

    profile: {
        ...user.profile,

        address: {
            ...user.profile.address,

            city: "Delhi"
        }
    }
};
```

Notice the rule:

> Every object on the path from the root to the changed value must receive a new reference.

---

# 27. Nested Update Mental Model

Changing:

```text
user.profile.address.city
```

means creating new:

```text
user
  │
  ▼
profile
  │
  ▼
address
```

while unchanged values can be reused.

```text
Old User
  │
  └── Old Profile
         │
         └── Old Address


New User
  │
  └── New Profile
         │
         └── New Address
                  │
                  ├── city changed
                  └── country reused
```

---

# 28. Do Not Deep Copy Everything

You could theoretically deep clone the entire object every time.

But that is usually unnecessary.

If only:

```text
user.profile.address.city
```

changes, we only need new references along that path.

This is structural sharing.

---

# 29. Deep Copy

A **deep copy** recursively copies nested data so nested references are not shared.

Conceptually:

```text
Original

Root
 ├── Object A
 │    └── Object B
 └── Object C


Deep Copy

New Root
 ├── New Object A
 │    └── New Object B
 └── New Object C
```

No nested object references are shared.

---

# 30. `structuredClone()`

Modern JavaScript provides:

```javascript
const copy =
    structuredClone(
        original
    );
```

Example:

```javascript
const user = {
    name: "Alice",

    address: {
        city: "Kolkata"
    }
};

const copy =
    structuredClone(user);
```

Now:

```javascript
copy === user;
// false
```

and:

```javascript
copy.address ===
user.address;
// false
```

---

# 31. What `structuredClone()` Supports

`structuredClone()` handles many values including:

```text
Objects
Arrays
Date
Map
Set
ArrayBuffer
Typed arrays
Circular references
```

Example:

```javascript
const original = {
    createdAt:
        new Date(),

    tags:
        new Set([
            "javascript",
            "react"
        ])
};

const copy =
    structuredClone(
        original
    );
```

---

# 32. `structuredClone()` Limitations

It is not a universal cloning mechanism for every JavaScript value.

For example, functions cannot be cloned:

```javascript
const object = {
    greet() {
        console.log(
            "Hello"
        );
    }
};
```

Trying to structured-clone such values can throw a `DataCloneError`.

Also remember:

```text
Deep Clone
≠
Automatically Better
```

For immutable state updates, structural sharing is usually preferable.

---

# 33. JSON Deep Copy Trick

You may see:

```javascript
const copy =
    JSON.parse(
        JSON.stringify(
            original
        )
    );
```

This should **not** be treated as a general deep-cloning solution.

It can lose or alter data.

Examples include:

```text
undefined
functions
symbols
BigInt
Date type information
Map
Set
special numeric values
circular references
```

Use:

```javascript
structuredClone()
```

when you actually need a supported deep clone.

---

# 34. Why Deep Cloning Every State Update Is Bad

Suppose state contains:

```text
Application State
│
├── 50,000 Users
├── Settings
├── Notifications
├── Products
└── Orders
```

Only:

```text
Settings.theme
```

changes.

Deep cloning everything would recreate all nested data unnecessarily.

Structural sharing allows:

```text
New State
│
├── Old Users
├── New Settings
├── Old Notifications
├── Old Products
└── Old Orders
```

Much more efficient.

---

# 35. Mutating Array Methods

These common array methods mutate the original array:

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

numbers.sort(
    (a, b) =>
        a - b
);
```

`numbers` itself changes.

---

# 36. Non-Mutating Array Methods

Common methods that return new arrays or values include:

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

But remember:

> A non-mutating method does not prevent your callback from mutating referenced objects.

---

# 37. `toSorted()`

Traditional:

```javascript
const numbers = [
    3,
    1,
    2
];

numbers.sort();
```

mutates `numbers`.

Immutable version:

```javascript
const sorted =
    numbers.toSorted(
        (a, b) =>
            a - b
    );
```

Now:

```javascript
numbers;
// [3, 1, 2]

sorted;
// [1, 2, 3]
```

---

# 38. `toReversed()`

Mutating:

```javascript
numbers.reverse();
```

Immutable:

```javascript
const reversed =
    numbers.toReversed();
```

Original remains unchanged.

---

# 39. `toSpliced()`

`splice()` mutates:

```javascript
numbers.splice(
    1,
    1
);
```

`toSpliced()` returns a new array:

```javascript
const updated =
    numbers.toSpliced(
        1,
        1
    );
```

---

# 40. `with()`

Suppose:

```javascript
const numbers = [
    10,
    20,
    30
];
```

Traditional mutation:

```javascript
numbers[1] = 100;
```

Immutable:

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

Original remains:

```javascript
[
    10,
    20,
    30
]
```

---

# 41. Immutable Insert

Suppose:

```javascript
const numbers = [
    10,
    20,
    30
];
```

Insert `15` at index `1`:

```javascript
const updated = [
    ...numbers.slice(0, 1),

    15,

    ...numbers.slice(1)
];
```

Result:

```javascript
[
    10,
    15,
    20,
    30
]
```

Or:

```javascript
const updated =
    numbers.toSpliced(
        1,
        0,
        15
    );
```

---

# 42. Immutable Remove

```javascript
const updated =
    users.filter(
        user =>
            user.id !== 2
    );
```

Or by index:

```javascript
const updated =
    items.toSpliced(
        index,
        1
    );
```

---

# 43. Immutable Replace

```javascript
const updated =
    users.map(
        user =>
            user.id === id
                ? {
                    ...user,
                    name: newName
                }
                : user
    );
```

Only the matching object is replaced.

---

# 44. Immutable Transform

```javascript
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

Result:

```javascript
[
    2,
    4,
    6
]
```

---

# 45. `const` Does Not Mean Immutable

This is extremely important.

```javascript
const user = {
    name: "Alice"
};
```

You cannot:

```javascript
user = {
    name: "Bob"
};
```

because `const` prevents reassignment.

But you can:

```javascript
user.name = "Bob";
```

because the object itself is still mutable.

---

# 46. `const` Mental Model

```text
const user
    │
    │ Cannot change reference
    ▼
  Object
    │
    │ Properties can change
    ▼
{
    name: "Alice"
}
```

`const` means:

```text
Binding cannot be reassigned
```

not:

```text
Value is deeply immutable
```

---

# 47. `Object.freeze()`

JavaScript provides:

```javascript
Object.freeze();
```

Example:

```javascript
const user =
    Object.freeze({
        name: "Alice",
        age: 25
    });
```

The object's own existing top-level properties cannot be changed, added, or deleted.

For example, in strict mode an attempted assignment such as:

```javascript
user.age = 30;
```

throws a `TypeError`.

Without strict mode, such an assignment may fail silently.

---

# 48. `Object.freeze()` Is Shallow

Consider:

```javascript
const user =
    Object.freeze({
        name: "Alice",

        address: {
            city: "Kolkata"
        }
    });
```

The top-level object is frozen.

But:

```javascript
user.address.city =
    "Delhi";
```

can still modify the nested object because:

```javascript
user.address
```

was not frozen.

---

# 49. Deep Freeze

You can recursively freeze objects:

```javascript
function deepFreeze(object) {
    Object.freeze(object);

    for (
        const value
        of Object.values(object)
    ) {
        if (
            value !== null &&
            typeof value ===
                "object" &&
            !Object.isFrozen(
                value
            )
        ) {
            deepFreeze(value);
        }
    }

    return object;
}
```

Usage:

```javascript
const user =
    deepFreeze({
        name: "Alice",

        address: {
            city: "Kolkata"
        }
    });
```

This is useful in some development/testing scenarios, but recursively freezing large object graphs has costs and caveats.

---

# 50. `Object.isFrozen()`

Check whether an object is frozen:

```javascript
Object.isFrozen(user);
```

Returns:

```text
true
```

or:

```text
false
```

---

# 51. Freeze vs Immutable Programming

`Object.freeze()` and immutable programming are related but not identical.

Immutable programming:

```text
Convention / Design

Do not mutate existing state.
Create new state.
```

`Object.freeze()`:

```text
Runtime restriction

Prevent certain mutations
on a specific object.
```

You do not need to freeze every object to write immutable code.

---

# 52. Map Is Mutable

Consider:

```javascript
const users =
    new Map();

users.set(
    1,
    "Alice"
);
```

`set()` modifies the existing `Map`.

Similarly:

```javascript
users.delete(1);
```

mutates it.

---

# 53. Immutable Map Update

You can create another Map:

```javascript
const users =
    new Map([
        [1, "Alice"],
        [2, "Bob"]
    ]);

const updatedUsers =
    new Map(users);

updatedUsers.set(
    3,
    "Charlie"
);
```

Now the original `users` remains unchanged.

---

# 54. Map Copy Is Shallow

Suppose:

```javascript
const users =
    new Map([
        [
            1,
            {
                name: "Alice"
            }
        ]
    ]);
```

Then:

```javascript
const copy =
    new Map(users);
```

The Map itself is new.

But:

```javascript
copy.get(1) ===
users.get(1);
```

is:

```text
true
```

Nested object values are still shared.

---

# 55. Set Is Mutable

```javascript
const tags =
    new Set([
        "javascript"
    ]);

tags.add("react");
```

This modifies the existing Set.

Other mutating methods include:

```javascript
delete()

clear()
```

---

# 56. Immutable Set Update

```javascript
const updatedTags =
    new Set([
        ...tags,
        "react"
    ]);
```

Or:

```javascript
const updatedTags =
    new Set(tags);

updatedTags.add(
    "react"
);
```

The new Set is modified while the original remains unchanged.

---

# 57. Nested Arrays

Suppose:

```javascript
const matrix = [
    [1, 2],
    [3, 4]
];
```

This:

```javascript
const copy = [
    ...matrix
];
```

only copies the outer array.

Therefore:

```javascript
copy[0] ===
matrix[0];
```

is:

```text
true
```

---

# 58. Updating Nested Arrays

Suppose we want:

```text
1 → 100
```

Instead of:

```javascript
matrix[0][0] = 100;
```

use:

```javascript
const updated =
    matrix.map(
        (row, index) =>
            index === 0
                ? row.with(
                    0,
                    100
                )
                : row
    );
```

Now only the changed row receives a new reference.

---

# 59. Mutation Through Function Parameters

Consider:

```javascript
function activateUser(user) {
    user.active = true;
}
```

Usage:

```javascript
const user = {
    name: "Alice",
    active: false
};

activateUser(user);
```

The function changes the caller's object.

This can create surprising behavior.

---

# 60. Immutable Function

Prefer:

```javascript
function activateUser(user) {
    return {
        ...user,
        active: true
    };
}
```

Then:

```javascript
const activeUser =
    activateUser(user);
```

Data flow is clearer:

```text
Old User
   │
   ▼
Function
   │
   ▼
New User
```

---

# 61. Shared Mutable State

Consider:

```javascript
const settings = {
    theme: "dark"
};
```

Multiple parts of the application may hold references:

```text
Component A ──┐
Component B ──┼──► settings
Component C ──┘
```

If Component B does:

```javascript
settings.theme = "light";
```

all other code observing that object sees the change.

This is **shared mutable state**.

It can make applications harder to reason about.

---

# 62. Problems with Shared Mutable State

Possible problems include:

```text
Unexpected changes

Execution-order bugs

Hard debugging

Hidden dependencies

Race conditions

Difficult testing

Unclear ownership

Stale UI
```

Immutability reduces many of these problems by representing changes as new values.

---

# 63. Aliasing

When multiple references point to the same mutable object, this is sometimes called **aliasing**.

```javascript
const a = {
    count: 0
};

const b = a;

const c = a;
```

Now:

```text
a ──┐
b ──┼──► Object
c ──┘
```

If:

```javascript
b.count++;
```

then:

```javascript
a.count;
// 1

c.count;
// 1
```

Understanding aliasing is crucial when debugging mutation bugs.

---

# 64. Function Arguments and References

Consider:

```javascript
function changeName(user) {
    user.name = "Bob";
}
```

Calling:

```javascript
changeName(user);
```

does not copy the object.

The function receives a value that is a reference to the same object.

Conceptually:

```text
Caller Variable
      │
      ▼
    Object
      ▲
      │
Function Parameter
```

So property mutations are visible to the caller.

---

# 65. JavaScript Is Pass-by-Value

A common misconception is:

```text
Objects are passed by reference.
```

More precisely, JavaScript is **pass-by-value**.

For objects, the value being copied is the reference.

Example:

```javascript
function replace(user) {
    user = {
        name: "Bob"
    };
}

const user = {
    name: "Alice"
};

replace(user);

console.log(
    user.name
);
```

Output:

```text
Alice
```

The parameter's reference value was reassigned.

The caller's variable was not.

---

# 66. But Object Mutation Is Visible

Compare:

```javascript
function change(user) {
    user.name = "Bob";
}
```

Now:

```javascript
const user = {
    name: "Alice"
};

change(user);

console.log(
    user.name
);
```

Output:

```text
Bob
```

Because both references pointed to the same object.

---

# 67. Reference Model

```text
PASS OBJECT

Caller Reference
      │
      ▼
    Object
      ▲
      │
Copied Reference
(Function Parameter)
```

Reassigning the parameter:

```text
Does not change caller reference
```

Mutating the object:

```text
Changes the shared object
```

This distinction is extremely important.

---

# 68. React State and Immutability

Suppose:

```javascript
const [user, setUser] =
    useState({
        name: "Alice",
        age: 25
    });
```

Avoid:

```javascript
user.age = 26;

setUser(user);
```

Why?

Because:

```javascript
oldUser === user;
```

still refers to the same object.

---

# 69. Correct React State Update

Prefer:

```javascript
setUser({
    ...user,
    age: 26
});
```

Now React receives a new object reference.

Conceptually:

```text
Old State Reference
       │
       ▼
   Old Object


New State Reference
       │
       ▼
   New Object
```

---

# 70. React Nested State Update

Suppose:

```javascript
const [user, setUser] =
    useState({
        name: "Alice",

        address: {
            city: "Kolkata",
            country: "India"
        }
    });
```

Avoid:

```javascript
user.address.city =
    "Delhi";

setUser(user);
```

Prefer:

```javascript
setUser({
    ...user,

    address: {
        ...user.address,
        city: "Delhi"
    }
});
```

---

# 71. Functional State Update

When next state depends on previous state:

```javascript
setUser(
    previousUser => ({
        ...previousUser,
        age:
            previousUser.age +
            1
    })
);
```

This is safer than relying on an older captured state value when updates may be queued.

---

# 72. Array State in React

Suppose:

```javascript
const [users, setUsers] =
    useState([]);
```

Add:

```javascript
setUsers(
    previousUsers => [
        ...previousUsers,
        newUser
    ]
);
```

Remove:

```javascript
setUsers(
    previousUsers =>
        previousUsers.filter(
            user =>
                user.id !== id
        )
);
```

Update:

```javascript
setUsers(
    previousUsers =>
        previousUsers.map(
            user =>
                user.id === id
                    ? {
                        ...user,
                        active: true
                    }
                    : user
        )
);
```

These patterns will appear constantly in React.

---

# 73. Why React Likes Immutability

React frequently reasons about whether values have changed through identity/reference comparisons.

Immutable updates make changes visible:

```text
Old Object
   │
   ▼

New Object
```

Instead of:

```text
Same Object
   │
   ▼
Internally Changed
```

This is useful for:

```text
Rendering

Memoization

Dependency comparisons

State updates

Reducers

Debugging
```

---

# 74. Referential Equality in React

Suppose:

```javascript
const previousUser = user;

user.name = "Bob";

const nextUser = user;
```

Then:

```javascript
previousUser === nextUser;
```

is:

```text
true
```

But with immutable update:

```javascript
const nextUser = {
    ...previousUser,
    name: "Bob"
};
```

Then:

```javascript
previousUser === nextUser;
```

is:

```text
false
```

The reference itself communicates that something changed.

---

# 75. Memoization

Suppose a system caches work based on object identity.

```text
Same Reference
→ Assume same value

Different Reference
→ Re-evaluate
```

Mutation can break this assumption.

Immutable updates work naturally with reference-based memoization.

This becomes relevant with React features such as memoized components, callbacks, and computed values.

---

# 76. Reducers and Immutability

Reducer:

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

The reducer does not mutate:

```javascript
state
```

It returns another state object.

---

# 77. Bad Reducer

```javascript
function reducer(
    state,
    action
) {
    switch (
        action.type
    ) {
        case "increment":

            state.count++;

            return state;

        default:
            return state;
    }
}
```

Problem:

```text
Old State
and
New State

are the same object.
```

---

# 78. Immutable Reducer Mental Model

```text
Current State
      │
      ▼
   Reducer
      │
      ▼
  New State


Current State
remains unchanged.
```

This makes transitions explicit.

---

# 79. State History

Immutability makes state history possible.

```text
State 1
   │
   ▼
State 2
   │
   ▼
State 3
   │
   ▼
State 4
```

Because older states were not modified, they can potentially be retained.

This enables patterns such as:

```text
Undo

Redo

Time-travel debugging

Audit history

State snapshots
```

---

# 80. Undo Example

```javascript
const history = [
    state1,
    state2,
    state3
];
```

If each state is immutable:

```text
state1 remains state1

state2 remains state2

state3 remains state3
```

Undo can simply select:

```javascript
state2
```

If states had been mutated in place, old versions could be lost.

---

# 81. Immutable Data Does Not Mean Copy Everything

Important:

```text
Immutability
≠
Deep clone everything
```

Prefer:

```text
Copy changed path

Reuse unchanged branches
```

Example:

```javascript
const updated = {
    ...state,

    settings: {
        ...state.settings,
        theme: "dark"
    }
};
```

Other state branches can retain their references.

---

# 82. Performance

Immutability creates new objects and arrays, so there is some allocation overhead.

However, structural sharing means we usually do not duplicate the entire object graph.

Example:

```text
State
│
├── Users
├── Products
├── Orders
└── Settings
```

Change settings:

```text
New State
│
├── Same Users
├── Same Products
├── Same Orders
└── New Settings
```

Only:

```text
Root
+
Settings
```

need new objects.

---

# 83. Garbage Collection

Old objects that are no longer referenced can eventually be reclaimed by JavaScript's garbage collector.

```text
Old State
   │
   X No references
   │
   ▼
Eligible for garbage collection
```

Immutability can create more short-lived objects, but modern JavaScript runtimes are designed to handle many such allocations efficiently.

Performance should be measured rather than assumed.

---

# 84. Mutation Can Be Faster

Mutation can sometimes be faster:

```javascript
array.push(item);
```

versus:

```javascript
const updated = [
    ...array,
    item
];
```

because the second operation creates another array and copies references.

For huge datasets or performance-critical algorithms, mutation may be appropriate.

Immutability is not a rule that must be followed blindly.

---

# 85. When Mutation Is Fine

Mutation is often fine when the data is:

```text
Local

Temporary

Not shared

Not externally observable

Performance-critical

Clearly owned
```

Example:

```javascript
function createNumbers(
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

The local `result` array is mutated internally.

This does not mutate caller-owned state.

---

# 86. Mutation at Controlled Boundaries

You may use mutation internally while exposing immutable behavior externally.

```javascript
function transform(
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

From the caller's perspective:

```text
Input unchanged

New output returned
```

This is perfectly reasonable.

---

# 87. Normalize Deep State

Deeply nested state:

```text
Company
└── Departments
    └── Teams
        └── Members
            └── Profile
```

can make immutable updates cumbersome.

Instead of deeply nesting everything, applications may normalize state:

```javascript
const state = {
    usersById: {
        1: {
            id: 1,
            name: "Alice"
        },

        2: {
            id: 2,
            name: "Bob"
        }
    },

    userIds: [
        1,
        2
    ]
};
```

Now updating one user is simpler.

---

# 88. Normalized State

Instead of:

```text
Post
 └── Comments
      └── Users
           └── Profile
```

store entities separately:

```text
Posts

Comments

Users
```

and connect them through IDs.

This can reduce:

```text
Deep copying

Duplicate data

Complex updates

Synchronization bugs
```

This becomes more relevant in larger state-management systems.

---

# 89. Immer Concept

Libraries such as Immer allow code that appears mutable:

```javascript
draft.user.age = 26;
```

while producing immutable state updates internally.

Conceptually:

```text
Mutable-Looking Code
        │
        ▼
      Immer
        │
        ▼
New Immutable State
```

You do not need Immer to understand immutability, but you may encounter this pattern in state-management libraries.

---

# 90. Common Mutation Bug — Sorting Props/State

Suppose:

```javascript
const sortedUsers =
    users.sort(
        (a, b) =>
            a.name.localeCompare(
                b.name
            )
    );
```

Problem:

```javascript
sort()
```

mutates `users`.

Better:

```javascript
const sortedUsers =
    users.toSorted(
        (a, b) =>
            a.name.localeCompare(
                b.name
            )
    );
```

or:

```javascript
const sortedUsers =
    [...users].sort(
        (a, b) =>
            a.name.localeCompare(
                b.name
            )
    );
```

---

# 91. Common Mutation Bug — Reverse

Wrong:

```javascript
const reversed =
    users.reverse();
```

`users` changed.

Better:

```javascript
const reversed =
    users.toReversed();
```

---

# 92. Common Mutation Bug — Nested Spread

Wrong:

```javascript
const updated = {
    ...user
};

updated.address.city =
    "Delhi";
```

The nested object remains shared.

Correct:

```javascript
const updated = {
    ...user,

    address: {
        ...user.address,
        city: "Delhi"
    }
};
```

---

# 93. Common Mutation Bug — Array of Objects

Wrong:

```javascript
const updated = [
    ...users
];

updated[0].name =
    "Bob";
```

The array is new.

But:

```javascript
updated[0] ===
users[0];
```

is still:

```text
true
```

So the original object's property was mutated.

---

# 94. Correct Array Object Update

```javascript
const updated =
    users.map(
        (user, index) =>
            index === 0
                ? {
                    ...user,
                    name: "Bob"
                }
                : user
    );
```

Now:

```text
Array
→ New

Changed Object
→ New

Unchanged Objects
→ Reused
```

Structural sharing.

---

# 95. Common Mutation Bug — Function Input

Wrong:

```javascript
function applyDiscount(
    product
) {
    product.price *= 0.9;

    return product;
}
```

Calling the function changes the caller's object.

Better:

```javascript
function applyDiscount(
    product
) {
    return {
        ...product,

        price:
            product.price *
            0.9
    };
}
```

---

# 96. Common Mutation Bug — Default Objects

Be careful with shared objects.

```javascript
const defaults = {
    retries: 3
};

function configure(
    config
) {
    const result =
        defaults;

    result.retries =
        config.retries;

    return result;
}
```

This modifies:

```javascript
defaults
```

Better:

```javascript
function configure(
    config
) {
    return {
        ...defaults,
        ...config
    };
}
```

---

# 97. Common Mutation Bug — Shared Configuration

Suppose:

```javascript
const config = {
    api: {
        timeout: 5000
    }
};

const developmentConfig = {
    ...config
};

developmentConfig
    .api
    .timeout = 10000;
```

Now:

```javascript
config.api.timeout;
```

is also:

```text
10000
```

because:

```javascript
config.api ===
developmentConfig.api;
```

is:

```text
true
```

---

# 98. Correct Nested Configuration

```javascript
const developmentConfig = {
    ...config,

    api: {
        ...config.api,
        timeout: 10000
    }
};
```

Now the nested configuration is independent.

---

# 99. Equality and Change Detection

Immutable updates make shallow comparisons useful.

Suppose:

```javascript
previousState ===
nextState;
```

If:

```text
true
```

the root reference did not change.

If:

```text
false
```

a new root state exists.

Then individual branches can also be compared:

```javascript
previousState.users ===
nextState.users;
```

This allows systems to efficiently determine which branches changed.

---

# 100. Shallow Comparison

Suppose:

```javascript
const oldState = {
    users,
    settings
};

const newState = {
    ...oldState,

    settings: {
        ...settings,
        theme: "dark"
    }
};
```

Then:

```javascript
oldState ===
newState;
// false
```

```javascript
oldState.users ===
newState.users;
// true
```

```javascript
oldState.settings ===
newState.settings;
// false
```

From reference comparisons alone:

```text
Root changed
Users unchanged
Settings changed
```

This is one of the major benefits of structural sharing.

---

# 101. Interview Question — What Is Immutability?

Immutability means avoiding modification of existing data and representing changes by creating new values or data structures.

---

# 102. Interview Question — Are JavaScript Objects Immutable?

No.

Objects and arrays are mutable by default.

```javascript
const user = {
    name: "Alice"
};

user.name = "Bob";
```

is valid.

---

# 103. Interview Question — Are Primitives Immutable?

Yes.

Primitive values themselves cannot be modified.

Operations create new primitive values instead.

---

# 104. Interview Question — Does `const` Make an Object Immutable?

No.

`const` prevents variable reassignment.

It does not prevent object mutation.

```javascript
const user = {
    name: "Alice"
};

user.name = "Bob";
```

is valid.

---

# 105. Interview Question — What Is a Shallow Copy?

A shallow copy creates a new top-level object or array while nested reference values remain shared.

Example:

```javascript
const copy = {
    ...original
};
```

Top level:

```text
New
```

Nested objects:

```text
Potentially Shared
```

---

# 106. Interview Question — What Is a Deep Copy?

A deep copy recursively duplicates nested data so the copied structure does not share the nested references being cloned with the original.

For supported structured data:

```javascript
structuredClone(
    object
);
```

can perform deep cloning.

---

# 107. Interview Question — Spread vs `structuredClone()`

Spread:

```javascript
{
    ...object
}
```

creates a shallow copy.

`structuredClone()`:

```javascript
structuredClone(
    object
);
```

deeply clones supported structured data.

They solve different problems.

---

# 108. Interview Question — What Is Structural Sharing?

Structural sharing means creating new references only for changed parts of an immutable structure while reusing unchanged parts.

Example:

```text
Old State
│
├── Users
├── Orders
└── Settings


New State
│
├── Same Users
├── Same Orders
└── New Settings
```

---

# 109. Interview Question — Why Is Immutability Important in React?

Because immutable state updates create new references, making changes predictable and compatible with React's reference-based comparisons, memoization, and state-management patterns.

It also prevents accidental modification of previous state.

---

# 110. Interview Question — Does `Object.freeze()` Deep Freeze?

No.

`Object.freeze()` is shallow.

Nested objects must be frozen separately if deep freezing is required.

---

# 111. Interview Question — Is Mutation Always Bad?

No.

Mutation can be appropriate when data is:

```text
Locally owned

Temporary

Not shared

Performance-sensitive

Not externally observable
```

The bigger concern is uncontrolled mutation of shared state.

---

# 112. Interview Question — Are Objects Passed by Reference?

More precisely:

> JavaScript is pass-by-value, and for objects the value being copied is a reference to the object.

This explains why object mutations are visible through other references while parameter reassignment does not change the caller's variable.

---

# 113. Interview Question — Why Not Deep Clone Every Update?

Because deep cloning:

```text
Copies unchanged data

Allocates unnecessary objects

Uses more memory

Requires more processing

Destroys useful reference identity
```

Structural sharing is generally more efficient.

---

# 114. Output Question

```javascript
const a = {
    count: 10
};

const b = a;

b.count = 20;

console.log(
    a.count
);
```

Output:

```text
20
```

Because:

```text
a
and
b
```

reference the same object.

---

# 115. Output Question

```javascript
const a = {
    count: 10
};

const b = {
    ...a
};

b.count = 20;

console.log(
    a.count
);

console.log(
    b.count
);
```

Output:

```text
10
20
```

The objects are separate.

---

# 116. Output Question

```javascript
const a = {
    nested: {
        count: 10
    }
};

const b = {
    ...a
};

b.nested.count = 20;

console.log(
    a.nested.count
);
```

Output:

```text
20
```

Because:

```javascript
a.nested ===
b.nested;
```

is:

```text
true
```

---

# 117. Output Question

```javascript
const a = {
    nested: {
        count: 10
    }
};

const b = {
    ...a,

    nested: {
        ...a.nested,
        count: 20
    }
};

console.log(
    a.nested.count
);

console.log(
    b.nested.count
);
```

Output:

```text
10
20
```

The nested object was also copied.

---

# 118. Output Question

```javascript
const users = [
    {
        name: "Alice"
    }
];

const copy = [
    ...users
];

copy[0].name =
    "Bob";

console.log(
    users[0].name
);
```

Output:

```text
Bob
```

The array is new, but the object inside it is shared.

---

# 119. Output Question

```javascript
const users = [
    {
        name: "Alice"
    }
];

const copy =
    users.map(
        user => ({
            ...user
        })
    );

copy[0].name =
    "Bob";

console.log(
    users[0].name
);
```

Output:

```text
Alice
```

Each user object was copied.

Note that this is still only one level of copying for each user object.

---

# 120. Output Question

```javascript
const a = {
    name: "Alice"
};

const b = {
    name: "Alice"
};

console.log(
    a === b
);
```

Output:

```text
false
```

Different object references.

---

# 121. Output Question

```javascript
const a = {
    name: "Alice"
};

const b = a;

console.log(
    a === b
);
```

Output:

```text
true
```

Same object reference.

---

# 122. Immutability Checklist

Before updating data, ask:

```text
Am I changing the original?

Is this data shared?

Do multiple references point
to this object?

Is the copy shallow?

Are nested objects shared?

Which path actually changed?

Can unchanged branches be reused?

Do I need a deep clone?

Would structural sharing be better?

Am I mutating React state?

Am I mutating function inputs?

Does this method mutate the array?
```

---

# 123. Immutability Cheat Sheet

```text
IMMUTABILITY
================================

Do not modify existing
shared state.

Create new values.


MUTATION
================================

user.age = 30;

array.push(item);

array.sort();


IMMUTABLE OBJECT UPDATE
================================

{
    ...user,
    age: 30
}


IMMUTABLE ARRAY ADD
================================

[
    ...items,
    item
]


REMOVE
================================

items.filter(...)


UPDATE
================================

items.map(...)


SORT
================================

items.toSorted(...)


REVERSE
================================

items.toReversed()


SPLICE
================================

items.toSpliced(...)


REPLACE INDEX
================================

items.with(
    index,
    value
)


REFERENCE
================================

const b = a;

a and b point to
the same object.


OBJECT EQUALITY
================================

{} === {}

false


SAME REFERENCE
================================

const b = a;

a === b

true


SHALLOW COPY
================================

{
    ...object
}

[
    ...array
]


SHALLOW MEANS
================================

Top level
→ New

Nested references
→ Shared


NESTED UPDATE
================================

{
    ...user,

    address: {
        ...user.address,
        city: "Delhi"
    }
}


DEEP COPY
================================

structuredClone(
    object
)


STRUCTURAL SHARING
================================

Changed branches
→ New references

Unchanged branches
→ Reused references


CONST
================================

Prevents reassignment.

Does NOT make objects
immutable.


OBJECT.FREEZE
================================

Prevents certain mutation
of the object's own
top-level properties.

Shallow.


MAP
================================

Mutable collection.

new Map(oldMap)
can create another Map.


SET
================================

Mutable collection.

new Set(oldSet)
can create another Set.


REACT
================================

Do not mutate state.

Create new state references.


REDUCER
================================

State + Action
      │
      ▼
   Reducer
      │
      ▼
New State


IMPORTANT
================================

Immutability
does NOT mean

"deep clone everything"

Prefer structural sharing.
```

---

# 124. Important Rules to Remember

```text
1. JavaScript objects are mutable.

2. JavaScript arrays are mutable.

3. Primitive values are immutable.

4. const does not make objects immutable.

5. const prevents reassignment of the binding.

6. Objects are reference values.

7. Arrays are objects and use references.

8. Assigning an object to another variable copies the reference value.

9. Two references can point to the same object.

10. Mutating through one reference affects all references to that object.

11. Two identical-looking object literals are not ===.

12. Reference equality compares object identity.

13. Immutable updates create new references.

14. Spread creates shallow copies.

15. Object.assign() can create shallow copies.

16. Array spread creates a new outer array.

17. Nested objects remain shared after shallow copying.

18. Nested arrays remain shared after shallow copying.

19. Arrays of objects require copying changed objects too.

20. Copy every changed level in a nested update.

21. Do not copy unchanged branches unnecessarily.

22. Structural sharing reuses unchanged data.

23. Structural sharing reduces copying.

24. Structural sharing makes reference comparisons useful.

25. Deep cloning and immutability are not the same thing.

26. Immutability does not require deep cloning everything.

27. structuredClone() deeply clones supported structured data.

28. structuredClone() cannot clone every JavaScript value.

29. Functions cannot be structured-cloned.

30. JSON stringify/parse is not a general cloning solution.

31. push() mutates.

32. pop() mutates.

33. shift() mutates.

34. unshift() mutates.

35. splice() mutates.

36. sort() mutates.

37. reverse() mutates.

38. fill() mutates.

39. copyWithin() mutates.

40. map() creates a new array.

41. filter() creates a new array.

42. slice() creates a new array.

43. concat() creates a new array.

44. toSorted() creates a sorted copy.

45. toReversed() creates a reversed copy.

46. toSpliced() creates a modified copy.

47. with() creates a copy with an index replaced.

48. Non-mutating array methods do not guarantee nested values are immutable.

49. Callbacks can still mutate objects.

50. Object.freeze() is shallow.

51. Object.freeze() is not the same as immutable programming.

52. Nested values can remain mutable after freeze().

53. Deep freezing requires recursively freezing nested objects.

54. Map is mutable.

55. Set is mutable.

56. Map copies are shallow.

57. Set copies can still contain shared object references.

58. Function parameters receive values.

59. JavaScript is pass-by-value.

60. Object reference values are copied into parameters.

61. Reassigning an object parameter does not reassign the caller's variable.

62. Mutating the referenced object is visible to the caller.

63. Shared mutable state can create hidden dependencies.

64. Shared mutable state can create unexpected behavior.

65. Aliasing means multiple references can point to the same mutable object.

66. Immutable functions should avoid mutating caller-owned inputs.

67. Returning new objects makes data flow clearer.

68. React state should be treated as immutable.

69. Do not mutate React state directly.

70. Use new object references for object state updates.

71. Use new array references for array state updates.

72. Copy nested state when nested values change.

73. Functional state updates are useful when next state depends on previous state.

74. Reducers should not mutate existing state.

75. Reducers generally return new state.

76. Immutable updates enable state history.

77. State history enables undo/redo patterns.

78. Immutability helps time-travel debugging.

79. Immutability helps memoization.

80. Immutability makes shallow comparisons useful.

81. New root reference can indicate a state change.

82. Shared unchanged references indicate unchanged branches.

83. Reference identity is useful for efficient change detection.

84. Deep cloning destroys reference sharing.

85. Deep cloning can be expensive.

86. Structural sharing is usually preferable for state updates.

87. Mutation may be faster in some algorithms.

88. Mutation is not universally bad.

89. Local temporary mutation is often fine.

90. Controlled mutation can be an implementation detail.

91. Shared mutation is the bigger concern.

92. Performance decisions should be measured.

93. Immutability can increase allocations.

94. Garbage collection handles unreachable objects.

95. Do not optimize away immutability without evidence.

96. Deeply nested state can make updates cumbersome.

97. Normalized state can simplify updates.

98. Entity IDs can reduce duplicated nested data.

99. Immer can provide mutable-looking immutable updates.

100. The key mental model is: create new references for changed paths and reuse unchanged data.
```

---

# Immutability in One Sentence

> Immutability means representing a change by creating new data instead of modifying existing shared data, while reusing unchanged parts whenever possible.

---

# Final Mental Model

## Mutation

```text
Variable
   │
   ▼
┌───────────────┐
│ Existing Data │
└───────┬───────┘
        │
        ▼
     MODIFY
        │
        ▼
┌───────────────┐
│ Same Object   │
│ New Contents  │
└───────────────┘
```

---

## Immutable Update

```text
Old Reference
      │
      ▼
┌───────────────┐
│   Old Data    │
└───────────────┘
      │
      │ remains unchanged
      │
      ▼

New Reference
      │
      ▼
┌───────────────┐
│   New Data    │
└───────────────┘
```

---

## Structural Sharing

```text
OLD STATE

Root
├── Users ───────────────┐
├── Orders ────────────┐ │
└── Settings           │ │
                       │ │
                       │ │
NEW STATE              │ │
                       │ │
New Root               │ │
├── Users ─────────────┘ │
├── Orders ──────────────┘
└── New Settings
```

Only changed paths receive new references.

---

## The Most Important Rule

```text
When something changes:

Do NOT automatically
copy everything.

Do NOT mutate the
existing shared data.

Instead:

Copy the changed path
        +
Reuse unchanged branches.
```

That is the mental model you want before React.
