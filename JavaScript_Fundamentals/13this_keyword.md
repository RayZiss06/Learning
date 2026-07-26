#  `this` Keyword

The **`this` keyword** in JavaScript refers to a value associated with the **current function execution**.

The most important rule is:

> For normal functions, `this` is generally determined by **how the function is called**, not where the function was written.

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

Here:

```text
this
↓
user
```

because `greet()` was called as:

```javascript
user.greet();
```

---

# 1. Why Do We Need `this`?

Suppose we have:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(user.name);
    }
};
```

This works:

```javascript
user.greet();
```

But the method directly depends on the variable:

```javascript
user
```

A better approach:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

Now the method refers to the object used to invoke it.

```text
user.greet()
     │
     ▼
this → user
```

This makes methods reusable.

---

# 2. `this` Is Not Determined by Where a Function Is Written

Consider:

```javascript
function greet() {
    console.log(this.name);
}
```

The function itself does not permanently know what `this` should be.

We can use the same function with different objects:

```javascript
const user1 = {
    name: "John",
    greet
};

const user2 = {
    name: "Alice",
    greet
};
```

Now:

```javascript
user1.greet();
```

Output:

```text
John
```

And:

```javascript
user2.greet();
```

Output:

```text
Alice
```

Same function:

```javascript
greet
```

Different `this`:

```text
user1.greet()
→ this = user1

user2.greet()
→ this = user2
```

---

# 3. The Most Important Mental Model

For normal functions, look at the **call site**.

Example:

```javascript
object.method();
```

Think:

```text
Who is calling the function?

object.method()
^^^^^^

this → object
```

Another example:

```javascript
user.profile.show();
```

The function is called by:

```javascript
user.profile
```

Therefore:

```text
this → user.profile
```

Not necessarily:

```text
this → user
```

---

# 4. `this` in Object Methods

Example:

```javascript
const user = {
    name: "John",
    age: 25,

    introduce() {
        console.log(
            `I am ${this.name} and I am ${this.age}`
        );
    }
};

user.introduce();
```

Output:

```text
I am John and I am 25
```

Inside:

```javascript
this.name
this.age
```

means:

```javascript
user.name
user.age
```

for this particular call.

---

# 5. Method Invocation

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this);
    }
};

user.greet();
```

The call:

```javascript
user.greet();
```

determines:

```text
this = user
```

Conceptually:

```text
user
│
├── name: "John"
│
└── greet()
      │
      ▼
     this
      │
      └────► user
```

---

# 6. Reusing Methods Between Objects

Because `this` depends on the call, a function can be reused.

```javascript
function greet() {
    console.log(
        `Hello ${this.name}`
    );
}

const user1 = {
    name: "John",
    greet
};

const user2 = {
    name: "Alice",
    greet
};
```

Now:

```javascript
user1.greet();
user2.greet();
```

Output:

```text
Hello John
Hello Alice
```

This is one major reason `this` exists.

---

# 7. `this` in a Regular Function

Consider:

```javascript
function showThis() {
    console.log(this);
}

showThis();
```

The result depends on the environment and mode.

In a classic browser script running in **non-strict mode**, a plain function call generally has:

```text
this → global object
```

In browsers, the global object is commonly:

```javascript
window
```

However, in **strict mode**:

```javascript
"use strict";

function showThis() {
    console.log(this);
}

showThis();
```

Output:

```text
undefined
```

Modern JavaScript code often runs in strict contexts, including ES modules.

So do not rely on a plain function call giving you the global object.

---

# 8. Strict Mode

Strict mode changes several JavaScript behaviors.

For `this`, the important difference is:

```javascript
function test() {
    console.log(this);
}

test();
```

Classic non-strict browser script:

```text
this → window
```

Strict mode:

```javascript
"use strict";

function test() {
    console.log(this);
}

test();
```

Output:

```text
undefined
```

---

# 9. Top-Level `this`

Top-level `this` depends on the JavaScript environment.

For example, in a classic browser script:

```javascript
console.log(this);
```

typically:

```text
this → window
```

But in an ES module:

```javascript
console.log(this);
```

top-level `this` is:

```text
undefined
```

Node.js environments can also behave differently depending on whether the code is CommonJS, an ES module, or running in another context.

Therefore:

> Do not assume top-level `this` always means the global object.

---

# 10. Global Object

Modern JavaScript provides:

```javascript
globalThis
```

which refers to the global object across environments.

Example:

```javascript
console.log(globalThis);
```

This is preferable when you specifically need the global object.

Do not use:

```javascript
this
```

as a universal replacement for:

```javascript
globalThis
```

because their values can differ.

---

# 11. Losing `this`

Consider:

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

Now:

```javascript
const greet = user.greet;

greet();
```

The method has been detached from the object.

Originally:

```javascript
user.greet();
```

Call site:

```text
user.greet()
^^^^

this → user
```

After:

```javascript
greet();
```

there is no object before the function call.

In strict mode:

```text
this → undefined
```

So:

```javascript
this.name
```

fails.

---

# 12. Functions Do Not Permanently Belong to Objects

This is important.

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

It may look like:

```text
greet permanently belongs to user
```

But JavaScript functions are values.

We can do:

```javascript
const greet = user.greet;
```

Now:

```javascript
greet();
```

has a different call site.

Therefore:

> The object storing a function does not permanently determine the function's `this`.

---

# 13. Nested Objects

Consider:

```javascript
const user = {
    name: "John",

    profile: {
        name: "Profile",

        showName() {
            console.log(this.name);
        }
    }
};

user.profile.showName();
```

Output:

```text
Profile
```

Why?

Call:

```javascript
user.profile.showName();
```

The object immediately before the method call is:

```javascript
user.profile
```

Therefore:

```text
this → user.profile
```

Not:

```text
this → user
```

---

# 14. Nested Functions and `this`

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        function inner() {
            console.log(this.name);
        }

        inner();
    }
};

