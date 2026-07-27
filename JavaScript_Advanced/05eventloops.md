# Event Loop

The **Event Loop** is the runtime scheduling mechanism that coordinates when asynchronous callbacks, tasks, and microtasks get an opportunity to execute.

It connects concepts we have already learned:

```text
JavaScript Engine
       +
Call Stack
       +
Runtime / Host APIs
       +
Tasks
       +
Microtasks
       ↓
   EVENT LOOP
```

A simplified browser mental model:

```text
             JavaScript Engine
                    │
                    ▼
              ┌──────────┐
              │Call Stack│
              └──────────┘
                    ▲
                    │
              Event Loop
               /        \
              /          \
             ▼            ▼
      Microtasks       Task Queues
             ▲            ▲
             │            │
       Promise.then    setTimeout
       queueMicrotask  Events
       async/await     Messages
```

The exact browser event loop is more sophisticated than this diagram, but this model is useful for understanding execution order.

---

# 1. Why Do We Need the Event Loop?

JavaScript commonly executes application code using one active call stack.

Suppose:

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timer");
}, 2000);

console.log("End");
```

Output:

```text
Start
End
Timer
```

JavaScript does not block the call stack for two seconds.

Instead:

```text
JavaScript registers timer
        ↓
Runtime manages timer
        ↓
JavaScript continues
        ↓
Current task completes
        ↓
Timer eventually becomes eligible
        ↓
Timer callback becomes scheduled task work
        ↓
Event-loop processing eventually selects it
        ↓
Callback executes
```

The event loop is part of the mechanism that makes this asynchronous scheduling possible.

---

# 2. Core Components

For most JavaScript event-loop questions, think about:

```text
1. Call Stack

2. Host / Web APIs

3. Tasks

4. Microtasks

5. Event Loop
```

Simplified architecture:

```text
┌───────────────────────────────────────┐
│               Browser                 │
│                                       │
│      ┌────────────────────────┐       │
│      │       Call Stack       │       │
│      └────────────────────────┘       │
│                                       │
│      Host / Web APIs                  │
│      ┌────────────────────────┐       │
│      │ Timers                 │       │
│      │ Networking             │       │
│      │ DOM Events             │       │
│      │ Other Browser APIs     │       │
│      └────────────────────────┘       │
│                                       │
│      Microtask Queue                  │
│                                       │
│      Task Queues                      │
│                                       │
│      Event Loop                       │
│                                       │
└───────────────────────────────────────┘
```

---

# 3. Call Stack

The **Call Stack** tracks currently executing JavaScript function calls.

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
┌───────────────┐
│    second()   │
├───────────────┤
│    first()    │
├───────────────┤
│ global script │
└───────────────┘
```

The currently running JavaScript must complete before the runtime can simply interrupt it to run an unrelated timer callback.

---

# 4. Run-to-Completion

JavaScript tasks generally follow a principle called:

```text
Run-to-Completion
```

Once JavaScript begins executing a task, that task runs until its JavaScript execution completes.

Example:

```javascript
console.log("A");

for (let i = 0; i < 1_000_000; i++) {
    // synchronous work
}

console.log("B");
```

Other queued callbacks do not suddenly interrupt the loop halfway through.

Conceptually:

```text
Current JavaScript starts
        ↓
Runs
        ↓
Runs
        ↓
Finishes
        ↓
Runtime can process later work
```

---

# 5. What Is a Task?

A **task** is a unit of work scheduled by the host environment.

Common examples can include:

```text
Timer callbacks
User interaction events
Message events
Script execution
Other host-defined work
```

Learning material often calls these:

```text
Macrotasks
```

However, browser specifications generally use the term:

```text
Task
```

So:

```text
"Macrotask Queue"
```

is useful teaching terminology, but **task** is the more standards-oriented term.

---

# 6. Task Queue

A simplified model:

```text
Task Queue

┌──────────────┐
│ Timer        │
├──────────────┤
│ Click Event  │
├──────────────┤
│ Message      │
└──────────────┘
```

The event loop chooses task work according to host scheduling rules.

A more accurate model is that browsers can have **multiple task queues/task sources**, rather than one universal queue.

For interview reasoning, however:

```text
Task Queue
```

is often used as a simplified abstraction.

---

# 7. What Is a Microtask?

A **microtask** is scheduled work that receives special processing at microtask checkpoints.

Important examples:

```text
Promise reactions

.then()
.catch()
.finally()

queueMicrotask()

async/await continuations
```

Example:

```javascript
Promise.resolve().then(() => {
    console.log("Microtask");
});
```

The `.then()` callback does not execute immediately.

It becomes microtask work.

---

# 8. Microtask Queue

Simplified:

```text
Microtask Queue

┌──────────────────┐
│ Promise callback │
├──────────────────┤
│ queueMicrotask   │
├──────────────────┤
│ async continuation│
└──────────────────┘
```

Microtasks are processed at specific points called:

```text
Microtask Checkpoints
```

---

