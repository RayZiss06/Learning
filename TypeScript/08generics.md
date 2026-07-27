# TypeScript — Generics

Generics allow us to create reusable functions, types, interfaces, and classes that work with different types **while preserving type information**.

The basic idea is:

```text
Write logic once
+
Allow the type to vary
+
Keep the relationship between
input and output types
```

Example:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

Usage:

```typescript
identity<string>("Hello");

identity<number>(100);

identity<boolean>(true);
```

Instead of creating:

```typescript
function stringIdentity(
    value: string
): string {
    return value;
}

function numberIdentity(
    value: number
): number {
    return value;
}
```

we create one generic function.

---

# 1. Why Generics Exist

Suppose we need a function that returns whatever value it receives.

We could write:

```typescript
function identity(
    value: string
): string {
    return value;
}
```

But now it only supports strings.

We could use:

```typescript
function identity(
    value: any
): any {
    return value;
}
```

This supports everything, but type information is lost.

Example:

```typescript
const value =
    identity("Hello");
```

If `identity` returns `any`:

```text
value
→ any
```

TypeScript no longer knows that it is a string.

Generics solve this.

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

Now:

```typescript
const value =
    identity("Hello");
```

TypeScript knows:

```text
value
→ string
```

---

# 2. Generic Type Parameter

The:

```typescript
<T>
```

in:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

is a:

```text
Generic Type Parameter
```

`T` represents a type that will be determined when the function is used.

Mental model:

```text
T
=
Type Placeholder
```

---

# 3. Generic Flow

Consider:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

Call:

```typescript
identity<string>(
    "Hello"
);
```

TypeScript effectively substitutes:

```text
T
→ string
```

So conceptually the function becomes:

```typescript
function identity(
    value: string
): string
```

for that call.

Another call:

```typescript
identity<number>(100);
```

means:

```text
T
→ number
```

Conceptually:

```typescript
function identity(
    value: number
): number
```

The implementation is still written only once.

---

# 4. Generics Preserve Relationships

This is the most important idea.

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

does not merely mean:

```text
Accept anything.
Return anything.
```

It means:

```text
Accept some type T.

Return that same type T.
```

There is a relationship between input and output.

---

# 5. Generic vs `any`

With `any`:

```typescript
function identity(
    value: any
): any {
    return value;
}
```

Then:

```typescript
const result =
    identity("Hello");

result.notARealMethod();
```

TypeScript may allow it because:

```text
result
→ any
```

With generics:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

Then:

```typescript
const result =
    identity("Hello");
```

TypeScript knows:

```text
result
→ string
```

So:

```typescript
result.toUpperCase();
```

works.

But:

```typescript
result.notARealMethod();
```

fails.

---

# 6. Generic Type Inference

We usually do not need to explicitly write:

```typescript
identity<string>(
    "Hello"
);
```

TypeScript can infer `T`.

```typescript
const result =
    identity("Hello");
```

TypeScript determines:

```text
T
→ string
```

Therefore:

```text
result
→ string
```

Similarly:

```typescript
const result =
    identity(100);
```

TypeScript determines:

```text
T
→ number
```

---

# 7. Explicit Generic Arguments

Sometimes we can explicitly specify the generic type.

```typescript
const value =
    identity<number>(100);
```

Here:

```text
T
→ number
```

This is called providing a:

```text
Type Argument
```

---

# 8. Type Parameter vs Type Argument

Definition:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

`T` is the:

```text
Type Parameter
```

Usage:

```typescript
identity<string>(
    "Hello"
);
```

`string` is the:

```text
Type Argument
```

Similar to normal functions:

```text
Function Parameter
→ value

Function Argument
→ "Hello"

Type Parameter
→ T

Type Argument
→ string
```

---

# 9. Generic Arrays

We can use generics with arrays.

```typescript
function getFirst<T>(
    items: T[]
): T | undefined {
    return items[0];
}
```

Usage:

```typescript
const first =
    getFirst([
        "Alice",
        "Bob"
    ]);
```

TypeScript infers:

```text
T
→ string

first
→ string | undefined
```

Another:

```typescript
const first =
    getFirst([
        10,
        20,
        30
    ]);
```

Now:

```text
T
→ number

first
→ number | undefined
```

---

# 10. `Array<T>` Syntax

These are equivalent:

```typescript
T[]
```

and:

```typescript
Array<T>
```

Therefore:

```typescript
function getFirst<T>(
    values: Array<T>
): T | undefined {
    return values[0];
}
```

is equivalent to:

```typescript
function getFirst<T>(
    values: T[]
): T | undefined {
    return values[0];
}
```

---

# 11. Generic Function Example

```typescript
function wrap<T>(
    value: T
): T[] {
    return [value];
}
```

Usage:

```typescript
const numbers =
    wrap(10);
```

Result:

```text
number[]
```

Another:

```typescript
const names =
    wrap("Alice");
```

Result:

```text
string[]
```

---

# 12. Generic Object Return

```typescript
function createResult<T>(
    data: T
) {
    return {
        success: true,
        data
    };
}
```

Usage:

```typescript
const result =
    createResult({
        id: 1,
        name: "Alice"
    });
```

TypeScript preserves the type of `data`.

---

# 13. Multiple Generic Parameters

A generic can have more than one type parameter.

```typescript
function pair<T, U>(
    first: T,
    second: U
): [T, U] {
    return [
        first,
        second
    ];
}
```

Usage:

```typescript
const result =
    pair(
        "Alice",
        25
    );
```

TypeScript infers:

```text
T
→ string

U
→ number
```

Result:

```text
[string, number]
```

---

# 14. Generic Naming Conventions

Common names include:

```text
T
U
V
K
E
R
```

Typical meanings:

```text
T
→ Type

K
→ Key

V
→ Value

E
→ Element

R
→ Return / Result
```

Example:

```typescript
function map<T, R>(
    value: T,
    transform:
        (value: T) => R
): R {
    return transform(value);
}
```

For complex code, descriptive names are often better:

```typescript
function transform<
    Input,
    Output
>(
    value: Input,
    mapper:
        (value: Input) =>
            Output
): Output {
    return mapper(value);
}
```

