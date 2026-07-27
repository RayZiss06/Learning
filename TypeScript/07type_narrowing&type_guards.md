# Type Narrowing & Type Guards

TypeScript often knows that a variable can contain **multiple possible types**.

Example:

```typescript
function printId(
    id: string | number
) {
    // id → string | number
}
```

Inside the function, TypeScript does not initially know whether `id` is:

```text
string
```

or:

```text
number
```

Before using type-specific operations, we need to determine which type the value currently has.

This process is called:

```text
Type Narrowing
```

---

# 1. What Is Type Narrowing?

Type narrowing is the process by which TypeScript reduces a broader type into a more specific type based on runtime checks and control flow.

Example:

```typescript
function printId(
    id: string | number
) {
    if (
        typeof id === "string"
    ) {
        console.log(
            id.toUpperCase()
        );
    }
}
```

Before the condition:

```text
id
→ string | number
```

Inside the condition:

```text
id
→ string
```

TypeScript has narrowed the type.

---

# 2. Why Narrowing Is Necessary

Consider:

```typescript
function format(
    value: string | number
) {
    return value.toUpperCase();
}
```

This fails because:

```text
number
```

does not have:

```text
toUpperCase()
```

TypeScript only knows:

```text
value
→ string | number
```

So we narrow first:

```typescript
function format(
    value: string | number
) {
    if (
        typeof value === "string"
    ) {
        return value.toUpperCase();
    }

    return value.toFixed(2);
}
```

Now each operation is safe for its branch.

---

# 3. Broad Type → Narrow Type

Think of narrowing like:

```text
string | number
       │
       │ typeof value === "string"
       ▼
     string
```

Else branch:

```text
string | number
       │
       │ string eliminated
       ▼
     number
```

TypeScript analyzes program flow to determine what types remain possible.

---

# 4. Control Flow Analysis

TypeScript does more than inspect individual conditions.

It analyzes:

```text
if / else

return

throw

switch

assignments

loops

logical expressions

type guards
```

to understand which types are possible at each point.

This is called:

```text
Control Flow Analysis
```

---

# 5. `typeof` Type Guard

JavaScript's:

```javascript
typeof
```

is one of the most common narrowing mechanisms.

Example:

```typescript
function printValue(
    value: string | number
) {
    if (
        typeof value === "string"
    ) {
        console.log(
            value.toUpperCase()
        );
    } else {
        console.log(
            value.toFixed(2)
        );
    }
}
```

Inside:

```text
if
→ string

else
→ number
```

---

# 6. Types Supported by `typeof`

Useful `typeof` results include:

```text
"string"

"number"

"boolean"

"bigint"

"symbol"

"undefined"

"function"

"object"
```

Example:

```typescript
function inspect(
    value:
        | string
        | number
        | boolean
) {
    if (
        typeof value === "boolean"
    ) {
        // value → boolean
    }
}
```

---

# 7. `typeof null` Problem

JavaScript has a historical behavior:

```javascript
typeof null
```

returns:

```text
"object"
```

Therefore:

```typescript
function process(
    value:
        | object
        | null
) {
    if (
        typeof value === "object"
    ) {
        // value may still be null
    }
}
```

You often need:

```typescript
if (
    value !== null &&
    typeof value === "object"
) {
    // value → object
}
```

---

# 8. Equality Narrowing

Equality comparisons can narrow types.

Example:

```typescript
function compare(
    a: string | number,
    b: string
) {
    if (a === b) {
        // a must be string here
        console.log(
            a.toUpperCase()
        );
    }
}
```

Why?

Because:

```text
b
→ string

a === b
```

means `a` must also be compatible with `string` in that branch.

---

# 9. Literal Equality Narrowing

```typescript
type Status =
    "loading"
    | "success"
    | "error";

function handle(
    status: Status
) {
    if (
        status === "loading"
    ) {
        // status → "loading"
    }
}
```

The union:

```text
"loading" | "success" | "error"
```

has been narrowed to:

```text
"loading"
```

---

# 10. `null` Narrowing

Consider:

```typescript
function greet(
    name: string | null
) {
    if (
        name !== null
    ) {
        console.log(
            name.toUpperCase()
        );
    }
}
```

Inside:

```text
name
→ string
```

because:

```text
null
```

has been eliminated.

---

# 11. `undefined` Narrowing

```typescript
function print(
    value:
        string | undefined
) {
    if (
        value !== undefined
    ) {
        console.log(
            value.toUpperCase()
        );
    }
}
```

Inside:

```text
value
→ string
```

---

# 12. `null` and `undefined` Together

```typescript
function print(
    value:
        string
        | null
        | undefined
) {
    if (
        value !== null &&
        value !== undefined
    ) {
        console.log(
            value.toUpperCase()
        );
    }
}
```

Inside:

```text
value
→ string
```

---

# 13. `value != null` Pattern

JavaScript's loose inequality can sometimes intentionally handle both:

```text
null
undefined
```

Example:

```typescript
function print(
    value:
        string
        | null
        | undefined
) {
    if (value != null) {
        // value → string
    }
}
```

Because:

```javascript
null == undefined
```

is true.

This is one of the few cases where:

```text
!= null
```

can be an intentional pattern.

However, explicit checks are often easier to understand.

---

# 14. Truthiness Narrowing

JavaScript conditions evaluate values as:

```text
truthy
```

or:

```text
falsy
```

Example:

```typescript
function greet(
    name:
        string | null
) {
    if (name) {
        console.log(
            name.toUpperCase()
        );
    }
}
```

