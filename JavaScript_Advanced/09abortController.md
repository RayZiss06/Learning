# AbortController & AbortSignal

`AbortController` is a Web API used to **signal cancellation** to asynchronous operations that support an `AbortSignal`.

It is commonly used with:

* `fetch()`
* API requests
* Search/autocomplete
* Timeouts
* Event listeners
* Long-running operations that support signals
* React effect cleanup
* Preventing stale requests

Basic example:

```javascript
const controller = new AbortController();

const response = await fetch("/api/users", {
    signal: controller.signal
});

// Somewhere else:
controller.abort();
```

Basic mental model:

```text
AbortController
      │
      │ owns
      ▼
 AbortSignal
      │
      │ passed to
      ▼
Async Operation
      │
      │ listens for abort
      ▼
Cancellation
```

---

# 1. Why Do We Need Cancellation?

Consider a search box.

The user types:

```text
j
ja
jav
java
javascript
```

If every input sends a request:

```text
GET /search?q=j
GET /search?q=ja
GET /search?q=jav
GET /search?q=java
GET /search?q=javascript
```

several requests may be active simultaneously.

They may complete in a different order:

```text
Request: j           starts
Request: java        starts
Request: javascript  starts

javascript finishes
java finishes
j finishes
```

Without proper handling, an old result could overwrite the newest one.

```text
User searches "javascript"

UI shows results for "j"
```

Cancellation can help prevent unnecessary stale work.

---

# 2. `AbortController`

Create a controller:

```javascript
const controller = new AbortController();
```

The controller exposes:

```javascript
controller.signal
controller.abort()
```

Conceptually:

```text
controller
   │
   ├── signal
   │
   └── abort()
```

---

# 3. `AbortSignal`

The signal is obtained from:

```javascript
controller.signal
```

Example:

```javascript
const controller = new AbortController();

const signal = controller.signal;

console.log(signal);
```

The signal represents the cancellation state.

The controller performs the cancellation.

```text
AbortController
=
Cancellation controller


AbortSignal
=
Cancellation notification/state
```

---

# 4. Passing a Signal to Fetch

```javascript
const controller =
    new AbortController();

const response = await fetch(
    "/api/users",
    {
        signal: controller.signal
    }
);
```

Now Fetch is connected to the controller.

```text
Controller
    │
    ▼
 Signal
    │
    ▼
 fetch()
```

---

# 5. Aborting a Fetch Request

Call:

```javascript
controller.abort();
```

Example:

```javascript
const controller =
    new AbortController();

fetch("/api/users", {
    signal: controller.signal
});

controller.abort();
```

The request receives the abort signal.

The Fetch operation rejects if it is still affected by the cancellation.

---

# 6. Handling an Aborted Fetch

Example:

```javascript
const controller =
    new AbortController();

try {
    const response = await fetch(
        "/api/users",
        {
            signal: controller.signal
        }
    );

    const users =
        await response.json();

    console.log(users);
} catch (error) {
    if (error.name === "AbortError") {
        console.log(
            "Request cancelled"
        );
    } else {
        console.error(error);
    }
}
```

Abort should often be treated differently from a genuine application failure.

---

# 7. Abort Is Not Necessarily an Error to the User

Suppose:

```text
User searches "Java"

Request starts

User changes search to "React"

Java request cancelled

React request starts
```

The cancellation was intentional.

Showing:

```text
ERROR: REQUEST FAILED!
```

would be misleading.

Instead:

```javascript
catch (error) {
    if (error.name === "AbortError") {
        return;
    }

    showError(error);
}
```

---

# 8. `signal.aborted`

You can inspect whether the signal has been aborted.

```javascript
const controller =
    new AbortController();

console.log(
    controller.signal.aborted
);
```

Output:

```text
false
```

After:

```javascript
controller.abort();
```

then:

```javascript
console.log(
    controller.signal.aborted
);
```

Output:

```text
true
```

---

# 9. Abort State Is Permanent

Once a signal becomes aborted:

```text
false
↓
abort()
↓
true
```

it stays aborted.

You cannot reset the same controller.

Wrong idea:

```javascript
controller.abort();

// somehow reset controller
```

There is no:

```javascript
controller.reset();
```

If you need another independent operation:

```javascript
const controller =
    new AbortController();
```

Create a new controller.

---

# 10. Reusing an Aborted Signal

Consider:

```javascript
const controller =
    new AbortController();

controller.abort();

await fetch("/api/users", {
    signal: controller.signal
});
```

The signal is already aborted.

The Fetch operation will reject rather than behaving like a fresh uncancelled request.

Therefore:

```text
New operation
↓
New AbortController
```

is usually the correct model.

---

# 11. `signal.reason`

Modern `AbortSignal` supports:

```javascript
signal.reason
```

Example:

```javascript
const controller =
    new AbortController();

controller.abort("User cancelled");

console.log(
    controller.signal.reason
);
```

Output conceptually:

```text
User cancelled
```

If `abort()` is called without an explicit reason, the signal uses the platform's default abort reason.

---

# 12. Passing an Abort Reason

```javascript
const controller =
    new AbortController();

controller.abort(
    new Error("Search replaced")
);
```

Then:

```javascript
console.log(
    controller.signal.reason
);
```

returns the reason supplied to `abort()`.

