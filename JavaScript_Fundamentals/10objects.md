#  Objects

A **JavaScript object** is a collection of **key-value pairs** used to represent related data and behavior as a single value.

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    isAdmin: false
};
```

Here:

```text
user
│
├── name    → "John"
├── age     → 25
└── isAdmin → false
```

Each key-value pair is called a **property**.

Objects are one of the most important concepts in JavaScript because they are used everywhere:

```text
Application data
API responses
Configuration
DOM objects
Classes
Modules
React props
React state
Events
Libraries
Frameworks
```

---

# 1. What Is an Object?

An object groups related information together.

Instead of:

```javascript
const name = "John";
const age = 25;
const email = "john@example.com";
```

we can write:

```javascript
const user = {
    name: "John",
    age: 25,
    email: "john@example.com"
};
```

Now all information belongs to one logical entity:

```text
user
│
├── name
├── age
└── email
```

---

# 2. Object Literal

The most common way to create an object is using **object literal syntax**.

```javascript
const user = {
    name: "John",
    age: 25
};
```

Syntax:

```javascript
const objectName = {
    key1: value1,
    key2: value2
};
```

Example:

```javascript
const product = {
    name: "Laptop",
    price: 1000,
    available: true
};
```

---

# 3. Object Properties

An object contains **properties**.

```javascript
const user = {
    name: "John",
    age: 25
};
```

Properties:

```text
Property
─────────────
name: "John"

Key   → name
Value → "John"


Property
─────────────
age: 25

Key   → age
Value → 25
```

---

# 4. Property Values Can Be Any Type

Object properties can contain almost any JavaScript value.

```javascript
const user = {
    name: "John",
    age: 25,
    active: true,
    address: null,
    skills: ["JavaScript", "React"],
    profile: {
        city: "Kolkata"
    },
    greet: function () {
        console.log("Hello");
    }
};
```

An object can therefore contain:

```text
Strings
Numbers
Booleans
null
undefined
Arrays
Objects
Functions
and other JavaScript values
```

---

# 5. Accessing Properties with Dot Notation

The most common way to access a property is **dot notation**.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(user.name);
console.log(user.age);
```

Output:

```text
John
25
```

Syntax:

```javascript
object.property
```

Use dot notation when the property name is known and is valid for dot-property syntax.

---

# 6. Accessing Properties with Bracket Notation

Properties can also be accessed using **bracket notation**.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(user["name"]);
console.log(user["age"]);
```

Output:

```text
John
25
```

Syntax:

```javascript
object["property"]
```

---

# 7. Dot Notation vs Bracket Notation

Both can access properties:

```javascript
user.name;
user["name"];
```

But bracket notation is required or especially useful when the key is dynamic or cannot be written directly after `.`.

Example:

```javascript
const user = {
    "first name": "John"
};

console.log(user["first name"]);
```

This does not work:

```javascript
user.first name;
```

---

# 8. Dynamic Property Access

Bracket notation allows a variable to determine the property name.

```javascript
const user = {
    name: "John",
    age: 25
};

const property = "name";

console.log(user[property]);
```

Output:

```text
John
```

Compare:

```javascript
user.property
```

This searches for a property literally named:

```text
property
```

while:

```javascript
user[property]
```

uses the value stored in the variable.

If:

```javascript
property = "name"
```

then:

```javascript
user[property]
```

means:

```javascript
user["name"]
```

---

# 9. Adding Properties

Properties can be added after an object is created.

```javascript
const user = {
    name: "John"
};

user.age = 25;

console.log(user);
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

Bracket notation also works:

```javascript
user["email"] = "john@example.com";
```

---

# 10. Updating Properties

Existing properties can be changed.

```javascript
const user = {
    name: "John",
    age: 25
};

user.age = 26;

console.log(user.age);
```

Output:

```text
26
```

Or:

```javascript
user["age"] = 26;
```

---

# 11. Deleting Properties

The `delete` operator removes a property.

```javascript
const user = {
    name: "John",
    age: 25
};

delete user.age;

console.log(user);
```

Result:

```javascript
{
    name: "John"
}
```

---

# 12. `const` Objects Can Be Modified

This is extremely important.

```javascript
const user = {
    name: "John"
};

user.name = "Alice";
user.age = 25;
```

This is valid.

Why?

`const` prevents reassignment of the variable binding.

It does **not** automatically make the object immutable.

This is not allowed:

```javascript
user = {
    name: "Bob"
};
```

because that attempts to assign a different object to `user`.

Remember:

```text
const object
→ object contents may still change

const binding
→ cannot be reassigned
```

---

# 13. Computed Property Names

A variable or expression can be used as a key while creating an object.

```javascript
const property = "name";

const user = {
    [property]: "John"
};

console.log(user);
```

Result:

```javascript
{
    name: "John"
}
```

Example:

```javascript
const field = "email";

const user = {
    name: "John",
    [field]: "john@example.com"
};
```

---

# 14. Property Shorthand