Inside the `if`, TypeScript knows `name` is not `null`.

---

# 15. Falsy Values

Common JavaScript falsy values include:

```text
false

0

-0

0n

""

null

undefined

NaN
```

This matters when narrowing.

---

# 16. Truthiness Narrowing Can Hide Valid Values

Suppose:

```typescript
function printLength(
    value: string | null
) {
    if (value) {
        console.log(
            value.length
        );
    }
}
```

This excludes:

```text
null
```

but also:

```text
""
```

An empty string may be valid.

If you only want to exclude `null`, prefer:

```typescript
if (value !== null) {
    console.log(
        value.length
    );
}
```

---

# 17. Number Truthiness Problem

```typescript
function process(
    count:
        number | undefined
) {
    if (count) {
        // count is truthy
    }
}
```

This excludes:

```text
0
```

even though `0` may be a valid number.

If you only care about `undefined`:

```typescript
if (
    count !== undefined
) {
    // count → number
}
```

---

# 18. `in` Operator Narrowing

JavaScript's:

```javascript
in
```

operator checks whether a property exists in an object.

TypeScript can use this to narrow object unions.

Example:

```typescript
type Admin = {
    name: string;
    permissions: string[];
};

type User = {
    name: string;
    email: string;
};
```

Function:

```typescript
function process(
    person: Admin | User
) {
    if (
        "permissions" in person
    ) {
        // person → Admin
    } else {
        // person → User
    }
}
```

---

# 19. `in` Operator Example

```typescript
type Dog = {
    bark(): void;
};

type Cat = {
    meow(): void;
};

function speak(
    animal: Dog | Cat
) {
    if (
        "bark" in animal
    ) {
        animal.bark();
    } else {
        animal.meow();
    }
}
```

TypeScript understands which object shape remains possible.

---

# 20. `instanceof` Narrowing

JavaScript's:

```javascript
instanceof
```

checks whether an object was created through a particular constructor/prototype chain.

Example:

```typescript
function format(
    value:
        Date | string
) {
    if (
        value instanceof Date
    ) {
        return value.toISOString();
    }

    return value.toUpperCase();
}
```

Inside:

```text
if
→ Date

else
→ string
```

---

# 21. `instanceof` with Classes

```typescript
class Dog {
    bark() {
        console.log("Woof");
    }
}

class Cat {
    meow() {
        console.log("Meow");
    }
}
```

Then:

```typescript
function speak(
    animal: Dog | Cat
) {
    if (
        animal instanceof Dog
    ) {
        animal.bark();
    } else {
        animal.meow();
    }
}
```

---

# 22. `instanceof` Is a Runtime Check

Unlike interfaces:

```typescript
interface User {
    name: string;
}
```

classes exist at runtime.

Therefore:

```typescript
value instanceof SomeClass
```

can perform a real runtime check.

Interfaces cannot be used with `instanceof` because interfaces disappear during compilation.

Invalid:

```typescript
interface User {
    name: string;
}

value instanceof User;
```

`User` does not exist at runtime.

---

# 23. Assignment Narrowing

Assignments can also affect narrowing.

Example:

```typescript
let value:
    string | number;

value = "Hello";
```

At this point TypeScript knows:

```text
value
→ string
```

Then:

```typescript
value.toUpperCase();
```

works.

---

# 24. Assignment Can Change the Narrowed Type

```typescript
let value:
    string | number;

value = "Hello";

// value → string

value = 100;

// value → number
```

TypeScript tracks the control flow.

---

# 25. Return-Based Narrowing

Consider:

```typescript
function print(
    value:
        string | null
) {
    if (
        value === null
    ) {
        return;
    }

    console.log(
        value.toUpperCase()
    );
}
```

After:

```typescript
return;
```

TypeScript knows execution continues only when:

```text
value !== null
```

Therefore:

```text
value
→ string
```

after the condition.

---

# 26. Early Return Pattern

Instead of:

```typescript
function process(
    user:
        User | null
) {
    if (user) {
        // huge block
    }
}
```

you can use:

```typescript
function process(
    user:
        User | null
) {
    if (!user) {
        return;
    }

    // user → User

    console.log(
        user.name
    );
}
```

This often makes code easier to read.

---

# 27. Throw-Based Narrowing

```typescript
function process(
    user:
        User | null
) {
    if (!user) {
        throw new Error(
            "User not found"
        );
    }

    // user → User

    console.log(
        user.name
    );
}
```

Because execution cannot continue after the `throw` in the failing branch.

---

# 28. Discriminated Unions

One of TypeScript's most important patterns is the:

```text
Discriminated Union
```

Example:

```typescript
type LoadingState = {
    status: "loading";
};

type SuccessState = {
    status: "success";
    data: string[];
};

type ErrorState = {
    status: "error";
    message: string;
};
```

Combine:

```typescript
type State =
    | LoadingState
    | SuccessState
    | ErrorState;
```

The property:

```text
status
```

is called the:

```text
discriminant
```

or:

```text
discriminator
```

---

# 29. Narrowing Discriminated Unions

```typescript
function render(
    state: State
) {
    if (
        state.status ===
        "loading"
    ) {
        // state → LoadingState
    }

    if (
        state.status ===
        "success"
    ) {
        console.log(
            state.data
        );
    }

    if (
        state.status ===
        "error"
    ) {
        console.log(
            state.message
        );
    }
}
```

TypeScript connects the literal `status` value with the corresponding object structure.

---

# 30. Why Discriminated Unions Are Powerful

Without them:

```typescript
type State = {
    loading: boolean;
    data?: string[];
    error?: string;
};
```

This allows strange combinations:

```typescript
{
    loading: true,
    data: ["A"],
    error: "Failed"
}
```

What state is this?

With discriminated unions:

```typescript
type State =
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: string[];
    }
    | {
        status: "error";
        message: string;
    };
```

each valid state has a precise structure.

---

# 31. `switch` Narrowing

Discriminated unions work very well with:

```typescript
switch
```

Example:

```typescript
function render(
    state: State
) {
    switch (state.status) {
        case "loading":
            return "Loading...";

        case "success":
            return state.data.join(
                ", "
            );

        case "error":
            return state.message;
    }
}
```

Each `case` narrows `state`.

---

# 32. React State Preview

Later in React, instead of:

```typescript
type State = {
    loading: boolean;
    data?: User[];
    error?: string;
};
```

we can model:

```typescript
type State =
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: User[];
    }
    | {
        status: "error";
        error: string;
    };
```

Then React rendering becomes safer:

```typescript
if (
    state.status === "success"
) {
    return (
        <UserList
            users={state.data}
        />
    );
}
```

TypeScript knows `data` exists in the success state.

---

# 33. Custom Type Guards

Sometimes built-in checks are not enough.

We can create our own function that tells TypeScript when a value has a specific type.

Example:

```typescript
type User = {
    id: number;
    name: string;
};
```

Custom guard:

```typescript
function isUser(
    value: unknown
): value is User {
    // runtime checks
}
```

The special syntax:

```typescript
value is User
```

is called a:

```text
Type Predicate
```

---

# 34. Type Predicate

General syntax:

```typescript
function isSomething(
    value: unknown
): value is SomeType {
    return condition;
}
```

Example:

```typescript
function isString(
    value: unknown
): value is string {
    return (
        typeof value ===
        "string"
    );
}
```

Usage:

```typescript
const value:
    unknown =
    "Hello";

if (isString(value)) {
    // value → string

    console.log(
        value.toUpperCase()
    );
}
```

---

# 35. Custom Object Type Guard

```typescript
type User = {
    id: number;
    name: string;
};
```

Guard:

```typescript
function isUser(
    value: unknown
): value is User {
    if (
        typeof value !==
            "object" ||
        value === null
    ) {
        return false;
    }

    return (
        "id" in value &&
        "name" in value &&
        typeof value.id ===
            "number" &&
        typeof value.name ===
            "string"
    );
}
```

Usage:

```typescript
const data:
    unknown =
    getData();

if (isUser(data)) {
    console.log(
        data.name
    );
}
```

Inside the branch:

```text
data
→ User
```

---

# 36. Type Guards Must Actually Validate

This is technically possible:

```typescript
function isUser(
    value: unknown
): value is User {
    return true;
}
```

TypeScript trusts the predicate.

But this is unsafe.

A custom type guard is a promise to the compiler that your runtime logic correctly establishes the type.

Therefore:

```text
Type Predicate
+
Incorrect Runtime Check
=
False Type Safety
```

---

# 37. Narrowing `unknown`

`unknown` is especially useful for external or uncertain values.

Example:

```typescript
function process(
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

You cannot directly use an `unknown` value.

You must first prove what it is.

---

# 38. `unknown` vs `any`

With:

```typescript
function process(
    value: any
) {
    value.foo.bar();
}
```

TypeScript allows unsafe operations.

With:

```typescript
function process(
    value: unknown
) {
    value.foo;
}
```

TypeScript rejects the operation until you narrow the value.

Therefore:

```text
unknown
→ "I don't know what this is yet."

any
→ "Stop type checking this."
```

Prefer `unknown` for uncertain data.

---

# 39. Narrowing API Data

Suppose:

```typescript
const data:
    unknown =
    await response.json();
```

Then:

```typescript
if (isUser(data)) {
    console.log(
        data.name
    );
}
```

This architecture is safer than:

```typescript
const data =
    await response.json()
        as User;
```

because the guard performs actual runtime checks.

---

# 40. Type Guard for Primitive Union

```typescript
function isNumber(
    value:
        string | number
): value is number {
    return (
        typeof value ===
        "number"
    );
}
```

Usage:

```typescript
function process(
    value:
        string | number
) {
    if (isNumber(value)) {
        // number
        value.toFixed(2);
    } else {
        // string
        value.toUpperCase();
    }
}
```

---

# 41. Type Guard with Arrays

```typescript
function isStringArray(
    value: unknown
): value is string[] {
    return (
        Array.isArray(value) &&
        value.every(
            item =>
                typeof item ===
                "string"
        )
    );
}
```

Usage:

```typescript
if (
    isStringArray(data)
) {
    data.map(
        value =>
            value.toUpperCase()
    );
}
```

---

# 42. `Array.isArray()` Narrowing

TypeScript understands:

```typescript
Array.isArray(value)
```

Example:

```typescript
function process(
    value:
        string | string[]
) {
    if (
        Array.isArray(value)
    ) {
        // value → string[]
    } else {
        // value → string
    }
}
```

This is useful when arrays are part of unions.

---

# 43. Array Filtering Problem

Consider:

```typescript
const values:
    (
        string
        | undefined
    )[] = [
        "Alice",
        undefined,
        "Bob"
    ];
```

We want:

```text
string[]
```

A type guard can make the intention explicit.

---

# 44. Filtering with a Type Predicate

```typescript
const values:
    (
        string
        | undefined
    )[] = [
        "Alice",
        undefined,
        "Bob"
    ];
