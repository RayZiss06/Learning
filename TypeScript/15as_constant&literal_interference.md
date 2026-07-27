# TypeScript — `as const` and Literal Inference

TypeScript does not always preserve the exact value of a variable as its type.

For example:

```typescript
let role = "admin";
```

TypeScript infers:

```typescript
let role: string;
```

rather than:

```typescript
let role: "admin";
```

Why?

Because a `let` variable can change:

```typescript
role = "user";
role = "guest";
```

TypeScript therefore **widens** `"admin"` into `string`.

Understanding:

* Literal Types
* Literal Widening
* Literal Inference
* `const`
* `as const`

is important when building strongly typed constants, configuration objects, routes, events, actions, API definitions, and React applications.

---

# 1. What Is a Literal Type?

A literal type represents one exact value.

Example:

```typescript
type Role = "admin";
```

Only this value is allowed:

```typescript
const role: Role = "admin";
```

This is invalid:

```typescript
const role: Role = "user";
// Error
```

because:

```typescript
Role
```

does not mean:

```typescript
string
```

It means exactly:

```text
"admin"
```

---

# 2. Primitive Type vs Literal Type

Primitive type:

```typescript
string
```

represents any string.

Literal type:

```typescript
"admin"
```

represents exactly one string.

Similarly:

```typescript
number
```

means any number.

While:

```typescript
100
```

as a type means exactly:

```text
100
```

And:

```typescript
boolean
```

means:

```text
true or false
```

while:

```typescript
true
```

means exactly:

```text
true
```

---

# 3. Literal Type Examples

String:

```typescript
type Status = "success";
```

Number:

```typescript
type Port = 3000;
```

Boolean:

```typescript
type Enabled = true;
```

These represent exact values.

---

# 4. Literal Union Types

Literal types are commonly combined into unions.

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

Then:

```typescript
let role: Role;

role = "admin";
role = "user";
role = "guest";
```

But:

```typescript
role = "manager";
// Error
```

We already covered unions earlier. The important point here is that each member is a **literal type**.

---

# 5. What Is Type Widening?

Type widening happens when TypeScript converts a specific literal type into a broader type.

For example:

```typescript
let username = "Alice";
```

TypeScript generally infers:

```typescript
string
```

rather than:

```typescript
"Alice"
```

Because:

```typescript
username = "Bob";
```

is valid.

Conceptually:

```text
"Alice"
   ↓
string
```

This is called **literal widening**.

---

# 6. Number Widening

```typescript
let port = 3000;
```

TypeScript infers:

```typescript
number
```

rather than:

```typescript
3000
```

because:

```typescript
port = 8080;
```

is allowed.

Conceptually:

```text
3000
 ↓
number
```

---

# 7. Boolean Widening

```typescript
let enabled = true;
```

TypeScript infers:

```typescript
boolean
```

because:

```typescript
enabled = false;
```

is allowed.

Conceptually:

```text
true
 ↓
boolean
```

---

# 8. `let` and Literal Widening

Consider:

```typescript
let role = "admin";
```

TypeScript asks:

```text
Can role change?
```

Yes.

Therefore:

```text
"admin"
   ↓
string
```

Similarly:

```typescript
let count = 10;
// number

let active = true;
// boolean
```

---

# 9. `const` Preserves Primitive Literals

Now consider:

```typescript
const role = "admin";
```

Since `role` cannot be reassigned, TypeScript can infer:

```typescript
const role: "admin";
```

rather than:

```typescript
string
```

Similarly:

```typescript
const port = 3000;
// 3000

const enabled = true;
// true
```

---

# 10. `let` vs `const`

```typescript
let role = "admin";
```

typically gives:

```typescript
string
```

while:

```typescript
const role = "admin";
```

gives:

```typescript
"admin"
```

Mental model:

```text
let

"admin"
   ↓
Can change
   ↓
string


const

"admin"
   ↓
Cannot be reassigned
   ↓
"admin"
```

---

# 11. Why Doesn't `const` Make Object Properties Literal?

Consider:

```typescript
const user = {
    role: "admin"
};
```

You might expect:

```typescript
{
    role: "admin";
}
```

But TypeScript generally infers:

```typescript
{
    role: string;
}
```

Why?

Because although:

```typescript
user
```

cannot be reassigned:

```typescript
user = {};
// Error
```

its properties can still change:

```typescript
user.role = "user";
```

Therefore `role` must allow other strings.

---

# 12. `const` Does Not Make Objects Immutable

This is valid:

```typescript
const user = {
    name: "Alice"
};

user.name = "Bob";
```

The variable:

```typescript
user
```

cannot point to another object.

But the existing object's properties remain mutable.

Think:

```text
const user

prevents:

user = anotherObject


It does NOT prevent:

user.name = "Bob"
```

---

# 13. Array Inference with `const`

Consider:

```typescript
const roles = [
    "admin",
    "user"
];
```

TypeScript generally infers:

```typescript
string[]
```

rather than:

```typescript
["admin", "user"]
```

Why?

Because the array can still change:

```typescript
roles.push("guest");
```

and:

```typescript
roles[0] = "manager";
```

---

# 14. The Problem

Sometimes we want TypeScript to preserve the exact values.

For example:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
];
```

We may want TypeScript to understand:

```text
Only these three roles exist.
```

But ordinary inference gives:

```typescript
string[]
```

This is where:

```typescript
as const
```

becomes useful.

---

# 15. What Is `as const`?

`as const` is a **const assertion**.

Example:

```typescript
const role =
    "admin" as const;
```

TypeScript preserves the exact literal:

```typescript
"admin"
```

instead of widening it to:

```typescript
string
```

---

# 16. Basic Syntax

```typescript
value as const
```

Examples:

```typescript
const role =
    "admin" as const;

const port =
    3000 as const;

const enabled =
    true as const;
