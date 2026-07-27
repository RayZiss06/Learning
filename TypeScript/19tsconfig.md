# `tsconfig.json`

`tsconfig.json` is the main configuration file used by the TypeScript compiler.

It tells TypeScript:

```text
Which files belong to the project?

Which JavaScript version should be targeted?

Which module system should be used?

How strict should type checking be?

Should JavaScript be generated?

Where should generated files go?

Which libraries and environment APIs are available?

How should modules be resolved?
```

A simple example:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "strict": true
    },
    "include": ["src"]
}
```

Think of `tsconfig.json` as:

```text
TypeScript Source Code
        │
        ▼
   tsconfig.json
        │
        ▼
TypeScript Compiler
        │
        ▼
Type Checking / Compilation
```

---

# 1. Why Do We Need `tsconfig.json`?

Without configuration, TypeScript would not know how we want our project compiled and checked.

Different projects have different requirements.

For example:

```text
React Application
Node.js Backend
npm Library
CLI Application
Monorepo
Legacy JavaScript Project
```

Each may require different TypeScript settings.

`tsconfig.json` defines those rules.

---

# 2. Creating `tsconfig.json`

TypeScript can generate a starter configuration:

```bash
npx tsc --init
```

This creates:

```text
tsconfig.json
```

Frameworks and build tools such as Vite often generate the necessary TypeScript configuration automatically when creating a TypeScript project.

---

# 3. Basic Structure

A typical configuration:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "strict": true
    },
    "include": ["src"],
    "exclude": ["node_modules"]
}
```

Main sections:

```text
compilerOptions
→ Controls TypeScript compiler behavior

include
→ Files/directories included in project

exclude
→ Files/directories excluded

files
→ Explicit list of files

extends
→ Inherit another configuration

references
→ Reference other TS projects
```

---

# 4. `compilerOptions`

Most TypeScript configuration lives inside:

```json
{
    "compilerOptions": {
    }
}
```

Example:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "strict": true,
        "noEmit": true
    }
}
```

Each option changes some aspect of TypeScript's behavior.

---

# 5. `target`

`target` controls the JavaScript language version TypeScript emits when TypeScript itself performs JavaScript transformation.

Example:

```json
{
    "compilerOptions": {
        "target": "ES2020"
    }
}
```

Suppose:

```typescript
const greet = (name: string) => {
    console.log(`Hello ${name}`);
};
```

Depending on the target, TypeScript may preserve modern syntax or transform features that need downleveling.

Common targets include:

```text
ES5
ES2015
ES2017
ES2020
ES2021
ES2022
ESNext
```

Modern applications commonly use a relatively modern target because modern browsers and Node versions support modern JavaScript.

---

# 6. `target` Does Not Polyfill APIs

This is important.

Suppose you use:

```typescript
Promise
```

or:

```typescript
Array.prototype.includes()
```

Changing:

```json
{
    "target": "ES5"
}
```

does not automatically install runtime polyfills.

Think:

```text
target
→ Controls emitted JavaScript syntax

Polyfill
→ Provides missing runtime APIs
```

They are different concerns.

---

# 7. `module`

`module` controls how TypeScript handles/emits JavaScript module syntax when it emits JavaScript.

Example:

```json
{
    "compilerOptions": {
        "module": "ESNext"
    }
}
```

Possible configurations include values such as:

```text
CommonJS
ES2020
ES2022
ESNext
Node16
NodeNext
Preserve
```

The correct option depends on the runtime and build tooling.

---

# 8. `target` vs `module`

Do not confuse them.

```text
target
→ JavaScript language syntax level

module
→ Module system/output behavior
```

Example:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext"
    }
}
```

means conceptually:

```text
Use modern JavaScript syntax
+
Use modern module semantics
```

---

# 9. `moduleResolution`

`moduleResolution` controls how TypeScript finds modules referenced by imports.

Example:

```typescript
import React from "react";

import {
    User
} from "./types";
```

TypeScript needs to determine what:

```text
react
./types
```

refer to.

Configuration example:

```json
{
    "compilerOptions": {
        "moduleResolution": "bundler"
    }
}
```

Modern possibilities include:

```text
node16
nodenext
bundler
```

Older projects may use:

```text
node
```

or other legacy resolution modes.

---

# 10. `module` vs `moduleResolution`

These are different.

```text
module
→ How modules are represented/handled

moduleResolution
→ How imports are located
```

Think:

```text
import { User } from "./user";
                      │
                      ▼
             moduleResolution


TypeScript Output
      │
      ▼
How module syntax behaves
      │
      ▼
    module
```

---

# 11. `moduleResolution: "bundler"`

Frontend applications using bundlers such as Vite commonly use:

```json
{
    "compilerOptions": {
        "moduleResolution": "bundler"
    }
}
```

This mode is designed for workflows where a bundler resolves modules.

It works naturally with modern ESM and package exports.

---

# 12. `Node16` / `NodeNext`

For Node.js applications, you may encounter:

```json
{
    "compilerOptions": {
        "module": "NodeNext",
        "moduleResolution": "NodeNext"
    }
}
```

These modes model Node's modern ESM/CommonJS behavior more closely.

The correct configuration depends on:

```text
Node version
package.json
"type" field
ESM vs CommonJS
Build tooling
```

---

# 13. `lib`

`lib` specifies which built-in JavaScript/environment declaration libraries TypeScript includes.

Example:

```json
{
    "compilerOptions": {
        "lib": [
            "ES2022",
            "DOM",
            "DOM.Iterable"
        ]
    }
}
```

These declarations tell TypeScript about APIs such as:

```text
Promise
Map
Set
Array
Window
Document
HTMLElement
```

---

# 14. `target` and `lib` Are Different

Suppose:

```json
{
    "compilerOptions": {
        "target": "ES2020",
        "lib": [
            "ES2022",
            "DOM"
        ]
    }
}
```

Conceptually:

```text
target
→ Output JavaScript syntax

lib
→ APIs TypeScript knows exist
```

`lib` does not install those APIs at runtime.

---

# 15. `DOM`

Frontend projects generally need DOM declarations.

Example:

```typescript
document.getElementById("root");

window.location.href;
```

TypeScript understands these through:

```text
DOM
```

declarations.

A backend Node application generally does not need DOM types unless it uses APIs or libraries requiring them.

---

# 16. `strict`

One of the most important options:

```json
{
    "compilerOptions": {
        "strict": true
    }
}
```

`strict` enables a family of strict type-checking behaviors.

For modern TypeScript projects, keeping:

```text
strict: true
```

is strongly recommended.

---

# 17. Why `strict` Matters

Without strict checking, TypeScript can allow code that weakens its safety.

For example:

```typescript
function greet(name) {
    return name.toUpperCase();
}
```

Strict settings help catch cases where TypeScript cannot safely determine types.

The goal is:

```text
JavaScript
→ Flexible

TypeScript
→ Controlled flexibility

strict TypeScript
→ Stronger guarantees
```

---

# 18. `noImplicitAny`

Example:

```json
{
    "compilerOptions": {
        "noImplicitAny": true
    }
}
```

Consider:

```typescript
function greet(name) {
    console.log(name);
}
```

If TypeScript cannot infer a safe type, the parameter could implicitly become `any`.

With `noImplicitAny`, TypeScript reports this.

Correct:

```typescript
function greet(
    name: string
) {
    console.log(name);
}
```

---

# 19. Explicit `any` vs Implicit `any`

`noImplicitAny` primarily targets accidental `any`.

