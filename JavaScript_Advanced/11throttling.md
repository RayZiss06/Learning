# JavaScript — Throttling

**Throttling** is a technique used to limit how frequently a function can execute.

When an event fires repeatedly, throttling allows the function to run **at most once during a specified time interval**.

Common uses:

* Scroll events
* Mouse movement
* Window resize
* Drag events
* UI position updates
* Expensive calculations
* Analytics events
* Progress updates
* Repeated event handling
* Some API interactions

---

# 1. The Problem Throttling Solves

Some browser events can fire extremely frequently.

Example:

```javascript
window.addEventListener(
    "scroll",
    () => {
        console.log(
            window.scrollY
        );
    }
);
```

While scrolling:

```text
scroll
scroll
scroll
scroll
scroll
scroll
scroll
...
```

The handler may execute many times in a short period.

If it performs expensive work:

```javascript
function handleScroll() {
    calculateLayout();
    updateUI();
    trackPosition();
}
```

repeated execution can become wasteful.

Throttling limits how frequently the handler runs.

---

# 2. What Throttling Does

Suppose:

```text
Throttle interval = 500 ms
```

Events continue:

```text
Event Event Event Event Event Event Event
```

Instead of:

```text
Execute Execute Execute Execute Execute...
```

we allow approximately:

```text
Execute
   │
   │ 500 ms
   ▼
Execute
   │
   │ 500 ms
   ▼
Execute
```

The events may continue, but the callback is limited.

---

# 3. Core Mental Model

```text
Events
│ │ │ │ │ │ │ │ │ │ │ │
▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼

Throttle
   │
   ▼

Execute
      Execute
             Execute
                    Execute
```

Think:

> "While events continue, execute at most once every X milliseconds."

---

# 4. Basic Timestamp-Based Throttle

One simple implementation uses timestamps.

```javascript
function throttle(callback, delay) {
    let lastExecution = 0;

    return function (...args) {
        const now = Date.now();

        if (
            now - lastExecution
            >= delay
        ) {
            lastExecution = now;

            callback.apply(
                this,
                args
            );
        }
    };
}
```

Usage:

```javascript
function handleScroll() {
    console.log(
        window.scrollY
    );
}

const throttledScroll =
    throttle(
        handleScroll,
        500
    );

window.addEventListener(
    "scroll",
    throttledScroll
);
```

---

# 5. How Timestamp Throttle Works

Initial state:

```text
lastExecution = 0
```

An event occurs.

Get current time:

```javascript
const now = Date.now();
```

Check:

```javascript
now - lastExecution >= delay
```

If enough time has passed:

```text
YES
 │
 ▼
Execute callback
 │
 ▼
Update lastExecution
```

Otherwise:

```text
NO
 │
 ▼
Ignore this call
```

---

# 6. Example Timeline

Suppose:

```text
delay = 1000 ms
```

Calls happen at:

```text
0 ms
200 ms
400 ms
900 ms
1100 ms
1400 ms
2200 ms
```

Conceptually:

```text
0 ms     → Execute
200 ms   → Ignore
400 ms   → Ignore
900 ms   → Ignore
1100 ms  → Execute
1400 ms  → Ignore
2200 ms  → Execute
```

So continuous calls do not cause continuous callback execution.

---

# 7. Throttle Is a Higher-Order Function

Throttle accepts a function:

```javascript
throttle(callback, delay)
```

and returns another function:

```javascript
return function (...args) {
    // ...
};
```

Therefore throttle is a **higher-order function**.

It uses concepts we already learned:

```text
Functions
Callbacks
Closures
Rest Parameters
Spread
this
Timers
Date/Time
```

---

# 8. Why Throttle Uses a Closure

```javascript
function throttle(callback, delay) {
    let lastExecution = 0;

    return function (...args) {
        // ...
    };
}
```

After:

```javascript
const throttled =
    throttle(handleScroll, 500);
```

`throttle()` has finished executing.

But the returned function still needs:

```javascript
lastExecution
```

The returned function forms a closure over that variable.

```text
throttle()
    │
    ├── lastExecution
    │
    ▼
Returned Function
    │
    ▼
Closure
```

That state is preserved between calls.

---

# 9. Each Throttled Function Has Independent State

```javascript
const scrollHandler =
    throttle(handleScroll, 500);

const mouseHandler =
    throttle(handleMouse, 100);
```

Conceptually:

```text
scrollHandler
    │
    ▼
Closure A
lastExecution A


mouseHandler
    │
    ▼
Closure B
lastExecution B
```

They do not share throttle state.

---

# 10. Preserving Arguments

Suppose:

```javascript
function updatePosition(x, y) {
    console.log(x, y);
}
```

