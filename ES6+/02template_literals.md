# Template Literals

**Template literals** are a modern way to create strings in JavaScript using **backticks** instead of single or double quotes.

They were introduced in **ES6 (ECMAScript 2015)**.

Basic syntax:

```javascript
const message = `Hello World`;
```

Template literals provide several useful features:

```text
String interpolation
Expression evaluation
Multiline strings
Function calls inside strings
Conditional expressions
Tagged templates
```

The most important feature is:

```javascript
${expression}
```

which allows JavaScript expressions to be inserted directly into strings.

---

# 1. Traditional Strings

Before template literals, strings were commonly created using:

```javascript
const name = "John";
```

or:

```javascript
const name = 'John';
```

Both create strings.

Example:

```javascript
console.log(typeof name);
```

Output:

```text
string
```

Template literal:

```javascript
const name = `John`;
```

also creates a string.

---

# 2. Template Literal Syntax

Template literals use:

```text
`
```

called a **backtick**.

Example:

```javascript
const message = `Hello World`;
```

Not:

```javascript
const message = "Hello World";
```

or:

```javascript
const message = 'Hello World';
```

All three create strings, but template literals provide additional capabilities.

---

# 3. Backticks

The backtick character looks like:

```text
`
```

Example:

```javascript
const language = `JavaScript`;
```

Check:

```javascript
console.log(language);
```

Output:

```text
JavaScript
```

Check type:

```javascript
console.log(typeof language);
```

Output:

```text
string
```

Template literals still produce normal JavaScript strings.

---

# 4. String Concatenation

Suppose:

```javascript
const name = "John";
const age = 25;
```

Traditional concatenation:

```javascript
const message =
    "My name is " +
    name +
    " and I am " +
    age +
    " years old.";
```

Result:

```text
My name is John and I am 25 years old.
```

This works, but becomes difficult to read as strings become more complex.

---

# 5. String Interpolation

Template literals allow values to be inserted directly into strings.

```javascript
const name = "John";
const age = 25;

const message =
    `My name is ${name} and I am ${age} years old.`;
```

Output:

```text
My name is John and I am 25 years old.
```

This is called:

```text
String Interpolation
```

---

# 6. `${}` Syntax

Inside a template literal:

```javascript
${expression}
```

means:

```text
Evaluate this JavaScript expression
and insert the result into the string.
```

Example:

```javascript
const name = "John";

console.log(`Hello ${name}`);
```

Output:

```text
Hello John
```

---

# 7. Variables Inside Template Literals

Example:

```javascript
const product = "Laptop";
const price = 1200;

const message =
    `${product} costs $${price}`;
```

Output:

```text
Laptop costs $1200
```

Here:

```javascript
${product}
```

becomes:

```text
Laptop
```

and:

```javascript
${price}
```

becomes:

```text
1200
```

---

# 8. Multiple Variables

Example:

```javascript
const firstName = "John";
const lastName = "Doe";
const age = 25;

const message =
    `${firstName} ${lastName} is ${age} years old.`;
```

Output:

```text
John Doe is 25 years old.
```

---

# 9. Expressions Inside Template Literals

`${}` can contain any JavaScript expression.

Example:

```javascript
const a = 10;
const b = 20;

console.log(
    `${a + b}`
);
```

Output:

```text
30
```

Another example:

```javascript
console.log(
    `10 * 20 = ${10 * 20}`
);
```

Output:

```text
10 * 20 = 200
```

---

# 10. Arithmetic Expressions

Example:

```javascript
const price = 100;
const quantity = 3;

const message =
    `Total: $${price * quantity}`;
```

Output:

```text
Total: $300
```

The expression:

```javascript
price * quantity
```

is evaluated first.

Then the result is inserted into the string.

---

# 11. Boolean Expressions

Example:

```javascript
const age = 25;

console.log(
    `Adult: ${age >= 18}`
);
```

Output:

```text
Adult: true
```

Because:

```javascript
age >= 18
```

evaluates to:

```text
true
```

---

# 12. Function Calls Inside Template Literals

Functions can be called inside:

```javascript
${}
```

Example:

```javascript
function getName() {
    return "John";
}

const message =
    `Hello ${getName()}`;
```

