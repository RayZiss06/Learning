#  Constructor Functions

A **constructor function** is a regular JavaScript function designed to create and initialize multiple objects with the same structure.

Before the `class` syntax was introduced in ES6, constructor functions were one of the primary ways to implement object-oriented patterns in JavaScript.

Example:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
```

Result:

```javascript
console.log(user1);
console.log(user2);
```

Conceptually:

```text
user1
├── name: "John"
└── age: 25

user2
├── name: "Alice"
└── age: 30
```

The same constructor created two independent objects.

---

# 1. Why Constructor Functions?

Suppose we need several user objects.

Without a constructor:

```javascript
const user1 = {
    name: "John",
    age: 25
};

const user2 = {
    name: "Alice",
    age: 30
};

const user3 = {
    name: "Bob",
    age: 28
};
```

This works, but the object creation logic is repeated.

Instead:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
const user3 = new User("Bob", 28);
```

Now the object creation logic is reusable.

---

# 2. Constructor Functions Are Regular Functions

There is no special `constructor function` declaration syntax.

This:

```javascript
function User(name) {
    this.name = name;
}
```

is still a normal function.

What makes it act as a constructor is calling it with:

```javascript
new
```

Example:

```javascript
const user = new User("John");
```

So:

```text
Constructor function
=
regular function intended to be called with new
```

---

# 3. Constructor Naming Convention

Constructor functions conventionally start with a capital letter.

Example:

```javascript
function User() {
}
```

Instead of:

```javascript
function user() {
}
```

Other examples:

```javascript
function Person() {
}

function Product() {
}

function Employee() {
}

function Car() {
}
```

This is a convention, not a language requirement.

It communicates:

```text
This function should probably be called using new.
```

---

# 4. Basic Constructor Function

Example:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}
```

Create an object:

```javascript
const user = new User("John", 25);
```

Now:

```javascript
console.log(user.name);
```

Output:

```text
John
```

And:

```javascript
console.log(user.age);
```

Output:

```text
25
```

---

# 5. The `new` Keyword

The `new` keyword is what turns a constructor call into object creation.

Example:

```javascript
const user = new User("John", 25);
```

The important part is:

```javascript
new User(...)
```

Without `new`, JavaScript performs an ordinary function call instead.

---

# 6. What Does `new` Do?

Consider:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

Conceptually, `new` performs four major steps:

```text
1. Creates a new empty object.

2. Sets the new object's [[Prototype]]
   to User.prototype.

3. Calls User() with this pointing
   to the new object.

4. Returns the new object,
   unless the constructor explicitly
   returns another object.
```

This is one of the most important things to understand about constructors.

---

# 7. `new` Step 1 — Create an Object

When:

```javascript
new User("John");
```

JavaScript conceptually begins with:

```javascript
const object = {};
```

A new object is created.

Conceptually:

```text
object
{}
```

---

# 8. `new` Step 2 — Connect the Prototype

The new object's internal:

```text
[[Prototype]]
```

is connected to:

```javascript
User.prototype
```

Conceptually:

```text
object
│
│ [[Prototype]]
▼
User.prototype
```

This is why objects created by the same constructor can share prototype methods.

---

# 9. `new` Step 3 — Bind `this`

JavaScript then calls:

```javascript
User("John")
```

with:

```text
this → newly created object
```

So:

```javascript
function User(name) {
    this.name = name;
}
```

becomes conceptually:

```javascript
object.name = "John";
```

Result:

```text
object
└── name: "John"
```

---

# 10. `new` Step 4 — Return the Object

If the constructor does not explicitly return another object, JavaScript returns the newly created object.

So:

```javascript
const user = new User("John");
```

results in:

```javascript
user
```

referencing that newly created object.

---

# 11. Complete `new` Mental Model

Code:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

const user = new User("John", 25);
```

Conceptually:

```javascript
const user = {};
```

Then:

```text
user.[[Prototype]]
=
User.prototype
```

Then:

```javascript
User.call(user, "John", 25);
```

Which effectively performs:

```javascript
user.name = "John";
user.age = 25;
```

Then:

```javascript
return user;
```

Important:

This is a conceptual model, not the exact internal implementation.

---

# 12. Constructor `this`

Inside a constructor called with `new`:

```text
this
```

refers to the newly created object.

Example:

```javascript
function User(name, age) {
    console.log(this);

    this.name = name;
    this.age = age;
}

const user = new User("John", 25);
```

Conceptually:

```text
this
 ↓
new object
```

Then:

```javascript
this.name = name;
```

means:

```text
newObject.name = name
```

---

# 13. Creating Multiple Instances

Example:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
const user3 = new User("Bob", 28);
```

Each call creates a different object:

```text
user1
├── name: John
└── age: 25


user2
├── name: Alice
└── age: 30


user3
├── name: Bob
└── age: 28
```

---

# 14. Instances Are Separate Objects

Example:

```javascript
function User(name) {
    this.name = name;
}

const user1 = new User("John");
const user2 = new User("Alice");
```

Check:

```javascript
console.log(user1 === user2);
```

Output:

```text
false
```

Each `new` call creates a new object.

---

# 15. Instance Properties

Properties assigned using:

```javascript
this.property
```

inside the constructor become properties of each instance.

Example:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
    this.active = true;
}
```

