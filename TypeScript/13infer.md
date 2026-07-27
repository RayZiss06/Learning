# TypeScript — `infer` Keyword

The `infer` keyword allows TypeScript to **extract a type from another type while evaluating a conditional type**.

Instead of already knowing the inner type, we tell TypeScript:

> If this type matches the structure, figure out what this part is.

The basic pattern is:

```typescript
type ExtractSomething<T> =
    T extends SomeStructure<infer U>
        ? U
        : never;
```

Here:

```typescript
infer U
```

means:

```text
Determine the type in this position
and temporarily call it U.
```

---

# 1. Why `infer` Exists

Suppose we have:

```typescript
type Users = User[];
```

and we want:

```typescript
User
```

We could access an array element type with:

```typescript
type Element =
    Users[number];
```

But what if we want a **generic utility** that works for any array?

```typescript
type ElementType<T> =
    T extends Array<infer U>
        ? U
        : never;
```

Now:

```typescript
type A =
    ElementType<string[]>;
// string

type B =
    ElementType<number[]>;
// number

type C =
    ElementType<User[]>;
// User
```

`infer` lets TypeScript discover the inner type for us.

---

# 2. Basic Mental Model

Consider:

```typescript
type ElementType<T> =
    T extends Array<infer U>
        ? U
        : never;
```

Now:

```typescript
type Result =
    ElementType<string[]>;
```

TypeScript conceptually performs:

```text
T = string[]

        ↓

Does string[] match:

Array<infer U>

        ↓

Yes

        ↓

string[] is Array<string>

        ↓

U = string

        ↓

Return U

        ↓

string
```

---

# 3. `infer` Works with Conditional Types

`infer` is introduced within the `extends` portion of a conditional type.

```typescript
T extends Something<infer U>
    ? U
    : never;
```

You cannot generally write:

```typescript
type Test =
    infer U;
```

The inferred type variable is created as part of conditional type matching.

---

# 4. Extracting Array Element Types

One of the simplest examples:

```typescript
type ArrayElement<T> =
    T extends Array<infer U>
        ? U
        : never;
```

Examples:

```typescript
type A =
    ArrayElement<string[]>;
// string

type B =
    ArrayElement<number[]>;
// number

type C =
    ArrayElement<boolean[]>;
// boolean
```

---

# 5. Generic Array Syntax

These represent the same general array concept:

```typescript
string[]
```

and:

```typescript
Array<string>
```

Therefore:

```typescript
type ArrayElement<T> =
    T extends Array<infer U>
        ? U
        : never;
```

can extract:

```text
Array<string>
      │
      ▼
   infer U
      │
      ▼
   U = string
```

---

# 6. Supporting Readonly Arrays

This:

```typescript
type ArrayElement<T> =
    T extends Array<infer U>
        ? U
        : never;
```

does not cover every readonly array use case.

A broader version is:

```typescript
type ArrayElement<T> =
    T extends readonly (infer U)[]
        ? U
        : never;
```

Now:

```typescript
type A =
    ArrayElement<string[]>;
// string
```

and:

```typescript
type B =
    ArrayElement<
        readonly number[]
    >;
// number
```

---

# 7. Extracting Promise Values

Suppose:

```typescript
type UserPromise =
    Promise<User>;
```

We want:

```typescript
User
```

Use:

```typescript
type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : never;
```

Then:

```typescript
type Result =
    PromiseValue<
        Promise<User>
    >;
```

Result:

```typescript
User
```

---

# 8. Promise Mental Model

```text
Promise<User>

      ↓

Does it match:

Promise<infer U>

      ↓

Yes

      ↓

U = User

      ↓

Return User
```

---

# 9. Returning Original Type When No Match

Sometimes we do not want:

```typescript
never
```

when the type is not a Promise.

We can write:

```typescript
type UnwrapPromise<T> =
    T extends Promise<infer U>
        ? U
        : T;
```

Then:

```typescript
type A =
    UnwrapPromise<
        Promise<string>
    >;
// string

type B =
    UnwrapPromise<number>;
// number
```

---

# 10. Recursive Promise Extraction

The previous utility unwraps one level:

```typescript
type UnwrapPromise<T> =
    T extends Promise<infer U>
        ? U
        : T;
```

A recursive version is:

```typescript
type DeepUnwrapPromise<T> =
    T extends Promise<infer U>
        ? DeepUnwrapPromise<U>
        : T;
```

Conceptually:

```text
Promise<Promise<User>>

        ↓

Promise<User>

        ↓

User
```

For standard asynchronous type unwrapping, TypeScript already provides:

```typescript
Awaited<T>
```

---

# 11. Extracting Function Return Types

Suppose:

```typescript
function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

We can extract its return type:

```typescript
type FunctionReturn<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Then:

```typescript
type User =
    FunctionReturn<
        typeof getUser
    >;
```

Result:

```typescript
{
    id: number;
    name: string;
}
```

---

# 12. Function Return Mental Model

Suppose:

```typescript
type Fn =
    (
        id: number
    ) => string;
```

Matching:

```typescript
(...args: any[]) => infer R
```

TypeScript sees:

```text
(id: number) => string

              ↓

(...args: any[]) => infer R

              ↓

R = string
```

So:

```typescript
type Result =
    FunctionReturn<Fn>;
```

becomes:

```typescript
string
```

---

# 13. Built-In `ReturnType<T>`

TypeScript already provides:

```typescript
ReturnType<T>
```

Example:

```typescript
function createUser() {
    return {
        id: 1,
        name: "Alice"
    };
}

type User =
    ReturnType<
        typeof createUser
    >;
```

Use the built-in utility in application code when it already expresses the requirement.

