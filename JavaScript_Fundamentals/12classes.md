#  Classes

A **class** in JavaScript is a syntax for defining how objects of a particular kind should be created and what behavior they should have.

Example:

```javascript
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(`Hello, I am ${this.name}`);
    }
}

const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
```

Here:

```text
User
│
├── constructor()
│      ├── name
│      └── age
│
└── greet()
```

And:

```text
User class
   │
   ├──── new User("John", 25)
   │            ↓
   │          user1
   │
   └──── new User("Alice", 30)
                ↓
              user2
```

Classes are used when we want multiple objects that share similar:

```text
Properties
Methods
Behavior
Structure
```

---

# 1. Why Do We Need Classes?

Suppose we create multiple users manually:

```javascript
const user1 = {
    name: "John",
    age: 25,

    greet() {
        console.log(`Hello, I am ${this.name}`);
    }
};

const user2 = {
    name: "Alice",
    age: 30,

    greet() {
        console.log(`Hello, I am ${this.name}`);
    }
};
```

There is repeated structure:

```text
name
age
greet()
```

A class lets us define that pattern once:

```javascript
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        console.log(`Hello, I am ${this.name}`);
    }
}
```

Then create as many users as needed:

```javascript
const user1 = new User("John", 25);
const user2 = new User("Alice", 30);
const user3 = new User("Bob", 35);
```

---

# 2. Basic Class Syntax

Syntax:

```javascript
class ClassName {
    constructor() {
        // initialization
    }

    methodName() {
        // behavior
    }
}
```

Example:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log("Hello");
    }
}
```

By convention, class names use **PascalCase**:

```javascript
class User {}
class BankAccount {}
class ShoppingCart {}
class ProductService {}
```

---

# 3. A Class Is Not an Object Instance

This:

```javascript
class User {
}
```

defines a class.

But this:

```javascript
const user = new User();
```

creates an object from that class.

Terminology:

```text
Class
→ definition/template for creating instances

Instance
→ object created from the class
```

Example:

```javascript
class User {
}

const user1 = new User();
const user2 = new User();
```

Here:

```text
User
→ class

user1
→ instance

user2
→ instance
```

---

# 4. The `new` Keyword

Objects are normally created from classes using:

```javascript
new
```

Example:

```javascript
class User {
}

const user = new User();
```

Without `new`:

```javascript
const user = User();
```

you get an error because JavaScript classes cannot be invoked like regular functions.

---

# 5. What `new` Does

When we write:

```javascript
const user = new User("John", 25);
```

conceptually JavaScript performs steps similar to:

```text
1. Create a new object

2. Connect that object's prototype
   to User.prototype

3. Run User's constructor
   with this referring to the new object

4. Return the created instance
```

Conceptually:

```text
new User("John", 25)

        ↓

Create object

        ↓

Connect prototype

        ↓

constructor("John", 25)

        ↓

this.name = "John"
this.age = 25

        ↓

Return object
```

---

# 6. Constructor

The `constructor` is a special method used to initialize a newly created instance.

```javascript
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

Usage:

```javascript
const user = new User(
    "John",
    25
);
```

JavaScript calls:

```javascript
constructor("John", 25);
```

automatically during `new User(...)`.

---

# 7. Constructor Parameters

Constructors can accept parameters.

```javascript
class Product {
    constructor(name, price) {
        this.name = name;
        this.price = price;
    }
}
```

Create objects:

```javascript
const product1 =
    new Product(
        "Laptop",
        1000
    );

const product2 =
    new Product(
        "Phone",
        500
    );
```

Result:

```text
product1
│
├── name  → "Laptop"
└── price → 1000


product2
│
├── name  → "Phone"
└── price → 500
```

---

# 8. Understanding `this` in the Constructor

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

When:

```javascript
const user =
    new User("John");
```

during construction:

```text
this
↓
newly created user object
```

Therefore:

```javascript
this.name = name;
```

means conceptually:

```javascript
user.name = "John";
```

Result:

```javascript
{
    name: "John"
}
```

---

# 9. Instance Properties

Properties stored on each created object are called **instance properties**.

```javascript
class User {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

Create:

```javascript
const user1 =
    new User("John", 25);

const user2 =
    new User("Alice", 30);
```

Each instance has its own property values:

```text
user1
├── name → John
└── age  → 25


user2
├── name → Alice
└── age  → 30
```

Changing one does not automatically change the other:

```javascript
user1.name = "Bob";

console.log(user2.name);
```

Output:

```text
Alice
```

---

# 10. Instance Methods

Methods defined in the class can be called through instances.

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(
            `Hello, ${this.name}`
        );
    }
}
```

Usage:

```javascript
const user =
    new User("John");

user.greet();
```

Output:

```text
Hello, John
```

---

# 11. Class Methods Are Shared Through the Prototype

Consider:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}
```

Create:

```javascript
const user1 = new User();
const user2 = new User();
```

The `greet()` method is not normally copied separately onto every object.

It exists on:

```javascript
User.prototype
```

Conceptually:

```text
user1 ──────┐
            │
            ▼
      User.prototype
            │
            └── greet()
            ▲
            │
user2 ──────┘
```

We will study prototypes separately, but this is essential:

> JavaScript classes are built on JavaScript's prototype system.

---

# 12. Checking Where a Method Exists

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

const user = new User();
```

Check:

```javascript
console.log(
    Object.hasOwn(user, "greet")
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

The method is inherited through the prototype chain.

---

# 13. Multiple Methods

A class can contain multiple methods.

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

    login() {
        console.log(
            `${this.name} logged in`
        );
    }

    logout() {
        console.log(
            `${this.name} logged out`
        );
    }
}
```

Usage:

```javascript
const user =
    new User("John");

user.greet();
user.login();
user.logout();
```

---

# 14. No Commas Between Class Methods

Object:

```javascript
const user = {
    greet() {
        console.log("Hello");
    },

    logout() {
        console.log("Goodbye");
    }
};
```

Class:

```javascript
class User {
    greet() {
        console.log("Hello");
    }

    logout() {
        console.log("Goodbye");
    }
}
```

Do not write:

```javascript
class User {
    greet() {
    },

    logout() {
    }
}
```

Class methods are not separated by commas.

---

# 15. Default Constructor

If you do not define a constructor:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}
```

JavaScript provides a default constructor behavior.

For a base class, conceptually:

```javascript
constructor() {
}
```

Therefore:

```javascript
const user = new User();
```

works.

Derived classes have different default constructor behavior, which we will cover with inheritance.

---

# 16. Default Parameter Values

Constructor parameters can use default values.

```javascript
class User {
    constructor(
        name,
        role = "user"
    ) {
        this.name = name;
        this.role = role;
    }
}
```

Create:

```javascript
const user =
    new User("John");
```

Result:

```javascript
{
    name: "John",
    role: "user"
}
```

Or:

```javascript
const admin =
    new User(
        "Alice",
        "admin"
    );
```

Result:

```javascript
{
    name: "Alice",
    role: "admin"
}
```

---

# 17. Class Fields

Modern JavaScript supports **class fields**.

```javascript
class User {
    role = "user";
    active = true;

    constructor(name) {
        this.name = name;
    }
}
```

Create:

```javascript
const user =
    new User("John");
```

Result:

```text
name   → John
role   → user
active → true
```

These fields become instance properties.

---

# 18. Class Field Initializers

Fields can also use expressions:

```javascript
class User {
    active = true;
    loginCount = 0;
    createdAt = new Date();