Throttle:

```javascript
const throttledUpdate =
    throttle(
        updatePosition,
        100
    );
```

Call:

```javascript
throttledUpdate(
    100,
    200
);
```

The wrapper captures:

```javascript
(...args)
```

which becomes:

```javascript
args = [100, 200];
```

Then:

```javascript
callback.apply(
    this,
    args
);
```

passes those arguments to the original callback.

---

# 11. Preserving `this`

A reusable throttle utility should also consider the calling context.

```javascript
function throttle(callback, delay) {
    let lastExecution = 0;

    return function (...args) {
        const now = Date.now();

        if (
            now - lastExecution
            >= delay
        ) {
            lastExecution = now;

            callback.apply(
                this,
                args
            );
        }
    };
}
```

Using:

```javascript
callback.apply(this, args);
```

preserves:

```text
this
+
arguments
```

---

# 12. Why Use a Normal Function Wrapper?

Prefer:

```javascript
return function (...args) {
```

when the utility needs dynamic `this`.

An arrow function:

```javascript
return (...args) => {
```

does not create its own `this`.

Arrow functions inherit `this` lexically.

So a generic utility often uses:

```javascript
function (...args)
```

for the returned wrapper.

---

# 13. Timer-Based Throttle

Throttle can also be implemented using timers.

```javascript
function throttle(callback, delay) {
    let waiting = false;

    return function (...args) {
        if (waiting) {
            return;
        }

        callback.apply(
            this,
            args
        );

        waiting = true;

        setTimeout(() => {
            waiting = false;
        }, delay);
    };
}
```

---

# 14. How Timer-Based Throttle Works

Initially:

```text
waiting = false
```

First call:

```text
waiting?
   │
   ▼
 false
   │
   ▼
Execute callback
   │
   ▼
waiting = true
```

Calls during the interval:

```text
waiting?
   │
   ▼
 true
   │
   ▼
Ignore
```

After delay:

```text
waiting = false
```

The next call can execute.

---

# 15. Timestamp vs Timer Throttle

## Timestamp-Based

```javascript
Date.now()
```

checks how much time has passed.

Advantages:

```text
Simple
No reset timer required
Natural leading execution
```

---

## Timer-Based

Uses:

```javascript
setTimeout()
```

to create a blocked period.

Advantages:

```text
Easy mental model
Can be extended for trailing calls
```

Neither implementation is universally superior.

The required behavior determines the design.

---

# 16. Leading Throttle

A **leading throttle** executes immediately when the allowed interval begins.

Example:

```text
Event
 │
 ▼
Execute immediately

Event
 │
 ▼
Ignore

Event
 │
 ▼
Ignore

Interval ends

Next Event
 │
 ▼
Execute immediately
```

Our basic timestamp implementation behaves like a leading throttle.

---

# 17. Problem with Simple Leading Throttle

Suppose:

```text
delay = 1000 ms
```

Events:

```text
0 ms    → Execute
300 ms  → Ignore
700 ms  → Ignore
900 ms  → Ignore

Events stop.
```

The event at:

```text
900 ms
```

is never processed.

Sometimes that is fine.

Sometimes we care about the **latest event**.

For example:

```text
Mouse position
Scroll position
Resize dimensions
```

In those cases, trailing behavior can be useful.

---

# 18. Trailing Throttle

A trailing throttle ensures the latest call can execute after the throttle interval.

Conceptually:

```text
Event
 │
 ▼
Schedule callback

More Events
 │
 ▼
Remember latest arguments

Interval ends
 │
 ▼
Execute latest call
```

This helps preserve the final state.

---

# 19. Simple Trailing Throttle

```javascript
function throttle(
    callback,
    delay
) {
    let timeoutId = null;
    let lastArgs;
    let lastThis;

    return function (...args) {
        lastArgs = args;
        lastThis = this;

        if (timeoutId) {
            return;
        }

        timeoutId =
            setTimeout(() => {
                callback.apply(
                    lastThis,
                    lastArgs
                );

                timeoutId = null;
            }, delay);
    };
}
```

During the interval, new calls update:

```text
lastArgs
lastThis
```

When the timer finishes, the latest call is used.

---

# 20. Leading + Trailing Throttle

A more advanced throttle may support both:

```text
First Event
    │
    ▼
Execute immediately
    │
    ▼
Ignore repeated executions
but remember latest call
    │
    ▼
Interval ends
    │
    ▼
Execute latest call if needed
```

This gives:

```text
Leading execution
+
Trailing execution
```

Many utility libraries provide configurable implementations for this behavior.

---

# 21. Scroll Example