Output:

```text
Hello John
```

The function runs first and its return value is inserted into the string.

---

# 13. Function with Arguments

Example:

```javascript
function add(a, b) {
    return a + b;
}

const message =
    `Result: ${add(10, 20)}`;
```

Output:

```text
Result: 30
```

---

# 14. Method Calls Inside Template Literals

Example:

```javascript
const name = "john";

const message =
    `Hello ${name.toUpperCase()}`;
```

Output:

```text
Hello JOHN
```

Another example:

```javascript
const text = "JAVASCRIPT";

console.log(
    `Language: ${text.toLowerCase()}`
);
```

Output:

```text
Language: javascript
```

---

# 15. Object Properties Inside Template Literals

Example:

```javascript
const user = {
    name: "John",
    age: 25
};

const message =
    `${user.name} is ${user.age} years old.`;
```

Output:

```text
John is 25 years old.
```

---

# 16. Array Values Inside Template Literals

Example:

```javascript
const languages = [
    "JavaScript",
    "Python",
    "Java"
];

console.log(
    `Languages: ${languages}`
);
```

Output:

```text
Languages: JavaScript,Python,Java
```

Why?

When an array is converted to a string, JavaScript effectively uses its string representation.

For formatted output:

```javascript
console.log(
    `Languages: ${languages.join(", ")}`
);
```

Output:

```text
Languages: JavaScript, Python, Java
```

---

# 17. Multiline Strings

Before template literals, multiline strings often required:

```javascript
const message =
    "Hello\n" +
    "Welcome to JavaScript\n" +
    "Have fun";
```

Template literals allow actual line breaks.

```javascript
const message = `Hello
Welcome to JavaScript
Have fun`;
```

Output:

```text
Hello
Welcome to JavaScript
Have fun
```

---

# 18. Multiline Example

```javascript
const user = `
Name: John
Age: 25
Role: Developer
`;
```

Print:

```javascript
console.log(user);
```

Output:

```text
Name: John
Age: 25
Role: Developer
```

This can make multiline text much easier to write.

---

# 19. Whitespace Is Preserved

Template literals preserve line breaks and spaces.

Example:

```javascript
const text = `Hello
    JavaScript
        Developer`;
```

The indentation becomes part of the resulting string.

Therefore be careful when formatting template literals across multiple lines.

---

# 20. Quotes Inside Template Literals

Because template literals use backticks, single and double quotes can usually be used without escaping.

Example:

```javascript
const message =
    `John said "Hello"`;
```

Output:

```text
John said "Hello"
```

Example:

```javascript
const message =
    `It's JavaScript`;
```

Output:

```text
It's JavaScript
```

---

# 21. Traditional Quote Escaping

With single quotes:

```javascript
const message =
    'It\'s JavaScript';
```

With template literals:

```javascript
const message =
    `It's JavaScript`;
```

This can improve readability.

---

# 22. Escaping Backticks

If you need a literal backtick inside a template literal, escape it.

```javascript
const message =
    `This is a \`backtick\``;
```

Output:

```text
This is a `backtick`
```

---

# 23. Escaping `${}`

Suppose you want the literal text:

```text
${name}
```

instead of interpolation.

Use:

```javascript
const text =
    `\${name}`;
```

Output:

```text
${name}
```

---

# 24. Conditional Expressions

Ternary operators are commonly used inside template literals.

Example:

```javascript
const age = 25;

const message =
    `Status: ${
        age >= 18
            ? "Adult"
            : "Minor"
    }`;
```

Output:

```text
Status: Adult
```

---

# 25. Simple Ternary Example

```javascript
const isLoggedIn = true;

const message =
    `User is ${
        isLoggedIn
            ? "Logged In"
            : "Logged Out"
    }`;
```

Output:

```text
User is Logged In
```

---

# 26. Logical Expressions

Example:

```javascript
const username = "John";

const message =
    `Hello ${username || "Guest"}`;
```

Output:

```text
Hello John
```

If:

```javascript
const username = "";
```

then:

```text
Hello Guest
```

because:

```javascript
"" || "Guest"
```

returns:

```text
Guest
```

---

# 27. Nullish Coalescing Inside Template Literals

Example:

```javascript
const username = null;