```

Guard:

```typescript
function isDefined<T>(
    value:
        T | undefined
): value is T {
    return (
        value !== undefined
    );
}
```

Then:

```typescript
const names =
    values.filter(
        isDefined
    );
```

Type:

```text
string[]
```

---

# 45. Generic Defined Guard

We can support both:

```text
null
undefined
```

with:

```typescript
function isDefined<T>(
    value:
        T | null | undefined
): value is T {
    return (
        value !== null &&
        value !== undefined
    );
}
```

Then:

```typescript
const users =
    possibleUsers.filter(
        isDefined
    );
```

can remove nullable values from the resulting type.

---

# 46. Assertion Functions

TypeScript also supports functions that assert a condition.

Syntax:

```typescript
function assert(
    condition: unknown
): asserts condition {
    if (!condition) {
        throw new Error(
            "Assertion failed"
        );
    }
}
```

Usage:

```typescript
const user:
    User | null =
    getUser();

assert(user);

// user → User
```

Because if `user` were falsy, the function would throw.

---

# 47. `asserts condition`

Example:

```typescript
function assert(
    condition: unknown
): asserts condition {
    if (!condition) {
        throw new Error(
            "Assertion failed"
        );
    }
}
```

Then:

```typescript
const value:
    string | null =
    getValue();

assert(value);

value.toUpperCase();
```

After the assertion:

```text
value
→ string
```

---

# 48. Assertion Function with Type Predicate

We can also write:

```typescript
function assertString(
    value: unknown
): asserts value is string {
    if (
        typeof value !==
        "string"
    ) {
        throw new Error(
            "Expected string"
        );
    }
}
```

Usage:

```typescript
const value:
    unknown =
    getValue();

assertString(value);

// value → string

console.log(
    value.toUpperCase()
);
```

---

# 49. Type Guard vs Assertion Function

Type guard:

```typescript
function isUser(
    value: unknown
): value is User {
    return condition;
}
```

Usage:

```typescript
if (isUser(value)) {
    // User
}
```

Assertion function:

```typescript
function assertUser(
    value: unknown
): asserts value is User {
    if (!condition) {
        throw new Error();
    }
}
```

Usage:

```typescript
assertUser(value);

// User from here onward
```

---

# 50. When to Use a Type Guard

Use a type guard when failure is a normal branch.

Example:

```typescript
if (isUser(data)) {
    processUser(data);
} else {
    showInvalidData();
}
```

---

# 51. When to Use an Assertion Function

Use an assertion function when invalid data means execution should stop.

Example:

```typescript
assertUser(data);

processUser(data);
```

If `data` is invalid:

```text
throw error
```

If execution continues:

```text
data is User
```

---

# 52. Exhaustive Narrowing

Suppose:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

We handle all cases:

```typescript
function handle(
    status: Status
) {
    switch (status) {
        case "loading":
            break;

        case "success":
            break;

        case "error":
            break;
    }
}
```

After every possible case is handled, no possibilities remain.

TypeScript represents an impossible value with:

```text
never
```

---

# 53. `never`

`never` represents a value that should never occur.

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

The function never successfully returns.

---

# 54. Exhaustiveness Checking with `never`

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

Function:

```typescript
function handle(
    status: Status
) {
    switch (status) {
        case "loading":
            return;

        case "success":
            return;

        case "error":
            return;

        default: {
            const impossible:
                never =
                status;

            return impossible;
        }
    }
}
```

Because all cases are handled:

```text
status
→ never
```

inside `default`.

---

# 55. Why Exhaustive Checking Is Useful

Later we add:

```typescript
type Status =
    "loading"
    | "success"
    | "error"
    | "cancelled";
```

But forget to update the switch.

Now:

```typescript
const impossible:
    never =
    status;
```

fails because:

```text
status
→ "cancelled"
```

rather than:

```text
never
```

The compiler tells us a case is missing.

---

# 56. `assertNever` Pattern

Another common pattern:

```typescript
function assertNever(
    value: never
): never {
    throw new Error(
        `Unexpected value: ${value}`
    );
}
```

Then:

```typescript
function handle(
    status: Status
) {
    switch (status) {
        case "loading":
            return;

        case "success":
            return;

        case "error":
            return;

        default:
            return assertNever(
                status
            );
    }
}
```

Adding a new union member without handling it produces a compile-time error.

---

# 57. Exhaustive Discriminated Union

```typescript
type Result =
    | {
        type: "success";
        data: string;
    }
    | {
        type: "error";
        message: string;
    };
```

Then:

```typescript
function process(
    result: Result
) {
    switch (result.type) {
        case "success":
            return result.data;

        case "error":
            return result.message;

        default:
            return assertNever(
                result
            );
    }
}
```

This is extremely useful in production code.

---

# 58. Narrowing Nested Properties

```typescript
type Response =
    | {
        success: true;
        data: string;
    }
    | {
        success: false;
        error: string;
    };
```

Then:

```typescript
function process(
    response: Response
) {
    if (response.success) {
        console.log(
            response.data
        );
    } else {
        console.log(
            response.error
        );
    }
}
```

The boolean literal:

```text
true | false
```

acts as a discriminator.

---

# 59. Boolean Discriminants

A discriminator does not have to be a string.

Example:

```typescript
type Result =
    | {
        success: true;
        data: User;
    }
    | {
        success: false;
        error: string;
    };
```

TypeScript can narrow based on:

```typescript
if (result.success) {
    // success branch
}
```

---

# 60. Numeric Discriminants

You can also use numeric literals:

```typescript
type Result =
    | {
        code: 200;
        data: string;
    }
    | {
        code: 404;
        message: string;
    };
