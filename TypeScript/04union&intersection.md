# Union & Intersection Types

TypeScript allows us to **combine types**.

The two fundamental mechanisms are:

```text
Union
→ A | B
→ value can satisfy A OR B

Intersection
→ A & B
→ value must satisfy A AND B
```

Example:

```typescript
let id: string | number;
```

`id` can be:

```typescript
id = 101;
id = "USR-101";
```

Intersection:

```typescript
type Employee = {
    employeeId: number;
};

type Developer = {
    language: string;
};

type DeveloperEmployee =
    Employee & Developer;
```

A `DeveloperEmployee` must satisfy **both** contracts.

---

# 1. Union Types

A union type allows a value to be one of multiple possible types.

Syntax:

```typescript
TypeA | TypeB
```

Example:

```typescript
let id:
    string | number;
```

Valid:

```typescript
id = 101;

id = "USR-101";
```

Invalid:

```typescript
id = true;
```

Because:

```text
id
→ string OR number
```

---

# 2. Why Union Types Exist

Without unions, we might define:

```typescript
let id: string;
```

But suppose the application accepts:

```text
101
"USR-101"
```

Typing it only as:

```typescript
string
```

would be inaccurate.

Typing it only as:

```typescript
number
```

would also be inaccurate.

So:

```typescript
let id:
    string | number;
```

models reality correctly.

---

# 3. Multiple Union Members

A union can contain more than two types.

```typescript
let value:
    string
    | number
    | boolean;
```

Valid:

```typescript
value = "Hello";

value = 100;

value = true;
```

Invalid:

```typescript
value = {};
```

---

# 4. Union Mental Model

Think of:

```typescript
string | number
```

as a set of possible values:

```text
string values
        │
        ├──────────┐
        │          │
        ▼          ▼
    "Alice"      "100"

number values
        │
        ├──────────┐
        │          │
        ▼          ▼
       100         25

Combined:

string | number
```

The variable may contain a value from either set.

---

# 5. Union Types Do Not Mean Both at Once

This:

```typescript
string | number
```

means:

```text
string
OR
number
```

It does **not** mean:

```text
string AND number
```

Example:

```typescript
let id:
    string | number;

id = 100;
```

At this moment:

```text
id
→ number
```

Later:

```typescript
id = "USR-100";
```

Now:

```text
id
→ string
```

---

# 6. Union Function Parameters

Suppose a function accepts numeric and string IDs.

```typescript
function findUser(
    id: string | number
) {
    console.log(id);
}
```

Both are valid:

```typescript
findUser(101);

findUser("USR-101");
```

Invalid:

```typescript
findUser(true);
```

---

# 7. Operations on Union Types

Consider:

```typescript
function printId(
    id: string | number
) {
    id.toUpperCase();
}
```

TypeScript rejects this.

Why?

Because:

```text
string
→ has toUpperCase()

number
→ does NOT have toUpperCase()
```

The operation must be safe for the possible current type.

---

# 8. Operations Common to All Members

Consider:

```typescript
function printId(
    id: string | number
) {
    console.log(
        id.toString()
    );
}
```

Both:

```text
string
number
```

support `toString()`.

So the operation is safe.

---

# 9. Narrowing Union Types

To use type-specific functionality, determine which union member you currently have.

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
    } else {
        console.log(
            id.toFixed(2)
        );
    }
}
```

Inside:

```typescript
typeof id === "string"
```

TypeScript knows:

```text
id
→ string
```

Inside `else`:

```text
id
→ number
```

This is:

```text
Type Narrowing
```

We will cover narrowing as its own topic later.

---

# 10. Union Types with `null`

A common application type:

```typescript
type User = {
    id: number;
    name: string;
};

let user:
    User | null =
    null;
```

Initially:

```text
user
→ null
```

Later:

```typescript
user = {
    id: 1,
    name: "Alice"
};
```

Now:

```text
user
→ User
```

---

# 11. Handling `null`

This is unsafe:

```typescript
console.log(
    user.name
);
```

if TypeScript knows `user` could still be `null`.

Handle it:

```typescript
if (user !== null) {
    console.log(
        user.name
    );
}
```

Inside the condition:

```text
User | null
     │
     ▼
    User
```

---

# 12. Union with `undefined`

```typescript
let username:
    string | undefined;
```

Possible:

```typescript
username = "Alice";

username = undefined;
```

Before using string-specific operations:

```typescript
if (
    username !== undefined
) {
    console.log(
        username.toUpperCase()
    );
}
```

---

# 13. Optional Values Are Often Unions

Consider:

```typescript
type User = {
    name: string;
    email?: string;
};
```

Reading:

```typescript
user.email
```

commonly gives:

```text
string | undefined
```

because the property may not exist.

So optional values naturally interact with unions.

---

# 14. Literal Union Types

One of the most important uses of unions is combining literal types.

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

Now:

```typescript
let status: Status;
```

Valid:

```typescript
status = "loading";

status = "success";
```

Invalid:

```typescript
status = "banana";
```

---

# 15. Why Literal Unions Are Powerful

Weak:

```typescript
let role: string;
```

This allows:

```typescript
role = "admin";
role = "user";
role = "manager";
role = "potato";
```

If only three roles exist, this is too broad.

Better:

```typescript
type Role =
    "admin"
    | "user"
    | "manager";
