# Type Aliases & Interfaces

TypeScript allows us to give **names to types and object contracts**.

The two primary mechanisms are:

```typescript
type
```

and:

```typescript
interface
```

Example:

```typescript
type User = {
    id: number;
    name: string;
};
```

or:

```typescript
interface User {
    id: number;
    name: string;
}
```

Both can describe the shape of a user.

But they are **not identical**.

Understanding their similarities and differences is important when building real TypeScript applications.

---

# 1. Why Named Types Exist

Without a named type:

```typescript
const user: {
    id: number;
    name: string;
    email: string;
} = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};
```

If another function needs the same structure:

```typescript
function printUser(user: {
    id: number;
    name: string;
    email: string;
}) {
    console.log(user.name);
}
```

We repeat the structure.

Instead:

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Now:

```typescript
const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};
```

and:

```typescript
function printUser(
    user: User
): void {
    console.log(user.name);
}
```

The contract is reusable.

---

# 2. What Is a Type Alias?

A type alias creates a **name for a type**.

Syntax:

```typescript
type TypeName = Type;
```

Example:

```typescript
type Username = string;
```

Now:

```typescript
let username: Username =
    "Alice";
```

`Username` is an alias for:

```typescript
string
```

---

# 3. Type Alias Does Not Create a Runtime Value

This:

```typescript
type UserId = number;
```

exists only during TypeScript type checking.

You cannot do:

```typescript
console.log(UserId);
```

because `UserId` does not exist at runtime.

After compilation:

```typescript
type UserId = number;
```

disappears.

---

# 4. Type Aliases for Primitive Types

```typescript
type UserId = number;

type Username = string;

type IsActive = boolean;
```

Usage:

```typescript
const id: UserId = 101;

const username: Username =
    "Alice";

const active: IsActive =
    true;
```

This can improve domain meaning.

Compare:

```typescript
function findUser(
    id: number
) {}
```

with:

```typescript
type UserId = number;

function findUser(
    id: UserId
) {}
```

The second communicates what the number represents.

---

# 5. Aliases Do Not Create Distinct Primitive Types

This is important.

```typescript
type UserId = number;

type ProductId = number;
```

TypeScript does not automatically treat them as completely separate nominal types.

For example:

```typescript
const userId: UserId = 1;

const productId: ProductId =
    userId;
```

This is allowed because both aliases ultimately describe:

```typescript
number
```

Type aliases improve meaning and reuse, but do not automatically create nominal identity.

---

# 6. Type Aliases for Objects

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};
```

Every required property must be present.

---

# 7. Optional Properties

```typescript
type User = {
    id: number;
    name: string;
    phone?: string;
};
```

Valid:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

Also valid:

```typescript
const user: User = {
    id: 1,
    name: "Alice",
    phone: "1234567890"
};
```

---

# 8. Readonly Properties

```typescript
type User = {
    readonly id: number;
    name: string;
};
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

Allowed:

```typescript
user.name = "Bob";
```

Rejected:

```typescript
user.id = 2;
```

The `readonly` behavior is exactly what we covered previously.

---

# 9. Type Aliases for Union Types

This is one major advantage of `type`.

```typescript
type Status =
    "idle"
    | "loading"
    | "success"
    | "error";
```

Usage:

```typescript
let status: Status =
    "loading";
```

Invalid:

```typescript
status = "banana";
```

---

# 10. Type Aliases for Primitive Unions

```typescript
type Id =
    string | number;
```

Usage:

```typescript
let id: Id;

id = 101;

id = "USR-101";
```

---

# 11. Type Aliases for Tuples

```typescript
type Coordinates = [
    number,
    number
];
```

Usage:

```typescript
const location:
    Coordinates = [
        10,
        20
    ];
```

Named tuple:

```typescript
type Coordinates = [
    latitude: number,
    longitude: number
];
```

---

# 12. Type Aliases for Function Types

Instead of:

```typescript
let add:
    (
        a: number,
        b: number
    ) => number;
```

create:

```typescript
type AddFunction = (
    a: number,
    b: number
) => number;
```

Then:

```typescript
const add: AddFunction =
    (a, b) => {
        return a + b;
    };
```

---

# 13. Function Type Alias Example

```typescript
type Formatter = (
    value: string
) => string;
```

Usage:

```typescript
const uppercase:
    Formatter =
    (value) => {
        return value.toUpperCase();
    };
```

TypeScript already knows:

```text
value
→ string

return
→ string
```

from the alias.

---

# 14. Type Aliases Can Reference Other Types

```typescript
type UserId = number;

type User = {
    id: UserId;
    name: string;
};
```

You can build larger domain types from smaller reusable types.

---

# 15. Type Aliases with Intersections

Suppose:

```typescript
type User = {
    id: number;
    name: string;
};

type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};
```

Combine them:

```typescript
type StoredUser =
    User & Timestamped;
```

Now `StoredUser` requires both contracts.

---

# 16. Type Aliases with Discriminated Unions

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

This is one of the most common uses of `type`.

---

# 17. Type Aliases Can Represent Almost Any Type

A `type` alias can represent:

```text
Primitive

Object

Union

Intersection

Tuple

Function

Literal

Array

Generic Type

Mapped Type

Conditional Type
```

Examples:

```typescript
type Name = string;

type Id =
    string | number;

type Point =
    [number, number];

type Handler =
    () => void;

type User = {
    id: number;
};

type Combined =
    A & B;
```

This flexibility is one of the main strengths of `type`.

---