```javascript
function handleScroll() {
    console.log(
        "Scroll:",
        window.scrollY
    );
}

const throttledScroll =
    throttle(
        handleScroll,
        200
    );

window.addEventListener(
    "scroll",
    throttledScroll
);
```

Without throttle:

```text
scroll → execute
scroll → execute
scroll → execute
scroll → execute
scroll → execute
```

With throttle:

```text
scroll scroll scroll scroll scroll
  │
  ▼
execute

...interval...

scroll scroll scroll
  │
  ▼
execute
```

---

# 22. Mouse Movement Example

`mousemove` can fire very frequently.

```javascript
function handleMouseMove(
    event
) {
    console.log(
        event.clientX,
        event.clientY
    );
}
```

Throttle:

```javascript
const throttledMouseMove =
    throttle(
        handleMouseMove,
        100
    );

document.addEventListener(
    "mousemove",
    throttledMouseMove
);
```

Instead of processing every movement event, the application processes periodic updates.

---

# 23. Drag Example

Suppose an element is being dragged.

```text
mousemove
mousemove
mousemove
mousemove
mousemove
...
```

Each event could trigger:

```text
Position calculation
DOM update
Collision detection
Layout calculation
```

Throttling can limit expensive work.

```javascript
const updateDrag =
    throttle(
        handleDrag,
        50
    );
```

---

# 24. Resize Example

```javascript
function handleResize() {
    console.log(
        window.innerWidth,
        window.innerHeight
    );
}

window.addEventListener(
    "resize",
    throttle(
        handleResize,
        200
    )
);
```

This produces periodic updates during resizing.

---

# 25. Resize — Debounce or Throttle?

It depends.

If you want:

```text
Update only after
resizing stops
```

use:

```text
Debounce
```

If you want:

```text
Periodic updates while
resizing continues
```

use:

```text
Throttle
```

---

# 26. Scroll — Debounce or Throttle?

Suppose you need:

```text
"User stopped scrolling"
```

Debounce may be useful.

Suppose you need:

```text
"Current scroll position
while scrolling"
```

Throttle may be useful.

The requirement determines the technique.

---

# 27. Debounce vs Throttle

This is the most important comparison.

## Debounce

```text
Event Event Event Event Event
                        │
                        ▼
                      Wait
                        │
                        ▼
                     Execute
```

Meaning:

> Execute after activity stops.

---

## Throttle

```text
Event Event Event Event Event Event
  │           │           │
  ▼           ▼           ▼
Execute     Execute      Execute
```

Meaning:

> Execute periodically while activity continues.

---

# 28. Real-World Comparison

Imagine a user scrolls continuously for 10 seconds.

## Debounce: 500 ms

While scrolling:

```text
No execution
No execution
No execution
No execution
```

User stops.

```text
500 ms
  │
  ▼
Execute
```

Maybe only one execution occurs.

---

## Throttle: 500 ms

While scrolling:

```text
0.0s → Execute

0.5s → Execute

1.0s → Execute

1.5s → Execute

2.0s → Execute

...
```

Execution continues periodically.

---

# 29. Search — Debounce or Throttle?

For a search box:

```text
j
ja
jav
java
javascript
```

Usually you care about the final query after typing pauses.

Therefore:

```text
Debounce
```

is commonly better.

Throttle could produce:

```text
j          → Search
java       → Search
javascript → Search
```

which may still create unnecessary requests.

---

# 30. Mouse Tracking — Debounce or Throttle?

Suppose you want to track mouse position while the user moves the mouse.

Debounce:

```text
Mouse moving...
Mouse moving...
Mouse moving...

Stops
 │
 ▼
One update
```

Not useful if continuous tracking is required.

Throttle:

```text
Mouse moving...

Update
   Update
      Update
         Update
```

Much more appropriate.

---

# 31. Analytics Example

Suppose you want approximate scroll-depth tracking.

Instead of recording every scroll event:

```javascript
window.addEventListener(
    "scroll",
    trackScroll
);
```

use:

```javascript
const throttledTracking =
    throttle(
        trackScroll,
        1000
    );

window.addEventListener(
    "scroll",
    throttledTracking
);
```

This can reduce unnecessary analytics work.

---

# 32. API Calls and Throttling

Throttle can control how frequently a client function attempts API requests.

Example:

```javascript
const sendPosition =
    throttle(
        async (position) => {
            await fetch(
                "/api/position",
                {
                    method: "POST",

                    headers: {
                        "Content-Type":
                            "application/json"
                    },

                    body:
                        JSON.stringify(
                            position
                        )
                }
            );
        },
        1000
    );
```

Now rapid position updates can produce fewer requests.

But this is **not server-side rate limiting**.

---

