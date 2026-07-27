# TypeScript — `keyof`, `typeof` & Indexed Access Types

These features allow TypeScript to **derive types from other types and values**.

The three core concepts are:

```typescript
keyof T

typeof value

T[K]
```

Think of them as:

```text
keyof
→ Type → Keys

typeof
→ Value → Type

Indexed Access
→ Type + Key → Property Type
```

Example:

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

We can derive the entire type:

```typescript
type User =
    typeof user;
```

derive its keys:

```typescript
type UserKey =
    keyof User;
```

and derive a property's type:

```typescript
type Name =
    User["name"];
```

Result:

```text
User
→ {
    id: number;
    name: string;
    active: boolean;
}

UserKey
→ "id" | "name" | "active"

Name
→ string
```

These concepts are extremely important because they allow us to **derive types instead of manually duplicating them**.

---

# 1. `keyof`

`keyof` takes an object type and produces a union of its keys.

Example:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

Then:

```typescript
type UserKeys =
    keyof User;
```

Result:

```typescript
type UserKeys =
    "id"
    | "name"
    | "email";
```

Mental model:

```text
Object Type
    │
    ▼
  keyof
    │
    ▼
Union of Keys
```

---

# 2. Basic `keyof` Example

```typescript
type Product = {
    id: number;
    title: string;
    price: number;
    available: boolean;
};
```

Then:

```typescript
type ProductKey =
    keyof Product;
```

Equivalent:

```typescript
type ProductKey =
    "id"
    | "title"
    | "price"
    | "available";
```

---

# 3. Using a `keyof` Type

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

Create:

```typescript
type UserKey =
    keyof User;
```

Valid:

```typescript
const key1:
    UserKey = "id";

const key2:
    UserKey = "name";
```

Invalid:

```typescript
const key:
    UserKey = "password";
```

because:

```text
"password"
```

is not a key of `User`.

---

# 4. Why `keyof` Is Useful

Without `keyof`:

```typescript
function printProperty(
    user: User,
    key: string
) {
    // ...
}
```

`key` could be:

```text
"id"

"name"

"email"

"banana"

"something"

"anything"
```

But if we write:

```typescript
function printProperty(
    user: User,
    key: keyof User
) {
    console.log(
        user[key]
    );
}
```

only valid `User` keys are accepted.

---

# 5. `keyof` Is Dynamic at the Type Level

Suppose:

```typescript
interface User {
    id: number;
    name: string;
}
```

Then:

```typescript
type UserKey =
    keyof User;
```

is:

```text
"id" | "name"
```

Later we add:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

Now:

```typescript
keyof User
```

automatically becomes:

```text
"id"
|
"name"
|
"email"
```

We do not manually update the key union.

---

# 6. Manual Key Union vs `keyof`

Avoid duplicating:

```typescript
type UserKey =
    "id"
    | "name"
    | "email";
```

when those values intentionally represent the keys of:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

Prefer:

```typescript
type UserKey =
    keyof User;
```

Now the relationship is explicit.

---

# 7. `keyof` with Numeric Keys

Example:

```typescript
type StatusCodes = {
    200: string;
    404: string;
    500: string;
};
```

Then:

```typescript
type StatusCode =
    keyof StatusCodes;
```

Result is based on those numeric property keys:

```text
200 | 404 | 500
```

So `keyof` is not limited to string keys.

---

# 8. JavaScript Property Keys

JavaScript object property keys can fundamentally be:

```text
string

number

symbol
```

TypeScript represents this with:

```typescript
PropertyKey
```

Conceptually:

```typescript
type PropertyKey =
    string
    | number
    | symbol;
```

This matters when working with generic object utilities.

---

# 9. `keyof` and Index Signatures

Consider:

```typescript
type Dictionary = {
    [key: string]: boolean;
};
```

Then:

```typescript
type Key =
    keyof Dictionary;
```

may include:

```text
string | number
```

Why number?

Because JavaScript converts numeric object keys into strings.

Example:

```javascript
const object = {
    1: true
};

console.log(
    object[1]
);

console.log(
    object["1"]
);
```

Both refer to the same property.

---

# 10. `typeof` in JavaScript

Before TypeScript's type-level `typeof`, remember JavaScript already has:

```javascript
typeof
```

Example:

```javascript
typeof "Hello"
```

returns:

```text
"string"
```

And:

```javascript
typeof 100
```

returns:

```text
"number"
```

This happens at **runtime**.

---

# 11. JavaScript `typeof`

```typescript
const value = "Hello";

console.log(
    typeof value
);
```

Output:

```text
string
```

This is JavaScript's runtime operator.

It returns a string describing the runtime type.

Examples:

```typescript
typeof 10
// "number"

typeof true
// "boolean"

typeof "hello"
// "string"

typeof undefined
// "undefined"

typeof {}
// "object"

typeof (() => {})
// "function"
```

---

# 12. TypeScript `typeof`

TypeScript also allows `typeof` inside a **type position**.

Example:

