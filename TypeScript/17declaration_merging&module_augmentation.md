# Declaration Merging & Module Augmentation

TypeScript allows certain declarations with the same name to be **combined into a single declaration**.

This behavior is called:

```text
Declaration Merging
```

TypeScript also allows us to extend declarations that belong to an existing module.

This is called:

```text
Module Augmentation
```

These features are particularly useful when:

* Extending library types
* Adding properties to existing interfaces
* Extending global objects
* Typing plugins or middleware
* Adding application-specific information to third-party types

---

# 1. What Is Declaration Merging?

Declaration merging happens when TypeScript encounters multiple compatible declarations with the same name and combines them.

Example:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

TypeScript treats them approximately as:

```typescript
interface User {
    name: string;
    age: number;
}
```

Therefore:

```typescript
const user: User = {
    name: "Alice",
    age: 25
};
```

Both properties are required.

---

# 2. Why Does Declaration Merging Exist?

JavaScript libraries can be extended dynamically.

For example, a library might expose an object, and another plugin may add functionality to it.

TypeScript needs a way to describe these extended APIs.

Declaration merging allows existing type declarations to be expanded without rewriting the original declaration.

Conceptually:

```text
Original Declaration
        +
Additional Declaration
        ↓
Merged Type
```

---

# 3. Interface Declaration Merging

Interfaces are the most common example.

```typescript
interface Employee {
    id: number;
}

interface Employee {
    name: string;
}

interface Employee {
    department: string;
}
```

TypeScript combines them.

Conceptually:

```typescript
interface Employee {
    id: number;
    name: string;
    department: string;
}
```

Usage:

```typescript
const employee: Employee = {
    id: 1,
    name: "Alice",
    department: "Engineering"
};
```

---

# 4. Missing Properties After Merging

Because all declarations are merged:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

this is invalid:

```typescript
const user: User = {
    name: "Alice"
};
```

because `age` is also part of `User`.

Correct:

```typescript
const user: User = {
    name: "Alice",
    age: 25
};
```

---

# 5. Properties Must Be Compatible

Consider:

```typescript
interface User {
    name: string;
}

interface User {
    name: string;
    age: number;
}
```

This works because both declarations agree that:

```typescript
name: string
```

But this is invalid:

```typescript
interface User {
    name: string;
}

interface User {
    name: number;
}
```

TypeScript cannot merge conflicting property types.

---

# 6. Why Interfaces Can Merge

Interfaces represent open declarations.

This means additional declarations can contribute members to the same interface.

Example:

```typescript
interface Config {
    apiUrl: string;
}
```

Later:

```typescript
interface Config {
    timeout: number;
}
```

Result:

```typescript
interface Config {
    apiUrl: string;
    timeout: number;
}
```

This behavior is sometimes described as interfaces being:

```text
Open
```

---

# 7. Type Aliases Do Not Merge

Consider:

```typescript
type User = {
    name: string;
};
```

Trying:

```typescript
type User = {
    age: number;
};
```

produces an error.

Type aliases cannot be declared repeatedly in the same scope.

So:

```text
interface
→ Can participate in declaration merging

type
→ Cannot be redeclared this way
```

---

# 8. Interface vs Type Alias

Interface:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

Valid.

Type alias:

```typescript
type User = {
    name: string;
};

type User = {
    age: number;
};
```

Invalid.

This is one of the important differences between `interface` and `type`.

---

# 9. Extending a Type Alias Is Different

Even though type aliases cannot merge, we can compose types.

```typescript
type BasicUser = {
    name: string;
};

type UserWithAge =
    BasicUser & {
        age: number;
    };
```

Result:

```typescript
{
    name: string;
    age: number;
}
```

But this is:

```text
Type composition
```

not:

```text
Declaration merging
```

---

# 10. Function Overloads and Merging

Interfaces can contain function overload signatures.

```typescript
interface Formatter {
    format(value: string): string;
}

interface Formatter {
    format(value: number): string;
}
```

After merging, conceptually:

```typescript
interface Formatter {
    format(value: string): string;
    format(value: number): string;
}
```

Implementation:

```typescript
const formatter: Formatter = {
    format(value: string | number) {
        return String(value);
    }
};
```

---

# 11. Namespace Merging

Namespaces with the same name can merge.

```typescript
namespace App {
    export const name = "MyApp";
}

namespace App {
    export const version = "1.0.0";
}
```

Now:

```typescript
console.log(App.name);
console.log(App.version);
```

Both are available.

Conceptually:

```text
App
├── name
└── version
```

---

# 12. Namespace Members Must Be Exported

Consider:

```typescript
namespace App {
    const secret = "abc";

    export const name = "MyApp";
}
```

`secret` is not exposed outside that namespace declaration.