# 33. Throttle Is Not Rate Limiting

Client throttle:

```text
Controls how often
your frontend invokes
an operation.
```

Server rate limit:

```text
Controls how many requests
a client is allowed to make.
```

A malicious client can bypass frontend throttle.

Therefore:

```text
Frontend throttle
≠
API security
```

---

# 34. Throttle Is Not Debounce

Throttle does not normally wait for events to stop.

Debounce:

```text
Wait for silence.
```

Throttle:

```text
Allow periodic execution.
```

Remember:

```text
Debounce
=
"When activity stops,
run this."


Throttle
=
"While activity continues,
run this at most this often."
```

---

# 35. Throttle Is Not `setInterval()`

You might think:

```javascript
setInterval(callback, 500);
```

is equivalent.

It is not.

`setInterval()` executes periodically regardless of whether events occur.

Throttle executes because calls/events occur.

```text
setInterval
───────────

Time
 │
 ▼
Execute
 │
 ▼
Execute
 │
 ▼
Execute


Throttle
────────

Event
 │
 ▼
Execute

No events
 │
 ▼
Nothing

Event
 │
 ▼
Execute when allowed
```

---

# 36. Throttle Is Demand-Driven

This distinction is useful.

Throttle:

```text
No calls
=
No callback executions
```

`setInterval()`:

```text
No user events
=
Timer can still keep executing
```

Throttle reacts to incoming calls but restricts their frequency.

---

# 37. Cancelling a Throttle

Timer-based throttle implementations may have pending trailing executions.

Sometimes we want to cancel them.

Example:

```text
Component unmounts
Page changes
Drag ends
Operation becomes invalid
```

We can expose:

```javascript
throttled.cancel();
```

---

# 38. Throttle with `cancel()`

```javascript
function throttle(
    callback,
    delay
) {
    let timeoutId = null;
    let lastArgs;
    let lastThis;

    function throttled(...args) {
        lastArgs = args;
        lastThis = this;

        if (timeoutId) {
            return;
        }

        timeoutId =
            setTimeout(() => {
                callback.apply(
                    lastThis,
                    lastArgs
                );

                timeoutId = null;
                lastArgs = undefined;
                lastThis = undefined;
            }, delay);
    }

    throttled.cancel =
        function () {
            clearTimeout(
                timeoutId
            );

            timeoutId = null;
            lastArgs = undefined;
            lastThis = undefined;
        };

    return throttled;
}
```

Usage:

```javascript
const update =
    throttle(
        updatePosition,
        100
    );

update(position);

update.cancel();
```

---

# 39. `flush()` Concept

Like debounce, a timer-based throttle can support:

```text
flush()
```

Meaning:

> If a trailing execution is pending, execute it immediately.

Useful when:

```text
Drag ends
Component closes
Operation completes
Final state must be processed
```

Example concept:

```javascript
throttledUpdate.flush();
```

---

# 40. Why Trailing Execution Matters

Suppose scroll events occur:

```text
0 ms
100 ms
200 ms
300 ms
400 ms
```

with:

```text
delay = 500 ms
```

A leading-only throttle might process:

```text
0 ms position
```

but not the final:

```text
400 ms position
```

If the final position matters, trailing execution can process the latest state.

---

# 41. Latest Arguments Pattern

A trailing throttle commonly stores:

```javascript
let lastArgs;
let lastThis;
```

Each call updates them:

```javascript
lastArgs = args;
lastThis = this;
```

Then when execution becomes available:

```javascript
callback.apply(
    lastThis,
    lastArgs
);
```

This means:

```text
Many calls
    │
    ▼
Remember newest call
    │
    ▼
Execute newest data
```

---

# 42. `requestAnimationFrame()`

For visual browser updates, sometimes throttling by milliseconds is not the best solution.

The browser provides:

```javascript
requestAnimationFrame()
```

It schedules work before the browser's next repaint.

Useful for:

```text
Animations
Visual movement
Scroll-based rendering
DOM position updates
Drag rendering
```

---

# 43. `requestAnimationFrame()` Example

```javascript
let scheduled = false;

window.addEventListener(
    "scroll",
    () => {
        if (scheduled) {
            return;
        }

        scheduled = true;

        requestAnimationFrame(
            () => {
                updateUI(
                    window.scrollY
                );

                scheduled = false;
            }
        );
    }
);
```

Flow:

```text
Many Scroll Events
       │
       ▼
Already scheduled?
      / \
    YES  NO
     │    │
 Ignore  Schedule
          │
          ▼
requestAnimationFrame
          │
          ▼
Browser repaint cycle
          │
          ▼
Update UI
```

---

# 44. Throttle vs `requestAnimationFrame()`