This:

```typescript
function process(
    value: any
) {
}
```

is explicit.

You deliberately chose `any`.

But:

```typescript
function process(value) {
}
```

may produce an implicit `any`.

TypeScript warns because the loss of safety was not explicitly chosen.

---

# 20. `strictNullChecks`

Example:

```json
{
    "compilerOptions": {
        "strictNullChecks": true
    }
}
```

Consider:

```typescript
function findUser(
    id: number
): User | undefined {
    // ...
}
```

Then:

```typescript
const user = findUser(1);

console.log(user.name);
```

produces an error because:

```text
user
→ User | undefined
```

You must handle:

```typescript
if (user) {
    console.log(user.name);
}
```

or another appropriate narrowing strategy.

---

# 21. Why `strictNullChecks` Is Important

Without strict null checking, values like:

```text
null
undefined
```

can become much easier to accidentally use as if they were real objects.

Many runtime errors look like:

```text
Cannot read properties of undefined
```

Strict null checking catches many such problems before runtime.

---

# 22. `strictFunctionTypes`

This option performs stricter checking of function parameter compatibility in relevant function-type positions.

You normally do not configure it separately because:

```json
{
    "strict": true
}
```

enables it.

The important idea is:

```text
Function assignability
should not allow unsafe
parameter assumptions.
```

---

# 23. `strictPropertyInitialization`

Consider:

```typescript
class User {
    name: string;
}
```

`name` is declared but never initialized.

With strict property initialization, TypeScript reports this.

Correct:

```typescript
class User {
    name: string;

    constructor(name: string) {
        this.name = name;
    }
}
```

or:

```typescript
class User {
    name = "Unknown";
}
```

---

# 24. Definite Assignment Assertion

Sometimes initialization occurs in a way TypeScript cannot detect.

You may encounter:

```typescript
class User {
    name!: string;
}
```

The `!` tells TypeScript:

```text
I guarantee this property
will be assigned.
```

Use it carefully because you are overriding the compiler's safety check.

---

# 25. `useUnknownInCatchVariables`

Consider:

```typescript
try {
    // ...
} catch (error) {
    console.log(error.message);
}
```

Anything can technically be thrown in JavaScript:

```javascript
throw "failed";
```

Therefore treating the catch value as automatically being an `Error` is unsafe.

With appropriate strict behavior, it is treated as `unknown`.

Safer:

```typescript
try {
    // ...
} catch (error) {
    if (error instanceof Error) {
        console.log(error.message);
    }
}
```

---

# 26. `noImplicitReturns`

Example:

```json
{
    "compilerOptions": {
        "noImplicitReturns": true
    }
}
```

Consider:

```typescript
function getStatus(
    active: boolean
) {
    if (active) {
        return "active";
    }
}
```

One execution path returns a value.

Another returns nothing.

`noImplicitReturns` helps catch inconsistent return paths.

Better:

```typescript
function getStatus(
    active: boolean
): string {
    if (active) {
        return "active";
    }

    return "inactive";
}
```

---

# 27. `noUnusedLocals`

Example:

```json
{
    "compilerOptions": {
        "noUnusedLocals": true
    }
}
```

Consider:

```typescript
const name = "Alice";

const age = 25;

console.log(name);
```

`age` is never used.

TypeScript can report it.

This helps keep code clean.

---

# 28. `noUnusedParameters`

Example:

```json
{
    "compilerOptions": {
        "noUnusedParameters": true
    }
}
```

Consider:

```typescript
function greet(
    name: string,
    age: number
) {
    console.log(name);
}
```

`age` is unused.

TypeScript can report it.

---

# 29. `noFallthroughCasesInSwitch`

Consider:

```typescript
switch (status) {
    case "pending":
        console.log("Pending");

    case "success":
        console.log("Success");
        break;
}
```

Without a `break`, execution falls from:

```text
pending
```

into:

```text
success
```

This may be accidental.

Configuration:

```json
{
    "compilerOptions": {
        "noFallthroughCasesInSwitch": true
    }
}
```

helps detect problematic fallthrough.

---

# 30. `noUncheckedIndexedAccess`

Consider:

```typescript
const users = [
    "Alice",
    "Bob"
];

const user =
    users[100];
```

Runtime result:

```text
undefined
```

Without stronger checking, indexed access may be treated too optimistically.

With:

```json
{
    "compilerOptions": {
        "noUncheckedIndexedAccess": true
    }
}
```

TypeScript can model indexed access as potentially undefined.

Example:

```text
string | undefined
```

This increases safety.

---

# 31. `exactOptionalPropertyTypes`

Consider:

```typescript
interface User {
    nickname?: string;
}
```

There is a conceptual difference between:

```typescript
{}
```

and:

```typescript
{
    nickname: undefined
}
```

With:

```json
{
    "compilerOptions": {
        "exactOptionalPropertyTypes": true
    }
}
```

TypeScript treats optional properties more precisely according to their declared type.

This can improve correctness but can also make typing stricter.

---

# 32. `noPropertyAccessFromIndexSignature`

Suppose:

```typescript
interface Config {
    [key: string]: string;
}
```

Then:

```typescript
config.apiUrl
```

may refer to a property known only through the index signature.

With:

```json
{
    "compilerOptions": {
        "noPropertyAccessFromIndexSignature": true
    }
}
```

TypeScript can require:

```typescript
config["apiUrl"]
```

for such dynamically declared properties.

This makes the dynamic nature explicit.

---

# 33. `allowUnreachableCode`

TypeScript can detect unreachable code.

Example:

```typescript
function run() {
    return;

    console.log("Never runs");
}
```

You can configure how TypeScript treats unreachable code.

In normal projects, avoid writing unreachable code rather than relying on permissive configuration.

---

# 34. `allowUnusedLabels`

JavaScript supports labels:

```typescript
loop:
for (;;) {
    break loop;
}
```

TypeScript can detect unused labels.

This option is rarely something you need to customize in everyday React development.

---

# 35. `esModuleInterop`

You will frequently encounter:

```json
{
    "compilerOptions": {
        "esModuleInterop": true
    }
}
```

It improves interoperability between CommonJS modules and ES module-style imports.

Historically, JavaScript package ecosystems contained many CommonJS packages, so this option made certain import patterns easier and more compatible.

---

# 36. `allowSyntheticDefaultImports`

Example:

```json
{
    "compilerOptions": {
        "allowSyntheticDefaultImports": true
    }
}
```

This allows default-import syntax in situations where the underlying module's type/runtime interop model supports it through tooling even if the original module does not have a native ES default export in the straightforward sense.

Example syntax:

```typescript
import something
    from "some-library";
```

Modern tooling often configures this appropriately.

---

# 37. `esModuleInterop` vs `allowSyntheticDefaultImports`

Conceptually:

```text
allowSyntheticDefaultImports
→ Type-checking/import convenience

esModuleInterop
→ Interop behavior for emitted module helpers
  and compatible import semantics
```

In many projects you do not manually reason about these daily because the framework/tooling provides suitable defaults.

---

# 38. `resolveJsonModule`

Example:

```json
{
    "compilerOptions": {
        "resolveJsonModule": true
    }
}
```

Then:

```typescript
import config
    from "./config.json";
```

TypeScript can understand the imported JSON structure.

Example JSON:

```json
{
    "apiUrl": "/api",
    "retry": 3
}
```

TypeScript can infer:

```text
apiUrl → string
retry  → number
```

---

# 39. `isolatedModules`

