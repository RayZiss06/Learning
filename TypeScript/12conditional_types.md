# TypeScript — Conditional Types

Conditional Types allow TypeScript to **choose one type or another based on a condition**.

The basic syntax is:

```typescript
T extends U ? X : Y
```

Read it as:

```text
If T is assignable to U
    use X
otherwise
    use Y
```

It looks similar to JavaScript's ternary operator:

```javascript
condition
    ? trueValue
    : falseValue;
```

But conditional types work entirely in the **TypeScript type system**.

---

# 1. Basic Syntax

```typescript
type Result<T> =
    T extends string
        ? "String"
        : "Not String";
```

Now:

```typescript
type A =
    Result<string>;
```

Result:

```typescript
"String"
```

While:

```typescript
type B =
    Result<number>;
```

Result:

```typescript
"Not String"
```

---

# 2. Mental Model

```typescript
type IsString<T> =
    T extends string
        ? true
        : false;
```

Think:

```text
          T
          │
          ▼
   Is T assignable
     to string?
       /     \
     Yes      No
      │        │
      ▼        ▼
    true      false
```

Example:

```typescript
type A =
    IsString<string>;
// true

type B =
    IsString<number>;
// false
```

---

# 3. `extends` Here Does Not Mean Class Inheritance

You may already know:

```typescript
class Dog extends Animal {}
```

There, `extends` represents class inheritance.

You may also know generic constraints:

```typescript
function print<T extends object>(
    value: T
) {}
```

In conditional types:

```typescript
T extends U
    ? X
    : Y
```

the meaning is closer to:

```text
Is T assignable to U?
```

Example:

```typescript
type Test =
    "hello" extends string
        ? true
        : false;
```

Result:

```typescript
true
```

because:

```text
"hello"
```

is assignable to:

```text
string
```

---

# 4. Literal Types and Conditional Types

```typescript
type A =
    "hello" extends string
        ? "YES"
        : "NO";
```

Result:

```typescript
"YES"
```

Because:

```text
"hello"
    ↓
is a string literal type
    ↓
assignable to string
```

Similarly:

```typescript
type B =
    100 extends number
        ? "YES"
        : "NO";
```

Result:

```typescript
"YES"
```

---

# 5. Simple Boolean Type Utility

```typescript
type IsNumber<T> =
    T extends number
        ? true
        : false;
```

Examples:

```typescript
type A =
    IsNumber<number>;
// true

type B =
    IsNumber<10>;
// true

type C =
    IsNumber<string>;
// false

type D =
    IsNumber<boolean>;
// false
```

---

# 6. Returning Types Instead of Booleans

Conditional types do not have to return:

```text
true
false
```

They can return any type.

```typescript
type Normalize<T> =
    T extends string
        ? string
        : number;
```

Example:

```typescript
type A =
    Normalize<"hello">;
// string

type B =
    Normalize<boolean>;
// number
```

The branches can contain:

```text
Primitive types

Object types

Arrays

Functions

Unions

Other generics

Other conditional types

Mapped types
```

---

# 7. Conditional Object Types

```typescript
type ApiResult<T> =
    T extends Error
        ? {
            success: false;
            error: T;
        }
        : {
            success: true;
            data: T;
        };
```

Then:

```typescript
type UserResult =
    ApiResult<User>;
```

becomes:

```typescript
{
    success: true;
    data: User;
}
```

While:

```typescript
type ErrorResult =
    ApiResult<Error>;
```

becomes:

```typescript
{
    success: false;
    error: Error;
}
```

---

# 8. Nested Conditional Types

We can chain conditions.

```typescript
type TypeName<T> =
    T extends string
        ? "string"
        : T extends number
            ? "number"
            : T extends boolean
                ? "boolean"
                : "other";
```

Examples:

```typescript
type A =
    TypeName<string>;
// "string"

type B =
    TypeName<number>;
// "number"

type C =
    TypeName<boolean>;
// "boolean"

type D =
    TypeName<Date>;
// "other"
```

---

# 9. Nested Conditional Mental Model

```text
                T
                │
                ▼
            string?
            /     \
          yes      no
           │        │
           ▼        ▼
       "string"   number?
                  /     \
                yes      no
                 │        │
                 ▼        ▼
             "number"   boolean?
                        /      \
                      yes       no
                       │         │
                       ▼         ▼
                  "boolean"   "other"
```

---

# 10. Conditional Types with Arrays

```typescript
type IsArray<T> =
    T extends unknown[]
        ? true
        : false;
```

Examples:

```typescript
type A =
    IsArray<string[]>;
// true

type B =
    IsArray<number[]>;
// true

type C =
    IsArray<string>;
// false
```

For readonly arrays, you may instead need:

```typescript
type IsArray<T> =
    T extends readonly unknown[]
        ? true
        : false;
```

---

# 11. Extracting Array Element Types

Suppose:

```typescript
type ElementType<T> =
    T extends (infer U)[]
        ? U
        : T;
```

Then:

```typescript
type A =
    ElementType<string[]>;
// string

type B =
    ElementType<number[]>;
// number

type C =
    ElementType<boolean>;
// boolean
```

Here:

```typescript
infer U
```

means:

```text
If T is an array,
figure out its element type
and call that type U.
```

We will cover `infer` more deeply later in this topic.

---

# 12. Conditional Types with Objects

```typescript
type IsObject<T> =
    T extends object
        ? true
        : false;
```

Examples:

```typescript
type A =
    IsObject<{ name: string }>;
// true

type B =
    IsObject<string>;
// false
```

But remember:

```text
object
```

also includes things such as:

```text
Arrays
Functions
Date
Map
Set
Class instances
```

So:

```typescript
type C =
    IsObject<string[]>;
// true
```

---

# 13. Conditional Types with Functions

```typescript
type IsFunction<T> =
    T extends (...args: any[]) => any
        ? true
        : false;
```

Examples:

```typescript
type A =
    IsFunction<() => void>;
// true

type B =
    IsFunction<(x: number) => string>;
// true

type C =
    IsFunction<string>;
// false
```

---

# 14. Conditional Return Type

Suppose we want:

```text
string
→ string[]

number
→ number[]

boolean
→ boolean[]
```

We can simply use:

```typescript
type ToArray<T> =
    T extends unknown
        ? T[]
        : never;
```

But this condition is always true for ordinary types.

So the simpler form is:

```typescript
type ToArray<T> =
    T[];
```

Conditional types are useful when branches actually differ.

Example:

```typescript
type ResponseType<T> =
    T extends string
        ? string[]
        : T extends number
            ? number[]
            : T;
```

---

# 15. Conditional Type with Generic Functions

Conditional types can describe a return type based on a generic input.

```typescript
type Result<T> =
    T extends string
        ? string[]
        : number[];
```

Then:

```typescript
function process<T>(
    value: T
): Result<T> {
    // implementation...
    return [] as Result<T>;
}
```

Calling:

```typescript
const a =
    process("hello");
```

gives:

```text
string[]
```

while:

```typescript
const b =
    process(100);
```

gives:

```text
number[]
```

Note that implementing such generic functions may require narrowing, overloads, or assertions because the runtime implementation must satisfy the generic relationship.

---

# 16. Conditional Types and Unions

This is one of the most important parts of conditional types.

Consider:

```typescript
type ToArray<T> =
    T extends unknown
        ? T[]
        : never;
```

Now:

```typescript
type Result =
    ToArray<
        string | number
    >;
```

You might expect:

```typescript
(string | number)[]
```

But the result is:

```typescript
string[]
|
number[]
```

Why?

Because conditional types can be **distributive over unions**.

---

# 17. Distributive Conditional Types

When the checked type is a **naked generic type parameter**:

```typescript
T extends U
    ? X
    : Y
```

and `T` is a union:

```typescript
A | B | C
```

TypeScript evaluates each member separately.

Conceptually:

```text
Conditional<A>
|
Conditional<B>
|
Conditional<C>
```

---

# 18. Distribution Example

```typescript
type Wrap<T> =
    T extends unknown
        ? {
            value: T;
        }
        : never;
```

Then:

```typescript
type Result =
    Wrap<
        string
        | number
    >;
```

TypeScript performs:

```typescript
Wrap<string>
|
Wrap<number>
```

Result:

```typescript
{
    value: string;
}
|
{
    value: number;
}
```

---

# 19. Distribution Step by Step

Given:

```typescript
type ToArray<T> =
    T extends unknown
        ? T[]
        : never;
```

and:

```typescript
type Result =
    ToArray<
        string | number
    >;
```

TypeScript distributes:

```text
string | number
       │
       ▼

ToArray<string>
|
ToArray<number>

       │
       ▼

string[]
|
number[]
```

Not:

```typescript
(string | number)[]
```

These types have different meanings.

---

# 20. Why These Array Types Are Different

```typescript
string[] | number[]
```

means:

```text
Either:

an array containing strings

OR

an array containing numbers
```

While:

```typescript
(string | number)[]
```

means:

```text
One array may contain both
strings and numbers.
```

Example:

```typescript
const values:
    (string | number)[] = [
        "hello",
        10,
        "world",
        20
    ];
```

But this would not fit:

```typescript
string[] | number[]
```

because it is neither purely `string[]` nor purely `number[]`.

---

# 21. Preventing Distribution

Wrap the types in tuples:

```typescript
type ToArrayNonDistributed<T> =
    [T] extends [unknown]
        ? T[]
        : never;
```

Now:

```typescript
type Result =
    ToArrayNonDistributed<
        string | number
    >;
```

Result:

```typescript
(string | number)[]
```

---

# 22. Why Tuple Wrapping Prevents Distribution

Distribution occurs when the condition directly checks:

```typescript
T extends U
```

But:

```typescript
[T] extends [U]
```

does not use a naked `T`.

Therefore the union is evaluated as one whole type.

Remember:

```text
T extends U
→ may distribute

[T] extends [U]
→ does not distribute
```

---

# 23. Filtering Unions

Conditional types are extremely useful for filtering union members.

Example:

```typescript
type OnlyStrings<T> =
    T extends string
        ? T
        : never;
```

Then:

```typescript
type Result =
    OnlyStrings<
        string
        | number
        | boolean
    >;
```

Distribution gives:

```text
string
→ string

number
→ never

boolean
→ never
```

Result:

```typescript
string
```

because `never` disappears from unions.

---

# 24. `never` in Unions

Consider:

```typescript
string
|
never
|
number
```

This simplifies to:

```typescript
string | number
```

Think of `never` as representing:

```text
No possible value
```

So it contributes nothing to a union.

This makes it ideal for filtering.

---

# 25. Filter Numbers

```typescript
type OnlyNumbers<T> =
    T extends number
        ? T
        : never;
```

Example:

```typescript
type Values =
    string
    | number
    | boolean
    | 100;
```

Then:

```typescript
type Numbers =
    OnlyNumbers<Values>;
```

Result:

```typescript
number
```

Because `100` is already included by `number`.

---

# 26. Filter Specific Literal Members

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

We can remove:

```text
"error"
```

using:

```typescript
type WithoutError<T> =
    T extends "error"
        ? never
        : T;
```

Then:

```typescript
type SafeStatus =
    WithoutError<Status>;
```

Result:

```typescript
"idle"
|
"loading"
|
"success"
```

---

# 27. Understanding `Exclude<T, U>`

TypeScript provides:

```typescript
Exclude<T, U>
```

A simplified implementation is:

```typescript
type MyExclude<T, U> =
    T extends U
        ? never
        : T;
```

Example:

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

Then:

```typescript
type Result =
    MyExclude<
        Status,
        "error"
    >;
```

Result:

```typescript
"idle"
|
"loading"
|
"success"
```

---

# 28. How `Exclude` Works

