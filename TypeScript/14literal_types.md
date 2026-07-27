# TypeScript — Template Literal Types

Template Literal Types allow us to **create, combine, constrain, and extract string literal types using template literal syntax**.

They look similar to JavaScript template literals:

```javascript
const name = "Alice";

const message =
    `Hello ${name}`;
```

But TypeScript Template Literal Types operate at the **type level**:

```typescript
type Greeting =
    `Hello ${string}`;
```

This represents strings that follow the pattern:

```text
Hello <some string>
```

For example:

```typescript
let message: Greeting;

message = "Hello Alice";
message = "Hello John";
message = "Hello TypeScript";
```

But:

```typescript
message = "Hi Alice";
// Error
```

because it does not match:

```text
Hello ${string}
```

---

# 1. Basic Template Literal Type

Syntax:

```typescript
type TypeName =
    `text${Type}`;
```

Example:

```typescript
type UserId =
    `user_${number}`;
```

Valid:

```typescript
const id1: UserId =
    "user_1";

const id2: UserId =
    "user_100";
```

Invalid:

```typescript
const id3: UserId =
    "admin_1";
// Error
```

because the required pattern is:

```text
user_<number>
```

---

# 2. JavaScript Template Literal vs TypeScript Template Literal Type

JavaScript:

```javascript
const user = "Alice";

const message =
    `Hello ${user}`;
```

This creates a **runtime string value**.

TypeScript:

```typescript
type Message =
    `Hello ${string}`;
```

This creates a **compile-time string type**.

Think:

```text
JavaScript Template Literal
        ↓
Creates values at runtime


TypeScript Template Literal Type
        ↓
Creates string patterns at compile time
```

Template Literal Types disappear after TypeScript compilation.

---

# 3. Literal Type Interpolation

We can insert literal types:

```typescript
type Role =
    "admin";

type AdminRole =
    `role_${Role}`;
```

Result:

```typescript
type AdminRole =
    "role_admin";
```

So:

```typescript
const role: AdminRole =
    "role_admin";
```

is valid.

But:

```typescript
const role: AdminRole =
    "role_user";
// Error
```

---

# 4. Template Literal Types with Unions

This is where Template Literal Types become extremely useful.

Suppose:

```typescript
type Role =
    "admin"
    | "user"
    | "guest";
```

Now:

```typescript
type RoleName =
    `role_${Role}`;
```

TypeScript generates:

```typescript
type RoleName =
    "role_admin"
    | "role_user"
    | "role_guest";
```

So:

```typescript
let role: RoleName;

role = "role_admin";
role = "role_user";
role = "role_guest";
```

are valid.

But:

```typescript
role = "role_manager";
// Error
```

---

# 5. Mental Model

Given:

```typescript
type Status =
    "success"
    | "error";
```

and:

```typescript
type StatusMessage =
    `status_${Status}`;
```

Think:

```text
Status

"success" | "error"

        ↓

`status_${Status}`

        ↓

"status_success"
|
"status_error"
```

TypeScript automatically generates the possible string literal types.

---

# 6. Prefixing Union Members

Suppose:

```typescript
type Entity =
    "user"
    | "product"
    | "order";
```

We want:

```text
getUser
getProduct
getOrder
```

We can write:

```typescript
type Getter =
    `get${Capitalize<Entity>}`;
```

Result:

```typescript
type Getter =
    "getUser"
    | "getProduct"
    | "getOrder";
```

Here:

```typescript
Capitalize<Entity>
```

capitalizes each union member.

We will cover the built-in string manipulation types shortly.

---

# 7. Adding Suffixes

Suppose:

```typescript
type Entity =
    "user"
    | "product"
    | "order";
```

We can create:

```typescript
type Service =
    `${Entity}Service`;
```

Result:

```typescript
type Service =
    "userService"
    | "productService"
    | "orderService";
```

---

# 8. Prefix + Value + Suffix

We can combine multiple parts.

```typescript
type Entity =
    "user"
    | "product";

type Event =
    `on${Capitalize<Entity>}Changed`;
```

Result:

```typescript
type Event =
    "onUserChanged"
    | "onProductChanged";
```

Template Literal Types are not limited to a single interpolation.

---

# 9. Multiple Interpolated Unions

Suppose:

```typescript
type Method =
    "get"
    | "create";

type Entity =
    "User"
    | "Product";
```

Now:

```typescript
type Action =
    `${Method}${Entity}`;
```

TypeScript generates every combination:

```typescript
type Action =
    "getUser"
    | "getProduct"
    | "createUser"
    | "createProduct";
```

---

# 10. Cross Multiplication of Unions

When multiple unions appear inside a Template Literal Type, TypeScript creates combinations of them.

Example:

```typescript
type Size =
    "small"
    | "large";

type Color =
    "red"
    | "blue";

type Variant =
    `${Size}-${Color}`;
```

Result:

```typescript
type Variant =
    "small-red"
    | "small-blue"
    | "large-red"
    | "large-blue";
```

Mental model:

```text
Size
├── small
└── large

Color
├── red
└── blue

        ↓

Every combination

        ↓

small-red
small-blue
large-red
large-blue
```