# 18. What Is an Interface?

An `interface` defines a contract, most commonly for an object-like structure.

Syntax:

```typescript
interface User {
    id: number;
    name: string;
}
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

---

# 19. Interface Properties

```typescript
interface User {
    id: number;
    username: string;
    email: string;
    active: boolean;
}
```

Usage:

```typescript
const user: User = {
    id: 1,
    username: "alice",
    email: "alice@example.com",
    active: true
};
```

---

# 20. Optional Interface Properties

```typescript
interface User {
    id: number;
    name: string;
    phone?: string;
}
```

`phone` may be omitted.

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

---

# 21. Readonly Interface Properties

```typescript
interface User {
    readonly id: number;
    name: string;
}
```

Then:

```typescript
user.name = "Bob";
```

is allowed.

But:

```typescript
user.id = 2;
```

is rejected.

---

# 22. Methods in Interfaces

Interfaces can describe methods.

```typescript
interface User {
    id: number;
    name: string;

    greet(): string;
}
```

Implementation:

```typescript
const user: User = {
    id: 1,
    name: "Alice",

    greet() {
        return `Hello ${this.name}`;
    }
};
```

---

# 23. Method Syntax vs Function Property

These look similar:

```typescript
interface User {
    greet(): string;
}
```

and:

```typescript
interface User {
    greet: () => string;
}
```

Both describe callable members, but they are not completely identical in all TypeScript variance scenarios.

For ordinary application modeling, both are common.

Method syntax:

```typescript
greet(): string;
```

is especially natural when modeling object methods.

Function-property syntax:

```typescript
greet: () => string;
```

is common for callbacks and function-valued properties.

---

# 24. Interfaces Can Reference Other Types

```typescript
type UserId = number;

interface User {
    id: UserId;
    name: string;
}
```

`type` and `interface` can work together.

They are not competing systems.

---

# 25. Extending Interfaces

Interfaces can inherit from other interfaces using:

```typescript
extends
```

Example:

```typescript
interface Person {
    name: string;
}

interface Employee
    extends Person {

    employeeId: number;
}
```

Now:

```typescript
const employee: Employee = {
    name: "Alice",
    employeeId: 101
};
```

`Employee` contains:

```text
name
employeeId
```

---

# 26. Interface Extension Mental Model

```text
Person
│
├── name
│
▼
Employee
│
├── name
└── employeeId
```

`Employee` must satisfy the inherited `Person` contract plus its own contract.

---

# 27. Multiple Interface Inheritance

An interface can extend multiple interfaces.

```typescript
interface Identifiable {
    id: string;
}

interface Timestamped {
    createdAt: Date;
    updatedAt: Date;
}

interface User
    extends
        Identifiable,
        Timestamped {

    username: string;
}
```

Now `User` requires:

```text
id
createdAt
updatedAt
username
```

---

# 28. Interface Extension vs Intersection

Using interfaces:

```typescript
interface Person {
    name: string;
}

interface Employee
    extends Person {

    employeeId: number;
}
```

Using type aliases:

```typescript
type Person = {
    name: string;
};

type Employee =
    Person & {
        employeeId: number;
    };
```

Both can model similar structures.

---

# 29. Interfaces Can Extend Type Aliases

If a type alias represents a statically known object structure, an interface can extend it.

```typescript
type Person = {
    name: string;
};

interface Employee
    extends Person {

    employeeId: number;
}
```

This is valid.

---

# 30. Type Aliases Can Use Interfaces

```typescript
interface Person {
    name: string;
}

type Employee =
    Person & {
        employeeId: number;
    };
```

Also valid.

Again:

```text
type and interface
can work together.
```

---

# 31. Interfaces Are Open

One of the most important differences is that interfaces support:

```text
Declaration Merging
```

Example:

```typescript
interface User {
    id: number;
}
```

Later:

```typescript
interface User {
    name: string;
}
```

TypeScript merges them.

The resulting contract is effectively:

```typescript
interface User {
    id: number;
    name: string;
}
```

---

# 32. Interface Declaration Merging

Example:

```typescript
interface Settings {
    theme: string;
}
```

Later:

```typescript
interface Settings {
    language: string;
}
```

Now:

```typescript
const settings:
    Settings = {

    theme: "dark",
    language: "en"
};
```

Both properties are required.

---

# 33. Type Aliases Cannot Be Redeclared

This:

```typescript
type User = {
    id: number;
};
```

followed by:

```typescript
type User = {
    name: string;
};
```

causes a duplicate identifier error.

Type aliases are **closed after declaration**.

---

# 34. Open vs Closed Mental Model

```text
interface
================================

Open

Can participate in
declaration merging.


type
================================

Closed

Cannot redeclare the
same alias and merge it.
```

This difference is especially important in library definitions and type augmentation.

---

# 35. Why Declaration Merging Exists

Declaration merging can be useful when extending existing type declarations.

For example, libraries and frameworks may expose interfaces designed to be augmented.

Conceptually:

```typescript
interface FrameworkConfig {
    existing: string;
}
```

Application augmentation:

```typescript
interface FrameworkConfig {
    customFeature: boolean;
}
```

Result:

```text
FrameworkConfig

existing
customFeature
```

This is a major reason interfaces are important even though type aliases are very flexible.

---

# 36. Declaration Merging Can Also Cause Problems

Suppose two files declare:

```typescript
interface User {
    id: number;
}
```

and:

```typescript
interface User {
    email: string;
}
```

They may merge when that was not intended.

So openness is both:

```text
a feature

