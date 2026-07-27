# TypeScript — `satisfies` Operator

The `satisfies` operator allows us to check whether a value is compatible with a type **while preserving the useful type TypeScript inferred for that value**.

Basic syntax:

```typescript
const value = expression satisfies SomeType;
```

Example:

```typescript
type Config = {
    api: string;
    timeout: number;
};

const config = {
    api: "/api",
    timeout: 5000
} satisfies Config;
```

TypeScript checks that `config` satisfies `Config`.

If the structure is wrong, TypeScript reports an error.

---

# 1. Why Does `satisfies` Exist?

Suppose we have:

```typescript
type Colors = 
    "red"
    | "green"
    | "blue";
```

And we want a configuration object:

```typescript
const palette = {
    red: "#ff0000",
    green: "#00ff00",
    blue: "#0000ff"
};
```

We want two things:

```text
1. Validate that palette has
   the correct structure.

2. Keep useful information
   TypeScript knows about palette.
```

Before `satisfies`, we might use a type annotation:

```typescript
const palette:
    Record<Colors, string> = {
        red: "#ff0000",
        green: "#00ff00",
        blue: "#0000ff"
    };
```

This validates the object.

But the variable is now viewed through:

```typescript
Record<Colors, string>
```

rather than simply retaining the most useful inferred type of the expression.

`satisfies` separates:

```text
Validation
```

from:

```text
The variable's resulting inferred type
```

---

# 2. Basic `satisfies` Example

```typescript
type User = {
    name: string;
    age: number;
};

const user = {
    name: "Alice",
    age: 25
} satisfies User;
```

TypeScript verifies:

```text
name → string

age → number
```

If everything matches, the code is accepted.

---

# 3. Invalid Property Type

```typescript
type User = {
    name: string;
    age: number;
};

const user = {
    name: "Alice",
    age: "25"
} satisfies User;
```

Error:

```text
age should be number

but received string
```

So `satisfies` performs compile-time compatibility checking.

---

# 4. Missing Property

```typescript
type User = {
    name: string;
    age: number;
};

const user = {
    name: "Alice"
} satisfies User;
```

Error because:

```text
age
```

is required by:

```typescript
User
```

---

# 5. Extra Property Checking

Consider:

```typescript
type User = {
    name: string;
    age: number;
};

const user = {
    name: "Alice",
    age: 25,
    email: "alice@example.com"
} satisfies User;
```

For an object literal checked directly this way, TypeScript performs excess-property checking.

Since `email` is not part of `User`, this can produce an error.

This is useful for catching typos and unexpected configuration keys.

---

# 6. `satisfies` Is Not a Type Annotation

Compare:

```typescript
const user: User = {
    name: "Alice",
    age: 25
};
```

with:

```typescript
const user = {
    name: "Alice",
    age: 25
} satisfies User;
```

Both check compatibility with `User`.

But they communicate different intentions.

Type annotation:

```text
Treat this variable as User.
```

`satisfies`:

```text
Check that this expression is
compatible with User,

while preserving useful inference
from the expression.
```

---

# 7. Annotation vs `satisfies`

Consider:

```typescript
type Config = {
    mode:
        "development"
        | "production";

    port: number;
};
```

Annotation:

```typescript
const config: Config = {
    mode: "production",
    port: 3000
};
```

The variable is typed as:

```typescript
Config
```

Therefore:

```typescript
config.mode
```

has type:

```typescript
"development"
| "production"
```

---

# 8. Using `satisfies`

```typescript
const config = {
    mode: "production",
    port: 3000
} satisfies Config;
```

TypeScript verifies that the object satisfies `Config`.

At the same time, contextual typing can preserve useful narrower information.

For example:

```typescript
config.mode
```

can remain:

```typescript
"production"
```

instead of being widened to the full union:

```typescript
"development"
| "production"
```

This is one of the major benefits of `satisfies`.

---

# 9. Important Nuance About Inference

Do not think:

```text
satisfies always preserves
every literal value exactly.
```

That is not its rule.

For example:

```typescript
const config = {
    port: 3000
} satisfies {
    port: number;
};
```

`config.port` can still be inferred as:

```typescript
number
```

rather than:

```typescript
3000
```

If exact literal preservation is required, that is where:

```typescript
as const
```

becomes relevant.

---

# 10. What `satisfies` Actually Does

Think of:

```typescript
expression satisfies Type
```

as:

```text
Expression
    │
    ▼
TypeScript infers information
about the expression
    │
    ▼
Check:
Is this compatible with Type?
    │
    ├── No → Compile-time error
    │
    └── Yes → Keep useful inferred type
```

The target type acts primarily as a **constraint/check**.

---

# 11. `satisfies` vs `as`

We already learned type assertions.

Compare:

```typescript
value as User
```

with:

```typescript
value satisfies User
```

They are fundamentally different.

---

# 12. Type Assertion

```typescript
const user = value as User;
```

means approximately:

```text
TypeScript, treat this value
as User.
```

Assertions can override or bypass some of TypeScript's inference/checking.

---

# 13. `satisfies`

```typescript
const user =
    value satisfies User;
```

means:

```text
TypeScript, verify that this
value is compatible with User.
```

If it is incompatible:

```text
Error
```

So:

```text
as
→ Assertion

satisfies
→ Validation / constraint
```

---

# 14. `satisfies` Does Not Convert Values

Suppose:

```typescript
const value = {
    age: "25"
} satisfies {
    age: number;
};
```

TypeScript does not convert:

```text
"25"
```

into:

```text
25
```

It reports an error.

`satisfies` performs type checking only.

---

# 15. Compile-Time Only

Like most TypeScript type-system features:

```typescript
satisfies
```

does not perform runtime validation.

