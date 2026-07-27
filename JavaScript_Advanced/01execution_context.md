# Execution Context

An **Execution Context** is the environment JavaScript creates to **prepare and execute code**.

Whenever JavaScript runs:

* the main script/module, or
* a function

JavaScript needs an environment containing the information required to execute that code.

That environment is called an:

```text
Execution Context
```

A useful mental model is:

```text
Execution Context
      ↓
Environment in which JavaScript code executes
      ↓
Tracks things such as
variables
functions
scope relationships
this
```

---

# 1. Why Execution Context Exists

Consider:

```javascript
const name = "John";

function greet() {
    const message = "Hello";

    console.log(message, name);
}

greet();
```

JavaScript must keep track of:

```text
name
greet
message
which scope is active
where outer variables can be found
what `this` is
what code is currently executing
```

Execution contexts provide the runtime environments needed to manage this information.

---

# 2. Types of Execution Context

The most important execution contexts are:

```text
1. Global Execution Context

2. Function Execution Context
```

You may also encounter:

```text
Eval Execution Context
```

when `eval()` executes JavaScript code, but `eval()` is generally avoided in normal application development.

Modules also have their own module-level environment and semantics, but for understanding the core execution model, start with:

```text
Global
Function
```

---

# 3. Global Execution Context

When a JavaScript script begins running, JavaScript creates a:

```text
Global Execution Context
```

often abbreviated:

```text
GEC
```

Example:

```javascript
const name = "John";
const age = 25;

function greet() {
    console.log("Hello");
}
```

These top-level declarations are handled within the environment associated with the global code.

Conceptually:

```text
Global Execution Context
│
├── name
├── age
└── greet
```

The global execution context is the starting execution context for a classic script.

---

# 4. Only One Global Execution Context

For a given script execution environment, there is one global execution context representing the execution of global code.

Functions then create additional function execution contexts.

Example:

```javascript
function first() {
}

function second() {
}

first();
second();
```

Conceptually:

```text
Global Execution Context
        │
        ├── first() called
        │       ↓
        │ Function Execution Context
        │
        └── second() called
                ↓
          Function Execution Context
```

The function contexts are created as those functions are invoked.

---

# 5. Function Execution Context

Whenever a function is **called**, JavaScript creates a new:

```text
Function Execution Context
```

often abbreviated:

```text
FEC
```

Example:

```javascript
function greet(name) {
    const message = `Hello ${name}`;

    console.log(message);
}

greet("John");
```

When:

```javascript
greet("John");
```

runs, JavaScript creates a new execution context for that invocation of `greet`.

Conceptually:

```text
Function Execution Context — greet
│
├── name = "John"
├── message = "Hello John"
└── function body execution
```

---

# 6. Function Declaration Does Not Create a Function Execution Context

This:

```javascript
function greet() {
    console.log("Hello");
}
```

defines the function.

It does **not** create a function execution context for `greet` merely because the declaration exists.

The context is created when:

```javascript
greet();
```

is executed.

Remember:

```text
Function defined
      ↓
No function execution context yet

Function called
      ↓
New function execution context
```

---

# 7. Every Function Call Gets a New Context

Consider:

```javascript
function greet(name) {
    console.log(name);
}

greet("John");
greet("Alice");
greet("Bob");
```

Each call creates a separate execution context.

Conceptually:

```text
greet("John")
      ↓
FEC #1

greet("Alice")
      ↓
FEC #2

greet("Bob")
      ↓
FEC #3
```

These are separate function invocations.

---

# 8. Same Function, Different Contexts

Consider:

```javascript
function calculate(a, b) {
    const result = a + b;

    return result;
}

calculate(10, 20);
calculate(50, 100);
```

First call:

```text
Function Execution Context #1

a = 10
b = 20
result = 30
```

Second call:

```text
Function Execution Context #2

a = 50
b = 100
result = 150
```

Same function definition.

Different calls.

Different execution contexts.

---

# 9. Execution Context Lifecycle

A useful high-level model is that an execution context goes through:

```text
Creation / Setup
       ↓
Execution
       ↓
Completion
```

For learning purposes, these are often called:

```text
Creation Phase
Execution Phase
```

