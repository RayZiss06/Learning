# JavaScript — Async/Await

`async/await` is syntax built on top of **Promises** that allows asynchronous code to be written in a style that resembles synchronous code.

Instead of:

```javascript
getUser()
    .then(user => getOrders(user.id))
    .then(orders => console.log(orders))
    .catch(error => console.error(error));
```

we can write:

```javascript
async function loadData() {
    try {
        const user = await getUser();
        const orders = await getOrders(user.id);

        console.log(orders);
    } catch (error) {
        console.error(error);
    }
}
```

Both approaches use Promises.

`async/await` does **not** replace Promises.

```text
Promises
   │
   ▼
async / await
   │
   ▼
Cleaner syntax for working
with Promise-based operations
```

---

# 1. `async` Functions

An `async` function is declared using:

```javascript
async function functionName() {
    // code
}
```

Example:

```javascript
async function greet() {
    return "Hello";
}
```

Calling it:

```javascript
const result = greet();

console.log(result);
```

returns a:

```text
Promise
```

not the string directly.

---

# 2. Async Functions Always Return a Promise

This:

```javascript
async function getValue() {
    return 10;
}
```

behaves conceptually like:

```javascript
function getValue() {
    return Promise.resolve(10);
}
```

Usage:

```javascript
getValue().then(value => {
    console.log(value);
});
```

Output:

```text
10
```

Or:

```javascript
const value = await getValue();
```

---

# 3. Returning Different Values

## Normal value

```javascript
async function example() {
    return 100;
}
```

Result:

```text
Fulfilled Promise
value = 100
```

## Throwing an error

```javascript
async function example() {
    throw new Error("Failed");
}
```

Result:

```text
Rejected Promise
reason = Error
```

## Returning a Promise

```javascript
async function example() {
    return Promise.resolve(100);
}
```

The async function's returned Promise adopts that result.

---

# 4. `await`

`await` waits for the settlement of a Promise-like value and gives you its fulfillment result.

Example:

```javascript
async function example() {
    const value = await Promise.resolve(10);

    console.log(value);
}

example();
```

Output:

```text
10
```

---

# 5. `await` Does Not Block JavaScript

This is extremely important.

Consider:

```javascript
async function example() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}

example();

console.log("C");
```

Output:

```text
A
C
B
```

`await` does **not** block the entire JavaScript runtime.

Instead:

```text
example()
   ↓
A
   ↓
await
   ↓
Suspend this async function
   ↓
Caller continues
   ↓
C
   ↓
Async continuation runs later
   ↓
B
```

---

# 6. Code Before `await` Runs Synchronously

Example:

```javascript
async function test() {
    console.log("1");

    await Promise.resolve();

    console.log("2");
}

console.log("3");

test();

console.log("4");
```

Output:

```text
3
1
4
2
```

Why?

```text
console.log("3")
↓
test()
↓
console.log("1")
↓
await
↓
test() suspends
↓
console.log("4")
↓
continuation
↓
console.log("2")
```

---

# 7. Code After `await` Runs Later

Consider:

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}
```

Conceptually:

```text
Before await
────────────

Runs immediately.


After await
────────────