user.greet();
```

The outer method has:

```text
this → user
```

But:

```javascript
inner();
```

is a plain function call.

So the inner function does **not automatically inherit** the outer function's `this`.

In strict mode:

```text
inner() → this = undefined
```

This is a common source of confusion.

---

# 15. Old Solution: Store `this`

Before arrow functions became common, developers often wrote:

```javascript
const user = {
    name: "John",

    greet() {
        const self = this;

        function inner() {
            console.log(self.name);
        }

        inner();
    }
};

user.greet();
```

Output:

```text
John
```

You may also see:

```javascript
const that = this;
```

in older code.

Modern JavaScript commonly uses arrow functions instead.

---

# 16. Arrow Functions and `this`

Arrow functions behave differently.

> Arrow functions do **not** create their own `this`.

Instead, they use `this` from the surrounding lexical context.

Example:

```javascript
const user = {
    name: "John",

    greet() {
        const inner = () => {
            console.log(this.name);
        };

        inner();
    }
};

user.greet();
```

Output:

```text
John
```

Why?

```text
user.greet()
    │
    ▼
this = user

inner arrow function
    │
    ▼
uses surrounding this

this = user
```

---

# 17. Lexical `this`

Arrow functions use **lexical `this`**.

That means:

```text
Arrow function
↓
does not determine this from its own call
↓
uses this from surrounding scope
```

Example:

```javascript
const user = {
    name: "John",

    greet() {
        const arrow = () => {
            console.log(this.name);
        };

        arrow();
    }
};

user.greet();
```

The arrow function was created while:

```text
this → user
```

So it uses that same `this`.

---

# 18. Arrow Function as Object Method

This is usually a mistake:

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};

user.greet();
```

Many beginners expect:

```text
John
```

But the arrow function does not receive:

```text
this → user
```

because arrow functions do not get `this` from the call:

```javascript
user.greet();
```

Instead, they capture `this` from the surrounding scope where the arrow was created.

Therefore:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

is usually the correct pattern when a method needs dynamic `this`.

---

# 19. Regular Method vs Arrow Method

Regular method:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Here:

```text
this → user
```

Arrow property:

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

Here:

```text
this → surrounding lexical this
```

not automatically:

```text
user
```

---

# 20. When Arrow Functions Help

Arrow functions are useful inside methods.

Example:

```javascript
const user = {
    name: "John",

    hobbies: [
        "Gaming",
        "Coding"
    ],

    showHobbies() {
        this.hobbies.forEach(
            (hobby) => {
                console.log(
                    `${this.name}: ${hobby}`
                );
            }
        );
    }
};

user.showHobbies();
```

Output:

```text
John: Gaming
John: Coding
```

The callback arrow function uses `this` from:

```javascript
showHobbies()
```

where:

```text
this → user
```

---

# 21. `this` in Callbacks

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

setTimeout(
    user.greet,
    1000
);
```

We passed:

```javascript
user.greet
```

as a function value.

We did **not** call:

```javascript
user.greet()
```

The callback will later be invoked by the timer mechanism, so the original method call context is lost.

Therefore, you should not expect:

```text
this → user
```

inside the callback.

---

# 22. Fixing Callback `this` with an Arrow Function

One solution:

```javascript
setTimeout(
    () => {
        user.greet();
    },
    1000
);
```

Now the callback eventually executes:

```javascript
user.greet();
```

So:

```text
this → user
```

---

# 23. Fixing Callback `this` with `bind()`

Another solution:

```javascript
setTimeout(
    user.greet.bind(user),
    1000
);
```

`bind()` creates a new function whose `this` is fixed to:

```javascript
user
```

---

# 24. `call()`

The `call()` method lets us explicitly choose `this`.

Example:

```javascript
function greet() {
    console.log(
        `Hello ${this.name}`
    );
}

const user = {
    name: "John"
};

greet.call(user);
```

Output:

```text
Hello John
```

Here:

```javascript
greet.call(user)
           │
           ▼
        this = user
```

---

# 25. `call()` with Arguments

Example:

```javascript
function introduce(
    city,
    country
) {
    console.log(
        `${this.name} lives in ${city}, ${country}`
    );
}

const user = {
    name: "John"
};

introduce.call(
    user,
    "Kolkata",
    "India"
);
```

Output:

```text
John lives in Kolkata, India
```

Syntax:

```javascript
functionName.call(
    thisValue,
    arg1,
    arg2,
    arg3
);
```

---

# 26. `apply()`

`apply()` is similar to `call()`.

Difference:

```text
call()
→ arguments passed individually

apply()
→ arguments passed as an array-like collection
```

Example:

```javascript
function introduce(
    city,
    country
) {
    console.log(
        `${this.name} lives in ${city}, ${country}`
    );
}

const user = {
    name: "John"
};

introduce.apply(
    user,
    ["Kolkata", "India"]
);
```

Output:

```text
John lives in Kolkata, India
```

---

# 27. `call()` vs `apply()`

`call()`:

```javascript
introduce.call(
    user,
    "Kolkata",
    "India"
);
```

`apply()`:

```javascript
introduce.apply(
    user,
    ["Kolkata", "India"]
);
```

Think:

```text
call
→ comma-separated arguments

apply
→ array-like arguments
```

---

# 28. `bind()`

`bind()` also lets us specify `this`.

But unlike:

```text
call()
apply()
```

`bind()` does **not immediately execute the function**.

It creates a new function.

Example:

```javascript
function greet() {
    console.log(
        `Hello ${this.name}`
    );
}

const user = {
    name: "John"
};

const boundGreet =
    greet.bind(user);

boundGreet();
```

Output:

```text
Hello John
```

---

# 29. `call()` vs `bind()`

`call()`:

```javascript
greet.call(user);
```

means:

```text
Set this to user
+
Execute now
```

`bind()`:

```javascript
const newFunction =
    greet.bind(user);