The exact ECMAScript specification model is more detailed, but this two-phase model is useful for understanding concepts such as hoisting.

---

# 10. Creation Phase

Before the statements of a context execute normally, JavaScript establishes the environment needed for that execution.

Conceptually, this involves setting up things such as:

```text
Lexical environment
Variable bindings
Function declarations
Outer environment reference
this binding where applicable
```

This setup is what helps explain:

```text
Hoisting
Scope
Closures
this
```

---

# 11. Execution Phase

After the required environment has been established, JavaScript executes the code.

Example:

```javascript
const x = 10;
const y = 20;

const result = x + y;

console.log(result);
```

During execution, statements are evaluated and bindings receive values according to JavaScript's declaration and initialization rules.

Conceptually:

```text
x → 10
y → 20

result
↓
10 + 20
↓
30
```

---

# 12. Execution Context and Hoisting

Consider:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Output:

```text
Hello
```

Why can `greet()` be called before the declaration appears textually?

Because the function declaration's binding is established during the setup/instantiation of the execution context before normal statement execution reaches:

```javascript
greet();
```

This behavior is part of what we describe as:

```text
Hoisting
```

So execution context helps explain **why hoisting happens**.

---

# 13. `var` During Setup

Consider:

```javascript
console.log(age);

var age = 25;
```

Output:

```text
undefined
```

Conceptually:

```text
Setup
──────────────

age → undefined


Execution
──────────────

console.log(age)
↓
undefined

age = 25
```

This is why `var` can be accessed before its assignment and produce:

```text
undefined
```

---

# 14. `let` and `const` During Setup

Consider:

```javascript
console.log(age);

let age = 25;
```

This throws:

```text
ReferenceError
```

`let` and `const` bindings are created as part of the lexical environment, but they are not initialized until execution reaches their declarations.

The period between entering the scope and initialization is called:

```text
Temporal Dead Zone
```

or:

```text
TDZ
```

So:

```text
var
↓
binding created and initialized to undefined

let / const
↓
binding created but initially uninitialized
↓
TDZ
↓
initialized when declaration executes
```

---

# 15. Function Declaration During Setup

Consider:

```javascript
sayHello();

function sayHello() {
    console.log("Hello");
}
```

Function declarations are instantiated so the function can be called before its textual declaration.

Conceptually:

```text
Setup
──────────────

sayHello
    ↓
function object


Execution
──────────────

sayHello()
    ↓
Hello
```

---

# 16. Function Expression with `var`

Consider:

```javascript
sayHello();

var sayHello = function () {
    console.log("Hello");
};
```

This fails with:

```text
TypeError
```

Why?

Conceptually, during setup:

```text
sayHello → undefined
```

Then execution reaches:

```javascript
sayHello();
```

which is effectively trying to call:

```javascript
undefined();
```

Therefore:

```text
TypeError
```

Later:

```javascript
sayHello = function () {
    console.log("Hello");
};
```

assigns the function object.

---

# 17. Function Expression with `let` or `const`

Consider:

```javascript
sayHello();

const sayHello = function () {
    console.log("Hello");
};
```

This throws:

```text
ReferenceError
```

because `sayHello` is still in the:

```text
Temporal Dead Zone
```

when the call is attempted.

---

# 18. Execution Context and Scope

Consider:

```javascript
const globalValue = 10;

function outer() {
    const outerValue = 20;

    function inner() {
        const innerValue = 30;

        console.log(
            globalValue,
            outerValue,
            innerValue
        );
    }

    inner();
}

outer();
```

`inner()` can access:

```text
innerValue
outerValue
globalValue
```

because lexical scope determines where identifiers can be resolved.

Conceptually:

```text
inner environment
      │
      ▼
outer environment
      │
      ▼
global environment
```

---

# 19. Lexical Environment

A **Lexical Environment** is a specification concept used to represent identifier bindings and their connection to an outer lexical environment.

Conceptually:

```text
Lexical Environment
│
├── Bindings
│
└── Outer Environment Reference
```

Example:

```javascript
const x = 10;

function test() {
    const y = 20;

    console.log(x, y);
}
```

Inside `test`, its lexical environment contains the local binding for:

