# Runtime

The **JavaScript Runtime** is the complete environment that allows JavaScript code to execute.

JavaScript itself is a programming language. To actually run JavaScript, we need an implementation of the language plus an environment that provides additional capabilities.

A simplified model is:

```text
JavaScript Runtime
│
├── JavaScript Engine
│   ├── Call Stack
│   ├── Memory / Heap
│   └── Executes JavaScript
│
├── Host APIs
│   ├── Timers
│   ├── Networking
│   ├── Events
│   └── Environment-specific APIs
│
├── Queues
│
└── Event Loop
```

Understanding the runtime explains an important question:

> If JavaScript executes synchronous code on a call stack, how can it handle timers, network requests, user events, and promises without simply blocking until everything finishes?

---

# 1. JavaScript Language vs Runtime

JavaScript defines things such as:

```javascript
const name = "John";

function greet() {
    return `Hello ${name}`;
}

const numbers = [1, 2, 3];

const doubled = numbers.map(
    number => number * 2
);
```

The language defines concepts such as:

```text
Variables
Functions
Objects
Arrays
Classes
Promises
Modules
Operators
Control flow
```

But applications need additional functionality.

For example:

```javascript
setTimeout(() => {
    console.log("Hello");
}, 1000);
```

Where does the timer come from?

The runtime environment provides timer functionality.

Similarly:

```javascript
fetch("/api/users");
```

requires networking capabilities supplied by the environment.

---

# 2. What Is a JavaScript Engine?

A **JavaScript Engine** is software that implements JavaScript and executes JavaScript code.

Examples include:

```text
V8
SpiderMonkey
JavaScriptCore
```

They are used by different browsers and runtimes.

Examples:

```text
Chrome
   ↓
V8


Firefox
   ↓
SpiderMonkey


Safari
   ↓
JavaScriptCore
```

Node.js also uses:

```text
V8
```

But:

```text
Chrome ≠ Node.js
```

even though both use V8.

Why?

Because the JavaScript engine is only one part of the runtime environment.

---

# 3. JavaScript Engine vs JavaScript Runtime

This distinction is important.

## JavaScript Engine

Responsible for executing JavaScript.

Conceptually:

```text
Source Code
     ↓
JavaScript Engine
     ↓
Execution
```

## JavaScript Runtime

Includes the engine plus the surrounding environment.

```text
JavaScript Runtime
│
├── JavaScript Engine
├── Host APIs
├── Event Loop
├── Queues
└── Other runtime functionality
```

Therefore:

```text
Engine
≠
Runtime
```

The engine is a component of the runtime.

---

# 4. Browser JavaScript Runtime

When JavaScript runs inside a browser, the browser provides the runtime environment.

Simplified architecture:

```text
┌─────────────────────────────────────┐
│               Browser               │
│                                     │
│  ┌───────────────────────────────┐  │
│  │      JavaScript Engine        │  │
│  │                               │  │
│  │      Call Stack               │  │
│  │      Memory                   │  │
│  └───────────────────────────────┘  │
│                                     │
│  Browser / Web APIs                 │
│                                     │
│  DOM                                │
│  Timers                             │
│  Fetch / Networking                 │
│  Events                             │
│  Storage                            │
│  etc.                               │
│                                     │
│  Queues                             │
│  Event Loop                         │
│                                     │
└─────────────────────────────────────┘
```

The browser provides capabilities beyond the JavaScript language itself.

---

# 5. Host Environment

JavaScript can run in different environments.

Examples:

```text
Browser
Node.js
Deno
Bun
Embedded runtimes
```

The environment hosting JavaScript is commonly called the:

```text
Host Environment
```

Different hosts can expose different APIs.

For example, browsers provide APIs for working with:

```text
DOM
Browser history
Window
Events
Web storage
```

Server-side runtimes provide APIs for things such as:

```text
File systems
Processes
Networking
Operating system interaction
```

---

# 6. Browser APIs Are Not the JavaScript Language

Consider:

```javascript
document.querySelector("#button");
```

`document` is provided by the browser's DOM APIs.

It is not a universal feature of the JavaScript language itself.

Similarly:

```javascript
window
localStorage
history
navigator
```

are browser-specific host APIs.

That is why code like:

```javascript
document.querySelector("h1");
```

will not work in a normal Node.js environment unless some DOM implementation has been added.

---

# 7. JavaScript Can Exist Without the DOM

A common misconception is:

```text
JavaScript = Browser
```

Not true.

JavaScript is a language.

The browser is one environment capable of running JavaScript.

Therefore:

```text
JavaScript
     │
     ├── Browser
     ├── Node.js
     ├── Deno
     ├── Bun
     └── Other environments
```

The available APIs depend on the host.

---

# 8. JavaScript Engine Architecture — Simplified

A modern JavaScript engine is much more complex than:

```text
Call Stack + Heap
```

But for learning, a simplified model is useful:

```text
JavaScript Engine
│
├── Parses code
├── Compiles/interprets code
├── Executes instructions
├── Manages runtime state
├── Manages memory
└── Performs optimizations
```

Modern engines typically use multiple techniques such as:

```text
Parsing
Bytecode
Interpretation
JIT compilation
Optimization
Deoptimization
Garbage collection
```

The exact architecture differs between engines.

---

# 9. Parsing

Suppose JavaScript receives:

```javascript
const result = 10 + 20;
```

The engine must first understand the source code.

At a high level:

```text
Source Code
     ↓
Parsing
     ↓
Internal Representation
```

Engines commonly represent parsed program structure using something conceptually related to an:

```text
Abstract Syntax Tree
```

or:

```text
AST
```

---

# 10. Abstract Syntax Tree

Consider:

```javascript
const total = price + tax;
```