```

means:

```text
Create new function
+
this fixed to user
+
Execute later
```

---

# 30. `apply()` vs `bind()`

`apply()`:

```javascript
greet.apply(user, []);
```

Executes immediately.

`bind()`:

```javascript
const bound =
    greet.bind(user);

bound();
```

Creates a new function first.

---

# 31. `call()`, `apply()`, `bind()` Cheat Sheet

```text
call()
│
├── sets this
├── arguments individually
└── executes immediately


apply()
│
├── sets this
├── arguments as array-like value
└── executes immediately


bind()
│
├── sets this
├── can preset arguments
└── returns new function
```

---

# 32. Borrowing Methods with `call()`

Example:

```javascript
const user1 = {
    name: "John",

    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
};

const user2 = {
    name: "Alice"
};
```

We can do:

```javascript
user1.greet.call(user2);
```

Output:

```text
Hello Alice
```

The function came from:

```javascript
user1.greet
```

but:

```text
this → user2
```

because of:

```javascript
.call(user2)
```

---

# 33. Explicit Binding

When we use:

```javascript
call()
apply()
bind()
```

we are explicitly specifying `this`.

Example:

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John"
};

greet.call(user);
```

This is called:

```text
Explicit Binding
```

because we explicitly say:

```text
this = user
```

---

# 34. Implicit Binding

When a function is called as:

```javascript
user.greet();
```

the object before the method determines `this`.

This is called:

```text
Implicit Binding
```

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

Here:

```text
this → user
```

---

# 35. Default Binding

A plain function call:

```javascript
greet();
```

uses default binding rules.

Example:

```javascript
function greet() {
    console.log(this);
}

greet();
```

In strict mode:

```text
this → undefined
```

In a classic non-strict browser script, it can become the global object.

---

# 36. Constructor Binding

When a function is called using:

```javascript
new
```

`this` refers to the newly created object.

Example:

```javascript
function User(name) {
    this.name = name;
}

const user =
    new User("John");
```

During:

```javascript
new User("John");
```

conceptually:

```text
Create object
     │
     ▼
this → new object
     │
     ▼
this.name = "John"
```

Result:

```javascript
{
    name: "John"
}
```

This is called:

```text
Constructor Binding
```

or:

```text
new Binding
```

---

# 37. `this` in Constructor Functions

Example:

```javascript
function User(
    name,
    age
) {
    this.name = name;
    this.age = age;
}

const user1 =
    new User(
        "John",
        25
    );

const user2 =
    new User(
        "Alice",
        30
    );
```

For:

```javascript
new User("John", 25);
```

```text
this → user1
```

For:

```javascript
new User("Alice", 30);
```

```text
this → user2
```

Each `new` call creates a new `this`.

---

# 38. `this` in Classes

Classes use the same fundamental idea.

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
}
```

Create:

```javascript
const user =
    new User("John");
```

During construction:

```text
this → newly created user
```

When:

```javascript
user.greet();
```

inside `greet()`:

```text
this → user
```

---

# 39. Class Constructor `this`

Example:

```javascript
class User {
    constructor(
        name,
        age
    ) {
        this.name = name;
        this.age = age;
    }
}
```

When:

```javascript
const user =
    new User(
        "John",
        25
    );
```

conceptually:

```text
new object created

       ↓

this → new object

       ↓

this.name = "John"
this.age = 25

       ↓

user
```

---

# 40. Losing `this` in Classes

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(this.name);
    }
}

const user =
    new User("John");

user.greet();
```

Output:

```text
John
```

But:

```javascript
const greet =
    user.greet;

greet();
```

The method loses its receiver.

Class bodies run in strict mode, so:

```text
this → undefined
```

and accessing:

```javascript
this.name
```

throws an error.

---

# 41. Binding Class Methods

We can bind the method:

```javascript
const greet =
    user.greet.bind(user);

greet();
```

Output:

```text
John
```

Now:

```text
this permanently bound
↓
user
```

for that bound function.

---

# 42. Binding in a Constructor

A pattern commonly seen in older React class components:

```javascript
class User {
    constructor(name) {
        this.name = name;

        this.greet =
            this.greet.bind(this);
    }

    greet() {
        console.log(this.name);
    }
}
```

Now:

```javascript
const user =
    new User("John");

const greet =
    user.greet;

greet();
```

Output:

```text
John
```

because the method was bound during construction.

---

# 43. Arrow Function Class Fields

Another approach:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet = () => {
        console.log(this.name);
    };
}
```

Now:

```javascript
const user =
    new User("John");

const greet =
    user.greet;

greet();
```

Output:

```text
John
```

The arrow function captures the instance's `this`.

---

# 44. Prototype Method vs Arrow Field

Prototype method:

```javascript
class User {
    greet() {
        console.log(this.name);
    }
}
```

Stored on:

```javascript
User.prototype
```

and shared between instances.

Arrow field:

```javascript
class User {
    greet = () => {
        console.log(this.name);
    };
}
```

creates a function for each instance.

Conceptually:

```text
Prototype Method

user1 ──┐
        ▼
User.prototype.greet()
        ▲
user2 ──┘
```

Versus:

```text
Arrow Field

user1
└── greet → Function A