```

The inferred types are:

```text
"admin"

3000

true
```

---

# 17. `as const` with `let`

Technically:

```typescript
let role =
    "admin" as const;
```

gives `role` the type:

```typescript
"admin"
```

Therefore:

```typescript
role = "user";
// Error
```

This is usually awkward.

If the variable should never change, prefer:

```typescript
const role = "admin";
```

rather than:

```typescript
let role = "admin" as const;
```

---

# 18. `as const` with Objects

Consider:

```typescript
const user = {
    role: "admin",
    active: true
} as const;
```

TypeScript infers something equivalent to:

```typescript
{
    readonly role: "admin";
    readonly active: true;
}
```

Notice two important effects:

```text
Exact literal values preserved

Properties become readonly
```

---

# 19. Object Without `as const`

```typescript
const user = {
    role: "admin",
    active: true
};
```

Type:

```typescript
{
    role: string;
    active: boolean;
}
```

Therefore:

```typescript
user.role = "user";
user.active = false;
```

is allowed.

---

# 20. Object With `as const`

```typescript
const user = {
    role: "admin",
    active: true
} as const;
```

Type:

```typescript
{
    readonly role: "admin";
    readonly active: true;
}
```

Now:

```typescript
user.role = "user";
// Error
```

and:

```typescript
user.active = false;
// Error
```

---

# 21. Object Comparison

Without:

```typescript
as const
```

```typescript
const config = {
    environment: "production",
    port: 3000
};
```

Type:

```typescript
{
    environment: string;
    port: number;
}
```

With:

```typescript
as const
```

```typescript
const config = {
    environment: "production",
    port: 3000
} as const;
```

Type:

```typescript
{
    readonly environment:
        "production";

    readonly port:
        3000;
}
```

---

# 22. `as const` with Arrays

Consider:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;
```

Instead of:

```typescript
string[]
```

TypeScript infers a readonly tuple:

```typescript
readonly [
    "admin",
    "user",
    "guest"
]
```

This is one of the most important uses of `as const`.

---

# 23. Array Without `as const`

```typescript
const roles = [
    "admin",
    "user",
    "guest"
];
```

Type:

```typescript
string[]
```

Therefore:

```typescript
roles.push("manager");
```

is valid.

---

# 24. Array With `as const`

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;
```

Type:

```typescript
readonly [
    "admin",
    "user",
    "guest"
]
```

Now:

```typescript
roles.push("manager");
// Error
```

because the tuple is readonly.

---

# 25. Tuple Inference

Without:

```typescript
as const
```

```typescript
const user = [
    1,
    "Alice"
];
```

TypeScript may infer:

```typescript
(string | number)[]
```

With:

```typescript
const user = [
    1,
    "Alice"
] as const;
```

TypeScript infers:

```typescript
readonly [
    1,
    "Alice"
]
```

Now TypeScript knows:

```text
Index 0 = 1

Index 1 = "Alice"

Length = 2
```

---

# 26. Deriving a Union from an Array

This is one of the most useful patterns in TypeScript.

Start with:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
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
    | "guest";
```

---

# 27. How `typeof roles[number]` Works

First:

```typescript
typeof roles
```

is:

```typescript
readonly [
    "admin",
    "user",
    "guest"
]
```

Then:

```typescript
typeof roles[number]
```

means:

```text
Give me the type of any valid
numeric element in this tuple.
```

The elements are:

```text
"admin"
"user"
"guest"
```

Therefore:

```typescript
"admin"
| "user"
| "guest"
```

---

# 28. Why This Pattern Is Important

Without it, you might write:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
];

type Role =
    "admin"
    | "user"
    | "guest";
```

Now the same information exists twice.

If you add:

```text
"manager"
```

to the array but forget to update the type, they become inconsistent.

Instead:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;

type Role =
    typeof roles[number];
```

creates one source of truth.

---

# 29. Runtime Values + Compile-Time Type

This pattern is especially powerful because:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;
```

exists at runtime.

You can use:

```typescript
roles.map(...);
roles.includes(...);
```

while:

```typescript
type Role =
    typeof roles[number];
```

exists only at compile time.

So one definition gives us:

```text
Runtime values
+
Compile-time type
```

---

# 30. Real Example — User Roles

```typescript
const USER_ROLES = [
    "admin",
    "editor",
    "viewer"
] as const;

type UserRole =
    typeof USER_ROLES[number];
```

Now:

```typescript
function assignRole(
    role: UserRole
) {}
```

Valid:

```typescript
assignRole("admin");
assignRole("editor");
```

Invalid:

```typescript
assignRole("owner");
// Error
```

---

# 31. Real Example — Application Environments

```typescript
const ENVIRONMENTS = [
    "development",
    "staging",
    "production"
] as const;

type Environment =
    typeof ENVIRONMENTS[number];
```

Now:

```typescript
function deploy(
    environment: Environment
) {}
```

TypeScript restricts the values to the declared environments.

---

# 32. Real Example — HTTP Methods

```typescript
const HTTP_METHODS = [
    "GET",
    "POST",
    "PUT",
    "PATCH",
    "DELETE"
] as const;

type HttpMethod =
    typeof HTTP_METHODS[number];
```

Result:

```typescript
type HttpMethod =
    "GET"
    | "POST"
    | "PUT"
    | "PATCH"
    | "DELETE";
```

---

# 33. Deriving Types from Objects

`as const` is also useful with objects.

```typescript
const STATUS = {
    SUCCESS: "success",
    ERROR: "error",
    LOADING: "loading"
} as const;
```

Now the object type is approximately:

```typescript
{
    readonly SUCCESS:
        "success";

    readonly ERROR:
        "error";

    readonly LOADING:
        "loading";
}
```

---

# 34. Deriving Object Keys

Given:

```typescript
const STATUS = {
    SUCCESS: "success",
    ERROR: "error",
    LOADING: "loading"
} as const;
```

We can derive:

```typescript
type StatusKey =
    keyof typeof STATUS;
