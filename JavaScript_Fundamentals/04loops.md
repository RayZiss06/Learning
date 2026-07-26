#  Loops

Loops are used to execute a block of code repeatedly.

Instead of writing:

```javascript
console.log(1);
console.log(2);
console.log(3);
console.log(4);
console.log(5);
```

we can use a loop:

```javascript
for (let i = 1; i <= 5; i++) {
    console.log(i);
}
```

Output:

```text
1
2
3
4
5
```

---

# 1. Types of Loops in JavaScript

The main looping constructs are:

```text
Loops
├── for
├── while
├── do...while
├── for...of
└── for...in
```

We also commonly use:

```text
break
continue
```

to control loop execution.

JavaScript arrays also provide methods such as:

```javascript
forEach()
map()
filter()
reduce()
```

These are not language-level loop statements, but they are heavily used for iterating over arrays and will be covered properly under **Arrays**.

---

# 2. `for` Loop

A `for` loop is useful when we want explicit control over:

- initialization
- condition
- update

Syntax:

```javascript
for (initialization; condition; update) {
    // code
}
```

Example:

```javascript
for (let i = 0; i < 5; i++) {
    console.log(i);
}
```

Output:

```text
0
1
2
3
4
```

The loop has three main parts:

```javascript
for (let i = 0; i < 5; i++)
```

```text
let i = 0
    ↓
Initialization

i < 5
    ↓
Condition

i++
    ↓
Update
```

---

# 3. How a `for` Loop Executes

Consider:

```javascript
for (let i = 0; i < 3; i++) {
    console.log(i);
}
```

Execution happens approximately like this:

```text
1. let i = 0

2. Check:
   i < 3
   0 < 3 → true

3. Execute body:
   console.log(0)

4. Execute:
   i++

5. Check:
   1 < 3 → true

6. Execute body:
   console.log(1)

7. Execute:
   i++

8. Check:
   2 < 3 → true

9. Execute body:
   console.log(2)

10. Execute:
    i++

11. Check:
    3 < 3 → false

12. Loop ends
```

Important:

The initialization:

```javascript
let i = 0
```

runs only once.

The condition:

```javascript
i < 3
```

is checked before each iteration.

The update:

```javascript
i++
```

runs after each completed iteration.

---

# 4. Looping Backwards

A `for` loop does not have to increment.

```javascript
for (let i = 5; i >= 1; i--) {
    console.log(i);
}
```

Output:

```text
5
4
3
2
1
```

---

# 5. Custom Increment

We can increment by any amount.

```javascript
for (let i = 0; i <= 10; i += 2) {
    console.log(i);
}
```

Output:

```text
0
2
4
6
8
10
```

---

# 6. Looping Through an Array with `for`

Consider:

```javascript
const fruits = ["Apple", "Banana", "Mango"];
```

We can iterate through the array using indexes:

```javascript
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}
```

Output:

```text
Apple
Banana
Mango
```

Remember:

```text
Array indexes start at 0.

fruits.length = 3

Valid indexes:
0
1
2
```

Therefore:

```javascript
i < fruits.length
```

is normally correct.

Using:

```javascript
i <= fruits.length
```

would run one extra iteration:

```javascript
const fruits = ["Apple", "Banana", "Mango"];

for (let i = 0; i <= fruits.length; i++) {
    console.log(fruits[i]);
}
```

Output:

```text
Apple
Banana
Mango
undefined
```

---

# 7. `while` Loop

A `while` loop repeatedly executes while its condition remains truthy.

Syntax:

```javascript
while (condition) {
    // code
}
```

Example:

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
    i++;
}
```

Output:

```text
0
1
2
3
4
```

The execution is:

```text
Check condition
      ↓
   true?
   /   \
 yes    no
  ↓      ↓
execute  stop
  ↓
check again
```

---

# 8. When to Use `while`

`while` is useful when the number of iterations is not naturally expressed as a fixed counter.

Example:

```javascript
let attempts = 0;

while (attempts < 3) {
    console.log("Trying...");
    attempts++;
}
```

Another conceptual example:

```javascript
while (isConnected) {
    // continue while condition remains true
}
```

Be careful: in real applications, a synchronous `while` loop waiting for an external condition to change can block JavaScript execution. It should not be used to wait for network requests, user input, timers, etc.

---

# 9. Infinite Loops

An infinite loop occurs when the condition never becomes false.

Example:

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
}
```

