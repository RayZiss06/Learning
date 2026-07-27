# Type Annotations & Type Inference

TypeScript needs to know the **type of values** in our program so it can determine whether operations are safe.

There are two primary ways TypeScript learns a type:

```text
1. Type Annotation
   → We explicitly tell TypeScript the type.

2. Type Inference
   → TypeScript determines the type automatically.
```

Example:

```typescript
// Explicit annotation
let username: string = "Alice";

// Type inference
let age = 25;
```

In both cases, TypeScript knows the variable's type.

---

# 1. Type Annotation

A **type annotation** explicitly tells TypeScript what type a value should have.

Syntax:

```typescript
let variableName: type;
```

Example:

```typescript
let username: string;
```

With initialization:

```typescript
let username: string = "Alice";
```

Here:

```text
username
    │
    ▼
string
```

TypeScript now expects `username` to contain strings.

---

# 2. Basic Type Annotations

## String

```typescript
let username: string = "Alice";
```

---

## Number

```typescript
let age: number = 25;
```

JavaScript does not have separate TypeScript types such as:

```text
integer
float
double
```

Ordinary JavaScript numbers use:

```typescript
number
```

Example:

```typescript
let integer: number = 10;

let decimal: number = 10.5;

let negative: number = -20;

let infinity: number = Infinity;

let notANumber: number = NaN;
```

---

## Boolean

```typescript
let active: boolean = true;
```

---

## BigInt

```typescript
let largeNumber: bigint =
    12345678901234567890n;
```

---

## Symbol

```typescript
let id: symbol =
    Symbol("id");
```

---

# 3. Invalid Assignment

Once a variable has a type:

```typescript
let username: string =
    "Alice";
```

this is invalid:

```typescript
username = 100;
```

TypeScript reports:

```text
Type 'number' is not assignable
to type 'string'.
```

Valid:

```typescript
username = "Bob";
```

---

# 4. Annotation Without Initialization

You can declare the type before assigning a value.

```typescript
let username: string;

username = "Alice";
```

Later:

```typescript
username = "Bob";
```

is valid.

But:

```typescript
username = 100;
```

is not.

---

# 5. Why Annotations Matter

Consider:

```typescript
let userId: number;
```

We have established a contract:

```text
userId
   │
   ▼
number
```

Any future assignment must satisfy that contract.

```typescript
userId = 1001;
```

Valid.

```typescript
userId = "1001";
```

Invalid.

---

# 6. Type Inference

Type annotations are not always necessary.

TypeScript can often determine the type automatically.

```typescript
let username = "Alice";
```

TypeScript infers:

```text
username
    │
    ▼
string
```

Therefore:

```typescript
username = "Bob";
```

works.

But:

```typescript
username = 100;
```

does not.

---

# 7. Number Inference

```typescript
let age = 25;
```

TypeScript infers:

```text
age
 │
 ▼
number
```

Therefore:

```typescript
age = 30;
```

works.

But:

```typescript
age = "Thirty";
```

does not.

---

# 8. Boolean Inference

```typescript
let active = true;
```

TypeScript infers:

```text
active
   │
   ▼
boolean
```

Therefore:

```typescript
active = false;
```

works.

But:

```typescript
active = "yes";
```

does not.

---

# 9. Annotation vs Inference

Explicit:

```typescript
let username: string =
    "Alice";
```

Inferred:

```typescript
let username =
    "Alice";
```

Both result in TypeScript treating `username` as:

```text
string
```

Therefore, writing:

```typescript
: string
```

is often unnecessary when the type is obvious.

---

# 10. Prefer Inference for Obvious Values

Instead of:

```typescript
const username: string =
    "Alice";

const age: number =
    25;

const active: boolean =
    true;
```

prefer:

```typescript
const username =
    "Alice";

const age =
    25;

const active =
    true;
```

TypeScript already understands the types.

This keeps code cleaner.

---

# 11. When Explicit Annotations Are Useful

Annotations become useful when:

```text
1. A variable is declared before assignment.

2. Function parameters need contracts.

3. Return types should be explicit.

4. A variable may contain multiple types.

5. Object structures need contracts.

6. Public APIs should expose clear types.

7. Inference produces a type different from the intended contract.

8. You want TypeScript to verify an implementation against a specific boundary.
```

---

# 12. `let` vs `const` Inference

There is an important difference.

```typescript
let status = "success";
```

TypeScript generally infers:

```text
string
```

because `status` can later change.

```typescript
status = "failed";
```

Valid.

---

With `const`:

```typescript
const status = "success";
```

TypeScript can infer the literal type:

```text
"success"
```

rather than merely:

```text
string
```

because the variable cannot be reassigned.

This becomes important when we study **Literal Types**.

---

# 13. Literal Type Inference

```typescript
const role = "admin";
```

TypeScript can know:

```text
role
 │
 ▼
"admin"
```

not just:

```text
string
```

But:

```typescript
let role = "admin";
```

normally becomes:

```text
string
```

because later:

```typescript
role = "user";
```

is possible.

---

# 14. Function Parameter Annotations

JavaScript:

```javascript
function greet(name) {
    return `Hello ${name}`;
}
```

