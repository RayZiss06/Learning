# Type Inference & Type Assertions

TypeScript can often determine a value's type automatically.

This is called:

```text
Type Inference
```

Example:

```typescript
let username = "Alice";
```

We did not write:

```typescript
let username: string = "Alice";
```

But TypeScript infers:

```text
username
→ string
```

TypeScript also allows developers to tell the compiler:

```text
"I know more about this value's type than you do."
```

This is called:

```text
Type Assertion
```

Example:

```typescript
const input =
    document.getElementById(
        "username"
    ) as HTMLInputElement;
```

The important difference is:

```text
Inference
→ TypeScript determines the type.

Annotation
→ Developer declares the expected type.

Assertion
→ Developer tells TypeScript to treat
  a value as a particular type.
```

---

# 1. Type Inference

Type inference means TypeScript automatically determines a type from available information.

Example:

```typescript
let age = 25;
```

TypeScript infers:

```text
age
→ number
```

Therefore:

```typescript
age = 30;
```

is valid.

But:

```typescript
age = "thirty";
```

is invalid.

Even though we never explicitly wrote:

```typescript
let age: number;
```

---

# 2. Why Type Inference Matters

Without inference, TypeScript code would become unnecessarily verbose.

Imagine writing:

```typescript
const name: string = "Alice";

const age: number = 25;

const active: boolean = true;

const score: number = 95;
```

TypeScript already knows these types from the values.

So this is usually enough:

```typescript
const name = "Alice";

const age = 25;

const active = true;

const score = 95;
```

The code remains fully typed.

---

# 3. Explicit Annotation vs Inference

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

In both cases TypeScript understands:

```text
username
→ string
```

For obvious local values, inference is often cleaner.

---

# 4. Variable Type Inference

```typescript
let count = 10;
```

Type:

```text
number
```

```typescript
let message = "Hello";
```

Type:

```text
string
```

```typescript
let active = true;
```

Type:

```text
boolean
```

---

# 5. `let` and Type Widening

Consider:

```typescript
let status = "loading";
```

TypeScript usually infers:

```text
string
```

rather than the exact literal:

```text
"loading"
```

Why?

Because `let` means the variable can be reassigned.

```typescript
status = "success";
status = "error";
```

So TypeScript widens the literal into the broader:

```text
string
```

---

# 6. `const` Inference

Consider:

```typescript
const status = "loading";
```

Because the variable cannot be reassigned, TypeScript can infer the literal type:

```text
"loading"
```

rather than merely:

```text
string
```

Similarly:

```typescript
const answer = 42;
```

can have the literal type:

```text
42
```

---

# 7. `let` vs `const` Literal Inference

```typescript
let role = "admin";
```

typically:

```text
role
→ string
```

But:

```typescript
const role = "admin";
```

typically:

```text
role
→ "admin"
```

Mental model:

```text
let
→ value may change
→ broader type often needed

const
→ variable binding cannot change
→ exact literal can often be retained
```

---

# 8. Explicit Literal Annotation

You can force a `let` variable to accept only one literal:

```typescript
let status: "loading" =
    "loading";
```

Now:

```typescript
status = "success";
```

is invalid.

Because:

```text
status
→ "loading"
```

not:

```text
string
```

---

# 9. Union Annotation

A more realistic example:

```typescript
type Status =
    "loading"
    | "success"
    | "error";

let status: Status =
    "loading";
```

Now:

```typescript
status = "success";
```

works.

But:

```typescript
status = "banana";
```

does not.

---

# 10. Array Type Inference

```typescript
const numbers = [
    10,
    20,
    30
];
```

TypeScript infers:

```text
number[]
```

So:

```typescript
numbers.push(40);
```

works.

But:

```typescript
numbers.push("50");
```

fails.

---

# 11. String Array Inference

```typescript
const names = [
    "Alice",
    "Bob",
    "Charlie"
];
```

TypeScript infers:

```text
string[]
```

---

# 12. Mixed Array Inference

```typescript
const values = [
    1,
    "Alice"
];
```

TypeScript may infer:

```typescript
(string | number)[]
```

because the array contains both:

```text
number
string
```

---

# 13. Empty Array Problem

An empty array provides little information:

```typescript
const users = [];
```

Its inferred behavior can depend on context and compiler settings.

When the intended element type matters, explicitly annotate it:

```typescript
const users: User[] = [];
```

This clearly communicates:

```text
users
→ array of User
```

---

# 14. Object Type Inference

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

TypeScript infers approximately:

```typescript
{
    id: number;
    name: string;
    active: boolean;
}
```

Therefore:

```typescript
user.name =
    "Bob";
```

works.

But:

```typescript
user.name =
    100;
```

does not.

---

# 15. `const` Object Does Not Make Properties Literal

This is important.

```typescript
const user = {
    role: "admin"
};
```

Although `user` cannot be reassigned:

```typescript
user = {};
```

the property can still change:

```typescript
user.role = "user";
```

Therefore TypeScript commonly infers:

```text
user.role
→ string
```

rather than:

```text
"admin"
```

`const` protects the variable binding, not the object's internal properties.

---

# 16. Function Return Type Inference

TypeScript can infer function return types.

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
return type
→ number
```

Equivalent explicit version:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

---

# 17. Multiple Return Paths

```typescript
function getValue(
    success: boolean
) {
    if (success) {
        return "Done";
    }

    return 404;
}
```

TypeScript can infer a union-like return:

```text
string | number
```

because both are possible.

---

# 18. Why Explicit Function Return Types Can Still Help

Inference works:

```typescript
function createUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

