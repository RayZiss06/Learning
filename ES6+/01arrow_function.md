# Arrow Functions

Arrow functions were introduced in **ES6 (ECMAScript 2015)**.

They provide a shorter syntax for writing functions and, more importantly, behave differently from regular functions when it comes to **`this`**.

Basic syntax:

```javascript
const add = (a, b) => {
    return a + b;
};
```

Shorter form:

```javascript
const add = (a, b) => a + b;
```

---

# 1. Regular Function vs Arrow Function

Regular function:

```javascript
function add(a, b) {
    return a + b;
}
```

Function expression:

```javascript
const add = function (a, b) {
    return a + b;
};
```

Arrow function:

```javascript
const add = (a, b) => {
    return a + b;
};
```

Short arrow function:

```javascript
const add = (a, b) => a + b;
```

All four can produce:

```javascript
console.log(add(10, 20));
```

Output:

```text
30
```

---

# 2. Basic Arrow Function Syntax

General syntax:

```javascript
const functionName = (parameters) => {
    // function body
};
```

Example:

```javascript
const greet = (name) => {
    console.log(`Hello ${name}`);
};

greet("John");
```

Output:

```text
Hello John
```

---

# 3. Arrow Function with No Parameters

When there are no parameters, parentheses are required.

```javascript
const greet = () => {
    console.log("Hello");
};
```

Call:

```javascript
greet();
```

Output:

```text
Hello
```

You cannot write:

```javascript
const greet = => {
    console.log("Hello");
};
```

That is invalid syntax.

---

# 4. Arrow Function with One Parameter

When there is exactly one simple parameter, parentheses are optional.

```javascript
const square = (number) => {
    return number * number;
};
```

Can become:

```javascript
const square = number => {
    return number * number;
};
```

Both are valid.

Many developers still use parentheses consistently:

```javascript
const square = (number) => {
    return number * number;
};
```

---

# 5. Arrow Function with Multiple Parameters

With multiple parameters, parentheses are required.

```javascript
const add = (a, b) => {
    return a + b;
};
```

This is invalid:

```javascript
const add = a, b => {
    return a + b;
};
```

Correct:

```javascript
const add = (a, b) => {
    return a + b;
};
```

---

# 6. Explicit Return

When curly braces are used:

```javascript
const add = (a, b) => {
    return a + b;
};
```

you normally need:

```javascript
return
```

to return a value.

Example:

```javascript
const multiply = (a, b) => {
    const result = a * b;

    return result;
};
```

---

# 7. Implicit Return

If the function contains a single expression, braces and `return` can be removed.

Instead of:

```javascript
const add = (a, b) => {
    return a + b;
};
```

write:

```javascript
const add = (a, b) => a + b;
```

This is called an **implicit return**.

The result of:

```javascript
a + b
```

is automatically returned.

---

# 8. Explicit vs Implicit Return

Explicit:

```javascript
const square = (number) => {
    return number * number;
};
```

Implicit:

```javascript
const square = (number) =>
    number * number;
```

Both behave the same:

```javascript
console.log(square(5));
```

Output:

```text
25
```

---

# 9. Curly Braces Change Return Behavior

Consider:

```javascript
const add = (a, b) => a + b;
```

This returns:

```text
a + b
```

But:

```javascript
const add = (a, b) => {
    a + b;
};
```

does **not** return the result.

Example:

```javascript
console.log(add(10, 20));
```

Output:

```text
undefined
```

Because curly braces create a function body, and there is no:

```javascript
return
```

Correct:

```javascript
const add = (a, b) => {
    return a + b;
};
```

---

# 10. Returning Objects

This is a common arrow function trap.

Suppose we want to return:

```javascript
{
    name: "John"
}
```

This may not behave as intended:

```javascript
const createUser = () => {
    name: "John"
};
```

The braces are interpreted as the function body.

Instead, wrap the object in parentheses:

```javascript
const createUser = () => ({
    name: "John"
});
```

Now:

```javascript
console.log(createUser());
```

Output:

```javascript
{
    name: "John"
}
```

---

# 11. Why Parentheses Are Needed for Object Returns

Arrow function:

```javascript
() => {}
```

means:

```text
Function body
```

But:

```javascript
() => ({})
```

means:

```text
Return this object expression
```

Example:

```javascript
const createUser = (name) => ({
    name: name
});
```

With property shorthand:

```javascript
const createUser = (name) => ({
    name
});
```

---

# 12. Arrow Functions as Callbacks

Arrow functions are extremely common as callbacks.

Regular function:

```javascript
const numbers = [1, 2, 3];

numbers.forEach(function (number) {
    console.log(number);
});
```

Arrow function:

```javascript
numbers.forEach((number) => {
    console.log(number);
});
```

Shorter:

```javascript
numbers.forEach(
    (number) => console.log(number)
);
```

---

# 13. Arrow Functions with `map()`

Example:

```javascript
const numbers = [1, 2, 3, 4];

const doubled = numbers.map(
    (number) => number * 2
);
```

Result:

```javascript
console.log(doubled);
```

Output:

```javascript
[2, 4, 6, 8]
```

Regular function equivalent:

```javascript
const doubled = numbers.map(
    function (number) {
        return number * 2;
    }
);
```

Arrow functions make callback-heavy code more concise.

---

# 14. Arrow Functions with `filter()`

Example:

```javascript
const numbers = [
    1,
    2,
    3,
    4,
    5,
    6
];

const evenNumbers = numbers.filter(
    (number) => number % 2 === 0
);
```

Result:

```javascript
console.log(evenNumbers);
```

Output:

```javascript
[2, 4, 6]
```

---

# 15. Arrow Functions with `find()`

Example:

```javascript
const users = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Alice"
    }
];

const user = users.find(
    (user) => user.id === 2
);
```

Result:

```javascript
console.log(user);
```

Output:

```javascript
{
    id: 2,
    name: "Alice"
}
```

---

# 16. Arrow Functions with `reduce()`

Example:

```javascript
const numbers = [
    10,
    20,
    30
];

const total = numbers.reduce(
    (sum, number) =>
        sum + number,
    0
);
```

Result:

```javascript
console.log(total);
```

Output:

```text
60
```

---

# 17. Arrow Functions and `this`

This is the **most important difference** between arrow functions and regular functions.

Regular functions have their own `this` behavior based on how they are called.

Arrow functions do **not** create their own `this`.

Instead, they capture `this` from the surrounding lexical scope.

This is called:

```text
Lexical this
```

---

# 18. Regular Function `this`

Example:

```javascript
const user = {
    name: "John",

    greet: function () {
        console.log(this.name);
    }
};

user.greet();
```

Output:

```text
John
```

Because:

```javascript
user.greet()
```

means:

```text
this → user
```

---

# 19. Method Shorthand and `this`

Modern syntax:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

Call:

```javascript
user.greet();
```

Output:

```text
John
```

This is generally preferred for object methods that need dynamic `this`.

---

# 20. Arrow Function as Object Method

Consider:

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

You might expect:

```text
John
```

But the arrow function does not receive:

```text
this → user
```

Arrow functions capture `this` from the surrounding scope.

Therefore:

```javascript
user.greet();
```

does not make `this` inside the arrow function become `user`.

This is why arrow functions are generally not appropriate for object methods that rely on dynamic `this`.

---

# 21. Arrow Functions Capture Surrounding `this`

Example:

```javascript
const user = {
    name: "John",

    greet() {
        const showName = () => {
            console.log(this.name);
        };

        showName();
    }
};

user.greet();
```

Output:

```text
John
```

Why?

`greet()` is called as:

```javascript
user.greet()
```

So inside `greet`:

```text
this → user
```

The arrow function:

```javascript
showName
```

captures that surrounding `this`.

Therefore:

```text
showName's this
→ surrounding this
→ user
```

---

# 22. Regular Nested Function Problem

Consider:

```javascript
const user = {
    name: "John",

    greet() {
        function showName() {
            console.log(this.name);
        }

        showName();
    }
};
```

`showName()` is called as a plain function.

Therefore it does not automatically inherit `greet()`'s `this`.

In strict mode:

```text
this → undefined
```

inside `showName`.

Arrow functions solve this pattern naturally:

```javascript
const user = {
    name: "John",

    greet() {
        const showName = () => {
            console.log(this.name);
        };

        showName();
    }
};
```