```

Then:

```typescript
if (
    result.code === 200
) {
    // success shape
}
```

String discriminants are usually the most readable, but the principle is the same.

---

# 61. Property Checks vs Discriminants

This works:

```typescript
if (
    "error" in result
) {
    // error branch
}
```

But if you control the type design, this is often clearer:

```typescript
if (
    result.status ===
    "error"
) {
    // error branch
}
```

Explicit discriminants usually make state models easier to understand.

---

# 62. Optional Properties Can Complicate Narrowing

Consider:

```typescript
type Bird = {
    fly(): void;
};

type Human = {
    fly?: () => void;
};
```

Checking:

```typescript
"fly" in value
```

does not necessarily mean the value must be `Bird`, because `Human` can also have a `fly` property.

Narrowing depends on the actual type definitions.

---

# 63. Narrowing Does Not Always Produce One Type

Example:

```typescript
type Fish = {
    swim(): void;
};

type Bird = {
    fly(): void;
};

type Human = {
    swim?: () => void;
    fly?: () => void;
};
```

Checking:

```typescript
if ("swim" in animal) {
    // Fish | Human may remain possible
}
```

because both types may contain `swim`.

Type guards narrow based on what the check logically proves.

---

# 64. Narrowing with Logical AND

```typescript
function print(
    value:
        string | null
) {
    if (
        value &&
        value.length > 0
    ) {
        // value → string
    }
}
```

The second condition is evaluated only when `value` is truthy.

TypeScript understands this flow.

---

# 65. Narrowing with Logical OR

```typescript
function print(
    value:
        string | null
) {
    if (
        value === null ||
        value.length === 0
    ) {
        return;
    }

    // value → string
}
```

If execution reaches the bottom:

```text
value
is not null
```

---

# 66. Narrowing with Optional Chaining

Optional chaining:

```typescript
user?.name
```

helps safely access nullable values.

However, optional chaining is not always equivalent to narrowing the variable for later unrelated statements.

Example:

```typescript
user?.print();
```

means:

```text
Call print only if user exists.
```

It does not mean:

```text
user can never be null afterward.
```

---

# 67. Narrowing Can Be Lost After Assignment

```typescript
let value:
    string | number =
    "Hello";

if (
    typeof value === "string"
) {
    // value → string

    value = 100;

    // value → number
}
```

TypeScript tracks assignments and updates its understanding.

---

# 68. Narrowing Mutable Properties

Be careful when narrowing mutable object properties.

Example:

```typescript
if (
    user.name !== null
) {
    // user.name narrowed
}
```

Complex mutations, callbacks, aliases, or assignments can affect what TypeScript can safely preserve.

A local constant can sometimes make intent clearer:

```typescript
const name =
    user.name;

if (name !== null) {
    // name → string
}
```

---

# 69. Type Guard Reuse

Instead of repeating:

```typescript
typeof value === "object" &&
value !== null &&
"id" in value &&
"name" in value
```

throughout the application, create:

```typescript
function isUser(
    value: unknown
): value is User {
    // validation
}
```

Then reuse:

```typescript
if (isUser(value)) {
    // User
}
```

This centralizes runtime validation logic.

---

# 70. Type Guard Naming

Common naming conventions:

```text
isUser

isAdmin

isString

isNumber

isDefined

isError

hasPermission
```

The:

```text
is...
```

prefix clearly communicates that the function checks a condition/type.

---

# 71. Assertion Function Naming

Common names:

```text
assertUser

assertDefined

assertString

assertPermission

assertAuthenticated
```

The:

```text
assert...
```

prefix communicates:

```text
If this condition is false,
execution will fail.
```

---

# 72. Guarding Error Values

In JavaScript/TypeScript, caught values may be treated as `unknown` under strict configurations.

Example:

```typescript
try {
    // ...
} catch (error) {
    if (
        error instanceof Error
    ) {
        console.log(
            error.message
        );
    }
}
```

This is safer than blindly assuming every thrown value is an `Error`.

---

# 73. Why Catch Values Can Be Unknown

JavaScript allows:

```javascript
throw new Error("Failed");
```

but also:

```javascript
throw "Failed";
```

or:

```javascript
throw 500;
```

Therefore a caught value is not guaranteed to be:

```text
Error
```

Narrow before using Error-specific properties.

---

# 74. Error Guard Example

```typescript
function isError(
    value: unknown
): value is Error {
    return (
        value instanceof Error
    );
}
```

Then:

```typescript
catch (error) {
    if (isError(error)) {
        console.log(
            error.message
        );
    }
}
```

---

# 75. Narrowing Function Parameters

```typescript
function normalize(
    value:
        string | number
): string {
    if (
        typeof value === "number"
    ) {
        return value.toString();
    }

    return value;
}
```

TypeScript understands:

```text
number branch
→ number

