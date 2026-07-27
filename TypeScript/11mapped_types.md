# TypeScript — Mapped Types

Mapped Types allow us to **create a new object type by iterating over the keys of another type or a union of property keys**.

The core syntax is:

```typescript
type NewType<T> = {
    [K in keyof T]: T[K];
};
```

At first this may look unusual.

Think of it like:

```text
for every key K in T
    create property K
    use T[K] as its type
```

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
type Copy<T> = {
    [K in keyof T]: T[K];
};

type UserCopy =
    Copy<User>;
```

produces:

```typescript
type UserCopy = {
    id: number;
    name: string;
    active: boolean;
};
```

Mapped types become powerful when we **modify those properties while mapping them**.

---

# 1. Why Mapped Types Exist

Suppose we have:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Now we want another type where every property is optional:

```typescript
type OptionalUser = {
    id?: number;
    name?: string;
    email?: string;
};
```

We could write it manually.

But if `User` changes:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    active: boolean;
};
```

we also have to update `OptionalUser`.

Instead:

```typescript
type Optional<T> = {
    [K in keyof T]?: T[K];
};
```

Now:

```typescript
type OptionalUser =
    Optional<User>;
```

automatically follows `User`.

This is the idea behind mapped types:

```text
Existing Type
      │
      ▼
Get its keys
      │
      ▼
Map over each key
      │
      ▼
Transform properties
      │
      ▼
New Type
```

---

# 2. Core Syntax

The basic structure is:

```typescript
type Mapped<T> = {
    [K in keyof T]: T[K];
};
```

There are three important pieces:

```typescript
keyof T
```

gets the keys.

```typescript
K in keyof T
```

iterates over those keys.

```typescript
T[K]
```

gets the type belonging to the current key.

---

# 3. Understanding `[K in keyof T]`

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

Then:

```typescript
keyof User
```

is:

```text
"id" | "name" | "active"
```

So:

```typescript
[K in keyof User]
```

conceptually processes:

```text
K = "id"

K = "name"

K = "active"
```

and creates a property for each one.

---

# 4. Understanding `T[K]`

For:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

when:

```text
K = "id"
```

then:

```typescript
User[K]
```

becomes:

```typescript
User["id"]
```

which is:

```text
number
```

When:

```text
K = "name"
```

we get:

```text
string
```

When:

```text
K = "active"
```

we get:

```text
boolean
```

Therefore:

```typescript
type Copy<T> = {
    [K in keyof T]: T[K];
};
```

preserves the original structure.

---

# 5. Mapping Over a Union

Mapped types do not require `keyof`.

We can map directly over a union:

```typescript
type Role =
    "admin"
    | "editor"
    | "viewer";
```

Then:

```typescript
type Permissions = {
    [R in Role]: boolean;
};
```

Result:

```typescript
type Permissions = {
    admin: boolean;
    editor: boolean;
    viewer: boolean;
};
```

---

# 6. Mapping Keys to Another Type

The resulting value does not need to be:

```typescript
T[K]
```

For example:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

We can create:

```typescript
type ValidationState<T> = {
    [K in keyof T]: boolean;
};
```

Then:

```typescript
type UserValidation =
    ValidationState<User>;
```

Result:

```typescript
type UserValidation = {
    id: boolean;
    name: boolean;
    active: boolean;
};
```

We kept the keys but changed every value type.

---

# 7. Mapping Everything to `string`

```typescript
type StringValues<T> = {
    [K in keyof T]: string;
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

Then:

```typescript
type UserStrings =
    StringValues<User>;
```

becomes:

```typescript
type UserStrings = {
    id: string;
    name: string;
    active: string;
};
```

---

# 8. Mapping Everything to Another Structure

```typescript
type FieldState<T> = {
    [K in keyof T]: {
        value: T[K];
        error: string | null;
        touched: boolean;
    };
};
```

Given:

```typescript
type LoginForm = {
    email: string;
    password: string;
};
```

Then:

```typescript
type LoginFormState =
    FieldState<LoginForm>;