---

# 15. Generic Type Aliases

Generics can be used with type aliases.

```typescript
type ApiResponse<T> = {
    success: boolean;
    data: T;
};
```

Now:

```typescript
type UserResponse =
    ApiResponse<User>;
```

Conceptually:

```typescript
type UserResponse = {
    success: boolean;
    data: User;
};
```

---

# 16. Reusable API Response

Instead of:

```typescript
type UserResponse = {
    success: boolean;
    data: User;
};

type ProductResponse = {
    success: boolean;
    data: Product;
};

type OrderResponse = {
    success: boolean;
    data: Order;
};
```

we can write:

```typescript
type ApiResponse<T> = {
    success: boolean;
    data: T;
};
```

Then:

```typescript
type UserResponse =
    ApiResponse<User>;

type ProductResponse =
    ApiResponse<Product>;

type OrderResponse =
    ApiResponse<Order>;
```

This removes repeated structure.

---

# 17. Generic Interface

Interfaces can also be generic.

```typescript
interface ApiResponse<T> {
    success: boolean;
    data: T;
}
```

Usage:

```typescript
const response:
    ApiResponse<User> = {
        success: true,
        data: {
            id: 1,
            name: "Alice"
        }
    };
```

---

# 18. Generic Interface with Multiple Parameters

```typescript
interface Response<
    TData,
    TError
> {
    data: TData | null;
    error: TError | null;
}
```

Usage:

```typescript
type UserResponse =
    Response<
        User,
        ApiError
    >;
```

---

# 19. Generic Result Type

A better API result can use a discriminated union:

```typescript
type Result<T> =
    | {
        status: "success";
        data: T;
    }
    | {
        status: "error";
        message: string;
    };
```

Usage:

```typescript
type UserResult =
    Result<User>;
```

Then:

```typescript
function handle(
    result: UserResult
) {
    if (
        result.status ===
        "success"
    ) {
        console.log(
            result.data.name
        );
    }
}
```

This combines:

```text
Generics
+
Discriminated Unions
+
Narrowing
```

---

# 20. Generic Callbacks

Generics become especially powerful with callbacks.

```typescript
function transform<T, R>(
    value: T,
    callback:
        (value: T) => R
): R {
    return callback(value);
}
```

Usage:

```typescript
const length =
    transform(
        "Hello",
        value =>
            value.length
    );
```

TypeScript infers:

```text
T
→ string

R
→ number
```

Therefore:

```text
length
→ number
```

---

# 21. Generic Mapper

```typescript
function mapArray<T, R>(
    items: T[],
    mapper:
        (item: T) => R
): R[] {
    return items.map(
        mapper
    );
}
```

Usage:

```typescript
const names =
    mapArray(
        [
            {
                id: 1,
                name: "Alice"
            },
            {
                id: 2,
                name: "Bob"
            }
        ],
        user =>
            user.name
    );
```

TypeScript infers:

```text
T
→ {
    id: number;
    name: string;
}

R
→ string
```

Result:

```text
string[]
```

---

# 22. Why Generics Matter for Callbacks

Without generics:

```typescript
function mapArray(
    items: any[],
    mapper:
        (item: any) => any
): any[] {
    return items.map(
        mapper
    );
}
```

we lose the relationship between:

```text
Input array type

Callback parameter type

Callback return type

Output array type
```

Generics preserve all of them.

---

# 23. Generic Constraints

Sometimes we want a generic to accept many types, but not every possible type.

Example:

```typescript
function printLength<T>(
    value: T
) {
    console.log(
        value.length
    );
}
```

This fails.

Why?

Because `T` could be:

```text
number
boolean
symbol
```

and TypeScript cannot guarantee that `length` exists.

We need a:

```text
Generic Constraint
```

---

# 24. `extends` Constraint

```typescript
function printLength<
    T extends {
        length: number;
    }
>(
    value: T
): T {
    console.log(
        value.length
    );

    return value;
}
```

Now `T` must contain:

```typescript
{
    length: number;
}
```

---

# 25. Constraint Examples

Works:

```typescript
printLength(
    "Hello"
);
```

because strings have:

```text
length
```

Works:

```typescript
printLength([
    1,
    2,
    3
]);
```

because arrays have:

```text
length
```

Works:

```typescript
printLength({
    length: 10,
    name: "Box"
});
```

Fails:

```typescript
printLength(100);
```

because numbers do not satisfy:

```typescript
{
    length: number;
}
```

---

# 26. Constraint Does Not Replace the Generic

This:

```typescript
function printLength<
    T extends {
        length: number;
    }
>(
    value: T
): T
```

does not mean `T` becomes:

```typescript
{
    length: number;
}
```

It means:

```text
T can be any type
as long as it has
length: number
```

So if we pass:

```typescript
const result =
    printLength({
        length: 10,
        name: "Box"
    });
```

TypeScript preserves:

```text
length
name
```

The specific input type is retained.

---

# 27. Generic Constraint with Interface

```typescript
interface Identifiable {
    id: number;
}
```

Then:

```typescript
function getId<
    T extends Identifiable
>(
    item: T
): number {
    return item.id;
}
```

Works:

```typescript
getId({
    id: 1,
    name: "Alice"
});
```

Fails:

```typescript
getId({
    name: "Alice"
});
```

---

# 28. Generic Constraint with Object

```typescript
function merge<
    T extends object,
    U extends object
>(
    first: T,
    second: U
): T & U {
    return {
        ...first,
        ...second
    };
}
```

Usage:

```typescript
const result =
    merge(
        {
            name: "Alice"
        },
        {
            age: 25
        }
    );
```

Result contains:

```text
name
age
```

---

# 29. `keyof` with Generics

One of the most important generic patterns combines:

```text
Generics
+
keyof
```

Suppose:

```typescript
function getProperty(
    object: object,
    key: string
) {
    return object[key];
}
```

This is unsafe because any string could be passed.

Instead:

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

---

# 30. Understanding `K extends keyof T`

Suppose:

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

Then:

```typescript
keyof typeof user
```

is:

```text
"id"
|
"name"
|
"active"
```

