# TypeScript Types

TypeScript adds a **static type system** on top of JavaScript.

JavaScript already has runtime values such as:

```text
string
number
boolean
bigint
symbol
undefined
null
object
```

We will **not repeat how those JavaScript values work**.

Instead, this chapter focuses on how TypeScript models values and on types that are especially important to TypeScript:

```text
any
unknown
never
void
null
undefined
object
arrays
tuples
literal types
readonly
```

---

# 1. Type System Overview

A simplified view:

```text
TypeScript Types
│
├── Primitive Types
│   ├── string
│   ├── number
│   ├── boolean
│   ├── bigint
│   ├── symbol
│   ├── null
│   └── undefined
│
├── Special Types
│   ├── any
│   ├── unknown
│   ├── never
│   └── void
│
├── Object Types
│   ├── object
│   ├── Object Shapes
│   ├── Arrays
│   └── Tuples
│
└── Type Modifiers / Specialized Types
    ├── Literal Types
    └── readonly
```

Later sections will build more complex types from these using:

```text
Union Types
Intersection Types
Type Aliases
Interfaces
Generics
Mapped Types
Conditional Types
```

---

# 2. Primitive Type Annotations

You already know these values from JavaScript.

TypeScript lets us describe them explicitly.

```typescript
let username: string = "Alice";

let age: number = 25;

let active: boolean = true;

let largeNumber: bigint = 123456789n;

let id: symbol = Symbol("id");
```

Usually inference is preferred when obvious:

```typescript
const username = "Alice";

const age = 25;

const active = true;
```

---

# 3. `any`

`any` is TypeScript's **escape hatch**.

```typescript
let value: any;
```

It effectively tells TypeScript:

```text
Do not type-check this value normally.
```

Example:

```typescript
let value: any = "Hello";

value = 100;

value = true;

value = {
    name: "Alice"
};
```

All are allowed.

---

# 4. Operations on `any`

Consider:

```typescript
let value: any = "Hello";

value.toUpperCase();

value.foo.bar.baz();

value();

value.doesNotExist();
```

TypeScript allows these operations because `value` is `any`.

That does **not** mean they are safe.

For example:

```typescript
let value: any = 100;

value.toUpperCase();
```

TypeScript may allow it.

Runtime:

```text
Error
```

because numbers do not have `toUpperCase()`.

---

# 5. Why `any` Is Dangerous

Normally TypeScript protects us:

```typescript
const age = 25;

age.toUpperCase();
```

TypeScript reports an error.

But:

```typescript
const age: any = 25;

age.toUpperCase();
```

can pass type checking.

Conceptually:

```text
Normal Type

Value
  │
  ▼
Type Checker
  │
  ▼
Safety Check


any

Value
  │
  ▼
Checking largely bypassed
```

---

# 6. `any` Can Spread

One dangerous characteristic of `any` is that it can contaminate surrounding code.

```typescript
function getData(): any {
    return {
        name: "Alice"
    };
}

const user = getData();
```

Now:

```typescript
user.name;

user.age;

user.whatever;

user.foo.bar;
```

can all avoid useful checking.

The original `any` has caused downstream code to lose type safety.

---

# 7. Avoid Explicit `any`

Avoid:

```typescript
function processData(
    data: any
) {
    data.doSomething();
}
```

when the value is genuinely unknown.

Prefer:

```typescript
function processData(
    data: unknown
) {
    // Must verify data before using it.
}
```

---

# 8. When `any` May Be Necessary

There are situations where `any` can appear:

```text
Migrating old JavaScript

Legacy libraries

Poorly typed third-party packages

Temporary migration code

Extremely dynamic interoperability
```

But treat it as:

```text
Escape hatch
```

not:

```text
Default TypeScript type
```

---

# 9. Implicit `any`

Consider:

```typescript
function greet(name) {
    return `Hello ${name}`;
}
```

If TypeScript cannot determine `name` and `noImplicitAny` is enabled, it reports an error because the parameter would implicitly become:

```typescript
any
```

Properly type it:

```typescript
function greet(
    name: string
): string {
    return `Hello ${name}`;
}
```

---

# 10. `noImplicitAny`

The compiler option:

```json
{
    "compilerOptions": {
        "noImplicitAny": true
    }
}
```

helps prevent TypeScript from silently introducing `any` when it cannot infer a type.

It is included in the broader strict-mode family of checks.

For serious projects, avoiding accidental `any` is important.

---

# 11. `unknown`

`unknown` means:

```text
There is a value here,
but we do not yet know its type.
```

Example:

```typescript
let value: unknown;
```

You can assign many values to it:

```typescript
value = "Hello";

value = 100;

value = true;

value = {
    name: "Alice"
};
```

So far, this looks similar to `any`.

The difference appears when we try to **use** the value.

---

# 12. `unknown` Prevents Unsafe Operations

```typescript
let value: unknown =
    "Hello";
```

This is unsafe:

```typescript
value.toUpperCase();
```

TypeScript does not allow it because:

```text
value
→ unknown
```

It could be:

```text
string
number
object
null
boolean
anything else
```

We must first determine what it actually is.

---

# 13. Narrowing `unknown`

```typescript
let value: unknown =
    "Hello";

if (
    typeof value === "string"
) {
    console.log(
        value.toUpperCase()
    );
}
```

Before:

```text
value
→ unknown
```

Inside the check:

```text
value
→ string
```

This process is called:

```text
Type Narrowing
```

We will study narrowing in detail later.

---

# 14. `any` vs `unknown`

`any`:

```typescript
let value: any = 100;

value.toUpperCase();
```

TypeScript allows it.

`unknown`:

```typescript
let value: unknown = 100;

value.toUpperCase();
```

TypeScript rejects it.

You must verify:

```typescript
if (
    typeof value === "string"
) {
    value.toUpperCase();
}
```

---

# 15. `any` vs `unknown` Mental Model

```text
any
================================

"I don't know the type,
and TypeScript should mostly
stop checking me."


unknown
================================

"I don't know the type,
so TypeScript should force me
to prove what it is."
```

Therefore:

```text
unknown
```

is usually safer.

---

# 16. Where `unknown` Is Useful

`unknown` is useful around uncertain data.

Examples:

```text
Parsed external data

Library boundaries

Error-like values

Dynamic input

Generic external values

Values requiring runtime validation
```

Example:

```typescript
function processValue(
    value: unknown
) {
    if (
        typeof value ===
        "string"
    ) {
        console.log(
            value.toUpperCase()
        );
    }
}
```

---

# 17. Assignment with `unknown`

Almost anything can be assigned **to** `unknown`.

```typescript
let value: unknown;

value = 100;

value = "Hello";

value = true;
```

But `unknown` cannot be freely assigned **from** to a more specific type.

```typescript
let value: unknown =
    "Hello";

let username: string =
    value;
```

TypeScript rejects this.

Why?

Because TypeScript does not know that `value` is definitely a string.

---

# 18. Safely Assigning `unknown`

First narrow it:

```typescript
let value: unknown =
    "Hello";

if (
    typeof value === "string"
) {
    const username: string =
        value;
}
```

Now TypeScript knows the assignment is safe.

---

# 19. `void`

`void` is primarily used for functions whose return value is not intended to be used meaningfully.

Example:

```typescript
function logMessage(
    message: string
): void {
    console.log(message);
}
```

The function performs an action:

```text
Log message
```

rather than producing a meaningful result for callers.

---

# 20. `void` Example

```typescript
function printUser(
    username: string
): void {
    console.log(username);
}
```

Usage:

```typescript
printUser("Alice");
```

The purpose is the side effect:

```text
Printing to console
```

not a meaningful returned value.

---

# 21. `void` Is Not Exactly `undefined`

A common beginner simplification is:

```text
void = undefined
```

But TypeScript uses `void` with special semantics, particularly for function return types.

For learning purposes:

```text
void
→ caller should not depend on a meaningful return value
```

Whereas:

```text
undefined
→ an actual possible JavaScript value
```

---

# 22. `void` and Callbacks

Consider:

```typescript
function execute(
    callback: () => void
) {
    callback();
}
```

This means:

```text
execute does not care about
the callback's return value.
```

A callback may still technically return something:

```typescript
execute(() => {
    return 123;
});
```

The important point is that `execute` treats the callback's return value as irrelevant.

This is why `void` should not simply be understood as:

```text
"This function can never return a value."
```

---

# 23. `undefined`

`undefined` is a JavaScript runtime value and also a TypeScript type.

```typescript
let value: undefined =
    undefined;
```

A variable typed only as `undefined` is rarely useful by itself.

It becomes important when combined with other types.

Example:

```typescript
let username:
    string | undefined;
```

Meaning:

```text
username can be

string

OR

undefined
```

---

# 24. Optional Values and `undefined`

Consider:

```typescript
function greet(
    name?: string
) {
    // ...
}
```

Inside the function:

```text
name
→ string | undefined
```

because the caller may omit the argument.

---

# 25. `null`

`null` is also a JavaScript runtime value and TypeScript type.

```typescript
let value: null =
    null;
```

Again, typing something as only `null` is rarely useful.

More commonly:

```typescript
let selectedUser:
    User | null =
    null;
```

Meaning:

```text
No selected user
        │
        ▼
       null

User selected
        │
        ▼
       User
```

---

# 26. `null` vs `undefined`

A common application convention is:

```text
undefined
→ value is absent / not provided

null
→ value intentionally represents no value
```

But JavaScript itself allows applications and APIs to use these differently.

TypeScript's job is to model the possibilities accurately.

Example:

```typescript
let middleName:
    string | null;

let optionalName:
    string | undefined;
```

---

# 27. `strictNullChecks`

This compiler option is extremely important.

```json
{
    "compilerOptions": {
        "strictNullChecks": true
    }
}
```

With strict null checking, TypeScript treats:

```text
null
undefined
```

as distinct possibilities that must be handled.

---

# 28. Why `strictNullChecks` Matters

Suppose:

```typescript
function getUser():
    User | undefined {
    // ...
}
```

Then:

```typescript
const user =
    getUser();

console.log(
    user.name
);
```

is unsafe because:

```text
user
→ User | undefined
```

TypeScript requires you to handle the missing case.

---

# 29. Handling `undefined`

```typescript
const user =
    getUser();

if (user) {
    console.log(
        user.name
    );
}
```

Inside:

```typescript
if (user)
```

TypeScript narrows:

```text
User | undefined
        │
        ▼
       User
```

---

# 30. Optional Chaining with Nullable Values

You already know optional chaining from JavaScript.

TypeScript understands it.

```typescript
const username =
    user?.name;
```

If:

```text
user
→ User | undefined
```

then `username` may become:

```text
string | undefined
```

TypeScript propagates the uncertainty.

---

# 31. Nullish Coalescing with Nullable Values

You already know:

```typescript
const username =
    user?.name ?? "Guest";
```

TypeScript understands that after the fallback:

```text
username
→ string
```