    constructor(name) {
        this.name = name;
    }
}
```

Each instance receives its own field initialization when it is created.

---

# 19. Public Fields

Normal class fields are public.

```javascript
class User {
    name;
    age;

    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

Access:

```javascript
const user =
    new User("John", 25);

console.log(user.name);
console.log(user.age);
```

Output:

```text
John
25
```

They can also be modified:

```javascript
user.age = 26;
```

---

# 20. Private Fields

JavaScript supports private class fields using:

```text
#
```

Example:

```javascript
class BankAccount {
    #balance = 0;

    deposit(amount) {
        this.#balance += amount;
    }

    getBalance() {
        return this.#balance;
    }
}
```

Usage:

```javascript
const account =
    new BankAccount();

account.deposit(100);

console.log(
    account.getBalance()
);
```

Output:

```text
100
```

But:

```javascript
account.#balance;
```

is not allowed outside the class body.

---

# 21. Private Fields Are Truly Private

Consider:

```javascript
class User {
    #password;

    constructor(password) {
        this.#password = password;
    }

    checkPassword(password) {
        return (
            password ===
            this.#password
        );
    }
}
```

Usage:

```javascript
const user =
    new User("secret123");

console.log(
    user.checkPassword(
        "secret123"
    )
);
```

Output:

```text
true
```

But:

```javascript
user.#password;
```

causes a syntax error.

Private fields can only be referenced where the class declaration allows access to that private name.

---

# 22. Private Methods

Methods can also be private.

```javascript
class User {
    #validatePassword(password) {
        return password.length >= 8;
    }

    setPassword(password) {
        if (
            this.#validatePassword(
                password
            )
        ) {
            console.log(
                "Password accepted"
            );
        }
    }
}
```

The private method:

```javascript
#validatePassword()
```

cannot be called from outside the class.

---

# 23. Encapsulation

Encapsulation means keeping internal implementation details controlled and exposing only the operations that should be publicly available.

Example:

```javascript
class BankAccount {
    #balance = 0;

    deposit(amount) {
        if (amount > 0) {
            this.#balance += amount;
        }
    }

    withdraw(amount) {
        if (
            amount > 0 &&
            amount <= this.#balance
        ) {
            this.#balance -= amount;
        }
    }

    getBalance() {
        return this.#balance;
    }
}
```

External code interacts through:

```text
deposit()
withdraw()
getBalance()
```

instead of directly changing:

```text
#balance
```

---

# 24. Getters

A getter lets a method-like calculation be accessed like a property.

```javascript
class User {
    constructor(
        firstName,
        lastName
    ) {
        this.firstName =
            firstName;

        this.lastName =
            lastName;
    }

    get fullName() {
        return (
            `${this.firstName} ` +
            `${this.lastName}`
        );
    }
}
```

Usage:

```javascript
const user =
    new User(
        "John",
        "Doe"
    );

console.log(user.fullName);
```

Output:

```text
John Doe
```

Notice:

```javascript
user.fullName
```

not:

```javascript
user.fullName()
```

---

# 25. Setters

A setter executes logic when assigning to a property.

```javascript
class User {
    constructor() {
        this._age = 0;
    }

    set age(value) {
        if (value >= 0) {
            this._age = value;
        }
    }

    get age() {
        return this._age;
    }
}
```

Usage:

```javascript
const user =
    new User();

user.age = 25;

console.log(user.age);
```

Output:

```text
25
```

---

# 26. Getter and Setter with Private Fields

A cleaner pattern:

```javascript
class User {
    #age = 0;

    set age(value) {
        if (value >= 0) {
            this.#age = value;
        }
    }

    get age() {
        return this.#age;
    }
}
```

Usage:

```javascript
const user =
    new User();

user.age = 25;

console.log(user.age);
```

Output:

```text
25
```

Here:

```text
#age
→ private storage

age getter
→ controlled read

age setter
→ controlled write
```

---

# 27. Static Methods

A static method belongs to the **class itself**, not its instances.

```javascript
class MathHelper {
    static add(a, b) {
        return a + b;
    }
}
```

Usage:

```javascript
console.log(
    MathHelper.add(10, 20)
);
```

Output:

```text
30
```

But:

```javascript
const helper =
    new MathHelper();

helper.add(10, 20);
```

does not work because `add()` is not an instance method.

---

# 28. Static vs Instance Methods

```javascript
class User {
    greet() {
        console.log(
            "Instance method"
        );
    }

    static createGuest() {
        console.log(
            "Static method"
        );
    }
}
```

Usage:

```javascript
const user =
    new User();

user.greet();
```

And:

```javascript
User.createGuest();
```

Think:

```text
Instance Method
→ object.method()


Static Method
→ Class.method()
```

---

# 29. Static Fields

Classes can also have static fields.

```javascript
class User {
    static count = 0;

    constructor(name) {
        this.name = name;

        User.count++;
    }
}
```

Create:

```javascript
const user1 =
    new User("John");

const user2 =
    new User("Alice");

console.log(User.count);
```

Output:

```text
2
```

The field belongs to:

```text
User
```

not individual instances.

---

# 30. Static Private Fields

Static fields can also be private.

```javascript
class User {
    static #count = 0;

    constructor(name) {
        this.name = name;
        User.#count++;
    }

    static getCount() {
        return User.#count;
    }
}
```

Usage:

```javascript
new User("John");
new User("Alice");

console.log(
    User.getCount()
);
```

Output:

```text
2
```

But:

```javascript
User.#count;
```

is not allowed outside the class.

---

# 31. Inheritance

Inheritance allows one class to derive behavior from another class.

Example:

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

class Admin extends User {
}
```

Now:

```javascript
const admin =
    new Admin("John");

admin.greet();
```

Output:

```text
Hello John
```

`Admin` inherited `greet()` from `User`.

---

# 32. `extends`

The `extends` keyword creates a derived class.

```javascript
class Animal {
    eat() {
        console.log("Eating");
    }
}

class Dog extends Animal {
    bark() {
        console.log("Barking");
    }
}
```

Usage:

```javascript
const dog = new Dog();

dog.eat();
dog.bark();
```

Output:

```text
Eating
Barking
```

Relationship:

```text
Animal
│
│
└── Dog
     │
     ├── inherited eat()
     └── own bark()
```

---

# 33. Parent and Child Classes

Terminology:

```javascript
class User {
}

class Admin extends User {
}
```

Here:

```text
User
→ parent class
→ base class
→ superclass


Admin
→ child class
→ derived class
→ subclass
```

All of these terms may appear in documentation and interviews.

---

# 34. `super()`

When a derived class defines its own constructor, it must call `super()` before accessing `this`.

Example:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}

class Admin extends User {
    constructor(name, role) {
        super(name);

        this.role = role;
    }
}
```

Create:

```javascript
const admin =
    new Admin(
        "John",
        "super-admin"
    );
```

Result:

```text
name → John
role → super-admin
```

---

# 35. What `super()` Does

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}

class Admin extends User {
    constructor(name, role) {
        super(name);
        this.role = role;
    }
}
```

Flow:

```text
new Admin("John", "admin")

        ↓

Admin constructor

        ↓

super("John")

        ↓

User constructor

        ↓

this.name = "John"

        ↓

return to Admin constructor

        ↓

this.role = "admin"
```

Result:

```text
Admin instance

name → John
role → admin
```

---

# 36. Why `super()` Must Come Before `this`

Incorrect:

```javascript
class Admin extends User {
    constructor(name, role) {
        this.role = role;

        super(name);
    }
}
```

This causes an error.

Correct:

```javascript
class Admin extends User {
    constructor(name, role) {
        super(name);

        this.role = role;
    }
}
```

In a derived constructor, `this` cannot be used before `super()` successfully initializes it.

---

# 37. Calling Parent Methods with `super`

`super` can also call methods from the parent class.

```javascript
class User {
    greet() {
        console.log(
            "Hello from User"
        );
    }
}

class Admin extends User {
    greet() {
        super.greet();

        console.log(
            "Hello from Admin"
        );
    }
}
```

Usage:

```javascript
const admin =
    new Admin();

admin.greet();
```

Output:

```text
Hello from User
Hello from Admin
```

---

# 38. Method Overriding

A child class can define a method with the same name as a parent method.

```javascript
class User {
    getRole() {
        return "user";
    }
}

class Admin extends User {
    getRole() {
        return "admin";
    }
}
```

Usage:

```javascript
const user =
    new User();

const admin =
    new Admin();

console.log(
    user.getRole()
);

console.log(
    admin.getRole()
);
```

Output:

```text
user
admin
```

The child implementation overrides the inherited method for child instances.

---

# 39. Polymorphism

Polymorphism means different objects can respond to the same operation in different ways.

Example:

```javascript
class User {
    getPermissions() {
        return ["read"];
    }
}