```

Now invalid values are impossible at compile time.

---

# 16. Number Literal Unions

```typescript
type Dice =
    1 | 2 | 3 | 4 | 5 | 6;
```

Valid:

```typescript
let roll: Dice = 4;
```

Invalid:

```typescript
let roll: Dice = 10;
```

---

# 17. Boolean Unions

Technically:

```typescript
true | false
```

represents:

```typescript
boolean
```

So:

```typescript
type Flag =
    true | false;
```

is effectively just:

```typescript
boolean
```

But individual boolean literals can still be useful in more complex object unions.

---

# 18. Union Type Alias

Instead of repeating:

```typescript
"loading"
| "success"
| "error"
```

everywhere:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

Then:

```typescript
let apiStatus: Status;

let uploadStatus: Status;
```

This improves reuse and consistency.

---

# 19. Union of Object Types

Unions can contain objects.

```typescript
type Admin = {
    role: "admin";
    permissions: string[];
};

type User = {
    role: "user";
    username: string;
};

type Account =
    Admin | User;
```

An `Account` can be:

```typescript
const account: Account = {
    role: "admin",
    permissions: [
        "read",
        "write"
    ]
};
```

or:

```typescript
const account: Account = {
    role: "user",
    username: "Alice"
};
```

---

# 20. Accessing Properties on Object Unions

Suppose:

```typescript
function printAccount(
    account: Account
) {
    console.log(
        account.role
    );
}
```

This works because:

```text
Admin
→ role

User
→ role
```

Both contain `role`.

But:

```typescript
console.log(
    account.permissions
);
```

is unsafe because:

```text
Admin
→ permissions exists

User
→ permissions does not exist
```

---

# 21. Narrowing Object Unions

Because `role` has literal values:

```typescript
function printAccount(
    account: Account
) {
    if (
        account.role === "admin"
    ) {
        console.log(
            account.permissions
        );
    } else {
        console.log(
            account.username
        );
    }
}
```

TypeScript understands:

```text
role === "admin"
        │
        ▼
      Admin


role === "user"
        │
        ▼
       User
```

---

# 22. Discriminated Unions

The previous pattern is called a:

```text
Discriminated Union
```

Each object contains a common property whose literal value identifies the specific variant.

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

type State =
    LoadingState
    | SuccessState
    | ErrorState;
```

The property:

```text
status
```

is the discriminator.

---

# 23. Using a Discriminated Union

```typescript
function renderState(
    state: State
) {
    switch (state.status) {
        case "loading":
            return "Loading...";

        case "success":
            return state.data.join(", ");

        case "error":
            return state.message;
    }
}
```

TypeScript knows:

```text
loading
→ LoadingState

success
→ SuccessState

error
→ ErrorState
```

This is one of the most useful TypeScript patterns.

---

# 24. Why Discriminated Unions Matter

Suppose we instead wrote:

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
    data: ["A", "B"],
    error: "Failed"
}
```

What does this state mean?

Loading?

Success?

Error?

All three?

---

A discriminated union:

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

makes the valid states explicit.

---

# 25. Make Invalid States Unrepresentable

This is an important TypeScript design principle.

Instead of:

```typescript
type RequestState = {
    loading: boolean;
    data?: User[];
    error?: string;
};
```

prefer:

```typescript
type RequestState =
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: User[];
    }
    | {
        status: "error";
        message: string;
    };
```

Now impossible combinations are harder to create.

---

# 26. Real-World React State Example

Later in React, instead of:

```typescript
const [loading, setLoading] =
    useState(false);

const [data, setData] =
    useState<User[] | null>(
        null
    );

const [error, setError] =
    useState<string | null>(
        null
    );
```

you may model one state:

```typescript
type UserState =
    | {
        status: "idle";
    }
    | {
        status: "loading";
    }
    | {
        status: "success";
        data: User[];
    }
    | {
        status: "error";
        message: string;
    };
```

Conceptually:

```text
UserState

idle
 OR
loading
 OR
success + data
 OR
error + message
```

This prevents contradictory states.

---

# 27. Function Return Unions

Functions can return multiple possible types.

```typescript
function findUsername(
    id: number
): string | undefined {
    if (id === 1) {
        return "Alice";
    }

    return undefined;
}
```

Caller:

```typescript
const username =
    findUsername(1);
```

Type:

```text
string | undefined
```

So the caller must handle both possibilities.

---

# 28. Union Return Types Should Represent Reality

Bad:

```typescript
function getUser():
    User {
    // But sometimes
    // no user exists.
}
```

If absence is possible:

```typescript
function getUser():
    User | null {
    // ...
}
```

The type should model actual runtime possibilities.

---

# 29. Array of Union Values

```typescript
const values:
    (string | number)[] = [
        1,
        "Alice",
        2,
        "Bob"
    ];
```

Parentheses matter.

```typescript
(string | number)[]
```

means:

```text
Array containing
strings OR numbers
```

---

# 30. Union of Arrays

Compare:

```typescript
string[] | number[]
```

This means:

```text
Array of strings
OR
Array of numbers
```

Examples:

```typescript
let values:
    string[] | number[];

values = [
    "A",
    "B"
];