assuming `user.name` itself is typed appropriately.

TypeScript does not change `??`.

It tracks the resulting type.

---

# 32. `object`

TypeScript has an `object` type.

```typescript
let value: object;
```

It represents non-primitive values.

For example:

```typescript
value = {};

value = [];

value = () => {};
```

These are objects/non-primitives in TypeScript's type model.

---

# 33. Primitive Values and `object`

These are not compatible with `object`:

```typescript
let value: object;

value = "Hello";

value = 100;

value = true;
```

because they are primitive values.

---

# 34. `object` Does Not Describe Properties

Suppose:

```typescript
let user: object = {
    name: "Alice"
};
```

This does **not** mean TypeScript knows:

```text
user.name
```

exists.

This can fail:

```typescript
user.name;
```

because `object` only says:

```text
This is some non-primitive value.
```

It does not describe its structure.

---

# 35. Prefer Object Shapes When Structure Is Known

Instead of:

```typescript
const user: object = {
    name: "Alice",
    age: 25
};
```

use:

```typescript
const user: {
    name: string;
    age: number;
} = {
    name: "Alice",
    age: 25
};
```

Or later:

```typescript
type User = {
    name: string;
    age: number;
};
```

Then:

```typescript
const user: User = {
    name: "Alice",
    age: 25
};
```

---

# 36. `object` vs `{}`

These are not identical concepts.

```typescript
object
```

means roughly:

```text
non-primitive value
```

Whereas:

```typescript
{}
```

in TypeScript is broader than many beginners expect and generally means a value that is not `null` or `undefined`.

Therefore:

```typescript
let value: {};

value = "Hello";
value = 100;
value = true;
value = {};
```

can be allowed under strict null checking.

Do not use `{}` when you mean:

```text
object with no properties
```

---

# 37. `Object` vs `object`

Avoid using:

```typescript
Object
```

for ordinary object typing.

Prefer:

```typescript
object
```

when you specifically mean a non-primitive value.

But usually the best choice is an actual object shape:

```typescript
type User = {
    name: string;
};
```

---

# 38. Array Types

You already know JavaScript arrays.

TypeScript adds the ability to describe what elements the array contains.

```typescript
const numbers:
    number[] = [
        1,
        2,
        3
    ];
```

Meaning:

```text
Array
│
├── number
├── number
└── number
```

---

# 39. Array Type Syntax

Two common forms:

```typescript
number[]
```

and:

```typescript
Array<number>
```

Example:

```typescript
const first:
    number[] = [
        1,
        2,
        3
    ];

const second:
    Array<number> = [
        1,
        2,
        3
    ];
```

Both describe arrays of numbers.

---

# 40. String Arrays

```typescript
const users:
    string[] = [
        "Alice",
        "Bob",
        "Charlie"
    ];
```

Valid:

```typescript
users.push("David");
```

Invalid:

```typescript
users.push(100);
```

---

# 41. Object Arrays

```typescript
type User = {
    id: number;
    name: string;
};

const users:
    User[] = [
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

Now every element must satisfy:

```text
User
```

---

# 42. Array Type Inference

Usually this is enough:

```typescript
const numbers = [
    1,
    2,
    3
];
```

TypeScript infers:

```text
number[]
```

Similarly:

```typescript
const names = [
    "Alice",
    "Bob"
];
```

TypeScript infers:

```text
string[]
```

---

# 43. Mixed Arrays

```typescript
const values = [
    1,
    "Alice"
];
```

TypeScript can infer:

```typescript
(number | string)[]
```

Meaning every element can be:

```text
number
OR
string
```

We will study unions next.

---

# 44. Arrays Are Variable-Length Collections

This matters when comparing arrays with tuples.

```typescript
const numbers:
    number[] = [
        1,
        2,
        3
    ];
```

The array can contain:

```text
0 numbers

3 numbers

10 numbers

100 numbers
```

The type specifies the element type, not a fixed length.

---

# 45. Tuple Types

A **tuple** is an array type where positions have known types and typically a known structure.

Example:

```typescript
let user:
    [number, string];
```

This means:

```text
Index 0
→ number

Index 1
→ string
```

Valid:

```typescript
user = [
    1,
    "Alice"
];
```

Invalid:

```typescript
user = [
    "Alice",
    1
];
```

Order matters.

---

# 46. Array vs Tuple

Array:

```typescript
string[]
```

means:

```text
Any number of elements

Every element
→ string
```

Tuple:

```typescript
[string, number]
```

means:

```text
Position 0
→ string

Position 1
→ number
```

---

# 47. Tuple Example — Coordinates

```typescript
const coordinates:
    [number, number] = [
        10,
        20
    ];
```

Meaning:

```text
coordinates[0]
→ number

coordinates[1]
→ number
```

The structure represents exactly two meaningful positions.

---

# 48. Tuple Example — API Result

```typescript
type Result =
    [boolean, string];

const result: Result = [
    true,
    "User created"
];
```

Meaning:

```text
Index 0
→ success flag

Index 1
→ message
```

---

# 49. Named Tuple Elements

TypeScript allows labels for tuple positions.

```typescript
type UserEntry = [
    id: number,
    username: string,
    active: boolean
];
```

Usage:

```typescript
const user:
    UserEntry = [
        1,
        "Alice",
        true
    ];
```

Labels improve readability.

They do not create object properties.

You still access:

```typescript
user[0];
user[1];
user[2];
```

---

# 50. Optional Tuple Elements

Tuple elements can be optional.

```typescript
type UserEntry = [
    id: number,
    username?: string
];
```

Valid:

```typescript
const first:
    UserEntry = [
        1
    ];