But:

```typescript
App.name
```

is available because it was exported.

When working with merged namespaces, exported members form the public API.

---

# 13. Class + Namespace Merging

A class and namespace can share a name in supported declaration patterns.

Example:

```typescript
class User {
    constructor(
        public name: string
    ) {}
}

namespace User {
    export const defaultRole =
        "user";
}
```

Now:

```typescript
const user =
    new User("Alice");

console.log(
    User.defaultRole
);
```

The class provides:

```text
new User(...)
```

while the namespace provides static-like additional members.

---

# 14. Class + Namespace Mental Model

```typescript
class User {
    ...
}

namespace User {
    export const defaultRole =
        "user";
}
```

Think:

```text
User
│
├── Constructor
│
└── defaultRole
```

The namespace augments the value associated with the class name.

---

# 15. Function + Namespace Merging

Functions can also be combined with namespaces.

```typescript
function build(
    value: string
) {
    return value;
}

namespace build {
    export const version =
        "1.0.0";
}
```

Now:

```typescript
build("hello");

console.log(
    build.version
);
```

So `build` behaves as both:

```text
Callable function
+
Object with additional properties
```

---

# 16. Why Function + Namespace Merging Exists

JavaScript functions are objects.

For example:

```javascript
function request() {}

request.version = "1.0.0";
```

TypeScript's declaration system can model APIs with this kind of shape.

Conceptually:

```text
request()
request.version
request.config
```

This pattern appears in some JavaScript libraries.

---

# 17. Enum + Namespace Merging

Enums can also be augmented with namespaces.

```typescript
enum Status {
    Pending,
    Success,
    Failed
}

namespace Status {
    export function isFinal(
        status: Status
    ) {
        return (
            status === Status.Success ||
            status === Status.Failed
        );
    }
}
```

Usage:

```typescript
Status.isFinal(
    Status.Success
);
```

Conceptually:

```text
Status
├── Pending
├── Success
├── Failed
└── isFinal()
```

---

# 18. What Is Module Augmentation?

Declaration merging works naturally with declarations in the same declaration space.

But what if we want to extend a type defined inside another module?

For example:

```typescript
import { SomeType } from "some-library";
```

We cannot edit the library's source code.

TypeScript provides:

```typescript
declare module
```

to augment existing module declarations.

This is called:

```text
Module Augmentation
```

---

# 19. Basic Module Augmentation Syntax

```typescript
declare module "module-name" {
    interface SomeInterface {
        newProperty: string;
    }
}
```

Conceptually:

```text
Existing Module
      │
      ▼
Existing Interface
      │
      +
Our Declaration
      │
      ▼
Augmented Interface
```

---

# 20. Example Module

Suppose we have:

```typescript
// user.ts

export interface User {
    id: number;
    name: string;
}
```

Normally:

```typescript
const user: User = {
    id: 1,
    name: "Alice"
};
```

Now suppose our application needs:

```typescript
role: string
```

added to that interface.

---

# 21. Augmenting Our Module

We can write:

```typescript
import "./user";

declare module "./user" {
    interface User {
        role: string;
    }
}
```

Now `User` conceptually becomes:

```typescript
interface User {
    id: number;
    name: string;
    role: string;
}
```

---

# 22. Using the Augmented Interface

```typescript
import { User } from "./user";

const user: User = {
    id: 1,
    name: "Alice",
    role: "admin"
};
```

The additional property is now recognized by TypeScript.

---

# 23. Why Import the Module?

You will often see:

```typescript
import "./user";
```

before:

```typescript
declare module "./user" {
    ...
}
```

This helps ensure TypeScript treats the declaration as augmentation of the existing module rather than accidentally describing an unrelated ambient module.

The exact setup depends on whether the file itself is already a module.

---

# 24. Third-Party Library Augmentation

One of the main real-world uses is extending third-party types.

Suppose a library defines:

```typescript
interface Request {
    ...
}
```

but our middleware adds:

```typescript
request.user
```

at runtime.

JavaScript understands the runtime assignment.

TypeScript may not.

Module augmentation lets us tell TypeScript about the property.

---

# 25. Express-Style Example

Imagine middleware does:

```typescript
request.user = {
    id: 1,
    role: "admin"
};
```

But TypeScript says:

```text
Property 'user' does not exist
on type Request.
```

We need to extend the relevant request type.

A typical augmentation may look conceptually like:

```typescript
declare module "some-request-module" {
    interface Request {
        user?: {
            id: number;
            role: string;
        };
    }
}
```

Now TypeScript knows that request objects may contain `user`.

> The exact module name and interface to augment depends on the library's actual type declarations.

---

# 26. Optional Properties in Augmentation

Middleware-added properties are often optional.

