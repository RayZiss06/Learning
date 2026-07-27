# TypeScript — Utility Types

TypeScript provides built-in **Utility Types** that let us create new types by transforming existing types.

Instead of repeatedly writing similar interfaces:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

we can derive new types from `User`.

Example:

```typescript
type UpdateUser =
    Partial<User>;
```

Now all properties are optional:

```typescript
type UpdateUser = {
    id?: number;
    name?: string;
    email?: string;
};
```

The core idea is:

```text
Existing Type
     │
     ▼
Utility Type
     │
     ▼
Modified Type
```

---

# 1. Why Utility Types Exist

Suppose we have:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    active: boolean;
}
```

For updating a user, we might create:

```typescript
interface UpdateUser {
    id?: number;
    name?: string;
    email?: string;
    active?: boolean;
}
```

But now we duplicated the structure.

If `User` changes:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    active: boolean;
    avatar: string;
}
```

we might forget to update `UpdateUser`.

Instead:

```typescript
type UpdateUser =
    Partial<User>;
```

automatically follows `User`.

This is the major purpose of utility types:

```text
Avoid duplicated type definitions.

Derive types from existing types.

Keep related types synchronized.
```

---

# 2. `Partial<T>`

`Partial<T>` makes every property in `T` optional.

Original:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

Create:

```typescript
type PartialUser =
    Partial<User>;
```

Equivalent to:

```typescript
type PartialUser = {
    id?: number;
    name?: string;
    email?: string;
};
```

---

# 3. `Partial<T>` Example

```typescript
function updateUser(
    user: User,
    updates: Partial<User>
): User {
    return {
        ...user,
        ...updates
    };
}
```

Now:

```typescript
updateUser(
    user,
    {
        name: "Bob"
    }
);
```

works.

We do not need to provide every property.

---

# 4. `Partial<T>` Use Cases

Common uses:

```text
PATCH requests

Update DTOs

Configuration overrides

Form drafts

State updates

Optional filters

Object merge functions
```

Example:

```typescript
type UpdateUserDto =
    Partial<User>;
```

---

# 5. `Partial<T>` Is Shallow

Consider:

```typescript
interface User {
    id: number;

    profile: {
        name: string;
        age: number;
    };
}
```

Then:

```typescript
type Update =
    Partial<User>;
```

becomes conceptually:

```typescript
type Update = {
    id?: number;

    profile?: {
        name: string;
        age: number;
    };
};
```

The `profile` property becomes optional.

But its internal properties remain required.

This is valid:

```typescript
const update: Update = {
    profile: {
        name: "Alice",
        age: 25
    }
};
```

But this may fail:

```typescript
const update: Update = {
    profile: {
        name: "Alice"
    }
};
```

because:

```text
age
```

is still required inside `profile`.

Therefore:

```text
Partial<T>
=
Shallow Partial
```

---

# 6. `Required<T>`

`Required<T>` does the opposite of `Partial<T>`.

It makes every property required.

Original:

```typescript
interface UserConfig {
    theme?: string;
    language?: string;
    notifications?: boolean;
}
```

Then:

```typescript
type CompleteConfig =
    Required<UserConfig>;
```

Equivalent to:

```typescript
type CompleteConfig = {
    theme: string;
    language: string;
    notifications: boolean;
};
```

---

# 7. `Required<T>` Example

```typescript
interface Config {
    apiUrl?: string;
    timeout?: number;
}
```

We might accept partial configuration:

```typescript
function initialize(
    config: Config
) {
    // ...
}
```

but after defaults are applied:

```typescript
const completeConfig:
    Required<Config> = {
        apiUrl:
            "https://example.com",
        timeout: 5000
    };
```

we know every property exists.

---

# 8. `Required<T>` Is Also Shallow

Just like `Partial<T>`, `Required<T>` affects only the first level.

It does not recursively modify nested objects.

---

# 9. `Readonly<T>`

`Readonly<T>` makes all properties read-only.

Original:

```typescript
interface User {
    id: number;
    name: string;
}
```

Then:

```typescript
type ReadonlyUser =
    Readonly<User>;
```

Equivalent:

```typescript
type ReadonlyUser = {
    readonly id: number;
    readonly name: string;
};
```

---

# 10. `Readonly<T>` Example

```typescript
const user:
    Readonly<User> = {
        id: 1,
        name: "Alice"
    };
```

Reading is allowed:

```typescript
console.log(
    user.name
);
```

Mutation is not:

```typescript
user.name = "Bob";
```

TypeScript reports an error.

---

# 11. `Readonly<T>` Does Not Freeze Runtime Objects

Important:

```typescript
Readonly<User>
```

is a compile-time restriction.

It does not automatically execute:

```javascript
Object.freeze(...)
```

at runtime.

Therefore:

```text
Readonly<T>
→ TypeScript immutability

Object.freeze()
→ Runtime behavior
```

These are different concepts.

---

# 12. `Readonly<T>` Is Shallow

Consider:

```typescript
interface User {
    id: number;

    profile: {
        name: string;
    };
}
```

Then:

```typescript
const user:
    Readonly<User> = {
        id: 1,

        profile: {
            name: "Alice"
        }
    };
```

This fails:

```typescript
user.profile = {
    name: "Bob"
};
```

because `profile` itself is readonly.

But:

```typescript
user.profile.name =
    "Bob";
```

can still be allowed because the nested object was not recursively made readonly.

---

# 13. `Pick<T, K>`

`Pick<T, K>` creates a type containing only selected properties from `T`.