Suppose:

```javascript
const name = "John";
const age = 25;
```

Instead of:

```javascript
const user = {
    name: name,
    age: age
};
```

JavaScript allows:

```javascript
const user = {
    name,
    age
};
```

Equivalent to:

```javascript
{
    name: name,
    age: age
}
```

This is called **property shorthand**.

It is extremely common in React and modern JavaScript.

---

# 15. Object Methods

When a function is stored as an object property, it is commonly called a **method**.

```javascript
const user = {
    name: "John",

    greet: function () {
        console.log("Hello");
    }
};

user.greet();
```

Output:

```text
Hello
```

Here:

```text
greet
→ property

function
→ property value

greet()
→ method call
```

---

# 16. Method Shorthand

Instead of:

```javascript
const user = {
    greet: function () {
        console.log("Hello");
    }
};
```

modern JavaScript commonly uses:

```javascript
const user = {
    greet() {
        console.log("Hello");
    }
};
```

Usage:

```javascript
user.greet();
```

Output:

```text
Hello
```

---

# 17. `this` Inside Object Methods

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Output:

```text
John
```

For a regular method call:

```javascript
user.greet();
```

`this` is determined by how the function is called.

Here:

```javascript
this
```

refers to:

```javascript
user
```

so:

```javascript
this.name
```

means:

```javascript
user.name
```

We will study `this` separately because its rules go far beyond objects.

---

# 18. Arrow Functions and `this`

Be careful using arrow functions as object methods when you expect `this` to refer to the object.

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

The arrow function does **not** create its own `this`.

It captures `this` lexically from the surrounding scope.

Therefore, do not assume:

```javascript
this === user
```

inside that arrow function.

For methods that need receiver-based `this`, regular method syntax is usually appropriate:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

---

# 19. Nested Objects

Objects can contain other objects.

```javascript
const user = {
    name: "John",

    address: {
        city: "Kolkata",
        country: "India"
    }
};
```

Access nested properties:

```javascript
console.log(user.address.city);
```

Output:

```text
Kolkata
```

Structure:

```text
user
│
├── name → "John"
│
└── address
      │
      ├── city    → "Kolkata"
      └── country → "India"
```

---

# 20. Deeply Nested Objects

Objects can be nested multiple levels.

```javascript
const user = {
    profile: {
        address: {
            city: "Kolkata",
            country: "India"
        }
    }
};
```

Access:

```javascript
console.log(user.profile.address.city);
```

Output:

```text
Kolkata
```

---

# 21. Objects Can Contain Arrays

```javascript
const user = {
    name: "John",
    skills: ["JavaScript", "React", "Node.js"]
};
```

Access:

```javascript
console.log(user.skills);
console.log(user.skills[0]);
```

Output:

```text
["JavaScript", "React", "Node.js"]

JavaScript
```

---

# 22. Arrays Can Contain Objects

This is extremely common.

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

Access:

```javascript
console.log(users[0].name);
```

Output:

```text
John
```

This structure is commonly returned by APIs.

---

# 23. Objects Are Reference Types

Objects behave differently from primitive values when assigned to variables.

Primitive example:

```javascript
let a = 10;
let b = a;

b = 20;

console.log(a);
console.log(b);
```

Output:

```text
10
20
```

But with objects:

```javascript
const user1 = {
    name: "John"
};

const user2 = user1;

user2.name = "Alice";

console.log(user1.name);
```

Output:

```text
Alice
```

Why?

Both variables refer to the same object.

Conceptually:

```text
user1 ─────┐
           │
           ▼
       ┌──────────┐
       │ Object   │
       │          │
       │ name:    │
       │ "Alice"  │
       └──────────┘
           ▲
           │
user2 ─────┘
```

---

# 24. Object Assignment Does Not Copy the Object

Consider:

```javascript
const original = {
    value: 10
};

const copy = original;
```

This does **not** create two independent objects.

Instead:

```text
original ───┐
            ▼
          Object
            ▲
copy ───────┘
```

Therefore:

```javascript
copy.value = 20;

console.log(original.value);
```

Output:

```text
20
```

---

# 25. Object Equality

Objects are compared by identity/reference, not by comparing their contents.

```javascript
const a = {
    value: 10
};

const b = {
    value: 10
};

console.log(a === b);
```

Output:

```text
false
```

Even though their contents look identical:

```text
a → Object A

b → Object B
```

They are different objects.

But:

```javascript
const a = {
    value: 10
};

const b = a;

console.log(a === b);
```

Output:

```text
true
```

because both variables refer to the same object.

---

# 26. Shallow Copy with Spread Operator

A new object can be created using object spread syntax.