Why?

Because the property may not exist before middleware executes.

For example:

```typescript
interface Request {
    user?: User;
}
```

rather than:

```typescript
interface Request {
    user: User;
}
```

This more accurately models:

```text
Before authentication
→ user may be undefined

After authentication
→ user exists
```

---

# 27. Module Augmentation Does Not Add Runtime Behavior

This is extremely important.

Suppose we write:

```typescript
declare module "some-library" {
    interface Request {
        user: User;
    }
}
```

This does **not** actually create:

```javascript
request.user
```

at runtime.

It only tells TypeScript:

```text
This property exists according
to our type model.
```

Runtime code still needs to add the property.

---

# 28. Type Declaration vs Runtime Behavior

Consider:

```typescript
interface Window {
    appVersion: string;
}
```

This does not execute:

```javascript
window.appVersion = "1.0.0";
```

Types describe runtime values.

They do not create those values.

Always remember:

```text
Type declaration
≠
Runtime implementation
```

---

# 29. What Is Global Augmentation?

Sometimes we need to extend types that exist globally rather than inside a specific imported module.

Examples:

```text
window

globalThis

ProcessEnv

Custom global variables
```

For this we can use:

```typescript
declare global
```

---

# 30. Basic `declare global`

```typescript
export {};

declare global {
    interface Window {
        appVersion: string;
    }
}
```

Now TypeScript recognizes:

```typescript
window.appVersion;
```

as:

```typescript
string
```

---

# 31. Why `export {}`?

A file containing:

```typescript
export {};
```

is treated as a module.

This allows us to safely use:

```typescript
declare global {
    ...
}
```

to augment the global scope from within that module.

A common pattern is:

```typescript
export {};

declare global {
    ...
}
```

---

# 32. Window Augmentation

Suppose JavaScript initializes:

```typescript
window.appConfig = {
    apiUrl: "/api"
};
```

TypeScript may complain:

```text
Property 'appConfig'
does not exist on Window.
```

We can define:

```typescript
export {};

declare global {
    interface Window {
        appConfig: {
            apiUrl: string;
        };
    }
}
```

Now:

```typescript
window.appConfig.apiUrl;
```

is recognized.

---

# 33. Better Window Type

Instead of defining everything inline:

```typescript
interface AppConfig {
    apiUrl: string;
    version: string;
}

export {};

declare global {
    interface Window {
        appConfig: AppConfig;
    }
}
```

Now:

```typescript
window.appConfig
```

has type:

```typescript
AppConfig
```

This keeps the augmentation clean.

---

# 34. Optional Window Properties

If a global property may not exist immediately:

```typescript
declare global {
    interface Window {
        appConfig?: AppConfig;
    }
}
```

Then TypeScript correctly requires:

```typescript
window.appConfig?.apiUrl;
```

or another undefined check.

---

# 35. Global Interface Merging

TypeScript already defines many global interfaces.

For example:

```typescript
Window
```

When we write:

```typescript
interface Window {
    appVersion: string;
}
```

in an appropriate global declaration context, TypeScript merges our members with the existing `Window` interface.

Conceptually:

```text
Existing Window
      +
Our Window declaration
      ↓
Extended Window
```

---

# 36. Environment Variable Typing

In Node.js applications, environment variables are commonly accessed through:

```typescript
process.env
```

We may want application-specific names such as:

```text
DATABASE_URL
JWT_SECRET
PORT
```

Type augmentation can help describe these variables.

---

# 37. Process Environment Example

A common declaration file can contain something like:

```typescript
declare namespace NodeJS {
    interface ProcessEnv {
        DATABASE_URL: string;
        JWT_SECRET: string;
        PORT?: string;
    }
}
```

Now TypeScript knows those property names.

However, remember:

```text
Typing an environment variable
does not guarantee that the
environment variable exists
at runtime.
```

Runtime validation is still important.

---

# 38. Environment Variables Are Strings

A common mistake is:

```typescript
interface ProcessEnv {
    PORT: number;
}
```

Environment variables normally arrive as strings.

So:

```typescript
PORT: string;
```

is generally more accurate.

Then parse it:

```typescript
const port =
    Number(process.env.PORT);
```

Runtime validation should verify that the parsed value is valid.

---

# 39. `.d.ts` Files

Type declarations are often placed inside:

```text
.d.ts
```

files.

Example:

```text
src/
├── types/
│   ├── global.d.ts
│   ├── express.d.ts
│   └── environment.d.ts
```

A `.d.ts` file contains type declarations rather than normal runtime implementation.

---

# 40. Declaration File Example

```typescript
// global.d.ts

interface AppConfig {
    apiUrl: string;
}

interface Window {
    appConfig: AppConfig;
}
```