Throttle:

```text
Time-based control

Example:
Every 100 ms
```

`requestAnimationFrame()`:

```text
Render-cycle-based control
```

For purely visual updates:

```text
requestAnimationFrame()
```

may be a better fit.

For general event-frequency control:

```text
Throttle
```

is more general.

---

# 45. Do Not Assume 60 FPS

You may hear:

```text
requestAnimationFrame
=
60 times per second
```

That is an oversimplification.

Its timing generally follows the browser's rendering/repaint scheduling and display conditions.

So think:

```text
Before an upcoming repaint
```

rather than:

```text
Exactly every 16.67 ms
```

---

# 46. React and Throttling

Suppose:

```javascript
function handleScroll() {
    setScrollY(
        window.scrollY
    );
}
```

Calling state updates on every scroll event may be unnecessary.

A throttled handler can reduce update frequency.

But React introduces an important issue:

```text
Function identity
```

The throttled function should not be recreated unnecessarily.

---

# 47. React — The Wrong Pattern

```javascript
function Component() {
    const handleScroll =
        throttle(() => {
            console.log(
                window.scrollY
            );
        }, 200);

    // ...
}
```

Every render creates a new:

```text
throttle()
↓
new closure
↓
new timer state
```

That can defeat the purpose and complicate event-listener cleanup.

---

# 48. React with `useMemo`

One possible pattern:

```javascript
const throttledScroll =
    useMemo(
        () =>
            throttle(() => {
                setScrollY(
                    window.scrollY
                );
            }, 200),
        []
    );
```

Then:

```javascript
useEffect(() => {
    window.addEventListener(
        "scroll",
        throttledScroll
    );

    return () => {
        window.removeEventListener(
            "scroll",
            throttledScroll
        );

        throttledScroll.cancel?.();
    };
}, [throttledScroll]);
```

The exact implementation depends on whether the throttled callback needs changing values from component state or props.

---

# 49. React and Stale Closures

Suppose:

```javascript
const throttled =
    useMemo(
        () =>
            throttle(() => {
                console.log(count);
            }, 500),
        []
    );
```

The callback may capture the value of:

```javascript
count
```

from the render where it was created.

If `count` later changes, the throttled callback may still observe an older captured value depending on how the code is structured.

This is a **stale closure** problem.

---

# 50. React Solutions Depend on the Requirement

Possible approaches include:

```text
Include dependencies
Recreate throttle intentionally
Pass current values as arguments
Use refs for mutable latest values
Structure state updates functionally
Use framework/library utilities
```

Example: pass current data as an argument.

```javascript
const throttledUpdate =
    useMemo(
        () =>
            throttle(
                (value) => {
                    console.log(value);
                },
                500
            ),
        []
    );
```

Then:

```javascript
throttledUpdate(count);
```

The latest value is passed directly instead of being read from an old closure.

---

# 51. Functional State Updates

If the callback updates state based on previous state:

```javascript
setCount(
    previous =>
        previous + 1
);
```

can avoid depending on a captured `count` value.

Compare:

```javascript
setCount(count + 1);
```

with:

```javascript
setCount(
    previous =>
        previous + 1
);
```

The functional form is often safer when asynchronous or delayed callbacks depend on previous state.

---

# 52. Throttle + Async Functions

You can throttle an async callback:

```javascript
const sendData =
    throttle(
        async (data) => {
            await saveData(data);
        },
        1000
    );
```

But be careful.

Throttle controls:

```text
When callback starts
```

It does not necessarily ensure:

```text
Previous async callback
has finished.
```

---

# 53. Async Overlap

Suppose:

```text
Throttle interval = 1 second
```

but each API request takes:

```text
3 seconds
```

Timeline:

```text
0s
Request A starts

1s
Request B starts

2s
Request C starts

3s
A completes
```

Multiple requests can still overlap.

Therefore:

```text
Throttling invocation frequency
≠
Limiting concurrency
```

---

# 54. Throttle vs Concurrency Control

Throttle:

```text
How often can work start?
```

Concurrency control:

```text
How many operations may
run simultaneously?
```

These are different concerns.

If only one request should run at a time, you may need additional logic such as:

```text
In-flight flag
Queue
Mutex/semaphore concept
AbortController
Request state management
```

depending on the application.

---

# 55. Throttle + AbortController

Sometimes new throttled work makes old work irrelevant.

Example:

```text
Position update request A
↓
Newer position becomes available
↓
Old request is no longer useful
```

You could combine:

```text
Throttle
+
AbortController
```

Throttle controls request frequency.

AbortController can cancel a supported previous request.

But only use this when the semantics make sense.

