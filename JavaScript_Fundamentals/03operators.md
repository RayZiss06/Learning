# Operators

Operators are symbols or keywords used to perform operations on values and variables.

```javascript
const a = 10;
const b = 5;

const result = a + b;

console.log(result); // 15
```

In `a + b`:

- `a` and `b` are **operands**
- `+` is the **operator**

---

## Types of Operators

The main JavaScript operators are:

1. Arithmetic Operators
2. Assignment Operators
3. Comparison Operators
4. Equality Operators
5. Logical Operators
6. Nullish Coalescing Operator
7. Unary Operators
8. Increment and Decrement Operators
9. Ternary Operator
10. Optional Chaining
11. Operator Precedence

---

# 1. Arithmetic Operators

Arithmetic operators perform mathematical operations.

| Operator | Description | Example | Result |
|---|---|---|---|
| `+` | Addition | `10 + 5` | `15` |
| `-` | Subtraction | `10 - 5` | `5` |
| `*` | Multiplication | `10 * 5` | `50` |
| `/` | Division | `10 / 5` | `2` |
| `%` | Remainder | `10 % 3` | `1` |
| `**` | Exponentiation | `2 ** 3` | `8` |

## Addition

```javascript
const result = 10 + 5;

console.log(result); // 15
```

The `+` operator can also concatenate strings.

```javascript
const firstName = "John";
const lastName = "Doe";

console.log(firstName + " " + lastName);
// John Doe
```

### Addition and Type Coercion

If one operand is a string, `+` can perform string concatenation.

```javascript
console.log(10 + "5"); // "105" string
console.log("10" + 5); // "105" string
```

Compare this with other arithmetic operators:

```javascript
console.log("10" - 5); // 5  number
console.log("10" * 2); // 20  number
console.log("10" / 2); // 5  number
```

`+` is special because it can mean either numeric addition or string concatenation.

---

## Remainder `%`

The remainder operator returns the remainder after division.

```javascript
console.log(10 % 3); // 1
console.log(20 % 5); // 0
```

A common use is checking whether a number is even or odd.

```javascript
const number = 10;

if (number % 2 === 0) {
    console.log("Even");
}
```

---

## Exponentiation `**`

Exponentiation raises the left operand to the power of the right operand.

```javascript
console.log(2 ** 3); // 8    (2^3)
console.log(5 ** 2); // 25   (5^2)
```

---

# 2. Assignment Operators

The assignment operator `=` assigns a value to a variable.

```javascript
let age = 25;
```

Compound assignment operators combine an operation with assignment.

| Operator | Example | Equivalent |
|---|---|---|
| `=` | `x = 10` | Assign `10` |
| `+=` | `x += 5` | `x = x + 5` |
| `-=` | `x -= 5` | `x = x - 5` |
| `*=` | `x *= 2` | `x = x * 2` |
| `/=` | `x /= 2` | `x = x / 2` |
| `%=` | `x %= 3` | `x = x % 3` |
| `**=` | `x **= 2` | `x = x ** 2` |

Example:

```javascript
let score = 10;

score += 5;

console.log(score); // 15
```

---

# 3. Comparison Operators

Comparison operators compare values and return a Boolean:

```javascript
true
false
```

| Operator | Meaning |
|---|---|
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

Example:

```javascript
const age = 25;

console.log(age > 18);  // true
console.log(age < 18);  // false
console.log(age >= 25); // true
console.log(age <= 20); // false
```

They are commonly used in conditions.

```javascript
if (age >= 18) {
    console.log("Adult");
}
```

---

# 4. Equality Operators

JavaScript has two forms of equality comparison:

```javascript
==   // loose equality
===  // strict equality
```

And two inequality operators:

```javascript
!=   // loose inequality
!==  // strict inequality
```

---

## Loose Equality `==`

Loose equality allows type coercion when comparing different types.

```javascript
console.log(5 == "5"); // true
```

Other examples:

```javascript
console.log(0 == false);        // true  any value except 1 is false
console.log(1 == true);         // true 
console.log("" == false);       // true
console.log(null == undefined); // true
```

Loose equality follows JavaScript's coercion rules, which can sometimes produce surprising results.

---

## Strict Equality `===`

Strict equality does not perform type coercion.

