# Memoization

**Memoization** is an optimization technique where the result of a function call is **cached based on its input**, so if the function is called again with the same input, the cached result can be returned instead of recalculating it.

Core idea:

```text
First Call

Input
  │
  ▼
Check Cache
  │
  ▼
Not Found
  │
  ▼
Calculate Result
  │
  ▼
Store in Cache
  │
  ▼
Return Result


Later Call With Same Input

Input
  │
  ▼
Check Cache
  │
  ▼
Found
  │
  ▼
Return Cached Result
```

Example:

```javascript
function square(number) {
    console.log("Calculating...");

    return number * number;
}

square(5);
square(5);
```

Output:

```text
Calculating...
Calculating...
```

The same calculation happens twice.

With memoization:

```javascript
const memoizedSquare =
    memoize(square);

memoizedSquare(5);
memoizedSquare(5);
```

Conceptually:

```text
First call:

5
↓
Calculate
↓
25
↓
Cache


Second call:

5
↓
Cache
↓
25
```

The calculation only needs to happen once.

---

# 1. Why Memoization Exists

Suppose a function performs an expensive calculation:

```javascript
function calculate(number) {
    console.log("Calculating...");

    let result = 0;

    for (
        let i = 0;
        i < 1_000_000;
        i++
    ) {
        result += number;
    }

    return result;
}
```

Calling:

```javascript
calculate(10);
calculate(10);
calculate(10);
```

performs the same work three times.

```text
10 → Calculate
10 → Calculate
10 → Calculate
```

If the result for `10` will always be the same, we can remember it.

```text
10 → Calculate → Cache
10 → Cache
10 → Cache
```

This is memoization.

---

# 2. Memoization Mental Model

Think of memoization as:

> "Have I already solved this exact problem?"

If:

```text
NO
```

then:

```text
Calculate
↓
Remember Result
↓
Return
```

If:

```text
YES
```

then:

```text
Return Remembered Result
```

---

# 3. Basic Memoization

Let's manually memoize a square function.

```javascript
function createSquare() {
    const cache = {};

    return function (number) {
        if (
            number in cache
        ) {
            console.log(
                "From cache"
            );

            return cache[number];
        }

        console.log(
            "Calculating"
        );

        const result =
            number * number;

        cache[number] =
            result;

        return result;
    };
}
```

Usage:

```javascript
const square =
    createSquare();

console.log(square(5));
console.log(square(5));
console.log(square(10));
console.log(square(10));
```

Output:

```text
Calculating
25

From cache
25

Calculating
100

From cache
100
```

---

# 4. What Is Happening?

When:

```javascript
const square =
    createSquare();
```

runs, this is created:

```javascript
const cache = {};
```

The returned function remembers the cache through a **closure**.

```text
createSquare()
     │
     ├── cache
     │
     ▼
Returned Function
     │
     ▼
Closure
```

Even after `createSquare()` finishes, the cache remains accessible to the returned function.

---

# 5. Memoization Uses Closures

Consider:

```javascript
function memoize(fn) {
    const cache = {};

    return function (value) {
        // cache is accessible here
    };
}
```

The returned function forms a closure over:

```javascript
cache
```

This allows cache data to survive between calls.

Example:

```text
Call 1
↓
cache = {}


Call 2
↓
same cache


Call 3
↓
same cache
```

Without persistent state, memoization would not work.

---

# 6. Generic Memoize Function

Instead of memoizing only `square()`, we can create a reusable function.

```javascript
function memoize(fn) {
    const cache = new Map();

    return function (value) {
        if (
            cache.has(value)
        ) {
            return cache.get(value);
        }

        const result =
            fn(value);

        cache.set(
            value,
            result
        );

        return result;
    };
}
```

Usage:

```javascript
function square(number) {
    console.log(
        "Calculating..."
    );

    return number * number;
}

const memoizedSquare =
    memoize(square);

console.log(
    memoizedSquare(5)
);

console.log(
    memoizedSquare(5)
);
```

Output:

```text
Calculating...
25
25
```

The second call uses the cache.

---

# 7. Why Use `Map`?

We could use:

```javascript
const cache = {};
```

But `Map` is often convenient for caches.

```javascript
const cache =
    new Map();
```

Useful methods:

```javascript
cache.has(key);

cache.get(key);

cache.set(
    key,
    value
);

cache.delete(key);

cache.clear();
```

Example:

```javascript
const cache =
    new Map();

cache.set(
    5,
    25
);

console.log(
    cache.has(5)
);
```

Output:

```text
true
```

---

# 8. Memoization Flow

Suppose:

```javascript
memoizedSquare(5);
```

Flow:

```text
Input: 5
   │
   ▼
cache.has(5)?
   │
   ▼
false
   │
   ▼
square(5)
   │
   ▼
25
   │
   ▼
cache.set(5, 25)
   │
   ▼
return 25
```

Call again:

```javascript
memoizedSquare(5);
```

Flow:

```text
Input: 5
   │
   ▼
cache.has(5)?
   │
   ▼
true
   │
   ▼
cache.get(5)
   │
   ▼
25
```

The original function does not execute again.

---

# 9. Memoization Is a Higher-Order Function Pattern

Our memoize function:

```javascript
function memoize(fn) {
    // ...

    return function (...) {
        // ...
    };
}
```

accepts:

```text
Function
```

and returns:

```text
Function
```

Therefore it is a **higher-order function**.

Concepts involved:

```text
Functions
Callbacks
Closures
Higher-Order Functions
Map
Caching
Rest Parameters
Spread
this
```

---

# 10. Memoization with Multiple Arguments

Our previous implementation only handled one argument:

```javascript
memoizedFunction(value);
```

But functions can have multiple arguments:

```javascript
function add(a, b) {
    return a + b;
}
```

We need a cache key representing:

```text
a + b
```

or more precisely:

```text
Arguments:
[a, b]
```

One simple approach is:

```javascript
JSON.stringify(args)
```

---

# 11. Multiple Argument Memoization

```javascript
function memoize(fn) {
    const cache =
        new Map();

    return function (...args) {
        const key =
            JSON.stringify(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const result =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            result
        );

        return result;
    };
}
```

Usage:

```javascript
function add(a, b) {
    console.log(
        "Calculating..."
    );

    return a + b;
}

const memoizedAdd =
    memoize(add);

console.log(
    memoizedAdd(10, 20)
);

console.log(
    memoizedAdd(10, 20)
);
```