```

Result:

```typescript
type StatusKey =
    "SUCCESS"
    | "ERROR"
    | "LOADING";
```

---

# 35. Deriving Object Values

We can derive the value union:

```typescript
type Status =
    typeof STATUS[
        keyof typeof STATUS
    ];
```

Result:

```typescript
type Status =
    "success"
    | "error"
    | "loading";
```

This is another important TypeScript pattern.

---

# 36. Object Value Union Mental Model

Given:

```typescript
const STATUS = {
    SUCCESS: "success",
    ERROR: "error"
} as const;
```

First:

```typescript
typeof STATUS
```

gives the object type.

Then:

```typescript
keyof typeof STATUS
```

gives:

```typescript
"SUCCESS" | "ERROR"
```

Then:

```typescript
typeof STATUS[
    keyof typeof STATUS
]
```

asks:

```text
What are the types of the values
at all these keys?
```

Result:

```typescript
"success" | "error"
```

---

# 37. `as const` as an Enum-Like Pattern

Instead of:

```typescript
enum Role {
    Admin = "admin",
    User = "user",
    Guest = "guest"
}
```

you can use:

```typescript
const Role = {
    Admin: "admin",
    User: "user",
    Guest: "guest"
} as const;
```

Then derive:

```typescript
type Role =
    typeof Role[
        keyof typeof Role
    ];
```

Result:

```typescript
"admin"
| "user"
| "guest"
```

---

# 38. Using the Enum-Like Object

```typescript
const Role = {
    Admin: "admin",
    User: "user",
    Guest: "guest"
} as const;
```

Runtime usage:

```typescript
console.log(
    Role.Admin
);
```

Result:

```text
admin
```

Type usage:

```typescript
type RoleValue =
    typeof Role[
        keyof typeof Role
    ];
```

So the same object provides:

```text
Runtime constants
+
Compile-time union
```

---

# 39. `enum` vs `as const` Object

Enum:

```typescript
enum Status {
    Success = "success",
    Error = "error"
}
```

Const object:

```typescript
const Status = {
    Success: "success",
    Error: "error"
} as const;
```

Both can model fixed named values.

The `as const` object approach uses normal JavaScript objects and makes it easy to derive literal unions.

Neither approach is universally better.

Choose based on your codebase and requirements.

---

# 40. `as const` with Configuration Objects

Consider:

```typescript
const config = {
    environment: "production",
    apiVersion: "v1",
    retries: 3
} as const;
```

TypeScript preserves:

```typescript
{
    readonly environment:
        "production";

    readonly apiVersion:
        "v1";

    readonly retries:
        3;
}
```

This is useful when values represent fixed configuration constants.

---

# 41. Without `as const` in Configuration

```typescript
const config = {
    environment: "production",
    apiVersion: "v1"
};
```

TypeScript sees:

```typescript
{
    environment: string;
    apiVersion: string;
}
```

If exact values matter to downstream types, this widening may lose useful information.

---

# 42. Literal Inference and Function Arguments

Suppose:

```typescript
function handleRole(
    role:
        "admin"
        | "user"
) {}
```

This works:

```typescript
handleRole("admin");
```

because the literal `"admin"` is directly available to TypeScript.

But consider:

```typescript
let role = "admin";

handleRole(role);
```

This can fail because:

```typescript
role
```

was inferred as:

```typescript
string
```

and `string` could contain values other than `"admin"` or `"user"`.

---

# 43. Fixing Widened Variables

Instead of:

```typescript
let role = "admin";
```

if it should never change:

```typescript
const role = "admin";
```

Then:

```typescript
handleRole(role);
```

works because the type is:

```typescript
"admin"
```

If the variable genuinely needs to change between valid roles, give it an explicit union type:

```typescript
let role:
    "admin"
    | "user"
    = "admin";

role = "user";
```

Do not use `as const` when the value is intentionally mutable.

---

# 44. Object Property Widening Problem

Suppose:

```typescript
function createUser(
    user: {
        role:
            "admin"
            | "user";
    }
) {}
```

Now:

```typescript
const user = {
    role: "admin"
};

createUser(user);
```

can produce a type error because:

```typescript
user.role
```

was inferred as:

```typescript
string
```

not:

```typescript
"admin"
```

---

# 45. Fixing Object Literal Widening

One solution:

```typescript
const user = {
    role: "admin"
} as const;
```

Now:

```typescript
createUser(user);
```

works because:

```typescript
user.role
```

has type:

```typescript
"admin"
```

But remember:

```typescript
as const
```

also makes the property readonly at the type level.

---

# 46. Another Solution — Explicit Type

Instead of:

```typescript
as const
```

we could define:

```typescript
type User = {
    role:
        "admin"
        | "user";
};
```

Then:

```typescript
const user: User = {
    role: "admin"
};
```

Now:

```typescript
user.role = "user";
```

is allowed.

This is better if the property should remain mutable.

---

# 47. `as const` vs Explicit Annotation

`as const`:

```typescript
const user = {
    role: "admin"
} as const;
```

gives:

```typescript
{
    readonly role: "admin";
}
```

Explicit annotation:

```typescript
const user: {
    role:
        "admin"
        | "user";
} = {
    role: "admin"
};
```

gives:

```typescript
{
    role:
        "admin"
        | "user";
}
```

The first represents a fixed value.

The second represents a mutable value constrained to a set of possibilities.

---

# 48. `as const` Is Not Just `readonly`

Consider:

```typescript
const user: {
    readonly role: string;
} = {
    role: "admin"
};
```

Here:

```typescript
user.role
```

is readonly, but its type is still:

```typescript
string
```

With:

```typescript
const user = {
    role: "admin"
} as const;
```

the property is:

```typescript
readonly role: "admin";
```

So `as const` does two relevant things here:

```text
Preserves the literal
+
Makes the property readonly
```

---

# 49. `readonly` vs `as const`

```typescript
type Config = {
    readonly environment: string;
};
```

means:

```text
environment cannot be reassigned
but its type is string.
```

While:

```typescript
const config = {
    environment: "production"
} as const;
```

means:

```text
environment cannot be reassigned
and its type is exactly "production".
```

---

# 50. `as const` Is Not Runtime Immutability

This is very important.

```typescript
const config = {
    environment: "production"
} as const;
```

TypeScript prevents:

```typescript
config.environment =
    "development";