Create:

```javascript
const user = new User("John", 25);
```

Result:

```javascript
console.log(user);
```

Conceptually:

```javascript
{
    name: "John",
    age: 25,
    active: true
}
```

These are **own properties** of `user`.

---

# 16. Checking Instance Properties

Example:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

Check:

```javascript
console.log(
    Object.hasOwn(user, "name")
);
```

Output:

```text
true
```

Because:

```text
name
```

exists directly on the instance.

---

# 17. Methods Inside Constructor Functions

You can create methods inside the constructor:

```javascript
function User(name) {
    this.name = name;

    this.greet = function () {
        console.log(`Hello ${this.name}`);
    };
}
```

Create:

```javascript
const user = new User("John");

user.greet();
```

Output:

```text
Hello John
```

This works, but there is an important problem when creating many instances.

---

# 18. Problem with Methods Inside Constructors

Consider:

```javascript
function User(name) {
    this.name = name;

    this.greet = function () {
        console.log(`Hello ${this.name}`);
    };
}

const user1 = new User("John");
const user2 = new User("Alice");
```

Each object receives its own function:

```text
user1
├── name: John
└── greet → Function A


user2
├── name: Alice
└── greet → Function B
```

Check:

```javascript
console.log(
    user1.greet === user2.greet
);
```

Output:

```text
false
```

Two separate function objects were created.

---

# 19. Shared Methods with `.prototype`

Instead of creating the method inside every instance:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};
```

Create:

```javascript
const user1 = new User("John");
const user2 = new User("Alice");
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

But both objects share the same method.

---

# 20. Constructor Prototype

Every normal constructable function has a property:

```javascript
prototype
```

Example:

```javascript
function User() {
}

console.log(User.prototype);
```

`User.prototype` is an object.

When:

```javascript
const user = new User();
```

JavaScript connects:

```text
user.[[Prototype]]
```

to:

```text
User.prototype
```

---

# 21. Constructor Prototype Relationship

Example:

```javascript
function User() {
}

const user = new User();
```

Check:

```javascript
console.log(
    Object.getPrototypeOf(user) === User.prototype
);
```

Output:

```text
true
```

Relationship:

```text
              User
               │
               │ .prototype
               ▼
        User.prototype
               ▲
               │
         [[Prototype]]
               │
              user
```

---

# 22. Shared Prototype Methods

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    return `Hello ${this.name}`;
};

const user1 = new User("John");
const user2 = new User("Alice");
```

Check:

```javascript
console.log(
    user1.greet === user2.greet
);
```

Output:

```text
true
```

Because both resolve:

```javascript
greet
```

from:

```javascript
User.prototype
```

---

# 23. Instance Properties vs Prototype Methods

Example:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};

User.prototype.login = function () {
    console.log(`${this.name} logged in`);
};
```

Create:

```javascript
const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
```

Conceptually:

```text
          User.prototype
          ├── greet()
          └── login()
              ▲
              │
        ┌─────┴─────┐
        │           │
      user1       user2
      ├ name      ├ name
      └ age       └ age
```

Think:

```text
Instance-specific data
→ instance

Shared behavior
→ prototype
```

---

# 24. How Prototype Method Lookup Works

Consider:

```javascript
user1.greet();
```

JavaScript checks:

```text
user1
│
├── greet?
│     NO
│
▼
User.prototype
│
├── greet?
│     YES
│
▼
Execute
```

Inside:

```javascript
User.prototype.greet = function () {
    console.log(this.name);
};
```

`this` is:

```text
user1
```

because the call was:

```javascript
user1.greet();
```

---

# 25. Complete Constructor Prototype Chain

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};

const user = new User("John");
```

Prototype chain:

```text
user
│
├── name: "John"
│
│ [[Prototype]]
▼
User.prototype
│
├── greet()
├── constructor
│
│ [[Prototype]]
▼
Object.prototype
│
├── toString()
├── valueOf()
├── hasOwnProperty()
├── ...
│
│ [[Prototype]]
▼
null
```

---

# 26. The `constructor` Property

By default:

```javascript
User.prototype
```

contains:

```javascript
constructor
```

which points back to:

```javascript
User
```

Example:

```javascript
function User() {
}

console.log(
    User.prototype.constructor === User
);
```

Output:

```text
true
```

Relationship:

```text
User
 │
 │ prototype
 ▼
User.prototype
 │
 │ constructor
 └──────────────► User
```

---

# 27. Instance `.constructor`

Because an instance inherits from:

```javascript
User.prototype
```

it can usually access:

```javascript
constructor
```

Example:

```javascript
function User() {
}

const user = new User();

console.log(
    user.constructor === User
);
```

Output:

```text
true
```

Lookup:

```text
user
│
├── constructor?
│     NO
│
▼
User.prototype
│
├── constructor?
│     YES → User
```

---

# 28. `instanceof`

The `instanceof` operator checks whether a constructor's prototype exists in an object's prototype chain.

Example:

```javascript
function User() {
}