Continues asynchronously
through Promise/microtask
scheduling.
```

This is one of the most important rules for understanding `async/await`.

---

# 8. Awaiting an Already Fulfilled Promise

Even if the Promise is already fulfilled:

```javascript
async function test() {
    console.log("A");

    await Promise.resolve(10);

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

`await` still causes the async function to suspend and continue asynchronously.

---

# 9. Awaiting a Normal Value

You can write:

```javascript
async function test() {
    const value = await 10;

    console.log(value);
}

test();
```

Output:

```text
10
```

Conceptually:

```javascript
await 10
```

behaves similarly to awaiting a Promise resolved with `10`.

However, continuation after the `await` still occurs asynchronously.

---

# 10. Awaiting a Thenable

Because Promise resolution supports thenables:

```javascript
const thenable = {
    then(resolve) {
        resolve("Hello");
    }
};
```

You can:

```javascript
async function test() {
    const value = await thenable;

    console.log(value);
}

test();
```

Output:

```text
Hello
```

---

# 11. Awaiting a Rejected Promise

Consider:

```javascript
async function test() {
    const value = await Promise.reject(
        new Error("Failed")
    );

    console.log(value);
}
```

The rejected Promise causes the `await` expression to throw inside the async function.

Conceptually:

```text
await rejectedPromise
        ↓
Throw rejection reason
        ↓
Normal execution stops
```

---

# 12. Error Handling with `try...catch`

Example:

```javascript
async function test() {
    try {
        const value = await Promise.reject(
            new Error("Failed")
        );

        console.log(value);
    } catch (error) {
        console.log(error.message);
    }
}

test();
```

Output:

```text
Failed
```

This is one major advantage of `async/await`.

Asynchronous Promise failures can often be handled using familiar:

```javascript
try {
}
catch {
}
```

syntax.

---

# 13. Promise `.catch()` vs `try...catch`

Promise style:

```javascript
getUser()
    .then(user => {
        console.log(user);
    })
    .catch(error => {
        console.error(error);
    });
```

Async/await style:

```javascript
async function loadUser() {
    try {
        const user = await getUser();

        console.log(user);
    } catch (error) {
        console.error(error);
    }
}
```

Both are valid.

Choose based on readability and the structure of the operation.

---

# 14. `finally`

Async code can use normal `finally`:

```javascript
async function loadUser() {
    try {
        const user = await getUser();

        console.log(user);
    } catch (error) {
        console.error(error);
    } finally {
        console.log("Finished");
    }
}
```

`finally` executes whether the operation:

```text
Succeeds
or
Fails
```

Useful for:

```text
Stopping loading indicators
Cleanup
Resetting state
Closing resources
```

---

# 15. Real Loading Pattern

```javascript
async function loadUsers() {
    setLoading(true);

    try {
        const response =
            await fetch("/api/users");

        const users =
            await response.json();

        setUsers(users);
    } catch (error) {
        console.error(error);
    } finally {
        setLoading(false);
    }
}
```

Pattern:

```text
Start loading
     ↓
try
     ↓
Async work
     ↓
Success / Failure
     ↓
finally
     ↓
Stop loading
```

This pattern is common in frontend applications.

---

# 16. Error Propagation

Consider:

```javascript
async function getUser() {
    throw new Error("User failed");
}

async function load() {
    const user = await getUser();

    console.log(user);
}
```

`getUser()` returns a rejected Promise.

`await getUser()` throws inside `load()`.

Since `load()` does not catch the error, `load()` itself returns a rejected Promise.

```text
getUser()
↓
Rejected Promise
↓
await
↓
Throws inside load()
↓
load() rejects
```

---

# 17. Handling Error Outside

```javascript
async function load() {
    const user = await getUser();

    return user;
}

load().catch(error => {
    console.error(error);
});
```

This is perfectly valid.

An async function does not have to catch every error internally.

Sometimes the caller should handle it.

---

# 18. Re-Throwing Errors

Sometimes you want to:

```text
Catch
↓
Log
↓
Re-throw
```

Example:

```javascript
async function loadUser() {
    try {
        return await getUser();
    } catch (error) {
        console.error(
            "Failed to load user:",
            error
        );

        throw error;
    }
}
```

The caller can still handle it:

```javascript
loadUser().catch(error => {
    console.error(
        "Application error:",
        error
    );
});
```

---

# 19. Transforming Errors

You can also throw a more meaningful error:

```javascript
async function loadUser() {
    try {
        return await getUser();
    } catch (error) {
        throw new Error(
            "Unable to load user",
            { cause: error }
        );
    }
}
```

This can preserve the original error as:

```javascript
error.cause
```

while providing higher-level context.

---

# 20. Sequential `await`

Consider:

```javascript
async function load() {
    const user =
        await getUser();

    const orders =
        await getOrders(user.id);

    return orders;
}
```

Execution:

```text
getUser()
████████

        getOrders()
        █████████
```

This is sequential because:

```javascript
getOrders(user.id)
```

depends on the result of:

```javascript
getUser()
```

This is correct.

---

# 21. Sequential Operations Are Sometimes Necessary

Example:

```javascript
const token =
    await authenticate();

const user =
    await getUser(token);

const orders =
    await getOrders(user.id);
```

Dependencies:

```text
authenticate
     ↓
token
     ↓
getUser
     ↓
user.id
     ↓
getOrders
```

These operations cannot simply be started together because later operations require earlier results.

---

# 22. Accidental Sequential Execution

Consider:

```javascript
const users =
    await getUsers();

const products =
    await getProducts();

const orders =
    await getOrders();
```

If these operations are independent, this creates unnecessary waiting.

Timeline:

```text
Users
████████

        Products
        █████████

                 Orders
                 ███████
```

Total time is roughly the sum of all three durations.

---

# 23. Concurrent Execution

Better:

```javascript
const usersPromise =
    getUsers();

const productsPromise =
    getProducts();

const ordersPromise =
    getOrders();

const [
    users,
    products,
    orders
] = await Promise.all([
    usersPromise,
    productsPromise,
    ordersPromise
]);
```

Timeline:

```text
Users
████████

Products
████████████

Orders
██████
```

Operations overlap.

Total time is closer to the duration of the slowest operation.

---

# 24. Shorter Concurrent Pattern

Usually:

```javascript
const [
    users,
    products,
    orders
] = await Promise.all([
    getUsers(),
    getProducts(),
    getOrders()
]);
```

This is one of the most important async performance patterns.

---

# 25. Sequential vs Concurrent

```text
SEQUENTIAL
================================

const a = await getA();
const b = await getB();

Use when:

B depends on A.


CONCURRENT
================================

const [a, b] = await Promise.all([
    getA(),
    getB()
]);

Use when:

A and B are independent.
```

Do not blindly use either approach.

Ask:

> Does the next operation depend on the previous result?

---

# 26. Concurrency Is Not Necessarily Parallelism

When we write:

```javascript
await Promise.all([
    fetch(url1),
    fetch(url2),
    fetch(url3)
]);
```

we commonly say the requests run:

```text
Concurrently
```

This does not necessarily mean JavaScript executes multiple pieces of JavaScript code simultaneously on the same thread.

The host environment can progress multiple asynchronous operations while JavaScript continues to use its event-loop model.

---

# 27. Starting Work Before Awaiting

Compare:

```javascript
const user =
    await getUser();

const products =
    await getProducts();
```

with:

```javascript
const userPromise =
    getUser();

const productsPromise =
    getProducts();

const user =
    await userPromise;

const products =
    await productsPromise;
```

In the second version, both operations start before the first `await`.

So even without `Promise.all()`, work can overlap.

However:

```javascript
Promise.all()
```

is generally clearer when you need all independent results together.

---

# 28. `await Promise.all()`

Common pattern:

```javascript
async function loadDashboard() {
    const [
        user,
        notifications,
        recommendations
    ] = await Promise.all([
        getUser(),
        getNotifications(),
        getRecommendations()
    ]);

    return {
        user,
        notifications,
        recommendations
    };
}
```

This combines:

```text
Readable async/await
+
Promise concurrency
```

---

# 29. `Promise.all()` Failure with Await

```javascript
async function load() {
    try {
        const results =
            await Promise.all([
                getUsers(),
                getProducts(),
                getOrders()
            ]);

        console.log(results);
    } catch (error) {
        console.error(error);
    }
}
```

If one input rejects:

```text
Promise.all()
↓
Rejects
↓
await throws
↓
catch executes
```

Remember:

`Promise.all()` does not automatically cancel the other operations.

---

# 30. `await Promise.allSettled()`

When every result matters:

```javascript
async function load() {
    const results =
        await Promise.allSettled([
            getUsers(),
            getProducts(),
            getOrders()
        ]);

    console.log(results);
}
```

Useful when partial failures are acceptable.

---

# 31. `await Promise.any()`

Example:

```javascript
async function getFastestSuccessfulServer() {
    return await Promise.any([
        requestServerA(),
        requestServerB(),
        requestServerC()
    ]);
}
```

The first fulfilled result wins.

If all reject:

```text
AggregateError
```

is thrown at the `await`.

---

# 32. `await Promise.race()`

Example:

```javascript
async function firstResult() {
    return await Promise.race([
        requestA(),
        requestB()
    ]);
}
```

The first settled Promise determines the result.

That settlement can be:

```text
Fulfilled
or
Rejected
```

---

# 33. Async Function Calling Async Function

```javascript
async function getUser() {
    return {
        id: 1,
        name: "John"
    };
}

async function displayUser() {
    const user =
        await getUser();

    console.log(user.name);
}

displayUser();
```

Async functions compose naturally because each async function returns a Promise.

---

# 34. Nested Async Functions

```javascript
async function getOrders() {
    return ["Order 1", "Order 2"];
}

async function getUserData() {
    const user =
        await getUser();

    const orders =
        await getOrders(user.id);

    return {
        user,
        orders
    };
}
```

Calling:

```javascript
const promise = getUserData();
```

returns:

```text
Promise
```

Calling from another async function:

```javascript
const data =
    await getUserData();
```

gives the fulfilled result.

---

# 35. Returning a Promise Directly

Consider:

```javascript
async function getData() {
    return fetch("/api/data");
}
```

This is valid.

The async function returns a Promise that adopts the returned Promise's eventual outcome.

You do not always need:

```javascript
async function getData() {
    return await fetch("/api/data");
}
```

---

# 36. `return promise` vs `return await promise`

Consider:

```javascript
async function example() {
    return somePromise();
}
```

and:

```javascript
async function example() {
    return await somePromise();
}
```

Both eventually expose the same fulfillment value or rejection to the caller.

But they are not identical in every context.

---

# 37. Why `return await` Can Matter

Consider:

```javascript
async function example() {
    try {
        return somePromise();
    } catch (error) {
        console.log("Caught");
    }
}
```

If `somePromise()` returns a Promise that later rejects, the `try...catch` does not catch that later rejection because the function returned the Promise before awaiting its settlement.

Compare:

```javascript
async function example() {
    try {
        return await somePromise();
    } catch (error) {
        console.log("Caught");

        return "Fallback";
    }
}
```

Now the rejection is observed at the `await` inside the `try`, so the local catch can handle it.

Therefore:

```text
return promise
```

is often sufficient when you simply want to propagate the result.

```text
return await promise
```

is useful when local `try...catch` or `finally` behavior depends on awaiting settlement inside the function.

---

# 38. Don't Automatically Remove `return await`

Older advice sometimes claimed:

```javascript
return await promise;
```

should always be replaced by:

```javascript
return promise;
```

That is too simplistic.

Modern JavaScript engines optimize async functions well, and `return await` can improve local error handling and stack behavior.

Use the form that correctly expresses the desired control flow.

---

# 39. Async Arrow Functions

Syntax:

```javascript
const getUser =
    async () => {
        return {
            id: 1,
            name: "John"
        };
    };
```

Or:

```javascript
const getValue =
    async () => 10;
```

Usage:

```javascript
const value =
    await getValue();
```

---

# 40. Async Methods

Objects can have async methods:

```javascript
const userService = {
    async getUser(id) {
        const response =
            await fetch(
                `/api/users/${id}`
            );

        return response.json();
    }
};
```

Usage:

```javascript
const user =
    await userService.getUser(1);
```

---

# 41. Async Class Methods

```javascript
class UserService {
    async getUser(id) {
        const response =
            await fetch(
                `/api/users/${id}`
            );

        return response.json();
    }
}
```

Usage:

```javascript
const service =
    new UserService();

const user =
    await service.getUser(1);
```

---

# 42. Async Event Handlers

```javascript
button.addEventListener(
    "click",
    async () => {
        const users =
            await getUsers();

        console.log(users);
    }
);
```

This works because the callback itself is an async function.

But errors should still be considered:

```javascript
button.addEventListener(
    "click",
    async () => {
        try {
            const users =
                await getUsers();

            console.log(users);
        } catch (error) {
            console.error(error);
        }
    }
);
```

---

# 43. Async Loops

Suppose:

```javascript
const ids = [1, 2, 3];
```

You want:

```javascript
getUser(id)
```

for each ID.

There are several patterns depending on whether operations should be:

```text
Sequential
or
Concurrent
```

---

# 44. Sequential `for...of`

```javascript
for (const id of ids) {
    const user =
        await getUser(id);

    console.log(user);
}
```

Timeline:

```text
User 1
██████

      User 2
      ██████

            User 3
            ██████
```

Each iteration waits for the previous one.

Use this when:

```text
Order matters
Rate limiting matters
Each operation depends on previous state
Sequential behavior is intentional
```

---

# 45. Concurrent `map()` + `Promise.all()`

If operations are independent:

```javascript
const users =
    await Promise.all(
        ids.map(id => getUser(id))
    );
```

Timeline:

```text
User 1
████████

User 2
██████

User 3
██████████
```

All operations can progress concurrently.

---

# 46. Async `map()`

Consider:

```javascript
const results =
    ids.map(async id => {
        return await getUser(id);
    });
```

What is `results`?

Not:

```javascript
[
    user1,
    user2,
    user3
]
```

Instead:

```text
[
    Promise,
    Promise,
    Promise
]
```

Because an async function always returns a Promise.

---

# 47. Correct Async `map()` Pattern

```javascript
const users =
    await Promise.all(
        ids.map(async id => {
            return await getUser(id);
        })
    );
```

This works.

But since no extra async logic is needed:

```javascript
const users =
    await Promise.all(
        ids.map(id => getUser(id))
    );
```

is simpler.

---

# 48. Common Mistake — `forEach()` with Async

Consider:

```javascript
ids.forEach(async id => {
    const user =
        await getUser(id);

    console.log(user);
});

console.log("Finished");
```

You might expect:

```text
User 1
User 2
User 3
Finished
```

But `forEach()` does not wait for Promises returned by its callback.

So:

```text
Finished
```

can execute before the async callbacks complete.

---

# 49. Why `forEach()` Does Not Await

`forEach()` expects a callback whose return value is ignored.

An async callback returns a Promise:

```javascript
async id => {
    ...
}
```

but `forEach()` does nothing with that Promise.

Conceptually:

```text
forEach
↓
Call callback
↓
Ignore returned Promise
↓
Move to next item
```

---

# 50. Use `for...of` for Sequential Work

```javascript
for (const id of ids) {
    const user =
        await getUser(id);

    console.log(user);
}
```

Use when you intentionally want:

```text
One
↓
Then next
↓
Then next
```

---

# 51. Use `Promise.all()` for Concurrent Work

```javascript
const users =
    await Promise.all(
        ids.map(id => getUser(id))
    );
```

Use when operations are independent.

---

# 52. Async `filter()` Problem

This does **not** work as many people expect:

```javascript
const activeUsers =
    users.filter(async user => {
        return await isActive(user);
    });
```

Why?

`filter()` expects the callback to return a truthy/falsy value synchronously.

But an async function returns:

```text
Promise
```

A Promise object is truthy.

So async predicates do not work directly with normal `filter()`.

---

# 53. Async Filter Pattern

One approach:

```javascript
const checks =
    await Promise.all(
        users.map(user =>
            isActive(user)
        )
    );

const activeUsers =
    users.filter(
        (_, index) => checks[index]
    );
```

Flow:

```text
Users
↓
Run async checks
↓
Wait for results
↓
[true, false, true]
↓
Filter synchronously
```

---

# 54. Async `reduce()`

Async `reduce()` can be used, but it becomes harder to read.

Example:

```javascript
const total =
    await values.reduce(
        async (
            accumulatorPromise,
            value
        ) => {
            const accumulator =
                await accumulatorPromise;

            const result =
                await getValue(value);

            return accumulator + result;
        },
        Promise.resolve(0)
    );
```

This is valid, but often a `for...of` loop is easier to understand.

---

# 55. Prefer Readability

Instead of clever async chains:

```javascript
const total =
    await values.reduce(...);
```

sometimes:

```javascript
let total = 0;

for (const value of values) {
    total += await getValue(value);
}
```

is clearer.

Readable asynchronous code is usually preferable to unnecessarily clever code.

---

# 56. Common Mistake — Missing `await`

```javascript
async function loadUser() {
    const user =
        getUser();

    console.log(user.name);
}
```

`user` is:

```text
Promise
```

not the resolved user object.

Correct:

```javascript
async function loadUser() {
    const user =
        await getUser();

    console.log(user.name);
}
```

---

# 57. Common Mistake — Awaiting Everything Sequentially

Potentially inefficient:

```javascript
const profile =
    await getProfile();

const notifications =
    await getNotifications();

const recommendations =
    await getRecommendations();
```

If independent:

```javascript
const [
    profile,
    notifications,
    recommendations
] = await Promise.all([
    getProfile(),
    getNotifications(),
    getRecommendations()
]);
```

The goal is not:

```text
Use await everywhere
```

The goal is:

```text
Use await where dependencies
require waiting.
```

---

# 58. Common Mistake — Unnecessary `await`

Example:

```javascript
async function getUser() {
    return await fetch("/api/user");
}
```

If no local handling depends on the await, you may simply write:

```javascript
async function getUser() {
    return fetch("/api/user");
}
```

Or if the function does nothing else:

```javascript
function getUser() {
    return fetch("/api/user");
}
```

Do not add `async` merely because a function returns a Promise.

---

# 59. Common Mistake — `async` Without Need

Unnecessary:

```javascript
async function add(a, b) {
    return a + b;
}
```

Calling:

```javascript
add(2, 3);
```

returns:

```text
Promise<5>
```

If asynchronous behavior is unnecessary:

```javascript
function add(a, b) {
    return a + b;
}
```

is better.

---

# 60. Common Mistake — Unhandled Rejection

```javascript
async function load() {
    throw new Error("Failed");
}

load();
```

Since:

```text
async function
↓
returns Promise
```

`load()` returns a rejected Promise.

If nobody handles it, the environment can report an unhandled rejection.

Handle:

```javascript
load().catch(error => {
    console.error(error);
});
```

---

# 61. Common Mistake — Catching and Ignoring Errors

Avoid:

```javascript
try {
    await saveData();
} catch (error) {
}
```

This silently swallows the failure.

If intentionally ignoring an error, make that decision explicit.

Otherwise:

```javascript
try {
    await saveData();
} catch (error) {
    console.error(
        "Failed to save data",
        error
    );

    throw error;
}
```

---

# 62. Catch Only What You Can Handle

Instead of wrapping a huge function:

```javascript
try {
    // 100 lines
} catch (error) {
    console.error(error);
}
```

consider narrower error boundaries when different failures need different responses.

Example:

```javascript
let user;

try {
    user = await getUser();
} catch (error) {
    throw new Error(
        "Unable to load user",
        { cause: error }
    );
}

return processUser(user);
```

---

# 63. Fetch Does Not Reject on Every HTTP Error

Important:

```javascript
const response =
    await fetch("/api/users");
```

`fetch()` normally rejects for failures such as network-level errors or aborts.

But HTTP responses such as:

```text
404
500
```

still produce a `Response`.

Therefore:

```javascript
if (!response.ok) {
    throw new Error(
        `HTTP ${response.status}`
    );
}
```

is commonly needed.

---

# 64. Proper Fetch Pattern

```javascript
async function getUsers() {
    const response =
        await fetch("/api/users");

    if (!response.ok) {
        throw new Error(
            `Request failed: ${response.status}`
        );
    }

    return response.json();
}
```

Usage:

```javascript
try {
    const users =
        await getUsers();

    console.log(users);
} catch (error) {
    console.error(error);
}
```

---

# 65. Multiple API Requests

```javascript
async function loadDashboard() {
    try {
        const [
            user,
            orders,
            notifications
        ] = await Promise.all([
            getUser(),
            getOrders(),
            getNotifications()
        ]);

        return {
            user,
            orders,
            notifications
        };
    } catch (error) {
        console.error(
            "Dashboard failed:",
            error
        );

        throw error;
    }
}
```

This is a common real-world pattern.

---

# 66. Partial Failure Pattern

Suppose dashboard widgets are independent and one failure should not break everything.

Use:

```javascript
async function loadDashboard() {
    const results =
        await Promise.allSettled([
            getUser(),
            getOrders(),
            getNotifications()
        ]);

    return results;
}
```

Then inspect:

```javascript
for (const result of results) {
    if (
        result.status === "fulfilled"
    ) {
        console.log(result.value);
    } else {
        console.error(result.reason);
    }
}
```

---

# 67. Async/Await with Timeout

A timeout Promise:

```javascript
function timeout(ms) {
    return new Promise((_, reject) => {
        setTimeout(() => {
            reject(
                new Error("Timed out")
            );
        }, ms);
    });
}
```

Then:

```javascript
async function load() {
    try {
        const response =
            await Promise.race([
                fetch("/api/users"),
                timeout(5000)
            ]);

        return response;
    } catch (error) {
        console.error(error);
    }
}
```

Remember:

```text
Promise.race timeout
≠
actual cancellation
```

The fetch may continue.

For cancellation, use `AbortController`.

---

# 68. Async/Await and Event Loop

Consider:

```javascript
console.log("A");

async function test() {
    console.log("B");

    await Promise.resolve();

    console.log("C");
}

test();

Promise.resolve().then(() => {
    console.log("D");
});

console.log("E");
```

Output:

```text
A
B
E
C
D
```

Why?

When `test()` reaches `await`, its continuation is scheduled before the later `.then()` callback.

Microtask order:

```text
C continuation
D callback
```

---

# 69. Change the Scheduling Order

```javascript
console.log("A");

Promise.resolve().then(() => {
    console.log("D");
});

async function test() {
    console.log("B");

    await Promise.resolve();

    console.log("C");
}

test();

console.log("E");
```

Output:

```text
A
B
E
D
C
```

Initial microtask order:

```text
D
C continuation
```

Scheduling order matters.

---

# 70. Multiple Awaits

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");

    await Promise.resolve();

    console.log("C");
}