```

at compile time.

But `as const` does **not** freeze the JavaScript object at runtime.

It does not automatically call:

```typescript
Object.freeze(config);
```

TypeScript types disappear during compilation.

---

# 51. `Object.freeze()` vs `as const`

```typescript
as const
```

is primarily a TypeScript type-system feature.

```typescript
Object.freeze()
```

is a JavaScript runtime operation.

Example:

```typescript
const config =
    Object.freeze({
        environment:
            "production"
    });
```

This freezes the object at runtime according to JavaScript's `Object.freeze` behavior.

Do not confuse:

```text
Compile-time readonly
```

with:

```text
Runtime immutability
```

---

# 52. `Object.freeze()` Is Shallow

Even when runtime immutability matters, remember that ordinary:

```typescript
Object.freeze()
```

is shallow.

Nested objects require separate handling if deep runtime immutability is required.

Likewise, TypeScript's `as const` behavior should not be treated as a runtime deep-freezing mechanism.

---

# 53. Nested Object with `as const`

Consider:

```typescript
const config = {
    database: {
        host: "localhost",
        port: 5432
    }
} as const;
```

TypeScript preserves nested literals and readonly information:

```typescript
{
    readonly database: {
        readonly host:
            "localhost";

        readonly port:
            5432;
    };
}
```

So:

```typescript
config.database.port =
    3306;
```

produces an error.

Again, this is compile-time readonly behavior.

---

# 54. Nested Arrays with `as const`

```typescript
const config = {
    roles: [
        "admin",
        "user"
    ]
} as const;
```

The nested array becomes a readonly tuple:

```typescript
{
    readonly roles:
        readonly [
            "admin",
            "user"
        ];
}
```

Therefore:

```typescript
config.roles.push(
    "guest"
);
// Error
```

---

# 55. Deriving Union from Nested Array

```typescript
const config = {
    roles: [
        "admin",
        "user",
        "guest"
    ]
} as const;
```

Then:

```typescript
type Role =
    typeof config.roles[number];
```

Result:

```typescript
"admin"
| "user"
| "guest"
```

---

# 56. Route Constants

```typescript
const ROUTES = {
    HOME: "/",
    USERS: "/users",
    PRODUCTS: "/products"
} as const;
```

Then:

```typescript
type Route =
    typeof ROUTES[
        keyof typeof ROUTES
    ];
```

Result:

```typescript
"/"
| "/users"
| "/products"
```

---

# 57. Route Function

```typescript
function navigate(
    route: Route
) {}
```

Valid:

```typescript
navigate(
    ROUTES.USERS
);
```

Valid:

```typescript
navigate(
    "/products"
);
```

Invalid:

```typescript
navigate(
    "/unknown"
);
// Error
```

---

# 58. API Endpoint Constants

```typescript
const ENDPOINTS = {
    USERS: "/api/users",
    PRODUCTS: "/api/products",
    ORDERS: "/api/orders"
} as const;
```

Derive:

```typescript
type Endpoint =
    typeof ENDPOINTS[
        keyof typeof ENDPOINTS
    ];
```

Now:

```typescript
function request(
    endpoint: Endpoint
) {}
```

accepts only the known endpoints.

---

# 59. Event Constants

```typescript
const EVENTS = {
    USER_CREATED:
        "user:created",

    USER_UPDATED:
        "user:updated",

    USER_DELETED:
        "user:deleted"
} as const;
```

Then:

```typescript
type Event =
    typeof EVENTS[
        keyof typeof EVENTS
    ];
```

Result:

```typescript
"user:created"
| "user:updated"
| "user:deleted"
```

---

# 60. Action Constants

```typescript
const ACTIONS = {
    LOGIN: "auth/login",
    LOGOUT: "auth/logout",
    REFRESH: "auth/refresh"
} as const;
```

Then:

```typescript
type Action =
    typeof ACTIONS[
        keyof typeof ACTIONS
    ];
```

This pattern is useful in event systems, reducers, state management, and other constant-driven APIs.

---

# 61. Discriminated Union Helper

Suppose:

```typescript
const success = {
    type: "success",
    data: "Completed"
} as const;
```

TypeScript knows:

```typescript
success.type
```

is exactly:

```typescript
"success"
```

not:

```typescript
string
```

This can be useful when creating discriminated union members.

---

# 62. Example Without `as const`

```typescript
const success = {
    type: "success",
    data: "Completed"
};
```

The inferred type is approximately:

```typescript
{
    type: string;
    data: string;
}
```

If `type` is supposed to act as a discriminator, widening to `string` can lose useful type information.

---

# 63. Action Creator Example

Consider:

```typescript
function createLoginAction() {
    return {
        type: "auth/login"
    } as const;
}
```

The return type contains:

```typescript
readonly type:
    "auth/login";
```

rather than:

```typescript
type: string;
```

This preserves the discriminator.

---

# 64. Derive Return Type

```typescript
function createLoginAction() {
    return {
        type: "auth/login"
    } as const;
}
```

Then:

```typescript
type LoginAction =
    ReturnType<
        typeof createLoginAction
    >;