Here:

```javascript
i
```

never changes.

Therefore:

```javascript
i < 5
```

always remains true.

Correct version:

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
    i++;
}
```

Another intentional infinite loop is:

```javascript
while (true) {
    // ...
}
```

Such loops require some mechanism such as `break` to terminate.

---

# 10. `do...while` Loop

A `do...while` loop is similar to `while`, but the condition is checked **after** executing the loop body.

Syntax:

```javascript
do {
    // code
} while (condition);
```

Example:

```javascript
let i = 0;

do {
    console.log(i);
    i++;
} while (i < 5);
```

Output:

```text
0
1
2
3
4
```

---

# 11. `while` vs `do...while`

The important difference is:

```text
while
→ condition checked first
→ body may execute zero times

do...while
→ body executed first
→ condition checked afterward
→ body executes at least once
```

Example:

```javascript
let number = 10;

while (number < 5) {
    console.log("while");
}
```

Nothing is printed because:

```text
10 < 5 → false
```

But:

```javascript
let number = 10;

do {
    console.log("do while");
} while (number < 5);
```

Output:

```text
do while
```

Even though the condition is false, the body runs once before the condition is checked.

---

# 12. `break`

`break` immediately terminates the nearest loop or `switch` statement containing it.

Example:

```javascript
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break;
    }

    console.log(i);
}
```

Output:

```text
0
1
2
3
4
```

When:

```javascript
i === 5
```

the loop stops completely.

Conceptually:

```text
Iteration
   ↓
condition
   ↓
break?
 /    \
yes    no
 ↓      ↓
stop   continue
```

---

# 13. `continue`

`continue` skips the rest of the **current iteration** and proceeds with the next iteration.

Example:

```javascript
for (let i = 0; i < 5; i++) {
    if (i === 2) {
        continue;
    }

    console.log(i);
}
```

Output:

```text
0
1
3
4
```

When:

```javascript
i === 2
```

that iteration is skipped.

The loop itself continues.

---

# 14. `break` vs `continue`

```text
break
→ terminate the loop completely

continue
→ skip current iteration
→ continue with the next iteration
```

Example:

```javascript
for (let i = 1; i <= 5; i++) {
    if (i === 3) {
        break;
    }

    console.log(i);
}
```

Output:

```text
1
2
```

Compare:

```javascript
for (let i = 1; i <= 5; i++) {
    if (i === 3) {
        continue;
    }

    console.log(i);
}
```

Output:

```text
1
2
4
5
```

---

# 15. Nested Loops

A loop can exist inside another loop.

```javascript
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 2; j++) {
        console.log(i, j);
    }
}
```

Output:

```text
1 1
1 2
2 1
2 2
3 1
3 2
```

For every iteration of the outer loop, the inner loop runs completely.

Conceptually:

```text
Outer i = 1
    Inner j = 1
    Inner j = 2

Outer i = 2
    Inner j = 1
    Inner j = 2

Outer i = 3
    Inner j = 1
    Inner j = 2
```

Nested loops are useful for structures such as:

- matrices
- grids
- tables
- nested collections

But they can also increase computational work quickly.

For example:

```javascript
for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
        // ...
    }
}
```

The body may execute approximately:

```text
n × n = n²
```

times.

---

# 16. `for...of`

`for...of` iterates over the **values of an iterable**.

It is commonly used with:

- Arrays
- Strings
- Maps
- Sets
- Other iterable objects

Example with an array:

```javascript
const fruits = ["Apple", "Banana", "Mango"];

for (const fruit of fruits) {
    console.log(fruit);
}
```

Output:

```text
Apple
Banana
Mango
```

Compared with a traditional `for` loop:

```javascript
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}
```

`for...of` is cleaner when you only need the values.

---

# 17. `for...of` with Strings

Strings are iterable.

```javascript
const name = "John";

for (const character of name) {
    console.log(character);
}
```

Output:

```text
J
o
h
n
```

---

# 18. Getting Array Index with `for...of`

`for...of` directly gives values, not indexes.

```javascript
const fruits = ["Apple", "Banana", "Mango"];