```

Also valid:

```typescript
const second:
    UserEntry = [
        1,
        "Alice"
    ];
```

---

# 51. Rest Elements in Tuples

Tuples can contain rest elements.

```typescript
type NumberList = [
    string,
    ...number[]
];
```

Valid:

```typescript
const values:
    NumberList = [
        "scores",
        10,
        20,
        30
    ];
```

Meaning:

```text
First element
→ string

Remaining elements
→ numbers
```

---

# 52. Tuples Are Useful for Fixed Structures

Use tuples when positions have specific meaning.

Examples:

```text
Coordinates

RGB-like values

Pairs

Function result pairs

Hook-style return values

Key-value entries
```

But if a structure becomes complex:

```typescript
[
    number,
    string,
    boolean,
    string,
    number,
    boolean
]
```

an object is often clearer.

---

# 53. Tuple vs Object

Tuple:

```typescript
type User =
    [number, string];
```

Usage:

```typescript
const user: User = [
    1,
    "Alice"
];
```

You must remember:

```text
0 → ID
1 → Name
```

Object:

```typescript
type User = {
    id: number;
    name: string;
};
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

For domain entities, objects are usually more readable.

---

# 54. Literal Types

Normally:

```typescript
let status: string;
```

means:

```text
Any string
```

For example:

```typescript
status = "loading";
status = "success";
status = "failed";
status = "anything";
```

But sometimes we want one **specific value** to be the type.

```typescript
let status:
    "success";
```

Now the only allowed value is:

```text
"success"
```

---

# 55. String Literal Type

```typescript
let role:
    "admin";

role = "admin";
```

Valid.

```typescript
role = "user";
```

Invalid.

Because the type is not:

```text
string
```

It is specifically:

```text
"admin"
```

---

# 56. Number Literal Type

```typescript
let code: 200;

code = 200;
```

Valid.

```typescript
code = 404;
```

Invalid.

The type is specifically:

```text
200
```

---

# 57. Boolean Literal Types

TypeScript also has:

```typescript
true
```

and:

```typescript
false
```

as literal types.

Example:

```typescript
let success: true =
    true;
```

This cannot become:

```typescript
false
```

---

# 58. Literal Types Become Powerful with Unions

A single literal type:

```typescript
let status:
    "success";
```

has limited usefulness.

But combine literals:

```typescript
let status:
    "loading"
    | "success"
    | "failed";
```

Now only these values are allowed:

```text
loading
success
failed
```

Anything else is rejected.

We will study this properly in **Union Types**.

---

# 59. Literal Types as Controlled Values

Instead of:

```typescript
let theme: string;
```

we can eventually model:

```typescript
let theme:
    "light" | "dark";
```

Now:

```typescript
theme = "light";

theme = "dark";
```

are valid.

But:

```typescript
theme = "purple";
```

is invalid.

This prevents invalid states.

---

# 60. Literal Inference with `const`

```typescript
const status =
    "success";
```

Because `status` cannot be reassigned, TypeScript can infer the literal type:

```text
"success"
```

Compare:

```typescript
let status =
    "success";
```

which generally becomes:

```text
string
```

because reassignment is possible.

---

# 61. Literal Widening in Objects

Consider:

```typescript
const request = {
    method: "GET"
};
```

Although `request` itself cannot be reassigned, its property can:

```typescript
request.method =
    "POST";
```

Therefore TypeScript generally infers:

```text
request.method
→ string
```

rather than:

```text
"GET"
```

---

# 62. `as const`

We can preserve literal values using:

```typescript
as const
```

Example:

```typescript
const request = {
    method: "GET"
} as const;
```

Now TypeScript treats the structure more narrowly.

Conceptually:

```text
method
→ "GET"

property
→ readonly
```

---

# 63. `readonly`

`readonly` prevents reassignment through a particular typed property/reference.

Example:

```typescript
type User = {
    readonly id: number;
    name: string;
};
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

This works:

```typescript
user.name =
    "Bob";
```

This does not:

```typescript
user.id =
    2;
```

because:

```text
id
→ readonly
```

---

# 64. `readonly` Does Not Mean `const`

Compare:

```typescript
const user = {
    id: 1
};
```

This prevents:

```typescript
user = {
    id: 2
};
```

But allows:

```typescript
user.id = 2;
```

Because `const` protects the variable binding.

---

With:

```typescript
type User = {
    readonly id: number;
};
```

this prevents:

```typescript
user.id = 2;
```

through a `User`-typed reference.

So:

```text
const
→ variable cannot be reassigned

readonly
→ typed property cannot be reassigned
   through that readonly view
```

---

# 65. Readonly Arrays

Normal array:

```typescript
const numbers:
    number[] = [
        1,
        2,
        3
    ];
```

You can mutate it:

```typescript
numbers.push(4);
```

Readonly array:

```typescript
const numbers:
    readonly number[] = [
        1,
        2,
        3
    ];
```

Now:

```typescript
numbers.push(4);
```

is rejected.

---

# 66. `ReadonlyArray<T>`

Alternative syntax:

```typescript
const numbers:
    ReadonlyArray<number> = [
        1,
        2,
        3
    ];
```

Equivalent concept:

```typescript
readonly number[]
```

---

# 67. Readonly Tuple

```typescript
const coordinates:
    readonly [
        number,
        number
    ] = [
        10,
        20
    ];