```

Result:

```typescript
{
    readonly type:
        "auth/login";
}
```

This combines:

```text
as const
+
typeof
+
ReturnType
```

---

# 65. React-Like Props Example

Suppose a component expects:

```typescript
type ButtonProps = {
    variant:
        "primary"
        | "secondary";
};
```

Direct usage:

```typescript
const props: ButtonProps = {
    variant: "primary"
};
```

works.

But:

```typescript
const config = {
    variant: "primary"
};
```

may infer:

```typescript
{
    variant: string;
}
```

If exact literal preservation is desired:

```typescript
const config = {
    variant: "primary"
} as const;
```

Then:

```typescript
config.variant
```

is:

```typescript
"primary"
```

---

# 66. Do Not Automatically Use `as const` for Every Object

Suppose:

```typescript
const user = {
    name: "Alice",
    age: 25
} as const;
```

Now:

```typescript
user.name = "Bob";
// Error

user.age = 26;
// Error
```

If the user object is supposed to change, this is the wrong model.

Use `as const` primarily when the data represents fixed constants or when exact literal preservation is intentionally required.

---

# 67. Good `as const` Candidates

Typical examples:

```text
Routes

Roles

Permissions

Status values

HTTP methods

Events

Action types

Configuration constants

Feature names

Supported locales

Theme names

API endpoint names

Fixed option lists
```

These values usually represent a known fixed set.

---

# 68. Bad `as const` Candidates

Be careful with:

```text
Mutable form state

Editable user data

Application state that changes

Database entities being modified

Temporary mutable arrays

Objects intentionally updated over time
```

`as const` may make these unnecessarily restrictive.

---

# 69. Fixed Options for UI

```typescript
const THEMES = [
    "light",
    "dark",
    "system"
] as const;
```

Derive:

```typescript
type Theme =
    typeof THEMES[number];
```

Now:

```typescript
function setTheme(
    theme: Theme
) {}
```

accepts only:

```text
light

dark

system
```

---

# 70. Dropdown Options

```typescript
const SORT_OPTIONS = [
    "newest",
    "oldest",
    "popular"
] as const;
```

Then:

```typescript
type SortOption =
    typeof SORT_OPTIONS[number];
```

This keeps runtime options and the TypeScript union synchronized.

---

# 71. Object-Based Options with Labels

Suppose:

```typescript
const ROLES = [
    {
        value: "admin",
        label: "Administrator"
    },
    {
        value: "user",
        label: "User"
    },
    {
        value: "guest",
        label: "Guest"
    }
] as const;
```

We can derive the `value` union.

```typescript
type Role =
    typeof ROLES[number]["value"];
```

Result:

```typescript
"admin"
| "user"
| "guest"
```

---

# 72. Understanding the Previous Pattern

```typescript
typeof ROLES
```

gives the readonly tuple.

Then:

```typescript
typeof ROLES[number]
```

gives the union of each object element.

Then:

```typescript
typeof ROLES[number]["value"]
```

extracts each object's `value` type.

Result:

```typescript
"admin"
| "user"
| "guest"
```

This is extremely useful for UI option lists.

---

# 73. Deriving Labels

Using the same array:

```typescript
type RoleLabel =
    typeof ROLES[number]["label"];
```

Result:

```typescript
"Administrator"
| "User"
| "Guest"
```

---

# 74. Numeric Constants

```typescript
const RETRY_COUNTS = [
    1,
    3,
    5
] as const;
```

Then:

```typescript
type RetryCount =
    typeof RETRY_COUNTS[number];
```

Result:

```typescript
1 | 3 | 5
```

Now:

```typescript
function retry(
    count: RetryCount
) {}
```

accepts:

```typescript
retry(1);
retry(3);
retry(5);
```

but not:

```typescript
retry(10);
// Error
```

---

# 75. Mixed Tuple

```typescript
const response = [
    200,
    "Success",
    true
] as const;
```

Type:

```typescript
readonly [
    200,
    "Success",
    true
]
```

So:

```typescript
response[0]
```

is type:

```typescript
200
```

```typescript
response[1]
```

is:

```typescript
"Success"
```

```typescript
response[2]
```

is:

```typescript
true
```

---

# 76. Function Returning a Tuple

Without `as const`:

```typescript
function getUser() {
    return [
        "Alice",
        25
    ];
}
```

TypeScript may infer an array containing:

```typescript
string | number
```

instead of a fixed tuple structure.

With:

```typescript
function getUser() {
    return [
        "Alice",
        25
    ] as const;
}
```

the return type becomes:

```typescript
readonly [
    "Alice",
    25
]
```

This is useful when the returned positions have distinct meanings, though explicit tuple types are often preferable when the values themselves are not fixed literals.

---

# 77. Fixed Values vs Fixed Shape

Be careful.

If you write:

```typescript
function createUser(
    name: string,
    age: number
) {
    return [
        name,
        age
    ] as const;
}
```

the return type is effectively:

```typescript
readonly [
    string,
    number
]
```

This is useful because `as const` preserves the tuple shape even though `name` and `age` are not literal constants.

So `as const` can be useful for:

```text
Tuple shape preservation
```

as well as literal values.

---

# 78. Function Tuple Example

```typescript
function useCounter() {
    let count = 0;

    const increment = () => {
        count++;
    };

    return [
        count,
        increment
    ] as const;
}
```

TypeScript understands:

```typescript
readonly [
    number,
    () => void
]
```

rather than:

```typescript
(
    number
    | (() => void)
)[]
```

This pattern is conceptually relevant to React hooks returning tuples.

---

# 79. Why Tuple Preservation Matters

Without a tuple:

```typescript
(
    number
    | (() => void)
)[]
```

then:

```typescript
const [
    count,
    increment
] = useCounter();
```

could give both variables a broad union.

With a tuple:

```typescript
readonly [
    number,
    () => void
]
```

TypeScript knows:

```text
count
→ number