```text
y
```

and is connected to the outer environment where:

```text
x
```

can be found.

---

# 20. Environment Record

A lexical environment conceptually contains an:

```text
Environment Record
```

The environment record stores bindings associated with that environment.

Example:

```javascript
function test(a) {
    const x = 10;
    let y = 20;
}
```

Conceptually:

```text
Environment Record

a → argument value
x → 10
y → 20
```

The specification has multiple types of environment records depending on the situation, so this should be treated as a mental model rather than a literal JavaScript object.

---

# 21. Outer Environment Reference

The lexical environment also links to an outer environment.

Example:

```javascript
const a = 10;

function outer() {
    const b = 20;

    function inner() {
        const c = 30;

        console.log(a, b, c);
    }

    inner();
}

outer();
```

Conceptually:

```text
inner
│
├── c
│
└── outer → outer environment
                │
                ├── b
                │
                └── outer → global environment
                                │
                                └── a
```

This forms the basis of the:

```text
Scope Chain
```

---

# 22. Identifier Lookup

Suppose:

```javascript
const name = "Global";

function outer() {
    const age = 25;

    function inner() {
        console.log(name);
    }

    inner();
}

outer();
```

When JavaScript needs:

```javascript
name
```

inside `inner`, conceptually it searches through the lexical environment chain:

```text
inner
↓
name?

No
↓

outer
↓
name?

No
↓

global
↓
name?

Yes
↓
"Global"
```

---

# 23. Variable Shadowing

Consider:

```javascript
const name = "Global";

function test() {
    const name = "Local";

    console.log(name);
}

test();
```

Output:

```text
Local
```

The local binding is found first.

Conceptually:

```text
test environment
│
└── name = "Local"
        ↑
        found here
```

JavaScript does not continue searching outward after finding the binding.

This is:

```text
Variable Shadowing
```

---

# 24. Execution Context and Closures

Consider:

```javascript
function outer() {
    let count = 0;

    return function inner() {
        count++;

        return count;
    };
}

const counter = outer();

console.log(counter());
console.log(counter());
```

Output:

```text
1
2
```

`outer()` finishes executing, but the returned `inner` function retains access to the lexical environment where it was created.

That is the foundation of:

```text
Closure
```

Conceptually:

```text
outer()
│
├── count = 0
│
└── creates inner()
        │
        ▼
inner retains access
to outer lexical environment
```

So execution context, lexical environments, and closures are closely related concepts.

---

# 25. Execution Context and `this`

An execution context also involves a `this` binding where applicable.

Example:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Output:

```text
John
```

For regular functions, the value of `this` depends on **how the function is invoked**.

This was covered in the `this` topic, but execution context is where the runtime's `this` binding is relevant.

Arrow functions differ because they do not create their own `this` binding; they use `this` lexically from the surrounding context.

---

# 26. Nested Function Calls

Consider:

```javascript
function first() {
    second();
}

function second() {
    third();
}

function third() {
    console.log("Hello");
}

first();
```

Execution contexts are created as functions are called:

```text
Global Context
      │
      ▼
first Context
      │
      ▼
second Context
      │
      ▼
third Context
```

But JavaScript needs a mechanism to track which execution context is currently active.

That mechanism is the:

```text
Call Stack
```

which is our next topic.

---

# 27. Execution Context Stack

The call stack stores active execution contexts.

For:

```javascript
function first() {
    second();
}

function second() {
    console.log("Second");
}

first();
```

Conceptually:

```text
Start

┌─────────────┐
│   Global    │
└─────────────┘
```

When `first()` runs:

```text
┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

When `second()` runs:

```text
┌─────────────┐
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

After `second()` finishes:

```text
┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

After `first()` finishes:

```text
┌─────────────┐
│   Global    │
└─────────────┘
```

This is why execution context and the call stack should be learned together.

---

# 28. Execution Context Is Not the Call Stack

Do not confuse:

```text
Execution Context
```

with:

```text
Call Stack
```

An execution context is:

```text
The runtime environment associated
with executing some code.
```

The call stack is:

```text
The stack used to track active
execution contexts/function calls.
```

Think:

```text
Execution Context
      ↓