Example:

```json
{
    "compilerOptions": {
        "isolatedModules": true
    }
}
```

This ensures each file can be safely processed independently by transpilers/build tools.

This matters because tools such as bundlers may transpile one file at a time rather than performing full TypeScript compilation for JavaScript output.

Frontend tooling commonly enables this or related modern options.

---

# 40. `isolatedDeclarations`

For declaration-generating library workflows, newer TypeScript configurations may use:

```json
{
    "compilerOptions": {
        "isolatedDeclarations": true
    }
}
```

This ensures declarations can be generated safely using isolated-file information.

This is primarily relevant to library/tooling workflows, not normal React application development.

---

# 41. `verbatimModuleSyntax`

Modern TypeScript projects may use:

```json
{
    "compilerOptions": {
        "verbatimModuleSyntax": true
    }
}
```

This makes TypeScript preserve module syntax more predictably and requires clearer distinction between runtime and type-only imports.

Example:

```typescript
import type {
    User
} from "./types";
```

versus:

```typescript
import {
    createUser
} from "./user";
```

This makes module intent explicit.

---

# 42. Type-Only Imports

With modern module configuration, use:

```typescript
import type {
    User
} from "./types";
```

when `User` is only a type.

Example:

```typescript
function printUser(
    user: User
) {
    console.log(user.name);
}
```

The import exists for type checking and does not represent a runtime dependency.

---

# 43. `skipLibCheck`

Common configuration:

```json
{
    "compilerOptions": {
        "skipLibCheck": true
    }
}
```

This skips type checking of declaration files such as many `.d.ts` files from dependencies.

Benefits:

```text
Faster compilation

Fewer dependency declaration conflicts
```

But remember:

```text
skipLibCheck
does not disable type checking
for your application code.
```

---

# 44. Why `skipLibCheck` Is Common

A project may depend on hundreds of declaration files.

Fully checking all dependency declarations can increase compiler work.

Many application projects use:

```json
{
    "skipLibCheck": true
}
```

while still keeping:

```json
{
    "strict": true
}
```

for their own code.

---

# 45. `forceConsistentCasingInFileNames`

Example:

```json
{
    "compilerOptions": {
        "forceConsistentCasingInFileNames": true
    }
}
```

Suppose the file is:

```text
UserService.ts
```

but you import:

```typescript
import {
    UserService
} from "./userservice";
```

Some operating systems may tolerate casing differences while others do not.

This option helps prevent cross-platform issues.

---

# 46. `rootDir`

For projects where TypeScript emits JavaScript directly:

```json
{
    "compilerOptions": {
        "rootDir": "./src"
    }
}
```

This indicates the expected root of source files for output structure.

Example:

```text
src/
├── app.ts
└── services/
    └── user.ts
```

with:

```text
rootDir = src
```

---

# 47. `outDir`

Example:

```json
{
    "compilerOptions": {
        "outDir": "./dist"
    }
}
```

TypeScript-generated files go to:

```text
dist/
```

Example:

```text
src/app.ts
      ↓
dist/app.js
```

This is common for Node applications and libraries when `tsc` emits JavaScript.

---

# 48. `rootDir` + `outDir`

Example:

```json
{
    "compilerOptions": {
        "rootDir": "./src",
        "outDir": "./dist"
    }
}
```

Project:

```text
src/
├── app.ts
└── services/
    └── user.ts
```

Output:

```text
dist/
├── app.js
└── services/
    └── user.js
```

The directory structure is preserved.

---

# 49. `noEmit`

Frontend applications commonly use:

```json
{
    "compilerOptions": {
        "noEmit": true
    }
}
```

This means:

```text
TypeScript
→ Type check

TypeScript
→ Do not generate JS files
```

Why?

Because another tool such as:

```text
Vite
esbuild
SWC
Babel
```

may handle transformation/bundling.

---

# 50. `noEmit` in React + Vite

A common workflow is:

```text
.ts / .tsx
    │
    ├── TypeScript
    │      ↓
    │   Type checking
    │
    └── Vite
           ↓
       Build / Bundle
```

Therefore TypeScript itself does not need to produce JavaScript.

Hence:

```json
{
    "noEmit": true
}
```

---

# 51. `sourceMap`

For projects where TypeScript emits JavaScript:

```json
{
    "compilerOptions": {
        "sourceMap": true
    }
}
```

This creates source maps.

Example:

```text
app.ts
   ↓
app.js
app.js.map
```

Source maps help debugging tools map generated JavaScript back to TypeScript source.

---

# 52. `inlineSourceMap`

Instead of a separate:

```text
.js.map
```

file, source map information can be embedded directly into generated JavaScript.

Example:

```json
{
    "compilerOptions": {
        "inlineSourceMap": true
    }
}
```

This is less common in standard application configurations.

---

# 53. `removeComments`

Example:

```json
{
    "compilerOptions": {
        "removeComments": true
    }
}
```

When TypeScript emits JavaScript, comments can be removed from the output.

In bundler-based frontend projects, minification/build tooling usually handles output optimization instead.

---

# 54. `declaration`

As covered previously:

```json
{
    "compilerOptions": {
        "declaration": true
    }
}
```

generates:

```text
.d.ts
```

files.

Important for:

```text
Libraries
SDKs
Shared packages
Component libraries
```

Usually unnecessary for a normal React application.

---

# 55. `declarationMap`

Example:

```json
{
    "compilerOptions": {
        "declaration": true,
        "declarationMap": true
    }
}
```

Generates:

```text
index.d.ts
index.d.ts.map
```

Useful for library consumers and editor navigation.

---

# 56. `emitDeclarationOnly`

Example:

```json
{
    "compilerOptions": {
        "declaration": true,
        "emitDeclarationOnly": true
    }
}
```

TypeScript emits declarations but not JavaScript.

Useful when:

```text
TypeScript
→ Generate types

Bundler
→ Generate JavaScript
```

---

# 57. `allowJs`

Suppose a project contains:

```text
src/
├── app.ts
├── legacy.js
└── utils.js
```

You can allow JavaScript files:

```json
{
    "compilerOptions": {
        "allowJs": true
    }
}
```

Useful when gradually migrating JavaScript to TypeScript.

---

# 58. `checkJs`

Example:

```json
{
    "compilerOptions": {
        "allowJs": true,
        "checkJs": true
    }
}
```

Now TypeScript can type-check JavaScript files as well.

This is useful for:

```text
JavaScript → TypeScript migration
```

---

# 59. `allowJs` vs `checkJs`

```text
allowJs
→ Allow JavaScript files
  to participate in project

checkJs
→ Type-check JavaScript files
```

Example:

```json
{
    "allowJs": true,
    "checkJs": false
}
```

means JavaScript is included but not checked as aggressively.

---

# 60. `jsx`

React uses JSX/TSX.

Example:

```tsx
function App() {
    return (
        <h1>Hello</h1>
    );
}
```

TypeScript needs to know how JSX should be handled.

Configuration:

```json
{
    "compilerOptions": {
        "jsx": "react-jsx"
    }
}
```

Modern React projects commonly use:

```text
react-jsx
```

---

# 61. `.tsx`

A TypeScript file containing JSX should generally use:

```text
.tsx
```

Example:

```text
App.tsx
Button.tsx
LoginPage.tsx
```

Normal TypeScript without JSX:

```text
.ts
```

Example:

```text
api.ts
types.ts
auth.ts
```

---

# 62. `jsx: "react-jsx"`

Modern React uses the newer JSX transform.