Conceptually, the structure might represent:

```text
Variable Declaration
│
├── Identifier
│      └── total
│
└── Binary Expression
       │
       ├── price
       ├── +
       └── tax
```

This is a simplified representation.

The engine does not merely read the source as plain text line by line.

It parses the syntax into structures it can process.

---

# 11. Compilation and Interpretation

Older explanations sometimes classify languages as:

```text
JavaScript = Interpreted
```

But modern JavaScript engines are more sophisticated.

They may use:

```text
Interpreter
+
JIT Compiler
```

where:

```text
JIT
=
Just-In-Time Compilation
```

The goal is to begin execution quickly while optimizing frequently executed code when useful.

---

# 12. Simplified Engine Pipeline

A simplified conceptual pipeline:

```text
JavaScript Source
       ↓
Parser
       ↓
Internal Representation
       ↓
Bytecode / Intermediate Form
       ↓
Interpreter
       ↓
Execution
       ↓
Frequently executed code
       ↓
Optimization
       ↓
Optimized machine code
```

Exact details vary between engines.

Do not memorize this as a universal internal architecture.

The important point is:

> Modern JavaScript engines combine interpretation and compilation techniques to execute JavaScript efficiently.

---

# 13. JIT Compilation

JIT means:

```text
Just-In-Time Compilation
```

Instead of compiling the entire application ahead of time in the traditional sense, runtime information can be used to optimize code during execution.

Conceptually:

```text
Code executes
      ↓
Engine observes behavior
      ↓
Frequently executed paths identified
      ↓
Optimization opportunities found
      ↓
Optimized code generated
```

This is one reason modern JavaScript can execute very efficiently.

---

# 14. Optimization and Deoptimization

Suppose:

```javascript
function add(a, b) {
    return a + b;
}
```

and the function is repeatedly called with numbers:

```javascript
add(10, 20);
add(30, 40);
add(50, 60);
```

An engine may optimize based on observed runtime behavior.

But JavaScript is dynamic:

```javascript
add("Hello ", "World");
```

Now the operation has different behavior.

Engines must account for JavaScript's dynamic nature.

Sometimes optimized assumptions become invalid and the engine may need to:

```text
Deoptimize
```

The exact behavior is engine-specific.

---

# 15. The Call Stack

We already covered the Call Stack.

It tracks:

```text
Active function calls
```

Example:

```javascript
function first() {
    second();
}

function second() {
    console.log("Hello");
}

first();
```

At the deepest point:

```text
┌──────────────┐
│    second    │
├──────────────┤
│    first     │
├──────────────┤
│    Global    │
└──────────────┘
```

The runtime combines this JavaScript execution mechanism with host functionality.

---

# 16. Memory / Heap

JavaScript needs memory for runtime data.

Objects, functions, arrays, strings, internal structures, and other values require memory.

A common simplified model uses:

```text
Stack
Heap
```

with the heap associated with dynamically allocated data.

However, avoid assuming simplistic rules such as:

```text
Primitive = always stack
Object = always heap
```

Actual memory layout is controlled and optimized by the JavaScript engine.

We will cover this more thoroughly under:

```text
Memory Management
Garbage Collection
```

---

# 17. Host APIs

The runtime environment exposes APIs that JavaScript code can use.

In a browser, examples include:

```text
DOM
Timers
Fetch
Events
Web Storage
Geolocation
WebSocket
Workers
```

These are often collectively referred to in learning material as:

```text
Web APIs
```

Example:

```javascript
setTimeout(() => {
    console.log("Hello");
}, 1000);
```

JavaScript calls the timer API.

The browser handles timer scheduling.

---

# 18. Why Host APIs Matter

Suppose JavaScript itself had to synchronously wait for every operation.

Example:

```text
Start network request
↓
Wait 5 seconds
↓
Response arrives
↓
Continue application
```

The application would be blocked while waiting.

Instead, asynchronous host APIs allow work such as:

```text
Network request
Timer
User event
```

to be handled by the runtime while JavaScript is free to continue other execution.

---

# 19. Example — `setTimeout`

Consider:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 1000);

console.log("End");
```

Output:

```text
Start
End
Timer
```

Why?

Because `setTimeout()` does not block JavaScript for one second.

---

# 20. `setTimeout` Execution — High Level

Conceptually:

```text
JavaScript
    │
    │ setTimeout(callback, 1000)
    ▼
Runtime Timer System
    │
    │ timer progresses
    │
    ▼
Callback becomes eligible
    │
    ▼
Task Queue
    │
    ▼
Event Loop
    │
    ▼
JavaScript executes callback
```

Meanwhile, JavaScript continues:

```javascript
console.log("End");
```

Therefore:

```text
Start
End
Timer
```

---

# 21. `setTimeout` Does Not Put the Callback on the Stack Immediately

Incorrect:

```text
setTimeout()
↓
Callback immediately added to Call Stack
```

Correct simplified model:

```text
setTimeout()
↓
Runtime handles timer
↓
Timer becomes eligible
↓
Callback task queued
↓
Scheduling rules allow it to run
↓
Callback executes on Call Stack
```

---

# 22. What Does the Delay Mean?

Consider:

```javascript
setTimeout(callback, 1000);
```

This does not mean:

```text
Execute callback exactly after 1000 ms.
```

It means more like:

```text
After the timer satisfies the runtime's
timing rules, the callback becomes eligible
to be scheduled.
```

Actual execution may happen later.

---

# 23. Why a Timer Can Be Delayed

Consider:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 1000);

const start = Date.now();

while (Date.now() - start < 5000) {
    // blocking
}
```

The timer may become eligible around the requested delay.

But JavaScript is busy executing the loop.

Conceptually:

```text
Timer requested
     ↓
1 second passes
     ↓
Timer task eligible
     ↓
JavaScript still busy
     ↓
Wait
     ↓
Loop finishes
     ↓
Current execution completes
     ↓
Timer callback eventually executes
```

Therefore timer delays are not exact execution guarantees.

---

# 24. Example — Network Request

Consider:

```javascript
console.log("Start");

fetch("/api/users")
    .then(response => response.json())
    .then(data => {
        console.log(data);
    });

console.log("End");
```

JavaScript does not synchronously sit on the call stack waiting for the network response.

Conceptually:

```text
JavaScript
     │
     │ initiate fetch
     ▼
Runtime Networking
     │
     │ request in progress
     ▼
Response
     │
     ▼
Promise settles
     │
     ▼
Promise reaction scheduled
     │
     ▼
JavaScript executes handler later
```

Meanwhile:

```javascript
console.log("End");
```

can execute.

---

# 25. Asynchronous Does Not Mean Parallel JavaScript Execution

This distinction is important.

```text
Asynchronous
≠
Automatically parallel JavaScript execution
```

Asynchronous means an operation can be initiated and completed later without forcing the current JavaScript execution to block waiting for it.

Example:

```text
JavaScript initiates network request
        ↓
Runtime handles waiting
        ↓
JavaScript continues
        ↓
Response arrives later
        ↓
JavaScript callback/continuation runs later
```

The waiting does not require JavaScript to sit on the call stack.

---

# 26. Runtime Concurrency

Even though JavaScript code often executes on one main JavaScript thread, the surrounding runtime can use other operating-system threads/processes internally.

For example:

```text
JavaScript Thread
       │
       ├── execute application JS
       │
       └── initiate operations

Runtime / Browser / OS
       │
       ├── networking
       ├── timers
       ├── rendering infrastructure
       └── other internal work
```

So:

```text
Single JavaScript Call Stack
```

does not mean:

```text
Entire browser has one thread.
```

---

# 27. Browser Main Thread

In browsers, much application JavaScript normally runs on the:

```text
Main Thread
```

The main thread also participates in browser work such as:

```text
DOM-related operations
Event processing
Rendering coordination
Layout
Painting
```

Exact browser internals are more complicated and use multiple threads/processes.

The practical consequence is:

```text
Long-running JavaScript
↓
Main thread busy
↓
UI responsiveness can suffer
```

---

# 28. Blocking Example

```javascript
const start = Date.now();

while (Date.now() - start < 10000) {
    // block for about 10 seconds
}
```

During this loop, the JavaScript thread cannot execute other JavaScript callbacks.

In a browser, this can make the interface appear frozen.

---

# 29. Why Async APIs Help

Suppose an API request takes 3 seconds.

Synchronous blocking model:

```text
Request
↓
Wait 3 seconds
↓
Do nothing else
↓
Response
↓
Continue
```

Asynchronous model:

```text
Request
↓
Runtime handles waiting
│
├── JavaScript continues other work
│
└── Response eventually arrives
          ↓
    continuation scheduled
          ↓
    JavaScript handles response
```

This is fundamental to modern frontend development.

---

# 30. Task Queue

When certain asynchronous operations are ready, associated work can be queued as a:

```text
Task
```

A simplified learning model often calls this queue:

```text
Task Queue
```

or historically:

```text
Callback Queue
Macrotask Queue
```

Examples of task sources can include:

```text
Timers
User events
Messages
Other host-defined tasks
```

Exact scheduling is defined by the host/runtime.

---

# 31. Microtask Queue

JavaScript runtimes also process:

```text
Microtasks
```

Important sources include:

```text
Promise reactions
queueMicrotask()
```

Example:

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});
```

The `.then()` callback is scheduled as a microtask.

---

# 32. Task vs Microtask

Simplified:

```text
Task Queue
────────────────────────

Timers
Events
Other tasks


Microtask Queue
────────────────────────

Promise reactions
queueMicrotask()
```

Microtasks receive special scheduling treatment and are processed at microtask checkpoints.

For common browser examples, this means promise handlers usually execute before the next timer task once the current synchronous task finishes.

---

# 33. Event Loop

The **Event Loop** coordinates execution between:

```text
JavaScript execution
Queues
Runtime
```

Simplified mental model:

```text
        Call Stack
            │
            ▼
     Current JS executes
            │
            ▼
      Current task ends
            │
            ▼
     Process Microtasks
            │
            ▼
Runtime may render / perform
other host scheduling work
            │
            ▼
      Next Task executes
            │
            └───────↺
```

We will cover the event loop in detail in its own topic.

---

# 34. Basic Runtime Architecture

A useful browser mental model:

```text
┌─────────────────────────────────────────┐
│                Browser                  │
│                                         │
│   ┌───────────────────────────────┐     │
│   │      JavaScript Engine        │     │
│   │                               │     │
│   │       Call Stack              │     │
│   │       Memory                  │     │
│   └───────────────────────────────┘     │
│                                         │
│   Web / Host APIs                       │
│   ┌───────────────────────────────┐     │
│   │ Timers                        │     │
│   │ DOM                           │     │
│   │ Networking                    │     │
│   │ Events                        │     │
│   │ Storage                       │     │
│   └───────────────────────────────┘     │
│                                         │
│   Microtask Queue                       │
│                                         │
│   Task Queues                           │
│                                         │
│   Event Loop                            │
│                                         │
└─────────────────────────────────────────┘
```

This is simplified, but useful for reasoning about application behavior.

---

# 35. Complete Timer Example

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

### Step 1

Global JavaScript begins.

```text
Call Stack