```

Result:

```typescript
type LoginFormState = {
    email: {
        value: string;
        error: string | null;
        touched: boolean;
    };

    password: {
        value: string;
        error: string | null;
        touched: boolean;
    };
};
```

This is useful in form systems.

---

# 9. Adding Optional Properties

Use:

```text
?
```

after the mapped property.

```typescript
type Optional<T> = {
    [K in keyof T]?: T[K];
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
type OptionalUser =
    Optional<User>;
```

becomes:

```typescript
type OptionalUser = {
    id?: number;
    name?: string;
};
```

---

# 10. Recreating `Partial<T>`

The built-in:

```typescript
Partial<T>
```

essentially performs this transformation:

```typescript
type MyPartial<T> = {
    [K in keyof T]?:
        T[K];
};
```

Example:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
type UpdateUser =
    MyPartial<User>;
```

produces:

```typescript
{
    id?: number;
    name?: string;
}
```

Now we understand why `Partial<T>` is called a mapped type.

---

# 11. Adding `readonly`

Mapped types can add:

```typescript
readonly
```

to every property.

```typescript
type Immutable<T> = {
    readonly [K in keyof T]:
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

Then:

```typescript
type ImmutableUser =
    Immutable<User>;
```

becomes:

```typescript
type ImmutableUser = {
    readonly id: number;
    readonly name: string;
};
```

---

# 12. Recreating `Readonly<T>`

The built-in:

```typescript
Readonly<T>
```

essentially works like:

```typescript
type MyReadonly<T> = {
    readonly [K in keyof T]:
        T[K];
};
```

Example:

```typescript
const user:
    MyReadonly<User> = {
        id: 1,
        name: "Alice"
    };
```

This fails:

```typescript
user.name = "Bob";
```

because `name` is readonly.

---

# 13. Mapping Modifiers

Mapped types can manipulate two important property modifiers:

```text
Optional
?

Readonly
readonly
```

We can:

```text
Add optional

Remove optional

Add readonly

Remove readonly
```

using mapped type modifier syntax.

---

# 14. Explicitly Adding Optional

These are equivalent:

```typescript
type A<T> = {
    [K in keyof T]?:
        T[K];
};
```

and:

```typescript
type B<T> = {
    [K in keyof T]+?:
        T[K];
};
```

The:

```text
+
```

means:

```text
Add this modifier.
```

Usually we omit `+` because adding is the default behavior.

---

# 15. Removing Optional Properties

Use:

```text
-?
```

to remove optional modifiers.

Example:

```typescript
type RequiredFields<T> = {
    [K in keyof T]-?:
        T[K];
};
```

Given:

```typescript
type User = {
    id?: number;
    name?: string;
};
```

Then:

```typescript
type CompleteUser =
    RequiredFields<User>;
```

becomes:

```typescript
type CompleteUser = {
    id: number;
    name: string;
};
```

---

# 16. Recreating `Required<T>`

The built-in:

```typescript
Required<T>
```

essentially works like:

```typescript
type MyRequired<T> = {
    [K in keyof T]-?:
        T[K];
};
```

The important part is:

```text
-?
```

Meaning:

```text
Remove optional modifier.
```

---

# 17. Explicitly Adding Readonly

We can write:

```typescript
type Immutable<T> = {
    +readonly [K in keyof T]:
        T[K];
};
```

This is equivalent to:

```typescript
type Immutable<T> = {
    readonly [K in keyof T]:
        T[K];
};
```

Again:

```text
+
```

means add.

---

# 18. Removing `readonly`

Use:

```text
-readonly
```

Example:

```typescript
type Mutable<T> = {
    -readonly [K in keyof T]:
        T[K];
};
```

Suppose:

```typescript
type User = {
    readonly id: number;
    readonly name: string;
};
```

Then:

```typescript
type MutableUser =
    Mutable<User>;
```

becomes:

```typescript
type MutableUser = {
    id: number;
    name: string;
};
```

---

# 19. Modifier Cheat Sheet

```text
?           Add optional

+?          Add optional

-?          Remove optional


readonly    Add readonly

+readonly   Add readonly

-readonly   Remove readonly
```

This is one of the most important mapped-type concepts.

---

# 20. Removing Both Modifiers

Suppose:

```typescript
type User = {
    readonly id?: number;
    readonly name?: string;
};
```

We can create:

```typescript
type MutableRequired<T> = {
    -readonly
    [K in keyof T]-?:
        T[K];
};
```

Then:

```typescript
type MutableUser =
    MutableRequired<User>;
```

becomes:

```typescript
type MutableUser = {
    id: number;
    name: string;
};
```

We removed both:

```text
readonly

?
```

---

# 21. Adding Both Modifiers

```typescript
type OptionalReadonly<T> = {
    readonly
    [K in keyof T]?:
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
    readonly id?: number;
    readonly name?: string;
}
```

---

# 22. Recreating `Pick<T, K>`

We previously used:

```typescript
Pick<T, K>
```

A simplified implementation is:

```typescript
type MyPick<
    T,
    K extends keyof T
> = {
    [P in K]:
        T[P];
};
```

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Then:

```typescript
type UserPreview =
    MyPick<
        User,
        "id" | "name"
    >;
```

produces:

```typescript
{
    id: number;
    name: string;
}
```

---

# 23. Understanding `MyPick`

```typescript
type MyPick<
    T,
    K extends keyof T
> = {
    [P in K]:
        T[P];
};
```

If:

```typescript
K =
    "id"
    | "name"
```

then:

```typescript
[P in K]
```

iterates over:

```text
"id"

"name"
```

Then:

```typescript
T[P]
```

retrieves each property's original type.

---

# 24. Key Remapping

Mapped types can do more than preserve existing property names.

We can create **new property names** using:

```typescript
as
```

Syntax:

```typescript
type Mapped<T> = {
    [K in keyof T as NewKey]:
        T[K];
};
```

This is called:

```text
Key Remapping
```

---

# 25. Basic Key Remapping

Suppose:

```typescript
type User = {
    name: string;
    email: string;
};
```

We want:

```typescript
type UserGetters = {
    getName: () => string;
    getEmail: () => string;
};
```

We can generate this automatically.

---

# 26. Generate Getter Names

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<
            string & K
        >}`
    ]: () => T[K];
};
```

Then:

```typescript
type UserGetters =
    Getters<User>;
```

becomes:

```typescript
type UserGetters = {
    getName:
        () => string;

    getEmail:
        () => string;
};
```

This combines:

```text
Mapped Types

keyof

Indexed Access

Key Remapping

Template Literal Types

Capitalize
```

---

# 27. Why `string & K`?

`keyof T` can potentially contain:

```text
string

number

