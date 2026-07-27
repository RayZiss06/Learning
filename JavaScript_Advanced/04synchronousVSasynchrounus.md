#  Synchronous vs Asynchronous

JavaScript code can involve two broad execution patterns:

```text
Synchronous
Asynchronous
```

Understanding the difference is essential before learning:

```text
Event Loop
Promises
async / await
Fetch API
Timers
API Calls
React data fetching
```

---

# 1. What Is Synchronous JavaScript?

**Synchronous execution** means JavaScript executes the current sequence of operations in order, and a synchronous operation must finish before execution proceeds past it.

Example:

```javascript
console.log("First");
console.log("Second");
console.log("Third");
```

Output:

```text
First
Second
Third
```

Execution:

```text
First
  ↓
Second
  ↓
Third
```

Each statement completes before execution proceeds to the next statement.

---

# 2. Synchronous Function Execution

```javascript
function calculate() {
    console.log("Calculating");

    return 10 + 20;
}

console.log("Start");

const result = calculate();

console.log(result);

console.log("End");
```

Output:

```text
Start
Calculating
30
End
```

Execution:

```text
Start
↓
calculate()
↓
Calculating
↓
return 30
↓
result = 30
↓
30
↓
End
```

The caller waits for `calculate()` to return before continuing.

---

# 3. Synchronous Does Not Mean Slow

This is important.

```text
Synchronous
≠
Slow
```

Example:

```javascript
const result = 10 + 20;
```

This operation is synchronous and extremely fast.

Synchronous only describes the execution relationship:

```text
Current operation completes
↓
Next operation continues
```

---

# 4. Blocking

A **blocking operation** prevents the JavaScript thread from progressing to other JavaScript work while that operation is executing.

Example:

```javascript
console.log("Start");

const start = Date.now();

while (Date.now() - start < 5000) {
    // block for about 5 seconds
}

console.log("End");
```

Output:

```text
Start

[about 5 seconds]

End
```

During the loop:

```text
JavaScript thread
↓
Busy
↓
Other JavaScript cannot execute
```

---

# 5. Synchronous vs Blocking

These terms are related but not identical.

**Synchronous** describes execution ordering.

**Blocking** describes whether ongoing work prevents progress elsewhere on the same execution thread.

For normal JavaScript:

```javascript
const result = 10 + 20;
```

is synchronous but so fast that blocking is irrelevant.

This:

```javascript
while (true) {}
```

is synchronous **and severely blocking**.

---

# 6. Why Blocking Matters in Browsers

Browser application JavaScript commonly executes on the main thread.

Long synchronous JavaScript can delay:

```text
Click handlers
Keyboard handlers
Timers
Rendering opportunities
Animations
Other JavaScript
```

Example:

```javascript
button.addEventListener("click", () => {
    const start = Date.now();

    while (Date.now() - start < 10000) {
        // expensive synchronous work
    }
});
```

After clicking the button, the UI may appear frozen while the loop runs.

---

# 7. What Is Asynchronous JavaScript?

**Asynchronous programming** allows an operation to be initiated now while its completion is handled later instead of forcing the current JavaScript execution to synchronously wait for it.

Example:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer finished");
}, 2000);

console.log("End");
```

Output:

```text
Start
End

[approximately 2+ seconds later]

Timer finished
```

Execution conceptually:

```text
Start
↓
Register timer
↓
Continue JavaScript
↓
End
↓
Timer becomes ready later
↓
Callback executes later
```

---

# 8. Asynchronous Does Not Mean Random

Asynchronous code follows runtime scheduling rules.

It is not:

```text
Anything can execute
at any random time
```

Instead, execution depends on mechanisms such as:

```text
Operation completion
Tasks
Microtasks
Event Loop
Current JavaScript execution
Host scheduling rules
```

---

# 9. Why Do We Need Asynchronous Programming?

Applications constantly perform operations that take time.

Examples:

```text
API requests
Database operations
File operations
Timers
User interactions
Network communication
WebSocket messages
```

Imagine an API request takes 5 seconds.

A blocking design would behave like:

```text
Send request
↓
Wait 5 seconds
↓
Application cannot progress
↓
Receive response
↓
Continue
```

An asynchronous design allows:

```text
Send request
↓
Runtime handles waiting
│
├── Application continues
│
├── UI remains responsive
│
└── Other work can happen
│
▼
Response arrives
↓
Handle response later
```

---

# 10. Asynchronous Does Not Mean Faster

Suppose an API takes 3 seconds.

Using asynchronous JavaScript does not magically make the server respond in:

```text
0.5 seconds
```

The operation may still take 3 seconds.

The benefit is:

```text
JavaScript does not need to
block while merely waiting.
```

So:

```text
Async
≠
Operation becomes faster
```

Instead:

```text
Async
=
Waiting can happen without
blocking current JavaScript execution
```

---

# 11. Blocking vs Non-Blocking

## Blocking

```text
Operation starts
↓
JavaScript waits
↓
Operation finishes
↓
JavaScript continues
```

## Non-Blocking

```text
Operation starts
↓
JavaScript continues
↓
Operation progresses elsewhere
↓
Completion handled later
```

Example of asynchronous non-blocking behavior:

```javascript
setTimeout(() => {
    console.log("Done");
}, 1000);