Output:

```text
Calculating...
30
30
```

---

# 12. Rest Parameters

This:

```javascript
function (...args)
```

collects all arguments into an array.

Example:

```javascript
memoizedAdd(
    10,
    20
);
```

produces:

```javascript
args = [10, 20];
```

Then:

```javascript
JSON.stringify(args);
```

produces a string such as:

```text
"[10,20]"
```

which can be used as a cache key in this simple implementation.

---

# 13. Why `apply()`?

```javascript
fn.apply(
    this,
    args
);
```

calls the original function while passing:

```text
this
+
arguments
```

Equivalent argument spreading could be:

```javascript
fn(...args);
```

But:

```javascript
fn.apply(
    this,
    args
);
```

also preserves the wrapper's dynamic `this`.

---

# 14. `JSON.stringify()` Is Not a Perfect Cache-Key Strategy

This implementation:

```javascript
const key =
    JSON.stringify(args);
```

is useful for learning, but it has limitations.

For example:

```javascript
undefined
functions
symbols
circular references
special object types
object property ordering concerns
large objects
```

can make serialization-based keys inappropriate or inefficient.

Therefore:

```text
JSON.stringify(args)
```

should not be treated as a universal production memoization solution.

---

# 15. Object Arguments

Suppose:

```javascript
function calculateUser(user) {
    return user.score * 2;
}
```

And:

```javascript
const user1 = {
    score: 10
};

const user2 = {
    score: 10
};
```

Although:

```javascript
user1.score === user2.score
```

they are different objects:

```javascript
user1 === user2
```

returns:

```text
false
```

This matters when object identity is used as the cache key.

---

# 16. Reference Identity

Consider:

```javascript
const cache =
    new Map();

const user1 = {
    id: 1
};

const user2 = {
    id: 1
};

cache.set(
    user1,
    "Result"
);

console.log(
    cache.has(user1)
);
```

Output:

```text
true
```

But:

```javascript
console.log(
    cache.has(user2)
);
```

Output:

```text
false
```

Because:

```text
user1
and
user2
```

are different object references.

---

# 17. Memoization by Object Identity

```javascript
function memoizeObject(fn) {
    const cache =
        new Map();

    return function (object) {
        if (
            cache.has(object)
        ) {
            return cache.get(
                object
            );
        }

        const result =
            fn(object);

        cache.set(
            object,
            result
        );

        return result;
    };
}
```

Usage:

```javascript
const user = {
    score: 10
};

memoized(user);
memoized(user);
```

The same object reference can hit the cache.

But:

```javascript
memoized({
    score: 10
});
```

is a new object reference and therefore a different key.

---

# 18. `WeakMap` for Object Keys

When cache keys are objects, `WeakMap` can sometimes be useful.

```javascript
const cache =
    new WeakMap();
```

A `WeakMap` accepts objects as keys.

Example:

```javascript
const cache =
    new WeakMap();

const user = {
    id: 1
};

cache.set(
    user,
    "Cached result"
);

console.log(
    cache.get(user)
);
```

---

# 19. Why `WeakMap` Can Be Useful

A normal `Map` holds strong references to its keys.

Conceptually:

```text
Map
 │
 └── Object Key
       │
       ▼
Object remains referenced
by the Map
```

A `WeakMap` does not prevent an object key from being garbage-collected when there are no other strong references to that object.

This can be useful for caches tied to object lifetimes.

---

# 20. `WeakMap` Limitations

Unlike `Map`, a `WeakMap` is intentionally not enumerable.

You cannot use it like:

```javascript
weakMap.keys();
```

or:

```javascript
weakMap.values();
```

or:

```javascript
weakMap.entries();
```

And object keys are required.

Use `WeakMap` when its object-lifetime semantics match the cache you need.

---

# 21. Memoization and Pure Functions

Memoization works most naturally with **pure functions**.

A pure function:

```text
Same Input
↓
Same Output
```

and does not rely on hidden changing external state for its result.

Example:

```javascript
function square(number) {
    return number * number;
}
```

```text
square(5)
→ 25

square(5)
→ 25

square(5)
→ 25
```

This is easy to memoize.

---

# 22. Problem with Impure Functions

Consider:

```javascript
let multiplier = 2;

function calculate(number) {
    return (
        number *
        multiplier
    );
}
```

First:

```javascript
calculate(10);
```

returns:

```text
20
```

Then:

```javascript
multiplier = 5;
```

Now:

```javascript
calculate(10);
```

should return:

```text
50
```

But if memoization only uses:

```text
number = 10
```

as the key, it may return the old cached result:

```text
20
```

That would be incorrect.

---

# 23. Hidden Dependencies Break Memoization Assumptions

The function:

```javascript
function calculate(number) {
    return (
        number *
        multiplier
    );
}
```

really depends on:

```text
number
+
multiplier
```

But the cache only knows about:

```text
number
```

Therefore:

```text
Cache key
does not represent
all dependencies
```

This is a common memoization bug.

---

# 24. Better Design

Instead of:

```javascript
let multiplier = 2;

function calculate(number) {
    return (
        number *
        multiplier
    );
}
```

prefer explicit inputs:

```javascript
function calculate(
    number,
    multiplier
) {
    return (
        number *
        multiplier
    );
}
```

Now:

```text
calculate(10, 2)
```

and:

```text
calculate(10, 5)
```

are clearly different inputs.

This makes memoization safer.

---

# 25. Memoization and Side Effects

Consider:

```javascript
function saveUser(user) {
    database.save(user);

    return true;
}
```

Memoizing this would be dangerous.

If:

```javascript
memoizedSaveUser(user);
```

returns a cached result later, the actual:

```text
database.save()
```

may not execute.

Memoization can skip function execution entirely.

Therefore functions whose purpose is a side effect are generally poor memoization candidates.

Examples:

```text
Database writes
Sending emails
POST requests
Logging
DOM mutations
File writes
Payments
Notifications
```

---

# 26. Memoization Changes Execution Frequency

Without memoization:

```text
Call
↓
Function Executes

Call
↓
Function Executes
```

With memoization:

```text
Call
↓
Function Executes
↓
Cache


Call
↓
Cache
↓
Function DOES NOT execute
```

This is why memoizing functions with important side effects can break application behavior.

---

# 27. Memoization vs Normal Caching

Memoization is a form of caching.

But the term usually refers specifically to caching **function results based on function inputs**.

