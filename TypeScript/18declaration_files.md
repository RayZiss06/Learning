# TypeScript — Declaration Files (`.d.ts`) & `declare`

TypeScript needs type information to understand JavaScript libraries, browser APIs, Node.js APIs, global variables, and other runtime code.

A **declaration file** describes the types of existing JavaScript code without implementing that code.

Declaration files use the extension:

```text
.d.ts
```

Example:

```text
index.d.ts
global.d.ts
environment.d.ts
types.d.ts
```

The `declare` keyword tells TypeScript:

> A value, function, class, module, or namespace exists at runtime. Here is its type.

The key idea is:

```text
Implementation
→ Creates runtime behavior

Declaration
→ Describes runtime behavior to TypeScript
```

---

# 1. What Is a Declaration File?

A declaration file is a TypeScript file ending in:

```text
.d.ts
```

Its purpose is to provide **type information**.

For example, imagine JavaScript provides:

```javascript
function greet(name) {
    return `Hello ${name}`;
}
```

A declaration file could describe it as:

```typescript
declare function greet(
    name: string
): string;
```

TypeScript now understands:

```typescript
greet("Alice");
```

and rejects:

```typescript
greet(100);
```

The declaration describes the function.

It does not implement it.

---

# 2. `.ts` vs `.d.ts`

Normal TypeScript:

```text
user.ts
```

can contain implementation:

```typescript
export function getUser() {
    return {
        id: 1,
        name: "Alice"
    };
}
```

Declaration file:

```text
user.d.ts
```

describes an API:

```typescript
export declare function getUser(): {
    id: number;
    name: string;
};
```

Think:

```text
.ts
→ Implementation + Types

.d.ts
→ Type declarations
```

---

# 3. Why Declaration Files Exist

TypeScript needs type information about JavaScript code.

Suppose a JavaScript library contains:

```javascript
export function add(a, b) {
    return a + b;
}
```

JavaScript itself does not explicitly tell TypeScript:

```text
a → number
b → number
return → number
```

A declaration can provide that information:

```typescript
export function add(
    a: number,
    b: number
): number;
```

Now TypeScript understands the library's API.

---

# 4. What Does `declare` Mean?

Consider:

```typescript
declare const API_URL: string;
```

This means:

```text
TypeScript:

Assume API_URL exists at runtime.

Its type is string.
```

It does **not** mean:

```typescript
const API_URL = "...";
```

No runtime value is created.

---

# 5. `declare` Mental Model

Think:

```text
declare
   │
   ▼
"This exists somewhere
at runtime."
   │
   ▼
"Here is its type."
```

For example:

```typescript
declare const VERSION: string;
```

TypeScript allows:

```typescript
console.log(
    VERSION.toUpperCase()
);
```

because it knows `VERSION` is a string.

But JavaScript must actually provide `VERSION` at runtime.

---

# 6. `declare const`

Suppose some runtime environment provides:

```javascript
globalThis.APP_VERSION = "1.0.0";
```

We may describe a global variable:

```typescript
declare const APP_VERSION: string;
```

Then:

```typescript
console.log(APP_VERSION);
```

is type-checked as a string.

Again:

```text
declare const
→ Describe existing constant

const
→ Create constant
```

---

# 7. `declare let`

We can describe an existing mutable variable:

```typescript
declare let currentUser: string;
```

TypeScript understands:

```typescript
currentUser = "Alice";
```

because its type is:

```typescript
string
```

But:

```typescript
currentUser = 100;
```

produces an error.

---

# 8. `declare var`

Older JavaScript environments may expose global variables.

We can describe them using:

```typescript
declare var legacyVersion: string;
```

This tells TypeScript that the variable exists.

Modern TypeScript code generally prefers `const` or `let` when those semantics match the runtime API.

---

# 9. `declare function`

Suppose a JavaScript file provides:

```javascript
function calculate(a, b) {
    return a + b;
}
```

A declaration could be:

```typescript
declare function calculate(
    a: number,
    b: number
): number;
```

Now:

```typescript
calculate(10, 20);
```

is valid.

But:

```typescript
calculate("10", "20");
```

is rejected by TypeScript.

---

# 10. No Function Body

This is a declaration:

```typescript
declare function greet(
    name: string
): string;
```

We do not write:

```typescript
declare function greet(
    name: string
): string {
    return `Hello ${name}`;
}
```

because `declare` describes an implementation that exists elsewhere.

---

# 11. Function Overloads in Declarations

A runtime library may support:

```javascript
format("hello");

format(100);
```

We can describe both:

```typescript
declare function format(
    value: string
): string;

declare function format(
    value: number
): string;
```

Now:

```typescript
format("hello");
format(100);
```

are valid.

But:

```typescript
format(true);
```

is invalid.

---

# 12. `declare class`

Suppose an external JavaScript library provides:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        return `Hello ${this.name}`;
    }
}
```

We can describe it:

```typescript
declare class User {
    constructor(name: string);

    name: string;

    greet(): string;
}
```

Then TypeScript understands:

```typescript
const user =
    new User("Alice");

user.greet();
```

---

# 13. Declaration Class Has No Implementation

We describe:

```typescript
declare class User {
    constructor(name: string);

    name: string;

    greet(): string;
}
```

We do not implement:

```typescript
greet() {
    ...
}
```

The actual JavaScript implementation must already exist.

---

# 14. `declare namespace`

Suppose a JavaScript library exposes:

```javascript
Analytics.track("login");

Analytics.version;
```

We can describe it:

```typescript
declare namespace Analytics {
    const version: string;

    function track(
        event: string
    ): void;
}
```

Now TypeScript understands:

```typescript
Analytics.track("login");