symbol
```

But:

```typescript
Capitalize<T>
```

expects a string-like type.

So:

```typescript
string & K
```

narrows `K` to its string-compatible part.

Another common approach is:

```typescript
K extends string
```

inside conditional transformations.

---

# 28. Generate Event Handler Names

Suppose:

```typescript
type Events = {
    click: MouseEvent;
    focus: FocusEvent;
    submit: SubmitEvent;
};
```

We can generate:

```typescript
type EventHandlers<T> = {
    [K in keyof T as
        `on${Capitalize<
            string & K
        >}`
    ]: (
        event: T[K]
    ) => void;
};
```

Result:

```typescript
type Handlers = {
    onClick:
        (
            event: MouseEvent
        ) => void;

    onFocus:
        (
            event: FocusEvent
        ) => void;

    onSubmit:
        (
            event: SubmitEvent
        ) => void;
};
```

This pattern is especially relevant to React.

---

# 29. Generate Boolean Flags

Suppose:

```typescript
type Features = {
    darkMode: unknown;
    search: unknown;
    analytics: unknown;
};
```

We can create:

```typescript
type FeatureFlags<T> = {
    [K in keyof T]:
        boolean;
};
```

Result:

```typescript
{
    darkMode: boolean;
    search: boolean;
    analytics: boolean;
}
```

---

# 30. Generate Nullable Version

```typescript
type Nullable<T> = {
    [K in keyof T]:
        T[K] | null;
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
type NullableUser =
    Nullable<User>;
```

becomes:

```typescript
{
    id: number | null;
    name: string | null;
}
```

---

# 31. Generate Promise Version

```typescript
type AsyncFields<T> = {
    [K in keyof T]:
        Promise<T[K]>;
};
```

Given:

```typescript
type Data = {
    user: User;
    products: Product[];
};
```

Then:

```typescript
type AsyncData =
    AsyncFields<Data>;
```

becomes:

```typescript
{
    user:
        Promise<User>;

    products:
        Promise<Product[]>;
}
```

---

# 32. Generate Functions

```typescript
type Functions<T> = {
    [K in keyof T]:
        () => T[K];
};
```

Given:

```typescript
type Config = {
    theme: string;
    timeout: number;
};
```

Result:

```typescript
{
    theme:
        () => string;

    timeout:
        () => number;
}
```

---

# 33. Generate Setter Functions

We can combine key remapping and indexed access:

```typescript
type Setters<T> = {
    [K in keyof T as
        `set${Capitalize<
            string & K
        >}`
    ]: (
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
type UserSetters = {
    setName:
        (
            value: string
        ) => void;

    setAge:
        (
            value: number
        ) => void;
};
```

---

# 34. Generate Getters and Setters

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<
            string & K
        >}`
    ]: () => T[K];
};

type Setters<T> = {
    [K in keyof T as
        `set${Capitalize<
            string & K
        >}`
    ]: (
        value: T[K]
    ) => void;
};
```

Then:

```typescript
type UserAccessors =
    Getters<User>
    &
    Setters<User>;
```

produces both getter and setter methods.

---

# 35. Filtering Properties

Key remapping can also **remove properties**.

If the remapped key becomes:

```typescript
never
```

that property disappears.

Example:

```typescript
type RemoveId<T> = {
    [K in keyof T as
        K extends "id"
            ? never
            : K
    ]: T[K];
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Then:

```typescript
type NewUser =
    RemoveId<User>;
```

becomes:

```typescript
{
    name: string;
    email: string;
}
```

---

# 36. Why `never` Removes the Key

During mapping:

```text
"id"
→ never

"name"
→ "name"

"email"
→ "email"
```

The `never` key is not created.

So:

```text
id disappears
```

This technique powers many advanced mapped-type transformations.

---

# 37. Generic Property Exclusion

Instead of hardcoding:

```text
"id"
```

we can create:

```typescript
type RemoveKeys<
    T,
    K
> = {
    [P in keyof T as
        P extends K
            ? never
            : P
    ]: T[P];
};
```

Then:

```typescript
type PublicUser =
    RemoveKeys<
        User,
        "password"
        | "token"
    >;
```

This demonstrates the idea behind property filtering.

For normal application code, built-in:

```typescript
Omit<T, K>
```

is usually clearer.

---

# 38. Filtering by Value Type

Mapped types can also inspect property value types.

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    age: number;
    active: boolean;
};
```

We want only string properties.

```typescript
type StringProperties<T> = {
    [K in keyof T as
        T[K] extends string
            ? K
            : never
    ]: T[K];
};
```

Then:

```typescript
type UserStrings =
    StringProperties<User>;
```

becomes:

```typescript
{
    name: string;
    email: string;
}
```

---

# 39. Filtering Numeric Properties

```typescript
type NumberProperties<T> = {
    [K in keyof T as
        T[K] extends number
            ? K
            : never
    ]: T[K];
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
    age: number;
    active: boolean;
};
```

Result:

```typescript
{
    id: number;
    age: number;
}
```

---

# 40. Filtering Boolean Properties

```typescript
type BooleanProperties<T> = {
    [K in keyof T as
        T[K] extends boolean
            ? K
            : never
    ]: T[K];
};
```

Result:

```typescript
{
    active: boolean;
}
```

This combines:

```text
Mapped Types

Indexed Access

Conditional Types

never
```

We will cover conditional types properly in the next topic.

---

# 41. Mapping Over Literal Unions

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
type StatusConfig = {
    [S in Status]: {
        label: string;
        retryable: boolean;
    };
};
```

Now every status must exist.

```typescript
const config:
    StatusConfig = {

    idle: {
        label: "Idle",
        retryable: false
    },

    loading: {
        label: "Loading",
        retryable: false
    },

    success: {
        label: "Complete",
        retryable: false
    },

    error: {
        label: "Failed",
        retryable: true
    }
};
```

---

# 42. Mapped Type vs `Record`

This:

```typescript
type StatusConfig = {
    [S in Status]: string;
};
```

is similar to:

```typescript
type StatusConfig =
    Record<
        Status,
        string
    >;
```

`Record` is essentially a convenient built-in abstraction for a common mapped-type pattern.

Use `Record` when it expresses the requirement clearly.

Use custom mapped types when you need additional transformations.

---

# 43. Form Error Model

Suppose:

```typescript
type RegistrationForm = {
    name: string;
    email: string;
    password: string;
    age: number;
};
```

We can derive:

```typescript
type FormErrors<T> = {
    [K in keyof T]?:
        string;
};
```

Then:

```typescript
type RegistrationErrors =
    FormErrors<
        RegistrationForm
    >;
```

Result:

```typescript
{
    name?: string;
    email?: string;
    password?: string;
    age?: string;
}
```

Notice:

```text
Original values
→ different types

Keys
→ preserved
```

---

# 44. Form Touched Model

```typescript
type FormTouched<T> = {
    [K in keyof T]?:
        boolean;
};
```

Then:

```typescript
type RegistrationTouched =
    FormTouched<
        RegistrationForm
    >;
```

becomes:

```typescript
{
    name?: boolean;
    email?: boolean;
    password?: boolean;
    age?: boolean;
}
```

---

# 45. Form State Model

We can combine multiple mapped types:

```typescript
type FormState<T> = {
    values: T;

    errors: {
        [K in keyof T]?:
            string;
    };

    touched: {
        [K in keyof T]?:
            boolean;
    };
};
```

Then:

```typescript
type RegistrationState =
    FormState<
        RegistrationForm
    >;
```

This ensures all form-related structures follow the same field names.

---

# 46. Validation Rules

Suppose:

```typescript
type UserForm = {
    name: string;
    email: string;
    age: number;
};
```

We can create:

```typescript
type Validators<T> = {
    [K in keyof T]?:
        (
            value: T[K]
        ) => string | null;
};
```

Then:

```typescript
const validators:
    Validators<UserForm> = {

    name: value =>
        value.length === 0
            ? "Name is required"
            : null,

    age: value =>
        value < 18
            ? "Must be 18 or older"
            : null
};
```

TypeScript knows:

```text
name validator
→ receives string

email validator
→ receives string

age validator
→ receives number
```

---

# 47. API Response Transformation

Suppose:

```typescript
type Services = {
    users: User[];
    products: Product[];
    settings: Settings;
};
```

We can create:

```typescript
type ApiMethods<T> = {
    [K in keyof T]:
        () => Promise<T[K]>;
};
```

Result:

```typescript
type API = {
    users:
        () => Promise<User[]>;

    products:
        () => Promise<Product[]>;

    settings:
        () => Promise<Settings>;
};
```

This can help model strongly typed service layers.

---

# 48. API State Model

A common frontend pattern is:

```typescript
type AsyncState<T> = {
    data: T | null;
    loading: boolean;
    error: string | null;
};
```

Now map every API resource:

```typescript
type ApiState<T> = {
    [K in keyof T]:
        AsyncState<T[K]>;
};
```

Given:

```typescript
type Resources = {
    users: User[];
    products: Product[];
};
```

Result:

```typescript
type State = {
    users:
        AsyncState<User[]>;

    products:
        AsyncState<Product[]>;
};
```

---

# 49. Permissions Model

Suppose:

```typescript
type Resource =
    "users"
    | "products"
    | "orders";
```

Create:

```typescript
type Permissions = {
    [R in Resource]: {
        read: boolean;
        create: boolean;
        update: boolean;
        delete: boolean;
    };
};
```

Now every resource must have a complete permission configuration.

---

# 50. Nested Mapped Types

Mapped types can be nested.

```typescript
type Resource =
    "users"
    | "products";

type Action =
    "read"
    | "create"
    | "update"
    | "delete";
```

Then:

```typescript
type Permissions = {
    [R in Resource]: {
        [A in Action]:
            boolean;
    };
};
```

Result:

```typescript
type Permissions = {
    users: {
        read: boolean;
        create: boolean;
        update: boolean;
        delete: boolean;
    };

    products: {
        read: boolean;
        create: boolean;
        update: boolean;
        delete: boolean;
    };
};
```

---

# 51. Feature Flag Configuration

```typescript
type Feature =
    "darkMode"
    | "newDashboard"
    | "betaSearch";
```

Then:

```typescript
type FeatureConfig = {
    [F in Feature]: {
        enabled: boolean;
        rolloutPercentage: number;
    };
};
```

This ensures every feature has the required configuration.

---

# 52. React Component State Pattern

Suppose:

```typescript
type Filters = {
    search: string;
    category: string;
    minPrice: number;
    available: boolean;
};
```

We could derive validation:

```typescript
type FilterValidation = {
    [K in keyof Filters]?:
        string;
};
```

and touched state:

```typescript
type FilterTouched = {
    [K in keyof Filters]?:
        boolean;
};
```

Instead of manually duplicating every filter key.

---

# 53. React Change Handler Pattern

```typescript
type ChangeHandlers<T> = {
    [K in keyof T as
        `on${Capitalize<
            string & K
        >}Change`
    ]: (
        value: T[K]
    ) => void;
};
```

Given:

```typescript
type Settings = {
    theme: string;
    notifications: boolean;
};
```

Result:

```typescript
type SettingsHandlers = {
    onThemeChange:
        (
            value: string
        ) => void;

    onNotificationsChange:
        (
            value: boolean
        ) => void;
};
```

---

# 54. Mapped Types Preserve Relationships

This is one of their biggest advantages.

Given:

```typescript
type Settings = {
    theme: string;
    notifications: boolean;
    timeout: number;
};
```

If we manually create:

```typescript
type Handlers = {
    onThemeChange:
        (value: string) => void;

    onNotificationsChange:
        (value: boolean) => void;

    onTimeoutChange:
        (value: number) => void;
};
```

we duplicate information.

With:

```typescript
type ChangeHandlers<T> = {
    [K in keyof T as
        `on${Capitalize<
            string & K
        >}Change`
    ]:
        (
            value: T[K]
        ) => void;
};
```

the relationship stays synchronized.

---

# 55. Deep Partial

We learned:

```typescript
Partial<T>
```

is shallow.

Mapped types can create a recursive version.

A simplified version:

```typescript
type DeepPartial<T> = {
    [K in keyof T]?:
        T[K] extends object
            ? DeepPartial<T[K]>
            : T[K];
};
```

Example:

```typescript
type User = {
    id: number;

    profile: {
        name: string;

        address: {
            city: string;
            country: string;
        };
    };
};
```

Then:

```typescript
type Update =
    DeepPartial<User>;
```

allows nested properties to become optional.

---

# 56. Deep Partial Example

```typescript
const update:
    DeepPartial<User> = {

    profile: {
        address: {
            city: "Kolkata"
        }
    }
};
```

With normal:

```typescript
Partial<User>
```

the nested structure would not automatically become partial.

---

# 57. Deep Readonly

Similarly:

```typescript
type DeepReadonly<T> = {
    readonly [K in keyof T]:
        T[K] extends object
            ? DeepReadonly<T[K]>
            : T[K];
};
```

This recursively applies readonly.

But real-world deep utility types require more care because:

```text
Arrays

Functions

Maps

Sets

Dates

Special objects
```

are also objects.

So simplified recursive examples should not automatically be treated as production-ready implementations.

---

# 58. Mapped Types and Arrays

Arrays themselves have keys and methods.

Therefore generic mapped types can interact with arrays in ways that may initially seem surprising.

TypeScript's mapped types have special handling for arrays and tuples in many common transformations.

For example:

```typescript
Readonly<number[]>
```

results in a readonly array type.

But when designing custom recursive mapped types, arrays should often be considered separately.

---

# 59. Mapped Types and Tuples

Given:

```typescript
type Coordinates = [
    number,
    number
];
```

A mapped transformation can preserve tuple structure.

For example:

```typescript
type Nullable<T> = {
    [K in keyof T]:
        T[K] | null;
};
```

Applied to a tuple, TypeScript can preserve the tuple-like relationship rather than simply treating it as an arbitrary object.

This becomes useful in advanced generic libraries.

---

# 60. Key Remapping with Prefixes

Suppose:

```typescript
type User = {
    name: string;
    email: string;
};
```

Generate:

```text
userName

userEmail
```

with:

```typescript
type Prefixed<T> = {
    [K in keyof T as
        `user${Capitalize<
            string & K
        >}`
    ]: T[K];
};
```

Result:

```typescript
{
    userName: string;
    userEmail: string;
}
```

---

# 61. Key Remapping with Suffixes

```typescript
type StateFields<T> = {
    [K in keyof T as
        `${string & K}State`
    ]: T[K];
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
    nameState: string;
    ageState: number;
}
```

---

# 62. Generate `is...` Properties

Suppose:

```typescript
type Status = {
    loading: unknown;
    active: unknown;
    authenticated: unknown;
};
```

Create:

```typescript
type BooleanStatus<T> = {
    [K in keyof T as
        `is${Capitalize<
            string & K
        >}`
    ]: boolean;
};
```

Result:

```typescript
{
    isLoading: boolean;
    isActive: boolean;
    isAuthenticated: boolean;
}
```

---

# 63. Generate API Method Names

```typescript
type Resources = {
    user: User;
    product: Product;
    order: Order;
};
```

Then:

```typescript
type GetMethods<T> = {
    [K in keyof T as
        `get${Capitalize<
            string & K
        >}`
    ]: () => Promise<T[K]>;
};
```

Result:

```typescript
{
    getUser:
        () => Promise<User>;

    getProduct:
        () => Promise<Product>;

    getOrder:
        () => Promise<Order>;
}
```

---

# 64. Mapped Types with Existing Utility Types

Mapped types can be combined with utilities.

Example:

```typescript
type OptionalFunctions<T> =
    Partial<{
        [K in keyof T]:
            () => T[K];
    }>;
```

Or:

```typescript
type ReadonlyState<T> =
    Readonly<{
        [K in keyof T]:
            AsyncState<T[K]>;
    }>;
```

But avoid unnecessary complexity when a simpler type communicates the same thing.

---

# 65. Mapping Only Selected Keys

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    password: string;
};
```

We can create:

```typescript
type OptionalFields<
    T,
    K extends keyof T