const user = new User();
```

Check:

```javascript
console.log(
    user instanceof User
);
```

Output:

```text
true
```

Because:

```text
user
│
▼
User.prototype
```

---

# 29. `instanceof Object`

Example:

```javascript
function User() {
}

const user = new User();
```

Check:

```javascript
console.log(
    user instanceof Object
);
```

Output:

```text
true
```

Because the prototype chain is:

```text
user
↓
User.prototype
↓
Object.prototype
↓
null
```

Therefore both are true:

```javascript
user instanceof User;
user instanceof Object;
```

---

# 30. Constructor Return Behavior

Normally, you do not explicitly return anything from a constructor.

Example:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

JavaScript automatically returns the new instance.

But explicit returns behave differently depending on what is returned.

---

# 31. Returning a Primitive from Constructor

Example:

```javascript
function User(name) {
    this.name = name;

    return 100;
}

const user = new User("John");
```

The primitive return value is ignored.

Result:

```javascript
console.log(user.name);
```

Output:

```text
John
```

Similarly:

```javascript
return "Hello";
return true;
return null;
```

do not replace the constructed object.

---

# 32. Returning an Object from Constructor

If the constructor explicitly returns an object, that object replaces the newly created instance.

Example:

```javascript
function User(name) {
    this.name = name;

    return {
        role: "admin"
    };
}

const user = new User("John");
```

Now:

```javascript
console.log(user);
```

Output:

```javascript
{
    role: "admin"
}
```

The original object containing:

```javascript
name: "John"
```

is not returned.

---

# 33. Constructor Return Rule

Remember:

```text
No explicit return
→ new instance returned


Return primitive
→ primitive ignored
→ new instance returned


Return object
→ returned object replaces instance
```

This is an important interview concept.

---

# 34. Calling Constructor Without `new`

Consider:

```javascript
function User(name) {
    this.name = name;
}
```

Correct:

```javascript
const user = new User("John");
```

Incorrect:

```javascript
const user = User("John");
```

Without `new`, it becomes an ordinary function call.

---

# 35. Why Missing `new` Is Dangerous

Consider:

```javascript
function User(name) {
    this.name = name;
}

const user = User("John");
```

In strict mode:

```javascript
"use strict";
```

`this` inside a plain function call is:

```text
undefined
```

Therefore:

```javascript
this.name = name;
```

causes an error.

Without strict mode in classic script contexts, `this` may refer to the global object, which can accidentally create or modify global state.

Do not rely on that behavior.

Use:

```javascript
new User(...)
```

for constructor functions.

---

# 36. `new.target`

JavaScript provides:

```javascript
new.target
```

inside functions.

It tells you whether the function was called using `new`.

Example:

```javascript
function User(name) {
    console.log(new.target);

    this.name = name;
}
```

With:

```javascript
new User("John");
```

`new.target` refers to:

```javascript
User
```

Without:

```javascript
User("John");
```

`new.target` is:

```text
undefined
```

---

# 37. Enforcing `new` with `new.target`

Example:

```javascript
function User(name) {
    if (!new.target) {
        throw new Error(
            "User must be called with new"
        );
    }

    this.name = name;
}
```

Correct:

```javascript
const user = new User("John");
```

Incorrect:

```javascript
User("John");
```

throws an error.

---

# 38. Automatically Calling with `new`

You may encounter older patterns such as:

```javascript
function User(name) {
    if (!(this instanceof User)) {
        return new User(name);
    }

    this.name = name;
}
```

Then both:

```javascript
new User("John");
```

and:

```javascript
User("John");
```

create instances.

However, modern code generally prefers clearer APIs rather than silently correcting a missing `new`.

---

# 39. Constructor Functions vs Factory Functions

A **constructor function**:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

A **factory function**:

```javascript
function createUser(name) {
    return {
        name
    };
}

const user = createUser("John");
```

Both can create objects, but they work differently.

---

# 40. Factory Function

Example:

```javascript
function createUser(name, age) {
    return {
        name,
        age,

        greet() {
            console.log(`Hello ${name}`);
        }
    };
}
```

Create:

```javascript
const user = createUser("John", 25);
```

No `new` is required.

---

# 41. Constructor vs Factory Function

Constructor:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

Factory:

```javascript
function createUser(name) {
    return {
        name
    };
}

const user = createUser("John");
```

Comparison:

```text
Constructor Function
--------------------
Usually Capitalized
Uses new
Uses this
Uses Constructor.prototype naturally
Works with instanceof


Factory Function
----------------
Usually camelCase
No new required
Usually explicitly returns object
Can use closures naturally
Does not require constructor prototype semantics
```

Neither approach is universally better.

They are different object-creation patterns.

---

# 42. Constructor Functions vs Classes

Constructor function:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};
```

Equivalent-style class:

```javascript
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(`Hello ${this.name}`);
    }
}
```

Usage:

```javascript
const user = new User("John", 25);
```

Both use:

```javascript
new
```

and both rely on prototypes.

---

# 43. Class `constructor`

Inside a class:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

the method named:

```javascript
constructor
```

runs when:

```javascript
new User("John")
```

is executed.

Example:

```javascript
const user = new User("John");
```

Then:

```text
this → newly created User instance
```

---

# 44. Constructor Function vs Class Constructor

Constructor function:

```javascript
function User(name) {
    this.name = name;
}
```

Class:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

Usage:

```javascript
const user1 = new User("John");
```

The class syntax is cleaner for many object-oriented patterns, but prototypes still exist underneath.

---

# 45. Classes Cannot Be Called Without `new`

Constructor function:

```javascript
function User() {
}
```

can syntactically be called:

```javascript
User();
```

although this may be incorrect for its intended use.

A class:

```javascript
class User {
}
```

cannot be called without:

```javascript
new
```

This:

```javascript
User();
```

throws an error.

You must use:

```javascript
new User();
```

---

# 46. Constructor Inheritance

Before `class extends`, constructor inheritance was often implemented manually.

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};
```

Now create:

```javascript
function Admin(name, permissions) {
    User.call(this, name);

    this.permissions = permissions;
}
```

Here:

```javascript
User.call(this, name);
```

calls `User` using the current `Admin` instance as `this`.

---

# 47. `call()` in Constructor Inheritance

Consider:

```javascript
function Admin(name) {
    User.call(this, name);
}
```

When:

```javascript
const admin = new Admin("John");
```

inside `Admin`:

```text
this → admin
```

Then:

```javascript
User.call(this, name);
```

effectively performs:

```javascript
User.call(admin, "John");
```

So:

```javascript
this.name = name;
```

inside `User` modifies:

```text
admin
```

---

# 48. `call()` Does Not Inherit Prototype Methods

Suppose:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log("Hello");
};

function Admin(name) {
    User.call(this, name);
}
```

This copies initialization behavior:

```text
name
```

But it does **not** connect:

```text
Admin.prototype
```

to:

```text
User.prototype
```

Therefore prototype inheritance must be established separately.

---

# 49. Prototype Inheritance with `Object.create()`

Use:

```javascript
Admin.prototype = Object.create(
    User.prototype
);
```

Complete example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};

function Admin(name, permissions) {
    User.call(this, name);

    this.permissions = permissions;
}

Admin.prototype = Object.create(
    User.prototype
);
```

Now:

```text
Admin.prototype
│
▼
User.prototype
```

---

# 50. Resetting `.constructor`

After:

```javascript
Admin.prototype = Object.create(
    User.prototype
);
```

the new `Admin.prototype` object inherits its `constructor` lookup from `User.prototype`.

Therefore:

```javascript
Admin.prototype.constructor
```

would resolve to:

```javascript
User
```

To fix it:

```javascript
Admin.prototype.constructor = Admin;
```

Complete:

```javascript
Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;
```

---

# 51. Complete Constructor Inheritance

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};

function Admin(name, permissions) {
    User.call(this, name);

    this.permissions = permissions;
}

Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;

Admin.prototype.deleteUser = function () {
    console.log("User deleted");
};
```

Create:

```javascript
const admin = new Admin(
    "John",
    ["delete", "edit"]
);
```

Now:

```javascript
admin.greet();
admin.deleteUser();
```

both work.

---

# 52. Constructor Inheritance Prototype Chain

For:

```javascript
const admin = new Admin("John", []);
```

the chain becomes:

```text
admin
│
├── name
├── permissions
│
▼
Admin.prototype
│
├── deleteUser()
├── constructor
│
▼
User.prototype
│
├── greet()
├── constructor
│
▼
Object.prototype
│
▼
null
```

---

# 53. `instanceof` with Inheritance

Using the previous setup:

```javascript
const admin = new Admin("John", []);
```

Check:

```javascript
console.log(
    admin instanceof Admin
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    admin instanceof User
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    admin instanceof Object
);
```

Output:

```text
true
```

Because the prototype chain contains:

```text
Admin.prototype
User.prototype
Object.prototype
```

---

# 54. Constructor Inheritance vs `extends`

Old constructor approach:

```javascript
function User(name) {
    this.name = name;
}

function Admin(name) {
    User.call(this, name);
}

Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;
```

Modern class approach:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}

class Admin extends User {
    constructor(name) {
        super(name);
    }
}
```

Classes make inheritance much easier to express.

---

# 55. Built-In Constructors

JavaScript contains many built-in constructors.

Examples:

```javascript
Object
Array
Function
Date
RegExp
Map
Set
Promise
Error
```

Examples:

```javascript
const date = new Date();

const map = new Map();

const set = new Set();
```

---

# 56. Array Constructor

You can create arrays using:

```javascript
const numbers = new Array(
    1,
    2,
    3
);
```

But usually prefer:

```javascript
const numbers = [
    1,
    2,
    3
];
```

Both create arrays.

---

# 57. Object Constructor

You can write:

```javascript
const user = new Object();
```

But usually prefer:

```javascript
const user = {};
```

Object literals are shorter and clearer.

---

# 58. String, Number, and Boolean Constructors

JavaScript provides:

```javascript
String
Number
Boolean
```

But be careful.

Example:

```javascript
const name = new String("John");
```

This creates a **String object**, not a primitive string.

Check:

```javascript
typeof name;
```

Output:

```text
object
```

Compare:

```javascript
const name = "John";

