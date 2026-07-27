# Call Stack

The **Call Stack** is the mechanism JavaScript uses to keep track of **currently executing function calls**.

JavaScript executes synchronous JavaScript on a single call stack.

When a function is called:

```text
Its execution is pushed onto the Call Stack.
```

When the function finishes:

```text
Its execution is popped from the Call Stack.
```

The Call Stack follows:

```text
LIFO

Last In
First Out
```

Basic mental model:

```text
Function called
      ↓
Push onto stack
      ↓
Execute function
      ↓
Function completes
      ↓
Pop from stack
      ↓
Resume caller
```

---

# 1. What Is a Stack?

A **stack** is a data structure where items are added and removed from the same end.

Think of a stack of plates:

```text
        ┌─────────┐
        │ Plate 3 │ ← remove first
        ├─────────┤
        │ Plate 2 │
        ├─────────┤
        │ Plate 1 │
        └─────────┘
```

Plate 3 was added last.

Plate 3 is removed first.

Therefore:

```text
Last In
First Out
```

or:

```text
LIFO
```

---

# 2. Push and Pop

Two important stack operations are:

```text
Push
Pop
```

**Push** means:

```text
Add something to the top.
```

**Pop** means:

```text
Remove something from the top.
```

Conceptually:

```text
Initial:

┌───────┐
│   A   │
└───────┘


Push B:

┌───────┐
│   B   │ ← top
├───────┤
│   A   │
└───────┘


Pop:

┌───────┐
│   A   │ ← top
└───────┘
```

The JavaScript call stack follows the same principle.

---

# 3. Call Stack and Function Calls

Consider:

```javascript
function greet() {
    console.log("Hello");
}

greet();
```

Conceptually, execution starts with global code:

```text
┌─────────────────┐
│ Global Execution│
└─────────────────┘
```

When:

```javascript
greet();
```

runs:

```text
┌─────────────────┐
│      greet      │ ← top
├─────────────────┤
│ Global Execution│
└─────────────────┘
```

After `greet()` finishes:

```text
┌─────────────────┐
│ Global Execution│
└─────────────────┘
```

Execution resumes after the call to `greet()`.

---

# 4. Execution Context and Call Stack

From the previous topic, we know:

```text
Function called
      ↓
Function Execution Context created
```

The call stack tracks the active execution of that function.

Conceptually:

```text
Execution Context
      ↓
Runtime state for executing code

Call Stack
      ↓
Tracks active calls/contexts
```

They are related, but they are not the same concept.

---

# 5. Simple Execution Example

```javascript
console.log("Start");

function greet() {
    console.log("Hello");
}

greet();

console.log("End");
```

Execution:

```text
Global execution begins
        ↓
console.log("Start")
        ↓
greet()
        ↓
greet executes
        ↓
greet completes
        ↓
Global execution resumes
        ↓
console.log("End")
```

Output:

```text
Start
Hello
End
```

---

# 6. Nested Function Calls

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

The calls happen in this order:

```text
first()
   ↓
second()
   ↓
third()
```

But they finish in the opposite order:

```text
third()
   ↓
second()
   ↓
first()
```

This happens because of:

```text
LIFO
```

---

# 7. Nested Call Stack Step by Step

Initially:

```text
┌─────────────┐
│   Global    │
└─────────────┘
```

Call:

```javascript
first();
```

Stack:

```text
┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`first()` calls:

```javascript
second();
```

Stack:

```text
┌─────────────┐
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`second()` calls:

```javascript
third();
```

Stack:

```text
┌─────────────┐
│    third    │ ← currently executing
├─────────────┤
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`third()` finishes:

```text
┌─────────────┐
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`second()` finishes:

```text
┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`first()` finishes:

```text
┌─────────────┐
│   Global    │
└─────────────┘
```

Finally, global execution finishes.

---

# 8. The Top of the Stack

The function at the top of the call stack is the one whose JavaScript code is currently executing.

Example:

```text
┌─────────────┐
│    third    │ ← currently executing
├─────────────┤
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘
```

`first()` and `second()` have not disappeared.

They are waiting for the function they called to finish.

---

# 9. Returning to the Caller

Consider:

```javascript
function multiply(a, b) {
    return a * b;
}

function calculate() {
    const result = multiply(5, 10);

    return result + 20;
}

console.log(calculate());
```

When `calculate()` calls:

```javascript
multiply(5, 10);
```

`calculate()` cannot continue this statement until `multiply()` returns.

Stack:

```text
┌─────────────┐
│  multiply   │
├─────────────┤
│  calculate  │
├─────────────┤
│   Global    │
└─────────────┘
```

`multiply()` returns:

```text
50
```

Then it leaves the stack:

```text
┌─────────────┐
│  calculate  │
├─────────────┤
│   Global    │
└─────────────┘
```

`calculate()` continues:

```javascript
return result + 20;
```

Result:

```text
70
```

---

# 10. Caller and Callee

Suppose:

```javascript
function first() {
    second();
}
```

Here:

```text
first
↓
Caller