TypeScript:

```typescript
function greet(
    name: string
) {
    return `Hello ${name}`;
}
```

Now TypeScript knows:

```text
name
 │
 ▼
string
```

Valid:

```typescript
greet("Alice");
```

Invalid:

```typescript
greet(100);
```

---

# 15. Why Function Parameters Usually Need Types

Consider:

```typescript
function add(a, b) {
    return a + b;
}
```

Under strict settings, TypeScript can report that `a` and `b` implicitly have `any` types.

Instead:

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}
```

Now the function contract is clear:

```text
number
   │
   ▼
   a

number
   │
   ▼
   b
```

---

# 16. Multiple Parameter Types

```typescript
function createUser(
    username: string,
    age: number,
    active: boolean
) {
    // ...
}
```

Usage:

```typescript
createUser(
    "Alice",
    25,
    true
);
```

Invalid:

```typescript
createUser(
    25,
    "Alice",
    true
);
```

TypeScript checks arguments by their expected parameter types.

---

# 17. Function Return Type Annotation

Syntax:

```typescript
function functionName(): ReturnType {
    // ...
}
```

Example:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

Here:

```text
a
→ number

b
→ number

return
→ number
```

---

# 18. Return Type Inference

Return types can often be inferred.

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}
```

TypeScript sees:

```typescript
a + b
```

and infers:

```text
return type
    │
    ▼
number
```

So explicitly writing:

```typescript
: number
```

is not always required.

---

# 19. Why Explicit Return Types Can Still Be Useful

Consider:

```typescript
function calculatePrice(
    price: number,
    quantity: number
): number {
    return price * quantity;
}
```

The explicit return type creates a contract.

If someone accidentally changes:

```typescript
return price * quantity;
```

to:

```typescript
return "Error";
```

TypeScript immediately reports that the implementation violates the declared return type.

---

# 20. Return Type as an Implementation Check

Without explicit return type:

```typescript
function getUserId() {
    return "100";
}
```

TypeScript simply infers:

```text
string
```

But maybe the function is supposed to return:

```text
number
```

If we declare:

```typescript
function getUserId(): number {
    return "100";
}
```

TypeScript catches the mistake.

So explicit return types can help enforce **intent**, especially at important boundaries.

---

# 21. Arrow Function Parameter Types

JavaScript:

```javascript
const add = (a, b) =>
    a + b;
```

TypeScript:

```typescript
const add = (
    a: number,
    b: number
): number => {
    return a + b;
};
```

Or:

```typescript
const add = (
    a: number,
    b: number
): number => a + b;
```

---

# 22. Arrow Function Return Inference

You can allow TypeScript to infer the return type:

```typescript
const add = (
    a: number,
    b: number
) => a + b;
```

TypeScript infers:

```text
(a: number, b: number)
        │
        ▼
      number
```

---

# 23. Function Expression Types

You can type the entire function variable:

```typescript
const add:
    (
        a: number,
        b: number
    ) => number =
    (a, b) => {
        return a + b;
    };
```

Notice something important:

```typescript
(a, b)
```

does not need annotations inside the implementation.

TypeScript already knows their types from the function type.

This is **contextual typing**.

---

# 24. Contextual Typing

TypeScript can infer types based on the context where a value appears.

Example:

```typescript
const multiply:
    (
        a: number,
        b: number
    ) => number =
    (a, b) => a * b;
```

TypeScript understands:

```text
a → number

b → number

return → number
```

from the surrounding function type.

---

# 25. Contextual Typing in Callbacks

Suppose:

```typescript
const numbers = [
    10,
    20,
    30
];
```

Then:

```typescript
numbers.map(
    number =>
        number * 2
);
```

You do **not** need:

```typescript
numbers.map(
    (
        number: number
    ) =>
        number * 2
);
```

TypeScript knows that `numbers` contains numbers.

Therefore the callback parameter must be a number.

---

# 26. Contextual Typing Is Extremely Important

You will see this constantly with:

```text
Array callbacks

Event handlers

Promises

React events

React props

Higher-order functions

Library callbacks
```

The surrounding API often provides enough type information for TypeScript to infer callback parameters.

---

# 27. Array Type Annotation

There are two common syntaxes.

## Syntax 1

```typescript
const numbers: number[] = [
    1,
    2,
    3
];
```

## Syntax 2

```typescript
const numbers:
    Array<number> = [
        1,
        2,
        3
    ];
```

Both represent:

```text
Array containing numbers
```

---

# 28. Array Type Inference

Usually:

```typescript
const numbers = [
    1,
    2,
    3
];
```

is enough.

TypeScript infers:

```text
number[]
```

Similarly:

```typescript
const users = [
    "Alice",
    "Bob"
];
```

becomes:

```text
string[]
```

---

# 29. Mixed Array Inference

Consider:

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

Meaning:

```text
Array whose elements can be

number
OR
string
```

We will study union types separately.

---

# 30. Empty Array and Context

An empty array has little information:

```typescript
const users = [];
```

The exact inferred behavior can depend on context and compiler settings.

For meaningful application state, it is often useful to provide the intended element type.

Example:

```typescript
type User = {
    id: number;
    name: string;
};

const users: User[] = [];
```

Now TypeScript knows what the array is intended to contain.

---

# 31. Object Type Annotation

You can directly describe an object:

```typescript
const user: {
    name: string;
    age: number;
} = {
    name: "Alice",
    age: 25
};
```

TypeScript now expects:

```text
user
│
├── name → string
│
└── age  → number
```

---

# 32. Object Type Inference

Usually this:

```typescript
const user = {
    name: "Alice",
    age: 25
};
```

is enough.

TypeScript infers roughly:

```typescript
{
    name: string;
    age: number;
}
```

So this works:

```typescript
user.name =
    "Bob";
```

But:

```typescript
user.name =
    100;
```

does not.

---

# 33. Missing Properties

If a type requires:

```typescript
const user: {
    name: string;
    age: number;
}
```

then:

```typescript
const user: {
    name: string;
    age: number;
} = {
    name: "Alice"
};
```

is invalid because:

```text
age
```

is required.

---

# 34. Extra Properties

TypeScript performs specific **excess property checks** on object literals.

Example:

```typescript
type User = {
    name: string;
};

const user: User = {
    name: "Alice",
    age: 25
};
```

TypeScript reports that `age` is not part of the expected object type.

However, structural typing has additional rules when values first exist in variables.

We will explore those rules when studying object types, aliases, and interfaces.

---

# 35. Optional Parameters

Sometimes a function parameter is not required.

Use:

```typescript
?
```

Example:

```typescript
function greet(
    name: string,
    title?: string
) {
    // ...
}
```

Both are valid:

```typescript
greet("Alice");

greet(
    "Alice",
    "Developer"
);
```

---

# 36. Optional Parameter Meaning

This:

```typescript
title?: string
```

means the parameter may be absent.

Inside the function, TypeScript effectively has to consider:

```text
string
OR
undefined
```

Therefore:

```typescript
function greet(
    name: string,
    title?: string
) {
    console.log(
        title.toUpperCase()
    );
}
```

can produce an error because `title` may be undefined.

---

# 37. Handling Optional Parameters

We can check first:

```typescript
function greet(
    name: string,
    title?: string
) {
    if (title) {
        console.log(
            title.toUpperCase()
        );
    }

    console.log(name);
}
```

TypeScript understands that inside:

```typescript
if (title)
```

the value has been narrowed.

We will study narrowing later.

---

# 38. Optional Parameters Must Follow Required Parameters

Valid:

```typescript
function createUser(
    name: string,
    age?: number
) {}
```

Problematic:

```typescript
function createUser(
    age?: number,
    name: string
) {}
```

A required parameter cannot normally follow an optional parameter.

Think:

```text
Required
Required
Optional
Optional
```

rather than:

```text
Optional
Required
```

---

# 39. Default Parameters

JavaScript already supports default parameters.

TypeScript adds type checking around them.

```typescript
function greet(
    name: string =
        "Guest"
) {
    return `Hello ${name}`;
}
```

Usage:

```typescript
greet();

greet("Alice");
```

Both work.

---

# 40. Default Parameter Type Inference

TypeScript can infer the type from the default value.

```typescript
function greet(
    name = "Guest"
) {
    return `Hello ${name}`;
}
```

TypeScript infers:

```text
name → string
```

So:

```typescript
greet(100);
```

is invalid.

---

# 41. Explicit Default Parameter Type

You can still write:

```typescript
function greet(
    name: string =
        "Guest"
) {
    return `Hello ${name}`;
}
```

But if the default already clearly communicates the type:

```typescript
name = "Guest"
```

the explicit annotation may be redundant.

---

# 42. Optional vs Default Parameter

Optional:

```typescript
function greet(
    name?: string
) {}
```

Inside:

```text
name
→ string | undefined
```

Default:

```typescript
function greet(
    name = "Guest"
) {}
```

Inside the function, omitted/`undefined` input gets replaced by `"Guest"`, so `name` is treated as a string.

This is an important distinction.

---

# 43. Rest Parameter Types

JavaScript:

```javascript
function sum(...numbers) {
    // ...
}
```

TypeScript:

```typescript
function sum(
    ...numbers: number[]
): number {
    return numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
}
```

The rest parameter:

```typescript
...numbers: number[]
```

means:

```text
Any number of arguments

Each argument
→ number
```

---

# 44. Calling Typed Rest Parameters

Valid:

```typescript
sum(
    10,
    20,
    30,
    40
);
```

Invalid:

```typescript
sum(
    10,
    "20",
    30
);
```

because every argument must satisfy:

```text
number
```

---

# 45. Rest Parameters with Strings

```typescript
function combine(
    ...values: string[]
): string {
    return values.join(" ");
}
```

Usage:

```typescript
combine(
    "TypeScript",
    "is",
    "great"
);
```

---

# 46. Return Type `void`

When a function does not return a meaningful value:

```typescript
function logMessage(
    message: string
): void {
    console.log(message);
}
```

`void` communicates that callers should not expect a useful return value from the function.

---

# 47. Inferred `void`

You do not always need to write:

```typescript
: void
```

Example:

```typescript
function logMessage(
    message: string
) {
    console.log(message);
}
```

TypeScript can infer the function's return behavior.

We will study `void` more carefully in the TypeScript Types section.

---

# 48. Callback Parameter Types

Suppose:

```typescript
function processUser(
    callback:
        (
            name: string
        ) => void
) {
    callback("Alice");
}
```

The callback must be compatible with:

```text
Input
→ string

Return
→ void
```

Usage:

```typescript
processUser(
    name => {
        console.log(name);
    }
);
```

TypeScript automatically knows:

```text
name
→ string
```

through contextual typing.

---

# 49. Do Not Re-Annotate Contextually Typed Callbacks

Unnecessary:

```typescript
processUser(
    (
        name: string
    ): void => {
        console.log(name);
    }
);
```

Usually prefer:

```typescript
processUser(
    name => {
        console.log(name);
    }
);
```

The callback contract already provides the information.

---

# 50. Promise Return Inference

Consider:

```typescript
async function getUsername() {
    return "Alice";
}
```

TypeScript infers:

```text
Promise<string>
```

because async functions always return promises.

Explicit:

```typescript
async function getUsername():
    Promise<string> {
    return "Alice";
}
```

Both are valid.

---

# 51. Explicit Async Return Types

For important API/service functions, explicit return types can make contracts clearer.

```typescript
async function getUserCount():
    Promise<number> {
    return 100;
}
```

If someone changes:

```typescript
return 100;
```

to:

```typescript
return "100";
```

TypeScript reports the mismatch.

---

# 52. Inference from Expressions

TypeScript can infer results from expressions.

```typescript
const firstName = "Alice";

const lastName = "Smith";

const fullName =
    `${firstName} ${lastName}`;
```

TypeScript infers:

```text
fullName
→ string
```

Similarly:

```typescript
const price = 100;

const quantity = 5;

const total =
    price * quantity;
```

TypeScript infers:

```text
total
→ number
```

---

# 53. Inference Through Function Calls

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}

const result =
    add(10, 20);
```

TypeScript knows:

```text
add()
→ number
```

Therefore:

```text
result
→ number
```

Type information flows through the program.

---

# 54. Inference Through Object Properties

```typescript
const user = {
    name: "Alice",
    age: 25
};

const username =
    user.name;
```

TypeScript infers:

```text
username
→ string
```

because:

```text
user.name
→ string
```

---

# 55. Inference Through Arrays

```typescript
const numbers = [
    10,
    20,
    30
];

const first =
    numbers[0];
```

With ordinary settings, TypeScript commonly treats `first` as:

```text
number
```

However, an array lookup can actually return `undefined` at runtime if the index does not exist.

TypeScript can model this more strictly using:

```json
{
    "compilerOptions": {
        "noUncheckedIndexedAccess": true
    }
}
```

Then indexed access may become:

```text
number | undefined
```

This is an example of compiler configuration affecting type safety.

---

# 56. Inference Is Not Runtime Inspection

TypeScript does not run your application to determine types.

It analyzes the source code.

```typescript
const age = 25;
```

TypeScript can statically determine:

```text
number
```

before execution.

This is static analysis.

---

# 57. Type Widening

Consider:

```typescript
let status = "success";
```

Initially the value is:

```text
"success"
```

but because `status` can be reassigned, TypeScript usually widens the type to:

```text
string
```

This allows:

```typescript
status = "failed";
```

---

# 58. Widening with Numbers

```typescript
let count = 10;
```

TypeScript usually infers:

```text
number
```

not:

```text
10
```

because:

```typescript
count = 20;
```

should be allowed.

---

# 59. `const` and Narrower Inference

```typescript
const status = "success";
```

Since reassignment is impossible, TypeScript can preserve:

```text
"success"
```

Similarly:

```typescript
const count = 10;
```

can have literal type:

```text
10
```

This distinction becomes useful with literal types and discriminated unions.

---

# 60. `const` Object Properties Are Still Mutable

Remember JavaScript behavior:

```typescript
const user = {
    role: "admin"
};
```

Although `user` cannot be reassigned:

```typescript
user.role = "member";
```

is valid.

Therefore TypeScript generally infers:

```text
user.role
→ string
```

rather than:

```text
"admin"
```

because the property can change.

---

# 61. `as const`

If we write:

```typescript
const user = {
    role: "admin"
} as const;
```

TypeScript preserves a narrower readonly representation.

Conceptually:

```text
user.role
→ "admin"

and the property is readonly
```

We will study `as const` properly in **Type Assertions**.

---

# 62. Type Inference and `null`

With strict null checking enabled:

```typescript
let user = null;
```

TypeScript may infer the value based on its context as `null`.

If the variable is intended to later hold a user, describe that explicitly.

Example:

```typescript
type User = {
    name: string;
};

let user:
    User | null =
    null;
```

This tells TypeScript the actual intended possibilities.

---

# 63. Annotation When Initial Value Is Not Enough

This is one of the most important reasons to annotate.

Suppose:

```typescript
let result = null;
```

The initializer alone does not express:

```text
What should result
contain later?
```

If the intention is:

```text
number OR null
```

write:

```typescript
let result:
    number | null =
    null;