console.log(
    Analytics.version
);
```

---

# 15. Namespace Declaration

The declaration:

```typescript
declare namespace Analytics {
    const version: string;

    function track(
        event: string
    ): void;
}
```

models a runtime object shaped roughly like:

```javascript
Analytics = {
    version: "...",
    track(event) {
        ...
    }
};
```

The declaration itself does not create that object.

---

# 16. What Is an Ambient Declaration?

A declaration describing something that exists elsewhere is commonly called an:

```text
Ambient Declaration
```

Examples:

```typescript
declare const VERSION: string;
```

```typescript
declare function greet(
    name: string
): string;
```

```typescript
declare class User {
    ...
}
```

The word **ambient** means that TypeScript is being told about an environment/API rather than implementing it.

---

# 17. Ambient Context

Declaration files are ambient declaration contexts.

For example, inside:

```text
library.d.ts
```

we might write:

```typescript
export function greet(
    name: string
): string;
```

We usually do not need:

```typescript
export declare function greet(
    name: string
): string;
```

because declarations inside `.d.ts` files are already declaration-only.

Both concepts point to the same idea:

```text
No runtime implementation here.
```

---

# 18. Declaration Files Produce No Runtime Logic

Suppose:

```typescript
// globals.d.ts

declare const API_URL: string;
```

TypeScript knows:

```typescript
API_URL
```

exists.

But the generated JavaScript does not suddenly contain:

```javascript
const API_URL = "...";
```

If the runtime does not provide it:

```typescript
console.log(API_URL);
```

can fail at runtime.

---

# 19. Declaration Files Are Contracts

A useful mental model is:

```text
JavaScript Runtime
        │
        ▼
Actual API
        │
        ↕
Declaration File
        │
        ▼
TypeScript Understanding
```

The declaration should accurately describe the runtime API.

If it lies, TypeScript cannot protect you.

---

# 20. Incorrect Declaration Example

Suppose runtime JavaScript returns:

```javascript
function getAge() {
    return "25";
}
```

But we declare:

```typescript
declare function getAge(): number;
```

TypeScript believes:

```typescript
const age = getAge();

age.toFixed(2);
```

is safe.

But runtime actually returns a string.

The declaration was incorrect.

Therefore:

```text
Declaration correctness
must match
runtime behavior.
```

---

# 21. Declaring Global Variables

Suppose HTML loads:

```html
<script>
    window.APP_NAME =
        "Enterprise Platform";
</script>
```

If another script expects a global:

```typescript
declare const APP_NAME: string;
```

TypeScript can understand:

```typescript
console.log(APP_NAME);
```

provided the runtime actually exposes the value under that global identifier.

---

# 22. Declaring Global Functions

Suppose a browser script provides:

```javascript
function showNotification(message) {
    alert(message);
}
```

Declaration:

```typescript
declare function showNotification(
    message: string
): void;
```

Usage:

```typescript
showNotification(
    "Deployment complete"
);
```

---

# 23. Declaring Global Objects

Suppose runtime provides:

```javascript
const App = {
    version: "1.0.0",

    start() {
        console.log("Started");
    }
};
```

We could describe it:

```typescript
declare const App: {
    version: string;
    start(): void;
};
```

Usage:

```typescript
App.start();

console.log(App.version);
```

---

# 24. Using Interfaces in Declarations

Instead of:

```typescript
declare const App: {
    version: string;
    start(): void;
};
```

we can write:

```typescript
interface AppAPI {
    version: string;

    start(): void;
}

declare const App: AppAPI;
```

This becomes useful for larger APIs.

---

# 25. Declaring External Modules

Suppose TypeScript does not have types for:

```typescript
import legacy from "legacy-library";
```

We can create:

```text
legacy-library.d.ts
```

and declare:

```typescript
declare module "legacy-library" {
    export default function legacy(
        value: string
    ): number;
}
```

Now TypeScript understands:

```typescript
import legacy
    from "legacy-library";

const result =
    legacy("hello");
```

---

# 26. Ambient Module Declaration

This:

```typescript
declare module "legacy-library" {
    ...
}
```

can describe a module that otherwise lacks type information.

Think:

```text
JavaScript Package
        │
        ▼
No TypeScript Types
        │
        ▼
Our .d.ts
        │
        ▼
TypeScript understands package
```

---

# 27. Named Exports

Suppose JavaScript exports:

```javascript
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}
```

Declaration:

```typescript
declare module "math-library" {
    export function add(
        a: number,
        b: number
    ): number;

    export function subtract(
        a: number,
        b: number
    ): number;
}
```

Usage:

```typescript
import {
    add,
    subtract
} from "math-library";
```

---

# 28. Default Export

Suppose:

```javascript
export default function greet(name) {
    return `Hello ${name}`;
}
```

Declaration:

```typescript
declare module "greeting-library" {
    export default function greet(
        name: string
    ): string;
}
```

Usage:

```typescript
import greet
    from "greeting-library";
```

---

# 29. Exporting Interfaces

A declaration file can expose types:

```typescript
declare module "user-library" {
    export interface User {
        id: number;
        name: string;
    }

    export function getUser(
        id: number
    ): User;
}
```

Usage:

```typescript
import {
    User,
    getUser
} from "user-library";

const user: User =
    getUser(1);
```

---

# 30. Typing a JavaScript File

Suppose:

```text
math.js
```

contains:

```javascript
export function multiply(a, b) {
    return a * b;
}
```

A matching declaration could be:

```text
math.d.ts
```

containing:

```typescript
export function multiply(
    a: number,
    b: number
): number;
```

Now consumers can receive type information for the JavaScript module.

---

# 31. Matching JavaScript and Declaration Files

Conceptually:

```text
math.js
→ Runtime implementation

math.d.ts
→ Type declaration
```

For example:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

```typescript
// math.d.ts

