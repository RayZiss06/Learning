# Modules (`import` / `export`)

JavaScript **modules** allow us to split code into separate files and share variables, functions, classes, and other values between those files.

The two fundamental keywords are:

```javascript
export
import
```

Example:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Another file can import it:

```javascript
// app.js

import { add } from "./math.js";

console.log(add(10, 20));
```

Output:

```text
30
```

The basic idea:

```text
math.js
   │
   │ export
   ▼
  add()
   │
   │ import
   ▼
 app.js
```

---

# 1. Why Modules Are Needed

Imagine putting an entire application inside one JavaScript file:

```text
app.js

Authentication
User Management
API Calls
Validation
Utilities
Dashboard
Payments
Logging
Configuration
...
```

As the application grows, the file becomes difficult to:

```text
Read
Maintain
Test
Debug
Reuse
Organize
```

Modules allow us to separate responsibilities:

```text
src/
│
├── auth.js
├── users.js
├── api.js
├── validation.js
├── config.js
└── main.js
```

Each file can focus on a specific responsibility.

---

# 2. What Is a Module?

A module is a JavaScript file that can:

```text
Export values
Import values
```

For example:

```javascript
// math.js

const PI = 3.14159;

function add(a, b) {
    return a + b;
}

export {
    PI,
    add
};
```

Another module:

```javascript
// app.js

import {
    PI,
    add
} from "./math.js";
```

---

# 3. ES Modules

The modern JavaScript module system is called:

```text
ES Modules
```

Often abbreviated:

```text
ESM
```

It uses:

```javascript
import
export
```

ES modules were standardized with ES2015 (ES6).

---

# 4. Exporting Values

Suppose:

```javascript
// math.js

const PI = 3.14159;

function add(a, b) {
    return a + b;
}
```

These values are local to:

```text
math.js
```

To use them elsewhere, export them.

```javascript
export {
    PI,
    add
};
```

---

# 5. Named Exports

A **named export** exports a value under a specific name.

Example:

```javascript
// math.js

export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}
```

This module has three named exports:

```text
PI
add
subtract
```

---

# 6. Importing Named Exports

Named exports are imported using:

```javascript
{ }
```

Example:

```javascript
import {
    PI,
    add,
    subtract
} from "./math.js";
```

Now:

```javascript
console.log(PI);
console.log(add(10, 5));
console.log(subtract(10, 5));
```

Output:

```text
3.14159
15
5
```

---

# 7. Named Imports Must Match

Suppose:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Correct:

```javascript
import { add } from "./math.js";
```

Incorrect:

```javascript
import { addition } from "./math.js";
```

unless the module actually exports:

```text
addition
```

Named imports normally use the exported name.

---

# 8. Exporting After Declaration

You do not have to place `export` before every declaration.

Instead:

```javascript
const PI = 3.14159;

function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

export {
    PI,
    add,
    subtract
};
```

This is equivalent to exporting them individually.

---

# 9. Exporting Variables

```javascript
export const API_URL =
    "/api";

export let requestCount = 0;
```

You can export:

```text
const
let
functions
classes
```

and other bindings/values through export syntax.

---

# 10. Exporting Classes

Example:

```javascript
// User.js

export class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        return `Hello ${this.name}`;
    }
}
```

Import:

```javascript
import { User } from "./User.js";

const user =
    new User("John");

console.log(user.greet());
```

Output:

```text
Hello John
```

---

# 11. Multiple Named Exports

A module can have many named exports.

```javascript
// math.js

export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export function multiply(a, b) {
    return a * b;
}

export function divide(a, b) {
    return a / b;
}
```

Import only what you need:

```javascript
import {
    add,
    multiply
} from "./math.js";
```

You do not need to import every export.

---

# 12. Importing a Single Named Export

```javascript
import { add } from "./math.js";
```

Only:

```text
add
```

is imported into the current module.

---

# 13. Importing Multiple Named Exports

```javascript
import {
    add,
    subtract,
    multiply
} from "./math.js";
```

This is common when a module exposes several related utilities.

---

# 14. Import Aliases

You can rename a named import using:

```javascript
as
```

Example:

```javascript
import {
    add as sum
} from "./math.js";
```

Now:

```javascript
sum(10, 20);
```

works.

But:

```javascript
add(10, 20);
```

is not defined by that import.

---

# 15. Why Import Aliases Are Useful

Suppose two modules export the same name:

```javascript
// user.js

export function getData() {
    // ...
}
```

and:

```javascript
// product.js

export function getData() {
    // ...
}
```

You can import:

```javascript
import {
    getData as getUserData
} from "./user.js";

import {
    getData as getProductData
} from "./product.js";
```

Now there is no naming conflict.

---

# 16. Export Aliases

Exports can also be renamed.

```javascript
function add(a, b) {
    return a + b;
}

export {
    add as sum
};
```

The exported name is now:

```text
sum
```

Import:

```javascript
import { sum } from "./math.js";
```