Suppose data comes from an API.

```typescript
const data =
    await response.json();
```

Writing types around it does not automatically inspect the runtime payload.

Runtime validation still requires actual runtime logic or a validation library.

---

# 16. The Main Problem `satisfies` Solves

Suppose:

```typescript
type Routes = {
    home: string;
    users: string;
    products: string;
};
```

We could write:

```typescript
const routes: Routes = {
    home: "/",
    users: "/users",
    products: "/products"
};
```

This works.

But sometimes we want:

```text
Routes

to define what is required
```

while still allowing:

```text
routes

to retain useful information
from the actual object.
```

Then:

```typescript
const routes = {
    home: "/",
    users: "/users",
    products: "/products"
} satisfies Routes;
```

is a useful pattern.

---

# 17. Catching Missing Configuration

```typescript
type Environment =
    "development"
    | "staging"
    | "production";

type Config = Record<
    Environment,
    string
>;
```

Now:

```typescript
const endpoints = {
    development:
        "http://localhost:3000",

    staging:
        "https://staging.example.com",

    production:
        "https://example.com"
} satisfies Config;
```

TypeScript ensures every environment exists.

---

# 18. Missing Environment

```typescript
const endpoints = {
    development:
        "http://localhost:3000",

    production:
        "https://example.com"
} satisfies Config;
```

Error because:

```text
staging
```

is missing.

This is very useful for configuration maps.

---

# 19. Catching Invalid Keys

Suppose:

```typescript
type Environment =
    "development"
    | "staging"
    | "production";
```

Then:

```typescript
const endpoints = {
    development: "/dev",
    staging: "/staging",
    production: "/prod",

    testing: "/test"
} satisfies Record<
    Environment,
    string
>;
```

`testing` is not part of:

```typescript
Environment
```

and the object literal can be rejected.

---

# 20. Catching Typos

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

Now:

```typescript
const permissions = {
    admin: ["read", "write"],
    user: ["read"],

    guset: []
} satisfies Record<
    Role,
    string[]
>;
```

Notice:

```text
guset
```

instead of:

```text
guest
```

TypeScript catches the mistake.

This is one of the most practical benefits of `satisfies`.

---

# 21. Configuration Maps

```typescript
type Service =
    "auth"
    | "users"
    | "orders";
```

Define:

```typescript
type ServiceConfig = {
    url: string;
    timeout: number;
};
```

Then:

```typescript
const services = {
    auth: {
        url: "/api/auth",
        timeout: 3000
    },

    users: {
        url: "/api/users",
        timeout: 5000
    },

    orders: {
        url: "/api/orders",
        timeout: 5000
    }
} satisfies Record<
    Service,
    ServiceConfig
>;
```

TypeScript validates:

```text
Required services

Required properties

Property types

Unexpected keys in the
direct object literal
```

---

# 22. Nested Validation

If we accidentally write:

```typescript
const services = {
    auth: {
        url: "/api/auth",
        timeout: "3000"
    },

    users: {
        url: "/api/users",
        timeout: 5000
    },

    orders: {
        url: "/api/orders",
        timeout: 5000
    }
} satisfies Record<
    Service,
    ServiceConfig
>;
```

TypeScript catches:

```typescript
timeout: "3000"
```

because:

```typescript
timeout
```

must be:

```typescript
number
```

---

# 23. `Record` + `satisfies`

A very common combination is:

```typescript
const something = {
    ...
} satisfies Record<
    KeyType,
    ValueType
>;
```

Example:

```typescript
type Role =
    "admin"
    | "user";

const labels = {
    admin: "Administrator",
    user: "Regular User"
} satisfies Record<
    Role,
    string
>;
```

This ensures:

```text
Every Role exists

Every value is a string
```

---

# 24. Permission Map

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
const permissions = {
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
} satisfies Record<
    Role,
    Permission[]
>;
```

Now TypeScript verifies the entire permission map.

---

# 25. Invalid Permission

```typescript
const permissions = {
    admin: [
        "read",
        "write",
        "execute"
    ],

    editor: [
        "read"
    ],

    viewer: [
        "read"
    ]
} satisfies Record<
    Role,
    Permission[]
>;
```

Error:

```text
"execute"
```

is not assignable to:

```typescript
Permission
```

---

# 26. Route Configuration

```typescript
type RouteConfig = {
    path: string;
    requiresAuth: boolean;
};
```

Then:

```typescript
const routes = {
    home: {
        path: "/",
        requiresAuth: false
    },

    dashboard: {
        path: "/dashboard",
        requiresAuth: true
    }
} satisfies Record<
    string,
    RouteConfig
>;
```

Every route must have:

```text
path

requiresAuth
```

with the correct types.

---

# 27. Why Not Just Use an Annotation?

We could write:

```typescript
const routes:
    Record<string, RouteConfig> = {
        home: {
            path: "/",
            requiresAuth: false
        },

        dashboard: {
            path: "/dashboard",
            requiresAuth: true
        }
    };
```

This is completely valid.

Use an annotation when you want the variable itself to have the declared type.

Use `satisfies` when you want:

```text
Constraint checking
+
Useful expression inference
```

---

# 28. Preserving Known Keys

Consider:

```typescript
const routes:
    Record<string, string> = {
        home: "/",
        dashboard: "/dashboard"
    };
```

The declared type says:

```text
Any string key
→ string
```

So the precise known-key information has been generalized.

With:

```typescript
const routes = {
    home: "/",
    dashboard: "/dashboard"
} satisfies Record<
    string,
    string
>;
```

TypeScript still knows the actual object's keys:

```text
home

dashboard
```

This can make autocomplete and downstream inference more useful.

---

# 29. Deriving Keys After `satisfies`

```typescript
const routes = {
    home: "/",
    dashboard: "/dashboard"
} satisfies Record<
    string,
    string
