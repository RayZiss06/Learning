# TypeScript Fundamentals

TypeScript is a programming language built on top of JavaScript that adds a **static type system** and additional developer tooling.

```text
JavaScript
    +
Static Type System
    +
Compile-Time Checking
    +
Developer Tooling
    =
TypeScript
```

TypeScript code is eventually transformed into JavaScript because browsers and JavaScript runtimes execute JavaScript, not TypeScript type syntax directly.

---

# 1. What Is TypeScript?

TypeScript is a **superset of JavaScript** developed by Microsoft.

A superset means TypeScript includes JavaScript and adds additional capabilities on top of it.

```text
┌─────────────────────────────┐
│         TypeScript          │
│                             │
│    ┌───────────────────┐    │
│    │    JavaScript     │    │
│    └───────────────────┘    │
│                             │
│  + Static Types             │
│  + Interfaces               │
│  + Generics                 │
│  + Type Checking            │
│  + Utility Types            │
│  + Type Manipulation        │
│  + Better Tooling           │
│                             │
└─────────────────────────────┘
```

JavaScript:

```javascript
function add(a, b) {
    return a + b;
}
```

TypeScript:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

The actual addition operation is still JavaScript behavior.

TypeScript only adds information about what types are expected.

---

# 2. TypeScript Does Not Replace JavaScript

TypeScript does not introduce a completely different runtime programming model.

Concepts such as:

```text
Variables
Functions
Objects
Arrays
Classes
Promises
Async/Await
Closures
Callbacks
Modules
Event Loop
Spread
Destructuring
```

still behave according to JavaScript.

TypeScript mainly adds a **type-checking layer** over that code.

---

# 3. JavaScript vs TypeScript

JavaScript:

```javascript
let username = "Alice";

username = 100;
```

JavaScript allows this.

Initially:

```text
username → string
```

Later:

```text
username → number
```

TypeScript can prevent this mistake during type checking.

```typescript
let username: string =
    "Alice";

username = 100;
```

TypeScript reports an error similar to:

```text
Type 'number' is not assignable
to type 'string'.
```

The problem can therefore be detected before the incorrect code reaches runtime.

---

# 4. Dynamic Typing vs Static Typing

JavaScript is dynamically typed.

```javascript
let value = "Hello";

value = 100;

value = true;
```

The variable can hold values of different types during execution.

---

## TypeScript Static Typing

TypeScript provides static type checking.

```typescript
let value: string =
    "Hello";

value = 100;
```

TypeScript reports a type error.

Conceptually:

```text
JavaScript

Write Code
    │
    ▼
Run Code
    │
    ▼
Some Errors Appear


TypeScript

Write Code
    │
    ▼
Type Checker
    │
    ├── Error
    │
    └── Valid
          │
          ▼
      JavaScript
          │
          ▼
         Run
```

---

# 5. TypeScript Checks Types Before Runtime

Consider:

```typescript
function calculatePrice(
    price: number,
    quantity: number
): number {
    return price * quantity;
}
```

Correct:

```typescript
calculatePrice(
    100,
    5
);
```

Incorrect:

```typescript
calculatePrice(
    "100",
    5
);
```

TypeScript detects that:

```text
"100"
```

is a:

```text
string
```

while:

```text
price
```

expects:

```text
number
```

---

# 6. Compile-Time vs Runtime

This distinction is extremely important.

## Compile Time

TypeScript performs type checking before execution.

Example:

```typescript
let age: number = "25";
```

TypeScript reports a compile-time/type-checking error.

---

## Runtime

Runtime is when the generated JavaScript is actually executing.

Example:

```javascript
const user = null;

console.log(
    user.name
);
```

This causes a runtime error.

TypeScript can detect many possible runtime mistakes beforehand when enough type information is available, but it cannot eliminate every runtime error.

---

# 7. TypeScript Types Do Not Exist at Runtime

Consider:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

After TypeScript compilation, the output may conceptually become:

```javascript
function add(a, b) {
    return a + b;
}
```

The:

```typescript
: number
```

information disappears.

This process is called **type erasure**.

---

# 8. Type Erasure

TypeScript types generally exist only during development/type checking.

```typescript
const username: string =
    "Alice";
```

Generated JavaScript:

```javascript
const username =
    "Alice";
```

The browser does not receive:

```typescript
: string
```

That information was used by TypeScript and then removed.

---

# 9. TypeScript Does Not Validate Runtime Data

This is a very important real-world concept.

Suppose:

```typescript
interface User {
    id: number;
    name: string;
}
```

You fetch data:

```typescript
const response =
    await fetch(
        "/api/user"
    );
```