---

# 11. Three Union Combinations

Suppose:

```typescript
type Environment =
    "dev"
    | "prod";

type Service =
    "api"
    | "auth";

type Region =
    "east"
    | "west";
```

Then:

```typescript
type ResourceName =
    `${Environment}-${Service}-${Region}`;
```

generates:

```text
dev-api-east
dev-api-west
dev-auth-east
dev-auth-west

prod-api-east
prod-api-west
prod-auth-east
prod-auth-west
```

This can be useful for strongly typed configuration naming conventions.

---

# 12. Template Literal Type with `string`

We can define patterns without enumerating every possible value.

```typescript
type UserRoute =
    `/users/${string}`;
```

Valid:

```typescript
const route1: UserRoute =
    "/users/123";

const route2: UserRoute =
    "/users/alice";
```

Invalid:

```typescript
const route3: UserRoute =
    "/products/123";
// Error
```

---

# 13. Template Literal Type with `number`

```typescript
type UserId =
    `user-${number}`;
```

Valid examples:

```typescript
const id1: UserId =
    "user-1";

const id2: UserId =
    "user-100";
```

Invalid:

```typescript
const id3: UserId =
    "admin-100";
// Error
```

---

# 14. Pattern-Based IDs

Instead of:

```typescript
type Id =
    string;
```

we could use:

```typescript
type UserId =
    `user_${number}`;

type ProductId =
    `product_${number}`;
```

Now:

```typescript
const userId: UserId =
    "user_123";

const productId: ProductId =
    "product_456";
```

This makes certain classes of accidental identifier mix-ups easier for TypeScript to catch.

---

# 15. Different Identifier Types

```typescript
type UserId =
    `user_${number}`;

type OrderId =
    `order_${number}`;
```

Then:

```typescript
function getUser(
    id: UserId
) {}
```

Valid:

```typescript
getUser("user_100");
```

Invalid:

```typescript
getUser("order_100");
// Error
```

Even though both values are strings, their required patterns differ.

---

# 16. Route Types

Template Literal Types are useful for route patterns.

```typescript
type UserRoute =
    `/users/${number}`;
```

Examples:

```typescript
const route1: UserRoute =
    "/users/1";

const route2: UserRoute =
    "/users/100";
```

Invalid:

```typescript
const route3: UserRoute =
    "/products/1";
// Error
```

---

# 17. Multiple Route Types

```typescript
type Entity =
    "users"
    | "products"
    | "orders";

type ApiRoute =
    `/api/${Entity}`;
```

Result:

```typescript
type ApiRoute =
    "/api/users"
    | "/api/products"
    | "/api/orders";
```

---

# 18. Nested Route Pattern

```typescript
type Entity =
    "users"
    | "products";

type DetailRoute =
    `/api/${Entity}/${number}`;
```

Examples:

```typescript
const route1: DetailRoute =
    "/api/users/1";

const route2: DetailRoute =
    "/api/products/100";
```

This lets the type system describe structured string formats.

---

# 19. HTTP Operation Names

Suppose:

```typescript
type Entity =
    "User"
    | "Product"
    | "Order";
```

We can generate:

```typescript
type Getter =
    `get${Entity}`;
```

Result:

```typescript
type Getter =
    "getUser"
    | "getProduct"
    | "getOrder";
```

Likewise:

```typescript
type Creator =
    `create${Entity}`;
```

Result:

```typescript
type Creator =
    "createUser"
    | "createProduct"
    | "createOrder";
```

---

# 20. Generate CRUD Method Names

```typescript
type Operation =
    "get"
    | "create"
    | "update"
    | "delete";

type Entity =
    "User"
    | "Product";
```

Then:

```typescript
type Method =
    `${Operation}${Entity}`;
```

Result:

```typescript
type Method =
    "getUser"
    | "getProduct"
    | "createUser"
    | "createProduct"
    | "updateUser"
    | "updateProduct"
    | "deleteUser"
    | "deleteProduct";
```

---

# 21. Event Names

Suppose:

```typescript
type Property =
    "name"
    | "email"
    | "age";
```

We want:

```text
nameChanged
emailChanged
ageChanged
```

Use:

```typescript
type EventName =
    `${Property}Changed`;
```

Result:

```typescript
type EventName =
    "nameChanged"
    | "emailChanged"
    | "ageChanged";
```

---

# 22. Generate Event Names from Object Keys

Suppose:

```typescript
type User = {
    name: string;
    age: number;
    active: boolean;
};
```

We already know:

```typescript
keyof User
```

produces:

```typescript
"name"
| "age"
| "active"
```

We can create:

```typescript
type UserEvent =
    `${keyof User}Changed`;
```

Result:

```typescript
type UserEvent =
    "nameChanged"
    | "ageChanged"
    | "activeChanged";
```

This is one of the most useful Template Literal Type patterns.

---

# 23. Generic Event Name

We can make it reusable:

```typescript
type EventName<T> =
    `${string & keyof T}Changed`;
```

Given:

```typescript
type User = {
    name: string;
    age: number;
};
```

Then:

```typescript
type UserEvent =
    EventName<User>;
```

Result:

```typescript
"nameChanged"
|
"ageChanged"
```

The:

```typescript
string & keyof T
```

portion ensures we are working with string-compatible keys.

---

# 24. Why `string & keyof T`?

Object keys can involve:

```text
string

number

symbol
```

Template Literal Types operate with interpolatable primitive types, but symbols cannot be interpolated.

So in generic string-key transformations, a common pattern is:

```typescript
string & keyof T
```

meaning:

```text
Only the string-compatible keys of T.
```

Another explicit approach is:

```typescript
Extract<keyof T, string>
```

Example:

```typescript
type EventName<T> =
    `${Extract<keyof T, string>}Changed`;
```

---

# 25. Event Function Example

Suppose:

```typescript
type User = {
    name: string;
    age: number;
};
```

We could define:

```typescript
type UserEvent =
    `${keyof User}Changed`;
```

Then:

```typescript
function subscribe(
    event: UserEvent,
    callback: () => void
) {}
```

Valid:

```typescript
subscribe(
    "nameChanged",
    () => {}
);

subscribe(
    "ageChanged",
    () => {}
);
```

Invalid:

```typescript
subscribe(
    "emailChanged",
    () => {}
);
// Error
```

---

# 26. Built-In String Manipulation Types

TypeScript provides four important string manipulation utility types:

```typescript
Uppercase<T>

Lowercase<T>

Capitalize<T>

Uncapitalize<T>
```

These work especially well with Template Literal Types.

---

# 27. `Uppercase<T>`

```typescript
type Role =
    "admin"
    | "user";
```

Then:

```typescript
type UpperRole =
    Uppercase<Role>;
```

Result:

```typescript
"ADMIN"
|
"USER"
```

---

# 28. `Lowercase<T>`

```typescript
type Environment =
    "DEV"
    | "PROD";
```

Then:

```typescript
type LowerEnvironment =
    Lowercase<Environment>;
```

Result:

```typescript
"dev"
|
"prod"
```

---

# 29. `Capitalize<T>`

```typescript
type Entity =
    "user"
    | "product";
```

Then:

```typescript
type CapitalizedEntity =
    Capitalize<Entity>;
```

Result:

```typescript
"User"
|
"Product"
```

This is particularly useful for generating method names.

---

# 30. `Uncapitalize<T>`

```typescript
type Entity =
    "User"
    | "Product";
```

Then:

```typescript
type LowerEntity =
    Uncapitalize<Entity>;
```

Result:

```typescript
"user"
|
"product"
```

---

# 31. Generate Getter Names

Suppose:

```typescript
type Property =
    "name"
    | "email"
    | "age";
```

We can generate:

```typescript
type Getter =
    `get${Capitalize<Property>}`;
```

Result:

```typescript
"getName"
|
"getEmail"
|
"getAge"
```

---

# 32. Generate Setter Names

```typescript
type Property =
    "name"
    | "email"
    | "age";

type Setter =
    `set${Capitalize<Property>}`;
```

Result:

```typescript
"setName"
|
"setEmail"
|
"setAge"
```

---

# 33. Generate Getter and Setter Names

```typescript
type Operation =
    "get"
    | "set";

type Property =
    "name"
    | "email";
```

Then:

```typescript
type Method =
    `${Operation}${Capitalize<Property>}`;
```

Result:

```typescript
"getName"
|
"getEmail"
|
"setName"
|
"setEmail"
```

---

# 34. Template Literal Types + Mapped Types

Suppose:

```typescript
type User = {
    name: string;
    age: number;
};
```

We can generate getter methods:

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};
```

Then:

```typescript
type UserGetters =
    Getters<User>;
```

Result:

```typescript
{
    getName: () => string;
    getAge: () => number;
}
```

This is a powerful combination:

```text
keyof
+
Mapped Types
+
Key Remapping
+
Template Literal Types
+
Capitalize
```

---

# 35. Generate Setters

```typescript
type Setters<T> = {
    [K in keyof T as
        `set${Capitalize<string & K>}`
    ]:
        (
            value: T[K]
        ) => void;
};
```

Given:

```typescript
type User = {
    name: string;
    age: number;
};
```

Result:

```typescript
{
    setName:
        (value: string) => void;

    setAge:
        (value: number) => void;
}
```

Notice that the generated method still preserves the original property type.

---

# 36. Generate Getters and Setters

```typescript
type Accessors<T> =
    Getters<T>
    &
    Setters<T>;
```

For:

```typescript
type User = {
    name: string;
    age: number;
};
```

Result conceptually:

```typescript
{
    getName: () => string;
    getAge: () => number;

    setName:
        (value: string) => void;

    setAge:
        (value: number) => void;
}
```

---

# 37. API Method Generation

Suppose:

```typescript
type Resource =
    "user"
    | "product";
```

We can generate:

```typescript
type ApiMethod =
    `get${Capitalize<Resource>}`
    | `create${Capitalize<Resource>}`
    | `delete${Capitalize<Resource>}`;
```

Result:

```typescript
"getUser"
| "getProduct"
| "createUser"
| "createProduct"
| "deleteUser"
| "deleteProduct"
```

---

# 38. CSS-Like Property Types

Template Literal Types can describe structured CSS values.

Example:

```typescript
type PixelValue =
    `${number}px`;