Global
```

### Step 2

Execute:

```javascript
console.log("A");
```

Output:

```text
A
```

### Step 3

Execute:

```javascript
setTimeout(callback, 0);
```

The runtime registers the timer.

JavaScript does not wait for it.

### Step 4

Execute:

```javascript
console.log("C");
```

Output:

```text
C
```

### Step 5

Current synchronous execution completes.

### Step 6

The timer callback, once eligible and selected as a task, executes.

Output:

```text
B
```

Final:

```text
A
C
B
```

---

# 36. Promise Example

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");
});

console.log("C");
```

Synchronous execution:

```text
A
C
```

The promise reaction is scheduled as a microtask.

After current synchronous execution:

```text
B
```

Final:

```text
A
C
B
```

---

# 37. Timer + Promise Example

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

Promise.resolve().then(() => {
    console.log("C");
});

console.log("D");
```

Output:

```text
A
D
C
B
```

High-level reason:

```text
Synchronous
────────────

A
D


Microtasks
────────────

C


Next timer task
────────────

B
```

---

# 38. Runtime Flow for Timer + Promise

Conceptually:

```text
Global JavaScript
      │
      ├── console.log("A")
      │
      ├── setTimeout(...)
      │       │
      │       └── timer handled by runtime
      │
      ├── Promise.then(...)
      │       │
      │       └── promise reaction becomes microtask
      │
      └── console.log("D")
              │
              ▼
       Current JS finishes
              │
              ▼
       Microtask checkpoint
              │
              ▼
              C
              │
              ▼
       Later next task
              │
              ▼
              B
```

---

# 39. Why Promise Runs Before Timer

Consider:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});
```

After current synchronous JavaScript finishes:

```text
Microtasks are processed
before moving on to the next task.
```

Therefore:

```text
Promise
Timer
```

for this common example.

This ordering is extremely important in JavaScript.

---

# 40. Microtasks Can Create More Microtasks

Example:

```javascript
Promise.resolve().then(() => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });
});
```

Output:

```text
A
B
```

During the microtask checkpoint, the first microtask can schedule another microtask.

The runtime continues processing microtasks according to the event-loop rules.

---

# 41. Microtask Starvation

Because microtasks can schedule additional microtasks, poorly designed code can keep adding more work.

Conceptually:

```text
Microtask
↓
Creates Microtask
↓
Creates Microtask
↓
Creates Microtask
↓
...
```

This can delay other work such as:

```text
Timers
Rendering opportunities
Other tasks
```

This is sometimes referred to as:

```text
Microtask Starvation
```

---

# 42. Browser Rendering

Browser rendering does not simply happen after every JavaScript statement.

A simplified flow might look like:

```text
Task
↓
JavaScript executes
↓
Microtasks processed
↓
Browser may render
↓
Next task
```

Actual browser scheduling is more sophisticated.

The important lesson is:

> Long JavaScript tasks and excessive microtasks can delay rendering and make the UI feel unresponsive.

---

# 43. User Events

Suppose:

```javascript
button.addEventListener("click", () => {
    console.log("Clicked");
});
```

When the user clicks:

```text
User clicks button
      ↓
Browser detects event
      ↓
Relevant event task is scheduled
      ↓
Runtime gets opportunity to run it
      ↓
Event callback executes as JavaScript
```

The event listener is not sitting on the call stack the entire time waiting for the user to click.

---

# 44. Event Listener Registration

This:

```javascript
button.addEventListener("click", handleClick);
```

does not execute:

```javascript
handleClick();
```

immediately.

Instead:

```text
Register handleClick
as event listener
      ↓
JavaScript continues
      ↓
User may click later
      ↓
Browser handles event
      ↓
Callback gets opportunity to execute
```

---

# 45. Runtime and DOM Events

Example:

```javascript
console.log("Start");

button.addEventListener("click", () => {
    console.log("Clicked");
});

console.log("End");
```

Immediate output:

```text
Start
End
```

Later, when the user clicks:

```text
Clicked
```

This demonstrates asynchronous event-driven programming.

---

# 46. Node.js Runtime

JavaScript also runs outside browsers.

One of the most important environments is:

```text
Node.js
```

Node.js uses:

```text
V8
```

to execute JavaScript.

But Node.js adds its own runtime functionality.

Simplified:

```text
Node.js
│
├── V8
│
├── Node APIs
│
├── Event Loop
│
├── File System APIs
│
├── Networking APIs
│
└── libuv
```

---

# 47. What Is libuv?

Node.js uses a library called:

```text
libuv
```

for important runtime functionality including:

```text
Event loop
Asynchronous I/O abstractions
Thread pool for certain operations
Timers-related infrastructure
```

Node's runtime architecture differs from browser runtime architecture.

Do not assume:

```text
Browser Event Loop
=
Node.js Event Loop
```

They share core asynchronous concepts but have different scheduling details.

---

# 48. Browser vs Node.js

## Browser

Provides APIs such as:

```text
DOM
window
document
localStorage
Browser events
```

## Node.js

Provides APIs such as:

```text
File system
Processes
Server networking
Streams
Buffers
OS utilities
```

Both execute JavaScript.

But their host APIs differ.

---

# 49. Example — Browser Only

```javascript
document.querySelector("button");
```

Normally works in:

```text
Browser
```

Not normal Node.js because Node does not provide a browser DOM by default.

---

# 50. Example — Node.js

```javascript
import fs from "node:fs";
```

Node provides filesystem functionality.

Browser JavaScript does not expose Node's filesystem API.

This demonstrates:

```text
Same language
+
Different runtime
=
Different available capabilities
```

---

# 51. Global Objects

Different environments expose different global objects.

Historically in browsers:

```javascript
window
```

In Node.js:

```javascript
global
```

Modern JavaScript provides:

```javascript
globalThis
```