for (const fruit of fruits) {
    console.log(fruit);
}
```

If both index and value are needed, we can use:

```javascript
fruits.entries();
```

Example:

```javascript
for (const [index, fruit] of fruits.entries()) {
    console.log(index, fruit);
}
```

Output:

```text
0 Apple
1 Banana
2 Mango
```

This uses array destructuring, which we will cover later.

---

# 19. `for...in`

`for...in` iterates over an object's **enumerable string property keys**, including enumerable inherited properties.

It is mainly useful for object property enumeration when that behavior is intended.

Example:

```javascript
const user = {
    name: "John",
    age: 25,
    city: "Kolkata"
};

for (const key in user) {
    console.log(key);
}
```

Output:

```text
name
age
city
```

To access the corresponding value:

```javascript
for (const key in user) {
    console.log(user[key]);
}
```

Output:

```text
John
25
Kolkata
```

To access both:

```javascript
for (const key in user) {
    console.log(key, user[key]);
}
```

---

# 20. Why `user[key]` Instead of `user.key`?

Consider:

```javascript
const user = {
    name: "John",
    age: 25
};

for (const key in user) {
    console.log(key);
}
```

`key` contains values such as:

```text
"name"
"age"
```

Therefore:

```javascript
user[key]
```

becomes conceptually:

```javascript
user["name"];
user["age"];
```

But:

```javascript
user.key
```

would literally search for a property named:

```text
key
```

which is not what we want.

---

# 21. `for...of` vs `for...in`

This distinction is important.

## `for...of`

Iterates over **values from an iterable**.

```javascript
const fruits = ["Apple", "Banana", "Mango"];

for (const fruit of fruits) {
    console.log(fruit);
}
```

Output:

```text
Apple
Banana
Mango
```

## `for...in`

Iterates over **enumerable string property keys**.

```javascript
const user = {
    name: "John",
    age: 25
};

for (const key in user) {
    console.log(key);
}
```

Output:

```text
name
age
```

Quick rule:

```text
for...of
→ iterable values

for...in
→ enumerable property keys
```

---

# 22. `for...in` with Arrays

Technically, `for...in` can iterate over an array:

```javascript
const fruits = ["Apple", "Banana", "Mango"];

for (const index in fruits) {
    console.log(index);
}
```

Output commonly looks like:

```text
0
1
2
```

And:

```javascript
for (const index in fruits) {
    console.log(fruits[index]);
}
```

prints the values.

However, `for...in` is generally **not the appropriate tool for array iteration**.

Why?

Because it enumerates property keys rather than array values and can include additional enumerable properties.

For arrays, prefer:

```javascript
for
for...of
forEach()
map()
filter()
```

depending on what you need.

---

# 23. Plain Objects Are Not `for...of` Iterable by Default

This does not work:

```javascript
const user = {
    name: "John",
    age: 25
};

for (const value of user) {
    console.log(value);
}
```

It throws a `TypeError` because a normal object is not iterable by default.

Instead, we can use:

```javascript
Object.keys()
Object.values()
Object.entries()
```

---

# 24. `Object.keys()`

Returns an array containing the object's own enumerable string keys.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(Object.keys(user));
```

Result:

```javascript
["name", "age"]
```

We can iterate over them:

```javascript
for (const key of Object.keys(user)) {
    console.log(key);
}
```

---

# 25. `Object.values()`

Returns an array containing the object's own enumerable string-keyed property values.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(Object.values(user));
```

Result:

```javascript
["John", 25]
```

Iteration:

```javascript
for (const value of Object.values(user)) {
    console.log(value);
}
```

Output:

```text
John
25
```

---

# 26. `Object.entries()`

Returns an array containing `[key, value]` pairs for the object's own enumerable string-keyed properties.

```javascript
const user = {
    name: "John",
    age: 25
};

console.log(Object.entries(user));
```

Conceptually:

```javascript
[
    ["name", "John"],
    ["age", 25]
]
```

This makes it convenient to iterate over both key and value:

```javascript
for (const [key, value] of Object.entries(user)) {
    console.log(key, value);
}
```

Output:

```text
name John
age 25
```

This is often a convenient alternative to `for...in` when you specifically want an object's **own** enumerable properties.

---

# 27. `break` and `continue` with `for...of`

Both work normally with `for...of`.

Example using `break`:

```javascript
const numbers = [10, 20, 30, 40];