```

Valid:

```typescript
const width: PixelValue =
    "100px";

const height: PixelValue =
    "50px";
```

Invalid:

```typescript
const width: PixelValue =
    "100rem";
// Error
```

---

# 39. Multiple CSS Units

```typescript
type Unit =
    "px"
    | "rem"
    | "em"
    | "%";

type CssSize =
    `${number}${Unit}`;
```

Examples:

```typescript
const a: CssSize =
    "100px";

const b: CssSize =
    "2rem";

const c: CssSize =
    "50%";
```

---

# 40. Structured Configuration Names

Suppose:

```typescript
type Environment =
    "dev"
    | "staging"
    | "prod";

type Service =
    "api"
    | "auth"
    | "worker";
```

Then:

```typescript
type Deployment =
    `${Environment}-${Service}`;
```

Examples:

```text
dev-api
dev-auth
dev-worker

staging-api
staging-auth
staging-worker

prod-api
prod-auth
prod-worker
```

This can help type configuration keys or resource naming conventions.

---

# 41. Log Level Names

```typescript
type Level =
    "info"
    | "warn"
    | "error";

type LogEvent =
    `log:${Uppercase<Level>}`;
```

Result:

```typescript
"log:INFO"
|
"log:WARN"
|
"log:ERROR"
```

---

# 42. Namespaced Events

```typescript
type Domain =
    "user"
    | "order";

type Action =
    "created"
    | "updated"
    | "deleted";

type Event =
    `${Domain}:${Action}`;
```

Result:

```typescript
"user:created"
| "user:updated"
| "user:deleted"
| "order:created"
| "order:updated"
| "order:deleted"
```

This is useful for event-driven systems.

---

# 43. Redux-Like Action Names

```typescript
type Feature =
    "user"
    | "product";

type Action =
    "load"
    | "success"
    | "error";

type ActionType =
    `${Feature}/${Action}`;
```

Result:

```typescript
"user/load"
| "user/success"
| "user/error"
| "product/load"
| "product/success"
| "product/error"
```

---

# 44. Template Literal Types with Conditional Types

Suppose we want to check whether a string starts with:

```text
user_
```

We can write:

```typescript
type IsUserId<T> =
    T extends `user_${string}`
        ? true
        : false;
```

Then:

```typescript
type A =
    IsUserId<"user_123">;
// true

type B =
    IsUserId<"order_123">;
// false
```

We already covered conditional types, so the new part here is that the string itself can be matched against a template pattern.

---

# 45. Pattern Matching Strings

Template Literal Types can act as string patterns.

```typescript
type IsApiRoute<T> =
    T extends `/api/${string}`
        ? true
        : false;
```

Examples:

```typescript
type A =
    IsApiRoute<
        "/api/users"
    >;
// true

type B =
    IsApiRoute<
        "/home"
    >;
// false
```

---

# 46. Template Literal Types + `infer`

We can extract parts of strings.

Suppose:

```typescript
type ExtractId<T> =
    T extends `user_${infer Id}`
        ? Id
        : never;
```

Then:

```typescript
type Result =
    ExtractId<
        "user_123"
    >;
```

Result:

```typescript
"123"
```

We already learned `infer`.

The important new idea is:

```typescript
`user_${infer Id}`
```

can pattern-match a string literal type.

---

# 47. Extract Route Parameter

Suppose:

```typescript
type ExtractParam<T> =
    T extends `/users/${infer Id}`
        ? Id
        : never;
```

Then:

```typescript
type Result =
    ExtractParam<
        "/users/123"
    >;
```

Result:

```typescript
"123"
```

---

# 48. Extract File Extension

```typescript
type Extension<T> =
    T extends `${string}.${infer E}`
        ? E
        : never;
```

Then:

```typescript
type A =
    Extension<
        "image.png"
    >;
// "png"

type B =
    Extension<
        "report.pdf"
    >;
// "pdf"
```

---

# 49. Extract Prefix and Suffix

```typescript
type Split<T> =
    T extends `${infer A}-${infer B}`
        ? [A, B]
        : never;
```

Then:

```typescript
type Result =
    Split<
        "user-admin"
    >;
```

Result:

```typescript
[
    "user",
    "admin"
]
```

---

# 50. Extract Event Property Name

Suppose:

```typescript
type EventProperty<T> =
    T extends `${infer P}Changed`
        ? P
        : never;
```

Then:

```typescript
type A =
    EventProperty<
        "nameChanged"
    >;
// "name"

type B =
    EventProperty<
        "ageChanged"
    >;
// "age"
```

This allows us to reverse a generated naming pattern.

---

# 51. Strongly Typed Event System

Suppose:

```typescript
type User = {
    name: string;
    age: number;
};
```

We want:

```text
nameChanged
ageChanged
```

and we want the callback value to match the property.

We can write:

```typescript
type EventSource<T> = {
    on<K extends string & keyof T>(
        event:
            `${K}Changed`,

        callback:
            (
                value: T[K]
            ) => void
    ): void;
};
```

Now:

```typescript
declare const user:
    EventSource<User>;