For:

```typescript
MyExclude<
    "idle"
    | "loading"
    | "error",
    "error"
>
```

distribution performs:

```text
"idle" extends "error"
→ false
→ "idle"

"loading" extends "error"
→ false
→ "loading"

"error" extends "error"
→ true
→ never
```

Result:

```text
"idle"
|
"loading"
```

---

# 29. Understanding `Extract<T, U>`

`Extract` does the opposite.

Simplified:

```typescript
type MyExtract<T, U> =
    T extends U
        ? T
        : never;
```

Example:

```typescript
type Values =
    string
    | number
    | boolean;
```

Then:

```typescript
type Result =
    MyExtract<
        Values,
        string | number
    >;
```

Result:

```typescript
string | number
```

---

# 30. `Exclude` vs `Extract`

```text
Exclude<T, U>
→ remove members assignable to U


Extract<T, U>
→ keep members assignable to U
```

Implementations:

```typescript
type MyExclude<T, U> =
    T extends U
        ? never
        : T;

type MyExtract<T, U> =
    T extends U
        ? T
        : never;
```

---

# 31. Understanding `NonNullable<T>`

TypeScript provides:

```typescript
NonNullable<T>
```

It removes:

```text
null

undefined
```

Example:

```typescript
type Value =
    string
    | null
    | undefined;
```

Then:

```typescript
type Result =
    NonNullable<Value>;
```

Result:

```typescript
string
```

A conceptual implementation is:

```typescript
type MyNonNullable<T> =
    T extends null | undefined
        ? never
        : T;
```

---

# 32. How `NonNullable` Works

Given:

```typescript
string
|
null
|
undefined
```

distribution evaluates:

```text
string
→ string

null
→ never

undefined
→ never
```

Result:

```typescript
string
```

---

# 33. Conditional Types with Mapped Types

Mapped types and conditional types are often combined.

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    active: boolean;
};
```

We want only properties whose values are strings.

```typescript
type StringProperties<T> = {
    [K in keyof T as
        T[K] extends string
            ? K
            : never
    ]:
        T[K];
};
```

Result:

```typescript
{
    name: string;
    email: string;
}
```

---

# 34. How Property Filtering Works

For:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    active: boolean;
};
```

TypeScript evaluates:

```text
id
→ number extends string?
→ No
→ never
→ remove

name
→ string extends string?
→ Yes
→ "name"

email
→ string extends string?
→ Yes
→ "email"

active
→ boolean extends string?
→ No
→ never
→ remove
```

Result:

```typescript
{
    name: string;
    email: string;
}
```

---

# 35. Extract Keys by Value Type

Instead of returning an object, we can return the matching keys.

```typescript
type KeysOfType<T, V> = {
    [K in keyof T]:
        T[K] extends V
            ? K
            : never;
}[keyof T];
```

Example:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
    active: boolean;
};
```

Then:

```typescript
type StringKeys =
    KeysOfType<
        User,
        string
    >;
```

Result:

```typescript
"name" | "email"
```

---

# 36. Understanding `KeysOfType`

First:

```typescript
{
    [K in keyof User]:
        User[K] extends string
            ? K
            : never;
}
```

creates conceptually:

```typescript
{
    id: never;
    name: "name";
    email: "email";
    active: never;
}
```

Then:

```typescript
...[keyof User]
```

extracts all values:

```text
never
|
"name"
|
"email"
|
never
```

which becomes:

```text
"name" | "email"
```

This is an important advanced pattern.

---

# 37. Conditional Transformation of Property Values

Instead of filtering keys, we can transform values.

```typescript
type Serialize<T> = {
    [K in keyof T]:
        T[K] extends Date
            ? string
            : T[K];
};
```

Given:

```typescript
type User = {
    id: number;
    name: string;
    createdAt: Date;
};
```

Then:

```typescript
type SerializedUser =
    Serialize<User>;
```

becomes:

```typescript
{
    id: number;
    name: string;
    createdAt: string;
}
```

---

# 38. Multiple Transformations

```typescript
type SerializeValue<T> =
    T extends Date
        ? string
        : T extends bigint
            ? string
            : T;
```

Then:

```typescript
type Serialize<T> = {
    [K in keyof T]:
        SerializeValue<T[K]>;
};
```

This separates:

```text
Property iteration
```

from:

```text
Value transformation
```

which often makes complex types easier to understand.

---

# 39. Conditional Types with Promises

Suppose:

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
```

```typescript
type B =
    UnwrapPromise<
        Promise<User>
    >;
// User
```

```typescript
type C =
    UnwrapPromise<number>;
// number
```

This introduces one of the most powerful features of conditional types:

```text
infer
```

---

# 40. What Is `infer`?

`infer` allows TypeScript to **extract part of a type while matching a conditional type**.

Syntax:

```typescript
T extends SomeType<infer U>
    ? U
    : ...
```

Think:

```text
If T matches this structure,
figure out the unknown type
inside it and call it U.
```

Example:

```typescript
type Element<T> =
    T extends Array<infer U>
        ? U
        : T;
```

For:

```typescript
string[]
```

TypeScript sees:

```text
Array<string>
```

and infers:

```text
U = string
```

---

# 41. `infer` Mental Model

```typescript
type Element<T> =
    T extends Array<infer U>
        ? U
        : never;
```

For:

```typescript
Element<number[]>
```

TypeScript performs:

```text
number[]
   │
   ▼
Array<number>
   │
   ▼
Does it match Array<infer U>?
   │
   ▼
YES
   │
   ▼
U = number
   │
   ▼
Return number
```

---

# 42. Extract Array Element

```typescript
type ArrayElement<T> =
    T extends readonly (infer U)[]
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
    ArrayElement<
        readonly boolean[]
    >;
// boolean
```

---

# 43. Extract Function Return Type

Suppose:

```typescript
function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

We can define:

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

# 44. Understanding Return Type Inference

For:

```typescript
() => string
```

matching:

```typescript
(...args: any[]) => infer R
```

TypeScript determines:

```text
R = string
```

Therefore:

```typescript
FunctionReturn<
    () => string
>
```

becomes:

```text
string
```

---

# 45. Built-In `ReturnType<T>`

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
```

Then:

```typescript
type User =
    ReturnType<
        typeof createUser
    >;
```

Result:

```typescript
{
    id: number;
    name: string;
}
```

A simplified idea is:

```typescript
type MyReturnType<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

The actual built-in utility has its own constraints and implementation details.

---

# 46. Extract Function Parameters

We can also infer parameters.

```typescript
type FunctionParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Suppose:

```typescript
function createUser(
    name: string,
    age: number,
    active: boolean
) {}
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

# 47. Built-In `Parameters<T>`

TypeScript provides:

```typescript
Parameters<T>
```

So:

```typescript
type Params =
    Parameters<
        typeof createUser
    >;
```

returns the parameter tuple.

This is especially useful when you want another function to reuse an existing function's parameter structure.

---

# 48. Extract First Parameter

```typescript
type FirstParameter<T> =
    T extends (
        first: infer F,
        ...args: any[]
    ) => any
        ? F
        : never;
```

Example:

```typescript
type A =
    FirstParameter<
        (
            name: string,
            age: number
        ) => void
    >;
```

Result:

```typescript
string
```

---

# 49. Extract Promise Value

```typescript
type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : never;
```

Example:

```typescript
type A =
    PromiseValue<
        Promise<User>
    >;
```

Result:

```typescript
User
```

---

# 50. Nested Promises

Our simple utility:

```typescript
type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : T;
```

unwraps only one level.

For:

```typescript
Promise<
    Promise<string>
>
```

the result would conceptually be:

```typescript
Promise<string>
```

To recursively unwrap:

```typescript
type DeepPromiseValue<T> =
    T extends Promise<infer U>
        ? DeepPromiseValue<U>
        : T;
```

Then:

```typescript
type Result =
    DeepPromiseValue<
        Promise<
            Promise<string>
        >
    >;
```

becomes:

```typescript
string
```

---

# 51. Built-In `Awaited<T>`

TypeScript provides:

```typescript
Awaited<T>
```

which models promise/thenable unwrapping more accurately than a simple custom example.

Example:

```typescript
type Result =
    Awaited<
        Promise<
            Promise<string>
        >
    >;
```

Result:

```typescript
string
```

For normal application code, prefer:

```typescript
Awaited<T>
```

when you need TypeScript's standard async unwrapping behavior.

---

# 52. Extract Object Property Type with `infer`

Suppose:

```typescript
type HasData<T> =
    T extends {
        data: infer D;
    }
        ? D
        : never;
```

Then:

```typescript
type Response = {
    success: boolean;
    data: User[];
};
```

```typescript
type Data =
    HasData<Response>;
```

Result:

```typescript
User[]
```

---

# 53. Extract Multiple Types

`infer` can appear multiple times.

```typescript
type KeyValue<T> =
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
    KeyValue<
        Map<string, User>
    >;
```

Result:

```typescript
[string, User]
```

---

# 54. Infer Tuple Parts

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
type A =
    First<
        [string, number, boolean]
    >;
```

Result:

```typescript
string
```

---

# 55. Infer Last Tuple Element

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
type A =
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

# 56. Infer Rest of Tuple

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

# 57. Conditional Types with React-Like Props

Suppose we have two modes:

```typescript
type Mode =
    "create"
    | "edit";
```

We can define:

```typescript
type FormProps<M extends Mode> =
    M extends "edit"
        ? {
            mode: M;
            userId: string;
        }
        : {
            mode: M;
        };
```

Then:

```typescript
type CreateProps =
    FormProps<"create">;
```

becomes:

```typescript
{
    mode: "create";
}
```

while:

```typescript
type EditProps =
    FormProps<"edit">;
```

becomes:

```typescript
{
    mode: "edit";
    userId: string;
}
```

For many React prop relationships, discriminated unions may be simpler, but conditional types are useful when building reusable generic APIs.

---

# 58. Conditional Event Values

Suppose:

```typescript
type InputType =
    "text"
    | "number"
    | "checkbox";
```

We can define:

```typescript
type InputValue<T extends InputType> =
    T extends "checkbox"
        ? boolean
        : T extends "number"
            ? number
            : string;
```

Then:

```typescript
type TextValue =
    InputValue<"text">;
// string
```

```typescript
type NumberValue =
    InputValue<"number">;
// number
```

```typescript
type CheckboxValue =
    InputValue<"checkbox">;
// boolean
```

---

# 59. Generic Field Model

```typescript
type Field<
    T extends InputType
> = {
    type: T;
    value: InputValue<T>;
};
```

Now:

```typescript
type CheckboxField =
    Field<"checkbox">;
```

becomes:

```typescript
{
    type: "checkbox";
    value: boolean;
}
```

while:

```typescript
type NumberField =
    Field<"number">;
```

becomes:

```typescript
{
    type: "number";
    value: number;
}
```

---

# 60. API Method Based on Operation

Suppose:

```typescript
type Operation =
    "get"
    | "create"
    | "delete";
```

We can define:

```typescript
type OperationResult<T> =
    T extends "get"
        ? User[]
        : T extends "create"
            ? User
            : void;
```

Then:

```typescript
type GetResult =
    OperationResult<"get">;
// User[]
```

```typescript
type CreateResult =
    OperationResult<"create">;
// User
```

```typescript
type DeleteResult =
    OperationResult<"delete">;
// void
```

---

# 61. Recursive Conditional Types

Conditional types can recursively reference themselves.

Example:

```typescript
type Flatten<T> =
    T extends readonly (
        infer U
    )[]
        ? Flatten<U>
        : T;
```

Then:

```typescript
type A =
    Flatten<
        string[][][]
    >;
```

Result:

```typescript
string
```

