#  Prototypes

JavaScript is a **prototype-based language**.

Objects can inherit properties and methods from other objects through a mechanism called the **prototype chain**.

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    user.toString()
);
```

We never defined:

```javascript
toString()
```

inside `user`.

So where did it come from?

Conceptually:

```text
user
│
├── name: "John"
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

JavaScript found `toString()` through the object's **prototype chain**.

---

# 1. What Is a Prototype?

Every ordinary JavaScript object has an internal link to another object called its **prototype**.

Conceptually:

```text
Object
  │
  │ [[Prototype]]
  ▼
Another Object
```

Example:

```javascript
const user = {
    name: "John"
};
```

Conceptually:

```text
user
│
├── name: "John"
│
▼
Object.prototype
```

The prototype provides properties and methods that the object can inherit.

---

# 2. Why Do Prototypes Exist?

Suppose we create:

```javascript
const user1 = {
    name: "John"
};

const user2 = {
    name: "Alice"
};

const user3 = {
    name: "Bob"
};
```

All ordinary objects can use methods such as:

```javascript
toString()
```

JavaScript does not need to copy `toString()` separately into every object.

Instead:

```text
user1 ─────┐
           │
user2 ─────┼────► Object.prototype
           │
user3 ─────┘
```

All of them can access shared behavior through the prototype chain.

This enables:

```text
Method sharing
Inheritance
Code reuse
Memory efficiency
Built-in object behavior
```

---

# 3. `[[Prototype]]`

The internal prototype link is formally represented as:

```text
[[Prototype]]
```

Example:

```javascript
const user = {
    name: "John"
};
```

Conceptually:

```text
user
│
├── name: "John"
│
└── [[Prototype]]
          │
          ▼
    Object.prototype
```

Important:

```text
[[Prototype]]
```

is an internal language concept.

It is not normally accessed using:

```javascript
user.[[Prototype]]
```

That syntax does not exist.

---

# 4. Getting an Object's Prototype

Use:

```javascript
Object.getPrototypeOf()
```

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    Object.getPrototypeOf(user)
);
```

For a normal object literal:

```javascript
Object.getPrototypeOf(user)
    === Object.prototype
```

is:

```text
true
```

Example:

```javascript
console.log(
    Object.getPrototypeOf(user)
        === Object.prototype
);
```

Output:

```text
true
```

---

# 5. Prototype Chain

A prototype can itself have another prototype.

This creates a **prototype chain**.

Example:

```text
object
│
▼
prototype
│
▼
prototype
│
▼
Object.prototype
│
▼
null
```

JavaScript follows this chain when searching for properties.

---

# 6. Property Lookup

Consider:

```javascript
const user = {
    name: "John"
};

console.log(user.name);
```

JavaScript first checks:

```text
user
│
├── name?
│
└── YES
```

So it returns:

```text
John
```

But:

```javascript
user.toString();
```

JavaScript searches:

```text
user
│
├── toString?
│     NO
│
▼
Object.prototype
│
├── toString?
│     YES
│
▼
Use Object.prototype.toString
```

This process is called **property lookup**.

---

# 7. Property Lookup Algorithm

When JavaScript evaluates:

```javascript
object.property
```

conceptually:

```text
1. Check object itself

2. Property found?
   YES → return it

3. Otherwise get object's prototype

4. Check prototype

5. Property found?
   YES → return it

6. Otherwise continue upward

7. Eventually reach null

8. Property not found
   → undefined
```

Example:

```javascript
console.log(
    user.someProperty
);
```

If it does not exist anywhere:

```text
user
↓
Object.prototype
↓
null
```

Result:

```text
undefined
```

---

# 8. `Object.prototype`

Most ordinary objects ultimately inherit from:

```javascript
Object.prototype
```

Example:

```javascript
const user = {};
```

Prototype chain:

```text
user
│
▼
Object.prototype
│
▼
null
```

`Object.prototype` contains common methods available through inheritance.

Examples include:

```javascript
toString()
valueOf()
hasOwnProperty()
isPrototypeOf()
propertyIsEnumerable()
```

---

# 9. `null` Ends the Prototype Chain

Check:

```javascript
Object.getPrototypeOf(
    Object.prototype
);
```

Result:

```text
null
```

Therefore:

```text
user
│
▼
Object.prototype
│
▼
null
```

`null` means:

```text
There is no further prototype.
```

---

# 10. Own Properties

A property stored directly on an object is called an **own property**.

Example:

```javascript
const user = {
    name: "John",
    age: 25
};
```

Here:

```text
name
age
```

are own properties of:

```javascript
user
```

Check:

```javascript
Object.hasOwn(
    user,
    "name"
);
```

Output:

```text
true
```

---

# 11. Inherited Properties

Properties found through the prototype chain are **inherited properties**.

Example:

```javascript
const user = {
    name: "John"
};
```

`toString` is not an own property:

```javascript
console.log(
    Object.hasOwn(
        user,
        "toString"
    )
);
```

Output:

```text
false
```

But:

```javascript
console.log(
    "toString" in user
);
```

Output:

```text
true
```

because it exists in the prototype chain.

---

# 12. `Object.hasOwn()`

Modern JavaScript provides:

```javascript
Object.hasOwn(
    object,
    property
);
```

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    Object.hasOwn(
        user,
        "name"
    )
);
```

Output:

```text
true
```

But:

```javascript
console.log(
    Object.hasOwn(
        user,
        "toString"
    )
);
```

Output:

```text
false
```

`Object.hasOwn()` checks only the object itself.

It does not search the prototype chain.

---

# 13. `hasOwnProperty()`

You may also see:

```javascript
user.hasOwnProperty(
    "name"
);
```

Example:

```javascript
console.log(
    user.hasOwnProperty(
        "name"
    )
);
```

Output:

```text
true
```

However, modern code often prefers:

```javascript
Object.hasOwn(
    user,
    "name"
);
```

because it works even with objects that do not inherit from `Object.prototype`, and it avoids problems if an object defines its own `hasOwnProperty` property.

---

# 14. The `in` Operator

The `in` operator checks both:

```text
Own properties
+
Inherited properties
```

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    "name" in user
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    "toString" in user
);
```

Output:

```text
true
```

because `toString` exists through:

```javascript
Object.prototype
```

---

# 15. `Object.hasOwn()` vs `in`

Example:

```javascript
const user = {
    name: "John"
};
```

For:

```javascript
"name"
```

```javascript
Object.hasOwn(
    user,
    "name"
);
```

Output:

```text
true
```

And:

```javascript
"name" in user;
```

Output:

```text
true
```

For:

```javascript
"toString"
```

```javascript
Object.hasOwn(
    user,
    "toString"
);
```

Output:

```text
false
```

But:

```javascript
"toString" in user;
```

Output:

```text
true
```

Think:

```text
Object.hasOwn()
→ object only