But explicit return types can enforce an API contract:

```typescript
function createUser(): User {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Now if implementation changes incorrectly, TypeScript catches it against the declared contract.

---

# 19. Callback Parameter Inference

Consider:

```typescript
const numbers = [
    1,
    2,
    3
];

numbers.map(
    number => number * 2
);
```

We did not write:

```typescript
number: number
```

TypeScript already knows the array contains numbers.

Therefore callback parameter:

```text
number
→ number
```

This is contextual typing.

---

# 20. Contextual Typing

TypeScript can infer a type from the location where an expression appears.

Example:

```typescript
const handler:
    (value: string) => void =
    value => {
        console.log(
            value.toUpperCase()
        );
    };
```

The arrow function parameter does not need:

```typescript
value: string
```

because the surrounding function type provides that information.

---

# 21. DOM Contextual Typing

Example:

```typescript
button.addEventListener(
    "click",
    event => {
        console.log(event);
    }
);
```

TypeScript uses the `addEventListener` definition and event name to infer useful information about `event`.

You usually do not need to manually annotate every callback parameter.

---

# 22. Best Common Type

When TypeScript sees multiple values, it tries to find a type that represents them.

Example:

```typescript
const values = [
    1,
    2,
    null
];
```

With strict null checking, the inferred element type may be:

```text
number | null
```

because the array contains both possibilities.

This process is related to TypeScript's best common type inference.

---

# 23. Inference Is Context Sensitive

Consider:

```typescript
const value = null;
```

How TypeScript treats this depends partly on compiler options such as:

```json
{
    "strictNullChecks": true
}
```

Modern TypeScript projects should generally use strict mode:

```json
{
    "strict": true
}
```

because it enables much stronger checking.

---

# 24. When to Let TypeScript Infer

Usually let TypeScript infer obvious local values.

Good:

```typescript
const name = "Alice";

const age = 25;

const active = true;
```

Writing:

```typescript
const name: string =
    "Alice";
```

is not wrong.

It is simply redundant when the type is obvious and no wider contract is needed.

---

# 25. When Explicit Annotations Are Useful

Annotations are useful when:

```text
The variable has no initial value.

The intended type is wider than
the initial value.

You want to enforce a contract.

A function is part of a public API.

The inferred type is unclear.

An empty collection needs a type.

You want to catch implementation
changes against a declared type.
```

Example:

```typescript
let user: User | null =
    null;
```

Inference alone from:

```typescript
let user = null;
```

would not communicate the intended future `User` state.

---

# 26. Annotation

An annotation tells TypeScript what type a declaration should have.

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

Here:

```text
: User
```

is a type annotation.

TypeScript checks the value against that contract.

---

# 27. What Is a Type Assertion?

A type assertion tells TypeScript:

```text
Treat this value as this type.
```

Syntax:

```typescript
value as Type
```

Example:

```typescript
const value:
    unknown =
    "Hello";

const text =
    value as string;
```

Now TypeScript treats:

```text
text
→ string
```

---

# 28. Assertion Does Not Convert the Value

This is critical.

```typescript
const value =
    "123" as unknown as number;
```

This does **not** convert:

```text
"123"
```

into:

```text
123
```

At runtime the value is still a string.

Assertions affect TypeScript's understanding.

They do not perform runtime conversion.

---

# 29. Type Conversion vs Type Assertion

Conversion:

```typescript
const value =
    Number("123");
```

Runtime result:

```text
123
→ number
```

Assertion:

```typescript
const value =
    "123" as unknown as number;
```

Runtime value:

```text
"123"
→ still string
```

Mental model:

```text
Conversion
→ changes runtime value/type

Assertion
→ changes compiler's view
```

---

# 30. Basic `as` Assertion

```typescript
const value:
    unknown =
    "TypeScript";

const text =
    value as string;
```

Now:

```typescript
text.toUpperCase();
```

is allowed.

Because TypeScript trusts the assertion that `value` is a string.

---

# 31. Assertions Are Not Runtime Checks

Suppose:

```typescript
const value:
    unknown =
    100;

const text =
    value as string;
```

An assertion cannot safely make an arbitrary incompatible conversion directly in every case, but the important concept is:

```text
Assertion
≠ runtime validation
```

If you force the compiler through unsafe assertions, the runtime value remains whatever it actually was.

---

# 32. Prefer Narrowing When Possible

Instead of asserting:

```typescript
const value:
    unknown =
    getValue();

const text =
    value as string;
```

prefer checking:

```typescript
const value:
    unknown =
    getValue();

if (
    typeof value === "string"
) {
    console.log(
        value.toUpperCase()
    );
}
```

Now the compiler and runtime evidence agree.

---

# 33. DOM Type Assertion

A common legitimate use of assertions occurs with DOM elements.

```typescript
const input =
    document.getElementById(
        "username"
    );
```

TypeScript may know:

```text
HTMLElement | null
```

But if we know this element is an `<input>`:

```typescript
const input =
    document.getElementById(
        "username"
    ) as HTMLInputElement;
```

Now:

```typescript
input.value
```

is available.

---

# 34. Better DOM Query Typing

Many DOM APIs support more precise patterns.

For example:

```typescript
const input =
    document.querySelector<HTMLInputElement>(
        "#username"
    );