>;
```

Now:

```typescript
type RouteName =
    keyof typeof routes;
```

produces:

```typescript
"home"
| "dashboard"
```

This is a major reason to use `satisfies` with configuration objects.

---

# 30. Compare with Annotation

```typescript
const routes:
    Record<string, string> = {
        home: "/",
        dashboard: "/dashboard"
    };
```

Then:

```typescript
type RouteName =
    keyof typeof routes;
```

is based on:

```typescript
Record<string, string>
```

rather than the specific object keys.

You have lost some of the concrete shape information.

---

# 31. Exact Keys + Structural Validation

This pattern:

```typescript
const routes = {
    home: "/",
    dashboard: "/dashboard"
} satisfies Record<
    string,
    string
>;
```

provides:

```text
Value structure validation
+
Specific known object keys
```

This is useful for:

```text
Configuration maps

Route maps

Service maps

Feature maps

Component maps

Handler maps
```

---

# 32. `satisfies` with Arrays

Suppose:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

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
] satisfies User[];
```

TypeScript checks every array element against:

```typescript
User
```

---

# 33. Invalid Array Element

```typescript
const users = [
    {
        id: 1,
        name: "Alice"
    },

    {
        id: "2",
        name: "Bob"
    }
] satisfies User[];
```

Error because:

```typescript
id
```

must be:

```typescript
number
```

---

# 34. Tuple Validation

Suppose we require:

```typescript
type Coordinate = [
    number,
    number
];
```

Then:

```typescript
const position = [
    10,
    20
] satisfies Coordinate;
```

TypeScript validates that the structure matches the tuple.

So:

```typescript
position[0]
```

and:

```typescript
position[1]
```

are known tuple positions.

---

# 35. Invalid Tuple Length

```typescript
const position = [
    10,
    20,
    30
] satisfies Coordinate;
```

Error because:

```typescript
Coordinate
```

expects exactly two elements.

---

# 36. Tuple Element Type Error

```typescript
const position = [
    10,
    "20"
] satisfies Coordinate;
```

Error because the second element must be:

```typescript
number
```

---

# 37. `as const` vs `satisfies`

Now compare the two features.

`as const`:

```typescript
const config = {
    mode: "production",
    port: 3000
} as const;
```

Primary purpose:

```text
Preserve literal information
+
Readonly inference
```

`satisfies`:

```typescript
const config = {
    mode: "production",
    port: 3000
} satisfies Config;
```

Primary purpose:

```text
Check compatibility
+
Retain useful inference
```

---

# 38. `as const` Does Not Validate Against a Domain Type

Suppose:

```typescript
const config = {
    mode: "wrong",
    port: 3000
} as const;
```

TypeScript happily infers:

```typescript
{
    readonly mode: "wrong";
    readonly port: 3000;
}
```

because:

```typescript
as const
```

does not know that `"wrong"` violates your application's `Config` type.

It only preserves the value.

---

# 39. `satisfies` Validates

Suppose:

```typescript
type Config = {
    mode:
        "development"
        | "production";

    port: number;
};
```

Now:

```typescript
const config = {
    mode: "wrong",
    port: 3000
} satisfies Config;
```

TypeScript reports an error.

So:

```text
as const
→ Preserve

satisfies
→ Validate
```

---

# 40. Combining `as const` + `satisfies`

Sometimes we want both:

```text
Exact literal preservation

Readonly structure

Validation against a contract
```

Then we can combine them.

```typescript
type Config = {
    mode:
        "development"
        | "production";

    port: number;
};

const config = {
    mode: "production",
    port: 3000
} as const satisfies Config;
```

Now:

```text
as const
    ↓
Preserve literals + readonly


satisfies Config
    ↓
Validate structure
```

---

# 41. Result of `as const satisfies`

```typescript
const config = {
    mode: "production",
    port: 3000
} as const satisfies Config;
```

The useful inferred type includes:

```typescript
{
    readonly mode:
        "production";

    readonly port:
        3000;
}
```

while TypeScript also confirms compatibility with:

```typescript
Config
```

---

# 42. Route Example with Both

```typescript
type Route =
    `/${string}`;

const ROUTES = {
    home: "/",
    users: "/users",
    products: "/products"
} as const satisfies Record<
    string,
    Route
>;
```

This gives us:

```text
Exact route values

Readonly properties

Validation that values
look like routes
```

---

# 43. Invalid Route

```typescript
const ROUTES = {
    home: "/",
    users: "/users",

    products:
        "products"
} as const satisfies Record<
    string,
    Route
>;
```

Error because:

```text
products
```

does not match:

```typescript
`/${string}`
```

---

# 44. Deriving Route Union

Using:

```typescript
const ROUTES = {
    home: "/",
    users: "/users",
    products: "/products"
} as const satisfies Record<
    string,
    Route
>;
```

we can derive:

```typescript
type AppRoute =
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

So we get:

```text
Runtime object

+

Compile-time validation

+

Literal union
```

from one source.

---

# 45. Strong Configuration Pattern

```typescript
type Environment =
    "development"
    | "staging"
    | "production";

type EnvironmentConfig = {
    apiUrl: string;
    debug: boolean;
};
```

Then:

```typescript
const CONFIG = {
    development: {
        apiUrl:
            "http://localhost:3000",

        debug: true
    },

    staging: {
        apiUrl:
            "https://staging.example.com",

        debug: false
    },

    production: {
        apiUrl:
            "https://example.com",

        debug: false
    }
} satisfies Record<
    Environment,
    EnvironmentConfig
>;
```

TypeScript ensures:

```text
All environments exist

Each environment has apiUrl