```typescript
const user = {
    id: 1,
    name: "Alice"
};
```

Instead of manually writing:

```typescript
type User = {
    id: number;
    name: string;
};
```

we can write:

```typescript
type User =
    typeof user;
```

TypeScript derives the type from the value.

---

# 13. Value → Type

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

Then:

```typescript
type User =
    typeof user;
```

Result:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

Mental model:

```text
Runtime Value
     │
     ▼
   typeof
     │
     ▼
Compile-Time Type
```

---

# 14. JavaScript `typeof` vs TypeScript `typeof`

Runtime:

```typescript
const user = {
    id: 1
};

const result =
    typeof user;
```

`result` contains:

```text
"object"
```

But:

```typescript
type User =
    typeof user;
```

produces the TypeScript type:

```typescript
{
    id: number;
}
```

So:

```text
typeof in expression
→ JavaScript runtime operation

typeof in type position
→ TypeScript type query
```

---

# 15. Type Position

Example:

```typescript
const user = {
    id: 1,
    name: "Alice"
};
```

Here:

```typescript
typeof user
```

in:

```typescript
type User =
    typeof user;
```

is being used where TypeScript expects a type.

Therefore TypeScript interprets it as a:

```text
Type Query
```

---

# 16. `typeof` with Primitive Variables

```typescript
let age = 25;
```

Then:

```typescript
type Age =
    typeof age;
```

Result:

```text
number
```

Similarly:

```typescript
let name = "Alice";

type Name =
    typeof name;
```

typically gives:

```text
string
```

---

# 17. `typeof` and Literal Widening

Consider:

```typescript
let status = "loading";
```

TypeScript generally infers:

```text
string
```

because `status` can later change.

So:

```typescript
type Status =
    typeof status;
```

is:

```text
string
```

But:

```typescript
const status =
    "loading";
```

can infer the literal:

```text
"loading"
```

Therefore:

```typescript
type Status =
    typeof status;
```

can be:

```text
"loading"
```

---

# 18. `typeof` with Objects

Consider:

```typescript
const config = {
    apiUrl:
        "https://api.example.com",

    timeout: 5000,

    retries: 3
};
```

Then:

```typescript
type Config =
    typeof config;
```

TypeScript derives:

```typescript
type Config = {
    apiUrl: string;
    timeout: number;
    retries: number;
};
```

Notice that object property values are generally widened.

For example:

```text
5000
→ number
```

rather than preserving:

```text
5000
```

This is because object properties remain mutable.

---

# 19. `typeof` with Functions

Consider:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

Then:

```typescript
type AddFunction =
    typeof add;
```

Result conceptually:

```typescript
type AddFunction =
    (
        a: number,
        b: number
    ) => number;
```

---

# 20. Function Type Derivation

Instead of manually duplicating:

```typescript
type AddFunction =
    (
        a: number,
        b: number
    ) => number;
```

we can derive:

```typescript
type AddFunction =
    typeof add;
```

This is useful when another variable must have the same function signature.

```typescript
const subtract:
    typeof add =
    (
        a,
        b
    ) => a - b;
```

Now `subtract` must match the signature of `add`.

---

# 21. `typeof` with Classes

Suppose:

```typescript
class User {
    constructor(
        public name: string
    ) {}
}
```

There are two sides to a class:

```text
Class Constructor

Class Instance
```

This:

```typescript
User
```

in a type position usually represents the **instance type**.

Example:

```typescript
const user:
    User =
    new User("Alice");
```

But:

```typescript
typeof User
```

represents the constructor itself.

---

# 22. Class Instance vs Constructor

```typescript
class User {
    constructor(
        public name: string
    ) {}
}
```

Instance:

```typescript
let user: User;
```

means:

```text
Object created by new User(...)
```

Constructor:

```typescript
let Constructor:
    typeof User;
```

means:

```text
The User class constructor itself
```

Then:

```typescript
Constructor = User;
```

works.

---

# 23. `keyof typeof`

This is one of the most useful combinations.

Suppose:

```typescript
const permissions = {
    read: true,
    write: true,
    delete: false
};
```

First:

```typescript
typeof permissions
```

produces:

```typescript
{
    read: boolean;
    write: boolean;
    delete: boolean;
}
```

Then:

```typescript
keyof typeof permissions
```

produces:

```text
"read"
|
"write"
|
"delete"
```

---

# 24. Understanding `keyof typeof`

```typescript
const config = {
    apiUrl: "...",
    timeout: 5000,
    retries: 3
};
```

Then:

```typescript
type ConfigKey =
    keyof typeof config;
```

Read it inside-out:

```text
config
   │
   ▼
typeof config
   │
   ▼
Config Object Type
   │
   ▼
keyof
   │
   ▼
"apiUrl"
|
"timeout"
|
"retries"
```

---

# 25. Why `keyof typeof` Is Useful

Suppose the runtime object is the source of truth:

```typescript
const routes = {
    home: "/",
    login: "/login",
    dashboard: "/dashboard"
};
```