---

# 17. Default Export

A module can have a **default export**.

Example:

```javascript
// calculator.js

function Calculator() {
    // ...
}

export default Calculator;
```

Import:

```javascript
import Calculator
    from "./calculator.js";
```

Notice:

```text
No { }
```

Default imports do not use curly braces.

---

# 18. Named vs Default Export

Named export:

```javascript
export function add() {
}
```

Import:

```javascript
import { add } from "./math.js";
```

Default export:

```javascript
export default function Calculator() {
}
```

Import:

```javascript
import Calculator from "./Calculator.js";
```

The major syntax difference:

```text
Named Export
↓
{ name }

Default Export
↓
name
```

---

# 19. Only One Default Export Per Module

A module can have:

```text
Many named exports
```

but only:

```text
One default export
```

Invalid:

```javascript
export default function add() {
}

export default function subtract() {
}
```

A module cannot have two default exports.

---

# 20. Named and Default Exports Together

A module can have one default export plus multiple named exports.

```javascript
// math.js

export default function add(a, b) {
    return a + b;
}

export const PI = 3.14159;

export function multiply(a, b) {
    return a * b;
}
```

Import:

```javascript
import add, {
    PI,
    multiply
} from "./math.js";
```

Notice:

```text
add
↓
Default import

PI, multiply
↓
Named imports
```

---

# 21. Default Import Can Be Renamed

Suppose:

```javascript
export default function Calculator() {
}
```

You can import it as:

```javascript
import Calculator
    from "./calculator.js";
```

or:

```javascript
import Calc
    from "./calculator.js";
```

or:

```javascript
import MyCalculator
    from "./calculator.js";
```

The importer chooses the local name for a default export.

---

# 22. Named Import Renaming Requires `as`

Named export:

```javascript
export function calculate() {
}
```

Normal import:

```javascript
import {
    calculate
} from "./math.js";
```

Rename:

```javascript
import {
    calculate as calc
} from "./math.js";
```

Named imports do not automatically get arbitrary names.

---

# 23. Default Export of a Class

```javascript
// User.js

export default class User {
    constructor(name) {
        this.name = name;
    }
}
```

Import:

```javascript
import User from "./User.js";
```

Then:

```javascript
const user =
    new User("John");
```

---

# 24. Default Export of a Function

```javascript
export default function greet(name) {
    return `Hello ${name}`;
}
```

Import:

```javascript
import greet from "./greet.js";
```

---

# 25. Default Export of an Expression

You can export an expression as default.

Example:

```javascript
const config = {
    port: 3000
};

export default config;
```

Import:

```javascript
import config from "./config.js";
```

---

# 26. Anonymous Default Function

This is valid:

```javascript
export default function () {
    console.log("Hello");
}
```

Import:

```javascript
import greet from "./greet.js";
```

The importing module gives it a local name.

For maintainability and debugging, named functions are often easier to identify.

---

# 27. Import Everything with `*`

You can import all named exports as a namespace object.

Example:

```javascript
// math.js

export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}
```

Import:

```javascript
import * as math
    from "./math.js";
```

Use:

```javascript
console.log(math.PI);

console.log(
    math.add(10, 20)
);
```

---

# 28. Namespace Import

This:

```javascript
import * as math
    from "./math.js";
```

creates a module namespace binding called:

```text
math
```

You access named exports through it:

```javascript
math.PI
math.add
math.subtract
```

This can be useful when many related exports should remain grouped.

---

# 29. Side-Effect Imports

Sometimes we import a module without importing a particular value.

```javascript
import "./setup.js";
```

This means:

```text
Load and execute the module.
```

Example:

```javascript
// setup.js

console.log(
    "Application initialized"
);
```

Then:

```javascript
import "./setup.js";
```

causes the module's top-level code to execute when the module is evaluated.

---

# 30. Module Scope

Modules have their own scope.

Suppose:

```javascript
// user.js

const name = "John";
```

and:

```javascript
// product.js

const name = "Laptop";
```

There is no conflict merely because both modules have:

```javascript
name
```

Each module has its own scope.

---

# 31. Variables Are Not Automatically Global

Inside:

```javascript
// user.js

const username = "John";
```

`username` is not automatically available inside:

```javascript
// app.js
```

To share it:

```javascript
export const username = "John";
```

Then:

```javascript
import {
    username
} from "./user.js";
```

---

# 32. Modules Use Strict Mode

ES modules automatically operate in:

```text
strict mode
```

You do not need:

```javascript
"use strict";
```

inside an ES module.

---

# 33. Strict Mode Example

In sloppy-mode classic scripts, some mistakes historically behaved differently.

For example:

```javascript
username = "John";
```

without declaring `username` is not allowed in module code.

Modules are strict automatically.

---

# 34. Browser Modules

In browsers, use:

```html
<script
    type="module"
    src="./app.js"
></script>
```

The important part is:

```html
type="module"
```

This tells the browser that:

```text
app.js
```