---

# 23. Arrow Functions in `setTimeout()`

Classic example:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(() => {
            console.log(this.name);
        }, 1000);
    }
};

user.greet();
```

Output after approximately one second:

```text
John
```

The arrow callback captures `this` from:

```javascript
greet()
```

where:

```text
this → user
```

---

# 24. Why Arrow Functions Are Useful for Callbacks

Before arrow functions, code sometimes stored:

```javascript
this
```

in another variable.

Example:

```javascript
const user = {
    name: "John",

    greet() {
        const self = this;

        setTimeout(function () {
            console.log(self.name);
        }, 1000);
    }
};
```

Arrow functions allow:

```javascript
const user = {
    name: "John",

    greet() {
        setTimeout(() => {
            console.log(this.name);
        }, 1000);
    }
};
```

Much cleaner.

---

# 25. Arrow Functions Do Not Have Their Own `this`

Remember:

```text
Regular Function
→ this depends on how function is called


Arrow Function
→ this comes from surrounding lexical scope
```

This distinction is extremely important.

---

# 26. `call()` Cannot Rebind Arrow Function `this`

Regular function:

```javascript
function greet() {
    console.log(this.name);
}

const user = {
    name: "John"
};

greet.call(user);
```

Output:

```text
John
```

`call()` sets:

```text
this → user
```

Now arrow function:

```javascript
const greet = () => {
    console.log(this.name);
};

greet.call(user);
```

`call()` does not change the arrow function's lexical `this`.

The arrow keeps the `this` from where it was created.

---

# 27. `apply()` Cannot Rebind Arrow `this`

Regular function:

```javascript
function greet() {
    console.log(this.name);
}

greet.apply({
    name: "John"
});
```

Output:

```text
John
```

Arrow:

```javascript
const greet = () => {
    console.log(this.name);
};

greet.apply({
    name: "John"
});
```

The supplied object does not become the arrow function's `this`.

---

# 28. `bind()` Cannot Rebind Arrow `this`

Regular function:

```javascript
function greet() {
    console.log(this.name);
}

const boundGreet = greet.bind({
    name: "John"
});

boundGreet();
```

Output:

```text
John
```

With an arrow function:

```javascript
const greet = () => {
    console.log(this.name);
};

const boundGreet = greet.bind({
    name: "John"
});
```

`bind()` does not replace the arrow function's lexical `this`.

---

# 29. Arrow Functions Do Not Have `arguments`

Regular functions have access to:

```javascript
arguments
```

Example:

```javascript
function showArguments() {
    console.log(arguments);
}

showArguments(
    10,
    20,
    30
);
```

`arguments` contains the supplied arguments.

Arrow functions do not create their own:

```javascript
arguments
```

---

# 30. Arrow Function and `arguments`

This is not valid as a way to get the arrow's own arguments:

```javascript
const showArguments = () => {
    console.log(arguments);
};
```

An arrow function does not create its own `arguments` object.

If an outer non-arrow function has `arguments`, an arrow can lexically access that outer binding.

But for arrow function parameters, prefer rest parameters.

---

# 31. Use Rest Parameters Instead

Instead of:

```javascript
arguments
```

use:

```javascript
...args
```

Example:

```javascript
const showArguments = (...args) => {
    console.log(args);
};

showArguments(
    10,
    20,
    30
);
```

Output:

```javascript
[10, 20, 30]
```

This is generally cleaner and gives you a real array.

---

# 32. Arrow Functions Cannot Be Constructors

Regular function:

```javascript
function User(name) {
    this.name = name;
}

const user = new User("John");
```

Works.

Arrow function:

```javascript
const User = (name) => {
    this.name = name;
};
```

This:

```javascript
const user = new User("John");
```

throws a:

```text
TypeError
```

Arrow functions cannot be used with:

```javascript
new
```

---

# 33. Arrow Functions Do Not Have Constructor `.prototype`

Example:

```javascript
const greet = () => {};
```

Check:

```javascript
console.log(greet.prototype);
```

Output:

```text
undefined
```

Arrow functions are not constructable and do not receive the normal constructor `prototype` property.

Compare:

```javascript
function Greet() {
}