```javascript
const user = {
    name: "John",
    age: 25
};

const copy = {
    ...user
};

console.log(copy);
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

Now:

```javascript
console.log(user === copy);
```

Output:

```text
false
```

because they are separate outer objects.

---

# 27. Updating While Copying

Object spread is commonly used to create an updated object.

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

The original remains:

```javascript
{
    name: "John",
    age: 25
}
```

This pattern is extremely important in React.

---

# 28. Spread Order Matters

Consider:

```javascript
const user = {
    name: "John",
    age: 25
};
```

This:

```javascript
const updated = {
    ...user,
    age: 30
};
```

produces:

```javascript
{
    name: "John",
    age: 30
}
```

But:

```javascript
const updated = {
    age: 30,
    ...user
};
```

produces:

```javascript
{
    name: "John",
    age: 25
}
```

Why?

Later properties overwrite earlier properties with the same key.

Remember:

```text
Last value wins
```

---

# 29. Spread Creates a Shallow Copy

This is extremely important.

Consider:

```javascript
const user = {
    name: "John",

    address: {
        city: "Kolkata"
    }
};

const copy = {
    ...user
};
```

Now:

```text
user !== copy
```

But the nested object is still shared:

```text
user.address === copy.address
```

Example:

```javascript
copy.address.city = "Delhi";

console.log(user.address.city);
```

Output:

```text
Delhi
```

Why?

Conceptually:

```text
user ──────► Object A
                │
                └────► Address Object
                         ▲
                         │
copy ──────► Object B ───┘
```

Only the outer object was copied.

---

# 30. Copying Nested Objects

If you need to update a nested object without mutating the original nested object:

```javascript
const user = {
    name: "John",

    address: {
        city: "Kolkata",
        country: "India"
    }
};
```

Create:

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
console.log(user.address.city);
```

Output:

```text
Kolkata
```

And:

```javascript
console.log(updatedUser.address.city);
```

Output:

```text
Delhi
```

This pattern becomes very important when working with React state.

---

# 31. `Object.assign()`

Another way to copy or combine object properties is:

```javascript
Object.assign()
```

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const copy = Object.assign({}, user);
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

Modern JavaScript often uses:

```javascript
const copy = {
    ...user
};
```

because it is concise.

Both approaches perform shallow copying in these examples.

---

# 32. Merging Objects

Object spread can merge objects.

```javascript
const personal = {
    name: "John",
    age: 25
};

const contact = {
    email: "john@example.com",
    phone: "1234567890"
};

const user = {
    ...personal,
    ...contact
};
```

Result:

```javascript
{
    name: "John",
    age: 25,
    email: "john@example.com",
    phone: "1234567890"
}
```

If duplicate keys exist, later values overwrite earlier ones.

---

# 33. Object Destructuring

Destructuring extracts properties into variables.

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

Equivalent conceptually to:

```javascript
const name = user.name;
const age = user.age;
```

Now:

```javascript
console.log(name);
console.log(age);
```

Output:

```text
John
25
```

This is extremely common in React.

---

# 34. Destructuring with Different Variable Names

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    name: userName,
    age: userAge
} = user;
```

Now:

```javascript
console.log(userName);
console.log(userAge);
```

Output:

```text
John
25
```

Syntax:

```text
propertyName: variableName
```

---

# 35. Destructuring Default Values

```javascript
const user = {
    name: "John"
};

const {
    name,
    age = 18
} = user;

console.log(age);
```

Output:

```text
18
```

The default applies when the property value is `undefined`.

For example:

```javascript
const user = {
    age: null
};

const {
    age = 18
} = user;

console.log(age);
```

Output:

```text
null
```

because `null` is an actual value, not `undefined`.

---

# 36. Rest Property in Object Destructuring

```javascript
const user = {
    id: 1,
    name: "John",
    age: 25,
    email: "john@example.com"
};

const {
    id,
    ...details
} = user;
```

Now:

```javascript
console.log(id);
```

Output:

```text
1
```

And:

```javascript
console.log(details);
```

Result:

```javascript
{
    name: "John",
    age: 25,
    email: "john@example.com"
}
```

---

# 37. Destructuring Function Parameters

Instead of:

```javascript
function displayUser(user) {
    console.log(user.name);
    console.log(user.age);
}
```

we can write:

```javascript
function displayUser({
    name,
    age
}) {
    console.log(name);
    console.log(age);
}
```

Usage:

```javascript
displayUser({
    name: "John",
    age: 25
});
```

This pattern is heavily used in React components.

---

# 38. Checking Whether a Property Exists

One option is the `in` operator.

```javascript
const user = {
    name: "John"
};

console.log("name" in user);
console.log("age" in user);
```

Output:

```text
true
false
```

`in` also checks the object's prototype chain.

---

# 39. `Object.hasOwn()`

To check whether a property belongs directly to an object:

```javascript
const user = {
    name: "John"
};

console.log(
    Object.hasOwn(user, "name")
);
```

Output:

```text
true
```

Example:

```javascript
console.log(
    Object.hasOwn(user, "toString")
);
```

Output:

```text
false
```

`Object.hasOwn()` checks own properties rather than inherited ones.

---

# 40. `Object.keys()`

`Object.keys()` returns an array containing an object's own enumerable string-keyed property names.

```javascript
const user = {
    name: "John",
    age: 25,
    city: "Kolkata"
};