Therefore:

```typescript
K extends keyof T
```

means:

```text
K must be one of
the valid keys of T.
```

---

# 31. Safe Property Access

```typescript
const user = {
    id: 1,
    name: "Alice"
};
```

Valid:

```typescript
getProperty(
    user,
    "name"
);
```

Invalid:

```typescript
getProperty(
    user,
    "email"
);
```

because:

```text
email
```

is not a key of `user`.

---

# 32. `T[K]`

In:

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

the return type:

```typescript
T[K]
```

means:

```text
The type of property K
inside type T.
```

If:

```typescript
const user = {
    id: 1,
    name: "Alice",
    active: true
};
```

Then:

```typescript
getProperty(
    user,
    "name"
);
```

returns:

```text
string
```

While:

```typescript
getProperty(
    user,
    "id"
);
```

returns:

```text
number
```

And:

```typescript
getProperty(
    user,
    "active"
);
```

returns:

```text
boolean
```

---

# 33. Generic Setter

The same idea can create a safe setter.

```typescript
function setProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K,
    value: T[K]
): void {
    object[key] = value;
}
```

Usage:

```typescript
const user = {
    id: 1,
    name: "Alice"
};
```

Valid:

```typescript
setProperty(
    user,
    "name",
    "Bob"
);
```

Invalid:

```typescript
setProperty(
    user,
    "name",
    100
);
```

because:

```text
user.name
→ string
```

---

# 34. Generic Classes

Classes can also use generic type parameters.

```typescript
class Box<T> {
    constructor(
        public value: T
    ) {}

    getValue(): T {
        return this.value;
    }
}
```

Usage:

```typescript
const stringBox =
    new Box<string>(
        "Hello"
    );
```

Then:

```text
stringBox.value
→ string
```

Another:

```typescript
const numberBox =
    new Box<number>(100);
```

Then:

```text
numberBox.value
→ number
```

---

# 35. Generic Class Inference

TypeScript can often infer the type:

```typescript
const box =
    new Box("Hello");
```

TypeScript can infer:

```text
Box<string>
```

---

# 36. Generic Collection Class

```typescript
class Collection<T> {
    private items: T[] = [];

    add(
        item: T
    ): void {
        this.items.push(item);
    }

    getAll(): T[] {
        return this.items;
    }
}
```

Usage:

```typescript
const users =
    new Collection<User>();

users.add({
    id: 1,
    name: "Alice"
});
```

Invalid:

```typescript
users.add("Alice");
```

because the collection expects:

```text
User
```

---

# 37. Generic Repository Pattern

A common backend architecture uses repositories.

```typescript
interface Entity {
    id: number;
}
```

Then:

```typescript
class Repository<
    T extends Entity
> {
    private items: T[] = [];

    add(
        item: T
    ): void {
        this.items.push(item);
    }

    findById(
        id: number
    ): T | undefined {
        return this.items.find(
            item =>
                item.id === id
        );
    }

    getAll(): T[] {
        return this.items;
    }
}
```

Usage:

```typescript
type User = {
    id: number;
    name: string;
};
```

Then:

```typescript
const users =
    new Repository<User>();
```

The repository logic is reusable while preserving entity types.

---

# 38. Generic Promise

You have already used generics even if you did not notice.

```typescript
Promise<T>
```

is generic.

Example:

```typescript
const promise:
    Promise<string> =
    Promise.resolve(
        "Hello"
    );
```

Meaning:

```text
Promise containing
a future string value.
```

---

# 39. Generic Async Function

```typescript
async function getUser():
    Promise<User> {

    return {
        id: 1,
        name: "Alice"
    };
}
```

`Promise<User>` means:

```text
When the promise resolves,
the value is User.
```

---

# 40. Other Generics You Already Use

Examples:

```typescript
Array<string>

Promise<User>

Map<string, User>

Set<number>

Record<string, boolean>
```

These are generic types.

Example:

```typescript
Map<K, V>
```

has two generic parameters:

```text
K
→ Key type

V
→ Value type
```

---

# 41. Generic API Function

Suppose we create:

```typescript
async function request<T>(
    url: string
): Promise<T> {
    const response =
        await fetch(url);

    return response.json();
}
```

Usage:

```typescript
const user =
    await request<User>(
        "/api/user"
    );
```

TypeScript now treats:

```text
user
→ User
```

However, there is an important issue.

---

# 42. Generic API Functions Do Not Validate Runtime Data

This:

```typescript
request<User>(
    "/api/user"
);
```

does not prove that the server actually returned a `User`.

Generics exist at compile time.

The server could return:

```json
{
    "banana": true
}
```

and TypeScript cannot prevent that at runtime.

Therefore:

```text
Generic Type
≠
Runtime Validation
```

For untrusted APIs:

```text
API
 ↓
unknown
 ↓
Runtime Validation
 ↓
Typed Domain Object
```

is safer.

---

# 43. Generic Error Result

```typescript
type Result<
    T,
    E
> =
    | {
        success: true;
        data: T;
    }
    | {
        success: false;
        error: E;
    };
```

Usage:

```typescript
type UserResult =
    Result<
        User,
        ApiError
    >;
```

Now both success and error types are configurable.

---

# 44. Generic Default Types

Generic parameters can have defaults.

```typescript
type ApiResponse<
    T = unknown
> = {
    data: T;
};
```

Now:

```typescript
ApiResponse
```

means:

```typescript
ApiResponse<unknown>
```

But:

```typescript
ApiResponse<User>
```

means:

```text
data
→ User
```

---

# 45. Multiple Generic Defaults

```typescript
type Result<
    TData = unknown,
    TError = Error
> = {
    data: TData | null;
    error: TError | null;
};
```

Usage:

```typescript
Result
```

uses:

```text
TData
→ unknown

TError
→ Error
```

Or:

```typescript
Result<User>
```

uses:

```text
TData
→ User

TError
→ Error
```

---

# 46. Generic Function Type

We can describe generic functions in a type.

```typescript
type Identity =
    <T>(
        value: T
    ) => T;
```

Then:

```typescript
const identity:
    Identity =
    value => value;
```