const message =
    `Hello ${username ?? "Guest"}`;
```

Output:

```text
Hello Guest
```

We will cover:

```javascript
??
```

in detail under **Nullish Coalescing**.

---

# 28. Optional Chaining Inside Template Literals

Example:

```javascript
const user = {
    profile: {
        name: "John"
    }
};

const message =
    `Hello ${user.profile?.name}`;
```

Output:

```text
Hello John
```

Again, we will cover:

```javascript
?.
```

separately.

---

# 29. Nested Property Expressions

Example:

```javascript
const user = {
    profile: {
        firstName: "John",
        lastName: "Doe"
    }
};

const message =
    `${user.profile.firstName} ${user.profile.lastName}`;
```

Output:

```text
John Doe
```

---

# 30. Template Literals with Array Methods

Example:

```javascript
const numbers = [
    1,
    2,
    3
];

const message =
    `Numbers: ${
        numbers.map(
            (number) => number * 2
        )
    }`;
```

Output:

```text
Numbers: 2,4,6
```

Better formatted:

```javascript
const message =
    `Numbers: ${
        numbers
            .map((number) => number * 2)
            .join(", ")
    }`;
```

Output:

```text
Numbers: 2, 4, 6
```

---

# 31. Template Literals with Objects

Directly inserting an object:

```javascript
const user = {
    name: "John"
};

console.log(
    `User: ${user}`
);
```

typically produces:

```text
User: [object Object]
```

because the object is converted to a string.

---

# 32. Displaying Object Data

Instead of:

```javascript
`User: ${user}`
```

access the properties:

```javascript
`User: ${user.name}`
```

Output:

```text
User: John
```

Or for debugging:

```javascript
`User: ${JSON.stringify(user)}`
```

Output:

```text
User: {"name":"John"}
```

---

# 33. Nested Template Literals

A template literal can exist inside another template expression.

Example:

```javascript
const user = {
    name: "John",
    admin: true
};

const message =
    `User: ${
        user.admin
            ? `${user.name} is Admin`
            : `${user.name} is User`
    }`;
```

Output:

```text
User: John is Admin
```

This works, although deeply nested templates can become difficult to read.

---

# 34. Calling Arrow Functions Inside Templates

Example:

```javascript
const double =
    (number) => number * 2;

const message =
    `Result: ${double(10)}`;
```

Output:

```text
Result: 20
```

This combines arrow functions with template literals.

---

# 35. Template Literals with `map()`

Example:

```javascript
const users = [
    "John",
    "Alice",
    "Bob"
];

const messages = users.map(
    (name) => `Hello ${name}`
);
```

Result:

```javascript
[
    "Hello John",
    "Hello Alice",
    "Hello Bob"
]
```

This is a very common modern JavaScript pattern.

---

# 36. Dynamic URLs

Template literals are extremely useful for constructing URLs.

Traditional:

```javascript
const userId = 10;

const url =
    "/api/users/" + userId;
```

Template literal:

```javascript
const url =
    `/api/users/${userId}`;
```

Result:

```text
/api/users/10
```

---

# 37. Dynamic URL with Multiple Values

Example:

```javascript
const userId = 10;
const postId = 25;

const url =
    `/api/users/${userId}/posts/${postId}`;
```

Result:

```text
/api/users/10/posts/25
```

This pattern is extremely common when working with APIs.

---

# 38. Query Parameters

Example:

```javascript
const page = 2;
const limit = 20;

const url =
    `/api/users?page=${page}&limit=${limit}`;
```

Result:

```text
/api/users?page=2&limit=20
```

For complex query parameters, APIs such as `URLSearchParams` are generally safer than manually building query strings.

---

# 39. Dynamic Messages

Example:

```javascript
const username = "John";
const unreadMessages = 5;

const message =
    `Hello ${username}, you have ${unreadMessages} unread messages.`;
```

Output:

```text
Hello John, you have 5 unread messages.
```

---

# 40. Dynamic Error Messages

Example:

```javascript
const field = "email";

const error =
    `${field} is required`;