second
↓
Callee
```

Because:

```text
first calls second
```

When `second()` completes, execution returns to:

```text
first()
```

---

# 11. Synchronous Execution

Normal JavaScript statements execute synchronously.

Consider:

```javascript
function taskOne() {
    console.log("One");
}

function taskTwo() {
    console.log("Two");
}

taskOne();
taskTwo();
```

Output:

```text
One
Two
```

Execution:

```text
taskOne()
↓
finish taskOne()

taskTwo()
↓
finish taskTwo()
```

`taskTwo()` does not start while synchronous execution of `taskOne()` is still active.

---

# 12. Long-Running Synchronous Code

Consider:

```javascript
console.log("Start");

for (let i = 0; i < 1_000_000_000; i++) {
    // expensive synchronous work
}

console.log("End");
```

The loop occupies JavaScript execution until it finishes.

Therefore:

```text
Start
↓
Long synchronous loop
↓
End
```

This is called:

```text
Blocking
```

because subsequent JavaScript cannot execute on that call stack until the current synchronous work completes.

---

# 13. Why Blocking Matters in Browsers

In a browser, JavaScript execution is closely integrated with the browser's main-thread work.

Long synchronous JavaScript can delay things such as:

```text
User interaction handling
Rendering updates
Other JavaScript callbacks
```

For example:

```javascript
function expensiveTask() {
    const start = Date.now();

    while (Date.now() - start < 5000) {
        // blocking for about 5 seconds
    }
}

expensiveTask();
```

During that synchronous work, the page can appear unresponsive.

---

# 14. Call Stack and Recursion

Recursion happens when a function calls itself.

Example:

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

Each call is a separate function invocation.

---

# 15. Recursive Call Stack

At the deepest point:

```text
┌────────────────┐
│ countdown(0)   │
├────────────────┤
│ countdown(1)   │
├────────────────┤
│ countdown(2)   │
├────────────────┤
│ countdown(3)   │
├────────────────┤
│ Global         │
└────────────────┘
```

`countdown(0)` returns.

Then:

```text
countdown(1)
```

returns.

Then:

```text
countdown(2)
```

returns.

Then:

```text
countdown(3)
```

returns.

Again:

```text
LIFO
```

---

# 16. Base Case in Recursion

A recursive function needs a condition that stops recursion.

Example:

```javascript
function countdown(n) {
    if (n === 0) {
        return;
    }

    countdown(n - 1);
}
```

This:

```javascript
if (n === 0) {
    return;
}
```

is the:

```text
Base Case
```

Without it, recursion may continue until the call stack limit is exceeded.

---

# 17. Stack Overflow

Consider:

```javascript
function test() {
    test();
}

test();
```

The function continuously calls itself:

```text
test()
 ↓
test()
 ↓
test()
 ↓
test()
 ↓
...
```

The stack keeps growing.

Eventually, the JavaScript engine reaches its call stack limit.

This is:

```text
Stack Overflow
```

---

# 18. Stack Overflow Error

Depending on the JavaScript engine, you may see an error such as:

```text
RangeError: Maximum call stack size exceeded
```

The exact message can differ between environments.

The underlying problem is:

```text
Too many nested active calls
```

---

# 19. Stack Overflow Is Not Only Infinite Recursion

Infinite recursion is a common cause:

```javascript
function test() {
    test();
}
```

But sufficiently deep finite recursion can also overflow the stack.

Example conceptually:

```javascript
recursiveFunction(1_000_000);
```

Even if it eventually has a base case, the recursion depth may exceed the engine's available stack capacity before reaching it.

---

# 20. Mutual Recursion

Functions can recursively call each other.

Example:

```javascript
function a() {
    b();
}

function b() {
    a();
}

a();
```

Flow:

```text
a()
↓
b()
↓
a()
↓
b()
↓
a()
↓
...
```

Unless there is a terminating condition, this eventually causes stack overflow.

---

# 21. Stack Frames

You will often hear the term:

```text
Stack Frame
```

A stack frame represents information associated with an active function call.

Conceptually it can involve things needed to execute and return from the call.

For learning purposes:

```text
Call Stack
│
├── Stack Frame → current function
├── Stack Frame → caller
└── Stack Frame → earlier caller
```

Do not assume a JavaScript engine literally stores the exact high-level structures shown in educational diagrams. Engines optimize their internal implementation.

---

# 22. Call Stack Example with Return Values

```javascript
function add(a, b) {
    return a + b;
}

function square(x) {
    return x * x;
}

function calculate() {
    const sum = add(2, 3);

    return square(sum);
}

console.log(calculate());
```

Execution:

```text
calculate()
     ↓
add(2, 3)
     ↓
returns 5
     ↓
calculate resumes
     ↓
square(5)
     ↓
returns 25
     ↓
calculate resumes
     ↓