```

Now attempts to mutate positions are rejected.

---

# 68. `as const` Creates Readonly Literal Structures

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

TypeScript treats this approximately as:

```text
readonly ["admin", "user"]
```

rather than:

```text
string[]
```

This becomes extremely useful for deriving types from constant configuration.

---

# 69. `as const` Object Example

```typescript
const config = {
    environment:
        "production",
    retries:
        3
} as const;
```

Conceptually TypeScript sees:

```text
readonly environment:
    "production"

readonly retries:
    3
```

rather than:

```text
environment:
    string

retries:
    number
```

---

# 70. `readonly` Is Compile-Time Protection

This is extremely important.

```typescript
type User = {
    readonly id: number;
};
```

TypeScript prevents:

```typescript
user.id = 2;
```

during type checking.

But `readonly` itself does not automatically freeze the JavaScript object at runtime.

After compilation, the type modifier is gone.

Therefore:

```text
readonly
≠
Object.freeze()
```

---

# 71. `readonly` vs Runtime Immutability

TypeScript:

```typescript
readonly
```

provides:

```text
Compile-time mutation protection
```

JavaScript:

```javascript
Object.freeze()
```

provides runtime freezing behavior.

They solve related but different problems.

---

# 72. Shallow Readonly

Consider:

```typescript
type User = {
    readonly profile: {
        name: string;
    };
};
```

Then:

```typescript
user.profile = {
    name: "Bob"
};
```

is rejected.

But:

```typescript
user.profile.name =
    "Bob";
```

can still be allowed because only the `profile` property itself is readonly.

The nested `name` property was not declared readonly.

---

# 73. Deep Readonly Requires More Modeling

To prevent nested mutation:

```typescript
type User = {
    readonly profile: {
        readonly name: string;
    };
};
```

Now:

```typescript
user.profile.name =
    "Bob";
```

is also rejected.

Therefore:

```text
readonly
is generally shallow
at the property where
it is declared.
```

---

# 74. `never`

`never` represents values that **never occur**.

This is one of the most important TypeScript-specific concepts.

Think:

```text
never
→ impossible value
```

---

# 75. Function That Never Returns

Example:

```typescript
function fail(
    message: string
): never {
    throw new Error(
        message
    );
}
```

This function never reaches a normal return.

It always throws.

Therefore:

```text
return type
→ never
```

---

# 76. Infinite Loop and `never`

```typescript
function runForever():
    never {
    while (true) {
        // ...
    }
}
```

The function never completes normally.

Therefore:

```text
never
```

---

# 77. `never` vs `void`

This distinction matters.

```typescript
function log():
    void {
    console.log("Hello");
}
```

The function completes normally.

It just has no meaningful return value.

---

```typescript
function fail():
    never {
    throw new Error(
        "Failed"
    );
}
```

The function never completes normally.

Therefore:

```text
void
================================

Function can finish normally.

Caller should not expect
a meaningful result.


never
================================

Function cannot finish normally.
```

---

# 78. `never` Represents Impossible States

Suppose eventually we have:

```typescript
type Status =
    "loading"
    | "success";
```

After checking:

```typescript
function handleStatus(
    status: Status
) {
    if (
        status === "loading"
    ) {
        return;
    }

    if (
        status === "success"
    ) {
        return;
    }

    // No Status value
    // should remain here.
}
```

TypeScript can reason that after all possibilities are handled, the remaining value is:

```text
never
```

This is the basis of **exhaustiveness checking**.

---

# 79. Exhaustiveness Checking with `never`

Later we can write patterns like:

```typescript
type Status =
    "loading"
    | "success"
    | "failed";

function handleStatus(
    status: Status
) {
    switch (status) {
        case "loading":
            break;

        case "success":
            break;

        case "failed":
            break;

        default: {
            const impossible:
                never =
                status;
        }
    }
}
```

If all cases are handled:

```text
status in default
→ never
```

If a new status is later added but not handled, TypeScript can detect the problem.

---

# 80. `never` in Type Operations

Later, when working with conditional types and unions, `never` also means:

```text
No possible member
```

Example conceptually:

```text
Allowed values:
A | B

Remove:
A | B

Remaining:
never
```

We will revisit this in advanced type manipulation.

---

# 81. `never` Is Assignable Very Narrowly

`never` represents no possible value.

You cannot normally create:

```typescript
let value: never =
    10;
```

or:

```typescript
let value: never =
    "Hello";
```

or:

```typescript
let value: never =
    undefined;
```

There is no ordinary runtime value you can provide that satisfies `never`.

---

# 82. Type Hierarchy Mental Model

A useful simplified model:

```text
             unknown
                │
                ▼
       Possible Values
                │
      ┌─────────┼─────────┐
      │         │         │
   string    number     object
      │         │         │
      └─────────┼─────────┘
                │
                ▼
              never
```

Think:

```text
unknown
→ could be anything

never
→ could be nothing
```

This is a conceptual model of assignability, not a full formal representation of TypeScript's type system.

---

# 83. `unknown` and `never` Are Opposite-Like Concepts

```text
unknown
================================

Maximum uncertainty.

Could contain almost any value.


never
================================

No possible value.

Represents impossibility.
```

This becomes very useful when learning generics and conditional types.

---

# 84. `any` Is Special

Do not think of `any` as simply another normal top type.

`any` behaves specially and intentionally bypasses many type-system guarantees.

Conceptually:

```text
unknown
→ "I don't know, prove it."

any
→ "I don't know, allow it."

never
→ "This cannot happen."
```

---

# 85. `void`, `undefined`, and `never`

These three are often confused.

## `undefined`

```text
Actual JavaScript value.
```

Example:

```typescript
let value:
    undefined =
    undefined;