Abort reasons are useful when multiple cancellation scenarios exist.

---

# 13. `throwIfAborted()`

Modern `AbortSignal` provides:

```javascript
signal.throwIfAborted();
```

Example:

```javascript
function processData(
    data,
    signal
) {
    signal.throwIfAborted();

    // continue processing
}
```

If the signal is not aborted:

```text
Continue normally
```

If it is aborted:

```text
Throw signal.reason
```

---

# 14. Why `throwIfAborted()` Is Useful

Suppose you build your own async operation:

```javascript
async function processItems(
    items,
    signal
) {
    for (const item of items) {
        signal.throwIfAborted();

        await processItem(item);
    }
}
```

Now cancellation can be checked between operations.

```text
Process item
    ↓
Check abort
    ↓
Process item
    ↓
Check abort
    ↓
...
```

---

# 15. AbortController Does Not Magically Cancel Everything

This is important.

```javascript
const controller =
    new AbortController();

controller.abort();
```

does not magically stop arbitrary JavaScript.

For example:

```javascript
async function work() {
    while (true) {
        // work
    }
}
```

creating an AbortController elsewhere does nothing to this function unless the function is designed to use the signal.

Cancellation is cooperative.

```text
Controller
   ↓
Signal
   ↓
Operation must support/listen
to the signal
```

---

# 16. Cooperative Cancellation

Custom function:

```javascript
async function processUsers(
    users,
    signal
) {
    for (const user of users) {
        signal.throwIfAborted();

        await processUser(user);
    }
}
```

Usage:

```javascript
const controller =
    new AbortController();

processUsers(
    users,
    controller.signal
);
```

Cancel:

```javascript
controller.abort();
```

The operation checks the signal and stops when it reaches the cancellation check.

---

# 17. Listening for the `abort` Event

`AbortSignal` is an `EventTarget`.

You can listen for:

```text
abort
```

Example:

```javascript
const controller =
    new AbortController();

controller.signal.addEventListener(
    "abort",
    () => {
        console.log("Cancelled");
    }
);

controller.abort();
```

Output:

```text
Cancelled
```

---

# 18. Custom Cancellation with Abort Event

Example:

```javascript
function wait(ms, signal) {
    return new Promise(
        (resolve, reject) => {
            const timeoutId =
                setTimeout(
                    resolve,
                    ms
                );

            signal.addEventListener(
                "abort",
                () => {
                    clearTimeout(
                        timeoutId
                    );

                    reject(
                        signal.reason
                    );
                },
                { once: true }
            );
        }
    );
}
```

Usage:

```javascript
const controller =
    new AbortController();

await wait(
    5000,
    controller.signal
);
```

Cancel:

```javascript
controller.abort();
```

---

# 19. Check Already-Aborted Signals

When implementing your own abortable function, consider the possibility that the signal was aborted before the function started.

Example:

```javascript
function wait(ms, signal) {
    return new Promise(
        (resolve, reject) => {
            if (signal.aborted) {
                reject(signal.reason);
                return;
            }

            const timeoutId =
                setTimeout(
                    resolve,
                    ms
                );

            signal.addEventListener(
                "abort",
                () => {
                    clearTimeout(
                        timeoutId
                    );

                    reject(
                        signal.reason
                    );
                },
                { once: true }
            );
        }
    );
}
```

Otherwise the function might miss an abort that happened before the listener was registered.

---

# 20. Abort Before Fetch

```javascript
const controller =
    new AbortController();

controller.abort();

try {
    await fetch("/api/users", {
        signal: controller.signal
    });
} catch (error) {
    console.log("Aborted");
}
```

Because the signal is already aborted, the request does not proceed normally.

---

# 21. Abort While Fetch Is Running

```javascript
const controller =
    new AbortController();

const promise = fetch(
    "/api/users",
    {
        signal: controller.signal
    }
);

setTimeout(() => {
    controller.abort();
}, 1000);

await promise;
```

If the request is still pending when the abort occurs, it is cancelled.

---

# 22. Abort After Fetch Completes

Suppose:

```javascript
const controller =
    new AbortController();

const response =
    await fetch("/api/users", {
        signal: controller.signal
    });

controller.abort();
```

The initial fetch has already produced a response.

However, Fetch cancellation can also affect consumption of the response body when that body has not yet been fully consumed.

This matters because:

```javascript
await fetch(...)
```

and:

```javascript
await response.json()
```

represent separate stages.

---

# 23. Aborting During Body Consumption

Example concept:

```javascript
const controller =
    new AbortController();

const response =
    await fetch("/large-file", {
        signal: controller.signal
    });

controller.abort();

const data =
    await response.text();
```

The body-reading operation may reject because the request was aborted before the body was fully consumed.

Mental model:

```text
Request headers received
        ↓
fetch() resolves
        ↓
Body may still be streaming
        ↓
Abort
        ↓
Body consumption can fail
```

---

# 24. One Controller Can Cancel Multiple Requests

The same signal can be passed to multiple operations.

```javascript
const controller =
    new AbortController();

const userPromise =
    fetch("/api/user", {
        signal: controller.signal
    });

const ordersPromise =
    fetch("/api/orders", {
        signal: controller.signal
    });

const productsPromise =
    fetch("/api/products", {
        signal: controller.signal
    });
```

Then:

```javascript
controller.abort();
```

signals cancellation to all three.

```text
             Controller
                 │
                 ▼
               Signal
              /   |   \
             /    |    \
            ▼     ▼     ▼
         User   Orders Products
```

---

# 25. When Shared Signals Are Useful

Suppose a dashboard requires:

```text
User
Orders
Notifications
Recommendations
```

If the user leaves the dashboard, all requests may become unnecessary.

You can create:

```javascript
const controller =
    new AbortController();
```

and pass:

```javascript
controller.signal
```

to all dashboard requests.

Leaving the page:

```javascript
controller.abort();
```

cancels the group.

---

# 26. When Not to Share a Controller

Suppose:

```text
Profile Request

Payment Request
```

are independent operations.

If they share the same controller:

```javascript
controller.abort();
```

would cancel both.

If you need independent cancellation:

```javascript
const profileController =
    new AbortController();

const paymentController =
    new AbortController();
```

General rule:

```text
Operations share lifecycle
        ↓
Can share controller


Operations have separate lifecycle
        ↓
Use separate controllers
```

---

# 27. Request Timeout

`fetch()` itself does not require you to use a timeout.

But applications often want:

```text
If request takes longer
than X seconds
↓
Cancel it
```

AbortController can implement this.

---

# 28. Manual Timeout Pattern

```javascript
async function fetchWithTimeout(
    url,
    timeout = 5000
) {
    const controller =
        new AbortController();

    const timeoutId =
        setTimeout(() => {
            controller.abort();
        }, timeout);

    try {
        return await fetch(
            url,
            {
                signal:
                    controller.signal
            }
        );
    } finally {
        clearTimeout(timeoutId);
    }
}
```

Usage:

```javascript
const response =
    await fetchWithTimeout(
        "/api/users",
        5000
    );
```

---

# 29. Why Clear the Timeout?

Suppose the request finishes in:

```text
500 ms
```

but the timeout is:

```text
5000 ms
```

Without:

```javascript
clearTimeout(timeoutId);
```

the timer remains scheduled unnecessarily.

Therefore:

```javascript
finally {
    clearTimeout(timeoutId);
}
```

cleans it up.

---

# 30. `AbortSignal.timeout()`

Modern environments may support:

```javascript
AbortSignal.timeout(ms)
```

Example:

```javascript
const response =
    await fetch("/api/users", {
        signal:
            AbortSignal.timeout(5000)
    });
```

This creates a signal that automatically aborts after the specified duration.

Much cleaner than manually creating:

```text
AbortController
+
setTimeout
+
clearTimeout
```

when timeout is the only cancellation requirement.

---

# 31. Timeout Error vs Manual Abort

When using timeout-specific signals, the rejection reason may represent a timeout rather than a normal manual abort.

For example, code may distinguish:

```javascript
catch (error) {
    if (error.name === "TimeoutError") {
        console.log(
            "Request timed out"
        );
    } else if (
        error.name === "AbortError"
    ) {
        console.log(
            "Request cancelled"
        );
    } else {
        console.error(error);
    }
}
```

Exact handling should follow the API/runtime behavior you are using.

---

# 32. `AbortSignal.any()`

Modern environments may support:

```javascript
AbortSignal.any([
    signal1,
    signal2
]);
```

The combined signal aborts when any input signal aborts.

Example:

```javascript
const controller =
    new AbortController();

const timeoutSignal =
    AbortSignal.timeout(5000);

const signal =
    AbortSignal.any([
        controller.signal,
        timeoutSignal
    ]);
```

Use:

```javascript
await fetch("/api/users", {
    signal
});
```

Now the request can stop because of:

```text
Manual cancellation

OR

Timeout
```

---

# 33. Combining User Cancellation + Timeout

```javascript
const controller =
    new AbortController();

const signal =
    AbortSignal.any([
        controller.signal,
        AbortSignal.timeout(5000)
    ]);

try {
    const response =
        await fetch("/api/users", {
            signal
        });

    return response;
} catch (error) {
    console.error(error);
}
```

Cancel manually:

```javascript
controller.abort();
```

or allow the timeout signal to abort automatically.

---

# 34. Cancellation vs `Promise.race()`

Earlier we saw:

```javascript
await Promise.race([
    fetch(url),
    timeout(5000)
]);
```

This can stop your code from waiting for the fetch.

But it does **not** automatically cancel the fetch.

```text
Promise.race()
      │
      ▼
Timeout wins
      │
      ▼
Your code continues
      │
      ▼
Fetch may still be running
```

---

# 35. AbortController Actually Signals Cancellation

With:

```javascript
controller.abort();
```

the signal is delivered to Fetch.

```text
Timeout
   │
   ▼
controller.abort()
   │
   ▼
AbortSignal
   │
   ▼
fetch()
   │
   ▼
Request cancelled
```

Therefore:

```text
Promise.race()
=
Stop waiting


AbortController
=
Request cancellation mechanism
```

---

# 36. User-Initiated Cancellation

Suppose there is:

```html
<button id="cancel">
    Cancel
</button>
```

JavaScript:

```javascript
const controller =
    new AbortController();

const cancelButton =
    document.querySelector(
        "#cancel"
    );

cancelButton.addEventListener(
    "click",
    () => {
        controller.abort();
    }
);
```

Fetch:

```javascript
try {
    const response =
        await fetch(
            "/api/report",
            {
                signal:
                    controller.signal
            }
        );
} catch (error) {
    if (
        error.name === "AbortError"
    ) {
        console.log(
            "Cancelled by user"
        );
    }
}
```

Useful for:

```text
Large downloads
Report generation
Searches
Long API requests
Navigation changes
```

---

# 37. Race Conditions

Suppose:

```text
Request A
search = "Java"

Request B
search = "React"
```

A starts first.

But B completes first:

```text
A starts
B starts
B completes
A completes
```

If both update the same UI:

```text
React results displayed
↓
Java results displayed
```

Now the UI contains stale data.

This is a race condition.

---

# 38. Why Async/Await Does Not Prevent Race Conditions

You might write:

```javascript
const result =
    await searchUsers(query);

displayResults(result);
```

This only means:

```text
This function waits
for its own request.
```

It does not mean another invocation of the same function cannot be running simultaneously.

Example:

```text
search("Java")
        │
        │ running
        ▼

search("React")
        │
        │ running
        ▼
```

Both async operations can exist at once.

---

# 39. Cancel Previous Search Request

One solution:

```javascript
let controller;

async function search(query) {
    if (controller) {
        controller.abort();
    }

    controller =
        new AbortController();

    try {
        const response =
            await fetch(
                `/api/search?q=${
                    encodeURIComponent(
                        query
                    )
                }`,
                {
                    signal:
                        controller.signal
                }
            );

        const data =
            await response.json();

        displayResults(data);
    } catch (error) {
        if (
            error.name ===
            "AbortError"
        ) {
            return;
        }

        console.error(error);
    }
}
```

Flow:

```text
Search Java
     │
     ▼
Controller A
     │
     ▼
Request A

Search React
     │
     ├── Abort Controller A
     │
     ▼
Controller B
     │
     ▼
Request B
```

---

# 40. New Request Cancels Old Request

Pattern:

```javascript
let controller = null;

async function loadData() {
    controller?.abort();

    controller =
        new AbortController();

    return fetch("/api/data", {
        signal:
            controller.signal
    });
}
```

Each invocation:

```text
Cancel previous
      ↓
Create new controller
      ↓
Start new request
```

This is common in:

```text
Search
Autocomplete
Filters
Sorting
Pagination
Route changes
```

---

# 41. Cancellation Is Not the Only Race-Condition Solution

AbortController is useful, but sometimes cancellation is:

```text
Impossible
Unsupported
Too late
Not desirable
```

Another solution is:

```text
Ignore stale results
```

---

# 42. Request ID Pattern

```javascript
let latestRequestId = 0;

async function search(query) {
    const requestId =
        ++latestRequestId;

    const data =
        await searchApi(query);

    if (
        requestId !==
        latestRequestId
    ) {
        return;
    }

    displayResults(data);
}
```

Suppose:

```text
Java  → Request 1
React → Request 2
```

Request 1 completes after Request 2.

Check:

```text
Request 1 !== Latest Request 2
```

so Request 1 is ignored.

---

# 43. Abort + Ignore Stale Results

For stronger protection, applications may combine:

```text
Abort previous request
+
Ignore stale result
```

Why?

Because cancellation is not always a substitute for application-level state correctness.

Mental model:

```text
Cancellation
=
Reduce unnecessary work


Stale-result check
=
Protect state correctness
```

---

# 44. Debouncing + Cancellation

For search inputs, another useful combination is:

```text
Debounce
+
AbortController
```

Debounce reduces how many requests are started.

AbortController cancels a previous request if a newer request supersedes it.

Flow:

```text
User types
   │
   ▼
Debounce
   │
   ▼
Send request
   │
User types again
   │
   ▼
Debounce
   │
   ▼
Abort previous request
   │
   ▼
Send newest request
```

We will cover debouncing next.

---

# 45. AbortController in API Functions

Instead of creating cancellation only inside `fetch()`, service functions can accept a signal.

```javascript
async function getUsers(signal) {
    const response =
        await fetch(
            "/api/users",
            {
                signal
            }
        );

    if (!response.ok) {
        throw new Error(
            `HTTP ${response.status}`
        );
    }

    return response.json();
}
```

Caller:

```javascript
const controller =
    new AbortController();

const users =
    await getUsers(
        controller.signal
    );
```

This keeps lifecycle ownership with the caller.

---

# 46. Why Pass Signal from the Caller?

Consider:

```javascript
function getUsers() {
    const controller =
        new AbortController();

    return fetch("/api/users", {
        signal:
            controller.signal
    });
}
```

The caller has no access to the controller.

So it cannot easily cancel the request.

Better:

```javascript
function getUsers(signal) {
    return fetch(
        "/api/users",
        { signal }
    );
}
```

Now:

```text
Caller
  │
  ├── owns controller
  │
  └── controls lifecycle
         │
         ▼
      Service
```

---

# 47. API Client with Signal

```javascript
async function apiRequest(
    url,
    {
        signal,
        ...options
    } = {}
) {
    const response =
        await fetch(url, {
            ...options,
            signal
        });

    if (!response.ok) {
        throw new Error(
            `HTTP ${response.status}`
        );
    }

    if (
        response.status === 204
    ) {
        return null;
    }

    return response.json();
}
```