class Admin extends User {
    getPermissions() {
        return [
            "read",
            "write",
            "delete"
        ];
    }
}
```

Now:

```javascript
const accounts = [
    new User(),
    new Admin()
];

accounts.forEach(
    (account) => {
        console.log(
            account.getPermissions()
        );
    }
);
```

Output:

```javascript
["read"]

["read", "write", "delete"]
```

Same method call:

```javascript
getPermissions()
```

Different behavior depending on the instance.

---

# 40. `instanceof`

`instanceof` checks whether an object's prototype chain contains a constructor's `prototype`.

Example:

```javascript
class User {
}

const user =
    new User();

console.log(
    user instanceof User
);
```

Output:

```text
true
```

---

# 41. `instanceof` with Inheritance

```javascript
class User {
}

class Admin extends User {
}

const admin =
    new Admin();
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

Also:

```javascript
console.log(
    admin instanceof User
);
```

Output:

```text
true
```

Why?

Prototype chain:

```text
admin
  │
  ▼
Admin.prototype
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

---

# 42. Classes Are Functions Internally

Consider:

```javascript
class User {
}
```

Check:

```javascript
console.log(
    typeof User
);
```

Output:

```text
function
```

Classes are special JavaScript constructs implemented on top of the language's constructor/prototype machinery.

However, class syntax has rules that differ from ordinary function declarations.

---

# 43. Classes Are Not Hoisted Like Function Declarations

This works:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

But this does not:

```javascript
const user =
    new User();

class User {
}
```

You cannot use the class before its declaration is evaluated.

This behavior is related to the **Temporal Dead Zone**.

So for practical use:

```text
Declare class first
Use class afterward
```

---

# 44. Class Declaration

The normal syntax:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

Then:

```javascript
const user =
    new User("John");
```

This is a **class declaration**.

---

# 45. Class Expression

Classes can also be assigned to variables.

```javascript
const User = class {
    constructor(name) {
        this.name = name;
    }
};
```

Usage:

```javascript
const user =
    new User("John");
```

This is called a **class expression**.

---

# 46. Named Class Expression

A class expression can have an internal name.

```javascript
const User = class UserClass {
    constructor(name) {
        this.name = name;
    }
};
```

The variable:

```text
User
```

holds the class.

The internal name:

```text
UserClass
```

is primarily available inside the class expression's own scope.

This pattern is less common than normal class declarations.

---

# 47. Class Methods and Strict Mode

Code inside JavaScript class bodies executes in **strict mode** automatically.

This matters for behavior such as `this`.

Example:

```javascript
class User {
    greet() {
        console.log(this);
    }
}
```

If the method loses its receiver:

```javascript
const user =
    new User();

const greet =
    user.greet;

greet();
```

then `this` is:

```text
undefined
```

rather than automatically becoming the global object.

---

# 48. Losing `this`

Consider:

```javascript
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

const user =
    new User("John");

const greet =
    user.greet;

greet();
```

This fails because the function is called without:

```javascript
user.
```

The original method call:

```javascript
user.greet();
```

has:

```text
this → user
```

But:

```javascript
greet();
```

does not.

This becomes important with callbacks and event handlers.

---

# 49. Fixing `this` with `bind()`

One solution:

```javascript
const greet =
    user.greet.bind(user);

greet();
```

Output:

```text
John
```

`bind()` creates a new function whose `this` is fixed to the provided value.

We will cover `this` in much greater detail in its dedicated topic.

---

# 50. Arrow Function Class Fields

Another pattern is:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet = () => {
        console.log(
            this.name
        );
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

Why?

Arrow functions capture `this` lexically from the instance field initialization context.

But there is an important difference.

Normal method:

```javascript
greet() {
}
```

is shared through the prototype.

Arrow field:

```javascript
greet = () => {
};
```

creates a function property for each instance.

---

# 51. Prototype Method vs Arrow Field

Normal method:

```javascript
class User {
    greet() {
    }
}
```

Conceptually:

```text
user1 ────┐
          │
          ▼
    User.prototype
          │
          └── greet()
          ▲
          │
user2 ────┘
```

Arrow field:

```javascript
class User {
    greet = () => {
    };
}
```

Conceptually:

```text
user1
└── greet() → Function A


user2
└── greet() → Function B
```

Therefore, do not automatically make every method an arrow field.

---

# 52. Classes vs Object Literals

Object:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(
            this.name
        );
    }
};
```

Useful when you need:

```text
One specific object
Configuration
Grouped data
Simple behavior
```

Class:

```javascript
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
```

Useful when you need:

```text
Multiple instances
Shared behavior
Encapsulation
Inheritance
Domain models
```

---

# 53. Classes vs Constructor Functions

Before class syntax, JavaScript commonly used constructor functions.

```javascript
function User(name) {
    this.name = name;
}

User.prototype.greet =
    function () {
        console.log(
            this.name
        );
    };

const user =
    new User("John");
```

Modern class syntax:

```javascript
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
```

Both use JavaScript's prototype system.

Class syntax provides a clearer and more structured way to work with constructor/prototype patterns.

---

# 54. Classes Are Syntactic Sugar — But Not Only Cosmetic

You will often hear:

> Classes are syntactic sugar over prototypes.

That is broadly useful as a mental model.