```

This is valid:

```typescript
user.on(
    "nameChanged",
    value => {
        // value is string
    }
);
```

And:

```typescript
user.on(
    "ageChanged",
    value => {
        // value is number
    }
);
```

The event name determines the callback value type.

---

# 52. Why the Event Example Is Powerful

For:

```typescript
user.on(
    "nameChanged",
    value => {}
);
```

TypeScript can connect:

```text
"nameChanged"
      ↓
K = "name"
      ↓
T[K]
      ↓
User["name"]
      ↓
string
```

Therefore:

```typescript
value
```

becomes:

```typescript
string
```

This is an example of multiple TypeScript features working together without manually duplicating event definitions.

---

# 53. Strongly Typed Route Names

Suppose:

```typescript
type Resource =
    "users"
    | "products"
    | "orders";
```

Define:

```typescript
type ApiRoute =
    `/api/${Resource}`;
```

Then:

```typescript
function request(
    route: ApiRoute
) {}
```

Valid:

```typescript
request(
    "/api/users"
);
```

Invalid:

```typescript
request(
    "/api/payments"
);
// Error
```

---

# 54. API Version Pattern

```typescript
type Version =
    1 | 2 | 3;

type ApiPath =
    `/api/v${Version}/${string}`;
```

Examples:

```typescript
const a: ApiPath =
    "/api/v1/users";

const b: ApiPath =
    "/api/v2/products";
```

Invalid:

```typescript
const c: ApiPath =
    "/api/v4/users";
// Error
```

---

# 55. Environment Variable Names

Suppose:

```typescript
type Service =
    "DATABASE"
    | "REDIS"
    | "API";
```

We can define:

```typescript
type EnvironmentVariable =
    `${Service}_URL`;
```

Result:

```typescript
"DATABASE_URL"
|
"REDIS_URL"
|
"API_URL"
```

---

# 56. Feature Flag Names

```typescript
type Feature =
    "chat"
    | "payments"
    | "analytics";
```

Then:

```typescript
type FeatureFlag =
    `feature_${Feature}`;
```

Result:

```typescript
"feature_chat"
|
"feature_payments"
|
"feature_analytics"
```

---

# 57. Permission Names

Suppose:

```typescript
type Resource =
    "user"
    | "product";

type Permission =
    "read"
    | "write"
    | "delete";
```

Then:

```typescript
type PermissionName =
    `${Resource}:${Permission}`;
```

Result:

```typescript
"user:read"
| "user:write"
| "user:delete"
| "product:read"
| "product:write"
| "product:delete"
```

This pattern is useful for typed RBAC-style permission names.

---

# 58. React-Like Event Prop Names

Suppose:

```typescript
type Event =
    "click"
    | "change"
    | "submit";
```

We can create:

```typescript
type EventProp =
    `on${Capitalize<Event>}`;
```

Result:

```typescript
"onClick"
|
"onChange"
|
"onSubmit"
```

This illustrates why Template Literal Types are useful in component and library APIs.

---

# 59. Component Variant Names

Suppose:

```typescript
type Size =
    "small"
    | "medium"
    | "large";

type State =
    "default"
    | "active";
```

Then:

```typescript
type Variant =
    `${Size}-${State}`;
```

Result:

```typescript
"small-default"
| "small-active"
| "medium-default"
| "medium-active"
| "large-default"
| "large-active"
```

---

# 60. Key Remapping with Template Literal Types

Suppose:

```typescript
type User = {
    name: string;
    age: number;
};
```

We can rename every property:

```typescript
type Prefixed<T> = {
    [K in keyof T as
        `user_${string & K}`
    ]:
        T[K];
};
```

Then:

```typescript
type Result =
    Prefixed<User>;