remaining branch
→ string
```

---

# 76. Narrowing with Multiple Types

```typescript
function process(
    value:
        | string
        | number
        | boolean
) {
    if (
        typeof value === "string"
    ) {
        // string
        return;
    }

    if (
        typeof value === "number"
    ) {
        // number
        return;
    }

    // boolean
}
```

Each branch eliminates possibilities.

---

# 77. Control Flow Elimination

Initial:

```text
string | number | boolean
```

After:

```typescript
if (
    typeof value === "string"
) {
    return;
}
```

remaining:

```text
number | boolean
```

After:

```typescript
if (
    typeof value === "number"
) {
    return;
}
```

remaining:

```text
boolean
```

TypeScript reasons by eliminating impossible types.

---

# 78. Narrowing Is Better Than Blind Casting

Avoid:

```typescript
function process(
    value:
        string | number
) {
    const text =
        value as string;

    return text.toUpperCase();
}
```

If `value` is actually a number, this can fail.

Prefer:

```typescript
function process(
    value:
        string | number
) {
    if (
        typeof value === "string"
    ) {
        return value.toUpperCase();
    }

    return value.toString();
}
```

---

# 79. API Response Example

```typescript
type ApiResult<T> =
    | {
        status: "success";
        data: T;
    }
    | {
        status: "error";
        message: string;
    };
```

Then:

```typescript
function handle<T>(
    result:
        ApiResult<T>
) {
    if (
        result.status ===
        "success"
    ) {
        return result.data;
    }

    console.error(
        result.message
    );
}
```

This is a very common production pattern.

---

# 80. Authentication State Example

```typescript
type AuthState =
    | {
        status:
            "unauthenticated";
    }
    | {
        status:
            "authenticated";
        user: User;
    };
```

Then:

```typescript
if (
    auth.status ===
    "authenticated"
) {
    console.log(
        auth.user.name
    );
}
```

No optional `user` is necessary.

The state itself guarantees whether `user` exists.

---

# 81. Better State Modeling

Avoid:

```typescript
type AuthState = {
    authenticated: boolean;
    user?: User;
};
```

because this permits:

```typescript
{
    authenticated: true
}
```

without a user.

Better:

```typescript
type AuthState =
    | {
        status:
            "unauthenticated";
    }
    | {
        status:
            "authenticated";
        user: User;
    };
```

Now invalid states are harder to represent.

---

# 82. Loading State Example

```typescript
type DataState<T> =
    | {
        status: "idle";
    }
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: T;
    }
    | {
        status: "error";
        error: Error;
    };
```

This pattern will become extremely useful in React.

---

# 83. Narrowing Generic State

```typescript
function render<T>(
    state:
        DataState<T>
) {
    switch (state.status) {
        case "idle":
            return;

        case "loading":
            return;

        case "success":
            console.log(
                state.data
            );
            return;

        case "error":
            console.error(
                state.error
            );
            return;
    }
}
```

TypeScript narrows the generic union correctly.

---

# 84. Runtime Checks and Static Types

The key relationship:

```text
Runtime JavaScript Check
          │
          ▼
TypeScript observes the check
          │
          ▼
Compiler narrows static type
```

Example:

```typescript
typeof value === "string"
```

does two things:

```text
Runtime
→ checks actual value

Compile Time
→ narrows value to string
```

---

# 85. Narrowing Does Not Change the Value

Before:

```text
value
→ string | number
```

After:

```typescript
if (
    typeof value === "string"
)
```

inside:

```text
value
→ string
```

But TypeScript did not transform the value.

It simply learned more about the value from the runtime condition.

---

# 86. Type Guard vs Type Assertion

Type guard:

```typescript
if (
    typeof value === "string"
) {
    // proven at runtime
}
```

Assertion:

```typescript
const text =
    value as string;
```

Difference:

```text
Guard
→ evidence

Assertion
→ promise
```

Prefer evidence whenever possible.

---

# 87. Type Predicate vs Assertion

Predicate:

```typescript
function isUser(
    value: unknown
): value is User
```

returns:

```text
boolean
```

and lets the caller branch.

Assertion:

```typescript
function assertUser(
    value: unknown
): asserts value is User
```

returns normally only if the value satisfies the condition; otherwise it should throw.

---

# 88. Narrowing with `typeof function`

```typescript
function execute(
    value:
        string
        | (() => void)
) {
    if (
        typeof value ===
        "function"
    ) {
        value();
    } else {
        console.log(value);
    }
}
```

Inside the function branch:

```text
value
→ () => void
```

---

# 89. Narrowing Symbols

```typescript
function process(
    value:
        string | symbol
) {
    if (
        typeof value ===
        "symbol"
    ) {
        // symbol
    } else {
        // string
    }
}
```

`typeof` works with other primitive categories as well.

---

# 90. Narrowing BigInt

```typescript
function process(
    value:
        number | bigint
) {
    if (
        typeof value ===
        "bigint"
    ) {
        // bigint
    } else {
        // number
    }
}
```

---

# 91. Avoid Unnecessary Type Guards

If TypeScript already knows:

```typescript
const name: string =
    "Alice";
```

there is no reason to write:

```typescript
if (
    typeof name === "string"
) {
    // ...
}
```

Type guards are useful when the original type actually contains multiple possibilities or uncertain data.

---

# 92. Avoid Guarding `any`

```typescript
function process(
    value: any
) {
    if (
        typeof value ===
        "string"
    ) {
        // ...
    }
}
```

Although runtime checking still works, starting with:

```typescript
unknown
```

provides much stronger compiler protection outside the guard.

Prefer:

```typescript
function process(
    value: unknown
) {
    // narrow before use
}
```

---

# 93. Common Mistake — Truthiness for Numbers

Avoid:

```typescript
if (count) {
    // ...
}
```

when:

```text
0
```

is valid.

Prefer:

```typescript
if (
    count !== undefined
) {
    // ...
}
```

if `undefined` is what you intend to exclude.

---

# 94. Common Mistake — Truthiness for Strings

Avoid truthiness narrowing when:

```text
""
```

is valid.

Be precise about what you actually want to exclude.

---

# 95. Common Mistake — Forgetting `typeof null`

Remember:

```javascript
typeof null === "object"
```

So:

```typescript
typeof value === "object"
```

does not automatically prove the value is non-null.

Use:

```typescript
value !== null &&
typeof value === "object"
```

when necessary.

---

# 96. Common Mistake — Using Interface with `instanceof`

Invalid:

```typescript
interface User {}