Example:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
    createdAt: Date;
}
```

Suppose we only need:

```text
id
name
email
```

We can write:

```typescript
type PublicUser =
    Pick<
        User,
        "id"
        | "name"
        | "email"
    >;
```

Equivalent:

```typescript
type PublicUser = {
    id: number;
    name: string;
    email: string;
};
```

---

# 14. `Pick<T, K>` Requires Valid Keys

This works:

```typescript
type BasicUser =
    Pick<
        User,
        "id" | "name"
    >;
```

This fails:

```typescript
type BasicUser =
    Pick<
        User,
        "banana"
    >;
```

because:

```text
"banana"
```

is not part of:

```typescript
keyof User
```

---

# 15. `Pick<T, K>` Use Cases

Common uses:

```text
API response models

Component props

List item models

Preview models

Table rows

Public views

Form fields
```

Example:

```typescript
type UserCardProps =
    Pick<
        User,
        "name" | "email"
    >;
```

---

# 16. `Omit<T, K>`

`Omit<T, K>` does the opposite of `Pick`.

It creates a type containing everything except selected properties.

Example:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
}
```

Then:

```typescript
type PublicUser =
    Omit<
        User,
        "password"
    >;
```

Equivalent:

```typescript
type PublicUser = {
    id: number;
    name: string;
    email: string;
};
```

---

# 17. Omitting Multiple Properties

```typescript
type CreateUser =
    Omit<
        User,
        "id"
        | "createdAt"
        | "updatedAt"
    >;
```

Useful when the server generates those fields.

Example:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    createdAt: Date;
    updatedAt: Date;
}
```

Client input:

```typescript
type CreateUser =
    Omit<
        User,
        "id"
        | "createdAt"
        | "updatedAt"
    >;
```

---

# 18. `Pick` vs `Omit`

Given:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
}
```

Use:

```typescript
Pick<
    User,
    "id" | "name"
>
```

when you want to specify:

```text
What should remain.
```

Use:

```typescript
Omit<
    User,
    "password"
>
```

when you want to specify:

```text
What should be removed.
```

---

# 19. Choosing `Pick` vs `Omit`

If the type has 20 properties and you need 3:

```typescript
Pick<T, ...>
```

is usually clearer.

If the type has 20 properties and you need 19:

```typescript
Omit<T, ...>
```

is usually clearer.

Choose whichever expresses the intent better.

---

# 20. `Record<K, T>`

`Record<K, T>` creates an object type where:

```text
K
→ keys

T
→ values
```

Example:

```typescript
type UserRoles =
    Record<
        string,
        string
    >;
```

Equivalent conceptually:

```typescript
type UserRoles = {
    [key: string]: string;
};
```

Example:

```typescript
const roles:
    UserRoles = {
        alice: "admin",
        bob: "user"
    };
```

---

# 21. `Record` with Literal Keys

This is where `Record` becomes especially useful.

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

Then:

```typescript
type Permissions =
    Record<
        Role,
        string[]
    >;
```

Now:

```typescript
const permissions:
    Permissions = {
        admin: [
            "read",
            "write",
            "delete"
        ],

        user: [
            "read",
            "write"
        ],

        guest: [
            "read"
        ]
    };
```

All three keys are required.

---

# 22. Missing `Record` Key

Given:

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

This may fail:

```typescript
const permissions:
    Record<
        Role,
        string[]
    > = {
        admin: [],
        user: []
    };
```

because:

```text
guest
```

is missing.

This makes `Record` useful for exhaustive mappings.

---

# 23. `Record` with Enums

Suppose:

```typescript
enum Status {
    Pending = "pending",
    Running = "running",
    Complete = "complete"
}
```

Then:

```typescript
const labels:
    Record<
        Status,
        string
    > = {
        [Status.Pending]:
            "Waiting",

        [Status.Running]:
            "Processing",

        [Status.Complete]:
            "Finished"
    };
```

If another enum value is added, TypeScript can help detect that the mapping needs updating.

---

# 24. `Record` Use Cases

Common uses:

```text
Lookup tables

Configuration maps

Role permissions

Status labels

Feature flags

Translation maps

Entity dictionaries

React component mappings
```

---

# 25. `Exclude<T, U>`

`Exclude<T, U>` removes members from a union.

Example:

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

Then:

```typescript
type FinishedStatus =
    Exclude<
        Status,
        "idle" | "loading"
    >;
```

Result:

```typescript
type FinishedStatus =
    "success" | "error";
```

---

# 26. How `Exclude` Works Conceptually

Given:

```text
T
=
A | B | C | D
```

and:

```text
U
=
B | D
```

Then:

```typescript
Exclude<T, U>
```

produces:

```text
A | C
```

Think:

```text
Union
-
Matching Members
=
Remaining Union
```

---

# 27. `Exclude` Example

```typescript
type Permission =
    "read"
    | "write"
    | "delete"
    | "admin";
```

Create normal permissions:

```typescript
type UserPermission =
    Exclude<
        Permission,
        "admin"
    >;
```

Result:

```text
"read"
|
"write"
|
"delete"
```

---

# 28. `Extract<T, U>`

`Extract<T, U>` does the opposite of `Exclude`.

It keeps only union members compatible with `U`.

Example:

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

Then:

```typescript
type ActiveStatus =
    Extract<
        Status,
        "loading"
        | "success"
    >;
```

Result:

```text
"loading"
|
"success"
```

---

# 29. `Exclude` vs `Extract`

Given:

```text
T
=
A | B | C
```

and:

```text
U
=
B | C
```