console.log("Continue");
```

Output:

```text
Continue
Done
```

---

# 12. Sequential Execution

Sequential means operations happen one after another.

```javascript
function first() {
    console.log("First");
}

function second() {
    console.log("Second");
}

first();
second();
```

Output:

```text
First
Second
```

Flow:

```text
first()
↓
finish

second()
↓
finish
```

---

# 13. Sequential Asynchronous Operations

Asynchronous operations can also be intentionally sequential.

Example:

```javascript
async function process() {
    const user = await getUser();

    const orders = await getOrders(user.id);

    const details = await getOrderDetails(orders[0].id);

    return details;
}
```

Here:

```text
getUser()
↓
wait for result
↓
getOrders()
↓
wait for result
↓
getOrderDetails()
```

This makes sense if each operation depends on the previous result.

---

# 14. Unnecessary Sequential Waiting

Consider:

```javascript
async function loadData() {
    const users = await fetch("/api/users");

    const products = await fetch("/api/products");
}
```

If these requests are independent, this creates unnecessary sequential waiting.

Conceptually:

```text
Users request
████████████

             Products request
             ████████████████
```

Total time is approximately:

```text
Users request duration
+
Products request duration
```

---

# 15. Concurrent Asynchronous Operations

Independent asynchronous operations can often be started together.

Example:

```javascript
async function loadData() {
    const usersPromise =
        fetch("/api/users");

    const productsPromise =
        fetch("/api/products");

    const [users, products] =
        await Promise.all([
            usersPromise,
            productsPromise
        ]);
}
```

Conceptually:

```text
Users request
████████████

Products request
███████████████
```

Both are in progress during overlapping time.

This is:

```text
Concurrency
```

---

# 16. Sequential vs Concurrent Timing

Suppose:

```text
Request A = 2 seconds
Request B = 3 seconds
```

Sequential:

```text
A
████████

        B
        ████████████

Total ≈ 5 seconds
```

Concurrent:

```text
A
████████

B
████████████

Total ≈ 3 seconds
```

assuming the operations can genuinely progress concurrently and no other bottleneck dominates.

---

# 17. Concurrency

**Concurrency** means multiple activities can be in progress during overlapping periods.

Example:

```javascript
const request1 = fetch("/api/users");
const request2 = fetch("/api/products");
const request3 = fetch("/api/orders");
```

The requests can all be in progress without JavaScript synchronously waiting for each one before starting the next.

---

# 18. Parallelism

**Parallelism** means multiple operations actually execute simultaneously.

Conceptually:

```text
CPU Core 1
──────────────
Task A


CPU Core 2
──────────────
Task B
```

Therefore:

```text
Concurrency
≠
Parallelism
```

Concurrency is about overlapping progress.

Parallelism is about simultaneous execution.

---

# 19. Asynchronous vs Concurrent vs Parallel

```text
ASYNCHRONOUS
================================

Completion can happen later
without synchronously blocking
the caller while waiting.


CONCURRENT
================================

Multiple activities can be
in progress over overlapping time.


PARALLEL
================================

Multiple operations execute
simultaneously.
```

They describe different concepts.

---

# 20. JavaScript and Single-Threaded Execution

JavaScript application code commonly executes with one active call stack per JavaScript execution thread.

This means ordinary synchronous JavaScript:

```javascript
function first() {
    // work
}

function second() {
    // work
}

first();
second();
```

does not automatically execute `first()` and `second()` simultaneously.

They execute sequentially.

---

# 21. Single-Threaded Does Not Mean Runtime Is Single-Threaded

The surrounding environment may use multiple threads and system resources.

Browser:

```text
JavaScript execution
       │
       └── Main thread

Browser / OS
       │
       ├── Networking
       ├── Rendering infrastructure
       ├── I/O
       └── Other internal work
```

Node.js similarly has runtime infrastructure outside the JavaScript call stack.

Therefore:

```text
JavaScript execution commonly uses
a single active call stack
```

does not mean:

```text
The entire environment uses
only one thread.
```

---

# 22. Timer Example

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 1000);

console.log("C");
```

Output:

```text
A
C
B
```

The timer does not block:

```javascript
console.log("C");
```

---

# 23. `setTimeout(0)`

Consider:

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

Even with:

```text
0 ms
```

the callback does not execute synchronously.

---

# 24. Why `setTimeout(0)` Runs Later

Conceptually:

```text
Global JavaScript starts
↓
console.log("A")
↓
setTimeout registers timer
↓
console.log("C")
↓
Current synchronous task finishes
↓
Timer callback gets an opportunity later
↓
console.log("B")
```

Therefore:

```text
A
C
B
```

---

# 25. Timers Are Minimum-Delay Style Scheduling

This:

```javascript
setTimeout(callback, 1000);
```

does not guarantee:

```text
callback executes exactly
1000 ms later
```

Instead, after timer conditions are satisfied, the callback becomes eligible for later scheduling.

It may execute later because:

```text
JavaScript is busy
Other tasks exist
Runtime scheduling
Timer clamping/rules
```

---

# 26. Callback

A **callback** is a function passed to another operation so that the operation can invoke it.

Example:

```javascript
function greet(name, callback) {
    console.log(`Hello ${name}`);

    callback();
}

greet("John", () => {
    console.log("Finished");
});
```

Output:

```text
Hello John
Finished
```

This callback is synchronous.

---

# 27. Callbacks Are Not Automatically Async

Example:

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

The `forEach()` callback executes synchronously.

Therefore:

```text
Callback
≠
Async
```

---

# 28. Asynchronous Callback

Example:

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

Here, the callback executes later.

So callbacks can be:

```text
Callback
│
├── Synchronous
│
└── Asynchronous
```

---

# 29. Callback-Based Async Programming

Before promises became widely used, asynchronous JavaScript frequently used callback patterns.

Example:

```javascript
getUser(userId, user => {
    getOrders(user.id, orders => {
        getOrderDetails(
            orders[0].id,
            details => {
                console.log(details);
            }
        );
    });
});
```

This can become difficult to manage.

---

# 30. Callback Hell

Deeply nested callbacks are commonly called:

```text
Callback Hell
```

or:

```text
Pyramid of Doom
```

Example:

```javascript
first(() => {
    second(() => {
        third(() => {
            fourth(() => {
                fifth(() => {
                    console.log("Done");
                });
            });
        });
    });
});
```

Problems can include:

```text
Hard to read
Hard to maintain
Hard to reason about
Complex error handling
Deep nesting
```

Promises provide a better abstraction for many asynchronous workflows.

---

# 31. Promises

A **Promise** represents the eventual result of an asynchronous operation.

A promise can be:

```text
Pending
Fulfilled
Rejected
```

Example:

```javascript
fetch("/api/users")
    .then(response => {
        return response.json();
    })
    .then(users => {
        console.log(users);
    })
    .catch(error => {
        console.error(error);
    });
```

---

# 32. Promise States

```text
          Promise
             │
             ▼
          Pending
          /     \
         /       \
        ▼         ▼
 Fulfilled      Rejected
```

Once settled:

```text
Fulfilled
or
Rejected
```

a promise does not return to `pending`.

---

# 33. Promise Handlers Are Asynchronous

Consider:

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

Even though:

```javascript
Promise.resolve()
```

creates an already fulfilled promise, the `.then()` handler does not execute synchronously in the current call stack.

---

# 34. Promise Handlers and Microtasks

Promise reactions are scheduled as:

```text
Microtasks
```

So:

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});
```

schedules the handler to run later through microtask processing.

This becomes extremely important when comparing promises with timers.

---

# 35. Promise vs Timer

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

Output:

```text
Start
End
Promise
Timer
```

Execution groups:

```text
Synchronous
────────────
Start
End

Microtask
────────────
Promise

Later task
────────────
Timer
```

---

# 36. Why Promise Runs Before Timer

After the current synchronous task completes:

```text
Microtasks are processed
before the runtime moves
to the next task.
```

Therefore:

```text
Promise
```

executes before the timer task in this example.

We'll study the exact mechanism in the **Event Loop** topic.

---

# 37. `async` Functions

The `async` keyword declares an asynchronous function.

Example:

```javascript
async function getUser() {
    return {
        name: "John"
    };
}
```

Calling an async function returns a:

```text
Promise
```

Example:

```javascript
const result = getUser();

console.log(result);
```

`result` is a promise, not the object directly.

---

# 38. Async Functions Always Return Promises

```javascript
async function getNumber() {
    return 10;
}
```

Conceptually behaves like:

```javascript
function getNumber() {
    return Promise.resolve(10);
}
```

So:

```javascript
getNumber().then(value => {
    console.log(value);
});
```

Output:

```text
10
```

---

# 39. `await`

`await` is used to wait for a promise's result **inside an async function** without blocking the JavaScript thread in the way a synchronous busy wait would.

Example:

```javascript
async function getUsers() {
    const response =
        await fetch("/api/users");

    const users =
        await response.json();

    return users;
}
```

This gives asynchronous code a sequential-looking syntax.

---

# 40. `await` Does Not Block the JavaScript Thread

Consider:

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

If `await` synchronously blocked the JavaScript thread, `C` could not execute first.

---

# 41. What Happens at `await`?

Simplified:

```text
async function starts
↓
Runs synchronously
↓
await encountered
↓
Function execution suspends
↓
Caller continues
↓
Awaited promise settles
↓
Continuation scheduled
↓
Async function resumes later
```

Example:

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}
```

The code after `await` executes later.

---

# 42. Code Before First `await` Is Synchronous

```javascript
async function test() {
    console.log("A");
    console.log("B");

    await Promise.resolve();

    console.log("C");
}

console.log("Start");

test();

console.log("End");
```

Output:

```text
Start
A
B
End
C
```

Everything before the first suspension point runs synchronously when `test()` is called.

---

# 43. `await` Does Not Mean Start Async Work

This is subtle.

Consider:

```javascript
const promise = fetch("/api/users");

const response = await promise;
```

The asynchronous request starts when:

```javascript
fetch("/api/users");
```

is called.

`await` does not start the request.

It waits for the promise representing that operation.

---

# 44. Sequential `await`

```javascript
async function load() {
    const user =
        await getUser();

    const orders =
        await getOrders(user.id);

    return orders;
}
```

This is sequential because:

```text
getOrders
```

depends on:

```text
user.id
```

So sequential execution is appropriate.

---

# 45. Independent Sequential `await`

Consider:

```javascript
async function load() {
    const users =
        await getUsers();

    const products =
        await getProducts();
}
```

If these operations are independent:

```text
getUsers
↓
wait

getProducts
↓
wait
```

may be unnecessarily slow.

---

# 46. Start Independent Work Together

Better:

```javascript
async function load() {
    const usersPromise =
        getUsers();

    const productsPromise =
        getProducts();

    const [users, products] =
        await Promise.all([
            usersPromise,
            productsPromise
        ]);

    return {
        users,
        products
    };
}
```

Now both operations can progress concurrently.

---

# 47. `Promise.all()`

`Promise.all()` is useful when multiple independent asynchronous operations are all required.

Example:

```javascript
const [users, products, orders] =
    await Promise.all([
        getUsers(),
        getProducts(),
        getOrders()
    ]);
```

Conceptually:

```text
getUsers()     ───────────────┐
                              │
getProducts()  ────────────┐  │
                           │  │
getOrders()    ─────────┐  │  │
                        ▼  ▼  ▼
                     Promise.all
                         ↓
                  all fulfilled
                         ↓
                     continue
```

---

# 48. `Promise.all()` Failure Behavior

If any input promise rejects, the promise returned by `Promise.all()` rejects.

Example:

```javascript
try {
    const results = await Promise.all([
        getUsers(),
        getProducts(),
        getOrders()
    ]);
} catch (error) {
    console.error(error);
}
```

Important:

> Rejection of `Promise.all()` does not automatically cancel the other underlying operations.

They may continue unless cancellation is explicitly supported and used.

---

# 49. `Promise.allSettled()`

Sometimes you want all operations to finish even if some fail.

Example:

```javascript
const results =
    await Promise.allSettled([
        getUsers(),
        getProducts(),
        getOrders()
    ]);
```

Results contain statuses such as:

```javascript
{
    status: "fulfilled",
    value: ...
}
```

or:

```javascript
{
    status: "rejected",
    reason: ...
}
```

Use it when individual failures should not prevent collecting the results of the other operations.

---

# 50. `Promise.race()`

`Promise.race()` settles according to whichever input promise settles first.

```javascript
const result = await Promise.race([
    request(),
    timeout()
]);
```

The first fulfilled **or rejected** promise determines the returned promise's settlement.

---

# 51. `Promise.any()`

`Promise.any()` fulfills when the first input promise fulfills.

```javascript
const result = await Promise.any([
    server1(),
    server2(),
    server3()
]);
```

Rejected promises are ignored while another promise could still fulfill.

If all reject, `Promise.any()` rejects with an `AggregateError`.

---

# 52. Promise Methods Comparison

```text
Promise.all()
────────────────────

Need every operation.

Rejects when an input rejects.


Promise.allSettled()
────────────────────

Wait for every operation
regardless of success/failure.


Promise.race()
────────────────────

First settled promise wins.

Fulfilled OR rejected.


Promise.any()
────────────────────

First fulfilled promise wins.

Rejects only if all reject.
```

---

# 53. Fetch Example

```javascript
async function loadUsers() {
    const response =
        await fetch("/api/users");

    const users =
        await response.json();

    console.log(users);
}
```

Flow:

```text
Call loadUsers()
↓
Start fetch
↓
Wait asynchronously
↓
Other JavaScript can execute
↓
Response becomes available
↓
Resume function
↓
Parse response body
↓
Resume again
↓
Log users
```

---

# 54. Important `fetch()` Behavior

`fetch()` rejects for certain failures such as network-level failures.

But an HTTP response such as:

```text
404
500
```

normally still fulfills the fetch promise with a `Response`.

Therefore:

```javascript
const response =
    await fetch("/api/users");

if (!response.ok) {
    throw new Error(
        `HTTP error: ${response.status}`
    );
}
```

is often needed.

---

# 55. Async Error Handling with `try...catch`

```javascript
async function getUsers() {
    try {
        const response =
            await fetch("/api/users");

        if (!response.ok) {
            throw new Error(
                `Request failed: ${response.status}`
            );
        }

        const users =
            await response.json();

        return users;

    } catch (error) {
        console.error(
            "Failed to load users:",
            error
        );

        throw error;
    }
}
```

`try...catch` works naturally with `await`.

---

# 56. Promise Error Handling

Without `async/await`:

```javascript
fetch("/api/users")
    .then(response => {
        if (!response.ok) {
            throw new Error(
                `HTTP ${response.status}`
            );
        }

        return response.json();
    })
    .then(users => {
        console.log(users);
    })
    .catch(error => {
        console.error(error);
    });
```

Both styles are valid.

---

# 57. Throwing Inside an Async Function

```javascript
async function test() {
    throw new Error("Failed");
}
```

Calling:

```javascript
test();
```

returns a rejected promise.

It does not return the error directly.

Handle it with:

```javascript
test().catch(error => {
    console.error(error);
});
```

or:

```javascript
try {
    await test();
} catch (error) {
    console.error(error);
}
```

---

# 58. `return` Inside Async Function

```javascript
async function getValue() {
    return 100;
}
```