For critical state-changing operations, cancelling requests requires careful API design.

---

# 56. Common Mistake — Recreating Throttle Every Event

Wrong:

```javascript
window.addEventListener(
    "scroll",
    () => {
        const handler =
            throttle(
                updateUI,
                500
            );

        handler();
    }
);
```

Each scroll event creates a new throttle.

```text
Event A
↓
Throttle A

Event B
↓
Throttle B

Event C
↓
Throttle C
```

Each has independent state.

They cannot throttle one another.

Correct:

```javascript
const handler =
    throttle(
        updateUI,
        500
    );

window.addEventListener(
    "scroll",
    handler
);
```

---

# 57. Common Mistake — Confusing Throttle with Debounce

If the requirement is:

```text
Search after typing stops
```

throttle is usually not the first choice.

Use:

```text
Debounce
```

If the requirement is:

```text
Update periodically
while scrolling
```

throttle is usually appropriate.

---

# 58. Common Mistake — Assuming Throttle Runs Exactly on Schedule

Timers are not exact real-time scheduling mechanisms.

```javascript
setTimeout(fn, 100);
```

means the callback becomes eligible after the delay, subject to the event loop and runtime scheduling.

Likewise, throttle intervals should generally be understood as frequency limits, not hard real-time guarantees.

---

# 59. Common Mistake — Losing Final Event

A leading-only throttle can ignore the final call.

Example:

```text
Execute
Event
Event
Final Event
Activity stops
```

If the final state matters, consider:

```text
Trailing throttle
```

or:

```text
Leading + trailing
```

---

# 60. Common Mistake — Losing Arguments

A reusable throttle should forward arguments.

Use:

```javascript
function (...args)
```

and:

```javascript
callback.apply(
    this,
    args
);
```

---

# 61. Common Mistake — Losing `this`

If the callback depends on dynamic `this`, avoid unintentionally changing its context.

Example:

```javascript
callback.apply(
    this,
    args
);
```

---

# 62. Common Mistake — Using Throttle as Backend Protection

Frontend throttle can be bypassed.

Server-side protections may still require:

```text
Rate limiting
Authentication
Authorization
Validation
Quotas
Abuse prevention
```

Throttle is primarily application behavior optimization.

---

# 63. Common Mistake — Assuming Throttle Prevents Async Overlap

It doesn't necessarily.

```text
Interval = 1 second
Request duration = 5 seconds
```

Multiple requests can be running simultaneously.

Throttle frequency and concurrency are separate.

---

# 64. Common Mistake — Forgetting Cleanup

If your timer-based throttle has a pending trailing call:

```javascript
throttled.cancel();
```

may be appropriate when the related lifecycle ends.

In React:

```text
Component unmount
↓
Remove event listener
↓
Cancel pending throttle
```

---

# 65. Common Mistake — Using Throttle for Every High-Frequency Event

Sometimes the correct solution may instead be:

```text
Debounce
requestAnimationFrame
IntersectionObserver
ResizeObserver
CSS
Different architecture
```

Throttle is a tool, not a universal solution.

---

# 66. Interview Question — What Is Throttling?

Throttling limits how frequently a function can execute.

Even if the function is invoked repeatedly, the callback executes at most according to the configured interval and throttle behavior.

---

# 67. Interview Question — Why Use Throttling?

To reduce excessive repeated work from high-frequency events while still allowing periodic updates.

Common examples:

```text
Scroll
Mouse movement
Resize
Drag
Analytics
```

---

# 68. Interview Question — Throttle vs Debounce?

Throttle:

> Execute periodically while activity continues.

Debounce:

> Execute after activity has stopped for a specified period.

---

# 69. Interview Question — Does Throttle Use Closures?

Typically yes.

The returned function needs persistent state such as:

```text
lastExecution
waiting
timeoutId
lastArgs
```

Closure preserves this state between calls.

---

# 70. Interview Question — Is Throttle a Higher-Order Function?

Yes.

It accepts a function and returns another function.

---

# 71. Interview Question — Leading vs Trailing Throttle?

Leading:

```text
Execute at the beginning
of an allowed interval.
```

Trailing:

```text
Execute the latest pending
call at the end of the interval.
```

Some implementations support both.

---

# 72. Interview Question — Timestamp vs Timer Throttle?

Timestamp throttle compares:

```javascript
Date.now()
```

against the previous execution time.

Timer throttle uses:

```javascript
setTimeout()
```

to control when execution becomes available or to schedule trailing work.

---

# 73. Interview Question — Throttle vs `setInterval()`?

`setInterval()` executes according to a timer even without incoming events.

Throttle only has work to execute when the throttled function is actually called.

---