```

becomes:

```typescript
{
    user_name: string;
    user_age: number;
}
```

---

# 61. Capitalized Key Remapping

```typescript
type CapitalizedKeys<T> = {
    [K in keyof T as
        Capitalize<
            string & K
        >
    ]:
        T[K];
};
```

Given:

```typescript
type User = {
    name: string;
    age: number;
};
```

Result:

```typescript
{
    Name: string;
    Age: number;
}
```

---

# 62. Prefix Every Property

```typescript
type ApiFields<T> = {
    [K in keyof T as
        `api_${string & K}`
    ]:
        T[K];
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

Result:

```typescript
{
    api_id: number;
    api_name: string;
}
```

---

# 63. Generate Boolean Getter Names

Suppose:

```typescript
type State = {
    loading: boolean;
    active: boolean;
    visible: boolean;
};
```

We can create:

```typescript
type StateGetters<T> = {
    [K in keyof T as
        `is${Capitalize<string & K>}`
    ]:
        () => T[K];
};
```

Result:

```typescript
{
    isLoading:
        () => boolean;

    isActive:
        () => boolean;

    isVisible:
        () => boolean;
}
```

---

# 64. Why Template Literal Types Matter

Without Template Literal Types, we might manually write:

```typescript
type Event =
    "nameChanged"
    | "emailChanged"
    | "ageChanged";
```

while already having:

```typescript
type User = {
    name: string;
    email: string;
    age: number;
};
```

That creates duplicated information.

Instead:

```typescript
type Event =
    `${keyof User}Changed`;
```

can derive the event names from the source type.

If the object changes, the derived type changes with it.

---

# 65. Avoiding Duplicated String Unions

Suppose:

```typescript
type User = {
    name: string;
    email: string;
    age: number;
};
```

Instead of:

```typescript
type Getter =
    "getName"
    | "getEmail"
    | "getAge";
```

derive:

```typescript
type Getter =
    `get${Capitalize<
        string & keyof User
    >}`;
```

This reduces duplicated type declarations.

---

# 66. Template Literal Types Are Compile-Time Only

This:

```typescript
type UserId =
    `user_${number}`;
```

does not automatically validate an arbitrary runtime string.

Suppose:

```typescript
const input =
    getUserInput();
```

If `input` is just:

```typescript
string
```

TypeScript cannot prove that it matches:

```typescript
`user_${number}`
```

You still need runtime validation when data comes from:

```text
Users

APIs

Files

Databases

Environment variables

Network requests

Forms
```

---

# 67. Type Safety Is Not Runtime Validation

This:

```typescript
type Email =
    `${string}@${string}`;
```

may provide some compile-time pattern checking for literal values.

But it is **not a real email validator**.

Many invalid emails could still match that broad shape.

Use runtime validation when correctness depends on actual input.

Template Literal Types describe string structures; they do not replace validation libraries or business rules.

---

# 68. Common Mistake — Confusing Runtime Templates with Types

Runtime:

```typescript
const name =
    "Alice";

const message =
    `Hello ${name}`;
```

Type level:

```typescript
type Message =
    `Hello ${string}`;
```

They use similar syntax but serve different purposes.

---

# 69. Common Mistake — Using `string` When a Pattern Matters

This:

```typescript
type UserId =
    string;
```

accepts:

```text
anything
```

while:

```typescript
type UserId =
    `user_${number}`;
```

describes a more specific pattern.

Use pattern types when the string genuinely has a stable format.

---

# 70. Common Mistake — Creating Huge Union Combinations

Suppose:

```typescript
type A =
    "a1" | "a2" | ...;

type B =
    "b1" | "b2" | ...;

type C =
    "c1" | "c2" | ...;
```

Then:

```typescript
type Result =
    `${A}-${B}-${C}`;
```

generates combinations across all three unions.

Large combinations can become difficult for both developers and the compiler.

For very large generated sets, consider a broader pattern or generating the values outside the type system.

---

# 71. Common Mistake — Overengineering String Types

You could attempt to represent extremely complex formats entirely with Template Literal Types.

That does not mean you should.

Use them where they improve:

```text
Autocomplete

Compile-time safety

Naming consistency

API design

Developer experience
```

Do not turn every runtime string format into a complicated type parser.

---

# 72. Common Mistake — Forgetting Key Compatibility

This generic type can cause problems:

```typescript
type Event<T> =
    `${keyof T}Changed`;
```

because `keyof T` may contain keys that are not suitable for interpolation.

A common solution:

```typescript
type Event<T> =
    `${string & keyof T}Changed`;
```

or:

```typescript
type Event<T> =
    `${Extract<
        keyof T,
        string
    >}Changed`;
```

---

# 73. Common Mistake — Duplicating Generated Types

If you already have:

```typescript
type Property =
    "name"
    | "email"
    | "age";
```

do not manually maintain:

```typescript
type Event =
    "nameChanged"
    | "emailChanged"
    | "ageChanged";
```

when:

```typescript
type Event =
    `${Property}Changed`;
```

can derive it automatically.

---

# 74. Common Mistake — Assuming Pattern Types Sanitize Values

This:

```typescript
type Path =
    `/api/${string}`;
```

does not prevent runtime values such as malformed paths from entering the application through untyped external data.

TypeScript does not sanitize strings.

Runtime validation and normalization remain separate responsibilities.

---

# 75. Common Mistake — Making Types Too Clever

A complicated recursive string parser may be impressive but difficult to maintain.

Prefer:

```text
Readable

Predictable

Useful

Maintainable
```

types.

Advanced TypeScript should reduce mistakes, not create a second programming language inside your application unless the complexity is justified.

---

# 76. When Should You Use Template Literal Types?

Good use cases include:

```text
Event names

API routes

Resource identifiers

Permission names

Feature flags

Configuration keys

CSS-like units

Method names

Generated property names

Namespaced events

Component prop names

Action names

Naming conventions
```

The common theme is:

```text
A string follows a predictable structure.
```

---

# 77. When Should You NOT Use Them?

Avoid using Template Literal Types when:

```text
Strings have no meaningful fixed pattern

The possible combinations are enormous

Runtime validation is the actual requirement

The type becomes harder to understand than string

The format changes frequently

A normal union already solves the problem cleanly
```

---

# 78. Interview Question — What Are Template Literal Types?

Template Literal Types allow TypeScript to construct string literal types using template literal syntax.

Example:

```typescript
type UserId =
    `user_${number}`;
```

They operate at compile time.

---

# 79. Interview Question — How Are They Different from JavaScript Template Literals?

JavaScript:

```typescript
const message =
    `Hello ${name}`;
```

creates a runtime value.

TypeScript:

```typescript
type Message =
    `Hello ${string}`;
```

creates a compile-time type describing a string pattern.

---

# 80. Interview Question — Can Template Literal Types Use Unions?

Yes.

```typescript
type Status =
    "success"
    | "error";

type Message =
    `status_${Status}`;
```

Result:

```typescript
"status_success"
|
"status_error"
```

---

# 81. Interview Question — What Happens with Multiple Unions?

TypeScript generates combinations.

```typescript
type A =
    "x"
    | "y";

type B =
    "1"
    | "2";

type Result =
    `${A}${B}`;
```

Result:

```typescript
"x1"
| "x2"
| "y1"
| "y2"
```

---

# 82. Interview Question — What String Utility Types Exist?

TypeScript provides:

```typescript
Uppercase<T>

Lowercase<T>

Capitalize<T>

Uncapitalize<T>
```

Example:

```typescript
type Entity =
    "user";

type Getter =
    `get${Capitalize<Entity>}`;
```

Result:

```typescript
"getUser"
```

---

# 83. Interview Question — Can They Be Used with `keyof`?

Yes.

```typescript
type User = {
    name: string;
    age: number;
};

type Event =
    `${keyof User}Changed`;
```

Result:

```typescript
"nameChanged"
|
"ageChanged"
```

For generic object types, string-key extraction is often needed.

---

# 84. Interview Question — Can They Be Used with Mapped Types?

Yes.

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};
```

This can generate new property names from existing keys.

---

# 85. Interview Question — Can They Be Used with Conditional Types?

Yes.

```typescript
type IsUserId<T> =
    T extends `user_${string}`
        ? true
        : false;