value instanceof User;
```

Interfaces do not exist at runtime.

Use:

```text
class

runtime property checks

custom type guards

schema validation
```

depending on the situation.

---

# 97. Common Mistake — Lying in a Type Predicate

Bad:

```typescript
function isUser(
    value: unknown
): value is User {
    return true;
}
```

The compiler trusts you.

Therefore custom predicates must correctly represent runtime logic.

---

# 98. Common Mistake — Assertion Instead of Guard

Avoid:

```typescript
const user =
    data as User;
```

when `data` is untrusted.

Prefer:

```typescript
if (isUser(data)) {
    // safely narrowed
}
```

or runtime schema validation for complex data.

---

# 99. Common Mistake — Optional Everything

Avoid modeling states like:

```typescript
type State = {
    loading?: boolean;
    data?: User[];
    error?: string;
};
```

This creates many invalid combinations.

Prefer discriminated unions when properties depend on state.

---

# 100. Common Mistake — Missing Exhaustive Cases

If a union represents important application states, use exhaustive handling where appropriate.

That way adding:

```text
cancelled
```

or:

```text
expired
```

can cause compile-time errors in code that has not been updated.

---

# 101. Interview Question — What Is Type Narrowing?

Type narrowing is TypeScript's process of reducing a broad type into a more specific type based on runtime checks and control flow.

Example:

```typescript
if (
    typeof value === "string"
) {
    // value → string
}
```

---

# 102. Interview Question — What Is a Type Guard?

A type guard is a runtime condition that allows TypeScript to determine a more specific type.

Examples:

```text
typeof

instanceof

in

equality checks

custom type predicates
```

---

# 103. Interview Question — What Is Control Flow Analysis?

Control flow analysis is TypeScript's ability to track possible types through:

```text
conditions

returns

throws

assignments

branches

switch statements
```

to determine which types are possible at each location.

---

# 104. Interview Question — What Is a Type Predicate?

A type predicate has syntax:

```typescript
parameter is Type
```

Example:

```typescript
function isUser(
    value: unknown
): value is User {
    // runtime validation
}
```

It tells TypeScript that when the function returns `true`, the parameter can be treated as `User`.

---

# 105. Interview Question — What Is a Discriminated Union?

A discriminated union is a union of object types that share a property containing distinct literal values.

Example:

```typescript
type Result =
    | {
        status: "success";
        data: string;
    }
    | {
        status: "error";
        message: string;
    };
```

The `status` property allows TypeScript to determine which union member is active.

---

# 106. Interview Question — What Is `never`?

`never` represents values that cannot occur.

It is commonly used for:

```text
Functions that never return

Impossible branches

Exhaustive union checking
```

---

# 107. Interview Question — Type Guard vs Assertion?

A type guard provides runtime evidence:

```typescript
typeof value === "string"
```

An assertion tells the compiler what to assume:

```typescript
value as string
```

Guards are safer when runtime uncertainty exists.

---

# 108. Interview Question — `unknown` vs `any`?

```text
unknown
→ value is uncertain
→ must narrow before use

any
→ disables most type checking
→ can be used freely
```

Prefer `unknown` for uncertain external values.

---

# 109. Interview Question — What Is an Assertion Function?

An assertion function has a return signature such as:

```typescript
asserts condition
```

or:

```typescript
asserts value is Type
```

It tells TypeScript that if the function returns normally, the asserted condition/type is true.

---

# 110. Interview Question — Why Use Discriminated Unions?

They make invalid states harder to represent and allow TypeScript to narrow dependent properties safely.

They are especially useful for:

```text
API results

Async states

Authentication states

UI states

Events

Reducers

State machines
```

---

# 111. Cheat Sheet

```text
TYPE NARROWING
================================

Broad type
→ runtime check
→ specific type


TYPEOF
================================

if (
    typeof value === "string"
) {
    // string
}


NULL CHECK
================================

if (value !== null) {
    // null removed
}


UNDEFINED CHECK
================================

if (
    value !== undefined
) {
    // undefined removed
}


TRUTHINESS
================================

if (value) {
    // falsy possibilities removed
}


IN OPERATOR
================================

if ("permissions" in user) {
    // object union narrowed
}


INSTANCEOF
================================

if (
    value instanceof Date
) {
    // Date
}


ARRAY
================================

if (
    Array.isArray(value)
) {
    // array
}


DISCRIMINATED UNION
================================

type Result =
    | {
        status: "success";
        data: string;
    }
    | {
        status: "error";
        message: string;
    };


NARROW
================================

if (
    result.status ===
    "success"
) {
    result.data;
}


TYPE PREDICATE
================================

function isUser(
    value: unknown
): value is User {
    // runtime checks
}


ASSERTION FUNCTION
================================

function assertUser(
    value: unknown
): asserts value is User {
    // validate or throw
}


NEVER
================================

Impossible type


EXHAUSTIVENESS
================================

default:
    return assertNever(
        value
    );


UNKNOWN
================================

unknown
→ narrow
→ use safely


CORE RULE
================================

Guard
→ prove the type

Assertion
→ claim the type
```

---

# 112. Important Rules to Remember

```text
1. Narrowing reduces broad types to specific types.