# 9. Task vs Microtask

```text
TASK
================================

Examples:

setTimeout callback
setInterval callback
DOM event
Message event


MICROTASK
================================

Examples:

Promise.then
Promise.catch
Promise.finally
queueMicrotask
async/await continuation
```

The key distinction:

```text
After current task
        ↓
Microtasks are drained
        ↓
Runtime may render / do host work
        ↓
Next task
```

---

# 10. Basic Event Loop Rule

For common browser examples:

```text
1. Execute current task

2. Execute synchronous JavaScript

3. When the task finishes,
   perform a microtask checkpoint

4. Drain microtasks

5. Browser may get a rendering opportunity

6. Select another task

7. Repeat
```

Simplified:

```text
Task
 ↓
Synchronous JavaScript
 ↓
Microtasks
 ↓
Possible Rendering
 ↓
Next Task
 ↓
Microtasks
 ↓
Possible Rendering
 ↓
...
```

---

# 11. The Most Important Rule

For output questions, remember:

```text
Synchronous Code
       ↓
Microtasks
       ↓
Next Task
```

Example:

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

Because:

```text
Synchronous
───────────
A
D

Microtask
───────────
C

Timer Task
───────────
B
```

---

# 12. Simple Timer Example

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

Why?

The timer callback is not executed synchronously.

---

# 13. Timer Execution Flow

```text
console.log("Start")
        ↓
Start


setTimeout(...)
        ↓
Timer registered with runtime


console.log("End")
        ↓
End


Current task completes
        ↓
Microtask checkpoint
        ↓
Timer task can execute later
        ↓
Timer
```

Final:

```text
Start
End
Timer
```

---

# 14. `setTimeout(0)` Is Not Immediate

This:

```javascript
setTimeout(() => {
    console.log("Hello");
}, 0);
```

does **not** mean:

```text
Execute now.
```

It means the timer has no requested delay beyond the specified zero, subject to timer rules, but its callback still executes through task scheduling.

So:

```javascript
setTimeout(() => {
    console.log("A");
}, 0);

console.log("B");
```

outputs:

```text
B
A
```

---

# 15. Timer Delay Is Not Exact Execution Time

```javascript
setTimeout(callback, 1000);
```

does not mean:

```text
callback executes exactly
1000 ms later
```

It means the callback cannot run before the timer satisfies applicable timing rules, and it still needs an opportunity to execute.

For example:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 1000);

const start = Date.now();

while (Date.now() - start < 5000) {
    // block
}
```

The callback cannot execute while the JavaScript thread is busy with the loop.

---

# 16. Promise Example

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

Why?

The `.then()` callback becomes a microtask.

---

# 17. Promise + Timer

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

Execution:

```text
Current Task
│
├── Start
├── Register Timer
├── Schedule Promise Microtask
└── End
        ↓
Task finishes
        ↓
Drain Microtasks
        ↓
Promise
        ↓
Later Task
        ↓
Timer
```

---

# 18. Why Promise Runs Before Timer

Because:

```text
Promise.then()
↓
Microtask


setTimeout()
↓
Timer Task
```

After current synchronous execution finishes:

```text
Microtasks
```

are processed before moving to the next task.

Therefore:

```text
Promise
Timer
```

---

# 19. `queueMicrotask()`

JavaScript provides:

```javascript
queueMicrotask(() => {
    console.log("Microtask");
});
```

It explicitly queues a microtask.

Example:

```javascript
console.log("A");