returns 25
```

Output:

```text
25
```

---

# 23. Stack Evolution for the Previous Example

Call:

```javascript
calculate();
```

```text
┌─────────────┐
│  calculate  │
├─────────────┤
│   Global    │
└─────────────┘
```

Then:

```javascript
add(2, 3);
```

```text
┌─────────────┐
│     add     │
├─────────────┤
│  calculate  │
├─────────────┤
│   Global    │
└─────────────┘
```

`add()` returns.

Then:

```javascript
square(5);
```

```text
┌─────────────┐
│   square    │
├─────────────┤
│  calculate  │
├─────────────┤
│   Global    │
└─────────────┘
```

`square()` returns.

Then `calculate()` returns.

---

# 24. Nested Expressions and the Call Stack

Consider:

```javascript
function add(a, b) {
    return a + b;
}

function multiply(a, b) {
    return a * b;
}

const result =
    multiply(
        add(2, 3),
        add(4, 5)
    );
```

JavaScript must evaluate the arguments before invoking `multiply` with their resulting values.

Conceptually:

```text
add(2, 3)
↓
5

add(4, 5)
↓
9

multiply(5, 9)
↓
45
```

Result:

```text
45
```

---

# 25. Error Stack Traces

Consider:

```javascript
function first() {
    second();
}

function second() {
    third();
}

function third() {
    throw new Error("Something failed");
}

first();
```

The runtime can produce a stack trace showing the chain of calls involved.

Conceptually:

```text
Error: Something failed
    at third (...)
    at second (...)
    at first (...)
    ...
```

This tells you approximately:

```text
Where the error occurred
↓
Which function called it
↓
Which function called that
↓
...
```

---

# 26. Why Stack Traces Are Useful

Suppose:

```text
saveUser()
    ↓
validateUser()
    ↓
validateEmail()
    ↓
Error
```

A stack trace can help identify:

```text
Error originated in validateEmail

validateEmail was called by validateUser

validateUser was called by saveUser
```

This is extremely useful when debugging applications.

---

# 27. Reading a Stack Trace

Example:

```javascript
function processOrder() {
    calculatePrice();
}

function calculatePrice() {
    applyDiscount();
}

function applyDiscount() {
    throw new Error("Invalid discount");
}

processOrder();
```

A stack trace may conceptually show:

```text
Error: Invalid discount
    at applyDiscount
    at calculatePrice
    at processOrder
```

Start with:

```text
Where did the error originate?
```

Then follow the call chain outward.

---

# 28. Call Stack and `console.log`

Consider:

```javascript
function test() {
    console.log("Hello");
}

test();
```

While `test()` executes, it invokes `console.log`.

Conceptually, nested calls occur as JavaScript interacts with the host-provided console functionality.

The important lesson is:

```text
Functions can call other functions
↓
Nested calls are tracked
↓
Control returns to the caller afterward
```

---

# 29. Call Stack and Asynchronous JavaScript

Now consider:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 0);

console.log("End");
```

Output:

```text
Start
End
Timer
```

At first glance, you might expect:

```text
Start
Timer
End
```

because the timeout is:

```text
0 milliseconds
```

But `setTimeout()` does **not** mean:

```text
Execute this callback immediately.
```

The callback is scheduled through the runtime.

---

# 30. Why `setTimeout(..., 0)` Runs Later

At a high level:

```text
Global JavaScript executing
        ↓
setTimeout registers timer work
        ↓
JavaScript continues
        ↓
console.log("End")
        ↓
Current synchronous execution completes
        ↓
Timer callback can later become eligible
for execution through the event loop
```

Therefore:

```text
Start
End
Timer
```

The call stack is only one piece of asynchronous JavaScript.

---

# 31. The Call Stack Does Not Manage Timers by Itself

The JavaScript engine's call stack does not independently provide browser timer functionality.

In a browser, APIs such as:

```javascript
setTimeout()
```

are provided by the host environment.

In Node.js, timer functionality is provided by the Node.js runtime.

So conceptually:

```text
JavaScript Engine
│
└── Call Stack

Runtime / Host Environment
│
├── Timers
├── I/O capabilities
├── Queues
└── Event-loop machinery
```

The details differ between browser and Node.js runtimes.

---

# 32. Call Stack + Runtime

A simplified browser model:

```text
┌───────────────────────────────┐
│       JavaScript Engine       │
│                               │
│          Call Stack           │
└───────────────────────────────┘

              ↕
              
┌───────────────────────────────┐
│        Browser Runtime        │
│                               │
│ Timers                        │
│ DOM                           │
│ Networking                    │
│ Event handling                │
└───────────────────────────────┘

              ↕

┌───────────────────────────────┐
│       Queues + Event Loop     │
└───────────────────────────────┘
```

We'll study this properly in the **JavaScript Runtime** and **Event Loop** topics.

---

# 33. Call Stack and Callbacks

Consider:

```javascript
function process(callback) {
    callback();
}

function done() {
    console.log("Done");
}

process(done);
```

This callback is synchronous.

Execution:

```text
process()
   ↓
callback()
   ↓
done()
```

Stack at the deepest point:

```text
┌─────────────┐
│    done     │
├─────────────┤
│   process   │
├─────────────┤
│   Global    │
└─────────────┘
```

Important:

```text
Callback does NOT automatically mean asynchronous.
```

A callback is simply a function passed to another function to be invoked.

---

# 34. Synchronous Callback Example

```javascript
const numbers = [1, 2, 3];

numbers.forEach(number => {
    console.log(number);
});

console.log("Done");
```

Output:

```text
1
2
3
Done
```

The `forEach` callback executes synchronously as part of the `forEach()` operation.

---

# 35. Asynchronous Callback Example

```javascript
setTimeout(() => {
    console.log("Timer");
}, 1000);

console.log("Done");
```

Output:

```text
Done
Timer
```

Here, the callback is scheduled to execute later.

So:

```text
Callbacks
├── Synchronous
└── Asynchronous
```

Do not equate callbacks with asynchronous behavior.

---

# 36. Call Stack and Promises

Consider:

```javascript
console.log("Start");

Promise.resolve().then(() => {
    console.log("Promise");
});

console.log("End");
```

Output:

```text
Start
End
Promise
```

The `.then()` handler does not execute immediately during the current synchronous stack.

Promise reactions are scheduled as:

```text
Microtasks
```

We'll study this under:

```text
Event Loop
```

---

# 37. Timer vs Promise

Consider:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});

console.log("End");
```

Typical browser/modern JavaScript output:

```text
Start
End
Promise
Timer
```

Why?

At a high level:

```text
Synchronous code
↓
Start
End

Microtasks
↓
Promise

Next eligible timer task
↓
Timer
```

Understanding this properly requires:

```text
Call Stack
+
Runtime
+
Task Queue
+
Microtask Queue
+
Event Loop
```

---

# 38. Empty Call Stack

You'll often hear:

```text
"The event loop waits until the call stack is empty."
```

This is a useful simplified model.

The core idea is:

```text
Currently executing synchronous JavaScript
must finish before queued callbacks can
begin executing on that same JavaScript thread.
```

Example:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

for (let i = 0; i < 1_000_000_000; i++) {
    // long synchronous operation
}
```

Even though the timer delay is `0`, the callback cannot interrupt the currently running synchronous loop.

---

# 39. `setTimeout(0)` Does Not Guarantee Zero Delay

This:

```javascript
setTimeout(callback, 0);
```

means roughly:

```text
Do not run the callback before the
timer becomes eligible according to
the runtime's timer rules.
```

It does not mean:

```text
Run exactly 0 ms later.
```

Actual execution depends on factors such as:

```text
Current JavaScript execution
Timer rules/minimum delays
Task scheduling
Other queued work
Runtime/environment
```

---

# 40. Run-to-Completion

A useful JavaScript execution principle is:

```text
Run to Completion
```

Once a JavaScript task starts executing, it generally continues until its synchronous work finishes before another queued task executes on the same thread.

Example:

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

Current synchronous execution completes:

```text
A
C
```

before the timer callback executes:

```text
B
```

---

# 41. Call Stack and Blocking

Suppose:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

const start = Date.now();

while (Date.now() - start < 3000) {
    // block for about 3 seconds
}
```

The timer may already be eligible while the loop is running.

But the callback cannot execute until the current synchronous JavaScript finishes.

So:

```text
Timer eligible
      ↓
Callback waiting
      ↓
Call stack busy
      ↓
Loop finishes
      ↓
Current task completes
      ↓
Callback may execute later
```

---

# 42. Why Heavy Synchronous Work Is Dangerous

Long-running synchronous work can block:

```text
Event handlers
Timers
Promise continuation progress
Rendering opportunities
Other application logic
```

This is why performance matters in frontend JavaScript.

Later, concepts such as:

```text
Code splitting
Web Workers
Debouncing
Throttling
Efficient rendering
```

help manage different performance problems.

---

# 43. Call Stack vs Heap

Do not confuse:

```text
Call Stack
```

with:

```text
Heap
```

At a high level:

```text
Call Stack
↓
Tracks active function execution/calls

Heap
↓
Memory region commonly associated
with dynamically allocated objects
and other runtime data
```

Example:

```javascript
const user = {
    name: "John"
};
```

The exact internal memory representation is engine-dependent.

Avoid oversimplifying this into:

```text
All primitives live on stack
All objects live on heap
```

JavaScript engines are free to optimize storage internally.

---

# 44. Call Stack vs Scope Chain

Call Stack:

```text
Which functions are currently active?
```

Scope Chain:

```text
Where should JavaScript search
for an identifier?
```

Example:

```javascript
const x = 10;

function outer() {
    const y = 20;

    function inner() {
        console.log(x + y);
    }

    inner();
}