Then:

```typescript
Exclude<T, U>
```

produces:

```text
A
```

While:

```typescript
Extract<T, U>
```

produces:

```text
B | C
```

Mental model:

```text
Exclude
→ remove matches

Extract
→ keep matches
```

---

# 30. `Extract` by Type

Suppose:

```typescript
type Value =
    string
    | number
    | boolean
    | null;
```

Then:

```typescript
type PrimitiveText =
    Extract<
        Value,
        string | number
    >;
```

Result:

```text
string | number
```

---

# 31. `NonNullable<T>`

`NonNullable<T>` removes:

```text
null
undefined
```

from a type.

Example:

```typescript
type User =
    {
        id: number;
    }
    | null
    | undefined;
```

Then:

```typescript
type ExistingUser =
    NonNullable<User>;
```

Result:

```typescript
{
    id: number;
}
```

---

# 32. `NonNullable<T>` Example

```typescript
type Name =
    string
    | null
    | undefined;
```

Then:

```typescript
type ValidName =
    NonNullable<Name>;
```

Result:

```text
string
```

---

# 33. `NonNullable<T>` Use Cases

Useful when:

```text
A value was previously nullable

Validation already occurred

A helper type needs the non-null version

Working with derived state

Transforming API/domain types
```

Remember that creating:

```typescript
NonNullable<T>
```

does not perform runtime validation.

---

# 34. `Parameters<T>`

`Parameters<T>` extracts the parameter types of a function as a tuple.

Example:

```typescript
function createUser(
    name: string,
    age: number,
    active: boolean
) {
    // ...
}
```

Then:

```typescript
type CreateUserParams =
    Parameters<
        typeof createUser
    >;
```

Result:

```typescript
[
    name: string,
    age: number,
    active: boolean
]
```

---

# 35. Why `typeof` Is Used with `Parameters`

This:

```typescript
createUser
```

is a runtime function value.

Utility types operate on types.

So:

```typescript
typeof createUser
```

gives us the function's type.

Then:

```typescript
Parameters<
    typeof createUser
>
```

extracts its parameters.

We will cover TypeScript's `typeof` operator separately in more depth.

---

# 36. Using Extracted Parameters

```typescript
type Params =
    Parameters<
        typeof createUser
    >;
```

Then:

```typescript
const args: Params = [
    "Alice",
    25,
    true
];
```

And:

```typescript
createUser(...args);
```

---

# 37. Parameter at Specific Position

Because `Parameters<T>` returns a tuple:

```typescript
type Params =
    Parameters<
        typeof createUser
    >;
```

we can access:

```typescript
type FirstParameter =
    Params[0];
```

Result:

```text
string
```

And:

```typescript
type SecondParameter =
    Params[1];
```

Result:

```text
number
```

---

# 38. `ReturnType<T>`

`ReturnType<T>` extracts the return type of a function.

Example:

```typescript
function createUser() {
    return {
        id: 1,
        name: "Alice",
        active: true
    };
}
```

Then:

```typescript
type User =
    ReturnType<
        typeof createUser
    >;
```

TypeScript derives:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

---

# 39. Why `ReturnType` Is Useful

Suppose a function's return structure changes.

Instead of manually maintaining:

```typescript
interface UserResult {
    // duplicate structure
}
```

we can derive:

```typescript
type UserResult =
    ReturnType<
        typeof createUser
    >;
```

Now the type follows the function automatically.

---

# 40. `ReturnType` with Async Functions

Consider:

```typescript
async function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Then:

```typescript
type Result =
    ReturnType<
        typeof getUser
    >;
```

Result:

```text
Promise<{
    id: number;
    name: string;
}>
```

Because the function returns a Promise.

To get the resolved value, we can combine it with `Awaited<T>`.

---

# 41. `Awaited<T>`

`Awaited<T>` determines the type obtained after awaiting a Promise-like value.

Example:

```typescript
type Result =
    Awaited<
        Promise<string>
    >;
```

Result:

```text
string
```

---

# 42. `Awaited<T>` with Async Function

```typescript
async function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

First:

```typescript
type PromiseResult =
    ReturnType<
        typeof getUser
    >;
```

Result:

```text
Promise<UserShape>
```

Then:

```typescript
type User =
    Awaited<
        ReturnType<
            typeof getUser
        >
    >;
```

Result:

```typescript
{
    id: number;
    name: string;
}
```

This pattern is useful when deriving types from async functions.

---

# 43. `Awaited<T>` Handles Nested Promises

Conceptually:

```typescript
type Value =
    Awaited<
        Promise<
            Promise<string>
        >
    >;
```

results in:

```text
string
```

It models JavaScript's `await` behavior.

---

# 44. `ConstructorParameters<T>`

`ConstructorParameters<T>` extracts constructor parameter types as a tuple.

Example:

```typescript
class User {
    constructor(
        public name: string,
        public age: number
    ) {}
}
```

Then:

```typescript
type UserConstructorArgs =
    ConstructorParameters<
        typeof User
    >;
```

Result:

```typescript
[
    name: string,
    age: number
]
```

---

# 45. Using Constructor Parameters

```typescript
type Args =
    ConstructorParameters<
        typeof User
    >;
```

Then:

```typescript
const args: Args = [
    "Alice",
    25
];
```

And:

```typescript
const user =
    new User(...args);
```

---

# 46. `InstanceType<T>`

`InstanceType<T>` extracts the instance type produced by a constructor.

Example:

```typescript
class User {
    name = "Alice";
}
```

Then:

```typescript
type UserInstance =
    InstanceType<
        typeof User
    >;
```

`UserInstance` represents:

```text
An instance created by
new User()
```

---

# 47. Constructor vs Instance

This distinction is important.

```typescript
typeof User
```

represents:

```text
The class constructor itself.
```

While:

```typescript
InstanceType<
    typeof User
>
```

represents:

```text
An object created from
that constructor.
```

Example:

```typescript
const Constructor:
    typeof User =
    User;
```

versus:

```typescript
const instance:
    InstanceType<
        typeof User
    > =
    new User();
```

---

# 48. `ThisParameterType<T>`

TypeScript can extract an explicit `this` parameter type from a function.

Example:

```typescript
function greet(
    this: {
        name: string;
    }
) {
    return this.name;
}
```

Then:

```typescript
type Context =
    ThisParameterType<
        typeof greet
    >;
```

Result:

```typescript
{
    name: string;
}
```

This is less common in normal React applications but useful when working with function contexts and libraries.

---

# 49. `OmitThisParameter<T>`

Given:

```typescript
function greet(
    this: User,
    message: string
) {
    // ...
}
```

we can derive:

```typescript
type BoundGreet =
    OmitThisParameter<
        typeof greet
    >;
```

which removes the explicit `this` parameter from the function type.

Useful for bound functions and APIs dealing with explicit `this`.

---

# 50. `Uppercase<T>`

TypeScript provides intrinsic string manipulation utility types.

```typescript
type Status =
    Uppercase<"active">;
```

Result:

```text
"ACTIVE"
```

---

# 51. `Lowercase<T>`

```typescript
type Status =
    Lowercase<"ACTIVE">;
```

Result:

```text
"active"
```

---

# 52. `Capitalize<T>`

```typescript
type Name =
    Capitalize<"alice">;
```

Result:

```text
"Alice"
```

---

# 53. `Uncapitalize<T>`

```typescript
type Name =
    Uncapitalize<"Alice">;
```

Result:

```text
"alice"
```

---

# 54. String Utilities Work at Type Level

Important:

```typescript
type Upper =
    Uppercase<"hello">;
```

creates:

```text
"HELLO"
```

as a **type**.

It does not execute:

```javascript
"hello".toUpperCase();
```

at runtime.

These utilities manipulate string literal types.

---

# 55. Combining Utility Types

Utility types can be composed.

Example:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
}
```

Suppose we need:

```text
name
email
```

but both optional.

We can write:

```typescript
type UpdateProfile =
    Partial<
        Pick<
            User,
            "name"
            | "email"
        >
    >;
```

Result:

```typescript
type UpdateProfile = {
    name?: string;
    email?: string;
};
```

---

# 56. Read Utility Types Inside-Out

When you see:

```typescript
Readonly<
    Partial<
        Pick<
            User,
            "name"
            | "email"
        >
    >
>
```

read from the inside:

```text
1. User

2. Pick name and email

3. Make them optional

4. Make them readonly
```

Result:

```typescript
{
    readonly name?: string;
    readonly email?: string;
}
```

---

# 57. Create DTO Pattern

Suppose:

```typescript
interface User {
    id: string;
    name: string;
    email: string;
    createdAt: Date;
    updatedAt: Date;
}
```

The client should not send:

```text
id
createdAt
updatedAt
```

when creating a user.

So:

```typescript
type CreateUserDto =
    Omit<
        User,
        "id"
        | "createdAt"
        | "updatedAt"
    >;
```

Result:

```typescript
{
    name: string;
    email: string;
}
```

---

# 58. Update DTO Pattern

We can derive:

```typescript
type UpdateUserDto =
    Partial<
        CreateUserDto
    >;
```

Result:

```typescript
{
    name?: string;
    email?: string;
}
```

Now:

```text
User
    │
    ▼
Omit generated fields
    │
    ▼
CreateUserDto
    │
    ▼
Partial
    │
    ▼
UpdateUserDto
```

This is a common pattern.

---

# 59. Public User Pattern

Suppose:

```typescript
interface User {
    id: string;
    name: string;
    email: string;
    passwordHash: string;
}
```

Do not expose:

```text
passwordHash
```

to the client.

Create:

```typescript
type PublicUser =
    Omit<
        User,
        "passwordHash"
    >;
```

---

# 60. Important API Design Warning

Although:

```typescript
Omit<
    User,
    "passwordHash"
>
```

creates a type without `passwordHash`, it does **not remove the property from an object at runtime**.

This:

```typescript
const user = {
    id: "1",
    name: "Alice",
    passwordHash: "secret"
};
```

does not magically lose `passwordHash` because you created an `Omit` type.

Utility types:

```text
Transform types.
```

They do not:

```text
Transform runtime objects.
```

You still need runtime serialization/mapping logic.

---

# 61. React Props Pattern

Suppose:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    passwordHash: string;
    createdAt: Date;
}
```

A user card may only need:

```typescript
type UserCardProps =
    Pick<
        User,
        "name"
        | "email"
    >;
```

Then:

```typescript
function UserCard(
    props: UserCardProps
) {
    return (
        <div>
            <h2>
                {props.name}
            </h2>

            <p>
                {props.email}
            </p>
        </div>
    );
}
```

The component only depends on the fields it actually needs.

---

# 62. React State Mapping Pattern

Suppose:

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

We can create:

```typescript
const labels:
    Record<
        Status,
        string
    > = {
        idle:
            "Ready",

        loading:
            "Loading...",

        success:
            "Complete",

        error:
            "Failed"
    };
```

