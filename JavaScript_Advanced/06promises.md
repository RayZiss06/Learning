# Promises

A **Promise** is an object representing the eventual completion or failure of an asynchronous operation and its resulting value.

Promises provide a structured way to:

```text
Start asynchronous work
        ↓
Represent its future result
        ↓
Handle success
        ↓
Handle failure
        ↓
Chain further operations
```

Promises are fundamental to modern JavaScript:

```javascript
fetch()
    .then(...)
    .catch(...);
```

and:

```javascript
const response = await fetch(url);
```

because `async/await` is built on Promise semantics.

---

# 1. Why Promises Exist

Before Promises, asynchronous operations commonly relied heavily on callbacks.

Example:

```javascript
getUser(1, user => {
    getOrders(user.id, orders => {
        getOrder(orders[0].id, order => {
            console.log(order);
        });
    });
});
```

As workflows become larger, this can create:

```text
Deep nesting
Complex error handling
Poor readability
Difficult composition
Callback hell
```

Promises allow asynchronous operations to be represented as values.

Instead:

```javascript
getUser(1)
    .then(user => getOrders(user.id))
    .then(orders => getOrder(orders[0].id))
    .then(order => console.log(order))
    .catch(error => console.error(error));
```

---

# 2. Creating a Promise

A Promise can be created using:

```javascript
new Promise(...)
```

Syntax:

```javascript
const promise = new Promise((resolve, reject) => {
    // asynchronous operation
});
```

The function passed to `Promise` is called the:

```text
Executor Function
```

It receives:

```javascript
resolve
reject
```

---

# 3. Promise Executor

Example:

```javascript
const promise = new Promise((resolve, reject) => {
    console.log("Executor running");
});
```

Important:

> The Promise executor runs synchronously when the Promise is constructed.

Example:

```javascript
console.log("A");

new Promise((resolve, reject) => {
    console.log("B");
});

console.log("C");
```

Output:

```text
A
B
C
```

Creating a Promise does **not** automatically make the executor asynchronous.

---

# 4. `resolve()`

`resolve()` tells the Promise resolution mechanism to resolve the Promise with a given value.

Simple example:

```javascript
const promise = new Promise((resolve, reject) => {
    resolve("Success");
});
```

Handle it:

```javascript
promise.then(value => {
    console.log(value);
});
```

Output:

```text
Success
```

---

# 5. `reject()`

`reject()` rejects the Promise with a reason.

Example:

```javascript
const promise = new Promise((resolve, reject) => {
    reject(new Error("Something went wrong"));
});
```

Handle it:

```javascript
promise.catch(error => {
    console.error(error.message);
});
```

Output:

```text
Something went wrong
```

Prefer rejecting with an `Error` object:

```javascript
reject(new Error("Request failed"));
```

rather than:

```javascript
reject("Request failed");
```

because `Error` objects contain useful debugging information such as stack traces.

---

# 6. Promise States

A Promise has three fundamental states:

```text
Pending
Fulfilled
Rejected
```

Flow:

```text
              Promise
                 │
                 ▼
              Pending
              /     \
             /       \
            ▼         ▼
      Fulfilled     Rejected
```

### Pending

The operation has not settled.

### Fulfilled

The operation completed successfully.

### Rejected

The operation failed.

---

# 7. Settled Promise

A Promise is called:

```text
Settled
```

when it is either:

```text
Fulfilled
```

or:

```text
Rejected
```

Therefore:

```text
Pending
   ↓
Settled
   │
   ├── Fulfilled
   │
   └── Rejected
```

Once settled, a Promise cannot change to another state.

---

# 8. Promise Settles Only Once

Example:

```javascript
const promise = new Promise((resolve, reject) => {
    resolve("First");

    resolve("Second");

    reject(new Error("Failed"));
});
```

Result:

```text
First
```

The later attempts do not change the Promise's outcome.

Think:

```text
Pending
↓
Fulfilled

DONE
```

It cannot later become rejected.

---

# 9. `.then()`

`.then()` registers handlers for a Promise.

Most commonly:

```javascript
promise.then(value => {
    console.log(value);
});
```

Example:

```javascript
Promise.resolve(100)
    .then(value => {
        console.log(value);
    });
```

Output:

```text
100
```

---

# 10. `.then()` Can Handle Rejection Too

Full form:

```javascript
promise.then(
    onFulfilled,
    onRejected
);
```

Example:

```javascript
Promise.reject(new Error("Failed"))
    .then(
        value => {
            console.log(value);
        },
        error => {
            console.error(error.message);
        }
    );
```

However, using:

```javascript
.catch(...)
```

is usually clearer for error handling.

---

# 11. `.catch()`

`.catch()` handles Promise rejection.

```javascript
promise.catch(error => {
    console.error(error);
});
```

Conceptually:

```javascript
promise.catch(onRejected);
```

is equivalent to:

```javascript
promise.then(undefined, onRejected);
```

---

# 12. `.finally()`

`.finally()` executes when the Promise settles, whether fulfilled or rejected.

Example:

```javascript
fetch("/api/users")
    .then(response => {
        return response.json();
    })
    .catch(error => {
        console.error(error);
    })
    .finally(() => {
        console.log("Request finished");
    });
```

Useful for cleanup:

```text
Stop loading indicator
Close connection
Release resources
Reset UI state
```

---

# 13. `.finally()` Does Not Receive the Result

Unlike `.then()`:

```javascript
promise.then(value => {
    console.log(value);
});
```

`.finally()` is not passed the fulfillment value:

```javascript
promise.finally(() => {
    console.log("Finished");
});
```

Its main purpose is cleanup, not transforming the result.

---

# 14. `.finally()` Usually Preserves the Outcome

Example:

```javascript
Promise.resolve(10)
    .finally(() => {
        console.log("Cleanup");
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
Cleanup
10
```

The original value passes through.

Similarly:

```javascript
Promise.reject(new Error("Failed"))
    .finally(() => {
        console.log("Cleanup");
    })
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Cleanup
Failed
```