WHAT environment is executing?

Call Stack
      ↓
WHICH execution context is active,
and which one should resume afterward?
```

---

# 29. Example — Complete Execution Flow

Consider:

```javascript
const x = 10;

function add(a, b) {
    const result = a + b;

    return result;
}

const total = add(x, 20);

console.log(total);
```

Conceptually:

```text
1. Global execution begins

2. Global environment is established

   x
   add
   total

3. Execution proceeds

   x = 10

4. add(x, 20) is called

5. New function execution context created

   a = 10
   b = 20

6. Function executes

   result = 30

7. Function returns 30

8. Function context completes

9. Global execution resumes

10. total = 30

11. console.log(total)

12. Output → 30
```

---

# 30. Example — Nested Execution

```javascript
const x = 10;

function outer() {
    const y = 20;

    function inner() {
        const z = 30;

        return x + y + z;
    }

    return inner();
}

console.log(outer());
```

Execution flow:

```text
Global
│
├── x = 10
│
└── outer()
      │
      ▼
   outer Context
      │
      ├── y = 20
      │
      └── inner()
             │
             ▼
          inner Context
             │
             └── z = 30
```

Inside `inner`:

```text
z
↓
local environment

y
↓
outer environment

x
↓
global environment
```

Result:

```text
60
```

---

# 31. Execution Context and Recursion

Consider:

```javascript
function countdown(n) {
    if (n === 0) {
        return;
    }

    console.log(n);

    countdown(n - 1);
}

countdown(3);
```

Calls:

```text
countdown(3)
      ↓
countdown(2)
      ↓
countdown(1)
      ↓
countdown(0)
```

Each invocation gets its **own execution context**.

Conceptually:

```text
countdown(0)
countdown(1)
countdown(2)
countdown(3)
Global
```

This is why recursive calls can maintain different values of:

```text
n
```

at the same time.

---

# 32. Execution Context and Parameters

Example:

```javascript
function add(a, b) {
    return a + b;
}

add(10, 20);
```

The function execution environment contains parameter bindings:

```text
a → 10
b → 20
```

Another call:

```javascript
add(50, 100);
```

gets a new environment:

```text
a → 50
b → 100
```

Parameters are local to each function invocation.

---

# 33. Execution Context and Local Variables

```javascript
function test() {
    const x = 10;
}
```

`x` belongs to the lexical environment associated with that function execution/scope.

Outside:

```javascript
console.log(x);
```

does not find that local binding.

When the function finishes, its execution context is removed from the call stack.

That does **not** necessarily mean every associated value is immediately destroyed.

If something remains reachable through a closure, the required lexical environment can remain reachable.

---

# 34. Execution Context vs Lexical Environment

These terms are related but not identical.

```text
Execution Context
│
├── Represents an active execution state
│
├── Contains/references environment information
│
└── Includes other execution-related state
```

A:

```text
Lexical Environment
```

specifically deals with:

```text
Identifier bindings
+
Outer lexical environment relationship
```

So do not treat them as exact synonyms.

---

# 35. Execution Context vs Scope

Scope answers:

```text
Where can this identifier be accessed?
```

Execution context answers more broadly:

```text
What runtime state/environment is associated
with the code currently being executed?
```

Example:

```javascript
function outer() {
    const x = 10;

    function inner() {
        console.log(x);
    }

    inner();
}
```

Lexical scope explains why `inner` can access `x`.

Execution contexts represent the active executions of:

```text
outer()
inner()
```

during runtime.

---

# 36. Execution Context vs Closure

Execution Context:

```text
Runtime state associated with
executing code/function invocation
```

Closure:

```text
A function together with access
to the lexical environment in
which it was created
```

Example:

```javascript
function outer() {
    const x = 10;

    return function inner() {
        return x;
    };
}

const fn = outer();
```

The execution of `outer()` finishes.

But:

```javascript
fn()
```

can still access:

```text
x
```

because of closure.

---

# 37. Execution Context vs Call Stack

```text
EXECUTION CONTEXT
===========================

Contains/references runtime state
needed for code execution.


CALL STACK
===========================