This can extend global declarations depending on the module/global context of the file.

---

# 41. Module Augmentation File

Example:

```typescript
// library-augmentation.d.ts

import "some-library";

declare module "some-library" {
    interface User {
        role: string;
    }
}
```

This extends the library's existing `User` interface.

---

# 42. `declare module`

There are two concepts that look similar:

```typescript
declare module "library" {
    ...
}
```

It may be used for:

```text
Ambient module declarations
```

or:

```text
Module augmentation
```

depending on context.

When augmenting an existing module, the intention is:

```text
Extend declarations that already exist.
```

---

# 43. Ambient Module Declaration

Suppose a JavaScript library has no TypeScript definitions.

We might declare:

```typescript
declare module "legacy-library" {
    export function doSomething(
        value: string
    ): void;
}
```

This describes a module to TypeScript.

That is not the same conceptual operation as augmenting an existing typed module.

---

# 44. Ambient Declaration vs Augmentation

Ambient declaration:

```text
TypeScript does not know
this module's API.

We describe it.
```

Module augmentation:

```text
TypeScript already knows
the module's API.

We extend it.
```

This distinction is important.

---

# 45. Module Augmentation Cannot Arbitrarily Create New Top-Level Exports

Module augmentation is designed to patch existing declarations.

For example, augmenting an existing interface is appropriate:

```typescript
declare module "./user" {
    interface User {
        role: string;
    }
}
```

But module augmentation is not intended as a replacement for writing actual module exports.

If you need a new runtime export, create it in runtime code.

---

# 46. Augmentation Must Match the Original Declaration

Suppose the library exports:

```typescript
interface User {
    id: number;
}
```

Then augment:

```typescript
interface User {
    role: string;
}
```

This works because interfaces support merging.

But if the library exposes something in a form that cannot participate in declaration merging, augmentation may not work the way you expect.

Always inspect the library's type declarations when augmenting third-party types.

---

# 47. You Cannot Augment a Default Export by Name

Module augmentation works by augmenting named declarations.

A default export does not give you a stable exported declaration name to target through augmentation in the same way.

This is one reason library declaration design matters.

---

# 48. Augmenting Generic Interfaces

Suppose:

```typescript
interface Container<T> {
    value: T;
}
```

If you merge another declaration:

```typescript
interface Container<T> {
    updatedAt: Date;
}
```

the generic parameter list must remain compatible.

Conceptually:

```typescript
interface Container<T> {
    value: T;
    updatedAt: Date;
}
```

---

# 49. Conflicting Merged Properties

This fails:

```typescript
interface Config {
    port: number;
}

interface Config {
    port: string;
}
```

because the same property cannot simultaneously have incompatible types.

Think:

```text
Declaration merging
does not mean

"last declaration wins"
```

It means:

```text
Declarations must combine
into a valid type.
```

---

# 50. Optional vs Required Conflicts

Be careful with:

```typescript
interface User {
    role?: string;
}

interface User {
    role: string;
}
```

Merged declarations for the same property must have compatible modifiers and types.

You cannot use merging to redefine an existing property however you want.

---

# 51. Declaration Merging Is Not Inheritance

Consider:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

This is merging.

Inheritance is:

```typescript
interface User {
    name: string;
}

interface Admin extends User {
    permissions: string[];
}
```

These solve different problems.

---

# 52. Merging vs Inheritance

Declaration merging:

```text
Same declaration name
+
Same declaration name
        ↓
One combined declaration
```

Inheritance:

```text
Base Type
   ↓
Derived Type
```

Example:

```text
User
  ↓
Admin
```

Do not confuse the two.

---

# 53. Merging vs Intersection

Intersection:

```typescript
type A = {
    name: string;
};

type B = {
    age: number;
};

type User = A & B;
```

produces a type requiring both structures.

Declaration merging:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

also produces a combined interface.

But the mechanisms are different.

---

# 54. When to Use Interface Extension Instead

If you control the types and want to model a relationship:

```typescript
interface User {
    id: number;
    name: string;
}

interface Admin extends User {
    permissions: string[];
}
```

is usually clearer than relying on declaration merging.

Declaration merging is especially valuable for:

```text
Open APIs

Library extensions

Global extensions

Plugin architectures
```

---

# 55. Real-World Example — Authentication Middleware

Suppose authentication middleware sets:

```typescript
request.user = authenticatedUser;
```

Our user type:

```typescript
interface AuthUser {
    id: string;
    role: "admin" | "user";
}
```

We can augment the relevant request interface:

```typescript
declare module "request-library" {
    interface Request {
        user?: AuthUser;
    }
}
```

Then route handlers can access:

```typescript
request.user
```

with type information.

---

# 56. Runtime Middleware Still Required