increment
→ () => void
```

Position matters.

---

# 80. Literal Widening in Function Return Objects

Consider:

```typescript
function getStatus() {
    return {
        status: "success"
    };
}
```

TypeScript generally infers:

```typescript
{
    status: string;
}
```

because the returned object itself is mutable.

Using:

```typescript
function getStatus() {
    return {
        status: "success"
    } as const;
}
```

preserves:

```typescript
{
    readonly status:
        "success";
}
```

---

# 81. Alternative — Explicit Return Type

If readonly behavior is not wanted:

```typescript
type Result = {
    status:
        "success"
        | "error";
};
```

Then:

```typescript
function getStatus(): Result {
    return {
        status: "success"
    };
}
```

Now:

```typescript
status
```

is constrained to the union without making the object readonly.

---

# 82. `as const` vs `satisfies`

You will often see:

```typescript
as const
```

and:

```typescript
satisfies
```

used near each other.

They solve different problems.

Very roughly:

```text
as const
→ Preserve exact literal information
  and readonly structure

satisfies
→ Check that a value conforms
  to a required type while retaining
  useful inference
```

We will cover `satisfies` in the next topic.

---

# 83. Combining `as const` and `satisfies`

A pattern you may encounter is:

```typescript
const routes = {
    home: "/",
    users: "/users"
} as const satisfies
    Record<string, string>;
```

Conceptually:

```text
as const
    ↓
Preserve exact values


satisfies
    ↓
Check required structure
```

We will understand this properly after learning `satisfies`.

---

# 84. `as const` vs Type Assertion

We already covered type assertions.

This:

```typescript
value as SomeType
```

tells TypeScript to treat a value as a particular type.

`as const` is a special const assertion that asks TypeScript to use the most specific literal/readonly representation available for that literal expression.

Example:

```typescript
const role =
    "admin" as const;
```

results in:

```typescript
"admin"
```

You are not manually writing the target literal type.

---

# 85. Common Mistake — Thinking `const` Makes Objects Readonly

Wrong assumption:

```typescript
const user = {
    name: "Alice"
};
```

Therefore:

```typescript
user.name = "Bob";
```

must fail.

It does not.

`const` prevents variable reassignment, not property mutation.

---

# 86. Common Mistake — Thinking `as const` Freezes Runtime Objects

```typescript
const config = {
    port: 3000
} as const;
```

does not mean JavaScript executes:

```typescript
Object.freeze(config);
```

`as const` affects TypeScript's type checking.

It is not a runtime freezing operation.

---

# 87. Common Mistake — Using `as const` Everywhere

Do not write:

```typescript
const user = {
    name: "Alice",
    age: 25,
    active: true
} as const;
```

just because stricter typing sounds better.

Ask:

```text
Should this data actually be fixed?
```

If the answer is no, `as const` may be inappropriate.

---

# 88. Common Mistake — Making Mutable State Readonly

Suppose:

```typescript
const form = {
    email: "",
    password: ""
} as const;
```

Now:

```typescript
form.email =
    "alice@example.com";
```

fails.

But form data is inherently mutable.

This is a poor use of `as const`.

---

# 89. Common Mistake — Using `let ... as const`

```typescript
let role =
    "admin" as const;
```

usually defeats the purpose of `let`, because:

```typescript
role = "user";
```

cannot happen.

Prefer:

```typescript
const role = "admin";
```

unless there is a specific reason for the unusual pattern.

---

# 90. Common Mistake — Forgetting Readonly Tuples

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

is not:

```typescript
["admin", "user"]
```

It is:

```typescript
readonly [
    "admin",
    "user"
]
```

So a function expecting:

```typescript
string[]
```

may not accept it if that function could mutate the array.

---

# 91. Readonly Array Compatibility

Suppose:

```typescript
function printRoles(
    roles: readonly string[]
) {
    roles.forEach(
        console.log
    );
}
```

Then:

```typescript
const roles = [
    "admin",
    "user"
] as const;

printRoles(roles);
```

works naturally because the function promises not to mutate the array through that parameter.

When APIs only read arrays, accepting:

```typescript
readonly T[]
```

can make them work well with const tuples.

---

# 92. Common Mistake — Losing Literal Types Before `as const`

Literal preservation works best when applied to the literal expression.

For example:

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

directly preserves the tuple.

If data has already been widened through another variable or API, adding assertions later cannot magically recover literal information TypeScript no longer knows.

The general lesson:

```text
Preserve useful literal information
at the point where it exists.
```

---

# 93. Common Mistake — Duplicating Constants and Types

Avoid:

```typescript
type Role =
    "admin"
    | "user"
    | "guest";

const roles = [
    "admin",
    "user",
    "guest"
];
```

when the array should be the source of truth.

Instead:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;

type Role =
    typeof roles[number];
```

---

# 94. But Don't Always Derive Types from Values

Sometimes the type should be the source of truth.

Example:

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

may represent a domain contract used throughout the application.

In that case, runtime values may need to conform to the type rather than define it.

This becomes especially relevant with:

```typescript
satisfies
```

which we will cover next.

---

# 95. When Should Values Be the Source of Truth?

Derive the type from values when the runtime list is the canonical definition.

Example:

```typescript
const THEMES = [
    "light",
    "dark",
    "system"
] as const;

type Theme =
    typeof THEMES[number];
```

If the application actually uses this array to render the supported theme choices, having it as the source of truth is useful.

---

# 96. When Should Types Be the Source of Truth?

Sometimes the domain contract comes first.

Example:

```typescript
type Environment =
    "development"
    | "staging"
    | "production";
```

You may then want a configuration object that must satisfy this domain model.

The upcoming:

```typescript
satisfies
```

operator is particularly useful for this scenario.

---

# 97. Interview Question — What Is `as const`?

