#  Data Types

JavaScript data types define the **kind of value** a variable can hold and determine what operations can be performed on that value.

JavaScript is a **dynamically typed language**, meaning you do not explicitly declare the type of a variable. The type is determined at runtime based on the value assigned to it.

```javascript
let value = 10;        // Number
value = "Hello";       // String
value = true;          // Boolean
```

The same variable can hold values of different types at different points in the program.

---

## 1. Types of Data Types

JavaScript data types are divided into two main categories:

```text
JavaScript Data Types
│
├── Primitive Types
│   ├── String
│   ├── Number
│   ├── BigInt
│   ├── Boolean
│   ├── Undefined
│   ├── Null
│   └── Symbol
│
└── Non-Primitive / Reference Types
    └── Object
        ├── Object
        ├── Array
        ├── Function
        ├── Date
        ├── Map
        ├── Set
        └── etc.
```

---

# Primitive Data Types

Primitive values represent simple values and are **immutable**.

JavaScript has **7 primitive data types**:

1. String
2. Number
3. BigInt
4. Boolean
5. Undefined
6. Null
7. Symbol

---

## 2. String

A `String` represents textual data.

Strings can be created using:

* Single quotes `' '`
* Double quotes `" "`
* Backticks `` ` ` ``

```javascript
let firstName = "Om";
let language = 'JavaScript';
let message = `Hello World`;
```

### Template Literals

Backticks allow values and expressions to be embedded directly inside strings.

```javascript
let name = "Om";
let age = 25;

let message = `My name is ${name} and I am ${age} years old.`;

console.log(message);
```

Output:

```text
My name is Om and I am 25 years old.
```

Strings are immutable.

```javascript
let name = "Om";

name[0] = "A";

console.log(name);
```

The original string is not modified.

---

## 3. Number

JavaScript uses the `Number` type for both integers and floating-point numbers.

```javascript
let age = 25;
let price = 99.99;
let temperature = -10;
```

Unlike languages such as Java, JavaScript does not normally separate numbers into types such as:

```text
int
float
double
```

They are represented using the `Number` type.

### Special Number Values

JavaScript also has special numeric values.

#### Infinity

```javascript
console.log(10 / 0);
```

Output:

```text
Infinity
```

#### -Infinity

```javascript
console.log(-10 / 0);
```

Output:

```text
-Infinity
```

#### NaN

`NaN` means **Not-a-Number**.

```javascript
console.log("hello" * 5);
```

Output:

```text
NaN
```

Despite its name:

```javascript
console.log(typeof NaN);
```

Output:

```text
number
```

A safer way to check for `NaN` is:

```javascript
Number.isNaN(value);
```

Example:

```javascript
let result = 0 / 0;

console.log(Number.isNaN(result));
```

Output:

```text
true
```

---

## 4. BigInt

`BigInt` is used for integers that are too large to be represented safely by the `Number` type.

JavaScript's maximum safe integer is:

```javascript
Number.MAX_SAFE_INTEGER;
```

Which is:

```text
9007199254740991
```

A BigInt can be created by adding `n` after an integer.

```javascript
let bigNumber = 900719925474099123456789n;

console.log(typeof bigNumber);
```

Output:

```text
bigint
```

You generally cannot directly mix `Number` and `BigInt` in arithmetic operations.

```javascript
let a = 10n;
let b = 5;

console.log(a + b);
```

This throws a `TypeError`.

Use compatible types instead:

```javascript
let a = 10n;
let b = 5n;

console.log(a + b);
```

Output:

```text
15n
```

---

## 5. Boolean

A `Boolean` represents a logical value.

It can only contain:

```text
true
false
```

Example:

```javascript
let isLoggedIn = true;
let isAdmin = false;
```

Booleans are commonly used with conditions.

```javascript
if (isLoggedIn) {
    console.log("Welcome");
}
```

Comparison operations also return Boolean values.

```javascript
console.log(10 > 5);   // true
console.log(10 < 5);   // false
console.log(10 === 10); // true
```

---

## 6. Undefined

`undefined` means a variable exists but **has not been assigned a value**.

```javascript
let username;

console.log(username);
```

Output:

```text
undefined
```

Checking its type:

```javascript
console.log(typeof username);
```

Output:

```text
undefined
```

Functions that do not explicitly return a value also return `undefined`.

```javascript
function greet() {
    console.log("Hello");
}

let result = greet();

console.log(result);
```

Output:

```text
undefined
```