For example:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}
```

creates a method on:

```javascript
User.prototype
```

However, class syntax also provides special semantics, including:

```text
Must use new
Strict mode
Non-hoisted practical usage / TDZ
Non-enumerable prototype methods
extends and super syntax
Private fields
Static fields
```

So classes are prototype-based, but they are more than just visually different syntax.

---

# 55. Prototype Relationship

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

Conceptually:

```text
user
│
│ [[Prototype]]
▼
User.prototype
│
├── constructor → User
├── greet()
│
│ [[Prototype]]
▼
Object.prototype
│
│ [[Prototype]]
▼
null
```

Therefore:

```javascript
user.greet();
```

JavaScript searches:

```text
user
│
├── greet?
│     No
│
▼
User.prototype
│
├── greet?
│     Yes
│
▼
Execute method
```

---

# 56. The `prototype` Property

Classes expose a `prototype` property:

```javascript
class User {
    greet() {
        console.log("Hello");
    }
}

console.log(
    User.prototype
);
```

Methods such as:

```javascript
greet()
```

are stored there.

Check:

```javascript
console.log(
    User.prototype.greet
);
```

You will get the function.

---

# 57. Constructor Reference

Normally:

```javascript
class User {
}
```

has:

```javascript
User.prototype.constructor
```

which points back to:

```javascript
User
```

So:

```javascript
console.log(
    User.prototype.constructor
        === User
);
```

Output:

```text
true
```

Conceptually:

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

# 58. Inheritance and Prototype Chains

Consider:

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
```

Create:

```javascript
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

This is how inherited methods are found.

---

# 59. Inheritance Example

```javascript
class Employee {
    constructor(
        name,
        salary
    ) {
        this.name = name;
        this.salary = salary;
    }

    work() {
        console.log(
            `${this.name} is working`
        );
    }
}

class Developer extends Employee {
    constructor(
        name,
        salary,
        language
    ) {
        super(name, salary);

        this.language =
            language;
    }

    code() {
        console.log(
            `${this.name} codes in ${this.language}`
        );
    }
}
```

Usage:

```javascript
const developer =
    new Developer(
        "John",
        5000,
        "JavaScript"
    );

developer.work();
developer.code();
```

Output:

```text
John is working
John codes in JavaScript
```

---

# 60. Multi-Level Inheritance

Inheritance can continue through multiple levels.

```javascript
class Animal {
    eat() {
        console.log("Eating");
    }
}

class Mammal extends Animal {
    breathe() {
        console.log(
            "Breathing"
        );
    }
}

class Dog extends Mammal {
    bark() {
        console.log(
            "Barking"
        );
    }
}
```

Usage:

```javascript
const dog = new Dog();

dog.eat();
dog.breathe();
dog.bark();
```

Prototype chain:

```text
dog
│
▼
Dog.prototype
│
▼
Mammal.prototype
│
▼
Animal.prototype
│
▼
Object.prototype
│
▼
null
```

Deep inheritance hierarchies can become difficult to maintain, so inheritance should be used deliberately.

---

# 61. Composition

Inheritance models an:

```text
"is-a"
```

relationship.

Example:

```text
Admin is a User
Dog is an Animal
```

Composition models a:

```text
"has-a"
```

relationship.

Example:

```text
Car has an Engine
User has Permissions
Order has Items
```

Example:

```javascript
class Engine {
    start() {
        console.log(
            "Engine started"
        );
    }
}

class Car {
    constructor() {
        this.engine =
            new Engine();
    }

    start() {
        this.engine.start();
    }
}
```

Usage:

```javascript
const car = new Car();

car.start();
```

---

# 62. Composition vs Inheritance

Inheritance:

```javascript
class Admin extends User {
}
```

Relationship:

```text
Admin IS A User
```

Composition:

```javascript
class User {
    constructor(permissions) {
        this.permissions =
            permissions;
    }
}
```

Relationship:

```text
User HAS permissions
```

General principle:

> Prefer composition when the relationship is not naturally "is-a".

Do not create inheritance merely to reuse a few lines of code.

---

# 63. Class with Objects and Arrays

Classes often contain objects and arrays.

```javascript
class User {
    constructor(name) {
        this.name = name;

        this.profile = {
            bio: "",
            city: ""
        };

        this.roles = [
            "user"
        ];
    }
}
```

Usage:

```javascript
const user =
    new User("John");

user.roles.push("admin");

user.profile.city =
    "Kolkata";
```

Classes work together with everything we have already studied:

```text
Objects
Arrays
Functions
Callbacks
Closures
Scope
```

---

# 64. Mutable Class Properties

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;

        this.roles = [];
    }

    addRole(role) {
        this.roles.push(role);
    }
}
```

Usage:

```javascript
const user =
    new User("John");

user.addRole("admin");

console.log(user.roles);
```

Output:

```javascript
["admin"]
```

The array stored in the object is mutable.

---

# 65. Instance Fields Are Per Instance

Consider:

```javascript
class User {
    roles = [];
}
```

Create:

```javascript
const user1 =
    new User();

const user2 =
    new User();

user1.roles.push("admin");
```

Then:

```javascript
console.log(
    user1.roles
);
```

Output:

```javascript
["admin"]
```

But:

```javascript
console.log(
    user2.roles
);
```

Output:

```javascript
[]
```

Each instance gets its own field initialization.

---

# 66. Returning from a Constructor

Normally you do not explicitly return from a constructor.

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}
```

The created instance is returned automatically by `new`.

Be careful: explicitly returning an object from a base-class constructor can replace the normal instance result.

Example:

```javascript
class User {
    constructor() {
        return {
            name: "Different"
        };
    }
}

const user =
    new User();