typeof name;
```

Output:

```text
string
```

---

# 59. Avoid Primitive Wrapper Constructors

Avoid:

```javascript
new String("John");
new Number(10);
new Boolean(false);
```

Prefer:

```javascript
"John";
10;
false;
```

Especially:

```javascript
new Boolean(false)
```

can be confusing because the resulting object itself is truthy.

Example:

```javascript
const value = new Boolean(false);

if (value) {
    console.log("Runs");
}
```

The block runs because:

```text
value
```

is an object, and objects are truthy.

---

# 60. Constructor Function with Default Values

Example:

```javascript
function User(
    name,
    age = 18,
    active = true
) {
    this.name = name;
    this.age = age;
    this.active = active;
}
```

Create:

```javascript
const user = new User("John");
```

Result:

```javascript
console.log(user);
```

Conceptually:

```javascript
{
    name: "John",
    age: 18,
    active: true
}
```

---

# 61. Constructor with Object Parameter

For constructors with many parameters, passing an object can improve readability.

Instead of:

```javascript
new User(
    "John",
    25,
    "john@example.com",
    true,
    "admin"
);
```

You can use:

```javascript
function User({
    name,
    age,
    email,
    active,
    role
}) {
    this.name = name;
    this.age = age;
    this.email = email;
    this.active = active;
    this.role = role;
}
```

Create:

```javascript
const user = new User({
    name: "John",
    age: 25,
    email: "john@example.com",
    active: true,
    role: "admin"
});
```

This can make the call easier to understand.

---

# 62. Private Data and Constructor Functions

Constructor functions can use closures to create private-like data.

Example:

```javascript
function BankAccount(balance) {
    let currentBalance = balance;

    this.getBalance = function () {
        return currentBalance;
    };

    this.deposit = function (amount) {
        currentBalance += amount;
    };
}
```

Create:

```javascript
const account = new BankAccount(1000);
```

Use:

```javascript
account.deposit(500);

console.log(
    account.getBalance()
);
```

Output:

```text
1500
```

But:

```javascript
account.currentBalance
```

is:

```text
undefined
```

because `currentBalance` is a local variable captured by the methods, not an object property.

---

# 63. Closure Trade-Off in Constructors

The previous pattern:

```javascript
function User(name) {
    let password = "secret";

    this.getPassword = function () {
        return password;
    };
}
```

creates a new:

```javascript
getPassword
```

function for every instance.

So:

```javascript
const user1 = new User("John");
const user2 = new User("Alice");

console.log(
    user1.getPassword ===
    user2.getPassword
);
```

Output:

```text
false
```

This may be appropriate when closure-based private state is required, but shared prototype methods are usually preferable when no per-instance closure is needed.

---

# 64. Static Properties on Constructor Functions

Remember:

```javascript
User
```

itself is a function object.

Therefore properties can be added directly to it.

Example:

```javascript
function User(name) {
    this.name = name;
}

User.type = "Application User";
```

Access:

```javascript
console.log(User.type);
```

Output:

```text
Application User
```

But:

```javascript
const user = new User("John");

console.log(user.type);
```

Output:

```text
undefined
```

because:

```text
User.type
```

belongs to the constructor function itself, not `User.prototype`.

---

# 65. Static Methods on Constructor Functions

Example:

```javascript
function User(name) {
    this.name = name;
}

User.createGuest = function () {
    return new User("Guest");
};
```

Use:

```javascript
const guest = User.createGuest();
```

Result:

```javascript
console.log(guest.name);
```

Output:

```text
Guest
```

But:

```javascript
guest.createGuest();
```

does not work.

The method belongs to:

```javascript
User
```

not:

```javascript
User.prototype
```

---

# 66. Static vs Prototype vs Instance

Consider:

```javascript
function User(name) {
    this.name = name;
}

User.type = "User";

User.prototype.greet = function () {
    console.log(`Hello ${this.name}`);
};

const user = new User("John");
```

There are three locations:

```text
User
│
└── type
```

Static property:

```javascript
User.type
```

---

```text
User.prototype
│
└── greet()
```

Shared instance behavior:

```javascript
user.greet()
```

---

```text
user
│
└── name
```

Instance-specific data:

```javascript
user.name
```

---

# 67. Constructor Function Mental Model

Code:

```javascript
function User(name) {
    this.name = name;
}

User.type = "User";

User.prototype.greet = function () {
    return `Hello ${this.name}`;
};

const user = new User("John");
```

Think:

```text
             User
             │
             ├── type: "User"
             │
             └── prototype
                    │
                    ▼
             User.prototype
             ├── greet()
             └── constructor
                    ▲
                    │
              [[Prototype]]
                    │
                  user
                    │
                    └── name: "John"
```

---

# 68. Common Mistake — Forgetting `new`

Wrong:

```javascript
function User(name) {
    this.name = name;
}

const user = User("John");
```

Correct:

```javascript
const user = new User("John");
```

---

# 69. Common Mistake — Lowercase Constructor Name

Technically valid:

```javascript
function user(name) {
    this.name = name;
}