in
→ object + prototype chain
```

---

# 16. Creating an Object with a Specific Prototype

Use:

```javascript
Object.create()
```

Example:

```javascript
const userMethods = {
    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
};

const user =
    Object.create(
        userMethods
    );

user.name = "John";
```

Now:

```javascript
user.greet();
```

Output:

```text
Hello John
```

But `greet` is not stored directly on `user`.

Check:

```javascript
Object.hasOwn(
    user,
    "greet"
);
```

Output:

```text
false
```

Because:

```text
user
│
├── name: "John"
│
▼
userMethods
│
└── greet()
```

---

# 17. `Object.create()` Mental Model

Code:

```javascript
const parent = {
    greet() {
        console.log("Hello");
    }
};

const child =
    Object.create(parent);
```

Conceptually:

```text
child
│
│ [[Prototype]]
▼
parent
│
├── greet()
│
│ [[Prototype]]
▼
Object.prototype
│
▼
null
```

Therefore:

```javascript
child.greet();
```

works.

---

# 18. Prototype Inheritance

Example:

```javascript
const animal = {
    eat() {
        console.log("Eating");
    }
};

const dog =
    Object.create(animal);

dog.bark = function () {
    console.log("Barking");
};
```

Now:

```javascript
dog.bark();
dog.eat();
```

Output:

```text
Barking
Eating
```

Prototype chain:

```text
dog
│
├── bark()
│
▼
animal
│
├── eat()
│
▼
Object.prototype
│
▼
null
```

This is **prototype inheritance**.

---

# 19. Inherited Methods Use the Calling Object as `this`

Consider:

```javascript
const userMethods = {
    greet() {
        console.log(
            `Hello ${this.name}`
        );
    }
};

const user =
    Object.create(
        userMethods
    );

user.name = "John";

user.greet();
```

Even though `greet()` exists on:

```javascript
userMethods
```

inside the method:

```text
this → user
```

Why?

Because the call is:

```javascript
user.greet();
```

This connects prototypes directly to the `this` topic.

---

# 20. Prototype Property Shadowing

Suppose:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);
```

Now:

```javascript
console.log(
    child.role
);
```

Output:

```text
user
```

Because JavaScript finds `role` on:

```javascript
parent
```

Now:

```javascript
child.role = "admin";
```

Then:

```javascript
console.log(
    child.role
);
```

Output:

```text
admin
```

The prototype still contains:

```text
role → "user"
```

But the child has its own property:

```text
role → "admin"
```

---

# 21. Shadowing Mental Model

Before:

```text
child
│
│ role?
│ NO
│
▼
parent
│
└── role: "user"
```

After:

```javascript
child.role = "admin";
```

```text
child
│
├── role: "admin"
│
▼
parent
│
└── role: "user"
```

Lookup:

```text
child.role
│
├── found on child
│
└── stop searching
```

Result:

```text
admin
```

This is called **property shadowing**.

---

# 22. Deleting a Shadowing Property

Example:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.role = "admin";
```

Now:

```javascript
console.log(child.role);
```

Output:

```text
admin
```

Delete the own property:

```javascript
delete child.role;
```

Now:

```javascript
console.log(child.role);
```

Output:

```text
user
```

JavaScript resumes finding the inherited property from:

```javascript
parent
```

---

# 23. Setting the Prototype

JavaScript provides:

```javascript
Object.setPrototypeOf()
```

Example:

```javascript
const animal = {
    eat() {
        console.log("Eating");
    }
};

const dog = {
    bark() {
        console.log("Barking");
    }
};

Object.setPrototypeOf(
    dog,
    animal
);
```

Now:

```javascript
dog.eat();
```

works.

Prototype chain:

```text
dog
↓
animal
↓
Object.prototype
↓
null
```

---

# 24. Avoid Frequently Changing Prototypes

Although:

```javascript
Object.setPrototypeOf()
```

exists, dynamically changing an object's prototype after creation can hurt performance and make code harder to reason about.

Prefer establishing the prototype when creating the object:

```javascript
Object.create(prototype)
```

or using:

```javascript
class
```

or constructor-based patterns when appropriate.

---

# 25. `__proto__`

You may encounter:

```javascript
object.__proto__
```

Example:

```javascript
const user = {};

console.log(
    user.__proto__
);
```

This historically exposes an object's prototype through an accessor inherited from `Object.prototype`.

But modern code should generally prefer:

```javascript
Object.getPrototypeOf(user)
```

and:

```javascript
Object.setPrototypeOf(
    user,
    prototype
);
```

rather than relying on:

```javascript
__proto__
```

---

# 26. `__proto__` vs `prototype`

These are **not the same thing**.

This causes major confusion.

```text
__proto__
```

historically refers to an object's prototype.

Example:

```javascript
user.__proto__
```

Whereas:

```text
prototype
```

is a property found on constructor functions.

Example:

```javascript
User.prototype
```

These concepts are related but different.

---

# 27. Constructor `.prototype`

Every constructable ordinary function has a property named:

```javascript
prototype
```

Example:

```javascript
function User() {
}

console.log(
    User.prototype
);
```

This object is used when instances are created with:

```javascript
new User()
```

---

# 28. `.prototype` Does Not Mean the Function's Own Prototype

This is important.

When we say:

```javascript
User.prototype
```

we are accessing a normal property named:

```text
prototype
```

on the `User` function object.

It is **not** the same thing as:

```javascript
Object.getPrototypeOf(User)
```

For example:

```text
User.prototype
→ object used for instances created by new User()


Object.getPrototypeOf(User)
→ prototype of the User function object itself
```

These are different relationships.

---

# 29. Constructor Function Example

Consider:

```javascript
function User(name) {
    this.name = name;
}
```

Create:

```javascript
const user =
    new User("John");
```

JavaScript connects:

```text
user.[[Prototype]]
```

to:

```text
User.prototype
```

Therefore:

```javascript
Object.getPrototypeOf(user)
    === User.prototype
```

is:

```text
true
```

---

# 30. `new` and Prototype Connection

When:

```javascript
const user =
    new User("John");
```

conceptually:

```text
1. Create new object

2. Set its [[Prototype]]
   to User.prototype

3. Call User with
   this = new object

4. Return object
```

Result:

```text
User
│
└── prototype
      │
      ▼