```

Type:

```text
HTMLInputElement | null
```

Now TypeScript knows the element subtype while still preserving the possibility that no element was found.

---

# 35. DOM Null Problem

Even if the element type is known:

```typescript
const input =
    document.querySelector<HTMLInputElement>(
        "#username"
    );
```

it can still be:

```text
null
```

because the selector may find nothing.

Safe:

```typescript
if (input) {
    console.log(
        input.value
    );
}
```

---

# 36. Assertion Can Accidentally Hide `null`

Be careful with:

```typescript
const input =
    document.getElementById(
        "username"
    ) as HTMLInputElement;
```

If no element exists, runtime value can still be:

```text
null
```

The assertion does not create the element.

So assertions can suppress useful compiler warnings.

---

# 37. Angle-Bracket Assertion Syntax

TypeScript also supports:

```typescript
const value =
    <string>someValue;
```

Equivalent style:

```typescript
const value =
    someValue as string;
```

However:

```typescript
as
```

is generally preferred, especially because angle-bracket assertions conflict with JSX syntax in `.tsx` files.

---

# 38. Use `as` in React/TSX

Avoid:

```typescript
const value =
    <string>data;
```

in React TypeScript code.

Use:

```typescript
const value =
    data as string;
```

because:

```text
<Type>
```

can be interpreted as JSX.

---

# 39. Assertion vs Annotation

Annotation:

```typescript
const user: User =
    getUser();
```

Meaning:

```text
Check that getUser() produces
something assignable to User.
```

Assertion:

```typescript
const user =
    getUser() as User;
```

Meaning:

```text
Treat this result as User
within assertion compatibility rules.
```

Annotations are generally stronger when you want the compiler to verify a declaration's contract.

---

# 40. Annotation Example

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

If we forget:

```text
name
```

TypeScript reports an error.

---

# 41. Assertion Example

```typescript
const user = {
    id: 1
} as User;
```

An assertion may allow you to bypass checking that would have caught the incomplete value.

At runtime:

```text
user.name
→ undefined
```

even though TypeScript may treat it as:

```text
string
```

This is why unnecessary assertions are dangerous.

---

# 42. Assertions Shift Responsibility to You

When writing:

```typescript
value as User
```

you are telling the compiler that you have additional knowledge.

Therefore:

```text
More assertions
≠ more type safety
```

Often:

```text
More assertions
→ compiler trusts developer more
→ fewer checks
→ more responsibility on developer
```

Use them when justified.

---

# 43. `unknown` and Assertions

Suppose:

```typescript
const response:
    unknown =
    getResponse();
```

You could write:

```typescript
const user =
    response as User;
```

But this does not prove the response is a `User`.

For untrusted external data, validate it.

Conceptually:

```text
External Data
     │
     ▼
   unknown
     │
     ▼
Runtime Validation
     │
     ▼
    User
```

---

# 44. Double Assertion

You may encounter:

```typescript
value as unknown as SomeType
```

Example:

```typescript
const value =
    "Hello"
        as unknown
        as number;
```

This can force TypeScript across otherwise incompatible types.

But runtime value remains:

```text
"Hello"
```

This should be extremely rare in normal application code.

---

# 45. Why Double Assertions Are Dangerous

```typescript
const value =
    "100"
        as unknown
        as number;

console.log(
    value + 50
);
```

TypeScript may believe:

```text
value
→ number
```

But runtime JavaScript still has:

```text
"100"
```

So the result can behave like string concatenation rather than numeric addition.

Assertions do not change runtime reality.

---

# 46. Non-Null Assertion Operator

TypeScript provides:

```text
!
```

after an expression.

Example:

```typescript
const input =
    document.getElementById(
        "username"
    )!;
```

This tells TypeScript:

```text
This value is not
null or undefined.
```

---

# 47. Non-Null Assertion Example

Without:

```typescript
const input =
    document.getElementById(
        "username"
    );

input.focus();
```

TypeScript may complain:

```text
input is possibly null
```

With:

```typescript
const input =
    document.getElementById(
        "username"
    )!;

input.focus();
```

TypeScript allows it.

---

# 48. Non-Null Assertion Does Not Perform a Check

If:

```typescript
document.getElementById(
    "username"
)
```

returns:

```text
null
```

then:

```typescript
!
```

does nothing at runtime.

Calling:

```typescript
input.focus();
```

can still fail.

So:

```text
!
→ compiler assertion

NOT

runtime null check
```

---

# 49. Prefer Real Null Checks

Safer:

```typescript
const input =
    document.getElementById(
        "username"
    );

if (input) {
    input.focus();
}
```

or:

```typescript
input?.focus();
```

depending on desired behavior.

Use `!` only when you genuinely know the value must exist.

---

# 50. Definite Assignment Assertion

There is another use of `!` in class fields:

```typescript
class UserService {
    private token!: string;
}
```

This tells TypeScript:

```text
token will be assigned
before it is used.
```

This is called a:

```text
Definite Assignment Assertion
```

It is related to assertions but different from:

```typescript
value!
```

---

# 51. Definite Assignment Example

```typescript
class App {
    private container!:
        HTMLElement;

    initialize() {
        this.container =
            document.body;
    }
}
```

The developer is promising that initialization will happen before use.

Again, TypeScript does not enforce that promise at runtime.

---

# 52. `as const`

TypeScript provides a special assertion:

```typescript
as const
```

Example:

```typescript
const status =
    "loading" as const;