const keys = Object.keys(user);

console.log(keys);
```

Output:

```javascript
["name", "age", "city"]
```

---

# 41. `Object.values()`

`Object.values()` returns the corresponding own enumerable string-keyed property values.

```javascript
const user = {
    name: "John",
    age: 25,
    city: "Kolkata"
};

console.log(
    Object.values(user)
);
```

Output:

```javascript
["John", 25, "Kolkata"]
```

---

# 42. `Object.entries()`

`Object.entries()` returns key-value pairs.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(
    Object.entries(user)
);
```

Output:

```javascript
[
    ["name", "John"],
    ["age", 25]
]
```

Each property becomes:

```text
[key, value]
```

---

# 43. Iterating with `Object.entries()`

```javascript
const user = {
    name: "John",
    age: 25
};

for (const [key, value] of Object.entries(user)) {
    console.log(key, value);
}
```

Output:

```text
name John
age 25
```

This combines:

```text
Object.entries()
+
for...of
+
Array destructuring
```

---

# 44. `Object.fromEntries()`

`Object.fromEntries()` does the reverse of `Object.entries()`.

```javascript
const entries = [
    ["name", "John"],
    ["age", 25]
];

const user = Object.fromEntries(entries);

console.log(user);
```

Result:

```javascript
{
    name: "John",
    age: 25
}
```

So:

```text
Object
↓
Object.entries()
↓
Array of [key, value]
↓
Object.fromEntries()
↓
Object
```

---

# 45. `for...in`

`for...in` iterates over enumerable string property names, including inherited enumerable properties.

```javascript
const user = {
    name: "John",
    age: 25
};

for (const key in user) {
    console.log(key);
}
```

Output:

```text
name
age
```

Access values:

```javascript
for (const key in user) {
    console.log(user[key]);
}
```

Output:

```text
John
25
```

Because `for...in` can include inherited enumerable properties, own-property checks may be needed depending on the object and use case.

---

# 46. `for...in` vs `for...of`

For an object:

```javascript
const user = {
    name: "John",
    age: 25
};
```

You can use:

```javascript
for (const key in user) {
    console.log(key);
}
```

But plain objects are not normally directly iterable with:

```javascript
for (const value of user) {
}
```

That throws a `TypeError`.

Instead:

```javascript
for (const value of Object.values(user)) {
    console.log(value);
}
```

Or:

```javascript
for (const [key, value] of Object.entries(user)) {
    console.log(key, value);
}
```

---

# 47. Optional Chaining

Suppose:

```javascript
const user = {
    name: "John"
};
```

This can cause an error:

```javascript
console.log(user.address.city);
```

because:

```javascript
user.address
```

is:

```text
undefined
```

Optional chaining allows:

```javascript
console.log(user.address?.city);
```

Output:

```text
undefined
```

instead of throwing because `address` is nullish.

---

# 48. Optional Chaining `?.`

Example:

```javascript
const user = {
    profile: {
        name: "John"
    }
};

console.log(
    user.profile?.name
);
```

Output:

```text
John
```

If:

```javascript
const user = {};
```

then:

```javascript
console.log(
    user.profile?.name
);
```

Output:

```text
undefined
```

Optional chaining stops when the value immediately before `?.` is:

```text
null
or
undefined
```

---

# 49. Optional Chaining with Methods

Optional chaining can also be used for method calls.

```javascript
const user = {
    greet() {
        console.log("Hello");
    }
};

user.greet?.();
```

Output:

```text
Hello
```

If `greet` does not exist:

```javascript
const user = {};

user.greet?.();
```

nothing is called and the expression evaluates to:

```text
undefined
```

---

# 50. Nullish Coalescing with Objects

Optional chaining is often combined with:

```javascript
??
```

Example:

```javascript
const user = {};

const city =
    user.address?.city ?? "Unknown";

console.log(city);
```

Output:

```text
Unknown
```

Meaning:

```text
If city is null or undefined
→ use "Unknown"
```

---

# 51. `??` vs `||`

Consider:

```javascript
const user = {
    score: 0
};
```

Using:

```javascript
const score = user.score || 100;
```

Result:

```text
100
```

because `0` is falsy.

Using:

```javascript
const score = user.score ?? 100;
```

Result:

```text
0
```

because `??` only falls back for:

```text
null
undefined
```

This distinction is important when `0`, `false`, or `""` are valid values.

---

# 52. Getters

A getter lets a property execute logic when read.

```javascript
const user = {
    firstName: "John",
    lastName: "Doe",

    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
};

console.log(user.fullName);
```

Output:

```text
John Doe
```

Notice:

```javascript
user.fullName
```

not:

```javascript
user.fullName()
```

It behaves like property access.

---

# 53. Setters

A setter runs logic when assigning to a property.