---

# 15. But `.finally()` Can Override the Outcome

If `finally()` throws:

```javascript
Promise.resolve(10)
    .finally(() => {
        throw new Error("Cleanup failed");
    })
    .then(console.log)
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Cleanup failed
```

Similarly, if `finally()` returns a rejected Promise, the chain becomes rejected with that reason.

---

# 16. `.then()` Returns a New Promise

This is one of the most important Promise concepts.

```javascript
const promise1 = Promise.resolve(10);

const promise2 = promise1.then(value => {
    return value * 2;
});
```

`promise2` is a **different Promise**.

```javascript
console.log(promise1 === promise2);
```

Output:

```text
false
```

This behavior enables:

```text
Promise Chaining
```

---

# 17. Promise Chaining

Example:

```javascript
Promise.resolve(10)
    .then(value => {
        return value * 2;
    })
    .then(value => {
        return value + 5;
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
25
```

Flow:

```text
10
↓
10 × 2
↓
20
↓
20 + 5
↓
25
```

---

# 18. Returning a Value from `.then()`

When a `.then()` handler returns a normal value:

```javascript
return 20;
```

the Promise returned by `.then()` becomes fulfilled with:

```text
20
```

Example:

```javascript
Promise.resolve(10)
    .then(value => {
        return value * 2;
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
20
```

Conceptually:

```javascript
return 20;
```

acts like the next Promise eventually receiving:

```javascript
Promise.resolve(20);
```

---

# 19. Returning Nothing from `.then()`

Example:

```javascript
Promise.resolve(10)
    .then(value => {
        console.log(value);
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
10
undefined
```

Why?

The first handler does not explicitly return anything.

JavaScript functions implicitly return:

```javascript
undefined
```

Therefore the next Promise fulfills with `undefined`.

---

# 20. Common Mistake — Forgetting `return`

Incorrect:

```javascript
getUser()
    .then(user => {
        getOrders(user.id);
    })
    .then(orders => {
        console.log(orders);
    });
```

The first handler returns:

```text
undefined
```

So `orders` will not contain the result of `getOrders()`.

Correct:

```javascript
getUser()
    .then(user => {
        return getOrders(user.id);
    })
    .then(orders => {
        console.log(orders);
    });
```

Or:

```javascript
getUser()
    .then(user => getOrders(user.id))
    .then(orders => {
        console.log(orders);
    });
```

---

# 21. Returning a Promise from `.then()`

Suppose:

```javascript
function getUser() {
    return Promise.resolve({
        id: 1,
        name: "John"
    });
}
```

Then:

```javascript
getUser()
    .then(user => {
        return getOrders(user.id);
    })
    .then(orders => {
        console.log(orders);
    });
```

The chain waits for the Promise returned by:

```javascript
getOrders(user.id)
```

before the next `.then()` runs.

---

# 22. Promise Adoption

Consider:

```javascript
const inner = new Promise(resolve => {
    setTimeout(() => {
        resolve("Done");
    }, 1000);
});

const outer = Promise.resolve(inner);
```

The outer Promise does not simply fulfill with the Promise object as an ordinary value.

It **adopts** the eventual state of `inner`.

Therefore:

```javascript
outer.then(value => {
    console.log(value);
});
```

eventually logs:

```text
Done
```

---

# 23. Resolution Is Not Always Fulfillment

This is an advanced terminology distinction.

People commonly say:

```text
resolve()
=
fulfill
```

but they are not always identical.

Consider:

```javascript
const promise = new Promise(resolve => {
    resolve(
        new Promise(innerResolve => {
            setTimeout(() => {
                innerResolve("Done");
            }, 1000);
        })
    );
});
```

The outer Promise has been **resolved** to follow the inner Promise, but its final fulfillment waits for the inner Promise.

Therefore:

```text
Resolved
```

does not always mean:

```text
Already fulfilled with final value
```

---

# 24. Promise Resolution Procedure

When a Promise is resolved with some value `x`, JavaScript determines how to handle `x`.

Simplified:

```text
resolve(x)
   │
   ├── Normal value
   │       ↓
   │   Fulfill with x
   │
   ├── Promise
   │       ↓
   │   Adopt its state
   │
   └── Thenable
           ↓
       Follow its behavior
```

This mechanism allows Promise chains to flatten naturally.

---

# 25. Promise Flattening

Consider:

```javascript
Promise.resolve(10)
    .then(value => {
        return Promise.resolve(
            Promise.resolve(
                Promise.resolve(value * 2)
            )
        );
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
20
```

You do not get:

```text
Promise<Promise<Promise<20>>>
```

Promises adopt nested Promise states.

---

# 26. Thenables

A **thenable** is an object with a callable `.then()` method.

Example:

```javascript
const thenable = {
    then(resolve, reject) {
        resolve("Hello");
    }
};
```

Then:

```javascript
Promise.resolve(thenable)
    .then(value => {
        console.log(value);
    });
```

Output:

```text
Hello
```

Promise resolution can assimilate thenables.

---

# 27. Why Thenables Matter

Promises may interact with:

```text
Third-party libraries
Custom Promise-like objects
Other JavaScript environments
Interop mechanisms
```

Rather than only recognizing native:

```javascript
Promise
```

the resolution procedure can work with compatible thenable objects.

---

# 28. Throwing Inside `.then()`

If a `.then()` handler throws:

```javascript
Promise.resolve(10)
    .then(value => {
        throw new Error("Failed");
    });
```

the Promise returned by `.then()` becomes rejected.

Therefore:

```javascript
Promise.resolve(10)
    .then(value => {
        throw new Error("Failed");
    })
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Failed
```

---

# 29. Return vs Throw

Inside `.then()`:

```javascript
return 10;
```

causes:

```text
Fulfilled Promise
value = 10
```

While:

```javascript
throw new Error("Failed");
```

causes:

```text
Rejected Promise
reason = Error
```

Mental model:

```text
return value
      ↓
FULFILLED


throw error
      ↓
REJECTED
```

---