queueMicrotask(() => {
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

# 20. `queueMicrotask()` vs `setTimeout()`

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

queueMicrotask(() => {
    console.log("Microtask");
});
```

Output:

```text
Microtask
Timer
```

because the microtask checkpoint occurs before the next timer task gets to run.

---

# 21. Promise vs `queueMicrotask()`

Both can schedule microtasks.

Example:

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});

queueMicrotask(() => {
    console.log("Microtask");
});
```

They are queued in the order they are scheduled.

Output:

```text
Promise
Microtask
```

Reverse them:

```javascript
queueMicrotask(() => {
    console.log("Microtask");
});

Promise.resolve().then(() => {
    console.log("Promise");
});
```

Output:

```text
Microtask
Promise
```

---

# 22. Microtasks Are FIFO

For normal examples, microtasks are processed in queue order.

```javascript
Promise.resolve().then(() => {
    console.log("A");
});

Promise.resolve().then(() => {
    console.log("B");
});

Promise.resolve().then(() => {
    console.log("C");
});
```

Output:

```text
A
B
C
```

---

# 23. Microtasks Can Add More Microtasks

Example:

```javascript
Promise.resolve().then(() => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });
});

Promise.resolve().then(() => {
    console.log("C");
});
```

Initial microtask queue:

```text
A callback
C callback
```

Execute A:

```text
A
```

A schedules B.

Queue becomes:

```text
C callback
B callback
```

Therefore output:

```text
A
C
B
```

---

# 24. Microtask Queue Is Drained

The runtime does not normally execute only one microtask and immediately move to the next task.

Instead:

```text
Microtask checkpoint
        ↓
Run microtask
        ↓
Run next microtask
        ↓
Run newly added microtasks
        ↓
Continue until queue is empty
```

Then the runtime can proceed.

---

# 25. Microtask Starvation

Because microtasks can create more microtasks, they can continuously keep the microtask queue non-empty.

Example:

```javascript
function loop() {
    queueMicrotask(loop);
}

loop();
```

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

This can prevent the runtime from getting to other work promptly.

This is called:

```text
Microtask Starvation
```

---

# 26. Why Microtask Starvation Matters

Suppose:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

function repeat() {
    queueMicrotask(repeat);
}

repeat();
```

The continuously replenished microtask queue can prevent the timer task from getting an opportunity to execute.

It can also interfere with browser rendering opportunities.

---

# 27. `async/await` and the Event Loop

`async/await` is built on promise semantics.

Example:

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

The code after `await` continues asynchronously.

---

# 28. Code Before `await`

When an async function is called:

```javascript
async function test() {
    console.log("A");

    await Promise.resolve();

    console.log("B");
}
```

the function begins executing synchronously.

So:

```javascript
test();
```

immediately executes:

```text
A
```

until it reaches the suspension point.

---

# 29. Code After `await`

After:

```javascript
await Promise.resolve();
```

the remainder:

```javascript
console.log("B");
```

continues later according to promise-job/microtask scheduling.

Conceptually:

```text
test()
↓
A
↓
await
↓
suspend continuation
↓
caller continues
↓
microtask checkpoint
↓
resume
↓
B
```

---

# 30. Async/Await Example

```javascript
console.log("1");

async function test() {
    console.log("2");

    await Promise.resolve();

    console.log("3");
}

test();

console.log("4");
```

Output:

```text
1
2
4
3
```

---

# 31. Multiple `await`s

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

Each `await` introduces another asynchronous continuation point.

---

# 32. Promise Chain

```javascript
console.log("A");

Promise.resolve()
    .then(() => {
        console.log("B");
    })
    .then(() => {
        console.log("C");
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

Important:

The second `.then()` is not immediately placed in the microtask queue at initial script execution.

It depends on the promise returned by the first `.then()`.

After the first handler executes and that promise fulfills, the next reaction becomes eligible.

---

# 33. Promise Chain with Another Promise

```javascript
Promise.resolve()
    .then(() => {
        console.log("A");
    })
    .then(() => {
        console.log("B");
    });

Promise.resolve().then(() => {
    console.log("C");
});
```

Output:

```text
A
C
B
```

Initial queue:

```text
A
C
```

Run A.

The chained B becomes queued.

Queue:

```text
C
B
```

Then:

```text
C
B
```

Final:

```text
A
C
B
```

---

# 34. Nested Promise

```javascript
Promise.resolve().then(() => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });

    console.log("C");
});
```

Output:

```text
A
C
B
```

Why?

Inside the first microtask:

```text
A
↓
schedule B
↓
C
↓
current microtask finishes
↓
B
```

---

# 35. Timer Inside Promise

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

Groups:

```text
Synchronous
────────────
A
E

Microtask
────────────
B
D

Later Timer Task
────────────
C
```

---

# 36. Promise Inside Timer

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

When the timer task runs:

```text
B
↓
schedule microtask C
↓
D
↓
timer task completes
↓
microtask checkpoint
↓
C
```

---

# 37. Two Timers with Promise

```javascript
setTimeout(() => {
    console.log("Timer 1");

    Promise.resolve().then(() => {
        console.log("Promise");
    });
}, 0);

setTimeout(() => {
    console.log("Timer 2");
}, 0);
```

Typical output:

```text
Timer 1
Promise
Timer 2
```

Why?

After the first timer task completes:

```text
Microtask checkpoint
```

occurs before the next task.

So:

```text
Timer 1
↓
Promise microtask
↓
Timer 2
```

---

# 38. This Rule Is Extremely Important

Do not think:

```text
Run all tasks
↓
Then all microtasks
```

Incorrect.

Instead:

```text
Task 1
↓
Drain Microtasks
↓
Task 2
↓
Drain Microtasks
↓
Task 3
↓
Drain Microtasks
```

This explains many interview questions.

---

# 39. Tasks Can Generate Microtasks

Example:

```javascript
setTimeout(() => {
    console.log("Task");

    Promise.resolve().then(() => {
        console.log("Microtask");
    });
}, 0);
```

Execution:

```text
Timer Task starts
↓
Task
↓
Promise microtask scheduled
↓
Timer Task ends
↓
Microtask checkpoint
↓
Microtask
```

---

# 40. Microtasks Can Generate Tasks

Example:

```javascript
Promise.resolve().then(() => {
    console.log("Microtask");

    setTimeout(() => {
        console.log("Task");
    }, 0);
});
```

Execution:

```text
Current task ends
↓
Microtask
↓
Timer registered
↓
Microtask queue drains
↓
Timer task later
```

Output:

```text
Microtask
Task
```

---

# 41. User Events

Consider:

```javascript
button.addEventListener("click", () => {
    console.log("Clicked");
});
```

The callback does not remain on the call stack.

Instead:

```text
Register listener
↓
JavaScript continues
↓
User clicks
↓
Browser processes event
↓
Event task gets scheduled
↓
Handler executes when selected
```

---

# 42. Event Handler + Promise

```javascript
button.addEventListener("click", () => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });

    console.log("C");
});
```

When clicked:

```text
A
C
B
```

Because the event handler runs as task work, and its promise reaction runs during the microtask checkpoint after the handler completes.

---

# 43. Event Handler + Timer + Promise

```javascript
button.addEventListener("click", () => {
    console.log("A");

    setTimeout(() => {
        console.log("B");
    }, 0);

    Promise.resolve().then(() => {
        console.log("C");
    });

    console.log("D");
});
```

After the click, assuming no other relevant scheduling interference:

```text
A
D
C
B
```

---

# 44. Browser Rendering

The browser also needs to render the page.

A simplified mental model:

```text
Task
↓
JavaScript
↓
Microtasks
↓
Possible Rendering Opportunity
↓
Next Task
```

Rendering can involve:

```text
Style calculation
Layout
Paint
Compositing
```

Actual browser rendering architecture is more complex, but this model explains why long tasks can hurt UI responsiveness.

---

# 45. Rendering Does Not Happen After Every Statement

Example:

```javascript
element.textContent = "Loading...";

const start = Date.now();

while (Date.now() - start < 5000) {
    // blocking
}

element.textContent = "Done";
```

You might expect:

```text
Loading...
↓
wait 5 sec
↓
Done
```

visually.

But the browser may not get a rendering opportunity between those JavaScript operations because the current task remains busy.

The user may only see:

```text
Done
```

after JavaScript finishes.

---

# 46. Long Tasks

A long-running JavaScript task can delay:

```text
Rendering
Input handling
Timers
Other events
Other JavaScript callbacks
```

Example:

```javascript
button.addEventListener("click", () => {
    for (let i = 0; i < 5_000_000_000; i++) {
        // expensive work
    }
});
```

The event loop cannot move on until the current JavaScript execution completes.

---

# 47. Breaking Work into Chunks

Large work can sometimes be split into smaller pieces.

Conceptually:

```text
Huge Task
████████████████████████████████
```

vs:

```text
Task
████

Task
████

Task
████

Task
████
```

This can give the browser opportunities to process other work between chunks.

The appropriate technique depends on the application.

---

# 48. `setInterval()`

Example:

```javascript
setInterval(() => {
    console.log("Tick");
}, 1000);
```

This requests repeated timer scheduling.

But it does not guarantee:

```text
Exactly one callback
every 1000 ms
```

If JavaScript is busy, callbacks can be delayed.

---

# 49. `setInterval()` and Slow Work

```javascript
setInterval(() => {
    const start = Date.now();

    while (Date.now() - start < 3000) {
        // expensive work
    }

    console.log("Done");
}, 1000);
```

The callback takes longer than the requested interval.

The runtime cannot execute multiple callbacks simultaneously on the same JavaScript call stack.

Timer scheduling is therefore affected by current execution and host timer rules.

---

# 50. `requestAnimationFrame()`

Browsers provide:

```javascript
requestAnimationFrame(callback);
```

for work associated with visual updates.

Example:

```javascript
function animate() {
    // update animation

    requestAnimationFrame(animate);
}

requestAnimationFrame(animate);
```

This is generally preferable to using `setInterval()` for frame-based browser animation because it integrates with the browser's rendering schedule.

---

# 51. Event Loop and Fetch

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

Immediate output:

```text
Start
End
```

The network operation progresses asynchronously.

When the fetch promise settles, its registered promise reactions are scheduled as microtasks.

---

# 52. Fetch Does Not Mean Task Callback

A common oversimplification is:

```text
fetch response
↓
Task Queue
↓
.then()
```

Promise reactions themselves are microtasks.

A better simplified model:

```text
Runtime handles network request
↓
Request completes
↓
Fetch promise settles
↓
Promise reaction becomes microtask
↓
Microtask checkpoint
↓
.then() handler executes
```

---

# 53. Fetch Ordering Is Timing-Dependent

Consider:

```javascript
setTimeout(() => {
    console.log("Timer");
}, 0);

fetch("/api/users")
    .then(() => {
        console.log("Fetch");
    });
```

You should **not** automatically claim:

```text
Fetch
Timer
```

just because promise handlers are microtasks.

The fetch promise cannot schedule its reaction until the network operation has progressed enough for the promise to settle.

The timer may become ready first.

So actual ordering depends on when the asynchronous operation completes relative to task scheduling.

---

# 54. Already-Resolved Promise Is Different

This:

```javascript
Promise.resolve().then(() => {
    console.log("Promise");
});

setTimeout(() => {
    console.log("Timer");
}, 0);
```

is predictable:

```text
Promise
Timer
```

because the promise is already fulfilled and its reaction microtask is scheduled during the current task.

---

# 55. Event Loop Algorithm — Simplified

A useful learning algorithm:

```text
LOOP:

1. Select runnable task

2. Execute task

3. JavaScript runs until completion

4. Perform microtask checkpoint

5. Drain microtask queue

6. Browser may update rendering

7. Repeat
```

Do not treat this as the full browser specification.

It is a mental model.

---

# 56. Why "Call Stack Must Be Empty" Is an Oversimplification

You will often hear:

> The event loop checks whether the call stack is empty and moves callbacks into it.

This is useful initially but incomplete.

A more accurate understanding is:

```text
The runtime processes tasks,
performs microtask checkpoints,
and schedules execution according
to the host event-loop algorithm.
```

Callbacks do not literally jump from one universal queue into the stack whenever it happens to look empty.

---

# 57. Multiple Task Queues

Browsers can maintain different task queues associated with different task sources.

Conceptually:

```text
Event Loop
│
├── Timer-related tasks
├── User interaction tasks
├── Networking-related work
└── Other task sources
```

The host determines how eligible tasks are selected.

Therefore:

```text
One Callback Queue
```

is only a simplified teaching model.

---

# 58. Microtask Checkpoint

A **microtask checkpoint** is when the runtime processes queued microtasks.

A key behavior is:

```text
Run microtasks
↓
If they add more microtasks
↓
Run those too
↓
Continue until queue is empty
```

This is why:

```javascript
queueMicrotask(() => {
    console.log("A");

    queueMicrotask(() => {
        console.log("B");
    });
});
```

outputs:

```text
A
B
```

before moving to the next task.

---

# 59. Error Handling Difference

Consider:

```javascript
queueMicrotask(() => {
    throw new Error("Error");
});
```

versus:

```javascript
Promise.resolve().then(() => {
    throw new Error("Error");
});
```

An exception thrown inside a promise reaction causes the promise returned by `.then()` to reject.

With `queueMicrotask()`, the exception is reported as an uncaught exception according to the host's error-reporting behavior.

This is one practical difference between the two approaches.

---

# 60. `async` Function Return

```javascript
async function getValue() {
    return 10;
}
```

Calling:

```javascript
const result = getValue();
```

gives a promise.

So:

```javascript
result.then(value => {
    console.log(value);
});
```

logs:

```text
10
```

through promise reaction scheduling.

---

# 61. Async Function Throw

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

Handle it:

```javascript
test().catch(error => {
    console.error(error);
});
```

The `.catch()` reaction is scheduled using promise/microtask semantics.

---

# 62. Common Output Strategy

When solving event-loop output questions, create three categories:

```text
SYNC
MICROTASK
TASK
```

Example:

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");
}, 0);

Promise.resolve().then(() => {
    console.log("C");
});

queueMicrotask(() => {
    console.log("D");
});

console.log("E");
```

Classify:

```text
SYNC
────
A
E


MICROTASK
─────────
C
D


TASK
────
B
```

Output:

```text
A
E
C
D
B
```

---

# 63. But Queues Change During Execution

For harder questions, don't classify everything once and stop.

Callbacks can create additional work.

Example:

```javascript
Promise.resolve().then(() => {
    console.log("A");

    setTimeout(() => {
        console.log("B");
    }, 0);

    Promise.resolve().then(() => {
        console.log("C");
    });
});
```

The queues change while the program executes.

You must simulate them step-by-step.

---

# 64. Output Question 1

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

# 65. Output Question 2

```javascript
console.log("1");

Promise.resolve().then(() => {
    console.log("2");
});

queueMicrotask(() => {
    console.log("3");
});

console.log("4");
```

Output:

```text
1
4
2
3
```

---

# 66. Output Question 3

```javascript
console.log("1");

queueMicrotask(() => {
    console.log("2");
});

Promise.resolve().then(() => {
    console.log("3");
});

console.log("4");
```

Output:

```text
1
4
2
3
```

Microtasks maintain scheduling order.

---

# 67. Output Question 4

```javascript
Promise.resolve().then(() => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });
});