User.prototype
      ▲
      │ [[Prototype]]
      │
     user
```

---

# 31. Adding Methods to `.prototype`

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        console.log(
            `Hello ${this.name}`
        );
    };
```

Create:

```javascript
const user1 =
    new User("John");

const user2 =
    new User("Alice");
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

Both objects share:

```javascript
User.prototype.greet
```

---

# 32. Why Put Methods on `.prototype`?

Consider:

```javascript
function User(name) {
    this.name = name;

    this.greet =
        function () {
            console.log(
                `Hello ${this.name}`
            );
        };
}
```

Every new object gets its own function:

```text
user1
└── greet → Function A

user2
└── greet → Function B
```

Instead:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        console.log(
            `Hello ${this.name}`
        );
    };
```

Now:

```text
user1 ─────┐
           │
           ▼
     User.prototype
           │
           └── greet()
           ▲
           │
user2 ─────┘
```

The method is shared.

---

# 33. Shared Prototype Methods

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        return (
            `Hello ${this.name}`
        );
    };

const user1 =
    new User("John");

const user2 =
    new User("Alice");
```

Check:

```javascript
console.log(
    user1.greet ===
    user2.greet
);
```

Output:

```text
true
```

Both instances find the same function on:

```javascript
User.prototype
```

---

# 34. Instance Properties Are Separate

Example:

```javascript
function User(name) {
    this.name = name;
}

const user1 =
    new User("John");

const user2 =
    new User("Alice");
```

Check:

```javascript
console.log(
    user1.name
);

console.log(
    user2.name
);
```

Output:

```text
John
Alice
```

Instance data:

```text
user1
└── name → John

user2
└── name → Alice
```

Shared behavior can live on:

```text
User.prototype
```

---

# 35. Instance Data vs Shared Behavior

A useful mental model:

```text
User.prototype
│
├── greet()
├── login()
└── logout()
      ▲
      │
 ┌────┴────┐
 │         │
user1     user2
 │         │
name      name
age       age
email     email
```

Usually:

```text
Instance-specific data
→ object itself

Shared methods
→ prototype
```

---

# 36. `constructor` Property

By default:

```javascript
User.prototype
```

contains a property:

```javascript
constructor
```

which refers back to:

```javascript
User
```

Example:

```javascript
function User() {
}