values = [
    1,
    2
];
```

The array itself is one kind or the other.

---

# 31. `(string | number)[]` vs `string[] | number[]`

These are different.

```typescript
(string | number)[]
```

allows:

```typescript
[
    "Alice",
    10,
    "Bob",
    20
]
```

Each element may differ.

---

```typescript
string[] | number[]
```

means the value is either:

```typescript
["Alice", "Bob"]
```

or:

```typescript
[10, 20]
```

The collection is typed as one array variant or the other.

---

# 32. Union with Functions

Function types can participate in unions.

```typescript
type Value =
    string
    | (() => string);
```

A value may be:

```typescript
const first: Value =
    "Hello";
```

or:

```typescript
const second: Value =
    () => "Hello";
```

Before calling:

```typescript
if (
    typeof second ===
    "function"
) {
    second();
}
```

---

# 33. Union with `unknown`

Consider:

```typescript
string | unknown
```

Since `unknown` already represents any possible value:

```text
string | unknown
→ unknown
```

The `string` adds nothing.

---

# 34. Union with `never`

Consider:

```typescript
string | never
```

`never` contributes no possible values.

Therefore:

```text
string | never
→ string
```

This becomes important later in conditional types.

---

# 35. Union with `any`

```typescript
string | any
```

effectively becomes:

```typescript
any
```

Because `any` overrides useful checking.

Another reason to avoid unnecessary `any`.

---

# 36. Intersection Types

Now consider:

```typescript
A & B
```

This is an:

```text
Intersection Type
```

Meaning the value must satisfy:

```text
A AND B
```

Example:

```typescript
type Person = {
    name: string;
};

type Employee = {
    employeeId: number;
};

type Staff =
    Person & Employee;
```

A `Staff` value needs both structures.

---

# 37. Intersection Example

```typescript
const employee: Staff = {
    name: "Alice",
    employeeId: 101
};
```

Valid.

Missing `name`:

```typescript
const employee: Staff = {
    employeeId: 101
};
```

Invalid.

Missing `employeeId`:

```typescript
const employee: Staff = {
    name: "Alice"
};
```

Invalid.

---

# 38. Intersection Mental Model

Suppose:

```typescript
type A = {
    a: string;
};

type B = {
    b: number;
};

type C =
    A & B;
```

Then:

```text
A
┌───────────────┐
│ a: string     │
└───────────────┘

          +

B
┌───────────────┐
│ b: number     │
└───────────────┘

          ↓

A & B
┌───────────────┐
│ a: string     │
│ b: number     │
└───────────────┘
```

---

# 39. Combining Multiple Contracts

```typescript
type Identifiable = {
    id: number;
};

type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type Named = {
    name: string;
};

type Entity =
    Identifiable
    & Timestamped
    & Named;
```

An `Entity` requires:

```text
id
name
createdAt
updatedAt
```

---

# 40. Real-World Intersection Example

```typescript
type User = {
    id: number;
    username: string;
};

type AuditInfo = {
    createdAt: Date;
    updatedAt: Date;
};

type AuditedUser =
    User & AuditInfo;
```

Then:

```typescript
const user:
    AuditedUser = {
        id: 1,
        username: "Alice",
        createdAt:
            new Date(),
        updatedAt:
            new Date()
    };
```

---

# 41. Intersections with Reusable Capabilities

```typescript
type HasId = {
    id: string;
};

type HasTimestamps = {
    createdAt: Date;
    updatedAt: Date;
};

type HasOwner = {
    ownerId: string;
};

type Resource =
    HasId
    & HasTimestamps
    & HasOwner;
```

This can be useful when building types from reusable contracts.

---

# 42. Union vs Intersection

Union:

```typescript
A | B
```

means:

```text
A OR B
```

Intersection:

```typescript
A & B
```

means:

```text
A AND B
```

Example:

```typescript
type AdminOrUser =
    Admin | User;
```

The value can be either.

```typescript
type AdminUser =
    Admin & User;
```

The value must satisfy both.

---

# 43. Visual Comparison

```text
UNION

A       B
 \     /
  \   /
   \ /
  A | B

Possible:
A OR B


INTERSECTION

A       B
 \     /
  \   /
   \ /
  A & B

Required:
A AND B
```

---

# 44. Intersection Property Conflicts

Suppose:

```typescript
type A = {
    id: string;
};

type B = {
    id: number;
};

type C =
    A & B;
```

What type should `id` have?

It must satisfy:

```text
string
AND
number
```

There is no ordinary value that is both.

So the property becomes effectively:

```typescript
never
```

---

# 45. Impossible Intersection

```typescript
type Impossible =
    string & number;
```

A value would need to be:

```text
string
AND
number
```

at the same time.

No ordinary value satisfies this.

So:

```text
string & number
→ never
```

Conceptually.

---

# 46. Literal Intersection

```typescript
type A =
    "admin";

type B =
    "user";

type C =
    A & B;
```

A value cannot simultaneously be:

```text
"admin"
AND
"user"
```

Therefore:

```text
C
→ never
```

---

# 47. Compatible Literal Intersection

```typescript
type A =
    "admin" | "user";

type B =
    "admin" | "manager";

type C =
    A & B;
```

The common possible value is:

```text
"admin"
```

Therefore:

```typescript
type C =
    "admin";
```

Conceptually, intersection keeps the overlap of possible values.

---

# 48. Set Mental Model

This is the deeper way to understand unions and intersections.

Suppose:

```text
Type A:
{ A, B, C }

Type B:
{ B, C, D }
```

Union:

```text
A | B

{ A, B, C, D }
```

All values from either set.

Intersection:

```text
A & B