# 30. Returning `Promise.reject()`

This:

```javascript
.then(() => {
    return Promise.reject(
        new Error("Failed")
    );
})
```

also causes the returned chain Promise to reject.

So these have similar chain effects:

```javascript
throw new Error("Failed");
```

and:

```javascript
return Promise.reject(
    new Error("Failed")
);
```

For synchronous validation inside a handler, `throw` is often simpler.

---

# 31. Error Propagation

Consider:

```javascript
Promise.resolve()
    .then(() => {
        throw new Error("Failed");
    })
    .then(() => {
        console.log("A");
    })
    .then(() => {
        console.log("B");
    })
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Failed
```

The fulfilled handlers are skipped while rejection propagates through the chain until an appropriate rejection handler is found.

---

# 32. Promise Chain Mental Model

```text
Promise
   ↓
.then()
   ↓
Promise
   ↓
.then()
   ↓
Promise
   ↓
.catch()
   ↓
Promise
```

Each chain method creates another Promise.

Think of a Promise chain as:

```text
Result
↓
Transformation
↓
Result
↓
Transformation
↓
Result
```

rather than one Promise being modified repeatedly.

---

# 33. Error Recovery with `.catch()`

A `.catch()` handler can recover from an error.

Example:

```javascript
Promise.reject(new Error("Failed"))
    .catch(error => {
        console.log(error.message);

        return "Fallback";
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
Failed
Fallback
```

Why?

The catch handler returned:

```javascript
"Fallback"
```

Therefore the Promise returned by `.catch()` is fulfilled.

---

# 34. Catch Can Re-Throw

If the error should continue:

```javascript
Promise.reject(new Error("Failed"))
    .catch(error => {
        console.error(error);

        throw error;
    })
    .catch(error => {
        console.log(
            "Handled elsewhere"
        );
    });
```

The first catch logs the error and rethrows it.

The rejection continues down the chain.

---

# 35. Catch Can Return Rejected Promise

Similarly:

```javascript
.catch(error => {
    return Promise.reject(error);
})
```

keeps the chain rejected.

---

# 36. Catch Placement Matters

Compare:

```javascript
getUser()
    .then(processUser)
    .then(saveUser)
    .catch(handleError);
```

This catch can handle rejections arising from:

```text
getUser()
processUser
saveUser
```

assuming those failures propagate through the chain.

But:

```javascript
getUser()
    .catch(handleUserError)
    .then(processUser);
```

behaves differently because the catch can potentially recover and allow the chain to continue.

---

# 37. `.then(success, failure)` vs `.catch()`

Consider:

```javascript
promise.then(
    value => {
        throw new Error("Handler failed");
    },
    error => {
        console.log("Rejected");
    }
);
```

The rejection handler passed as the second argument does **not** handle an error thrown by the sibling fulfillment handler.

Why?

Both handlers belong to the same `.then()` call and determine the outcome of the **new Promise returned by that call**.

Instead:

```javascript
promise
    .then(value => {
        throw new Error("Handler failed");
    })
    .catch(error => {
        console.log(error.message);
    });
```

The catch is attached to the Promise returned by `.then()`, so it can catch that error.

This is one reason `.catch()` is often easier to reason about.

---

# 38. Promise Handlers Execute Asynchronously

Even an already fulfilled Promise does not run `.then()` synchronously.

```javascript
console.log("A");

Promise.resolve("B")
    .then(value => {
        console.log(value);
    });

console.log("C");
```

Output:

```text
A
C
B
```

Promise reactions are scheduled as microtasks.

---

# 39. Promise Executor vs Promise Handler

This distinction is extremely important.

```javascript
new Promise(resolve => {
    console.log("Executor");

    resolve();
}).then(() => {
    console.log("Handler");
});

console.log("End");
```

Output:

```text
Executor
End
Handler
```

Because:

```text
Promise Executor
=
Synchronous


.then Handler
=
Microtask
```

---

# 40. Promise Constructor Example

```javascript
console.log("1");

const promise = new Promise(resolve => {
    console.log("2");

    resolve("4");

    console.log("3");
});

promise.then(value => {
    console.log(value);
});

console.log("5");
```

Output:

```text
1
2
3
5
4
```

Reason:

```text
Synchronous:
1
2
3
5

Microtask:
4
```

---

# 41. `Promise.resolve()`

`Promise.resolve()` creates/resolves a Promise from a value.

```javascript
const promise =
    Promise.resolve(100);

promise.then(value => {
    console.log(value);
});
```

Output:

```text
100
```

Useful when an API needs to return a Promise consistently.

---

# 42. `Promise.resolve()` with Promise

```javascript
const promise1 =
    Promise.resolve(100);

const promise2 =
    Promise.resolve(promise1);

console.log(
    promise1 === promise2
);
```

For a native Promise of the same constructor, this is typically:

```text
true
```

`Promise.resolve()` can return the existing Promise rather than wrapping it unnecessarily.

---

# 43. `Promise.reject()`

Creates an already rejected Promise:

```javascript
const promise =
    Promise.reject(
        new Error("Failed")
    );

promise.catch(error => {
    console.log(error.message);
});
```

Output:

```text
Failed
```

---

# 44. `Promise.all()`

`Promise.all()` accepts an iterable of values/Promises and returns a Promise.

Example:

```javascript
const result =
    await Promise.all([
        Promise.resolve(10),
        Promise.resolve(20),
        Promise.resolve(30)
    ]);

console.log(result);
```

Output:

```javascript
[10, 20, 30]
```

---

# 45. `Promise.all()` Preserves Input Order

Suppose:

```javascript
const p1 = new Promise(resolve => {
    setTimeout(() => resolve("A"), 3000);
});

const p2 = new Promise(resolve => {
    setTimeout(() => resolve("B"), 1000);
});

const p3 = new Promise(resolve => {
    setTimeout(() => resolve("C"), 2000);
});
```

Then:

```javascript
const result =
    await Promise.all([
        p1,
        p2,
        p3
    ]);

console.log(result);
```