Promise.resolve().then(() => {
    console.log("C");
});
```

Output:

```text
A
C
B
```

---

# 68. Output Question 5

```javascript
setTimeout(() => {
    console.log("A");

    Promise.resolve().then(() => {
        console.log("B");
    });
}, 0);

setTimeout(() => {
    console.log("C");
}, 0);
```

Typical output:

```text
A
B
C
```

After the first timer task:

```text
A
↓
microtask B
↓
next timer task
↓
C
```

---

# 69. Output Question 6

```javascript
Promise.resolve().then(() => {
    console.log("A");

    setTimeout(() => {
        console.log("B");
    }, 0);
});

Promise.resolve().then(() => {
    console.log("C");
});
```

Output:

```text
A
C
B
```

---

# 70. Output Question 7

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

# 71. Output Question 8

```javascript
async function first() {
    console.log("A");

    await second();

    console.log("B");
}

async function second() {
    console.log("C");
}

console.log("D");

first();

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

---

# 72. Output Question 9

```javascript
console.log("A");

setTimeout(() => {
    console.log("B");

    Promise.resolve().then(() => {
        console.log("C");
    });

    setTimeout(() => {
        console.log("D");
    }, 0);

    console.log("E");
}, 0);

console.log("F");
```

Output:

```text
A
F
B
E
C
D
```