as a standardized way to reference the global `this` value across environments where applicable.

Example:

```javascript
console.log(globalThis);
```

---

# 52. Runtime Determines Available APIs

Suppose:

```javascript
fetch("/api/users");
```

Whether an API is available depends on the runtime.

Modern browsers provide `fetch`.

Modern Node.js versions also provide `fetch`.

But this does not make `fetch` a core syntax feature of the ECMAScript language.

It is an API supplied by the host/runtime environment.

---

# 53. ECMAScript vs Web APIs

ECMAScript defines the core JavaScript language.

Examples:

```text
Array
Object
Promise
Map
Set
WeakMap
WeakSet
Symbol
Functions
Classes
Modules
```

Browser standards define Web APIs such as:

```text
DOM
Fetch
Web Storage
History
WebSocket
Geolocation
```

So:

```text
JavaScript Application
        │
        ├── ECMAScript
        │
        └── Host APIs
```

---

# 54. Promise Is JavaScript, `fetch` Is Host API

Important distinction:

```javascript
Promise
```

is part of ECMAScript.

But:

```javascript
fetch()
```

is a host-provided API standardized by web/platform specifications and also implemented by environments such as modern Node.js.

Similarly:

```javascript
setTimeout()
```

is provided by the host environment.

---

# 55. `console` Is Also Host-Provided

You constantly write:

```javascript
console.log("Hello");
```

But the `console` API is supplied by the environment.

Browsers provide a console.

Node.js also provides a console.

They expose similar interfaces, but they are host/runtime functionality rather than JavaScript syntax.

---

# 56. Runtime and Async/Await

Consider:

```javascript
async function loadUsers() {
    const response =
        await fetch("/api/users");

    const users =
        await response.json();

    return users;
}
```

`async/await` is JavaScript language functionality built on promises.

But:

```javascript
fetch()
```

uses runtime networking functionality.

Conceptually:

```text
async / await
     │
     ▼
Promises
     │
     ▼
JavaScript semantics

fetch
     │
     ▼
Runtime networking
```

They work together.

---

# 57. What Happens at `await`?

Suppose:

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}

test();

console.log("C");
```

Output:

```text
A
C
B
```

Why?

At a high level:

```text
test starts
↓
A

await reached
↓
remaining async-function work
continues later

global code continues
↓
C

microtask continuation
↓
B
```

We'll study this properly under:

```text
Promises
Async / Await
```

---

# 58. Runtime Does Not Mean Everything Is Async

The runtime supports asynchronous behavior.

But normal JavaScript remains synchronous unless an operation uses asynchronous mechanisms.

Example:

```javascript
console.log("A");
console.log("B");
console.log("C");
```

Output:

```text
A
B
C
```

Nothing asynchronous happens here.

---

# 59. Synchronous Function

```javascript
function calculate() {
    return 10 + 20;
}

const result = calculate();

console.log(result);
```

Flow:

```text
calculate called
↓
calculate executes
↓
returns 30
↓
caller continues
```

No runtime queue is needed for this function to complete.

---

# 60. Asynchronous Operation

```javascript
setTimeout(() => {
    console.log("Done");
}, 1000);
```

Flow:

```text
Register timer
↓
JavaScript continues
↓
Timer progresses outside current JS execution
↓
Callback becomes eligible
↓
Task scheduled
↓
Callback executes later
```

---

# 61. Runtime Does Not Make JavaScript Multi-Threaded Automatically

Suppose:

```javascript
function first() {
    // synchronous work
}

function second() {
    // synchronous work
}

first();
second();
```

These functions do not automatically run simultaneously on separate threads.

They execute sequentially.

To perform actual parallel computation in browsers, mechanisms such as:

```text
Web Workers
```

can be used.

Node.js has mechanisms such as:

```text
Worker Threads
```

But that is different from ordinary asynchronous programming.

---

# 62. Concurrency vs Parallelism

## Concurrency

Multiple tasks can make progress over overlapping periods.

Conceptually:

```text
Task A starts
↓
A waits

Task B progresses

A becomes ready
↓
A continues
```

## Parallelism

Multiple operations execute at the same time on different execution resources.

Conceptually:

```text
CPU Core 1 → Task A

CPU Core 2 → Task B
```

They are related but not identical.

---

# 63. Async vs Concurrency vs Parallelism

```text
Asynchronous
────────────────────

An operation can complete later
without forcing the current code
to block waiting for it.


Concurrency
────────────────────

Multiple activities can make
progress over overlapping time.


Parallelism
────────────────────

Multiple operations execute
simultaneously.
```

Do not treat these terms as synonyms.

---

# 64. Real-World Example

Imagine a restaurant.

JavaScript is the waiter.

A customer orders food.

Bad blocking model:

```text
Waiter gives order to kitchen
↓
Waiter stands at kitchen
for 20 minutes
↓
Food ready
↓
Waiter serves customer
↓
Only now handles next customer
```

Asynchronous model:

```text
Waiter gives order to kitchen
↓
Kitchen prepares food
↓
Waiter handles other customers
↓
Food becomes ready
↓
Waiter eventually serves it
```

The waiter does not cook multiple dishes simultaneously.

Other systems handle work while the waiter remains available for other tasks.

This is a useful mental model for:

```text
JavaScript
+
Runtime
+
Async APIs
```

---

# 65. Complete Browser Runtime Example

Consider:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});

button.addEventListener("click", () => {
    console.log("Click");
});

console.log("End");
```

Immediate synchronous output:

```text
Start
End
```

Then the promise microtask:

```text
Promise
```

Then, assuming the timer task is next:

```text
Timer
```

If the user clicks later:

```text
Click
```

So one possible timeline is:

```text
Start
End
Promise
Timer
...
Click
```

---