Tracks active execution contexts
in stack order.
```

Conceptually:

```text
Call Stack

┌──────────────────┐
│ Execution Context│
├──────────────────┤
│ Execution Context│
├──────────────────┤
│ Execution Context│
└──────────────────┘
```

---

# 38. Important Terminology

You should recognize:

```text
GEC
↓
Global Execution Context


FEC
↓
Function Execution Context


Lexical Environment
↓
Bindings + outer environment relationship


Environment Record
↓
Stores bindings for an environment


Scope Chain
↓
Lexical chain used for identifier lookup


Call Stack
↓
Tracks active execution contexts
```

---

# 39. Common Mistake — Function Definition Creates Context

Incorrect:

```text
A function execution context is
created when a function is defined.
```

Correct:

```text
A function execution context is
created when the function is invoked.
```

Example:

```javascript
function test() {
}
```

No `test` execution context yet.

Then:

```javascript
test();
```

Now one is created.

---

# 40. Common Mistake — Hoisting Means Code Moves

You may hear:

```text
JavaScript moves declarations
to the top of the file.
```

This is only a simplified teaching metaphor.

JavaScript does not literally rewrite:

```javascript
console.log(x);

var x = 10;
```

into:

```javascript
var x;

console.log(x);

x = 10;
```

Instead, declaration instantiation/environment setup before statement execution produces behavior that appears similar.

---

# 41. Common Mistake — `let` and `const` Are Not Hoisted

Incorrect:

```text
let and const are not hoisted.
```

More accurate:

```text
Their bindings are created before
their declarations execute,
but remain uninitialized until
the declaration is evaluated.
```

Therefore accessing them during the TDZ produces:

```text
ReferenceError
```

---

# 42. Common Mistake — Context Ends Means Everything Is Deleted

Suppose:

```javascript
function outer() {
    const value = 100;

    return function () {
        return value;
    };
}

const fn = outer();
```

`outer()` has completed.

Its execution context is no longer on the call stack.

But:

```javascript
fn();
```

still returns:

```text
100
```

because the required lexical environment remains reachable through the closure.

So:

```text
Execution context removed from stack
```

does not mean:

```text
Every associated value immediately disappears.
```

---

# 43. Common Mistake — Execution Context Equals Scope

They are related but different.

```text
Scope
↓
Identifier accessibility rules

Execution Context
↓
Runtime execution state/environment
```

Scope is largely determined lexically by where code is written.

Execution contexts are created as code/functions execute.

---

# 44. Common Mistake — One Function Has One Context Forever

Incorrect:

```text
Function
↓
One execution context
```

Correct:

```text
Each invocation
↓
New function execution context
```

Example:

```javascript
test();
test();
test();
```

produces three separate function invocations and therefore three function execution contexts over time.

---

# 45. Interview Question — What Is an Execution Context?

**Answer:**

An execution context is the runtime structure/environment JavaScript uses to manage the state required while executing global code or a function, including lexical environments, bindings, `this` information where applicable, and other execution state.

---

# 46. Interview Question — What Are the Main Types?

The main types are:

```text
Global Execution Context
Function Execution Context
```

You may also encounter execution associated with:

```text
eval()
```

but it is uncommon in normal application development.

---

# 47. Interview Question — When Is a Function Execution Context Created?

When the function is:

```text
Invoked / Called
```

Not merely when it is defined.

---

# 48. Interview Question — Does Every Function Call Get a New Context?

Yes.

```javascript
function test(x) {
    return x;
}

test(1);
test(2);
test(3);
```

Each call gets its own function execution context.

---

# 49. Interview Question — What Is a Lexical Environment?

A lexical environment is a specification structure containing:

```text
An environment record
+
A reference to an outer lexical environment
```

It helps JavaScript resolve identifiers according to lexical scope.

---

# 50. Interview Question — What Is an Environment Record?

It is the part of an environment that records identifier bindings.

Conceptually:

```text
name → "John"
age → 25
greet → function
```

Different kinds of environment records exist in the JavaScript specification.

---

# 51. Interview Question — What Is the Scope Chain?

The scope chain is the lexical chain JavaScript follows when resolving identifiers.

Conceptually:

```text
Current Environment
      ↓