Usage:

```javascript
const controller =
    new AbortController();

const users =
    await apiRequest(
        "/api/users",
        {
            signal:
                controller.signal
        }
    );
```

---

# 48. React `useEffect` Problem

Suppose:

```javascript
useEffect(() => {
    async function loadUser() {
        const response =
            await fetch(
                `/api/users/${id}`
            );

        const user =
            await response.json();

        setUser(user);
    }

    loadUser();
}, [id]);
```

If `id` changes quickly:

```text
id = 1
↓
Request User 1

id = 2
↓
Request User 2
```

User 1 might finish after User 2.

That can produce stale state.

---

# 49. React `useEffect` Cleanup with AbortController

A common pattern:

```javascript
useEffect(() => {
    const controller =
        new AbortController();

    async function loadUser() {
        try {
            const response =
                await fetch(
                    `/api/users/${id}`,
                    {
                        signal:
                            controller.signal
                    }
                );

            if (!response.ok) {
                throw new Error(
                    `HTTP ${
                        response.status
                    }`
                );
            }

            const user =
                await response.json();

            setUser(user);
        } catch (error) {
            if (
                error.name ===
                "AbortError"
            ) {
                return;
            }

            setError(error);
        }
    }

    loadUser();

    return () => {
        controller.abort();
    };
}, [id]);
```

---

# 50. What Happens During React Cleanup?

Suppose:

```text
id = 1
```

Effect runs:

```text
Controller A
Request User 1
```

Then:

```text
id = 2
```

Before the new effect setup is applied, React runs the previous cleanup:

```javascript
controller.abort();
```

Then the new effect creates:

```text
Controller B
Request User 2
```

Conceptually:

```text
Effect for ID 1
      │
      ▼
Request 1
      │
 ID changes
      │
      ▼
Cleanup
      │
      ▼
Abort Request 1
      │
      ▼
Effect for ID 2
      │
      ▼
Request 2
```

---

# 51. Component Unmount Cleanup

If a component leaves the UI while a request is active:

```text
Component
   │
   ▼
Request starts
   │
   ▼
Component unmounts
   │
   ▼
Cleanup
   │
   ▼
Abort request
```

This avoids keeping unnecessary work alive after the component no longer needs the result.

---

# 52. One Controller per Effect Execution

Good:

```javascript
useEffect(() => {
    const controller =
        new AbortController();

    // request

    return () => {
        controller.abort();
    };
}, [id]);
```

Each effect execution gets its own controller.

Avoid reusing a controller that has already been aborted.

---

# 53. Do Not Make `useEffect` Callback Async

Avoid:

```javascript
useEffect(async () => {
    const data =
        await getData();
}, []);
```

An async function returns a Promise.

But React expects the effect callback to return:

```text
Nothing

OR

Cleanup function
```

So instead:

```javascript
useEffect(() => {
    async function load() {
        const data =
            await getData();
    }

    load();
}, []);
```

Then cleanup can return normally:

```javascript
return () => {
    controller.abort();
};
```

---

# 54. Cancellation and Loading State

Consider:

```javascript
setLoading(true);

try {
    await fetchData();
} catch (error) {
    // ...
} finally {
    setLoading(false);
}
```

With multiple overlapping requests, an old cancelled request may execute:

```javascript
setLoading(false);
```

while a newer request is still running.

Therefore loading state can itself experience race conditions.

This is why real applications may use:

```text
Request IDs
Request state per operation
Data-fetching libraries
Centralized server-state management
```

rather than a single global Boolean for complex flows.

---

# 55. Cancellation Does Not Undo Server Work

Very important.

Suppose:

```javascript
fetch("/api/orders", {
    method: "POST",
    body: ...
});
```

Then:

```javascript
controller.abort();
```

Cancellation means the client stops/aborts its side of the operation.

It does **not** guarantee that the server has not already processed the request.

Example:

```text
Client sends POST
       │
       ▼
Server receives request
       │
       ▼
Creates order
       │
Client aborts
```

The order may already exist.

---

# 56. Why This Matters for State-Changing Requests

For:

```text
GET
```

aborting generally means you no longer care about the response.

For:

```text
POST
PUT
PATCH
DELETE
```

you cannot assume:

```text
Client aborted
=
Server operation did not happen
```

For critical operations, APIs may need mechanisms such as:

```text
Idempotency keys
Operation IDs
Status endpoints
Transaction design
```

depending on the system.

---

# 57. Cancellation vs Rollback

These are different.

```text
Cancellation
=
Stop/abort ongoing client-side
interaction when possible.


Rollback
=
Reverse an operation that
already happened.
```

AbortController does not provide transaction rollback.

---

# 58. AbortSignal with Event Listeners

Signals can also be useful with event listeners in modern browsers.

Example:

```javascript
const controller =
    new AbortController();

window.addEventListener(
    "resize",
    handleResize,
    {
        signal:
            controller.signal
    }
);
```

Later:

```javascript
controller.abort();
```

can remove the listener automatically.

This can simplify cleanup.

---

# 59. Multiple Event Listeners with One Signal

```javascript
const controller =
    new AbortController();

window.addEventListener(
    "resize",
    handleResize,
    {
        signal:
            controller.signal
    }
);

window.addEventListener(
    "scroll",
    handleScroll,
    {
        signal:
            controller.signal
    }
);

document.addEventListener(
    "click",
    handleClick,
    {
        signal:
            controller.signal
    }
);
```