export function add(
    a: number,
    b: number
): number;
```

These two files describe the same public API at different levels.

---

# 32. Declaration Files for JavaScript Libraries

A JavaScript package may contain:

```text
my-library/
│
├── index.js
├── index.d.ts
└── package.json
```

Where:

```text
index.js
→ Runtime library

index.d.ts
→ TypeScript declarations
```

This allows JavaScript libraries to provide first-class TypeScript support without rewriting the implementation in TypeScript.

---

# 33. What Are `@types` Packages?

Many JavaScript libraries historically did not ship their own TypeScript declarations.

The community provides declarations through packages under:

```text
@types/*
```

For example:

```bash
npm install --save-dev @types/node
```

This installs type declarations for Node.js APIs.

---

# 34. Example — Node.js Types

Without Node types, TypeScript may not understand:

```typescript
process
```

or:

```typescript
Buffer
```

or Node-specific modules.

Installing:

```bash
npm install --save-dev @types/node
```

provides declaration files describing Node.js APIs.

---

# 35. `@types` Naming Convention

If a package is:

```text
example-library
```

its community type package may be:

```text
@types/example-library
```

For scoped packages, the naming transformation may differ.

In practice, first check whether the library already ships its own TypeScript declarations before installing a separate `@types` package.

---

# 36. Libraries Can Ship Their Own Types

Modern libraries often include:

```text
index.d.ts
```

or generated declarations directly in the package.

Their `package.json` may point to declarations through package metadata such as:

```json
{
    "types": "./dist/index.d.ts"
}
```

or equivalent modern package export configuration.

Then consumers do not need a separate `@types` package.

---

# 37. How to Know If a Library Has Types

Install/import the package normally.

If TypeScript understands its API, the package likely:

```text
Ships its own types
```

or:

```text
Has installed declarations available
```

If TypeScript reports that it cannot find declaration information, then investigate whether:

```text
The package ships types

An @types package exists

You need custom declarations
```

---

# 38. DefinitelyTyped

Many `@types` packages are maintained through the community project:

```text
DefinitelyTyped
```

It is a large collection of TypeScript declaration files for JavaScript libraries.

Packages published from it generally appear under:

```text
@types/*
```

---

# 39. Three Common Library Scenarios

When installing a library, you will commonly encounter:

```text
Scenario 1
============================

Library written in TypeScript
or ships declarations

→ Nothing extra required


Scenario 2
============================

JavaScript library
+
@types package available

→ Install @types package


Scenario 3
============================

JavaScript library
+
No types available

→ Write custom .d.ts
```

---

# 40. Missing Declaration Error

You may encounter an error similar to:

```text
Could not find a declaration file
for module 'some-library'.
```

This means TypeScript found the JavaScript package but does not have enough type information for it.

Possible solutions:

```text
1. Check whether the package ships types

2. Install its @types package

3. Write a custom declaration
```

---

# 41. Quick Temporary Module Declaration

Suppose:

```typescript
import legacy
    from "legacy-library";
```

has no types.

A quick declaration is:

```typescript
declare module "legacy-library";
```

This tells TypeScript that the module exists.

But its API will generally be treated very loosely, often effectively as `any`.

This removes useful type safety.

---

# 42. Better Custom Declaration

Instead of:

```typescript
declare module "legacy-library";
```

write:

```typescript
declare module "legacy-library" {
    export function parse(
        input: string
    ): ParsedResult;

    export interface ParsedResult {
        value: string;
        valid: boolean;
    }
}
```

Now TypeScript can actually protect you.

---

# 43. Avoid `any` Declarations When Possible

This:

```typescript
declare module "legacy-library";
```

is useful as a temporary escape hatch.

But ideally replace it with actual declarations.

Think:

```text
declare module "library";
→ "I know this exists,
   but I don't know its types."


Detailed declaration
→ "I know this exists,
   and here is its API."
```

---

# 44. Wildcard Module Declarations

TypeScript projects often import non-code assets.

For example:

```typescript
import logo
    from "./logo.svg";
```

Depending on your tooling, TypeScript may need a declaration explaining what an SVG import represents.

We can use wildcard module declarations.

---

# 45. Declaring SVG Imports

Example:

```typescript
declare module "*.svg" {
    const src: string;

    export default src;
}
```

Now:

```typescript
import logo
    from "./logo.svg";
```

can be understood as:

```typescript
logo: string
```

if that matches the bundler's runtime behavior.

---

# 46. Declaring PNG Imports

```typescript
declare module "*.png" {
    const src: string;

    export default src;
}
```

Usage:

```typescript
import avatar
    from "./avatar.png";
```

TypeScript understands:

```typescript
avatar
```

as:

```typescript
string
```

provided the build tooling treats the import that way.

---

# 47. Declaring Other Image Types

We could define:

```typescript
declare module "*.jpg" {
    const src: string;
    export default src;
}

declare module "*.jpeg" {
    const src: string;
    export default src;
}

declare module "*.webp" {
    const src: string;
    export default src;
}
```

Again, declarations must match your bundler/framework behavior.

---

# 48. CSS Module Declarations

Suppose:

```typescript
import styles
    from "./Button.module.css";
```

We might describe CSS modules:

```typescript
declare module "*.module.css" {
    const classes: {
        readonly [key: string]: string;
    };

    export default classes;
}
```

Now:

```typescript
styles.button
```

has type:

```typescript
string
```

---

# 49. Better CSS Module Types

The generic declaration:

```typescript
readonly [key: string]: string;
```

allows any key.

So this may compile:

```typescript
styles.doesNotExist
```

even if that CSS class does not exist.

Some tooling can generate exact declaration files containing actual class names.

Example:

```typescript
declare const styles: {
    readonly button: string;
    readonly container: string;
};

export default styles;
```

This provides stronger safety.

---

# 50. JSON Imports

Modern TypeScript can support JSON imports through compiler/module settings.

For example:

```typescript
import config
    from "./config.json";
```

Depending on the project configuration, TypeScript can infer the JSON structure directly.

You generally should not create wildcard JSON declarations unless your tooling/setup actually requires them.

---

# 51. Typing Text File Imports

Suppose a bundler supports:

```typescript
import content
    from "./readme.txt";
```

We could declare:

```typescript
declare module "*.txt" {
    const content: string;

    export default content;
}
```

Then TypeScript knows:

```typescript
content
```

is a string.

---

# 52. Typing Markdown Imports

If your build system imports Markdown as raw text:

```typescript
import markdown
    from "./article.md";
```

a declaration could be:

```typescript
declare module "*.md" {
    const content: string;

    export default content;
}
```

But only use this if your runtime/bundler actually returns a string for that import.

---

# 53. Declaration Must Match Tooling

This is important.

Suppose you write:

```typescript
declare module "*.svg" {
    const src: string;
    export default src;
}
```

but your framework actually transforms SVG files into components.

Your declaration would be incorrect.

Declarations must match:

```text
Bundler behavior

Framework behavior

Runtime behavior
```

---

# 54. TypeScript Standard Library Declarations

When you write:

```typescript
const values =
    [1, 2, 3];

values.map(...);
```

you did not define the type of:

```typescript
Array.prototype.map
```

TypeScript knows it because standard library declaration files describe JavaScript's built-in APIs.

---

# 55. `lib.d.ts`

TypeScript ships declaration files for standard JavaScript and environment APIs.

Conceptually:

```text
lib.es*.d.ts
→ JavaScript language APIs

lib.dom.d.ts
→ Browser DOM APIs
```

These declarations teach TypeScript about things such as:

```text
Array

Promise

Map

Set

Window

Document

HTMLElement

console
```

depending on the configured libraries/environment.

---

# 56. Browser Example

TypeScript understands:

```typescript
document.getElementById(
    "app"
);
```

because DOM declarations describe:

```typescript
document
```

and:

```typescript
getElementById()
```

You do not need to manually declare them in a normal browser TypeScript project.

---

# 57. Promise Example

TypeScript understands:

```typescript
Promise<string>
```

because standard library declarations describe `Promise`.

Which standard APIs are available depends partly on compiler settings such as:

```text
target

lib
```

We will cover those under `tsconfig.json`.

---

# 58. How TypeScript Finds Types

TypeScript can obtain type information from several places:

```text
Your .ts files

Your .d.ts files

Standard library declarations

Declarations shipped by packages

@types packages

Compiler configuration
```

Conceptually:

```text
TypeScript Compiler
        │
        ├── Source types
        ├── Standard library
        ├── Package declarations
        ├── @types packages
        └── Custom declarations
```

---

# 59. Package Type Declarations

A package might look like:

```text
node_modules/
└── some-library/
    ├── package.json
    ├── dist/
    │   ├── index.js
    │   └── index.d.ts
    └── ...
```

TypeScript uses package metadata and module resolution rules to locate the appropriate declaration.

---

# 60. `types` in `package.json`

A package may declare:

```json
{
    "types": "./dist/index.d.ts"
}
```

This tells TypeScript where the package's declaration entry point is.

You may also encounter the older synonymous field:

```json
{
    "typings": "./dist/index.d.ts"
}
```

Modern packages generally use `types`.

---

# 61. Generating Declaration Files

If you are building a TypeScript library, TypeScript can generate `.d.ts` files from your TypeScript source.

Example source:

```typescript
export function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

Generated declaration:

```typescript
export declare function add(
    a: number,
    b: number
): number;
```

The implementation disappears from the declaration.

Only the public type signature remains.

---

# 62. `declaration` Compiler Option

In `tsconfig.json`:

```json
{
    "compilerOptions": {
        "declaration": true
    }
}
```

TypeScript can generate declaration files during compilation.

For example:

```text
src/math.ts
        ↓
dist/math.js
dist/math.d.ts
```

This is especially important for libraries.

---

# 63. Why Libraries Generate `.d.ts`

Suppose you publish:

```text
my-library
```

written in TypeScript.

Consumers need:

```text
JavaScript
→ Runtime execution

.d.ts
→ Type information
```

Therefore a package may publish:

```text
dist/
├── index.js
└── index.d.ts
```

---

# 64. Application vs Library

For an application:

```text
React application

Node backend

Internal service
```

you usually do not need to publish declaration files for consumers.

For a reusable library:

```text
npm package

SDK

Shared utility package

Component library
```

declaration files are much more important.

---

# 65. `declarationMap`

Libraries may also enable:

```json
{
    "compilerOptions": {
        "declaration": true,
        "declarationMap": true
    }
}
```

This generates declaration map files:

```text
.d.ts.map
```

They can improve editor navigation from generated declarations back to source code.

---

# 66. `emitDeclarationOnly`

A project may use:

```json
{
    "compilerOptions": {
        "declaration": true,
        "emitDeclarationOnly": true
    }
}
```

This tells TypeScript to emit declarations without emitting normal JavaScript from that compiler invocation.

This can be useful when another tool handles JavaScript bundling/transpilation.

---

# 67. Custom Global Declaration File

A project might contain:

```text
src/
├── types/
│   └── globals.d.ts
├── app.ts
└── main.ts
```

Example:

```typescript
// globals.d.ts

declare const BUILD_VERSION: string;
```

Then:

```typescript
console.log(
    BUILD_VERSION
);
```

can be recognized throughout the applicable compilation.

---

# 68. Module Declaration File

Example:

```text
src/types/legacy-library.d.ts
```

```typescript
declare module "legacy-library" {
    export interface Config {
        debug: boolean;
    }

    export function start(
        config: Config
    ): void;
}
```

Then:

```typescript
import {
    start
} from "legacy-library";

start({
    debug: true
});
```

is typed.

---

# 69. Asset Declaration File

A project might use:

```text
src/types/assets.d.ts
```

containing:

```typescript
declare module "*.png" {
    const src: string;
    export default src;
}

declare module "*.svg" {
    const src: string;
    export default src;
}
```

This keeps asset declarations together.

---

# 70. Possible Declaration File Organization

```text
src/
│
├── types/
│   ├── globals.d.ts
│   ├── assets.d.ts
│   └── legacy-library.d.ts
│
├── components/
├── services/
└── main.ts
```

The organization is a convention.

TypeScript mainly cares that the files are included and resolved correctly.

---

# 71. `include`

Suppose:

```json
{
    "include": [
        "src"
    ]
}
```

Then declaration files under:

```text
src/
```

are generally part of the project.

Example:

```text
src/types/global.d.ts
```

will normally be included.

---

# 72. Declaration File Outside `include`

Suppose:

```text
custom-types/global.d.ts
```

but:

```json
{
    "include": [
        "src"
    ]
}
```

TypeScript may not include that file.

You may need:

```json
{
    "include": [
        "src",
        "custom-types"
    ]
}
```

depending on the project configuration.

---

# 73. `typeRoots`

TypeScript also supports:

```json
{
    "compilerOptions": {
        "typeRoots": [
            "./node_modules/@types",
            "./types"
        ]
    }
}
```

`typeRoots` controls package-style type declaration directories TypeScript considers.

This option is usually unnecessary for normal projects.

Be careful when configuring it because you can accidentally hide default type packages if configured incorrectly.

---

# 74. `types`

Another option is:

```json
{
    "compilerOptions": {
        "types": [
            "node"
        ]
    }
}
```

This controls which visible `@types` packages contribute global declarations to the project.

It does not mean:

```text
Only these modules may be imported.
```

It controls automatic inclusion of those type packages in the global type environment.

---

# 75. Example `types`

```json
{
    "compilerOptions": {
        "types": [
            "node"
        ]
    }
}
```

makes Node's global type package available.

Depending on the project, this can provide types such as:

```text
process

Buffer
```

and Node-related global declarations.

---

# 76. Avoid Unnecessary Type Configuration

Most projects should not immediately configure:

```text
typeRoots
types
```

TypeScript's defaults often work correctly.

Only configure them when the project actually requires control over the available type environment.

---

# 77. `.d.ts` Files Can Export Types

Example:

```typescript
// models.d.ts

export interface User {
    id: number;
    name: string;
}

export type Role =
    "admin"
    | "user";
```

Then:

```typescript
import type {
    User,
    Role
} from "./models";
```

can consume those declarations.

However, for types you own inside a TypeScript application, a normal `.ts` file is often preferable.

---

# 78. Do Not Use `.d.ts` for Every Interface

Suppose your application has:

```typescript
interface User {
    id: number;
    name: string;
}
```

You do not need to place this inside:

```text
user.d.ts
```

just because it is a type.

A normal file is fine:

```text
user.types.ts
```

```typescript
export interface User {
    id: number;
    name: string;
}
```

`.d.ts` has a more specific purpose: declaration-only type information for APIs/modules/globals.

---

# 79. `.d.ts` vs `.types.ts`

These are conceptually different.

```text
user.types.ts
```

is simply a normal TypeScript module containing types.

```text
global.d.ts
```

is a declaration file.

For application-owned domain types, prefer normal TypeScript modules unless ambient declarations are actually needed.

---

# 80. Example Application Type File

```typescript
// user.types.ts

export interface User {
    id: string;
    name: string;
}

export type Role =
    "admin"
    | "user";
```

Usage:

```typescript
import type {
    User,
    Role
} from "./user.types";
```

This is normal TypeScript module design.

No `.d.ts` is required.

---

# 81. When Should You Write `.d.ts` Yourself?

Common situations:

```text
JavaScript library without types

Global runtime variables

Bundler-specific asset imports

Legacy JavaScript integration

Custom runtime APIs

Framework/plugin declarations

Library type publication
```

For ordinary application models:

```text
User

Product

Order

API response types
```

use normal `.ts` modules.

---

# 82. Type-Only Imports

When consuming a type:

```typescript
import type {
    User
} from "./user.types";
```

`import type` explicitly indicates that the import is used only by the TypeScript type system.

Example:

```typescript
import type {
    User
} from "./user.types";

function printUser(
    user: User
) {
    console.log(user.name);
}
```

This import has no runtime value requirement.

---

# 83. `declare` vs Type Annotation

These are different:

```typescript
declare const apiUrl: string;
```

and:

```typescript
const apiUrl: string =
    "/api";
```

First:

```text
Describe existing value
```

Second:

```text
Create value
+
Annotate its type
```

---

# 84. `declare` vs `interface`

An interface defines a type shape:

```typescript
interface User {
    id: number;
}
```

`declare const` says a runtime value exists:

```typescript
declare const currentUser: User;
```

Think:

```text
interface
→ Describe a type shape

declare const
→ Describe an existing runtime value
```

---

# 85. `declare` vs `type`

Similarly:

```typescript
type UserId = string;
```

creates a type alias.

But:

```typescript
declare const userId: UserId;
```

describes an existing runtime value.

Types exist only in the type system.

Declarations can describe runtime entities.

---

# 86. Common Mistake — Expecting Runtime Code

Wrong:

```typescript
declare const API_URL: string;
```

and assuming:

```typescript
console.log(API_URL);
```

must work.

It only works if runtime actually provides `API_URL`.

---

# 87. Common Mistake — Lying to TypeScript

Suppose:

```typescript
declare const PORT: number;
```

but runtime provides:

```javascript
PORT = "3000";
```

TypeScript trusts your declaration.

Therefore the declaration has created false type safety.

Declarations must accurately model runtime behavior.

---

# 88. Common Mistake — Declaring Everything as `any`

```typescript
declare module "library";
```

may remove the immediate compiler error.

But you lose meaningful checking.

Prefer:

```typescript
declare module "library" {
    export function parse(
        value: string
    ): Result;
}
```

when you know the API.

---

# 89. Common Mistake — Recreating Existing Types

Before writing:

```typescript
declare module "some-library" {
    ...
}
```

check whether:

```text
The package already ships types

An @types package exists
```

Writing duplicate custom declarations can create conflicts or maintenance problems.

---

# 90. Common Mistake — Installing `@types` Unnecessarily

If a library already ships its own declarations, you usually do not need:

```text
@types/library
```

Installing unrelated/outdated duplicate declarations can cause conflicts.

Check the package first.

---

# 91. Common Mistake — Declaration Does Not Match Version

Suppose the runtime package is:

```text
library v5
```

but declarations describe:

```text
library v3
```

TypeScript may provide incorrect APIs.

Runtime package versions and declaration versions should be compatible.

---

# 92. Common Mistake — Asset Declaration Doesn't Match Bundler

You declare:

```typescript
declare module "*.svg" {
    const src: string;
    export default src;
}
```

but your bundler treats SVG as a component.

TypeScript and runtime now disagree.

Always model the actual toolchain.

---

# 93. Common Mistake — Putting Runtime Code in `.d.ts`

A declaration file is not where you implement:

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}
```

Instead it describes:

```typescript
export function add(
    a: number,
    b: number
): number;
```

Runtime implementation belongs elsewhere.

---

# 94. Common Mistake — Using `.d.ts` for Ordinary Types

Avoid turning:

```text
src/types/
```

into a collection of `.d.ts` files simply because the files contain interfaces.

Use:

```text
user.types.ts

api.types.ts

auth.types.ts
```

for normal imported application types.

Use `.d.ts` when declaration semantics are actually needed.

---

# 95. Common Mistake — Global Pollution

Writing many global declarations can create naming conflicts.

For example:

```typescript
interface Config {
    ...
}
```

inside global declaration space may collide with another `Config`.

Prefer modules:

```typescript
export interface Config {
    ...
}
```

when global availability is unnecessary.

---

# 96. Real-World Example — Build-Time Constant

Suppose your bundler injects:

```text
__BUILD_VERSION__
```

at build time.

Declaration:

```typescript
declare const __BUILD_VERSION__:
    string;
```

Usage:

```typescript
console.log(
    `Version: ${__BUILD_VERSION__}`
);
```

The bundler creates/replaces the runtime value.

The declaration tells TypeScript about it.

---

# 97. Real-World Example — Feature Flag Constant

Runtime/build tooling provides:

```text
__ENABLE_ANALYTICS__
```

Declaration:

```typescript
declare const __ENABLE_ANALYTICS__:
    boolean;
```

Usage:

```typescript
if (__ENABLE_ANALYTICS__) {
    startAnalytics();
}
```

Again:

```text
Build system
→ Runtime/build value

Declaration
→ Type information
```

---

# 98. Real-World Example — Legacy SDK

Suppose a script loaded from somewhere else creates:

```javascript
LegacySDK.initialize(...);
LegacySDK.track(...);
```

Declaration:

```typescript
declare namespace LegacySDK {
    interface Config {
        apiKey: string;
    }

    function initialize(
        config: Config
    ): void;

    function track(
        event: string
    ): void;
}
```

Usage:

```typescript
LegacySDK.initialize({
    apiKey: "abc"
});

LegacySDK.track(
    "login"
);
```

---

# 99. Real-World Example — JavaScript Package

Runtime:

```javascript
// analytics.js

export function track(event, data) {
    // ...
}
```

Declaration:

```typescript
// analytics.d.ts

export interface EventData {
    userId?: string;
}

export function track(
    event: string,
    data?: EventData
): void;
```

Consumers receive type checking without changing the JavaScript implementation.

---

# 100. Real-World Example — React Asset Imports

A frontend project may import:

```typescript
import logo
    from "./assets/logo.svg";
```

If the toolchain needs a declaration:

```typescript
declare module "*.svg" {
    const src: string;
    export default src;
}
```

TypeScript now understands the import according to that build setup.

Modern frameworks often provide these declarations themselves, so custom declarations may not be necessary.

---

# 101. Real-World Example — CSS Modules

```typescript
import styles
    from "./Button.module.css";

function Button() {
    return (
        <button
            className={styles.button}
        >
            Save
        </button>
    );
}
```

Possible declaration:

```typescript
declare module "*.module.css" {
    const classes:
        Readonly<
            Record<string, string>
        >;

    export default classes;
}
```

This tells TypeScript what the imported CSS module looks like.

---

# 102. Real-World Example — Library Build

Source:

```typescript
// src/index.ts

export interface User {
    id: string;
    name: string;
}

export function getName(
    user: User
): string {
    return user.name;
}
```

Compiler configuration:

```json
{
    "compilerOptions": {
        "declaration": true,
        "outDir": "dist"
    }
}
```

Output conceptually:

```text
dist/
├── index.js
└── index.d.ts
```

Declaration:

```typescript
export interface User {
    id: string;
    name: string;
}

export declare function getName(
    user: User
): string;
```

Consumers receive both runtime JavaScript and TypeScript type information.

---

# 103. Interview Question — What Is a `.d.ts` File?

A `.d.ts` file is a TypeScript declaration file used to describe the type information of existing JavaScript APIs, libraries, modules, globals, or generated TypeScript library APIs without providing their normal runtime implementation.

---

# 104. Interview Question — What Does `declare` Do?

`declare` tells TypeScript that a runtime entity exists elsewhere and describes its type.

Example:

```typescript
declare const VERSION: string;
```

It does not create `VERSION`.

---

# 105. Interview Question — Does `declare` Generate JavaScript?

No.

Declarations provide compile-time type information.

They do not implement runtime behavior.

---

# 106. Interview Question — `.ts` vs `.d.ts`?

```text
.ts
→ Normal TypeScript source
→ Can contain implementation

.d.ts
→ Declaration file
→ Describes types/APIs
→ No normal runtime implementation
```

---

# 107. Interview Question — What Is `@types`?

`@types` packages provide TypeScript declaration files for JavaScript libraries that do not necessarily ship their own declarations.

Example:

```bash
npm install --save-dev @types/node
```

---

# 108. Interview Question — What Is DefinitelyTyped?

DefinitelyTyped is the community-maintained repository containing declaration definitions for many JavaScript libraries.

Many of those declarations are published as:

```text
@types/*
```

packages.

---

# 109. Interview Question — What If a Library Has No Types?

Possible approaches:

```text
1. Check whether the library ships declarations

2. Check for an @types package

3. Write a custom .d.ts declaration

4. Temporarily declare the module loosely
```

Prefer accurate declarations over `any`.

---

# 110. Interview Question — What Does `declare module` Do?

It can describe an external module to TypeScript.

Example:

```typescript
declare module "legacy-library" {
    export function start(): void;
}
```

It can also participate in module augmentation, which we covered separately.

Context determines the purpose.

---

# 111. Interview Question — What Is an Ambient Declaration?

An ambient declaration describes something expected to exist at runtime without implementing it.

Example:

```typescript
declare const VERSION: string;
```

---

# 112. Interview Question — Why Generate `.d.ts` Files?

When publishing a TypeScript library, consumers need type information about the library's public API.

Enabling:

```json
{
    "compilerOptions": {
        "declaration": true
    }
}
```

allows TypeScript to generate declaration files.

---

# 113. Interview Question — What Is `emitDeclarationOnly`?

It tells TypeScript to emit declaration files without emitting normal JavaScript from that compiler run.

Example:

```json
{
    "compilerOptions": {
        "declaration": true,
        "emitDeclarationOnly": true
    }
}
```

Useful when another build tool handles JavaScript output.

---

# 114. Interview Question — What Is `declarationMap`?

`declarationMap` generates:

```text
.d.ts.map
```

files.

They can improve navigation from declaration output back to the original TypeScript source.

---

# 115. Interview Question — Should Application Interfaces Go in `.d.ts`?

Usually no.

For normal application types, use regular TypeScript modules:

```text
user.types.ts
```

```typescript
export interface User {
    ...
}
```

Use `.d.ts` when declaration semantics are required.

---

# 116. Cheat Sheet

```text
DECLARATION FILE
================================

*.d.ts


PURPOSE
================================

Describe runtime APIs
to TypeScript


NORMAL TYPESCRIPT
================================

file.ts

→ Types
→ Runtime implementation


DECLARATION FILE
================================

file.d.ts

→ Type declarations
→ No normal implementation


DECLARE
================================

declare const VERSION: string;

Meaning:

"VERSION exists at runtime
and is a string."


DECLARE CONST
================================

declare const API_URL: string;


DECLARE LET
================================

declare let currentUser: string;


DECLARE FUNCTION
================================

declare function greet(
    name: string
): string;


DECLARE CLASS
================================

declare class User {

    constructor(name: string);

    name: string;

    greet(): string;
}


DECLARE NAMESPACE
================================

declare namespace SDK {

    const version: string;

    function start(): void;

}


AMBIENT DECLARATION
================================

Declaration describing
something implemented elsewhere


IMPORTANT
================================

declare
≠
implementation


DECLARE MODULE
================================

declare module "library" {

    export function start(): void;

}


UNTYPED MODULE
================================

declare module "library";

→ Quick escape hatch
→ Weak type safety


CUSTOM DECLARATION
================================

declare module "library" {

    export function parse(
        input: string
    ): Result;

}


ASSET IMPORT
================================

declare module "*.png" {

    const src: string;

    export default src;

}


SVG
================================

declare module "*.svg" {

    const src: string;

    export default src;

}


CSS MODULE
================================

declare module "*.module.css" {

    const classes:
        Readonly<
            Record<string, string>
        >;

    export default classes;

}


STANDARD LIBRARY
================================

TypeScript ships declarations
for JavaScript APIs


DOM
================================

lib.dom.d.ts

→ Window
→ Document
→ HTMLElement
→ Browser APIs


@TYPES
================================

@types/library

→ Community/library declarations


NODE
================================

npm install --save-dev @types/node


DEFINITELYTYPED
================================

Community repository
for many @types packages


PACKAGE TYPES
================================

package.json

{
    "types":
        "./dist/index.d.ts"
}


GENERATE DECLARATIONS
================================

{
    "compilerOptions": {
        "declaration": true
    }
}


DECLARATION MAP
================================

{
    "compilerOptions": {
        "declarationMap": true
    }
}


DECLARATION ONLY
================================

{
    "compilerOptions": {
        "declaration": true,
        "emitDeclarationOnly": true
    }
}


APPLICATION TYPES
================================

user.types.ts

api.types.ts

auth.types.ts


NOT NECESSARILY
================================

user.d.ts

api.d.ts

auth.d.ts


USE .d.ts FOR
================================

External JS APIs

Global variables

Legacy libraries

Asset imports

Custom runtime APIs

Package declarations

Library output


CORE RULE
================================

Runtime code
→ Implements behavior

Declaration file
→ Describes behavior
```

---

# 117. Important Rules to Remember

```text
1. .d.ts means TypeScript declaration file.

2. Declaration files provide type information.

3. Declaration files do not provide normal runtime implementation.

4. declare describes something expected to exist elsewhere.

5. declare does not create runtime values.

6. declare const describes an existing constant.

7. declare let describes an existing mutable variable.

8. declare var can describe existing variables.

9. declare function describes an existing function.

10. declare class describes an existing class.

11. declare namespace can describe namespace-style runtime APIs.

12. Ambient declarations describe externally implemented APIs.

13. .d.ts files are declaration contexts.

14. Declarations must match runtime behavior.

15. Incorrect declarations create false type safety.

16. JavaScript libraries can provide .d.ts files.

17. TypeScript libraries can generate .d.ts files.

18. Packages can ship their own declarations.

19. package.json can identify declaration entry points.

20. @types packages provide declarations for many JS libraries.

21. DefinitelyTyped maintains many community declarations.

22. Check whether a library ships types before installing @types.

23. Avoid duplicate/outdated declaration packages.

24. Custom .d.ts files can describe untyped libraries.

25. declare module can describe an external module.

26. Module augmentation is a separate use of declare module.

27. A bare declare module can be a temporary escape hatch.

28. Bare module declarations provide weak type safety.

29. Prefer detailed declarations when possible.

30. Wildcard declarations can describe asset imports.

31. *.svg can be declared.

32. *.png can be declared.

33. *.module.css can be declared.

34. Declarations must match bundler behavior.

35. Do not assume every SVG import is a string.

36. Frameworks may provide asset declarations automatically.

37. TypeScript ships standard library declarations.

38. Standard declarations describe JavaScript built-ins.

39. DOM declarations describe browser APIs.

40. Node APIs generally require Node type declarations.

41. .ts can contain runtime implementation.

42. .d.ts describes types only.

43. Ordinary application interfaces do not need .d.ts.

44. Normal types can live in .types.ts files.

45. import type can import type-only dependencies.

46. Declaration files can export interfaces.

47. Declaration files can export type aliases.

48. Declaration files can describe named exports.

49. Declaration files can describe default exports.

50. Declaration files can describe function overloads.

51. declaration: true generates .d.ts output.

52. declarationMap generates .d.ts.map files.

53. emitDeclarationOnly emits declarations without normal JS.

54. Declaration generation is important for libraries.

55. Applications usually do not publish declaration files.

56. SDKs commonly publish declaration files.

57. Shared packages commonly publish declaration files.

58. Component libraries commonly publish declaration files.

59. Declaration files are compile-time information.

60. They do not perform runtime validation.

61. They do not initialize variables.

62. They do not create functions.

63. They do not create classes.

64. They do not create modules.

65. They do not load JavaScript libraries.

66. They do not guarantee globals exist.

67. They describe expected runtime behavior.

68. Runtime code must satisfy that contract.

69. TypeScript can find types from multiple sources.

70. Source files provide types.

71. Standard libraries provide types.

72. Packages can provide types.

73. @types packages can provide types.

74. Custom .d.ts files can provide types.

75. tsconfig influences available declarations.

76. include can affect custom declaration discovery.

77. types can control automatic global type packages.

78. typeRoots can control type package directories.

79. Avoid configuring typeRoots unnecessarily.

80. Avoid configuring types unnecessarily.

81. Declaration files should be kept accurate.

82. Library upgrades may require declaration updates.

83. Runtime versions and declaration versions should agree.

84. any should not be used just to silence errors.

85. A declaration is a contract.

86. The compiler trusts declarations.

87. Therefore incorrect declarations can be dangerous.

88. Use declarations mainly at TypeScript/runtime boundaries.

89. Normal domain modeling belongs in normal TS modules.

90. The core mental model is:

Runtime implementation
        +
Type declaration
        ↓
Typed JavaScript API
```

---

# 118. Final Mental Model

Consider a JavaScript library:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

At runtime:

```text
math.js
    │
    ▼
JavaScript Engine
    │
    ▼
add() actually executes
```

TypeScript needs type information:

```typescript
// math.d.ts

export function add(
    a: number,
    b: number
): number;
```

At development/compile time:

```text
math.d.ts
    │
    ▼
TypeScript Compiler
    │
    ▼
add(
    number,
    number
): number
```

Together:

```text
                LIBRARY

        ┌─────────────────┐
        │     math.js     │
        │                 │
        │ Runtime Code    │
        └────────┬────────┘
                 │
                 │ executes
                 ▼
             JavaScript


        ┌─────────────────┐
        │    math.d.ts    │
        │                 │
        │ Type Contract   │
        └────────┬────────┘
                 │
                 │ checked by
                 ▼
             TypeScript
```

The same idea applies to:

```text
Browser APIs

Node.js APIs

JavaScript libraries

Legacy code

Build-time globals

Asset imports

SDKs

npm packages
```

The shortest rule to remember:

```text
.ts
→ Write TypeScript implementation


.d.ts
→ Describe an API


declare
→ "This exists elsewhere"


@types
→ Type declarations for libraries


declaration: true
→ Generate .d.ts files


Runtime code
→ Makes things exist


Declaration files
→ Tell TypeScript what exists
```

If you see:

```typescript
declare const VERSION: string;
```

read it as:

```text
"I promise that VERSION
exists at runtime,
and when it exists,
it is a string."
```

That is the fundamental idea behind TypeScript declaration files.