Outer Environment
      ↓
Outer Environment
      ↓
...
```

until the identifier is found or lookup fails.

---

# 52. Interview Question — How Does Execution Context Relate to Hoisting?

Before ordinary statement execution, JavaScript establishes declaration bindings and execution environments.

Different declarations are initialized differently.

For example:

```text
Function Declaration
↓
Function binding available before
its textual declaration executes


var
↓
Initialized to undefined


let / const
↓
Created but uninitialized
until declaration executes
```

These behaviors are described at a high level through the concept of hoisting.

---

# 53. Interview Question — Why Does `var` Return `undefined`?

Example:

```javascript
console.log(x);

var x = 10;
```

During declaration setup:

```text
x → undefined
```

Then:

```javascript
console.log(x);
```

runs before:

```javascript
x = 10;
```

Therefore:

```text
undefined
```

---

# 54. Interview Question — Why Does `let` Throw ReferenceError?

```javascript
console.log(x);

let x = 10;
```

The binding exists, but it is uninitialized until execution reaches:

```javascript
let x = 10;
```

The preceding region is the:

```text
Temporal Dead Zone
```

Access during the TDZ causes:

```text
ReferenceError
```

---

# 55. Interview Question — What Happens When a Function Returns?

Its active execution context completes and is removed from the call stack.

Conceptually:

```text
Function running
      ↓
Context on stack
      ↓
Function completes
      ↓
Context removed from stack
```

Reachable lexical environments may remain alive when needed by closures.

---

# 56. Interview Question — Execution Context vs Call Stack?

Execution Context:

```text
Runtime state/environment associated
with executing code
```

Call Stack:

```text
Stack structure tracking active
execution contexts/function calls
```

---

# 57. Interview Question — Execution Context vs Scope?

Scope:

```text
Determines where identifiers
can be accessed
```

Execution Context:

```text
Represents runtime execution state
while code is executing
```

---

# 58. Interview Question — Why Are Closures Possible?

Functions are created with a relationship to their surrounding lexical environment.

Therefore a function can continue accessing bindings from that environment even after the outer function invocation has completed, as long as those bindings remain reachable through the closure.

---

# 59. Interview Output Question

What happens?

```javascript
console.log(x);

var x = 10;
```

Answer:

```text
undefined
```

---

# 60. Interview Output Question

What happens?

```javascript
console.log(x);

let x = 10;
```

Answer:

```text
ReferenceError
```

because `x` is in the TDZ.

---

# 61. Interview Output Question

What happens?

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Output:

```text
Hello
```

because the function declaration is available before normal statement execution reaches the call.

---

# 62. Interview Output Question

What happens?

```javascript
greet();

var greet = function () {
    console.log("Hello");
};
```

Answer:

```text
TypeError
```

because at the moment of the call:

```text
greet → undefined
```

and JavaScript attempts to call it as a function.

---

# 63. Interview Output Question

What is the output?

```javascript
const x = 10;

function outer() {
    const x = 20;

    function inner() {
        console.log(x);
    }

    inner();
}

outer();
```

Output:

```text
20
```

Identifier lookup finds `x` in the nearest outer lexical environment.

---

# 64. Interview Output Question

What is the output?

```javascript
function createCounter() {
    let count = 0;

    return function () {
        return ++count;
    };
}

const counter =
    createCounter();

console.log(counter());
console.log(counter());
```

Output:

```text
1
2
```

because the returned function closes over the `count` binding.

---

# 65. Mental Model

Consider:

```javascript
const x = 10;

function outer(a) {
    const y = 20;

    function inner() {
        return x + y + a;
    }

    return inner();
}

outer(30);
```

Conceptually:

```text
GLOBAL
================================

x = 10
outer = function

             │
             │ outer(30)
             ▼

OUTER FUNCTION EXECUTION
================================

a = 30
y = 20
inner = function

Outer lexical environment
        │
        ▼
      Global

             │
             │ inner()
             ▼

INNER FUNCTION EXECUTION
================================

Local bindings

Outer lexical environment
        │
        ▼
Outer environment
        │
        ▼