Output:

```javascript
["A", "B", "C"]
```

Even though completion order is approximately:

```text
B
C
A
```

the result array follows:

```text
Input order
```

---

# 46. `Promise.all()` Rejects Early

```javascript
Promise.all([
    Promise.resolve("A"),
    Promise.reject(
        new Error("Failed")
    ),
    Promise.resolve("C")
])
    .then(console.log)
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Failed
```

The returned Promise rejects once one of its inputs rejects.

---

# 47. `Promise.all()` Does Not Cancel Work

Suppose:

```javascript
const p1 = requestA();
const p2 = requestB();
const p3 = requestC();

await Promise.all([
    p1,
    p2,
    p3
]);
```

If `p2` rejects:

```text
Promise.all
↓
Rejects
```

but:

```text
requestA
requestC
```

do not automatically stop.

Cancellation requires an explicit mechanism supported by the operation, such as:

```javascript
AbortController
```

for applicable Web APIs.

---

# 48. `Promise.all()` Accepts Non-Promise Values

```javascript
Promise.all([
    Promise.resolve(10),
    20,
    30
]).then(values => {
    console.log(values);
});
```

Output:

```javascript
[10, 20, 30]
```

Non-Promise values are treated as fulfilled values.

---

# 49. `Promise.allSettled()`

`Promise.allSettled()` waits until all inputs settle.

Example:

```javascript
const results =
    await Promise.allSettled([
        Promise.resolve("A"),
        Promise.reject("B"),
        Promise.resolve("C")
    ]);

console.log(results);
```

Conceptually:

```javascript
[
    {
        status: "fulfilled",
        value: "A"
    },
    {
        status: "rejected",
        reason: "B"
    },
    {
        status: "fulfilled",
        value: "C"
    }
]
```

---

# 50. When to Use `Promise.allSettled()`

Use it when:

```text
Every operation should be attempted
and you need every outcome.
```

Example:

```text
Send 10 notifications
↓
8 succeed
2 fail
↓
Need report for all 10
```

`Promise.all()` may be inappropriate because one failure rejects the aggregate Promise early.

`Promise.allSettled()` gives every result.

---

# 51. `Promise.race()`

`Promise.race()` settles when the first input settles.

Example:

```javascript
const result =
    await Promise.race([
        new Promise(resolve => {
            setTimeout(
                () => resolve("A"),
                1000
            );
        }),

        new Promise(resolve => {
            setTimeout(
                () => resolve("B"),
                2000
            );
        })
    ]);

console.log(result);
```

Output:

```text
A
```

---

# 52. `Promise.race()` Includes Rejections

The first **settled** Promise wins.

Example:

```javascript
Promise.race([
    Promise.reject(
        new Error("Failed")
    ),

    new Promise(resolve => {
        setTimeout(
            () => resolve("Success"),
            1000
        );
    })
])
    .then(console.log)
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
Failed
```

So:

```text
race()
=
First settled

fulfilled OR rejected
```

---

# 53. Timeout Pattern with `Promise.race()`

Conceptually:

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

Promise.race([
    fetch("/api/users"),
    timeout(5000)
]);
```

This can make the returned race Promise reject after the timeout wins.

However:

> `Promise.race()` itself does not cancel the fetch.

The underlying request may continue unless explicitly aborted.

For actual request cancellation, use mechanisms such as `AbortController`.

---

# 54. `Promise.any()`

`Promise.any()` fulfills with the first input that fulfills.

Example:

```javascript
const result =
    await Promise.any([
        Promise.reject("A"),
        Promise.resolve("B"),
        Promise.resolve("C")
    ]);

console.log(result);
```

Output:

```text
B
```

Rejections are ignored while another input may still fulfill.

---

# 55. `Promise.any()` When All Reject

If all input Promises reject:

```javascript
Promise.any([
    Promise.reject("A"),
    Promise.reject("B"),
    Promise.reject("C")
])
    .catch(error => {
        console.log(error);
    });
```

the returned Promise rejects with:

```text
AggregateError
```

The error contains information about the collection of rejection reasons.

---

# 56. Promise Static Methods Comparison

```text
Promise.all()
================================

Need ALL successes.

Rejects when an input rejects.

Results preserve input order.


Promise.allSettled()
================================

Need ALL outcomes.

Waits for every input.

Returns fulfilled/rejected status.


Promise.race()
================================

First settlement wins.

Success OR failure.


Promise.any()
================================

First fulfillment wins.

Individual failures ignored
while success remains possible.

Rejects with AggregateError
if all reject.
```

---

# 57. Sequential Promise Execution

Suppose:

```javascript
const user =
    await getUser();

const orders =
    await getOrders(user.id);
```

This is sequential:

```text
getUser
████████

        getOrders
        ████████
```

Necessary because `getOrders()` needs:

```javascript
user.id
```

---

# 58. Concurrent Promise Execution

If operations are independent:

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
getUsers
████████████

getProducts
████████████████
```

The operations overlap.

---

# 59. Common Performance Mistake

Potentially inefficient:

```javascript
const users =
    await getUsers();

const products =
    await getProducts();

const orders =
    await getOrders();
```

If independent:

```text
Users
████

    Products
    █████

         Orders
         █████
```

Better:

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

Conceptually:

```text
Users
████████

Products
████████████

Orders
██████
```

---

# 60. Promise Composition

Promises become powerful because asynchronous operations can be composed.

Example:

```javascript
getUser()
    .then(user => {
        return Promise.all([
            getOrders(user.id),
            getNotifications(user.id)
        ]);
    })
    .then(([orders, notifications]) => {
        return {
            orders,
            notifications
        };
    })
    .then(data => {
        console.log(data);
    })
    .catch(error => {
        console.error(error);
    });
```

This combines:

```text
Sequential dependency
+
Concurrent independent work
```

---

# 61. Real API Example

```javascript
function getUsers() {
    return fetch("/api/users")
        .then(response => {
            if (!response.ok) {
                throw new Error(
                    `HTTP ${response.status}`
                );
            }

            return response.json();
        });
}
```