Instead of separately writing:

```typescript
type RouteName =
    "home"
    | "login"
    | "dashboard";
```

derive it:

```typescript
type RouteName =
    keyof typeof routes;
```

Now adding:

```typescript
settings: "/settings"
```

automatically updates `RouteName`.

---

# 26. Deriving Types from Configuration

```typescript
const featureFlags = {
    darkMode: true,
    newDashboard: false,
    betaSearch: true
};
```

Then:

```typescript
type Feature =
    keyof typeof featureFlags;
```

Result:

```text
"darkMode"
|
"newDashboard"
|
"betaSearch"
```

This is useful when configuration objects are the source of truth.

---

# 27. Indexed Access Types

Indexed access types allow us to obtain the type of a property from another type.

Example:

```typescript
interface User {
    id: number;
    name: string;
    active: boolean;
}
```

Then:

```typescript
type UserName =
    User["name"];
```

Result:

```text
string
```

---

# 28. Indexed Access Mental Model

```typescript
User["name"]
```

looks similar to JavaScript:

```javascript
user["name"]
```

But:

```typescript
User["name"]
```

works at the **type level**.

Think:

```text
Object Type
+
Property Key
=
Property Type
```

---

# 29. Basic Indexed Access

```typescript
type Product = {
    id: number;
    title: string;
    price: number;
    available: boolean;
};
```

Then:

```typescript
type ProductId =
    Product["id"];
```

Result:

```text
number
```

```typescript
type ProductTitle =
    Product["title"];
```

Result:

```text
string
```

```typescript
type ProductAvailable =
    Product["available"];
```

Result:

```text
boolean
```

---

# 30. Accessing Multiple Properties

Indexed access can use a union of keys.

```typescript
type ProductValue =
    Product[
        "title"
        | "price"
    ];
```

Since:

```text
title
→ string

price
→ number
```

Result:

```text
string | number
```

---

# 31. `T[keyof T]`

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

We know:

```typescript
keyof User
```

is:

```text
"id"
|
"name"
|
"active"
```

Therefore:

```typescript
User[keyof User]
```

means:

```typescript
User[
    "id"
    | "name"
    | "active"
]
```

Result:

```text
number
|
string
|
boolean
```

This gives us a union of **all property value types**.

---

# 32. Keys vs Values

Given:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

Keys:

```typescript
type Keys =
    keyof User;
```

Result:

```text
"id"
|
"name"
|
"active"
```

Values:

```typescript
type Values =
    User[keyof User];
```

Result:

```text
number
|
string
|
boolean
```

Mental model:

```text
keyof T
→ keys of T

T[keyof T]
→ values of T
```

---

# 33. Reusable Value Type Helper

We can create:

```typescript
type ValueOf<T> =
    T[keyof T];
```

Then:

```typescript
type UserValues =
    ValueOf<User>;
```

Result:

```text
number
|
string
|
boolean
```

This is a common helper pattern.

---

# 34. Indexed Access with Nested Objects

Suppose:

```typescript
interface User {
    id: number;

    profile: {
        firstName: string;
        lastName: string;
        age: number;
    };
}
```

Then:

```typescript
type Profile =
    User["profile"];
```

Result:

```typescript
{
    firstName: string;
    lastName: string;
    age: number;
}
```

Then:

```typescript
type FirstName =
    User["profile"]["firstName"];
```

Result:

```text
string
```

---

# 35. Chained Indexed Access

```typescript
type Age =
    User["profile"]["age"];
```

Read:

```text
User
 ↓
profile
 ↓
age
 ↓
number
```

This lets us extract deeply nested property types without redefining them.

---

# 36. Array Indexed Access

Suppose:

```typescript
type Users =
    User[];
```

We can get the element type using:

```typescript
type UserElement =
    Users[number];
```

Result:

```text
User
```

Why?

Because arrays are indexed by numbers.

```text
Users[0]
Users[1]
Users[2]
...
```

At the type level:

```typescript
Users[number]
```

means:

```text
The type of any element
inside this array.
```

---

# 37. `ArrayType[number]`

Example:

```typescript
type Names =
    string[];
```

Then:

```typescript
type Name =
    Names[number];
```

Result:

```text
string
```

Another:

```typescript
type Values =
    (
        string
        | number
    )[];
```

Then:

```typescript
type Value =
    Values[number];
```

Result:

```text
string | number
```

---

# 38. `typeof array[number]`

This is a very useful pattern.

Suppose:

```typescript
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
```

We can derive the element type:

```typescript
type User =
    typeof users[number];
```

Read inside-out:

```text
users
  │
  ▼
typeof users
  │
  ▼
Array Type
  │
  ▼
[number]
  │
  ▼
Element Type
```

Result:

```typescript
{
    id: number;
    name: string;
}
```

---

# 39. Avoid Duplicating Array Element Types

Instead of:

```typescript
type User = {
    id: number;
    name: string;
};

const users: User[] = [
    // ...
];
```

sometimes the runtime data is intentionally the source of truth:

```typescript
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
```

Then:

```typescript
type User =
    typeof users[number];
```

derives the element structure.

Which direction you choose depends on whether the **type** or the **value** should be the source of truth.

---

# 40. Tuple Indexed Access

Suppose:

```typescript
type UserTuple = [
    number,
    string,
    boolean
];
```

Then:

```typescript
type Id =
    UserTuple[0];
```

Result:

```text
number
```

```typescript
type Name =
    UserTuple[1];
```

Result:

```text
string
```

```typescript
type Active =
    UserTuple[2];
```

Result:

```text
boolean
```

---

# 41. All Tuple Values

Given:

```typescript
type UserTuple = [
    number,
    string,
    boolean
];
```

Then:

```typescript
type Value =
    UserTuple[number];
```

Result:

```text
number
|
string
|
boolean
```

This works because:

```text
[number]
```

asks for the type of all numeric positions.

---

# 42. Deriving Union from Constant Array

Suppose:

```typescript
const roles = [
    "admin",
    "editor",
    "viewer"
] as const;
```

Then:

```typescript
type Role =
    typeof roles[number];
```

Result:

```text
"admin"
|
"editor"
|
"viewer"
```

This is a very common TypeScript pattern.

---

# 43. Why `as const` Matters

Without:

```typescript
as const
```

this:

```typescript
const roles = [
    "admin",
    "editor",
    "viewer"
];
```

is generally inferred as:

```text
string[]
```

Therefore:

```typescript
typeof roles[number]
```

would be:

```text
string
```

But:

```typescript
const roles = [
    "admin",
    "editor",
    "viewer"
] as const;
```

preserves the literal values.

Then:

```typescript
typeof roles[number]
```

becomes:

```text
"admin"
|
"editor"
|
"viewer"
```

We will cover `as const` separately.

---

# 44. Deriving Union from Object Values

Suppose:

```typescript
const Status = {
    Pending: "pending",
    Running: "running",
    Complete: "complete"
} as const;
```

Keys:

```typescript
type StatusName =
    keyof typeof Status;
```

Result:

```text
"Pending"
|
"Running"
|
"Complete"
```

But what if we want:

```text
"pending"
|
"running"
|
"complete"
```

?

Use:

```typescript
type StatusValue =
    typeof Status[
        keyof typeof Status
    ];
```

---

# 45. Understanding Object Value Union

```typescript
const Status = {
    Pending: "pending",
    Running: "running",
    Complete: "complete"
} as const;
```

Step 1:

```typescript
typeof Status
```

produces the object type.

Step 2:

```typescript
keyof typeof Status
```

produces:

```text
"Pending"
|
"Running"
|
"Complete"
```

Step 3:

```typescript
typeof Status[
    keyof typeof Status
]
```

gets the values corresponding to all those keys:

```text
"pending"
|
"running"
|
"complete"
```

---

# 46. Reusable `ValueOf`

We can simplify:

```typescript
type ValueOf<T> =
    T[keyof T];
```

Then:

```typescript
type StatusValue =
    ValueOf<
        typeof Status
    >;
```

Result:

```text
"pending"
|
"running"
|
"complete"
```

---

# 47. Enum-Like Objects

Instead of:

```typescript
enum Status {
    Pending = "pending",
    Running = "running",
    Complete = "complete"
}
```

some TypeScript codebases use:

```typescript
const Status = {
    Pending: "pending",
    Running: "running",
    Complete: "complete"
} as const;
```

Then:

```typescript
type Status =
    typeof Status[
        keyof typeof Status
    ];
```

This creates:

```text
"pending"
|
"running"
|
"complete"
```

while keeping a runtime object:

```typescript
Status.Pending
```

available.

---

# 48. Type as Source of Truth

Sometimes we start with:

```typescript
type Role =
    "admin"
    | "editor"
    | "viewer";
```

Then values must conform to that type.

This means:

```text
Type
→ Source of Truth
```

---

# 49. Value as Source of Truth

Sometimes we start with:

```typescript
const roles = [
    "admin",
    "editor",
    "viewer"
] as const;
```

Then derive:

```typescript
type Role =
    typeof roles[number];
```

This means:

```text
Runtime Value
→ Source of Truth
```

Both approaches are valid.

The important thing is avoiding unnecessary duplication.

---

# 50. `keyof` with Generics

We already saw:

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

Now we can understand every part.

---

# 51. Understanding `T`

Suppose:

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

Calling:

```typescript
getProperty(
    user,
    "name"
);
```

makes:

```text
T
→ {
    id: number;
    name: string;
    active: boolean;
}
```

---

# 52. Understanding `keyof T`

For that `T`:

```typescript
keyof T
```

is:

```text
"id"
|
"name"
|
"active"
```

---

# 53. Understanding `K extends keyof T`

```typescript
K extends keyof T
```

means:

```text
K must be one of:

"id"
"name"
"active"
```

Therefore:

```typescript
getProperty(
    user,
    "name"
);
```

works.

But:

```typescript
getProperty(
    user,
    "password"
);
```

fails.

---

# 54. Understanding `T[K]`

If:

```text
K = "name"
```

then:

```typescript
T[K]
```

becomes:

```typescript
T["name"]
```

which is:

```text
string
```

Therefore:

```typescript
const result =
    getProperty(
        user,
        "name"
    );
```

has type:

```text
string
```

---

# 55. Complete Generic Relationship

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

Flow:

```text
Object
  │
  ▼
T
  │
  ▼
keyof T
  │
  ▼
Valid Keys
  │
  ▼
K
  │
  ▼
T[K]
  │
  ▼
Correct Value Type
```

---

# 56. Safe Setter

We can use the same relationship for writing properties.

```typescript
function setProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K,
    value: T[K]
): void {
    object[key] = value;
}
```

Given:

```typescript
const user = {
    id: 1,
    name: "Alice"
};
```

Valid:

```typescript
setProperty(
    user,
    "name",
    "Bob"
);
```

Invalid:

```typescript
setProperty(
    user,
    "name",
    100
);
```

because:

```typescript
typeof user["name"]
```

is:

```text
string
```

---

# 57. Dynamic Property Access Problem

Suppose:

```typescript
const user = {
    id: 1,
    name: "Alice"
};

const key: string =
    "name";
```

Then:

```typescript
user[key]
```

may produce a TypeScript error under strict typing.

Why?

Because:

```text
string
```

means any string:

```text
"name"

"id"

"banana"

"anything"
```

TypeScript cannot prove the key exists.

---

# 58. Correct Dynamic Key Type

Instead:

```typescript
const key:
    keyof typeof user =
    "name";
```

Now:

```typescript
user[key]
```

is safe because TypeScript knows `key` must be a valid property.

---

# 59. Function Example

Avoid:

```typescript
function displayField(
    key: string
) {
    return user[key];
}
```

Prefer:

```typescript
function displayField(
    key:
        keyof typeof user
) {
    return user[key];
}
```

Now callers cannot pass invalid keys.

---

# 60. React Table Column Pattern

Suppose:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

A table column might be:

```typescript
type UserColumn = {
    key: keyof User;
    label: string;
};
```

Then:

```typescript
const columns:
    UserColumn[] = [
        {
            key: "id",
            label: "ID"
        },

        {
            key: "name",
            label: "Name"
        },

        {
            key: "email",
            label: "Email"
        }
    ];
```

This fails:

```typescript
{
    key: "banana",
    label: "Banana"
}
```

because `banana` is not a `User` property.

---

# 61. Generic Table Pattern

Instead of tying the column to `User`:

```typescript
type Column<T> = {
    key: keyof T;
    label: string;
};
```

Then:

```typescript
type UserColumn =
    Column<User>;

type ProductColumn =
    Column<Product>;
```

The same column model works for multiple row types.

---

# 62. Form Field Pattern

Suppose:

```typescript
type LoginForm = {
    email: string;
    password: string;
};
```

A field name can be:

```typescript
type LoginField =
    keyof LoginForm;
```

Result:

```text
"email"
|
"password"
```

Useful for typed form helpers:

```typescript
function updateField<
    K extends keyof LoginForm
>(
    field: K,
    value: LoginForm[K]
) {
    // ...
}
```

---

# 63. Form Value Relationship

For:

```typescript
updateField(
    "email",
    "alice@example.com"
);
```

TypeScript sees:

```text
K
→ "email"

LoginForm[K]
→ string
```

If a form contained:

```typescript
type Form = {
    name: string;
    age: number;
    subscribed: boolean;
};
```

then:

```typescript
updateField(
    "age",
    25
);
```

could require `number`, while:

```typescript
updateField(
    "subscribed",
    true
);
```

could require `boolean`.

This is the power of indexed access combined with generics.

---

# 64. Event Map Pattern

Suppose:

```typescript
type Events = {
    userCreated: {
        id: number;
        name: string;
    };

    userDeleted: {
        id: number;
    };

    logout: undefined;
};
```

Then event names are:

```typescript
type EventName =
    keyof Events;
```

Result:

```text
"userCreated"
|
"userDeleted"
|
"logout"
```

Payload:

```typescript
type CreatedPayload =
    Events["userCreated"];
```

Result:

```typescript
{
    id: number;
    name: string;
}
```

---

# 65. Strongly Typed Event Function

```typescript
function emit<
    K extends keyof Events
>(
    event: K,
    payload: Events[K]
) {
    // ...
}
```

Then:

```typescript
emit(
    "userDeleted",
    {
        id: 10
    }
);
```

works.

But:

```typescript
emit(
    "userDeleted",
    {
        name: "Alice"
    }
);
```

fails.

The event name determines the payload type.

---

# 66. Configuration Getter Pattern

```typescript
const config = {
    apiUrl: "/api",
    timeout: 5000,
    retries: 3,
    debug: true
};
```

We can write:

```typescript
function getConfig<
    K extends keyof typeof config
>(
    key: K
): typeof config[K] {
    return config[key];
}
```

Then:

```typescript
const timeout =
    getConfig("timeout");
```

is:

```text
number
```

And:

```typescript
const debug =
    getConfig("debug");
```

is:

```text
boolean
```

---

# 67. `typeof config[K]`

In:

```typescript
typeof config[K]
```

TypeScript first gets:

```typescript
typeof config
```

and then indexes it with:

```typescript
[K]
```

Conceptually:

```text
typeof config
→ Config Type

Config Type[K]
→ Value type for K
```

---

# 68. API Response Derivation

Suppose:

```typescript
const defaultResponse = {
    success: false,
    message: "",
    data: null
};
```

We could derive:

```typescript
type DefaultResponse =
    typeof defaultResponse;
```

But be careful.

Type inference reflects the value's structure, which may not represent the complete intended domain.

For example:

```typescript
data: null
```

may infer:

```text
null
```

when your real application expects:

```text
User | null
```

So deriving from values is useful only when the value accurately represents the desired type.

---

# 69. Type-First vs Value-First Design

Type-first:

```typescript
type Config = {
    apiUrl: string;
    timeout: number;
};

const config: Config = {
    apiUrl: "/api",
    timeout: 5000
};
```

Value-first:

```typescript
const config = {
    apiUrl: "/api",
    timeout: 5000
};

type Config =
    typeof config;
```

Use type-first when the contract defines the value.

Use value-first when the value is intentionally the canonical source.

---

# 70. `typeof` Cannot Query Arbitrary Expressions in Type Positions

You will commonly use:

```typescript
typeof someVariable

typeof someFunction

typeof SomeClass
```

TypeScript's type query syntax is not simply the same as applying runtime `typeof` to any arbitrary expression.

For complex expressions, store the value first:

```typescript
const result =
    createUser();

type Result =
    typeof result;
```

Or derive from the function:

```typescript
type Result =
    ReturnType<
        typeof createUser
    >;
```

---

# 71. Common Mistake — Confusing `keyof` with Values

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

This:

```typescript
keyof User
```

produces:

```text
"id" | "name"
```

not:

```text
number | string
```

For values:

```typescript
User[keyof User]
```

produces:

```text
number | string
```

---

# 72. Common Mistake — Confusing Runtime `typeof`

This:

```typescript
typeof value
```

inside:

```typescript
if (
    typeof value === "string"
)
```

is runtime JavaScript.

This:

```typescript
type Value =
    typeof value;
```

is a TypeScript type query.

Same keyword.

Different context.

---

# 73. Common Mistake — Using `string` for Known Keys

Avoid:

```typescript
function getField(
    key: string
) {}
```

when `key` must correspond to a known object.

Prefer:

```typescript
function getField(
    key: keyof User
) {}
```

This prevents invalid property names.

---

# 74. Common Mistake — Manually Duplicating Key Unions

Avoid:

```typescript
type UserKey =
    "id"
    | "name"
    | "email";
```

if the keys should always follow `User`.

Prefer:

```typescript
type UserKey =
    keyof User;
```

---

# 75. Common Mistake — Losing Literal Values

Suppose:

```typescript
const roles = [
    "admin",
    "user"
];
```

Then:

```typescript
type Role =
    typeof roles[number];
```

may simply become:

```text
string
```

If the literal values themselves should define the type:

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

Then:

```typescript
type Role =
    typeof roles[number];
```

becomes:

```text
"admin" | "user"
```

---

# 76. Common Mistake — Assuming `typeof` Validates Runtime Data

```typescript
type User =
    typeof someValue;
```

only derives a compile-time type from what TypeScript knows about `someValue`.

It does not validate external data.

For:

```text
API responses

localStorage

JSON files

User input

Database results

Third-party data
```

runtime validation may still be necessary.

---

# 77. Common Mistake — Over-Deriving Types

Just because you can write:

```typescript
typeof something
```

does not mean you always should.

If a type represents an important domain contract:

```text
User

Order

Payment

API Contract
```

an explicit type may communicate intent better.

Derive types when the dependency is intentional.

---

# 78. Common Mistake — Choosing Wrong Source of Truth

Avoid maintaining both:

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

and:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
];
```

independently if they are supposed to represent exactly the same thing.

They can drift apart.

Choose one source of truth and derive the other where appropriate.

---

# 79. Common Mistake — Invalid Indexed Access

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

This works:

```typescript
type Name =
    User["name"];
```

This fails:

```typescript
type Password =
    User["password"];
```

because `password` is not a valid key.

---

# 80. Common Mistake — Thinking `[number]` Means Index `number`

In:

```typescript
type Item =
    Users[number];
```

`number` does not mean:

```text
Property named "number"
```

It means:

```text
Any numeric index.
```

Therefore it extracts the array element type.

---

# 81. Interview Question — What Does `keyof` Do?

`keyof` takes an object type and creates a union of its property keys.

Example:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
keyof User
```

is:

```text
"id" | "name"
```