Configuration:

```json
{
    "compilerOptions": {
        "jsx": "react-jsx"
    }
}
```

This means you do not need to import React solely for JSX transformation in the way older React configurations required.

---

# 63. `baseUrl`

You may encounter:

```json
{
    "compilerOptions": {
        "baseUrl": "."
    }
}
```

Historically this has often been used with path aliases.

However, modern TypeScript setups can use `paths` without relying on `baseUrl` in many cases.

Do not add `baseUrl` automatically unless your setup needs it.

---

# 64. `paths`

Suppose your project contains:

```text
src/
├── components/
├── services/
└── utils/
```

Without aliases:

```typescript
import Button
    from "../../../components/Button";
```

We may configure:

```json
{
    "compilerOptions": {
        "paths": {
            "@/*": [
                "./src/*"
            ]
        }
    }
}
```

Then:

```typescript
import Button
    from "@/components/Button";
```

This is easier to read.

---

# 65. `paths` Does Not Automatically Configure Runtime Tooling

This is important.

Adding:

```json
{
    "paths": {
        "@/*": [
            "./src/*"
        ]
    }
}
```

teaches TypeScript how to resolve the alias.

Your runtime/bundler must also understand it.

For example:

```text
TypeScript
→ Must understand @/

Vite
→ Must understand @/
```

Both sides must agree.

---

# 66. Path Alias Mental Model

```text
Import:

@/components/Button
        │
        ├── TypeScript resolution
        │
        └── Bundler resolution
```

If only TypeScript knows the alias:

```text
Editor
→ Works

Build/runtime
→ May fail
```

Therefore configure aliases consistently.

---

# 67. `include`

Example:

```json
{
    "include": [
        "src"
    ]
}
```

This tells TypeScript which files should be part of the project.

You may also see:

```json
{
    "include": [
        "src/**/*.ts",
        "src/**/*.tsx"
    ]
}
```

Usually:

```json
{
    "include": ["src"]
}
```

is sufficient.

---

# 68. `exclude`

Example:

```json
{
    "exclude": [
        "node_modules",
        "dist"
    ]
}
```

This prevents matching files from being included through normal include discovery.

Common examples:

```text
node_modules
dist
build
coverage
```

depending on the project.

---

# 69. `exclude` Is Not a Security Boundary

`exclude` simply affects project file discovery.

It does not mean:

```text
TypeScript can never see these files.
```

Files may still become part of the program through imports or other configuration behavior.

Think of it as compilation/project configuration, not access control.

---

# 70. `files`

Instead of broad inclusion:

```json
{
    "files": [
        "src/main.ts",
        "src/config.ts"
    ]
}
```

This explicitly specifies files belonging to the project.

This is useful for small or specialized configurations but uncommon for normal React applications.

---

# 71. `files` vs `include`

```text
files
→ Explicit file list

include
→ Pattern/directory-based inclusion
```

Example:

```json
{
    "include": ["src"]
}
```

is easier for projects where files are frequently added.

---

# 72. `extends`

A TypeScript configuration can inherit from another configuration.

Example:

```json
{
    "extends": "./tsconfig.base.json",
    "compilerOptions": {
        "outDir": "./dist"
    }
}
```

Base:

```json
{
    "compilerOptions": {
        "strict": true,
        "target": "ES2022"
    }
}
```

Child receives those options and adds/overrides its own configuration.

---

# 73. Why Use `extends`?

Large projects may have:

```text
Frontend
Backend
Shared Library
Tests
Scripts
```

They may share:

```text
strict
target
module rules
```

but differ in:

```text
DOM libraries
Node types
Output directories
JSX settings
```

A shared base configuration prevents duplication.

---

# 74. Example Shared Configuration

```text
project/
│
├── tsconfig.base.json
│
├── frontend/
│   └── tsconfig.json
│
└── backend/
    └── tsconfig.json
```

Base:

```json
{
    "compilerOptions": {
        "strict": true,
        "skipLibCheck": true
    }
}
```

Frontend and backend extend it.

---

# 75. `references`

Large TypeScript projects can use:

```json
{
    "references": [
        {
            "path": "./packages/shared"
        }
    ]
}
```

This supports:

```text
Project References
```

Useful in:

```text
Monorepos
Large applications
Multiple TypeScript packages
Incremental builds
```

This is more advanced and not needed for a basic React application.

---

# 76. `composite`

Referenced TypeScript projects commonly enable:

```json
{
    "compilerOptions": {
        "composite": true
    }
}
```

This allows TypeScript to treat the project as part of a larger project-reference build structure.

Think:

```text
Project A
    ↓
Project B
    ↓
Project C
```

TypeScript can understand dependencies between projects.

---

# 77. `incremental`

Example:

```json
{
    "compilerOptions": {
        "incremental": true
    }
}
```

TypeScript stores information from previous builds so later compilations can be faster.

Useful for larger projects.

---

# 78. `tsBuildInfoFile`

With incremental/project builds, TypeScript can store build metadata.

Example:

```json
{
    "compilerOptions": {
        "incremental": true,
        "tsBuildInfoFile":
            "./node_modules/.cache/app.tsbuildinfo"
    }
}
```

This is build metadata, not application runtime data.

---

# 79. `types`

As discussed in declaration files:

```json
{
    "compilerOptions": {
        "types": [
            "node"
        ]
    }
}
```

This controls which installed type packages contribute globals automatically.

Do not confuse this with:

```text
Which packages can be imported.
```

It primarily controls the global type environment from type packages.

---

# 80. `typeRoots`

Example:

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

This controls where TypeScript looks for type package directories.

Most applications do not need to configure this manually.

Incorrect configuration can hide expected type packages.

---

# 81. `types` vs `typeRoots`

```text
types
→ Which type packages should
  be automatically included

typeRoots
→ Which directories contain
  type packages
```

Normally:

```text
Leave defaults alone
unless you have a reason
to change them.
```

---

# 82. `useDefineForClassFields`

Modern projects may use:

```json
{
    "compilerOptions": {
        "useDefineForClassFields": true
    }
}
```

This controls how class fields align with modern JavaScript class field semantics when emitting code.

Example:

```typescript
class User {
    name = "Alice";
}
```

Modern configurations generally use behavior matching standard JavaScript semantics.

---

# 83. `downlevelIteration`

Older JavaScript targets may need:

```json
{
    "compilerOptions": {
        "downlevelIteration": true
    }
}
```

to more accurately support iteration behavior when compiling newer iteration constructs to older JavaScript.

Example:

```typescript
for (
    const value of collection
) {
}
```

For modern targets, this is less commonly something you need to think about.

---

# 84. `importsNotUsedAsValues`

You may encounter this option in older TypeScript configurations.

Modern TypeScript generally prefers:

```text
verbatimModuleSyntax
```

and explicit:

```typescript
import type
```

Do not copy outdated `tsconfig` settings blindly from old tutorials.

---

# 85. `preserveValueImports`

Similarly, you may encounter older configurations using:

```text
preserveValueImports
```

Modern TypeScript module handling has evolved.

Prefer current tooling-generated configurations and current TypeScript documentation rather than combining options from old project templates.

---

# 86. `allowImportingTsExtensions`

Some modern workflows may use imports like:

```typescript
import {
    something
} from "./utils.ts";
```

TypeScript has configuration controlling whether TypeScript file extensions can appear directly in imports under supported conditions.

Most React/Vite application code traditionally uses:

```typescript
import {
    something
} from "./utils";
```