```

Output:

```text
email is required
```

---

# 41. Dynamic Logging

Example:

```javascript
const userId = 100;
const action = "LOGIN";

console.log(
    `User ${userId} performed ${action}`
);
```

Output:

```text
User 100 performed LOGIN
```

Template literals are useful for logs, although production systems often prefer structured logging.

---

# 42. Template Literals with Dates

Example:

```javascript
const year = 2026;
const month = 7;
const day = 26;

const date =
    `${year}-${month}-${day}`;
```

Result:

```text
2026-7-26
```

Formatting logic can also be used:

```javascript
const date =
    `${year}-${String(month).padStart(2, "0")}-${String(day).padStart(2, "0")}`;
```

Result:

```text
2026-07-26
```

---

# 43. Template Literals with Functions

Example:

```javascript
function getFullName(
    firstName,
    lastName
) {
    return `${firstName} ${lastName}`;
}
```

Call:

```javascript
console.log(
    getFullName(
        "John",
        "Doe"
    )
);
```

Output:

```text
John Doe
```

Template literals can also be used inside functions to build return values.

---

# 44. Template Literals in Classes

Example:

```javascript
class User {
    constructor(name) {
        this.name = name;
    }

    greet() {
        return `Hello ${this.name}`;
    }
}
```

Create:

```javascript
const user =
    new User("John");

console.log(
    user.greet()
);
```

Output:

```text
Hello John
```

---

# 45. Template Literals with Object Methods

Example:

```javascript
const user = {
    name: "John",
    age: 25,

    getInfo() {
        return `${this.name} is ${this.age} years old.`;
    }
};
```

Call:

```javascript
console.log(
    user.getInfo()
);
```

Output:

```text
John is 25 years old.
```

---

# 46. Template Literals and React

Template literals are frequently used in React.

Example:

```javascript
const username = "John";

const message =
    `Welcome ${username}`;
```

JSX:

```jsx
<h1>{message}</h1>
```

Result:

```text
Welcome John
```

---

# 47. Dynamic CSS Class Names

A common React pattern:

```jsx
const active = true;

<div
    className={`menu ${active ? "active" : ""}`}
>
    Menu
</div>
```

If:

```javascript
active === true
```

the class becomes:

```text
menu active
```

If false:

```text
menu
```

possibly with trailing whitespace, which is normally harmless.

---

# 48. Dynamic Class Example

```jsx
const status = "success";

<div
    className={`alert alert-${status}`}
>
    Operation completed
</div>
```

Result:

```html
<div class="alert alert-success">
```

Template literals make dynamic class construction straightforward.

---

# 49. Dynamic Image Paths

Example:

```javascript
const imageName = "profile";

const path =
    `/images/${imageName}.png`;
```

Result:

```text
/images/profile.png
```

---

# 50. Dynamic API Endpoint in React

Example:

```javascript
const userId = 10;

fetch(`/api/users/${userId}`);
```

This is one of the most common real-world uses of template literals.

---

# 51. Environment Variables

Example:

```javascript
const baseUrl =
    "https://api.example.com";

const userId = 10;

const url =
    `${baseUrl}/users/${userId}`;
```

Result:

```text
https://api.example.com/users/10
```

In applications, the base URL may come from configuration or environment variables.

---

# 52. Template Literal Expressions Can Be Complex

Technically this works:

```javascript
const age = 25;

const message =
    `User is ${
        age >= 18
            ? "an adult"
            : "a minor"
    }`;
```

But avoid putting too much logic inside template literals.

Instead of:

```javascript
const message =
    `Status: ${
        user &&
        user.active &&
        user.role === "admin"
            ? "Active Admin"
            : "Other"
    }`;
```

consider:

```javascript
const status =
    user &&
    user.active &&
    user.role === "admin"
        ? "Active Admin"
        : "Other";

const message =
    `Status: ${status}`;
```

Readability matters.

---

# 53. Tagged Template Literals

Template literals have an advanced feature called:

```text
Tagged Templates
```

Example:

```javascript
function tag(strings, value) {
    console.log(strings);
    console.log(value);
}

const name = "John";