const john = new user("John");
```

But confusing.

Prefer:

```javascript
function User(name) {
    this.name = name;
}
```

Capitalization signals constructor usage.

---

# 70. Common Mistake — Defining Every Method Inside Constructor

Less efficient when sharing is desired:

```javascript
function User(name) {
    this.name = name;

    this.greet = function () {
        console.log("Hello");
    };
}
```

Prefer:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log("Hello");
};
```

when the method does not need its own per-instance closure.

---

# 71. Common Mistake — Confusing Constructor with `.constructor`

These are related but different.

Constructor function:

```javascript
function User() {
}
```

Prototype property:

```javascript
User.prototype.constructor
```

The latter normally points back to:

```javascript
User
```

So:

```javascript
User.prototype.constructor === User
```

is:

```text
true
```

---

# 72. Common Mistake — Confusing `.prototype` and `[[Prototype]]`

Constructor property:

```javascript
User.prototype
```

Instance internal prototype:

```javascript
Object.getPrototypeOf(user)
```

After:

```javascript
const user = new User();
```

they point to the same object:

```javascript
Object.getPrototypeOf(user)
    === User.prototype;
```

But the concepts are different.

---

# 73. Common Mistake — Replacing `.prototype`

Consider:

```javascript
function User() {
}

User.prototype = {
    greet() {
        console.log("Hello");
    }
};
```

Now the default prototype object was replaced.

Therefore:

```javascript
User.prototype.constructor === User
```

is no longer normally true.

If necessary:

```javascript
User.prototype = {
    constructor: User,

    greet() {
        console.log("Hello");
    }
};
```

---

# 74. Existing Instances and Prototype Replacement

Example:

```javascript
function User() {
}

const user1 = new User();

User.prototype = {
    greet() {
        console.log("Hello");
    }
};

const user2 = new User();
```

Now:

```text
user1
→ old User.prototype


user2
→ new User.prototype
```

Therefore:

```javascript
typeof user1.greet;
```

Output:

```text
undefined
```

But:

```javascript
typeof user2.greet;
```

Output:

```text
function
```

---

# 75. Modifying Prototype Is Different

Example:

```javascript
function User() {
}

const user = new User();

User.prototype.greet = function () {
    console.log("Hello");
};
```

Now:

```javascript
user.greet();
```

works.

Why?

Because `user` still points to the same prototype object, and that object was modified.

---

# 76. Common Mistake — Arrow Function as Constructor

Arrow functions cannot be used as constructors.

Example:

```javascript
const User = (name) => {
    this.name = name;
};
```

This:

```javascript
new User("John");
```

throws:

```text
TypeError
```

Arrow functions are not constructable and do not have the normal constructor `.prototype` behavior.

Use:

```javascript
function User(name) {
    this.name = name;
}
```

or:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

---

# 77. Not Every Function Can Be Used with `new`

Normal function declarations are generally constructable:

```javascript
function User() {
}

new User();
```

Arrow functions are not:

```javascript
const User = () => {};

new User();
```

Error.

Methods defined using concise method syntax are also not constructors.

Example:

```javascript
const object = {
    create() {
    }
};
```

This does not work:

```javascript
new object.create();
```

---

# 78. Constructor Interview Question 1

What is the output?

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");

console.log(user.name);
```

Answer:

```text
John
```

---

# 79. Constructor Interview Question 2

What is the output?

```javascript
function User(name) {
    this.name = name;
}

const user1 = new User("John");
const user2 = new User("Alice");

console.log(user1 === user2);
```

Answer:

```text
false
```

Each `new` call creates a new object.

---

# 80. Constructor Interview Question 3

What is the output?

```javascript
function User() {
}

const user = new User();

console.log(
    Object.getPrototypeOf(user)
        === User.prototype
);
```

Answer:

```text
true
```

---

# 81. Constructor Interview Question 4

What is the output?

```javascript
function User() {
}

const user = new User();

console.log(
    user instanceof User
);
```

Answer:

```text
true
```

---

# 82. Constructor Interview Question 5

What is the output?

```javascript
function User() {
}

console.log(
    User.prototype.constructor
        === User
);
```

Answer:

```text
true
```

---

# 83. Constructor Interview Question 6

What is the output?

```javascript
function User(name) {
    this.name = name;

    return 10;
}

const user = new User("John");

console.log(user.name);
```

Answer:

```text
John
```

Primitive return values are ignored.

---

# 84. Constructor Interview Question 7

What is the output?

```javascript
function User(name) {
    this.name = name;

    return {
        name: "Alice"
    };
}

const user = new User("John");

console.log(user.name);
```

Answer:

```text
Alice
```

The explicitly returned object replaces the constructed instance.

---

# 85. Constructor Interview Question 8

What is the output?

```javascript
function User(name) {
    this.name = name;

    this.greet = function () {
        return "Hello";
    };
}

const user1 = new User("John");
const user2 = new User("Alice");