This produces a promise fulfilled with:

```text
100
```

Equivalent conceptually to:

```javascript
function getValue() {
    return Promise.resolve(100);
}
```

---

# 59. Returning a Promise from Async Function

```javascript
async function getValue() {
    return Promise.resolve(100);
}
```

The async function adopts the returned promise's eventual state.

Using it:

```javascript
const value = await getValue();

console.log(value);
```

Output:

```text
100
```

---

# 60. Forgetting `await`

Consider:

```javascript
async function load() {
    const users = getUsers();

    console.log(users);
}
```

If `getUsers()` returns a promise:

```text
users
```

contains a promise, not the resolved data.

Usually you want:

```javascript
const users =
    await getUsers();
```

---

# 61. But Not Every Promise Should Be Awaited Immediately

Sometimes you intentionally want to start multiple operations first:

```javascript
const usersPromise = getUsers();
const productsPromise = getProducts();

const users = await usersPromise;
const products = await productsPromise;
```

Because both operations started before the first `await`.

This can preserve concurrency.

---

# 62. Sequential vs Concurrent Example

## Sequential

```javascript
const users =
    await getUsers();

const products =
    await getProducts();
```

Timeline:

```text
Users
████████████

            Products
            ████████████
```

## Concurrent

```javascript
const usersPromise =
    getUsers();

const productsPromise =
    getProducts();

const [users, products] =
    await Promise.all([
        usersPromise,
        productsPromise
    ]);
```

Timeline:

```text
Users
████████████

Products
██████████████
```

---

# 63. Dependency Determines Strategy

Use sequential execution when:

```text
Operation B needs result of A.
```

Example:

```javascript
const user = await getUser();

const orders =
    await getOrders(user.id);
```

Use concurrent execution when:

```text
A and B are independent.
```

Example:

```javascript
const [users, products] =
    await Promise.all([
        getUsers(),
        getProducts()
    ]);
```

---

# 64. Real Application Example

Suppose a dashboard needs:

```text
User profile
Notifications
Products
Recent orders
```

If independent:

```javascript
const [
    profile,
    notifications,
    products,
    orders
] = await Promise.all([
    getProfile(),
    getNotifications(),
    getProducts(),
    getOrders()
]);
```

This can be much better than waiting for each request sequentially.

---

# 65. Dependent API Example

Suppose:

```text
Get logged-in user
↓
Need user's ID
↓
Get user's orders
↓
Need order ID
↓
Get order details
```

Then:

```javascript
const user =
    await getUser();

const orders =
    await getOrders(user.id);

const details =
    await getOrderDetails(
        orders[0].id
    );
```

Sequential execution is necessary because of data dependencies.

---

# 66. Async Loops — Common Mistake

Consider:

```javascript
items.forEach(async item => {
    await processItem(item);
});

console.log("Done");
```

A common mistake is assuming:

```text
Done
```

waits until every `processItem()` completes.

It does not.

`forEach()` does not await the promises returned by its callback.

---

# 67. Sequential Async Loop

If items must process one at a time:

```javascript
for (const item of items) {
    await processItem(item);
}

console.log("Done");
```

Flow:

```text
Item 1
↓
finish

Item 2
↓
finish

Item 3
↓
finish

Done
```

---

# 68. Concurrent Async Loop

If operations are independent:

```javascript
await Promise.all(
    items.map(item =>
        processItem(item)
    )
);

console.log("Done");
```

Now all operations are started and their promises are awaited together.

---

# 69. Sequential vs Concurrent Loop

Sequential:

```javascript
for (const item of items) {
    await processItem(item);
}
```

Use when:

```text
Order matters
Dependencies exist
Rate limiting is required
Resource constraints exist
```

Concurrent:

```javascript
await Promise.all(
    items.map(processItem)
);
```

Use when:

```text
Operations are independent
Concurrency is safe
Workload size is appropriate
```

Do not launch unlimited concurrent operations for extremely large collections without considering resource limits.

---

# 70. Async Does Not Solve CPU-Heavy Work

Consider:

```javascript
async function calculate() {
    let total = 0;

    for (
        let i = 0;
        i < 5_000_000_000;
        i++
    ) {
        total += i;
    }

    return total;
}
```

Adding:

```javascript
async
```

does not make the loop non-blocking.

The loop remains synchronous CPU work.

So:

```text
async function
≠
Run function on another thread
```

---

# 71. `await` Does Not Make CPU Work Async

This:

```javascript
async function calculate() {
    const result = expensiveCalculation();

    return result;
}
```

still executes:

```javascript
expensiveCalculation();
```

synchronously.

If it takes 10 seconds, JavaScript can still be blocked for 10 seconds.

---

# 72. CPU-Bound vs I/O-Bound

## CPU-Bound

Work dominated by computation.

Examples:

```text
Large calculations
Image processing
Data transformation
Compression
Cryptography
```

## I/O-Bound

Work dominated by waiting for external operations.

Examples:

```text
Network requests
Database queries
File reads
External services
```

Asynchronous programming is especially useful for I/O-bound workflows.

---

# 73. Web Workers

For CPU-heavy work in browsers, one option is:

```text
Web Workers
```

They allow JavaScript to execute on a separate worker thread.

Conceptually:

```text
Main Thread
│
├── UI
├── Events
└── React

Worker Thread
│
└── Heavy computation
```

Communication happens through messaging.

---

# 74. Worker Threads in Node.js

Node.js provides:

```text
Worker Threads
```

for cases where CPU-heavy JavaScript should execute on additional threads.

This is different from normal asynchronous I/O.

```text
Async I/O
≠
Worker Thread
```

---

# 75. Real-World Analogy

Imagine ordering food at a restaurant.

## Blocking

```text
Place order
↓
Stand at kitchen
↓
Wait 20 minutes
↓
Receive food
↓
Do anything else
```

## Asynchronous

```text
Place order
↓
Kitchen works
↓
You do something else
↓
Food becomes ready
↓
You are notified
↓
Collect food
```

The cooking did not necessarily become faster.

You simply did not block yourself while waiting.

---

# 76. Another Analogy — API Request

Think of sending an email.

You do not:

```text
Send email
↓
Freeze completely
↓
Wait until recipient replies
↓
Continue life
```

Instead:

```text
Send email
↓
Continue other work
↓
Reply arrives later
↓
Handle reply
```

That is similar to asynchronous programming.

---

# 77. Common Mistake — Async Means Faster

Incorrect:

```text
async
=
faster
```

Correct:

```text
async
=
do not unnecessarily block
while waiting for completion
```

Performance can improve because waiting periods overlap with other work, but the individual operation itself may not become faster.

---

# 78. Common Mistake — Async Means Parallel

Incorrect:

```text
Async
=
Multiple JS functions executing
simultaneously
```

Async does not automatically create additional JavaScript threads.

---

# 79. Common Mistake — `async` Makes Function Non-Blocking

```javascript
async function test() {
    expensiveCalculation();
}
```

`expensiveCalculation()` remains synchronous.

The `async` keyword mainly changes:

```text
Return behavior
+
Ability to use await
```

It does not move the entire function to another thread.

---

# 80. Common Mistake — `await` Blocks JavaScript

`await` suspends the async function's continuation.

It does not busy-wait and block the JavaScript thread until the promise resolves.

Example:

```javascript
async function test() {
    await delay(5000);
}

test();

console.log("Hello");
```

`Hello` can execute before the awaited delay completes.

---

# 81. Common Mistake — `await` Makes Everything Concurrent

Consider:

```javascript
const a = await getA();
const b = await getB();
```

This is sequential.

`getB()` is not even called until `getA()` has fulfilled successfully.

For independent operations:

```javascript
const [a, b] =
    await Promise.all([
        getA(),
        getB()
    ]);
```

may be more appropriate.

---

# 82. Common Mistake — `Promise.all()` Executes Functions

`Promise.all()` receives promises/values.

In:

```javascript
Promise.all([
    getUsers(),
    getProducts()
]);
```

the functions are called while constructing the array:

```javascript
getUsers()
getProducts()
```

Those calls start the operations.

Then their resulting promises are passed to `Promise.all()`.

---

# 83. Common Mistake — `Promise.all()` Cancels Others on Failure

If:

```javascript
getUsers()
```

rejects, `Promise.all()` rejects.

But:

```javascript
getProducts()
getOrders()
```

are not automatically cancelled.

Explicit cancellation mechanisms such as `AbortController` may be needed where supported.

---

# 84. Common Mistake — `fetch()` Rejects on Every HTTP Error

This:

```text
HTTP 404
HTTP 500
```

does not normally cause `fetch()` itself to reject.

Check:

```javascript
if (!response.ok) {
    throw new Error(
        `HTTP ${response.status}`
    );
}
```

---

# 85. Common Mistake — `forEach()` Waits for Async Callback

Incorrect:

```javascript
await items.forEach(async item => {
    await processItem(item);
});
```

`forEach()` itself returns `undefined`, not a promise representing completion of all callbacks.

Use:

```javascript
for (const item of items) {
    await processItem(item);
}
```

for sequential processing.

Or:

```javascript
await Promise.all(
    items.map(processItem)
);
```

for concurrent processing.

---

# 86. Common Mistake — Promise Callback Runs Immediately

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});

console.log("Sync");
```

Output:

```text
Sync
Promise
```

The `.then()` handler is scheduled as a microtask.

---

# 87. Common Mistake — Timer Delay Determines Exact Execution Time

```javascript
setTimeout(callback, 1000);
```

does not guarantee:

```text
callback executes exactly at 1000 ms
```

The callback must still wait until runtime scheduling allows it to execute.

---

# 88. Common Mistake — Async Code Has No Order

Async code absolutely has ordering rules.

For example:

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");
});

console.log("C");
```

always follows the relevant language/runtime scheduling rules.

Output:

```text
A
C
B
```

It is not random.

---

# 89. Interview Question — Synchronous vs Asynchronous?

**Synchronous:**

```text
Current operation completes
before execution proceeds past it.
```

**Asynchronous:**

```text
An operation can complete later,
allowing current JavaScript execution
to continue instead of synchronously
waiting for completion.
```

---

# 90. Interview Question — Blocking vs Non-Blocking?

**Blocking:**

An operation prevents further work on the relevant execution thread until it completes.

**Non-blocking:**

The operation can be initiated without requiring the current execution thread to wait for its completion.