TypeScript cannot guarantee that the server actually returns:

```json
{
    "id": 1,
    "name": "Alice"
}
```

The server could return:

```json
{
    "id": "wrong",
    "name": null
}
```

TypeScript types disappear at runtime.

Therefore:

```text
TypeScript Type Checking
≠
Runtime Validation
```

External data may still need runtime validation.

---

# 10. TypeScript Is JavaScript with Type Information

JavaScript:

```javascript
const user = {
    name: "Alice",
    age: 25
};
```

TypeScript:

```typescript
const user: {
    name: string;
    age: number;
} = {
    name: "Alice",
    age: 25
};
```

The runtime object is still fundamentally:

```javascript
{
    name: "Alice",
    age: 25
}
```

TypeScript adds compile-time knowledge about its expected structure.

---

# 11. Basic Type Annotation Syntax

The general syntax is:

```typescript
variableName: Type
```

Example:

```typescript
let username: string;

let age: number;

let active: boolean;
```

Initialization:

```typescript
let username: string =
    "Alice";

let age: number =
    25;

let active: boolean =
    true;
```

---

# 12. Function Parameter Types

JavaScript:

```javascript
function greet(name) {
    return `Hello ${name}`;
}
```

TypeScript:

```typescript
function greet(
    name: string
) {
    return `Hello ${name}`;
}
```

Now:

```typescript
greet("Alice");
```

is valid.

But:

```typescript
greet(100);
```

produces a type error.

---

# 13. Function Return Types

You can also specify the return type:

```typescript
function add(
    a: number,
    b: number
): number {
    return a + b;
}
```

Structure:

```text
function add(
    a: number,
    b: number
): number
   ▲
   │
Return Type
```

---

# 14. Type Safety

Type safety means using type information to prevent invalid operations.

Example:

```typescript
function calculateDiscount(
    price: number,
    discount: number
): number {
    return (
        price -
        discount
    );
}
```

TypeScript prevents calls such as:

```typescript
calculateDiscount(
    "hello",
    true
);
```

because the arguments do not match the declared contract.

---

# 15. TypeScript as a Contract

Consider:

```typescript
function createUser(
    name: string,
    age: number
): string {
    return `${name} - ${age}`;
}
```

The function contract is:

```text
INPUT

name
→ string

age
→ number


OUTPUT

string
```

Anyone using the function can understand what it expects without reading the implementation.

---

# 16. TypeScript Errors Are Development Errors

Suppose:

```typescript
const age: number =
    "Twenty Five";
```

Your editor may immediately highlight this.

The TypeScript compiler also reports the problem.

This provides feedback during development instead of waiting for the incorrect value to cause problems during execution.

---

# 17. Why TypeScript Is Useful

Consider a JavaScript function:

```javascript
function processUser(user) {
    return user.profile.name;
}
```

What exactly is `user`?

Does it contain:

```text
profile?

name?

Is profile optional?

Is name a string?

Can user be null?
```

You may need to inspect other code to determine this.

With TypeScript:

```typescript
interface User {
    profile: {
        name: string;
    };
}

function processUser(
    user: User
): string {
    return user.profile.name;
}
```

The expected structure is explicit.

---

# 18. TypeScript Improves Editor Tooling

Because TypeScript understands types, editors can provide better:

```text
Autocomplete

Property suggestions

Function signatures

Parameter information

Refactoring

Navigation

Error detection

Documentation hints
```

Example:

```typescript
const username: string =
    "Alice";

username.
```

The editor knows that `username` is a string and can suggest string methods such as:

```text
toUpperCase()

toLowerCase()

includes()

slice()

trim()

replace()
```

---

# 19. TypeScript Helps With Refactoring

Suppose:

```typescript
interface User {
    username: string;
}
```

Your application contains:

```typescript
function greet(
    user: User
) {
    return user.username;
}
```

If the structure changes to:

```typescript
interface User {
    displayName: string;
}
```

TypeScript can identify code still expecting:

```typescript
user.username
```

This makes large refactors much safer.

---

# 20. TypeScript Is Especially Useful in Large Applications

As applications grow:

```text
10 files
   ↓
100 files
   ↓
500 files
   ↓
1000+ files
```

it becomes difficult to remember:

```text
What does this function accept?

What does this API return?

Can this value be null?

What properties exist?

What type does this callback receive?

What does this component expect?
```

TypeScript makes many of these contracts explicit.

---

# 21. `.ts` Files

Normal TypeScript files use:

```text
.ts
```

Example:

```text
app.ts
user.ts
api.ts
utils.ts
```