```

Type:

```text
"loading"
```

instead of:

```text
string
```

---

# 53. `as const` with Objects

Consider:

```typescript
const config = {
    environment:
        "production",
    retries: 3
};
```

TypeScript commonly infers approximately:

```typescript
{
    environment: string;
    retries: number;
}
```

Now:

```typescript
const config = {
    environment:
        "production",
    retries: 3
} as const;
```

Type becomes conceptually:

```typescript
{
    readonly environment:
        "production";

    readonly retries:
        3;
}
```

---

# 54. `as const` with Arrays

Without:

```typescript
const coordinates = [
    10,
    20
];
```

Type:

```text
number[]
```

With:

```typescript
const coordinates = [
    10,
    20
] as const;
```

Type becomes:

```text
readonly [10, 20]
```

This preserves:

```text
Exact values
Exact positions
Readonly behavior
```

---

# 55. `as const` Is Useful for Literal Data

Example:

```typescript
const roles = [
    "admin",
    "user",
    "manager"
] as const;
```

Instead of simply:

```text
string[]
```

TypeScript knows:

```text
readonly [
    "admin",
    "user",
    "manager"
]
```

This can later be used to derive a union type.

---

# 56. Deriving a Union from `as const`

```typescript
const roles = [
    "admin",
    "user",
    "manager"
] as const;
```

Then:

```typescript
type Role =
    typeof roles[number];
```

Result:

```typescript
type Role =
    "admin"
    | "user"
    | "manager";
```

This is a powerful production TypeScript pattern.

We will explore `typeof` and indexed access types more deeply in advanced type manipulation.

---

# 57. `as const` with Configuration Objects

```typescript
const routes = {
    home: "/",
    users: "/users",
    admin: "/admin"
} as const;
```

TypeScript preserves exact path values.

Conceptually:

```text
routes.home
→ "/"

routes.users
→ "/users"

routes.admin
→ "/admin"
```

instead of widening everything to:

```text
string
```

---

# 58. `as const` Is Not Deep Runtime Immutability

This:

```typescript
const config = {
    mode: "dark"
} as const;
```

creates readonly typing.

But `as const` is primarily a TypeScript type-system feature.

Do not treat it as a runtime security or deep-freezing mechanism.

If runtime immutability is required, that is a separate JavaScript concern.

---

# 59. The `satisfies` Operator

Modern TypeScript provides:

```typescript
satisfies
```

Example:

```typescript
type Config = {
    mode:
        "light" | "dark";
    retries: number;
};
```

Then:

```typescript
const config = {
    mode: "dark",
    retries: 3
} satisfies Config;
```

This checks that the value satisfies the contract while preserving useful information about the expression.

---

# 60. Why `satisfies` Exists

Consider annotation:

```typescript
const config: Config = {
    mode: "dark",
    retries: 3
};
```

The variable is viewed through:

```text
Config
```

Sometimes we want:

```text
Validate this object against Config

BUT

retain useful inferred information
about the actual object
```

That's where:

```typescript
satisfies
```

is useful.

---

# 61. `satisfies` Checks the Value

```typescript
type Config = {
    mode:
        "light" | "dark";
    retries: number;
};
```

Valid:

```typescript
const config = {
    mode: "dark",
    retries: 3
} satisfies Config;
```

Invalid:

```typescript
const config = {
    mode: "banana",
    retries: 3
} satisfies Config;
```

TypeScript catches the invalid mode.

---

# 62. `satisfies` Does Not Mean Assertion

Compare:

```typescript
const config =
    value as Config;
```

with:

```typescript
const config =
    value satisfies Config;
```

They mean different things.

Assertion:

```text
Treat value as Config.
```

`satisfies`:

```text
Check whether this expression
is assignable to Config while
preserving its useful inferred type.
```

---

# 63. Annotation vs Assertion vs `satisfies`

## Annotation

```typescript
const config: Config = {
    mode: "dark",
    retries: 3
};
```

Meaning:

```text
The variable's declared type
is Config.

Check assignment against Config.
```

---

## Assertion

```typescript
const config = {
    mode: "dark",
    retries: 3
} as Config;
```

Meaning:

```text
Treat this expression as Config.
```

---

## `satisfies`

```typescript
const config = {
    mode: "dark",
    retries: 3
} satisfies Config;
```

Meaning:

```text
Verify compatibility with Config,
while preserving useful inferred
information about the expression.
```

---

# 64. `satisfies` Example with Record

```typescript
type Role =
    "admin"
    | "user"
    | "manager";

type Permission = {
    read: boolean;
    write: boolean;
};
```

Then:

```typescript
const permissions = {
    admin: {
        read: true,
        write: true
    },

    user: {
        read: true,
        write: false
    },

    manager: {
        read: true,
        write: true
    }
} satisfies
    Record<Role, Permission>;
```

TypeScript checks that all expected roles and permission structures are valid.

---

# 65. `satisfies` Can Catch Missing Keys

Given:

```typescript
type Role =
    "admin"
    | "user";
```

Then:

```typescript
const permissions = {
    admin: {
        read: true
    }
} satisfies
    Record<
        Role,
        { read: boolean }
    >;