Follow the conventions of the runtime/build system.

---

# 87. `moduleDetection`

TypeScript can determine whether files should be treated as modules.

Modern configurations may use:

```json
{
    "compilerOptions": {
        "moduleDetection": "force"
    }
}
```

This ensures files are treated consistently as modules.

This can help prevent accidental global declarations.

---

# 88. Script vs Module

File without imports/exports:

```typescript
const name = "Alice";
```

may historically behave as a script in some contexts.

File with:

```typescript
export {};
```

is definitely a module.

Modern module detection settings can make project behavior more predictable.

---

# 89. `erasableSyntaxOnly`

Newer TypeScript workflows may use:

```json
{
    "compilerOptions": {
        "erasableSyntaxOnly": true
    }
}
```

This restricts TypeScript code to syntax that can be handled by simply removing type information, avoiding TypeScript-specific constructs that require runtime transformation.

This becomes useful in environments/toolchains that directly strip TypeScript types.

It is not required for every project.

---

# 90. `noEmitOnError`

For projects where TypeScript emits JavaScript:

```json
{
    "compilerOptions": {
        "noEmitOnError": true
    }
}
```

prevents JavaScript output when TypeScript reports errors.

For projects with:

```json
{
    "noEmit": true
}
```

this is irrelevant because TypeScript is not emitting JavaScript anyway.

---

# 91. `pretty`

TypeScript can format compiler errors for easier terminal reading.

```json
{
    "compilerOptions": {
        "pretty": true
    }
}
```

This is mostly developer experience rather than type-system behavior.

---

# 92. `sourceRoot` and `mapRoot`

These options control paths associated with generated source maps.

They are mainly relevant to library/build tooling and are not something you usually configure manually in a standard Vite application.

---

# 93. `newLine`

TypeScript can control emitted newline style.

For example:

```text
LF
CRLF
```

This is rarely important in modern application setups because formatting/version-control tooling usually handles newline conventions.

---

# 94. `stripInternal`

Library authors can mark declarations as internal and remove them from generated declaration output when configured appropriately.

This is primarily a library authoring feature.

It is not needed for normal React applications.

---

# 95. React + Vite TypeScript Architecture

A modern Vite React TypeScript project may contain:

```text
project/
│
├── src/
│   ├── main.tsx
│   └── App.tsx
│
├── tsconfig.json
├── tsconfig.app.json
├── tsconfig.node.json
└── vite.config.ts
```

Exact generated files can vary by Vite version/template.

The important idea is that TypeScript configuration may be separated by environment.

---

# 96. Why Multiple `tsconfig` Files?

Consider:

```text
src/
→ Browser application

vite.config.ts
→ Node/build environment
```

These environments need different type information.

Browser:

```text
DOM
Window
Document
React
```

Node/build tooling:

```text
Node APIs
process
filesystem
build configuration
```

Separate configurations keep environments clean.

---

# 97. Root `tsconfig.json`

A root configuration may primarily reference other projects.

Conceptually:

```json
{
    "files": [],
    "references": [
        {
            "path":
                "./tsconfig.app.json"
        },
        {
            "path":
                "./tsconfig.node.json"
        }
    ]
}
```

Think:

```text
tsconfig.json
    │
    ├── Application config
    │
    └── Tooling config
```

Exact Vite templates may differ over time.

---

# 98. `tsconfig.app.json`

The application configuration is typically responsible for:

```text
src/**/*.ts

src/**/*.tsx

Browser libraries

React JSX

Strict checking

Frontend module resolution
```

Conceptual example:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "lib": [
            "ES2022",
            "DOM",
            "DOM.Iterable"
        ],
        "module": "ESNext",
        "moduleResolution":
            "bundler",
        "jsx": "react-jsx",
        "strict": true,
        "noEmit": true,
        "skipLibCheck": true
    },
    "include": [
        "src"
    ]
}
```

Do not treat this as a template that must exactly match every Vite version.

---

# 99. `tsconfig.node.json`

Build tooling may use a separate configuration.

Conceptually:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "moduleResolution":
            "bundler",
        "strict": true,
        "noEmit": true,
        "skipLibCheck": true
    },
    "include": [
        "vite.config.ts"
    ]
}
```

The exact settings depend on the tooling version and environment.

---

# 100. Browser vs Node Configuration

Frontend:

```text
React Components
DOM APIs
Browser APIs
TSX
```

Build/backend tooling:

```text
Node APIs
Configuration scripts
Filesystem
Process environment
```

Therefore:

```text
One universal tsconfig
```

is not always the best design.

---

# 101. React Strict Configuration

A strong React project should generally keep:

```json
{
    "compilerOptions": {
        "strict": true
    }
}
```

Do not disable strict mode simply because TypeScript reports errors.

Errors often indicate places where the code needs better modeling.

---

# 102. Bad Approach

Avoid:

```json
{
    "compilerOptions": {
        "strict": false,
        "noImplicitAny": false
    }
}
```

just to make errors disappear.

This defeats much of the purpose of using TypeScript.

Better:

```text
Understand the error
        ↓
Fix the type model
        ↓
Keep strict checking
```

---

# 103. Example React Component

```tsx
interface ButtonProps {
    label: string;
    onClick: () => void;
}

function Button({
    label,
    onClick
}: ButtonProps) {
    return (
        <button onClick={onClick}>
            {label}
        </button>
    );
}
```

With strict TypeScript:

```tsx
<Button
    label="Save"
    onClick={() => {
        console.log("Saved");
    }}
/>
```

is checked safely.

---

# 104. TypeScript Is Often a Checker in Frontend Projects

With Vite:

```text
TypeScript
→ Type checking

Vite
→ Development server

Bundler/toolchain
→ Transformation

Bundler/toolchain
→ Production build
```

This is why:

```json
{
    "noEmit": true
}
```

is common.

---

# 105. Type Checking vs Building

Do not confuse:

```bash
tsc --noEmit
```

with:

```bash
vite build
```

Conceptually:

```text
tsc --noEmit
→ Check TypeScript correctness

vite build
→ Build frontend application
```

Projects may run both as part of the build workflow.

---

# 106. Example Build Script

A project might use:

```json
{
    "scripts": {
        "build":
            "tsc -b && vite build"
    }
}
```

Here:

```text
tsc -b
→ TypeScript project build/check

vite build
→ Frontend production build
```

The exact script depends on project configuration.

---

# 107. `tsc`

The TypeScript compiler command is:

```bash
tsc
```

If installed locally:

```bash
npx tsc
```

TypeScript searches for:

```text
tsconfig.json
```

and uses the project configuration.

---

# 108. `tsc --noEmit`

Useful command:

```bash
npx tsc --noEmit
```

This type-checks without generating JavaScript.

Useful in:

```text
Development

CI/CD

Pull request checks

Pre-commit/pre-push workflows
```

---

# 109. `tsc -p`

You can specify a project configuration:

```bash
npx tsc -p tsconfig.app.json
```

This tells TypeScript to use that configuration.

Useful when a repository contains multiple TypeScript projects.

---

# 110. `tsc --showConfig`

Useful command:

```bash
npx tsc --showConfig
```

It displays the resolved TypeScript configuration.

This is helpful when:

```text
extends
multiple configs
defaults
```

make it difficult to determine the final settings.

---

# 111. `tsc -b`

Project references use build mode:

```bash
npx tsc -b
```

This can build/check referenced TypeScript projects in dependency order.

Useful for:

```text
Monorepos

Frontend + shared packages

Multiple libraries
```

---

# 112. Configuration Precedence

Suppose base configuration:

```json
{
    "compilerOptions": {
        "strict": true,
        "target": "ES2020"
    }
}
```

Child:

```json
{
    "extends": "./tsconfig.base.json",
    "compilerOptions": {
        "target": "ES2022"
    }
}
```

The child overrides:

```text
target
```

while inheriting:

```text
strict
```

Conceptually:

```text
Base
+
Child overrides
=
Final configuration
```

---

# 113. `tsconfig` for Backend Node Project

Conceptually:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "NodeNext",
        "moduleResolution":
            "NodeNext",
        "strict": true,
        "rootDir": "./src",
        "outDir": "./dist",
        "skipLibCheck": true
    },
    "include": [
        "src"
    ]
}
```

But the correct Node configuration depends heavily on whether the project is:

```text
ESM
CommonJS
Bundled
Executed directly
```

so do not copy a configuration without understanding the runtime.

---

# 114. `package.json` Matters for Node

Suppose:

```json
{
    "type": "module"
}
```

Node treats relevant `.js` files as ES modules.

TypeScript settings such as:

```text
NodeNext
```

interact with Node's module behavior.

Therefore Node TypeScript configuration is not determined by `tsconfig.json` alone.

You must consider:

```text
tsconfig.json
+
package.json
+
Node version
+
Runtime strategy
```

---

# 115. Common Mistake — Copying Random `tsconfig`

Do not search for:

```text
best tsconfig
```

and blindly copy everything.

A configuration for:

```text
Node library
```

may be wrong for:

```text
React + Vite
```

A configuration for:

```text
Next.js
```

may be wrong for:

```text
Express
```

Configuration depends on the environment.

---

# 116. Common Mistake — Disabling `strict`

You encounter:

```text
Object is possibly undefined
```

and change:

```json
{
    "strict": false
}
```

Bad approach.

The error may be correctly identifying unsafe code.

Instead:

```typescript
if (user) {
    console.log(user.name);
}
```

Fix the actual uncertainty.

---

# 117. Common Mistake — Using `any`

Compiler error:

```text
Parameter implicitly has an 'any' type
```

Bad fix:

```typescript
function process(
    value: any
) {
}
```

unless `any` is genuinely appropriate.

Better:

```typescript
function process(
    value: User
) {
}
```

or:

```typescript
function process(
    value: unknown
) {
}
```

depending on the problem.

---

# 118. Common Mistake — Wrong `lib`

Suppose a backend project unnecessarily includes:

```json
{
    "lib": [
        "ES2022",
        "DOM"
    ]
}
```

TypeScript now assumes browser APIs such as:

```text
document
window
```

exist.

But Node may not provide them.

Your type environment should reflect the actual runtime.

---

# 119. Common Mistake — Confusing Types With Runtime Support

If TypeScript knows:

```typescript
document
```

because:

```text
DOM
```

types are included, that does not make `document` exist in Node.

Types describe runtime expectations.

They do not create runtime APIs.

---

# 120. Common Mistake — Alias Only in TypeScript

You configure:

```json
{
    "paths": {
        "@/*": [
            "./src/*"
        ]
    }
}
```

Editor works.

Then runtime/build fails.

Why?

The bundler/runtime does not know the alias.

Remember:

```text
TypeScript resolution
+
Runtime/build resolution
```

must agree.

---

# 121. Common Mistake — Incorrect `moduleResolution`

A project uses Vite but configuration uses module resolution designed for a different runtime.

Or a Node project uses bundler-oriented resolution despite being executed directly by Node.

Module resolution should match:

```text
How modules actually get loaded.
```

---

# 122. Common Mistake — Old Tutorials

TypeScript changes over time.

You may encounter old configurations containing settings that have been replaced, deprecated, or are unnecessary with modern tooling.

Examples include older patterns around:

```text
moduleResolution

importsNotUsedAsValues

preserveValueImports

React JSX configuration
```

Use tooling-generated configurations as a strong starting point.

---

# 123. Common Mistake — Editing Generated Config Without Reason

Vite creates working TypeScript configuration.

Do not immediately replace it with a custom configuration copied from somewhere else.

Better:

```text
Start with generated config
        ↓
Understand each option
        ↓
Modify only when needed
```

---

# 124. Common Mistake — `skipLibCheck: false` as a Goal

Turning dependency declaration checking on is not automatically "more professional."

Application teams often use:

```json
{
    "skipLibCheck": true
}
```

because dependency declaration checking can add noise and compilation cost.

Your application code can still remain strict.

---

# 125. Common Mistake — Treating `tsconfig` as ESLint

TypeScript checks types and certain correctness issues.

ESLint handles broader code-quality/style rules.

Formatter tools handle formatting.

Think:

```text
TypeScript
→ Types

ESLint
→ Code quality / lint rules

Formatter
→ Formatting

Vite
→ Build/dev tooling
```

They overlap slightly, but they have different responsibilities.

---

# 126. Real-World Development Pipeline

A React TypeScript application might conceptually use:

```text
Developer writes
.ts / .tsx
      │
      ▼
TypeScript
      │
      ├── Type errors
      │
      ▼
ESLint
      │
      ├── Code-quality errors
      │
      ▼
Vite
      │
      ├── Transform
      ├── Bundle
      └── Build
      │
      ▼
Browser
```

Each tool has a different responsibility.

---

# 127. CI/CD Example

A CI pipeline might run:

```bash
npm run lint
npm run typecheck
npm run test
npm run build
```

Where:

```text
lint
→ ESLint

typecheck
→ TypeScript

test
→ Test framework

build
→ Vite
```

This is a common professional workflow.

---

# 128. Example Scripts

```json
{
    "scripts": {
        "typecheck":
            "tsc --noEmit",
        "build":
            "vite build"
    }
}
```

Or with project references:

```json
{
    "scripts": {
        "typecheck":
            "tsc -b",
        "build":
            "tsc -b && vite build"
    }
}
```

The correct script depends on the project configuration.

---

# 129. Development vs Production Configuration

Sometimes projects need different settings for:

```text
Development
Testing
Production
Library Build
```

You can use separate configurations:

```text
tsconfig.json

tsconfig.app.json

tsconfig.node.json

tsconfig.test.json

tsconfig.build.json
```

and share common options through:

```text
extends
```

---

# 130. Example Configuration Hierarchy

```text
tsconfig.base.json
        │
        ├── tsconfig.app.json
        │
        ├── tsconfig.node.json
        │
        └── tsconfig.test.json
```

Base:

```text
Shared strict rules
Shared compiler behavior
```

Children:

```text
Environment-specific settings
```

---

# 131. Recommended Mental Categories

Instead of memorizing dozens of options, divide them into categories.

```text
LANGUAGE / OUTPUT
=================

target
module
jsx


MODULE RESOLUTION
=================

moduleResolution
paths
types
typeRoots


TYPE SAFETY
=================

strict
noImplicitAny
strictNullChecks
noUncheckedIndexedAccess
exactOptionalPropertyTypes


CODE QUALITY
=================

noUnusedLocals
noUnusedParameters
noImplicitReturns
noFallthroughCasesInSwitch


OUTPUT
=================

noEmit
outDir
rootDir
sourceMap
declaration


PROJECT STRUCTURE
=================

include
exclude
files
extends
references


TOOLING
=================