JavaScript uses:

```text
.js
```

TypeScript uses:

```text
.ts
```

---

# 22. `.tsx` Files

When TypeScript contains JSX, the file commonly uses:

```text
.tsx
```

Example:

```text
Button.tsx
App.tsx
Dashboard.tsx
```

This will become important when we move into React.

For now:

```text
.ts
→ TypeScript


.tsx
→ TypeScript + JSX
```

---

# 23. TypeScript Compiler

The TypeScript compiler is commonly called:

```text
tsc
```

It can:

```text
Read TypeScript
      │
      ▼
Type Check
      │
      ▼
Transform
      │
      ▼
JavaScript
```

---

# 24. Installing TypeScript

For a project:

```bash
npm install --save-dev typescript
```

This installs TypeScript as a development dependency.

Then you can run the compiler using:

```bash
npx tsc
```

---

# 25. Why Install TypeScript as a Dev Dependency?

TypeScript is generally needed while:

```text
Developing

Type checking

Building
```

The resulting application runs JavaScript.

Therefore TypeScript itself usually does not need to be part of the runtime application dependencies.

---

# 26. Check TypeScript Version

```bash
npx tsc --version
```

Example output:

```text
Version X.Y.Z
```

The exact version depends on the version installed in the project.

---

# 27. First TypeScript File

Create:

```text
index.ts
```

Add:

```typescript
const message: string =
    "Hello TypeScript";

console.log(message);
```

Compile:

```bash
npx tsc index.ts
```

The compiler can produce:

```text
index.js
```

containing JavaScript.

---

# 28. Compilation Flow

```text
index.ts
   │
   ▼
TypeScript Compiler
   │
   ├── Type Checking
   │
   └── Transformation
          │
          ▼
       index.js
          │
          ▼
   JavaScript Runtime
```

---

# 29. TypeScript Compiler Does More Than Remove Types

A simplified explanation is:

```text
TypeScript
→ Remove types
→ JavaScript
```

But `tsc` can also transform syntax depending on your configured JavaScript target.

For example, newer JavaScript syntax may be emitted differently for older targets.

So compilation involves:

```text
Type Checking
+
Type Erasure
+
Possible JavaScript Syntax Transformation
```

---

# 30. Initialize a TypeScript Project

Run:

```bash
npx tsc --init
```

This creates:

```text
tsconfig.json
```

This file configures the TypeScript project.

---

# 31. `tsconfig.json`

A basic configuration might look like:

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "strict": true
    }
}
```

The exact configuration depends on the environment and build tooling.

---

# 32. Purpose of `tsconfig.json`

`tsconfig.json` tells TypeScript things such as:

```text
Which files belong
to the project?

How strict should
type checking be?

Which JavaScript version
should be targeted?

Which module system
should be emitted?

Where should output go?

Which libraries
are available?
```

Conceptually:

```text
TypeScript Source
       │
       ▼
  tsconfig.json
       │
       ▼
       tsc
       │
       ▼
JavaScript / Type Errors
```

---

# 33. `compilerOptions`

Most TypeScript settings live under:

```json
{
    "compilerOptions": {}
}
```

Example:

```json
{
    "compilerOptions": {
        "strict": true,
        "target": "ES2022"
    }
}
```

We will cover the important configuration options in the dedicated TypeScript configuration topic.

---

# 34. `target`

Example:

```json
{
    "compilerOptions": {
        "target": "ES2022"
    }
}
```

`target` controls the JavaScript language version TypeScript emits.

Conceptually:

```text
TypeScript
    │
    ▼
target
    │
    ▼
JavaScript Version
```

Possible targets depend on the TypeScript version.

---

# 35. `module`

Example:

```json
{
    "compilerOptions": {
        "module": "ESNext"
    }
}
```

This controls module-related code generation and behavior.

Different environments may use configurations appropriate for:

```text
Browser applications

Node.js

Bundlers

Libraries
```

We will cover module-related TypeScript differences later.

---

# 36. `strict`

One of the most important options:

```json
{
    "compilerOptions": {
        "strict": true
    }
}
```

This enables a collection of strict type-checking behaviors.

For serious TypeScript projects, strict mode is generally the right starting point.

```text
strict: true

        │
        ▼

Stronger Type Checking
        │
        ▼