The augmentation:

```typescript
interface Request {
    user?: AuthUser;
}
```

does not authenticate anyone.

We still need runtime logic:

```typescript
request.user =
    await authenticateUser();
```

Think:

```text
Middleware
→ Creates runtime property

Augmentation
→ Describes property to TypeScript
```

---

# 57. Real-World Example — Request ID

Middleware might add:

```typescript
request.requestId =
    crypto.randomUUID();
```

Type augmentation:

```typescript
declare module "request-library" {
    interface Request {
        requestId: string;
    }
}
```

Then:

```typescript
console.log(
    request.requestId
);
```

is type-safe.

---

# 58. Real-World Example — Application Globals

Suppose HTML or startup code provides:

```javascript
window.__APP_CONFIG__ = {
    apiUrl: "/api",
    environment: "production"
};
```

We can model it:

```typescript
type Environment =
    "development"
    | "staging"
    | "production";

interface AppConfig {
    apiUrl: string;
    environment: Environment;
}

export {};

declare global {
    interface Window {
        __APP_CONFIG__: AppConfig;
    }
}
```

Now:

```typescript
window.__APP_CONFIG__.apiUrl;
```

is typed.

---

# 59. Real-World Example — Plugin System

Suppose an application allows plugins to contribute functionality.

Original interface:

```typescript
interface PluginRegistry {
    logger: LoggerPlugin;
}
```

Another package could contribute:

```typescript
interface PluginRegistry {
    metrics: MetricsPlugin;
}
```

Another:

```typescript
interface PluginRegistry {
    cache: CachePlugin;
}
```

The merged interface becomes:

```text
PluginRegistry
├── logger
├── metrics
└── cache
```

This open-interface pattern can support extensible architectures.

---

# 60. Real-World Example — Metadata Registry

```typescript
interface Metadata {
    createdAt: Date;
}
```

Another module:

```typescript
interface Metadata {
    updatedAt: Date;
}
```

Another:

```typescript
interface Metadata {
    createdBy: string;
}
```

Result:

```typescript
interface Metadata {
    createdAt: Date;
    updatedAt: Date;
    createdBy: string;
}
```

Again, use this intentionally; ordinary interfaces should not be scattered across a codebase without a clear reason.

---

# 61. Common Mistake — Accidental Interface Merging

Suppose two declarations unintentionally share the same name:

```typescript
interface Config {
    apiUrl: string;
}
```

Elsewhere in the same applicable declaration scope:

```typescript
interface Config {
    theme: string;
}
```

You might expect two unrelated interfaces.

Instead, TypeScript may merge them.

Now `Config` requires:

```text
apiUrl
theme
```

This can create confusing errors.

---

# 62. Avoid Accidental Globals

Modern TypeScript projects generally use modules:

```typescript
export {};
```

or actual imports/exports.

This prevents unrelated declarations from leaking into the global scope.

Example:

```typescript
export interface Config {
    apiUrl: string;
}
```

is scoped to its module.

---

# 63. Common Mistake — Thinking `declare` Creates Something

Consider:

```typescript
declare const API_URL: string;
```

This tells TypeScript:

```text
API_URL exists at runtime.
Trust this declaration.
```

It does not create:

```javascript
const API_URL = "...";
```

Similarly:

```typescript
declare global
```

and:

```typescript
declare module
```

describe or augment types.

They do not create runtime functionality.

---

# 64. Common Mistake — Augmenting the Wrong Module

Suppose a library exposes a request type through an internal package.

You augment:

```typescript
declare module "library" {
    interface Request {
        user: User;
    }
}
```

but the actual `Request` interface lives in:

```text
library-core
```

Your augmentation may not affect the type you expect.

Always identify the module where the declaration actually originates.

---

# 65. Common Mistake — Incorrect Module Specifier

The string inside:

```typescript
declare module "..."
```

must match the module declaration/import path being augmented.

For example:

```typescript
declare module "./user"
```

is not necessarily the same as:

```typescript
declare module "./user.js"
```

or:

```typescript
declare module "@app/user"
```

Module resolution rules matter.

---

# 66. Common Mistake — `.d.ts` Not Included

You may create:

```text
src/types/global.d.ts
```

but TypeScript still ignores it.

Why?

The file may not be included by your `tsconfig.json`.

For example:

```json
{
    "include": [
        "src"
    ]
}
```

will generally include declaration files under `src`.

But if your declarations live outside the included paths, TypeScript may not see them.

---

# 67. Check `tsconfig.json`

If augmentation seems not to work, check:

```text
include

exclude

files

typeRoots

types

moduleResolution
```

These settings can affect which declarations TypeScript sees.

We will cover `tsconfig.json` separately.

---

# 68. Common Mistake — Making Runtime Values Required