# 74. Interview Question — Does Throttle Prevent Concurrent Requests?

No.

Throttle controls how frequently operations start.

If operations last longer than the throttle interval, they can overlap.

---

# 75. Interview Question — Does Throttle Replace API Rate Limiting?

No.

Frontend throttle is not a security boundary.

API rate limiting must be enforced by trusted backend infrastructure.

---

# 76. Interview Question — Why Preserve `this` and Arguments?

The wrapper should behave like the original function as much as possible.

Therefore:

```javascript
callback.apply(
    this,
    args
);
```

can preserve the caller context and arguments.

---

# 77. Interview Question — Why Might Trailing Throttle Be Important?

Because leading-only throttling can ignore the final event.

If the final state matters, trailing execution ensures the latest pending call can be processed.

---

# 78. Interview Question — Why Use `requestAnimationFrame()`?

For visual work synchronized with browser rendering.

Examples:

```text
Animations
DOM movement
Scroll-based rendering
Drag visuals
```

It can be more appropriate than arbitrary millisecond intervals for rendering-related updates.

---

# 79. Implementation Question — Write a Basic Throttle

```javascript
function throttle(
    callback,
    delay
) {
    let lastExecution = 0;

    return function (...args) {
        const now = Date.now();

        if (
            now - lastExecution
            >= delay
        ) {
            lastExecution = now;

            callback.apply(
                this,
                args
            );
        }
    };
}
```

You should understand this implementation rather than only memorizing it.

---

# 80. Explain the Implementation

```javascript
let lastExecution = 0;
```

stores when the callback last ran.

```javascript
const now = Date.now();
```

gets the current timestamp.

```javascript
now - lastExecution
```

calculates elapsed time.

If:

```javascript
now - lastExecution >= delay
```

then enough time has passed.

So:

```javascript
lastExecution = now;
```

records the new execution time.

Then:

```javascript
callback.apply(
    this,
    args
);
```

runs the callback.

---

# 81. Output Question

Consider conceptually:

```javascript
const log =
    throttle(
        console.log,
        1000
    );

log("A");

setTimeout(() => {
    log("B");
}, 200);

setTimeout(() => {
    log("C");
}, 500);

setTimeout(() => {
    log("D");
}, 1100);
```

Using a leading timestamp throttle:

```text
~0 ms
A → Execute

~200 ms
B → Ignore

~500 ms
C → Ignore

~1100 ms
D → Execute
```

Output:

```text
A
D
```

---

# 82. Compare with Debounce

Same calls:

```text
A at 0 ms
B at 200 ms
C at 500 ms
D at 1100 ms
```

with a 1000 ms trailing debounce:

```text
A
↓
Timer reset by B

B
↓
Timer reset by C

C
↓
Timer reset by D

D
↓
No more calls
↓
Wait 1000 ms
↓
Execute D
```

Output:

```text
D
```

This demonstrates the fundamental difference.

---

# 83. Real-World Scroll Pattern

```javascript
function updateScrollProgress() {
    const scrollTop =
        window.scrollY;

    const documentHeight =
        document.documentElement
            .scrollHeight
        - window.innerHeight;

    const progress =
        documentHeight > 0
            ? scrollTop /
              documentHeight
            : 0;

    renderProgress(progress);
}

const throttledProgress =
    throttle(
        updateScrollProgress,
        100
    );

window.addEventListener(
    "scroll",
    throttledProgress
);
```

Flow:

```text
Scroll Events
│ │ │ │ │ │ │ │ │ │ │
▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼

Throttle
   │
   ▼

Calculate Progress
        │
        ▼
      Render
```

periodically rather than for every event.

---

# 84. Throttle Decision Guide

Ask:

```text
Do I need the function
to run while events
are continuing?
```

If:

```text
YES
```

consider:

```text
Throttle
```

If:

```text
NO, I mainly need
the final result after
events stop
```

consider:

```text
Debounce
```

If:

```text
The work is visual
and should align with
browser rendering
```

consider:

```text
requestAnimationFrame()
```

---

# 85. Debounce vs Throttle vs `requestAnimationFrame`

```text
DEBOUNCE
================================

Wait until activity stops.

Best for:

Search
Validation
Auto-save


THROTTLE
================================

Limit execution frequency
while activity continues.

Best for:

Scroll tracking
Mouse tracking
Periodic updates


REQUESTANIMATIONFRAME
================================

Schedule visual work around
browser repaint cycles.

Best for:

Animations
Visual scroll effects
Drag rendering
DOM movement
```

---

# 86. Throttle Mental Model