```

TypeScript can detect that:

```text
user
```

is missing.

This is useful for configuration maps.

---

# 66. `satisfies` Can Catch Extra Keys

For a constrained object shape, `satisfies` can also participate in excess-property checking.

Example:

```typescript
type Config = {
    mode:
        "light" | "dark";
};
```

Then:

```typescript
const config = {
    mode: "dark",
    banana: true
} satisfies Config;
```

can produce an excess-property error because `banana` is not part of the target shape.

---

# 67. `as const` + `satisfies`

These can work together.

```typescript
type Route =
    "/" | "/users";
```

Example:

```typescript
const routes = {
    home: "/",
    users: "/users"
} as const satisfies
    Record<string, Route>;
```

This provides:

```text
Validation
+
literal preservation
+
readonly literal structure
```

A useful pattern for configuration data.

---

# 68. Inference with Destructuring

```typescript
const user = {
    id: 1,
    name: "Alice"
};

const {
    id,
    name
} = user;
```

TypeScript infers:

```text
id
→ number

name
→ string
```

No additional annotations are necessary.

---

# 69. Inference with Array Destructuring

```typescript
const numbers = [
    10,
    20,
    30
];

const [
    first,
    second
] = numbers;
```

TypeScript knows:

```text
first
→ number

second
→ number
```

---

# 70. Tuple Destructuring Inference

```typescript
const user:
    [number, string] =
    [1, "Alice"];

const [
    id,
    name
] = user;
```

TypeScript knows:

```text
id
→ number

name
→ string
```

because tuple positions have known types.

---

# 71. Inference with Default Values

```typescript
function greet(
    name = "Guest"
) {
    console.log(name);
}
```

TypeScript infers:

```text
name
→ string
```

from the default value.

---

# 72. Return Type Inference with Arrow Functions

```typescript
const multiply = (
    a: number,
    b: number
) => a * b;
```

TypeScript infers:

```text
return
→ number
```

No need to write:

```typescript
const multiply = (
    a: number,
    b: number
): number => a * b;
```

unless the explicit contract improves the code.

---

# 73. Contextual Return Inference

```typescript
type Calculator = (
    a: number,
    b: number
) => number;
```

Then:

```typescript
const add: Calculator =
    (a, b) => a + b;
```

TypeScript gets:

```text
a
→ number

b
→ number

expected return
→ number
```

from the surrounding type.

---

# 74. Inference Across Array Methods

```typescript
const users: User[] =
    getUsers();
```

Then:

```typescript
users.filter(
    user =>
        user.active
);
```

The callback parameter is inferred:

```text
user
→ User
```

Similarly:

```typescript
users.map(
    user => user.name
);
```

TypeScript can infer the resulting array:

```text
string[]
```

---

# 75. Map Inference Example

```typescript
const numbers = [
    1,
    2,
    3
];

const labels =
    numbers.map(
        number =>
            `Item ${number}`
    );
```

TypeScript infers:

```text
labels
→ string[]
```

because the callback returns strings.

---

# 76. Filter Inference

```typescript
const numbers = [
    1,
    2,
    3,
    4
];

const even =
    numbers.filter(
        number =>
            number % 2 === 0
    );
```

Type:

```text
number[]
```

The array type flows into the callback and back into the result.

---

# 77. Inference Does Not Mean TypeScript Knows Runtime Truth

Suppose:

```typescript
const response =
    JSON.parse(json);
```

`JSON.parse()` traditionally returns:

```text
any
```

So TypeScript cannot automatically prove the runtime structure.

Inference is only as safe as the type information available.

External data remains a trust boundary.

---

# 78. Avoid Asserting API Responses Blindly

Avoid:

```typescript
const user =
    await response.json()
        as User;
```

if the server response is not guaranteed or validated.

This tells TypeScript:

```text
Trust me.
This is User.
```

It does not check the response.

Safer architecture:

```text
HTTP Response
     │
     ▼
unknown
     │
     ▼
Runtime Schema Validation
     │
     ▼
User
```

---

# 79. Assertion Is Not Parsing

This:

```typescript
const age =
    input.value as unknown
        as number;
```

does not parse a number.

Correct runtime conversion:

```typescript
const age =
    Number(input.value);
```

or:

```typescript
const age =
    parseInt(
        input.value,
        10
    );
```

depending on the intended parsing behavior.

---

# 80. Assertion Is Not Validation

This:

```typescript
const user =
    data as User;
```

does not check:

```text
Does id exist?

Is id a number?

Does name exist?

Is name a string?
```

It merely affects static typing.

---

# 81. Assertions Can Be Valid When You Have External Knowledge

Example:

```typescript
const canvas =
    document.querySelector(
        "#gameCanvas"
    ) as HTMLCanvasElement;
```

If your application guarantees that the element exists and is a canvas, the assertion may be reasonable.

But the guarantee comes from your application structure, not from the assertion itself.

---

# 82. Prefer Specific Assertions

If an assertion is needed:

Better:

```typescript
value as HTMLInputElement
```

than:

```typescript
value as any
```

because the specific type still provides meaningful checking afterward.

---

# 83. Avoid `as any`

Example:

```typescript
const value =
    something as any;
```

Now:

```typescript
value.whatever.foo.bar();
```

may compile.

You have effectively disabled type safety for that value.

If the type is unknown, prefer:

```typescript
unknown
```

and narrow it.

---

# 84. Assertion Chains Are a Warning Sign

Code such as:

```typescript
value
    as unknown
    as SomeComplexType
```

often indicates:

```text
The model may be wrong.

The library types may need
better integration.

Runtime validation may be missing.