```text
Caching
================================

Broad concept.

Can cache:

API responses
Database queries
Files
Images
Pages
Objects
Function results


Memoization
================================

Usually:

Function Input
↓
Cached Function Output
```

So:

```text
Memoization
⊂
Caching
```

Memoization is a specific caching technique.

---

# 28. Memoization vs Debouncing

These are completely different.

Memoization:

```text
Same input?
↓
Return cached result
```

Debounce:

```text
Repeated calls?
↓
Wait until activity stops
↓
Execute
```

Memoization optimizes repeated calculations.

Debounce controls execution timing.

---

# 29. Memoization vs Throttling

Memoization:

```text
Cache based on input
```

Throttle:

```text
Limit execution frequency
```

Example:

```javascript
memoizedCalculate(10);
```

asks:

```text
Have we calculated 10 before?
```

Throttle asks:

```text
Has enough time passed
since execution?
```

Different problems.

---

# 30. Memoization vs Lazy Evaluation

Lazy evaluation means:

```text
Don't calculate until
the result is actually needed.
```

Memoization means:

```text
Once calculated,
remember the result for
future equivalent calls.
```

The concepts can be combined, but they are not the same.

---

# 31. Memoization and Recursion

Memoization becomes extremely useful with recursive algorithms that repeatedly solve the same subproblems.

A classic example is Fibonacci.

```javascript
function fibonacci(n) {
    if (n <= 1) {
        return n;
    }

    return (
        fibonacci(n - 1) +
        fibonacci(n - 2)
    );
}
```

Example:

```javascript
fibonacci(5);
```

---

# 32. Fibonacci Without Memoization

To calculate:

```text
fib(5)
```

we calculate:

```text
fib(4)
+
fib(3)
```

But:

```text
fib(4)
```

also needs:

```text
fib(3)
```

So the same subproblems are recalculated.

Conceptually:

```text
                fib(5)
               /      \
          fib(4)      fib(3)
          /   \        /   \
     fib(3) fib(2) fib(2) fib(1)
      /  \
 fib(2) fib(1)
```

Notice:

```text
fib(3)
fib(2)
fib(1)
```

appear repeatedly.

---

# 33. The Problem with Recursive Fibonacci

For larger values, repeated calculations grow dramatically.

The naive recursive Fibonacci algorithm has exponential time complexity:

```text
O(2^n)
```

more precisely often described as:

```text
O(φ^n)
```

where `φ` is the golden ratio.

For practical discussion, the important idea is:

```text
Exponential growth
```

which becomes expensive quickly.

---

# 34. Memoized Fibonacci

```javascript
function fibonacci(
    n,
    cache = new Map()
) {
    if (cache.has(n)) {
        return cache.get(n);
    }

    if (n <= 1) {
        return n;
    }

    const result =
        fibonacci(
            n - 1,
            cache
        ) +
        fibonacci(
            n - 2,
            cache
        );

    cache.set(
        n,
        result
    );

    return result;
}
```

Now repeated subproblems can reuse cached results.

---

# 35. Memoized Fibonacci Flow

Suppose:

```text
fib(5)
```

needs:

```text
fib(3)
```

The first time:

```text
fib(3)
↓
Calculate
↓
Store
```

Later:

```text
fib(3)
↓
Cache
↓
Return
```

No repeated recursive tree is required for that subproblem.

---

# 36. Complexity Improvement

Naive Fibonacci:

```text
Time:
Exponential
```

Memoized Fibonacci:

```text
Time:
O(n)
```

because each Fibonacci value:

```text
fib(0)
fib(1)
fib(2)
...
fib(n)
```

only needs to be calculated once.

But memoization introduces storage:

```text
Space:
O(n)
```

for cached results, in addition to recursion-stack considerations.

---

# 37. Memoization Is a Time-Space Tradeoff

This is one of the most important concepts.

Without memoization:

```text
Less cache memory
+
More repeated computation
```

With memoization:

```text
More memory
+
Less repeated computation
```

So memoization trades:

```text
SPACE
for
TIME
```

It is not free optimization.

---

# 38. Memoization Can Increase Memory Usage

Suppose:

```javascript
const cache =
    new Map();
```

and every unique function call adds another entry:

```text
Input 1 → Cache
Input 2 → Cache
Input 3 → Cache
Input 4 → Cache
...
Input 1,000,000 → Cache
```

If nothing removes entries, the cache can become very large.

Therefore real systems may need:

```text
Cache size limits
Expiration
LRU policies
WeakMap
Manual invalidation
Lifecycle-based caches
```

depending on the use case.

---

# 39. Cache Invalidation

Suppose a cached result depends on data that changes.

```text
Input
↓
Result
↓
Cache
```

Later the underlying data changes.

The cache may now contain:

```text
Stale Result
```

You need some way to:

```text
Delete
Refresh
Expire
Recalculate
```

the cached value.

This is called **cache invalidation**.

---

# 40. Simple Cache Clearing

Suppose our memoized function exposes:

```javascript
memoized.clear();
```

We can implement:

```javascript
function memoize(fn) {
    const cache =
        new Map();

    function memoized(...args) {
        const key =
            JSON.stringify(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const result =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            result
        );

        return result;
    }

    memoized.clear =
        function () {
            cache.clear();
        };

    return memoized;
}
```

Usage:

```javascript
memoized.clear();
```

Now the next call recalculates.

---

# 41. Deleting One Cached Entry

You could expose:

```javascript
memoized.delete(...args);
```

Example:

```javascript
function memoize(fn) {
    const cache =
        new Map();

    function createKey(args) {
        return JSON.stringify(args);
    }

    function memoized(...args) {
        const key =
            createKey(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const result =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            result
        );

        return result;
    }

    memoized.delete =
        function (...args) {
            cache.delete(
                createKey(args)
            );
        };

    return memoized;
}
```

Then:

```javascript
memoized.delete(
    10,
    20
);
```

removes that cached result.

---

# 42. Cache Expiration

Sometimes cached results should only survive for a limited time.

Example:

```text
Cache Result
↓
Valid for 5 minutes
↓
Expire
↓
Recalculate
```

This is often called:

```text
TTL
=
Time To Live
```

---

# 43. Simple TTL Memoization

```javascript
function memoize(
    fn,
    ttl
) {
    const cache =
        new Map();

    return function (...args) {
        const key =
            JSON.stringify(args);

        const cached =
            cache.get(key);

        const now =
            Date.now();

        if (
            cached &&
            now - cached.time < ttl
        ) {
            return cached.value;
        }

        const result =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            {
                value: result,
                time: now
            }
        );

        return result;
    };
}
```