tag`Hello ${name}`;
```

Notice:

```javascript
tag`...`
```

There are no parentheses around the template literal.

---

# 54. How Tagged Templates Work

Example:

```javascript
function tag(strings, name) {
    console.log(strings);
    console.log(name);
}

const username = "John";

tag`Hello ${username}!`;
```

Conceptually, the tag receives:

```javascript
strings
```

containing the static string portions:

```javascript
[
    "Hello ",
    "!"
]
```

and:

```javascript
name
```

contains:

```text
John
```

---

# 55. Tagged Template with Multiple Expressions

Example:

```javascript
function tag(
    strings,
    name,
    age
) {
    console.log(strings);
    console.log(name);
    console.log(age);
}

const name = "John";
const age = 25;

tag`${name} is ${age} years old`;
```

Conceptually:

```javascript
strings = [
    "",
    " is ",
    " years old"
];
```

Values:

```text
name = John
age = 25
```

---

# 56. Generic Tagged Template

A common structure is:

```javascript
function tag(
    strings,
    ...values
) {
    console.log(strings);
    console.log(values);
}
```

Usage:

```javascript
const name = "John";
const age = 25;

tag`Name: ${name}, Age: ${age}`;
```

`strings` contains the static parts.

`values` contains:

```javascript
[
    "John",
    25
]
```

---

# 57. Custom Formatting with Tagged Templates

Example:

```javascript
function upper(
    strings,
    ...values
) {
    let result = "";

    strings.forEach(
        (string, index) => {
            result += string;

            if (index < values.length) {
                result += String(
                    values[index]
                ).toUpperCase();
            }
        }
    );

    return result;
}
```

Usage:

```javascript
const name = "john";

const message =
    upper`Hello ${name}`;

console.log(message);
```

Output:

```text
Hello JOHN
```

The tag controls how template values are processed.

---

# 58. Why Tagged Templates Exist

Tagged templates allow developers or libraries to:

```text
Transform strings
Format values
Build DSL-like APIs
Escape values
Create styling systems
Process localization strings
Construct queries safely when a library implements parameterization
```

They are an advanced feature.

For React preparation, understanding the basic idea is enough.

---

# 59. Tagged Templates Do Not Automatically Make Input Safe

Important:

Using:

```javascript
tag`...`
```

does not automatically prevent:

```text
SQL injection
HTML injection
XSS
```

Security depends entirely on what the tag function does.

A library may implement a tag that safely parameterizes values, but template literals themselves provide no automatic sanitization.

---

# 60. Template Literals vs Concatenation

Traditional:

```javascript
const name = "John";
const age = 25;

const message =
    "Hello " +
    name +
    ", you are " +
    age +
    " years old.";
```

Template literal:

```javascript
const message =
    `Hello ${name}, you are ${age} years old.`;
```

The template literal is generally easier to read.

---

# 61. Another Concatenation Comparison

Traditional:

```javascript
const url =
    "/users/" +
    userId +
    "/posts/" +
    postId;
```

Template literal:

```javascript
const url =
    `/users/${userId}/posts/${postId}`;
```

For dynamic strings, template literals usually provide clearer syntax.

---

# 62. Template Literals Do Not Replace Every String

This is perfectly fine:

```javascript
const name = "John";
```

You do not need:

```javascript
const name = `John`;
```

unless template-literal features are useful.

Simple strings can use:

```javascript
""
```

or:

```javascript
''
```

Template literals are especially useful for:

```text
Interpolation
Multiline strings
Dynamic strings
Tagged templates
```

---

# 63. Common Mistake — Using Quotes Instead of Backticks

Wrong:

```javascript
const name = "John";

const message =
    "Hello ${name}";
```

Output:

```text
Hello ${name}
```

Interpolation does not work inside normal quotes.

Correct:

```javascript
const message =
    `Hello ${name}`;
```

Output:

```text
Hello John
```

---

# 64. Common Mistake — Using Single Quotes

Wrong:

```javascript
const name = "John";

const message =
    'Hello ${name}';
```

Output:

```text
Hello ${name}
```

Correct:

```javascript
const message =
    `Hello ${name}`;
```

---

# 65. Common Mistake — Missing `$`

Wrong:

```javascript
const name = "John";