console.log(Greet.prototype);
```

This gives the function's constructor prototype object.

---

# 34. Arrow Functions and `new.target`

Arrow functions do not establish their own:

```javascript
new.target
```

because they cannot be constructors.

They may lexically inherit `new.target` from a surrounding constructable function.

This is similar to their lexical behavior with:

```text
this
arguments
```

---

# 35. Arrow Functions Cannot Be Generator Functions

Generator function:

```javascript
function* generateNumbers() {
    yield 1;
    yield 2;
}
```

There is no arrow-function equivalent such as:

```javascript
const generateNumbers = *() => {};
```

Arrow functions cannot be generator functions.

---

# 36. Async Arrow Functions

Arrow functions can be asynchronous.

Syntax:

```javascript
const fetchData = async () => {
    // asynchronous code
};
```

Example:

```javascript
const getData = async () => {
    return "Data";
};
```

Calling:

```javascript
getData();
```

returns a:

```text
Promise
```

We will cover this properly in the **Async/Await** topic.

---

# 37. Arrow Functions with Default Parameters

Example:

```javascript
const greet = (
    name = "Guest"
) => {
    console.log(`Hello ${name}`);
};
```

Call:

```javascript
greet();
```

Output:

```text
Hello Guest
```

Call:

```javascript
greet("John");
```

Output:

```text
Hello John
```

---

# 38. Arrow Functions with Destructuring

Object destructuring:

```javascript
const greet = ({
    name,
    age
}) => {
    console.log(
        `${name} is ${age}`
    );
};
```

Call:

```javascript
greet({
    name: "John",
    age: 25
});
```

Output:

```text
John is 25
```

---

# 39. Array Destructuring in Parameters

Example:

```javascript
const add = ([a, b]) =>
    a + b;
```

Call:

```javascript
console.log(
    add([10, 20])
);
```

Output:

```text
30
```

We will cover destructuring separately.

---

# 40. Arrow Functions with Rest Parameters

Example:

```javascript
const sum = (...numbers) => {
    return numbers.reduce(
        (total, number) =>
            total + number,
        0
    );
};
```

Call:

```javascript
console.log(
    sum(10, 20, 30)
);
```

Output:

```text
60
```

---

# 41. Arrow Functions with Ternary Operators

Example:

```javascript
const getStatus = (age) =>
    age >= 18
        ? "Adult"
        : "Minor";
```

Call:

```javascript
console.log(
    getStatus(25)
);
```

Output:

```text
Adult
```

This pattern is common for small expressions.

---

# 42. Arrow Functions with Logical Operators

Example:

```javascript
const greet = (name) =>
    name &&
    `Hello ${name}`;
```

Call:

```javascript
console.log(
    greet("John")
);
```

Output:

```text
Hello John
```

Use concise expressions only when they remain readable.

---

# 43. Arrow Function Returning an Array

No special parentheses are required for arrays.

Example:

```javascript
const getNumbers = () =>
    [1, 2, 3];
```

Call:

```javascript
console.log(
    getNumbers()
);
```

Output:

```javascript
[1, 2, 3]
```

The special syntax issue primarily concerns object literals because `{}` can be interpreted as the function body.

---

# 44. Arrow Function Returning Another Function

Example:

```javascript
const multiply = (a) => {
    return (b) => {
        return a * b;
    };
};
```

Can be shortened:

```javascript
const multiply =
    (a) => (b) => a * b;
```

Usage:

```javascript
const double = multiply(2);

console.log(
    double(10)
);
```

Output:

```text
20
```

This demonstrates how arrow functions work well with higher-order functions.

---

# 45. Arrow Functions and Closures

Arrow functions can create closures like regular functions.

Example:

```javascript
const createCounter = () => {
    let count = 0;

    return () => {
        count++;

        return count;
    };
};
```

Create:

```javascript
const counter =
    createCounter();
```

Call:

```javascript
console.log(counter());
console.log(counter());
console.log(counter());
```

Output:

```text
1
2
3
```

The returned arrow function closes over:

```javascript
count
```

---

# 46. Arrow Functions Are Still Functions

Example:

```javascript
const greet = () => {
    console.log("Hello");
};
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

Arrow functions are function objects even though they have different semantics from regular functions.