`as const` is a TypeScript const assertion that prevents literal widening for a literal expression and gives object properties/array structures readonly literal inference.

Example:

```typescript
const role =
    "admin" as const;
```

Type:

```typescript
"admin"
```

---

# 98. Interview Question — `const` vs `as const`?

`const` is a JavaScript variable declaration:

```typescript
const role = "admin";
```

It prevents variable reassignment.

`as const` is a TypeScript const assertion:

```typescript
const user = {
    role: "admin"
} as const;
```

It preserves literal information and produces readonly object/tuple types for the literal expression.

---

# 99. Interview Question — Why Does `const role = "admin"` Infer `"admin"`?

Because the variable cannot be reassigned.

TypeScript can safely preserve the exact primitive literal type:

```typescript
"admin"
```

instead of widening it to:

```typescript
string
```

---

# 100. Interview Question — Why Does a `const` Object Still Widen Properties?

Because:

```typescript
const user = {
    role: "admin"
};
```

prevents:

```typescript
user = anotherObject;
```

but still allows:

```typescript
user.role = "user";
```

Therefore the property is inferred as:

```typescript
string
```

rather than:

```typescript
"admin"
```

---

# 101. Interview Question — What Happens to an Array with `as const`?

```typescript
const roles = [
    "admin",
    "user"
] as const;
```

is inferred as a readonly tuple:

```typescript
readonly [
    "admin",
    "user"
]
```

rather than:

```typescript
string[]
```

---

# 102. Interview Question — How Do You Create a Union from a Const Array?

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;

type Role =
    typeof roles[number];
```

Result:

```typescript
"admin"
| "user"
| "guest"
```

---

# 103. Interview Question — How Do You Create a Union from Object Values?

```typescript
const Status = {
    Success: "success",
    Error: "error"
} as const;
```

Then:

```typescript
type Status =
    typeof Status[
        keyof typeof Status
    ];
```

Result:

```typescript
"success"
| "error"
```

---

# 104. Interview Question — Does `as const` Freeze an Object?

No.

`as const` affects TypeScript's compile-time type system.

It does not perform runtime freezing.

For runtime freezing, JavaScript provides:

```typescript
Object.freeze()
```

with its own runtime semantics.

---

# 105. Interview Question — `readonly` vs `as const`?

`readonly` prevents mutation through a TypeScript type.

Example:

```typescript
type Config = {
    readonly environment:
        string;
};
```

The value type remains:

```typescript
string
```

`as const` also preserves literal values:

```typescript
const config = {
    environment: "production"
} as const;
```

giving:

```typescript
readonly environment:
    "production";
```

---

# 106. Interview Question — Why Is `as const` Useful for React?

It can help preserve:

* Fixed option lists
* Component variants
* Action names
* Event names
* Route constants
* Tuple return types
* Configuration values

For example:

```typescript
const variants = [
    "primary",
    "secondary"
] as const;

type Variant =
    typeof variants[number];
```

can define both runtime options and a component prop type.

---

# 107. Cheat Sheet

```text
LITERAL TYPE
================================

"admin"

3000

true


WIDENING
================================

let role = "admin"

→ string


CONST PRIMITIVE
================================

const role = "admin"

→ "admin"


CONST OBJECT
================================

const user = {
    role: "admin"
}

→ {
    role: string
}


AS CONST OBJECT
================================

const user = {
    role: "admin"
} as const

→ {
    readonly role: "admin"
}


NORMAL ARRAY
================================

const roles = [
    "admin",
    "user"
]

→ string[]


AS CONST ARRAY
================================

const roles = [
    "admin",
    "user"
] as const

→ readonly [
    "admin",
    "user"
]


ARRAY → UNION
================================

const roles = [
    "admin",
    "user",
    "guest"
] as const;

type Role =
    typeof roles[number];

→

"admin"
| "user"
| "guest"


OBJECT KEYS → UNION
================================

const Status = {
    Success: "success",
    Error: "error"
} as const;

type StatusKey =
    keyof typeof Status;

→

"Success"
| "Error"


OBJECT VALUES → UNION
================================

type Status =
    typeof Status[
        keyof typeof Status
    ];

→

"success"
| "error"


OBJECT ARRAY PROPERTY → UNION
================================

const roles = [
    {
        value: "admin",
        label: "Administrator"
    },
    {
        value: "user",
        label: "User"
    }
] as const;

type Role =
    typeof roles[number]["value"];

→

"admin"
| "user"


TUPLE
================================

const result = [
    200,
    "Success"
] as const;

→

readonly [
    200,
    "Success"
]


CORE DIFFERENCE
================================

const

→ Prevent variable reassignment


as const

→ Preserve literal information
→ readonly object properties
→ readonly tuple structures


RUNTIME
================================

as const

≠ Object.freeze()


COMMON PATTERN
================================

const VALUES = [
    "a",
    "b",
    "c"
] as const;

type Value =
    typeof VALUES[number];


MENTAL MODEL
================================

Value

    ↓

Should TypeScript generalize it?

    ↓

Normal mutable value

"admin"
    ↓
string


Fixed literal value

"admin"
    ↓
"admin"


as const

    ↓

Preserve exact information
```

---

# 108. Important Rules to Remember

```text
1. Literal types represent exact values.

2. "admin" is a literal type.

3. string is a primitive type.

4. 3000 is a numeric literal type.

5. true is a boolean literal type.

6. Literal widening converts specific literals into broader types.

7. let variables generally widen primitive literals.

8. let role = "admin" usually becomes string.

9. let count = 10 usually becomes number.

10. let active = true usually becomes boolean.

11. const primitive variables preserve literal types.

12. const role = "admin" can infer "admin".

13. const does not make object properties immutable.

14. const prevents variable reassignment.

15. const object properties can still change.

16. Therefore object properties commonly widen.