The value may need narrowing.

The design may need refactoring.
```

Not always—but investigate before accepting it.

---

# 85. Over-Annotation

Avoid unnecessarily verbose code:

```typescript
const count: number = 10;

const name: string = "Alice";

const active: boolean = true;
```

When nothing is gained, prefer:

```typescript
const count = 10;

const name = "Alice";

const active = true;
```

TypeScript still knows the types.

---

# 86. Under-Annotation

Inference should not be used blindly either.

Example:

```typescript
let result = null;
```

If the actual intended state is:

```text
User | null
```

say so:

```typescript
let result:
    User | null =
    null;
```

The annotation communicates future possibilities.

---

# 87. Public API Return Types

Consider:

```typescript
export function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Inference works.

But for public/library boundaries, explicit return types can make the intended contract clearer:

```typescript
export function getUser():
    User {

    return {
        id: 1,
        name: "Alice"
    };
}
```

Now accidental implementation changes are checked against the contract.

---

# 88. Inference Inside, Explicit Contracts at Boundaries

A useful engineering principle:

```text
Inside implementation
→ rely heavily on inference

At important boundaries
→ consider explicit contracts
```

Examples of boundaries:

```text
Exported functions

API models

Component props

Service interfaces

Public library APIs

Configuration contracts

Database/domain boundaries
```

This avoids both excessive verbosity and weak contracts.

---

# 89. React State Inference Preview

Later with React:

```typescript
const [
    count,
    setCount
] = useState(0);
```

TypeScript can infer:

```text
count
→ number
```

But:

```typescript
const [
    user,
    setUser
] = useState(null);
```

may infer too narrowly for the intended state.

So you might write:

```typescript
const [
    user,
    setUser
] =
    useState<User | null>(
        null
    );
```

This is an example of knowing when inference is enough and when explicit typing is needed.

---

# 90. React Event Assertion Preview

You may encounter code like:

```typescript
const value =
    (
        event.target
        as HTMLInputElement
    ).value;
```

Assertions sometimes appear around DOM event targets because the general DOM type may not know the exact element subtype.

However, correctly typing the event or using `currentTarget` often gives safer typing.

We will cover this properly in React + TypeScript.

---

# 91. `satisfies` for Configuration Objects

Suppose:

```typescript
type Environment =
    "development"
    | "production";

type AppConfig = {
    environment:
        Environment;
    port: number;
};
```

Then:

```typescript
const config = {
    environment:
        "production",
    port: 3000
} satisfies AppConfig;
```

This is excellent for configuration objects because TypeScript validates the contract without unnecessarily forcing the variable to be typed exactly as `AppConfig`.

---

# 92. `as const` for Constants

Suppose:

```typescript
const HTTP_METHODS = [
    "GET",
    "POST",
    "PUT",
    "DELETE"
] as const;
```

Then:

```typescript
type HttpMethod =
    typeof HTTP_METHODS[number];
```

produces:

```typescript
type HttpMethod =
    "GET"
    | "POST"
    | "PUT"
    | "DELETE";
```

One source of truth can provide both runtime values and compile-time types.

---

# 93. Why One Source of Truth Matters

Without deriving the type:

```typescript
const roles = [
    "admin",
    "user",
    "manager"
];

type Role =
    "admin"
    | "user"
    | "manager";
```

we duplicated the information.

Someone might later change:

```typescript
const roles = [
    "admin",
    "user",
    "manager",
    "moderator"
];
```

but forget to update `Role`.

Using:

```typescript
as const
```

plus:

```typescript
typeof roles[number]
```

can derive the type from the runtime constant.

---

# 94. Common Mistake — Annotating Everything

Avoid thinking TypeScript requires:

```typescript
const x: number = 10;
const y: string = "Hello";
const z: boolean = true;
```

Inference is a core feature of the language.

Use it.

---

# 95. Common Mistake — Asserting Everything

Avoid:

```typescript
const user =
    data as User;

const config =
    data as Config;

const response =
    data as ApiResponse;
```

especially around external data.

Assertions can hide real problems.

---

# 96. Common Mistake — Assertion Instead of Conversion

Wrong:

```typescript
const number =
    input as unknown
        as number;
```

Correct conversion:

```typescript
const number =
    Number(input);
```

---

# 97. Common Mistake — Assertion Instead of Narrowing

Avoid:

```typescript
function print(
    value: unknown
) {
    console.log(
        (value as string)
            .toUpperCase()
    );
}
```

Prefer:

```typescript
function print(
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

# 98. Common Mistake — Blind Non-Null Assertion

Avoid:

```typescript
const element =
    document.getElementById(
        "missing"
    )!;

element.focus();
```

unless existence is genuinely guaranteed.

Prefer checking when absence is possible.

---

# 99. Common Mistake — Thinking `as const` Freezes Runtime Data

```typescript
const config = {
    mode: "dark"
} as const;
```

provides readonly/literal typing.

It is not equivalent to a runtime deep freeze.

---

# 100. Common Mistake — Confusing `satisfies` with `as`

These are not equivalent:

```typescript
value as Config
```

and:

```typescript
value satisfies Config
```

Remember:

```text
as
→ assertion

satisfies
→ compatibility check
   while preserving useful inference
```

---

# 101. Common Mistake — Explicit Type Destroying Useful Precision

Sometimes an annotation intentionally broadens what you see.

Example:

```typescript
type Routes =
    Record<string, string>;