More Errors Found
During Development
```

---

# 37. Why Strict Mode Matters

Without strong type checking, you can accidentally weaken many of TypeScript's benefits.

The goal is not:

```text
Make TypeScript stop complaining
```

The goal is:

```text
Describe the program accurately
enough that TypeScript can help us.
```

---

# 38. `noEmit`

Sometimes TypeScript is used only for type checking while another tool handles transformation/building.

Example:

```json
{
    "compilerOptions": {
        "noEmit": true
    }
}
```

Then TypeScript checks the project without generating JavaScript output.

This pattern is common with modern frontend tooling.

---

# 39. Type Checking Without Emitting JavaScript

Run:

```bash
npx tsc --noEmit
```

Conceptually:

```text
.ts / .tsx
    │
    ▼
TypeScript
    │
    ▼
Check Types
    │
    ├── Errors
    │
    └── Success

No JS emitted
```

This command is useful in:

```text
Development

CI/CD

Pull-request checks

Build validation
```

---

# 40. TypeScript Compiler Errors

Example:

```typescript
const age: number =
    "25";
```

Running:

```bash
npx tsc
```

will report a type error.

This allows TypeScript checking to be part of automated pipelines.

Example:

```text
Developer
    │
    ▼
Push Code
    │
    ▼
CI Pipeline
    │
    ▼
npx tsc --noEmit
    │
    ├── Errors → Fail
    │
    └── Success → Continue
```

---

# 41. Type Inference

You do not need to explicitly annotate every value.

Example:

```typescript
let username =
    "Alice";
```

TypeScript can infer:

```text
username
→ string
```

Therefore:

```typescript
username = 100;
```

produces a type error.

Even though we never wrote:

```typescript
let username: string;
```

---

# 42. Type Annotation vs Type Inference

Explicit:

```typescript
const age: number =
    25;
```

Inferred:

```typescript
const age =
    25;
```

TypeScript can understand both.

This is important because good TypeScript does **not** mean writing type annotations everywhere.

---

# 43. Avoid Unnecessary Type Annotations

This:

```typescript
const name: string =
    "Alice";

const age: number =
    25;

const active: boolean =
    true;
```

is valid.

But TypeScript already understands:

```typescript
const name =
    "Alice";

const age =
    25;

const active =
    true;
```

Explicit types are most useful where they improve contracts, clarity, or safety.

---

# 44. TypeScript Is Structurally Typed

One important characteristic of TypeScript is that it primarily cares about the **structure** of values.

Suppose:

```typescript
interface User {
    name: string;
}
```

And:

```typescript
const employee = {
    name: "Alice",
    employeeId: 1001
};
```

This can work:

```typescript
function greet(
    user: User
) {
    console.log(
        user.name
    );
}

greet(employee);
```

Why?

Because `employee` has the required structure:

```text
name: string
```

This is called **structural typing**.

---

# 45. Structural Typing Mental Model

TypeScript often asks:

```text
Does this value have
the required structure?
```

rather than:

```text
Was this object created
from this exact named type?
```

Example:

```text
Expected

{
    name: string
}


Received

{
    name: string,
    employeeId: number
}


Required structure exists
        │
        ▼
Compatible
```

This becomes important when working with:

```text
Interfaces

Type aliases

Objects

Functions

Generics

React props
```

---

# 46. TypeScript Is Gradually Typed

TypeScript allows JavaScript code to be migrated gradually.

You can start with:

```javascript
function add(a, b) {
    return a + b;
}
```

and gradually introduce types:

```typescript
function add(
    a: number,
    b: number
) {
    return a + b;
}
```

This makes it possible to migrate existing JavaScript applications incrementally instead of rewriting everything at once.

---

# 47. TypeScript Can Work with JavaScript Projects

TypeScript tooling can be introduced into existing JavaScript codebases.

Projects can potentially contain:

```text
.js

.ts

.tsx
```

depending on their configuration and migration strategy.

This is useful when moving a large JavaScript application toward TypeScript.

---

# 48. TypeScript Does Not Guarantee Correct Logic

This code is perfectly type-safe:

```typescript
function add(
    a: number,
    b: number
): number {
    return a - b;
}
```

TypeScript sees:

```text
number
+
number
→
number
```

The types are valid.

But the logic is wrong because a function named `add` is subtracting.

Therefore:

```text
Type Safety
≠
Logical Correctness
```

You still need:

```text
Testing

Code Review

Validation

Good Design

Correct Business Logic
```

---

# 49. TypeScript Does Not Replace Tests

Suppose:

```typescript
function calculateDiscount(
    price: number
): number {
    return price * 10;
}
```

The return type is still:

```text
number
```

So TypeScript is satisfied.

But perhaps the intended formula was:

```typescript
price * 0.1
```

TypeScript cannot determine business intent.

Therefore:

```text
TypeScript
+
Tests
+
Runtime Validation
+
Good Architecture
```

work together.

---

# 50. TypeScript Does Not Replace Runtime Validation

Suppose an API returns:

```json
{
    "id": "ABC",
    "age": "hello"
}
```

Your application expects:

```typescript
interface User {
    id: number;
    age: number;
}
```

TypeScript cannot force the external server to obey that interface.

Data entering your application from:

```text
APIs