> = {
    [P in K]?:
        T[P];
};
```

Then:

```typescript
type EditableUser =
    OptionalFields<
        User,
        "name" | "email"
    >;
```

Result:

```typescript
{
    name?: string;
    email?: string;
}
```

This behaves similarly to:

```typescript
Partial<
    Pick<
        User,
        "name" | "email"
    >
>
```

---

# 66. Making Only Some Properties Optional

Sometimes we want the whole original type but selected fields optional.

Example:

```typescript
type SetOptional<
    T,
    K extends keyof T
> =
    Omit<T, K>
    &
    Partial<
        Pick<T, K>
    >;
```

Given:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Then:

```typescript
type UserInput =
    SetOptional<
        User,
        "id"
    >;
```

conceptually becomes:

```typescript
{
    id?: number;
    name: string;
    email: string;
}
```

This combines utility types that are themselves based on mapped-type concepts.

---

# 67. Making Selected Properties Required

```typescript
type SetRequired<
    T,
    K extends keyof T
> =
    Omit<T, K>
    &
    Required<
        Pick<T, K>
    >;
```

Useful when a broader model has optional fields but a specific operation requires some of them.

---

# 68. Mapped Types vs Index Signatures

These look similar:

```typescript
type Dictionary = {
    [key: string]:
        string;
};
```

and:

```typescript
type StatusMap = {
    [K in Status]:
        string;
};
```

But they represent different ideas.

Index signature:

```typescript
[key: string]: string
```

means:

```text
Any string key is allowed.
```

Mapped type:

```typescript
[K in Status]: string
```

means:

```text
Create properties from
a known set of keys.
```

---

# 69. Index Signature Example

```typescript
type Dictionary = {
    [key: string]:
        string;
};
```

This allows:

```typescript
const dictionary:
    Dictionary = {
        anything: "value",
        hello: "world",
        random: "text"
    };