```javascript
console.log(5 === "5"); // false
console.log(5 === 5);   // true
```

Examples:

```javascript
console.log(true === true);      // true
console.log(true === 1);         // false
console.log(null === undefined); // false
```

### Recommended Practice

In modern JavaScript, generally prefer:

```javascript
===
!==
```

unless loose equality is intentionally required.

---

# 5. Logical Operators

Logical operators are commonly used to combine or modify conditions.

The main logical operators are:

| Operator | Name |
|---|---|
| `&&` | AND |
| `||` | OR |
| `!` | NOT |

---

## Logical AND `&&`

With Boolean operands, `&&` is true only when both operands are true.

```javascript
true && true;   // true
true && false;  // false
false && true;  // false
false && false; // false
```

Example:

```javascript
const age = 25;
const hasLicense = true;

if (age >= 18 && hasLicense) {
    console.log("Can drive");
}
```

Both conditions must be truthy.

---

## Logical OR `||`

With Boolean operands, `||` is true when at least one operand is true.

```javascript
true || true;   // true
true || false;  // true
false || true;  // true
false || false; // false
```

Example:

```javascript
const isAdmin = false;
const isModerator = true;

if (isAdmin || isModerator) {
    console.log("Access granted");
}
```

---

## Logical NOT `!`

`!` converts the operand to Boolean and reverses it.

```javascript
console.log(!true);  // false
console.log(!false); // true
```

Example:

```javascript
const isLoggedIn = false;

if (!isLoggedIn) {
    console.log("Please log in");
}
```

It also works with truthy and falsy values:

```javascript
console.log(!0);       // true
console.log(!1);       // false
console.log(!"hello"); // false
console.log(!null);    // true
```

### Double NOT `!!`

Double NOT is commonly used to explicitly convert a value to Boolean.

```javascript
console.log(!!"hello"); // true
console.log(!!"");      // false
console.log(!!1);       // true
console.log(!!0);       // false
```

Equivalent to:

```javascript
Boolean("hello");
```

---

# 6. Short-Circuit Evaluation

`&&` and `||` do not always return Boolean values.

They return one of their operands.

This is an important JavaScript concept.

---

## `&&` Short-Circuiting

`&&` returns the **first falsy value** it encounters.

If every operand is truthy, it returns the last operand.

```javascript
console.log(true && "Hello"); // "Hello"

console.log("Hello" && 100); // 100

console.log(false && "Hello"); // false

console.log("Hello" && 0 && "World"); // 0
```

Conceptually:

```javascript
A && B
```

If `A` is falsy, return `A`.

If `A` is truthy, evaluate and return `B`.

This is useful in React:

```jsx
{isLoggedIn && <Dashboard />}
```

If `isLoggedIn` is truthy, the component expression is evaluated.

---

## `||` Short-Circuiting

`||` returns the **first truthy value** it encounters.

If all values are falsy, it returns the last value.

```javascript
console.log(false || "Hello"); // "Hello"

console.log("" || "Default"); // "Default"

console.log(null || undefined || "Guest");
// "Guest"
```

This has traditionally been used for default values:

```javascript
const username = inputUsername || "Guest";
```

However, there is an important problem with this approach.

Consider:

```javascript
const count = 0;

const result = count || 10;

console.log(result); // 10
```

`0` may be a perfectly valid value, but it is falsy.

This is where `??` becomes useful.

---

# 7. Nullish Coalescing Operator `??`

The nullish coalescing operator returns the right operand only when the left operand is:

```javascript
null
```

or

```javascript
undefined
```

Example:

```javascript
const username = null;

const result = username ?? "Guest";

console.log(result); // "Guest"
```

Unlike `||`, values such as these are preserved:

```javascript
0
false
""
```

Example:

```javascript
console.log(0 || 10);  // 10
console.log(0 ?? 10);  // 0

console.log("" || "Default"); // "Default"
console.log("" ?? "Default"); // ""

console.log(false || true); // true
console.log(false ?? true); // false
```

### `||` vs `??`

Use:

```javascript
value || defaultValue
```

when you want the default for **any falsy value**.

Use:

```javascript
value ?? defaultValue
```

when you only want the default for **null or undefined**.

This distinction is very important when `0`, `false`, or `""` are valid values.

---

# 8. Logical Assignment Operators