console.log(
    user1.greet === user2.greet
);
```

Answer:

```text
false
```

Each instance receives its own function.

---

# 86. Constructor Interview Question 9

What is the output?

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    return "Hello";
};

const user1 = new User("John");
const user2 = new User("Alice");

console.log(
    user1.greet === user2.greet
);
```

Answer:

```text
true
```

Both inherit the same function from:

```javascript
User.prototype
```

---

# 87. Constructor Interview Question 10

What is the output?

```javascript
function User() {
}

const user = new User();

console.log(
    user instanceof Object
);
```

Answer:

```text
true
```

Prototype chain:

```text
user
↓
User.prototype
↓
Object.prototype
↓
null
```

---

# 88. Constructor Interview Question 11

What happens?

```javascript
const User = () => {
};

const user = new User();
```

Answer:

```text
TypeError
```

Arrow functions cannot be constructors.

---

# 89. Constructor Interview Question 12

What is the output?

```javascript
function User() {
}

User.type = "user";

const user = new User();

console.log(user.type);
```

Answer:

```text
undefined
```

`type` belongs directly to:

```javascript
User
```

not:

```javascript
User.prototype
```

---

# 90. Constructor Interview Question 13

What is the output?

```javascript
function User() {
}

User.prototype.role = "user";

const user = new User();

console.log(user.role);
```

Answer:

```text
user
```

The property is inherited from:

```javascript
User.prototype
```

---

# 91. Constructor Interview Question 14

What is the output?

```javascript
function User() {
}

User.prototype.role = "user";

const user = new User();

user.role = "admin";

console.log(user.role);
```

Answer:

```text
admin
```

The own property shadows the prototype property.

---

# 92. Constructor Interview Question 15

What is the output?

```javascript
function User() {
}

const user = new User();

User.prototype.greet = function () {
    return "Hello";
};

console.log(user.greet());
```

Answer:

```text
Hello
```

The existing instance references the same prototype object that was modified.

---

# 93. Constructor Interview Question 16

What is the output?

```javascript
function User() {
}

const user1 = new User();

User.prototype = {
    greet() {
        return "Hello";
    }
};

const user2 = new User();

console.log(
    typeof user1.greet
);

console.log(
    typeof user2.greet
);
```

Answer:

```text
undefined
function
```

Because:

```text
user1 → old prototype
user2 → new prototype
```

---

# 94. Constructor Interview Question 17

What is the output?

```javascript
function User() {
}

function Admin() {
}

Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;

const admin = new Admin();

console.log(
    admin instanceof Admin
);

console.log(
    admin instanceof User
);
```

Answer:

```text
true
true
```

---

# 95. Constructor Interview Question 18

What is the output?

```javascript
function User(name) {
    this.name = name;
}

function Admin(name) {
    User.call(this, name);
}

const admin = new Admin("John");

console.log(admin.name);
```

Answer:

```text
John
```

`User.call(this, name)` initializes the `Admin` instance using `User`'s constructor logic.

---

# 96. Constructor Interview Question 19

Does this make `admin instanceof User` true?

```javascript
function User(name) {
    this.name = name;
}

function Admin(name) {
    User.call(this, name);
}

const admin = new Admin("John");
```

Answer:

```text
No
```

Because:

```javascript
User.call(this, name);
```

only runs the `User` initialization logic.

It does not establish:

```text
Admin.prototype
↓
User.prototype
```

You need:

```javascript
Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;
```

---

# 97. Constructor Interview Question 20

What does `new.target` contain?

```javascript
function User() {
    console.log(new.target);
}

new User();
```

Answer:

```text
User
```

But:

```javascript
User();
```

gives:

```text
undefined
```

for `new.target`.

---

# 98. How `this`, `new`, and Prototypes Connect

Consider:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet = function () {
    console.log(
        `Hello ${this.name}`
    );
};

const user = new User("John");

user.greet();
```

There are three important mechanisms.

### `new`

Creates the instance:

```text
user
```

and connects:

```text
user.[[Prototype]]
↓
User.prototype
```

### `this` inside constructor

```javascript
this.name = name;
```

stores:

```text
name
```

on the newly created instance.

### Prototype

```javascript
User.prototype.greet
```

provides shared behavior.

When:

```javascript
user.greet();
```

JavaScript finds `greet` through the prototype chain.

Inside `greet`:

```text
this → user
```

because the method was called as:

```javascript
user.greet()
```

---

# 99. Complete Constructor Mental Model

Code:

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}

User.prototype.greet = function () {
    return `Hello ${this.name}`;
};

User.prototype.isAdult = function () {
    return this.age >= 18;
};

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
```

Think:

```text
                  User
                   │
                   │ .prototype
                   ▼
          ┌─────────────────────┐
          │   User.prototype    │
          │                     │
          │ greet()             │
          │ isAdult()           │
          │ constructor         │
          └──────────┬──────────┘
                     ▲
                     │
              [[Prototype]]
             ┌───────┴───────┐
             │               │
             │               │
       ┌────────────┐   ┌────────────┐
       │   user1    │   │   user2    │
       │            │   │            │
       │ name: John │   │ name: Alice│
       │ age: 25    │   │ age: 30    │
       └────────────┘   └────────────┘
```

Both instances contain their own data.