and

something to understand carefully
```

---

# 37. Type Alias for Union

This works:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

An interface cannot directly represent that same union:

```typescript
interface Status =
    "loading" | "success";
```

Invalid syntax.

This is a major reason `type` is required.

---

# 38. Type Alias for Tuple

```typescript
type Coordinates = [
    number,
    number
];
```

This is natural with `type`.

Interfaces are primarily intended for object-like contracts rather than being the general syntax for arbitrary tuple aliases.

---

# 39. Type Alias for Primitive

```typescript
type UserId = string;
```

An interface cannot directly alias:

```typescript
string
```

So this is invalid:

```typescript
interface UserId = string;
```

---

# 40. Type Alias for Literal

```typescript
type Environment =
    "development"
    | "testing"
    | "production";
```

Again, this is a job for:

```typescript
type
```

---

# 41. Interface for Object Contracts

Interfaces are especially natural for:

```text
Domain objects

Class contracts

Service contracts

Configuration objects

Library extension points

Public object APIs
```

Example:

```typescript
interface User {
    id: string;
    username: string;
    email: string;
}
```

---

# 42. Type for Flexible Composition

`type` is especially natural when modeling:

```text
Unions

Intersections

Tuples

Primitive aliases

Literal unions

Function types

Complex derived types
```

Example:

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

---

# 43. Type vs Interface — Basic Object

These are very similar:

```typescript
type User = {
    id: number;
    name: string;
};
```

and:

```typescript
interface User {
    id: number;
    name: string;
}
```

For a simple object contract, either can often work.

---

# 44. Type vs Interface — Extension

Interface:

```typescript
interface Person {
    name: string;
}

interface Employee
    extends Person {

    employeeId: number;
}
```

Type:

```typescript
type Person = {
    name: string;
};

type Employee =
    Person & {
        employeeId: number;
    };
```

Conceptually both can produce:

```text
name
employeeId
```

---

# 45. Extension Conflicts

Suppose:

```typescript
interface A {
    id: string;
}
```

Trying to extend it incompatibly:

```typescript
interface B extends A {
    id: number;
}
```

TypeScript reports a conflict because `B` cannot correctly extend `A` while changing `id` incompatibly.

This tends to make extension conflicts visible directly.

---

# 46. Intersection Conflicts

With aliases:

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

The `id` requirement becomes:

```text
string & number
→ never
```

as covered in the previous chapter.

This is an important behavioral difference between:

```text
interface extends
```

and:

```text
type intersection
```

when conflicts occur.

---

# 47. Structural Typing

TypeScript uses a primarily **structural type system**.

That means compatibility depends mainly on the shape of a value, not necessarily the declared name of its type.

Example:

```typescript
interface User {
    id: number;
    name: string;
}
```

Now:

```typescript
const person = {
    id: 1,
    name: "Alice"
};
```

This works:

```typescript
const user: User =
    person;
```

`person` satisfies the required structure.

It does not need to explicitly declare:

```text
"I implement User."
```

---

# 48. Structural Typing with Type Aliases

Same behavior:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
const person = {
    id: 1,
    name: "Alice"
};

const user: User =
    person;
```

works because the shape is compatible.

---

# 49. Extra Properties and Structural Typing

Consider:

```typescript
interface User {
    id: number;
    name: string;
}
```

Then:

```typescript
const employee = {
    id: 1,
    name: "Alice",
    department: "Engineering"
};
```

This can be assigned:

```typescript
const user: User =
    employee;
```

because `employee` contains everything `User` requires.

The extra `department` property does not automatically make it incompatible.

---

# 50. Excess Property Checking

But direct object literals receive additional checking.

```typescript
interface User {
    id: number;
    name: string;
}
```

This can produce an error:

```typescript
const user: User = {
    id: 1,
    name: "Alice",
    department: "Engineering"
};
```

because `department` is not part of the known `User` contract.

This behavior is called:

```text
Excess Property Checking
```

---

# 51. Why the Previous Assignment Can Work

This:

```typescript
const employee = {
    id: 1,
    name: "Alice",
    department: "Engineering"
};

const user: User =
    employee;
```

can work because TypeScript checks whether `employee` structurally satisfies `User`.

It does.

But a fresh object literal assigned directly to `User` receives stricter excess-property checking.

---

# 52. Excess Property Checking Is Not Exact Object Typing

Do not assume:

```typescript
interface User {
    id: number;
    name: string;
}
```

means:

```text
This object can NEVER contain
any additional properties.
```

TypeScript is structurally typed.

Excess-property checking is an additional check applied in certain contexts, especially fresh object literals.

---

# 53. Interfaces and Classes

Classes can implement interfaces.

```typescript
interface Printable {
    print(): void;
}
```

Class:

```typescript
class Report
    implements Printable {

    print(): void {
        console.log(
            "Printing report"
        );
    }
}
```

The class must satisfy the interface contract.

---

# 54. Multiple Interface Implementation

```typescript
interface Printable {
    print(): void;
}

interface Savable {
    save(): void;
}
```

Class:

```typescript
class Document
    implements
        Printable,
        Savable {

    print(): void {
        console.log("Print");
    }

    save(): void {
        console.log("Save");
    }
}
```

The class must satisfy both interfaces.

---

# 55. Interfaces Describe Contracts, Not Implementation

```typescript
interface Repository {
    save(): void;
}
```

The interface says:

```text
Any Repository must
provide save()
```

It does not specify how `save()` works.

Different classes can implement it differently.