test();

console.log("D");
```

Output:

```text
A
D
B
C
```

Each `await` creates another suspension/resumption point.

---

# 71. Multiple Async Functions

```javascript
async function first() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}

async function second() {
    console.log("C");

    await Promise.resolve();

    console.log("D");
}

first();
second();

console.log("E");
```

Output:

```text
A
C
E
B
D
```

Both functions run synchronously until their first `await`.

Then their continuations execute in scheduling order.

---

# 72. Async Function Return Timing

```javascript
async function getValue() {
    return 10;
}

console.log("A");

getValue().then(value => {
    console.log(value);
});

console.log("B");
```

Output:

```text
A
B
10
```

Even though `getValue()` immediately returns `10` internally, the caller receives a Promise and its `.then()` handler executes asynchronously.

---

# 73. Awaiting Async Function

```javascript
async function getValue() {
    return 10;
}

async function display() {
    console.log("A");

    const value =
        await getValue();

    console.log(value);
}

display();

console.log("B");
```

Output:

```text
A
B
10
```

---

# 74. Top-Level Await

Modern JavaScript modules can use:

```javascript
const response =
    await fetch("/api/data");

const data =
    await response.json();
```

without wrapping the code in an async function.

This is called:

```text
Top-Level Await
```

It applies in module contexts, not arbitrary classic scripts.

---

# 75. Top-Level Await Example

```javascript
// module.js