```

The annotation communicates the intended future state.

---

# 64. Delayed Initialization

Suppose:

```typescript
let username: string;

if (someCondition) {
    username = "Alice";
}
```

Then later:

```typescript
console.log(username);
```

TypeScript can detect that `username` may be used before assignment.

This is part of TypeScript's control-flow analysis.

---

# 65. Inference Helps Prevent Type Drift

Suppose:

```typescript
const price = 100;

const tax = 20;

const total =
    price + tax;
```

TypeScript knows the entire chain is numeric.

If later:

```typescript
const tax = "20";
```

then the behavior of:

```typescript
price + tax
```

changes.

TypeScript's inferred types help surface these kinds of mismatches through the surrounding contracts and usages.

---

# 66. Explicit Types as Documentation

Consider:

```typescript
function fetchUser(
    id: number
): Promise<User> {
    // ...
}
```

Without reading the implementation, we know:

```text
Input
→ number

Output
→ Promise<User>
```

This makes TypeScript types useful as executable documentation.

---

# 67. Public Boundaries

Explicit types are particularly valuable around boundaries such as:

```text
Exported functions

Public APIs

Service methods

Reusable libraries

Component props

API responses

Shared utilities

Configuration objects
```

Example:

```typescript
export function calculateTotal(
    price: number,
    quantity: number
): number {
    return price * quantity;
}
```

The contract is immediately visible.

---

# 68. Local Implementation vs Public Contract

Inside a small implementation:

```typescript
const total =
    price * quantity;
```

inference is usually ideal.

At a public boundary:

```typescript
function calculateTotal(
    price: number,
    quantity: number
): number
```

explicit typing may be valuable.

A useful principle:

```text
Local implementation
→ Prefer inference

Important boundary
→ Consider explicit contract
```

---

# 69. `any` Can Destroy Inference

Consider:

```typescript
let data: any =
    "Alice";

const result =
    data.toUpperCase();
```

TypeScript allows many operations because:

```text
data
→ any
```

Later:

```typescript
data = 100;

data.toUpperCase();
```

TypeScript may not protect you from this operation because `any` disables much of the checking.

We will study `any` in the next section.

---

# 70. `unknown` Preserves Safety

Compare:

```typescript
let data: unknown =
    "Alice";
```

You cannot immediately do:

```typescript
data.toUpperCase();
```

because TypeScript does not know whether `data` is actually a string.

You must first prove it:

```typescript
if (
    typeof data ===
    "string"
) {
    data.toUpperCase();
}
```

We will study `unknown` and narrowing later.

---

# 71. Avoid Wrapper Object Types

Prefer:

```typescript
string
number
boolean
symbol
bigint
```

rather than:

```typescript
String
Number
Boolean
Symbol
BigInt
```

Example:

```typescript
let username: string =
    "Alice";
```

not:

```typescript
let username: String =
    "Alice";
```

The lowercase types represent ordinary primitive values and are the types you normally want.

---

# 72. `String` vs `string`

```typescript
string
```

represents primitive strings.

```typescript
String
```

refers to the wrapper object type.

In normal TypeScript application code:

```text
Use
→ string

Avoid
→ String
```

The same principle applies to:

```text
number vs Number

boolean vs Boolean
```

---

# 73. Annotation Does Not Convert Values

This is critical.

You cannot write:

```typescript
const age: number =
    "25";
```

and expect TypeScript to convert `"25"` into `25`.

Type annotations do not perform runtime conversion.

If conversion is needed:

```typescript
const input =
    "25";

const age =
    Number(input);
```

Then TypeScript infers:

```text
age
→ number
```

---

# 74. Type Annotation Is Not Validation

This:

```typescript
const age: number =
    getAge();
```

means TypeScript believes `getAge()` returns a number based on its static type.

It does not mean runtime data has been validated.

Remember:

```text
Type Annotation
≠
Runtime Validation

Type Annotation
≠
Runtime Conversion
```

---

# 75. Type Inference Can Cross Multiple Operations

```typescript
function getPrice() {
    return 100;
}

const price =
    getPrice();

const tax =
    price * 0.18;

const total =
    price + tax;
```

TypeScript infers:

```text
getPrice()
→ number

price
→ number

tax
→ number

total
→ number
```

Types flow through expressions.

---

# 76. Context Can Flow Backward

Type information does not only flow from values outward.

Context can also tell TypeScript what a value should be.

Example:

```typescript
const handler:
    (
        value: string
    ) => void =
    value => {
        console.log(
            value.toUpperCase()
        );
    };
```

Even though:

```typescript
value
```

has no explicit annotation inside the arrow function, the surrounding function type provides it.

This is contextual typing.

---

# 77. Inference in `map()`

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

const names =
    users.map(
        user =>
            user.name
    );
```

TypeScript can infer:

```text
user
→ {
    id: number;
    name: string;
}

names
→ string[]
```

No explicit annotations are needed.

---

# 78. Inference in `filter()`

```typescript
const numbers = [
    1,
    2,
    3,
    4
];

const evenNumbers =
    numbers.filter(
        number =>
            number % 2 === 0
    );
```