```javascript
const user = {
    firstName: "John",
    lastName: "Doe",

    set fullName(value) {
        const [firstName, lastName] = value.split(" ");

        this.firstName = firstName;
        this.lastName = lastName;
    }
};
```

Usage:

```javascript
user.fullName = "Alice Smith";

console.log(user.firstName);
console.log(user.lastName);
```

Output:

```text
Alice
Smith
```

---

# 54. `Object.freeze()`

`Object.freeze()` prevents ordinary additions, removals, and writes to an object's own data properties.

```javascript
const user = {
    name: "John",
    age: 25
};

Object.freeze(user);

user.name = "Alice";
user.email = "test@example.com";
delete user.age;
```

These mutations do not succeed.

In strict mode, attempts to perform forbidden writes can throw `TypeError`.

Important:

```text
Object.freeze()
→ shallow
```

---

# 55. `Object.freeze()` Is Shallow

Consider:

```javascript
const user = {
    name: "John",

    address: {
        city: "Kolkata"
    }
};

Object.freeze(user);
```

The outer object is frozen.

But the nested object is not automatically frozen.

```javascript
user.address.city = "Delhi";

console.log(user.address.city);
```

Output:

```text
Delhi
```

If deep immutability is required, nested objects must also be handled appropriately.

---

# 56. `Object.seal()`

`Object.seal()` prevents properties from being added or removed, but existing writable properties can still be changed.

```javascript
const user = {
    name: "John",
    age: 25
};

Object.seal(user);

user.name = "Alice";
```

Allowed:

```text
name
→ Alice
```

But adding:

```javascript
user.email = "john@example.com";
```

does not succeed.

And:

```javascript
delete user.age;
```

does not succeed.

Strict mode may throw for invalid mutations.

---

# 57. Freeze vs Seal

```text
Object.freeze()
│
├── Cannot add properties
├── Cannot delete properties
└── Existing data properties cannot normally be changed


Object.seal()
│
├── Cannot add properties
├── Cannot delete properties
└── Existing writable properties can be changed
```

Both are shallow operations.

---

# 58. Objects as Function Arguments

Objects can be passed to functions.

```javascript
function displayUser(user) {
    console.log(user.name);
}

const user = {
    name: "John"
};

displayUser(user);
```

Output:

```text
John
```

The parameter receives the object's reference value.

This means the function can mutate the same object if it modifies its properties.

---

# 59. Mutating an Object Inside a Function

```javascript
function changeName(user) {
    user.name = "Alice";
}

const user = {
    name: "John"
};

changeName(user);

console.log(user.name);
```

Output:

```text
Alice
```

The function modified the same object.

Conceptually:

```text
user variable ─────┐
                   ▼
                 Object
                   ▲
                   │
function parameter ┘
```

---

# 60. Reassigning the Parameter Is Different

Consider:

```javascript
function replaceUser(user) {
    user = {
        name: "Alice"
    };
}

const user = {
    name: "John"
};

replaceUser(user);

console.log(user.name);
```

Output:

```text
John
```

Why?

The parameter initially receives the same reference value.

But:

```javascript
user = {
    name: "Alice"
};
```

changes what the **local parameter variable** refers to.

It does not reassign the caller's variable.

---

# 61. JavaScript Is Pass-by-Value

A useful precise rule is:

> JavaScript passes values to functions.

For objects, the value being passed is a reference to the object.

So:

```javascript
function update(obj) {
    obj.name = "Alice";
}
```

can mutate the object through the copied reference.

But:

```javascript
function replace(obj) {
    obj = {};
}
```

only changes the local parameter binding.

Avoid saying:

```text
JavaScript passes objects by reference
```

as a strict language rule.

A more accurate mental model is:

```text
JavaScript
→ pass-by-value

Object value
→ reference to an object
```

---

# 62. Object Mutation

Mutation means changing an existing object.

```javascript
const user = {
    name: "John"
};

user.name = "Alice";
```

The original object was modified.

This is mutation.

Another example:

```javascript
user.age = 25;
```

also mutates the existing object.

---

# 63. Immutable-Style Update

Instead of modifying:

```javascript
user.name = "Alice";
```

create a new object:

```javascript
const updatedUser = {
    ...user,
    name: "Alice"
};
```

Now:

```text
user
→ original object

updatedUser
→ new object
```

This pattern is fundamental in React state management.

---

# 64. Objects and `typeof`

```javascript
const user = {
    name: "John"
};

console.log(typeof user);
```

Output:

```text
object
```

Arrays also return:

```javascript
typeof [];
```

Output:

```text
object
```

And historically:

```javascript
typeof null;
```

also returns:

```text
object
```

which is a long-standing JavaScript quirk.

---

# 65. Detecting Arrays

Do not rely on:

```javascript
typeof value === "object"
```

to distinguish arrays from other objects.

Use:

```javascript
Array.isArray(value);
```

Example:

```javascript
console.log(
    Array.isArray([1, 2, 3])
);
```

Output:

```text
true
```

---