Forms

Files

Databases

Environment variables

Local storage

User input

Message queues
```

may require runtime validation.

---

# 51. Trust Boundaries

A useful mental model:

```text
External World
      │
      ▼
Untrusted Data
      │
      ▼
Runtime Validation
      │
      ▼
Known Application Data
      │
      ▼
TypeScript Type System
```

TypeScript helps once your program has reliable knowledge about the data.

It does not magically validate external systems.

---

# 52. TypeScript and API Development

TypeScript becomes especially useful when describing structures such as:

```typescript
interface User {
    id: number;
    username: string;
    email: string;
}
```

Functions can then clearly express:

```typescript
function getUsername(
    user: User
): string {
    return user.username;
}
```

The type system documents and checks the expected data contract.

---

# 53. TypeScript and React

JavaScript React component:

```jsx
function UserCard({
    user
}) {
    return (
        <h1>
            {user.name}
        </h1>
    );
}
```

What exactly is `user`?

TypeScript can describe it:

```tsx
interface User {
    id: number;
    name: string;
}

interface UserCardProps {
    user: User;
}

function UserCard({
    user
}: UserCardProps) {
    return (
        <h1>
            {user.name}
        </h1>
    );
}
```

Now the component contract is explicit.

We will cover React typing after learning React itself.

---

# 54. TypeScript Development Flow

A simplified development flow:

```text
Write TypeScript
      │
      ▼
Type Checker
      │
      ├── Error
      │     │
      │     ▼
      │   Fix Code
      │
      └── Valid
            │
            ▼
       Build Tool
            │
            ▼
       JavaScript
            │
            ▼
          Runtime
```

---

# 55. TypeScript in Modern Frontend Projects

Modern frontend projects commonly use tools that understand TypeScript syntax.

For example, a project may use:

```text
TypeScript
    │
    ├── Type Checking
    │
    ▼
Bundler / Build Tool
    │
    ▼
Browser JavaScript
```

The tool performing JavaScript transformation is not necessarily the same tool performing TypeScript type checking.

This is why commands such as:

```bash
npx tsc --noEmit
```

are useful.

---

# 56. TypeScript Is Not a Runtime Dependency by Definition

Consider:

```typescript
interface User {
    name: string;
}

const user: User = {
    name: "Alice"
};
```

After compilation:

```javascript
const user = {
    name: "Alice"
};
```

The interface disappears.

The runtime application does not need the `User` interface because it only existed for development and type checking.

---

# 57. Types vs Values

This distinction becomes extremely important later.

Some things exist only in the **type system**:

```typescript
interface User {
    name: string;
}

type UserId =
    string | number;
```

Others exist at runtime:

```typescript
const user = {
    name: "Alice"
};

class UserService {}
```

Conceptually:

```text
TYPE SPACE
================

interface

type

generic types

utility types


VALUE SPACE
================

variables

functions

objects

classes

runtime values
```

Some constructs, such as classes, participate in both contexts in different ways.

We will explore this later.

---

# 58. TypeScript Compiler vs JavaScript Engine

Do not confuse these.

## TypeScript Compiler

```text
tsc
```

performs tasks such as:

```text
Type Checking

Type Erasure

Code Transformation

JavaScript Emission
```

---

## JavaScript Engine

Examples include engines used by browsers and runtimes.

They execute:

```text
JavaScript
```

Conceptually:

```text
TypeScript
    │
    ▼
TypeScript Tooling
    │
    ▼
JavaScript
    │
    ▼
JavaScript Engine
    │
    ▼
Program Runs
```

---

# 59. TypeScript Errors vs Runtime Errors

## Type Error

```typescript
const age: number =
    "Alice";
```

Detected by TypeScript.

---

## Runtime Error

```typescript
throw new Error(
    "Something failed"
);
```

Occurs while the JavaScript program executes.

---

## Logic Error

```typescript
function multiply(
    a: number,
    b: number
): number {
    return a + b;
}
```

Types are valid.

Logic is wrong.

Therefore:

```text
Type Errors
Runtime Errors
Logic Errors
```

are different categories.

---

# 60. TypeScript's Goal

TypeScript's goal is not:

```text
Make JavaScript complicated.
```

The goal is closer to:

```text
Understand JavaScript code
before it executes.
```

By knowing:

```text
Types