user2
└── greet → Function B
```

---

# 45. `this` in Event Handlers

In browser DOM code:

```javascript
button.addEventListener(
    "click",
    function () {
        console.log(this);
    }
);
```

For a normal function listener registered this way, the browser calls the listener with:

```text
this → element receiving the listener
```

So if:

```javascript
button
```

has the listener:

```text
this → button
```

---

# 46. Arrow Functions in Event Handlers

Example:

```javascript
button.addEventListener(
    "click",
    () => {
        console.log(this);
    }
);
```

The arrow function does not receive:

```text
this → button
```

It uses lexical `this` from its surrounding scope.

If you need the element, a clearer modern approach is often:

```javascript
button.addEventListener(
    "click",
    (event) => {
        console.log(
            event.currentTarget
        );
    }
);
```

---

# 47. `target` vs `currentTarget`

In DOM events:

```javascript
event.target
```

means:

```text
the element where the event originated
```

while:

```javascript
event.currentTarget
```

means:

```text
the element whose listener is currently running
```

Example:

```javascript
button.addEventListener(
    "click",
    (event) => {
        console.log(
            event.currentTarget
        );
    }
);
```

This often avoids depending on `this`.

---

# 48. `this` Inside `forEach()`

Consider:

```javascript
const user = {
    name: "John",
    hobbies: [
        "Gaming",
        "Coding"
    ],

    showHobbies() {
        this.hobbies.forEach(
            function (hobby) {
                console.log(
                    this.name,
                    hobby
                );
            }
        );
    }
};
```

The callback is a regular function.

It does not automatically inherit:

```text
this → user
```

from `showHobbies()`.

This is why arrow functions are commonly used:

```javascript
showHobbies() {
    this.hobbies.forEach(
        (hobby) => {
            console.log(
                this.name,
                hobby
            );
        }
    );
}
```

Now the arrow captures:

```text
this → user
```

from `showHobbies()`.

---

# 49. Arrow Functions Do Not Have Their Own `this`

This rule should be memorized:

```text
Regular Function
→ this depends on invocation

Arrow Function
→ no own this
→ uses surrounding lexical this
```

Example:

```javascript
const arrow = () => {
    console.log(this);
};
```

Calling:

```javascript
arrow.call(user);
```

does **not** make the arrow's `this` become `user`.

---

# 50. `call()` Does Not Change Arrow Function `this`

Example:

```javascript
const user = {
    name: "John"
};

const greet = () => {
    console.log(this.name);
};

greet.call(user);
```

`call()` cannot override the lexical `this` of an arrow function.

The arrow continues using the `this` from the scope where it was created.

---

# 51. `apply()` Does Not Change Arrow Function `this`

Likewise:

```javascript
const arrow = () => {
    console.log(this);
};

arrow.apply({
    name: "John"
});
```

The object passed to `apply()` does not become the arrow's `this`.

---

# 52. `bind()` Does Not Change Arrow Function `this`

Example:

```javascript
const arrow = () => {
    console.log(this);
};

const bound =
    arrow.bind({
        name: "John"
    });

bound();
```

`bind()` cannot replace the arrow's lexical `this`.

---

# 53. Arrow Functions Cannot Be Constructors

Regular function:

```javascript
function User(name) {
    this.name = name;
}

const user =
    new User("John");
```

Works.

Arrow function:

```javascript
const User = (name) => {
    this.name = name;
};
```

Then:

```javascript
new User("John");
```

throws an error.

Arrow functions cannot be used with:

```javascript
new
```

because they do not have constructor behavior.

---

# 54. Regular Function vs Arrow Function

Regular function:

```javascript
function greet() {
    console.log(this);
}
```

`this` depends on invocation.

Arrow:

```javascript
const greet = () => {
    console.log(this);
};
```

`this` comes from surrounding lexical scope.

Comparison:

```text
Regular Function
├── own dynamic this
├── can use call()
├── can use apply()
├── can use bind()
└── normal functions can potentially be constructors


Arrow Function
├── no own this
├── lexical this
├── call() cannot replace its this
├── apply() cannot replace its this
├── bind() cannot replace its this
└── cannot be constructor
```

---

# 55. Function Declarations vs Function Expressions

Both regular function declarations and regular function expressions use dynamic `this`.

Declaration:

```javascript
function greet() {
    console.log(this.name);
}
```

Expression:

```javascript
const greet =
    function () {
        console.log(this.name);
    };
```

Both can receive `this` from:

```text
method invocation
call()
apply()
bind()
new
```

The special behavior belongs to **arrow functions**, not function expressions generally.

---

# 56. `this` and Closures

Closures and `this` are different concepts.

A closure remembers lexical variables:

```javascript
function outer() {
    const name = "John";

    return function () {
        console.log(name);
    };
}
```

The inner function remembers:

```text
name
```

But regular-function `this` is generally determined when the returned function is called.

Example:

```javascript
function createGreeting() {
    const greeting = "Hello";

    return function () {
        console.log(
            `${greeting} ${this.name}`
        );
    };
}
```

Here:

```text
greeting
→ closure

this
→ call-site binding
```

Do not confuse them.

---

# 57. Arrow Functions Combine Closures with Lexical `this`

Example:

```javascript
function outer() {
    const message = "Hello";

    const arrow = () => {
        console.log(
            message,
            this.name
        );
    };

    return arrow;
}
```

The arrow can capture:

```text
message
→ lexical variable

this
→ lexical this
```

Both come from the surrounding execution context.

---

# 58. `this` Does Not Mean "Current Function"

A common misconception:

```text
this = current function
```

Wrong.

Example:

```javascript
function test() {
    console.log(
        this === test
    );
}

test();
```

`this` does not automatically refer to:

```javascript
test
```

---

# 59. `this` Does Not Mean "Where Function Was Defined"

Another misconception:

```text
this = object where function was written
```

Not necessarily.

Example:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

const admin = {
    name: "Alice"
};

admin.greet =
    user.greet;

admin.greet();
```

Output:

```text
Alice
```

Even though the function originally appeared inside:

```javascript
user
```

the call is:

```javascript
admin.greet();
```

Therefore:

```text
this → admin
```

---

# 60. `this` Is About Invocation

This is the central rule:

```text
Where was the function defined?
        │
        └── Usually not enough

How was the function called?
        │
        └── Determines this
            for regular functions
```

Except:

```text
Arrow functions
→ lexical this
```

---

# 61. Binding Rules

The main `this` binding rules are:

```text
1. Default Binding

2. Implicit Binding

3. Explicit Binding

4. Constructor / new Binding

5. Lexical Binding
   → arrow functions
```

Understanding these rules removes most `this` confusion.

---

# 62. Default Binding

Example:

```javascript
function greet() {
    console.log(this);
}

greet();
```

No object.

No:

```javascript
call()
apply()
bind()
new
```

So:

```text
Default Binding
```

In strict mode:

```text
this → undefined
```

---

# 63. Implicit Binding

Example:

```javascript
const user = {
    greet() {
        console.log(this);
    }
};

user.greet();
```

Because the call is:

```javascript
user.greet();
```

```text
this → user
```

This is:

```text
Implicit Binding
```

---

# 64. Explicit Binding

Example:

```javascript
function greet() {
    console.log(this);
}

const user = {};

greet.call(user);
```

We explicitly specify:

```text
this → user
```

using:

```text
call()
```

This is:

```text
Explicit Binding
```

---

# 65. `new` Binding

Example:

```javascript
function User() {
    console.log(this);
}

new User();
```

`new` creates an object and binds:

```text
this → new object
```

This is:

```text
new Binding
```

---

# 66. Lexical Binding

Example:

```javascript
const arrow = () => {
    console.log(this);
};
```

The arrow uses:

```text
this from surrounding lexical scope
```

This is:

```text
Lexical Binding
```

---

# 67. Binding Precedence

When multiple rules could appear relevant, there is a useful precedence model.

For normal functions:

```text
new binding
   ↓
explicit binding
   ↓
implicit binding
   ↓
default binding
```

Arrow functions are separate because their `this` is lexical.

---

# 68. Implicit vs Explicit Binding

Example:

```javascript
function greet() {
    console.log(this.name);
}

const user1 = {
    name: "John",
    greet
};

const user2 = {
    name: "Alice"
};
```

Normally:

```javascript
user1.greet();
```

Output:

```text
John
```

But:

```javascript
user1.greet.call(user2);
```

Output:

```text
Alice
```

Explicit binding overrides the ordinary implicit receiver for that call.

---

# 69. `bind()` Creates a Bound Function

Example:

```javascript
function greet() {
    console.log(this.name);
}

const user1 = {
    name: "John"
};

const user2 = {
    name: "Alice"
};

const bound =
    greet.bind(user1);
```

Now:

```javascript
bound();
```

Output:

```text
John
```

And:

```javascript
bound.call(user2);
```

still outputs:

```text
John
```

The bound function's ordinary call-time `this` cannot be replaced by another `call()`.

---

# 70. Partial Application with `bind()`

`bind()` can also preset arguments.

Example:

```javascript
function greet(
    greeting,
    punctuation
) {
    console.log(
        `${greeting} ${this.name}${punctuation}`
    );
}

const user = {
    name: "John"
};

const sayHello =
    greet.bind(
        user,
        "Hello"
    );

sayHello("!");
```

Output:

```text
Hello John!
```

`bind()` fixed:

```text
this → user

greeting → "Hello"
```

Then later:

```text
punctuation → "!"
```

---

# 71. Method Extraction Problem

Consider:

```javascript
const calculator = {
    value: 10,

    double() {
        return this.value * 2;
    }
};

console.log(
    calculator.double()
);
```

Output:

```text
20
```

Now:

```javascript
const double =
    calculator.double;

double();
```

The connection to:

```javascript
calculator
```

is gone.

This is known as:

```text
Method Extraction
```

or:

```text
Lost Context
```

---

# 72. Fixing Method Extraction

Option 1:

```javascript
const double =
    calculator.double.bind(
        calculator
    );
```

Option 2:

```javascript
const double = () =>
    calculator.double();
```

Both preserve the intended object relationship in different ways.

---

# 73. Passing Methods as Callbacks

This commonly causes bugs:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(this.name);
    }
}

const user =
    new User("John");

setTimeout(
    user.greet,
    1000
);
```

The method is passed as a value:

```javascript
user.greet
```

not invoked as:

```javascript
user.greet()
```

So the original receiver is lost.

Fix:

```javascript
setTimeout(
    user.greet.bind(user),
    1000
);
```

or:

```javascript
setTimeout(
    () => user.greet(),
    1000
);
```

---

# 74. `this` in Object Method Callbacks

Example:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(
            function () {
                console.log(
                    this.name
                );
            },
            1000
        );
    }
};
```

The callback does not automatically inherit the method's `this`.

Use:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(
            () => {
                console.log(
                    this.name
                );
            },
            1000
        );
    }
};
```

Now:

```text
this → user
```

inside the arrow callback.

---

# 75. `this` and Destructuring

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

Destructure:

```javascript
const { greet } = user;

greet();
```

This loses the receiver exactly like:

```javascript
const greet = user.greet;
```

The function is now called as:

```javascript
greet();
```

not:

```javascript
user.greet();
```

---

# 76. `this` with Getters

Example:

```javascript
const user = {
    firstName: "John",
    lastName: "Doe",

    get fullName() {
        return (
            `${this.firstName} ${this.lastName}`
        );
    }
};

console.log(
    user.fullName
);
```

Output:

```text
John Doe
```

Inside the getter:

```text
this → user
```

---

# 77. `this` with Setters

Example:

```javascript
const user = {
    firstName: "John",
    lastName: "Doe",

    set fullName(value) {
        const [
            firstName,
            lastName
        ] = value.split(" ");

        this.firstName =
            firstName;

        this.lastName =
            lastName;
    }
};

user.fullName =
    "Alice Smith";
```

Now:

```javascript
console.log(
    user.firstName
);
```

Output:

```text
Alice
```

---

# 78. `this` with Inheritance

Consider:

```javascript
class User {
    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
}

class Admin extends User {
    constructor(name) {
        super();

        this.name = name;
    }
}

const admin =
    new Admin("John");

admin.greet();
```

Output:

```text
Hello John
```

Even though:

```javascript
greet()
```

was defined in:

```javascript
User
```

the method is called on:

```javascript
admin
```

Therefore:

```text
this → admin
```

---

# 79. Inherited Methods Use the Calling Instance

Example:

```javascript
class Animal {
    speak() {
        console.log(
            this.sound
        );
    }
}