Suppose middleware conditionally adds:

```typescript
request.user
```

But you declare:

```typescript
interface Request {
    user: User;
}
```

Now TypeScript assumes:

```text
request.user always exists
```

even when runtime behavior says otherwise.

This is unsafe.

Use:

```typescript
user?: User;
```

when absence is possible.

---

# 69. Narrow Optional Augmented Properties

If:

```typescript
request.user?: User;
```

then:

```typescript
request.user.id
```

may produce an error.

Correct:

```typescript
if (request.user) {
    console.log(
        request.user.id
    );
}
```

or:

```typescript
request.user?.id;
```

depending on the required behavior.

---

# 70. Better Authentication Design

After authentication, sometimes you want a type guaranteeing that `user` exists.

Instead of globally pretending every request is authenticated, define a narrower type for authenticated code.

Conceptually:

```typescript
interface AuthenticatedRequest
    extends Request {

    user: User;
}
```

This can be safer than making the globally augmented property required.

---

# 71. Common Mistake — Overusing Global Augmentation

Global augmentation is powerful, but excessive use can make code difficult to understand.

If every file silently modifies global types, developers may struggle to determine where properties originate.

Prefer explicit imports and local types when possible.

Use global augmentation when the runtime API itself is genuinely global.

---

# 72. Common Mistake — Using Merging Instead of Composition

Suppose you control everything.

Instead of:

```typescript
interface User {
    id: number;
}

interface User {
    profile: Profile;
}

interface User {
    permissions: Permission[];
}
```

it may be clearer to define:

```typescript
interface User {
    id: number;
    profile: Profile;
    permissions: Permission[];
}
```

Declaration merging is not a code-organization technique by default.

Use it when openness/extensibility is intentional.

---

# 73. Common Mistake — Conflicting Library Types

Suppose a library already defines:

```typescript
interface Request {
    user: LibraryUser;
}
```

and you augment:

```typescript
interface Request {
    user: MyUser;
}
```

If the declarations conflict, TypeScript reports errors.

You cannot arbitrarily overwrite existing properties through declaration merging.

---

# 74. Declaration Merging and Libraries

Declaration merging is especially useful for library authors because consumers can extend certain interfaces.

For example, a library might expose:

```typescript
interface CustomTypes {}
```

and use:

```typescript
keyof CustomTypes
```

elsewhere.

Consumers can augment:

```typescript
interface CustomTypes {
    user: User;
}
```

This enables extensible type systems.

---

# 75. Open vs Closed Type Design

Interfaces support an open model:

```text
interface
    ↓
Can be augmented
```

Type aliases are more closed:

```text
type
    ↓
Cannot be redeclared
```

This can influence API design.

If extension by consumers is intentional, an interface may be useful.

If you want a fixed alias representing a union or complex composition, `type` may be more appropriate.

---

# 76. Declaration Merging and DOM Types

TypeScript's DOM definitions contain interfaces such as:

```typescript
Window
Document
HTMLElement
```

Global interface augmentation allows applications to describe custom additions to these runtime objects.

Example:

```typescript
export {};

declare global {
    interface Window {
        analytics: AnalyticsClient;
    }
}
```

Then:

```typescript
window.analytics.track(
    "page_view"
);
```

can be typed.

---

# 77. But Ensure Runtime Initialization

Even if TypeScript accepts:

```typescript
window.analytics.track(
    "page_view"
);
```

runtime code must have actually assigned:

```typescript
window.analytics = ...
```

Otherwise JavaScript can fail.

Type safety cannot create missing runtime values.

---

# 78. Declaration File Organization

A project might use:

```text
src/
│
├── types/
│   │
│   ├── global.d.ts
│   ├── environment.d.ts
│   └── third-party.d.ts
│
├── services/
├── components/
└── app.ts
```

Possible responsibilities:

```text
global.d.ts
→ Global browser/application declarations

environment.d.ts
→ Environment variable types

third-party.d.ts
→ Library augmentations
```

The exact organization is a project convention, not a TypeScript requirement.

---

# 79. Interview Question — What Is Declaration Merging?

Declaration merging is a TypeScript feature where multiple compatible declarations with the same name are combined into a single declaration.

Example:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

Result conceptually:

```typescript
interface User {
    name: string;
    age: number;
}
```

---

# 80. Interview Question — Can Type Aliases Merge?

No.

This is invalid:

```typescript
type User = {
    name: string;
};

type User = {
    age: number;
};
```

Type aliases cannot be redeclared in the same scope this way.

Interfaces can participate in declaration merging.

---

# 81. Interview Question — What Is Module Augmentation?

Module augmentation allows us to extend existing declarations belonging to another module.

Example:

```typescript
import "./user";

declare module "./user" {
    interface User {
        role: string;
    }
}
```

The existing `User` interface gains:

```typescript
role: string;
```

at the type level.

---

# 82. Interview Question — Does Module Augmentation Change Runtime Code?

No.

It changes TypeScript's understanding of the runtime API.

Actual runtime behavior must already exist or be implemented separately.

---

# 83. Interview Question — What Is `declare global`?

`declare global` allows declarations inside a module to augment the global declaration scope.

Example:

```typescript
export {};

declare global {
    interface Window {
        appVersion: string;
    }
}
```

Now TypeScript recognizes:

```typescript
window.appVersion;
```

---

# 84. Interview Question — Why Use `export {}`?

`export {}` marks the file as a module.

This is commonly used when a file contains only type/global augmentation declarations but needs module context for:

```typescript
declare global
```

---

# 85. Interview Question — Declaration Merging vs Inheritance?

Merging:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

creates one combined `User`.

Inheritance:

```typescript
interface Admin extends User {
    permissions: string[];
}
```

creates a different type based on another type.

---

# 86. Interview Question — Declaration Merging vs Intersection?

Intersection:

```typescript
type User =
    NameInfo &
    AgeInfo;
```

explicitly combines multiple types into another type.

Declaration merging:

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}
```

combines declarations sharing the same interface identity.

---

# 87. Interview Question — Why Is Module Augmentation Useful?

Typical examples include:

```text
Adding authenticated users to request types

Adding custom globals to Window

Typing plugin-added properties

Extending library configuration

Extending framework contexts

Describing middleware modifications

Extending library registries
```

---

# 88. Interview Question — What Is a `.d.ts` File?

A `.d.ts` file is a TypeScript declaration file.

It describes types and APIs without providing their normal runtime implementation.

Examples:

```text
global.d.ts

environment.d.ts

library.d.ts
```

---

# 89. Interview Question — Can Augmentation Add Runtime Methods?

No.

You can declare that a method exists:

```typescript
interface User {
    getRole(): string;
}
```

but you must separately ensure that:

```javascript
user.getRole
```

actually exists at runtime.

Declarations describe behavior; they do not implement it.

---

# 90. Cheat Sheet

```text
DECLARATION MERGING
================================

Multiple compatible declarations
with the same name

        ↓

Combined declaration


INTERFACE MERGING
================================

interface User {
    name: string;
}

interface User {
    age: number;
}

Result:

User {
    name
    age
}


TYPE ALIAS
================================

type User = {...}

type User = {...}

→ Error

Type aliases do not merge.


NAMESPACE MERGING
================================

namespace App {
    export const name = "App";
}

namespace App {
    export const version = "1";
}

App.name
App.version


CLASS + NAMESPACE
================================

class User {}

namespace User {
    export const role = "user";
}

new User()

User.role


FUNCTION + NAMESPACE
================================

function build() {}

namespace build {
    export const version = "1";
}

build()

build.version


ENUM + NAMESPACE
================================

enum Status {
    Success,
    Failed
}

namespace Status {
    export function isFinal() {}
}

Status.Success

Status.isFinal()


MODULE AUGMENTATION
================================

import "library";

declare module "library" {

    interface Something {
        customProperty: string;
    }

}


GLOBAL AUGMENTATION
================================

export {};

declare global {

    interface Window {
        appVersion: string;
    }

}


WINDOW
================================

window.appVersion


ENVIRONMENT VARIABLES
================================

declare namespace NodeJS {

    interface ProcessEnv {
        DATABASE_URL: string;
    }

}


DECLARATION FILE
================================

*.d.ts


IMPORTANT
================================

Type declaration

≠

Runtime implementation


INTERFACE
================================

Open / augmentable declaration


TYPE
================================

Cannot be redeclared
for declaration merging


MODULE AUGMENTATION
================================

Existing module
      +
Our declaration
      ↓
Extended type information


GLOBAL AUGMENTATION
================================

Existing global type
      +
Our declaration
      ↓
Extended global type


COMMON USE CASES
================================

Request.user

Window configuration

Environment variables

Plugin systems

Framework contexts

Third-party library extensions

Middleware-added properties
```

---

# 91. Important Rules to Remember

```text
1. Declaration merging combines compatible declarations.

2. Interfaces can participate in declaration merging.

3. Type aliases cannot be redeclared for merging.

4. Same-name interface declarations are combined.

5. Merged properties must be compatible.

6. Conflicting property types produce errors.

7. Declaration merging is not inheritance.

8. Declaration merging is not intersection typing.

9. Namespaces can merge.

10. Exported namespace members form the public API.

11. Classes can merge with namespaces in supported patterns.

12. Functions can merge with namespaces.

