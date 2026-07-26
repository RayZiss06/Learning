# Variables

`Variables are named storage locations that hold values. In JavaScript, variables don't have fixed types; the values do. A variable can reference a number now and a string later.`

### JavaScript is dynamically typed. 
```javascript
let value = 10;
value = "Hello";
value = true;
```



## The Three Ways to Declare Variables

- var
- let
- const


### 1. var (Legacy)
```javascript
var age = 25;
```

**Characteristics:**
- Function scoped
- Can be redeclared
- Can be reassigned
- Hoisted
- Attached to the global object (when declared globally in a non-module script)

Example:
```javascript
var x = 10;
var x = 20;

console.log(x);
```

Output:
```javascript
20
// No error.
```


#### This is one reason *var* often causes bugs in large codebases.
```javascript
// Function Scope
function test() {
    var a = 10;
}

console.log(a);
```
Output
```javascript
ReferenceError
// because a only exists inside the function.
```


However,
```javascript
if (true) {
    var x = 5;
}

console.log(x);
```
Output
```javascript
5
// Many developers expect an error because of the if block, but var ignores block boundaries.
```


### 2. let

Introduced in ES6.
```javascript
let count = 0;
```
**Characteristics:**
- Block scoped
- Cannot be redeclared in the same scope
- Can be reassigned
- Hoisted (but not initialized)
- Preferred for mutable values

Example:
```javascript
let score = 90;
score = 95;
// Valid.
```
But
```javascript
let score = 90;
let score = 100;

// Produces: SyntaxError
```

Block Scope
```javascript
if (true) {
    let age = 25;
}

console.log(age);

// Produces: ReferenceError
```
Unlike var, let respects block boundaries.


### 3. const

Example
```javascript
const PI = 3.14159;
```
**Characteristics:**
- Block scoped
- Cannot be redeclared
- Cannot be reassigned
- Must be initialized at declaration

Invalid:
```javascript
const x;
x=10;
// Produces: SyntaxError
```
Correct:
```javascript
const x = 10;
```
Cannot be reassigned
```javascript
const x=10;
x++;
// Produces: TypeError

```
***Common Misconception About const***

Many developers think:  ***const*** makes a value *immutable*.

This is incorrect.

***const*** makes the binding *immutable*, not the object or array it refers to.

Example:
```javascript
const user = {
    name: "Om"
};

user.name = "John";
// Above works because you're modifying the object's contents, not changing what user points to.

// But below is invalid:
user = {};

// because it tries to assign a new object to the variable.
```

The same applies to arrays:
```javascript
const numbers = [1, 2, 3];

numbers.push(4);
// Valid.

numbers = [];
// Invalid.
```
If you need true immutability, use techniques like Object.freeze() (shallow only) or immutable update patterns/libraries.


### What does "hoisted" mean?

Before JavaScript executes the code in a scope, it first creates bindings for declarations it finds there.


```javascript
let
Before execution:

x → binding exists, but is uninitialized

Execution:

console.log(x);   ← x exists, but cannot be accessed → ReferenceError

let x = 10;       ← x is initialized with 10
```

```javascript
var
Before execution:

x → undefined

Then execution:

console.log(x);   // undefined

x = 10;

var is hoisted and initialized to undefined.
```

##### Temporal Dead Zone (TDZ)

The period between entering the scope and executing the declaration is called the Temporal Dead Zone (TDZ).


## Which One Should You Use?

Modern practice:
- Use const by default.
- Use let only when the variable needs to be reassigned.
- Avoid var in new code.

This approach communicates intent clearly: if a variable is declared with const, readers know it won't be reassigned.