console.log(user);
```

Result:

```javascript
{
    name: "Different"
}
```

This is unusual and generally avoided.

---

# 67. Static Factory Methods

Static methods can create instances.

```javascript
class User {
    constructor(name, role) {
        this.name = name;
        this.role = role;
    }

    static createAdmin(name) {
        return new User(
            name,
            "admin"
        );
    }
}
```

Usage:

```javascript
const admin =
    User.createAdmin("John");

console.log(admin);
```

Result:

```javascript
User {
    name: "John",
    role: "admin"
}
```

This pattern is called a **factory method**.

---

# 68. Validation in Constructors

Constructors can validate input.

```javascript
class User {
    constructor(name, age) {
        if (!name) {
            throw new Error(
                "Name is required"
            );
        }

        if (age < 0) {
            throw new Error(
                "Age cannot be negative"
            );
        }

        this.name = name;
        this.age = age;
    }
}
```

Usage:

```javascript
const user =
    new User("John", 25);
```

Invalid:

```javascript
const user =
    new User("", -5);
```

throws an error.

---

# 69. Methods Can Return Values

Methods do not have to just print values.

```javascript
class Rectangle {
    constructor(
        width,
        height
    ) {
        this.width = width;
        this.height = height;
    }

    getArea() {
        return (
            this.width *
            this.height
        );
    }
}
```

Usage:

```javascript
const rectangle =
    new Rectangle(10, 5);

const area =
    rectangle.getArea();

console.log(area);
```

Output:

```text
50
```

---

# 70. Methods Can Call Other Methods

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    getGreeting() {
        return (
            `Hello ${this.name}`
        );
    }

    greet() {
        console.log(
            this.getGreeting()
        );
    }
}
```

Usage:

```javascript
const user =
    new User("John");

user.greet();
```

Output:

```text
Hello John
```

---

# 71. Static Methods Cannot Directly Access Instance Properties

Consider:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    static greet() {
        console.log(this.name);
    }
}
```

Calling:

```javascript
User.greet();
```

does not make `this` refer to a particular user instance.

For a static method call:

```text
this
→ User class/constructor
```

not:

```text
specific user instance
```

If a static method needs an instance:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    static greet(user) {
        console.log(
            user.name
        );
    }
}
```

Then:

```javascript
const user =
    new User("John");

User.greet(user);
```

---

# 72. Static Methods Can Access Static Properties

```javascript
class User {
    static type =
        "Application User";

    static getType() {
        return this.type;
    }
}
```

Usage:

```javascript
console.log(
    User.getType()
);
```

Output:

```text
Application User
```

Here:

```text
this
→ User
```

because:

```javascript
User.getType()
```

was called on the class.

---

# 73. Class Example: Shopping Cart

```javascript
class ShoppingCart {
    constructor() {
        this.items = [];
    }

    addItem(product) {
        this.items.push(product);
    }

    removeItem(productId) {
        this.items =
            this.items.filter(
                (product) =>
                    product.id !==
                    productId
            );
    }

    getTotal() {
        return this.items.reduce(
            (
                total,
                product
            ) =>
                total +
                product.price,
            0
        );
    }
}
```

Usage:

```javascript
const cart =
    new ShoppingCart();

cart.addItem({
    id: 1,
    name: "Laptop",
    price: 1000
});

cart.addItem({
    id: 2,
    name: "Mouse",
    price: 50
});

console.log(
    cart.getTotal()
);
```

Output:

```text
1050
```

Notice how this combines:

```text
Classes
Objects
Arrays
Callbacks
filter()
reduce()
```

---

# 74. Class Example: User Management

```javascript
class User {
    constructor(
        id,
        name,
        role = "user"
    ) {
        this.id = id;
        this.name = name;
        this.role = role;
        this.active = true;
    }

    deactivate() {
        this.active = false;
    }

    activate() {
        this.active = true;
    }

    isAdmin() {
        return (
            this.role === "admin"
        );
    }
}
```

Usage:

```javascript
const user =
    new User(
        1,
        "John",
        "admin"
    );

console.log(
    user.isAdmin()
);
```

Output:

```text
true
```

---

# 75. Class Example: API Service

Classes may also organize related behavior.

```javascript
class ApiClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }

    async get(path) {
        const response =
            await fetch(
                `${this.baseUrl}${path}`
            );

        return response.json();
    }
}
```

Usage:

```javascript
const api =
    new ApiClient(
        "https://api.example.com"
    );

const users =
    await api.get("/users");
```

Classes are often used for:

```text
API clients
Services
Domain models
Repositories
Controllers
SDKs
Utilities
Stateful abstractions
```

---

# 76. Class Example: Bank Account

```javascript
class BankAccount {
    #balance = 0;

    constructor(owner) {
        this.owner = owner;
    }

    deposit(amount) {
        if (amount <= 0) {
            throw new Error(
                "Invalid amount"
            );
        }

        this.#balance += amount;
    }

    withdraw(amount) {
        if (
            amount <= 0 ||
            amount > this.#balance
        ) {
            throw new Error(
                "Invalid withdrawal"
            );
        }

        this.#balance -= amount;
    }

    get balance() {
        return this.#balance;
    }
}
```

Usage:

```javascript
const account =
    new BankAccount("John");

account.deposit(1000);
account.withdraw(200);

console.log(
    account.balance
);
```

Output:

```text
800
```

This demonstrates:

```text
Class
Constructor
Public property
Private field
Methods
Validation
Getter
Encapsulation
```