---

# 91. Interview Question — Is JavaScript Single-Threaded?

Ordinary JavaScript execution uses one call stack per JavaScript execution thread.

In browser applications, much JavaScript executes on the main thread.

However, the surrounding runtime can use multiple threads/processes internally, and mechanisms such as Web Workers can provide additional JavaScript execution threads.

---

# 92. Interview Question — Is Async JavaScript Multi-Threaded?

Not necessarily.

Asynchronous programming does not automatically mean JavaScript executes on multiple threads.

The runtime can handle waiting for external operations while JavaScript continues executing other work.

---

# 93. Interview Question — What Does `async` Do?

An `async` function:

```text
Always returns a Promise

Allows await inside its body
```

Example:

```javascript
async function getValue() {
    return 10;
}
```

returns a promise fulfilled with `10`.

---

# 94. Interview Question — What Does `await` Do?

`await` pauses the continuation of an async function until the awaited value/promise is resolved appropriately.

It does not block the JavaScript thread while waiting.

The function resumes later according to promise/microtask scheduling.

---

# 95. Interview Question — Why Use `Promise.all()`?

Use `Promise.all()` when multiple independent asynchronous operations can run concurrently and all results are required.

Example:

```javascript
const [users, products] =
    await Promise.all([
        getUsers(),
        getProducts()
    ]);
```

---

# 96. Interview Question — `Promise.all()` vs `allSettled()`?

`Promise.all()`:

```text
Rejects if an input rejects.
```

`Promise.allSettled()`:

```text
Waits for all inputs and reports
the result of each one.
```

---

# 97. Interview Question — `Promise.race()` vs `Promise.any()`?

`Promise.race()`:

```text
First settled promise wins.

Success OR failure.
```

`Promise.any()`:

```text
First fulfilled promise wins.

Rejects only if all reject.
```

---

# 98. Interview Question — Why Is `await` Inside `forEach` Problematic?

Because `forEach()` does not wait for promises returned by its callback.

Therefore:

```javascript
items.forEach(async item => {
    await processItem(item);
});
```

does not represent completion of all `processItem()` calls.

Use `for...of` or `Promise.all()` depending on whether processing should be sequential or concurrent.

---

# 99. Output Question

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

# 100. Output Question

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

---

# 101. Output Question

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

---

# 102. Output Question

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

---

# 103. Output Question

```javascript
async function test() {
    return 10;
}

console.log(test());
```

The result is a:

```text
Promise
```

not the number `10` directly.

To obtain the fulfilled value:

```javascript
test().then(value => {
    console.log(value);
});
```

or:

```javascript
const value = await test();
```

where `await` is valid.

---

# 104. Output Question

```javascript
async function test() {
    console.log("1");

    await Promise.resolve();

    console.log("2");

    await Promise.resolve();

    console.log("3");
}

console.log("4");

test();

console.log("5");
```

Output:

```text
4
1
5
2
3
```

Reason:

```text
Synchronous
────────────

4
1
5


First continuation
────────────

2


Second continuation
────────────

3
```

---

# 105. Output Question

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");
    })
    .then(() => {
        console.log("B");
    });

console.log("C");
```

Output:

```text
C
A
B
```

---

# 106. Output Question

```javascript
setTimeout(() => {
    console.log("Timer 1");
}, 0);

setTimeout(() => {
    console.log("Timer 2");
}, 0);

console.log("Sync");
```

In a normal browser example where the timers are registered in this order and both become eligible normally:

```text
Sync
Timer 1
Timer 2
```

The synchronous code executes first.

---

# 107. Output Question — Nested Promise

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");

    Promise.resolve().then(() => {
        console.log("C");
    });
});

console.log("D");
```

Output:

```text
A
D
B
C
```

The second promise handler is added as another microtask.

---

# 108. Output Question — Promise Inside Timer

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");

    Promise.resolve().then(() => {
        console.log("C");
    });

    console.log("D");
}, 0);

console.log("E");
```

Output:

```text
A
E
B
D
C
```

Inside the timer task:

```text
B
↓
schedule microtask C
↓
D
↓
timer callback finishes
↓
microtask C
```

---

# 109. Output Question — Timer Inside Promise

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("B");

    setTimeout(() => {
        console.log("C");
    }, 0);

    console.log("D");
});

console.log("E");
```

Output:

```text
A
E
B
D
C
```

The promise handler runs as a microtask.

The timer is scheduled as later task work.

---

# 110. Output Question — Multiple Awaits

```javascript
async function one() {
    console.log("A");

    await two();

    console.log("B");
}

async function two() {
    console.log("C");
}

console.log("D");

one();

console.log("E");
```

Output:

```text
D
A
C
E
B
```

`two()` runs synchronously until it returns its fulfilled promise.

`one()` suspends at `await`.

Its continuation runs later.

---

# 111. Synchronous vs Asynchronous Mental Model

```text
SYNCHRONOUS
================================

Task A
████████

        Task B
        ████████

                Task C
                ████████


ASYNCHRONOUS WAITING
================================

JavaScript starts A
████

A waits externally
    ......................

JavaScript continues B
    ████████

JavaScript continues C
            ████████

A becomes ready
                    ↓

Handle A result later
                    █████
```