---

# 56. Type Aliases Can Also Describe Class Contracts

A class can implement an object type alias too.

```typescript
type Printable = {
    print(): void;
};

class Report
    implements Printable {

    print(): void {
        console.log("Print");
    }
}
```

So:

```text
implements
```

is not exclusive to interfaces.

The type must represent an appropriate object-like contract.

---

# 57. Interface Function Signature

Interfaces can describe callable objects/functions.

```typescript
interface Add {
    (
        a: number,
        b: number
    ): number;
}
```

Usage:

```typescript
const add: Add =
    (a, b) => a + b;
```

However, a type alias is often simpler for ordinary function types:

```typescript
type Add = (
    a: number,
    b: number
) => number;
```

---

# 58. Interface Index Signatures

Interfaces can describe dynamic property keys.

```typescript
interface Scores {
    [username: string]:
        number;
}
```

Usage:

```typescript
const scores: Scores = {
    Alice: 95,
    Bob: 88,
    Charlie: 91
};
```

Every string-keyed property must have a numeric value.

---

# 59. Type Alias Index Signatures

Type aliases can do the same:

```typescript
type Scores = {
    [username: string]:
        number;
};
```

So index signatures are not unique to interfaces.

---

# 60. Interface with Known and Dynamic Properties

```typescript
interface Scores {
    total: number;

    [username: string]:
        number;
}
```

This works because:

```text
total
→ number
```

matches the index signature's value type.

But this would conflict:

```typescript
interface Scores {
    title: string;

    [username: string]:
        number;
}
```

because `title` is also a string-keyed property but its value is `string`, not `number`.

---

# 61. Nested Interfaces

```typescript
interface Address {
    city: string;
    country: string;
}

interface User {
    id: number;
    name: string;
    address: Address;
}
```

Usage:

```typescript
const user: User = {
    id: 1,
    name: "Alice",

    address: {
        city: "Kolkata",
        country: "India"
    }
};
```

Named contracts make complex structures easier to read.

---

# 62. Nested Type Aliases

Same concept:

```typescript
type Address = {
    city: string;
    country: string;
};

type User = {
    id: number;
    name: string;
    address: Address;
};
```

There is no major difference here for ordinary object modeling.

---

# 63. Recursive Interfaces

Interfaces can reference themselves.

```typescript
interface TreeNode {
    value: string;
    children: TreeNode[];
}
```

Usage:

```typescript
const root: TreeNode = {
    value: "root",

    children: [
        {
            value: "child",
            children: []
        }
    ]
};
```

---

# 64. Recursive Type Aliases

Type aliases can also model recursive structures.

```typescript
type TreeNode = {
    value: string;
    children: TreeNode[];
};
```

Modern TypeScript supports many useful recursive alias patterns.

---

# 65. Generic Interfaces

Interfaces can accept generic type parameters.

```typescript
interface ApiResponse<T> {
    data: T;
    success: boolean;
}
```

Usage:

```typescript
interface User {
    id: number;
    name: string;
}

const response:
    ApiResponse<User> = {

    data: {
        id: 1,
        name: "Alice"
    },

    success: true
};
```

We will cover generics in detail later.

---

# 66. Generic Type Aliases

Type aliases can also be generic.

```typescript
type ApiResponse<T> = {
    data: T;
    success: boolean;
};
```

Both approaches work.

---

# 67. Generic Alias Beyond Objects

Because `type` can represent arbitrary types, generic aliases can do things interfaces cannot express directly as aliases.

Example:

```typescript
type Nullable<T> =
    T | null;
```

Usage:

```typescript
type NullableUser =
    Nullable<User>;
```

Result:

```text
User | null
```

---

# 68. React Props with Interface

Later in React:

```typescript
interface ButtonProps {
    label: string;
    disabled?: boolean;
}
```

Component:

```typescript
function Button(
    props: ButtonProps
) {
    return (
        <button
            disabled={
                props.disabled
            }
        >
            {props.label}
        </button>
    );
}
```

Interfaces are commonly used for component props.

---

# 69. React Props with Type

This is equally valid:

```typescript
type ButtonProps = {
    label: string;
    disabled?: boolean;
};
```

Then:

```typescript
function Button(
    props: ButtonProps
) {
    // ...
}
```

Both styles are common in React codebases.

---

# 70. React Props with Union Variants

`type` becomes especially useful when props have mutually exclusive variants.

Example:

```typescript
type ButtonProps =
    | {
        variant: "link";
        href: string;
    }
    | {
        variant: "button";
        onClick: () => void;
    };
```

Now:

```text
link
→ requires href

button
→ requires onClick
```

This relationship is naturally modeled using a union alias.

---

# 71. API Domain Model

```typescript
interface User {
    id: string;
    username: string;
    email: string;
}
```

Then:

```typescript
interface CreateUserRequest {
    username: string;
    email: string;
}
```

and:

```typescript
interface UserResponse {
    user: User;
}
```

Interfaces work well for stable object contracts.

---

# 72. API Result Union

For success/failure states:

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

This is naturally a type alias because the overall type is a union.

---

# 73. Using Both Together

Real projects commonly use both.

```typescript
interface User {
    id: string;
    name: string;
}

interface Product {
    id: string;
    name: string;
}

type SearchResult =
    User | Product;

type RequestStatus =
    "idle"
    | "loading"
    | "success"
    | "error";
```

There is no requirement to choose one syntax for the entire project.

---

# 74. A Practical Convention

One reasonable convention is:

```text
interface
→ stable object contracts

type
→ unions, tuples, functions,
   primitives, compositions,
   derived types
```

Example:

```typescript
interface User {
    id: string;
    name: string;
}
```

and:

```typescript
type UserState =
    | {
        status: "loading";
    }
    | {
        status: "success";
        user: User;
    }
    | {
        status: "error";
        message: string;
    };
```

This is a convention, not a TypeScript requirement.

---

# 75. Another Valid Convention

Some teams simply use:

```typescript
type
```

for almost everything.

Example:

```typescript
type User = {
    id: string;
    name: string;
};
```

This is also valid.

Consistency within a codebase matters more than arguing that one syntax must always be used.

---

# 76. When Interface Is Especially Useful

Consider `interface` when:

```text
You are modeling object contracts

The contract may be extended

Declaration merging is intentionally useful

You are defining library extension points

You are describing class contracts

The codebase convention prefers interfaces
```

---

# 77. When Type Is Especially Useful

Use `type` when you need:

```text
Primitive alias

Union

Intersection

Tuple

Literal union

Function alias

Conditional type

Mapped type

Complex type composition
```

Example:

```typescript
type Role =
    "admin"
    | "user"
    | "manager";
```

This cannot be replaced directly by an equivalent interface declaration.

---

# 78. Do Not Choose Based on Performance

There is usually no runtime performance question between:

```typescript
type User = {
    name: string;
};
```

and:

```typescript
interface User {
    name: string;
}
```

Both are TypeScript type-level constructs.

They disappear from emitted JavaScript.

Choose based on:

```text
Semantics

Capabilities

Readability

Project conventions
```

not runtime speed.

---

# 79. Type Alias Does Not Copy a Type

Consider:

```typescript
type Name = string;
```

Do not imagine TypeScript creating a new runtime datatype.

It simply creates a type-level name.

Likewise:

```typescript
type User = {
    id: number;
};
```

does not create:

```text
constructor

class

runtime validator

object
```

It creates a compile-time type alias.

---

# 80. Interface Does Not Create an Object

This:

```typescript
interface User {
    id: number;
    name: string;
}
```

does not create an object.

You still need:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

The interface describes what compatible values must look like.

---

# 81. Neither Performs Runtime Validation

Suppose external data is:

```typescript
const data =
    JSON.parse(input);
```

Declaring:

```typescript
const user: User =
    data;
```

does not magically validate the runtime data if `data` is typed as `any`.

Types and interfaces disappear at runtime.

For external data, runtime validation may still be necessary.

---

# 82. TypeScript Contracts Are Compile-Time Contracts

Think:

```text
Type / Interface
        │
        ▼
TypeScript Compiler
        │
        ▼
Checks Compatibility
        │
        ▼
JavaScript Output

Type declarations disappear.
```

They provide development-time safety.

---

# 83. Naming Conventions

Prefer:

```typescript
interface User {
    // ...
}

type UserId = string;

type RequestStatus =
    "loading"
    | "success"
    | "error";
```

Avoid old-style prefixes such as:

```typescript
interface IUser {
    // ...
}
```

unless the project's existing convention specifically requires them.

Modern TypeScript projects generally use descriptive names directly.

---

# 84. Good Type Names

Prefer names representing domain concepts:

```text
User

Product

Order

ApiResponse

RequestStatus

UserId

ButtonProps

AuthState

PaymentMethod
```

Avoid vague names such as:

```text
Data

Thing

ObjectData

Stuff

Info
```

unless those names genuinely represent the domain concept.

---

# 85. Avoid Redundant Aliases

This:

```typescript
type StringType =
    string;
```

usually adds no useful meaning.

But:

```typescript
type UserId =
    string;
```

communicates domain intent.

The alias should make the code easier to understand.

---

# 86. Avoid Giant Interfaces

Instead of one enormous contract:

```typescript
interface User {
    id: string;
    name: string;
    email: string;
    city: string;
    country: string;
    companyName: string;
    companyId: string;
    // 50 more properties
}
```

consider meaningful nested domain structures:

```typescript
interface Address {
    city: string;
    country: string;
}

interface Company {
    id: string;
    name: string;
}

interface User {
    id: string;
    name: string;
    email: string;
    address: Address;
    company: Company;
}
```

Do this when those nested concepts are meaningful in the domain.

---

# 87. Avoid Unnecessary Fragmentation

The opposite extreme is also bad.

Avoid creating:

```typescript
type UserName = string;

type UserEmail = string;

type UserCity = string;

type UserCountry = string;
```

for every property without a meaningful reason.

Types should improve:

```text
Safety

Meaning

Reuse

Readability
```

not merely increase the number of declarations.

---

# 88. Interface Composition Example

```typescript
interface BaseEntity {
    id: string;
}

interface Auditable {
    createdAt: Date;
    updatedAt: Date;
}

interface User
    extends
        BaseEntity,
        Auditable {

    username: string;
    email: string;
}
```

Result:

```text
User
│
├── id
├── createdAt
├── updatedAt
├── username
└── email
```

---

# 89. Type Composition Example

```typescript
type BaseEntity = {
    id: string;
};

type Auditable = {
    createdAt: Date;
    updatedAt: Date;
};

type User =
    BaseEntity
    & Auditable
    & {
        username: string;
        email: string;
    };
```

Result is conceptually similar.

---

# 90. Interface Hierarchy Example

```typescript
interface Animal {
    name: string;
}

interface Pet
    extends Animal {

    ownerName: string;
}

interface Dog
    extends Pet {

    breed: string;
}
```