outer();
```

Call stack during `inner()`:

```text
inner
outer
Global
```

Lexical lookup for `x`:

```text
inner scope
↓
outer scope
↓
global scope
↓
x found
```

They may look similar in a simple nested example, but they represent different concepts.

---

# 45. Call Stack Does Not Determine Lexical Scope

This is important.

Consider:

```javascript
const x = "Global";

function showX() {
    console.log(x);
}

function caller() {
    const x = "Caller";

    showX();
}

caller();
```

Output:

```text
Global
```

During `showX()` the call stack is conceptually:

```text
showX
caller
Global
```

But `showX` does **not** resolve `x` from `caller` merely because `caller` called it.

`showX` was lexically defined in the global scope.

Therefore it resolves:

```text
x → "Global"
```

This proves:

```text
Call Stack ≠ Scope Chain
```

---

# 46. Lexical Scope vs Call Relationship

Consider:

```javascript
function outer() {
    const message = "Hello";

    return function inner() {
        console.log(message);
    };
}

const fn = outer();

function execute() {
    fn();
}

execute();
```

When `fn()` runs, the call stack is:

```text
inner
execute
Global
```

But `inner` gets:

```text
message
```

from the lexical environment of:

```text
outer
```

even though `outer()` is no longer on the call stack.

This is closure.

Again:

```text
Call Stack
↓
Who called whom / active execution

Lexical Environment
↓
Where the function was defined
and which outer bindings it can access
```

---

# 47. Call Stack and `return`

Consider:

```javascript
function test() {
    console.log("A");

    return;

    console.log("B");
}

test();
```

Output:

```text
A
```

When `return` executes:

```text
Function execution ends
↓
Function call completes
↓
Its frame/context leaves the stack
↓
Caller resumes
```

Anything after that `return` in the same execution path is not executed.

---

# 48. Return Value Goes Back to Caller

```javascript
function getNumber() {
    return 10;
}

const number = getNumber();
```

Flow:

```text
Global calls getNumber()
        ↓
getNumber pushed
        ↓
returns 10
        ↓
getNumber popped
        ↓
Global receives 10
        ↓
number = 10
```

---

# 49. Exceptions and the Call Stack

Consider:

```javascript
function first() {
    second();
}

function second() {
    throw new Error("Failed");
}

first();
```

When the error is thrown, JavaScript looks for an applicable error handler while unwinding through active calls.

If no handler catches the error, it propagates outward.

---

# 50. Stack Unwinding

Example:

```javascript
function a() {
    b();
}

function b() {
    c();
}

function c() {
    throw new Error("Failed");
}

a();
```

At error time:

```text
c
b
a
Global
```

If the error is not caught, frames are unwound as the exception propagates outward.

This process is commonly called:

```text
Stack Unwinding
```

---

# 51. Catching During Unwinding

```javascript
function first() {
    try {
        second();
    } catch (error) {
        console.log("Caught");
    }
}

function second() {
    throw new Error("Failed");
}

first();
```

Flow:

```text
first()
↓
second()
↓
throw Error
↓
second cannot handle it
↓
propagates to first
↓
catch handles it
```

Output:

```text
Caught
```

We'll cover this in detail under:

```text
Error Handling
```

---

# 52. Call Stack in Debugging

Browser developer tools allow you to pause JavaScript execution.

When paused, you can inspect the:

```text
Call Stack
```

For example:

```text
calculateTotal
processCart
handleCheckout
```

This tells you:

```text
calculateTotal
was called by

processCart
which was called by

handleCheckout
```

This is extremely useful for debugging complex applications.

---

# 53. Breakpoints and the Call Stack

Suppose:

```javascript
function checkout() {
    calculateTotal();
}

function calculateTotal() {
    debugger;
}

checkout();
```

When execution reaches:

```javascript
debugger;
```

developer tools can pause execution.

The call stack may show:

```text
calculateTotal
checkout
(global)
```

You can then inspect:

```text
Local variables
Function arguments
Callers
Current source location
```

---

# 54. Call Stack and React

Later in React you might have:

```jsx
function Button() {
    function handleClick() {
        console.log("Clicked");
    }

    return (
        <button onClick={handleClick}>
            Click
        </button>
    );
}
```

When the browser/React event system eventually invokes:

```javascript
handleClick()
```

that function executes as a JavaScript call and participates in the call stack like other JavaScript functions.

React does not replace JavaScript's call stack.

It operates using JavaScript's runtime model.

---

# 55. Call Stack and API Requests

Later you may write:

```javascript
async function getUsers() {
    const response =
        await fetch("/api/users");

    return response.json();
}
```

It would be incorrect to imagine `getUsers` simply occupying the call stack while waiting for the network response.

Asynchronous operations involve the runtime, promises, queues, and later continuation of JavaScript execution.

We'll understand this after:

```text
JavaScript Runtime
Event Loop
Promises
Async / Await
```

---

# 56. Call Stack Mental Model

Consider:

```javascript
function one() {
    two();
}