is an ES module.

---

# 35. Browser Example

HTML:

```html
<!DOCTYPE html>

<html>
<head>
    <title>Modules</title>
</head>

<body>

<script
    type="module"
    src="./main.js"
></script>

</body>
</html>
```

Then:

```javascript
// main.js

import { add }
    from "./math.js";

console.log(
    add(10, 20)
);
```

---

# 36. Module Scripts Are Deferred

Browser module scripts behave as deferred scripts by default.

Conceptually:

```text
HTML parsing continues
↓
Module graph is loaded
↓
Module code executes after document parsing
```

You generally do not need to add:

```html
defer
```

to a normal:

```html
<script type="module">
```

---

# 37. Relative Import Paths

Example structure:

```text
src/
│
├── main.js
│
└── utils/
    └── math.js
```

From:

```text
main.js
```

import:

```javascript
import { add }
    from "./utils/math.js";
```

Here:

```text
./
```

means:

```text
Current directory
```

---

# 38. Parent Directory

Structure:

```text
src/
│
├── utils/
│   └── math.js
│
└── pages/
    └── home.js
```

From:

```text
pages/home.js
```

to:

```text
utils/math.js
```

you might use:

```javascript
import { add }
    from "../utils/math.js";
```

Here:

```text
../
```

means:

```text
Go up one directory
```

---

# 39. Import Paths Are Relative to the Importing Module

Suppose:

```text
src/
├── main.js
├── utils/
│   └── math.js
└── pages/
    └── dashboard.js
```

Inside:

```text
dashboard.js
```

the path is calculated relative to:

```text
dashboard.js
```

not necessarily relative to the project root.

---

# 40. File Extensions

In native browser ES modules, imports commonly include the file extension:

```javascript
import { add }
    from "./math.js";
```

Tools such as bundlers and development environments may support additional resolution behavior.

For revision, remember:

```text
Native Browser ESM
↓
Use correct module paths and typically explicit extensions.

Bundler/Tooling
↓
Resolution rules may differ.
```

---

# 41. Static Imports

This:

```javascript
import { add }
    from "./math.js";
```

is a **static import**.

Static imports are declared at the module level.

They allow tools to understand module dependencies before executing the application.

---

# 42. Imports Are Hoisted

Static imports are processed as part of module linking before module code executes.

For example:

```javascript
console.log(add(10, 20));

import { add }
    from "./math.js";
```

can work in an ES module.

However, convention is to put imports at the top:

```javascript
import { add }
    from "./math.js";

console.log(
    add(10, 20)
);
```

This is much clearer.

---

# 43. Static Imports Are Not Conditional Statements

You cannot use a static import like:

```javascript
if (condition) {
    import { add }
        from "./math.js";
}
```

Static `import` declarations belong at module top level.

For conditional loading, use:

```javascript
import()
```

which is dynamic import syntax.

---

# 44. Dynamic Import

Dynamic imports use:

```javascript
import()
```

Example:

```javascript
const module =
    await import("./math.js");
```

Then:

```javascript
console.log(
    module.add(10, 20)
);
```

---

# 45. Dynamic Import Returns a Promise

This:

```javascript
import("./math.js")
```

returns a:

```text
Promise
```

Therefore:

```javascript
import("./math.js")
    .then(module => {
        console.log(
            module.add(10, 20)
        );
    });
```

Or with `await` where allowed:

```javascript
const module =
    await import("./math.js");
```

---

# 46. Conditional Dynamic Import

Example:

```javascript
if (isAdmin) {
    const adminModule =
        await import(
            "./admin.js"
        );

    adminModule.showDashboard();
}
```

The module is loaded only when that code path runs.

This can be useful for:

```text
Conditional features
Lazy loading
Code splitting
Large modules
Route-based loading
```

---

# 47. Dynamic Named Export

Suppose:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Dynamic import:

```javascript
const module =
    await import("./math.js");

module.add(10, 20);
```

Named exports become properties on the returned module namespace object.

---

# 48. Dynamic Default Export

Suppose:

```javascript
// User.js

export default class User {
}
```

Dynamic import:

```javascript
const module =
    await import("./User.js");

const User =
    module.default;
```

The default export is available through:

```javascript
module.default
```

---

# 49. Re-exporting

One module can re-export values from another module.

Suppose:

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Then:

```javascript
// index.js

export {
    add
} from "./math.js";
```

Now another module can import from:

```javascript
index.js
```

instead.

---

# 50. Re-export Multiple Values

```javascript
export {
    add,
    subtract,
    multiply
} from "./math.js";
```

This forwards those exports.

---

# 51. Re-export Everything

```javascript
export * from "./math.js";
```

This re-exports the module's named exports.

Important:

```text
export *
```

does not re-export the source module's default export.

---

# 52. Re-export Default

You can explicitly re-export a default export:

```javascript
export {
    default
} from "./User.js";
```

Or expose it under a named export:

```javascript
export {
    default as User
} from "./User.js";
```