TypeScript knows:

```text
number
→ number

evenNumbers
→ number[]
```

from the original array.

---

# 79. Inference in `reduce()`

```typescript
const numbers = [
    1,
    2,
    3
];

const total =
    numbers.reduce(
        (
            accumulator,
            number
        ) =>
            accumulator +
            number,
        0
    );
```

TypeScript can infer:

```text
accumulator
→ number

number
→ number

total
→ number
```

Contextual typing removes unnecessary annotations.

---

# 80. Inference with Destructuring

JavaScript:

```javascript
const {
    name,
    age
} = user;
```

TypeScript can infer destructured property types from `user`.

```typescript
const user = {
    name: "Alice",
    age: 25
};

const {
    name,
    age
} = user;
```

TypeScript knows:

```text
name
→ string

age
→ number
```

No need to relearn destructuring.

TypeScript simply propagates the existing type information.

---

# 81. Typed Destructured Function Parameters

Suppose:

```typescript
type User = {
    name: string;
    age: number;
};
```

Then:

```typescript
function printUser(
    {
        name,
        age
    }: User
) {
    console.log(
        name,
        age
    );
}
```

The annotation belongs to the entire destructured parameter.

Not:

```typescript
function printUser({
    name: string,
    age: number
}) {}
```

That would mean something different in JavaScript destructuring syntax.

---

# 82. Correct Destructuring Mental Model

```typescript
function printUser(
    {
        name,
        age
    }: User
) {}
```

Think:

```text
{
    name,
    age
}
    │
    ▼
Entire parameter
has type User
```

Then TypeScript infers:

```text
name → string
age  → number
```

---

# 83. Return Type Inference with Multiple Returns

```typescript
function getValue(
    success: boolean
) {
    if (success) {
        return "Done";
    }

    return 0;
}
```

TypeScript can infer a return type compatible with:

```text
string
OR
number
```

This becomes a union type:

```typescript
string | number
```

We will study unions in detail later.

---

# 84. Inference and Missing Return Paths

Consider:

```typescript
function findUser(
    found: boolean
) {
    if (found) {
        return "Alice";
    }
}
```

One path returns:

```text
string
```

Another reaches the end and returns:

```text
undefined
```

So under strict null checking, TypeScript can model the return as:

```text
string | undefined
```

This is another example of control-flow-based inference.

---

# 85. Explicit Return Type Can Catch Missing Paths

Suppose the contract says:

```typescript
function findUser(
    found: boolean
): string {
    if (found) {
        return "Alice";
    }
}
```

TypeScript can report that the function does not return a string on every path.

This is one reason explicit return types can be valuable for important functions.

---

# 86. Inference vs Annotation Decision

Ask:

```text
Can TypeScript clearly infer
the intended type?
```

If yes:

```text
Prefer inference.
```

If no:

```text
Add annotation.
```

If the type represents an important contract:

```text
Consider explicit annotation
even if inference is possible.
```

---

# 87. Bad TypeScript Style

Avoid:

```typescript
const username: string =
    "Alice";

const age: number =
    25;

const active: boolean =
    true;

const scores: number[] = [
    10,
    20,
    30
];
```

when all types are completely obvious and no contract is being clarified.

It creates noise without adding much safety.

---

# 88. Better TypeScript Style

```typescript
const username =
    "Alice";

const age =
    25;

const active =
    true;

const scores = [
    10,
    20,
    30
];
```

TypeScript already knows:

```text
username → "Alice"
age      → 25
active   → true
scores   → number[]
```

The exact literal-vs-widened inference depends on context and mutability, but the key point is that explicit annotations are unnecessary here.

---

# 89. Where Explicit Typing Becomes Valuable

Example:

```typescript
type User = {
    id: number;
    name: string;
};

function getUser(
    id: number
): User {
    return {
        id,
        name: "Alice"
    };
}
```

Here the types define meaningful contracts.

This is more valuable than annotating obvious local primitives.

---

# 90. Common Mistake — Assuming Annotation Changes Runtime

```typescript
let age: number;
```

does not create a special runtime numeric variable.

After compilation, it may simply become JavaScript variable code.

The annotation exists for TypeScript.

---

# 91. Common Mistake — Using `any` Instead of Typing

Avoid:

```typescript
function createUser(
    user: any
) {
    return user.name;
}
```

just because TypeScript complains.

This effectively disables checking for `user`.

Instead, describe the actual expected shape.

```typescript
type User = {
    name: string;
};

function createUser(
    user: User
) {
    return user.name;
}
```

---

# 92. Common Mistake — Duplicate Types Everywhere

Avoid unnecessary repetition:

```typescript
const numbers: number[] = [
    1,
    2,
    3
];

numbers.map(
    (
        number: number
    ): number => {
        return number * 2;
    }
);
```

Better:

```typescript
const numbers = [
    1,
    2,
    3
];

numbers.map(
    number =>
        number * 2
);
```

TypeScript already knows the entire chain.

---

# 93. Common Mistake — Overusing Return Annotations

Not every tiny local function needs:

```typescript
(): number
```

Example:

```typescript
const double = (
    value: number
) => value * 2;
```