isolatedModules
verbatimModuleSyntax
skipLibCheck
```

This is much easier than memorizing every compiler option individually.

---

# 132. What You Actually Need to Remember for React

For React + TypeScript, the options you should recognize immediately are:

```text
target

lib

module

moduleResolution

jsx

strict

noEmit

skipLibCheck

isolatedModules

verbatimModuleSyntax

include
```

And depending on the generated configuration:

```text
noUnusedLocals

noUnusedParameters

noFallthroughCasesInSwitch

moduleDetection

allowImportingTsExtensions

erasableSyntaxOnly
```

You do not need to memorize every TypeScript compiler option.

---

# 133. What You Need to Remember for Node

For Node TypeScript projects, pay special attention to:

```text
target

module

moduleResolution

strict

types

rootDir

outDir

sourceMap

declaration
```

and:

```text
package.json
"type"
```

because Node module behavior matters.

---

# 134. What You Need to Remember for Libraries

For libraries:

```text
target

module

moduleResolution

strict

rootDir

outDir

declaration

declarationMap

sourceMap
```

Potentially:

```text
emitDeclarationOnly

composite

isolatedDeclarations
```

depending on the build system.

---

# 135. Example Strictness Configuration

A project wanting stronger checking may use:

```json
{
    "compilerOptions": {
        "strict": true,
        "noImplicitReturns": true,
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noFallthroughCasesInSwitch": true,
        "noUncheckedIndexedAccess": true,
        "exactOptionalPropertyTypes": true
    }
}
```

But understand each option before enabling it across an existing codebase because stronger settings may reveal many existing issues.

---

# 136. Recommended Approach for New Projects

For a new project:

```text
1. Start strict.

2. Keep strict enabled.

3. Use tooling-generated configuration.

4. Add stronger checks deliberately.

5. Avoid any unless necessary.

6. Match module resolution to runtime/tooling.

7. Keep browser and Node environments separate.

8. Do not copy old configs blindly.

9. Let bundlers handle bundling.

10. Let TypeScript handle type safety.
```

---

# 137. Interview Question — What Is `tsconfig.json`?

`tsconfig.json` is the TypeScript project configuration file.

It controls:

```text
Compiler behavior

Type checking

Module handling

Language target

Available libraries

File inclusion

Output behavior

Project structure
```

---

# 138. Interview Question — What Does `strict` Do?

```json
{
    "strict": true
}
```

enables TypeScript's family of strict type-checking options.

It provides stronger compile-time safety and should generally remain enabled in modern projects.

---

# 139. Interview Question — `target` vs `lib`?

```text
target
→ Controls emitted JavaScript language level

lib
→ Controls built-in/environment type declarations
```

Neither automatically polyfills missing runtime APIs.

---

# 140. Interview Question — `module` vs `moduleResolution`?

```text
module
→ Module output/semantics

moduleResolution
→ How TypeScript finds imported modules
```

---

# 141. Interview Question — What Does `noEmit` Do?

```json
{
    "noEmit": true
}
```

tells TypeScript not to generate output files.

This is common when another tool such as Vite handles transformation/building.

---

# 142. Interview Question — What Does `skipLibCheck` Do?

It skips type checking of declaration files.

It does not disable checking of your normal application TypeScript code.

---

# 143. Interview Question — What Does `paths` Do?

`paths` defines custom module resolution mappings.

Example:

```json
{
    "paths": {
        "@/*": [
            "./src/*"
        ]
    }
}
```

allowing imports such as:

```typescript
import Button
    from "@/components/Button";
```

Your bundler/runtime must also understand the alias.

---

# 144. Interview Question — `include` vs `exclude`?

```text
include
→ Defines files/directories considered part of project

exclude
→ Removes matching files from normal discovery
```

---

# 145. Interview Question — What Does `extends` Do?

It allows one TypeScript configuration to inherit another.

Example:

```json
{
    "extends":
        "./tsconfig.base.json"
}
```

Useful for sharing settings across multiple applications/packages.

---

# 146. Interview Question — Why Multiple `tsconfig` Files?

Different environments may need different compiler/type settings.

Example:

```text
React frontend
→ Browser + DOM

Vite config
→ Node/build environment
```

Separate configs prevent unnecessary environment mixing.

---

# 147. Interview Question — What Is `noUncheckedIndexedAccess`?

It makes indexed access more accurately account for values that may not exist.

Example:

```typescript
const names: string[] = [];

const name = names[10];
```

With stronger indexed checking:

```text
name
→ string | undefined
```

rather than assuming a string definitely exists.

---

# 148. Interview Question — What Is `exactOptionalPropertyTypes`?

It makes optional properties model absence more precisely.

```typescript
interface User {
    nickname?: string;
}
```

The absence of `nickname` and explicitly assigning `undefined` are not automatically treated as identical unless the property type allows it.

---

# 149. Interview Question — Why `import type`?

```typescript
import type {
    User
} from "./types";
```

makes it explicit that `User` exists only for type checking.

It helps TypeScript and tooling distinguish type dependencies from runtime dependencies.

---

# 150. Interview Question — What Does `resolveJsonModule` Do?

It allows TypeScript to resolve JSON modules and understand their structure.

Example:

```typescript
import config
    from "./config.json";
```

---

# 151. Interview Question — `allowJs` vs `checkJs`?

```text
allowJs
→ Include JavaScript files

checkJs
→ Type-check JavaScript files
```

Useful when migrating JavaScript projects to TypeScript.

---

# 152. Interview Question — What Is `jsx: react-jsx`?

It configures TypeScript for the modern React JSX transform.

It is commonly used in modern React TypeScript projects.

---

# 153. Interview Question — What Are Project References?

Project references allow TypeScript projects to depend on other TypeScript projects.

Example:

```text
Web App
    ↓
Shared Package
    ↓
Core Package
```

They are useful for large repositories and monorepos.

---

# 154. Cheat Sheet

```text
TSCONFIG.JSON
================================

TypeScript project configuration


CREATE
================================

npx tsc --init


MAIN SECTION
================================

{
    "compilerOptions": {}
}


TARGET
================================

target

→ JavaScript output language level


MODULE
================================

module

→ Module output / semantics


MODULE RESOLUTION
================================

moduleResolution

→ How imports are located


LIB
================================

lib

→ Available built-in/environment types


STRICT
================================

strict: true

→ Strong type checking


NO IMPLICIT ANY
================================

noImplicitAny: true

→ Prevent accidental any


STRICT NULL CHECKS
================================

strictNullChecks: true

→ null/undefined handled explicitly


NO IMPLICIT RETURNS
================================

noImplicitReturns: true

→ Detect missing return paths


NO UNUSED LOCALS
================================

noUnusedLocals: true


NO UNUSED PARAMETERS
================================

noUnusedParameters: true


INDEX SAFETY
================================

noUncheckedIndexedAccess: true


OPTIONAL PROPERTY SAFETY
================================

exactOptionalPropertyTypes: true


JSX
================================

jsx: "react-jsx"


NO EMIT
================================

noEmit: true

→ Type-check only


ROOT DIR
================================

rootDir: "./src"


OUTPUT DIR
================================

outDir: "./dist"


SOURCE MAP
================================

sourceMap: true


DECLARATIONS
================================

declaration: true

→ Generate .d.ts


SKIP LIB CHECK
================================

skipLibCheck: true

→ Skip declaration-file checking


JSON IMPORT
================================

resolveJsonModule: true


JAVASCRIPT
================================

allowJs: true


CHECK JAVASCRIPT
================================

checkJs: true


PATH ALIAS
================================