const response =
    await fetch("/api/config");

export const config =
    await response.json();
```

Modules that depend on this module may need to wait for its asynchronous evaluation.

Use top-level await carefully because it can affect module loading.

---

# 76. Async IIFE

Before top-level await was available or when working outside module contexts, a common pattern was:

```javascript
(async () => {
    const users =
        await getUsers();

    console.log(users);
})();
```

This is an:

```text
Async Immediately Invoked
Function Expression
```

or:

```text
Async IIFE
```

---

# 77. Async Constructors Are Not Allowed

You cannot write:

```javascript
class User {
    async constructor() {
    }
}
```

Constructors cannot be async.

Instead, use a factory method:

```javascript
class User {
    constructor(data) {
        this.data = data;
    }

    static async create(id) {
        const data =
            await getUser(id);

        return new User(data);
    }
}
```

Usage:

```javascript
const user =
    await User.create(1);
```

---

# 78. Controlled Concurrency

This:

```javascript
await Promise.all(
    ids.map(id => getUser(id))
);
```

starts all operations immediately.

For:

```text
5 items
```

that may be fine.

For:

```text
100,000 items
```

it may be a bad idea.

Possible problems:

```text
Too many network requests
API rate limits
High memory usage
Database overload
Resource exhaustion
```

Real applications sometimes need:

```text
Concurrency Limits
```

instead of either:

```text
Fully Sequential
```

or:

```text
Everything at Once
```

---

# 79. Simple Batch Processing

One simple strategy:

```javascript
async function processInBatches(
    items,
    batchSize
) {
    for (
        let i = 0;
        i < items.length;
        i += batchSize
    ) {
        const batch =
            items.slice(
                i,
                i + batchSize
            );

        await Promise.all(
            batch.map(item =>
                processItem(item)
            )
        );
    }
}
```

Usage:

```javascript
await processInBatches(
    users,
    10
);
```

This processes:

```text
10 concurrently
↓
wait
↓
next 10
↓
wait
↓
...
```

It is a simple form of concurrency control.

---

# 80. React Event Handler Example

```javascript
async function handleSubmit() {
    setLoading(true);

    try {
        const response =
            await saveUser(formData);

        setUser(response);
    } catch (error) {
        setError(
            "Unable to save user"
        );
    } finally {
        setLoading(false);
    }
}
```

This pattern appears constantly in frontend applications.

---

# 81. React Effect Pattern

You may encounter:

```javascript
useEffect(() => {
    async function loadData() {
        const data =
            await getData();

        setData(data);
    }

    loadData();
}, []);
```

Why not simply:

```javascript
useEffect(async () => {
    // ...
}, []);
```

Because an effect callback has a specific return contract: it may return a cleanup function, whereas an `async` function always returns a Promise.

So the common pattern is:

```text
Synchronous effect callback
↓
Define/call async function inside
```

We will cover this more deeply during React.

---

# 82. Race Conditions

Async operations can finish in a different order from the order they started.

Example:

```text
Request A starts
↓
Request B starts
↓
Request B finishes
↓
Request A finishes
```

If both update the same state, older data can overwrite newer data.

This is an:

```text
Async Race Condition
```

Example scenario:

```text
User searches "Java"
↓
Request A