This is a simplified educational implementation.

---

# 44. TTL Flow

Suppose:

```text
TTL = 5000 ms
```

First call:

```text
0 seconds
↓
Calculate
↓
Cache
```

Second call:

```text
2 seconds
↓
Cache still valid
↓
Return cached result
```

Third call:

```text
7 seconds
↓
Cache expired
↓
Recalculate
```

---

# 45. Cache Size Limits

Instead of keeping every result forever, a cache may have:

```text
Maximum entries = 100
```

When a new entry arrives:

```text
101 entries
```

something may need to be removed.

One strategy is:

```text
LRU
=
Least Recently Used
```

---

# 46. LRU Concept

Suppose cache capacity is:

```text
3
```

Cache:

```text
A
B
C
```

Then `A` and `B` are used recently, while `C` is old.

New value:

```text
D
```

needs to enter.

LRU may remove:

```text
C
```

Result:

```text
A
B
D
```

The exact implementation is a separate data-structure topic, but understand why bounded caches exist.

---

# 47. Memoizing Async Functions

Consider:

```javascript
async function fetchUser(id) {
    const response =
        await fetch(
            `/api/users/${id}`
        );

    return response.json();
}
```

Can this be memoized?

Yes, but there are important details.

An async function immediately returns:

```text
Promise
```

So memoization can cache the promise.

---

# 48. Promise Memoization

```javascript
function memoizeAsync(fn) {
    const cache =
        new Map();

    return function (...args) {
        const key =
            JSON.stringify(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const promise =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            promise
        );

        return promise;
    };
}
```

Now:

```javascript
const getUser =
    memoizeAsync(fetchUser);
```

If two callers request:

```javascript
getUser(42);
getUser(42);
```

the second call can receive the same cached promise.

---

# 49. Why Cache the Promise Immediately?

Suppose two calls happen almost simultaneously.

Without immediate promise caching:

```text
Call A
↓
Request starts

Call B
↓
Request starts
```

Two network requests may occur.

If the promise is cached immediately:

```text
Call A
↓
Create Promise
↓
Cache Promise
↓
Request starts


Call B
↓
Find Promise
↓
Return same Promise
```

This can deduplicate in-flight work.

---

# 50. Problem: Rejected Promises

Suppose:

```text
Request
↓
Promise
↓
Cache
↓
Reject
```

If the rejected promise stays cached, future calls may keep receiving the same rejection without retrying.

Depending on requirements, failed promises may need to be removed.

---

# 51. Async Memoization with Failure Removal

```javascript
function memoizeAsync(fn) {
    const cache =
        new Map();

    return function (...args) {
        const key =
            JSON.stringify(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const promise =
            Promise.resolve(
                fn.apply(
                    this,
                    args
                )
            );

        cache.set(
            key,
            promise
        );

        promise.catch(() => {
            cache.delete(key);
        });

        return promise;
    };
}
```

Now a failed operation can be retried later.

Whether failures should be cached is a design decision.

---

# 52. Memoizing API Requests Requires Care

Caching API calls introduces questions:

```text
How long is data valid?

Can data change?

Should failures be cached?

Should requests be deduplicated?

Should cache survive navigation?

Should different users share cache?

How is authorization handled?

When should data refresh?
```

Memoization is not automatically a complete data-fetching strategy.

Libraries and frameworks often provide more sophisticated caching mechanisms.

---

# 53. Memoization and `this`

Suppose:

```javascript
const calculator = {
    multiplier: 2,

    calculate(number) {
        return (
            number *
            this.multiplier
        );
    }
};
```

If a memoization wrapper calls:

```javascript
fn(...args);
```

without preserving context appropriately, `this` behavior may change.

A generic wrapper can use:

```javascript
fn.apply(
    this,
    args
);
```

to preserve the calling context.

But there is another issue.

---

# 54. `this` Can Be Part of the Input

Consider:

```javascript
function calculate(number) {
    return (
        number *
        this.multiplier
    );
}
```

Two objects:

```javascript
const a = {
    multiplier: 2
};

const b = {
    multiplier: 5
};
```

If both call:

```text
calculate(10)
```

the outputs differ because `this` differs.

So a cache based only on:

```text
[10]
```

would be incorrect.

Conceptually the real input is:

```text
this
+
arguments
```

This shows why generic memoization can become complicated.

---

# 55. Memoization Requires Correct Cache Keys

The central rule is:

> The cache key must represent everything that determines the function result.

If the result depends on:

```text
A
B
C
```

but your key contains only:

```text
A
B
```

then the cache can return incorrect results when `C` changes.

---

# 56. Cache Key Design

Possible cache keys include:

```text
Primitive argument
Multiple arguments
Serialized arguments
Object identity
Tuple-like nested Maps
Custom IDs
Normalized values
this context
```

There is no universal cache-key strategy.

The function's input semantics determine the correct design.

---

# 57. Primitive-Key Memoization

For a single primitive argument:

```javascript
function memoize(fn) {
    const cache =
        new Map();

    return function (value) {
        if (
            cache.has(value)
        ) {
            return cache.get(value);
        }

        const result =
            fn(value);

        cache.set(
            value,
            result
        );

        return result;
    };
}
```

This is simple and reliable.

---

# 58. Multiple Arguments with Nested Maps

Instead of converting arguments to strings, a more advanced cache can use nested `Map`s.

Conceptually:

```text
Argument 1
   │
   ▼
Map
   │
Argument 2
   │
   ▼
Map
   │
Argument 3
   │
   ▼
Result
```

For:

```javascript
fn(
    "user",
    42,
    true
);
```

conceptually:

```text
"user"
   │
   ▼
  42
   │
   ▼
 true
   │
   ▼
Result
```

This avoids some serialization problems but requires more implementation complexity.

---

# 59. Memoization and Referential Equality

This becomes particularly important in React.

Objects and arrays use reference identity.

```javascript
const a = {
    value: 10
};

const b = {
    value: 10
};

console.log(
    a === b
);
```

Output:

```text
false
```

Likewise:

```javascript
[] === []
```

is:

```text
false
```

Even though their contents look identical.

---

# 60. Same Reference vs Same Contents

```javascript
const a = {
    value: 10
};

const b = a;

console.log(
    a === b
);
```