```

The keys are open-ended.

---

# 70. Mapped Type Example

```typescript
type Role =
    "admin"
    | "user";
```

Then:

```typescript
type RoleLabels = {
    [R in Role]:
        string;
};
```

Requires exactly the relevant known properties:

```typescript
const labels:
    RoleLabels = {
        admin:
            "Administrator",

        user:
            "User"
    };
```

This is based on a finite key union.

---

# 71. Mapped Types vs `Record`

This:

```typescript
type RoleLabels = {
    [R in Role]:
        string;
};
```

can usually be simplified to:

```typescript
type RoleLabels =
    Record<
        Role,
        string
    >;
```

Use `Record` when you simply need:

```text
Known Keys
→ Same Value Type
```

Use mapped types when you need:

```text
Modifiers

Original T[K] relationships

Key remapping

Filtering

Conditional transformations

Generated property names
```

---

# 72. Common Mistake — Using Mapped Types for Everything

Avoid creating:

```typescript
type UserStrings<T> = {
    [K in keyof T]:
        string;
};
```

if you only need a simple:

```typescript
Record<
    keyof User,
    string
>
```

and that expresses the intent more clearly.

Use the simplest tool that models the requirement.

---

# 73. Common Mistake — Forgetting `T[K]`

Suppose:

```typescript
type Copy<T> = {
    [K in keyof T]:
        string;
};
```

This does **not** copy `T`.

It preserves only the keys.

Every value becomes:

```text
string
```

To preserve the original property types:

```typescript
type Copy<T> = {
    [K in keyof T]:
        T[K];
};
```

---

# 74. Common Mistake — Confusing `K` with `T[K]`

In:

```typescript
[K in keyof T]:
    T[K]