"paths": {
    "@/*": [
        "./src/*"
    ]
}


INCLUDE
================================

"include": [
    "src"
]


EXCLUDE
================================

"exclude": [
    "dist"
]


FILES
================================

"files": [
    "src/main.ts"
]


EXTENDS
================================

"extends":
    "./tsconfig.base.json"


PROJECT REFERENCES
================================

"references": [
    {
        "path": "./package"
    }
]


COMPOSITE
================================

composite: true


INCREMENTAL
================================

incremental: true


TYPE-ONLY IMPORT
================================

import type {
    User
} from "./types";


VITE / REACT
================================

TypeScript
→ Type checking

Vite
→ Build / bundling


NODE
================================

module/moduleResolution
must match Node runtime strategy


CORE RULE
================================

tsconfig.json

defines how TypeScript
understands and checks
your project.
```

---

# 155. Important Rules to Remember

```text
1. tsconfig.json configures a TypeScript project.

2. compilerOptions contains compiler settings.

3. target controls JavaScript output language level.

4. target does not provide polyfills.

5. module controls module semantics/output.

6. moduleResolution controls module lookup.

7. module and moduleResolution are different.

8. lib controls available standard/environment declarations.

9. lib does not create runtime APIs.

10. strict should generally remain enabled.

11. strict enables several strict checking behaviors.

12. noImplicitAny catches accidental any.

13. Explicit any is still possible.

14. Avoid any when safer types are available.

15. strictNullChecks forces null/undefined handling.

16. strictPropertyInitialization checks class initialization.

17. useUnknownInCatchVariables makes error handling safer.

18. noImplicitReturns catches inconsistent return paths.

19. noUnusedLocals detects unused local variables.

20. noUnusedParameters detects unused parameters.

21. noFallthroughCasesInSwitch catches switch fallthrough issues.

22. noUncheckedIndexedAccess improves indexed-access safety.

23. exactOptionalPropertyTypes makes optional properties more precise.

24. esModuleInterop improves module interoperability.

25. allowSyntheticDefaultImports affects default import compatibility.

26. resolveJsonModule enables typed JSON imports.

27. isolatedModules supports isolated-file transpilation workflows.

28. verbatimModuleSyntax encourages explicit module intent.

29. import type is used for type-only imports.

30. skipLibCheck skips declaration-file checking.

31. skipLibCheck does not disable application type checking.

32. forceConsistentCasingInFileNames prevents casing problems.

33. rootDir describes source structure for emit.

34. outDir controls generated output location.

35. noEmit disables TypeScript output generation.

36. noEmit is common with Vite.

37. sourceMap assists debugging.

38. declaration generates .d.ts files.

39. declaration is important for libraries.

40. declaration is usually unnecessary for normal React applications.

41. declarationMap creates declaration maps.

42. emitDeclarationOnly emits declarations without JavaScript.

43. allowJs includes JavaScript files.

44. checkJs type-checks JavaScript files.

45. JSX TypeScript files normally use .tsx.

46. Modern React commonly uses jsx: react-jsx.

47. paths creates TypeScript path mappings.

48. Path aliases must also work in bundler/runtime configuration.

49. include controls project file discovery.

50. exclude removes files from normal discovery.

51. exclude is not an access-control mechanism.

52. files explicitly lists project files.

53. extends inherits another tsconfig.

54. Child configs can override base settings.

55. references support multi-project architectures.

56. composite supports project references.

57. incremental can improve repeated build performance.

58. types controls automatic global type packages.

59. typeRoots controls type-package directories.

60. Most projects should not customize typeRoots unnecessarily.

61. Browser projects commonly require DOM declarations.

62. Node projects should not pretend browser APIs exist.

63. Runtime environment and type environment should agree.

64. React and build tooling may use separate tsconfig files.

65. Vite projects may have tsconfig.app.json.

66. Vite projects may have tsconfig.node.json.

67. Generated project structure can change between tooling versions.

68. Do not blindly replace generated configs.

69. Do not blindly copy old tutorials.

70. TypeScript configuration evolves.

71. moduleResolution should match actual module loading strategy.

72. Node module settings interact with package.json.

73. package.json "type" can affect Node ESM behavior.

74. tsc invokes the TypeScript compiler.

75. tsc --noEmit performs type checking without output.

76. tsc -p selects a configuration.

77. tsc --showConfig shows resolved configuration.

78. tsc -b works with project-reference builds.

79. TypeScript and ESLint serve different purposes.

80. TypeScript and bundlers serve different purposes.

81. TypeScript checks types.

82. ESLint checks broader code-quality rules.

83. Vite handles frontend development/building.

84. Formatters handle formatting.

85. CI should type-check TypeScript code.

86. New projects should start strict.

87. Fix type errors rather than disabling safety.

88. Do not use any merely to silence errors.

89. Stronger checks can be enabled deliberately.

90. noUncheckedIndexedAccess is useful for stronger safety.

91. exactOptionalPropertyTypes is useful for stronger modeling.

92. TypeScript types do not create runtime features.

93. DOM types do not create document/window in Node.

94. JSON typing does not validate runtime external JSON.

95. Path mappings do not automatically modify runtime imports.

96. Declaration generation does not replace runtime JavaScript.

97. noEmit means another tool must handle execution/building.

98. tsconfig should match the project architecture.

99. Different environments can use different configs.

100. The core mental model is:

Source Code
    ↓
tsconfig.json
    ↓
TypeScript Compiler
    ↓
Type Checking
+
Optional Output
```

---

# 156. Final Mental Model

When you create:

```text
React + Vite + TypeScript
```

think:

```text
             SOURCE CODE

          .ts        .tsx
            \         /
             \       /
              ▼     ▼

          TypeScript Compiler
                 ▲
                 │
          tsconfig.json
                 │
      ┌──────────┼──────────┐
      │          │          │
      ▼          ▼          ▼

   Language    Safety     Modules
   Settings    Rules      Resolution

   target      strict     module
   lib         null       moduleResolution
   jsx         any        paths

                 │
                 ▼

           Type Checking

                 │
                 ▼

          Is code valid?
             /       \
           NO         YES
           │           │
           ▼           ▼
       Fix errors    Vite
                       │
                       ▼
                 Build / Bundle
                       │
                       ▼
                    Browser
```

For a Node project where TypeScript emits JavaScript:

```text
src/*.ts
    │
    ▼
TypeScript
    │
    ├── Type Checking
    │
    └── Compilation
            │
            ▼
        dist/*.js
            │
            ▼
           Node
```

For React + Vite:

```text
TypeScript
    │
    ▼
Type Checking

Vite
    │
    ▼
Transform + Bundle + Build
```

The shortest rules to remember are:

```text
target
→ JavaScript language level

lib
→ Available API type declarations

module
→ Module semantics/output

moduleResolution
→ How imports are found

strict
→ Strong type checking

jsx
→ JSX handling

noEmit
→ Don't generate JS

outDir
→ Where generated files go

paths
→ Import aliases

include
→ Project files

extends
→ Inherit configuration

references
→ Connect TS projects
```

For **React + Vite + TypeScript**, your default mindset should be:

```text
strict: true
        +
noEmit: true
        +
jsx: react-jsx
        +
bundler-aware module resolution
        +
Vite handles the build
```

Most importantly:

```text
Do not memorize every tsconfig option.

Understand:

1. Type safety
2. Runtime environment
3. Module system
4. Build tool
5. Project structure

Then configure TypeScript
to match those five things.
```