Global environment
```

Inside:

```javascript
return x + y + a;
```

lookup:

```text
x
↓
inner? NO
↓
outer? NO
↓
global? YES → 10


y
↓
inner? NO
↓
outer? YES → 20


a
↓
inner? NO
↓
outer? YES → 30
```

Result:

```text
60
```

---

# 66. Big Picture

Execution context connects many JavaScript concepts:

```text
                    EXECUTION CONTEXT
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
       Scope           Hoisting            this
          │
          ▼
Lexical Environment
          │
          ▼
     Scope Chain
          │
          ▼
       Closures


Execution Context
        │
        ▼
    Call Stack
        │
        ▼
 JavaScript Runtime
        │
        ▼
    Event Loop
```

This is why execution context is useful even after learning scope, hoisting, closures, and `this` individually: it shows how those concepts fit into JavaScript's runtime model.

---

# 67. Cheat Sheet

```text
EXECUTION CONTEXT
===================================

Environment/runtime state used
while JavaScript executes code.


MAIN TYPES
===================================

Global Execution Context

Function Execution Context


FUNCTION CALL
===================================

function defined
↓
No FEC for an invocation yet

function called
↓
New FEC


HIGH-LEVEL LIFECYCLE
===================================

Setup / Creation
↓
Execution
↓
Completion


LEXICAL ENVIRONMENT
===================================

Environment Record
+
Outer Environment Reference


ENVIRONMENT RECORD
===================================

Stores identifier bindings.


OUTER ENVIRONMENT
===================================

Connects lexical scopes.


SCOPE CHAIN
===================================

Current Environment
↓
Outer Environment
↓
Outer Environment
↓
...


VAR
===================================

Binding established
↓
initialized to undefined
↓
assignment happens later


LET / CONST
===================================

Binding established
↓
uninitialized
↓
Temporal Dead Zone
↓
declaration executes
↓
initialized


FUNCTION DECLARATION
===================================

Function binding is available
before normal statement execution
reaches its declaration.


FUNCTION EXPRESSION
===================================

Behavior depends on the variable
declaration holding the function.


CALL STACK
===================================

Tracks active execution contexts.


CLOSURE
===================================

Function retains access to the
lexical environment where it
was created.


IMPORTANT
===================================

Execution Context ≠ Scope

Execution Context ≠ Call Stack

Execution Context ≠ Lexical Environment

They are related concepts,
but they are not synonyms.
```

---

# 68. Key Points to Remember

```text
1. Execution context represents runtime state/environment used to execute code.

2. Global code executes within a global execution context.

3. Function calls create function execution contexts.

4. Defining a function does not create its invocation's function execution context.

5. Every function invocation gets a separate execution context.

6. Execution contexts have setup and execution behavior.

7. Declaration setup helps explain hoisting.

8. var bindings are initialized to undefined before assignment executes.

9. let and const bindings exist before declaration execution but remain uninitialized.

10. This uninitialized period is the Temporal Dead Zone.

11. Function declarations are available before their textual declaration is executed.

12. Function expressions follow the behavior of the variable declaration holding them.

13. Lexical environments store and connect identifier bindings.

14. Environment records contain bindings.

15. Lexical environments reference outer lexical environments.

16. These relationships form the lexical scope chain.

17. Identifier lookup starts locally and proceeds outward.

18. The nearest matching binding wins.

19. This explains variable shadowing.

20. Execution context is related to scope but is not the same thing.

21. Execution context is related to lexical environments but is not identical to them.

22. Execution context is related to closures.

23. Closures can keep lexical environments reachable after an outer function completes.

24. Execution contexts are tracked using the call stack.

25. The currently executing context is at the top of the stack.

26. Nested function calls create nested active execution contexts.

27. Recursive calls create a new execution context for every invocation.

28. When a function completes, its execution context leaves the call stack.

29. Leaving the call stack does not guarantee all associated data is immediately garbage-collected.

30. Execution context provides a runtime model connecting hoisting, scope, closures, this, and the call stack.
```

---

# Execution Context in One Sentence

> An execution context is the runtime structure JavaScript uses to manage the environment and state required while executing global code or a function, including lexical bindings, outer environment relationships, `this` information where applicable, and other execution state.

---