function two() {
    three();
}

function three() {
    console.log("Hello");
}

one();
```

Think:

```text
START
====================

Global


CALL one()
====================

one
Global


CALL two()
====================

two
one
Global


CALL three()
====================

three
two
one
Global


three RETURNS
====================

two
one
Global


two RETURNS
====================

one
Global


one RETURNS
====================

Global


GLOBAL COMPLETES
====================

Done
```

---

# 57. LIFO Mental Model

```text
CALL ORDER
====================

first
↓
second
↓
third


RETURN ORDER
====================

third
↓
second
↓
first
```

Therefore:

```text
Last called
↓
First to return
```

assuming normal nested synchronous calls.

---

# 58. Call Stack vs Queue

A stack follows:

```text
LIFO

Last In
First Out
```

A queue generally follows:

```text
FIFO

First In
First Out
```

Conceptually:

```text
STACK
====================

      ↓ Push
┌──────────┐
│    C     │ ← Pop
├──────────┤
│    B     │
├──────────┤
│    A     │
└──────────┘


QUEUE
====================

A → B → C → D

First added
↓
Normally first removed
```

This distinction becomes important when learning the event loop.

---

# 59. Call Stack vs Task Queue

Do not confuse:

```text
Call Stack
```

with:

```text
Task Queue
```

Call Stack:

```text
Tracks JavaScript currently executing.
```

Task Queue:

```text
Contains tasks that may eventually
run when scheduling rules allow.
```

Simplified:

```text
Task Queue
    │
    │ Event Loop
    ▼
Call Stack
    │
    ▼
Execute
```

We'll refine this model later.

---

# 60. Call Stack vs Microtask Queue

Promise handlers are associated with:

```text
Microtasks
```

Example:

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});
```

The callback does not execute directly just because the promise is already resolved.

It is scheduled as a microtask.

At the appropriate microtask checkpoint, the callback can execute.

We will cover the exact ordering under:

```text
Event Loop
```

---

# 61. Common Mistake — Call Stack Stores All Variables

The call stack is primarily a model for tracking active execution/calls.

Do not reduce JavaScript memory management to:

```text
All variables are stored
on the call stack.
```

JavaScript engine memory implementations are more complicated and optimized.

We'll handle memory separately.

---

# 62. Common Mistake — Stack and Scope Are the Same

Incorrect:

```text
Call Stack = Scope Chain
```

Correct:

```text
Call Stack
↓
Active calls/execution

Scope Chain
↓
Lexical identifier lookup
```

A function's caller does not automatically become its lexical parent.

---

# 63. Common Mistake — Callback Means Async

Incorrect:

```text
Callback
=
Asynchronous function
```

Example:

```javascript
[1, 2, 3].map(number => {
    return number * 2;
});
```

The callback executes synchronously.

Callbacks can be:

```text
Synchronous
or
Asynchronous
```

depending on the API using them.

---

# 64. Common Mistake — `setTimeout(0)` Runs Immediately

Incorrect:

```javascript
setTimeout(callback, 0);
```

does not mean:

```text
Run callback immediately.
```

The callback is scheduled and cannot interrupt the currently executing synchronous JavaScript.

---

# 65. Common Mistake — Async Callback Remains on Stack While Waiting

Incorrect mental model:

```text
setTimeout callback
↓
sits on call stack for 1 second
```

No.

The runtime manages the timer.

The callback executes later when it is scheduled to run and JavaScript gets an opportunity to execute it.

---

# 66. Common Mistake — JavaScript Waits for Network Requests on the Stack

When using asynchronous network APIs, JavaScript does not normally keep a function actively blocking the call stack for the duration of the network request.

The runtime handles asynchronous work and JavaScript continues when appropriate.

This is fundamental to:

```text
Non-blocking asynchronous programming
```

---

# 67. Common Mistake — Recursion Reuses One Function Context

Incorrect:

```text
recursive()
↓
same function execution reused
```

Correct:

```text
recursive call #1
↓
new invocation

recursive call #2
↓
new invocation

recursive call #3
↓
new invocation
```

Each active invocation occupies its own position/frame in the call stack.

---

# 68. Common Mistake — Stack Overflow Means Memory Is Full

Stack overflow specifically means the call stack exceeded its available capacity/limit.

It does not necessarily mean:

```text
The computer has completely run out of RAM.
```

It usually indicates:

```text
Excessive call depth
```

often caused by uncontrolled recursion.

---

# 69. Interview Question — What Is the Call Stack?

**Answer:**

The call stack is the stack structure JavaScript uses to track active function calls and execution, determining which function is currently running and where execution should return when that function completes.

---

# 70. Interview Question — What Principle Does It Follow?

```text
LIFO
```

Meaning:

```text
Last In
First Out
```

---

# 71. Interview Question — What Happens When a Function Is Called?

Conceptually:

```text
Function called
↓
New function execution begins
↓
Its frame/context becomes active
on the call stack
↓
Function executes
```

---

# 72. Interview Question — What Happens When a Function Returns?

```text
Function completes
↓
Its call/frame leaves the stack
↓
Execution resumes in its caller
```

---

# 73. Interview Question — What Is a Stack Frame?

A stack frame is the runtime information associated with an active function invocation.

At a conceptual level, each active call occupies a frame/entry on the call stack.

---

# 74. Interview Question — What Is Stack Overflow?

A stack overflow occurs when nested calls exceed the call stack's available capacity.

A common example:

```javascript
function test() {
    test();
}

test();
```

---

# 75. Interview Question — Why Does Infinite Recursion Cause Stack Overflow?

Because each recursive call creates another active invocation before the previous one returns.

Therefore:

```text
Stack grows
↓
Stack grows
↓
Stack grows
↓
Limit reached
↓
Stack Overflow
```

---

# 76. Interview Question — What Is a Base Case?

A base case is the condition that stops recursive calls.

Example:

```javascript
if (n === 0) {
    return;
}
```

Without an appropriate terminating condition, recursion can continue indefinitely until the stack overflows.

---

# 77. Interview Question — Is JavaScript Synchronous?

JavaScript executes ordinary synchronous code sequentially on its call stack.

However, JavaScript runtimes provide asynchronous capabilities through mechanisms such as:

```text
Timers
Network APIs
Events
Promises
Queues
Event Loop
```

So applications can perform asynchronous workflows even though JavaScript execution itself uses the call stack.

---

# 78. Interview Question — Can `setTimeout` Interrupt Current JavaScript?

No.

Example:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

console.log("End");
```

Output:

```text
End
Timer
```

The timer callback executes later rather than interrupting the current synchronous execution.

---

# 79. Interview Question — Callback vs Call Stack?

A callback is:

```text
A function supplied to another operation
to be invoked according to that operation's logic.
```

When the callback actually executes, it executes as JavaScript and participates in the call stack.

---

# 80. Interview Question — What Is Stack Unwinding?

Stack unwinding is the process of exiting active function calls as control returns or as an exception propagates through callers.

It is especially important for:

```text
Exception handling
Stack traces
try / catch
```

---

# 81. Interview Question — Call Stack vs Scope Chain?

Call Stack:

```text
Tracks active function calls.
```

Scope Chain:

```text
Determines lexical identifier lookup.
```

They are not the same.

---

# 82. Interview Question — Does Caller Determine Scope?

No.

Example:

```javascript
const x = 10;

function show() {
    console.log(x);
}

function test() {
    const x = 20;

    show();
}

test();
```

Output:

```text
10
```

`show()` uses lexical scope based on where it was defined, not where it was called.

---

# 83. Interview Output Question

What is the output?

```javascript
function first() {
    console.log("First");

    second();

    console.log("First End");
}

function second() {
    console.log("Second");
}

first();
```

Output:

```text
First
Second
First End
```

Why?

```text
first starts
↓
first calls second
↓
second completes
↓
first resumes
```

---

# 84. Interview Output Question

What is the output?

```javascript
function a() {
    console.log("A1");

    b();

    console.log("A2");
}

function b() {
    console.log("B1");

    c();

    console.log("B2");
}

function c() {
    console.log("C");
}

a();
```

Output:

```text
A1
B1
C
B2
A2
```

Execution follows nested call/return order.

---

# 85. Interview Output Question

What happens?

```javascript
function test() {
    test();
}

test();
```

Eventually:

```text
Stack Overflow
```

typically represented by an error such as:

```text
RangeError: Maximum call stack size exceeded
```

---

# 86. Interview Output Question

What is the output?

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

console.log("C");
```

Output:

```text
A
C
B
```

---

# 87. Interview Output Question

What is the output?

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");
});

console.log("C");
```

Output:

```text
A
C
B
```

The promise handler executes as a microtask after current synchronous execution.

---

# 88. Interview Output Question

What is the typical output?

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});

console.log("Sync");
```

Output:

```text
Sync
Promise
Timer
```

We'll explain exactly why when studying the event loop.

---

# 89. Interview Output Question

What is the output?

```javascript
const x = "Global";

function show() {
    console.log(x);
}

function test() {
    const x = "Local";

    show();
}

test();
```

Output:

```text
Global
```

because lexical scope is based on where `show` was defined, not the active caller.

---

# 90. Full Mental Model

Consider:

```javascript
function first() {
    console.log("First");

    second();
}

function second() {
    console.log("Second");

    third();
}

function third() {
    console.log("Third");
}

first();
```

Execution:

```text
GLOBAL
│
│ first()
▼

┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘

first prints "First"

first calls second

┌─────────────┐
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘

second prints "Second"

second calls third

┌─────────────┐
│    third    │
├─────────────┤
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘

third prints "Third"

third returns

┌─────────────┐
│   second    │
├─────────────┤
│    first    │
├─────────────┤
│   Global    │
└─────────────┘

second returns

┌─────────────┐
│    first    │
├─────────────┤
│   Global    │
└─────────────┘

first returns

┌─────────────┐
│   Global    │
└─────────────┘

Global execution completes.
```