for (const number of numbers) {
    if (number === 30) {
        break;
    }

    console.log(number);
}
```

Output:

```text
10
20
```

Example using `continue`:

```javascript
const numbers = [10, 20, 30, 40];

for (const number of numbers) {
    if (number === 30) {
        continue;
    }

    console.log(number);
}
```

Output:

```text
10
20
40
```

---

# 28. `break` and `continue` in Nested Loops

By default, `break` terminates only the nearest loop.

```javascript
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 3; j++) {
        if (j === 2) {
            break;
        }

        console.log(i, j);
    }
}
```

Output:

```text
1 1
2 1
3 1
```

The inner loop stops when `j === 2`, but the outer loop continues.

JavaScript also supports labeled statements for controlling outer loops, although they are uncommon in everyday application code.

```javascript
outerLoop:
for (let i = 1; i <= 3; i++) {
    for (let j = 1; j <= 3; j++) {
        if (i === 2 && j === 2) {
            break outerLoop;
        }

        console.log(i, j);
    }
}
```

Here:

```javascript
break outerLoop;
```

terminates the labeled outer loop.

Labels exist, but most application code can usually be structured without them.

---

# 29. Loop Variable Scope

When `let` is declared inside a loop:

```javascript
for (let i = 0; i < 3; i++) {
    console.log(i);
}
```

`i` is scoped to the loop.

This does not work afterward:

```javascript
console.log(i); // ReferenceError
```

Using `var` behaves differently:

```javascript
for (var i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i); // 3
```

because `var` is function-scoped rather than block-scoped.

Modern JavaScript generally uses `let` for loop counters:

```javascript
for (let i = 0; i < 5; i++) {
    // ...
}
```

---

# 30. `const` with `for...of`

You will commonly see:

```javascript
const users = ["John", "Alice", "Bob"];

for (const user of users) {
    console.log(user);
}
```

Why can `const` be used when `user` changes on every iteration?

Conceptually, each iteration gets a new block-scoped binding.

So this is valid:

```javascript
for (const user of users) {
    console.log(user);
}
```

But this would not be:

```javascript
for (const user of users) {
    user = "Other"; // TypeError
}
```

You cannot reassign that iteration's `const` binding.

---

# 31. Loops vs Array Methods

Suppose:

```javascript
const numbers = [1, 2, 3, 4];
```

Traditional loop:

```javascript
for (let i = 0; i < numbers.length; i++) {
    console.log(numbers[i]);
}
```

`for...of`:

```javascript
for (const number of numbers) {
    console.log(number);
}
```

`forEach()`:

```javascript
numbers.forEach((number) => {
    console.log(number);
});
```

They can all iterate through the array, but they are not interchangeable in every situation.

For example, `break` works with a loop:

```javascript
for (const number of numbers) {
    if (number === 3) {
        break;
    }
}
```

But you cannot use `break` to terminate a `forEach()` callback:

```javascript
numbers.forEach((number) => {
    // break; // SyntaxError
});
```

Array methods will be covered in detail under **Arrays**.

---

# 32. `for...of` with `await`

A useful distinction for asynchronous JavaScript is that a normal loop can work naturally with `await` inside an `async` function.

```javascript
async function processUsers(users) {
    for (const user of users) {
        await processUser(user);
    }
}
```

This processes each item sequentially:

```text
User 1 completes
        ↓
User 2 starts
        ↓
User 2 completes
        ↓
User 3 starts
```

By comparison, this pattern is commonly misunderstood:

```javascript
users.forEach(async (user) => {
    await processUser(user);
});
```

`forEach()` itself does not wait for the promises returned by its callback.

We will cover this properly when revising:

```text
Promises
async / await
```

---

# 33. Common Loop Mistakes

## Mistake 1: Off-by-One Error

Incorrect:

```javascript
const numbers = [10, 20, 30];

for (let i = 0; i <= numbers.length; i++) {
    console.log(numbers[i]);
}
```

The final iteration accesses:

```javascript
numbers[3]
```

which is:

```javascript
undefined
```

Correct:

```javascript
for (let i = 0; i < numbers.length; i++) {
    console.log(numbers[i]);
}
```

---

## Mistake 2: Infinite Loop

Incorrect:

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
}
```