Both share:

```text
greet()
isAdult()
```

through:

```javascript
User.prototype
```

---

# 100. What `new User()` Really Means

When you see:

```javascript
const user = new User("John");
```

mentally read it as:

```text
Create object
      ↓
Connect object to User.prototype
      ↓
Call User with this = object
      ↓
this.name = "John"
      ↓
Return object
      ↓
Assign object to user
```

This mental model explains constructor functions.

---

# 101. Constructor Function Cheat Sheet

### Define Constructor

```javascript
function User(name, age) {
    this.name = name;
    this.age = age;
}
```

### Create Instance

```javascript
const user = new User(
    "John",
    25
);
```

### Add Shared Method

```javascript
User.prototype.greet = function () {
    console.log(
        `Hello ${this.name}`
    );
};
```

### Check Prototype

```javascript
Object.getPrototypeOf(user)
    === User.prototype;
```

### Check Instance

```javascript
user instanceof User;
```

### Check Own Property

```javascript
Object.hasOwn(
    user,
    "name"
);
```

### Constructor Reference

```javascript
User.prototype.constructor
    === User;
```

### Static Property

```javascript
User.type = "User";
```

### Static Method

```javascript
User.create = function (name) {
    return new User(name);
};
```

### Constructor Inheritance

```javascript
function Admin(name) {
    User.call(this, name);
}

Admin.prototype = Object.create(
    User.prototype
);

Admin.prototype.constructor = Admin;
```

### Check `new`

```javascript
function User() {
    if (!new.target) {
        throw new Error(
            "Use new"
        );
    }
}
```

---

# 102. Important Rules to Remember

```text
1. Constructor functions are regular functions.

2. They are designed to create objects.

3. Constructor names conventionally begin with uppercase letters.

4. Constructor functions are normally called using new.

5. new creates a new object.

6. new connects the object's [[Prototype]] to Constructor.prototype.

7. new calls the constructor with this pointing to the new object.

8. new normally returns the new object.

9. this.property creates instance properties.

10. Every new call creates a separate instance.

11. Instance properties belong directly to each object.

12. Methods defined inside constructors are recreated per instance.

13. Shared methods can be placed on Constructor.prototype.

14. Prototype methods are shared between instances.

15. Object.getPrototypeOf(instance) normally equals Constructor.prototype.

16. Constructor.prototype.constructor normally points back to Constructor.

17. instanceof checks whether Constructor.prototype exists in the object's prototype chain.

18. Returning a primitive from a constructor does not replace the instance.

19. Returning an object can replace the instance.

20. Calling a constructor without new performs an ordinary function call.

21. Missing new can cause incorrect this behavior.

22. new.target can detect whether new was used.

23. Arrow functions cannot be constructors.

24. Factory functions create objects without requiring new.

25. Constructor functions and factory functions are different patterns.

26. Classes provide cleaner syntax for many constructor/prototype patterns.

27. Classes still rely on JavaScript's prototype system.

28. User.call(this, ...) can reuse constructor initialization logic.

29. call() alone does not establish prototype inheritance.

30. Object.create() can establish prototype inheritance between constructor prototypes.

31. After replacing a prototype, the constructor property may need to be restored.

32. Existing instances keep their existing prototype object.

33. Modifying a prototype affects instances that reference that prototype.

34. Replacing a prototype does not update existing instances.

35. Static properties belong to the constructor function itself.

36. Prototype properties are inherited by instances.

37. Instance properties belong directly to individual objects.

38. Avoid new String(), new Number(), and new Boolean() for primitive values.

39. Constructor functions connect new, this, objects, and prototypes.

40. Understanding constructor functions explains how pre-ES6 JavaScript object-oriented code works.
```

---

# 103. Final Mental Model

If you remember only one diagram, remember this:

```text
                     User
                      │
                      │ .prototype
                      ▼
               User.prototype
               ├── greet()
               ├── login()
               └── constructor
                      ▲
                      │
               [[Prototype]]
                ┌─────┴─────┐
                │           │
              user1       user2
              │           │
              ├ name      ├ name
              └ age       └ age
```

When:

```javascript
const user1 = new User(
    "John",
    25
);
```

`new` does conceptually:

```text
Create user1
      ↓
user1.[[Prototype]] = User.prototype
      ↓
this = user1
      ↓
Run User("John", 25)
      ↓
Set user1.name and user1.age
      ↓
Return user1
```

When:

```javascript
user1.greet();
```

JavaScript does:

```text
user1
│
├── greet?
│     NO
│
▼
User.prototype
│
├── greet?
│     YES
│
▼
Call greet with this = user1
```

So the complete relationship is:

```text
Constructor Function
        │
        │ new
        ▼
     Instance
        │
        │ [[Prototype]]
        ▼
Constructor.prototype
        │
        ▼
 Object.prototype
        │
        ▼
       null
```

That is the core of **JavaScript constructor functions**.

---

# Constructor Functions in One Sentence

> A constructor function is a regular function called with `new`, where `new` creates an object, connects it to the constructor's `.prototype`, executes the constructor with `this` pointing to that object, and normally returns the resulting instance.

---