Each environment has debug

apiUrl is string

debug is boolean
```

---

# 46. Feature Flags

```typescript
type Feature =
    "chat"
    | "payments"
    | "analytics";

type FeatureConfig = {
    enabled: boolean;
};
```

Then:

```typescript
const features = {
    chat: {
        enabled: true
    },

    payments: {
        enabled: false
    },

    analytics: {
        enabled: true
    }
} satisfies Record<
    Feature,
    FeatureConfig
>;
```

If we forget:

```text
payments
```

TypeScript reports an error.

---

# 47. Component Configuration

Suppose:

```typescript
type ComponentConfig = {
    title: string;
    enabled: boolean;
};
```

Then:

```typescript
const components = {
    dashboard: {
        title: "Dashboard",
        enabled: true
    },

    profile: {
        title: "Profile",
        enabled: true
    }
} satisfies Record<
    string,
    ComponentConfig
>;
```

Now TypeScript knows the actual keys:

```text
dashboard

profile
```

while validating each configuration.

---

# 48. Derive Component Name

```typescript
type ComponentName =
    keyof typeof components;
```

Result:

```typescript
"dashboard"
| "profile"
```

This is cleaner than manually writing:

```typescript
type ComponentName =
    "dashboard"
    | "profile";
```

---

# 49. Handler Maps

Suppose:

```typescript
type Event =
    "login"
    | "logout"
    | "refresh";
```

We can define:

```typescript
const handlers = {
    login: () => {
        console.log("Login");
    },

    logout: () => {
        console.log("Logout");
    },

    refresh: () => {
        console.log("Refresh");
    }
} satisfies Record<
    Event,
    () => void
>;
```

TypeScript ensures every event has a handler.

---

# 50. Missing Handler

```typescript
const handlers = {
    login: () => {},
    logout: () => {}
} satisfies Record<
    Event,
    () => void
>;
```

Error because:

```text
refresh
```

is missing.

This pattern is useful when every union member must be handled.

---

# 51. API Method Configuration

```typescript
type HttpMethod =
    "GET"
    | "POST"
    | "PUT"
    | "DELETE";

type EndpointConfig = {
    method: HttpMethod;
    path: `/${string}`;
};
```

Then:

```typescript
const endpoints = {
    getUsers: {
        method: "GET",
        path: "/users"
    },

    createUser: {
        method: "POST",
        path: "/users"
    }
} satisfies Record<
    string,
    EndpointConfig
>;
```

TypeScript validates both:

```text
HTTP method

Route pattern
```

---

# 52. Invalid API Method

```typescript
const endpoints = {
    getUsers: {
        method: "FETCH",
        path: "/users"
    }
} satisfies Record<
    string,
    EndpointConfig
>;
```

Error because:

```text
FETCH
```

is not a valid:

```typescript
HttpMethod
```

---

# 53. Invalid API Path

```typescript
const endpoints = {
    getUsers: {
        method: "GET",
        path: "users"
    }
} satisfies Record<
    string,
    EndpointConfig
>;
```

Error because:

```text
users
```

does not satisfy:

```typescript
`/${string}`
```

This demonstrates how:

```text
satisfies
+
Template Literal Types
```

can work together.

---

# 54. `satisfies` with Utility Types

Suppose:

```typescript
type User = {
    name: string;
    email: string;
    age: number;
};
```

We can validate:

```typescript
const update = {
    name: "Alice",
    age: 26
} satisfies Partial<User>;
```

This means:

```text
Properties are optional

but if provided,
they must match User
```

---

# 55. Invalid `Partial` Value

```typescript
const update = {
    age: "26"
} satisfies Partial<User>;
```

Error because `age`, when present, must still be:

```typescript
number
```

---

# 56. `satisfies` with `Pick`

```typescript
const credentials = {
    name: "Alice",
    email: "alice@example.com"
} satisfies Pick<
    User,
    "name" | "email"
>;
```

TypeScript checks only the selected structure.

---

# 57. `satisfies` with `Readonly`

```typescript
const config = {
    api: "/api"
} satisfies Readonly<{
    api: string;
}>;
```

Important:

`satisfies` does not transform the inferred variable into the target type in the same way an annotation does.

So do not assume that merely satisfying:

```typescript
Readonly<...>
```

makes the resulting variable readonly.

If you want readonly inference, use an appropriate readonly declaration or `as const`.

---

# 58. `satisfies` Does Not Apply the Target Type

This is critical.

Suppose:

```typescript
type Config = {
    name: string;
};
```

Then:

```typescript
const config = {
    name: "App"
} satisfies Config;
```

Do not think:

```text
config's type = Config
```

The actual idea is:

```text
config's inferred type
must be compatible with Config
```

The target is a constraint, not necessarily the final variable type.

---

# 59. Mutation After `satisfies`

Suppose:

```typescript
const user = {
    name: "Alice",
    age: 25
} satisfies User;
```

The object is still mutable unless its inferred type or another type rule makes something readonly.

For example:

```typescript
user.name = "Bob";
```

is allowed.

`satisfies` does not mean:

```text
readonly
```

---

# 60. `satisfies` vs `as const` Mutation

```typescript
const user = {
    name: "Alice"
} satisfies {
    name: string;
};
```

This is mutable:

```typescript
user.name = "Bob";
```

But:

```typescript
const user = {
    name: "Alice"
} as const;
```

produces readonly property inference:

```typescript
user.name = "Bob";
// Error
```

So:

```text
satisfies
≠ readonly
```

---

# 61. `satisfies` with Function Maps

```typescript
type Operation =
    "add"
    | "subtract";

type OperationFunction =
    (
        a: number,
        b: number
    ) => number;