---

# 77. Class Interview Example 1

What is the output?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
}

const user =
    new User("John");

console.log(user.name);
```

Answer:

```text
John
```

---

# 78. Class Interview Example 2

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

Why?

Both instances find the same method through:

```javascript
User.prototype
```

---

# 79. Class Interview Example 3

What is the output?

```javascript
class User {
    name = "John";
}

const user1 =
    new User();

const user2 =
    new User();

user1.name = "Alice";

console.log(
    user2.name
);
```

Answer:

```text
John
```

Each instance has its own `name` field.

---

# 80. Class Interview Example 4

What is the output?

```javascript
class User {
    static type = "user";
}

const user =
    new User();

console.log(User.type);
console.log(user.type);
```

Answer:

```text
user
undefined
```

The static field belongs to the class.

---

# 81. Class Interview Example 5

What is the output?

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

const admin =
    new Admin();

console.log(
    admin.greet()
);
```

Answer:

```text
Admin
```

The child class overrides the parent method.

---

# 82. Class Interview Example 6

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
```

Answer:

```text
true
true
```

Because both prototypes exist in the object's prototype chain.

---

# 83. Class Interview Example 7

What happens?

```javascript
const user =
    new User();

class User {
}
```

Answer:

```text
ReferenceError
```

The class cannot be accessed before its declaration is initialized.

---

# 84. Class Interview Example 8

What is the output?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        return this.name;
    }
}

const user =
    new User("John");

const greet =
    user.greet;

console.log(greet());
```

Answer:

It throws because:

```text
this
→ undefined
```

inside the detached method call.

Trying to read:

```javascript
this.name
```

therefore fails.

---

# 85. Class Interview Example 9

What is the output?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet = () => {
        return this.name;
    };
}

const user =
    new User("John");

const greet =
    user.greet;

console.log(greet());
```

Answer:

```text
John
```

The arrow field retains lexical `this`.

---

# 86. Class Interview Example 10

What is the output?

```javascript
class Counter {
    static count = 0;

    constructor() {
        Counter.count++;
    }
}

new Counter();
new Counter();
new Counter();

console.log(
    Counter.count
);
```

Answer:

```text
3
```

---

# 87. Class Interview Example 11

What happens?

```javascript
class Parent {
    constructor(name) {
        this.name = name;
    }
}

class Child extends Parent {
    constructor(name) {
        this.age = 10;

        super(name);
    }
}
```

Answer:

An error occurs because the derived constructor attempts to access:

```javascript
this
```

before calling:

```javascript
super()
```

Correct:

```javascript
class Child extends Parent {
    constructor(name) {
        super(name);

        this.age = 10;
    }
}
```

---

# 88. Class Interview Example 12

What is the output?

```javascript
class User {
    #name = "John";

    getName() {
        return this.#name;
    }
}

const user =
    new User();

console.log(
    user.getName()
);
```

Answer:

```text
John
```

But:

```javascript
user.#name;
```

cannot be accessed outside the class.

---

# 89. Class Interview Example 13

What is the output?

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    get upperName() {
        return (
            this.name
                .toUpperCase()
        );
    }
}

const user =
    new User("John");

console.log(
    user.upperName
);
```

Answer:

```text
JOHN
```

A getter is accessed like a property.

---

# 90. Class Interview Example 14

What is the output?

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

const admin =
    new Admin();

console.log(
    admin.greet()
);
```

Answer:

```text
Hello Admin
```

`super.greet()` calls the parent implementation.

---

# 91. Class Interview Example 15

What is the output?

```javascript
class User {
    roles = [];
}

const user1 =
    new User();

const user2 =
    new User();

user1.roles.push("admin");

console.log(
    user2.roles
);
```

Answer:

```javascript
[]
```

Each instance receives its own `roles` array.

---

# 92. Classes and React

Older React code commonly used class components.

Example:

```jsx
class Counter extends React.Component {
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

You may encounter this code in:

```text
Older React applications
Legacy enterprise projects
Older tutorials
Existing codebases
Interview questions
```

---

# 93. Class Component State

Older class components use:

```javascript
this.state
```

Example:

```jsx
class Counter extends React.Component {
    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };
    }

    render() {
        return (
            <h1>
                {this.state.count}
            </h1>
        );
    }
}
```

---

# 94. Updating Class Component State

Class components commonly use:

```javascript
this.setState()
```

Example:

```jsx
this.setState({
    count: 10
});
```

When the next state depends on previous state, use the functional form:

```jsx
this.setState(
    (previousState) => ({
        count:
            previousState.count + 1
    })
);
```

Do not directly mutate:

```javascript
this.state.count++;
```

---

# 95. Modern React Usually Uses Function Components

Modern React code generally uses function components:

```jsx
function Counter() {
    const [count, setCount] =
        useState(0);

    return (
        <button
            onClick={() =>
                setCount(
                    (count) =>
                        count + 1
                )
            }
        >
            {count}
        </button>
    );
}
```

rather than:

```jsx
class Counter
    extends React.Component {
}
```

So for React:

```text
Understand class components
→ useful for legacy code

Focus primarily on function components + hooks
→ modern React development
```

---

# 96. Classes Are Still Important Outside React Components

Even though modern React usually uses function components, JavaScript classes remain important.

You may encounter them in:

```text
Libraries
SDKs
API clients
Services
Domain models
Error classes
Data structures
Backend code
Node.js applications
ORM models
Framework internals
Legacy code
```

Example:

```javascript
class ApiClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }

    getUsers() {
        // ...
    }
}
```

A React component can use such a class without itself being a class component.

---

# 97. Extending Built-In Classes

JavaScript classes can extend built-in classes.

Example:

```javascript
class ValidationError
    extends Error {

    constructor(message, field) {
        super(message);

        this.name =
            "ValidationError";

        this.field = field;
    }
}
```

Usage:

```javascript
throw new ValidationError(
    "Email is invalid",
    "email"
);
```

This is a common practical use of class inheritance.

---

# 98. Class Mental Model

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
                 User
                  │
                  │ prototype
                  ▼
          ┌──────────────────┐
          │ User.prototype   │
          │                  │
          │ greet()          │
          └──────────────────┘
                  ▲
                  │
             [[Prototype]]
                  │
          ┌──────────────────┐
user ───► │ name: "John"     │
          └──────────────────┘
```

Instance data:

```text
user
└── name
```

Shared behavior:

```text
User.prototype
└── greet()
```

This mental model is extremely important before studying prototypes.

---

# 99. Class Syntax Cheat Sheet

```javascript
class User {