Output:

```text
true
```

Because:

```text
a
and
b
```

refer to the same object.

Memoization strategies that rely on reference identity care about:

```text
Same object?
```

not necessarily:

```text
Same contents?
```

---

# 61. Why Immutability Helps Memoization

Suppose:

```javascript
const user = {
    name: "Alice",
    score: 10
};
```

You mutate:

```javascript
user.score = 50;
```

The object reference remains the same.

```text
Before mutation:
user → Object A

After mutation:
user → Object A
```

A cache using object identity may think:

```text
Same object
→ Same cached result
```

even though relevant contents changed.

---

# 62. Immutable Update

Instead:

```javascript
const updatedUser = {
    ...user,
    score: 50
};
```

Now:

```text
user
↓
Object A


updatedUser
↓
Object B
```

Different state can have a different reference.

This makes reference-based memoization much easier to reason about.

This is one reason immutability is important in React and state management.

---

# 63. Memoization and React

React has APIs such as:

```javascript
useMemo()
```

and:

```javascript
useCallback()
```

These are related to memoization concepts.

But:

```text
useMemo
≠
generic memoize function
```

React memoization has lifecycle and dependency semantics tied to rendering.

Still, understanding general memoization makes these APIs easier to understand.

---

# 64. `useMemo` Concept

Conceptually:

```javascript
const result =
    useMemo(
        () => expensiveCalculation(data),
        [data]
    );
```

means:

```text
Render
↓
Did dependencies change?
   /             \
 NO               YES
 │                 │
 ▼                 ▼
Reuse             Calculate
Previous          Again
Value
```

This is similar to memoization:

```text
Input/dependencies
↓
Cached result
```

---

# 65. `useCallback` Concept

Conceptually:

```javascript
const handleClick =
    useCallback(
        () => {
            doSomething(id);
        },
        [id]
    );
```

`useCallback` memoizes a **function reference** based on dependencies.

Conceptually:

```text
Dependencies unchanged
↓
Reuse same function reference


Dependencies changed
↓
Create new function reference
```

We will cover this properly in React.

---

# 66. `useMemo` vs `useCallback`

Conceptually:

```text
useMemo
================================

Memoizes a VALUE.


useCallback
================================

Memoizes a FUNCTION
REFERENCE.
```

Example:

```javascript
const value =
    useMemo(
        () => calculate(),
        []
    );
```

versus:

```javascript
const fn =
    useCallback(
        () => calculate(),
        []
    );
```

Do not worry about memorizing the React syntax yet.

Understand the underlying memoization idea first.

---

# 67. Memoization Is Not Automatically an Optimization

Suppose:

```javascript
function add(a, b) {
    return a + b;
}
```

This calculation is extremely cheap.

Adding memoization introduces:

```text
Cache lookup
Cache storage
Memory usage
Key creation
More code
```

That could be more work than simply calculating:

```javascript
a + b
```

Memoization has overhead.

---

# 68. When Memoization Helps

Memoization is most useful when:

```text
Function is expensive

Same inputs occur repeatedly

Result is deterministic

Cache lookup is cheaper
than recalculation

Cache memory is acceptable

Result remains valid
```

Examples:

```text
Expensive calculations
Recursive algorithms
Parsing
Transforming large datasets
Selectors
Repeated derived state
Some request deduplication
```

---

# 69. When Memoization May Not Help

Avoid blindly memoizing when:

```text
Calculation is cheap

Inputs rarely repeat

Cache key generation is expensive

Results change frequently

Function has side effects

Memory usage matters

Cache invalidation is difficult
```

Memoization should solve a measured or expected problem.

---

# 70. Memoization Overhead

Suppose the original calculation costs:

```text
1 unit
```

but memoization requires:

```text
Create key
Check Map
Store result
Maintain cache
```

The optimization may be pointless.

But if calculation costs:

```text
1,000,000 units
```

and cache lookup costs very little, memoization can be valuable.

The point:

```text
Optimization benefit
depends on workload.
```

---

# 71. Cache Hit

A **cache hit** occurs when the requested result already exists.

```text
Input
↓
Cache
↓
FOUND
↓
Return Result
```

Example:

```javascript
memoizedSquare(5);
```

when `5` already exists in the cache.

---

# 72. Cache Miss

A **cache miss** occurs when the requested result does not exist.

```text
Input
↓
Cache
↓
NOT FOUND
↓
Calculate
↓
Store
↓
Return
```

---

# 73. Hit Rate

Cache performance can be discussed using:

```text
Cache Hit Rate
```

Conceptually:

```text
Cache Hits
──────────────
Total Requests
```

If most inputs are unique:

```text
1
2
3
4
5
6
7
8
```

there may be almost no cache reuse.

Memoization gives little benefit.

If inputs repeat:

```text
1
2
1
1
2
1
2
```

cache reuse can be high.

---

# 74. Memoization Example — Factorial

Without memoization:

```javascript
function factorial(n) {
    if (n <= 1) {
        return 1;
    }

    return (
        n *
        factorial(n - 1)
    );
}
```

Calling:

```javascript
factorial(10);
factorial(9);
```

repeats work.

A memoized implementation can reuse previous results.

---

# 75. Memoized Factorial

```javascript
function createFactorial() {
    const cache =
        new Map([
            [0, 1],
            [1, 1]
        ]);

    function factorial(n) {
        if (
            cache.has(n)
        ) {
            return cache.get(n);
        }

        const result =
            n *
            factorial(n - 1);

        cache.set(
            n,
            result
        );

        return result;
    }

    return factorial;
}
```

Usage:

```javascript
const factorial =
    createFactorial();

factorial(10);
factorial(9);
```

After calculating `factorial(10)`, several smaller factorial values are already cached.

---

# 76. Memoization Example — Large Array Transformation

Suppose:

```javascript
function processData(data) {
    return data
        .filter(...)
        .map(...)
        .sort(...);
}
```

If:

```text
Same data
```

is repeatedly processed and the computation is expensive, memoization may help.

But if a new array is created every time:

```javascript
processData([...data]);
```

a reference-based cache may treat it as a different input.

This is why referential stability matters.

---

# 77. Memoization Example — Derived Data

Suppose:

```javascript
function calculateCartTotal(
    cart
) {
    return cart.reduce(
        (total, item) =>
            total +
            item.price *
            item.quantity,
        0
    );
}
```