---

# 47. Function Declaration vs Arrow Function

Function declaration:

```javascript
function greet() {
    console.log("Hello");
}
```

Arrow function expression:

```javascript
const greet = () => {
    console.log("Hello");
};
```

Important difference:

```text
Function declaration
→ function declaration is hoisted with its function value


const arrow function variable
→ variable binding is hoisted but remains
  in the Temporal Dead Zone until declaration
```

---

# 48. Arrow Function Hoisting

This works:

```javascript
greet();

function greet() {
    console.log("Hello");
}
```

Output:

```text
Hello
```

But:

```javascript
greet();

const greet = () => {
    console.log("Hello");
};
```

throws:

```text
ReferenceError
```

because `greet` is in the Temporal Dead Zone before the `const` declaration is evaluated.

---

# 49. Arrow Functions Stored with `let`

Example:

```javascript
greet();

let greet = () => {
    console.log("Hello");
};
```

Also throws:

```text
ReferenceError
```

because `let` also has a Temporal Dead Zone.

---

# 50. Arrow Functions Stored with `var`

Example:

```javascript
greet();

var greet = () => {
    console.log("Hello");
};
```

Conceptually before assignment:

```javascript
var greet;
```

So calling:

```javascript
greet();
```

before assignment attempts:

```javascript
undefined();
```

and results in:

```text
TypeError
```

This connects arrow functions to the **hoisting** topic.

---

# 51. Arrow Function vs Regular Function — `this`

Regular:

```javascript
function greet() {
    console.log(this);
}
```

`this` depends on how `greet` is called.

Arrow:

```javascript
const greet = () => {
    console.log(this);
};
```

`this` comes from the surrounding lexical environment.

Remember:

```text
Regular function
→ dynamic this


Arrow function
→ lexical this
```

---

# 52. Arrow Function vs Regular Function — `arguments`

Regular:

```javascript
function add() {
    console.log(arguments);
}
```

Has its own:

```text
arguments
```

Arrow:

```javascript
const add = () => {
    // no own arguments object
};
```

Use:

```javascript
const add = (...args) => {
    console.log(args);
};
```

---

# 53. Arrow Function vs Regular Function — Constructor

Regular function:

```javascript
function User() {
}

new User();
```

Can work as a constructor.

Arrow:

```javascript
const User = () => {};
```

Cannot:

```javascript
new User();
```

---

# 54. Arrow Function vs Regular Function — `.prototype`

Regular constructable function:

```javascript
function User() {
}

console.log(User.prototype);
```

Has:

```text
prototype object
```

Arrow:

```javascript
const User = () => {};
```

```javascript
console.log(User.prototype);
```

Output:

```text
undefined
```

---

# 55. Comparison Table

| Feature                              | Regular Function            | Arrow Function |
| ------------------------------------ | --------------------------- | -------------- |
| Short syntax                         | ❌                           | ✅              |
| Own `this` behavior                  | ✅                           | ❌              |
| Lexical `this`                       | ❌                           | ✅              |
| Own `arguments`                      | ✅                           | ❌              |
| Can use `new`                        | Often ✅                     | ❌              |
| Constructor `.prototype`             | For constructable functions | ❌              |
| Can be generator                     | ✅                           | ❌              |
| Can be `async`                       | ✅                           | ✅              |
| Great for callbacks                  | ✅                           | ✅              |
| Implicit return                      | ❌                           | ✅              |
| Good as dynamic-`this` object method | ✅                           | Usually ❌      |

---

# 56. When Should You Use Arrow Functions?

Arrow functions are excellent for:

```text
Callbacks
Array methods
Short transformations
Functional-style code
Functions that should capture surrounding this
Promise callbacks
Event-processing callbacks when lexical this is desired
Small helper functions
React callbacks
```

Examples:

```javascript
numbers.map(
    (number) => number * 2
);
```

```javascript
users.filter(
    (user) => user.active
);
```

```javascript
promise.then(
    (data) => console.log(data)
);
```

---

# 57. When Should You Prefer Regular Functions?

Use regular functions when you need:

```text
Dynamic this
Constructor behavior
new
Own arguments object
Generator functions
Traditional method semantics
```

Example object method:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