```

Then:

```typescript
const operations = {
    add: (a, b) => a + b,

    subtract:
        (a, b) => a - b
} satisfies Record<
    Operation,
    OperationFunction
>;
```

The contextual type helps TypeScript understand:

```text
a → number

b → number

return → number
```

---

# 62. Wrong Function Return Type

```typescript
const operations = {
    add:
        (a, b) =>
            `${a + b}`,

    subtract:
        (a, b) =>
            a - b
} satisfies Record<
    Operation,
    OperationFunction
>;
```

Error because `add` returns:

```typescript
string
```

instead of:

```typescript
number
```

---

# 63. Navigation Configuration

```typescript
type NavigationItem = {
    label: string;
    path: `/${string}`;
};
```

Then:

```typescript
const navigation = {
    home: {
        label: "Home",
        path: "/"
    },

    users: {
        label: "Users",
        path: "/users"
    },

    settings: {
        label: "Settings",
        path: "/settings"
    }
} satisfies Record<
    string,
    NavigationItem
>;
```

Now:

```typescript
type NavigationKey =
    keyof typeof navigation;
```

gives:

```typescript
"home"
| "users"
| "settings"
```

---

# 64. Theme Configuration

```typescript
type Theme =
    "light"
    | "dark";

type ThemeConfig = {
    background: string;
    text: string;
};
```

Then:

```typescript
const themes = {
    light: {
        background: "#ffffff",
        text: "#000000"
    },

    dark: {
        background: "#000000",
        text: "#ffffff"
    }
} satisfies Record<
    Theme,
    ThemeConfig
>;
```

TypeScript guarantees both themes exist and match the required structure.

---

# 65. Status Metadata

```typescript
type Status =
    "pending"
    | "running"
    | "success"
    | "failed";

type StatusInfo = {
    label: string;
    terminal: boolean;
};
```

Then:

```typescript
const statusInfo = {
    pending: {
        label: "Pending",
        terminal: false
    },

    running: {
        label: "Running",
        terminal: false
    },

    success: {
        label: "Success",
        terminal: true
    },

    failed: {
        label: "Failed",
        terminal: true
    }
} satisfies Record<
    Status,
    StatusInfo
>;
```

This ensures that adding a new `Status` forces us to update the metadata map.

---

# 66. Exhaustive Configuration

Suppose later we change:

```typescript
type Status =
    "pending"
    | "running"
    | "success"
    | "failed"
    | "cancelled";
```

Now the existing:

```typescript
statusInfo
```

will produce an error because:

```text
cancelled
```

is missing.

This is a powerful way to keep domain types and configuration synchronized.

---

# 67. When Should You Use `satisfies`?

Use it when:

```text
You have a value

AND

You have a contract/type

AND

You want to verify the value
against the contract

WITHOUT unnecessarily replacing
the useful inferred type
```

Typical examples:

```text
Configuration objects

Route definitions

Permission maps

Feature flags

Handler maps

Component registries

Theme definitions

API endpoint definitions

Status metadata

Lookup tables
```

---

# 68. When Should You Use a Type Annotation Instead?

Use:

```typescript
const value: Type = ...
```

when you intentionally want:

```text
The variable itself
to be typed as Type.
```

Example:

```typescript
let currentStatus: Status =
    "pending";
```

Here the variable needs to change among all valid `Status` values.

A normal annotation is appropriate.

---

# 69. When Should You Use `as const` Instead?

Use:

```typescript
as const
```

when your primary goal is:

```text
Preserve exact literal values

Create readonly object inference

Create readonly tuples
```

Example:

```typescript
const roles = [
    "admin",
    "user",
    "guest"
] as const;
```

---

# 70. When Should You Use `as`?

Use type assertions only when you have information TypeScript cannot determine and you have a valid reason to assert a type.

Example:

```typescript
const element =
    document.getElementById(
        "email"
    ) as HTMLInputElement;
```

Do not use assertions merely to silence legitimate type errors.

---

# 71. Four-Way Comparison

Consider these four:

```typescript
const value = {
    role: "admin"
};
```

```typescript
const value: User = {
    role: "admin"
};
```

```typescript
const value = {
    role: "admin"
} as const;
```

```typescript
const value = {
    role: "admin"
} satisfies User;
```

They mean different things.

---

# 72. Normal Inference

```typescript
const value = {
    role: "admin"
};
```

Think:

```text
Infer the object normally.
```

Potential result:

```typescript
{
    role: string;
}
```

---

# 73. Type Annotation

```typescript
const value: User = {
    role: "admin"
};
```

Think:

```text
value is User.
```

The declared type becomes the variable's type.

---

# 74. `as const`

```typescript
const value = {
    role: "admin"
} as const;
```

Think:

```text
Preserve exact literal information
and readonly structure.
```

Result:

```typescript
{
    readonly role: "admin";
}
```

---

# 75. `satisfies`

```typescript
const value = {
    role: "admin"
} satisfies User;
```

Think:

```text
Verify this value is compatible
with User while keeping useful
inference for this expression.
```

---

# 76. `as const satisfies`

```typescript
const value = {
    role: "admin"
} as const satisfies User;
```

Think:

```text
Preserve exact literals
+
Readonly inference
+
Validate against User
```

This combination is particularly useful for fixed configuration constants.

---

# 77. Common Mistake — Treating `satisfies` Like `as`

Wrong mental model:

```typescript
value satisfies User
```

means:

```text
Force value to become User.
```

No.

It means:

```text
Check compatibility with User.
```

---

# 78. Common Mistake — Expecting Runtime Validation

```typescript
const data =
    apiResponse satisfies User;