Flow:

```text
string[][][]
    ↓
string[][]
    ↓
string[]
    ↓
string
```

---

# 62. Recursive Array Flattening

```typescript
type Flatten<T> =
    T extends readonly (infer U)[]
        ? Flatten<U>
        : T;
```

Examples:

```typescript
type A =
    Flatten<number>;
// number
```

```typescript
type B =
    Flatten<number[]>;
// number
```

```typescript
type C =
    Flatten<number[][][]>;
// number
```

TypeScript has recursion-depth limits, so recursive types should be designed carefully.

---

# 63. Recursive Promise Unwrapping

```typescript
type Unwrap<T> =
    T extends Promise<infer U>
        ? Unwrap<U>
        : T;
```

Example:

```typescript
type Result =
    Unwrap<
        Promise<
            Promise<
                User
            >
        >
    >;
```

Result:

```typescript
User
```

Again, built-in:

```typescript
Awaited<T>
```

should usually be preferred for actual promise unwrapping.

---

# 64. Conditional Type Based on Property Presence

Suppose:

```typescript
type HasId<T> =
    T extends {
        id: unknown;
    }
        ? true
        : false;
```

Then:

```typescript
type A =
    HasId<{
        id: number;
        name: string;
    }>;
// true
```

```typescript
type B =
    HasId<{
        name: string;
    }>;
// false
```

---

# 65. Return Property If It Exists

```typescript
type IdType<T> =
    T extends {
        id: infer I;
    }
        ? I
        : never;
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
type UserId =
    IdType<User>;
```

Result:

```typescript
number
```

---

# 66. Property Shape Matching

Conditional types use structural typing.

Example:

```typescript
type HasName<T> =
    T extends {
        name: string;
    }
        ? true
        : false;
```

Then:

```typescript
type A =
    HasName<{
        id: number;
        name: string;
        age: number;
    }>;
```

Result:

```typescript
true
```

The object may have additional properties.

It only needs to satisfy the required structure.

---

# 67. Optional Property Matching

These are not identical:

```typescript
T extends {
    name: string;
}
```

and:

```typescript
T extends {
    name?: string;
}
```

The first requires the property.

The second allows it to be optional.

This distinction matters when using conditional types to inspect object structures.

---

# 68. Conditional Types and `any`

`any` behaves specially throughout TypeScript.

For example:

```typescript
type Test<T> =
    T extends string
        ? "yes"
        : "no";
```

Then:

```typescript
type Result =
    Test<any>;
```

can produce:

```typescript
"yes" | "no"
```

rather than behaving like an ordinary concrete type.

This is another reason to avoid unnecessary use of `any`.

---

# 69. Conditional Types and `never`

`never` also behaves specially with distributive conditional types.

Consider:

```typescript
type IsNever<T> =
    T extends never
        ? true
        : false;
```

You might expect:

```typescript
IsNever<never>
```

to produce:

```typescript
true
```

But because distribution has no union members to process, the result is:

```typescript
never
```

---

# 70. Correct `IsNever`

Prevent distribution:

```typescript
type IsNever<T> =
    [T] extends [never]
        ? true
        : false;
```

Now:

```typescript
type A =
    IsNever<never>;
// true
```

```typescript
type B =
    IsNever<string>;
// false
```

This is a classic TypeScript interview concept.

---

# 71. Checking Exact Union as a Whole

Suppose:

```typescript
type IsString<T> =
    T extends string
        ? true
        : false;
```

Then:

```typescript
type Result =
    IsString<
        string | number
    >;
```

produces:

```typescript
true | false
```

because distribution occurs.

If you want to ask:

```text
Is the entire type assignable to string?
```

use:

```typescript
type IsEntirelyString<T> =
    [T] extends [string]
        ? true
        : false;
```

Then:

```typescript
type Result =
    IsEntirelyString<
        string | number
    >;
```

is:

```typescript
false
```

---

# 72. Distribution Is Not Always Desired

Distribution is excellent for:

```text
Filtering unions

Transforming each union member

Extracting union members
```

But sometimes you want to inspect:

```text
the union as one complete type
```

Then use:

```typescript
[T] extends [U]
```

instead of:

```typescript
T extends U
```

---

# 73. Conditional Types vs Function Overloads

Suppose a function behaves differently based on input.

You could use overloads:

```typescript
function format(
    value: string
): string;

function format(
    value: number
): number;

function format(
    value: string | number
): string | number {
    return value;
}
```

Or for reusable generic relationships, conditional types may model the return type.

```typescript
type FormatResult<T> =
    T extends string
        ? string
        : number;
```

Which approach is clearer depends on the API.

Conditional types are not automatically better than overloads.

---

# 74. Conditional Types vs Union Types

If the relationship is fixed and simple:

```typescript
type Result =
    string | number;
```

is better than unnecessary conditional logic.

Use conditional types when:

```text
Output type depends on input type.
```

Do not use them just because they are available.

---

# 75. Conditional Types vs Mapped Types

Mapped type:

```typescript
type Nullable<T> = {
    [K in keyof T]:
        T[K] | null;
};
```

asks:

```text
How should every property
be transformed?
```

Conditional type:

```typescript
type NullableValue<T> =
    T extends string
        ? string | null
        : T;
```

asks:

```text
Which type should be selected
based on a condition?
```

They solve different problems but are often combined.

---

# 76. Mapped + Conditional Example

```typescript
type FormValues<T> = {
    [K in keyof T]:
        T[K] extends Date
            ? string
            : T[K] extends boolean
                ? boolean
                : T[K];
};
```

Given:

```typescript
type Model = {
    name: string;
    birthday: Date;
    active: boolean;
};
```

Result:

```typescript
{
    name: string;
    birthday: string;
    active: boolean;
}
```

---

# 77. DTO Transformation

Suppose your internal model contains:

```typescript
type User = {
    id: number;
    name: string;
    createdAt: Date;
    updatedAt: Date;
};
```