The return type is obvious.

Explicit return types are most valuable when they express a meaningful contract or catch accidental implementation drift.

---

# 94. Common Mistake — Under-Typing Boundaries

The opposite problem is leaving important inputs unclear.

For example:

```typescript
function saveUser(user) {
    // ...
}
```

With strict TypeScript this should be properly typed.

```typescript
function saveUser(
    user: User
): void {
    // ...
}
```

Public boundaries should communicate what they accept.

---

# 95. Common Mistake — Confusing Optional and Nullable

Optional:

```typescript
name?: string
```

means the value/property/parameter may be absent or undefined depending on context.

Nullable:

```typescript
name: string | null
```

means the value exists in the modeled shape but can explicitly be `null`.

These are not identical.

We will study `null`, `undefined`, optional properties, and unions separately.

---

# 96. Common Mistake — Capitalized Primitive Types

Avoid:

```typescript
let name: String;

let age: Number;

let active: Boolean;
```

Prefer:

```typescript
let name: string;

let age: number;

let active: boolean;
```

---

# 97. Common Mistake — Fighting Inference

If TypeScript already knows:

```typescript
const users = [
    "Alice",
    "Bob"
];
```

you do not gain much by forcing:

```typescript
const users: string[] = [
    "Alice",
    "Bob"
];
```

unless the annotation is communicating a broader intended contract.

Use the type system rather than manually repeating everything it already knows.

---

# 98. Interview Question — What Is a Type Annotation?

A type annotation explicitly declares the expected type of a value.

```typescript
let age: number = 25;
```

Here:

```typescript
: number
```

is the type annotation.

---

# 99. Interview Question — What Is Type Inference?

Type inference is TypeScript's ability to determine types automatically from the code.

```typescript
const age = 25;
```

TypeScript infers the type without requiring an explicit annotation.

---

# 100. Interview Question — Annotation vs Inference?

Annotation:

```typescript
let age: number = 25;
```

The developer specifies the type.

Inference:

```typescript
let age = 25;
```

TypeScript determines the type.

Both participate in the same type-checking system.

---

# 101. Interview Question — Should We Annotate Everything?

No.

Prefer inference when the type is obvious.

Use annotations where they:

```text
Define contracts

Clarify intent

Describe future possibilities

Type function parameters

Protect public boundaries

Constrain implementations
```

---

# 102. Interview Question — What Is Contextual Typing?

Contextual typing occurs when TypeScript determines a value's type from the surrounding context.

Example:

```typescript
const handler:
    (
        value: string
    ) => void =
    value => {
        console.log(value);
    };
```

`value` is inferred as `string` from the function type.

---

# 103. Interview Question — Why Type Function Parameters?

Because TypeScript often cannot safely determine what arbitrary callers are intended to pass into a standalone function.

Parameter annotations define the function's input contract.

```typescript
function add(
    a: number,
    b: number
) {}
```

---

# 104. Interview Question — Are Return Types Required?

No.

TypeScript can often infer return types.

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}
```

TypeScript infers:

```text
number
```

Explicit return types are useful when you want to enforce or document a contract.

---

# 105. Interview Question — Optional Parameter Syntax?

```typescript
function greet(
    name?: string
) {}
```

The:

```text
?
```

marks the parameter as optional.

---

# 106. Interview Question — How Do You Type Rest Parameters?

```typescript
function sum(
    ...numbers: number[]
) {}
```

This means the function accepts any number of numeric arguments.

---

# 107. Interview Question — What Is Type Widening?

Type widening is when TypeScript generalizes a narrow literal value into a broader type where mutation/reassignment requires it.

Example:

```typescript
let status = "success";
```

typically becomes:

```text
string
```

rather than remaining:

```text
"success"
```

---

# 108. Interview Question — Why Can `const` Infer Literal Types?

Because:

```typescript
const status = "success";
```

cannot be reassigned.

TypeScript can therefore preserve the specific literal:

```text
"success"
```

in many contexts.

---

# 109. Interview Question — Does Annotation Convert Data?

No.

```typescript
const age: number =
    "25";
```

is invalid.

Use runtime conversion when needed:

```typescript
const age =
    Number("25");
```

---

# 110. Interview Question — `string` vs `String`?

Use:

```typescript
string
```

for primitive string values.

`String` refers to the wrapper object type and should generally not be used for ordinary string annotations.

---

# 111. Type Annotation Cheat Sheet

```text
VARIABLE
================================

let name: string;

let age: number;

let active: boolean;


INITIALIZATION
================================

let name: string =
    "Alice";


INFERENCE
================================

let name = "Alice";

TypeScript infers:

string


FUNCTION PARAMETERS
================================

function add(
    a: number,
    b: number
) {}


RETURN TYPE
================================

function add(
    a: number,
    b: number
): number {
    return a + b;
}


ARROW FUNCTION
================================

const add = (
    a: number,
    b: number
): number =>
    a + b;


OPTIONAL PARAMETER
================================

function greet(
    name?: string
) {}


DEFAULT PARAMETER
================================

function greet(
    name = "Guest"
) {}


REST PARAMETER
================================