JavaScript also provides logical assignment operators.

```javascript
||=
&&=
??=
```

---

## OR Assignment `||=`

Assigns the right value if the current value is falsy.

```javascript
let username = "";

username ||= "Guest";

console.log(username); // "Guest"
```

Conceptually similar to:

```javascript
username || (username = "Guest");
```

---

## AND Assignment `&&=`

Assigns the right value if the current value is truthy.

```javascript
let username = "John";

username &&= "Updated";

console.log(username); // "Updated"
```

---

## Nullish Assignment `??=`

Assigns the right value only if the current value is `null` or `undefined`.

```javascript
let username = null;

username ??= "Guest";

console.log(username); // "Guest"
```

But:

```javascript
let count = 0;

count ??= 10;

console.log(count); // 0
```

---

# 9. Increment and Decrement Operators

Increment:

```javascript
++
```

Decrement:

```javascript
--
```

Example:

```javascript
let count = 5;

count++;

console.log(count); // 6
```

Equivalent to:

```javascript
count = count + 1;
```

Similarly:

```javascript
count--;
```

decreases the value by `1`.

---

## Prefix vs Postfix

### Postfix

```javascript
let x = 5;

let y = x++;

console.log(x); // 6
console.log(y); // 5
```

`x++` returns the old value and then increments `x`.

### Prefix

```javascript
let x = 5;

let y = ++x;

console.log(x); // 6
console.log(y); // 6
```

`++x` increments first and then returns the new value.

Same concept applies to:

```javascript
x--
--x
```

---

# 10. Unary Operators

Unary operators operate on a single operand.

Important unary operators include:

```javascript
typeof
!
+
-
delete
```

---

## `typeof`

Returns a string describing the type category of a value.

```javascript
typeof "Hello";   // "string"
typeof 10;        // "number"
typeof true;      // "boolean"
typeof undefined; // "undefined"
typeof {};        // "object"
```

Remember:

```javascript
typeof null; // "object"
typeof [];   // "object"
```

---

## Unary Plus `+`

Attempts to convert a value to a number.

```javascript
console.log(+"10"); // 10
console.log(+"5.5"); // 5.5
console.log(+true); // 1
console.log(+false); // 0
```

Invalid numeric conversion produces `NaN`.

```javascript
console.log(+"hello"); // NaN
```

For readability, explicit conversion is often clearer:

```javascript
Number("10");
```

---

## Unary Minus `-`

Converts the operand to a number when necessary and negates it.

```javascript
const number = 10;

console.log(-number); // -10
```

Example with coercion:

```javascript
console.log(-"10"); // -10
```

---

## `delete`

Removes a property from an object.

```javascript
const user = {
    name: "John",
    age: 25
};

delete user.age;

console.log(user);
// { name: "John" }
```

`delete` is for object properties, not normal variable deletion.

---

# 11. Ternary Operator

The ternary operator is a concise conditional expression.

Syntax:

```javascript
condition ? valueIfTrue : valueIfFalse
```

Example:

```javascript
const age = 20;

const status = age >= 18 ? "Adult" : "Minor";

console.log(status); // "Adult"
```

Equivalent `if...else`:

```javascript
let status;

if (age >= 18) {
    status = "Adult";
} else {
    status = "Minor";
}
```

The ternary operator **returns a value**, which makes it useful inside expressions.

### React Example

```jsx
{isLoggedIn ? <Dashboard /> : <Login />}
```

Use ternaries for reasonably simple conditional expressions. Deeply nested ternaries can quickly become difficult to read.

---

# 12. Optional Chaining `?.`

Optional chaining safely accesses properties or calls something when the value before `?.` might be `null` or `undefined`.

Consider:

```javascript
const user = {
    name: "John"
};

console.log(user.address.city);
```

This throws an error because `address` is `undefined`.

With optional chaining:

```javascript
console.log(user.address?.city);
// undefined
```

No error is thrown.

Nested example:

```javascript
const user = {
    profile: {
        address: {
            city: "Kolkata"
        }
    }
};

console.log(user.profile?.address?.city);
// Kolkata
```

If something in the optional part of the chain is `null` or `undefined`, evaluation short-circuits and returns `undefined`.

---

## Optional Chaining with Methods