{ B, C }
```

Only values belonging to both sets.

---

# 49. Why Object Intersection Looks Like Property Addition

This sometimes confuses beginners.

Suppose:

```typescript
type A = {
    name: string;
};

type B = {
    age: number;
};
```

Then:

```typescript
A & B
```

appears to "combine properties":

```typescript
{
    name: string;
    age: number;
}
```

But the deeper rule is:

```text
Value must satisfy A
AND
Value must satisfy B
```

To satisfy both, the object needs both required properties.

---

# 50. Union of Objects Does Not Merge Properties

Suppose:

```typescript
type A = {
    name: string;
};

type B = {
    age: number;
};

type C =
    A | B;
```

Do not think:

```typescript
{
    name?: string;
    age?: number;
}
```

The actual meaning is:

```text
Value satisfies A
OR
Value satisfies B
```

Those are different concepts.

---

# 51. Common Properties in Object Unions

```typescript
type Dog = {
    name: string;
    bark(): void;
};

type Cat = {
    name: string;
    meow(): void;
};

function printAnimal(
    animal: Dog | Cat
) {
    console.log(
        animal.name
    );
}
```

`name` is safe because both variants provide it.

But:

```typescript
animal.bark();
```

is not safe until TypeScript knows the value is a `Dog`.

---

# 52. `in` Narrowing with Object Unions

```typescript
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

The `in` operator helps narrow object unions.

Again, narrowing gets its own chapter later.

---

# 53. Discriminated Union vs Intersection

These solve different problems.

Union:

```typescript
type Account =
    Admin | Customer;
```

means:

```text
An account is one of
several possible variants.
```

Intersection:

```typescript
type AuditedAdmin =
    Admin & AuditInfo;
```

means:

```text
An AuditedAdmin satisfies
both contracts.
```

---

# 54. Combining Union and Intersection

They can be used together.

```typescript
type BaseUser = {
    id: number;
    name: string;
};

type AdminDetails = {
    role: "admin";
    permissions: string[];
};

type CustomerDetails = {
    role: "customer";
    orders: number[];
};

type User =
    BaseUser
    & (
        AdminDetails
        | CustomerDetails
    );
```

Now every user has:

```text
id
name
```

and is either:

```text
Admin
OR
Customer
```

---

# 55. Parentheses Matter

Consider:

```typescript
A & (B | C)
```

Meaning:

```text
A
AND
either B OR C
```

This is different conceptually from carelessly reading:

```typescript
A & B | C
```

Use parentheses when combining unions and intersections to make intent obvious.

---

# 56. Real-World API Response

```typescript
type ApiSuccess<T> = {
    status: "success";
    data: T;
};

type ApiError = {
    status: "error";
    message: string;
};

type ApiResponse<T> =
    ApiSuccess<T>
    | ApiError;
```

Usage:

```typescript
type User = {
    id: number;
    name: string;
};

const response:
    ApiResponse<User> = {
        status: "success",
        data: {
            id: 1,
            name: "Alice"
        }
    };
```

`T` is a generic type parameter, which we will cover later.

---

# 57. Handling the API Response

```typescript
function handleResponse(
    response:
        ApiResponse<User>
) {
    if (
        response.status ===
        "success"
    ) {
        console.log(
            response.data.name
        );
    } else {
        console.error(
            response.message
        );
    }
}
```

TypeScript knows the correct structure based on `status`.

---

# 58. Why This Is Better Than Optional Properties

Less precise:

```typescript
type ApiResponse<T> = {
    success: boolean;
    data?: T;
    message?: string;
};
```

This permits:

```typescript
{
    success: true
}
```

with no `data`.

Or:

```typescript
{
    success: true,
    message: "Error"
}
```

The relationships between fields are not encoded.

A discriminated union models them directly.

---

# 59. HTTP Request Example

```typescript
type GetRequest = {
    method: "GET";
    query?: string;
};

type PostRequest = {
    method: "POST";
    body: unknown;
};

type Request =
    GetRequest
    | PostRequest;
```

Then:

```typescript
function handleRequest(
    request: Request
) {
    if (
        request.method === "POST"
    ) {
        console.log(
            request.body
        );
    } else {
        console.log(
            request.query
        );
    }
}
```

---

# 60. Payment State Example

```typescript
type PaymentState =
    | {
        status: "pending";
    }
    | {
        status: "completed";
        transactionId: string;
    }
    | {
        status: "failed";
        reason: string;
    };
```

Now:

```typescript
{
    status: "completed"
}
```

is invalid because a completed payment requires:

```text
transactionId
```

This is exactly the kind of relationship unions can enforce.

---

# 61. Authentication State Example

```typescript
type AuthState =
    | {
        status:
            "unauthenticated";
    }
    | {
        status:
            "authenticating";
    }
    | {
        status:
            "authenticated";
        user: User;
    }
    | {
        status: "error";
        message: string;
    };
```

Now:

```text
authenticated
```

guarantees:

```text
user exists
```

No separate `user?: User` guesswork is required.

---

# 62. Permission Example

```typescript
type ReadPermission = {
    canRead: true;
};

type WritePermission = {
    canWrite: true;
};

type ReadWritePermission =
    ReadPermission
    & WritePermission;
```

Value:

```typescript
const permission:
    ReadWritePermission = {
        canRead: true,
        canWrite: true
    };
```