console.log(
    User.prototype.constructor
        === User
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
 └── prototype
       │
       └── constructor
              │
              └────► User
```

---

# 37. Replacing `.prototype`

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

Now the original default prototype object has been replaced.

The new object does not automatically have:

```javascript
constructor === User
```

Instead, because it inherits from `Object.prototype`, accessing:

```javascript
User.prototype.constructor
```

may resolve to:

```javascript
Object
```

If preserving the constructor reference matters:

```javascript
User.prototype = {
    constructor: User,

    greet() {
        console.log("Hello");
    }
};
```

In modern code, classes often make these patterns easier to manage.

---

# 38. Existing Instances and Replacing `.prototype`

This is an important detail.

```javascript
function User() {
}

const user1 =
    new User();

User.prototype = {
    greet() {
        console.log("Hello");
    }
};

const user2 =
    new User();
```

Now:

```text
user1
→ points to OLD User.prototype object

user2
→ points to NEW User.prototype object
```

Replacing:

```javascript
User.prototype
```

does not retroactively change the prototype of existing instances.

---

# 39. Modifying Existing `.prototype`

Different situation:

```javascript
function User() {
}

const user1 =
    new User();

User.prototype.greet =
    function () {
        console.log("Hello");
    };
```

Now:

```javascript
user1.greet();
```

works.

Why?

`user1` already points to the same prototype object.

We modified that object rather than replacing it.

---

# 40. Replacing vs Modifying Prototype

Modifying:

```javascript
User.prototype.greet =
    function () {};
```

Conceptually:

```text
User.prototype
│
└── add greet()
```

Existing instances still reference that same object.

Replacing:

```javascript
User.prototype = {
    greet() {}
};
```

Conceptually:

```text
User.prototype
↓
different object
```

Existing instances still reference the previous object.

This distinction is important.

---

# 41. Prototype Chain with Constructor Functions

Example:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        console.log(
            `Hello ${this.name}`
        );
    };

const user =
    new User("John");
```

Prototype chain:

```text
user
│
├── name: "John"
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
├── toString()
├── valueOf()
├── ...
│
▼
null
```

---

# 42. Property Lookup with Constructor Functions

When:

```javascript
user.greet();
```

JavaScript searches:

```text
user
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
execute greet()
```

When:

```javascript
user.toString();
```

JavaScript searches:

```text
user
│
├── toString?
│     NO
│
▼
User.prototype
│
├── toString?
│     NO
│
▼
Object.prototype
│
├── toString?
│     YES
```

---

# 43. `instanceof`

The `instanceof` operator checks whether a constructor's `.prototype` object appears somewhere in an object's prototype chain.

Example:

```javascript
function User() {
}

const user =
    new User();
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

contains the exact object referenced by:

```javascript
User.prototype
```

---

# 44. How `instanceof` Works

Conceptually:

```javascript
object instanceof Constructor
```

asks:

```text
Does Constructor.prototype
appear in object's prototype chain?
```

Example:

```javascript
user instanceof User
```

JavaScript checks:

```text
user.[[Prototype]]
      │
      ▼
User.prototype
```

Match:

```text
YES
```

Therefore:

```text
true
```

---

# 45. `instanceof Object`

Example:

```javascript
function User() {
}

const user =
    new User();
```

Then:

```javascript
console.log(
    user instanceof User
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    user instanceof Object
);
```

Output:

```text
true
```

Why?

Prototype chain:

```text
user
│
▼
User.prototype
│
▼
Object.prototype
│
▼
null
```

Both:

```text
User.prototype
Object.prototype
```

appear in the chain.

---

# 46. `isPrototypeOf()`

You can directly check whether an object appears in another object's prototype chain.

Example:

```javascript
const animal = {};

const dog =
    Object.create(animal);
```

Check:

```javascript
console.log(
    animal.isPrototypeOf(dog)
);
```

Output:

```text
true
```

Because:

```text
dog
│
▼
animal
```

---

# 47. `Object.prototype.isPrototypeOf()`

Example:

```javascript
const user = {};
```

Check:

```javascript
console.log(
    Object.prototype
        .isPrototypeOf(user)
);
```

Output:

```text
true
```

because:

```text
user
│
▼
Object.prototype
```

---

# 48. Prototype Chains Can Have Multiple Levels

Example:

```javascript
const animal = {
    eat() {
        console.log("Eating");
    }
};

const mammal =
    Object.create(animal);

mammal.breathe =
    function () {
        console.log(
            "Breathing"
        );
    };

const dog =
    Object.create(mammal);

dog.bark =
    function () {
        console.log(
            "Barking"
        );
    };
```

Now:

```javascript
dog.bark();
dog.breathe();
dog.eat();
```

Prototype chain:

```text
dog
│
├── bark()
│
▼
mammal
│
├── breathe()
│
▼
animal
│
├── eat()
│
▼
Object.prototype
│
▼
null
```

---

# 49. Prototype Chain Lookup

For:

```javascript
dog.eat();
```

JavaScript searches:

```text
dog
│
├── eat?
│     NO
│
▼
mammal
│
├── eat?
│     NO
│
▼
animal
│
├── eat?
│     YES
│
▼
execute
```

This is the core mechanism behind JavaScript inheritance.

---

# 50. Classes Use Prototypes

Consider:

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

The method:

```javascript
greet()
```

is placed on:

```javascript
User.prototype
```

not directly on:

```javascript
user
```

---

# 51. Checking Class Methods

Example:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

const user =
    new User();
```

Check:

```javascript
console.log(
    Object.hasOwn(
        user,
        "greet"
    )
);
```

Output:

```text
false
```

But:

```javascript
console.log(
    Object.hasOwn(
        User.prototype,
        "greet"
    )
);
```

Output:

```text
true
```

---

# 52. Class Prototype Chain

Example:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

const user =
    new User();
```

Prototype chain:

```text
user
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

This is why we say:

> JavaScript classes are built on top of the prototype system.

---

# 53. Classes Share Methods

Example:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

const user1 =
    new User();

const user2 =
    new User();
```

Check:

```javascript
console.log(
    user1.greet ===
    user2.greet
);
```

Output:

```text
true
```

Both instances inherit the same method from:

```javascript
User.prototype
```

---

# 54. Class Fields Are Different

Example:

```javascript
class User {
    active = true;

    greet() {
        console.log("Hello");
    }
}
```

Create:

```javascript
const user =
    new User();
```

Then:

```javascript
Object.hasOwn(
    user,
    "active"
);
```

Output:

```text
true
```

But:

```javascript
Object.hasOwn(
    user,
    "greet"
);
```

Output:

```text
false
```

Why?

```text
active
→ instance field
→ own property

greet()
→ prototype method
→ inherited property
```

---

# 55. Class Arrow Fields Are Own Properties

Example:

```javascript
class User {
    greet = () => {
        console.log("Hello");
    };
}

const user1 =
    new User();

const user2 =
    new User();
```

Check:

```javascript
Object.hasOwn(
    user1,
    "greet"
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    user1.greet ===
    user2.greet
);
```

Output:

```text
false
```

Each instance receives its own arrow function.

---

# 56. Class Prototype Method vs Arrow Field

Prototype method:

```javascript
class User {
    greet() {
    }
}
```

Conceptually:

```text
user1 ───┐
         ▼
 User.prototype
         │
         └── greet()
         ▲
user2 ───┘
```

Arrow field:

```javascript
class User {
    greet = () => {};
}
```

Conceptually:

```text
user1
└── greet → Function A

user2
└── greet → Function B
```

---

# 57. Class Inheritance and Prototypes

Example:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

class Admin extends User {
    deleteUser() {
        console.log(
            "User deleted"
        );
    }
}

const admin =
    new Admin();
```

Prototype chain:

```text
admin
│
▼
Admin.prototype
│
├── deleteUser()
│
▼
User.prototype
│
├── greet()
│
▼
Object.prototype
│
▼
null
```

This is how:

```javascript
admin.greet();
```

works.

---

# 58. `extends` Creates Prototype Relationships

With:

```javascript
class Admin extends User {
}
```

JavaScript establishes relationships including:

```text
Admin.prototype
│
│ [[Prototype]]
▼
User.prototype
```

Therefore:

```javascript
Object.getPrototypeOf(
    Admin.prototype
) === User.prototype
```

is:

```text
true
```

There is also a constructor-side inheritance relationship:

```javascript
Object.getPrototypeOf(Admin)
    === User
```

is normally:

```text
true
```

This helps static members participate in inheritance as well.

---

# 59. Inherited Class Methods Use Child Instance as `this`

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
    constructor(name) {
        super();

        this.name = name;
    }
}

const admin =
    new Admin("John");

admin.greet();
```

`greet()` exists on:

```javascript
User.prototype
```

but:

```text
this → admin
```

because the call is:

```javascript
admin.greet();
```

---

# 60. Method Overriding Is Prototype Shadowing

Example:

```javascript
class User {
    greet() {
        return "User";
    }
}

class Admin extends User {
    greet() {
        return "Admin";
    }
}
```

Prototype chain:

```text
admin
│
▼
Admin.prototype
│
├── greet() → "Admin"
│
▼
User.prototype
│
├── greet() → "User"
```

When:

```javascript
admin.greet();
```

JavaScript finds:

```javascript
Admin.prototype.greet
```

first.

Therefore:

```text
Admin
```

This is essentially property shadowing within a prototype chain.

---

# 61. `super` and Prototype Lookup

Example:

```javascript
class User {
    greet() {
        return "Hello";
    }
}

class Admin extends User {
    greet() {
        return (
            super.greet() +
            " Admin"
        );
    }
}
```

When:

```javascript
super.greet()
```

is used inside `Admin`, method lookup begins from the parent prototype.

Conceptually:

```text
Admin.prototype
      │
      ▼
User.prototype
      │
      └── greet()
```

The current instance remains the `this` value.

---

# 62. Built-In Objects Use Prototypes

JavaScript built-ins also use prototype chains.

Examples:

```text
Array
String
Function
Date
Map
Set
RegExp
Promise
Error
```

Their instances inherit methods from corresponding prototype objects.

---

# 63. Array Prototypes

Example:

```javascript
const numbers = [
    1,
    2,
    3
];