Then:

```javascript
controller.abort();
```

can clean up all listeners attached with that signal.

Useful when they share the same lifecycle.

---

# 60. AbortController as Lifecycle Management

A useful way to think about AbortController is not only:

```text
Cancel HTTP request
```

but:

```text
Represent the lifetime
of an operation.
```

Example:

```text
Search operation starts
      │
      ├── Fetch request
      ├── Event listener
      └── Custom async work

Search operation ends
      │
      ▼
controller.abort()
```

Anything connected to the signal can respond to the lifecycle ending.

---

# 61. Common Mistake — Reusing an Aborted Controller

Wrong:

```javascript
const controller =
    new AbortController();

controller.abort();

fetch("/api/users", {
    signal:
        controller.signal
});
```

Create a new controller:

```javascript
const controller =
    new AbortController();
```

---

# 62. Common Mistake — Creating Controller but Not Passing Signal

This does nothing:

```javascript
const controller =
    new AbortController();

fetch("/api/users");

controller.abort();
```

Fetch has no connection to the controller.

Correct:

```javascript
fetch("/api/users", {
    signal:
        controller.signal
});
```

---

# 63. Common Mistake — Treating Abort as Normal Failure

Avoid displaying:

```text
Something went wrong!
```

every time a request is intentionally cancelled.

Distinguish cancellation when appropriate.

---

# 64. Common Mistake — Assuming Abort Rolls Back Request

Wrong assumption:

```text
POST started
↓
abort()
↓
Server definitely did nothing
```

The server may already have processed the request.

---

# 65. Common Mistake — Assuming Abort Stops Arbitrary Code

This:

```javascript
controller.abort();
```

does not interrupt:

```javascript
while (true) {
    // synchronous work
}
```

Cancellation must be supported by the operation.

---

# 66. Common Mistake — Forgetting Old Request Race

Even with:

```javascript
await fetch(...)
```

multiple calls can overlap.

`await` does not globally serialize every invocation of a function.

---

# 67. Common Mistake — Sharing Controller Accidentally

```javascript
const controller =
    new AbortController();
```

used for unrelated operations means:

```javascript
controller.abort();
```

cancels all operations using that signal.

Use shared controllers only when the operations share a lifecycle.

---

# 68. Common Mistake — Never Clearing Manual Timeout

Avoid:

```javascript
setTimeout(() => {
    controller.abort();
}, 5000);

await fetch(...);
```

without cleanup.

Better:

```javascript
const timeoutId =
    setTimeout(
        () => controller.abort(),
        5000
    );

try {
    return await fetch(...);
} finally {
    clearTimeout(timeoutId);
}
```

---

# 69. Common Mistake — Using Promise.race as Cancellation

This:

```javascript
await Promise.race([
    fetch(url),
    timeout()
]);
```

does not cancel Fetch automatically.

If actual cancellation is required, use a signal supported by the operation.

---

# 70. Common Mistake — Ignoring Already-Aborted Signal

When creating custom abortable APIs, check:

```javascript
signal.aborted
```

or:

```javascript
signal.throwIfAborted();
```

before starting work.

Otherwise an operation may begin even though cancellation already occurred.

---

# 71. Common Mistake — Forgetting Abort Listener Cleanup

When implementing custom abortable functions, be mindful of event-listener lifecycle.

Using:

```javascript
{ once: true }
```

for an abort listener can be useful because abort only needs to be handled once.

For more complex custom APIs, remove listeners when the operation finishes if necessary.

---

# 72. Interview Question — What Is AbortController?

`AbortController` is a Web API that creates an `AbortSignal` and allows code to signal cancellation by calling:

```javascript
controller.abort();
```

Operations that support the signal can react to that cancellation.

---

# 73. Interview Question — What Is AbortSignal?

`AbortSignal` represents cancellation state and notifications.

It can be passed to supported asynchronous APIs.

Example:

```javascript
fetch(url, {
    signal:
        controller.signal
});
```

---

# 74. Interview Question — Difference Between Controller and Signal?

```text
AbortController
=
Controls cancellation.


AbortSignal
=
Communicates cancellation.
```

The controller calls:

```javascript
abort()
```

The operation receives:

```javascript
signal
```

---

# 75. Interview Question — Can AbortController Be Reused?

A controller can coordinate multiple operations before cancellation.

But once its signal is aborted, it remains aborted.

For a new independent lifecycle, create a new controller.

---

# 76. Interview Question — Can One Controller Cancel Multiple Fetches?

Yes.

Pass the same signal:

```javascript
const signal =
    controller.signal;

fetch(url1, { signal });
fetch(url2, { signal });
fetch(url3, { signal });
```

Then:

```javascript
controller.abort();
```

signals cancellation to all of them.

---

# 77. Interview Question — Does AbortController Cancel Promises?

Not automatically.

A Promise has no universal cancellation mechanism.

The underlying operation must support the `AbortSignal` or implement cancellation behavior itself.

---

# 78. Interview Question — Does Abort Stop JavaScript Execution?

No.

AbortController does not forcibly interrupt arbitrary synchronous JavaScript.

Cancellation is cooperative.

---

# 79. Interview Question — What Is `signal.aborted`?