Must satisfy both capabilities.

---

# 63. Base Entity with Intersections

```typescript
type Entity = {
    id: string;
};

type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type UserData = {
    username: string;
    email: string;
};

type User =
    Entity
    & Timestamped
    & UserData;
```

This creates a user contract composed from reusable pieces.

---

# 64. Intersections vs Repetition

Instead of:

```typescript
type User = {
    id: string;
    createdAt: Date;
    updatedAt: Date;
    username: string;
};

type Product = {
    id: string;
    createdAt: Date;
    updatedAt: Date;
    name: string;
};
```

you could reuse:

```typescript
type Entity = {
    id: string;
};

type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type User =
    Entity
    & Timestamped
    & {
        username: string;
    };

type Product =
    Entity
    & Timestamped
    & {
        name: string;
    };
```

---

# 65. But Do Not Overuse Intersections

This:

```typescript
type User =
    HasId
    & HasName
    & HasEmail
    & HasRole
    & HasCreatedAt
    & HasUpdatedAt;
```

may become harder to understand than:

```typescript
type User = {
    id: string;
    name: string;
    email: string;
    role: Role;
    createdAt: Date;
    updatedAt: Date;
};
```

Reuse types when it improves design, not merely because TypeScript allows composition.

---

# 66. Union Types and Function Overloads

Sometimes you may start with:

```typescript
function format(
    value: string | number
): string {
    return String(value);
}
```

This is perfect when the function behaves essentially the same for both types.

For more complex relationships between input and output, function overloads or generics may be better.

We will cover those separately.

---

# 67. Union Parameters vs `any`

Bad:

```typescript
function printValue(
    value: any
) {
    console.log(value);
}
```

If only strings and numbers are accepted:

```typescript
function printValue(
    value:
        string | number
) {
    console.log(value);
}
```

Union types preserve flexibility without sacrificing safety.

---

# 68. Union Parameters vs `unknown`

If valid inputs are known:

```typescript
string | number
```

is better than:

```typescript
unknown
```

because TypeScript already knows the allowed possibilities.

Use:

```text
union
→ known set of possibilities

unknown
→ possibilities are genuinely unknown
```

---

# 69. Union vs Optional Property

These model different things.

```typescript
type User = {
    phone?: string;
};
```

means `phone` may be absent.

Whereas:

```typescript
type User = {
    phone:
        string | null;
};
```

means the property is part of the shape but may explicitly contain `null`.

And:

```typescript
type User = {
    phone?:
        string | null;
};
```

can allow:

```text
string
null
undefined/absent
```

depending on compiler settings and usage.

---

# 70. Union Type Narrowing Through Assignment

```typescript
let value:
    string | number;

value = "Hello";
```

At that point TypeScript knows:

```text
value
→ string
```

So:

```typescript
value.toUpperCase();
```

can be valid.

Later:

```typescript
value = 100;
```

TypeScript now knows:

```text
value
→ number
```

TypeScript tracks types through control flow and assignments.

---

# 71. Union Narrowing Through Equality

```typescript
type Status =
    "loading"
    | "success"
    | "error";

function handle(
    status: Status
) {
    if (
        status === "success"
    ) {
        // status is "success"
    }
}
```

The equality check narrows the literal union.

---

# 72. Union Narrowing Through Truthiness