numbers.map(
    (number) =>
        number * 2
);
```

Where does:

```javascript
map()
```

come from?

Not from the individual array itself.

It comes from:

```javascript
Array.prototype
```

Conceptually:

```text
numbers
│
├── 0: 1
├── 1: 2
├── 2: 3
│
▼
Array.prototype
│
├── map()
├── filter()
├── reduce()
├── push()
├── pop()
├── forEach()
├── ...
│
▼
Object.prototype
│
▼
null
```

---

# 64. Checking Array Prototype

Example:

```javascript
const numbers = [
    1,
    2,
    3
];
```

Check:

```javascript
console.log(
    Object.getPrototypeOf(
        numbers
    ) === Array.prototype
);
```

Output:

```text
true
```

And:

```javascript
console.log(
    Object.getPrototypeOf(
        Array.prototype
    ) === Object.prototype
);
```

Output:

```text
true
```

---

# 65. Array Method Sharing

Create:

```javascript
const a = [];
const b = [];
```

Check:

```javascript
console.log(
    a.map === b.map
);
```

Output:

```text
true
```

Both arrays inherit:

```javascript
map
```

from:

```javascript
Array.prototype
```

---

# 66. Why Arrays Can Use Object Methods

Arrays ultimately inherit from:

```javascript
Object.prototype
```

Prototype chain:

```text
array
│
▼
Array.prototype
│
▼
Object.prototype
│
▼
null
```

Therefore arrays can access suitable inherited methods from both:

```text
Array.prototype
Object.prototype
```

---

# 67. Function Prototypes

Functions are also objects.

Example:

```javascript
function greet() {
}
```

Check:

```javascript
console.log(
    typeof greet
);
```

Output:

```text
function
```

But functions can use methods such as:

```javascript
greet.call(...)
greet.apply(...)
greet.bind(...)
```

Where do these come from?

```javascript
Function.prototype
```

---

# 68. Function Prototype Chain

Example:

```javascript
function greet() {
}
```

Conceptually:

```text
greet
│
▼
Function.prototype
│
├── call()
├── apply()
├── bind()
├── ...
│
▼
Object.prototype
│
▼
null
```

That is why:

```javascript
greet.call(...)
```

works.

---

# 69. Functions Are Objects

Functions can have properties:

```javascript
function greet() {
}

greet.description =
    "Greeting function";

console.log(
    greet.description
);
```

Output:

```text
Greeting function
```

Functions participate in prototype chains because functions are specialized callable objects.

---

# 70. Do Not Confuse Two Function Prototype Relationships

For:

```javascript
function User() {
}
```

there are two different concepts.

### Prototype of the function object

```javascript
Object.getPrototypeOf(User)
```

normally gives:

```javascript
Function.prototype
```

So:

```text
User
│
▼
Function.prototype
│
▼
Object.prototype
│
▼
null
```

### The `prototype` property of the function

```javascript
User.prototype
```

is the object used for instances created with:

```javascript
new User()
```

So:

```text
user
│
▼
User.prototype
│
▼
Object.prototype
│
▼
null
```

These two chains are different.

---

# 71. Important Diagram: Constructor and Instance

Consider:

```javascript
function User(name) {
    this.name = name;
}

const user =
    new User("John");
```

Conceptually:

```text
                User
                 │
                 │ .prototype
                 ▼
          User.prototype
                 ▲
                 │
                 │ [[Prototype]]
                 │
               user
```

Separately:

```text
User
│
│ [[Prototype]]
▼
Function.prototype
│
▼
Object.prototype
│
▼
null
```

And:

```text
User.prototype
│
│ [[Prototype]]
▼
Object.prototype
│
▼
null
```

---

# 72. String Prototypes

Primitive strings can use methods:

```javascript
const name = "john";

console.log(
    name.toUpperCase()
);
```

Output:

```text
JOHN
```

The method is associated with:

```javascript
String.prototype
```

JavaScript temporarily provides object-like behavior for primitives so these methods can be used.

Conceptually:

```text
"john"
│
▼
String.prototype
│
├── toUpperCase()
├── slice()
├── includes()
├── ...
│
▼
Object.prototype
│
▼
null
```

Do not interpret this as the primitive string permanently storing those methods itself.

---

# 73. Number Prototypes

Example:

```javascript
const number = 10;

console.log(
    number.toFixed(2)
);
```

Output:

```text
10.00
```

`toFixed()` is provided through:

```javascript
Number.prototype
```

Conceptually:

```text
number behavior
│
▼
Number.prototype
│
▼
Object.prototype
│
▼
null
```

---

# 74. Boolean Prototypes

Boolean values can use methods associated with:

```javascript
Boolean.prototype
```

Example:

```javascript
const value = true;

console.log(
    value.toString()
);
```

Output:

```text
true
```

---

# 75. Primitive vs Wrapper Objects

JavaScript has primitives:

```javascript
"hello"
10
true
```

and wrapper constructors:

```javascript
String
Number
Boolean
```

You generally should not create wrapper objects manually:

```javascript
new String("hello");
new Number(10);
new Boolean(true);
```

Prefer primitives:

```javascript
"hello"
10
true
```

JavaScript handles access to prototype methods automatically when needed.

---

# 76. Date Prototype

Example:

```javascript
const date =
    new Date();

date.getFullYear();
```

`getFullYear()` is inherited from:

```javascript
Date.prototype
```

Prototype chain:

```text
date
│
▼
Date.prototype
│
▼
Object.prototype
│
▼
null
```

---

# 77. Promise Prototype

Example:

```javascript
const promise =
    Promise.resolve(10);

promise.then(
    (value) => {
        console.log(value);
    }
);
```

Methods such as:

```text
then()
catch()
finally()
```

are available through:

```javascript
Promise.prototype
```

---

# 78. `Object.create(null)`

Normally:

```javascript
const object = {};
```

has:

```text
Object.prototype
```

But:

```javascript
const object =
    Object.create(null);
```

creates an object with:

```text
[[Prototype]] → null
```

Check:

```javascript
console.log(
    Object.getPrototypeOf(
        object
    )
);
```

Output:

```text
null
```

---

# 79. Null-Prototype Objects

Example:

```javascript
const dictionary =
    Object.create(null);

dictionary.name = "John";
```

Prototype chain:

```text
dictionary
│
├── name: "John"
│
▼
null
```

Therefore:

```javascript
dictionary.toString
```

is:

```text
undefined
```

because there is no:

```javascript
Object.prototype
```

in the chain.

---

# 80. Why Use `Object.create(null)`?

It can be useful for dictionary-like objects where you want no inherited keys.

Example:

```javascript
const dictionary =
    Object.create(null);

dictionary.admin = true;
dictionary.user = true;
```

There is no inherited:

```text
toString
constructor
hasOwnProperty
```

However, modern JavaScript often uses:

```javascript
Map
```

when a dedicated key-value collection is more appropriate.

---

# 81. `for...in` and Prototypes

`for...in` iterates over enumerable string properties, including inherited enumerable properties.

Example:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.name = "John";

for (const key in child) {
    console.log(key);
}
```

Output can include:

```text
name
role
```

because:

```text
name
→ own enumerable property

role
→ inherited enumerable property
```

---

# 82. Filtering `for...in`

If you only want own properties:

```javascript
for (const key in child) {
    if (
        Object.hasOwn(
            child,
            key
        )
    ) {
        console.log(key);
    }
}
```

Output:

```text
name
```

---

# 83. `Object.keys()` and Prototypes

`Object.keys()` returns own enumerable string-keyed properties.

Example:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.name = "John";

console.log(
    Object.keys(child)
);
```

Output:

```javascript
["name"]
```

It does not include inherited:

```text
role
```

---

# 84. Spread and Prototype Properties

Consider:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.name = "John";
```

Now:

```javascript
const copy = {
    ...child
};
```

`copy` gets the relevant own enumerable properties from `child`.

So:

```javascript
console.log(copy);
```

Result:

```javascript
{
    name: "John"
}
```

Inherited:

```text
role
```

is not copied by object spread.

---

# 85. Prototype Mutation Affects Inheriting Objects

Example:

```javascript
const parent = {};

const child1 =
    Object.create(parent);

const child2 =
    Object.create(parent);
```

Later:

```javascript
parent.greet =
    function () {
        console.log("Hello");
    };
```

Now:

```javascript
child1.greet();
child2.greet();
```

both work.

Why?

Both objects reference the same prototype:

```text
child1 ───┐
          ▼
        parent
          ▲
child2 ───┘
```

---

# 86. Own Property Overrides Prototype Changes

Example:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.role = "admin";
```

Now change:

```javascript
parent.role = "guest";
```

Then:

```javascript
console.log(
    child.role
);
```

Output:

```text
admin
```

because the own property shadows the prototype property.

---

# 87. Deleting Prototype Properties

Example:

```javascript
const parent = {
    greet() {
        console.log("Hello");
    }
};

const child =
    Object.create(parent);

child.greet();
```

Works.

Now:

```javascript
delete parent.greet;
```

Then:

```javascript
child.greet();
```

fails because the property no longer exists in the prototype chain.

---

# 88. Avoid Modifying Built-In Prototypes

Technically:

```javascript
Array.prototype.first =
    function () {
        return this[0];
    };
```

Then:

```javascript
const numbers = [
    10,
    20,
    30
];

console.log(
    numbers.first()
);
```

Output:

```text
10
```

But modifying built-in prototypes is usually a bad idea.

Avoid modifying:

```text
Array.prototype
Object.prototype
String.prototype
Function.prototype
```

in normal application code.

---

# 89. Why Modifying Built-In Prototypes Is Risky

It can cause:

```text
Naming collisions
Unexpected behavior
Library conflicts
Iteration problems
Maintenance issues
Compatibility issues
Security problems
```

For example:

```javascript
Array.prototype.someMethod =
    function () {};
```

A future JavaScript version or library could introduce a method with the same name.

Prefer:

```javascript
function first(array) {
    return array[0];
}
```

or your own classes/utilities.

---

# 90. Prototype Pollution

Prototype pollution is a security problem where unsafe code allows attacker-controlled properties to modify shared prototypes or prototype-bearing objects.

Conceptually, dangerous code may accidentally allow something equivalent to modifying:

```javascript
Object.prototype
```

If a property is added there:

```text
Object.prototype
│
└── dangerousProperty
       ▲
       │
   many objects
```

Many objects may appear to inherit it.

---

# 91. Why Prototype Pollution Is Dangerous

Suppose shared prototype state is polluted:

```javascript
Object.prototype.isAdmin =
    true;
```

Then:

```javascript
const user = {
    name: "John"
};

console.log(
    user.isAdmin
);
```

Output:

```text
true
```

even though `user` never had its own:

```javascript
isAdmin
```

property.

Do **not** use this as an actual authorization pattern; it simply demonstrates why trusting inherited properties can be dangerous.

Security-sensitive checks should verify expected data explicitly.

For example:

```javascript
Object.hasOwn(
    user,
    "isAdmin"
);
```

---

# 92. Safer Property Checks

Instead of blindly assuming:

```javascript
if (config.isAdmin) {
}
```

understand whether inherited properties should count.

When only own data is valid:

```javascript
if (
    Object.hasOwn(
        config,
        "isAdmin"
    ) &&
    config.isAdmin === true
) {
    // ...
}
```

Prototype pollution defenses also require safe parsing, merging, validation, and library practices; `Object.hasOwn()` alone is not a complete defense.

---

# 93. Prototype Interview Example 1

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    user.toString
        ===
    Object.prototype.toString
);
```

Answer:

```text
true
```

because `user` inherits `toString` from:

```javascript
Object.prototype
```

---

# 94. Prototype Interview Example 2

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    Object.hasOwn(
        user,
        "toString"
    )
);
```

Answer:

```text
false
```

`toString` is inherited.

---

# 95. Prototype Interview Example 3

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    "toString" in user
);
```

Answer:

```text
true
```

The `in` operator searches the prototype chain.

---

# 96. Prototype Interview Example 4

What is the output?

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

console.log(
    child.role
);
```

Answer:

```text
user
```

because `role` is inherited.

---

# 97. Prototype Interview Example 5

What is the output?

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.role = "admin";

console.log(
    child.role
);
```

Answer:

```text
admin
```

The own property shadows the inherited property.

---

# 98. Prototype Interview Example 6

What is the output?

```javascript
function User() {
}

const user =
    new User();

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

# 99. Prototype Interview Example 7

What is the output?

```javascript
function User() {
}

const user =
    new User();

console.log(
    user instanceof User
);
```

Answer:

```text
true
```

because:

```javascript
User.prototype
```

exists in `user`'s prototype chain.

---

# 100. Prototype Interview Example 8

What is the output?

```javascript
function User() {
}

const user =
    new User();

console.log(
    user instanceof Object
);
```

Answer:

```text
true
```

because:

```javascript
Object.prototype
```

also exists in the prototype chain.

---

# 101. Prototype Interview Example 9

What is the output?

```javascript
class User {
    greet() {
        return "Hello";
    }
}

const user1 =
    new User();

const user2 =
    new User();

console.log(
    user1.greet ===
    user2.greet
);
```

Answer:

```text
true
```

Both instances inherit the same:

```javascript
User.prototype.greet
```

---

# 102. Prototype Interview Example 10

What is the output?

```javascript
class User {
    greet = () => {
        return "Hello";
    };
}

const user1 =
    new User();

const user2 =
    new User();