User quickly searches "React"
↓
Request B

React response returns first
↓
UI shows React

Java response returns later
↓
UI incorrectly shows Java
```

This becomes important in React.

Solutions may include:

```text
AbortController
Request IDs
Ignoring stale results
State-management strategies
```

---

# 83. Async/Await Does Not Prevent Race Conditions

Writing:

```javascript
const result =
    await fetchData();
```

makes the code easier to read.

It does **not** guarantee that unrelated async operations complete in the order you want.

Async sequencing and concurrency still need to be designed deliberately.

---

# 84. Interview Question — What Is `async/await`?

`async/await` is syntax built on Promises that makes Promise-based asynchronous code easier to read and structure.

---

# 85. Interview Question — What Does `async` Do?

Declaring a function as:

```javascript
async
```

ensures that calling the function returns a Promise.

A returned value becomes a fulfillment value.

A thrown error becomes a rejection.

---

# 86. Interview Question — What Does `await` Do?

`await` evaluates an expression, converts/adopts it using Promise semantics, suspends the current async function, and resumes the function after the awaited value settles.

If fulfilled:

```text
await returns value
```

If rejected:

```text
await throws reason
```

---

# 87. Interview Question — Does `await` Block JavaScript?

No.

It suspends the current async function.

It does not synchronously block the entire event loop while waiting for the Promise.

---

# 88. Interview Question — Can We Await Non-Promises?

Yes.

```javascript
const value =
    await 10;