```

This checks whether a string literal type matches a pattern.

---

# 86. Interview Question — Can `infer` Extract String Parts?

Yes.

```typescript
type ExtractId<T> =
    T extends `user_${infer Id}`
        ? Id
        : never;
```

Then:

```typescript
ExtractId<"user_123">
```

produces:

```typescript
"123"
```

---

# 87. Interview Question — Do Template Literal Types Validate Runtime Strings?

No.

They provide compile-time type checking.

External strings still require runtime validation.

---

# 88. Interview Question — What Are Common Use Cases?

Common uses include:

```text
Typed event names

Routes

IDs

Permissions

Configuration keys

Generated methods

Property remapping

CSS units

Action names

Feature flags
```

---

# 89. Cheat Sheet

```text
TEMPLATE LITERAL TYPE
================================

type Result =
    `prefix_${Type}`;


BASIC
================================

type UserId =
    `user_${number}`;


UNION
================================

type Role =
    "admin"
    | "user";

type RoleName =
    `role_${Role}`;

Result:

"role_admin"
|
"role_user"


MULTIPLE UNIONS
================================

type Action =
    "get"
    | "create";

type Entity =
    "User"
    | "Product";

type Method =
    `${Action}${Entity}`;

Result:

"getUser"
"getProduct"
"createUser"
"createProduct"


ROUTE
================================

type Route =
    `/users/${number}`;


CSS UNIT
================================

type Unit =
    "px"
    | "rem";

type Size =
    `${number}${Unit}`;


EVENT
================================

type Property =
    "name"
    | "age";

type Event =
    `${Property}Changed`;

Result:

"nameChanged"
|
"ageChanged"


OBJECT KEYS
================================

type User = {
    name: string;
    age: number;
};

type Event =
    `${keyof User}Changed`;


GENERIC STRING KEYS
================================

type Event<T> =
    `${
        Extract<keyof T, string>
    }Changed`;


UPPERCASE
================================

Uppercase<"hello">

→ "HELLO"


LOWERCASE
================================

Lowercase<"HELLO">

→ "hello"


CAPITALIZE
================================

Capitalize<"user">

→ "User"


UNCAPITALIZE
================================

Uncapitalize<"User">

→ "user"


GETTER NAME
================================

type Getter<T extends string> =
    `get${Capitalize<T>}`;


MAPPED GETTERS
================================

type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};


PATTERN MATCHING
================================

type IsUserId<T> =
    T extends `user_${string}`
        ? true
        : false;


INFER STRING PART
================================

type ExtractId<T> =
    T extends `user_${infer Id}`
        ? Id
        : never;


ROUTE EXTRACTION
================================

type ExtractId<T> =
    T extends `/users/${infer Id}`
        ? Id
        : never;


PREFIX + SUFFIX EXTRACTION
================================

type Split<T> =
    T extends
        `${infer A}-${infer B}`
            ? [A, B]
            : never;


CORE IDEA
================================

Known string structure
        +
Dynamic type parts
        ↓
Template Literal Types
        ↓
Strongly typed strings
```

---

# 90. Important Rules to Remember

```text
1. Template Literal Types operate at the type level.

2. JavaScript template literals create runtime strings.

3. TypeScript Template Literal Types create string types.

4. They use backtick syntax.

5. Types can be interpolated using ${...}.

6. Literal types can be interpolated.

7. Union types can be interpolated.