# 66. JSON vs JavaScript Objects

A JavaScript object:

```javascript
const user = {
    name: "John",
    age: 25
};
```

JSON representation:

```json
{
    "name": "John",
    "age": 25
}
```

They look similar but are not the same thing.

```text
JavaScript Object
→ runtime JavaScript value

JSON
→ text-based data interchange format
```

---

# 67. `JSON.stringify()`

Convert a JavaScript value to JSON text:

```javascript
const user = {
    name: "John",
    age: 25
};

const json = JSON.stringify(user);

console.log(json);
```

Output:

```text
{"name":"John","age":25}
```

The result is a:

```text
string
```

---

# 68. `JSON.parse()`

Convert JSON text into a JavaScript value:

```javascript
const json = `
{
    "name": "John",
    "age": 25
}
`;

const user = JSON.parse(json);

console.log(user.name);
```

Output:

```text
John
```

Flow:

```text
JavaScript Object
      │
      ▼
JSON.stringify()
      │
      ▼
JSON Text
      │
      ▼
JSON.parse()
      │
      ▼
JavaScript Value
```

---

# 69. JSON Has Limitations

JSON is a data format and does not represent every JavaScript value directly.

For example, object properties whose values are functions or `undefined` are omitted by `JSON.stringify()`.

```javascript
const user = {
    name: "John",
    age: undefined,

    greet() {
        console.log("Hello");
    }
};

console.log(
    JSON.stringify(user)
);
```

Output:

```text
{"name":"John"}
```

JSON is intended for data, not executable behavior.

---

# 70. Object vs Map

JavaScript also provides:

```javascript
Map
```

Example:

```javascript
const user = new Map();

user.set("name", "John");
user.set("age", 25);

console.log(user.get("name"));
```

Output:

```text
John
```

General idea:

```text
Object
→ commonly represents structured records/entities

Map
→ purpose-built keyed collection
```

`Map` supports keys of any value type and has APIs such as:

```text
set()
get()
has()
delete()
size
```

Plain objects are still extremely common for application data.

---

# 71. Object Keys

Ordinary object property keys are:

```text
Strings
or
Symbols
```

If you write:

```javascript
const object = {
    10: "Hello"
};
```

then:

```javascript
console.log(object["10"]);
```

Output:

```text
Hello
```

The numeric literal property name becomes a string property key.

---

# 72. Symbol Keys

Objects can also have Symbol properties.

```javascript
const id = Symbol("id");

const user = {
    name: "John",
    [id]: 100
};

console.log(user[id]);
```

Output:

```text
100
```

Symbols are useful for unique property keys.

They are a more advanced JavaScript topic, but remember:

```text
Object property keys
→ String
→ Symbol
```

---

# 73. Object Constructor

Objects can also be created with:

```javascript
new Object()
```

Example:

```javascript
const user = new Object();

user.name = "John";
user.age = 25;
```

But object literal syntax is usually preferred:

```javascript
const user = {
    name: "John",
    age: 25
};
```

It is shorter and easier to read.

---

# 74. Constructor Functions Preview

Another older pattern for creating similar objects is a constructor function.

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
```

Result:

```text
user1
→ { name: "John", age: 25 }

user2
→ { name: "Alice", age: 30 }
```

We will study this properly under:

```text
Constructor Functions
Prototype
Classes
new keyword
```

---

# 75. Objects and Prototypes

JavaScript objects can inherit properties and methods through a **prototype chain**.

For example:

```javascript
const user = {
    name: "John"
};

console.log(user.toString);
```

`toString` is not defined directly in the object literal, but it can be found through the prototype chain.

Conceptually:

```text
user
│
├── own property: name
│
▼
Object.prototype
│
├── toString()
├── valueOf()
└── ...
```

We will study this deeply in the **Prototype** topic.

---

# 76. Own vs Inherited Properties

Consider:

```javascript
const user = {
    name: "John"
};
```

`name` is an:

```text
Own Property
```

But methods such as:

```javascript
user.toString
```

are typically inherited from the prototype chain.

This distinction matters for:

```text
Object.hasOwn()
in
for...in
Object.keys()
```

---

# 77. Object Interview Example 1

What is the output?

```javascript
const user = {
    name: "John"
};

const anotherUser = user;

anotherUser.name = "Alice";

console.log(user.name);
```

Answer:

```text
Alice
```

Both variables refer to the same object.

---

# 78. Object Interview Example 2

What is the output?

```javascript
const a = {
    value: 10
};

const b = {
    value: 10
};

console.log(a === b);
```

Answer:

```text
false
```

They are separate objects.

---

# 79. Object Interview Example 3

What is the output?

```javascript
const a = {
    value: 10
};

const b = a;

console.log(a === b);
```

Answer:

```text
true
```

Both variables hold references to the same object.

---

# 80. Object Interview Example 4

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

console.log(user.age);
console.log(updated.age);
```

Answer:

```text
25
30
```

The outer object was copied before updating `age` in the new object.