---

# 58. Arrow Functions in React

Arrow functions appear constantly in React.

Example:

```javascript
const handleClick = () => {
    console.log("Clicked");
};
```

JSX:

```jsx
<button onClick={handleClick}>
    Click
</button>
```

Or:

```jsx
<button
    onClick={() => {
        console.log("Clicked");
    }}
>
    Click
</button>
```

---

# 59. Arrow Functions with React `map()`

A very common React pattern:

```jsx
const users = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Alice"
    }
];

return (
    <div>
        {users.map((user) => (
            <p key={user.id}>
                {user.name}
            </p>
        ))}
    </div>
);
```

The arrow callback transforms each object into JSX.

Understanding:

```javascript
map()
```

and arrow functions is essential for React.

---

# 60. Arrow Function Event Handler with Arguments

Suppose:

```javascript
const deleteUser = (id) => {
    console.log(id);
};
```

In React:

```jsx
<button
    onClick={() => deleteUser(10)}
>
    Delete
</button>
```

The arrow function creates a callback that will later call:

```javascript
deleteUser(10)
```

Instead of immediately executing it during rendering.

---

# 61. Common Mistake — Calling Instead of Passing Callback

Suppose:

```javascript
const handleClick = () => {
    console.log("Clicked");
};
```

Correct:

```javascript
button.addEventListener(
    "click",
    handleClick
);
```

Wrong:

```javascript
button.addEventListener(
    "click",
    handleClick()
);
```

The second version immediately calls the function and passes its return value.

This problem is not specific to arrow functions, but you will encounter it often with them.

---

# 62. Common Mistake — Missing `return`

Wrong:

```javascript
const numbers = [
    1,
    2,
    3
];

const doubled = numbers.map(
    (number) => {
        number * 2;
    }
);
```

Result:

```javascript
[
    undefined,
    undefined,
    undefined
]
```

Correct:

```javascript
const doubled = numbers.map(
    (number) => {
        return number * 2;
    }
);
```

Or:

```javascript
const doubled = numbers.map(
    (number) => number * 2
);
```

---

# 63. Common Mistake — Returning Object

Wrong:

```javascript
const users = [
    "John",
    "Alice"
];

const result = users.map(
    (name) => {
        name
    }
);
```

This does not return objects.

Correct:

```javascript
const result = users.map(
    (name) => ({
        name
    })
);
```

Result:

```javascript
[
    {
        name: "John"
    },
    {
        name: "Alice"
    }
]
```

---

# 64. Common Mistake — Arrow Function as Method

Potentially wrong:

```javascript
const user = {
    name: "John",

    greet: () => {
        console.log(this.name);
    }
};
```

If you want `this` to refer to `user`, prefer:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

---

# 65. Common Mistake — Arrow Function as Constructor

Wrong:

```javascript
const User = (name) => {
    this.name = name;
};

const user = new User("John");
```

Arrow functions cannot be constructors.

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

# 66. Common Mistake — Thinking Arrow Functions Always Better

Arrow functions are not a replacement for every function.

Choose based on behavior.

Use:

```javascript
const double =
    (number) => number * 2;
```

when concise functional behavior is useful.

Use:

```javascript
const user = {
    name: "John",

    greet() {
        console.log(this.name);
    }
};
```

when dynamic method `this` is required.

---

# 67. Interview Question 1

What is the output?

```javascript
const add =
    (a, b) => a + b;

console.log(
    add(10, 20)
);
```

Answer:

```text
30
```

---

# 68. Interview Question 2

What is the output?

```javascript
const add = (a, b) => {
    a + b;
};

console.log(
    add(10, 20)
);
```

Answer:

```text
undefined
```

Because there is no:

```javascript
return
```

---

# 69. Interview Question 3

What does this return?

```javascript
const createUser = (name) => ({
    name
});
```

Call:

```javascript
createUser("John");
```

Answer:

```javascript
{
    name: "John"
}
```

---

# 70. Interview Question 4

What happens?

```javascript
const User = () => {};

new User();
```

Answer:

```text
TypeError
```

Arrow functions cannot be constructors.

---

# 71. Interview Question 5

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

The arrow function captures `this` from `greet()`.

---