```

`K` represents:

```text
Property name
```

while:

```typescript
T[K]
```

represents:

```text
Property value type
```

Example:

```text
K
→ "age"

T[K]
→ number
```

---

# 75. Common Mistake — Confusing `in` with Runtime Loops

This:

```typescript
[K in keyof T]
```

does not execute a JavaScript loop.

It is entirely part of TypeScript's type system.

No runtime iteration occurs.

---

# 76. Common Mistake — Expecting Runtime Objects

This:

```typescript
type Flags<T> = {
    [K in keyof T]:
        boolean;
};
```

creates a type.

It does not create:

```javascript
{
    property1: false,
    property2: false
}
```

at runtime.

You still need JavaScript code to construct the actual object.

---

# 77. Common Mistake — Assuming Mapped Types Validate Data

Mapped types only provide compile-time checking.

They do not validate:

```text
API responses

JSON

User input

Database records

localStorage

External services
```

at runtime.

---

# 78. Common Mistake — Forgetting Modifiers Can Be Preserved

Homomorphic mapped types such as:

```typescript
type Copy<T> = {
    [K in keyof T]:
        T[K];
};
```

can preserve property modifiers from `T`.

For example:

```typescript
type User = {
    readonly id: number;
    name?: string;
};
```

A direct mapped copy can preserve:

```text
readonly

optional
```

This behavior is important when creating transformations based directly on `keyof T`.

---

# 79. Common Mistake — Complex Recursive Types

Something like:

```typescript
type DeepWhatever<T> = {
    [K in keyof T]:
        T[K] extends object
            ? DeepWhatever<T[K]>
            : T[K];
};
```

looks simple but may behave unexpectedly with:

```text
Functions

Arrays

Dates

Maps

Sets

Promises

Classes
```

Production deep utility types often require more careful conditional logic.

---

# 80. Common Mistake — Overusing Key Remapping

This:

```typescript
type Something<T> = {
    [K in keyof T as
        `someVeryLongGeneratedName${Capitalize<string & K>}`
    ]: ...
};
```

may be clever but harder to maintain.

Use generated keys when the relationship is genuinely systematic.

Do not use them merely to avoid writing a few explicit properties.

---

# 81. Common Mistake — Forgetting `never` Filters Keys

In:

```typescript
[K in keyof T as
    Condition
        ? K
        : never
]
```

properties mapped to:

```text
never
```

are removed.

This is fundamental for property filtering.

---

# 82. Common Mistake — Duplicating Built-In Utilities

You should understand:

```typescript
type MyPartial<T> = {
    [K in keyof T]?:
        T[K];
};
```

for learning purposes.

But in production code, use:

```typescript
Partial<T>
```

instead of maintaining your own equivalent unless you need different behavior.

Same applies to:

```text
Readonly

Required

Pick

Record
```

---

# 83. Interview Question — What Is a Mapped Type?

A mapped type creates a new type by iterating over a union of property keys.

Example:

```typescript
type Copy<T> = {
    [K in keyof T]:
        T[K];
};
```

It allows us to systematically transform properties of another type.

---

# 84. Interview Question — What Does `[K in keyof T]` Mean?

```typescript
keyof T
```

creates a union of keys.

```typescript
K in keyof T
```

maps over each key.

For every key, a property is created in the resulting type.

---

# 85. Interview Question — What Is `T[K]`?

`T[K]` is an indexed access type.

It gets the value type associated with property `K` in `T`.

Example:

```typescript
type User = {
    age: number;
};
```

Then:

```typescript
User["age"]
```

is:

```text
number
```

---

# 86. Interview Question — How Does `Partial<T>` Work?

Conceptually:

```typescript
type MyPartial<T> = {
    [K in keyof T]?:
        T[K];
};
```

It maps over every property and adds:

```text
?
```

making each property optional.

---

# 87. Interview Question — How Does `Readonly<T>` Work?

Conceptually:

```typescript
type MyReadonly<T> = {
    readonly [K in keyof T]:
        T[K];
};
```

It maps over every property and adds:

```text
readonly
```

---

# 88. Interview Question — How Does `Required<T>` Work?

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

removes optional modifiers.

---

# 89. Interview Question — How Do You Remove `readonly`?

Use:

```typescript
-readonly
```

Example:

```typescript
type Mutable<T> = {
    -readonly [K in keyof T]:
        T[K];
};
```

---

# 90. Interview Question — What Is Key Remapping?

Key remapping allows a mapped type to transform property names using:

```typescript
as
```

Example:

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};
```