```

works.

The value is handled through Promise-style resolution semantics.

The continuation still occurs asynchronously.

---

# 89. Interview Question — Can We Await Thenables?

Yes.

Thenables can be assimilated through Promise resolution behavior.

---

# 90. Interview Question — Does Async Function Always Return Promise?

Yes.

```javascript
async function test() {
    return 10;
}
```

returns a Promise fulfilled with `10`.

---

# 91. Interview Question — What Happens If Async Function Throws?

```javascript
async function test() {
    throw new Error("Failed");
}
```

returns a rejected Promise.

---

# 92. Interview Question — Sequential vs Concurrent Await?

Sequential:

```javascript
const a = await getA();
const b = await getB();
```

`B` starts after `A` completes if `getB()` is only called on the second line.

Concurrent:

```javascript
const [a, b] =
    await Promise.all([
        getA(),
        getB()
    ]);
```

Both operations can begin before either result is awaited individually.

---

# 93. Interview Question — Why Is Async `forEach()` Problematic?

Because `forEach()` ignores the Promise returned by its callback.

Therefore:

```javascript
array.forEach(async item => {
    await process(item);
});
```

does not make the surrounding code wait for all operations.

Use:

```javascript
for (const item of array) {
    await process(item);
}
```

for sequential processing.

Or:

```javascript
await Promise.all(
    array.map(item =>
        process(item)
    )
);
```

for concurrent processing.

---

# 94. Interview Question — `return` vs `return await`?

```javascript
return promise;
```

generally propagates the Promise's result.

```javascript
return await promise;
```

awaits it inside the current function before returning the function's Promise outcome.

`return await` can matter for local:

```text
try
catch
finally
```

behavior and can improve error-stack behavior in some cases.

---

# 95. Interview Question — Can Constructors Be Async?

No.

Use an async factory function or static factory method instead.

---

# 96. Interview Question — What Is Top-Level Await?

Top-level await allows `await` to be used directly at the top level of JavaScript modules without wrapping it in an async function.

---

# 97. Interview Question — Does Await Make Independent Requests Concurrent?

No.

This:

```javascript
const a = await getA();
const b = await getB();
```

is sequential if `getB()` is not called until after `getA()` finishes.

Concurrency must be created deliberately.

---

# 98. Interview Question — Can `try...catch` Catch Async Errors?

Yes, when the Promise is awaited inside the `try`.

```javascript
try {
    await operation();
} catch (error) {
    console.error(error);
}
```

But:

```javascript
try {
    operation();
} catch (error) {
}
```

will not catch a later Promise rejection merely because the function call occurred inside `try`.

---

# 99. Output Question 1

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

# 100. Output Question 2

```javascript
async function test() {
    return 10;
}