const message =
    `Hello {name}`;
```

Output:

```text
Hello {name}
```

Correct:

```javascript
const message =
    `Hello ${name}`;
```

---

# 66. Common Mistake — Missing Braces

Wrong:

```javascript
const name = "John";

const message =
    `Hello $name`;
```

Output:

```text
Hello $name
```

Correct:

```javascript
const message =
    `Hello ${name}`;
```

---

# 67. Common Mistake — Incorrect Multiline Expectations

This:

```javascript
const message = `
    Hello
    John
`;
```

contains whitespace and line breaks.

The indentation is part of the resulting string.

If exact formatting matters, account for that whitespace.

---

# 68. Common Mistake — Object Interpolation

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    `User: ${user}`
);
```

Output:

```text
User: [object Object]
```

If you need the property:

```javascript
`User: ${user.name}`
```

If debugging:

```javascript
`User: ${JSON.stringify(user)}`
```

---

# 69. Common Mistake — Overcomplicated Expressions

Technically possible:

```javascript
const message =
    `Result: ${
        users
            .filter((user) => user.active)
            .map((user) => user.name)
            .join(", ")
    }`;
```

Sometimes this is fine.

But if logic becomes difficult to understand:

```javascript
const activeUsers =
    users
        .filter((user) => user.active)
        .map((user) => user.name)
        .join(", ");

const message =
    `Result: ${activeUsers}`;
```

can be easier to maintain.

---

# 70. Interview Question 1

What is the output?

```javascript
const name = "John";

console.log(
    `Hello ${name}`
);
```

Answer:

```text
Hello John
```

---

# 71. Interview Question 2

What is the output?

```javascript
const a = 10;
const b = 20;

console.log(
    `Result: ${a + b}`
);
```

Answer:

```text
Result: 30
```

---

# 72. Interview Question 3

What is the output?

```javascript
const name = "John";

console.log(
    "Hello ${name}"
);
```

Answer:

```text
Hello ${name}
```

Normal quotes do not perform interpolation.

---

# 73. Interview Question 4

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

console.log(
    `Numbers: ${numbers}`
);
```

Answer:

```text
Numbers: 1,2,3
```

The array is converted to its string representation.

---

# 74. Interview Question 5

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    `User: ${user}`
);
```

Answer:

```text
User: [object Object]
```

for a normal plain object.

---

# 75. Interview Question 6

What is the output?

```javascript
const age = 20;

console.log(
    `Status: ${
        age >= 18
            ? "Adult"
            : "Minor"
    }`
);
```

Answer:

```text
Status: Adult
```

---

# 76. Interview Question 7

What is the output?

```javascript
function greet() {
    return "Hello";
}

console.log(
    `${greet()} John`
);
```

Answer:

```text
Hello John
```

---

# 77. Interview Question 8

What is the output?

```javascript
const value = null;

console.log(
    `Value: ${value}`
);
```

Answer:

```text
Value: null
```

The value is converted to a string during interpolation.

---

# 78. Interview Question 9

What is the output?

```javascript
const value = undefined;

console.log(
    `Value: ${value}`
);
```

Answer:

```text
Value: undefined
```

---

# 79. Interview Question 10

What is the output?

```javascript
const active = false;

console.log(
    `Active: ${active}`
);
```

Answer:

```text
Active: false
```

---

# 80. Interview Question 11

What is the output?

```javascript
const name = "john";

console.log(
    `Hello ${name.toUpperCase()}`
);
```

Answer:

```text
Hello JOHN
```

---

# 81. Interview Question 12

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

console.log(
    `Values: ${numbers.join(" - ")}`
);
```

Answer:

```text
Values: 1 - 2 - 3
```

---

# 82. Interview Question 13

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    `User: ${JSON.stringify(user)}`
);
```

Answer:

```text
User: {"name":"John"}
```

---

# 83. Interview Question 14

What is the output?

```javascript
const name = "John";

console.log(
    `\${name}`
);
```

Answer:

```text
${name}
```

The `$` is escaped, so interpolation does not occur.

---

# 84. Interview Question 15

What feature is being used here?