8. Union interpolation generates possible combinations.

9. Multiple unions generate cross-product combinations.

10. Large cross products should be avoided.

11. string can describe variable string portions.

12. number can describe number-like string portions.

13. Template Literal Types can describe structured IDs.

14. They can describe routes.

15. They can describe event names.

16. They can describe permission names.

17. They can describe configuration names.

18. They can describe CSS-like units.

19. They can describe action names.

20. They can describe feature flag names.

21. They can describe resource naming conventions.

22. Template Literal Types work with keyof.

23. Object keys can generate event names.

24. Object keys can generate method names.

25. Generic keyof may contain non-string keys.

26. string & keyof T can select string-compatible keys.

27. Extract<keyof T, string> can explicitly select string keys.

28. Template Literal Types work with mapped types.

29. They are useful for key remapping.

30. Mapped properties can become generated getters.

31. Mapped properties can become generated setters.

32. Original property value types can be preserved.

33. Uppercase<T> transforms strings to uppercase.

34. Lowercase<T> transforms strings to lowercase.

35. Capitalize<T> capitalizes the first character.

36. Uncapitalize<T> lowercases the first character.

37. These utilities work with string literal unions.

38. They combine naturally with Template Literal Types.

39. Template Literal Types work with conditional types.

40. Strings can be tested against template patterns.

41. Template Literal Types work with infer.

42. infer can extract portions of strings.

43. Prefixes can be matched.

44. Suffixes can be matched.

45. Route values can be extracted.

46. Event property names can be extracted.

47. String structures can be decomposed.

48. Template Literal Types are compile-time only.

49. They generate no runtime JavaScript.

50. They do not validate arbitrary external strings at runtime.

51. They do not sanitize strings.

52. They do not replace runtime validation.

53. API data still requires runtime validation when necessary.

54. User input still requires runtime validation.

55. Environment variables still require runtime validation.

56. Template Literal Types can improve autocomplete.

57. They can reduce duplicated string unions.

58. Derived names automatically follow source types.

59. They help enforce naming conventions.

60. They can improve library APIs.

61. They are useful for strongly typed event systems.

62. Event names can determine callback types.

63. They are useful for strongly typed route systems.

64. They are useful for typed configuration systems.

65. They are useful for RBAC permission strings.

66. They are useful for namespaced events.

67. They are useful for component prop generation.

68. Do not model every string with Template Literal Types.

69. Use them when strings have meaningful structure.

70. Avoid enormous generated unions.

71. Prefer readable types over clever types.

72. A normal string is sometimes the correct type.

73. A normal union is sometimes simpler.

74. Template Literal Types should improve safety or developer experience.

75. The core syntax to remember is:

`prefix_${Type}`

76. With unions:

`${UnionA}-${UnionB}`

generates combinations.

77. With string utilities:

`get${Capitalize<Property>}`

can generate method names.

78. With conditional types:

T extends `prefix_${string}`

can test patterns.

79. With infer:

T extends `prefix_${infer Value}`

can extract string parts.

80. With mapped types:

[K in keyof T as
    `get${Capitalize<string & K>}`]

can generate new object keys.
```

---

# 91. Final Mental Model

Start with:

```typescript
type Entity =
    "user"
    | "product";
```

Template Literal Types can transform this:

```typescript
type Service =
    `${Entity}Service`;
```

into:

```text
"userService"
|
"productService"
```

Add string utilities:

```typescript
type Getter =
    `get${Capitalize<Entity>}`;
```

and get:

```text
"getUser"
|
"getProduct"
```

Use multiple unions:

```typescript
type Operation =
    "get"
    | "create";

type Method =
    `${Operation}${Capitalize<Entity>}`;
```

and get:

```text
getUser
getProduct
createUser
createProduct
```

Use object keys:

```typescript
type User = {
    name: string;
    age: number;
};

type Event =
    `${keyof User}Changed`;
```

and get:

```text
nameChanged
ageChanged
```

Use pattern matching:

```typescript
type IsUserId<T> =
    T extends `user_${string}`
        ? true
        : false;
```

and TypeScript can test:

```text
"user_123"
     ↓
matches user_${string}
     ↓
true
```

Use `infer`:

```typescript
type ExtractId<T> =
    T extends `user_${infer Id}`
        ? Id
        : never;
```

and:

```text
"user_123"
     ↓
user_${infer Id}
     ↓
Id = "123"
```

The complete mental model is:

```text
String Literal Types
        │
        ▼
Template Literal Types
        │
        ├───────────────┐
        ▼               ▼
     Unions         String Utilities
        │               │
        ▼               ▼
 Generate Names      Transform Names
        │               │
        └───────┬───────┘
                ▼
             keyof
                │
                ▼
         Object Properties
                │
                ▼
          Mapped Types
                │
                ▼
          Generate Keys
                │
                ▼
      Conditional Types
                │
                ▼
          Match Strings
                │
                ▼
              infer
                │
                ▼
        Extract String Parts
```

The most important thing to remember is:

```typescript
type UserId =
    `user_${number}`;
```

**Template Literal Types allow the TypeScript type system to understand the structure of strings instead of treating every string simply as `string`.**