---

## 7. Null

`null` represents the **intentional absence of a value**.

```javascript
let selectedUser = null;
```

This can mean:

> The variable exists, but currently there is intentionally no value assigned to it.

For example:

```javascript
let currentUser = null;

currentUser = {
    name: "Om"
};
```

### null vs undefined

```text
undefined → value has not been assigned

null → absence of value was intentionally assigned
```

Example:

```javascript
let user;
let selectedUser = null;

console.log(user);          // undefined
console.log(selectedUser);  // null
```

### typeof null

JavaScript has a famous historical behavior:

```javascript
console.log(typeof null);
```

Output:

```text
object
```

However, `null` is **not an object**.

It is a primitive value.

`typeof null === "object"` is a historical JavaScript quirk retained for backward compatibility.

To check for `null`:

```javascript
value === null
```

---

## 8. Symbol

`Symbol` creates a unique primitive value.

```javascript
let id = Symbol();
```

Symbols can also have descriptions.

```javascript
let id = Symbol("id");
```

Every Symbol is unique.

```javascript
let id1 = Symbol("id");
let id2 = Symbol("id");

console.log(id1 === id2);
```

Output:

```text
false
```

Even though both Symbols have the same description, they are different values.

Symbols are commonly used as unique object property keys.

```javascript
const userId = Symbol("userId");

const user = {
    name: "Om",
    [userId]: 101
};

console.log(user[userId]);
```

Output:

```text
101
```

---

# Non-Primitive / Reference Types

## 9. Object

`Object` is JavaScript's main non-primitive data type.

Objects store collections of values using **key-value pairs**.

```javascript
let user = {
    name: "Om",
    age: 25,
    isAdmin: true
};
```

Access properties using dot notation:

```javascript
console.log(user.name);
```

Or bracket notation:

```javascript
console.log(user["name"]);
```

Objects can contain almost any type of value.

```javascript
let user = {
    name: "Om",
    age: 25,
    active: true,
    address: null,
    skills: ["JavaScript", "React"]
};
```

---

## 10. Arrays

Arrays are used to store ordered collections of values.

```javascript
let technologies = [
    "JavaScript",
    "React",
    "Node.js"
];
```

Array elements are accessed using indexes.

```javascript
console.log(technologies[0]);
```

Output:

```text
JavaScript
```

Arrays can contain different data types.

```javascript
let values = [
    "Hello",
    100,
    true,
    null,
    { name: "Om" }
];
```

Arrays are technically objects in JavaScript.

```javascript
console.log(typeof []);
```

Output:

```text
object
```

To correctly check whether something is an array:

```javascript
Array.isArray([]);
```

Output:

```text
true
```

---

## 11. Functions

Functions are reusable blocks of code.

```javascript
function greet() {
    console.log("Hello");
}
```

Functions can be assigned to variables.

```javascript
const greet = function () {
    console.log("Hello");
};
```

Checking the type:

```javascript
console.log(typeof greet);
```

Output:

```text
function
```

Functions are technically objects in JavaScript, but `typeof` returns `"function"` for convenience.

---

# Primitive vs Reference Values

One of the most important concepts to understand is how primitive and object values behave when assigned to variables.

## Primitive Values

Primitive values are copied **by value**.

```javascript
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
```

Conceptually:

```text
a → 10
b → 10
```

`b` receives its own copy of the primitive value.

Changing `b` does not affect `a`.

---

## Object Values

With objects, the value stored in the variable refers to an object.

```javascript
let user1 = {
    name: "Om"
};

let user2 = user1;

user2.name = "Rahul";

console.log(user1.name);
```

Output:

```text
Rahul
```

Conceptually:

```text
user1 ──┐
        ├──→ { name: "Rahul" }
user2 ──┘
```

Both variables refer to the **same object**.

Therefore, modifying the object through one variable is visible through the other.

> More precisely, JavaScript is always pass-by-value. For objects, the value being copied is a reference to the object.

---

# Mutable vs Immutable

Primitive values are immutable.

```javascript
let name = "Om";

name[0] = "A";

console.log(name);
```

The original string cannot be modified in place.

Objects are mutable.

```javascript
const user = {
    name: "Om"
};

user.name = "Rahul";

console.log(user.name);
```

Output:

```text
Rahul
```

The object itself can be modified.

This is also why this works:

```javascript
const user = {
    name: "Om"
};

user.name = "Rahul";
```

`const` prevents reassignment of the variable:

```javascript
user = {};
```

But it does **not** make the object immutable.

---

# typeof Operator

The `typeof` operator tells us the type of a value.

```javascript
typeof value
```

Examples:

```javascript
console.log(typeof "Hello");       // string
console.log(typeof 100);           // number
console.log(typeof 100n);          // bigint
console.log(typeof true);          // boolean
console.log(typeof undefined);     // undefined
console.log(typeof Symbol("id"));  // symbol
console.log(typeof {});            // object
console.log(typeof []);            // object
console.log(typeof function() {}); // function
console.log(typeof null);          // object
```

Reference table:

| Value          | `typeof` Result |
| -------------- | --------------- |
| `"Hello"`      | `"string"`      |
| `100`          | `"number"`      |
| `100n`         | `"bigint"`      |
| `true`         | `"boolean"`     |
| `undefined`    | `"undefined"`   |
| `Symbol()`     | `"symbol"`      |
| `{}`           | `"object"`      |
| `[]`           | `"object"`      |
| `function(){}` | `"function"`    |
| `null`         | `"object"`      |

Remember:

```javascript
typeof null === "object"
```

is a historical JavaScript quirk.

---

# Dynamic Typing

JavaScript is dynamically typed.

The type belongs to the **value**, not permanently to the variable.

```javascript
let value = 10;

console.log(typeof value); // number

value = "Hello";

console.log(typeof value); // string

value = true;

console.log(typeof value); // boolean
```

This is valid JavaScript.

---

# Type Conversion

Values can be converted from one type to another.

## String Conversion

```javascript
let number = 100;

let text = String(number);

console.log(text);        // "100"
console.log(typeof text); // string
```

---

## Number Conversion

```javascript
let value = "100";

let number = Number(value);

console.log(number);        // 100
console.log(typeof number); // number
```

Invalid numeric conversion:

```javascript
Number("hello");
```

Result:

```text
NaN
```

---

## Boolean Conversion

```javascript
Boolean(1);       // true
Boolean(0);       // false
Boolean("hello"); // true
Boolean("");      // false
```

This leads to the concept of **truthy and falsy values**.

---

# Truthy and Falsy Values

JavaScript converts values to Boolean when they are used in Boolean contexts such as `if` statements.

Falsy values include:

```javascript
false
0
-0
0n
""
null
undefined
NaN
```

Everything else is generally truthy.

Examples:

```javascript
Boolean("");        // false
Boolean("hello");   // true

Boolean(0);         // false
Boolean(10);        // true

Boolean(null);      // false
Boolean(undefined); // false

Boolean({});        // true
Boolean([]);        // true
```

An important point:

```javascript
Boolean("false");
```

returns:

```text
true
```

because `"false"` is a **non-empty string**.

Also:

```javascript
Boolean([]);
Boolean({});
```

both return:

```text
true
```

because arrays and objects are truthy.

---

# Quick Revision

```text
JavaScript has 8 fundamental data type categories.

Primitive:
1. String
2. Number
3. BigInt
4. Boolean
5. Undefined
6. Null
7. Symbol

Non-Primitive:
8. Object

Arrays and functions are object-based values.

Primitive values:
→ Immutable
→ Copied by value

Objects:
→ Mutable
→ Variables can refer to the same object

JavaScript:
→ Dynamically typed
→ Type belongs to the value

Important quirks:
typeof null        → "object"
typeof []          → "object"
typeof function(){} → "function"
typeof NaN         → "number"

Useful checks:
Array.isArray(value)
Number.isNaN(value)
value === null
```

---

# Interview Points

### How many primitive data types are there in JavaScript?

There are **7 primitive data types**:

```text
String
Number
BigInt
Boolean
Undefined
Null
Symbol
```

### Is Array a data type in JavaScript?

Arrays are a specialized kind of object.

```javascript
typeof [] // "object"
```

Use:

```javascript
Array.isArray(value);
```

to check for an array.

### Why does `typeof null` return `"object"`?

It is a historical JavaScript behavior preserved for backward compatibility. `null` itself is a primitive.

### What is the difference between `null` and `undefined`?

```text
undefined → value is missing/not assigned
null      → intentional absence of a value
```

### What is the difference between primitive and object values?

Primitive values are immutable and their values are copied independently.

Object variables can contain references to the same object, so mutations through one reference can be observed through another.

### Is JavaScript statically or dynamically typed?

JavaScript is **dynamically typed**. A variable can hold values of different types during execution.