```typescript
function printName(
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

This removes `null`, but remember:

```text
""
```

is also falsy.

So truthiness narrowing may exclude more than just `null`.

Use explicit checks when empty strings are meaningful.

---

# 73. Better Explicit Null Check

```typescript
function printName(
    name:
        string | null
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

Now:

```text
""
```

is still accepted as a valid string.

---

# 74. Exhaustive Union Handling

Suppose:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

We can handle every case:

```typescript
function handleStatus(
    status: Status
) {
    switch (status) {
        case "loading":
            return "Loading";

        case "success":
            return "Done";

        case "error":
            return "Failed";

        default: {
            const exhaustive:
                never =
                status;

            return exhaustive;
        }
    }
}
```

If all possibilities are handled:

```text
status
→ never
```

inside `default`.

---

# 75. Why Exhaustiveness Checking Is Useful

Suppose later we change:

```typescript
type Status =
    "loading"
    | "success"
    | "error"
    | "cancelled";
```

but forget to update the `switch`.

Then:

```typescript
const exhaustive:
    never =
    status;
```

can fail because `status` may now be:

```text
"cancelled"
```

This catches missing cases during development.

---

# 76. Intersection with Optional Properties

```typescript
type A = {
    name?: string;
};

type B = {
    age: number;
};

type C =
    A & B;
```

Then:

```typescript
const value: C = {
    age: 25
};
```

is valid.

Because:

```text
name
→ optional

age
→ required
```

Intersection preserves the requirements of each component.

---

# 77. Intersection with Readonly Properties

```typescript
type Identifiable = {
    readonly id: string;
};

type Named = {
    name: string;
};

type Entity =
    Identifiable
    & Named;
```

Then:

```typescript
const entity: Entity = {
    id: "E1",
    name: "Example"
};
```

This is invalid:

```typescript
entity.id = "E2";
```

The `readonly` constraint remains.

---

# 78. Intersection Property Compatibility

Suppose:

```typescript
type A = {
    value:
        string | number;
};

type B = {
    value: string;
};

type C =
    A & B;
```

To satisfy both:

```text
value must satisfy

string | number

AND

string
```

The overlap is:

```text
string
```

So conceptually:

```typescript
C["value"]
```

is:

```text
string
```

---

# 79. Intersection Can Narrow Types

Example:

```typescript
type A =
    "admin"
    | "user";

type B =
    "admin"
    | "manager";

type C =
    A & B;
```

Only:

```text
"admin"
```

exists in both.

Therefore:

```text
C
→ "admin"
```

Intersection is not simply "merge objects."

It means:

```text
values satisfying both types
```

---

# 80. Union Can Broaden Types

```typescript
type A =
    "admin";

type B =
    "user";

type C =
    A | B;
```

Now:

```text
C
→ "admin" OR "user"
```

Union broadens the set of possible values.

---

# 81. Broadening vs Narrowing Mental Model

```text
UNION

"admin"
   +
"user"

   ↓

"admin" | "user"

More possible values.


INTERSECTION

"admin" | "user"
        &
"admin" | "manager"

        ↓

      "admin"

Fewer possible values.
```

---

# 82. Union of Related Domain Models

```typescript
type CreditCardPayment = {
    type: "card";
    cardLast4: string;
};

type BankPayment = {
    type: "bank";
    accountId: string;
};

type WalletPayment = {
    type: "wallet";
    walletId: string;
};

type Payment =
    CreditCardPayment
    | BankPayment
    | WalletPayment;
```

Then:

```typescript
function processPayment(
    payment: Payment
) {
    switch (payment.type) {
        case "card":
            return payment.cardLast4;

        case "bank":
            return payment.accountId;

        case "wallet":
            return payment.walletId;
    }
}
```

Each variant carries only the fields that make sense for that state.

---

# 83. Avoid One Giant Optional Object

Less precise:

```typescript
type Payment = {
    type:
        "card"
        | "bank"
        | "wallet";

    cardLast4?: string;
    accountId?: string;
    walletId?: string;
};
```

This allows:

```typescript
{
    type: "card",
    walletId: "W123"
}
```

which is logically inconsistent.

Discriminated unions encode relationships between fields.

---

# 84. Intersection for Metadata

Suppose many API entities share metadata:

```typescript
type Metadata = {
    createdAt: string;
    updatedAt: string;
};

type UserData = {
    id: string;
    name: string;
};

type UserResponse =
    UserData
    & Metadata;
```

Now the resulting object requires both:

```text
User data
AND
Metadata
```

---

# 85. Intersection with Authentication Context

```typescript
type User = {
    id: string;
    username: string;
};

type Session = {
    sessionId: string;
    expiresAt: Date;
};

type AuthContext =
    User & Session;
```

Then:

```typescript
const context:
    AuthContext = {
        id: "U1",
        username: "Alice",
        sessionId: "S1",
        expiresAt:
            new Date()
    };
```

---

# 86. Common Mistake — Using `any` Instead of Union

Avoid:

```typescript
let id: any;
```

when the actual possibilities are known.

Prefer:

```typescript
let id:
    string | number;
```

---

# 87. Common Mistake — Calling Type-Specific Methods Directly

Invalid:

```typescript
function format(
    value:
        string | number
) {
    return value.toUpperCase();
}
```

because `number` does not have `toUpperCase()`.

Narrow first:

```typescript
function format(
    value:
        string | number
) {
    if (
        typeof value ===
        "string"
    ) {
        return value.toUpperCase();
    }

    return value.toFixed(2);
}
```

---

# 88. Common Mistake — Confusing `|` and `&`

Remember:

```text
|
→ OR

&
→ AND
```

Example:

```typescript
Admin | User
```

means either.

```typescript
Admin & User
```

means both contracts simultaneously.

---

# 89. Common Mistake — Assuming Object Union Combines Properties

```typescript
type A = {
    name: string;
};

type B = {
    age: number;
};

type C =
    A | B;
```

Do not assume `C` means:

```typescript
{
    name: string;
    age: number;
}
```

That would be closer to:

```typescript
A & B
```

---

# 90. Common Mistake — Intersecting Conflicting Properties

Avoid:

```typescript
type A = {
    id: string;
};

type B = {
    id: number;
};

type C =
    A & B;
```

because `id` becomes impossible to satisfy normally.

Design compatible contracts.

---

# 91. Common Mistake — Too Many Optional Properties

Avoid:

```typescript
type State = {
    loading?: boolean;
    data?: User[];
    error?: string;
};
```

if these fields represent mutually exclusive states.

Prefer:

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
        message: string;
    };
```

---

# 92. Common Mistake — Broad String Instead of Literal Union

Avoid:

```typescript
type User = {
    role: string;
};
```

if valid roles are known.

Prefer:

```typescript
type Role =
    "admin"
    | "user"
    | "manager";

type User = {
    role: Role;
};
```

---

# 93. Common Mistake — Non-Discriminated Related Objects

Less convenient:

```typescript
type Success = {
    data: string[];
};

type Failure = {
    error: string;
};

type Result =
    Success | Failure;
```

This can still work with:

```typescript
"data" in result
```

But when variants represent explicit states, a discriminator is often clearer:

```typescript
type Result =
    | {
        status: "success";
        data: string[];
    }
    | {
        status: "error";
        message: string;
    };