const routes: Routes = {
    home: "/",
    users: "/users"
};
```

The declared type is broadly:

```text
Record<string, string>
```

If you want to validate against a broad contract while retaining more information about the actual object, `satisfies` may be more appropriate:

```typescript
const routes = {
    home: "/",
    users: "/users"
} satisfies
    Record<string, string>;
```

---

# 102. Common Mistake — Ignoring Compiler Configuration

Inference behavior is affected by compiler options.

Important options include:

```json
{
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
}
```

In modern projects, enabling:

```json
{
    "strict": true
}
```

is generally recommended.

---

# 103. Interview Question — What Is Type Inference?

Type inference is TypeScript's ability to determine types automatically from values, context, return statements, generic relationships, and surrounding type information.

Example:

```typescript
const age = 25;
```

TypeScript infers:

```text
number
```

---

# 104. Interview Question — What Is Contextual Typing?

Contextual typing means TypeScript determines a type based on where an expression appears.

Example:

```typescript
const handler:
    (value: string) => void =
    value => {
        console.log(value);
    };
```

`value` is inferred as `string` from the surrounding function type.

---

# 105. Interview Question — What Is Type Widening?

Type widening occurs when TypeScript moves from a specific literal type to a broader type.

Example:

```typescript
let status = "loading";
```

typically becomes:

```text
string
```

rather than:

```text
"loading"
```

because the variable can be reassigned.

---

# 106. Interview Question — What Is a Type Assertion?

A type assertion tells TypeScript to treat a value as a particular type.

Example:

```typescript
const input =
    element
        as HTMLInputElement;
```

It affects static type checking and does not perform runtime conversion or validation.

---

# 107. Interview Question — Type Annotation vs Assertion?

Annotation:

```typescript
const user: User =
    value;
```

declares the variable's type and asks TypeScript to check assignment compatibility.

Assertion:

```typescript
const user =
    value as User;
```

tells TypeScript to treat the expression as `User` within assertion rules.

Prefer normal typing/narrowing when possible.

---

# 108. Interview Question — Does `as` Convert a Value?

No.

```typescript
value as number
```

does not call:

```typescript
Number(value)
```

Assertions only affect compile-time type information.

---

# 109. Interview Question — What Does `!` Mean After a Value?

The non-null assertion:

```typescript
value!
```

tells TypeScript to exclude:

```text
null
undefined
```

from the type.

It does not perform a runtime null check.

---

# 110. Interview Question — What Is `as const`?

`as const` tells TypeScript to preserve literal values as narrowly as possible and make object properties/array structures readonly at the type level.

Example:

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

becomes a readonly tuple of literal values rather than a general `string[]`.

---

# 111. Interview Question — What Is `satisfies`?

`satisfies` verifies that an expression is assignable to a target type while preserving useful inferred information about the expression.

Example:

```typescript
const config = {
    mode: "dark"
} satisfies Config;
```

It is useful for typed configuration objects and maps.

---

# 112. Interview Question — `as` vs `satisfies`?

```text
as
→ tell TypeScript how to view
  the value

satisfies
→ ask TypeScript to verify
  compatibility
```

`satisfies` is preferable when your goal is validation of a declaration while retaining useful inference.

---

# 113. Interview Question — Why Is `as unknown as T` Dangerous?

Because it can force otherwise incompatible types through the type system without changing or validating the runtime value.

Example:

```typescript
"100"
    as unknown
    as number
```

is still a string at runtime.

---

# 114. Interview Question — When Should Explicit Types Be Used?

Common situations:

```text
Public APIs

Function boundaries

Empty collections

Nullable initial state

Complex domain contracts

Values whose intended type is
wider than their initializer

Cases where inference is unclear
```

Use inference when the compiler already has enough information.

---

# 115. Cheat Sheet

```text
TYPE INFERENCE
================================

const age = 25;

→ number


STRING INFERENCE
================================

const name = "Alice";

→ "Alice" for the const variable's
literal value context


LET WIDENING
================================

let status = "loading";

→ string


CONST LITERAL
================================

const status = "loading";

→ "loading"


ARRAY
================================

const values = [1, 2, 3];

→ number[]


MIXED ARRAY
================================

const values = [1, "A"];

→ (string | number)[]


OBJECT
================================

const user = {
    id: 1,
    name: "Alice"
};

→ {
    id: number;
    name: string;
}


RETURN INFERENCE
================================

function add(
    a: number,
    b: number
) {
    return a + b;
}

→ number


ANNOTATION
================================

const user: User = value;

→ declare/check variable as User


ASSERTION
================================

const user =
    value as User;

→ treat value as User


CONVERSION
================================

Number("123")

→ runtime number conversion


NON-NULL ASSERTION
================================

value!

→ exclude null/undefined
  from compiler's view


CONST ASSERTION
================================

const roles = [
    "admin",
    "user"
] as const;

→ readonly literal tuple


SATISFIES
================================

const config = {
    mode: "dark"
} satisfies Config;

→ check Config compatibility
→ preserve useful inference


CORE RULE
================================

Inference
→ compiler knows

Annotation
→ developer declares

Assertion
→ developer promises

satisfies
→ compiler verifies

Conversion
→ runtime value changes
```

---

# 116. Important Rules to Remember

```text
1. TypeScript does not require explicit types everywhere.

2. Type inference is a core TypeScript feature.

3. Obvious local variables usually do not need annotations.

4. const and let can infer differently.