The function itself is generic.

---

# 47. Generic Interface for Function

```typescript
interface Transformer<
    Input,
    Output
> {
    (
        value: Input
    ): Output;
}
```

Usage:

```typescript
const getLength:
    Transformer<
        string,
        number
    > =
    value =>
        value.length;
```

---

# 48. Generic Interface vs Generic Method

Generic interface:

```typescript
interface Processor<T> {
    process(
        value: T
    ): T;
}
```

Here the type is selected when creating:

```typescript
Processor<string>
```

Generic method:

```typescript
interface Processor {
    process<T>(
        value: T
    ): T;
}
```

Here each call can use a different `T`.

These are different designs.

---

# 49. Generic Interface Example

```typescript
interface Storage<T> {
    save(
        value: T
    ): void;

    get():
        T | undefined;
}
```

Implementation:

```typescript
class MemoryStorage<T>
    implements Storage<T> {

    private value:
        T | undefined;

    save(
        value: T
    ): void {
        this.value = value;
    }

    get():
        T | undefined {
        return this.value;
    }
}
```

---

# 50. Generic Constraints with `keyof`

A very common production pattern:

```typescript
function pluck<
    T,
    K extends keyof T
>(
    items: T[],
    key: K
): T[K][] {
    return items.map(
        item =>
            item[key]
    );
}
```

Example:

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
```

Then:

```typescript
const names =
    pluck(
        users,
        "name"
    );
```

Result:

```text
string[]
```

While:

```typescript
const ids =
    pluck(
        users,
        "id"
    );
```

Result:

```text
number[]
```

---

# 51. Generic Factory Function

```typescript
function create<T>(
    value: T
): {
    value: T;
} {
    return {
        value
    };
}
```

Usage:

```typescript
const result =
    create("Hello");
```

Type:

```typescript
{
    value: string;
}
```

---

# 52. Generic Factory with Constructor

Suppose:

```typescript
type Constructor<T> =
    new () => T;
```

Then:

```typescript
function createInstance<T>(
    Constructor:
        new () => T
): T {
    return new Constructor();
}
```

Usage:

```typescript
class UserService {
    getUsers() {
        // ...
    }
}

const service =
    createInstance(
        UserService
    );
```

TypeScript knows:

```text
service
→ UserService
```

---

# 53. Constructor Generic with Arguments

```typescript
type Constructor<
    T,
    Args extends unknown[]
> =
    new (...args: Args) => T;
```

This can model constructors whose argument types are also generic.

You will see patterns like this in libraries, dependency injection systems, frameworks, and advanced TypeScript code.

---

# 54. Generic Tuple Function

```typescript
function createPair<T, U>(
    first: T,
    second: U
): [T, U] {
    return [
        first,
        second
    ];
}
```

Usage:

```typescript
const user =
    createPair(
        1,
        "Alice"
    );
```

Result:

```text
[number, string]
```

---

# 55. Generic Wrapper

```typescript
type Wrapper<T> = {
    value: T;
    createdAt: Date;
};
```

Then:

```typescript
type UserWrapper =
    Wrapper<User>;
```

Result conceptually:

```typescript
{
    value: User;
    createdAt: Date;
}
```

---

# 56. Generic Pagination

A very common API structure:

```typescript
type PaginatedResponse<T> = {
    items: T[];

    page: number;

    pageSize: number;

    total: number;
};
```

Then:

```typescript
type UsersResponse =
    PaginatedResponse<User>;

type ProductsResponse =
    PaginatedResponse<Product>;