13. Enums can merge with namespaces.

14. Function + namespace can model callable objects with properties.

15. Class + namespace can model static-like additions.

16. Module augmentation extends existing module declarations.

17. declare module can be used for module augmentation.

18. The module specifier must identify the intended module.

19. Module augmentation does not change runtime behavior.

20. Runtime properties must actually exist.

21. Global augmentation extends global declarations.

22. declare global is used for global augmentation from module context.

23. export {} can mark a declaration file as a module.

24. Window can be globally augmented.

25. Middleware request types can be augmented.

26. Environment variable types can be augmented.

27. Environment variables are generally strings at runtime.

28. Typing an environment variable does not guarantee it exists.

29. Runtime validation may still be required.

30. .d.ts files contain declarations.

31. Declaration files do not normally provide runtime implementations.

32. Augmentation should match the original declaration.

33. Interfaces are particularly suitable for augmentation.

34. You cannot arbitrarily overwrite existing properties.

35. Existing and augmented members must remain compatible.

36. Generic declarations must merge compatibly.

37. Optional and required modifiers cannot be arbitrarily changed.

38. Module augmentation is useful for third-party libraries.

39. It is useful for middleware-added properties.

40. It is useful for plugin architectures.

41. It is useful for global browser configuration.

42. It is useful for framework contexts.

43. It is useful for library registries.

44. Global augmentation should be used carefully.

45. Excessive globals make code harder to understand.

46. Explicit imports are preferable when global behavior is unnecessary.

47. Avoid accidental interface merging.

48. Modules help avoid accidental global declarations.

49. Merging should be intentional.

50. Do not use declaration merging merely to split ordinary interfaces.

51. Use composition when composition better models the domain.

52. Use inheritance when there is an actual type relationship.

53. Use merging when declarations intentionally share an identity.

54. Module augmentation extends types, not runtime code.

55. Global augmentation extends types, not runtime objects.

56. declare does not create JavaScript values.

57. declare tells TypeScript about values expected to exist.

58. Runtime implementation remains necessary.

59. Optional middleware properties should generally be typed optional.

60. Narrow optional properties before using them.

61. Do not claim a property always exists if runtime behavior disagrees.

62. Authenticated request types can provide stronger guarantees after authentication.

63. Augment the declaration that actually owns the type.

64. Inspect third-party declaration files when necessary.

65. Incorrect module paths can prevent augmentation.

66. tsconfig can affect declaration discovery.

67. include determines files TypeScript includes.

68. exclude can remove files from compilation.

69. typeRoots and types can affect available declarations.

70. Declaration files must be visible to the compiler.

71. Ambient module declarations and module augmentation are related but different.

72. Ambient declarations describe modules TypeScript does not otherwise know.

73. Augmentation extends modules TypeScript already knows.

74. Declaration merging is a compile-time TypeScript feature.

75. It does not perform runtime validation.

76. It does not create properties.

77. It does not initialize globals.

78. It does not execute middleware.

79. It does not install plugins.

80. The core mental model is:

Existing declaration
        +
Additional compatible declaration
        ↓
Merged type information
```

---

# 92. Final Mental Model

Declaration merging:

```text
interface User
┌─────────────────┐
│ name: string    │
└─────────────────┘

        +

interface User
┌─────────────────┐
│ age: number     │
└─────────────────┘

        ↓

interface User
┌─────────────────┐
│ name: string    │
│ age: number     │
└─────────────────┘
```

Module augmentation:

```text
Third-Party Module

Request
┌──────────────────┐
│ Existing fields  │
└──────────────────┘

        +

Our Augmentation

Request
┌──────────────────┐
│ user?: User      │
└──────────────────┘

        ↓

TypeScript sees:

Request
┌──────────────────┐
│ Existing fields  │
│ user?: User      │
└──────────────────┘
```

But runtime behavior is separate:

```text
TYPE LEVEL

declare module ...
        ↓
TypeScript knows request.user


RUNTIME

Authentication middleware
        ↓
request.user = user
```

Both must agree.

The shortest rule to remember:

```text
Declaration Merging
→ Same declaration identity
  gets additional members


Module Augmentation
→ Extend declarations
  inside an existing module


Global Augmentation
→ Extend global declarations


.d.ts
→ Describe types/APIs


declare
→ Tell TypeScript something exists


declare
≠
Create it at runtime
```

The most common practical pattern you are likely to encounter is:

```typescript
import "some-library";

declare module "some-library" {
    interface ExistingInterface {
        customProperty?: MyType;
    }
}
```

or global augmentation:

```typescript
export {};

declare global {
    interface Window {
        appConfig: AppConfig;
    }
}
```

Always remember:

```text
Runtime code creates behavior.

Type declarations describe behavior.
```