Usage:

```javascript
getUsers()
    .then(users => {
        console.log(users);
    })
    .catch(error => {
        console.error(
            "Failed:",
            error
        );
    });
```

---

# 62. Fetch + Promise Chain

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
        return users.filter(
            user => user.active
        );
    })
    .then(activeUsers => {
        console.log(activeUsers);
    })
    .catch(error => {
        console.error(error);
    })
    .finally(() => {
        console.log("Finished");
    });
```

Flow:

```text
Fetch
↓
Check HTTP response
↓
Parse JSON
↓
Filter users
↓
Use data

If failure
↓
catch()

Finally
↓
cleanup
```

---

# 63. Common Mistake — Nested Promises

Avoid unnecessary nesting:

```javascript
getUser().then(user => {
    getOrders(user.id).then(orders => {
        console.log(orders);
    });
});
```

Better:

```javascript
getUser()
    .then(user => {
        return getOrders(user.id);
    })
    .then(orders => {
        console.log(orders);
    });
```

This keeps one chain.

---

# 64. Why Promise Nesting Is Problematic

Nested chains can recreate the same structural problems as callback hell:

```text
Promise
    Promise
        Promise
            Promise
```

Flat chains are easier for:

```text
Reading
Error handling
Composition
Debugging
Reasoning
```

---

# 65. Common Mistake — Creating Unnecessary Promises

Avoid:

```javascript
function getUsers() {
    return new Promise((resolve, reject) => {
        fetch("/api/users")
            .then(resolve)
            .catch(reject);
    });
}
```

`fetch()` already returns a Promise.

Simply:

```javascript
function getUsers() {
    return fetch("/api/users");
}
```

This unnecessary wrapping is sometimes called the:

```text
Promise Constructor Antipattern
```

---

# 66. When `new Promise()` Is Appropriate

`new Promise()` is useful when converting callback-style APIs into Promise-based APIs.

Example:

```javascript
function delay(ms) {
    return new Promise(resolve => {
        setTimeout(resolve, ms);
    });
}
```

Usage:

```javascript
delay(1000).then(() => {
    console.log("1 second passed");
});
```

Here `setTimeout()` itself does not return a Promise, so wrapping it is useful.

---

# 67. Promisification

Converting callback-based behavior:

```javascript
function operation(callback) {
    // ...
}
```

into:

```javascript
function operation() {
    return new Promise(
        (resolve, reject) => {
            // ...
        }
    );
}
```

is commonly called:

```text
Promisification
```

---

# 68. Unhandled Promise Rejection

Consider:

```javascript
Promise.reject(
    new Error("Failed")
);
```

If no rejection handler is attached appropriately, the environment may report an:

```text
Unhandled Promise Rejection
```

This can indicate a bug.

---

# 69. Common Cause of Unhandled Rejection

```javascript
async function load() {
    throw new Error("Failed");
}

load();
```

Remember:

```text
async function
↓
returns Promise
```

Therefore the thrown error becomes a rejected Promise.

If nobody handles it:

```text
Unhandled rejection
```

Handle:

```javascript
load().catch(error => {
    console.error(error);
});
```

or from another async context:

```javascript
try {
    await load();
} catch (error) {
    console.error(error);
}
```

---

# 70. Fire-and-Forget Promises

Sometimes an operation is intentionally started without awaiting its result.

Example:

```javascript
sendAnalytics();
```

But if it can reject, ignoring it can cause unhandled rejection issues.

A deliberate pattern might be:

```javascript
void sendAnalytics()
    .catch(error => {
        console.error(error);
    });
```

The important idea:

> If you intentionally do not await a Promise, still consider how its failures should be handled.

---

# 71. Promise Immutability

Once a Promise settles:

```text
Its state cannot change.
Its result cannot change.
```

But calling:

```javascript
promise.then(...)
```

does not modify the original Promise.

It creates a new one.

Example:

```javascript
const original =
    Promise.resolve(10);

const doubled =
    original.then(value => {
        return value * 2;
    });

original.then(console.log);
doubled.then(console.log);
```

Output:

```text
10
20
```

---

# 72. Multiple Handlers Can Observe Same Promise

```javascript
const promise =
    Promise.resolve(10);

promise.then(value => {
    console.log(value);
});

promise.then(value => {
    console.log(value * 2);
});

promise.then(value => {
    console.log(value * 3);
});
```

Output:

```text
10
20
30
```

A Promise's result can be observed by multiple handlers.

Promises are not "consumed" after one `.then()`.

---

# 73. Promise Is Not the Async Operation Itself

This distinction matters.

A Promise is:

```text
An object representing
the eventual outcome.
```

The underlying operation may be:

```text
Network request
Timer
Database query
File operation
Worker operation
```

The Promise represents the result of that work.

---

# 74. Promise Does Not Automatically Start on `.then()`

Consider:

```javascript
const promise = new Promise(resolve => {
    console.log("Started");

    setTimeout(() => {
        resolve("Done");
    }, 1000);
});
```

Output immediately includes:

```text
Started
```

even if you never call:

```javascript
promise.then(...)
```

Why?

The executor runs when the Promise is constructed.

Promises are generally **eager**, not lazy.

---

# 75. Eager Promise Example

```javascript
console.log("A");

const promise = new Promise(resolve => {
    console.log("B");

    resolve();
});

console.log("C");
```

Output:

```text
A
B
C
```

The Promise does not wait for:

```javascript
.then()
```

before its executor runs.

---

# 76. Lazy Behavior Can Be Created with Functions

If you want work to begin only when requested:

```javascript
function createRequest() {
    return fetch("/api/users");
}
```

Now:

```javascript
const fn = createRequest;
```

does not start the request.

Only:

```javascript
const promise = createRequest();
```

does.

So functions can act as factories for Promise-producing operations.

---

# 77. Promise and Event Loop

Recall:

```javascript
console.log("A");