If another status is added:

```text
cancelled
```

TypeScript can tell us the mapping needs to be updated.

---

# 63. Feature Flag Pattern

```typescript
type Feature =
    "darkMode"
    | "newDashboard"
    | "betaSearch";
```

Then:

```typescript
type FeatureFlags =
    Record<
        Feature,
        boolean
    >;
```

Example:

```typescript
const features:
    FeatureFlags = {
        darkMode: true,
        newDashboard: false,
        betaSearch: true
    };
```

---

# 64. Permission Mapping Pattern

```typescript
type Role =
    "admin"
    | "editor"
    | "viewer";

type Permission =
    "read"
    | "write"
    | "delete";
```

Then:

```typescript
const permissions:
    Record<
        Role,
        Permission[]
    > = {
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

---

# 65. Entity Dictionary Pattern

Suppose:

```typescript
interface User {
    id: string;
    name: string;
}
```

Instead of:

```typescript
User[]
```

we might store:

```typescript
Record<
    string,
    User
>
```

Example:

```typescript
const users:
    Record<
        string,
        User
    > = {
        "user-1": {
            id: "user-1",
            name: "Alice"
        },

        "user-2": {
            id: "user-2",
            name: "Bob"
        }
    };
```

Useful for normalized state and lookup tables.

---

# 66. Utility Types with Generics

We can create reusable helpers:

```typescript
type UpdateDto<T> =
    Partial<T>;
```

Then:

```typescript
type UserUpdate =
    UpdateDto<User>;

type ProductUpdate =
    UpdateDto<Product>;
```

But remember:

```typescript
Partial<T>
```

already exists, so this alias is useful only if it adds domain meaning.

---

# 67. Generic Create Model

Suppose every entity has:

```typescript
interface BaseEntity {
    id: string;
    createdAt: Date;
    updatedAt: Date;
}
```

We can define:

```typescript
type CreateModel<T> =
    Omit<
        T,
        keyof BaseEntity
    >;
```

Then:

```typescript
interface User
    extends BaseEntity {

    name: string;
    email: string;
}
```

Now:

```typescript
type CreateUser =
    CreateModel<User>;
```

contains only:

```typescript
{
    name: string;
    email: string;
}
```

---

# 68. Generic Update Model

Build on the previous helper:

```typescript
type UpdateModel<T> =
    Partial<
        CreateModel<T>
    >;
```

Then:

```typescript
type UpdateUser =
    UpdateModel<User>;
```

Result:

```typescript
{
    name?: string;
    email?: string;
}
```

This demonstrates how utility types can compose into reusable domain transformations.

---

# 69. `Partial` vs Optional Properties

These:

```typescript
interface User {
    name?: string;
    email?: string;
}
```

and:

```typescript
type User =
    Partial<{
        name: string;
        email: string;
    }>;
```

can produce similar shapes.

Difference:

```text
Optional properties
→ define the type directly.

Partial<T>
→ derive an optional version
   from another type.
```

Use `Partial` when there is an existing source type worth deriving from.

---

# 70. `Readonly` vs `as const`

These are related but different.

```typescript
Readonly<User>
```

transforms an object type.

`as const` works on expressions:

```typescript
const config = {
    mode: "dark"
} as const;
```

and produces narrow literal/readonly inference.

We will cover `as const` separately.

---

# 71. `Pick` Preserves Property Modifiers

Suppose:

```typescript
interface User {
    readonly id: number;
    name?: string;
}
```

Then:

```typescript
type Selected =
    Pick<
        User,
        "id" | "name"
    >;
```

preserves:

```typescript
{
    readonly id: number;
    name?: string;
}
```

`Pick` selects the properties with their existing modifiers.

---

# 72. `Omit` Preserves Remaining Property Modifiers

Likewise:

```typescript
type WithoutName =
    Omit<
        User,
        "name"
    >;
```

keeps the modifiers of the properties that remain.

---

# 73. `Required` Removes Optional Modifiers

Given:

```typescript
interface User {
    id?: number;
    name?: string;
}
```

Then:

```typescript
Required<User>
```

removes:

```text
?
```

from each property.

---

# 74. `Readonly` Adds Readonly Modifiers

Given:

```typescript
interface User {
    id: number;
    name: string;
}
```

Then:

```typescript
Readonly<User>
```

adds:

```text
readonly
```

to each property.

These transformations become easier to understand once we learn **Mapped Types**.

---

# 75. Simplified Idea Behind `Partial`

Conceptually, `Partial` behaves like:

```typescript
type MyPartial<T> = {
    [K in keyof T]?:
        T[K];
};
```

Do not worry if this looks unfamiliar.

It uses:

```text
Generics

keyof

Mapped Types

Indexed Access Types
```

We will study those concepts separately.

---

# 76. Simplified Idea Behind `Readonly`

Conceptually:

```typescript
type MyReadonly<T> = {
    readonly
    [K in keyof T]:
        T[K];
};
```

Meaning:

```text
For every property K in T:

Keep its value type

Add readonly
```

---

# 77. Simplified Idea Behind `Required`

Conceptually:

```typescript
type MyRequired<T> = {
    [K in keyof T]-?:
        T[K];
};
```

The:

```text
-?
```

removes the optional modifier.

We will understand this fully when learning mapped types.

---

# 78. Simplified Idea Behind `Pick`

Conceptually:

```typescript
type MyPick<
    T,
    K extends keyof T
> = {
    [P in K]:
        T[P];
};
```

Meaning:

```text
Take selected keys K