---

# 82. Interview Question — What Does TypeScript `typeof` Do?

In a type position, `typeof` derives the type of an existing value.

Example:

```typescript
const user = {
    id: 1,
    name: "Alice"
};

type User =
    typeof user;
```

This avoids manually redefining the object's structure.

---

# 83. Interview Question — Runtime vs Type-Level `typeof`?

Runtime:

```typescript
typeof value
```

returns a string such as:

```text
"string"

"number"

"object"
```

Type-level:

```typescript
type T =
    typeof value;
```

captures the TypeScript type of the value.

---

# 84. Interview Question — What Is `keyof typeof`?

It derives the keys of an existing runtime value.

Example:

```typescript
const config = {
    theme: "dark",
    timeout: 5000
};
```

Then:

```typescript
type ConfigKey =
    keyof typeof config;
```

Result:

```text
"theme"
|
"timeout"
```

---

# 85. Interview Question — What Is Indexed Access?

Indexed access extracts property types from another type.

Example:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
type Name =
    User["name"];
```

Result:

```text
string
```

---

# 86. Interview Question — What Does `T[K]` Mean?

`T[K]` means:

```text
The type of property K
inside T.
```

It is especially useful when:

```typescript
K extends keyof T
```

because TypeScript knows `K` is a valid property.

---

# 87. Interview Question — What Does `T[keyof T]` Mean?

It creates a union of the value types of `T`.

Example:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

Then:

```typescript
User[keyof User]
```

is:

```text
number
|
string
|
boolean
```

---

# 88. Interview Question — How Do You Get an Array Element Type?

Use:

```typescript
ArrayType[number]
```

Example:

```typescript
type Users =
    User[];

type UserElement =
    Users[number];
```

`UserElement` is:

```text
User
```

---

# 89. Interview Question — How Do You Derive a Union from a Constant Array?

```typescript
const roles = [
    "admin",
    "editor",
    "viewer"
] as const;
```

Then:

```typescript
type Role =
    typeof roles[number];
```

Result:

```text
"admin"
|
"editor"
|
"viewer"
```

---

# 90. Interview Question — How Do You Get Object Value Types?

Create:

```typescript
type ValueOf<T> =
    T[keyof T];
```

Then:

```typescript
type Values =
    ValueOf<MyObject>;
```

returns a union of the object's property value types.

---

# 91. Interview Question — Why Use `K extends keyof T`?

It ensures a generic key is actually a valid property of the generic object type.

Example:

```typescript
function get<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

This preserves both key safety and the correct return type.

---

# 92. Cheat Sheet

```text
KEYOF
================================

keyof T

Type
→ Keys


EXAMPLE
================================

type User = {
    id: number;
    name: string;
};

keyof User

→ "id" | "name"


TYPEOF
================================

typeof value

Value
→ Type


EXAMPLE
================================

const user = {
    id: 1,
    name: "Alice"
};

type User =
    typeof user;


KEYOF TYPEOF
================================

keyof typeof value

Value
→ Type
→ Keys


INDEXED ACCESS
================================

T[K]

Type + Key
→ Property Type


EXAMPLE
================================

User["name"]

→ string


MULTIPLE KEYS
================================

User[
    "id" | "name"
]

→ number | string


ALL KEYS
================================

keyof User


ALL VALUES
================================

User[keyof User]


VALUE HELPER
================================

type ValueOf<T> =
    T[keyof T];


ARRAY ELEMENT
================================

ArrayType[number]


VALUE ARRAY ELEMENT
================================

typeof array[number]


CONST ARRAY UNION
================================

const roles = [
    "admin",
    "user"
] as const;

type Role =
    typeof roles[number];

→ "admin" | "user"


OBJECT KEYS
================================

keyof typeof object


OBJECT VALUES
================================

typeof object[
    keyof typeof object
]


GENERIC KEY
================================

K extends keyof T


GENERIC VALUE
================================

T[K]


SAFE GETTER
================================

function get<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K]


CORE MENTAL MODEL
================================

keyof T
→ Keys

typeof value
→ Type

T[K]
→ Value Type

T[keyof T]
→ All Value Types

typeof array[number]
→ Array Element Type
```

---

# 93. Important Rules to Remember