class Dog extends Animal {
    constructor() {
        super();

        this.sound = "Woof";
    }
}

class Cat extends Animal {
    constructor() {
        super();

        this.sound = "Meow";
    }
}
```

Now:

```javascript
const dog = new Dog();
const cat = new Cat();

dog.speak();
cat.speak();
```

Output:

```text
Woof
Meow
```

Same inherited method:

```javascript
speak()
```

Different:

```text
this
```

depending on the instance.

---

# 80. `super` and `this`

Example:

```javascript
class User {
    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
}

class Admin extends User {
    greet() {
        super.greet();
    }
}
```

When:

```javascript
admin.greet();
```

calls:

```javascript
super.greet();
```

the parent method executes with the same current receiver:

```text
this → admin
```

`super` determines where the method lookup starts; it does not turn `this` into the parent prototype.

---

# 81. `this` in Derived Constructors

Example:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}

class Admin extends User {
    constructor(
        name,
        role
    ) {
        super(name);

        this.role = role;
    }
}
```

In a derived constructor:

```javascript
super(...)
```

must run before using:

```javascript
this
```

Incorrect:

```javascript
constructor(name, role) {
    this.role = role;

    super(name);
}
```

Correct:

```javascript
constructor(name, role) {
    super(name);

    this.role = role;
}
```

---

# 82. Common Mistake: Arrow Object Methods

Avoid:

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

when you need:

```text
this → user
```

Use:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

---

# 83. Common Mistake: Detached Method

Problem:

```javascript
const greet =
    user.greet;

greet();
```

Fix:

```javascript
const greet =
    user.greet.bind(user);
```

or:

```javascript
const greet = () =>
    user.greet();
```

---

# 84. Common Mistake: Regular Nested Callback

Problem:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(
            function () {
                console.log(
                    this.name
                );
            },
            1000
        );
    }
};
```

Fix:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(
            () => {
                console.log(
                    this.name
                );
            },
            1000
        );
    }
};
```

---

# 85. Common Mistake: Assuming `this` Is Lexical Everywhere

This:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

does not mean the function permanently captures:

```javascript
user
```

If moved:

```javascript
const admin = {
    name: "Alice",
    greet: user.greet
};

admin.greet();
```

Output:

```text
Alice
```

because:

```text
this → admin
```

---

# 86. Common Mistake: Assuming `this` Means Parent Scope

Regular nested function:

```javascript
const user = {
    name: "John",

    outer() {
        function inner() {
            console.log(this.name);
        }

        inner();
    }
};
```

`inner()` does not automatically use the outer method's `this`.

Use an arrow when lexical `this` is desired:

```javascript
outer() {
    const inner = () => {
        console.log(this.name);
    };

    inner();
}
```

---

# 87. Common Mistake: Using `bind()` but Forgetting the Returned Function

Incorrect:

```javascript
user.greet.bind(user);

const greet =
    user.greet;

greet();
```

`bind()` does not modify the original function.

It returns a new function.

Correct:

```javascript
const greet =
    user.greet.bind(user);

greet();
```

---

# 88. `bind()` Does Not Execute Immediately

Example:

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John"
};

const bound =
    greet.bind(user);
```

Nothing has been printed yet.

Only:

```javascript
bound();
```

executes the function.

---

# 89. Interview Example 1

What is the output?

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

user.greet();
```

Answer:

```text
John
```

Because:

```text
this → user
```

---

# 90. Interview Example 2

What happens?

```javascript
"use strict";

const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};

const greet =
    user.greet;

greet();
```

Answer:

An error occurs because:

```text
this → undefined
```

and the function tries to read:

```javascript
this.name
```

---

# 91. Interview Example 3

What is the output?

```javascript
const user1 = {
    name: "John"
};

const user2 = {
    name: "Alice"
};

function greet() {
    console.log(this.name);
}

user1.greet = greet;
user2.greet = greet;

user1.greet();
user2.greet();
```

Answer:

```text
John
Alice
```

Same function.

Different invocation objects.

---

# 92. Interview Example 4

What is the output?

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};

user.greet();
```

Answer:

Not reliably:

```text
John
```

The arrow function does not use `user` as `this`.

It uses the surrounding lexical `this`.

---

# 93. Interview Example 5

What is the output?

```javascript
const user = {
    name: "John",

    greet() {
        const inner = () => {
            console.log(this.name);
        };

        inner();
    }
};

user.greet();
```

Answer:

```text
John
```

The arrow captures:

```text
this → user
```

from `greet()`.

---

# 94. Interview Example 6

What is the output?

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John"
};

greet.call(user);
```

Answer:

```text
John
```

---

# 95. Interview Example 7

What is the output?

```javascript
function greet(
    greeting
) {
    console.log(
        `${greeting} ${this.name}`
    );
}

const user = {
    name: "John"
};

greet.apply(
    user,
    ["Hello"]
);
```

Answer:

```text
Hello John
```

---

# 96. Interview Example 8

What is the output?

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John"
};

const bound =
    greet.bind(user);

bound();
```

Answer:

```text
John
```

---

# 97. Interview Example 9

What is the output?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(this.name);
    }
}

const user =
    new User("John");

user.greet();
```

Answer:

```text
John
```

---

# 98. Interview Example 10

What happens?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(this.name);
    }
}

const user =
    new User("John");

const greet =
    user.greet;

greet();
```

Answer:

An error occurs because class methods execute in strict mode and the detached call gives:

```text
this → undefined
```

---

# 99. Interview Example 11

What is the output?

```javascript
const user = {
    name: "John",

    profile: {
        name: "Alice",

        greet() {
            console.log(
                this.name
            );
        }
    }
};

user.profile.greet();
```

Answer:

```text
Alice
```

Because:

```text
this → user.profile
```

---

# 100. Interview Example 12

What is the output?

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John",
    greet
};

const admin = {
    name: "Alice"
};

user.greet.call(admin);
```