---

# 53. Barrel Files

A **barrel file** is commonly an:

```text
index.js
```

file that re-exports values from multiple modules.

Example:

```text
components/
│
├── Button.js
├── Navbar.js
├── Modal.js
└── index.js
```

`index.js`:

```javascript
export {
    default as Button
} from "./Button.js";

export {
    default as Navbar
} from "./Navbar.js";

export {
    default as Modal
} from "./Modal.js";
```

Now:

```javascript
import {
    Button,
    Navbar,
    Modal
} from "./components/index.js";
```

instead of several separate imports.

Barrels can be convenient, but excessive barrel usage can also complicate dependency graphs and circular dependencies.

---

# 54. Live Bindings

ES module imports are **live bindings**.

Suppose:

```javascript
// counter.js

export let count = 0;

export function increment() {
    count++;
}
```

Import:

```javascript
import {
    count,
    increment
} from "./counter.js";

console.log(count);

increment();

console.log(count);
```

Output:

```text
0
1
```

The imported binding reflects updates made by the exporting module.

---

# 55. Imported Bindings Are Read-Only to the Importer

Suppose:

```javascript
import {
    count
} from "./counter.js";
```

You cannot do:

```javascript
count = 100;
```

The importing module cannot reassign the imported binding.

The exporting module can update its own binding.

---

# 56. Importing Objects

Suppose:

```javascript
// config.js

export const config = {
    theme: "dark"
};
```

Import:

```javascript
import {
    config
} from "./config.js";
```

The imported binding itself cannot be reassigned:

```javascript
config = {};
```

But if the exported object is mutable, its properties may still be mutable:

```javascript
config.theme = "light";
```

`const` prevents reassignment of the binding; it does not automatically make an object immutable.

---

# 57. Module Evaluation Happens Once

When multiple modules import the same module, that module is normally instantiated/evaluated once for that module graph and its module instance is reused.

Example:

```javascript
// config.js

console.log(
    "Config loaded"
);

export const config = {};
```

If several modules import `config.js`, you do not normally get a fresh independent module state for each importer.

This matters for shared module-level state.

---

# 58. Module Singleton-Like State

Example:

```javascript
// counter.js

let count = 0;

export function increment() {
    count++;
}

export function getCount() {
    return count;
}
```

Different importing modules interact with the same module-level `count` for that module instance.

This can be useful, but shared mutable state should be designed carefully.

---

# 59. Circular Dependencies

Suppose:

```text
a.js
↓ imports
b.js

b.js
↓ imports
a.js
```

This creates:

```text
Circular Dependency
```

ES modules can support cycles, but initialization order and accessing bindings too early can create confusing behavior.

A better architecture often avoids unnecessary cycles.

---

# 60. ES Modules vs CommonJS

There are two module systems you will frequently encounter in JavaScript ecosystems:

```text
ES Modules
CommonJS
```

ES Modules:

```javascript
import {
    something
} from "./module.js";

export {
    something
};
```

CommonJS:

```javascript
const something =
    require("./module");

module.exports =
    something;
```

---

# 61. CommonJS

CommonJS historically became the primary module system in Node.js before ES modules were standardized and broadly supported.

Example:

```javascript
// math.js

function add(a, b) {
    return a + b;
}

module.exports = {
    add
};
```

Import:

```javascript
const {
    add
} = require("./math");
```

---

# 62. ES Module Version

```javascript
// math.js

export function add(a, b) {
    return a + b;
}
```

Import:

```javascript
import {
    add
} from "./math.js";
```

Modern frontend development commonly uses ES module syntax.

---

# 63. `require()` vs `import`

CommonJS:

```javascript
const express =
    require("express");
```

ES Modules:

```javascript
import express
    from "express";
```

They belong to different module systems.

How they interoperate depends on the runtime, package configuration, and tooling.

---

# 64. `module.exports` vs `export`

CommonJS:

```javascript
module.exports = {
    add,
    subtract
};
```

ES Modules:

```javascript
export {
    add,
    subtract
};
```

Do not assume their semantics are identical simply because both expose values between files.

---

# 65. Node.js ES Modules

Modern Node.js supports ES modules.

A project may use:

```json
{
    "type": "module"
}
```

inside:

```text
package.json
```

to treat `.js` files in that package scope as ES modules.

Node.js also supports:

```text
.mjs
```

for ES module files.

The exact behavior depends on file extension and package configuration.

---

# 66. Node.js CommonJS

A project may use CommonJS with:

```javascript
const module =
    require("./module");
```

and:

```javascript
module.exports = {};
```

The:

```text
.cjs
```

extension explicitly indicates CommonJS in Node.js.

---

# 67. React Uses ES Modules Everywhere

In React projects, you will constantly see:

```javascript
import
export
```

Example:

```jsx
// Button.jsx

export default function Button() {
    return (
        <button>
            Click Me
        </button>
    );
}
```

Then:

```jsx
// App.jsx

import Button
    from "./Button.jsx";

function App() {
    return (
        <Button />
    );
}
```

---

# 68. React Named Component Export

Instead:

```jsx
// Button.jsx

export function Button() {
    return (
        <button>
            Click Me
        </button>
    );
}
```

Import:

```jsx
import {
    Button
} from "./Button.jsx";
```

The difference comes from JavaScript modules, not React.

---

# 69. React Default vs Named Export

Default:

```jsx
export default function Button() {
}
```

Import:

```javascript
import Button from "./Button.jsx";
```

Named:

```jsx
export function Button() {
}
```

Import:

```javascript
import {
    Button
} from "./Button.jsx";
```

This distinction is extremely important.

---

# 70. React Multiple Named Exports

Example:

```jsx
export function Header() {
    return <header />;
}

export function Footer() {
    return <footer />;
}
```

Import:

```javascript
import {
    Header,
    Footer
} from "./Layout.jsx";
```

---

# 71. React Default + Named

Example:

```jsx
export default function Dashboard() {
    return <main />;
}

export function DashboardHeader() {
    return <header />;
}
```

Import:

```javascript
import Dashboard, {
    DashboardHeader
} from "./Dashboard.jsx";
```

---

# 72. Vite and ES Modules

A React application created with Vite uses ES module syntax throughout the source code.

Typical structure:

```text
src/
│
├── main.jsx
├── App.jsx
├── components/
├── pages/
├── hooks/
├── services/
└── utils/
```

Example:

```javascript
import App from "./App.jsx";
```

Inside:

```text
App.jsx
```

you might have:

```javascript
import Navbar
    from "./components/Navbar.jsx";

import Dashboard
    from "./pages/Dashboard.jsx";
```

Modules connect the application files together.

---

# 73. Importing Libraries

Imports are not limited to your own files.

Example:

```javascript
import React from "react";
```

or:

```javascript
import {
    createRoot
} from "react-dom/client";
```

Notice:

```javascript
"react"
```

does not start with:

```text
./
../
```

It is a package specifier resolved by the environment/tooling.

---

# 74. Local Module vs Package Import

Local module:

```javascript
import Button
    from "./components/Button.jsx";
```

Package:

```javascript
import {
    createRoot
} from "react-dom/client";
```

Conceptually:

```text
./ or ../
↓
Relative module path

Package name
↓
External/package dependency
```

---

# 75. CSS Imports in Vite

You may see:

```javascript
import "./index.css";
```

This is a tooling-supported side-effect import.

Vite processes the CSS dependency and includes it in the application.

This is not the same as the browser natively treating CSS as a normal JavaScript module in every context.

---

# 76. JSON and Other Assets

Modern tooling may allow imports such as:

```javascript
import data from "./data.json";
```

or:

```javascript
import logo from "./logo.svg";
```

How these imports behave depends on:

```text
Runtime
Bundler
Build tool
Configuration
```

Do not assume every asset import works identically in every JavaScript environment.

---

# 77. Tree Shaking

ES module structure allows build tools to analyze exports and imports.

Example:

```javascript
export function add() {
}

export function subtract() {
}

export function multiply() {
}
```

If the application imports only:

```javascript
import {
    add
} from "./math.js";
```

a production build tool may be able to remove unused code.

This optimization is called:

```text
Tree Shaking
```

Whether code can actually be removed depends on side effects and the build tool.

---

# 78. Why Static Imports Help Tooling

Static:

```javascript
import {
    add
} from "./math.js";
```

makes dependencies explicit.

Tools can analyze:

```text
Which module is imported?
Which exports are used?
How are modules connected?
```

This helps with:

```text
Bundling
Tree shaking
Dependency analysis
Code optimization
```

---

# 79. Dynamic Imports and Code Splitting

Suppose a large admin feature is not needed by every user.

Instead of loading it immediately:

```javascript
import AdminDashboard
    from "./AdminDashboard.js";
```

you may load it dynamically:

```javascript
const module =
    await import(
        "./AdminDashboard.js"
    );
```

Build tools can often use dynamic imports as code-splitting boundaries.

---

# 80. React Lazy Loading Connection

Later in React you may see:

```javascript
const Dashboard =
    lazy(() =>
        import("./Dashboard.jsx")
    );
```

The:

```javascript
import("./Dashboard.jsx")
```

part is JavaScript dynamic import.

React's:

```javascript
lazy()
```

uses it to support lazy-loaded components.

Understanding modules now makes this much easier later.

---

# 81. Common Mistake — Forgetting Curly Braces

Suppose:

```javascript
export function add() {
}
```

Incorrect:

```javascript
import add from "./math.js";
```

unless `math.js` also has a default export that fits this import.

For the named export:

```javascript
import {
    add
} from "./math.js";
```

---

# 82. Common Mistake — Adding Braces to Default Import

Suppose:

```javascript
export default function Calculator() {
}
```

Incorrect for importing that default:

```javascript
import {
    Calculator
} from "./Calculator.js";
```

Correct:

```javascript
import Calculator
    from "./Calculator.js";
```

---

# 83. Common Mistake — Wrong Named Export

Suppose:

```javascript
export function add() {
}
```

Incorrect:

```javascript
import {
    Add
} from "./math.js";
```

JavaScript identifiers are case-sensitive.

Correct:

```javascript
import {
    add
} from "./math.js";
```

---

# 84. Common Mistake — Wrong Path

Suppose:

```text
src/
├── main.js
└── utils/
    └── math.js
```

Correct:

```javascript
import {
    add
} from "./utils/math.js";
```

Not:

```javascript
import {
    add
} from "./math.js";
```

unless `math.js` actually exists beside `main.js`.

---

# 85. Common Mistake — Multiple Default Exports

Invalid:

```javascript
export default function add() {
}

export default function subtract() {
}
```

Remember:

```text
One module
↓
Maximum one default export
```

But you can have:

```text
Many named exports
```

---

# 86. Common Mistake — Reassigning an Import

Suppose:

```javascript
import {
    count
} from "./counter.js";
```

Invalid:

```javascript
count = 100;
```

Imports are bindings that the importer cannot reassign.

---

# 87. Common Mistake — Assuming Imports Are Copies

Suppose:

```javascript
export let count = 0;

export function increment() {
    count++;
}
```

An importer sees updates to:

```javascript
count
```

Imports are live bindings, not simple copied snapshots.

---

# 88. Common Mistake — Mixing Module Systems Without Understanding Runtime Rules

Example:

```javascript
import something
    from "./module.js";

const other =
    require("./other");
```

Whether mixing these works depends on the runtime and configuration.

Do not randomly mix:

```text
ESM
CommonJS
```

Use the module system configured for your project and understand interop when needed.

---

# 89. Common Mistake — Assuming Every Environment Resolves Imports the Same Way

This:

```javascript
import something
    from "./module";
```

may work in some build tools.

Native browser ESM commonly expects the actual resource path, such as:

```javascript
import something
    from "./module.js";
```

Node.js and bundlers also have their own resolution rules.

Always consider the runtime.

---

# 90. Common Mistake — Unnecessary `import *`

Instead of:

```javascript
import * as math
    from "./math.js";
```

when you only need:

```javascript
math.add()
```

you can often write:

```javascript
import {
    add
} from "./math.js";
```

Specific imports can make dependencies clearer.

---

# 91. Common Mistake — Confusing `export *` with Default Export

Suppose:

```javascript
// User.js

export default class User {
}
```

Then:

```javascript
export * from "./User.js";
```

does not automatically forward that default export.

You must explicitly re-export it:

```javascript
export {
    default as User
} from "./User.js";
```

---

# 92. Interview Question 1

What is wrong?

```javascript
// math.js

export function add() {
}
```

```javascript
// app.js

import add from "./math.js";
```

Answer:

```text
add is a named export,
but the code is trying to import
a default export.
```

Correct:

```javascript
import {
    add
} from "./math.js";
```

---

# 93. Interview Question 2

What is wrong?

```javascript
// User.js

export default class User {
}
```

```javascript
import {
    User
} from "./User.js";
```

Answer:

```text
User is the default export,
not a named export.
```

Correct:

```javascript
import User from "./User.js";
```

---

# 94. Interview Question 3

How many default exports can one module have?

Answer:

```text
One
```

A module can have many named exports.

---

# 95. Interview Question 4

Can a module have both default and named exports?

Answer:

```text
Yes
```

Example:

```javascript
export default function add() {
}

export const PI = 3.14159;
```

Import:

```javascript
import add, {
    PI
} from "./math.js";
```

---

# 96. Interview Question 5

What does this mean?

```javascript
import * as math
    from "./math.js";
```

Answer:

```text
Import the module namespace
under the local name "math".
```

Named exports can then be accessed as:

```javascript
math.add
math.subtract
math.PI
```

---

# 97. Interview Question 6

What does this do?

```javascript
import "./setup.js";
```

Answer:

```text
Loads the module for its side effects
without importing a specific binding.
```

---

# 98. Interview Question 7

What is:

```javascript
import()
```

Answer:

```text
Dynamic import.
```

It returns a:

```text
Promise
```

and can load a module dynamically.

---

# 99. Interview Question 8

Can static `import` declarations be placed inside a normal `if` block?

Answer:

```text
No
```

Use dynamic:

```javascript
import()
```

for conditional loading.

---

# 100. Interview Question 9

What is tree shaking?

Answer:

```text
A build optimization where tooling
removes code that can be determined
to be unused from the final bundle.
```

ES module structure makes this analysis easier.

---

# 101. Interview Question 10

What are live bindings?

Answer:

```text
Imported bindings reflect updates
made to the corresponding exported
binding by the exporting module.
```

Example:

```javascript
export let count = 0;

export function increment() {
    count++;
}
```