and copy their types
from T.
```

---

# 79. Utility Types Are Mostly Type Transformations

Think:

```text
User
 │
 ├── Partial<User>
 │       ↓
 │    Optional User
 │
 ├── Required<User>
 │       ↓
 │    Required User
 │
 ├── Readonly<User>
 │       ↓
 │    Immutable View
 │
 ├── Pick<User, K>
 │       ↓
 │    Selected Fields
 │
 └── Omit<User, K>
         ↓
      Removed Fields
```

For unions:

```text
Union
 │
 ├── Exclude<T, U>
 │       ↓
 │    Remove Matches
 │
 └── Extract<T, U>
         ↓
      Keep Matches
```

For functions:

```text
Function
 │
 ├── Parameters<T>
 │       ↓
 │    Parameter Tuple
 │
 └── ReturnType<T>
         ↓
      Return Type
```

For async values:

```text
Promise<T>
    │
    ▼
Awaited<Promise<T>>
    │
    ▼
T
```

---

# 80. Common Mistake — Thinking `Partial` Is Deep

Incorrect assumption:

```text
Partial<User>
```

makes every nested property optional.

It does not.

`Partial` is shallow.

If deep behavior is needed, you must create a recursive type or use an appropriate library type.

---

# 81. Common Mistake — Thinking `Readonly` Is Deep

`Readonly<T>` only affects the top-level properties.

Nested mutable objects can remain mutable.

Also remember:

```text
Readonly<T>
```

does not freeze the object at runtime.

---

# 82. Common Mistake — Using `Partial` Everywhere

Avoid:

```typescript
function createUser(
    user: Partial<User>
) {
    // ...
}
```

if creating a user actually requires:

```text
name
email
password
```

`Partial<User>` would allow:

```typescript
createUser({});
```

which may violate the domain rules.

Create a precise type instead:

```typescript
type CreateUserDto =
    Pick<
        User,
        "name"
        | "email"
    >
    & {
        password: string;
    };
```

Types should model the actual domain rules.

---

# 83. Common Mistake — Exposing Sensitive Fields with `Omit`

This:

```typescript
type PublicUser =
    Omit<
        User,
        "passwordHash"
    >;
```

helps type your public representation.

But it does not sanitize runtime data.

Never rely on TypeScript types as a security mechanism.

You must actually remove sensitive fields from the runtime object before sending it.

---

# 84. Common Mistake — Using `Record<string, T>` When Keys Are Known

Suppose only these statuses exist:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

Avoid:

```typescript
Record<
    string,
    string
>
```

if every status must be represented.

Prefer:

```typescript
Record<
    Status,
    string
>
```

Now TypeScript can enforce completeness.

---

# 85. Common Mistake — Duplicating Types

Avoid:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}

interface UserPreview {
    id: number;
    name: string;
}
```

if `UserPreview` is intentionally derived from `User`.

Prefer:

```typescript
type UserPreview =
    Pick<
        User,
        "id" | "name"
    >;
```

Now the relationship is explicit.

---

# 86. Common Mistake — Over-Composing Utilities

Technically possible:

```typescript
Readonly<
    Partial<
        Omit<
            Pick<
                User,
                keyof User
            >,
            "password"
        >
    >
>
```

But if the type becomes difficult to understand, simplify it.

Type safety should improve maintainability, not create type puzzles.

---

# 87. Common Mistake — Deriving Types That Should Be Independent

Not every similar type should be derived.

For example:

```text
Database User

API User

UI User
```

may look similar today but represent different architectural contracts.

Blindly deriving everything from one giant model can create tight coupling.

Utility types are useful when the types are **conceptually related**, not merely structurally similar.

---

# 88. Common Mistake — Assuming `ReturnType` Executes a Function

```typescript
ReturnType<
    typeof getUser
>
```

does not execute `getUser`.

It analyzes its type at compile time.

Same for:

```typescript
Parameters<T>

ConstructorParameters<T>

InstanceType<T>
```

These are type-level operations.

---

# 89. Common Mistake — Forgetting Async Return Is Promise

Given:

```typescript
async function getUser() {
    return {
        id: 1
    };
}
```

This:

```typescript
ReturnType<
    typeof getUser
>
```

is:

```text
Promise<{ id: number }>
```

not:

```text
{ id: number }
```

Use:

```typescript
Awaited<
    ReturnType<
        typeof getUser
    >
>
```

for the resolved type.

---

# 90. Utility Types Do Not Generate Runtime Code

All of these:

```typescript
Partial<T>

Required<T>

Readonly<T>

Pick<T, K>

Omit<T, K>

Record<K, T>

Exclude<T, U>

Extract<T, U>

NonNullable<T>

Parameters<T>

ReturnType<T>

Awaited<T>
```

operate at compile time.

They disappear from emitted JavaScript.

---

# 91. Interview Question — What Are Utility Types?

Utility types are built-in TypeScript generic types that transform existing types into new types.

Examples:

```text
Partial

Required

Readonly

Pick

Omit

Record

Exclude

Extract
```

They reduce duplicated type definitions and express relationships between types.

---

# 92. Interview Question — `Partial` vs `Required`?

```typescript
Partial<T>
```

makes properties optional.

```typescript
Required<T>
```

makes properties required.

Example:

```typescript
type A =
    Partial<User>;

type B =
    Required<User>;
```

---

# 93. Interview Question — `Pick` vs `Omit`?

```typescript
Pick<T, K>
```

keeps selected properties.

```typescript
Omit<T, K>
```

removes selected properties.

Example:

```typescript
Pick<
    User,
    "id" | "name"
>
```

versus:

```typescript
Omit<
    User,
    "password"
>
```

---

# 94. Interview Question — What Does `Record<K, T>` Do?

It creates an object type where keys are `K` and values are `T`.

Example:

```typescript
type Status =
    "success"
    | "error";

type Labels =
    Record<
        Status,
        string
    >;
```

Equivalent conceptually:

```typescript
{
    success: string;
    error: string;
}
```

---

# 95. Interview Question — `Exclude` vs `Extract`?

Given a union:

```typescript
type Value =
    string
    | number
    | boolean;
```

Then:

```typescript
Exclude<
    Value,
    string
>
```

produces:

```text
number | boolean
```

While:

```typescript
Extract<
    Value,
    string | number
>
```

produces:

```text
string | number
```

---

# 96. Interview Question — What Does `NonNullable` Do?

It removes:

```text
null
undefined
```

from a type.

Example:

```typescript
NonNullable<
    string
    | null
    | undefined
>
```

becomes:

```text
string
```

---

# 97. Interview Question — What Does `Parameters<T>` Do?

It extracts function parameters into a tuple.

Example:

```typescript
function test(
    name: string,
    age: number
) {}
```

Then:

```typescript
Parameters<
    typeof test
>
```

becomes:

```typescript
[
    string,
    number
]
```

---

# 98. Interview Question — What Does `ReturnType<T>` Do?

It extracts a function's return type.

Example:

```typescript
function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Then:

```typescript
type User =
    ReturnType<
        typeof getUser
    >;
```

derives the returned object type.

---

# 99. Interview Question — What Does `Awaited<T>` Do?

`Awaited<T>` determines the value type obtained after awaiting a Promise-like type.

Example:

```typescript
Awaited<
    Promise<User>
>
```

becomes:

```text
User
```

A common pattern is:

```typescript
Awaited<
    ReturnType<
        typeof asyncFunction
    >
>
```

---

# 100. Interview Question — Are Utility Types Runtime Features?

No.

Utility types operate only in TypeScript's type system.

They do not:

```text
Modify objects

Remove properties

Freeze objects

Validate data

Execute functions
```

at runtime.

---

# 101. Interview Question — Are `Partial` and `Readonly` Deep?

No.

Both are shallow by default.

```typescript
Partial<T>
```

makes top-level properties optional.

```typescript
Readonly<T>
```

makes top-level properties readonly.

Nested structures are not recursively transformed.

---

# 102. Interview Question — Why Derive Types?

Deriving types helps:

```text
Reduce duplication

Keep related types synchronized

Express relationships

Improve refactoring

Prevent model drift
```

But only derive types when the models are conceptually related.

---

# 103. Cheat Sheet

```text
PARTIAL
================================

Partial<T>

All properties optional.


REQUIRED
================================

Required<T>

All properties required.


READONLY
================================

Readonly<T>

All properties readonly.


PICK
================================

Pick<T, K>

Keep selected properties.


OMIT
================================

Omit<T, K>

Remove selected properties.


RECORD
================================

Record<K, T>

Keys K
→ Values T


EXCLUDE
================================

Exclude<T, U>

Remove matching union members.


EXTRACT
================================

Extract<T, U>

Keep matching union members.


NONNULLABLE
================================

NonNullable<T>

Remove:

null
undefined


PARAMETERS
================================

Parameters<T>

Function
→ parameter tuple


RETURN TYPE
================================

ReturnType<T>

Function
→ return type


CONSTRUCTOR PARAMETERS
================================

ConstructorParameters<T>

Constructor
→ parameter tuple


INSTANCE TYPE
================================

InstanceType<T>

Constructor
→ instance type


AWAITED
================================

Awaited<T>

Promise<T>
→ T


STRING UTILITIES
================================

Uppercase<T>

Lowercase<T>

Capitalize<T>

Uncapitalize<T>


COMMON COMPOSITION
================================

Partial<
    Pick<
        User,
        "name" | "email"
    >
>


CREATE DTO
================================

Omit<
    User,
    "id"
    | "createdAt"
    | "updatedAt"
>


UPDATE DTO
================================

Partial<CreateUserDto>


PUBLIC MODEL
================================

Omit<
    User,
    "passwordHash"
>


ASYNC RESULT
================================

Awaited<
    ReturnType<
        typeof getUser
    >
>


CORE RULE
================================

Utility types transform types.

They do not transform
runtime values.
```

---

# 104. Important Rules to Remember

```text
1. Utility types transform existing types.

2. Utility types reduce type duplication.

3. Utility types are built using TypeScript's type system.

4. Partial<T> makes properties optional.

5. Partial<T> is shallow.

6. Required<T> makes properties required.

7. Required<T> is shallow.

8. Readonly<T> makes properties readonly.

9. Readonly<T> is shallow.

10. Readonly<T> does not freeze runtime objects.

11. Pick<T, K> selects properties.

12. K must represent valid keys of T.

13. Pick preserves property modifiers.

14. Omit<T, K> removes properties.

15. Omit preserves modifiers of remaining properties.

16. Pick is useful when few fields are needed.

17. Omit is useful when few fields should be removed.

18. Record<K, T> creates a key-value object type.

19. Literal unions make Record exhaustive.

20. Record is useful for lookup tables.

21. Record is useful for role mappings.

22. Record is useful for status mappings.

23. Record is useful for feature flags.

24. Exclude<T, U> removes union members.

25. Extract<T, U> keeps matching union members.