A Boolean indicating whether the signal has been aborted.

```javascript
if (signal.aborted) {
    // cancellation occurred
}
```

---

# 80. Interview Question — What Is `signal.reason`?

It contains the reason associated with the signal's abortion.

A custom reason can be supplied:

```javascript
controller.abort(
    "User cancelled"
);
```

---

# 81. Interview Question — What Is `throwIfAborted()`?

It throws the signal's abort reason if the signal has already been aborted.

Useful for custom abortable operations.

```javascript
signal.throwIfAborted();
```

---

# 82. Interview Question — AbortController vs Promise.race?

`Promise.race()` determines which Promise settles the race first.

It does not automatically cancel the losing operation.

AbortController provides a cancellation signal to operations that support it.

---

# 83. Interview Question — Does Aborting Fetch Undo Server Work?

No guarantee.

The server may already have received and processed the request.

Client cancellation and server-side rollback are different concepts.

---

# 84. Interview Question — How Do You Implement Fetch Timeout?

A common pattern:

```text
AbortController
+
Timer
```

or, where supported:

```javascript
AbortSignal.timeout(ms)
```

Example:

```javascript
fetch(url, {
    signal:
        AbortSignal.timeout(5000)
});
```

---

# 85. Interview Question — Why Abort Requests in React Cleanup?

To end requests that belong to an old effect lifecycle.

This can:

```text
Avoid unnecessary work
Prevent stale request flows
Simplify cleanup
```

and helps when dependencies change or the component unmounts.

---

# 86. Interview Question — Does AbortController Solve Every Race Condition?

No.

Cancellation can reduce stale work, but application-level correctness may still require techniques such as:

```text
Request IDs
Latest-result checks
State versioning
Data-fetching libraries
```

---

# 87. Interview Question — Can AbortSignal Be Used Outside Fetch?

Yes.

Other APIs can support signals, and your own functions can implement signal-aware cancellation.

Event listeners can also accept signals in supported browser environments.

---

# 88. Scenario — Live Search

Requirement:

```text
User types search query.
Only newest request matters.
```

Possible solution:

```text
Input
  │
  ▼
Debounce
  │
  ▼
Abort old controller
  │
  ▼
Create new controller
  │
  ▼
Fetch
  │
  ▼
Check result is current
  │
  ▼
Update UI
```

This is a strong real-world pattern.

---

# 89. Scenario — Dashboard

Dashboard starts:

```text
User Request
Orders Request
Metrics Request
Notifications Request
```

All belong to the same dashboard lifecycle.

Use:

```javascript
const controller =
    new AbortController();

const signal =
    controller.signal;
```

Pass `signal` to all requests.

If the dashboard is left:

```javascript
controller.abort();
```

---

# 90. Scenario — Independent Operations

Suppose:

```text
Search Request
File Upload
Payment Request
```

These should not automatically cancel together.

Use:

```text
Search Controller
Upload Controller
Payment Controller
```

because they have separate lifecycles.

---

# 91. Scenario — Timeout + Manual Cancel

Requirement:

```text
Cancel after 10 seconds

OR

Cancel when user clicks Cancel
```

Where supported:

```javascript
const controller =
    new AbortController();

const signal =
    AbortSignal.any([
        controller.signal,
        AbortSignal.timeout(10000)
    ]);

await fetch(url, {
    signal
});
```

Manual cancellation:

```javascript
controller.abort();
```

Timeout cancellation:

```text
Occurs automatically
after 10 seconds.
```

---

# 92. Scenario — Custom Processing

Suppose:

```javascript
async function processRecords(
    records,
    signal
) {
    for (
        const record of records
    ) {
        signal.throwIfAborted();

        await processRecord(
            record
        );
    }
}
```

Now a caller can stop future iterations:

```javascript
controller.abort();
```

This demonstrates cooperative cancellation beyond Fetch.

---

# 93. AbortController Mental Model

```text
          AbortController
                │
                │
        ┌───────┴────────┐
        │                │
        ▼                ▼
     signal           abort()
        │                │
        │                │
        ▼                │
 Async Operation         │
        │                │
        │                │
        └──── listens ◄──┘
                │
                ▼
          Abort occurs
                │
        ┌───────┴────────┐
        │                │
        ▼                ▼
 signal.aborted       true
 signal.reason        reason
        │
        ▼
 Operation reacts
        │
        ▼
 Stop / reject / cleanup
```

---

# 94. Fetch Cancellation Mental Model

```text
Create Controller
      │
      ▼
Get Signal
      │
      ▼
fetch(url, { signal })
      │
      ▼
Request Running
      │
      ├───────────────┐
      │               │
      ▼               ▼
Completes          abort()
      │               │
      ▼               ▼
Response        Signal Aborted
                      │
                      ▼
               Fetch cancellation
                      │
                      ▼
                  Rejection
```

---

# 95. React Mental Model

```text
Component / Effect
        │
        ▼
Create Controller
        │
        ▼
Start Request
        │
        ▼
Pass Signal
        │
        ▼
Request Running
        │
        ├─────────────────┐
        │                 │
        ▼                 ▼
Response            Cleanup occurs
        │                 │
        ▼                 ▼
Update State          abort()
                          │
                          ▼
                   Cancel old request
```

---

# 96. AbortController Cheat Sheet