```javascript
user.sayHello?.();
```

The method is called only if `user.sayHello` is not `null` or `undefined`.

---

## Optional Chaining with Arrays / Computed Properties

```javascript
const users = null;

console.log(users?.[0]);
// undefined
```

Optional chaining is extremely common when handling API responses and nested data.

---

# 13. Combining `?.` and `??`

Optional chaining and nullish coalescing are commonly used together.

```javascript
const user = {
    profile: {}
};

const city = user.profile?.address?.city ?? "Unknown";

console.log(city); // "Unknown"
```

Conceptually:

```text
?. → safely access the value

?? → provide a fallback if the result is null or undefined
```

This pattern is very useful when working with API data.

---

# 14. Object and Array Operators

Some operators behave differently with objects and arrays because object values are compared by identity.

Example:

```javascript
const user1 = {
    name: "John"
};

const user2 = {
    name: "John"
};

console.log(user1 === user2); // false
```

Although their properties contain the same values, they are different objects.

```javascript
const user3 = user1;

console.log(user1 === user3); // true
```

Both variables refer to the same object.

The same applies to arrays:

```javascript
console.log([1, 2] === [1, 2]); // false

const numbers = [1, 2];
const copy = numbers;

console.log(numbers === copy); // true
```

---

# 15. Operator Precedence

When multiple operators appear in one expression, precedence determines which operation happens first.

```javascript
const result = 2 + 3 * 4;

console.log(result); // 14
```

Multiplication happens before addition:

```text
2 + (3 * 4)

2 + 12

14
```

Using parentheses changes the order:

```javascript
const result = (2 + 3) * 4;

console.log(result); // 20
```

You do not need to memorize the entire JavaScript precedence table.

When an expression could be unclear, use parentheses:

```javascript
const result = (a + b) * c;
```

This makes the intended behavior explicit.

---

# 16. Common Operator Traps

## `=` vs `===`

`=` assigns a value:

```javascript
let age = 20;
```

`===` compares values:

```javascript
age === 20;
```

---

## `==` vs `===`

```javascript
5 == "5";  // true
5 === "5"; // false
```

Prefer strict equality:

```javascript
===
!==
```

---

## `||` vs `??`

```javascript
const count = 0;

console.log(count || 10); // 10
console.log(count ?? 10); // 0
```

`||` reacts to any falsy value.

`??` reacts only to `null` and `undefined`.

---

## Objects Are Compared by Identity

```javascript
{} === {}; // false
[] === []; // false
```

Each expression creates a different object.

---

## Logical Operators Return Values

Do not think of `&&` and `||` as operators that always return `true` or `false`.

```javascript
"John" && 25; // 25

null || "Guest"; // "Guest"
```

They return operands according to short-circuit rules.

---

# 17. React-Relevant Operator Patterns

Operators appear constantly in React.

## Conditional Rendering with `&&`

```jsx
{isLoggedIn && <Dashboard />}
```

---

## Conditional Rendering with Ternary

```jsx
{isLoggedIn ? <Dashboard /> : <Login />}
```

---

## Default Value with `??`

```javascript
const username = user.name ?? "Guest";
```

---

## Safe Property Access with `?.`

```javascript
const city = user?.profile?.address?.city;
```

---

## Combining Them

```javascript
const username = user?.profile?.name ?? "Guest";
```

And in JSX:

```jsx
<h1>{user?.name ?? "Guest"}</h1>
```

These patterns become very common when working with React components, props, state, and API responses.

---

# Quick Revision

```text
Arithmetic
+  -  *  /  %  **

Assignment
=  +=  -=  *=  /=  %=  **=

Comparison
>  <  >=  <=

Equality
==   Loose equality
===  Strict equality
!=   Loose inequality
!==  Strict inequality

Logical
&&   AND / first falsy or last value
||   OR / first truthy or last value
!    NOT

Nullish
??   Fallback only for null/undefined

Logical Assignment
||=
&&=
??=

Increment / Decrement
++
--

Conditional
condition ? trueValue : falseValue

Optional Chaining
?.

Important Rules
===  → Prefer strict equality
&&   → First falsy, otherwise last operand
||   → First truthy, otherwise last operand
??   → Right side only for null/undefined
?.   → Safe access when preceding value may be null/undefined
```