    // Public instance field
    active = true;

    // Private instance field
    #password;

    // Static field
    static count = 0;

    // Constructor
    constructor(
        name,
        password
    ) {
        this.name = name;
        this.#password =
            password;

        User.count++;
    }

    // Instance method
    greet() {
        return (
            `Hello ${this.name}`
        );
    }

    // Getter
    get displayName() {
        return this.name;
    }

    // Setter
    set displayName(value) {
        this.name = value;
    }

    // Private method
    #validatePassword(
        password
    ) {
        return (
            password ===
            this.#password
        );
    }

    // Public method using
    // private behavior
    login(password) {
        return (
            this.#validatePassword(
                password
            )
        );
    }

    // Static method
    static getCount() {
        return User.count;
    }
}
```

Create:

```javascript
const user =
    new User(
        "John",
        "secret"
    );
```

---

# 100. Important Rules to Remember

```text
1. Classes define patterns for creating objects.

2. Objects created from classes are called instances.

3. Use new to create a class instance.

4. constructor() initializes new instances.

5. this inside a constructor refers to the new instance.

6. Instance properties belong to individual objects.

7. Normal class methods are stored on the prototype.

8. Prototype methods are shared between instances.

9. Class fields create instance properties.

10. Public fields are accessible outside the class.

11. # creates private fields/methods.

12. Private fields cannot be accessed directly outside the class.

13. Getters are accessed like properties.

14. Setters run when assigning through a property.

15. static members belong to the class itself.

16. Static methods are called with Class.method().

17. extends creates inheritance.

18. A child class inherits parent behavior.

19. super() calls the parent constructor.

20. In a derived constructor, super() must run before using this.

21. super.method() can call parent methods.

22. Child classes can override inherited methods.

23. instanceof checks prototype-chain relationships.

24. JavaScript classes use prototypes internally.

25. Classes cannot be invoked without new.

26. Classes cannot be used before initialization.

27. Class bodies run in strict mode.

28. Detached methods can lose their this receiver.

29. bind() can explicitly bind this.

30. Arrow class fields capture lexical this.

31. Arrow field functions are created per instance.

32. Prototype methods are normally shared.

33. Inheritance models an is-a relationship.

34. Composition models a has-a relationship.

35. Avoid unnecessary deep inheritance hierarchies.

36. Classes can contain objects and arrays.

37. Constructors can validate input.

38. Static factory methods can create instances.

39. Modern React primarily uses function components.

40. Class components remain important for understanding legacy React.
```

---

# 101. Classes + Objects + Arrays + Callbacks

Everything studied so far starts combining together.

```javascript
class UserManager {
    constructor() {
        this.users = [];
    }

    addUser(user) {
        this.users.push(user);
    }

    getActiveUsers() {
        return this.users.filter(
            (user) =>
                user.active
        );
    }

    getUserNames() {
        return this.users.map(
            (user) =>
                user.name
        );
    }
}

class User {
    constructor(
        id,
        name,
        active = true
    ) {
        this.id = id;
        this.name = name;
        this.active = active;
    }
}
```

Usage:

```javascript
const manager =
    new UserManager();

manager.addUser(
    new User(
        1,
        "John",
        true
    )
);

manager.addUser(
    new User(
        2,
        "Alice",
        false
    )
);

manager.addUser(
    new User(
        3,
        "Bob",
        true
    )
);

console.log(
    manager.getActiveUsers()
);

console.log(
    manager.getUserNames()
);
```

Concepts:

```text
User
│
└── Class

new User(...)
│
└── Object / Instance

UserManager
│
├── Class
│
└── users
      │
      └── Array
           │
           └── User objects

filter()
│
└── Callback

map()
│
└── Callback
```

This is how JavaScript concepts begin forming complete application logic.

---

# 102. React-Relevant Class Summary

For modern React, you should understand these class concepts:

```text
Class syntax
Constructor
new
Instance
Instance properties
Methods
this
Prototype methods
Static methods
Inheritance
extends
super
Private fields
Getters/setters
instanceof
```

But when writing modern React components, you will usually use:

```jsx
function UserProfile({
    user
}) {
    return (
        <div>
            {user.name}
        </div>
    );
}
```

rather than:

```jsx
class UserProfile
    extends React.Component {

    render() {
        return (
            <div>
                {this.props.user.name}
            </div>
        );
    }
}
```