Custom implementations are useful for understanding `infer`.

---

# 14. Extracting Function Parameters

Consider:

```typescript
function createUser(
    name: string,
    age: number,
    active: boolean
) {}
```

We can extract all parameters:

```typescript
type FunctionParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Then:

```typescript
type Params =
    FunctionParameters<
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

# 15. Why Parameters Become a Tuple

Function parameters:

```typescript
(
    name: string,
    age: number,
    active: boolean
)
```

can be represented as:

```typescript
[
    name: string,
    age: number,
    active: boolean
]
```

Therefore:

```typescript
...args: infer P
```

infers the entire parameter list as a tuple.

---

# 16. Built-In `Parameters<T>`

TypeScript provides:

```typescript
Parameters<T>
```

Example:

```typescript
type Params =
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

# 17. Extract First Function Parameter

Suppose:

```typescript
type Fn =
    (
        name: string,
        age: number
    ) => void;
```

We can extract the first parameter:

```typescript
type FirstParameter<T> =
    T extends (
        first: infer F,
        ...args: any[]
    ) => any
        ? F
        : never;
```

Then:

```typescript
type Result =
    FirstParameter<Fn>;
```

Result:

```typescript
string
```

---

# 18. Extract Remaining Function Parameters

```typescript
type RestParameters<T> =
    T extends (
        first: any,
        ...rest: infer R
    ) => any
        ? R
        : never;
```

Given:

```typescript
type Fn =
    (
        name: string,
        age: number,
        active: boolean
    ) => void;
```

Result:

```typescript
type Rest =
    RestParameters<Fn>;
```

becomes:

```typescript
[
    age: number,
    active: boolean
]
```

---

# 19. Extracting Object Property Types

Suppose:

```typescript
type ApiResponse = {
    success: boolean;
    data: User[];
};
```

We can extract `data`:

```typescript
type DataType<T> =
    T extends {
        data: infer D;
    }
        ? D
        : never;
```

Then:

```typescript
type Data =
    DataType<ApiResponse>;
```

Result:

```typescript
User[]
```

---

# 20. Property Extraction Mental Model

```text
{
    success: boolean;
    data: User[];
}

          ↓

Does it match:

{
    data: infer D;
}

          ↓

Yes

          ↓

D = User[]

          ↓

Return User[]
```

Extra properties do not prevent structural matching.

---

# 21. Extract Multiple Object Properties

`infer` can appear multiple times.

```typescript
type ResponseParts<T> =
    T extends {
        data: infer D;
        error: infer E;
    }
        ? [D, E]
        : never;
```

Given:

```typescript
type Response = {
    data: User[];
    error: string | null;
};
```

Then:

```typescript
type Parts =
    ResponseParts<Response>;
```

Result:

```typescript
[
    User[],
    string | null
]
```

---

# 22. Extracting Generic Type Arguments

Suppose:

```typescript
type ApiResponse<T> = {
    data: T;
    success: boolean;
};
```

We can extract `T`:

```typescript
type ResponseData<T> =
    T extends ApiResponse<infer D>
        ? D
        : never;
```

Then:

```typescript
type Result =
    ResponseData<
        ApiResponse<User[]>
    >;
```

Result:

```typescript
User[]
```

This pattern works with many generic type structures.

---

# 23. Extracting Map Key and Value Types

Suppose:

```typescript
type UserMap =
    Map<string, User>;
```

We can extract both generic parameters:

```typescript
type MapParts<T> =
    T extends Map<
        infer K,
        infer V
    >
        ? [K, V]
        : never;
```

Then:

```typescript
type Result =
    MapParts<UserMap>;
```

Result:

```typescript
[string, User]
```

---

# 24. Extract Map Key Type

```typescript
type MapKey<T> =
    T extends Map<
        infer K,
        any
    >
        ? K
        : never;
```

Example:

```typescript
type Key =
    MapKey<
        Map<number, User>
    >;
```

Result:

```typescript
number
```

---

# 25. Extract Map Value Type

```typescript
type MapValue<T> =
    T extends Map<
        any,
        infer V
    >
        ? V
        : never;
```

Example:

```typescript
type Value =
    MapValue<
        Map<string, User>
    >;
```

Result:

```typescript
User
```

---

# 26. Extract Set Value Type

```typescript
type SetValue<T> =
    T extends Set<infer V>
        ? V
        : never;
```

Example:

```typescript
type Result =
    SetValue<
        Set<string>
    >;
```

Result:

```typescript
string
```

---

# 27. Tuple Inference

`infer` is especially powerful with tuples.

Suppose:

```typescript
type Values = [
    string,
    number,
    boolean
];
```

We can decompose this structure.

---

# 28. Extract First Tuple Element

```typescript
type First<T> =
    T extends readonly [
        infer F,
        ...unknown[]
    ]
        ? F
        : never;
```

Then:

```typescript
type Result =
    First<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
string
```

---

# 29. Extract Last Tuple Element

```typescript
type Last<T> =
    T extends readonly [
        ...unknown[],
        infer L
    ]
        ? L
        : never;
```

Example:

```typescript
type Result =
    Last<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
boolean
```

---

# 30. Extract Tuple Tail

The tail means:

```text
Everything except the first element.
```

Use:

```typescript
type Tail<T> =
    T extends readonly [
        unknown,
        ...infer R
    ]
        ? R
        : [];
```

Example:

```typescript
type Result =
    Tail<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
[
    number,
    boolean
]
```

---

# 31. Extract Tuple Init

The opposite is:

```text
Everything except the last element.
```

```typescript
type Init<T> =
    T extends readonly [
        ...infer R,
        unknown
    ]
        ? R
        : [];