Object shapes

Function contracts

Possible values

Relationships between types
```

TypeScript can detect many mistakes early.

---

# 61. TypeScript Does Not Mean Writing Types Everywhere

Bad mental model:

```typescript
const username: string =
    "Alice";

const age: number =
    25;

const active: boolean =
    true;

const score: number =
    100;
```

This is valid but often unnecessary.

Better:

```typescript
const username =
    "Alice";

const age =
    25;

const active =
    true;

const score =
    100;
```

TypeScript already knows these types.

Use explicit annotations where they add useful information.

---

# 62. TypeScript Should Describe Reality

Avoid forcing TypeScript to accept incorrect assumptions.

For example, do not think:

```text
TypeScript gives error
      │
      ▼
How do I silence it?
```

Instead think:

```text
TypeScript gives error
      │
      ▼
Why does the type system
believe this is unsafe?
      │
      ▼
Is my type wrong?
Is my code wrong?
Is the data uncertain?
```

This mindset is important when learning TypeScript.

---

# 63. TypeScript Is About Modeling Possibilities

Suppose:

```typescript
let username:
    string | null;
```

This says:

```text
username can be:

string

OR

null
```

Your code must then handle those possibilities safely.

TypeScript is essentially helping us model:

```text
What values can exist here?
```

and:

```text
What operations are safe
for every possible value?
```

---

# 64. TypeScript Development Mental Model

Instead of thinking:

```text
TypeScript =
JavaScript with : string
```

think:

```text
TypeScript
       │
       ▼
Model the possible values
       │
       ▼
Check operations against
those possibilities
       │
       ▼
Catch invalid assumptions
before runtime
```

This becomes increasingly important when we reach:

```text
Union Types

Narrowing

Generics

Conditional Types

Utility Types
```

---

# 65. Common Mistake — Annotating Everything

Unnecessary:

```typescript
const username: string =
    "Alice";
```

Often:

```typescript
const username =
    "Alice";
```

is enough.

Use TypeScript's inference system.

---

# 66. Common Mistake — Thinking Types Exist at Runtime

Wrong idea:

```typescript
interface User {
    name: string;
}
```

Then trying conceptually to check:

```text
Is runtime object a User interface?
```

Interfaces disappear after compilation.

Runtime checks require runtime constructs or validation logic.

---

# 67. Common Mistake — Trusting API Types

Writing:

```typescript
const user: User =
    await getUser();
```

does not magically guarantee the external response matches `User`.

TypeScript checks your program's assumptions.

It does not control external systems.

---

# 68. Common Mistake — Using TypeScript Instead of Validation

Suppose a user enters:

```text
abc
```

into an age field.

Declaring:

```typescript
age: number
```

does not convert or validate that input automatically.

Runtime input still needs appropriate:

```text
Parsing

Validation

Error handling
```

---

# 69. Common Mistake — Assuming TypeScript Prevents Every Bug

TypeScript can catch:

```text
Wrong argument types

Missing properties

Invalid property access

Possible null values

Incorrect return types

Many unsafe assumptions
```

But it cannot automatically detect:

```text
Wrong business rules

Incorrect algorithms

Server failures

Network failures

Bad external data

Security vulnerabilities

Performance problems

Every runtime exception
```

---

# 70. Common Mistake — Fighting the Compiler

Suppose TypeScript says:

```text
Object is possibly undefined.
```

Do not immediately force the compiler to accept the code.

First determine:

```text
Can the value actually
be undefined?
```

If yes:

```text
Handle it.
```

If no:

```text
Improve the type/model
so TypeScript understands why.
```

---

# 71. Common Mistake — Treating TypeScript as Java/C#

TypeScript adds static typing, but the runtime remains JavaScript.

JavaScript concepts still matter:

```text
Prototype chain

Closures

this

Event loop

Promises

Objects

Reference behavior

Mutation

Modules
```

TypeScript does not replace JavaScript's runtime model.

---

# 72. Interview Question — What Is TypeScript?

TypeScript is a statically typed superset of JavaScript that adds compile-time type checking and tooling while ultimately producing JavaScript for execution.

---

# 73. Interview Question — Is TypeScript a Superset of JavaScript?

Yes.

TypeScript builds on JavaScript and adds features primarily around static typing and developer tooling.

---

# 74. Interview Question — Does the Browser Execute TypeScript?

Browsers execute JavaScript.

TypeScript type syntax must be removed/transformed before browser execution.

---

# 75. Interview Question — What Is Static Typing?

Static typing means types can be checked before the program executes.

Example:

```typescript
let age: number =
    25;