2. Narrowing is based on runtime checks and control flow.

3. TypeScript performs control flow analysis.

4. typeof is a built-in type guard.

5. typeof works well with primitive types.

6. typeof null returns "object".

7. Object checks may need an explicit null check.

8. Equality checks can narrow types.

9. Literal equality can narrow literal unions.

10. null can be removed with explicit checks.

11. undefined can be removed with explicit checks.

12. Truthiness narrowing removes falsy possibilities.

13. Truthiness is not always the same as null checking.

14. 0 is falsy.

15. Empty string is falsy.

16. false is falsy.

17. Be precise when valid falsy values exist.

18. The in operator can narrow object unions.

19. in checks property existence at runtime.

20. Optional properties can limit how far in narrows.

21. instanceof can narrow class instances.

22. instanceof requires runtime constructors.

23. Interfaces cannot be used with instanceof.

24. Interfaces disappear during compilation.

25. Classes exist at runtime.

26. Array.isArray can narrow arrays.

27. Assignments affect narrowing.

28. TypeScript tracks assignment flow.

29. Early returns can eliminate union members.

30. Throws can eliminate union members.

31. switch statements support narrowing.

32. Discriminated unions are a major TypeScript pattern.

33. Discriminated unions use shared literal properties.

34. String discriminants are commonly used.

35. Boolean literals can also discriminate unions.

36. Numeric literals can also discriminate unions.

37. Discriminated unions make invalid states harder to represent.

38. Avoid excessive optional properties for state modeling.

39. Model dependent properties with union variants.

40. Custom type guards use type predicates.

41. Type predicate syntax is parameter is Type.

42. Type predicates must perform correct runtime checks.

43. TypeScript trusts custom predicates.

44. Incorrect predicates create false safety.

45. unknown should be narrowed before use.

46. unknown is safer than any for uncertain values.

47. any largely bypasses type checking.

48. External data should often begin as unknown.

49. Runtime validation can narrow external data.

50. Type assertions are not validation.

51. Guards provide evidence.

52. Assertions provide promises.

53. Prefer evidence when uncertainty exists.

54. Generic type guards can be reusable.

55. isDefined is useful for nullable collections.

56. Type predicates can improve filter result types.

57. Assertion functions use asserts.

58. asserts condition narrows based on a condition.

59. asserts value is Type narrows to a specific type.

60. Assertion functions should fail when their assertion is false.

61. Type guards are useful when failure is a normal branch.

62. Assertion functions are useful when failure should stop execution.

63. never represents impossible values.

64. never can be used for exhaustive checking.

65. Exhaustive checking catches missing union cases.

66. Adding a union member can expose unhandled code paths.

67. assertNever is a common exhaustive-checking helper.

68. API results are good candidates for discriminated unions.

69. Authentication state is a good candidate for discriminated unions.

70. Async UI state is a good candidate for discriminated unions.

71. React reducers benefit from discriminated unions.

72. Event systems benefit from discriminated unions.

73. State machines benefit from discriminated unions.

74. Runtime checks do not transform values.

75. Narrowing changes the compiler's knowledge.

76. Narrowing does not change runtime values.

77. typeof performs an actual JavaScript runtime operation.

78. instanceof performs an actual JavaScript runtime operation.

79. in performs an actual JavaScript runtime operation.

80. Equality checks are runtime operations.

81. Type predicates wrap runtime logic with TypeScript information.

82. Assertions do not provide runtime evidence.

83. Narrowing can be lost after reassignment.

84. Mutable values require careful reasoning.

85. TypeScript continuously updates its understanding through control flow.

86. Multiple branches can progressively eliminate union members.

87. Remaining possibilities determine the narrowed type.

88. Early-return patterns often simplify narrowing.

89. Avoid unnecessary type guards for already-known types.

90. Avoid starting uncertain values as any.

91. Catch values should be narrowed before assuming Error.

92. JavaScript can throw values other than Error objects.

93. instanceof Error is useful for caught errors.

94. Property existence is not always enough to identify one union member.

95. Design discriminants when you control the data model.

96. Good type design makes narrowing easier.

97. Good narrowing reduces the need for assertions.

98. Good narrowing makes union types practical.

99. Type safety is strongest when runtime reality and static types agree.

100. Narrow first, assert only when necessary.
```

---

# 113. Final Mental Model

```text
             BROAD TYPE

       string | number | null

                 │
                 ▼

          Runtime Check

    typeof / equality / in
    instanceof / custom guard

                 │
                 ▼

        TypeScript analyzes
          control flow

                 │
        ┌────────┴────────┐
        ▼                 ▼

     string             number

       or                 or

       null              etc.
```

For object states:

```text
                 State
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼

     loading    success      error
        │          │           │
        │          │           │
        ▼          ▼           ▼

     status     status       status
    "loading"  "success"    "error"

                   │
                   ▼

             TypeScript uses
             the discriminator

                   │
                   ▼

            Correct object shape
```

The most important principle is:

```text
Do not tell TypeScript
what something is
when you can prove
what something is.
```

Prefer:

```typescript
if (
    typeof value === "string"
) {
    value.toUpperCase();
}
```

over blindly writing:

```typescript
(
    value as string
).toUpperCase();
```

And for complex state:

```typescript
type State =
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: User[];
    }
    | {
        status: "error";
        error: Error;
    };
```

is usually much safer than:

```typescript
type State = {
    loading: boolean;
    data?: User[];
    error?: Error;
};
```

because:

```text
Good type design
        +
Good narrowing
        =
Much stronger TypeScript safety
```