# 66. Runtime Architecture for the Example

```text
JavaScript
│
├── console.log("Start")
│
├── setTimeout(...)
│      │
│      └── Browser timer system
│
├── Promise.then(...)
│      │
│      └── Microtask
│
├── addEventListener(...)
│      │
│      └── Browser event system
│
└── console.log("End")

        ↓

Current synchronous task completes

        ↓

Microtask checkpoint

        ↓

Promise callback

        ↓

Later tasks

        ├── Timer callback
        └── Click callback when event occurs
```

---

# 67. Why Understanding Runtime Matters for React

React applications constantly use runtime features.

Examples:

```javascript
fetch("/api/products");

setTimeout(() => {}, 1000);

Promise.resolve();

button.addEventListener(...);
```

React itself also interacts with:

```text
Browser events
DOM
Scheduling
JavaScript execution
Microtasks/tasks
Rendering
```

Understanding the runtime helps explain problems such as:

```text
Why API responses arrive later

Why state updates are not equivalent
to normal variable assignment

Why heavy computation freezes the UI

Why timers execute later

Why promises run before timers

Why event handlers execute only
after events occur
```

---

# 68. Runtime and UI Freezing

Suppose a React event handler does:

```javascript
function handleClick() {
    const start = Date.now();

    while (Date.now() - start < 5000) {
        // expensive synchronous work
    }
}
```

The browser's main thread remains occupied by JavaScript.

The UI can appear frozen.

React cannot magically bypass the JavaScript runtime.

Understanding:

```text
Call Stack
Runtime
Event Loop
Rendering
```

helps diagnose this kind of performance issue.

---

# 69. Common Mistake — JavaScript Engine Is the Browser

Incorrect:

```text
JavaScript Engine = Browser
```

Correct:

```text
Browser
│
├── JavaScript Engine
├── Rendering Engine
├── Networking
├── Web APIs
├── Storage
├── Event Loop infrastructure
└── Many other components
```

The JavaScript engine is one component.

---

# 70. Common Mistake — V8 Is Node.js

Incorrect:

```text
V8 = Node.js
```

Correct:

```text
Node.js
│
├── V8
├── Node APIs
├── libuv
├── Event Loop
└── Other components
```

V8 executes JavaScript.

Node.js provides the larger server-side runtime environment.

---

# 71. Common Mistake — V8 Is Chrome

Incorrect:

```text
V8 = Chrome
```

Correct:

```text
Chrome
│
├── V8
├── Browser UI
├── Rendering engine
├── Networking
├── Storage
├── Web APIs
└── Many other systems
```

V8 is Chrome's JavaScript engine.

It is not the entire browser.

---

# 72. Common Mistake — `setTimeout` Is Core JavaScript

`setTimeout` is not part of ECMAScript itself.

It is supplied by runtime environments such as browsers and Node.js.

That is why we distinguish:

```text
JavaScript Language
vs
Host APIs
```

---

# 73. Common Mistake — `fetch` Is Core JavaScript

`fetch` is not ECMAScript syntax.

It is an API provided by runtime environments that implement it.

Modern browsers and modern Node.js provide `fetch`, but the API belongs to the surrounding platform rather than the ECMAScript language specification itself.

---

# 74. Common Mistake — Promise Is a Browser API

Incorrect.

```javascript
Promise
```

is part of ECMAScript.

So:

```text
Promise
↓
JavaScript / ECMAScript


fetch
↓
Host API


setTimeout
↓
Host API
```

This distinction is useful in interviews.

---

# 75. Common Mistake — JavaScript Handles the Timer Itself

Incorrect simplified model:

```text
JavaScript
↓
counts 1000 ms
↓
executes callback
```

Better model:

```text
JavaScript registers timer
↓
Runtime manages timing
↓
Callback becomes eligible
↓
Task scheduling
↓
JavaScript executes callback later
```

---

# 76. Common Mistake — `setTimeout(0)` Means Immediate

Incorrect:

```javascript
setTimeout(callback, 0);
```

does not mean:

```text
Execute callback now.
```

It means the timer can become eligible without an intentional delay beyond applicable runtime rules, but the callback still has to wait for scheduling.

---

# 77. Common Mistake — Async Code Runs on Call Stack While Waiting

Incorrect:

```text
fetch()
↓
Function stays on stack
for 5 seconds
```

Instead, asynchronous operations allow the current JavaScript execution to yield/complete while the runtime handles waiting.

JavaScript continuation happens later.

---

# 78. Common Mistake — Browser Has One Thread

JavaScript application code commonly runs on the browser main thread, but modern browsers use many threads and processes internally.

Therefore:

```text
JavaScript often uses one main execution thread
```

does not mean:

```text
Browser has only one thread.
```

---

# 79. Common Mistake — Async Means Parallel

Incorrect:

```text
Asynchronous
=
Parallel
```

An operation can be asynchronous without your JavaScript code executing in parallel.

The runtime may handle waiting or external work while JavaScript continues.

---

# 80. Common Mistake — Event Loop Executes JavaScript

The event loop is a scheduling mechanism.

The JavaScript engine executes JavaScript.

A better mental model:

```text
Event Loop
↓
Coordinates scheduling

JavaScript Engine
↓
Executes JavaScript
```

---

# 81. Common Mistake — Event Loop Only Handles `setTimeout`

The event loop participates in scheduling many kinds of work, including:

```text
Timers
Events
Promise-related microtasks
Messages
Other runtime tasks
```

The exact model depends on the environment.

---

# 82. Interview Question — What Is JavaScript Runtime?

**Answer:**

A JavaScript runtime is the complete environment in which JavaScript executes, combining a JavaScript engine with host APIs and scheduling/runtime infrastructure such as event loops and queues.