```

---

## `void`

```text
No meaningful return value
expected from a function.
```

Example:

```typescript
function log():
    void {}
```

---

## `never`

```text
Function/value can never
successfully exist at that point.
```

Example:

```typescript
function fail():
    never {
    throw new Error();
}
```

---

# 86. Comparison Table

| Type        | Meaning                                             |
| ----------- | --------------------------------------------------- |
| `any`       | Disable/escape much of type checking                |
| `unknown`   | Value exists but type is unknown                    |
| `void`      | No meaningful return value expected                 |
| `never`     | No possible value / never completes normally        |
| `undefined` | JavaScript `undefined` value                        |
| `null`      | JavaScript `null` value                             |
| `object`    | Non-primitive value                                 |
| `T[]`       | Array containing `T`                                |
| `[A, B]`    | Tuple with known positions                          |
| `"value"`   | Specific literal value as a type                    |
| `readonly`  | Prevent assignment/mutation through that typed view |

---

# 87. Choosing `any` vs `unknown`

If you receive data whose type is genuinely uncertain:

Avoid:

```typescript
function handleData(
    data: any
) {
    console.log(
        data.user.profile.name
    );
}
```

Prefer:

```typescript
function handleData(
    data: unknown
) {
    // Validate/narrow first.
}
```

Rule:

```text
Unknown data
     │
     ▼
Use unknown
     │
     ▼
Narrow / Validate
     │
     ▼
Use safely
```

---

# 88. Choosing Array vs Tuple

Use an array when:

```text
Collection length varies

Elements represent the same
general kind of value
```

Example:

```typescript
User[]
```

Use a tuple when:

```text
Positions have specific meaning

Structure is fixed or constrained
```

Example:

```typescript
[id: number, name: string]
```

---

# 89. Choosing `null` vs `undefined`

There is no universal rule that every project must follow, but be consistent.

A common approach:

```text
undefined
→ not supplied / missing

null
→ intentionally empty
```

Example:

```typescript
type User = {
    middleName?: string;
    deletedAt:
        Date | null;
};
```

Here:

```text
middleName absent
→ undefined

deletedAt null
→ user has not been deleted
```

---

# 90. Choosing `readonly`

Use `readonly` when mutation should not be part of the contract.

Example:

```typescript
type User = {
    readonly id: number;
    name: string;
};
```

The ID should remain stable:

```typescript
user.id = 100;
```

Rejected.

But editable data can remain mutable:

```typescript
user.name = "Bob";
```

Allowed.

---

# 91. `readonly` Communicates Intent

Without:

```typescript
readonly
```

another developer may assume mutation is valid.

With:

```typescript
readonly id: number;
```

the type contract communicates:

```text
This property should not
be reassigned through this type.
```

Types are not only error prevention.

They also document design intent.

---

# 92. Literal Types Prevent Invalid States

Weak:

```typescript
let status: string;
```

Possible:

```typescript
status = "success";

status = "failed";

status = "banana";

status = "whatever";
```

Stronger:

```typescript
type Status =
    "loading"
    | "success"
    | "failed";
```

Now invalid states are rejected.

This idea is central to good TypeScript design:

```text
Make invalid states
harder to represent.
```

---

# 93. TypeScript Types Are Sets of Possible Values

A powerful mental model is to think of a type as a **set of possible values**.

Example:

```typescript
string
```

represents:

```text
"Hello"
"Alice"
"Admin"
""
and every other string
```

---

Literal:

```typescript
"admin"
```

represents only:

```text
"admin"
```

---

`never` represents:

```text
{}
```

No possible values.

---

`unknown` represents:

```text
All possible values
```

Conceptually:

```text
unknown
    │
    ▼
Many possible values
    │
    ▼
string
    │
    ▼
"admin"
    │
    ▼
never
```

As types become narrower, fewer values are allowed.

---

# 94. Narrow Types vs Broad Types

Broad:

```typescript
string
```

Many possible values.

Narrow:

```typescript
"admin"
```

One possible string value.

Even broader:

```typescript
unknown
```

Could be many kinds of values.

Impossible:

```typescript
never
```

No possible value.

Understanding broad vs narrow types is essential for:

```text
Type Narrowing

Union Types

Generics

Conditional Types

Discriminated Unions
```

---

# 95. Common Mistake — Using `any` Everywhere

Bad:

```typescript
function createUser(
    user: any
): any {
    return user;
}
```

This removes much of TypeScript's benefit.

Better:

```typescript
type User = {
    id: number;
    name: string;
};

function createUser(
    user: User
): User {
    return user;
}
```

---

# 96. Common Mistake — Using `object` for Known Structures

Avoid:

```typescript
function printUser(
    user: object
) {
    // user.name?
}
```

Prefer:

```typescript
type User = {
    name: string;
};

function printUser(
    user: User
) {
    console.log(
        user.name
    );
}
```

---

# 97. Common Mistake — Treating `unknown` Like `any`

This is invalid:

```typescript
let data: unknown;

data.name;
```

That restriction is the point.

You must first prove what `data` is.

---

# 98. Common Mistake — Using Tuple for Large Domain Objects

Avoid:

```typescript
type User = [
    number,
    string,
    string,
    boolean,
    number
];
```

Then:

```typescript
user[3]
```

What does index `3` mean?

An object is clearer:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    active: boolean;
    age: number;
};
```

---

# 99. Common Mistake — Assuming `readonly` Freezes Runtime Objects

```typescript
readonly
```