26. Exclude and Extract operate primarily on unions.

27. NonNullable<T> removes null.

28. NonNullable<T> removes undefined.

29. NonNullable does not perform runtime validation.

30. Parameters<T> extracts function parameters.

31. Parameters returns a tuple type.

32. ReturnType<T> extracts a function return type.

33. typeof function is commonly used with ReturnType.

34. typeof function is commonly used with Parameters.

35. ReturnType of an async function is a Promise type.

36. Awaited<T> unwraps Promise-like types.

37. Awaited can be combined with ReturnType.

38. ConstructorParameters<T> extracts constructor parameters.

39. ConstructorParameters returns a tuple.

40. InstanceType<T> extracts the constructed instance type.

41. typeof Class represents the constructor side.

42. InstanceType<typeof Class> represents the instance side.

43. Uppercase transforms string literal types.

44. Lowercase transforms string literal types.

45. Capitalize transforms string literal types.

46. Uncapitalize transforms string literal types.

47. String utility types operate at type level.

48. Utility types can be composed.

49. Read nested utility types inside-out.

50. Partial<Pick<T, K>> is useful for partial updates of selected fields.

51. Omit can derive create DTOs.

52. Partial can derive update DTOs.

53. Omit can derive public models.

54. Pick can derive component props.

55. Record can model normalized state.

56. Record can enforce complete mappings.

57. Utility types are generic types.

58. Utility types combine naturally with generics.

59. Utility types combine with keyof.

60. Utility types combine with unions.

61. Utility types combine with indexed access types.

62. Utility types combine with mapped types.

63. Many utility types are implemented using mapped types.

64. Some utility types use conditional types.

65. Utility types are compile-time only.

66. Utility types disappear from emitted JavaScript.

67. Omit does not delete runtime properties.

68. Readonly does not call Object.freeze.

69. Partial does not modify runtime objects.

70. NonNullable does not check runtime values.

71. ReturnType does not execute functions.

72. Parameters does not inspect functions at runtime.

73. Do not use Partial when fields are actually required.

74. Types should model domain requirements.

75. Do not expose sensitive runtime data merely because its type uses Omit.

76. Runtime sanitization remains necessary.

77. Prefer known key unions over Record<string, T> when keys are finite.

78. Avoid duplicating intentionally derived models.

79. Do not derive unrelated models merely because they currently look similar.

80. Database, API, and UI models can represent different contracts.

81. Overusing utility types can make types difficult to read.

82. Create named intermediate types when compositions become complex.

83. Utility types should improve maintainability.

84. Derived types automatically reflect many changes to source types.

85. This can make refactoring safer.

86. But derivation also creates coupling.

87. Use derivation when that coupling is intentional.

88. Partial and Required change optional modifiers.

89. Readonly changes readonly modifiers.

90. Pick selects keys.

91. Omit excludes keys.

92. Record constructs key-value mappings.

93. Exclude filters unions by removing matches.

94. Extract filters unions by keeping matches.

95. NonNullable filters nullish members.

96. Parameters derives argument tuples.

97. ReturnType derives return values.

98. Awaited derives resolved async values.

99. ConstructorParameters derives constructor argument tuples.

100. InstanceType derives class instance types.

101. Utility types are a major part of real-world TypeScript.

102. React code frequently uses Pick, Omit, Partial, Record, and ReturnType.

103. Backend DTOs frequently use Pick, Omit, Partial, and Required.

104. Configuration models frequently use Partial and Required.

105. Permission systems frequently use Record.

106. State mappings frequently use Record.

107. Async helpers frequently use Awaited and ReturnType.

108. Utility types should describe relationships between existing contracts.

109. Compile-time transformations are not runtime transformations.

110. Always separate type safety from runtime validation and security.
```

---

# 105. Final Mental Model

For object transformations:

```text
                    User
                      │
       ┌──────────────┼───────────────┐
       │              │               │
       ▼              ▼               ▼

 Partial<User>   Readonly<User>   Required<User>

       │              │               │
       ▼              ▼               ▼

   Optional         Readonly        Required
   Properties       Properties      Properties
```

For selecting properties:

```text
                User
                  │
          ┌───────┴───────┐
          ▼               ▼

     Pick<User,K>      Omit<User,K>
          │               │
          ▼               ▼

      Keep K           Remove K
```

For unions:

```text
             A | B | C | D
                  │
        ┌─────────┴─────────┐
        ▼                   ▼

   Exclude<T,U>        Extract<T,U>
        │                   │
        ▼                   ▼

  Remove Matches        Keep Matches
```

For functions:

```text
             Function Type
                   │
          ┌────────┴────────┐
          ▼                 ▼

    Parameters<T>      ReturnType<T>
          │                 │
          ▼                 ▼

    Argument Tuple       Result Type
```

For async functions:

```text
async function getUser()
          │
          ▼
typeof getUser
          │
          ▼
ReturnType<typeof getUser>
          │
          ▼
Promise<User>
          │
          ▼
Awaited<...>
          │
          ▼
User
```

The main principle is:

```text
Do not redefine a type
when the new type is
intentionally a transformation
of an existing type.
```

Instead of:

```typescript
interface UpdateUser {
    name?: string;
    email?: string;
}
```

when those fields intentionally come from `User`, we can express that relationship:

```typescript
type UpdateUser =
    Partial<
        Pick<
            User,
            "name" | "email"
        >
    >;
```

But remember:

```text
Utility Types
     │
     ▼
Type Transformation
     │
     ▼
Compile-Time Safety

NOT

Runtime Transformation
```