---

# 81. Object Interview Example 5

What is the output?

```javascript
const user = {
    address: {
        city: "Kolkata"
    }
};

const copy = {
    ...user
};

copy.address.city = "Delhi";

console.log(user.address.city);
```

Answer:

```text
Delhi
```

Because object spread performs a shallow copy.

The nested `address` object is shared.

---

# 82. Object Interview Example 6

What is the output?

```javascript
const key = "name";

const user = {
    name: "John"
};

console.log(user.key);
console.log(user[key]);
```

Answer:

```text
undefined
John
```

Why?

```javascript
user.key
```

looks for:

```text
"key"
```

while:

```javascript
user[key]
```

uses the variable value:

```text
"name"
```

---

# 83. Object Interview Example 7

What is the output?

```javascript
const user = {
    name: "John"
};

const {
    name,
    age = 18
} = user;

console.log(name);
console.log(age);
```

Answer:

```text
John
18
```

---

# 84. Object Interview Example 8

What is the output?

```javascript
const user = {
    score: 0
};

console.log(user.score || 100);
console.log(user.score ?? 100);
```

Answer:

```text
100
0
```

Because:

```text
||
→ reacts to falsy values

??
→ reacts only to null or undefined
```

---

# 85. Object Interview Example 9

What is the output?

```javascript
function update(obj) {
    obj.value = 20;
}

const data = {
    value: 10
};

update(data);

console.log(data.value);
```

Answer:

```text
20
```

The function received a copy of the reference value and used it to mutate the same object.

---

# 86. Object Interview Example 10

What is the output?

```javascript
function replace(obj) {
    obj = {
        value: 20
    };
}

const data = {
    value: 10
};

replace(data);

console.log(data.value);
```

Answer:

```text
10
```

Reassigning the parameter does not reassign the caller's variable.

---

# 87. Object Interview Example 11

What is the output?

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Answer:

```text
John
```

For this method call:

```javascript
user.greet()
```

`this` refers to `user`.

---

# 88. Object Interview Example 12

What is the output?

```javascript
const user = {
    profile: null
};

console.log(
    user.profile?.name ?? "Unknown"
);
```

Answer:

```text
Unknown
```

Because:

```javascript
user.profile
→ null

?.name
→ undefined

undefined ?? "Unknown"
→ "Unknown"
```

---

# 89. Object Interview Example 13

What is the output?

```javascript
const name = "John";
const age = 25;

const user = {
    name,
    age
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

This uses property shorthand.

---

# 90. Object Interview Example 14

What is the output?

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    name: userName
} = user;

console.log(userName);
```

Answer:

```text
John
```

`name` is the property being extracted.

`userName` is the new variable name.

---

# 91. React Props Are Objects

Consider:

```jsx
function User(props) {
    return (
        <h1>
            {props.name}
        </h1>
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

Conceptually, the component receives a props object similar to:

```javascript
{
    name: "John",
    age: 25
}
```

This is why object knowledge is essential for React.

---

# 92. React Props Destructuring

Instead of:

```jsx
function User(props) {
    return (
        <div>
            <h1>{props.name}</h1>
            <p>{props.age}</p>
        </div>
    );
}
```

we can destructure:

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

This is just JavaScript object destructuring.

React did not introduce this syntax.

---

# 93. React State Objects

State may contain objects.

```jsx
const [user, setUser] = useState({
    name: "John",
    age: 25
});
```

Suppose we want to update:

```text
age
```

A common React pattern is:

```jsx
setUser((previousUser) => ({
    ...previousUser,
    age: 26
}));
```

This creates a new object instead of mutating the existing state object.

---

# 94. Why Not Mutate React State Directly?

Avoid:

```jsx
user.age = 26;
setUser(user);
```

This mutates the existing object.

Instead:

```jsx
setUser((previousUser) => ({
    ...previousUser,
    age: 26
}));
```

Conceptually:

```text
Old State
│
▼
Object A


Update
│
▼
Create Object B
│
├── copy previous properties
└── change age


New State
│
▼
Object B
```

React state should be treated as immutable.

---

# 95. Updating Nested React State

Suppose:

```jsx
const [user, setUser] = useState({
    name: "John",

    address: {
        city: "Kolkata",
        country: "India"
    }
});
```

To update only the city:

```jsx
setUser((previousUser) => ({
    ...previousUser,

    address: {
        ...previousUser.address,
        city: "Delhi"
    }
}));
```

Why copy twice?

```text
...previousUser
→ copies outer object