For a huge cart repeatedly processed without changes, a memoized selector could potentially reuse the previous result.

But if the cart is small, direct calculation may be simpler and faster.

---

# 78. Memoization and Recursion vs Dynamic Programming

Memoization is often described as:

```text
Top-Down Dynamic Programming
```

Example:

```text
Start with fib(n)
↓
Recursively solve smaller problems
↓
Cache results
```

Another approach is:

```text
Bottom-Up Dynamic Programming
```

where smaller results are calculated first.

---

# 79. Top-Down

Memoized Fibonacci:

```text
fib(5)
↓
Need fib(4)
↓
Need fib(3)
↓
Need fib(2)
...
```

Results are cached as recursion solves them.

This is:

```text
Top-Down
+
Memoization
```

---

# 80. Bottom-Up

Instead:

```javascript
function fibonacci(n) {
    if (n <= 1) {
        return n;
    }

    let previous = 0;
    let current = 1;

    for (
        let i = 2;
        i <= n;
        i++
    ) {
        const next =
            previous +
            current;

        previous =
            current;

        current =
            next;
    }

    return current;
}
```

This works upward:

```text
fib(0)
↓
fib(1)
↓
fib(2)
↓
fib(3)
↓
...
↓
fib(n)
```

No recursive memo cache is required in this optimized version.

---

# 81. Memoization vs Tabulation

```text
MEMOIZATION
================================

Usually top-down.

Recursive.

Calculate values when needed.

Cache solved subproblems.


TABULATION
================================

Usually bottom-up.

Often iterative.

Calculate smaller values first.

Build toward final answer.
```

Both are common dynamic programming techniques.

---

# 82. Cache Ownership

Ask:

```text
Who owns the cache?
```

Example:

```javascript
const memoized =
    memoize(fn);
```

The returned function owns its cache through closure.

Another:

```javascript
const a =
    memoize(fn);

const b =
    memoize(fn);
```

Now:

```text
a
↓
Cache A


b
↓
Cache B
```

They do not automatically share results.

---

# 83. Independent Memoized Functions

```javascript
const first =
    memoize(square);

const second =
    memoize(square);
```

Call:

```javascript
first(5);
```

Cache A:

```text
5 → 25
```

Then:

```javascript
second(5);
```

Cache B is empty.

So `square(5)` executes again.

Each memoization wrapper has independent closure state.

---

# 84. Global Cache vs Local Cache

You could create:

```javascript
const cache =
    new Map();
```

globally.

But this creates shared global state.

A closure:

```javascript
function memoize(fn) {
    const cache =
        new Map();

    return function (...) {
        // ...
    };
}
```

keeps the cache private.

Benefits:

```text
Encapsulation
Independent caches
Less global state
Cleaner ownership
```

---

# 85. Cache Encapsulation

Outside code cannot directly do:

```javascript
cache.set(...);
```

because:

```javascript
cache
```

exists inside `memoize()`.

Only the returned function can access it unless you intentionally expose cache operations.

This is another useful application of closures.

---

# 86. Common Mistake — Memoizing Side Effects

Bad candidate:

```javascript
function sendEmail(email) {
    // send email
}
```

Memoizing this could prevent later sends because the same argument may return from cache.

Memoize results, not operations whose execution itself is the purpose.

---

# 87. Common Mistake — Incorrect Cache Key

Suppose:

```javascript
function calculate(
    a,
    b
) {
    return a * b;
}
```

But cache key is only:

```javascript
a
```

Then:

```text
calculate(5, 2)
→ 10

calculate(5, 10)
→ Should be 50
```

but the cache might return:

```text
10
```

The cache key must represent all relevant inputs.

---

# 88. Common Mistake — Ignoring External State

```javascript
let taxRate = 0.1;

function total(price) {
    return (
        price +
        price * taxRate
    );
}
```

If:

```text
taxRate
```

changes, cached results based only on `price` become stale.

Make dependencies explicit or invalidate the cache appropriately.

---

# 89. Common Mistake — Unbounded Cache

```javascript
const cache =
    new Map();
```

If millions of unique inputs arrive and nothing is removed:

```text
Cache
↓
Keeps Growing
↓
Memory Usage Grows
```

Consider lifecycle, expiration, limits, or alternative structures.

---

# 90. Common Mistake — Mutating Object Inputs

```javascript
const user = {
    score: 10
};

memoized(user);

user.score = 100;

memoized(user);
```

If caching by reference:

```text
Same object reference
```

may cause the old result to be returned.

Immutability or proper invalidation helps avoid this.

---

# 91. Common Mistake — Expensive Cache Keys

Suppose:

```javascript
JSON.stringify(
    hugeObject
);
```

takes significant time.

If the original calculation is cheap, key generation may cost more than recalculation.

Caching itself has a cost.

---

# 92. Common Mistake — Assuming Same Contents Means Same Reference

```javascript
memoized({
    value: 10
});

memoized({
    value: 10
});
```

These are two different objects.

Reference-based caches treat them as separate inputs.

---

# 93. Common Mistake — Caching Errors Forever

For async memoization:

```text
Request
↓
Reject
↓
Rejected Promise Cached
```

Future calls may repeatedly receive the cached failure.

Decide whether failures should:

```text
Remain cached
Expire
Be deleted immediately
Retry later
```

---

# 94. Common Mistake — Memoizing Everything in React

Later, when learning React, do not turn every calculation into:

```javascript
useMemo(...)
```

or every function into:

```javascript
useCallback(...)
```

Memoization itself has overhead and increases code complexity.

Use it when it solves an actual rendering or computation problem.

---

# 95. Common Mistake — Confusing Memoization with Storing State

Memoization stores derived results for reuse.

Application state represents information the application needs to model.

These are not the same concept.

Example:

```text
User's selected theme
→ State


Expensive calculation derived
from existing data
→ Possible memoization candidate
```

---

# 96. Interview Question — What Is Memoization?

Memoization is an optimization technique that caches a function's result based on its inputs so repeated calls with equivalent inputs can reuse the cached result instead of recalculating it.

---

# 97. Interview Question — How Does Memoization Work?

```text
Receive Input
↓
Create/Identify Cache Key
↓
Check Cache
   /        \
Found       Missing
  │            │
  ▼            ▼
Return       Calculate
Cached          │
Result          ▼
              Store
                │
                ▼
              Return
```

---

# 98. Interview Question — Why Are Closures Useful for Memoization?