```

does not magically inspect runtime API data.

If the value is typed as `any`, the type system may not protect you meaningfully.

External data needs runtime validation when trust matters.

---

# 79. Common Mistake — Expecting Runtime Conversion

```typescript
{
    age: "25"
} satisfies {
    age: number;
};
```

does not convert:

```text
"25"
→
25
```

It reports a type error.

---

# 80. Common Mistake — Expecting Readonly

```typescript
const config = {
    name: "App"
} satisfies Config;
```

does not mean:

```typescript
readonly
```

If the inferred object is mutable, it remains mutable.

---

# 81. Common Mistake — Expecting Every Literal to Stay Exact

Do not assume:

```typescript
const config = {
    timeout: 5000
} satisfies {
    timeout: number;
};
```

means:

```typescript
config.timeout
```

must have type:

```typescript
5000
```

Literal inference depends on context.

If exact literal preservation is required, consider:

```typescript
as const
```

---

# 82. Common Mistake — Using `satisfies` When Annotation Is Simpler

Suppose:

```typescript
let status: Status =
    "pending";
```

This is clear.

Do not force:

```typescript
satisfies
```

into every declaration.

Use the simplest feature that expresses your intention.

---

# 83. Common Mistake — Using `as const satisfies` Everywhere

This:

```typescript
as const satisfies ...
```

is powerful.

But it also makes literal structures readonly.

If your object needs to mutate, it may be the wrong choice.

Use it mainly for genuinely fixed definitions.

---

# 84. Common Mistake — Overusing `Record<string, ...>`

Suppose:

```typescript
const routes = {
    home: "/",
    users: "/users"
} satisfies Record<
    string,
    string
>;
```

This validates values as strings.

But it does not restrict keys to a known union because:

```typescript
string
```

allows arbitrary string keys.

If keys should be constrained:

```typescript
type RouteName =
    "home"
    | "users";
```

then use:

```typescript
Record<
    RouteName,
    string
>
```

---

# 85. Known Keys vs Arbitrary Keys

Arbitrary:

```typescript
Record<string, string>
```

means conceptually:

```text
Any string key
→ string
```

Known:

```typescript
Record<
    "home" | "users",
    string
>
```

means:

```text
home
→ required

users
→ required
```

Choose based on the domain.

---

# 86. Common Mistake — Using Assertions to Hide Errors

Suppose:

```typescript
const config = {
    port: "3000"
} as Config;
```

Depending on the relationship between the types, an assertion may allow you to push past checks or tempt you into unsafe double assertions.

Instead:

```typescript
const config = {
    port: "3000"
} satisfies Config;
```

forces the value to actually satisfy the contract.

When validation is your goal, prefer validation.

---

# 87. Real-World Example — Application Routes

```typescript
type RouteConfig = {
    path: `/${string}`;
    auth: boolean;
};

const ROUTES = {
    home: {
        path: "/",
        auth: false
    },

    dashboard: {
        path: "/dashboard",
        auth: true
    },

    profile: {
        path: "/profile",
        auth: true
    }
} as const satisfies Record<
    string,
    RouteConfig
>;
```

Now we get:

```text
Validated route structure

Exact route keys

Exact path literals

Readonly configuration
```

---

# 88. Derive Route Names

```typescript
type RouteName =
    keyof typeof ROUTES;
```

Result:

```typescript
"home"
| "dashboard"
| "profile"
```

---

# 89. Derive Route Paths

```typescript
type RoutePath =
    typeof ROUTES[
        keyof typeof ROUTES
    ]["path"];
```

Result:

```typescript
"/"
| "/dashboard"
| "/profile"
```

This gives us a strongly typed route system from one runtime configuration object.

---

# 90. Real-World Example — Permissions

```typescript
type Role =
    "admin"
    | "editor"
    | "viewer";

type Permission =
    "users:read"
    | "users:write"
    | "users:delete";
```

Then:

```typescript
const ROLE_PERMISSIONS = {
    admin: [
        "users:read",
        "users:write",
        "users:delete"
    ],

    editor: [
        "users:read",
        "users:write"
    ],

    viewer: [
        "users:read"
    ]
} as const satisfies Record<
    Role,
    readonly Permission[]
>;
```

Notice:

```typescript
readonly Permission[]
```

because `as const` creates readonly arrays.

---

# 91. Why `readonly Permission[]`?

With:

```typescript
as const
```

the arrays become readonly.

Therefore:

```typescript
Permission[]
```

may be too mutable for the const-inferred values.

Using:

```typescript
readonly Permission[]
```

says:

```text
This contract accepts
read-only permission arrays.
```

This is a useful pattern when combining `as const` with nested arrays.

---

# 92. Real-World Example — API Endpoints

```typescript
type HttpMethod =
    "GET"
    | "POST"
    | "PUT"
    | "PATCH"
    | "DELETE";

type Endpoint = {
    method: HttpMethod;
    path: `/${string}`;
    auth: boolean;
};
```

Then:

```typescript
const API = {
    getUsers: {
        method: "GET",
        path: "/users",
        auth: true
    },

    createUser: {
        method: "POST",
        path: "/users",
        auth: true
    },

    login: {
        method: "POST",
        path: "/auth/login",
        auth: false
    }
} as const satisfies Record<
    string,
    Endpoint
>;
```

This creates a strongly typed endpoint registry.

---

# 93. Derive API Operation Names

```typescript
type ApiOperation =
    keyof typeof API;
```

Result:

```typescript
"getUsers"
| "createUser"
| "login"
```

---

# 94. Derive API Paths

```typescript
type ApiPath =
    typeof API[
        keyof typeof API
    ]["path"];
```

Result:

```typescript
"/users"
| "/auth/login"
```

Duplicate values naturally collapse in the union.

---

# 95. Real-World Example — Status Metadata

```typescript
type Status =
    "pending"
    | "running"
    | "success"
    | "failed";