console.log("A");

test().then(value => {
    console.log(value);
});

console.log("B");
```

Output:

```text
A
B
10
```

---

# 101. Output Question 3

```javascript
async function test() {
    console.log("A");

    await 10;

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

---

# 102. Output Question 4

```javascript
async function test() {
    try {
        await Promise.reject("A");

        console.log("B");
    } catch (error) {
        console.log(error);
    }

    console.log("C");
}

test();
```

Output:

```text
A
C
```

`"B"` is skipped because the awaited Promise rejects.

---

# 103. Output Question 5

```javascript
async function test() {
    try {
        return await Promise.resolve(
            "A"
        );
    } finally {
        console.log("B");
    }
}

test().then(value => {
    console.log(value);
});
```

Output:

```text
B
A
```

`finally` runs before the async function completes its returned Promise with `"A"`.

---

# 104. Output Question 6

```javascript
async function first() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}

async function second() {
    console.log("C");

    await Promise.resolve();

    console.log("D");
}

first();
second();

console.log("E");
```

Output:

```text
A
C
E
B
D
```

---

# 105. Output Question 7

```javascript
console.log("1");

Promise.resolve().then(() => {
    console.log("2");
});

async function test() {
    console.log("3");

    await Promise.resolve();

    console.log("4");
}

test();

console.log("5");
```

Output:

```text
1
3
5
2
4
```

Microtasks were scheduled:

```text
2
4
```

in that order.

---

# 106. Output Question 8

```javascript
console.log("1");

async function test() {
    console.log("2");

    await Promise.resolve();

    console.log("3");
}

test();

Promise.resolve().then(() => {
    console.log("4");
});

console.log("5");
```

Output:

```text
1
2
5
3
4
```

Here the async continuation is scheduled before the explicit Promise handler.

---

# 107. Output Question 9

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");

    await Promise.resolve();

    console.log("C");
}

test();

Promise.resolve().then(() => {
    console.log("D");
});
```

Output:

```text
A
B? 
```

Do not guess.

Let's process it correctly.

Initial execution:

```text
A
```

First `await` schedules continuation containing `B`.

Then:

```javascript
Promise.resolve().then(...)
```

schedules `D`.

Initial microtask queue:

```text
B continuation
D
```

Run B continuation:

```text
B
```

Then second `await` schedules C continuation.

Queue becomes:

```text
D
C
```

Therefore final output:

```text
A
B
D
C
```

The important lesson:

> Every `await` can create a new scheduling point.

---

# 108. Output Question 10

```javascript
async function getValue() {
    console.log("A");

    return "B";
}

async function test() {
    console.log("C");

    const value =
        await getValue();

    console.log(value);
}

console.log("D");

test();

console.log("E");
```

Output:

```text
D
C
A
E
B
```

`getValue()` runs synchronously until it returns.

But `test()` resumes after awaiting its Promise.

---

# 109. Async/Await Mental Model

```text
Call async function
        │
        ▼
Execute synchronously
        │
        ▼
Reach await
        │
        ▼
Evaluate awaited expression
        │
        ▼
Suspend current async function
        │
        ▼
Caller continues
        │
        ▼
Awaited value settles
        │
        ▼
Continuation scheduled
        │
        ▼
Microtask processing
        │
        ▼
Resume async function
        │
        ├── Fulfilled
        │       ↓
        │   await gives value
        │
        └── Rejected
                ↓
            await throws
```

---

# 110. Async/Await Cheat Sheet

```text
ASYNC
================================

async function test() {}

Calling test()
returns a Promise.


RETURN VALUE
================================

async function test() {
    return 10;
}

↓

Fulfilled Promise<10>


THROW
================================

async function test() {
    throw new Error();
}

↓

Rejected Promise


AWAIT
================================

const value =
    await promise;

Fulfilled
↓
value

Rejected
↓
throw


BEFORE AWAIT
================================

Runs synchronously.


AFTER AWAIT
================================

Runs asynchronously
through microtask scheduling.


AWAIT DOES NOT
================================

Block the entire
JavaScript event loop.


SEQUENTIAL
================================

const a = await getA();
const b = await getB();

Use when dependent.


CONCURRENT
================================

const [a, b] =
    await Promise.all([
        getA(),
        getB()
    ]);