`i` never changes.

Correct:

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
    i++;
}
```

---

## Mistake 3: Using `for...in` for Array Values

Avoid:

```javascript
const fruits = ["Apple", "Banana"];

for (const fruit in fruits) {
    console.log(fruit);
}
```

This prints property keys:

```text
0
1
```

If you want values:

```javascript
for (const fruit of fruits) {
    console.log(fruit);
}
```

Output:

```text
Apple
Banana
```

---

## Mistake 4: Assuming `for...of` Works on Plain Objects

Incorrect:

```javascript
const user = {
    name: "John",
    age: 25
};

for (const value of user) {
    console.log(value);
}
```

Use:

```javascript
for (const value of Object.values(user)) {
    console.log(value);
}
```

Or:

```javascript
for (const [key, value] of Object.entries(user)) {
    console.log(key, value);
}
```

---

## Mistake 5: Forgetting That `break` and `continue` Are Different

```javascript
break;
```

means:

```text
Stop the loop.
```

Whereas:

```javascript
continue;
```

means:

```text
Skip the rest of this iteration.
Continue the loop.
```

---

# 34. Which Loop Should You Use?

## Use `for`

When you need explicit control over an index or counter.

```javascript
for (let i = 0; i < 10; i++) {
    // ...
}
```

---

## Use `while`

When repetition is primarily controlled by a condition.

```javascript
while (condition) {
    // ...
}
```

---

## Use `do...while`

When the body must execute at least once before checking the condition.

```javascript
do {
    // ...
} while (condition);
```

---

## Use `for...of`

When iterating over values from an iterable such as an array.

```javascript
for (const item of items) {
    // ...
}
```

---

## Use `for...in`

When you intentionally need to enumerate an object's enumerable string property keys.

```javascript
for (const key in object) {
    // ...
}
```

For own object properties, these are often convenient:

```javascript
Object.keys(object);
Object.values(object);
Object.entries(object);
```

---

# 35. React Relevance

Traditional loops are still useful JavaScript knowledge, but when rendering collections in React, `map()` is particularly important.

Suppose:

```javascript
const users = ["John", "Alice", "Bob"];
```

In normal JavaScript, we could iterate:

```javascript
for (const user of users) {
    console.log(user);
}
```

But React commonly transforms an array of data into an array of JSX elements:

```jsx
const users = ["John", "Alice", "Bob"];

return (
    <ul>
        {users.map((user) => (
            <li key={user}>{user}</li>
        ))}
    </ul>
);
```

Why `map()`?

Because `map()` returns a new array.

Conceptually:

```text
["John", "Alice", "Bob"]

        map()

          ↓

[
    <li>John</li>,
    <li>Alice</li>,
    <li>Bob</li>
]
```

React can render that resulting array of elements.

We'll cover:

```javascript
map()
filter()
find()
some()
every()
reduce()
forEach()
```

properly under **Arrays**.

---

# 36. Quick Revision

```text
JavaScript Loops

for
→ explicit initialization, condition and update

while
→ repeat while condition is truthy
→ may execute zero times

do...while
→ body executes before condition check
→ executes at least once

for...of
→ iterate over values of iterables
→ commonly arrays, strings, maps and sets

for...in
→ enumerate enumerable string property keys
→ mainly used with objects when that behavior is intended

break
→ terminate the nearest loop

continue
→ skip the rest of the current iteration
```

### Traditional `for`

```javascript
for (let i = 0; i < 5; i++) {
    console.log(i);
}
```

### `while`

```javascript
let i = 0;

while (i < 5) {
    console.log(i);
    i++;
}
```

### `do...while`

```javascript
let i = 0;

do {
    console.log(i);
    i++;
} while (i < 5);
```

### `for...of`

```javascript
for (const value of array) {
    console.log(value);
}
```

### `for...in`

```javascript
for (const key in object) {
    console.log(key, object[key]);
}
```

### Object Entries

```javascript
for (const [key, value] of Object.entries(object)) {
    console.log(key, value);
}
```

### Important Distinction

```text
for...of → values from iterables

for...in → enumerable property keys
```