type StatusConfig = {
    label: string;
    terminal: boolean;
};
```

Then:

```typescript
const STATUS = {
    pending: {
        label: "Pending",
        terminal: false
    },

    running: {
        label: "Running",
        terminal: false
    },

    success: {
        label: "Success",
        terminal: true
    },

    failed: {
        label: "Failed",
        terminal: true
    }
} as const satisfies Record<
    Status,
    StatusConfig
>;
```

If a new status is added to:

```typescript
Status
```

TypeScript forces this configuration to be updated.

---

# 96. Interview Question — What Is `satisfies`?

The `satisfies` operator checks whether an expression is assignable to a target type while preserving useful inference for the expression instead of simply annotating the variable as the target type.

Example:

```typescript
const config = {
    port: 3000
} satisfies {
    port: number;
};
```

---

# 97. Interview Question — `satisfies` vs Type Annotation?

Annotation:

```typescript
const value: Type = ...
```

means:

```text
The variable has Type.
```

`satisfies`:

```typescript
const value =
    ... satisfies Type;
```

means:

```text
Check that the expression
is compatible with Type
while retaining useful inference.
```

---

# 98. Interview Question — `satisfies` vs `as`?

`as`:

```typescript
value as Type
```

is a type assertion.

`satisfies`:

```typescript
value satisfies Type
```

checks compatibility.

Think:

```text
as
→ "Treat it as this type."

satisfies
→ "Prove it satisfies this type."
```

---

# 99. Interview Question — `satisfies` vs `as const`?

`satisfies`:

```text
Validates against a type.
```

`as const`:

```text
Preserves literal information
and creates readonly inference.
```

They solve different problems and can be combined.

---

# 100. Interview Question — Can `satisfies` and `as const` Be Combined?

Yes.

```typescript
const config = {
    mode: "production",
    port: 3000
} as const satisfies Config;
```

This can provide:

```text
Literal preservation

Readonly inference

Contract validation
```

---

# 101. Interview Question — Does `satisfies` Change Runtime Behavior?

No.

It is a TypeScript compile-time feature.

It does not:

```text
Validate runtime API data

Convert values

Sanitize values

Freeze objects

Generate runtime checks
```

---

# 102. Interview Question — Why Use `satisfies` with `Record`?

Example:

```typescript
const labels = {
    admin: "Administrator",
    user: "User"
} satisfies Record<
    Role,
    string
>;
```

It can ensure:

```text
Every required key exists

Every value has the correct type

Unexpected direct object-literal keys
are caught
```

while retaining useful information about the actual object.

---

# 103. Cheat Sheet

```text
SATISFIES
================================

const value =
    expression satisfies Type;


CORE PURPOSE
================================

Validate expression against Type

WITHOUT unnecessarily replacing
the useful inferred type.


EXAMPLE
================================

type Config = {
    port: number;
};

const config = {
    port: 3000
} satisfies Config;


WRONG TYPE
================================

const config = {
    port: "3000"
} satisfies Config;

→ Error


MISSING PROPERTY
================================

type User = {
    name: string;
    age: number;
};

const user = {
    name: "Alice"
} satisfies User;

→ Error


TYPE ANNOTATION
================================

const value: Type = expression;

Meaning:

Variable has Type.


SATISFIES
================================

const value =
    expression satisfies Type;

Meaning:

Expression must be
compatible with Type.


TYPE ASSERTION
================================

value as Type

Meaning:

Treat value as Type.


SATISFIES VS AS
================================

as
→ Assertion

satisfies
→ Compatibility check


AS CONST
================================

value as const

→ Preserve literals
→ readonly object inference
→ readonly tuples


SATISFIES
================================

value satisfies Type

→ Validate against Type
→ retain useful inference


COMBINED
================================

const value = {
    ...
} as const satisfies Type;

→ Preserve
→ Readonly
→ Validate


RECORD
================================

const values = {
    ...
} satisfies Record<
    Key,
    Value
>;


ROUTES
================================

type Route =
    `/${string}`;

const ROUTES = {
    home: "/",
    users: "/users"
} as const satisfies Record<
    string,
    Route
>;


DERIVE KEYS
================================

type RouteName =
    keyof typeof ROUTES;


DERIVE VALUES
================================

type Route =
    typeof ROUTES[
        keyof typeof ROUTES
    ];


ARRAY
================================

const users = [
    ...
] satisfies User[];


TUPLE
================================

const point = [
    10,
    20
] satisfies [
    number,
    number
];


PARTIAL
================================

const update = {
    name: "Alice"
} satisfies Partial<User>;


HANDLER MAP
================================

const handlers = {
    login: () => {},
    logout: () => {}
} satisfies Record<
    Event,
    () => void
>;


MAIN QUESTION
================================

Do I want to...

Declare variable as Type?

→ : Type


Preserve exact constants?

→ as const


Assert something TypeScript
cannot determine?

→ as Type


Validate an inferred value
against a contract?

→ satisfies Type


Preserve constants AND validate?

→ as const satisfies Type
```

---

# 104. Important Rules to Remember

```text
1. satisfies is a TypeScript operator.

2. It checks compatibility with a target type.

3. It is primarily a compile-time feature.

4. It does not perform runtime validation.

5. It does not convert runtime values.

6. It does not sanitize values.

7. It does not freeze objects.

8. It does not automatically make properties readonly.

9. It is different from a type annotation.

10. A type annotation declares the variable as a type.

11. satisfies checks an expression against a type.

12. satisfies aims to preserve useful inference.

13. It is different from a type assertion.

14. as Type is an assertion.

15. satisfies Type is a compatibility check.

16. Assertions can be used when TypeScript lacks information.