age = "hello";
```

TypeScript detects the invalid assignment during type checking.

---

# 76. Interview Question — JavaScript vs TypeScript?

JavaScript:

```text
Dynamic runtime language
```

TypeScript:

```text
JavaScript
+
Static type system
+
Compile-time checking
+
Additional tooling
```

The runtime behavior ultimately comes from JavaScript.

---

# 77. Interview Question — What Is Type Erasure?

Type erasure means TypeScript-specific type information is removed before the resulting JavaScript executes.

Example:

```typescript
const age: number =
    25;
```

becomes conceptually:

```javascript
const age =
    25;
```

---

# 78. Interview Question — Does TypeScript Validate API Responses?

No.

TypeScript's static types do not validate runtime network data.

External data may require runtime validation.

---

# 79. Interview Question — What Is `tsc`?

`tsc` is the TypeScript compiler.

It can:

```text
Type check TypeScript

Transform code

Remove type syntax

Emit JavaScript

Generate supporting outputs
depending on configuration
```

---

# 80. Interview Question — What Is `tsconfig.json`?

`tsconfig.json` defines the configuration of a TypeScript project.

It controls things such as:

```text
Compiler options

Type checking behavior

Target

Modules

Included files

Excluded files

Output behavior
```

---

# 81. Interview Question — What Does `strict` Do?

```json
{
    "compilerOptions": {
        "strict": true
    }
}
```

enables TypeScript's strict family of type-checking options, providing stronger safety guarantees.

---

# 82. Interview Question — What Is Type Inference?

Type inference allows TypeScript to determine a type automatically from the code.

```typescript
const age = 25;
```

TypeScript understands that `age` is numeric without requiring:

```typescript
const age: number =
    25;
```

---

# 83. Interview Question — What Is Structural Typing?

Structural typing determines compatibility primarily based on the shape of a value rather than requiring the same explicit named type.

Example:

```typescript
interface Person {
    name: string;
}

const employee = {
    name: "Alice",
    id: 1
};

function greet(
    person: Person
) {}

greet(employee);
```

`employee` satisfies the required structure.

---

# 84. Interview Question — Does TypeScript Replace Testing?

No.

TypeScript checks type correctness.

Tests verify behavior and business expectations.

Both solve different problems.

---

# 85. Interview Question — Does TypeScript Exist at Runtime?

Most TypeScript type information does not.

Types are removed before JavaScript execution.

Some TypeScript/JavaScript constructs can generate or correspond to runtime JavaScript, but the static type system itself does not remain available as runtime validation.

---

# 86. Interview Question — `.ts` vs `.tsx`

```text
.ts
→ TypeScript source


.tsx
→ TypeScript source containing JSX
```

`.tsx` is commonly used for React components written with TypeScript.

---

# 87. Interview Question — Why Use TypeScript?

Common reasons include:

```text
Earlier error detection

Safer refactoring

Better autocomplete

Explicit contracts

Better maintainability

Improved navigation

Safer large codebases

Better collaboration
```

---

# 88. TypeScript Fundamentals Cheat Sheet

```text
TYPESCRIPT
================================

JavaScript
+
Static Type System
+
Compile-Time Checking
+
Developer Tooling


FILE
================================

.ts

TypeScript


.tsx

TypeScript + JSX


ANNOTATION
================================

let name: string;


FUNCTION
================================

function add(
    a: number,
    b: number
): number {
    return a + b;
}


INFERENCE
================================

const age = 25;

TypeScript infers
the type.


TYPE CHECKING
================================

TypeScript checks types
before runtime.


TYPE ERASURE
================================

: string
: number
interfaces
type aliases

etc.

do not become runtime
type validation.


COMPILATION
================================

TypeScript
    │
    ▼
Type Checking
    │
    ▼
Transformation
    │
    ▼
JavaScript


TSC
================================

npx tsc


CHECK ONLY
================================

npx tsc --noEmit


CONFIG
================================

tsconfig.json


INITIALIZE
================================

npx tsc --init


STRICT MODE
================================

{
    "compilerOptions": {
        "strict": true
    }
}


INSTALL
================================

npm install --save-dev typescript


STRUCTURAL TYPING
================================

Compatibility depends
primarily on structure.


RUNTIME DATA
================================

TypeScript types
do NOT automatically
validate runtime data.


IMPORTANT
================================

Type Safety
≠
Runtime Validation

Type Safety
≠
Correct Logic

TypeScript
≠
Replacement for Tests
```

---

# 89. Important Rules to Remember

```text
1. TypeScript is built on top of JavaScript.

2. TypeScript is a superset of JavaScript.