```

Example:

```typescript
type Result =
    Init<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
[
    string,
    number
]
```

---

# 32. Extract First and Rest

We can infer multiple tuple parts:

```typescript
type Split<T> =
    T extends readonly [
        infer First,
        ...infer Rest
    ]
        ? [First, Rest]
        : never;
```

Example:

```typescript
type Result =
    Split<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
[
    string,
    [
        number,
        boolean
    ]
]
```

---

# 33. Extract First and Last

```typescript
type Ends<T> =
    T extends readonly [
        infer F,
        ...unknown[],
        infer L
    ]
        ? [F, L]
        : never;
```

Example:

```typescript
type Result =
    Ends<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
[
    string,
    boolean
]
```

---

# 34. Variadic Tuple Inference

The syntax:

```typescript
...infer R
```

allows TypeScript to infer:

```text
zero or more tuple elements
```

Example:

```typescript
type RemoveFirst<T> =
    T extends readonly [
        unknown,
        ...infer R
    ]
        ? R
        : [];
```

This is called **variadic tuple inference**.

It is useful for:

```text
Function parameters

Tuple manipulation

Generic APIs

Middleware pipelines

Event systems

Library utilities
```

---

# 35. Constructor Parameter Extraction

`infer` can inspect constructors.

Suppose:

```typescript
class User {
    constructor(
        public name: string,
        public age: number
    ) {}
}
```

We can extract constructor arguments:

```typescript
type ConstructorParams<T> =
    T extends new (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Then:

```typescript
type Params =
    ConstructorParams<
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

# 36. Built-In `ConstructorParameters<T>`

TypeScript provides:

```typescript
ConstructorParameters<T>
```

So:

```typescript
type Params =
    ConstructorParameters<
        typeof User
    >;
```

extracts the constructor parameters.

---

# 37. Extract Instance Type

Given:

```typescript
class User {
    constructor(
        public name: string
    ) {}
}
```

we can create:

```typescript
type Instance<T> =
    T extends new (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Then:

```typescript
type UserInstance =
    Instance<typeof User>;
```

Result:

```typescript
User
```

---

# 38. Built-In `InstanceType<T>`

TypeScript already provides:

```typescript
InstanceType<T>
```

Example:

```typescript
type UserInstance =
    InstanceType<
        typeof User
    >;
```

This extracts the instance produced by a constructor type.

---

# 39. `typeof User` vs `User`

This distinction matters.

Given:

```typescript
class User {}
```

`User` used as a type refers to:

```text
Instance side
```

while:

```typescript
typeof User
```

refers to:

```text
Constructor / static side
```

So:

```typescript
InstanceType<typeof User>
```

means:

```text
Take the User constructor type
and determine the instance
it creates.
```

---

# 40. Extracting Async Function Results

Suppose:

```typescript
async function getUsers() {
    return [
        {
            id: 1,
            name: "Alice"
        }
    ];
}
```

First:

```typescript
ReturnType<
    typeof getUsers
>
```

is approximately:

```typescript
Promise<
    {
        id: number;
        name: string;
    }[]
>
```

Then:

```typescript
Awaited<
    ReturnType<
        typeof getUsers
    >
>
```

produces:

```typescript
{
    id: number;
    name: string;
}[]
```

This is a very useful real-world pattern.

---

# 41. Async Result Utility

We can define:

```typescript
type AsyncResult<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? Awaited<R>
        : never;
```

Then:

```typescript
type Users =
    AsyncResult<
        typeof getUsers
    >;
```

extracts the resolved result.

---

# 42. Extract Array Element from Async Function

We can combine utilities.

```typescript
type AsyncArrayElement<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? Awaited<R> extends
            readonly (infer U)[]
                ? U
                : never
        : never;
```

If:

```typescript
async function getUsers() {
    return [
        {
            id: 1,
            name: "Alice"
        }
    ];
}
```

then:

```typescript
type User =
    AsyncArrayElement<
        typeof getUsers
    >;
```

becomes:

```typescript
{
    id: number;
    name: string;
}
```

---

# 43. Prefer Composition When Possible

Instead of writing a complex conditional:

```typescript
type AsyncArrayElement<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? Awaited<R> extends
            readonly (infer U)[]
                ? U
                : never
        : never;
```

we could compose existing utilities:

```typescript
type ArrayElement<T> =
    T extends readonly (infer U)[]
        ? U
        : never;

type User =
    ArrayElement<
        Awaited<
            ReturnType<
                typeof getUsers
            >
        >
    >;
```

This is often easier to read.

---

# 44. `infer` with Union Types

Conditional types may distribute over unions.

```typescript
type ElementType<T> =
    T extends readonly (infer U)[]
        ? U
        : never;
```

Then:

```typescript
type Result =
    ElementType<
        string[]
        | number[]
    >;
```

becomes:

```typescript
string | number
```

because TypeScript evaluates:

```text
string[]
→ string

number[]
→ number
```

and combines the results:

```text
string | number
```

---

# 45. Filtering Using `infer`

Consider:

```typescript
type ArrayElements<T> =
    T extends readonly (infer U)[]
        ? U
        : never;
```

Then:

```typescript
type Result =
    ArrayElements<
        string[]
        | number
        | boolean[]
    >;
```

TypeScript processes:

```text
string[]
→ string

number
→ never

boolean[]
→ boolean
```

Result:

```typescript
string | boolean
```

So inference and filtering can happen together.

---

# 46. `infer` with Mapped Types

Suppose:

```typescript
type Services = {
    getUser:
        () => Promise<User>;

    getProducts:
        () => Promise<Product[]>;

    logout:
        () => void;
};
```

We can extract method return types:

```typescript
type ServiceReturns<T> = {
    [K in keyof T]:
        T[K] extends (
            ...args: any[]
        ) => infer R
            ? R
            : never;
};
```

Result:

```typescript
{
    getUser:
        Promise<User>;

    getProducts:
        Promise<Product[]>;

    logout:
        void;
}
```

---

# 47. Resolve Service Return Types

We can add:

```typescript
Awaited
```

```typescript
type ServiceResults<T> = {
    [K in keyof T]:
        T[K] extends (
            ...args: any[]
        ) => infer R
            ? Awaited<R>
            : never;
};
```

Result:

```typescript
{
    getUser: User;
    getProducts: Product[];
    logout: void;
}
```

This demonstrates:

```text
Mapped Types
+
Conditional Types
+
infer
+
Awaited
```

---

# 48. Extract Function Properties

Suppose:

```typescript
type UserService = {
    name: string;

    getUser:
        (id: number) => User;

    deleteUser:
        (id: number) => void;

    version: number;
};
```

We can keep only methods:

```typescript
type MethodsOnly<T> = {
    [K in keyof T as
        T[K] extends (
            ...args: any[]
        ) => any
            ? K
            : never
    ]:
        T[K];
};
```

Result:

```typescript
{
    getUser:
        (id: number) => User;

    deleteUser:
        (id: number) => void;
}
```

`infer` is not required here because we only need to test whether the property is a function.

Use `infer` when you need to **extract something from the matching function**.

---

# 49. Extract Return Types of Only Methods

```typescript
type MethodResults<T> = {
    [K in keyof T as
        T[K] extends (
            ...args: any[]
        ) => any
            ? K
            : never
    ]:
        T[K] extends (
            ...args: any[]
        ) => infer R
            ? R
            : never;
};
```

Result:

```typescript
{
    getUser: User;
    deleteUser: void;
}
```

---

# 50. Extract Function Parameter Types from Object

```typescript
type MethodParameters<T> = {
    [K in keyof T as
        T[K] extends (
            ...args: any[]
        ) => any
            ? K
            : never
    ]:
        T[K] extends (
            ...args: infer P
        ) => any
            ? P
            : never;
};
```

For:

```typescript
type Service = {
    getUser:
        (id: number) => User;

    search:
        (
            query: string,
            limit: number
        ) => User[];
};
```

Result:

```typescript
{
    getUser:
        [id: number];

    search:
        [
            query: string,
            limit: number
        ];
}
```

---

# 51. Inferring Nested Structures

`infer` can match deeply nested types.

```typescript
type NestedArrayValue<T> =
    T extends Promise<
        Array<infer U>
    >
        ? U
        : never;
```

Then:

```typescript
type Result =
    NestedArrayValue<
        Promise<User[]>
    >;
```

Result:

```typescript
User
```

The pattern says:

```text
T must be:

Promise<
    Array<
        something
    >
>

Extract that something.
```

---

# 52. Multiple Nested Inferences

```typescript
type ApiParts<T> =
    T extends Promise<
        ApiResponse<infer D>
    >
        ? D
        : never;
```

If:

```typescript
type Result =
    ApiParts<
        Promise<
            ApiResponse<User[]>
        >
    >;
```

then:

```typescript
Result
```

is:

```typescript
User[]
```

---

# 53. Recursive Array Flattening

```typescript
type Flatten<T> =
    T extends readonly (infer U)[]
        ? Flatten<U>
        : T;
```

Examples:

```typescript
type A =
    Flatten<string>;
// string

type B =
    Flatten<string[]>;
// string

type C =
    Flatten<string[][]>;
// string

type D =
    Flatten<string[][][]>;
// string
```

---

# 54. Flatten Mental Model

For:

```typescript
Flatten<number[][][]>
```

TypeScript evaluates:

```text
number[][][]
     │
     ▼
infer U = number[][]
     │
     ▼
Flatten<number[][]>
     │
     ▼
infer U = number[]
     │
     ▼
Flatten<number[]>
     │
     ▼
infer U = number
     │
     ▼
Flatten<number>
     │
     ▼
number
```

---

# 55. Recursive Tuple Processing

We can recursively process tuples.

Example:

```typescript
type TupleToUnion<T> =
    T extends readonly [
        infer F,
        ...infer R
    ]
        ? F | TupleToUnion<R>
        : never;
```

Then:

```typescript
type Result =
    TupleToUnion<
        [
            string,
            number,
            boolean
        ]
    >;
```

Result:

```typescript
string | number | boolean
```

---

# 56. But Indexed Access May Be Simpler

For tuple-to-union:

```typescript
type Values = [
    string,
    number,
    boolean
];
```

you can simply write:

```typescript
type Result =
    Values[number];
```

Result:

```typescript
string | number | boolean
```

Do not use recursive `infer` when a simpler TypeScript feature solves the problem.

---

# 57. Template Literal Inference

`infer` can also extract parts of template literal types.

Suppose:

```typescript
type Route =
    "users/:id";
```

We can write:

```typescript
type Parameter<T> =
    T extends `${string}:${infer P}`
        ? P
        : never;
```

Then:

```typescript
type Result =
    Parameter<
        "users/:id"
    >;
```

Result:

```typescript
"id"
```

This combines:

```text
Template Literal Types
+
Conditional Types
+
infer
```

We will cover template literal types separately.

---

# 58. Extract File Extension

```typescript
type Extension<T> =
    T extends `${string}.${infer E}`
        ? E
        : never;
```

Example:

```typescript
type A =
    Extension<
        "image.png"
    >;
```

Result:

```typescript
"png"
```

```typescript
type B =
    Extension<
        "document.pdf"
    >;
```

Result:

```typescript
"pdf"
```

For strings with multiple dots, a simple pattern like this may not represent the exact runtime semantics you want, so treat it as a type-pattern example.

---

# 59. Extract Prefix and Suffix

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

# 60. Extract React-Like Component Props

Suppose:

```typescript
type Component<P> =
    (
        props: P
    ) => unknown;
```

Then:

```typescript
type ComponentProps<T> =
    T extends (
        props: infer P
    ) => unknown
        ? P
        : never;
```

Example:

```typescript
type ButtonProps = {
    label: string;
    disabled?: boolean;
};

type Button =
    Component<ButtonProps>;
```

Then:

```typescript
type Props =
    ComponentProps<Button>;
```

Result:

```typescript
ButtonProps
```

React itself provides utilities for several component-related extraction patterns, but understanding the underlying idea is useful.

---

# 61. Extract Event Handler Parameter

Suppose:

```typescript
type ClickHandler =
    (
        event: MouseEvent
    ) => void;
```

We can write:

```typescript
type EventOf<T> =
    T extends (
        event: infer E
    ) => any
        ? E
        : never;
```

Then:

```typescript
type Event =
    EventOf<ClickHandler>;
```

Result:

```typescript
MouseEvent
```

---

# 62. Extract Callback Result

Suppose:

```typescript
type Callback =
    (
        data: User[]
    ) => boolean;
```

Then:

```typescript
type CallbackResult<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Result:

```typescript
boolean
```

Again, this is equivalent to a common use of:

```typescript
ReturnType<T>
```

---

# 63. Extract Callback Input

```typescript
type CallbackInput<T> =
    T extends (
        value: infer V,
        ...args: any[]
    ) => any
        ? V
        : never;
```

Then:

```typescript
type Input =
    CallbackInput<
        (
            user: User
        ) => void
    >;
```

Result:

```typescript
User
```

---

# 64. Extract API Data from Function

Suppose:

```typescript
async function fetchUsers():
    Promise<User[]> {
    return [];
}
```

We want:

```typescript
User[]
```

Use:

```typescript
type FunctionData<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? Awaited<R>
        : never;
```

Then:

```typescript
type Users =
    FunctionData<
        typeof fetchUsers
    >;
```

Result:

```typescript
User[]
```

---

# 65. Extract Individual API Entity

From:

```typescript
async function fetchUsers():
    Promise<User[]> {
    return [];
}
```

we can derive:

```typescript
type Users =
    Awaited<
        ReturnType<
            typeof fetchUsers
        >
    >;
```

Then:

```typescript
type UserItem =
    Users[number];
```

This is usually clearer than building a complicated custom utility.

---

# 66. Real-World Service Example

Suppose:

```typescript
const userService = {
    getAll: async () => {
        return [] as User[];
    },

    getById: async (
        id: number
    ) => {
        return {} as User;
    },

    create: async (
        input: CreateUser
    ) => {
        return {} as User;
    }
};
```

We can derive:

```typescript
type GetAllResult =
    Awaited<
        ReturnType<
            typeof userService.getAll
        >
    >;
```

Result:

```typescript
User[]
```

This prevents manually duplicating:

```typescript
User[]
```

when the function already defines the relationship.

---

# 67. Real-World API Response Example

Suppose:

```typescript
type ApiResponse<T> = {
    success: boolean;
    data: T;
    message: string;
};
```

and:

```typescript
type UsersResponse =
    ApiResponse<User[]>;
```

Extract:

```typescript
type ResponseData<T> =
    T extends ApiResponse<infer D>
        ? D
        : never;
```

Then:

```typescript
type Users =
    ResponseData<
        UsersResponse
    >;
```

Result:

```typescript
User[]
```

---

# 68. Real-World Repository Pattern

Suppose:

```typescript
interface Repository<T> {
    findAll():
        Promise<T[]>;

    findById(
        id: string
    ):
        Promise<T | null>;
}
```

We can extract the repository entity:

```typescript
type RepositoryEntity<T> =
    T extends Repository<infer E>
        ? E
        : never;
```

Given:

```typescript
type UserRepository =
    Repository<User>;
```

Then:

```typescript
type Entity =
    RepositoryEntity<
        UserRepository
    >;
```

Result:

```typescript
User
```

---

# 69. Real-World Event System

Suppose:

```typescript
type EventHandler<T> =
    (
        event: T
    ) => void;
```

Then:

```typescript
type EventType<T> =
    T extends EventHandler<infer E>
        ? E
        : never;
```

Given:

```typescript
type UserCreatedHandler =
    EventHandler<{
        userId: string;
        timestamp: number;
    }>;
```

Then:

```typescript
type Event =
    EventType<
        UserCreatedHandler
    >;
```

Result:

```typescript
{
    userId: string;
    timestamp: number;
}
```

---

# 70. Real-World State Wrapper

Suppose:

```typescript
type State<T> = {
    value: T;
    loading: boolean;
};
```

We can extract:

```typescript
type StateValue<T> =
    T extends State<infer V>
        ? V
        : never;
```

Then:

```typescript
type UserState =
    State<User>;
```

```typescript
type Value =
    StateValue<UserState>;
```

Result:

```typescript
User
```

---

# 71. Common Mistake — Thinking `infer` Is Runtime

This:

```typescript
infer U
```

does not create a JavaScript variable.

It exists only during TypeScript type checking.

There is no generated runtime equivalent.

---

# 72. Common Mistake — Using `infer` Outside Its Context

You cannot use `infer` as a general variable declaration.

Wrong conceptually:

```typescript
type Result =
    infer U;
```

Use it as part of conditional matching:

```typescript
type Result<T> =
    T extends Array<infer U>
        ? U
        : never;
```

---

# 73. Common Mistake — Using `infer` When Indexed Access Is Simpler

Given:

```typescript
type User = {
    id: number;
    name: string;
};
```

Do not write:

```typescript
type Name<T> =
    T extends {
        name: infer N;
    }
        ? N
        : never;
```

if all you need for a known type is:

```typescript
type Name =
    User["name"];
```

Result:

```typescript
string
```

Use `infer` when the **structure itself needs to be generically matched and decomposed**.

---

# 74. Common Mistake — Recreating `ReturnType`

Instead of:

```typescript
type FunctionReturn<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

normally use:

```typescript
ReturnType<T>
```

unless you need custom behavior.

---

# 75. Common Mistake — Recreating `Parameters`

Instead of:

```typescript
type FunctionParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;
```

normally use:

```typescript
Parameters<T>
```

---

# 76. Common Mistake — Recreating `Awaited`

Instead of maintaining a custom recursive Promise utility:

```typescript
type Unwrap<T> =
    T extends Promise<infer U>
        ? Unwrap<U>
        : T;
```

prefer:

```typescript
Awaited<T>
```

for standard asynchronous unwrapping behavior.

---

# 77. Common Mistake — Overusing `any`

Examples often use:

```typescript
(...args: any[]) => any
```

for readability.

When building reusable utilities, consider whether:

```typescript
unknown
```

or a more specific constraint can represent the requirement more safely.

However, function type constraints can have variance considerations, so use the pattern appropriate to the utility.

---

# 78. Common Mistake — Forgetting Union Distribution

```typescript
type Element<T> =
    T extends readonly (infer U)[]
        ? U
        : never;
```

Given:

```typescript
string[]
|
number[]
```

produces:

```typescript
string | number
```

because the conditional type distributes over the union.

This behavior comes from conditional types, not `infer` itself.

---

# 79. Common Mistake — Overcomplicated Type Extraction

If you have:

```typescript
type User = {
    profile: {
        address: {
            city: string;
        };
    };
};
```

and simply want the city type:

```typescript
type City =
    User[
        "profile"
    ][
        "address"
    ][
        "city"
    ];
```

is clearer than constructing a nested `infer` utility unless you genuinely need a reusable structural matcher.

---

# 80. Common Mistake — Recursive Inference Without Need

This:

```typescript
type DeepSomething<T> =
    T extends ...
        ? DeepSomething<...>
        : ...;
```

can become difficult to understand and may hit compiler recursion limits.

Prefer the simplest type transformation that satisfies the requirement.

---

# 81. Common Mistake — Assuming Extraction Changes Data

```typescript
type Result =
    Awaited<
        ReturnType<
            typeof fetchUsers
        >
    >;
```

only derives a TypeScript type.

It does not:

```text
Call fetchUsers

Await the function

Fetch API data

Validate data

Transform runtime values
```

It is compile-time only.

---

# 82. Common Mistake — Creating Duplicate Source of Truth

Suppose:

```typescript
async function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Instead of manually writing a duplicate type when appropriate:

```typescript
type User = {
    id: number;
    name: string;
};
```

you could derive:

```typescript
type User =
    Awaited<
        ReturnType<
            typeof getUser
        >
    >;
```

But deriving types from implementations is not always best.

Sometimes the domain type should be the source of truth and the function should explicitly return:

```typescript
Promise<User>
```

Choose the direction of dependency intentionally.

---

# 83. When Should You Use `infer`?

Use `infer` when you need to generically extract types from structures such as:

```text
Array<T>

Promise<T>

Functions

Constructors

Tuples

Generic wrappers

Object structures

Template literal types

Event handlers

Service methods
```

Typical question:

```text
"I have this type.

What type is inside it?"
```

That is where `infer` is useful.

---

# 84. When Should You NOT Use `infer`?

Do not use `infer` if simpler tools already solve the problem.

Examples:

```typescript
User["name"]
```

instead of structural inference.

```typescript
Array[number]
```

instead of recursive tuple logic.

```typescript
ReturnType<T>
```

instead of custom return extraction.

```typescript
Parameters<T>
```

instead of custom parameter extraction.

```typescript
Awaited<T>
```

instead of custom Promise unwrapping.

The goal is not to use `infer` everywhere.

The goal is to understand how TypeScript can extract types when needed.

---

# 85. Interview Question — What Is `infer`?

`infer` allows TypeScript to introduce and infer a type variable while matching a type inside a conditional type.

Example:

```typescript
type Element<T> =
    T extends Array<infer U>
        ? U
        : never;
```

For:

```typescript
Element<string[]>
```

TypeScript infers:

```text
U = string
```

---

# 86. Interview Question — Where Can `infer` Be Used?

Most commonly, `infer` is introduced in the `extends` clause of a conditional type.

Example:

```typescript
T extends Promise<infer U>
    ? U
    : never;
```

It allows the matching structure to introduce a new inferred type variable.

---

# 87. Interview Question — How Do You Extract an Array Element Type?

```typescript
type Element<T> =
    T extends readonly (infer U)[]
        ? U
        : never;
```

Example:

```typescript
Element<User[]>
```

Result:

```typescript
User
```

---

# 88. Interview Question — How Do You Extract a Promise Value?

```typescript
type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : never;
```

Or for standard async unwrapping:

```typescript
Awaited<T>
```

---

# 89. Interview Question — How Do You Extract a Function Return Type?

```typescript
type FunctionReturn<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Or:

```typescript
ReturnType<T>
```

---

# 90. Interview Question — How Do You Extract Function Parameters?

```typescript
type FunctionParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Or:

```typescript
Parameters<T>
```

---

# 91. Interview Question — How Do You Extract Constructor Parameters?

```typescript
type ConstructorParams<T> =
    T extends new (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Or:

```typescript
ConstructorParameters<T>
```

---

# 92. Interview Question — How Do You Extract an Instance Type?

```typescript
type Instance<T> =
    T extends new (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Or:

```typescript
InstanceType<T>
```

---

# 93. Interview Question — How Do You Infer Multiple Types?

Use multiple `infer` declarations.

```typescript
type Pair<T> =
    T extends Map<
        infer K,
        infer V
    >
        ? [K, V]
        : never;
```

For:

```typescript
Map<string, User>
```

TypeScript infers:

```text
K = string

V = User
```

---

# 94. Interview Question — Can `infer` Work with Tuples?

Yes.

First:

```typescript
type First<T> =
    T extends [
        infer F,
        ...unknown[]
    ]
        ? F
        : never;
```

Last:

```typescript
type Last<T> =
    T extends [
        ...unknown[],
        infer L
    ]
        ? L
        : never;
```

Rest:

```typescript
type Tail<T> =
    T extends [
        unknown,
        ...infer R
    ]
        ? R
        : [];
```

---

# 95. Interview Question — Can `infer` Be Recursive?

Yes.

Example:

```typescript
type Flatten<T> =
    T extends readonly (infer U)[]
        ? Flatten<U>
        : T;
```

Then:

```typescript
Flatten<number[][][]>
```

becomes:

```typescript
number
```

Recursive types should be used carefully.

---

# 96. Interview Question — `infer` vs Indexed Access?

Indexed access retrieves a known property type:

```typescript
User["name"]
```

`infer` extracts a type by matching a generic structure:

```typescript
T extends Promise<infer U>
    ? U
    : never;
```

Use indexed access when the key is known.

Use `infer` when the type needs to be discovered from a structural pattern.

---

# 97. Interview Question — Does `infer` Exist at Runtime?

No.

`infer` is part of TypeScript's type system.

It disappears during compilation and produces no JavaScript code.

---

# 98. Built-In Utilities Related to Inference

Important utilities to know:

```typescript
ReturnType<T>
```

Extracts a function's return type.

```typescript
Parameters<T>
```

Extracts function parameters.

```typescript
ConstructorParameters<T>
```

Extracts constructor parameters.

```typescript
InstanceType<T>
```

Extracts a constructor's instance type.

```typescript
Awaited<T>
```

Recursively models awaited values.

These are useful examples of the kind of problems conditional inference solves.

---

# 99. Cheat Sheet

```text
INFER
================================

infer U

→ Ask TypeScript to determine
  the type in this position.


CORE PATTERN
================================

type Extract<T> =
    T extends Structure<infer U>
        ? U
        : never;


ARRAY ELEMENT
================================

type Element<T> =
    T extends readonly (infer U)[]
        ? U
        : never;


PROMISE VALUE
================================

type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : never;


FUNCTION RETURN
================================

type FunctionReturn<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;


FUNCTION PARAMETERS
================================

type FunctionParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;


FIRST PARAMETER
================================

type FirstParameter<T> =
    T extends (
        first: infer F,
        ...args: any[]
    ) => any
        ? F
        : never;


OBJECT PROPERTY
================================

type Data<T> =
    T extends {
        data: infer D;
    }
        ? D
        : never;


FIRST TUPLE ELEMENT
================================

type First<T> =
    T extends readonly [
        infer F,
        ...unknown[]
    ]
        ? F
        : never;


LAST TUPLE ELEMENT
================================

type Last<T> =
    T extends readonly [
        ...unknown[],
        infer L
    ]
        ? L
        : never;


TUPLE TAIL
================================

type Tail<T> =
    T extends readonly [
        unknown,
        ...infer R
    ]
        ? R
        : [];


TUPLE INIT
================================

type Init<T> =
    T extends readonly [
        ...infer R,
        unknown
    ]
        ? R
        : [];


MAP KEY + VALUE
================================

type MapParts<T> =
    T extends Map<
        infer K,
        infer V
    >
        ? [K, V]
        : never;


CONSTRUCTOR PARAMETERS
================================

type ConstructorParams<T> =
    T extends new (
        ...args: infer P
    ) => any
        ? P
        : never;


INSTANCE
================================

type Instance<T> =
    T extends new (
        ...args: any[]
    ) => infer R
        ? R
        : never;


RECURSIVE FLATTEN
================================

type Flatten<T> =
    T extends readonly (infer U)[]
        ? Flatten<U>
        : T;


TEMPLATE LITERAL EXTRACTION
================================

type Extension<T> =
    T extends `${string}.${infer E}`
        ? E
        : never;


BUILT-IN UTILITIES
================================

ReturnType<T>

Parameters<T>

ConstructorParameters<T>

InstanceType<T>

Awaited<T>


CORE QUESTION
================================

"I have this type.

What type exists inside it?"

→ infer
```

---

# 100. Important Rules to Remember

```text
1. infer extracts types from matching structures.

2. infer is used with conditional type matching.

3. infer creates a temporary type variable.

4. The inferred variable exists only in the type system.

5. infer creates no runtime JavaScript.

6. A common syntax is Structure<infer U>.

7. U can be returned from the true branch.

8. U can also be used to construct another type.

9. infer can extract array element types.

10. infer can support readonly arrays.

11. infer can extract Promise values.

12. Awaited<T> should usually be preferred for standard async unwrapping.

13. infer can extract function return types.

14. ReturnType<T> already provides standard return extraction.

15. infer can extract function parameter tuples.

16. Parameters<T> already provides standard parameter extraction.

17. Function parameter inference produces tuples.

18. Individual function parameters can also be inferred.

19. Rest parameters can be inferred.

20. infer can extract object property types.

21. Structural matching applies to object patterns.

22. Extra object properties do not necessarily prevent matching.

23. Multiple infer variables can exist in one pattern.

24. Generic type arguments can be extracted.

25. Map keys can be inferred.

26. Map values can be inferred.

27. Set values can be inferred.

28. Tuple elements can be inferred.

29. First tuple elements can be extracted.

30. Last tuple elements can be extracted.

31. Tuple tails can be extracted.

32. Tuple prefixes can be extracted.

33. ...infer R performs variadic tuple inference.

34. Multiple tuple parts can be extracted.

35. Constructors can be inspected.

36. Constructor parameters can be inferred.

37. ConstructorParameters<T> provides the standard utility.

38. Constructor instance types can be inferred.

39. InstanceType<T> provides the standard utility.

40. typeof Class refers to the constructor/static side.

41. Class used as a type refers to its instance side.

42. infer can extract asynchronous function results.

43. Awaited<ReturnType<T>> is a useful pattern.

44. Array elements can then be extracted with indexed access.

45. Prefer composition over giant type expressions.

46. infer works with distributive conditional types.

47. Union members may be inferred independently.

48. The inferred results can become unions.

49. Nonmatching members can become never.

50. This allows extraction and filtering together.

51. infer can combine with mapped types.

52. infer can extract method return types.

53. infer can extract method parameter types.

54. infer can combine with Awaited.

55. infer can inspect deeply nested structures.

56. Nested inference can extract deeply wrapped types.

57. infer can be recursive.

58. Recursive inference can flatten nested arrays.

59. Recursive inference can unwrap nested structures.

60. Recursive types should be used carefully.

61. Compiler recursion limits exist.

62. infer can work with template literal types.

63. String segments can be inferred.

64. Prefixes and suffixes can be extracted.

65. Route parameters can be extracted.

66. File extensions can be extracted.

67. Template literal types will be covered separately.

68. infer can model component prop extraction.

69. infer can extract event handler inputs.

70. infer can extract callback inputs.

71. infer can extract callback outputs.

72. infer can derive service return types.

73. infer can derive API response data.

74. infer can derive repository entity types.

75. infer can derive wrapper contents.

76. infer should not be used merely because it is advanced.

77. Indexed access is better when the property key is already known.

78. T[number] is often simpler for arrays and tuples.

79. ReturnType<T> is often simpler for function returns.

80. Parameters<T> is often simpler for function parameters.

81. Awaited<T> is often simpler for Promise unwrapping.

82. ConstructorParameters<T> is often simpler for constructor arguments.

83. InstanceType<T> is often simpler for constructor instances.

84. Built-in utilities should normally be preferred when they match the requirement.

85. Custom infer utilities are useful when behavior differs from built-ins.

86. infer does not inspect runtime values.

87. infer does not execute functions.

88. infer does not await promises.

89. infer does not fetch data.

90. infer does not validate API responses.

91. infer only manipulates compile-time types.

92. Avoid unnecessary any when safer alternatives work.

93. Keep generic utilities readable.

94. Break complex extraction into helper types.

95. Do not duplicate domain models without considering the source of truth.

96. Sometimes implementation-derived types are appropriate.

97. Sometimes explicit domain types should drive implementations.

98. Choose dependency direction intentionally.

99. The key question is: what type exists inside this structure?

100. The core pattern to remember is:

T extends Structure<infer U>
    ? U
    : never
```

---

# 101. Final Mental Model

Without `infer`, we know the outer type:

```typescript
Promise<User>
```

but want to discover:

```typescript
User
```

So:

```typescript
type Unwrap<T> =
    T extends Promise<infer U>
        ? U
        : never;
```

means:

```text
             T
             │
             ▼
       Promise<User>
             │
             ▼
    Match against pattern
             │
             ▼
      Promise<infer U>
             │
             ▼
         U = User
             │
             ▼
           User
```

For arrays:

```text
User[]
   │
   ▼
readonly (infer U)[]
   │
   ▼
U = User
```

For functions:

```text
(id: number) => User
        │
        ▼
(...args) => infer R
        │
        ▼
R = User
```

For parameters:

```text
(name: string, age: number) => void
              │
              ▼
       (...args: infer P)
              │
              ▼
P = [name: string, age: number]
```

For tuples:

```text
[string, number, boolean]
          │
          ▼
[infer F, ...infer R]
          │
     ┌────┴────┐
     ▼         ▼
F = string   R = [number, boolean]
```

So:

```text
Conditional Types
        │
        ▼
Does this structure match?
        │
       YES
        │
        ▼
      infer
        │
        ▼
Discover unknown type parts
        │
        ├── Array element
        ├── Promise value
        ├── Function return
        ├── Function parameters
        ├── Tuple parts
        ├── Object properties
        ├── Generic arguments
        ├── Constructor arguments
        └── String segments
```

The key distinction is:

```text
Generics
→ Accept a type

keyof
→ Get keys from a type

Indexed Access
→ Get a known property type

Mapped Types
→ Transform properties

Conditional Types
→ Choose types based on conditions

infer
→ Discover types inside structures
```

If you remember one pattern, remember:

```typescript
type Extract<T> =
    T extends Wrapper<infer U>
        ? U
        : never;
```

Think:

```text
"If T looks like Wrapper<something>,
tell me what something is."
```

That is the core purpose of `infer`.