Answer:

```text
Alice
```

Explicit binding wins over the normal implicit receiver.

---

# 101. Interview Example 13

What is the output?

```javascript
class Animal {
    speak() {
        console.log(this.sound);
    }
}

class Dog extends Animal {
    constructor() {
        super();

        this.sound = "Woof";
    }
}

const dog =
    new Dog();

dog.speak();
```

Answer:

```text
Woof
```

The inherited method runs with:

```text
this → dog
```

---

# 102. Interview Example 14

What happens?

```javascript
const User = (
    name
) => {
    this.name = name;
};

const user =
    new User("John");
```

Answer:

An error occurs because arrow functions cannot be constructors.

---

# 103. Interview Example 15

What is the output?

```javascript
function greet() {
    return this.name;
}

const user = {
    name: "John"
};

const bound =
    greet.bind(user);

const another = {
    name: "Alice"
};

console.log(
    bound.call(another)
);
```

Answer:

```text
John
```

The bound function remains bound to:

```javascript
user
```

for ordinary calls, even when `call()` attempts to provide another `this`.

---

# 104. Interview Example 16

What is the output?

```javascript
const user = {
    name: "John",

    greet() {
        return () => {
            console.log(
                this.name
            );
        };
    }
};

const fn =
    user.greet();

fn();
```

Answer:

```text
John
```

When:

```javascript
user.greet()
```

runs:

```text
this → user
```

The returned arrow captures that `this`.

---

# 105. Interview Example 17

What is the output?

```javascript
function createFunction() {
    return function () {
        console.log(
            this.name
        );
    };
}

const user = {
    name: "John"
};

const greet =
    createFunction();

greet.call(user);
```

Answer:

```text
John
```

The returned regular function receives `this` from:

```javascript
call(user)
```

---

# 106. Interview Example 18

What is the output?

```javascript
const user = {
    name: "John",

    getName() {
        return this.name;
    }
};

const admin = {
    name: "Alice",
    getName:
        user.getName
};

console.log(
    admin.getName()
);
```

Answer:

```text
Alice
```

The function's original location does not determine `this`.

---

# 107. `this` in React Class Components

Older React class components heavily use:

```javascript
this
```

Example:

```jsx
class Counter
    extends React.Component {

    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };
    }

    render() {
        return (
            <button>
                {this.state.count}
            </button>
        );
    }
}
```

Here:

```text
this
→ component instance
```

---

# 108. `this.props`

In class components:

```jsx
class User
    extends React.Component {

    render() {
        return (
            <h1>
                {this.props.name}
            </h1>
        );
    }
}
```

Here:

```javascript
this.props
```

contains the component's props.

---

# 109. `this.state`

Class component state:

```jsx
class Counter
    extends React.Component {

    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };
    }
}
```

Access:

```javascript
this.state.count
```

---

# 110. `this.setState()`

Class components update state using:

```javascript
this.setState()
```

Example:

```jsx
this.setState({
    count: 10
});
```

When new state depends on previous state:

```jsx
this.setState(
    (previousState) => ({
        count:
            previousState.count + 1
    })
);
```

---

# 111. React Class Method Binding

Older class components often required binding event handlers.

Example:

```jsx
class Counter
    extends React.Component {

    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };

        this.increment =
            this.increment.bind(this);
    }

    increment() {
        this.setState(
            (state) => ({
                count:
                    state.count + 1
            })
        );
    }

    render() {
        return (
            <button
                onClick={
                    this.increment
                }
            >
                {
                    this.state.count
                }
            </button>
        );
    }
}
```

Why bind?

Because passing:

```javascript
this.increment
```

passes the method as a callback.

Without binding, it can lose the component instance as `this`.

---

# 112. Arrow Methods in React Classes

Another pattern:

```jsx
class Counter
    extends React.Component {

    state = {
        count: 0
    };

    increment = () => {
        this.setState(
            (state) => ({
                count:
                    state.count + 1
            })
        );
    };

    render() {
        return (
            <button
                onClick={
                    this.increment
                }
            >
                {
                    this.state.count
                }
            </button>
        );
    }
}
```

The arrow field captures the component instance's `this`.

---

# 113. Modern React Mostly Avoids `this`

Modern React primarily uses function components.

Example:

```jsx
function Counter() {
    const [
        count,
        setCount
    ] = useState(0);

    function increment() {
        setCount(
            (count) =>
                count + 1
        );
    }

    return (
        <button
            onClick={
                increment
            }
        >
            {count}
        </button>
    );
}
```

No:

```text
this.state
this.props
this.setState
method binding
```

Instead:

```text
props
variables
hooks
closures
```

---

# 114. Why `this` Still Matters for React Developers

Even if modern React uses function components, `this` remains important because JavaScript itself uses it extensively.

You will encounter:

```text
Classes
Legacy React
Libraries
SDKs
DOM APIs
Object methods
Constructor functions
Backend JavaScript
Node.js
Third-party packages
Existing enterprise applications
Interview questions
```

So:

```text
Modern React
→ less direct this usage

JavaScript
→ this remains fundamental
```

---

# 115. Complete Binding Mental Model

When you see:

```javascript
function something() {
    console.log(this);
}
```

ask:

```text
How is this function being called?
```

### Plain Call

```javascript
something();
```

```text
Default Binding
```

Strict mode:

```text
this → undefined
```

---

### Method Call

```javascript
object.something();
```

```text
Implicit Binding

this → object
```

---

### `call()`

```javascript
something.call(object);
```

```text
Explicit Binding

this → object
```

---

### `apply()`

```javascript
something.apply(
    object,
    []
);
```

```text
Explicit Binding

this → object
```

---

### `bind()`

```javascript
const fn =
    something.bind(object);

fn();
```

```text
Bound Function

this → object
```

---

### `new`

```javascript
new Something();
```

```text
Constructor Binding

this → newly created object
```