3. JavaScript runtime behavior still applies.

4. TypeScript adds static type checking.

5. TypeScript catches many errors before runtime.

6. TypeScript does not eliminate every runtime error.

7. TypeScript does not guarantee correct business logic.

8. TypeScript does not replace testing.

9. TypeScript does not automatically validate external data.

10. TypeScript types generally disappear before runtime.

11. Type removal is called type erasure.

12. Browsers execute JavaScript, not TypeScript type syntax.

13. Node.js ultimately executes JavaScript runtime semantics.

14. TypeScript source commonly uses .ts.

15. TypeScript with JSX commonly uses .tsx.

16. tsc is the TypeScript compiler.

17. TypeScript can be installed as a development dependency.

18. npx tsc can run the project compiler.

19. npx tsc --noEmit performs type checking without JavaScript output.

20. tsconfig.json configures a TypeScript project.

21. compilerOptions contains compiler configuration.

22. target controls emitted JavaScript language level.

23. module controls module-related compilation behavior.

24. strict enables stronger type checking.

25. Strong type checking is usually desirable.

26. Type annotations explicitly describe expected types.

27. Type inference lets TypeScript infer types automatically.

28. Do not annotate every obvious value unnecessarily.

29. Function parameters can have types.

30. Function return values can have types.

31. Types act as development-time contracts.

32. TypeScript improves autocomplete.

33. TypeScript improves refactoring.

34. TypeScript improves navigation.

35. TypeScript improves maintainability.

36. TypeScript becomes especially useful in large codebases.

37. TypeScript is structurally typed.

38. Structural typing primarily compares value shapes.

39. Compatible values do not always need the same explicit named type.

40. TypeScript supports gradual adoption.

41. JavaScript projects can migrate incrementally.

42. Static type checking happens before execution.

43. Runtime errors happen while JavaScript executes.

44. Logic errors can still exist in perfectly typed code.

45. External API data should not be blindly trusted because of a TypeScript type.

46. Forms contain runtime data.

47. Files contain runtime data.

48. Environment variables contain runtime data.

49. Network responses contain runtime data.

50. Runtime data may require validation.

51. TypeScript interfaces do not automatically exist at runtime.

52. Type aliases do not automatically exist at runtime.

53. Type space and value space are different concepts.

54. TypeScript should model what values can actually exist.

55. Do not use types merely to silence compiler errors.

56. Compiler errors often reveal unsafe assumptions.

57. TypeScript does not replace JavaScript knowledge.

58. Closures still behave like JavaScript closures.

59. Promises still behave like JavaScript promises.

60. Objects still use JavaScript reference semantics.

61. The event loop is still JavaScript runtime behavior.

62. TypeScript does not change how JavaScript operators work.

63. TypeScript does not change how loops fundamentally work.

64. TypeScript does not change JavaScript scope rules.

65. TypeScript does not change JavaScript closure behavior.

66. TypeScript does not change JavaScript async runtime behavior.

67. TypeScript primarily checks whether those operations are type-safe.

68. Modern build tools may transform TypeScript separately from type checking.

69. tsc --noEmit is useful for CI type checking.

70. TypeScript can make application contracts explicit.

71. Explicit contracts improve collaboration.

72. Explicit contracts help understand unfamiliar code.

73. Type inference is a core part of TypeScript.

74. Good TypeScript balances inference and explicit annotations.

75. strict mode should generally be enabled for new projects.

76. TypeScript should describe reality rather than hide uncertainty.

77. If a value can be null, model that possibility.

78. If data is unknown, model that uncertainty safely.

79. TypeScript helps identify invalid assumptions.

80. Think of TypeScript as a static analysis layer over JavaScript.
```

---

# 90. TypeScript in One Sentence

> TypeScript is JavaScript with a static type system that checks assumptions about values and program structure before the resulting JavaScript executes.

---

# Final Mental Model

```text
                    TYPESCRIPT

JavaScript Knowledge
        │
        ▼
┌──────────────────────────┐
│                          │
│      Type System         │
│                          │
│  What values can exist?  │
│                          │
│  What operations are     │
│  safe on those values?   │
│                          │
└────────────┬─────────────┘
             │
             ▼
        Type Checker
             │
       ┌─────┴─────┐
       │           │
       ▼           ▼
     Error       Valid
                   │
                   ▼
              JavaScript
                   │
                   ▼
                 Runtime
```

The key distinction:

```text
JavaScript
================================

What happens when
the program runs?


TypeScript
================================

What can we determine
about the program
before it runs?
```

That distinction is the foundation for everything else in TypeScript.

---