console.log(
    user1.greet ===
    user2.greet
);
```

Answer:

```text
false
```

Each instance gets its own function.

---

# 103. Prototype Interview Example 11

What is the output?

```javascript
const parent = {
    value: 10
};

const child =
    Object.create(parent);

child.value = 20;

delete child.value;

console.log(
    child.value
);
```

Answer:

```text
10
```

After deleting the own property, lookup reaches the prototype.

---

# 104. Prototype Interview Example 12

What is the output?

```javascript
function User() {
}

const user =
    new User();

User.prototype.greet =
    function () {
        return "Hello";
    };

console.log(
    user.greet()
);
```

Answer:

```text
Hello
```

The existing instance still references the same `User.prototype` object that was modified.

---

# 105. Prototype Interview Example 13

What happens here?

```javascript
function User() {
}

const user1 =
    new User();

User.prototype = {
    greet() {
        return "Hello";
    }
};

const user2 =
    new User();
```

Now:

```javascript
console.log(
    typeof user1.greet
);
```

Output:

```text
undefined
```

But:

```javascript
console.log(
    user2.greet()
);
```

Output:

```text
Hello
```

because:

```text
user1 → old prototype
user2 → new prototype
```

---

# 106. Prototype Interview Example 14

What is the output?

```javascript
const array = [];

console.log(
    Object.getPrototypeOf(array)
        === Array.prototype
);
```

Answer:

```text
true
```

---

# 107. Prototype Interview Example 15

What is the output?

```javascript
const array = [];

console.log(
    array instanceof Array
);

console.log(
    array instanceof Object
);
```

Answer:

```text
true
true
```

Prototype chain:

```text
array
↓
Array.prototype
↓
Object.prototype
↓
null
```

---

# 108. Prototype Interview Example 16

What is the output?

```javascript
function greet() {
}

console.log(
    Object.getPrototypeOf(greet)
        === Function.prototype
);
```

Answer:

```text
true
```

---

# 109. Prototype Interview Example 17

What is the output?

```javascript
const object =
    Object.create(null);

console.log(
    Object.getPrototypeOf(
        object
    )
);
```

Answer:

```text
null
```

---

# 110. Prototype Interview Example 18

What is the output?

```javascript
const parent = {
    value: 10
};

const child =
    Object.create(parent);

console.log(
    Object.hasOwn(
        child,
        "value"
    )
);

console.log(
    "value" in child
);
```

Answer:

```text
false
true
```

Because:

```text
value
→ inherited property
```

---

# 111. Prototype Interview Example 19

What is the output?

```javascript
class User {
}

class Admin extends User {
}

const admin =
    new Admin();

console.log(
    Object.getPrototypeOf(
        Admin.prototype
    ) === User.prototype
);
```

Answer:

```text
true
```

---

# 112. Prototype Interview Example 20

What is the output?

```javascript
class User {
}

class Admin extends User {
}

const admin =
    new Admin();

console.log(
    admin instanceof Admin
);

console.log(
    admin instanceof User
);

console.log(
    admin instanceof Object
);
```

Answer:

```text
true
true
true
```

Because the chain contains:

```text
Admin.prototype
User.prototype
Object.prototype
```

---

# 113. Common Mistake: Confusing `.prototype` with `[[Prototype]]`

This:

```javascript
User.prototype
```

is a property on the constructor.

This:

```javascript
Object.getPrototypeOf(user)
```

returns the object's internal prototype.

When:

```javascript
const user =
    new User();
```

the connection is:

```javascript
Object.getPrototypeOf(user)
    === User.prototype
```

---

# 114. Common Mistake: Thinking Every Object Has `.prototype`

Example:

```javascript
const user = {};
```

Typically:

```javascript
user.prototype
```

is:

```text
undefined
```

Ordinary objects do not automatically have a meaningful `.prototype` property.

Constructor functions/classes expose `.prototype` for constructing instances.

But every ordinary object has an internal:

```text
[[Prototype]]
```

which can be inspected using:

```javascript
Object.getPrototypeOf(user)
```

---

# 115. Common Mistake: Thinking Prototype Methods Are Copied

Consider:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

const user =
    new User();
```

JavaScript does not normally copy:

```javascript
greet()
```

onto `user`.

Instead:

```text
user
│
▼
User.prototype
│
└── greet()
```

The method is found through lookup.

---

# 116. Common Mistake: Thinking Prototype Means Parent Class

Prototype inheritance exists even without classes.

Example:

```javascript
const parent = {
    greet() {
        console.log("Hello");
    }
};

const child =
    Object.create(parent);

child.greet();
```

No class exists.

Yet inheritance works through:

```text
[[Prototype]]
```

Classes are one syntax for working with the underlying prototype system.

---

# 117. Common Mistake: Mutating Built-In Prototypes

Avoid:

```javascript
Object.prototype.foo =
    "bar";
```

because now many ordinary objects may appear to contain:

```javascript
foo
```

Example:

```javascript
const user = {};

console.log(user.foo);
```

could produce:

```text
bar
```

This creates global side effects and can cause serious bugs.

---

# 118. Common Mistake: Assuming `for...in` Means Own Properties

Example:

```javascript
const parent = {
    role: "user"
};

const child =
    Object.create(parent);

child.name = "John";
```

Then:

```javascript
for (const key in child) {
    console.log(key);
}
```

can include inherited enumerable properties.

If own properties are required:

```javascript
if (
    Object.hasOwn(
        child,
        key
    )
) {
}
```

---

# 119. Prototype Mental Model

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        return (
            `Hello ${this.name}`
        );
    }
}

const user =
    new User("John");
```

Think:

```text
          ┌─────────────────────┐
          │        user         │
          │                     │
          │ name: "John"        │
          └──────────┬──────────┘
                     │
               [[Prototype]]
                     │
                     ▼
          ┌─────────────────────┐
          │   User.prototype    │
          │                     │
          │ greet()             │
          │ constructor         │
          └──────────┬──────────┘
                     │
               [[Prototype]]
                     │
                     ▼
          ┌─────────────────────┐
          │ Object.prototype    │
          │                     │
          │ toString()          │
          │ valueOf()           │
          │ ...                 │
          └──────────┬──────────┘
                     │
               [[Prototype]]
                     │
                     ▼
                    null
```

When:

```javascript
user.greet();
```

lookup:

```text
user
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
Execute with this = user
```

---

# 120. Complete Constructor Mental Model

Code:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        return (
            `Hello ${this.name}`
        );
    };

const user =
    new User("John");
```

Relationships:

```text
                User
                 │
                 │ .prototype
                 ▼
        ┌──────────────────┐
        │ User.prototype   │
        │                  │
        │ greet()          │
        │ constructor      │
        └────────┬─────────┘
                 ▲
                 │
           [[Prototype]]
                 │
        ┌────────┴─────────┐
        │       user       │
        │                  │
        │ name: "John"     │
        └──────────────────┘
```

And:

```text
User.prototype
      │
      ▼
Object.prototype
      │
      ▼
null
```

Meanwhile:

```text
User
      │
      ▼
Function.prototype
      │
      ▼
Object.prototype
      │
      ▼
null
```

This diagram explains much of JavaScript's object model.

---

# 121. Prototype Cheat Sheet

```javascript
// Get prototype

Object.getPrototypeOf(
    object
);
```

```javascript
// Create object with prototype

const child =
    Object.create(parent);
```

```javascript
// Check own property

Object.hasOwn(
    object,
    "property"
);
```

```javascript
// Check own + inherited

"property" in object;
```

```javascript
// Check prototype relationship

parent.isPrototypeOf(
    child
);
```

```javascript
// Constructor prototype

function User() {
}

User.prototype;
```

```javascript
// Instance prototype

const user =
    new User();

Object.getPrototypeOf(
    user
);
```

```javascript
// Relationship

Object.getPrototypeOf(user)
    === User.prototype;
```

```javascript
// Add shared method

User.prototype.greet =
    function () {
        console.log("Hello");
    };
```

```javascript
// Check constructor relationship

user instanceof User;
```

---

# 122. Prototype Chain Cheat Sheet

### Plain Object

```text
object
↓
Object.prototype
↓
null
```

### Array

```text
array
↓
Array.prototype
↓
Object.prototype
↓
null
```

### Function

```text
function
↓
Function.prototype
↓
Object.prototype
↓
null
```

### Class Instance

```text
user
↓
User.prototype
↓
Object.prototype
↓
null
```

### Inherited Class Instance

```text
admin
↓
Admin.prototype
↓
User.prototype
↓
Object.prototype
↓
null
```

### Null-Prototype Object

```text
object
↓
null
```

---

# 123. Important Rules to Remember

```text
1. JavaScript is prototype-based.

2. Objects can inherit properties from other objects.

3. Every ordinary object has an internal [[Prototype]].

4. Object.getPrototypeOf() retrieves an object's prototype.

5. Object.create() creates an object with a chosen prototype.

6. Prototypes can have prototypes.

7. This forms the prototype chain.

8. Property lookup begins on the object itself.

9. If not found, lookup continues through prototypes.

10. Lookup stops when the property is found.

11. The prototype chain eventually ends at null.

12. Object.prototype is near the top of most ordinary object chains.

13. Own properties exist directly on an object.

14. Inherited properties come through the prototype chain.

15. Object.hasOwn() checks only own properties.

16. The in operator checks own and inherited properties.

17. An own property can shadow an inherited property.

18. Deleting the own property can reveal the inherited property again.

19. Object.setPrototypeOf() can change an object's prototype.

20. Frequently changing prototypes is generally discouraged.

21. __proto__ is legacy-style prototype access.

22. Prefer Object.getPrototypeOf() for inspection.

23. Constructor functions have a .prototype property.

24. .prototype and [[Prototype]] are different concepts.

25. new connects the new object's [[Prototype]] to Constructor.prototype.

26. Shared methods can be stored on Constructor.prototype.

27. Prototype methods are not copied to every instance.

28. Multiple instances can share the same prototype method.

29. Instance data usually lives directly on each instance.

30. User.prototype.constructor normally refers to User.

31. Replacing .prototype differs from modifying the existing prototype object.

32. Existing instances keep their existing prototype object.

33. instanceof checks whether Constructor.prototype appears in an object's prototype chain.

34. isPrototypeOf() checks whether one object appears in another object's prototype chain.

35. Classes use prototypes internally.

36. Class methods normally live on Class.prototype.

37. Class fields normally become own instance properties.

38. Arrow class fields are own properties, not prototype methods.

39. extends establishes prototype inheritance.

40. Method overriding works through property lookup and shadowing.

41. super can access behavior higher in the inheritance structure.

42. Arrays inherit methods from Array.prototype.

43. map(), filter(), reduce(), push(), etc. are shared through Array.prototype.

44. Functions inherit call(), apply(), and bind() from Function.prototype.

45. Built-in objects use prototype chains.

46. Object.create(null) creates an object with no prototype.

47. for...in can include inherited enumerable properties.

48. Object.keys() returns own enumerable string-keyed properties.

49. Object spread does not copy inherited properties.

50. Avoid modifying built-in prototypes.

51. Prototype pollution can create security vulnerabilities.

52. Prototypes explain how JavaScript implements inheritance.

53. Understanding prototypes makes classes much easier to understand internally.
```

---

# 124. The Most Important Distinction

This is the part you should remember before moving forward:

```javascript
function User() {
}
```

There are **two different prototype relationships**.

### `User.prototype`

```javascript
User.prototype
```

is an object used for instances created by:

```javascript
new User();
```

Relationship:

```text
user
│
│ [[Prototype]]
▼
User.prototype
```

Therefore:

```javascript
Object.getPrototypeOf(user)
    === User.prototype;
```

---

### Prototype of `User` itself

`User` is also a function object.

Its own prototype is:

```javascript
Function.prototype
```

Relationship:

```text
User
│
│ [[Prototype]]
▼
Function.prototype
│
▼
Object.prototype
│
▼
null
```

So:

```javascript
Object.getPrototypeOf(User)
    === Function.prototype;
```

---

### Put Together

```text
                   User
                    │
          .prototype│
                    ▼
             User.prototype
                    ▲
                    │ [[Prototype]]
                    │
                   user


Meanwhile:


User
 │
 │ [[Prototype]]
 ▼
Function.prototype
 │
 ▼
Object.prototype
 │
 ▼
null


And:


User.prototype
 │
 │ [[Prototype]]
 ▼
Object.prototype
 │
 ▼
null
```

Do not confuse:

```text
Constructor.prototype
```

with:

```text
Object's [[Prototype]]
```

They are different concepts connected by `new`.

---

# 125. Connecting Classes to Prototypes

When you write:

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

JavaScript conceptually creates a prototype relationship similar to:

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        console.log(
            `Hello ${this.name}`
        );
    };
```

These are not identical in every language-level detail, but they share the same core prototype model.

Both lead to:

```text
user
│
├── name
│
▼
User.prototype
│
├── greet()
│
▼
Object.prototype
│
▼
null
```

This is why classes are often described as a cleaner syntax over JavaScript's prototype-based object system.

---