```text
               Function Call
                     │
                     ▼
              Can Execute?
                 /       \
               YES        NO
                │          │
                ▼          ▼
             Execute     Ignore /
                │       Remember Latest
                ▼
           Block Interval
                │
                ▼
          Interval Expires
                │
                ▼
           Can Execute Again
```

---

# 87. Throttle Cheat Sheet

```text
THROTTLE
================================

Limit how frequently
a function executes.


BASIC IDEA
================================

Many Calls
   │
   ▼
Throttle
   │
   ▼
Periodic Executions


TIMESTAMP VERSION
================================

function throttle(fn, delay) {
    let last = 0;

    return function (...args) {
        const now = Date.now();

        if (now - last >= delay) {
            last = now;

            fn.apply(
                this,
                args
            );
        }
    };
}


CORE CONCEPTS
================================

Closure
Higher-Order Functions
Callbacks
Date.now()
Timers
Rest Parameters
this


LEADING
================================

Execute immediately
when interval begins.


TRAILING
================================

Execute latest pending
call at interval end.


DEBOUNCE
================================

Wait for activity to stop.


THROTTLE
================================

Execute periodically
during activity.


SETINTERVAL
================================

Runs periodically regardless
of incoming events.


THROTTLE
================================

Runs only in response to
incoming calls.


ASYNC
================================

Throttle limits start
frequency, not concurrency.


RATE LIMITING
================================

Throttle is not server-side
API protection.


VISUAL WORK
================================

Consider
requestAnimationFrame().
```

---

# 88. Important Rules to Remember

```text
1. Throttling limits function execution frequency.

2. Repeated calls can continue while throttling is active.

3. Throttle still allows periodic execution.

4. Debounce usually waits until activity stops.

5. Throttle is useful when updates are needed during activity.

6. Scroll is a common throttle use case.

7. Mouse movement is a common throttle use case.

8. Drag events can use throttling.

9. Resize can use throttle or debounce depending on requirements.

10. Throttle is a higher-order function.

11. Throttle usually uses closures.

12. Closure preserves throttle state between calls.

13. Timestamp throttle can use Date.now().

14. Timer throttle can use setTimeout().

15. Leading throttle executes at the beginning.

16. Trailing throttle processes a pending latest call later.

17. Leading-only throttle may lose the final event.

18. Leading + trailing can preserve responsiveness and final state.

19. Rest parameters preserve arguments.

20. apply() can preserve this and arguments.

21. A normal function wrapper can preserve dynamic this.

22. Arrow functions have lexical this.

23. Each throttled function should normally have independent state.

24. Do not recreate throttle for every event.

25. Calls must share the same throttle closure.

26. Throttle is not the same as setInterval().

27. setInterval() can execute without incoming events.

28. Throttle is demand-driven by function calls.

29. Throttle does not automatically cancel async operations.

30. Throttle does not necessarily prevent async overlap.

31. Invocation frequency and concurrency are different concepts.

32. Throttle does not replace AbortController.

33. Throttle can be combined with AbortController when appropriate.

34. Throttle does not replace backend rate limiting.

35. Frontend throttle is not a security mechanism.

36. Throttle timing is not hard real-time scheduling.

37. Timer callbacks depend on event-loop scheduling.

38. Trailing throttles may need cleanup.

39. cancel() can remove pending trailing execution.

40. flush() can process pending work immediately.

41. requestAnimationFrame() can be useful for visual updates.

42. requestAnimationFrame() follows browser rendering scheduling.

43. Do not assume requestAnimationFrame means exactly 60 FPS.

44. React throttled functions need stable identity when appropriate.

45. Recreating a throttle on every render resets its closure state.

46. React throttled callbacks can suffer from stale closures.

47. Passing current values as arguments can help avoid stale captured values.

48. Functional state updates can help when previous state is required.

49. Choose debounce when the final event after inactivity matters.

50. Choose throttle when periodic execution during activity matters.
```

---

# Throttling in One Sentence

> Throttling limits how frequently a function can execute while allowing periodic execution during continuous activity.

---

# Final Mental Model

```text
EVENTS

│ │ │ │ │ │ │ │ │ │ │ │ │ │ │
▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼


WITHOUT THROTTLE

E E E E E E E E E E E E E E E


WITH THROTTLE

E         E         E         E
│         │         │         │
▼         ▼         ▼         ▼
Run       Run       Run       Run
```

And the most important comparison:

```text
DEBOUNCE
================================

Event Event Event Event Event
                        │
                        ▼
                      Wait
                        │
                        ▼
                     Execute

"When activity stops,
run the function."


THROTTLE
================================

Event Event Event Event Event Event
  │           │           │
  ▼           ▼           ▼
Execute     Execute      Execute

"While activity continues,
limit how often the
function runs."
```