...previousUser.address
→ copies nested address object
```

This avoids mutating the previous nested object.

---

# 96. Objects from APIs

API responses commonly contain objects.

Example:

```json
{
    "id": 1,
    "name": "John",
    "email": "john@example.com",
    "roles": [
        "user",
        "admin"
    ],
    "address": {
        "city": "Kolkata",
        "country": "India"
    }
}
```

After parsing JSON, JavaScript can work with the resulting object:

```javascript
console.log(user.name);
console.log(user.address.city);
console.log(user.roles[0]);
```

Understanding objects is therefore essential for:

```text
Frontend Development
Backend Development
REST APIs
React
Node.js
Databases
Configuration
JSON
```

---

# 97. Common Object Patterns

## Read Property

```javascript
user.name;
```

## Dynamic Read

```javascript
user[key];
```

## Add Property

```javascript
user.age = 25;
```

## Update Property

```javascript
user.age = 26;
```

## Delete Property

```javascript
delete user.age;
```

## Copy Object

```javascript
const copy = {
    ...user
};
```

## Update Immutably

```javascript
const updated = {
    ...user,
    age: 30
};
```

## Destructure

```javascript
const {
    name,
    age
} = user;
```

## Get Keys

```javascript
Object.keys(user);
```

## Get Values

```javascript
Object.values(user);
```

## Get Entries

```javascript
Object.entries(user);
```

---

# 98. Object Mental Model

Consider:

```javascript
const user = {
    name: "John",
    age: 25
};

const anotherUser = user;
```

Think:

```text
Stack / Variables
────────────────────

user
  │
  │ reference value
  ▼

      Object
┌─────────────────┐
│ name → "John"   │
│ age  → 25       │
└─────────────────┘
  ▲
  │ reference value
  │
anotherUser
```

Then:

```javascript
anotherUser.age = 30;
```

changes:

```text
      Object
┌─────────────────┐
│ name → "John"   │
│ age  → 30       │
└─────────────────┘
```

Therefore:

```javascript
console.log(user.age);
```

returns:

```text
30
```

---

# 99. Important Rules to Remember

```text
1. Objects store related data as key-value properties.

2. Object literals use { }.

3. Properties can contain almost any JavaScript value.

4. Dot notation accesses known property names.

5. Bracket notation supports dynamic property access.

6. Properties can be added, updated, and deleted.

7. const prevents variable reassignment, not object mutation.

8. Functions stored as object properties can act as methods.

9. Regular method calls can use this based on the call site.

10. Arrow functions do not create their own this.

11. Objects can contain nested objects and arrays.

12. Arrays can contain objects.

13. Assigning an object to another variable does not copy the object.

14. Object equality compares identity/reference.

15. Spread syntax creates a shallow copy.

16. Nested objects remain shared unless separately copied.

17. Object destructuring extracts properties into variables.

18. Object.keys() returns own enumerable string keys.

19. Object.values() returns own enumerable string-keyed values.

20. Object.entries() returns [key, value] pairs.

21. Object.hasOwn() checks own properties.

22. Optional chaining safely handles nullish intermediate values.

23. ?? provides defaults only for null and undefined.

24. Object.freeze() and Object.seal() are shallow.

25. JavaScript is pass-by-value.

26. Object values passed around are references to objects.

27. Functions can mutate objects through those references.

28. JSON and JavaScript objects are related but different.

29. React props are objects.

30. React state objects should be updated without mutation.
```

---

# 100. Variables + Scope + Closures + Callbacks + Objects

The concepts covered so far now begin working together.

Example:

```javascript
function createUser(name) {
    const user = {
        name,
        loginCount: 0
    };

    return function login(callback) {
        user.loginCount++;

        callback(user);
    };
}

const loginUser = createUser("John");

loginUser((user) => {
    console.log(
        `${user.name}: ${user.loginCount}`
    );
});
```

Output:

```text
John: 1
```

What is happening?

```text
createUser("John")
│
├── name parameter
│
├── creates user object
│
└── returns login function
        │
        ▼
loginUser
        │
        ├── closure over user
        │
        ├── modifies object
        │
        └── receives callback
                │
                ▼
          callback(user)
```

Concepts:

```text
Variable
→ user

Object
→ stores name and loginCount

Closure
→ login retains access to user

Callback
→ function passed to login

Scope
→ determines where user is accessible
```

---

# 101. React-Relevant Object Summary

Objects are everywhere in React.

### Props

```jsx
function User({
    name,
    age
}) {
    return (
        <div>
            {name} - {age}
        </div>
    );
}
```

Props are received through an object.

---

### State

```jsx
const [user, setUser] = useState({
    name: "John",
    age: 25
});
```

---

### State Update

```jsx
setUser((previousUser) => ({
    ...previousUser,
    age: 26
}));
```

---

### Nested State

```jsx
setUser((previousUser) => ({
    ...previousUser,

    address: {
        ...previousUser.address,
        city: "Delhi"
    }
}));
```

---

### API Data

```javascript
const user = {
    id: 1,
    name: "John",
    roles: ["admin"],
    profile: {
        city: "Kolkata"
    }
};
```

---

### Destructuring

```jsx
function User({
    name,
    profile
}) {
    return (
        <h1>
            {name} - {profile.city}
        </h1>
    );
}
```

---

### Object Spread

```javascript
const updatedUser = {
    ...user,
    name: "Alice"
};
```