# 72. Interview Question 6

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) => number * 2
);

console.log(result);
```

Answer:

```javascript
[2, 4, 6]
```

---

# 73. Interview Question 7

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) => {
        number * 2;
    }
);

console.log(result);
```

Answer:

```javascript
[
    undefined,
    undefined,
    undefined
]
```

No value was returned from the callback.

---

# 74. Interview Question 8

What is the output?

```javascript
const greet = (
    name = "Guest"
) => `Hello ${name}`;

console.log(greet());
```

Answer:

```text
Hello Guest
```

---

# 75. Interview Question 9

What is the output?

```javascript
const createUser =
    (name, age) => ({
        name,
        age
    });

console.log(
    createUser(
        "John",
        25
    )
);
```

Answer:

```javascript
{
    name: "John",
    age: 25
}
```

---

# 76. Interview Question 10

Can `call()`, `apply()`, or `bind()` change an arrow function's `this`?

Answer:

```text
No.
```

Arrow functions use lexical `this`.

These methods can still pass arguments in their normal ways, but they cannot replace the arrow's captured `this`.

---

# 77. Arrow Function Mental Model

Regular function:

```javascript
function greet() {
    console.log(this);
}
```

Think:

```text
Who called me?
      ↓
Determine this
```

Arrow function:

```javascript
const greet = () => {
    console.log(this);
};
```

Think:

```text
Where was I created?
      ↓
Capture surrounding this
```

This is the most important conceptual difference.

---

# 78. Arrow Function Cheat Sheet

### No Parameters

```javascript
const greet = () => {
    console.log("Hello");
};
```

### One Parameter

```javascript
const square =
    number => number * number;
```

### Multiple Parameters

```javascript
const add =
    (a, b) => a + b;
```

### Explicit Return

```javascript
const add = (a, b) => {
    return a + b;
};
```

### Implicit Return

```javascript
const add =
    (a, b) => a + b;
```

### Return Object

```javascript
const createUser =
    (name) => ({
        name
    });
```

### Rest Parameters

```javascript
const sum = (...numbers) => {
    // ...
};
```

### Callback

```javascript
numbers.map(
    (number) => number * 2
);
```

### Async Arrow

```javascript
const fetchData = async () => {
    // ...
};
```

---

# 79. Important Rules to Remember

```text
1. Arrow functions were introduced in ES6.

2. They provide shorter function syntax.

3. () is required when there are no parameters.

4. Parentheses can be omitted for one simple parameter.

5. Parentheses are required for multiple parameters.

6. Curly braces create a function body.

7. A block body normally requires return to return a value.

8. Single expressions can use implicit return.

9. Object literals in implicit returns should be wrapped in parentheses.

10. Arrow functions do not create their own this.

11. Arrow functions capture this from surrounding lexical scope.

12. call(), apply(), and bind() cannot replace an arrow function's lexical this.

13. Arrow functions do not create their own arguments object.

14. Rest parameters can be used instead of arguments.

15. Arrow functions cannot be constructors.

16. Arrow functions cannot be called with new.

17. Arrow functions do not have the normal constructor .prototype property.

18. Arrow functions cannot be generator functions.

19. Arrow functions can be async.

20. Arrow functions can create closures.

21. Arrow functions are excellent for callbacks.

22. They are heavily used with map(), filter(), find(), reduce(), etc.

23. They are heavily used in React.

24. They are usually inappropriate as object methods when dynamic this is required.

25. Arrow functions assigned to const or let cannot be called before initialization.

26. Arrow functions are still function objects.

27. Arrow functions are not always better than regular functions.

28. Choose arrow vs regular functions based on semantics, especially this.
```

---

# 80. Most Important Concept

If you remember one thing about arrow functions, remember:

```text
Regular function:

this
↓
determined by how the function is called


Arrow function:

this
↓
captured from surrounding scope
```

Example:

```javascript
const user = {
    name: "John",

    greet() {
        const showName = () => {
            console.log(this.name);
        };

        showName();
    }
};

user.greet();
```

Output:

```text
John
```

Mental model:

```text
user.greet()
     │
     ▼
this = user
     │
     ▼
arrow function created
     │
     ▼
captures this
     │
     ▼
this = user
```