A `Dog` requires:

```text
name
ownerName
breed
```

Inheritance can represent meaningful relationships.

---

# 91. Avoid Deep Interface Hierarchies Without Reason

This:

```text
Entity
  ↓
NamedEntity
  ↓
OwnedEntity
  ↓
AuditedOwnedEntity
  ↓
UserEntity
  ↓
AdminUserEntity
```

can become difficult to understand.

Composition or a simpler contract may be clearer.

Do not create inheritance simply because `extends` exists.

---

# 92. Interface Declaration Merging Example

First declaration:

```typescript
interface WindowConfig {
    theme: string;
}
```

Second declaration:

```typescript
interface WindowConfig {
    language: string;
}
```

Result:

```typescript
const config:
    WindowConfig = {

    theme: "dark",
    language: "en"
};
```

Both declarations contribute to the same interface.

---

# 93. Declaration Merging Requires Compatible Members

Suppose:

```typescript
interface User {
    id: string;
}
```

Then:

```typescript
interface User {
    id: number;
}
```

is not a valid way to redefine `id`.

Merged declarations must remain compatible according to TypeScript's declaration-merging rules.

---

# 94. Extending Multiple Interfaces with Conflicts

Suppose:

```typescript
interface A {
    id: string;
}

interface B {
    id: number;
}
```

Trying:

```typescript
interface C
    extends A, B {}
```

causes a conflict because `id` is incompatible between the parent contracts.

This catches contradictory inheritance.

---

# 95. Interface vs Type Comparison

| Feature                 |                                    `interface` |                                 `type` |
| ----------------------- | ---------------------------------------------: | -------------------------------------: |
| Object shapes           |                                            Yes |                                    Yes |
| Optional properties     |                                            Yes |                                    Yes |
| `readonly` properties   |                                            Yes |                                    Yes |
| Methods                 |                                            Yes |                                    Yes |
| Function contracts      |                                            Yes |                                    Yes |
| Index signatures        |                                            Yes |                                    Yes |
| Generics                |                                            Yes |                                    Yes |
| Extend object contracts |                                      `extends` |                                    `&` |
| Multiple composition    |                                            Yes |                                    Yes |
| Primitive alias         |                                             No |                                    Yes |
| Literal alias           |                                             No |                                    Yes |
| Union alias             |                                             No |                                    Yes |
| Tuple alias             |                Not the general alias mechanism |                                    Yes |
| Intersection alias      |                         No direct alias syntax |                                    Yes |
| Conditional types       |                                             No |                                    Yes |
| Mapped types            | Use type aliases for direct mapped-type syntax |                                    Yes |
| Declaration merging     |                                            Yes |                                     No |
| Class `implements`      |                                            Yes | Yes, for appropriate object-like types |

---

# 96. Practical Decision Tree

```text
Need a named type?
        │
        ▼

Is it a union, primitive,
tuple, literal, function,
conditional or mapped type?
        │
       YES
        │
        ▼
       type


Otherwise:
object contract
        │
        ▼

Need declaration merging
or intentional interface
extension?
        │
       YES
        │
        ▼
    interface


Otherwise:
        │
        ▼
type OR interface

Follow project convention.
```

---

# 97. Common Mistake — Thinking Interface Is Always Better for Objects

This is perfectly valid:

```typescript
type User = {
    id: number;
    name: string;
};
```

You do not need to convert every object type to an interface.

---

# 98. Common Mistake — Thinking Type Is Always Better

Interfaces have capabilities such as:

```text
Declaration merging

Natural extension syntax

Library augmentation patterns
```

So there are cases where `interface` is specifically useful.

---

# 99. Common Mistake — Using Interface for a Union

Do not try:

```typescript
interface Status =
    "loading"
    | "success";
```

Use:

```typescript
type Status =
    "loading"
    | "success";
```

---

# 100. Common Mistake — Expecting Runtime Validation

This:

```typescript
interface User {
    id: number;
}
```

does not validate an API response.

Neither does:

```typescript
type User = {
    id: number;
};
```

Runtime data still needs runtime validation when trust cannot be assumed.

---

# 101. Common Mistake — Assuming Alias Means New Runtime Type

```typescript
type UserId = string;
```

does not create a new JavaScript datatype.

It is still structurally based on:

```typescript
string
```

---

# 102. Common Mistake — Using `I` Prefix Everywhere

Avoid automatically writing:

```typescript
interface IUser {}
interface IProduct {}
interface IOrder {}
```

unless the project's established naming convention requires it.

Prefer:

```typescript
interface User {}
interface Product {}
interface Order {}
```

---

# 103. Common Mistake — Duplicate Interface Names

Because interfaces can merge, accidentally reusing a name can produce unexpected contracts.

Understand the scope and module where declarations exist.

Declaration merging should be intentional.

---

# 104. Common Mistake — Excessive Inheritance

Avoid:

```text
A extends B
B extends C
C extends D
D extends E
```

unless the hierarchy represents a meaningful domain relationship.

Simple composition is often easier to maintain.

---

# 105. Common Mistake — Repeating Object Structures

Avoid:

```typescript
function createUser(user: {
    id: string;
    name: string;
}) {}

function deleteUser(user: {
    id: string;
    name: string;
}) {}
```

Prefer:

```typescript
interface User {
    id: string;
    name: string;
}

function createUser(
    user: User
) {}

function deleteUser(
    user: User
) {}
```

---