Promise.resolve()
    .then(() => {
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

Because:

```text
Synchronous
───────────
A
C


Microtask
───────────
B
```

Promises rely heavily on microtask scheduling.

---

# 78. Promise Chain Scheduling

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");
    })
    .then(() => {
        console.log("B");
    });

Promise.resolve()
    .then(() => {
        console.log("C");
    });
```

Output:

```text
A
C
B
```

Initial microtasks:

```text
A
C
```

After A completes:

```text
B
```

becomes eligible.

Queue becomes:

```text
C
B
```

Therefore:

```text
A
C
B
```

---

# 79. Promise Handler Returning Promise

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");

        return Promise.resolve("B");
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
A
B
```

The second handler waits for the Promise returned by the first handler.

---

# 80. Promise Handler Returning Delayed Promise

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");

        return new Promise(resolve => {
            setTimeout(() => {
                resolve("B");
            }, 1000);
        });
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
A

[approximately 1 second later]

B
```

The chain adopts the returned Promise.

---

# 81. Self-Resolution Is Invalid

A Promise cannot resolve with itself.

Conceptually:

```javascript
let promise2;

const promise1 =
    Promise.resolve();

promise2 = promise1.then(() => {
    return promise2;
});
```

This creates a chaining cycle.

The Promise must reject with a:

```text
TypeError
```

because it cannot wait for itself to resolve.

---

# 82. Promise Dependency Chain

Valid:

```text
Promise A
↓
Promise B
↓
Promise C
```

Invalid:

```text
Promise A
↓
Promise B
↓
Promise B
```

The latter creates a cycle.

---

# 83. Promise Result vs Promise Reference

Consider:

```javascript
const promise =
    Promise.resolve(10);

console.log(promise);
```

You are looking at the Promise object.

To access its eventual fulfillment value:

```javascript
promise.then(value => {
    console.log(value);
});
```

or:

```javascript
const value = await promise;
```

You cannot synchronously extract the value from an arbitrary Promise.

---

# 84. Promise Is Not a Replacement for Every Callback

Callbacks are still useful.

Examples:

```javascript
array.map(item => ...)
array.filter(item => ...)
button.addEventListener("click", ...)
```

Promises are particularly useful for:

```text
One eventual success/failure result
```

An event listener may fire:

```text
0 times
1 time
100 times
```

so a Promise is not automatically the correct abstraction for every event-based system.

---

# 85. Promise Represents One Settlement

A Promise settles once.

Therefore it naturally models:

```text
Request finishes
File loads
Timer completes
Database query returns
Operation succeeds/fails
```

It does not naturally represent a continuous stream of values.

For streams/events, other abstractions may be more appropriate.

---

# 86. Interview Question — What Is a Promise?

A Promise is an object representing the eventual completion or failure of an asynchronous operation and its resulting value.

It can be:

```text
Pending
Fulfilled
Rejected
```

---

# 87. Interview Question — What Does `new Promise()` Do?

It creates a Promise and immediately executes the provided executor function synchronously.

The executor receives:

```javascript
resolve
reject
```

which control the Promise's eventual resolution or rejection.

---

# 88. Interview Question — Is Promise Executor Async?

No.

Example:

```javascript
console.log("A");

new Promise(resolve => {
    console.log("B");
});

console.log("C");
```

Output:

```text
A
B
C
```

The executor runs synchronously.

---

# 89. Interview Question — Are `.then()` Handlers Synchronous?

No.

Even for an already fulfilled Promise:

```javascript
Promise.resolve()
    .then(() => {
        console.log("Later");
    });
```

the handler runs asynchronously as a microtask.

---

# 90. Interview Question — What Does `.then()` Return?

`.then()` always returns a new Promise.

Its eventual state depends on what the handler does.

```text
return value
↓
Fulfilled


throw error
↓
Rejected


return Promise
↓
Adopt Promise state
```

---

# 91. Interview Question — What Does `.catch()` Return?

`.catch()` also returns a new Promise.

If its handler returns a normal value:

```javascript
.catch(() => {
    return "Recovered";
});
```

the chain becomes fulfilled with:

```text
Recovered
```

unless the handler throws or returns a rejected Promise.

---

# 92. Interview Question — What Does `.finally()` Return?

`.finally()` returns a new Promise.

Normally it preserves the original settlement:

```text
Fulfilled → Fulfilled
Rejected  → Rejected
```

unless the `finally` handler itself throws or returns a rejected Promise.

---

# 93. Interview Question — Promise vs Callback?

A callback is a function passed for later or immediate invocation.

A Promise is an object representing an eventual result.

Promises provide built-in mechanisms for:

```text
Chaining
Composition
Error propagation
Concurrency coordination
```

---

# 94. Interview Question — Why Return a Promise from `.then()`?

Because the next handler should wait for that asynchronous operation.

```javascript
getUser()
    .then(user => {
        return getOrders(user.id);
    })
    .then(orders => {
        console.log(orders);
    });
```

Without returning the Promise, the chain does not wait for it.

---

# 95. Interview Question — What Is Promise Chaining?

Promise chaining connects asynchronous operations through the Promises returned by:

```text
.then()
.catch()
.finally()
```

Example:

```javascript
getUser()
    .then(getOrders)
    .then(processOrders)
    .catch(handleError);