is TypeScript-level protection.

It does not automatically call:

```javascript
Object.freeze()
```

Types disappear at runtime.

---

# 100. Common Mistake — Confusing `void` and `never`

`void`:

```typescript
function log():
    void {
    console.log("Hello");
}
```

Function finishes normally.

`never`:

```typescript
function fail():
    never {
    throw new Error();
}
```

Function never finishes normally.

---

# 101. Common Mistake — Ignoring `null` and `undefined`

Avoid designing everything as though values always exist.

Real applications have states such as:

```text
User not loaded

Search returned nothing

Optional configuration missing

API field absent

Record intentionally empty
```

Model those possibilities accurately.

Example:

```typescript
let user:
    User | null =
    null;
```

---

# 102. Common Mistake — Using `{}` for an Empty Object

This:

```typescript
let value: {};
```

does **not** simply mean:

```text
object containing zero properties
```

It can accept many non-nullish values.

If you mean a specific object structure, describe that structure.

---

# 103. Common Mistake — Overusing Type Assertions

When uncertain data appears:

```typescript
const data: unknown =
    getData();
```

do not immediately force:

```typescript
const user =
    data as User;
```

That tells TypeScript to trust your assertion.

It does not validate the runtime value.

Prefer validation/narrowing where uncertainty is real.

---

# 104. Interview Question — What Is `any`?

`any` is a special TypeScript type that largely disables type checking for a value.

It is useful as an escape hatch but weakens type safety and should generally be minimized.

---

# 105. Interview Question — What Is `unknown`?

`unknown` represents a value whose type is not yet known.

Unlike `any`, TypeScript requires the value to be narrowed or otherwise proven safe before performing type-specific operations.

---

# 106. Interview Question — `any` vs `unknown`?

```text
any
→ allows unsafe operations

unknown
→ requires checking first
```

Prefer `unknown` when the type is genuinely uncertain.

---

# 107. Interview Question — What Is `void`?

`void` is commonly used for functions whose return value is not intended to be used meaningfully.

Example:

```typescript
function log(
    message: string
): void {
    console.log(message);
}
```

---

# 108. Interview Question — What Is `never`?

`never` represents a value that can never occur.

Common examples include:

```text
Functions that always throw

Functions that never terminate

Impossible branches after exhaustive narrowing
```

---

# 109. Interview Question — `never` vs `void`?

```text
void
→ function may complete normally,
  but caller does not use a
  meaningful return value

never
→ function cannot complete normally
```

---

# 110. Interview Question — What Is a Tuple?

A tuple is an array type with a known positional structure.

```typescript
type UserEntry = [
    number,
    string
];
```

Meaning:

```text
index 0 → number

index 1 → string
```

---

# 111. Interview Question — Array vs Tuple?

Array:

```typescript
string[]
```

represents a variable-length collection of strings.

Tuple:

```typescript
[string, number]
```

represents a specific positional structure.

---

# 112. Interview Question — What Are Literal Types?

Literal types represent specific values as types.

```typescript
"admin"

200

true
```

Example:

```typescript
let role:
    "admin" | "user";
```

Only those values are allowed.

---

# 113. Interview Question — What Is `readonly`?

`readonly` prevents reassignment through a typed property or collection.

Example:

```typescript
type User = {
    readonly id: number;
};
```

TypeScript rejects:

```typescript
user.id = 2;
```

---

# 114. Interview Question — `const` vs `readonly`?

```text
const
→ prevents variable reassignment

readonly
→ prevents mutation/reassignment
  through a typed property/view
```

Example:

```typescript
const user = {
    id: 1
};

user.id = 2;
```

is valid because `const` protects the `user` binding, not the object's properties.

---

# 115. Interview Question — Does `readonly` Make Objects Immutable at Runtime?

No.

`readonly` is a TypeScript compile-time restriction.

It does not automatically freeze the runtime JavaScript object.

---

# 116. Interview Question — What Is `strictNullChecks`?

`strictNullChecks` makes TypeScript treat `null` and `undefined` as distinct possibilities that must be handled when they can occur.

This greatly improves null safety.

---

# 117. Interview Question — What Does `object` Mean?

`object` represents non-primitive values.

It does not describe the specific properties available on the object.

For known structures, use an explicit object type, interface, or type alias.

---

# 118. Interview Question — `object` vs `{}`?

Simplified:

```text
object
→ non-primitive values

{}
→ generally non-nullish values
```

Therefore `{}` should not be assumed to mean an empty object.

---

# 119. TypeScript Types Cheat Sheet

```text
ANY
================================

let value: any;

Type checking is largely bypassed.

Use sparingly.


UNKNOWN
================================

let value: unknown;

Value may be anything.

Must narrow before
type-specific operations.


VOID
================================

function log(): void {}

No meaningful return value
expected by caller.


NEVER
================================

function fail(): never {
    throw new Error();
}

No possible normal result.


NULL
================================

let user:
    User | null;


UNDEFINED
================================

let user:
    User | undefined;


OBJECT
================================

let value: object;

Non-primitive value.

Does not describe properties.


ARRAY
================================

number[]

string[]

User[]

Array<number>


TUPLE
================================

[number, string]

Fixed/constrained
positional structure.


NAMED TUPLE
================================

[
    id: number,
    name: string
]


LITERAL
================================

"admin"

200

true


LITERAL UNION
================================

"loading"
| "success"
| "failed"


READONLY PROPERTY
================================

readonly id: number;


READONLY ARRAY
================================

readonly number[]

ReadonlyArray<number>


READONLY TUPLE
================================

readonly [
    number,
    number
]


AS CONST
================================

const config = {
    mode: "production"
} as const;


STRICT NULL CHECKING
================================

strictNullChecks: true


MENTAL MODEL
================================

unknown
→ could be anything

any
→ stop checking

void
→ return value not meaningful

never
→ impossible

readonly
→ compile-time mutation restriction
```