```text
1. keyof operates on types.

2. keyof produces property keys.

3. keyof T usually creates a union of keys.

4. keyof does not return property value types.

5. T[keyof T] creates property value types.

6. Object property keys can be string, number, or symbol.

7. keyof can work with numeric keys.

8. Index signatures affect keyof results.

9. JavaScript typeof operates at runtime.

10. TypeScript typeof can operate in type positions.

11. Runtime typeof returns strings describing runtime types.

12. Type-level typeof derives a TypeScript type from a value.

13. typeof value can avoid duplicated type definitions.

14. typeof works with variables.

15. typeof works with objects.

16. typeof works with functions.

17. typeof works with classes.

18. typeof Class refers to the constructor side.

19. Class in a type position usually represents the instance side.

20. keyof typeof derives keys from runtime values.

21. Read keyof typeof inside-out.

22. typeof object derives the object type.

23. keyof then derives its keys.

24. Indexed access extracts property types.

25. T["key"] gets a specific property type.

26. T["a" | "b"] gets a union of property types.

27. T[keyof T] gets all property value types.

28. Indexed access can be chained.

29. Nested types can be extracted without duplication.

30. ArrayType[number] gets an array element type.

31. Tuple[0] gets the first tuple element type.

32. Tuple[number] gets all tuple element types.

33. typeof array[number] derives an element type from a runtime array.

34. as const can preserve literal array values.

35. typeof constArray[number] can create literal unions.

36. keyof typeof object can derive object key unions.

37. typeof object[keyof typeof object] can derive object value unions.

38. ValueOf<T> is commonly implemented as T[keyof T].

39. K extends keyof T constrains K to valid keys.

40. T[K] connects a key to its property value type.

41. Generic getters commonly use K extends keyof T.

42. Generic setters commonly use T[K].

43. Dynamic string keys are often too broad.

44. keyof can safely constrain dynamic property access.

45. Table columns can use keyof row types.

46. Form field names can use keyof form types.

47. Form field values can use T[K].

48. Event names can use keyof event maps.

49. Event payloads can use EventMap[K].

50. Configuration getters can use keyof typeof config.

51. Value-first design derives types from values.

52. Type-first design defines contracts before values.

53. Neither design is universally better.

54. Choose the correct source of truth.

55. Avoid maintaining duplicate key unions manually.

56. Avoid maintaining duplicate value unions manually when derivation is intentional.

57. Deriving types creates coupling.

58. That coupling should be intentional.

59. Domain contracts may deserve explicit types.

60. Configuration values often work well as value-first sources.

61. Constant arrays often work well as value-first sources.

62. as const prevents useful literals from widening.

63. let values often widen more than const values.

64. Object properties can still widen even with const objects.

65. as const provides deeper literal preservation.

66. keyof is compile-time only.

67. Type-level typeof is compile-time only.

68. Indexed access types are compile-time only.

69. These features generate no runtime validation.

70. These features generate no runtime object transformations.

71. External data still needs runtime validation.

72. typeof does not make API responses trustworthy.

73. keyof does not inspect objects dynamically at runtime.

74. Indexed access types do not access runtime values.

75. User["name"] is a type-level operation.

76. user["name"] is a runtime operation.

77. Case matters for property keys.

78. Invalid indexed keys produce type errors.

79. Arrays use numeric indexing.

80. [number] is useful for extracting element types.

81. keyof T and T[keyof T] are different.

82. keyof T gives keys.

83. T[keyof T] gives values.

84. keyof typeof value gives keys of a value's inferred type.

85. typeof value[key] requires understanding precedence/context carefully.

86. Named intermediate types can improve readability.

87. Complex derivations should be read inside-out.

88. keyof works extensively with generics.

89. Indexed access works extensively with mapped types.

90. typeof works extensively with ReturnType and Parameters.

91. These concepts power many utility types.

92. These concepts are common in React reusable components.

93. These concepts are common in typed forms.

94. These concepts are common in API helpers.

95. These concepts are common in configuration systems.

96. These concepts are common in event systems.

97. These concepts are common in generic libraries.

98. Prefer deriving information when the relationship should stay synchronized.

99. Prefer explicit types when they communicate an independent contract.

100. The core relationship is:

Type → keyof → Keys

Value → typeof → Type

Type + Key → T[K] → Value Type
```

---

# 94. Final Mental Model

Start with a runtime value:

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

Get its type:

```typescript
type User =
    typeof user;
```

```text
user
 │
 │ typeof
 ▼

{
    id: number;
    name: string;
    active: boolean;
}
```

Get its keys:

```typescript
type UserKey =
    keyof User;
```

```text
User
 │
 │ keyof
 ▼

"id"
|
"name"
|
"active"
```

Get a specific value type:

```typescript
type Name =
    User["name"];
```

```text
User
+
"name"
 │
 ▼
string
```

Get all value types:

```typescript
type UserValue =
    User[keyof User];
```

```text
User
 │
 ├── id     → number
 │
 ├── name   → string
 │
 └── active → boolean
                │
                ▼

number | string | boolean
```

Or directly:

```typescript
type UserKey =
    keyof typeof user;
```

Flow:

```text
Runtime Value
     │
     │ typeof
     ▼
Object Type
     │
     │ keyof
     ▼
Key Union
```

The four patterns worth remembering are:

```typescript
keyof T
```

```text
Get keys from a type.
```

```typescript
typeof value
```

```text
Get a type from a value.
```

```typescript
T[K]
```

```text
Get a value type from
a type and key.
```

```typescript
typeof array[number]
```

```text
Get an element type
from an array value.
```

Together:

```text
Value
  │
  │ typeof
  ▼
Type
  │
  │ keyof
  ▼
Keys
  │
  │ T[K]
  ▼
Value Types
```