5. let values are commonly widened.

6. const primitive bindings can preserve literal types.

7. const does not make object properties immutable.

8. Arrays infer element types.

9. Mixed arrays can infer union element types.

10. Empty collections may benefit from explicit annotations.

11. Objects infer property types.

12. Functions can infer return types.

13. Callback parameters can be contextually typed.

14. Array methods commonly infer callback parameter types.

15. map can infer result array types.

16. Destructuring preserves useful type information.

17. Default parameter values can contribute to inference.

18. Inference is affected by context.

19. Compiler options affect some inference behavior.

20. Prefer strict TypeScript configuration.

21. Explicit types are useful at important boundaries.

22. Public APIs may benefit from explicit return types.

23. Domain contracts should be intentionally modeled.

24. Type annotations declare expected types.

25. Type assertions tell the compiler how to view a value.

26. Assertions are not runtime checks.

27. Assertions are not conversions.

28. Assertions are not parsing.

29. Assertions are not validation.

30. as string does not convert to string.

31. as number does not convert to number.

32. Use String(), Number(), etc. for runtime conversions when appropriate.

33. Prefer narrowing over assertion when runtime evidence is available.

34. Assertions shift responsibility from the compiler to the developer.

35. Excessive assertions weaken type safety.

36. DOM APIs are a common legitimate assertion use case.

37. DOM assertions do not guarantee elements exist.

38. Preserve null when absence is possible.

39. Use real null checks when appropriate.

40. Angle-bracket assertion syntax exists.

41. Prefer as syntax.

42. Angle-bracket assertions conflict with JSX.

43. React TypeScript code should use as syntax.

44. Non-null assertion uses ! after an expression.

45. value! removes null/undefined from the compiler's view.

46. Non-null assertions perform no runtime check.

47. Blind ! usage can create runtime failures.

48. Optional chaining may be safer when absence is acceptable.

49. Definite assignment assertion also uses !.

50. Class field ! promises later initialization.

51. Definite assignment assertions do not initialize values.

52. as const preserves literal information.

53. as const makes object properties readonly at the type level.

54. as const turns array literals into readonly tuples.

55. as const is useful for constant lookup data.

56. as const does not deep-freeze runtime objects.

57. satisfies checks compatibility.

58. satisfies is not an assertion.

59. satisfies can preserve useful inferred information.

60. satisfies is useful for configuration objects.

61. satisfies is useful for Record maps.

62. satisfies can catch missing required keys.

63. satisfies can catch incompatible values.

64. satisfies can participate in excess-property checking.

65. as const and satisfies can be combined.

66. Annotations and satisfies solve different problems.

67. Assertions and satisfies solve different problems.

68. Type inference and runtime validation are different concepts.

69. External data should not be blindly trusted.

70. JSON/API data may require runtime validation.

71. unknown is safer than any for untrusted values.

72. Narrow unknown before using it.

73. Avoid as any.

74. any disables useful checking.

75. Double assertions should be rare.

76. as unknown as T can bypass useful safety.

77. Double assertions do not alter runtime values.

78. Assertion chains are often a design warning.

79. TypeScript inference depends on available type information.

80. Bad library types can affect inference.

81. any can spread through inference.

82. Contextual typing reduces annotation noise.

83. Callback types often come from surrounding APIs.

84. Function return inference is often sufficient internally.

85. Explicit return types can protect exported contracts.

86. Type widening is intentional.

87. Literal types are more specific than primitive types.

88. "admin" is a subtype-like narrower possibility of string.

89. 42 is narrower than number.

90. true is narrower than boolean.

91. Union annotations can preserve controlled reassignment.

92. Use explicit unions when future values are known.

93. let user: User | null = null expresses intent better than a bare null initializer.

94. React state sometimes requires explicit generic typing.

95. Simple React state often infers correctly.

96. DOM event typing should prefer accurate event/context types over assertions when possible.

97. Runtime truth always matters more than an assertion.

98. TypeScript types disappear during compilation.

99. The JavaScript runtime does not know about type assertions.

100. Good TypeScript balances inference with explicit contracts.
```

---

# 117. Final Mental Model

```text
                   VALUE
                     │
                     ▼
             TypeScript Compiler
                     │
         ┌───────────┼────────────┐
         │           │            │
         ▼           ▼            ▼

    INFERENCE    ANNOTATION    ASSERTION

 Compiler        Developer      Developer
 determines      declares       tells compiler
 the type        the contract   how to view value

         │           │            │
         ▼           ▼            ▼

 const x = 10   const x: number   x as number
```

And:

```text
              satisfies
                   │
                   ▼

        "Does this expression
         satisfy this contract?"

                   │
                   ▼

        Compiler verifies it
        while retaining useful
        inferred information.
```

Remember the most important distinction:

```text
Type Annotation
----------------
const age: number = value;

"age must be number."


Type Assertion
----------------
const age =
    value as number;

"Treat value as number."


Runtime Conversion
----------------
const age =
    Number(value);

"Convert value to number."


Runtime Validation
----------------
if (
    typeof value === "number"
) {
    // We have runtime evidence
}


satisfies
----------------
const config = {
    ...
} satisfies Config;

"Verify this value satisfies
Config without unnecessarily
replacing useful inference."
```

A good TypeScript developer does **not** try to write the maximum number of types.

The goal is:

```text
Give TypeScript enough information
to understand the program,
then let inference do the rest.
```