Use when independent.


ERROR HANDLING
================================

try {
    await operation();
} catch (error) {
    ...
} finally {
    ...
}


FOR...OF
================================

Use for intentional
sequential async loops.


MAP + PROMISE.ALL
================================

Use for concurrent
independent operations.


ASYNC MAP
================================

Returns array of Promises.


ASYNC FOREACH
================================

forEach does not wait
for callback Promises.


FETCH
================================

Check response.ok
for HTTP errors.


RETURN PROMISE
================================

return promise;

Propagate Promise result.


RETURN AWAIT
================================

return await promise;

Useful when local
try/catch/finally needs
to observe settlement.


TOP-LEVEL AWAIT
================================

Available in modules.


ASYNC CONSTRUCTOR
================================

Not allowed.

Use async factory.


RACE CONDITIONS
================================

Async/await does not
prevent them.


CONCURRENCY LIMITS
================================

Don't always launch
unbounded async work.
```

---

# 111. Important Rules to Remember

```text
1. async/await is built on Promises.

2. async functions always return Promises.

3. Returning a normal value fulfills the async function's Promise.

4. Throwing rejects the async function's Promise.

5. await works with Promises.

6. await also works with normal values.

7. await can assimilate thenables.

8. Code before the first await runs synchronously.

9. Code after await resumes asynchronously.

10. await does not block the entire JavaScript runtime.

11. await suspends the current async function.

12. Awaiting an already fulfilled Promise still creates an async continuation.

13. Awaiting a rejected Promise throws.

14. try...catch can handle awaited rejections.

15. finally runs whether async work succeeds or fails.

16. Errors can propagate through nested async functions.

17. Errors do not need to be caught in every async function.

18. Callers can handle rejected async functions.

19. Errors can be re-thrown.

20. Error cause can preserve underlying failures.

21. Sequential await is correct when operations depend on each other.

22. Sequential await can be inefficient for independent operations.

23. Promise.all() is useful for independent concurrent operations.

24. Promise.all() rejects if an input rejects.

25. Promise.all() does not automatically cancel other operations.

26. Promise.allSettled() is useful for partial failure handling.

27. Promise.any() returns the first fulfillment.

28. Promise.race() returns the first settlement.

29. Concurrency is not the same as JavaScript parallel execution.

30. Promise-producing operations can be started before awaiting them.

31. async functions compose naturally.

32. Returning a Promise directly from an async function is valid.

33. return await is not always unnecessary.

34. return await can matter inside try/catch/finally.

35. Async arrow functions return Promises.

36. Object methods can be async.

37. Class methods can be async.

38. Constructors cannot be async.

39. Use async factory methods when async initialization is required.

40. for...of works well for sequential async iteration.

41. map() with async callbacks returns an array of Promises.

42. Promise.all() can resolve an async map.

43. forEach() does not await async callbacks.

44. Normal filter() does not understand async predicates.

45. Async filtering usually requires resolving checks first.

46. Async reduce is possible but can be difficult to read.

47. Prefer clear asynchronous code over clever code.

48. Missing await means you may be working with the Promise instead of its value.

49. Do not use async when no Promise behavior is needed.

50. Do not await independent operations sequentially without reason.

51. Unhandled rejected async functions can cause unhandled rejections.

52. Avoid silently swallowing errors.

53. Catch errors at appropriate boundaries.

54. fetch() does not reject merely because an HTTP response is 404 or 500.

55. Check response.ok when appropriate.

56. Promise.race() timeout does not cancel underlying work.

57. AbortController can provide actual cancellation for supported APIs.

58. Async continuations participate in microtask scheduling.

59. Scheduling order matters.

60. Multiple awaits create multiple suspension points.

61. Multiple async functions can interleave through microtasks.

62. Top-level await is available in module contexts.

63. Top-level await can affect module loading.

64. Async IIFEs provide another way to create an async scope.

65. Starting thousands of operations simultaneously can cause resource problems.

66. Real applications may require concurrency limits.

67. Async operations can complete out of order.

68. Async/await does not prevent race conditions.

69. Stale async results can overwrite newer state.

70. Async/await is essential for modern JavaScript and React development.
```

---

# Async/Await in One Sentence

> `async/await` is Promise-based JavaScript syntax that lets asynchronous operations be expressed using structured control flow while preserving Promise semantics and event-loop scheduling.

---

# Final Mental Model

```text
async function
      │
      ▼
Runs synchronously
      │
      ▼
   await
      │
      ▼
Suspend function
      │
      ├───────────────┐
      │               │
      ▼               ▼
Other JS runs    Awaited operation
                      │
                      ▼
                    Settles
                      │
                      ▼
               Microtask continuation
                      │
                      ▼
                Resume function
                  /        \
                 /          \
                ▼            ▼
           Fulfilled       Rejected
                │            │
                ▼            ▼
          Get value       Throw error
                │            │
                └─────┬──────┘
                      ▼
               Continue execution
                      │
                      ▼
              Return / Throw
                      │
                      ▼
          Async function Promise
```

For performance decisions, remember:

```text
Does B depend on A?
        │
     ┌──┴──┐
     │     │
    YES    NO
     │     │
     ▼     ▼

Sequential       Concurrent

const a =        const [a, b] =
  await A();       await Promise.all([
                    A(),
const b =           B()
  await B();      ]);
```

And for async collections:

```text
Need to process array?
        │
        ▼
Should operations run
one after another?
        │
     ┌──┴──┐
     │     │
    YES    NO
     │     │
     ▼     ▼

for...of        Promise.all()
+ await         + map()

for (item) {    await Promise.all(
  await work();   items.map(work)
}               );
```