Importers observe the updated value after:

```javascript
increment();
```

---

# 102. Interview Question 11

Can an importer reassign a named import?

```javascript
import {
    count
} from "./counter.js";

count = 100;
```

Answer:

```text
No
```

Imported bindings cannot be reassigned by the importer.

---

# 103. Interview Question 12

Are ES modules automatically strict?

Answer:

```text
Yes
```

ES modules execute in strict mode automatically.

---

# 104. Interview Question 13

What does this do?

```javascript
export * from "./math.js";
```

Answer:

```text
Re-exports named exports
from math.js.
```

It does not automatically re-export the default export.

---

# 105. Interview Question 14

What is a barrel file?

Answer:

```text
A module, often index.js,
that re-exports values from
multiple modules to provide
a centralized import surface.
```

Example:

```javascript
export {
    default as Button
} from "./Button.js";

export {
    default as Modal
} from "./Modal.js";
```

---

# 106. Interview Question 15

What is the difference between:

```javascript
import User from "./User.js";
```

and:

```javascript
import {
    User
} from "./User.js";
```

Answer:

```text
First:
Imports the default export
under the local name User.

Second:
Imports a named export
whose exported name is User.
```

---

# 107. Interview Question 16

Can a default import use a different local name?

Given:

```javascript
export default class User {
}
```

Can we do:

```javascript
import Person from "./User.js";
```

Answer:

```text
Yes
```

A default import's local name is chosen by the importing module.

---

# 108. Interview Question 17

What is the difference between:

```javascript
import { add }
    from "./math.js";
```

and:

```javascript
const module =
    await import("./math.js");
```

Answer:

```text
First:
Static import.

Second:
Dynamic import returning a Promise
that resolves to a module namespace object.
```

---

# 109. Interview Question 18

What module system uses:

```javascript
require()
module.exports
```

Answer:

```text
CommonJS
```

What module system uses:

```javascript
import
export
```

Answer:

```text
ES Modules
```

---

# 110. Interview Question 19

What does:

```html
<script type="module">
```

do?

Answer:

```text
Treats the script as an ES module.
```

This enables module behavior such as:

```text
import/export
module scope
automatic strict mode
```

---

# 111. Interview Question 20

What happens here?

```javascript
// counter.js

export let count = 0;

export function increment() {
    count++;
}
```

```javascript
// app.js

import {
    count,
    increment
} from "./counter.js";

console.log(count);

increment();

console.log(count);
```

Output:

```text
0
1
```

Because imports are live bindings.

---

# 112. Named Export Mental Model

```javascript
// math.js

export function add() {
}
```

Think:

```text
math.js
│
├── Named Export
│
└── add
      │
      ▼
import { add }
      │
      ▼
app.js
```

Curly braces mean:

```text
Give me the export
with this exported name.
```

---

# 113. Default Export Mental Model

```javascript
// User.js

export default class User {
}
```

Think:

```text
User.js
│
└── Default Export
       │
       ▼
import Anything from "./User.js"
       │
       ▼
Current Module
```

The importing module chooses the local identifier.

---

# 114. Named vs Default Mental Model

```text
NAMED EXPORT
==============================

export const name = "John";

             │
             ▼

import { name } from "./user.js";


DEFAULT EXPORT
==============================

export default User;

             │
             ▼

import User from "./User.js";
```

Remember:

```text
Named
↓
{ }

Default
↓
No { }
```

---

# 115. Static vs Dynamic Import

```text
STATIC IMPORT
==============================

import { add } from "./math.js";

Dependency declared
at module level.


DYNAMIC IMPORT
==============================

const module =
    await import("./math.js");

Loaded dynamically
when execution reaches it.

Returns a Promise.
```

---

# 116. ES Modules vs CommonJS

```text
ES MODULES
================================

export
export default
import
import()

Modern standardized module system.


COMMONJS
================================

module.exports
exports
require()

Historically common in Node.js.
```

---

# 117. React Mental Model

Suppose:

```text
src/
│
├── main.jsx
├── App.jsx
└── components/
    ├── Navbar.jsx
    └── Button.jsx
```

Connections:

```text
main.jsx
   │
   │ import
   ▼
 App.jsx
   │
   ├──────────────┐
   │              │
 import          import
   │              │
   ▼              ▼
Navbar.jsx     Button.jsx
```

Modules are what allow these separate files to work together as one application.

---

# 118. Module Cheat Sheet

## Named Export

```javascript
export const name = "John";
```

Import:

```javascript
import {
    name
} from "./user.js";
```

## Named Function

```javascript
export function greet() {
}
```

Import:

```javascript
import {
    greet
} from "./greet.js";
```

## Multiple Named Exports

```javascript
export {
    add,
    subtract
};
```

Import:

```javascript
import {
    add,
    subtract
} from "./math.js";
```

## Default Export

```javascript
export default User;
```

Import:

```javascript
import User from "./User.js";
```

## Default + Named

```javascript
export default User;

export const role = "Admin";
```