---

# 83. Interview Question — What Is a JavaScript Engine?

A JavaScript engine is software that implements and executes JavaScript.

Examples:

```text
V8
SpiderMonkey
JavaScriptCore
```

---

# 84. Interview Question — Engine vs Runtime?

```text
Engine
────────────────────

Executes JavaScript.


Runtime
────────────────────

Engine
+
Host APIs
+
Scheduling infrastructure
+
Environment-specific capabilities
```

---

# 85. Interview Question — Is V8 JavaScript?

No.

JavaScript is the language.

V8 is one implementation/engine capable of executing JavaScript.

---

# 86. Interview Question — Is V8 Node.js?

No.

Node.js uses V8 as its JavaScript engine.

Node.js also includes runtime functionality such as:

```text
Node APIs
libuv
Event Loop
File System APIs
Networking
```

---

# 87. Interview Question — What Are Web APIs?

Web APIs are browser/platform APIs exposed to JavaScript for capabilities such as:

```text
DOM manipulation
Networking
Timers
Events
Storage
Geolocation
```

They are provided by the browser environment rather than the ECMAScript language itself.

---

# 88. Interview Question — Is `setTimeout` JavaScript?

`setTimeout` is not defined by ECMAScript itself.

It is provided by host environments such as browsers and Node.js.

---

# 89. Interview Question — Is `Promise` JavaScript?

Yes.

`Promise` is part of ECMAScript.

---

# 90. Interview Question — Is `fetch` JavaScript?

`fetch()` is callable from JavaScript, but it is not defined by the core ECMAScript language.

It is a platform API implemented by environments such as browsers and modern Node.js.

---

# 91. Interview Question — How Can JavaScript Handle Async Operations?

At a high level:

```text
JavaScript initiates operation
↓
Runtime handles asynchronous work/waiting
↓
JavaScript continues
↓
Operation completes
↓
Relevant continuation/callback is scheduled
↓
JavaScript executes it later
```

The exact mechanism depends on the API.

---

# 92. Interview Question — What Is the Event Loop?

The event loop is the runtime scheduling mechanism that coordinates when queued tasks and microtasks get opportunities to execute relative to current JavaScript execution and other host activities.

We'll cover this in depth separately.

---

# 93. Interview Question — Task vs Microtask?

Simplified:

```text
Task
────────────────────

Examples:
Timers
Events


Microtask
────────────────────

Examples:
Promise reactions
queueMicrotask()
```

Microtasks are processed at microtask checkpoints, generally before the runtime proceeds to the next task.

---

# 94. Interview Question — Why Does Promise Run Before Timer?

Example:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});
```

Typical output:

```text
Promise
Timer
```

because after the current task completes, promise reactions in the microtask queue are processed before selecting the next task such as an eligible timer.

---

# 95. Interview Question — Does `setTimeout(0)` Run Immediately?

No.

The callback is scheduled through the runtime and executes only when scheduling rules allow.

The `0` does not mean synchronous execution.

---

# 96. Interview Question — What Is JIT?

JIT stands for:

```text
Just-In-Time Compilation
```

Modern JavaScript engines can compile and optimize code during runtime using information observed while the program executes.

---

# 97. Interview Question — Browser vs Node.js Runtime?

Both can execute JavaScript.

But they provide different host capabilities.

Browser:

```text
DOM
window
Browser events
Web APIs
```

Node.js:

```text
File system
Processes
Server networking
Streams
OS APIs
```

---

# 98. Interview Output Question

What is the output?

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

Answer:

```text
A
C
B
```

---

# 99. Interview Output Question

What is the output?

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");
});

console.log("C");
```

Answer:

```text
A
C
B
```

---

# 100. Interview Output Question

What is the output?

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

Promise.resolve().then(() => {
    console.log("C");
});

console.log("D");
```

Answer:

```text
A
D
C
B
```

Reason:

```text
Synchronous
↓
A
D

Microtask
↓
C

Timer task
↓
B
```

---

# 101. Interview Output Question

What is the output?

```javascript
console.log("Start");

Promise.resolve()
    .then(() => {
        console.log("One");
    })
    .then(() => {
        console.log("Two");
    });

console.log("End");
```

Output:

```text
Start
End
One
Two
```

Promise reactions execute asynchronously as microtasks.

---

# 102. Interview Output Question

What is the output?

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}

console.log("C");

test();

console.log("D");
```

Output:

```text
C
A
D
B
```

`test()` begins synchronously.

At `await`, the remaining continuation executes later through promise/microtask scheduling.

---

# 103. Full Mental Model

```text
                    JAVASCRIPT APPLICATION
                             │
                             ▼
                   JAVASCRIPT RUNTIME
                             │
         ┌───────────────────┼────────────────────┐
         │                   │                    │
         ▼                   ▼                    ▼
  JavaScript Engine      Host APIs            Scheduling
         │                   │                    │
         │                   │             ┌──────┴──────┐
         │                   │             │             │
         ▼                   ▼             ▼             ▼
   Call Stack             Timers       Task Queues   Microtasks
   Memory                 Network           │             │
   Execution              Events            └──────┬──────┘
   Optimization           DOM                      │
                                                    ▼
                                                Event Loop
```

Remember:

```text
JavaScript Engine
↓
Executes JavaScript


Host APIs
↓
Provide environment capabilities


Queues
↓
Hold scheduled work


Event Loop
↓
Coordinates when work gets
an opportunity to execute


Call Stack
↓
Tracks currently active
JavaScript execution
```

---

# 104. Browser Example — Full Flow

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 0);

fetch("/api/users")
    .then(() => {
        console.log("Fetch");
    });

Promise.resolve()
    .then(() => {
        console.log("Promise");
    });