```javascript
function tag(
    strings,
    ...values
) {
    console.log(strings);
    console.log(values);
}

const name = "John";

tag`Hello ${name}`;
```

Answer:

```text
Tagged Template Literal
```

The function receives the static string parts and interpolated values separately.

---

# 85. Template Literal Mental Model

When JavaScript sees:

```javascript
const name = "John";
const age = 25;

const message =
    `Hello ${name}, age ${age}`;
```

Think:

```text
`Hello ${name}, age ${age}`
        │             │
        ▼             ▼
      "John"          25
        │             │
        └──────┬──────┘
               ▼
      Convert/insert values
               │
               ▼
"Hello John, age 25"
```

---

# 86. Expression Mental Model

For:

```javascript
`Total: $${price * quantity}`
```

JavaScript conceptually does:

```text
Find ${...}
     ↓
Evaluate expression
     ↓
price * quantity
     ↓
Convert result to string
     ↓
Insert into template
     ↓
Produce final string
```

---

# 87. Template Literal Cheat Sheet

### Basic

```javascript
const text =
    `Hello World`;
```

### Variable

```javascript
const text =
    `Hello ${name}`;
```

### Multiple Variables

```javascript
const text =
    `${firstName} ${lastName}`;
```

### Expression

```javascript
const text =
    `Total: ${a + b}`;
```

### Function

```javascript
const text =
    `Hello ${getName()}`;
```

### Object Property

```javascript
const text =
    `Hello ${user.name}`;
```

### Method

```javascript
const text =
    `${name.toUpperCase()}`;
```

### Conditional

```javascript
const text =
    `${age >= 18 ? "Adult" : "Minor"}`;
```

### Multiline

```javascript
const text = `Line 1
Line 2
Line 3`;
```

### Dynamic URL

```javascript
const url =
    `/users/${userId}`;
```

### Escape Backtick

```javascript
const text =
    `Use \`backticks\``;
```

### Escape Interpolation

```javascript
const text =
    `\${name}`;
```

### Tagged Template

```javascript
tag`Hello ${name}`;
```

---

# 88. Important Rules to Remember

```text
1. Template literals were introduced in ES6.

2. Template literals use backticks (`).

3. They still produce normal JavaScript strings.

4. ${expression} performs interpolation.

5. Variables can be inserted with ${}.

6. Any valid expression can be evaluated inside ${}.

7. Arithmetic expressions can be used.

8. Boolean expressions can be used.

9. Function calls can be used.

10. Method calls can be used.

11. Object properties can be used.

12. Conditional expressions can be used.

13. Template literals support multiline strings.

14. Line breaks and whitespace are preserved.

15. Single and double quotes can usually be used without escaping.

16. Backticks inside a template literal must be escaped when intended literally.

17. Interpolation can be escaped using \${...}.

18. Arrays are converted to strings when directly interpolated.

19. Plain objects usually become [object Object] when directly interpolated.

20. JSON.stringify() can be useful when debugging objects.

21. Template literals are excellent for dynamic URLs.

22. They are commonly used for dynamic messages.

23. They are commonly used for dynamic CSS classes.

24. They are common in React applications.

25. Normal single or double quoted strings do not support ${} interpolation.

26. Complex logic inside template literals should be kept readable.

27. Tagged templates allow functions to process template strings.

28. Tagged templates receive static strings and interpolated values separately.

29. Tagged templates do not automatically provide security.

30. Template literals generally make dynamic strings easier to read than concatenation.
```

---

# 89. Most Important Concept

Traditional concatenation:

```javascript
const message =
    "Hello " +
    name +
    ", you are " +
    age +
    " years old.";
```

Template literal:

```javascript
const message =
    `Hello ${name}, you are ${age} years old.`;
```

Mental model:

```text
Template Literal
      │
      ▼
`Hello ${name}`
         │
         ▼
Evaluate name
         │
         ▼
Convert value to string
         │
         ▼
Insert into template
         │
         ▼
"Hello John"
```

The core syntax to remember is:

```javascript
`text ${expression} more text`
```

---

# 90. Template Literals in One Sentence

> Template literals are ES6 strings written with backticks that support expression interpolation using `${}`, multiline strings, and advanced processing through tagged templates.