function sum(
    ...numbers: number[]
) {}


ARRAY
================================

number[]

string[]

Array<number>


OBJECT
================================

{
    name: string;
    age: number;
}


VOID
================================

function log(): void {}


ASYNC
================================

async function get():
    Promise<string> {}


CONTEXTUAL TYPING
================================

const handler:
    (value: string) => void =
    value => {
        // value inferred string
    };


PREFER
================================

const age = 25;


OVER
================================

const age: number = 25;

when the type is obvious.


IMPORTANT
================================

Annotation
≠
Conversion

Annotation
≠
Runtime Validation
```

---

# 112. Important Rules to Remember

```text
1. Type annotations explicitly specify types.

2. Type inference determines types automatically.

3. TypeScript does not require annotations everywhere.

4. Prefer inference when the type is obvious.

5. Use annotations when they communicate meaningful intent.

6. Function parameters commonly require explicit types.

7. Function return types can usually be inferred.

8. Explicit return types can enforce contracts.

9. Public boundaries benefit from clear types.

10. Local implementation details often benefit from inference.

11. string represents primitive strings.

12. number represents ordinary JavaScript numbers.

13. boolean represents boolean values.

14. bigint represents BigInt values.

15. symbol represents Symbol values.

16. Avoid String for ordinary string annotations.

17. Avoid Number for ordinary number annotations.

18. Avoid Boolean for ordinary boolean annotations.

19. Type annotations do not convert values.

20. Type annotations do not perform runtime validation.

21. Type inference is static analysis.

22. TypeScript does not need to run the program to infer types.

23. let values are often widened.

24. const values can preserve literal types.

25. const object properties can still be mutable.

26. Object property inference considers mutability.

27. Array element types can be inferred.

28. Mixed arrays can produce union element types.

29. Empty collections may need an explicit intended type.

30. Object structures can be explicitly annotated.

31. Object structures can also be inferred.

32. Missing required properties cause type errors.

33. Object literals can undergo excess property checking.

34. Optional parameters use ?.

35. Optional parameters may be undefined.

36. Required parameters normally precede optional parameters.

37. Default parameters can have inferred types.

38. Default parameters differ from optional parameters.

39. Rest parameters are typed as arrays or tuple-like forms.

40. ...numbers: number[] means every collected argument is a number.

41. void represents no meaningful return value for callers.

42. void can often be inferred.

43. Async functions return Promise-based types.

44. Promise return types can be inferred.

45. Contextual typing derives types from surrounding context.

46. Callback parameters are frequently contextually typed.

47. Array callbacks usually do not need repeated annotations.

48. map() callbacks receive types from the array.

49. filter() callbacks receive types from the array.

50. reduce() often receives types from its source and initializer.

51. Destructured variables inherit types from their source.

52. Destructured function parameters type the whole parameter pattern.

53. Type information flows through expressions.

54. Type information flows through function returns.

55. Type information flows through property access.

56. Type information flows through callbacks.

57. Context can also provide type information backward into expressions.

58. Inference can produce union types.

59. Multiple return paths affect inferred return types.

60. Missing return paths can introduce undefined.

61. Explicit return contracts can reveal missing returns.

62. Type widening allows mutable variables to accept broader values.

63. const often enables narrower inference.

64. Literal types will be studied separately.

65. Union types will be studied separately.

66. Narrowing will be studied separately.

67. any weakens type safety.

68. unknown preserves uncertainty safely.

69. Avoid using any merely to remove errors.

70. An annotation should describe reality.

71. Do not lie to the type system.

72. Runtime data still requires runtime handling.

73. Compiler configuration can affect inferred safety.

74. noUncheckedIndexedAccess makes indexed access safer.

75. Array indexing can return undefined at runtime.

76. TypeScript can model possible undefined values.

77. Inference reduces repetitive type declarations.

78. Excessive annotation creates unnecessary noise.

79. Too little typing at boundaries creates unclear contracts.

80. Good TypeScript balances explicit contracts and inference.

81. TypeScript types are documentation that the compiler can check.

82. Important service functions often benefit from explicit return types.

83. Exported functions often benefit from clear contracts.

84. Reusable library APIs should expose intentional types.

85. React props will later use these same principles.

86. React event callbacks heavily use contextual typing.

87. Generic functions will build on inference.

88. Type annotations exist for the type checker.

89. They do not create special runtime variables.

90. TypeScript ultimately executes using JavaScript runtime semantics.
```

---

# 113. Final Mental Model

```text
                TYPE INFORMATION
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼

   TYPE ANNOTATION             TYPE INFERENCE

Developer tells             TypeScript figures
TypeScript the type.        out the type.

          │                         │
          └────────────┬────────────┘
                       │
                       ▼
                TYPE CHECKER
                       │
                       ▼
              Is this operation
                    safe?
```

Example:

```typescript
let username: string =
    "Alice";
```

```text
Developer
   │
   ▼
"This is string"
```

Versus:

```typescript
let username =
    "Alice";
```

```text
TypeScript
   │
   ▼
"This is string"
```

Both eventually give TypeScript information it can use.

The important skill is knowing **when TypeScript already knows enough and when you need to give it more information**.