Closures allow the returned memoized function to retain access to its private cache after the outer `memoize()` function has completed.

---

# 99. Interview Question — Is Memoization a Higher-Order Function?

A memoization utility commonly is.

Example:

```javascript
memoize(fn);
```

takes a function and returns a wrapped function.

---

# 100. Interview Question — When Is Memoization Useful?

When:

```text
Calculations are expensive

Inputs repeat

Outputs are deterministic

Cache lookup is cheaper

Memory cost is acceptable
```

---

# 101. Interview Question — When Should You Avoid Memoization?

When:

```text
Calculation is cheap

Inputs rarely repeat

Function has side effects

Results depend on hidden state

Cache becomes too large

Invalidation is difficult
```

---

# 102. Interview Question — Why Do Pure Functions Work Well with Memoization?

Because pure functions produce the same output for the same inputs.

That makes cached results predictable and safe to reuse.

---

# 103. Interview Question — What Is a Cache Hit?

When a requested result already exists in the cache.

```text
Input
↓
Cache
↓
Found
```

---

# 104. Interview Question — What Is a Cache Miss?

When the requested result is absent.

```text
Input
↓
Cache
↓
Missing
↓
Calculate
```

---

# 105. Interview Question — What Is Cache Invalidation?

Removing or refreshing cached values when they are no longer valid.

Possible strategies:

```text
Manual deletion
TTL expiration
Version changes
Dependency changes
LRU eviction
Lifecycle cleanup
```

---

# 106. Interview Question — `Map` vs `WeakMap` for Memoization?

`Map`:

```text
Supports primitive
and object keys.

Enumerable.

Strongly references keys.
```

`WeakMap`:

```text
Object keys.

Not enumerable.

Does not prevent object
keys from being garbage
collected.
```

`WeakMap` can be useful for object-identity caches tied to object lifetimes.

---

# 107. Interview Question — What Is Referential Equality?

For objects, arrays, and functions, equality checks whether references point to the same object.

```javascript
{} === {}
```

is:

```text
false
```

while:

```javascript
const a = {};

const b = a;

a === b;
```

is:

```text
true
```

This matters for reference-based memoization.

---

# 108. Interview Question — Why Does Immutability Help Memoization?

Because changed data can receive a new reference.

```text
Old State
↓
Reference A


New State
↓
Reference B
```

Reference changes make it easier to determine when cached results should be recalculated.

---

# 109. Interview Question — Can Async Functions Be Memoized?

Yes.

Since async functions return promises, the promise can be cached.

This can also deduplicate concurrent requests for the same input.

But you must consider:

```text
Failures
Expiration
Staleness
Invalidation
Authorization
Cache lifetime
```

---

# 110. Interview Question — Why Cache a Promise Immediately?

To prevent simultaneous calls with the same key from starting duplicate work.

```text
Call A
↓
Create Promise
↓
Cache


Call B
↓
Same Cache Key
↓
Reuse Promise
```

---

# 111. Interview Question — What Happens if a Cached Promise Rejects?

If it remains cached, later calls may receive the same rejected promise.

Depending on the requirement, the rejected promise may need to be removed so the operation can retry.

---

# 112. Interview Question — Memoization vs Caching?

Caching is the broad concept of storing data for reuse.

Memoization specifically usually means caching function outputs according to function inputs.

---

# 113. Interview Question — Memoization vs Debouncing?

Memoization:

```text
Avoid repeated calculations
for equivalent inputs.
```

Debouncing:

```text
Delay execution until
calls stop for a period.
```

---

# 114. Interview Question — Memoization vs Throttling?

Memoization:

```text
Input-based optimization.
```

Throttling:

```text
Time/frequency-based
execution control.
```

---

# 115. Interview Question — Memoization vs Dynamic Programming?

Memoization is a technique often used in dynamic programming.

Top-down dynamic programming commonly uses recursion plus memoization.

Bottom-up dynamic programming commonly uses tabulation.

---

# 116. Interview Question — What Is the Time-Space Tradeoff?

Memoization uses additional memory to avoid repeated computation.

```text
More Cache Memory
↓
Potentially Less CPU Work
```

---

# 117. Interview Question — Why Isn't Memoization Always Faster?

Because memoization itself requires work:

```text
Generate cache key
Check cache
Store results
Manage memory
Invalidate entries
```

For cheap or rarely repeated calculations, the overhead may exceed the savings.

---

# 118. Interview Question — What Determines a Correct Cache Key?

Everything that can affect the function's output must be represented by the cache key.

If the result depends on:

```text
a
b
c
```

then a key based only on:

```text
a
b
```

is insufficient.

---

# 119. Implementation Question — Write Memoize

```javascript
function memoize(fn) {
    const cache =
        new Map();

    return function (...args) {
        const key =
            JSON.stringify(args);

        if (
            cache.has(key)
        ) {
            return cache.get(key);
        }

        const result =
            fn.apply(
                this,
                args
            );

        cache.set(
            key,
            result
        );

        return result;
    };
}
```

Remember:

This is a **learning implementation**.

`JSON.stringify()` is not a universal production cache-key solution.

---

# 120. Explain the Implementation

```javascript
const cache =
    new Map();
```

creates private cache storage.

```javascript
return function (...args)
```

creates the wrapped function.

```javascript
const key =
    JSON.stringify(args);
```

creates a simple representation of the arguments.

```javascript
cache.has(key)
```

checks whether the result already exists.

```javascript
cache.get(key)
```

returns the cached result.

```javascript
fn.apply(
    this,
    args
);
```

executes the original function.

```javascript
cache.set(
    key,
    result
);
```

stores the result.

Closure keeps:

```text
cache
```

alive between calls.

---

# 121. Output Question

```javascript
function multiply(a, b) {
    console.log(
        "Calculating"
    );

    return a * b;
}

const memoizedMultiply =
    memoize(multiply);

console.log(
    memoizedMultiply(5, 10)
);

console.log(
    memoizedMultiply(5, 10)
);

console.log(
    memoizedMultiply(5, 20)
);
```

Output:

```text
Calculating
50

50

Calculating
100
```

Why?

```text
(5, 10)
↓
Cache Miss
↓
Calculate


(5, 10)
↓
Cache Hit


(5, 20)
↓
Different Input
↓
Cache Miss
↓
Calculate
```

---

# 122. Memoization Decision Guide

Ask:

```text
Is the calculation expensive?
        │
       YES
        │
        ▼
Do the same inputs repeat?
        │
       YES
        │
        ▼
Is the output deterministic
for those inputs?
        │
       YES
        │
        ▼
Is cache memory acceptable?
        │
       YES
        │
        ▼
Consider Memoization
```

If the calculation is:

```text
Cheap
```

or inputs:

```text
Almost never repeat
```

memoization may provide little value.

---

# 123. Memoization Cheat Sheet

```text
MEMOIZATION
================================

Cache function results
based on inputs.


CORE FLOW
================================

Input
↓
Cache?
↓
Hit → Return Cached Result
↓
Miss → Calculate
       ↓
       Cache
       ↓
       Return


CACHE HIT
================================

Result already exists.


CACHE MISS
================================

Result does not exist.


COMMON STORAGE
================================

Map
WeakMap
Objects


CLOSURE
================================

Keeps private cache alive
between calls.


PURE FUNCTIONS
================================

Best memoization candidates.

Same Input
→
Same Output


SIDE EFFECTS
================================

Usually poor candidates.


MAP
================================

Primitive + object keys.

Strong references.


WEAKMAP
================================

Object keys.

Weak key references.

Not enumerable.


CACHE INVALIDATION
================================

Remove stale results.


TTL
================================

Time To Live.

Expire result after time.


LRU
================================

Least Recently Used.

Evict older/less recently
used entries when capacity
is reached.


ASYNC MEMOIZATION
================================

Cache Promise.

Can deduplicate
in-flight work.


REFERENCE IDENTITY
================================

{} !== {}

[] !== []


IMMUTABILITY
================================

New data
→
New reference

Useful for reference-based
memoization.


REACT
================================

useMemo
→ Memoized value

useCallback
→ Memoized function reference


DYNAMIC PROGRAMMING
================================

Top-down
→ Memoization

Bottom-up
→ Tabulation


TRADEOFF
================================

More memory
for
less repeated computation.
```

---

# 124. Important Rules to Remember

```text
1. Memoization caches function results.

2. Cached results are associated with function inputs.

3. Same input can reuse a previous result.

4. A cache hit avoids recalculation.

5. A cache miss requires calculation.

6. Memoization is a form of caching.

7. Memoization commonly uses closures.

8. Closures keep cache state between calls.

9. Memoization wrappers are commonly higher-order functions.

10. Map is useful for caches.

11. WeakMap can be useful for object-key caches.

12. WeakMap does not prevent object keys from being garbage collected.

13. WeakMap keys are objects.

14. WeakMap is not enumerable.

15. Cache keys must represent all relevant inputs.

16. Incorrect cache keys produce incorrect results.

17. JSON.stringify(args) is useful for learning but has limitations.

18. Object reference identity matters.

19. Two identical-looking objects may be different keys.

20. The same object reference can reuse a reference-based cache entry.

21. Mutating cached object inputs can produce stale results.

22. Immutability makes reference-based memoization easier.

23. Pure functions are strong memoization candidates.

24. Same input should produce the same output for safe reuse.

25. Hidden external dependencies can break memoization.

26. Make dependencies explicit where possible.

27. Functions with side effects are usually poor memoization candidates.

28. Memoization may skip original function execution.

29. Do not memoize important side effects blindly.

30. Memoization uses extra memory.

31. Memoization is a time-space tradeoff.

32. Unbounded caches can consume excessive memory.

33. Real caches may need size limits.

34. Real caches may need expiration.

35. TTL means Time To Live.

36. Cache invalidation removes stale results.

37. LRU means Least Recently Used.

38. LRU can limit cache growth.

39. Async functions can be memoized.

40. Async memoization can cache promises.

41. Caching promises can deduplicate simultaneous work.

42. Rejected promises may need to be removed from cache.

43. API memoization requires staleness and invalidation strategies.

44. Memoization does not replace proper data-fetching architecture.

45. this can affect function output.

46. this may therefore need to be considered as part of cache identity.

47. apply() can preserve the calling context.

48. Memoization is different from debouncing.

49. Memoization is different from throttling.

50. Memoization is different from lazy evaluation.

51. Memoization is commonly used with recursion.

52. Recursive Fibonacci demonstrates repeated subproblems.

53. Memoized Fibonacci reduces repeated computation.

54. Memoized Fibonacci can run in O(n) time.

55. Memoization is commonly called top-down dynamic programming.

56. Tabulation is commonly bottom-up dynamic programming.

57. Memoization is not automatically faster.

58. Cache lookup has overhead.

59. Cache key creation has overhead.

60. Cache storage consumes memory.

61. Cheap calculations often do not need memoization.

62. Inputs that rarely repeat provide little cache benefit.

63. High cache hit rates make memoization more useful.

64. Cache ownership matters.

65. Different memoization wrappers usually have separate caches.

66. Closures provide private cache ownership.

67. Global caches introduce shared state.

68. React useMemo is related to memoization.

69. React useCallback memoizes function references based on dependencies.

70. Do not memoize everything in React automatically.

71. Memoization should solve an actual performance problem.

72. Measure performance when optimization matters.

73. Cache correctness is more important than cache speed.

74. Stale cache results can cause application bugs.

75. Always understand what determines the function output.
```

---

# Memoization in One Sentence

> Memoization caches the result of a function for a given input so future equivalent calls can reuse that result instead of performing the same computation again.

---

# Final Mental Model

Without memoization:

```text
calculate(10)
     │
     ▼
   Work
     │
     ▼
  Result


calculate(10)
     │
     ▼
   Work
     │
     ▼
  Result


calculate(10)
     │
     ▼
   Work
     │
     ▼
  Result
```

With memoization:

```text
calculate(10)
     │
     ▼
Check Cache
     │
     ▼
   MISS
     │
     ▼
   Work
     │
     ▼
  Result
     │
     ▼
   Cache


calculate(10)
     │
     ▼
Check Cache
     │
     ▼
    HIT
     │
     ▼
  Result


calculate(10)
     │
     ▼
Check Cache
     │
     ▼
    HIT
     │
     ▼
  Result
```

The most important idea:

```text
INPUT
  │
  ▼
CACHE
 /   \
HIT   MISS
 │      │
 ▼      ▼
Return Calculate
       │
       ▼
      Store
       │
       ▼
      Return
```

And always remember:

```text
Memoization
is not

"Make every function faster."


Memoization
is

"Trade memory for potentially
less repeated computation
when the same work occurs
again."
```