---

### Arrow Function

```javascript
const something = () => {
    console.log(this);
};
```

```text
Lexical Binding

this → surrounding this
```

---

# 116. Quick Decision Tree

When trying to determine `this`, think:

```text
Is it an arrow function?
│
├── YES
│     │
│     └── Use surrounding lexical this
│
└── NO
      │
      ▼
Was function called with new?
      │
      ├── YES
      │     └── this = new object
      │
      └── NO
            │
            ▼
Was call/apply/bind used?
            │
            ├── YES
            │     └── this = explicitly provided value
            │
            └── NO
                  │
                  ▼
Was it called as object.method()?
                  │
                  ├── YES
                  │     └── this = object
                  │
                  └── NO
                        │
                        ▼
                   Default Binding
```

For modern code, strict-mode behavior is the safest mental model for plain calls:

```text
this → undefined
```

---

# 117. `this` Cheat Sheet

```javascript
// ---------------------
// OBJECT METHOD
// ---------------------

const user = {
    name: "John",

    greet() {
        console.log(
            this.name
        );
    }
};

user.greet();

// this → user
```

```javascript
// ---------------------
// DETACHED METHOD
// ---------------------

const greet =
    user.greet;

greet();

// this → undefined
// in strict mode
```

```javascript
// ---------------------
// CALL
// ---------------------

greet.call(user);

// this → user
```

```javascript
// ---------------------
// APPLY
// ---------------------

greet.apply(
    user,
    []
);

// this → user
```

```javascript
// ---------------------
// BIND
// ---------------------

const bound =
    greet.bind(user);

bound();

// this → user
```

```javascript
// ---------------------
// CONSTRUCTOR
// ---------------------

function User(name) {
    this.name = name;
}

const john =
    new User("John");

// this → new instance
```

```javascript
// ---------------------
// ARROW
// ---------------------

const arrow = () => {
    console.log(this);
};

// this → surrounding
// lexical this
```

```javascript
// ---------------------
// CLASS
// ---------------------

class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(
            this.name
        );
    }
}

const john =
    new User("John");

john.greet();

// this → john
```

---

# 118. Important Rules to Remember

```text
1. `this` is a special JavaScript keyword.

2. For normal functions, `this` generally depends on how the function is called.

3. Where a normal function was defined does not permanently determine `this`.

4. object.method() usually gives `this = object`.

5. A method can lose `this` when detached from its object.

6. const fn = object.method can lose the original receiver.

7. Destructuring a method can also lose the receiver.

8. Plain function calls use default binding.

9. In strict mode, a plain function call has `this = undefined`.

10. Top-level `this` depends on the environment and module type.

11. `globalThis` is the standard cross-environment global-object reference.

12. Arrow functions do not have their own `this`.

13. Arrow functions use lexical `this`.

14. Arrow functions are useful inside methods when the callback needs the outer `this`.

15. Arrow functions are usually unsuitable as object methods when dynamic `this` is needed.

16. `call()` explicitly sets `this` and executes immediately.

17. `apply()` explicitly sets `this` and executes immediately.

18. `call()` passes arguments individually.

19. `apply()` passes arguments using an array-like value.

20. `bind()` returns a new bound function.

21. `bind()` does not execute the function immediately.

22. Bound functions keep their bound `this` for ordinary calls.

23. `new` creates a new object and establishes constructor `this`.

24. `this` inside a constructor refers to the new instance.

25. Class constructors use `this` for instance properties.

26. Class methods receive `this` based on the instance used to call them.

27. Class methods can lose `this` when detached.

28. Arrow class fields can preserve lexical `this`.

29. Normal class methods are generally shared through the prototype.

30. Arrow class fields create functions per instance.

31. Regular nested functions do not automatically inherit outer `this`.

32. Arrow nested functions can capture outer `this`.

33. `call()`, `apply()`, and `bind()` cannot replace an arrow function's lexical `this`.

34. Arrow functions cannot be constructors.

35. `this` is not the current function.

36. `this` is not automatically the object where a function was originally written.

37. Inherited methods use the instance that invoked them as `this`.

38. `super.method()` keeps the current receiver as `this`.

39. Derived class constructors must call `super()` before using `this`.

40. Modern React function components mostly avoid `this`.

41. Legacy React class components use `this` extensively.

42. Understanding `this` is essential for classes, callbacks, DOM code, and constructor functions.
```

---

# 119. Final Mental Model

The easiest way to remember `this` is:

```text
Regular Function
      │
      ▼
Look at HOW it is called
      │
      ├── fn()
      │      ↓
      │   default binding
      │
      ├── obj.fn()
      │      ↓
      │   this = obj
      │
      ├── fn.call(obj)
      │      ↓
      │   this = obj
      │
      ├── fn.apply(obj)
      │      ↓
      │   this = obj
      │
      ├── fn.bind(obj)
      │      ↓
      │   bound function
      │
      └── new Fn()
             ↓
          this = new instance
```

But:

```text
Arrow Function
      │
      ▼
Ignore its call site for this
      │
      ▼
Look at surrounding lexical this
```

So the two core rules are:

```text
NORMAL FUNCTION
→ this depends on invocation

ARROW FUNCTION
→ this comes from surrounding scope
```

---

# 120. Connection to Topics Already Covered

At this point, the topics connect like this:

```text
Variables
   │
   ▼
Data Types
   │
   ▼
Operators
   │
   ▼
Loops
   │
   ▼
Functions
   │
   ├── Scope
   │
   ├── Hoisting
   │
   ├── Closures
   │
   └── Callbacks
          │
          ▼
       Objects
          │
          ├── Methods
          │     │
          │     └── this
          │
          ▼
        Arrays
          │
          └── callback methods
                 │
                 └── arrow functions / this
          │
          ▼
        Classes
          │
          ├── constructor
          ├── methods
          ├── inheritance
          └── this
```

The next topics will explain **why classes and constructor functions actually work internally**.