```

Each step creates another Promise whose result determines the next step.

---

# 96. Interview Question — What Is Promise Adoption?

When a Promise is resolved with another Promise or a compatible thenable, it follows that object's eventual state rather than simply treating it as an ordinary value.

---

# 97. Interview Question — Resolved vs Fulfilled?

**Fulfilled** means the Promise has successfully settled with its final value.

**Resolved** means its outcome has been determined or linked to another Promise/thenable.

A Promise can be resolved to another still-pending Promise and therefore not yet fulfilled.

---

# 98. Interview Question — What Is a Thenable?

A thenable is an object containing a callable:

```javascript
.then()
```

method.

Promise resolution can adopt compatible thenables.

---

# 99. Interview Question — Why Does `.catch()` Sometimes Continue to `.then()`?

Because if the catch handler returns a normal value:

```javascript
.catch(() => {
    return 10;
})
```

the Promise returned by `.catch()` is fulfilled.

Therefore the next fulfillment handler executes.

---

# 100. Interview Question — Does `Promise.all()` Run Promises?

No.

Consider:

```javascript
Promise.all([
    getUsers(),
    getProducts()
]);
```

The function calls:

```javascript
getUsers()
getProducts()
```

create/start the operations before `Promise.all()` receives their returned Promises.

`Promise.all()` coordinates their results.

---

# 101. Interview Question — Does `Promise.all()` Preserve Order?

Yes.

Its result array preserves the input order, regardless of the order in which the Promises fulfill.

---

# 102. Interview Question — Does `Promise.all()` Cancel Remaining Operations?

No.

It rejects when an input rejects, but it does not automatically cancel other operations.

Cancellation must be handled separately.

---

# 103. Interview Question — `all()` vs `allSettled()`?

```text
all()
────────────

Need all successes.

Reject if an input rejects.


allSettled()
────────────

Need every outcome.

Wait for all settlements.
```

---

# 104. Interview Question — `race()` vs `any()`?

```text
race()
────────────

First settlement wins.

Success OR failure.


any()
────────────

First fulfillment wins.

Failures ignored until
all have failed.
```

---

# 105. Interview Question — Are Promises Lazy?

Native Promise executors are eager.

```javascript
new Promise(resolve => {
    console.log("Runs now");
});
```

The executor runs immediately.

You can create lazy behavior by wrapping Promise creation in a function.

---

# 106. Output Question 1

```javascript
console.log("A");

new Promise(resolve => {
    console.log("B");

    resolve();
}).then(() => {
    console.log("C");
});