17. satisfies should be used when validation is the goal.

18. Invalid property types are caught.

19. Missing required properties are caught.

20. Excess properties in directly checked object literals can be caught.

21. Typos in constrained keys can be caught.

22. satisfies works well with Record.

23. Record can enforce required keys.

24. Record can enforce value types.

25. Known key unions provide stronger checks than string.

26. Record<string, T> allows arbitrary string keys.

27. Record<Union, T> requires union members as keys.

28. satisfies works with nested objects.

29. Nested property types are validated.

30. satisfies works with arrays.

31. Array elements can be validated.

32. satisfies works with tuples.

33. Tuple length can be validated.

34. Tuple element types can be validated.

35. satisfies works with utility types.

36. It can be used with Partial.

37. It can be used with Pick.

38. It can be used with Record.

39. It can be used with Readonly contracts.

40. Satisfying a readonly type does not necessarily make the inferred value readonly.

41. as const preserves literal information.

42. as const produces readonly object inference.

43. as const produces readonly tuples.

44. satisfies validates against a contract.

45. as const and satisfies solve different problems.

46. They can be combined.

47. as const satisfies Type is useful for fixed configuration.

48. The order commonly used is:

value as const satisfies Type

49. The combination preserves literals.

50. The combination provides readonly inference.

51. The combination validates the contract.

52. Readonly nested arrays may require readonly array contracts.

53. readonly T[] works well with as const arrays.

54. satisfies can preserve specific object keys.

55. This improves keyof typeof patterns.

56. It can improve autocomplete.

57. It can prevent loss of concrete shape information.

58. It is useful for route maps.

59. It is useful for configuration maps.

60. It is useful for permission maps.

61. It is useful for feature flags.

62. It is useful for handler maps.

63. It is useful for status metadata.

64. It is useful for component registries.

65. It is useful for API endpoint definitions.

66. It is useful for theme configuration.

67. It is useful for navigation definitions.

68. It can enforce exhaustive configuration for union keys.

69. Adding a union member can reveal missing configuration.

70. This helps synchronize domain types and lookup tables.

71. satisfies does not guarantee runtime API data.

72. External data still needs runtime validation.

73. any can undermine type safety.

74. satisfies is not a replacement for runtime schemas.

75. satisfies does not always preserve every primitive literal exactly.

76. Literal inference still follows TypeScript's contextual typing rules.

77. Use as const when exact literal preservation is required.

78. Use annotations when the variable should have the declared type.

79. Use satisfies when the expression should merely conform to a type.

80. Use assertions when you legitimately know more than TypeScript.

81. Do not use assertions to hide legitimate errors.

82. Do not use satisfies everywhere.

83. Use the simplest construct that expresses your intention.

84. Mutable variables often benefit from annotations.

85. Fixed configuration often benefits from satisfies.

86. Fixed literal configuration may benefit from as const satisfies.

87. satisfies can be combined with Template Literal Types.

88. This can validate structured strings.

89. Route patterns are a good example.

90. satisfies can be combined with mapped and utility types.

91. It can validate function maps.

92. Function parameters can receive contextual types.

93. Function return types can be checked.

94. satisfies does not replace explicit domain modeling.

95. It connects concrete values to domain contracts.

96. It helps maintain a single source of truth.

97. It helps catch configuration drift.

98. It helps catch missing mappings.

99. It helps catch invalid values.

100. The core mental model is:

Infer value
    ↓
Check against Type
    ↓
Keep useful inference
```

---

# 105. Final Mental Model

We now have four related tools:

```typescript
: Type

as Type

as const

satisfies Type
```

They are not interchangeable.

Think:

```text
                 VALUE
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼

   : Type       as Type    satisfies Type

       │           │           │
       ▼           ▼           ▼

   Declare       Assert       Validate
   variable      type         against
   as Type                    Type


                   │
                   ▼

                as const

                   │
                   ▼

            Preserve literal
              information
                  +
              readonly
              inference
```

The key distinction:

```typescript
const config: Config = {
    ...
};
```

means:

```text
config IS typed as Config.
```

---

```typescript
const config = {
    ...
} as Config;
```

means:

```text
Treat config AS Config.
```

---

```typescript
const config = {
    ...
} as const;
```

means:

```text
Preserve this literal structure
as specifically as possible.
```

---

```typescript
const config = {
    ...
} satisfies Config;
```

means:

```text
Check that config satisfies Config,
while retaining useful inference
from the actual expression.
```

---

And:

```typescript
const config = {
    ...
} as const satisfies Config;
```

means:

```text
Preserve exact constants
        +
Readonly inference
        +
Validate against Config
```

A practical example:

```typescript
type Route =
    `/${string}`;

const ROUTES = {
    home: "/",
    dashboard: "/dashboard",
    users: "/users"
} as const satisfies Record<
    string,
    Route
>;
```

From this one object we get:

```text
Runtime configuration
        │
        ├── home
        ├── dashboard
        └── users
        │
        ▼
as const
        │
        ▼
Exact literal values
        +
Readonly properties
        │
        ▼
satisfies
        │
        ▼
Validate every value
against Route
        │
        ▼
typeof + keyof
        │
        ▼
Derive additional types
```

For example:

```typescript
type RouteName =
    keyof typeof ROUTES;
```

gives:

```typescript
"home"
| "dashboard"
| "users"
```

and:

```typescript
type RoutePath =
    typeof ROUTES[
        keyof typeof ROUTES
    ];
```

gives:

```typescript
"/"
| "/dashboard"
| "/users"
```

So the shortest rule to remember is:

```text
: Type
→ Declare


as Type
→ Assert


as const
→ Preserve


satisfies Type
→ Validate


as const satisfies Type
→ Preserve + Validate
```