# 106. Common Mistake — Creating Aliases with No Meaning

Avoid:

```typescript
type MyString = string;
type MyNumber = number;
```

unless the aliases represent meaningful domain concepts.

Better:

```typescript
type UserId = string;
type RetryCount = number;
```

---

# 107. Interview Question — What Is a Type Alias?

A type alias gives a name to a TypeScript type.

Example:

```typescript
type UserId = string;
```

It can represent primitives, objects, unions, intersections, tuples, functions, literals, and more advanced type expressions.

---

# 108. Interview Question — What Is an Interface?

An interface defines a TypeScript contract, commonly for object-like structures.

Example:

```typescript
interface User {
    id: number;
    name: string;
}
```

Compatible values must satisfy that structure.

---

# 109. Interview Question — Type vs Interface?

Both can describe object shapes.

Major differences include:

```text
interface
→ declaration merging
→ extends
→ object-oriented contract syntax

type
→ unions
→ intersections
→ primitives
→ tuples
→ literals
→ conditional/mapped compositions
```

For ordinary object types, either may be appropriate.

---

# 110. Interview Question — Can Interfaces Extend Interfaces?

Yes.

```typescript
interface Person {
    name: string;
}

interface Employee
    extends Person {

    id: number;
}
```

`Employee` contains both contracts.

---

# 111. Interview Question — Can an Interface Extend Multiple Interfaces?

Yes.

```typescript
interface User
    extends
        Identifiable,
        Timestamped {

    name: string;
}
```

It must satisfy every inherited contract.

---

# 112. Interview Question — Can Types Be Extended?

Type aliases do not use interface-style extension syntax.

Instead, object types are commonly composed using intersections:

```typescript
type Employee =
    Person & {
        employeeId: number;
    };
```

---

# 113. Interview Question — What Is Declaration Merging?

Declaration merging allows multiple declarations of the same compatible interface to contribute to one resulting interface.

```typescript
interface User {
    id: number;
}

interface User {
    name: string;
}
```

Result:

```text
User

id
name
```

---

# 114. Interview Question — Do Type Aliases Support Declaration Merging?

No.

```typescript
type User = {
    id: number;
};

type User = {
    name: string;
};
```

causes a duplicate identifier error.

---

# 115. Interview Question — Can Interfaces Represent Unions?

Not directly as a union alias.

Use:

```typescript
type Status =
    "loading"
    | "success"
    | "error";
```

---

# 116. Interview Question — Can Classes Implement Type Aliases?

Yes, when the alias represents an appropriate object-like contract.

```typescript
type Printable = {
    print(): void;
};

class Report
    implements Printable {

    print(): void {}
}
```

---

# 117. Interview Question — What Is Structural Typing?

Structural typing means compatibility is primarily based on a value's structure rather than its declared type name.

If an object contains the required properties with compatible types, it can satisfy the contract.

---

# 118. Interview Question — What Is Excess Property Checking?

TypeScript performs additional checks on fresh object literals to detect properties not expected by the target object type.

Example:

```typescript
interface User {
    id: number;
}
```

This may fail:

```typescript
const user: User = {
    id: 1,
    random: true
};
```

because `random` is not part of the known `User` shape.

---

# 119. Interview Question — Do Interfaces Exist at Runtime?

No.

Interfaces are removed during TypeScript compilation.

They provide compile-time type information only.

---

# 120. Interview Question — Do Type Aliases Exist at Runtime?

No.

Type aliases are also erased during compilation.

Neither creates runtime validation or runtime objects.

---

# 121. Interview Question — When Should I Use `type`?

Use `type` when you need:

```text
Union types

Intersection types

Primitive aliases

Tuple aliases

Literal types

Function aliases

Mapped types

Conditional types

Complex type composition
```

It can also describe ordinary object types.

---

# 122. Interview Question — When Should I Use `interface`?

Interfaces are particularly useful for:

```text
Object contracts

Class contracts

Extendable public APIs

Declaration merging

Library augmentation

Stable domain object structures
```

But project conventions should also guide the choice.

---

# 123. Cheat Sheet

```text
TYPE ALIAS
================================

type UserId = string;


OBJECT
================================

type User = {
    id: number;
    name: string;
};


UNION
================================

type Status =
    "loading"
    | "success"
    | "error";


INTERSECTION
================================

type Employee =
    Person & Worker;


TUPLE
================================

type Point = [
    number,
    number
];


FUNCTION
================================

type Handler =
    (value: string) => void;


INTERFACE
================================

interface User {
    id: number;
    name: string;
}


OPTIONAL
================================

interface User {
    phone?: string;
}


READONLY
================================

interface User {
    readonly id: number;
}


METHOD
================================

interface User {
    greet(): string;
}


EXTENDS
================================

interface Employee
    extends Person {

    employeeId: number;
}


MULTIPLE EXTENSION
================================

interface User
    extends
        Identifiable,
        Timestamped {

    name: string;
}


DECLARATION MERGING
================================

interface User {
    id: number;
}

interface User {
    name: string;
}

Result:

User
├── id
└── name


CLASS CONTRACT
================================

interface Printable {
    print(): void;
}

class Report
    implements Printable {

    print(): void {}
}


STRUCTURAL TYPING
================================

Compatible shape
→ compatible type


CORE DIFFERENCE
================================

interface
→ object-focused
→ extendable
→ declaration merging

type
→ arbitrary type expressions
→ unions
→ intersections
→ tuples
→ literals
→ functions
→ advanced composition
```

---