```

---

# 94. Common Mistake — Using Assertions Instead of Narrowing

Avoid:

```typescript
function printId(
    id:
        string | number
) {
    console.log(
        (id as string)
            .toUpperCase()
    );
}
```

If `id` is actually a number, the assertion did not convert it.

Prefer:

```typescript
if (
    typeof id === "string"
) {
    console.log(
        id.toUpperCase()
    );
}
```

---

# 95. Common Mistake — Forgetting Parentheses with Union Arrays

If you mean:

```text
array whose elements can be
string OR number
```

write:

```typescript
(string | number)[]
```

Do not confuse it with:

```typescript
string[] | number[]
```

They represent different structures.

---

# 96. Common Mistake — Overusing Intersections

Do not split every property into its own type merely to combine everything later.

This:

```typescript
type User = {
    id: string;
    name: string;
    email: string;
};
```

may be clearer than unnecessary composition.

Use intersections when the component contracts are meaningful and reusable.

---

# 97. Interview Question — What Is a Union Type?

A union allows a value to belong to one of multiple types.

```typescript
let id:
    string | number;
```

The value can be either a string or a number.

---

# 98. Interview Question — What Is an Intersection Type?

An intersection requires a value to satisfy multiple types simultaneously.

```typescript
type User =
    Person & Employee;
```

The resulting value must satisfy both contracts.

---

# 99. Interview Question — Union vs Intersection?

```text
Union
A | B
→ A OR B

Intersection
A & B
→ A AND B
```

Union expands possibilities.

Intersection restricts values to those satisfying both types.

---

# 100. Interview Question — What Is a Discriminated Union?

A discriminated union is a union of object types that share a common property with distinct literal values.

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

`status` identifies the active variant.

---

# 101. Interview Question — Why Use Discriminated Unions?

They:

```text
Model state variants clearly

Connect fields to specific states

Improve narrowing

Prevent contradictory states

Enable exhaustive checking
```

They are especially useful for:

```text
API results
React state
Authentication state
Payments
Async operations
Workflows
```

---

# 102. Interview Question — Can a Union Contain `null`?

Yes.

```typescript
let user:
    User | null;
```

This means the value can be either a `User` or `null`.

---

# 103. Interview Question — Can Union Types Be Function Parameters?

Yes.

```typescript
function printId(
    id:
        string | number
) {}
```

The function accepts either type.

---

# 104. Interview Question — Why Must Unions Be Narrowed?

Because TypeScript only permits operations that are safe for the currently known possibilities.

If a method exists only on one union member, you must first establish that the value is that member.

---

# 105. Interview Question — What Happens with `string | never`?

Conceptually:

```text
string | never
→ string
```

Because `never` contributes no possible values.

---

# 106. Interview Question — What Happens with `string | unknown`?

```text
string | unknown
→ unknown
```

Because `unknown` already covers all possible values.

---

# 107. Interview Question — What Happens with `string & number`?

Conceptually:

```text
string & number
→ never
```

because no ordinary value can simultaneously satisfy both primitive types.

---

# 108. Interview Question — What Happens with Conflicting Intersection Properties?

Example:

```typescript
type A = {
    id: string;
};

type B = {
    id: number;
};

type C =
    A & B;
```

The resulting `id` must satisfy:

```text
string & number
```

which is effectively impossible:

```text
never
```

---

# 109. Interview Question — What Is Exhaustiveness Checking?

It ensures every possible member of a union has been handled.

A common pattern uses `never`:

```typescript
default: {
    const exhaustive:
        never =
        value;

    return exhaustive;
}
```

If a union member remains unhandled, TypeScript reports an error.

---

# 110. Interview Question — `(string | number)[]` vs `string[] | number[]`?

```typescript
(string | number)[]
```

means one array may contain both strings and numbers.

```typescript
string[] | number[]
```

means the value is either a string array or a number array.

---

# 111. Interview Question — Why Prefer Literal Unions over `string`?

Literal unions restrict values to known valid options.

```typescript
type Role =
    "admin"
    | "user";
```

This prevents invalid strings and improves autocomplete and narrowing.

---

# 112. Union & Intersection Cheat Sheet

```text
UNION
================================

A | B

A OR B


EXAMPLE
================================

string | number


FUNCTION PARAMETER
================================

function print(
    value:
        string | number
) {}


NULLABLE
================================

User | null


OPTIONAL RESULT
================================

User | undefined


LITERAL UNION
================================

"loading"
| "success"
| "error"


ARRAY OF UNION
================================

(string | number)[]


UNION OF ARRAYS
================================

string[] | number[]


OBJECT UNION
================================

Admin | User


DISCRIMINATED UNION
================================

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
        message: string;
    };


INTERSECTION
================================

A & B

A AND B


OBJECT INTERSECTION
================================

type Person = {
    name: string;
};

type Employee = {
    id: number;
};

type Staff =
    Person & Employee;


RESULT
================================

{
    name: string;
    id: number;
}


IMPOSSIBLE INTERSECTION
================================

string & number

→ never


UNION WITH NEVER
================================

string | never

→ string


UNION WITH UNKNOWN
================================

string | unknown

→ unknown


CORE RULE
================================

|
→ OR

&
→ AND
```

---

# 113. Important Rules to Remember

```text
1. Union types use |.

2. Intersection types use &.

3. A | B means A OR B.

4. A & B means A AND B.

5. Unions describe multiple possible types.

6. Intersections require multiple contracts.

7. Union members can be primitives.

8. Union members can be literals.