This converts properties like:

```text
name
```

into:

```text
getName
```

---

# 91. Interview Question — How Do You Filter Keys?

Map unwanted keys to:

```typescript
never
```

Example:

```typescript
type RemoveId<T> = {
    [K in keyof T as
        K extends "id"
            ? never
            : K
    ]:
        T[K];
};
```

The `id` property disappears from the resulting type.

---

# 92. Interview Question — Mapped Type vs Index Signature?

Index signature:

```typescript
[key: string]: Value
```

allows an open-ended set of keys.

Mapped type:

```typescript
[K in SomeUnion]: Value
```

maps over a known key union.

Mapped types can also:

```text
Add/remove modifiers

Transform values

Rename keys

Filter keys
```

---

# 93. Interview Question — Mapped Type vs `Record`?

`Record<K, V>` is a built-in utility for mapping keys `K` to value type `V`.

Example:

```typescript
Record<
    Role,
    boolean
>
```

A custom mapped type is useful when transformations depend on each key or its original property type.

---

# 94. Interview Question — Are Mapped Types Runtime?

No.

Mapped types exist only in TypeScript's type system.

They:

```text
Do not iterate runtime objects

Do not create objects

Do not modify objects

Do not validate external data
```

They disappear when TypeScript is compiled to JavaScript.

---

# 95. Cheat Sheet

```text
MAPPED TYPE
================================

type Mapped<T> = {
    [K in keyof T]:
        T[K];
};


KEYS
================================

keyof T

→ keys of T


ITERATION
================================

K in keyof T

→ map over keys


VALUE TYPE
================================

T[K]

→ type of property K


OPTIONAL
================================

[K in keyof T]?:
    T[K]


ADD OPTIONAL
================================

[K in keyof T]+?:
    T[K]


REMOVE OPTIONAL
================================

[K in keyof T]-?:
    T[K]


READONLY
================================

readonly [K in keyof T]:
    T[K]


ADD READONLY
================================

+readonly [K in keyof T]:
    T[K]


REMOVE READONLY
================================

-readonly [K in keyof T]:
    T[K]


PARTIAL
================================

type MyPartial<T> = {
    [K in keyof T]?:
        T[K];
};


REQUIRED
================================

type MyRequired<T> = {
    [K in keyof T]-?:
        T[K];
};


READONLY
================================

type MyReadonly<T> = {
    readonly [K in keyof T]:
        T[K];
};


MUTABLE
================================

type Mutable<T> = {
    -readonly [K in keyof T]:
        T[K];
};


PICK
================================

type MyPick<
    T,
    K extends keyof T
> = {
    [P in K]:
        T[P];
};


MAP UNION
================================

type Role =
    "admin"
    | "user";

type Permissions = {
    [R in Role]:
        boolean;
};


KEY REMAPPING
================================

[K in keyof T as NewKey]:
    T[K]


FILTER PROPERTY
================================

[K in keyof T as
    Condition
        ? K
        : never
]:
    T[K]


GETTERS
================================

type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};


SETTERS
================================

type Setters<T> = {
    [K in keyof T as
        `set${Capitalize<string & K>}`
    ]:
        (
            value: T[K]
        ) => void;
};


NULLABLE
================================

type Nullable<T> = {
    [K in keyof T]:
        T[K] | null;
};


FORM ERRORS
================================

type FormErrors<T> = {
    [K in keyof T]?:
        string;
};


VALIDATORS
================================

type Validators<T> = {
    [K in keyof T]?:
        (
            value: T[K]
        ) => string | null;
};


DEEP PARTIAL
================================

type DeepPartial<T> = {
    [K in keyof T]?:
        T[K] extends object
            ? DeepPartial<T[K]>
            : T[K];
};


CORE MODEL
================================

Type
 ↓
keyof
 ↓
Keys
 ↓
Mapped Type
 ↓
Transform Each Property
 ↓
New Type
```

---

# 96. Important Rules to Remember