console.log("End");
```

Immediate synchronous execution:

```text
Start
End
```

Then the already-scheduled promise microtask:

```text
Promise
```

The relative timing of:

```text
Timer
Fetch
```

cannot be determined simply from source order because the network request must complete and runtime scheduling determines when its promise is settled and when the resulting microtask is queued.

So do **not** assume:

```text
Start
End
Promise
Timer
Fetch
```

will always be the exact complete order.

The guaranteed lesson here is:

```text
Start
End
```

happen synchronously, while the timer, fetch continuation, and promise continuation depend on asynchronous scheduling.

---

# 105. Runtime Cheat Sheet

```text
JAVASCRIPT RUNTIME
================================

Environment in which JavaScript runs.


RUNTIME
================================

JavaScript Engine
+
Host APIs
+
Scheduling infrastructure
+
Environment-specific functionality


ENGINE
================================

Executes JavaScript.


COMMON ENGINES
================================

V8
SpiderMonkey
JavaScriptCore


V8
================================

Used by Chrome.

Also used by Node.js.

V8 ≠ Chrome
V8 ≠ Node.js


BROWSER RUNTIME
================================

JavaScript Engine
+
Web APIs
+
Event Loop
+
Queues
+
Browser capabilities


NODE.JS
================================

V8
+
Node APIs
+
libuv
+
Event Loop
+
Server-side capabilities


HOST APIs
================================

Provided by environment.


BROWSER EXAMPLES
================================

DOM
Timers
Fetch
Events
Storage


NODE EXAMPLES
================================

File system
Processes
Networking
Streams


PROMISE
================================

ECMAScript feature.


setTimeout
================================

Host API.


fetch
================================

Host/platform API.


CALL STACK
================================

Tracks active JavaScript calls.


TASK
================================

Unit of work scheduled by runtime.

Examples include:
Timers
Events


MICROTASK
================================

Special scheduled work.

Examples:
Promise reactions
queueMicrotask()


COMMON ORDER
================================

Current synchronous task
↓
Microtasks
↓
Next task


EVENT LOOP
================================

Coordinates scheduling of
tasks and microtasks.


ASYNCHRONOUS
================================

Operation can complete later
without blocking current JS
while waiting.


PARALLEL
================================

Operations execute simultaneously.

Async ≠ Parallel


BLOCKING
================================

Long synchronous JavaScript
prevents other JS work from
executing on the same thread.


JIT
================================

Just-In-Time Compilation.


MAIN IDEA
================================

JavaScript does not provide
all application capabilities
by itself.

JavaScript Engine
+
Runtime Environment
=
Working JavaScript Application
```

---

# 106. Key Points to Remember

```text
1. JavaScript is a programming language.

2. A JavaScript engine executes JavaScript.

3. V8, SpiderMonkey, and JavaScriptCore are JavaScript engines.

4. The JavaScript engine is not the complete runtime.

5. A runtime combines the engine with host functionality.

6. Browsers provide a JavaScript runtime.

7. Node.js provides a JavaScript runtime.

8. Browser and Node.js runtimes expose different APIs.

9. The DOM is not part of core ECMAScript.

10. setTimeout is not part of core ECMAScript.

11. fetch is a host/platform API.

12. Promise is part of ECMAScript.

13. console is provided by the host environment.

14. Modern JavaScript engines use sophisticated execution strategies.

15. Modern engines may combine interpretation and JIT compilation.

16. Engines can optimize frequently executed code.

17. Engines can deoptimize when optimization assumptions become invalid.

18. The call stack tracks active JavaScript calls.

19. JavaScript needs memory for runtime values and internal structures.

20. Actual engine memory management is more complex than simple stack/heap diagrams.

21. Host APIs allow JavaScript to interact with the surrounding environment.

22. Browsers provide timers, networking, DOM APIs, events, and storage.

23. Async host APIs prevent JavaScript from blocking while merely waiting for operations.

24. setTimeout registers timer work with the runtime.

25. setTimeout does not synchronously wait for the delay.

26. A timer delay does not guarantee exact callback execution time.

27. A busy JavaScript thread can delay timer callbacks.

28. Network requests can progress without JavaScript synchronously waiting on the call stack.

29. Asynchronous does not automatically mean parallel.

30. The browser itself is not single-threaded just because application JavaScript commonly executes on one main thread.

31. Long synchronous JavaScript can block the main thread.

32. Blocking the main thread can make browser UIs unresponsive.

33. Tasks represent scheduled units of runtime work.

34. Promise reactions use microtasks.

35. queueMicrotask() schedules a microtask.

36. Microtasks have different scheduling semantics from normal tasks.

37. After a task, microtasks are processed at a microtask checkpoint.

38. Promise handlers commonly execute before a timer scheduled from the same synchronous task.

39. Microtasks can create more microtasks.

40. Excessive microtasks can delay other runtime work.

41. User event handlers execute when the runtime schedules their event tasks.

42. Event listeners do not remain on the call stack while waiting for events.

43. Node.js uses V8.

44. Node.js uses libuv for important asynchronous/runtime functionality.

45. Browser and Node.js event-loop details are not identical.

46. globalThis provides a standardized global reference across environments.

47. async/await works with JavaScript promises.

48. async/await can interact with host APIs such as fetch.

49. await does not synchronously block the JavaScript thread waiting for a promise.

50. Runtime knowledge is fundamental for understanding Promises, async/await, Fetch, events, and React behavior.
```

---

# JavaScript Runtime in One Sentence

> The JavaScript runtime is the complete environment that executes JavaScript by combining a JavaScript engine with host APIs, memory management, queues, event-loop scheduling, and environment-specific capabilities such as browser DOM/networking APIs or Node.js filesystem and server APIs.