---

# 120. Important Rules to Remember

```text
1. TypeScript models JavaScript values with static types.

2. JavaScript primitive behavior does not change.

3. any is an escape hatch.

4. any weakens TypeScript's safety.

5. Avoid unnecessary any.

6. any can spread through surrounding code.

7. noImplicitAny helps prevent accidental any.

8. unknown represents uncertain values safely.

9. Almost any value can be assigned to unknown.

10. unknown cannot be used freely without narrowing.

11. unknown is usually preferable to any for uncertain data.

12. any means "allow me."

13. unknown means "prove it first."

14. void is commonly used for functions with no meaningful return value.

15. void does not simply mean undefined.

16. Callback () => void means the caller ignores the callback's return value.

17. undefined is an actual JavaScript value.

18. null is an actual JavaScript value.

19. null and undefined should be modeled when they can occur.

20. strictNullChecks improves safety.

21. Optional parameters commonly involve undefined.

22. Optional properties commonly involve absence/undefined semantics.

23. Optional chaining propagates possible undefined values.

24. Nullish coalescing can provide a non-nullish fallback.

25. object represents non-primitive values.

26. object does not describe object properties.

27. Prefer explicit shapes when object structure is known.

28. Object and object are not the same type.

29. Prefer object over Object when a generic non-primitive type is truly needed.

30. {} does not simply mean empty object.

31. Avoid using {} as an empty-object type assumption.

32. Arrays can specify their element types.

33. number[] means an array of numbers.

34. Array<number> is an alternative generic syntax.

35. User[] means an array of User values.

36. Arrays normally have variable lengths.

37. Tuples describe positional structures.

38. Tuple position matters.

39. Tuple elements can have different types.

40. Tuple elements can have labels.

41. Tuple labels improve readability.

42. Tuple labels do not create object properties.

43. Tuple elements can be optional.

44. Tuples can contain rest elements.

45. Prefer objects for complex domain structures.

46. Literal types represent specific values.

47. "admin" can itself be a type.

48. 200 can itself be a type.

49. true can itself be a type.

50. Literal types become powerful when combined with unions.

51. Literal types can restrict application states.

52. const often produces narrower literal inference.

53. let often causes literal widening.

54. Object properties remain mutable even when the object variable is const.

55. as const preserves narrow literal information.

56. as const also creates readonly-like structures.

57. readonly prevents reassignment through the typed view.

58. readonly properties cannot be reassigned through that type.

59. readonly arrays reject mutating array methods through that view.

60. ReadonlyArray<T> represents a readonly array.

61. readonly tuples prevent tuple mutation through that view.

62. readonly is compile-time protection.

63. readonly does not automatically freeze runtime objects.

64. readonly is generally shallow where declared.

65. Nested values need their own readonly modeling for deeper protection.

66. never represents impossible values.

67. Functions that always throw can return never.

68. Functions that never terminate can return never.

69. Exhaustively handled branches can narrow to never.

70. never is useful for exhaustiveness checking.

71. never becomes important in conditional types.

72. void and never are different.

73. void functions can complete normally.

74. never functions cannot complete normally.

75. undefined and void are different concepts.

76. unknown and any are different.

77. unknown and never are useful opposite-like mental models.

78. unknown represents maximum uncertainty.

79. never represents impossibility.

80. any behaves specially and bypasses many guarantees.

81. Types can be understood as sets of possible values.

82. string represents many possible string values.

83. A literal type represents fewer possible values.

84. never represents zero possible values.

85. Narrower types allow fewer possible values.

86. Type narrowing reduces the possible set of values.

87. Union types combine sets of possible values.

88. Good TypeScript models real possibilities accurately.

89. Do not remove null simply because handling it is inconvenient.

90. Do not use any simply because typing is inconvenient.

91. Do not assert unknown data without understanding it.

92. Runtime validation and static typing solve different problems.

93. readonly and runtime immutability solve different problems.

94. Arrays and tuples solve different modeling problems.

95. object and object shapes solve different problems.

96. Literal types help make invalid states harder to represent.

97. Prefer the narrowest useful type that accurately models the value.

98. Do not make types narrower than reality.

99. Do not make types unnecessarily broad.

100. TypeScript's type system is primarily about modeling possible values safely.
```

---

# 121. Final Mental Model

```text
                    TYPESCRIPT TYPES
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         ▼                 ▼                 ▼

      UNKNOWN           KNOWN TYPES        NEVER

 Could be anything      string             Impossible
 but must prove it      number             value
                        User
                        etc.
         │
         │
         ▼
        ANY

 Special escape hatch:
 bypass much of checking
```

Another useful model:

```text
unknown
│
│ Narrowing
▼
string
│
│ Narrowing
▼
"admin"
│
│ Impossible remainder
▼
never
```

And for functions:

```text
void
→ function may finish normally
→ return value is not meaningful


never
→ function cannot finish normally
→ no normal return value exists
```

For collections:

```text
ARRAY
================================

User[]

0...N User values


TUPLE
================================

[number, string]

Specific positional structure
```

For mutation:

```text
const
→ protects variable binding


readonly
→ protects typed property/view


Object.freeze()
→ runtime behavior
```