Import:

```javascript
import User, {
    role
} from "./User.js";
```

## Import Alias

```javascript
import {
    add as sum
} from "./math.js";
```

## Export Alias

```javascript
export {
    add as sum
};
```

## Namespace Import

```javascript
import * as math
    from "./math.js";
```

## Side-Effect Import

```javascript
import "./setup.js";
```

## Dynamic Import

```javascript
const module =
    await import("./math.js");
```

## Re-export

```javascript
export {
    add
} from "./math.js";
```

## Re-export Named Exports

```javascript
export * from "./math.js";
```

## Re-export Default as Named

```javascript
export {
    default as User
} from "./User.js";
```

---

# 119. Important Rules to Remember

```text
1. Modules split JavaScript into separate files.

2. Modules improve organization and reuse.

3. ES Modules use import and export.

4. ES Modules are commonly abbreviated ESM.

5. Named exports use explicit exported names.

6. Named imports use curly braces.

7. Named import names must match the exported names unless aliased.

8. Named imports can use `as` aliases.

9. Exports can also be aliased.

10. A module can have multiple named exports.

11. A module can have only one default export.

12. Default imports do not use curly braces.

13. The importer chooses the local name of a default import.

14. A module can contain both default and named exports.

15. `import * as name` creates a module namespace binding.

16. Side-effect imports load a module without importing a specific binding.

17. Modules have their own scope.

18. Module variables are not automatically global.

19. ES modules execute in strict mode automatically.

20. Browsers use `<script type="module">` for module scripts.

21. Browser module scripts are deferred by default.

22. Relative imports commonly use `./` and `../`.

23. Import paths are resolved relative to the importing module for relative specifiers.

24. Native browser imports commonly use explicit file extensions.

25. Tooling may provide additional module resolution behavior.

26. Static imports are declared at module level.

27. Static imports are processed during module linking.

28. Conventionally imports are placed at the top of the file.

29. Static import declarations cannot be placed inside ordinary conditional blocks.

30. `import()` performs dynamic imports.

31. Dynamic import returns a Promise.

32. Dynamic imports can support conditional loading.

33. Dynamic imports are useful for lazy loading.

34. Build tools can use dynamic imports for code splitting.

35. Modules can re-export values from other modules.

36. `export *` forwards named exports.

37. `export *` does not automatically forward a default export.

38. Barrel files centralize exports from multiple modules.

39. Imports are live bindings.

40. Imported bindings cannot be reassigned by the importer.

41. Exported mutable bindings can change in the exporting module.

42. Imported objects may still contain mutable properties.

43. Module code is normally evaluated once per module instance.

44. Module-level state can therefore be shared by importers.

45. Circular dependencies are possible.

46. Circular dependencies can create initialization problems.

47. ES Modules and CommonJS are different module systems.

48. CommonJS uses require().

49. CommonJS uses module.exports/exports.

50. ES Modules use import/export.

51. Modern Node.js supports ES Modules.

52. Node.js module behavior depends on file extension and package configuration.

53. `.mjs` explicitly represents ESM in Node.js.

54. `.cjs` explicitly represents CommonJS in Node.js.

55. React/Vite projects heavily use ES Modules.

56. React component imports and exports are JavaScript module behavior.

57. Default React component exports use default import syntax.

58. Named React component exports use named import syntax.

59. Packages can also be imported through module specifiers.

60. Build tools may support CSS, JSON, SVG, and other asset imports.

61. Asset import behavior depends on tooling/runtime.

62. Static ESM structure helps dependency analysis.

63. ESM enables build tools to perform tree-shaking analysis.

64. Tree shaking can remove unused code when safe.

65. Do not confuse named and default exports.

66. Do not randomly mix CommonJS and ESM without understanding runtime interop.

67. Do not assume every runtime resolves import paths identically.

68. Modules are foundational to modern JavaScript architecture.
```

---

# 120. Most Important Concept

Remember these two patterns:

```javascript
// NAMED EXPORT

export function add() {
}
```

Import:

```javascript
import {
    add
} from "./math.js";
```

Think:

```text
Named Export
     ↓
{ exactName }
```

And:

```javascript
// DEFAULT EXPORT

export default function Calculator() {
}
```

Import:

```javascript
import Calculator
    from "./Calculator.js";
```

Think:

```text
Default Export
     ↓
No curly braces
     ↓
Importer chooses local name
```

So:

```text
export
↓
Makes something available
outside the module

import
↓
Uses something exported
by another module
```

The module graph:

```text
                Application
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
       Users       Auth       API
          │          │          │
          ▼          ▼          ▼
      Utilities   Config     Services
```

Each file has its own responsibility, while:

```javascript
import
export
```

connect them together.

---

# 121. Modules in One Sentence

> JavaScript modules allow applications to split code across separate files using `export` to expose values and `import` to consume them, providing module scope, reusable dependencies, and the foundation for modern JavaScript and React application architecture.