```text
CREATE
================================

const controller =
    new AbortController();


SIGNAL
================================

controller.signal


ABORT
================================

controller.abort();


FETCH
================================

fetch(url, {
    signal:
        controller.signal
});


CHECK STATE
================================

signal.aborted


ABORT REASON
================================

signal.reason


THROW IF ABORTED
================================

signal.throwIfAborted();


LISTEN
================================

signal.addEventListener(
    "abort",
    callback
);


MULTIPLE REQUESTS
================================

Use same signal.


INDEPENDENT REQUESTS
================================

Use separate controllers.


TIMEOUT
================================

AbortSignal.timeout(ms)


COMBINE SIGNALS
================================

AbortSignal.any([
    signal1,
    signal2
]);


REACT CLEANUP
================================

return () => {
    controller.abort();
};


PROMISE.RACE
================================

Stops waiting.

Does not automatically
cancel underlying work.


ABORTCONTROLLER
================================

Signals cancellation to
supported operations.


SERVER WORK
================================

Abort does not guarantee
server-side rollback.


NEW OPERATION
================================

Create a new controller
if previous signal was
already aborted.
```

---

# 97. Important Rules to Remember

```text
1. AbortController is a Web API.

2. AbortController creates an AbortSignal.

3. controller.signal gives access to the signal.

4. controller.abort() aborts the signal.

5. Signals can be passed to supported operations.

6. Fetch supports AbortSignal.

7. Cancellation is cooperative.

8. AbortController does not magically stop arbitrary JavaScript.

9. signal.aborted indicates cancellation state.

10. An aborted signal stays aborted.

11. AbortController cannot be reset.

12. Create a new controller for a new lifecycle.

13. An already-aborted signal should not be treated as fresh.

14. signal.reason contains the abort reason.

15. abort() can receive a custom reason.

16. throwIfAborted() throws when already aborted.

17. AbortSignal emits an abort event.

18. Custom async APIs can support AbortSignal.

19. Custom APIs should consider already-aborted signals.

20. One controller can cancel multiple operations.

21. Share controllers only when operations share a lifecycle.

22. Use separate controllers for independently cancellable work.

23. Fetch can be aborted while pending.

24. Cancellation can affect response-body consumption.

25. Abort is often an intentional control-flow event.

26. Do not always display cancellation as an application error.

27. Manual timeouts can use AbortController + setTimeout.

28. Clear manual timeout timers after completion.

29. AbortSignal.timeout() can create timeout signals where supported.

30. AbortSignal.any() can combine signals where supported.

31. User cancellation and timeout cancellation can be combined.

32. Promise.race() does not automatically cancel losing operations.

33. AbortController signals actual cancellation to supported operations.

34. Async/await does not prevent race conditions.

35. Multiple invocations of an async function can overlap.

36. Cancelling stale requests can help prevent stale work.

37. Request IDs can prevent stale state updates.

38. Cancellation and stale-result checking solve related but different problems.

39. Debouncing reduces how many requests start.

40. Cancellation can stop requests that are no longer needed.

41. Service functions should often accept a signal.

42. The caller should usually own request lifecycle cancellation.

43. API clients can forward signals to fetch.

44. React effects can create controllers per effect execution.

45. React cleanup can abort requests from old effects.

46. Do not make useEffect callbacks async.

47. Loading state can also suffer from race conditions.

48. Cancellation does not guarantee server work was undone.

49. AbortController does not provide rollback.

50. State-changing requests need careful cancellation semantics.

51. Critical APIs may need idempotency mechanisms.

52. Signals can be used with supported event listeners.

53. A shared signal can clean up multiple related resources.

54. AbortController can represent operation lifecycle.

55. Do not create a controller without passing its signal.

56. Do not reuse an already-aborted controller for new work.

57. Do not confuse cancellation with Promise rejection semantics.

58. Do not confuse cancellation with server-side rollback.

59. Do not assume AbortController solves every race condition.

60. AbortController is especially useful with Fetch, React effects, and live search.
```

---

# AbortController in One Sentence

> `AbortController` provides a reusable cancellation mechanism by creating an `AbortSignal` that supported asynchronous operations can observe and respond to when `abort()` is called.

---

# Final Mental Model

```text
                 Operation Starts
                        │
                        ▼
             new AbortController()
                        │
                        ▼
                controller.signal
                        │
                        ▼
              Pass signal to work
                        │
                        ▼
                Operation Running
                        │
            ┌───────────┴───────────┐
            │                       │
            ▼                       ▼
       Work finishes           Work obsolete
            │                       │
            ▼                       ▼
          Result             controller.abort()
                                    │
                                    ▼
                            signal.aborted
                               = true
                                    │
                                    ▼
                          Operation observes
                              cancellation
                                    │
                                    ▼
                           Stop / Reject /
                               Cleanup
```

For request lifecycle:

```text
Does this request
still matter?
      │
   ┌──┴──┐
   │     │
  YES    NO
   │     │
   ▼     ▼

Continue      Abort
request       request
```

For overlapping requests:

```text
Old Request
     │
     ▼
New Request Starts
     │
     ├──────────────► Abort Old
     │
     ▼
Create New Controller
     │
     ▼
Start New Request
     │
     ▼
Newest Result
     │
     ▼
Update UI
```