Your API may represent dates as strings.

```typescript
type ToDto<T> = {
    [K in keyof T]:
        T[K] extends Date
            ? string
            : T[K];
};
```

Then:

```typescript
type UserDto =
    ToDto<User>;
```

becomes:

```typescript
{
    id: number;
    name: string;
    createdAt: string;
    updatedAt: string;
}
```

Remember: this only transforms the **type**. It does not convert actual `Date` objects into strings at runtime.

---

# 78. Recursive DTO Transformation

A simplified recursive version:

```typescript
type ToDto<T> =
    T extends Date
        ? string
        : T extends readonly (infer U)[]
            ? ToDto<U>[]
            : T extends object
                ? {
                    [K in keyof T]:
                        ToDto<T[K]>;
                }
                : T;
```

This demonstrates how:

```text
Conditional Types

Mapped Types

infer

Recursion
```

can work together.

Production serialization types may need more cases.

---

# 79. Conditional API State

```typescript
type ApiState<T> =
    T extends Error
        ? {
            status: "error";
            error: T;
        }
        : {
            status: "success";
            data: T;
        };
```

Conditional types can model systematic type relationships.

However, if both states need to coexist independently, a discriminated union is often more appropriate.

---

# 80. Extracting Function Return Types from Object Methods

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

We can create:

```typescript
type ServiceResults<T> = {
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

# 81. Await Service Results

Combine:

```typescript
Awaited
```

with conditional inference:

```typescript
type AsyncServiceResults<T> = {
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

This is a realistic example of type composition.

---

# 82. Common Mistake — Thinking Conditional Types Run at Runtime

This:

```typescript
type Result<T> =
    T extends string
        ? A
        : B;
```

does not generate:

```javascript
if (...)
```

It exists only during type checking.

Conditional types disappear from the generated JavaScript.

---

# 83. Common Mistake — Reading `extends` as Only Inheritance

In:

```typescript
T extends string
    ? A
    : B
```

read:

```text
Is T assignable to string?
```

not:

```text
Does T inherit from string?
```

---

# 84. Common Mistake — Forgetting Distribution

```typescript
type ToArray<T> =
    T extends unknown
        ? T[]
        : never;
```

For:

```typescript
string | number
```

result:

```typescript
string[] | number[]
```

not:

```typescript
(string | number)[]
```

because `T` is distributed.

---

# 85. Common Mistake — Preventing Distribution Accidentally

These behave differently:

```typescript
T extends U
    ? X
    : Y
```

and:

```typescript
[T] extends [U]
    ? X
    : Y
```

The second prevents distribution.

Do not tuple-wrap automatically.

Know whether you want:

```text
each union member
```

or:

```text
the union as a whole.
```

---

# 86. Common Mistake — `never` Check

This:

```typescript
type IsNever<T> =
    T extends never
        ? true
        : false;
```

does not reliably test `never`.

Use:

```typescript
type IsNever<T> =
    [T] extends [never]
        ? true
        : false;
```

---

# 87. Common Mistake — Overusing Nested Conditionals

This:

```typescript
type Result<T> =
    T extends A
        ? X
        : T extends B
            ? Y
            : T extends C
                ? Z
                : T extends D
                    ? ...
                    : ...;
```

can become difficult to understand.

For complex domain models, consider:

```text
Explicit unions

Discriminated unions

Mapped lookup types

Separate helper types
```

instead.

---

# 88. Common Mistake — Reimplementing Built-In Utilities

Learning this:

```typescript
type MyExclude<T, U> =
    T extends U
        ? never
        : T;
```

is useful.

But in application code, prefer:

```typescript
Exclude<T, U>
```

Likewise prefer built-ins such as:

```text
Extract

NonNullable

ReturnType

Parameters

Awaited
```

when they already model your requirement.

---

# 89. Common Mistake — `object` Is Broad

```typescript
T extends object
```

does not mean:

```text
plain JavaScript object only
```

It may include:

```text
Arrays

Functions

Dates

Maps

Sets

Class instances
```

This matters particularly for recursive conditional types.

---

# 90. Common Mistake — Confusing Type Transformation with Data Transformation

```typescript
type ToDto<T> = {
    [K in keyof T]:
        T[K] extends Date
            ? string
            : T[K];
};
```

does not transform:

```javascript
new Date()
```

into an actual string.

You still need runtime code:

```typescript
date.toISOString()
```

or another serialization mechanism.

---

# 91. Common Mistake — Using `any` in Type Logic

Because `any` has unusual behavior, types such as:

```typescript
type Test<T> =
    T extends string
        ? A
        : B;
```

may behave unexpectedly for:

```typescript
Test<any>
```

Prefer:

```typescript
unknown
```

when you mean:

```text
Some type exists,
but I do not know what it is.
```

---

# 92. Common Mistake — Overengineering Types

A type such as:

```typescript
type MassiveRecursiveConditionalMappedInferUtility<...>
```

may technically work but be harder to maintain than explicit domain types.

Advanced TypeScript should improve:

```text
Safety

Reuse

Correctness

Developer experience
```

not simply maximize type-system complexity.

---

# 93. Interview Question — What Is a Conditional Type?

A conditional type selects one type or another based on assignability.

Syntax:

```typescript
T extends U
    ? X
    : Y;
```

Meaning:

```text
If T is assignable to U,
return X.

Otherwise,
return Y.
```

---

# 94. Interview Question — What Does `extends` Mean Here?

In conditional types:

```typescript
T extends U
```

checks whether `T` is assignable to `U`.

It is not limited to class inheritance.

---

# 95. Interview Question — What Is a Distributive Conditional Type?

A conditional type using a naked generic parameter:

```typescript
T extends U
    ? X
    : Y
```

distributes over unions.

Example:

```typescript
type ToArray<T> =
    T extends unknown
        ? T[]
        : never;
```

Then:

```typescript
ToArray<
    string | number
>
```

becomes:

```typescript
string[]
|
number[]
```

---

# 96. Interview Question — How Do You Prevent Distribution?

Wrap both sides in tuples:

```typescript
[T] extends [U]
    ? X
    : Y;
```

Example:

```typescript
type ToArray<T> =
    [T] extends [unknown]
        ? T[]
        : never;
```

Then:

```typescript
ToArray<
    string | number
>
```

becomes:

```typescript
(string | number)[]
```

---

# 97. Interview Question — Why Is `never` Useful?

`never` disappears from unions.

Example:

```typescript
string | never
```

becomes:

```typescript
string
```

Therefore conditional types can filter union members by returning:

```typescript
never
```

for unwanted members.

---

# 98. Interview Question — How Does `Exclude` Work?

Conceptually:

```typescript
type MyExclude<T, U> =
    T extends U
        ? never
        : T;
```

It distributes over `T` and removes members assignable to `U`.

---

# 99. Interview Question — How Does `Extract` Work?

Conceptually:

```typescript
type MyExtract<T, U> =
    T extends U
        ? T
        : never;
```

It keeps only members of `T` assignable to `U`.

---

# 100. Interview Question — What Is `infer`?

`infer` introduces a type variable inside a conditional type and allows TypeScript to determine that type from a matching structure.

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

and returns:

```typescript
string
```

---

# 101. Interview Question — How Do You Extract a Function Return Type?

```typescript
type MyReturnType<T> =
    T extends (
        ...args: any[]
    ) => infer R
        ? R
        : never;
```

Or use the built-in:

```typescript
ReturnType<T>
```

---

# 102. Interview Question — How Do You Extract Function Parameters?

```typescript
type MyParameters<T> =
    T extends (
        ...args: infer P
    ) => any
        ? P
        : never;
```

Or use:

```typescript
Parameters<T>
```

---

# 103. Interview Question — How Do You Unwrap a Promise?

Simple version:

```typescript
type Unwrap<T> =
    T extends Promise<infer U>
        ? U
        : T;
```

For standard promise/thenable unwrapping, use:

```typescript
Awaited<T>
```

---

# 104. Interview Question — Mapped vs Conditional Types?

Mapped types transform properties:

```typescript
type Nullable<T> = {
    [K in keyof T]:
        T[K] | null;
};
```

Conditional types choose types:

```typescript
type Result<T> =
    T extends string
        ? A
        : B;
```

They are frequently combined.

---

# 105. Cheat Sheet

```text
CONDITIONAL TYPE
================================

T extends U
    ? X
    : Y


MEANING
================================

Is T assignable to U?

YES
→ X

NO
→ Y


BASIC
================================

type IsString<T> =
    T extends string
        ? true
        : false;


NESTED
================================

type TypeName<T> =
    T extends string
        ? "string"
        : T extends number
            ? "number"
            : "other";


DISTRIBUTION
================================

type ToArray<T> =
    T extends unknown
        ? T[]
        : never;


string | number

→

string[] | number[]


PREVENT DISTRIBUTION
================================

type ToArray<T> =
    [T] extends [unknown]
        ? T[]
        : never;


string | number

→

(string | number)[]


FILTER UNION
================================

type OnlyStrings<T> =
    T extends string
        ? T
        : never;


EXCLUDE
================================

type MyExclude<T, U> =
    T extends U
        ? never
        : T;


EXTRACT
================================

type MyExtract<T, U> =
    T extends U
        ? T
        : never;


NONNULLABLE
================================

type MyNonNullable<T> =
    T extends null | undefined
        ? never
        : T;


INFER
================================

T extends SomeType<infer U>
    ? U
    : never;


ARRAY ELEMENT
================================

type Element<T> =
    T extends readonly (infer U)[]
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


PROMISE VALUE
================================

type PromiseValue<T> =
    T extends Promise<infer U>
        ? U
        : T;


PROPERTY EXTRACTION
================================

type DataType<T> =
    T extends {
        data: infer D;
    }
        ? D
        : never;


FILTER PROPERTIES
================================

type StringProperties<T> = {
    [K in keyof T as
        T[K] extends string
            ? K
            : never
    ]:
        T[K];
};


KEYS BY VALUE TYPE
================================

type KeysOfType<T, V> = {
    [K in keyof T]:
        T[K] extends V
            ? K
            : never;
}[keyof T];


RECURSIVE FLATTEN
================================

type Flatten<T> =
    T extends readonly (infer U)[]
        ? Flatten<U>
        : T;


IS NEVER
================================

type IsNever<T> =
    [T] extends [never]
        ? true
        : false;


CORE MODEL
================================

Input Type
    │
    ▼
T extends U ?
   /      \
 YES      NO
  │        │
  ▼        ▼
  X        Y
```

---

# 106. Important Rules to Remember

```text
1. Conditional types make decisions at the type level.

2. Basic syntax is T extends U ? X : Y.

3. Read extends as an assignability check.

4. Conditional types are compile-time only.

5. They do not create runtime if statements.

6. They disappear after TypeScript compilation.

7. Either branch can return any valid type.

8. Conditional types can be generic.

9. Conditional types can be nested.

10. Nested conditionals can model multiple type categories.

11. Avoid excessive nesting when simpler models exist.

12. Literal types can satisfy broader primitive types.

13. "hello" extends string is true.

14. 10 extends number is true.

15. Structural typing applies to object checks.

16. Objects can have additional properties and still match a required shape.

17. T extends object is broader than plain objects.

18. Arrays satisfy object.

19. Functions satisfy object.

20. Date satisfies object.

21. Conditional types can inspect function shapes.

22. Conditional types can inspect array shapes.

23. Conditional types can inspect promise shapes.

24. Conditional types can inspect object properties.

25. Conditional types can transform values based on their types.

26. Conditional types can combine with mapped types.

27. Conditional types can combine with keyof.

28. Conditional types can combine with indexed access types.

29. Conditional types can combine with template literal types.

30. Conditional types can be recursive.

31. Recursive types have compiler depth limits.

32. Conditional types may distribute over unions.

33. Distribution occurs with naked generic type parameters.

34. T extends U may distribute.

35. Distribution processes each union member separately.

36. Results are recombined into a union.

37. ToArray<string | number> may become string[] | number[].

38. string[] | number[] differs from (string | number)[].

39. Tuple wrapping can prevent distribution.

40. [T] extends [U] checks the union as a whole.

41. Distribution is useful for union filtering.

42. Distribution is useful for union transformations.

43. Distribution is not always desired.

44. Know whether you want member-wise or whole-union behavior.

45. never represents impossible values.

46. never disappears from unions.

47. Returning never can filter union members.

48. Exclude uses conditional-type behavior.

49. Extract uses conditional-type behavior.

50. NonNullable removes null and undefined.

51. Prefer built-in utilities in application code.

52. Reimplement utilities mainly for learning or custom behavior.

53. infer extracts types from matching structures.

54. infer is used inside conditional types.

55. infer can extract array element types.

56. infer can extract function return types.

57. infer can extract function parameters.

58. infer can extract Promise values.

59. infer can extract object property types.

60. infer can extract tuple members.

61. infer can extract multiple types simultaneously.

62. ReturnType uses conditional/inference concepts.

63. Parameters uses conditional/inference concepts.

64. Awaited performs async type unwrapping.

65. Prefer Awaited over naive Promise utilities when appropriate.

66. infer does not create runtime variables.

67. Inferred type variables exist only in the type system.

68. Conditional types can filter mapped-type properties.

69. Mapping a key to never removes it.

70. T[K] can be tested inside mapped types.

71. Properties can be filtered by value type.

72. Keys can be extracted by value type.

73. Property values can be transformed conditionally.

74. Date properties can be represented as strings in DTO types.

75. Type transformation does not perform runtime serialization.

76. Type transformation does not validate runtime data.

77. API data still requires runtime handling.

78. External input still requires runtime validation.

79. Recursive transformations need special care.

80. Arrays need special handling in many recursive utilities.

81. Functions may require special handling.

82. Date may require special handling.

83. Map and Set may require special handling.

84. Promise may require special handling.

85. any behaves specially in conditional types.

86. Avoid any when unknown better represents uncertainty.

87. never behaves specially in distributive conditional types.

88. T extends never is not a reliable never check.

89. [T] extends [never] can test never.

90. Conditional types can model generic React APIs.

91. Conditional types can model form field value relationships.

92. Conditional types can model API method relationships.

93. Conditional types can model service result relationships.

94. Conditional types can model reusable library APIs.

95. Discriminated unions may be clearer for fixed application states.

96. Function overloads may be clearer for some runtime APIs.

97. Conditional types are useful when output depends systematically on input.

98. Do not use conditional types when a simple union is enough.

99. Do not use conditional types solely to make types look advanced.

100. Type complexity should provide real safety or reuse.

101. Separate complex transformations into helper types.

102. Give advanced utilities meaningful names.

103. Keep type-level intent readable.

104. Use built-in utilities where they clearly express intent.

105. Conditional types are one of the foundations of advanced TypeScript.

106. Mapped types answer how properties transform.

107. Conditional types answer which type should be selected.

108. infer answers what type exists inside another type.

109. never helps remove unwanted possibilities.

110. Distribution allows union members to be processed individually.

111. Tuple wrapping allows unions to be processed as a whole.

112. The core syntax to remember is:

T extends U ? X : Y
```

---

# 107. Final Mental Model

Start with:

```typescript
type Check<T> =
    T extends string
        ? "STRING"
        : "OTHER";
```

For:

```typescript
Check<string>
```

TypeScript evaluates:

```text
string
   │
   ▼
string extends string?
   │
  YES
   │
   ▼
"STRING"
```

For:

```typescript
Check<number>
```

```text
number
   │
   ▼
number extends string?
   │
   NO
   │
   ▼
"OTHER"
```

For a union:

```typescript
Check<
    string | number
>
```

TypeScript may distribute:

```text
string | number
      │
      ▼
 ┌─────────────┐
 │             │
 ▼             ▼
string       number
 │             │
 ▼             ▼
"STRING"    "OTHER"
 │             │
 └──────┬──────┘
        ▼

"STRING" | "OTHER"
```

Conditional types can also filter:

```typescript
type OnlyStrings<T> =
    T extends string
        ? T
        : never;
```

```text
string | number | boolean
        │
        ▼
 ┌──────┼────────┐
 ▼      ▼        ▼
string number  boolean
 │      │        │
 ▼      ▼        ▼
string never   never
   \     |      /
    \    |     /
     └───┬────┘
         ▼

       string
```

And `infer` allows us to extract types:

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

```text
string[]
    │
    ▼
Array<string>
    │
    ▼
Array<infer U>
    │
    ▼
U = string
    │
    ▼
string
```

So remember these four ideas:

```typescript
T extends U
    ? X
    : Y
```

```text
Conditional decision
```

```typescript
T extends U
    ? T
    : never
```

```text
Union filtering
```

```typescript
T extends SomeType<infer U>
    ? U
    : never
```

```text
Type extraction
```

```typescript
[T] extends [U]
    ? X
    : Y
```

```text
Prevent union distribution
```

Together:

```text
                    Input Type
                        │
                        ▼
                 Conditional Type
                        │
             T extends U ? X : Y
                        │
            ┌───────────┴───────────┐
            ▼                       ▼

       Type Selection          Union Handling
            │                       │
            ▼                       ▼

      Choose X or Y          Distribute / Prevent
            │                       │
            └───────────┬───────────┘
                        ▼

                       infer
                        │
                        ▼
                Extract Inner Types
                        │
                        ▼
                      never
                        │
                        ▼
                Filter Possibilities
```

Conditional types are where TypeScript starts behaving like a small **type transformation language** rather than simply a system for declaring variable types.