```text
1. Mapped types create object types from key unions.

2. keyof T is commonly used as the source of keys.

3. [K in keyof T] maps over each property.

4. K represents the current property key.

5. T[K] represents the current property's value type.

6. Mapped types are compile-time only.

7. Mapped types do not execute loops.

8. Mapped types do not create runtime objects.

9. Mapped types can preserve existing property types.

10. Mapped types can replace property types.

11. Mapped types can add optional modifiers.

12. ? adds optional behavior.

13. +? explicitly adds optional behavior.

14. -? removes optional behavior.

15. readonly adds readonly behavior.

16. +readonly explicitly adds readonly.

17. -readonly removes readonly.

18. Multiple modifiers can be transformed together.

19. Partial<T> is based on mapped-type concepts.

20. Required<T> is based on mapped-type concepts.

21. Readonly<T> is based on mapped-type concepts.

22. Pick<T, K> is based on mapped-type concepts.

23. Record<K, V> represents a common mapped pattern.

24. Built-in utilities should normally be preferred over recreating equivalents.

25. Recreating utilities is useful for understanding TypeScript.

26. Mapped types can map over keyof T.

27. Mapped types can map over literal unions.

28. Mapped values do not have to use T[K].

29. Every property can be mapped to boolean.

30. Every property can be mapped to string.

31. Every property can be mapped to a function.

32. Every property can be wrapped in Promise.

33. Every property can be wrapped in another object type.

34. Mapped types can derive form error models.

35. Mapped types can derive touched state.

36. Mapped types can derive validators.

37. Mapped types can derive API state.

38. Mapped types can derive permissions.

39. Mapped types can derive feature configuration.

40. Mapped types can be nested.

41. Nested mapped types can model matrices of known keys.

42. Key remapping uses as.

43. Key remapping can rename properties.

44. Template literal types can generate property names.

45. Capitalize can transform generated names.

46. string & K can restrict keys to string-compatible keys.

47. Keys can be prefixed.

48. Keys can be suffixed.

49. Getter names can be generated.

50. Setter names can be generated.

51. Event handler names can be generated.

52. Change handler names can be generated.

53. Properties can be filtered.

54. Filtering is commonly done by mapping keys to never.

55. never removes the mapped property.

56. Property filtering can depend on the key.

57. Property filtering can depend on T[K].

58. String-valued properties can be selected.

59. Number-valued properties can be selected.

60. Boolean-valued properties can be selected.

61. Filtering often uses conditional types.

62. Conditional types will be covered separately.

63. Mapped types can be recursive.

64. Recursive mapped types can create deep transformations.

65. DeepPartial is a common example.

66. DeepReadonly is another common example.

67. Simplified deep utilities may mishandle special object types.

68. Arrays require consideration in recursive transformations.

69. Functions are objects for many type checks.

70. Date is an object.

71. Map and Set are objects.

72. Production deep utilities require careful design.

73. Mapped types can work with arrays.

74. Mapped types can work with tuples.

75. TypeScript often preserves tuple relationships through mapped transformations.

76. Mapped types differ from index signatures.

77. Index signatures allow open-ended keys.

78. Mapped types commonly work with known key unions.

79. Record is useful when all known keys share one value type.

80. Custom mapped types are useful when values depend on T[K].

81. Custom mapped types are useful when modifiers must change.

82. Custom mapped types are useful when keys must be renamed.

83. Custom mapped types are useful when keys must be filtered.

84. K is a key, not the value type.

85. T[K] is the value type.

86. Forgetting T[K] can destroy the relationship with the original type.

87. Homomorphic mapped types can preserve modifiers.

88. Deriving related types reduces duplication.

89. Deriving types keeps relationships synchronized.

90. Deriving types also creates coupling.

91. That coupling should be intentional.

92. Avoid mapped types when a simple explicit type is clearer.

93. Avoid mapped types when Record communicates the requirement better.

94. Avoid overly complex generated property names.

95. Avoid clever types that reduce readability.

96. Mapped types are especially powerful with generics.

97. Mapped types are especially powerful with keyof.

98. Mapped types are especially powerful with indexed access.

99. Mapped types combine with template literal types.

100. Mapped types combine with conditional types.

101. Mapped types combine with utility types.

102. Many advanced TypeScript utilities are combinations of these features.

103. Mapped types do not validate runtime data.

104. Mapped types do not sanitize runtime data.

105. Mapped types do not modify runtime values.

106. External data still requires runtime validation.

107. Type transformations disappear after compilation.

108. Use mapped types when properties follow a systematic transformation.

109. Prefer explicit types when properties have unrelated semantics.

110. Core syntax to remember:

[K in keyof T]: T[K]
```

---

# 97. Final Mental Model

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    active: boolean;
};
```

First:

```typescript
keyof User
```

produces:

```text
"id"
|
"name"
|
"active"
```

Then:

```typescript
type Copy<T> = {
    [K in keyof T]:
        T[K];
};
```

does:

```text
User
 │
 ▼
keyof User
 │
 ▼

"id" | "name" | "active"

 │
 ▼
Map Each Key
 │
 ├── "id"
 │      ↓
 │   User["id"]
 │      ↓
 │    number
 │
 ├── "name"
 │      ↓
 │   User["name"]
 │      ↓
 │    string
 │
 └── "active"
        ↓
     User["active"]
        ↓
      boolean
```

Result:

```typescript
{
    id: number;
    name: string;
    active: boolean;
}
```

But we can transform the properties:

```typescript
type Optional<T> = {
    [K in keyof T]?:
        T[K];
};
```

Result:

```typescript
{
    id?: number;
    name?: string;
    active?: boolean;
}
```

Or transform values:

```typescript
type Flags<T> = {
    [K in keyof T]:
        boolean;
};
```

Result:

```typescript
{
    id: boolean;
    name: boolean;
    active: boolean;
}
```

Or transform keys:

```typescript
type Getters<T> = {
    [K in keyof T as
        `get${Capitalize<string & K>}`
    ]:
        () => T[K];
};
```

Result:

```typescript
{
    getId:
        () => number;

    getName:
        () => string;

    getActive:
        () => boolean;
}
```

Or remove keys:

```typescript
type WithoutId<T> = {
    [K in keyof T as
        K extends "id"
            ? never
            : K
    ]:
        T[K];
};
```

Result:

```typescript
{
    name: string;
    active: boolean;
}
```

So the complete mental model is:

```text
             Existing Type
                   │
                   ▼
                keyof
                   │
                   ▼
               Key Union
                   │
                   ▼
            Mapped Type
                   │
         [K in keyof T]
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼

      Modify    Modify    Modify
       Keys     Values   Modifiers

          │        │        │
          └────────┼────────┘
                   ▼
                New Type
```

The syntax worth remembering is:

```typescript
type Transform<T> = {
    [K in keyof T]:
        T[K];
};
```

From there we can change:

```text
K
→ property name

T[K]
→ property value type

?
→ optional

-?
→ required

readonly
→ readonly

-readonly
→ mutable

as
→ rename/filter keys

never
→ remove keys
```

That is the foundation of mapped types.