9. Union members can be objects.

10. Union members can include null.

11. Union members can include undefined.

12. Union members can include functions.

13. Unions are useful when runtime possibilities are known.

14. Prefer a union over any when valid possibilities are known.

15. unknown is different from a known union.

16. Type-specific operations on unions require narrowing.

17. Operations common to all union members are safe.

18. typeof can narrow primitive unions.

19. Equality checks can narrow literal unions.

20. The in operator can narrow object unions.

21. Discriminated unions use a common discriminator property.

22. Discriminators usually use literal types.

23. status is a common discriminator.

24. type and kind are also common discriminators.

25. Discriminated unions model state machines well.

26. Discriminated unions are useful for API results.

27. Discriminated unions are useful for React state.

28. Discriminated unions are useful for authentication.

29. Discriminated unions are useful for workflows.

30. Discriminated unions prevent contradictory state combinations.

31. Prefer modeling relationships between fields explicitly.

32. Optional properties are not always a replacement for unions.

33. string | number is broader than either member individually.

34. Union expands possible values.

35. Intersection reduces values to those satisfying all members.

36. Object intersections commonly appear to combine properties.

37. The deeper rule is that the value must satisfy every intersected type.

38. Intersections can combine reusable contracts.

39. Intersections can preserve readonly properties.

40. Intersections can preserve optional properties.

41. Intersections can narrow compatible property types.

42. Conflicting intersections can produce never.

43. string & number is effectively never.

44. Different incompatible literal intersections produce never.

45. Compatible intersections keep overlapping values.

46. "admin" | "user" combined with "admin" | "manager" via & leaves "admin".

47. never contributes nothing to a union.

48. string | never simplifies to string.

49. unknown dominates ordinary union members.

50. string | unknown simplifies to unknown.

51. any in a union destroys useful safety.

52. Avoid unnecessary any.

53. (A | B)[] means an array containing A or B elements.

54. A[] | B[] means either an A array or a B array.

55. These array types are not equivalent.

56. Parentheses make complex types clearer.

57. A & (B | C) means A plus either B or C.

58. Unions and intersections can be combined.

59. Base contracts can be intersected with variant unions.

60. Literal unions provide controlled value sets.

61. Literal unions are usually safer than broad strings for known options.

62. Union function returns should model real possibilities.

63. If a function can return nothing, model null or undefined where appropriate.

64. Do not lie about return types merely to simplify caller code.

65. Narrow nullable values before unsafe access.

66. Optional chaining can work with nullable unions.

67. Nullish coalescing can provide fallbacks.

68. Truthiness narrowing may exclude valid falsy values.

69. Empty strings are falsy.

70. Zero is falsy.

71. Explicit null checks are safer when falsy values remain valid.

72. TypeScript tracks union members through control flow.

73. Assignment can narrow a union.

74. Branch conditions can narrow a union.

75. switch statements work well with discriminated unions.

76. never enables exhaustive union checking.

77. Exhaustive checking helps when unions evolve.

78. Adding a new union member can reveal unhandled logic.

79. Union object members should ideally have clear distinguishing properties.

80. A discriminator makes narrowing easier.

81. Union does not mean merged object properties.

82. Intersection does not simply mean property concatenation.

83. Think in terms of possible value sets.

84. A union combines possible value sets.

85. An intersection keeps overlapping valid values.

86. Broad types allow more possible values.

87. Narrow types allow fewer possible values.

88. Literal unions are narrower than string.

89. never contains no possible values.

90. unknown conceptually covers all possible values.

91. Use intersections for meaningful reusable contracts.

92. Do not create excessive tiny types solely for intersection composition.

93. Readability matters more than clever typing.

94. Use objects instead of complicated tuple/union structures when clearer.

95. Use discriminated unions instead of unrelated boolean flags for mutually exclusive states.

96. Make invalid states harder to represent.

97. Model actual runtime states.

98. Do not use assertions to avoid narrowing.

99. Assertions do not perform conversion.

100. Assertions do not perform validation.

101. Union types are fundamental to TypeScript.

102. Intersection types are fundamental to type composition.

103. Narrowing makes unions practical.

104. never makes exhaustive unions safer.

105. Literal types make discriminated unions possible.

106. Type aliases make unions reusable.

107. Interfaces and intersections can also work together.

108. Generics can contain and return unions.

109. Conditional types rely heavily on unions and never.

110. React state modeling benefits heavily from discriminated unions.
```

---

# 114. Final Mental Model

## Union

```text
       Type A
         │
         │
         ▼

        OR

         ▲
         │
         │
       Type B


A | B
```

The value can satisfy either type.

Example:

```typescript
string | number
```

---

## Intersection

```text
       Type A
         │
         │
         ▼

        AND

         ▲
         │
         │
       Type B


A & B
```

The value must satisfy both types.

Example:

```typescript
type User = {
    name: string;
};

type Employee = {
    employeeId: number;
};

type Staff =
    User & Employee;
```

Result:

```text
Staff
│
├── name
└── employeeId
```

---

## Discriminated Union

```text
                  State
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼

     loading     success       error
                    │             │
                    ▼             ▼
                   data         message
```

Represented as:

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
        message: string;
    };
```

The key design principle is:

```text
Don't ask:

"How can I make this value
accept everything?"

Ask:

"What values can actually
exist in my application?"

Then model exactly those
possibilities.
```