console.log("D");
```

Output:

```text
A
B
D
C
```

---

# 107. Output Question 2

```javascript
Promise.resolve(10)
    .then(value => {
        console.log(value);

        return value * 2;
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
10
20
```

---

# 108. Output Question 3

```javascript
Promise.resolve(10)
    .then(value => {
        console.log(value);
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
10
undefined
```

---

# 109. Output Question 4

```javascript
Promise.resolve()
    .then(() => {
        throw new Error("A");
    })
    .then(() => {
        console.log("B");
    })
    .catch(error => {
        console.log(error.message);

        return "C";
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
A
C
```

The rejection skips the `"B"` handler.

The catch recovers by returning `"C"`.

---

# 110. Output Question 5

```javascript
Promise.reject("A")
    .catch(error => {
        console.log(error);

        throw "B";
    })
    .catch(error => {
        console.log(error);
    });
```

Output:

```text
A
B
```

---

# 111. Output Question 6

```javascript
console.log("1");

Promise.resolve()
    .then(() => {
        console.log("2");
    })
    .then(() => {
        console.log("3");
    });

Promise.resolve()
    .then(() => {
        console.log("4");
    });

console.log("5");
```

Output:

```text
1
5
2
4
3
```

---

# 112. Output Question 7

```javascript
Promise.resolve("A")
    .finally(() => {
        console.log("B");
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
B
A
```

---

# 113. Output Question 8

```javascript
Promise.resolve("A")
    .finally(() => {
        return "B";
    })
    .then(value => {
        console.log(value);
    });
```

Output:

```text
A
```

Returning a normal value from `finally()` does not replace the original fulfillment value.

---

# 114. Output Question 9

```javascript
Promise.resolve("A")
    .finally(() => {
        throw new Error("B");
    })
    .then(console.log)
    .catch(error => {
        console.log(error.message);
    });
```

Output:

```text
B
```

---

# 115. Output Question 10

```javascript
const promise =
    Promise.resolve(10);

promise.then(value => {
    console.log(value);
});

promise.then(value => {
    console.log(value * 2);
});
```

Output:

```text
10
20
```

Both handlers observe the same Promise result.

---

# 116. Output Question 11

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");

        return Promise.resolve("B");
    })
    .then(value => {
        console.log(value);
    });

Promise.resolve()
    .then(() => {
        console.log("C");
    });
```

Output begins:

```text
A
C
```

and then:

```text
B
```

So:

```text
A
C
B
```

The returned Promise must be adopted before the next chained handler executes.

---

# 117. Output Question 12

```javascript
const p1 =
    Promise.resolve(10);

const p2 =
    p1.then(value => value * 2);

const p3 =
    p1.then(value => value * 3);

Promise.all([p1, p2, p3])
    .then(console.log);
```

Output:

```javascript
[10, 20, 30]
```

The same Promise can branch into multiple independent chains.

---

# 118. Promise Branching

Promises do not only form linear chains.

Example:

```text
             Promise A
            /         \
           /           \
          ▼             ▼
    Promise B       Promise C
        │               │
        ▼               ▼
    Handler B        Handler C
```

Example:

```javascript
const userPromise =
    getUser();

const ordersPromise =
    userPromise.then(user => {
        return getOrders(user.id);
    });

const profilePromise =
    userPromise.then(user => {
        return getProfile(user.id);
    });
```

Both chains depend on the same user result.

---

# 119. Complete Promise Mental Model

```text
                 Promise
                    │
                    ▼
                 Pending
                 /     \
                /       \
               ▼         ▼
         Fulfilled     Rejected
               │         │
               ▼         ▼
             .then     .catch
               │         │
               └────┬────┘
                    │
                    ▼
              New Promise
                    │
        ┌───────────┼────────────┐
        │           │            │
        ▼           ▼            ▼
 return value   throw error   return Promise
        │           │            │
        ▼           ▼            ▼
   Fulfilled     Rejected      Adopt state
        │           │            │
        └───────────┴────────────┘
                    │
                    ▼
                 Continue
                   Chain
```

---

# 120. Promise Cheat Sheet

```text
PROMISE
================================

Represents eventual
completion/failure.


STATES
================================

Pending
Fulfilled
Rejected


SETTLED
================================

Fulfilled OR Rejected.


EXECUTOR
================================

Runs synchronously.


resolve(value)
================================

Resolve Promise with value.

Can adopt Promise/thenable.


reject(reason)
================================

Reject Promise.


.then()
================================

Handles fulfillment.

Returns new Promise.


.catch()
================================

Handles rejection.

Returns new Promise.


.finally()
================================

Runs after settlement.

Normally preserves outcome.


RETURN VALUE
================================

.then(() => value)

↓

Fulfilled Promise


THROW ERROR
================================

.then(() => {
    throw error;
})

↓

Rejected Promise


RETURN PROMISE
================================

.then(() => promise)

↓

Chain adopts returned
Promise's state.


PROMISE CHAINING
================================

Promise
↓
.then
↓
Promise
↓
.then
↓
Promise


ERROR PROPAGATION
================================

Rejection skips fulfillment
handlers until handled.


ERROR RECOVERY
================================

.catch(() => value)

↓

Chain becomes fulfilled.


PROMISE.resolve()
================================

Create/resolve Promise
from a value.


PROMISE.reject()
================================

Create rejected Promise.


PROMISE.all()
================================

All successes required.

Rejects when input rejects.

Preserves input order.


PROMISE.allSettled()
================================

Wait for every outcome.


PROMISE.race()
================================

First settlement wins.


PROMISE.any()
================================

First fulfillment wins.


PROMISE HANDLERS
================================

Run as microtasks.


PROMISE EXECUTOR
================================

Runs synchronously.


PROMISES ARE EAGER
================================

Executor starts immediately.


PROMISES SETTLE ONCE
================================

State cannot change
after settlement.


PROMISE ADOPTION
================================

Promise can follow another
Promise/thenable.


PROMISE FLATTENING
================================

Nested Promise results
are adopted automatically.


THENABLE
================================

Object with callable .then()


CONCURRENCY
================================

Start independent operations
before waiting for results.


CANCELLATION
================================

Promises do not automatically
cancel underlying operations.
```

---

# 121. Important Rules to Remember

```text
1. A Promise represents an eventual result.

2. Promise states are pending, fulfilled, and rejected.

3. Fulfilled and rejected Promises are settled.

4. A Promise settles only once.

5. Promise executors execute synchronously.

6. Creating a Promise does not automatically make its executor async.

7. resolve() resolves a Promise.

8. reject() rejects a Promise.

9. Rejecting with Error objects is generally preferable.

10. .then() returns a new Promise.

11. .catch() returns a new Promise.

12. .finally() returns a new Promise.

13. Promise handlers execute asynchronously.

14. Promise reactions are microtasks.

15. Returning a value from .then() fulfills the next Promise.

16. Returning nothing means returning undefined.

17. Throwing inside .then() rejects the next Promise.

18. Returning a rejected Promise rejects the chain.

19. Returning another Promise causes state adoption.

20. Promise chains naturally flatten returned Promises.

21. Forgetting return is a common Promise-chain bug.

22. Rejections propagate through chains.

23. Fulfillment handlers are skipped while rejection propagates.

24. catch() can recover from an error.

25. Returning a normal value from catch() fulfills the chain.

26. Throwing inside catch() keeps the chain rejected.

27. finally() is useful for cleanup.

28. finally() normally preserves the original outcome.

29. Throwing inside finally() can replace the outcome with rejection.

30. Promise.resolve() can resolve values, Promises, and thenables.

31. Promise.reject() creates a rejected Promise.

32. A resolved Promise is not necessarily already fulfilled.

33. Promises can adopt other Promise states.

34. Thenables are objects with callable .then() methods.

35. Promise resolution handles thenables.

36. Promises cannot resolve with themselves.

37. Self-resolution causes TypeError.

38. Promise.all() coordinates multiple inputs.

39. Promise.all() preserves input order.

40. Promise.all() rejects when an input rejects.

41. Promise.all() does not cancel remaining operations.

42. Promise.allSettled() waits for every settlement.

43. Promise.race() uses the first settlement.

44. Promise.any() uses the first fulfillment.

45. Promise.any() rejects with AggregateError if all reject.

46. Independent operations can often run concurrently.

47. Dependent operations should remain sequential.

48. Promise.all() does not start operations by itself.

49. Calling Promise-producing functions starts their work.

50. Native Promise executors are eager.

51. Functions can provide lazy Promise creation.

52. Promises can have multiple handlers.

53. A Promise is not consumed by .then().

54. Promise chains can branch.

55. Nested Promise chains should usually be flattened.

56. Avoid unnecessary new Promise wrappers.

57. new Promise() is useful when promisifying callback APIs.

58. Unhandled rejections should be avoided.

59. Fire-and-forget Promises still need failure consideration.

60. Promise represents the result, not necessarily the underlying operation.

61. Promises naturally model one eventual settlement.

62. Promises are not replacements for every callback.

63. Promise cancellation is separate from Promise settlement.

64. Promise understanding is fundamental to async/await.

65. Understanding Promise microtasks is fundamental to the Event Loop.
```

---

# Promise in One Sentence

> A JavaScript Promise is an object representing one eventual result whose fulfillment or rejection can be composed, transformed, chained, and handled asynchronously.

---

# Final Mental Model

```text
Start Operation
      │
      ▼
   Promise
      │
      ▼
   Pending
    /   \
   /     \
  ▼       ▼
Fulfilled Rejected
   │        │
   ▼        ▼
.then()   .catch()
   │        │
   └────┬───┘
        ▼
   New Promise
        │
        ├── return value
        │       ↓
        │   fulfilled
        │
        ├── throw error
        │       ↓
        │    rejected
        │
        └── return Promise
                ↓
           adopt its state
                │
                ▼
            Continue Chain
```

And remember:

```text
Promise Constructor
        ↓
Executor runs NOW
        ↓
Promise settles eventually
        ↓
Handlers scheduled
        ↓
Microtask Queue
        ↓
Event Loop
        ↓
Handler executes
        ↓
Handler result determines
new Promise
        ↓
Chain continues
```