---

# 112. Sequential vs Concurrent Async Mental Model

```text
SEQUENTIAL
================================

Request A
████████████

            Request B
            ████████████

                        Request C
                        ████████████


CONCURRENT
================================

Request A
████████████

Request B
████████████████

Request C
██████████
```

---

# 113. Complete Mental Model

```text
JavaScript Code
      │
      ▼
Synchronous Execution
      │
      ├── Statements
      ├── Function calls
      └── CPU work
      │
      ▼
Call Stack
      │
      │ starts async operation
      ▼
Runtime / Host APIs
      │
      ├── Timers
      ├── Networking
      ├── Events
      └── I/O
      │
      ▼
Operation completes
      │
      ▼
Scheduled Work
      │
      ├── Tasks
      └── Microtasks
      │
      ▼
Event Loop Scheduling
      │
      ▼
JavaScript Executes
Continuation / Callback
```

---

# 114. Cheat Sheet

```text
SYNCHRONOUS
================================

Current operation completes
before execution proceeds.


ASYNCHRONOUS
================================

Completion can happen later
without forcing current JS
to synchronously wait.


BLOCKING
================================

Prevents progress on the
relevant execution thread.


NON-BLOCKING
================================

Allows execution to continue
while completion happens later.


ASYNC ≠ FAST
================================

Async does not make the
underlying operation faster.


ASYNC ≠ PARALLEL
================================

Async does not automatically
create multiple JS threads.


CONCURRENCY
================================

Multiple activities progress
over overlapping time.


PARALLELISM
================================

Multiple operations execute
simultaneously.


CALLBACK
================================

Function supplied to another
operation for later/current use.

Can be synchronous or async.


PROMISE
================================

Represents eventual completion.

States:

Pending
Fulfilled
Rejected


ASYNC FUNCTION
================================

Always returns Promise.


AWAIT
================================

Suspends async-function
continuation until awaited
promise/value is ready.

Does not busy-wait/block
the JavaScript thread.


PROMISE.ALL
================================

Use for independent operations
when all results are required.


PROMISE.ALLSETTLED
================================

Wait for every operation,
including failures.


PROMISE.RACE
================================

First settlement wins.


PROMISE.ANY
================================

First fulfillment wins.


SEQUENTIAL
================================

await A
↓
await B
↓
await C


CONCURRENT
================================

Start A
Start B
Start C
↓
await together


CPU-BOUND
================================

Async keyword does not make
heavy computation non-blocking.


I/O-BOUND
================================

Async programming is highly
useful for waiting operations.


forEach + async
================================

forEach does not wait for
async callback promises.


Sequential loop:

for...of + await


Concurrent loop:

Promise.all(items.map(...))
```

---

# 115. Important Rules to Remember

```text
1. Normal JavaScript executes synchronously.

2. Synchronous execution follows program order.

3. Synchronous does not automatically mean slow.

4. Long synchronous operations can block JavaScript.

5. Blocking JavaScript can freeze browser UI.

6. Asynchronous operations can complete later.

7. JavaScript does not need to synchronously wait for async operations.

8. Async does not mean random execution.

9. Async does not automatically mean faster.

10. Async does not automatically mean parallel.

11. Concurrency and parallelism are different concepts.

12. Callbacks can be synchronous.

13. Callbacks can be asynchronous.

14. Callback does not mean async.

15. Deep callback nesting can create callback hell.

16. Promises represent eventual completion.

17. Promise states are pending, fulfilled, and rejected.

18. Promise handlers execute asynchronously.

19. Promise reactions are microtasks.

20. async functions always return promises.

21. Returning a value from async produces a fulfilled promise.

22. Throwing inside async produces a rejected promise.

23. await suspends the async function's continuation.

24. await does not busy-wait and block the JavaScript thread.

25. Code before the first suspension point runs synchronously.

26. Code after await executes later when continuation is scheduled.

27. await does not start the asynchronous operation.

28. Calling the async API starts the operation.

29. Sequential await can be necessary.

30. Sequential await can also cause unnecessary waiting.

31. Independent operations can often run concurrently.

32. Promise.all() is useful for independent operations.

33. Promise.all() rejects when an input rejects.

34. Promise.all() does not automatically cancel remaining work.

35. Promise.allSettled() collects every result.

36. Promise.race() uses the first settlement.

37. Promise.any() uses the first fulfillment.

38. forEach() does not wait for async callbacks.

39. for...of with await provides sequential processing.

40. Promise.all() with map can provide concurrent processing.

41. Unlimited concurrency can create resource problems.

42. CPU-heavy work remains blocking even inside async functions.

43. async does not move a function to another thread.

44. Web Workers can handle CPU-heavy work off the browser main thread.

45. Node.js Worker Threads can provide additional JS execution threads.

46. fetch is asynchronous.

47. HTTP 404/500 responses do not normally make fetch reject by themselves.

48. response.ok should often be checked.

49. try...catch works naturally with await.

50. Understanding async execution is required before understanding the Event Loop.
```

---

# Synchronous vs Asynchronous in One Sentence

> Synchronous JavaScript completes current operations before proceeding, while asynchronous programming allows operations to complete later so JavaScript can continue doing other work instead of blocking while waiting.

---