Output:

```text
First
Second
Third
```

---

# 91. Big Picture

```text
JavaScript Code
       │
       ▼
Execution Context
       │
       ▼
Call Stack
       │
       ├── Function A
       │
       ├── Function B
       │
       └── Function C
       │
       ▼
Current JavaScript Execution
```

For asynchronous JavaScript:

```text
                    JavaScript Runtime
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         ▼                 ▼                 ▼
    Call Stack          Host APIs          Queues
         │                                   │
         │                                   ▼
         │                              Event Loop
         │                                   │
         └───────────────────────────────────┘
```

The call stack explains:

```text
What JavaScript is executing now.
```

The event loop will explain:

```text
How asynchronous work gets another
opportunity to execute JavaScript later.
```

---

# 92. Cheat Sheet

```text
CALL STACK
================================

Tracks active function calls.


DATA STRUCTURE
================================

Stack


ORDER
================================

LIFO

Last In
First Out


FUNCTION CALL
================================

Call function
↓
Push frame/context
↓
Execute


FUNCTION RETURN
================================

Function finishes
↓
Pop
↓
Resume caller


TOP OF STACK
================================

Currently executing JavaScript call.


NESTED CALLS
================================

A()
↓
B()
↓
C()

Stack:

C
B
A
Global


RETURN ORDER
================================

C
↓
B
↓
A


RECURSION
================================

Function calls itself.

Each invocation creates
another active call.


BASE CASE
================================

Stops recursion.


STACK OVERFLOW
================================

Too many nested active calls.


SYNCHRONOUS CODE
================================

Runs sequentially and
can block further JavaScript.


CALLBACK
================================

Can be synchronous
or asynchronous.


setTimeout
================================

Schedules timer-related work.

Callback does not interrupt
current synchronous execution.


PROMISE HANDLER
================================

Runs later as a microtask,
not immediately inside the
current synchronous stack.


CALL STACK ≠ SCOPE CHAIN
================================

Call Stack:
Active calls.

Scope Chain:
Lexical variable lookup.


CALL STACK ≠ HEAP
================================

Call Stack:
Execution tracking.

Heap:
Runtime memory concept commonly
associated with allocated objects.


STACK TRACE
================================

Shows the chain of function calls
related to an execution/error.


STACK UNWINDING
================================

Active calls are exited as
control returns or an exception
propagates.
```

---

# 93. Important Rules to Remember

```text
1. The call stack tracks active JavaScript function calls.

2. It follows LIFO — Last In, First Out.

3. A function call adds an active call/frame to the stack.

4. A function completion removes that call/frame.

5. The top of the stack represents the currently executing call.

6. Nested functions create nested active calls.

7. The most recently called nested function normally completes first.

8. After a callee finishes, execution resumes in its caller.

9. Function return values are passed back to the caller.

10. Every recursive invocation creates another active call.

11. Recursion requires an appropriate terminating/base case.

12. Excessive recursion can cause stack overflow.

13. Infinite recursion is a common stack-overflow cause.

14. Finite but extremely deep recursion can also overflow the stack.

15. JavaScript synchronous code runs sequentially.

16. Long synchronous work can block other JavaScript work.

17. In browsers, long main-thread JavaScript can make the interface unresponsive.

18. Callbacks are not automatically asynchronous.

19. Array callbacks such as map/forEach are normally synchronous.

20. Timer callbacks are scheduled for later execution.

21. setTimeout(callback, 0) does not execute the callback immediately.

22. Timer callbacks cannot interrupt currently executing synchronous JavaScript.

23. Promise handlers execute through the microtask mechanism.

24. Promise handlers do not run synchronously merely because a promise is already resolved.

25. The call stack alone does not explain asynchronous JavaScript.

26. The runtime provides asynchronous capabilities.

27. The event loop coordinates opportunities for queued work to execute.

28. The call stack and scope chain are different concepts.

29. The call stack tracks active calls.

30. The scope chain controls lexical identifier lookup.

31. A function's caller does not determine its lexical scope.

32. Closures can reference environments belonging to functions no longer on the call stack.

33. The call stack and heap are different concepts.

34. JavaScript engine memory implementation is more complex than simple stack/heap rules.

35. Stack traces help identify function call chains.

36. Developer tools can display the current call stack.

37. Exceptions can propagate through active calls.

38. Exception propagation can cause stack unwinding.

39. try/catch can intercept a propagating exception.

40. Understanding the call stack is required to properly understand the event loop.
```

---

# 94. Call Stack in One Sentence

> The JavaScript call stack is a LIFO stack that tracks active function calls, placing a call on top when a function executes and removing it when the function completes so execution can resume in its caller.l