```

No need to duplicate pagination fields.

---

# 57. Generic Page Function

```typescript
function createPage<T>(
    items: T[],
    page: number,
    pageSize: number,
    total: number
): PaginatedResponse<T> {
    return {
        items,
        page,
        pageSize,
        total
    };
}
```

TypeScript preserves the element type automatically.

---

# 58. Generic Tree

Generics are useful for recursive data structures.

```typescript
type TreeNode<T> = {
    value: T;

    children:
        TreeNode<T>[];
};
```

Example:

```typescript
const tree:
    TreeNode<string> = {
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

# 59. Generic Linked Structure

```typescript
type ListNode<T> = {
    value: T;

    next:
        ListNode<T>
        | null;
};
```

Now we can create:

```typescript
ListNode<number>

ListNode<string>

ListNode<User>
```

using the same structure.

---

# 60. Generic Dictionary

```typescript
type Dictionary<T> = {
    [key: string]: T;
};
```

Example:

```typescript
const users:
    Dictionary<User> = {
        user1: {
            id: 1,
            name: "Alice"
        },

        user2: {
            id: 2,
            name: "Bob"
        }
    };
```

This concept is similar to:

```typescript
Record<string, User>
```

---

# 61. Generic Event Payload

```typescript
type AppEvent<T> = {
    timestamp: Date;
    payload: T;
};
```

Then:

```typescript
type UserCreatedEvent =
    AppEvent<User>;
```

Or:

```typescript
type PaymentEvent =
    AppEvent<Payment>;
```

This pattern appears frequently in event-driven applications.

---

# 62. Strongly Typed Events

We can model events more precisely:

```typescript
type EventMap = {
    userCreated: User;
    userDeleted: {
        id: number;
    };
};
```

Then:

```typescript
function emit<
    K extends keyof EventMap
>(
    event: K,
    payload: EventMap[K]
): void {
    // emit event
}
```

Valid:

```typescript
emit(
    "userDeleted",
    {
        id: 10
    }
);
```

Invalid:

```typescript
emit(
    "userDeleted",
    {
        name: "Alice"
    }
);
```

This combines:

```text
Generics
+
keyof
+
Indexed Access Types
```

---

# 63. Generic Database Result

```typescript
type QueryResult<T> = {
    rows: T[];
    rowCount: number;
};
```

Then:

```typescript
type UserQueryResult =
    QueryResult<User>;
```

This allows database infrastructure to remain generic while domain types remain specific.

---

# 64. Generic Cache

```typescript
class Cache<T> {
    private values =
        new Map<string, T>();

    set(
        key: string,
        value: T
    ): void {
        this.values.set(
            key,
            value
        );
    }

    get(
        key: string
    ): T | undefined {
        return this.values.get(
            key
        );
    }
}
```

Usage:

```typescript
const userCache =
    new Cache<User>();
```

Now only `User` values belong in this cache.

---

# 65. Generic Service

```typescript
interface Service<T> {
    getAll():
        Promise<T[]>;

    getById(
        id: number
    ):
        Promise<
            T | undefined
        >;
}
```

Then:

```typescript
class UserService
    implements Service<User> {

    async getAll():
        Promise<User[]> {
        return [];
    }

    async getById(
        id: number
    ):
        Promise<
            User | undefined
        > {
        return undefined;
    }
}
```

---

# 66. Generic CRUD Interface

```typescript
interface CrudService<
    TEntity,
    TCreate,
    TUpdate
> {
    create(
        data: TCreate
    ):
        Promise<TEntity>;

    update(
        id: number,
        data: TUpdate
    ):
        Promise<TEntity>;

    getById(
        id: number
    ):
        Promise<
            TEntity | null
        >;

    delete(
        id: number
    ):
        Promise<void>;
}
```

This allows us to reuse a service contract while keeping different entity/create/update models.

---

# 67. Why Multiple Types Matter in CRUD

Suppose:

```typescript
type User = {
    id: number;
    name: string;
    createdAt: Date;
};
```

Creation may require only:

```typescript
type CreateUser = {
    name: string;
};
```

Updating may allow:

```typescript
type UpdateUser = {
    name?: string;
};
```

Therefore:

```typescript
CrudService<
    User,
    CreateUser,
    UpdateUser
>
```

is more accurate than using `User` everywhere.

---

# 68. Generic Function Returning Promise

```typescript
async function resolve<T>(
    value: T
): Promise<T> {
    return value;
}
```

Usage:

```typescript
const result =
    await resolve(
        "Hello"
    );
```

TypeScript knows:

```text
result
→ string
```

---

# 69. Generic Async Result

```typescript
type AsyncResult<T> =
    Promise<Result<T>>;
```

Then:

```typescript
function getUser():
    AsyncResult<User> {
    // ...
}
```

Generics can be composed with other generic types.

---

# 70. Nested Generics

Example:

```typescript
Promise<
    ApiResponse<
        User[]
    >
>
```

Read from the inside out:

```text
User[]

→ ApiResponse<User[]>

→ Promise<ApiResponse<User[]>>
```

Meaning:

```text
A Promise

that resolves to an ApiResponse

whose data contains User[]
```

---

# 71. Generic Composition

Suppose:

```typescript
type ApiResponse<T> = {
    data: T;
};

type Paginated<T> = {
    items: T[];
    total: number;
};
```

Then:

```typescript
type Response =
    ApiResponse<
        Paginated<User>
    >;
```

Conceptually:

```text
API Response
    │
    ▼
Paginated Data
    │
    ▼
User[]
```

---

# 72. Generic Defaults with Constraints

We can combine:

```text
Default Types
+
Constraints
```

Example:

```typescript
type Store<
    T extends object =
        Record<
            string,
            unknown
        >
> = {
    value: T;
};
```

The generic has both:

```text
Constraint
→ must be object

Default
→ Record<string, unknown>
```

---

# 73. Generic Parameter Order

Required generic parameters should normally come before defaulted ones.

Example:

```typescript
type Response<
    TData,
    TError = Error
> = {
    data: TData;
    error: TError | null;
};
```

Then:

```typescript
Response<User>
```

means:

```text
TData
→ User

TError
→ Error
```

---

# 74. Generics with Union Constraints

```typescript
function stringify<
    T extends
        string
        | number
        | boolean
>(
    value: T
): string {
    return String(value);
}
```

Now only:

```text
string
number
boolean
```

are allowed.

---

# 75. Generics with Literal Types

```typescript
function createStatus<
    T extends
        "loading"
        | "success"
        | "error"
>(
    status: T
): T {
    return status;
}
```

Calling:

```typescript
const status =
    createStatus(
        "success"
    );
```

can preserve:

```text
"success"
```

rather than merely:

```text
string
```

---

# 76. Generics Can Preserve More Specific Information

Suppose:

```typescript
function returnObject(
    value: object
): object {
    return value;
}
```

Then:

```typescript
const user =
    returnObject({
        id: 1,
        name: "Alice"
    });
```

The return type is only:

```text
object
```

We lost useful property information.

Generic:

```typescript
function returnObject<
    T extends object
>(
    value: T
): T {
    return value;
}
```

Now:

```text
id
name
```

are preserved.

---

# 77. Constraint vs Parameter Type

Compare:

```typescript
function process(
    value: {
        id: number;
    }
): {
    id: number;
} {
    return value;
}
```

with:

```typescript
function process<
    T extends {
        id: number;
    }
>(
    value: T
): T {
    return value;
}
```

The second version preserves additional information from the input type.

This is one of the main reasons generic constraints are useful.

---

# 78. When Generics Are Useful

Generics are useful when:

```text
The same logic works across
multiple types

AND

there is a meaningful relationship
between those types.
```

Examples:

```text
Input type → Output type

Array element → Returned element

Object → Valid key

Key → Property value

API data → Response data

Entity → Repository entity

Event name → Event payload

State data → State result
```

---

# 79. When Generics Are NOT Necessary

Do not use generics simply because you can.

Bad:

```typescript
function add<T extends number>(
    a: T,
    b: T
): number {
    return a + b;
}
```

There is no useful generic relationship here.

Better:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

---

# 80. Another Unnecessary Generic

Bad:

```typescript
function print<
    T extends string
>(
    value: T
): void {
    console.log(value);
}
```

If you do not need to preserve the specific literal type or relate `T` to something else:

```typescript
function print(
    value: string
): void {
    console.log(value);
}
```

is simpler.

---

# 81. Generic Used Only Once

A useful heuristic:

If a generic type parameter appears only once and does not establish a meaningful relationship, ask whether it should simply be a normal type.

Example:

```typescript
function log<T>(
    value: T
): void {
    console.log(value);
}
```

This is valid, but if no type relationship is needed, depending on intent:

```typescript
function log(
    value: unknown
): void {
    console.log(value);
}
```

may communicate the design more clearly.

Generics should express relationships, not decorate functions.

---

# 82. Generic vs Union

Suppose:

```typescript
function identity(
    value:
        string | number
):
    string | number {
    return value;
}
```

Call:

```typescript
const result =
    identity("Hello");
```

Result is:

```text
string | number
```

The relationship has been lost.

Generic:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

Now:

```typescript
const result =
    identity("Hello");
```

returns:

```text
string
```

---

# 83. Generic vs Overload

Sometimes overloads and generics can solve similar problems.

Generic:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

is better than writing overloads for every possible type.

Use generics when the relationship can be described generally.

Use overloads when different input shapes produce different behavior that cannot be expressed cleanly with one generic relationship.

---

# 84. React `useState` Preview

React's `useState` uses generics.

Conceptually:

```typescript
useState<T>(
    initialValue: T
)
```

Example:

```typescript
const [
    count,
    setCount
] = useState<number>(0);
```

Usually TypeScript infers:

```typescript
useState(0)
```

as number state.

---

# 85. React Nullable State Preview

Generics become useful when inference is too narrow.

```typescript
const [
    user,
    setUser
] =
    useState<User | null>(
        null
    );
```

Without the generic:

```typescript
useState(null)
```

TypeScript may infer only:

```text
null
```

But the intended state is:

```text
User | null
```

---

# 86. Generic React Props Preview

We may eventually create reusable components like:

```typescript
type ListProps<T> = {
    items: T[];

    renderItem:
        (item: T) =>
            React.ReactNode;
};
```

Then:

```typescript
function List<T>(
    props: ListProps<T>
) {
    return (
        <>
            {props.items.map(
                props.renderItem
            )}
        </>
    );
}
```

Now the component can safely render:

```text
Users

Products

Orders

Games

etc.
```

while preserving the correct item type.

---

# 87. Generic React Hook Preview

Custom hooks can also use generics.

Conceptually:

```typescript
function useApi<T>(
    url: string
) {
    // ...

    return {
        data:
            null as T | null
    };
}
```

Then:

```typescript
const {
    data
} =
    useApi<User[]>(
        "/api/users"
    );
```

Generics allow reusable hooks to preserve domain-specific types.

Runtime API validation is still a separate concern.

---

# 88. Generic Table Preview

A reusable table may accept:

```typescript
type TableProps<T> = {
    rows: T[];

    columns:
        Array<{
            key: keyof T;
            label: string;
        }>;
};
```

This allows TypeScript to ensure table column keys actually exist on the row type.

Example:

```text
User table
→ id, name, email

Product table
→ id, title, price
```

The same component can support both safely.

---

# 89. Generic Form Preview

Forms can use:

```typescript
type FormErrors<T> = {
    [K in keyof T]?:
        string;
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
FormErrors<LoginForm>
```

becomes conceptually:

```typescript
{
    email?: string;
    password?: string;
}
```

This introduces mapped types, which we will study separately.

---

# 90. Generic Utility Types

TypeScript's built-in utility types use generics.

Examples:

```typescript
Partial<T>

Required<T>

Readonly<T>

Pick<T, K>

Omit<T, K>

Record<K, V>

Exclude<T, U>

Extract<T, U>

NonNullable<T>

ReturnType<T>
```

For example:

```typescript
Partial<User>
```

means:

```text
Take User
and make its properties optional.
```

We will cover these in the Utility Types topic rather than duplicating them here.

---

# 91. Common Mistake — Using `any` Instead of Generics

Avoid:

```typescript
function first(
    items: any[]
): any {
    return items[0];
}
```

Prefer:

```typescript
function first<T>(
    items: T[]
): T | undefined {
    return items[0];
}
```

Now the element type is preserved.

---

# 92. Common Mistake — Overusing Generics

Avoid:

```typescript
function greet<
    T extends string
>(
    name: T
): string {
    return `Hello ${name}`;
}
```

when:

```typescript
function greet(
    name: string
): string {
    return `Hello ${name}`;
}
```

does exactly what is needed.

---

# 93. Common Mistake — Missing Constraint

This fails:

```typescript
function getLength<T>(
    value: T
) {
    return value.length;
}
```

because `T` might not have `length`.

Use:

```typescript
function getLength<
    T extends {
        length: number;
    }
>(
    value: T
) {
    return value.length;
}
```

---

# 94. Common Mistake — Constraint Too Broad

Sometimes:

```typescript
T extends object
```

is technically valid but does not describe what the implementation actually needs.

If the implementation needs:

```typescript
item.id
```

prefer:

```typescript
T extends {
    id: number;
}
```

The constraint should describe the required capability.

---

# 95. Common Mistake — Explicit Type Argument When Inference Works

This:

```typescript
identity<string>(
    "Hello"
);
```

is valid.

But usually:

```typescript
identity("Hello");
```

is cleaner because TypeScript can infer `string`.

Provide explicit type arguments when they are necessary or improve intent.

---

# 96. Common Mistake — Generics as Runtime Validation

This:

```typescript
const user =
    await request<User>(
        "/api/user"
    );
```

does not validate the server response.

Generics disappear during compilation.

Never confuse:

```text
Compile-time type information
```

with:

```text
Runtime data validation
```

---

# 97. Common Mistake — Losing Relationships

Avoid:

```typescript
function getProperty(
    object:
        Record<
            string,
            unknown
        >,
    key: string
): unknown {
    return object[key];
}
```

when the actual relationship can be expressed:

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

Now key validity and return type are connected.

---

# 98. Common Mistake — Too Many Generic Parameters

Code like:

```typescript
function process<
    T,
    U,
    V,
    W,
    X,
    Y
>(...)
```

can become difficult to understand.

Use descriptive names and ask whether all those relationships actually need to be generic.

Complex generic code is not automatically better code.

---

# 99. Common Mistake — Using `object` and Losing Structure

```typescript
function clone(
    value: object
): object {
    return {
        ...value
    };
}
```

The result loses the specific input type.

Better:

```typescript
function clone<
    T extends object
>(
    value: T
): T {
    return {
        ...value
    };
}
```

Now the relationship is preserved.

---

# 100. Common Mistake — Generic Return Type Not Connected to Input

Be suspicious of:

```typescript
function convert<T>():
    T {
    return something as T;
}
```

The caller can request any type:

```typescript
convert<User>();
```

even if the runtime value is not a `User`.

A generic is safest when the type can be inferred or justified from actual inputs, constraints, validation, or another trusted relationship.

---

# 101. Interview Question — What Are Generics?

Generics allow reusable code to operate across multiple types while preserving type information and relationships between those types.

Example:

```typescript
function identity<T>(
    value: T
): T {
    return value;
}
```

---

# 102. Interview Question — Why Not Use `any`?

`any` removes much of TypeScript's checking and loses type relationships.

Generics allow flexibility while preserving type safety.

```text
any
→ flexibility without safety

generic
→ flexibility with relationships
```

---

# 103. Interview Question — What Is a Type Parameter?

A type parameter is a placeholder for a type.

Example:

```typescript
<T>
```

in:

```typescript
function identity<T>(
    value: T
): T
```

---

# 104. Interview Question — What Is a Type Argument?

The concrete type supplied to a generic is a type argument.

```typescript
identity<string>(
    "Hello"
);
```

Here:

```text
T
→ type parameter

string
→ type argument
```

---

# 105. Interview Question — What Is a Generic Constraint?

A generic constraint limits which types can be used for a generic parameter.

Example:

```typescript
function getId<
    T extends {
        id: number;
    }
>(
    value: T
) {
    return value.id;
}
```

`T` can vary but must have:

```typescript
id: number
```

---

# 106. Interview Question — What Does `K extends keyof T` Mean?

It means:

```text
K must be a valid key of T.
```

Example:

```typescript
function getProperty<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K] {
    return object[key];
}
```

This safely connects an object, its valid keys, and the corresponding property value type.

---

# 107. Interview Question — What Is `T[K]`?

`T[K]` is an indexed access type.

It means:

```text
The type of property K
inside T.
```

Example:

```typescript
type User = {
    id: number;
    name: string;
};

type Name =
    User["name"];
```

Result:

```text
string
```

---

# 108. Interview Question — Can Classes Be Generic?

Yes.

```typescript
class Box<T> {
    constructor(
        public value: T
    ) {}
}
```

Then:

```typescript
Box<string>

Box<number>

Box<User>
```

can use the same class implementation with different value types.

---

# 109. Interview Question — Can Interfaces Be Generic?

Yes.

```typescript
interface Response<T> {
    data: T;
}
```

Then:

```typescript
Response<User>

Response<Product>
```

reuse the same structure.

---

# 110. Interview Question — What Are Generic Defaults?

Generic parameters can provide default types.

```typescript
type Response<
    T = unknown
> = {
    data: T;
};
```

If no type argument is supplied:

```text
T
→ unknown
```

---

# 111. Interview Question — Generic vs Union?

A union says:

```text
The value may be one of
these types.
```

A generic says:

```text
The type may vary between uses,
and relationships involving that
type should be preserved.
```

Example:

```typescript
function identity<T>(
    value: T
): T
```

preserves the exact relationship better than:

```typescript
function identity(
    value:
        string | number
):
    string | number
```

---

# 112. Interview Question — Generic vs Overload?

Use generics when one reusable type relationship describes the behavior.

Use overloads when different input forms require distinct call signatures or return relationships that are clearer as separate signatures.

They can also be combined when necessary.

---

# 113. Interview Question — Do Generics Exist at Runtime?

No.

TypeScript generics are compile-time constructs.

They are erased when TypeScript is compiled to JavaScript.

Therefore generics cannot validate runtime API data.

---

# 114. Interview Question — When Should Generics Be Used?

Use them when:

```text
Logic should work across
multiple types

AND

type relationships need
to be preserved.
```

Common examples:

```text
Collections

Callbacks

API structures

Repositories

Services

Utilities

Event systems

React components

React hooks
```

---

# 115. Interview Question — When Should Generics NOT Be Used?

Avoid generics when:

```text
Only one concrete type is needed.

No meaningful type relationship exists.

A normal parameter type is clearer.

The generic adds complexity without safety.

A union better describes the domain.
```

---

# 116. Cheat Sheet

```text
BASIC GENERIC
================================

function identity<T>(
    value: T
): T {
    return value;
}


TYPE INFERENCE
================================

identity("Hello")

T → string


EXPLICIT TYPE ARGUMENT
================================

identity<string>(
    "Hello"
)


ARRAY GENERIC
================================

function first<T>(
    items: T[]
): T | undefined


ARRAY SYNTAX
================================

T[]

Array<T>


MULTIPLE GENERICS
================================

function pair<T, U>(
    first: T,
    second: U
): [T, U]


GENERIC TYPE
================================

type Response<T> = {
    data: T;
};


GENERIC INTERFACE
================================

interface Response<T> {
    data: T;
}


GENERIC CLASS
================================

class Box<T> {
    value: T;
}


CONSTRAINT
================================

T extends SomeType


LENGTH CONSTRAINT
================================

T extends {
    length: number;
}


KEY CONSTRAINT
================================

K extends keyof T


INDEXED ACCESS
================================

T[K]


SAFE GETTER
================================

function get<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K]


GENERIC CALLBACK
================================

function map<T, R>(
    value: T,
    callback:
        (value: T) => R
): R


GENERIC PROMISE
================================

Promise<T>


GENERIC MAP
================================

Map<K, V>


GENERIC SET
================================

Set<T>


DEFAULT
================================

type Response<
    T = unknown
> = {
    data: T;
};


RESULT
================================

type Result<T, E> =
    | {
        success: true;
        data: T;
    }
    | {
        success: false;
        error: E;
    };


PAGINATION
================================

type Paginated<T> = {
    items: T[];
    total: number;
};


CORE IDEA
================================

Generics preserve relationships
between types.
```

---

# 117. Important Rules to Remember

```text
1. Generics create reusable typed structures.

2. Generics preserve type information.

3. Generics are safer than any when type relationships matter.

4. T is commonly used as a generic type parameter.

5. Generic names are arbitrary identifiers.

6. Descriptive names are useful for complex generics.

7. Generic type arguments can often be inferred.

8. Do not explicitly provide generic arguments when inference is already clear unless it improves intent.

9. Generic functions can preserve input/output relationships.

10. Generics work with arrays.

11. Generics work with tuples.

12. Generics work with objects.

13. Generics work with callbacks.

14. Generics work with type aliases.

15. Generics work with interfaces.

16. Generics work with classes.

17. Generics work with promises.

18. Generics work with Maps and Sets.

19. Generics can have multiple type parameters.

20. Different generic parameters can represent different relationships.

21. Constraints restrict allowed generic types.

22. extends is used for generic constraints.

23. Constraints describe required capabilities.

24. Constraints should not be broader than necessary.

25. T extends object restricts T to object-like values.

26. Structural constraints can require specific properties.

27. Constraints still preserve the specific input type.

28. keyof can be combined with generics.

29. K extends keyof T means K must be a valid key of T.

30. T[K] gives the type of property K on T.

31. Generic getters can safely access dynamic properties.

32. Generic setters can enforce property value types.

33. Generic callbacks preserve callback parameter types.

34. Generic callbacks can infer output types.

35. Generic mapper functions commonly use T and R.

36. Generic API structures reduce duplication.

37. Generic pagination is a common production pattern.

38. Generic Result types model reusable success/error structures.

39. Generics combine well with discriminated unions.

40. Generics combine well with narrowing.

41. Generic classes are useful for typed containers.

42. Generic repositories can preserve entity types.

43. Generic services can preserve domain types.

44. CRUD services may need multiple generic parameters.

45. Entity, create, and update models may differ.

46. Generic defaults provide fallback types.

47. Generic defaults reduce repeated type arguments.

48. Defaults can be combined with constraints.

49. Promise<T> is generic.

50. Array<T> is generic.

51. Map<K, V> is generic.

52. Set<T> is generic.

53. Record<K, V> is generic.

54. TypeScript utility types are largely generic.

55. Generics can be nested.

56. Read nested generics from the inside outward.

57. Generic types can compose other generic types.

58. Generic recursive structures are possible.

59. Trees commonly use generics.

60. Linked structures commonly use generics.

61. Generic dictionaries are reusable.

62. Event payloads can be generic.

63. Event names can be related to payload types using keyof.

64. Generics are useful for strongly typed event systems.

65. Generics can model database results.

66. Generic caches preserve cached value types.

67. Generic factories can preserve created types.

68. Constructor signatures can be generic.

69. Generic interfaces can describe functions.

70. Generic methods differ from generic interfaces.

71. Generic interface type parameters are chosen for the interface instance/type use.

72. Generic method parameters can vary per method call.

73. Generics are compile-time constructs.

74. Generics disappear from emitted JavaScript.

75. Generics cannot validate runtime data.

76. API response typing is not runtime validation.

77. External data should still be validated.

78. unknown is useful at trust boundaries.

79. Runtime validation and generics solve different problems.

80. Avoid using any where a generic relationship exists.

81. Avoid unnecessary generics.

82. Generic complexity should provide real value.

83. A generic used only once may be unnecessary.

84. Generics should usually represent relationships.

85. Generic constraints should match implementation needs.

86. Avoid forcing generic return types with unsafe assertions.

87. Caller-selected T does not prove runtime data is T.

88. Type inference often produces cleaner generic code.

89. Generics can preserve literal types in some contexts.

90. Generic unions can constrain allowed types.

91. Generics and unions solve different problems.

92. A union describes possible values.

93. A generic describes variable type relationships across uses.

94. Generics can replace repetitive overloads when behavior is structurally identical.

95. Overloads remain useful for distinct call signatures.

96. React APIs use generics extensively.

97. useState can accept explicit generic state types.

98. Nullable React state often benefits from explicit generic types.

99. Reusable React components can be generic.

100. Custom React hooks can be generic.

101. Generic tables can constrain columns to row keys.

102. Generic forms can derive types from form models.

103. Generic repositories are common backend abstractions.

104. Generic service contracts are common enterprise patterns.

105. Type parameters can depend on other type parameters.

106. K can be constrained by keyof T.

107. T[K] connects a key with its corresponding value type.

108. Good generics make APIs safer.

109. Bad generics make APIs harder to understand.

110. Prefer the simplest type model that preserves the relationship you actually need.
```

---

# 118. Final Mental Model

```text
WITHOUT GENERICS
================================

Input
  │
  ▼
any
  │
  ▼
Output

Type information lost.


WITH GENERICS
================================

Input<T>
   │
   ▼
Generic Logic
   │
   ▼
Output<T>

Type relationship preserved.
```

Example:

```typescript
function first<T>(
    values: T[]
): T | undefined {
    return values[0];
}
```

Call:

```typescript
first([
    "Alice",
    "Bob"
]);
```

Flow:

```text
Input
    │
    ▼
string[]
    │
    ▼
T = string
    │
    ▼
first<T>()
    │
    ▼
string | undefined
```

For multiple types:

```typescript
function transform<
    Input,
    Output
>(
    value: Input,
    mapper:
        (
            value: Input
        ) => Output
): Output
```

Flow:

```text
Input
   │
   ▼
Input Type
   │
   ├──────────────┐
   ▼              ▼
value          mapper
                  │
                  ▼
             Output Type
                  │
                  ▼
                Result
```

For object keys:

```typescript
function get<
    T,
    K extends keyof T
>(
    object: T,
    key: K
): T[K]
```

Relationship:

```text
Object T
   │
   ▼
keyof T
   │
   ▼
Valid Key K
   │
   ▼
T[K]
   │
   ▼
Correct Property Type
```

The most important principle:

```text
Generics are not about
"accepting any type."

Generics are about
preserving relationships
between types.
```

Use:

```typescript
<T>
```

when the type needs to vary **and that type matters elsewhere**.

Do not use:

```typescript
<T>
```

just because a function happens to accept different kinds of values.