Reason:

```text
Initial synchronous
───────────────────
A
F


First Timer Task
───────────────────
B
E


Microtask after timer
───────────────────
C


Later Timer Task
───────────────────
D
```

---

# 73. Output Question 10

```javascript
console.log("1");

Promise.resolve()
    .then(() => {
        console.log("2");
    })
    .then(() => {
        console.log("3");
    });

Promise.resolve().then(() => {
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

Initial microtasks:

```text
2
4
```

After `2`, the next chained handler `3` becomes queued:

```text
4
3
```

Therefore:

```text
2
4
3
```

---

# 74. Output Question 11

```javascript
console.log("A");

queueMicrotask(() => {
    console.log("B");

    queueMicrotask(() => {
        console.log("C");
    });
});

queueMicrotask(() => {
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

Queue evolution:

```text
Initial:

B
D


Run B:

B schedules C


Queue:

D
C
```

---

# 75. Output Question 12

```javascript
setTimeout(() => {
    console.log("Timer 1");

    queueMicrotask(() => {
        console.log("Microtask 1");
    });

    Promise.resolve().then(() => {
        console.log("Microtask 2");
    });
}, 0);

setTimeout(() => {
    console.log("Timer 2");
}, 0);
```

Typical output:

```text
Timer 1
Microtask 1
Microtask 2
Timer 2
```

---

# 76. Output Question 13

```javascript
console.log("A");

async function test() {
    console.log("B");

    await null;

    console.log("C");
}

Promise.resolve().then(() => {
    console.log("D");
});

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

Why?

The first promise reaction is queued before `test()` reaches its `await`.

Conceptually:

```text
Queue:

D
C continuation
```

Therefore:

```text
D
C
```

---

# 77. Output Question 14

```javascript
console.log("A");

async function test() {
    console.log("B");

    await null;

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

Here the async continuation is scheduled before the explicit promise reaction.

Microtask order matters.

---

# 78. Output Question 15

```javascript
setTimeout(() => {
    console.log("A");
}, 0);

Promise.resolve().then(() => {
    console.log("B");

    Promise.resolve().then(() => {
        console.log("C");
    });
});

queueMicrotask(() => {
    console.log("D");
});

console.log("E");
```

Output:

```text
E
B
D
C
A
```

Initial microtasks:

```text
B
D
```

Run B:

```text
B
```

B schedules C.

Queue:

```text
D
C
```

Then:

```text
D
C
```

After microtasks drain:

```text
A
```

---

# 79. Interview Question — What Is the Event Loop?

The Event Loop is part of the runtime mechanism that coordinates execution of scheduled work, including tasks and microtasks, relative to currently executing JavaScript and other host activities.

---

# 80. Interview Question — Why Is the Event Loop Needed?

Because JavaScript needs a mechanism to coordinate asynchronous work such as:

```text
Timers
Network operations
Events
Promise continuations
```

without synchronously blocking the JavaScript thread while waiting for those operations.

---

# 81. Interview Question — What Is a Task?

A task is a unit of work scheduled by the host environment.

Examples include timer callbacks and user-interaction events.

The term **macrotask** is often used informally in tutorials.

---

# 82. Interview Question — What Is a Microtask?

A microtask is scheduled work processed at microtask checkpoints.

Important examples:

```text
Promise reactions
queueMicrotask()
async/await continuations
```

---

# 83. Interview Question — Task vs Microtask?

```text
TASK
────────────────────

Examples:

Timers
Events
Messages


MICROTASK
────────────────────

Examples:

Promise reactions
queueMicrotask
async continuation
```

After current task execution completes, microtasks are drained before moving to another task.

---

# 84. Interview Question — What Has Higher Priority?

People often say:

```text
Microtasks have higher priority
than tasks.
```

This is useful shorthand.

A more precise statement is:

> Microtasks are drained during microtask checkpoints before the event loop proceeds to another task.

---

# 85. Interview Question — Does Event Loop Execute JavaScript?

No.

The:

```text
JavaScript Engine
```

executes JavaScript.

The event loop coordinates scheduling of work.

---

# 86. Interview Question — Does Event Loop Push Callbacks onto Stack?

That is a simplified teaching explanation.

A better explanation is:

> The host event-loop algorithm selects work to execute, and the JavaScript engine executes the corresponding JavaScript.

---

# 87. Interview Question — Why Does Promise Run Before `setTimeout`?

Because promise reactions are microtasks, while timer callbacks are task work.

After the current task finishes, microtasks are processed before another task is selected.

---

# 88. Interview Question — Is `setTimeout(0)` Immediate?

No.

It schedules timer work for later execution.

Current synchronous JavaScript and applicable microtasks execute first.

---

# 89. Interview Question — Can Microtasks Delay Timers?

Yes.

If microtasks continuously schedule additional microtasks, the runtime can keep processing them before moving to another task.

This can delay timers and other work.

---

# 90. Interview Question — What Is Microtask Starvation?

Microtask starvation occurs when microtasks continually generate additional microtasks, preventing or significantly delaying progression to other task work or rendering opportunities.

---

# 91. Interview Question — Does Rendering Happen Between Every Task?

Rendering is controlled by the browser and occurs when rendering opportunities are available.

A useful simplified model is:

```text
Task
↓
Microtasks
↓
Possible Rendering
↓
Next Task
```

Rendering is not guaranteed after every JavaScript operation.

---

# 92. Interview Question — Is Browser Event Loop Same as Node.js?

No.

They share important asynchronous scheduling concepts, but Node.js has its own event-loop architecture and phases.

Do not assume every browser scheduling rule maps directly to Node.js.

---

# 93. Node.js Event Loop Overview

Node.js uses:

```text
V8
+
libuv
```

A simplified Node.js event-loop phase model includes:

```text
Timers
↓
Pending callbacks
↓
Poll
↓
Check
↓
Close callbacks
```

There are additional details and version-dependent behaviors.

Node.js deserves its own topic if you're studying backend JavaScript deeply.

---

# 94. Node.js `process.nextTick()`

Node.js provides:

```javascript
process.nextTick(callback);
```

This is Node-specific.

It has special scheduling semantics and should not simply be treated as equivalent to:

```javascript
queueMicrotask()
```

For frontend/React learning, the browser task/microtask model is the main priority.

---

# 95. Browser vs Node.js

```text
BROWSER
================================

Web APIs
DOM Events
Rendering
Task queues
Microtasks
Browser Event Loop


NODE.JS
================================

Node APIs
libuv
I/O
Event Loop Phases
Microtasks
process.nextTick
```

The concepts overlap, but implementation and scheduling details differ.

---

# 96. Event Loop and React

Understanding the Event Loop helps explain React behavior involving:

```text
Event handlers
API requests
Promises
async/await
Timers
Rendering
State updates
Batching
Long-running handlers
UI responsiveness
```

Example:

```javascript
function handleClick() {
    setLoading(true);

    expensiveSynchronousWork();

    setLoading(false);
}
```

You might expect the browser to visibly render:

```text
Loading...
```

before the expensive work.

But if the entire handler remains one long task, the browser may not get a rendering opportunity until the task completes.

---

# 97. React API Example

```javascript
async function loadUsers() {
    setLoading(true);

    const response =
        await fetch("/api/users");

    const users =
        await response.json();

    setUsers(users);

    setLoading(false);
}
```

At each `await`, the function can suspend while the browser remains free to process other work.

When the promises settle, execution resumes through promise/microtask scheduling.

---

# 98. React Does Not Replace the Event Loop

React runs within the JavaScript/browser runtime.

Conceptually:

```text
Browser Runtime
│
├── JavaScript Engine
├── Event Loop
├── DOM
├── Web APIs
│
└── React Application
```

React scheduling adds another layer of behavior, but it still operates within the underlying runtime.

---

# 99. How to Solve Event Loop Questions

Use this process:

```text
STEP 1
──────────────

Execute synchronous code.


STEP 2
──────────────

Record microtasks as they
are scheduled.


STEP 3
──────────────

Record task work such as timers.


STEP 4
──────────────

When current task ends,
drain microtasks.


STEP 5
──────────────

If microtasks create more
microtasks, append them.


STEP 6
──────────────

Continue until microtask
queue is empty.


STEP 7
──────────────

Move to next eligible task.


STEP 8
──────────────

After that task,
drain microtasks again.


STEP 9
──────────────

Repeat.
```

---

# 100. Event Loop Cheat Sheet

```text
EVENT LOOP
================================

Coordinates scheduled work
in the JavaScript runtime.


CALL STACK
================================

Tracks active JavaScript
function execution.


TASK
================================

Unit of host-scheduled work.

Examples:

setTimeout callback
setInterval callback
DOM event
Message event


MACROTASK
================================

Common tutorial term for
normal task work.

"Task" is the preferred
browser-spec terminology.


MICROTASK
================================

Special scheduled work.

Examples:

Promise.then
Promise.catch
Promise.finally
queueMicrotask
async/await continuation


CORE ORDER
================================

Current Task
↓
Synchronous JavaScript
↓
Microtask Checkpoint
↓
Drain Microtasks
↓
Possible Rendering
↓
Next Task


PROMISE
================================

Promise reactions
are microtasks.


setTimeout
================================

Timer callback runs
as task work.


queueMicrotask
================================

Directly schedules
a microtask.


async/await
================================

Uses Promise-related
asynchronous continuation.


IMPORTANT
================================

Sync
↓
Microtasks
↓
Next Task


AFTER EVERY TASK
================================

Microtask checkpoint.


MICROTASKS
================================

Drain until empty.


NESTED MICROTASK
================================

Added to queue
and processed before
moving to next task.


MICROTASK STARVATION
================================

Continuous microtasks can
delay tasks and rendering.


setTimeout(0)
================================

Not immediate.


TIMER DELAY
================================

Not exact execution time.


RENDERING
================================

Can occur at browser
rendering opportunities.

Long tasks can delay it.


EVENT LOOP
================================

Schedules/co-ordinates work.

Does not itself execute
JavaScript.


JAVASCRIPT ENGINE
================================

Executes JavaScript.


BROWSER ≠ NODE
================================

Different event-loop
architectures/details.
```

---

# 101. Important Rules to Remember

```text
1. JavaScript executes current synchronous work first.

2. JavaScript tasks follow run-to-completion.

3. Async callbacks do not interrupt currently executing JavaScript.

4. Timers are handled by the runtime.

5. Timer callbacks execute as task work.

6. setTimeout(0) is not synchronous.

7. Timer delays do not guarantee exact execution times.

8. Promise reactions are microtasks.

9. queueMicrotask() schedules a microtask.

10. async/await continuation uses promise/microtask scheduling.

11. Code before an await initially executes synchronously.

12. Code after await continues asynchronously.

13. After current task execution, microtasks are processed.

14. Microtasks are drained before moving to another task.

15. Microtasks generally follow queue order.

16. Microtasks can schedule additional microtasks.

17. Newly scheduled microtasks are processed in the same checkpoint.

18. Continuous microtasks can cause starvation.

19. Tasks can create microtasks.

20. Microtasks can create tasks.

21. A microtask checkpoint occurs after task execution.

22. Promise chains can create microtasks incrementally.

23. Later .then() handlers may not be queued until earlier promises settle.

24. Multiple task queues/task sources can exist.

25. One callback queue is only a simplified model.

26. "Macrotask" is common teaching terminology.

27. Browser standards generally use "task."

28. Event handlers are scheduled through host event-loop mechanisms.

29. Event handlers can schedule microtasks.

30. Microtasks created by a task run before the next task.

31. Fetch promise reactions are microtasks.

32. Network completion timing determines when fetch promises settle.

33. An unresolved fetch does not automatically beat a timer.

34. Already-resolved promise reactions are predictably microtasks.

35. Browser rendering does not happen after every JavaScript statement.

36. Long tasks can delay rendering.

37. Long tasks can delay user input.

38. Long tasks can delay timers.

39. Splitting work can improve responsiveness.

40. requestAnimationFrame integrates animation work with browser rendering.

41. setInterval timing is not exact.

42. Slow callbacks can affect interval timing.

43. The event loop coordinates execution.

44. The JavaScript engine executes JavaScript.

45. "Event loop pushes callbacks to stack" is an oversimplification.

46. Browser and Node.js event loops differ.

47. Node.js uses libuv.

48. Node.js has event-loop phases.

49. process.nextTick() is Node-specific.

50. Event Loop knowledge is essential for Promises, async/await, APIs, and React.
```

---

# Event Loop in One Sentence

> The JavaScript Event Loop coordinates asynchronous execution by allowing the current task to run to completion, draining queued microtasks at microtask checkpoints, and then allowing the runtime to proceed to subsequent tasks and browser work such as rendering.

---

# Final Mental Model

```text
             JAVASCRIPT RUNTIME
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
 JavaScript Engine             Host APIs
        │                         │
        ▼                         ├── Timers
    Call Stack                   ├── Network
        │                         ├── Events
        │                         └── Other APIs
        │
        │                    Async completion
        │                         │
        │               ┌─────────┴─────────┐
        │               │                   │
        │               ▼                   ▼
        │          Microtasks             Tasks
        │               │                   │
        └───────────────┴─────────┬─────────┘
                                  │
                                  ▼
                             EVENT LOOP
                                  │
                                  ▼
                         JavaScript executes
```

For most output questions, remember this:

```text
┌─────────────────────────────┐
│        CURRENT TASK         │
│                             │
│   Synchronous JavaScript    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│     MICROTASK CHECKPOINT    │
│                             │
│ Promise.then()              │
│ Promise.catch()             │
│ Promise.finally()           │
│ queueMicrotask()            │
│ async/await continuation    │
│                             │
│ Drain until empty           │
└──────────────┬──────────────┘
               │
               ▼
        Possible Rendering
               │
               ▼
┌─────────────────────────────┐
│          NEXT TASK          │
│                             │
│ Timer / Event / Message     │
└──────────────┬──────────────┘
               │
               ▼
       Microtasks again
               │
               ▼
              ...
```