17. const { role: "admin" } does not automatically mean role: "admin".

18. It commonly means role: string.

19. Arrays declared with const are still mutable.

20. const array = [...] does not create a readonly tuple.

21. as const is a TypeScript const assertion.

22. as const prevents literal widening for literal expressions.

23. as const preserves exact string literals.

24. as const preserves exact numeric literals.

25. as const preserves exact boolean literals.

26. as const makes object properties readonly in the inferred type.

27. as const turns array literals into readonly tuples.

28. Tuple element positions are preserved.

29. Tuple lengths are preserved.

30. Literal array elements are preserved.

31. Nested literal structures receive const-style inference.

32. as const does not freeze JavaScript objects at runtime.

33. TypeScript types disappear after compilation.

34. Object.freeze() is a runtime JavaScript feature.

35. Runtime immutability and compile-time readonly are different concepts.

36. readonly does not necessarily preserve literal values.

37. as const provides literal preservation plus readonly inference.

38. readonly property: string is still string.

39. as const can produce readonly property: "value".

40. typeof can extract the type of a const value.

41. typeof ARRAY[number] can derive an element union.

42. This is a common TypeScript pattern.

43. const arrays can become the source of truth for value unions.

44. This avoids duplicating runtime arrays and type unions.

45. keyof typeof OBJECT derives object key unions.

46. typeof OBJECT[keyof typeof OBJECT] derives object value unions.

47. Const objects can be used as enum-like structures.

48. Enum-like const objects exist at runtime.

49. Their value unions can be derived at compile time.

50. as const works well for fixed constants.

51. Routes are good candidates.

52. Status values are good candidates.

53. Roles are good candidates.

54. Permissions are good candidates.

55. Events are good candidates.

56. HTTP methods are good candidates.

57. Fixed option lists are good candidates.

58. Feature names are good candidates.

59. Configuration constants can be good candidates.

60. Component variants can be good candidates.

61. Mutable domain entities are usually poor candidates.

62. Mutable form state is usually a poor candidate.

63. Frequently changing application state may be a poor candidate.

64. Do not use as const merely to make types stricter.

65. Use it when exact values or tuple structure matter.

66. as const can preserve tuple return shapes.

67. Tuple return types preserve positional meaning.

68. This is relevant to hook-like APIs.

69. Function return objects may widen literal properties.

70. as const can preserve discriminators in returned objects.

71. Discriminated unions benefit from exact literal discriminators.

72. Explicit return types are another solution.

73. Explicit annotations can preserve allowed unions without readonly behavior.

74. as const and explicit annotations solve different problems.

75. If data should mutate between allowed values, use an appropriate union type.

76. Do not use let value = "x" as const when ordinary const is appropriate.

77. Const tuples are readonly.

78. Mutable-array APIs may not accept readonly tuples.

79. Read-only APIs can accept readonly T[].

80. Preserve literal information before it gets widened.

81. TypeScript cannot always recover lost literal information later.

82. Values can be the source of truth.

83. Types can also be the source of truth.

84. Choose the source of truth intentionally.

85. as const is useful when runtime values define the allowed set.

86. satisfies is useful when values need to conform to an existing type.

87. as const and satisfies can be combined.

88. satisfies does not have the same purpose as as const.

89. as const is a special form of type assertion.

90. It requests const-style inference.

91. It does not create JavaScript code.

92. It does not perform runtime validation.

93. It does not sanitize values.

94. It does not replace runtime validation.

95. It does not replace Object.freeze().

96. Fixed arrays + typeof + indexed access are an important pattern.

97. Fixed objects + keyof + typeof are an important pattern.

98. as const helps maintain a single source of truth.

99. The main question is whether exact literal information matters.

100. The core pattern to remember is:

const VALUES = [
    "a",
    "b",
    "c"
] as const;

type Value =
    typeof VALUES[number];
```

---

# 109. Final Mental Model

Start with:

```typescript
let role = "admin";
```

TypeScript sees:

```text
"admin"
   │
   ▼
Can change
   │
   ▼
string
```

Now:

```typescript
const role = "admin";
```

TypeScript sees:

```text
"admin"
   │
   ▼
Cannot be reassigned
   │
   ▼
"admin"
```

But:

```typescript
const user = {
    role: "admin"
};
```

still allows:

```typescript
user.role = "user";
```

so:

```text
{
    role: "admin"
}
        │
        ▼
Property can change
        │
        ▼
{
    role: string
}
```

Now add:

```typescript
const user = {
    role: "admin"
} as const;
```

and TypeScript preserves:

```text
{
    readonly role:
        "admin"
}
```

For arrays:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
];
```

gives:

```text
string[]
```

while:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;
```

gives:

```text
readonly [
    "admin",
    "user",
    "guest"
]
```

Then:

```typescript
type Role =
    typeof roles[number];
```

gives:

```text
"admin"
|
"user"
|
"guest"
```

So the core flow is:

```text
Runtime Constants
       │
       ▼
    as const
       │
       ▼
Preserve Literal Information
       │
       ├─────────────┐
       ▼             ▼
   Objects         Arrays
       │             │
       ▼             ▼
 readonly        readonly tuple
 properties          │
       │              ▼
       │        typeof X[number]
       │              │
       ▼              ▼
 keyof typeof      Value Union
       │
       ▼
   Key Union
```

The most useful pattern to remember is:

```typescript
const VALUES = [
    "first",
    "second",
    "third"
] as const;

type Value =
    typeof VALUES[number];
```

Think:

```text
VALUES
   ↓
Actual runtime list

as const
   ↓
Preserve exact values

typeof VALUES
   ↓
Get tuple type

[number]
   ↓
Get every element type

Value
   ↓
"first"
| "second"
| "third"
```

`as const` is therefore especially useful when **runtime constants should also define compile-time types without maintaining the same information twice**.