# 124. Important Rules to Remember

```text
1. type creates a name for a type.

2. interface defines a contract, commonly for objects.

3. Both are compile-time constructs.

4. Both disappear from emitted JavaScript.

5. Neither performs runtime validation.

6. Both can describe object structures.

7. Both support optional properties.

8. Both support readonly properties.

9. Both can describe methods.

10. Both can work with generics.

11. Both can describe index signatures.

12. Both can model recursive object structures.

13. type can alias primitives.

14. type can alias literals.

15. type can alias unions.

16. type can alias intersections.

17. type can alias tuples.

18. type can alias functions.

19. type can express conditional types.

20. type can express mapped types.

21. interface cannot directly alias a primitive.

22. interface cannot directly alias a union.

23. interface cannot directly alias a literal union.

24. Interfaces can extend interfaces.

25. Interfaces can extend compatible object type aliases.

26. Interfaces can extend multiple interfaces.

27. Type aliases commonly compose object contracts using intersections.

28. Interfaces support declaration merging.

29. Type aliases do not support declaration merging.

30. Repeated compatible interface declarations can merge.

31. Repeated type alias declarations cause errors.

32. Declaration merging should be intentional.

33. Interfaces are open to declaration merging.

34. Type aliases are closed after declaration.

35. Classes can implement interfaces.

36. Classes can also implement appropriate object type aliases.

37. implements checks whether the class satisfies the contract.

38. Interfaces do not provide implementation.

39. Type aliases do not provide implementation.

40. TypeScript uses structural typing.

41. Type names alone do not determine compatibility.

42. Compatible structure usually determines compatibility.

43. Extra properties may exist on structurally compatible variables.

44. Fresh object literals receive excess-property checking.

45. Excess-property checking is not exact object typing.

46. Type aliases do not create new runtime primitive types.

47. type UserId = string is still structurally a string.

48. Type aliases can improve domain meaning.

49. Avoid meaningless aliases.

50. Avoid unnecessary interface inheritance.

51. Avoid excessive type fragmentation.

52. Prefer meaningful domain names.

53. Avoid IUser-style naming unless project conventions require it.

54. interface is natural for stable object contracts.

55. type is natural for unions and complex composition.

56. Either can often be used for ordinary object models.

57. There is no universal rule requiring interface for every object.

58. There is no universal rule requiring type for every object.

59. Follow project conventions when either works.

60. Use declaration merging only when its openness is useful.

61. Use literal unions for controlled sets of values.

62. Use intersections when multiple contracts must be satisfied.

63. Use extends when interface inheritance communicates the relationship clearly.

64. Conflicting interface extensions produce errors.

65. Conflicting intersections may produce never properties.

66. Type and interface can reference each other.

67. They are complementary tools.

68. Interfaces are useful for public extension points.

69. Type aliases are useful for derived types.

70. React props can use either interface or type.

71. Variant React props are often naturally modeled with union type aliases.

72. API object models can use interfaces.

73. API success/error results are naturally modeled with union aliases.

74. Domain models should describe actual application concepts.

75. Do not duplicate anonymous object structures everywhere.

76. Extract reusable contracts when they improve clarity.

77. Do not extract types merely for the sake of extraction.

78. A type alias does not instantiate anything.

79. An interface does not instantiate anything.

80. Neither validates JSON or API responses at runtime.

81. Runtime validation is separate from static typing.

82. Structural typing allows objects to satisfy contracts without explicit declarations.

83. Object literals may receive stricter checks than variables.

84. Interface method syntax and function-property syntax are related but not identical in every advanced type-system scenario.

85. Interface index signatures can model dynamic keys.

86. Type aliases can also model dynamic keys.

87. Known properties must be compatible with relevant index signatures.

88. Generic interfaces can reuse object structures with different data types.

89. Generic type aliases can reuse arbitrary type expressions.

90. type Nullable<T> = T | null is possible because type can represent unions.

91. Complex state modeling usually benefits from type aliases.

92. Object-oriented contract modeling often reads naturally with interfaces.

93. Library augmentation commonly relies on interfaces.

94. Mapped types are generally expressed with type aliases.

95. Conditional types require type aliases/type expressions.

96. Consistency improves maintainability.

97. Readability is more important than blindly choosing one syntax.

98. Choose the construct that best expresses the type relationship.

99. Understand the difference instead of memorizing "always use type" or "always use interface."

100. Both are fundamental tools in production TypeScript.
```

---

# 125. Final Mental Model

```text
                  TYPESCRIPT
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼

        type                  interface
          │                       │
          │                       │
          ▼                       ▼

  General type naming        Object-focused
  and composition            contracts
          │                       │
   ┌──────┼──────┐           ┌────┼────┐
   │      │      │           │    │    │
   ▼      ▼      ▼           ▼    ▼    ▼

 Union   Tuple  Primitive   Object Extend Merge
   │
   ├── Intersection
   ├── Literal
   ├── Function
   ├── Conditional
   └── Mapped
```

For ordinary objects:

```typescript
type User = {
    id: number;
    name: string;
};
```

and:

```typescript
interface User {
    id: number;
    name: string;
}
```

are often both perfectly valid.

The important difference is what happens when the type becomes more complex:

```text
Need a union?
→ type

Need a tuple alias?
→ type

Need a primitive alias?
→ type

Need complex type composition?
→ type

Need declaration merging?
→ interface

Need an intentionally extendable object contract?
→ interface

Just a normal object?
→ either

Existing project has a convention?
→ follow it
```
