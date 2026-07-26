#  Arrays

A **JavaScript array** is an ordered collection of values stored inside a single variable.

Example:

```javascript
const fruits = ["Apple", "Banana", "Mango"];
```

Structure:

```text
Index       0          1         2
            ↓          ↓         ↓
fruits = ["Apple", "Banana", "Mango"]
```

Unlike objects, which usually access values using property names:

```javascript
user.name
```

arrays commonly access values using **numeric indexes**:

```javascript
fruits[0]
```

Arrays are one of the most important data structures in JavaScript and are used everywhere:

```text
Lists of users
API responses
Products
Messages
Notifications
Database records
Search results
React list rendering
State management
Data processing
```

---

# 1. What Is an Array?

Suppose we want to store multiple usernames.

Without an array:

```javascript
const user1 = "John";
const user2 = "Alice";
const user3 = "Bob";
```

With an array:

```javascript
const users = [
    "John",
    "Alice",
    "Bob"
];
```

Now the values belong to one collection:

```text
users
│
├── [0] → "John"
├── [1] → "Alice"
└── [2] → "Bob"
```

---

# 2. Creating Arrays

The most common way to create an array is using an **array literal**.

```javascript
const numbers = [10, 20, 30];
```

Syntax:

```javascript
const arrayName = [
    value1,
    value2,
    value3
];
```

Example:

```javascript
const technologies = [
    "JavaScript",
    "React",
    "Node.js"
];
```

---

# 3. Creating Arrays with `new Array()`

Arrays can also be created using:

```javascript
const numbers = new Array(
    10,
    20,
    30
);
```

Result:

```javascript
[10, 20, 30]
```

But array literal syntax is usually preferred:

```javascript
const numbers = [10, 20, 30];
```

because it is shorter and clearer.

Be careful with:

```javascript
const numbers = new Array(3);
```

This creates an array with length `3` containing three empty slots.

It does **not** create:

```javascript
[3]
```

---

# 4. Arrays Can Store Different Data Types

JavaScript arrays can contain different types of values.

```javascript
const data = [
    "John",
    25,
    true,
    null,
    undefined,
    { city: "Kolkata" },
    [1, 2, 3],
    function () {
        console.log("Hello");
    }
];
```

An array can contain:

```text
Strings
Numbers
Booleans
null
undefined
Objects
Arrays
Functions
and other JavaScript values
```

In real applications, arrays often contain values of a consistent logical type because they are easier to work with.

---

# 5. Array Indexes

Arrays use **zero-based indexing**.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];
```

Indexes:

```text
Value      Apple    Banana    Mango
Index        0         1        2
```

The first element is:

```javascript
fruits[0]
```

not:

```javascript
fruits[1]
```

Remember:

```text
First Element  → index 0
Second Element → index 1
Third Element  → index 2
```

---

# 6. Accessing Array Elements

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

console.log(fruits[0]);
console.log(fruits[1]);
console.log(fruits[2]);
```

Output:

```text
Apple
Banana
Mango
```

Syntax:

```javascript
array[index]
```

---

# 7. Accessing an Index That Does Not Exist

```javascript
const fruits = [
    "Apple",
    "Banana"
];

console.log(fruits[10]);
```

Output:

```text
undefined
```

JavaScript does not throw an error simply because an array index is outside the current array contents.

---

# 8. Updating Array Elements

Array elements can be changed using their index.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

fruits[1] = "Orange";

console.log(fruits);
```

Output:

```javascript
[
    "Apple",
    "Orange",
    "Mango"
]
```

---

# 9. `const` Arrays Can Be Modified

This is the same concept we saw with objects.

```javascript
const fruits = [
    "Apple",
    "Banana"
];

fruits[0] = "Mango";
```

This is valid.

Also valid:

```javascript
fruits.push("Orange");
```

But this is not allowed:

```javascript
fruits = ["Grapes"];
```

Why?

```text
const
→ prevents reassignment of the variable

const
→ does NOT make the array immutable
```

---

# 10. Array `length`

Arrays have a `length` property.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

console.log(fruits.length);
```

Output:

```text
3
```

Structure:

```text
Array
["Apple", "Banana", "Mango"]

Indexes
0        1         2

Length
3
```

---

# 11. Last Element of an Array

Because indexing starts from `0`:

```text
Last index = length - 1
```

Example:

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

console.log(
    fruits[fruits.length - 1]
);
```

Output:

```text
Mango
```

---

# 12. `.at()`

Modern JavaScript provides:

```javascript
array.at(index)
```

Example:

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

console.log(fruits.at(0));
```

Output:

```text
Apple
```

A useful feature is negative indexing:

```javascript
console.log(fruits.at(-1));
```

Output:

```text
Mango
```

So:

```javascript
fruits.at(-1)
```

is a convenient way to access the last element.

---

# 13. Adding Elements with `push()`

`push()` adds one or more elements to the **end** of an array.

```javascript
const fruits = [
    "Apple",
    "Banana"
];

fruits.push("Mango");

console.log(fruits);
```

Output:

```javascript
[
    "Apple",
    "Banana",
    "Mango"
]
```

`push()` **mutates** the original array.

---

# 14. `push()` Return Value

`push()` returns the new array length.

```javascript
const fruits = [
    "Apple",
    "Banana"
];

const result = fruits.push("Mango");

console.log(result);
```

Output:

```text
3
```

The array becomes:

```javascript
[
    "Apple",
    "Banana",
    "Mango"
]
```

Important:

```text
push()
→ modifies original array
→ returns new length
```

---

# 15. Adding Elements with `unshift()`

`unshift()` adds elements to the **beginning**.

```javascript
const fruits = [
    "Banana",
    "Mango"
];

fruits.unshift("Apple");

console.log(fruits);
```

Output:

```javascript
[
    "Apple",
    "Banana",
    "Mango"
]
```

`unshift()` mutates the original array and returns its new length.

---

# 16. Removing Elements with `pop()`

`pop()` removes the last element.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

const removed = fruits.pop();

console.log(removed);
console.log(fruits);
```

Output:

```text
Mango
```

Array:

```javascript
[
    "Apple",
    "Banana"
]
```

Important:

```text
pop()
→ removes last element
→ mutates array
→ returns removed element
```

---

# 17. Removing Elements with `shift()`

`shift()` removes the first element.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

const removed = fruits.shift();

console.log(removed);
console.log(fruits);
```

Output:

```text
Apple
```

Array:

```javascript
[
    "Banana",
    "Mango"
]
```

---

# 18. `push`, `pop`, `shift`, `unshift`

Quick comparison:

```text
Method      Action                  Mutation
─────────────────────────────────────────────
push()      Add to end              Yes

pop()       Remove from end         Yes

unshift()   Add to beginning        Yes

shift()     Remove from beginning   Yes
```

Visual:

```text
                ARRAY
         [A, B, C, D]

unshift() → [X, A, B, C, D]

shift()   → [B, C, D]

push()    → [A, B, C, D, X]

pop()     → [A, B, C]
```

---

# 19. `slice()`

`slice()` extracts part of an array and returns a **new array**.

```javascript
const numbers = [
    10,
    20,
    30,
    40,
    50
];

const result = numbers.slice(1, 4);

console.log(result);
```

Output:

```javascript
[20, 30, 40]
```

Syntax:

```javascript
array.slice(start, end)
```

Important:

```text
start
→ included

end
→ excluded
```

---

# 20. `slice()` Does Not Mutate the Original

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const result = numbers.slice(1, 3);

console.log(result);
console.log(numbers);
```

Output:

```javascript
[20, 30]

[10, 20, 30, 40]
```

Important:

```text
slice()
→ returns new array
→ does NOT mutate original array
```

---

# 21. `slice()` with One Argument

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const result = numbers.slice(2);
```

Result:

```javascript
[30, 40]
```

Meaning:

```text
Start at index 2
Continue until end
```

---

# 22. `slice()` with Negative Indexes

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

console.log(numbers.slice(-2));
```

Output:

```javascript
[30, 40]
```

Negative indexes count from the end.

---

# 23. `splice()`

`splice()` can:

```text
Remove elements
Add elements
Replace elements
```

and it **mutates the original array**.

Syntax:

```javascript
array.splice(
    start,
    deleteCount,
    item1,
    item2,
    ...
);
```

---

# 24. Removing with `splice()`

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango",
    "Orange"
];

const removed = fruits.splice(1, 2);
```

Result:

```javascript
fruits
```

becomes:

```javascript
[
    "Apple",
    "Orange"
]
```

`removed` becomes:

```javascript
[
    "Banana",
    "Mango"
]
```

Meaning:

```text
Start index
→ 1

Delete count
→ 2
```

---

# 25. Adding with `splice()`

```javascript
const fruits = [
    "Apple",
    "Mango"
];

fruits.splice(
    1,
    0,
    "Banana"
);
```

Result:

```javascript
[
    "Apple",
    "Banana",
    "Mango"
]
```

Here:

```text
Start at index 1
Delete 0 elements
Insert "Banana"
```

---

# 26. Replacing with `splice()`

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

fruits.splice(
    1,
    1,
    "Orange"
);
```

Result:

```javascript
[
    "Apple",
    "Orange",
    "Mango"
]
```

Meaning:

```text
Start index 1
Delete 1 element
Insert "Orange"
```

---

# 27. `slice()` vs `splice()`

This is a common interview question.

```text
slice()
──────────────────────────
Extracts elements
Returns new array
Does NOT mutate original
End index excluded


splice()
──────────────────────────
Adds/removes/replaces elements
Mutates original array
Returns removed elements
Uses deleteCount
```

Remember:

```text
slice
→ copy/extract

splice
→ modify
```

---

# 28. `concat()`

`concat()` combines arrays and returns a new array.

```javascript
const frontend = [
    "React",
    "Vue"
];

const backend = [
    "Node.js",
    "Express"
];

const technologies =
    frontend.concat(backend);

console.log(technologies);
```

Output:

```javascript
[
    "React",
    "Vue",
    "Node.js",
    "Express"
]
```

The original arrays are not modified.

---

# 29. Spread Operator with Arrays

Modern JavaScript commonly uses the spread operator:

```javascript
...
```

to combine arrays.

```javascript
const frontend = [
    "React",
    "Vue"
];

const backend = [
    "Node.js",
    "Express"
];

const technologies = [
    ...frontend,
    ...backend
];
```

Result:

```javascript
[
    "React",
    "Vue",
    "Node.js",
    "Express"
]
```

---

# 30. Copying an Array with Spread

```javascript
const original = [
    10,
    20,
    30
];

const copy = [
    ...original
];
```

Now:

```javascript
console.log(
    original === copy
);
```

Output:

```text
false
```

They are separate outer arrays.

---

# 31. Arrays Are Objects

In JavaScript:

```javascript
const numbers = [
    1,
    2,
    3
];

console.log(typeof numbers);
```

Output:

```text
object
```

Arrays are specialized objects.

Therefore:

```javascript
typeof []
```

returns:

```text
object
```

---

# 32. Checking for an Array

Use:

```javascript
Array.isArray()
```

Example:

```javascript
console.log(
    Array.isArray([1, 2, 3])
);
```

Output:

```text
true
```

Example:

```javascript
console.log(
    Array.isArray({
        0: 1,
        1: 2
    })
);
```

Output:

```text
false
```

So prefer:

```javascript
Array.isArray(value)
```

when checking whether something is an array.

---

# 33. Arrays Are Reference Types

Consider:

```javascript
const array1 = [
    1,
    2,
    3
];

const array2 = array1;

array2.push(4);

console.log(array1);
```

Output:

```javascript
[1, 2, 3, 4]
```

Why?

Both variables refer to the same array.

Conceptually:

```text
array1 ─────┐
            │
            ▼
       ┌──────────────┐
       │ [1,2,3,4]    │
       └──────────────┘
            ▲
            │
array2 ─────┘
```

---

# 34. Array Equality

Consider:

```javascript
const a = [1, 2, 3];
const b = [1, 2, 3];

console.log(a === b);
```

Output:

```text
false
```

Even though they contain identical values, they are different arrays.

But:

```javascript
const a = [1, 2, 3];
const b = a;

console.log(a === b);
```

Output:

```text
true
```

Both refer to the same array.

---

# 35. Spread Creates a Shallow Copy

Consider:

```javascript
const users = [
    {
        name: "John"
    }
];

const copy = [
    ...users
];
```

The outer arrays are different:

```javascript
console.log(
    users === copy
);
```

Output:

```text
false
```

But:

```javascript
console.log(
    users[0] === copy[0]
);
```

Output:

```text
true
```

The nested object is shared.

---

# 36. Shallow Copy Problem

```javascript
const users = [
    {
        name: "John"
    }
];

const copy = [
    ...users
];

copy[0].name = "Alice";

console.log(
    users[0].name
);
```

Output:

```text
Alice
```

Why?

```text
users
 │
 ▼
Array A
 │
 └───────► User Object
              ▲
              │
Array B ──────┘
 ▲
 │
copy
```

Only the outer array was copied.

---

# 37. Nested Arrays

Arrays can contain other arrays.

```javascript
const matrix = [
    [1, 2],
    [3, 4],
    [5, 6]
];
```

Access:

```javascript
console.log(
    matrix[1][0]
);
```

Output:

```text
3
```

Why?

```text
matrix[1]
→ [3, 4]

matrix[1][0]
→ 3
```

---

# 38. Arrays of Objects

One of the most common structures in JavaScript:

```javascript
const users = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Alice"
    },
    {
        id: 3,
        name: "Bob"
    }
];
```

Access:

```javascript
console.log(
    users[1].name
);
```

Output:

```text
Alice
```

This structure appears constantly in:

```text
API responses
Database results
React lists
Search results
Tables
Dashboards
```

---

# 39. Array Destructuring

Array destructuring extracts values into variables.

```javascript
const numbers = [
    10,
    20,
    30
];

const [a, b, c] = numbers;

console.log(a);
console.log(b);
console.log(c);
```

Output:

```text
10
20
30
```

Unlike object destructuring, array destructuring is based on **position**.

---

# 40. Object vs Array Destructuring

Object:

```javascript
const user = {
    name: "John",
    age: 25
};

const {
    age,
    name
} = user;
```

Property names determine the values.

Array:

```javascript
const values = [
    "John",
    25
];

const [
    name,
    age
] = values;
```

Position determines the values.

Remember:

```text
Object Destructuring
→ property name

Array Destructuring
→ position
```

---

# 41. Skipping Elements During Destructuring

```javascript
const numbers = [
    10,
    20,
    30
];

const [
    first,
    ,
    third
] = numbers;

console.log(first);
console.log(third);
```

Output:

```text
10
30
```

The second element was skipped.

---

# 42. Default Values in Array Destructuring

```javascript
const numbers = [10];

const [
    first,
    second = 20
] = numbers;

console.log(first);
console.log(second);
```

Output:

```text
10
20
```

The default applies when the destructured value is `undefined`.

---

# 43. Rest Operator with Arrays

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const [
    first,
    ...remaining
] = numbers;
```

Now:

```javascript
console.log(first);
```

Output:

```text
10
```

And:

```javascript
console.log(remaining);
```

Output:

```javascript
[20, 30, 40]
```

---

# 44. Swapping Variables with Array Destructuring

Suppose:

```javascript
let a = 10;
let b = 20;
```

You can swap them:

```javascript
[a, b] = [b, a];
```

Now:

```javascript
console.log(a);
console.log(b);
```

Output:

```text
20
10
```

---

# 45. Looping Through Arrays with `for`

Traditional loop:

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

for (
    let i = 0;
    i < fruits.length;
    i++
) {
    console.log(fruits[i]);
}
```

Output:

```text
Apple
Banana
Mango
```

Use a traditional `for` loop when you need direct control over the index or iteration.

---

# 46. `for...of`

`for...of` iterates over array values.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

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

It is simpler when you only need the elements.

---

# 47. `for...in` with Arrays

You technically can write:

```javascript
const fruits = [
    "Apple",
    "Banana"
];

for (const index in fruits) {
    console.log(index);
}
```

Output:

```text
0
1
```

But `for...in` iterates enumerable property names and is generally not the preferred tool for array values.

Prefer:

```javascript
for...of
```

or array methods.

---

# 48. `forEach()`

`forEach()` executes a callback for each array element.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

fruits.forEach((fruit) => {
    console.log(fruit);
});
```

Output:

```text
Apple
Banana
Mango
```

---

# 49. `forEach()` Parameters

The callback can receive:

```text
element
index
array
```

Example:

```javascript
const fruits = [
    "Apple",
    "Banana"
];

fruits.forEach(
    (fruit, index, array) => {
        console.log(
            fruit,
            index,
            array
        );
    }
);
```

Conceptually:

```javascript
callback(
    element,
    index,
    array
);
```

---

# 50. `forEach()` Return Value

`forEach()` itself returns:

```text
undefined
```

Example:

```javascript
const result = [
    1,
    2,
    3
].forEach((number) => {
    return number * 2;
});

console.log(result);
```

Output:

```text
undefined
```

If you want to create a transformed array, use:

```javascript
map()
```

---

# 51. `map()`

`map()` transforms each element and returns a **new array**.

```javascript
const numbers = [
    1,
    2,
    3
];

const doubled = numbers.map(
    (number) => {
        return number * 2;
    }
);

console.log(doubled);
```

Output:

```javascript
[2, 4, 6]
```

Original:

```javascript
[1, 2, 3]
```

---

# 52. `map()` Mental Model

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) => number * 10
);
```

Think:

```text
Original             Callback            Result

1        ───────►    1 × 10    ───────►   10

2        ───────►    2 × 10    ───────►   20

3        ───────►    3 × 10    ───────►   30


Final:

[10, 20, 30]
```

Important:

```text
map()
→ transforms every element
→ returns new array
```

---

# 53. `map()` with Objects

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

const names = users.map(
    (user) => user.name
);

console.log(names);
```

Output:

```javascript
[
    "John",
    "Alice"
]
```

---

# 54. Returning Objects from `map()`

```javascript
const users = [
    {
        name: "John"
    },
    {
        name: "Alice"
    }
];

const updatedUsers = users.map(
    (user) => {
        return {
            ...user,
            active: true
        };
    }
);
```

Result:

```javascript
[
    {
        name: "John",
        active: true
    },
    {
        name: "Alice",
        active: true
    }
]
```

Short form:

```javascript
const updatedUsers = users.map(
    (user) => ({
        ...user,
        active: true
    })
);
```

The parentheses are important because:

```javascript
() => ({})
```

means:

```text
return an object
```

---

# 55. `filter()`

`filter()` creates a new array containing elements that satisfy a condition.

```javascript
const numbers = [
    1,
    2,
    3,
    4,
    5
];

const evenNumbers =
    numbers.filter((number) => {
        return number % 2 === 0;
    });

console.log(evenNumbers);
```

Output:

```javascript
[2, 4]
```

---

# 56. `filter()` Mental Model

```text
Value     Condition          Keep?

1         1 % 2 === 0        false ❌

2         2 % 2 === 0        true  ✓

3         3 % 2 === 0        false ❌

4         4 % 2 === 0        true  ✓

5         5 % 2 === 0        false ❌


Result:

[2, 4]
```

Important:

```text
filter()
→ callback returns truthy/falsy

truthy
→ keep element

falsy
→ remove element from result
```

The original array is not changed by `filter()` itself.

---

# 57. `filter()` with Objects

```javascript
const users = [
    {
        name: "John",
        active: true
    },
    {
        name: "Alice",
        active: false
    },
    {
        name: "Bob",
        active: true
    }
];

const activeUsers =
    users.filter((user) => {
        return user.active;
    });
```

Result:

```javascript
[
    {
        name: "John",
        active: true
    },
    {
        name: "Bob",
        active: true
    }
]
```

---

# 58. `find()`

`find()` returns the **first element** that satisfies a condition.

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

const result = numbers.find(
    (number) => {
        return number > 20;
    }
);

console.log(result);
```

Output:

```text
30
```

Only the first match is returned.

---

# 59. `find()` with Objects

Extremely common:

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
    (user) => {
        return user.id === 2;
    }
);

console.log(user);
```

Result:

```javascript
{
    id: 2,
    name: "Alice"
}
```

---

# 60. `find()` When Nothing Matches

```javascript
const numbers = [
    10,
    20,
    30
];

const result = numbers.find(
    (number) => number > 100
);

console.log(result);
```

Output:

```text
undefined
```

---

# 61. `findIndex()`

`findIndex()` returns the index of the first matching element.

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

const index = users.findIndex(
    (user) => user.id === 2
);

console.log(index);
```

Output:

```text
1
```

If nothing matches:

```text
-1
```

---

# 62. `find()` vs `filter()`

Consider:

```javascript
const numbers = [
    10,
    20,
    30,
    40
];
```

```javascript
numbers.find(
    (number) => number > 20
);
```

Result:

```text
30
```

But:

```javascript
numbers.filter(
    (number) => number > 20
);
```

Result:

```javascript
[30, 40]
```

Remember:

```text
find()
→ first matching element
→ element or undefined


filter()
→ all matching elements
→ new array
```

---

# 63. `some()`

`some()` checks whether **at least one** element satisfies a condition.

```javascript
const numbers = [
    1,
    3,
    5,
    8
];

const hasEvenNumber =
    numbers.some((number) => {
        return number % 2 === 0;
    });

console.log(hasEvenNumber);
```

Output:

```text
true
```

Think:

```text
some()
→ Does ANY element pass?
```

---

# 64. `every()`

`every()` checks whether **all elements** satisfy a condition.

```javascript
const numbers = [
    2,
    4,
    6,
    8
];

const allEven =
    numbers.every((number) => {
        return number % 2 === 0;
    });

console.log(allEven);
```

Output:

```text
true
```

Think:

```text
every()
→ Do ALL elements pass?
```

---

# 65. `some()` vs `every()`

```text
some()
→ at least one must satisfy condition

every()
→ all must satisfy condition
```

Example:

```javascript
const users = [
    { active: true },
    { active: false },
    { active: true }
];
```

```javascript
users.some(
    (user) => user.active
);
```

Result:

```text
true
```

```javascript
users.every(
    (user) => user.active
);
```

Result:

```text
false
```

---

# 66. `includes()`

`includes()` checks whether an array contains a value.

```javascript
const roles = [
    "user",
    "admin",
    "editor"
];

console.log(
    roles.includes("admin")
);
```

Output:

```text
true
```

If not present:

```javascript
roles.includes("owner");
```

Result:

```text
false
```

---

# 67. `indexOf()`

`indexOf()` returns the first index of a value.

```javascript
const fruits = [
    "Apple",
    "Banana",
    "Mango"
];

console.log(
    fruits.indexOf("Banana")
);
```

Output:

```text
1
```

If the value does not exist:

```javascript
fruits.indexOf("Orange");
```

Output:

```text
-1
```

---

# 68. `includes()` vs `indexOf()`

If you only need to know whether a value exists:

```javascript
fruits.includes("Apple");
```

Result:

```text
true
```

If you need its position:

```javascript
fruits.indexOf("Apple");
```

Result:

```text
0
```

Remember:

```text
includes()
→ boolean

indexOf()
→ index or -1
```

---

# 69. `reduce()`

`reduce()` combines array elements into a single accumulated result.

Example:

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const total = numbers.reduce(
    (accumulator, number) => {
        return accumulator + number;
    },
    0
);

console.log(total);
```

Output:

```text
10
```

---

# 70. Understanding `reduce()`

Structure:

```javascript
array.reduce(
    callback,
    initialValue
);
```

Callback:

```javascript
(
    accumulator,
    currentValue
) => {
    return newAccumulator;
}
```

Example:

```javascript
const numbers = [
    1,
    2,
    3,
    4
];
```

Initial accumulator:

```text
0
```

Execution:

```text
Accumulator     Current     New Accumulator

0               1           1

1               2           3

3               3           6

6               4           10
```

Final:

```text
10
```

---

# 71. `reduce()` Can Produce Different Result Types

`reduce()` does not have to return a number.

It can produce:

```text
Number
String
Object
Array
Map
and more
```

Example:

```javascript
const users = [
    {
        name: "John",
        active: true
    },
    {
        name: "Alice",
        active: false
    }
];

const count = users.reduce(
    (total, user) => {
        if (user.active) {
            return total + 1;
        }

        return total;
    },
    0
);

console.log(count);
```

Output:

```text
1
```

---

# 72. `sort()`

`sort()` sorts an array **in place**.

```javascript
const fruits = [
    "Mango",
    "Apple",
    "Banana"
];

fruits.sort();

console.log(fruits);
```

Output:

```javascript
[
    "Apple",
    "Banana",
    "Mango"
]
```

Important:

```text
sort()
→ mutates original array
```

---

# 73. Default `sort()` with Numbers

Be careful:

```javascript
const numbers = [
    100,
    2,
    30,
    4
];

numbers.sort();

console.log(numbers);
```

The result is based on default string-style comparison, not normal numeric ordering.

You may get:

```javascript
[
    100,
    2,
    30,
    4
]
```

Therefore numeric sorting usually requires a compare function.

---

# 74. Numeric Ascending Sort

```javascript
const numbers = [
    100,
    2,
    30,
    4
];

numbers.sort(
    (a, b) => a - b
);

console.log(numbers);
```

Output:

```javascript
[
    2,
    4,
    30,
    100
]
```

Remember:

```javascript
(a, b) => a - b
```

means ascending numeric order.

---

# 75. Numeric Descending Sort

```javascript
const numbers = [
    100,
    2,
    30,
    4
];

numbers.sort(
    (a, b) => b - a
);
```

Output:

```javascript
[
    100,
    30,
    4,
    2
]
```

Remember:

```text
a - b
→ ascending

b - a
→ descending
```

---

# 76. Sorting Objects

```javascript
const users = [
    {
        name: "John",
        age: 30
    },
    {
        name: "Alice",
        age: 20
    },
    {
        name: "Bob",
        age: 25
    }
];

users.sort(
    (a, b) => a.age - b.age
);
```

Result:

```text
Alice → 20
Bob   → 25
John  → 30
```

Again:

```text
sort()
→ mutates original array
```

---

# 77. `toSorted()`

Modern JavaScript provides:

```javascript
toSorted()
```

which returns a sorted copy without modifying the original.

```javascript
const numbers = [
    30,
    10,
    20
];

const sorted = numbers.toSorted(
    (a, b) => a - b
);
```

Now:

```javascript
console.log(numbers);
```

Output:

```javascript
[30, 10, 20]
```

And:

```javascript
console.log(sorted);
```

Output:

```javascript
[10, 20, 30]
```

Difference:

```text
sort()
→ mutates original

toSorted()
→ returns sorted copy
```

---

# 78. `reverse()`

`reverse()` reverses the array **in place**.

```javascript
const numbers = [
    1,
    2,
    3
];

numbers.reverse();

console.log(numbers);
```

Output:

```javascript
[3, 2, 1]
```

Important:

```text
reverse()
→ mutates original array
```

---

# 79. `toReversed()`

Modern JavaScript provides:

```javascript
toReversed()
```

which returns a reversed copy.

```javascript
const numbers = [
    1,
    2,
    3
];

const reversed =
    numbers.toReversed();
```

Original:

```javascript
[1, 2, 3]
```

New:

```javascript
[3, 2, 1]
```

---

# 80. `join()`

`join()` converts array elements into a string.

```javascript
const technologies = [
    "JavaScript",
    "React",
    "Node.js"
];

const result =
    technologies.join(", ");

console.log(result);
```

Output:

```text
JavaScript, React, Node.js
```

---

# 81. `join()` Examples

```javascript
const values = [
    "A",
    "B",
    "C"
];
```

```javascript
values.join("-");
```

Result:

```text
A-B-C
```

```javascript
values.join(" ");
```

Result:

```text
A B C
```

```javascript
values.join("");
```

Result:

```text
ABC
```

---

# 82. `flat()`

`flat()` flattens nested arrays.

```javascript
const numbers = [
    1,
    2,
    [3, 4]
];

const result = numbers.flat();

console.log(result);
```

Output:

```javascript
[1, 2, 3, 4]
```

Default depth:

```text
1
```

---

# 83. `flat()` with Depth

```javascript
const numbers = [
    1,
    [
        2,
        [
            3,
            4
        ]
    ]
];

console.log(
    numbers.flat(2)
);
```

Output:

```javascript
[1, 2, 3, 4]
```

To flatten deeply:

```javascript
numbers.flat(Infinity);
```

Use this only when deep flattening is actually appropriate for the data structure.

---

# 84. `flatMap()`

`flatMap()` combines:

```text
map()
+
flat(1)
```

Example:

```javascript
const words = [
    "Hello World",
    "JavaScript Array"
];

const result = words.flatMap(
    (word) => word.split(" ")
);

console.log(result);
```

Output:

```javascript
[
    "Hello",
    "World",
    "JavaScript",
    "Array"
]
```

Conceptually:

```javascript
words
    .map(...)
    .flat(1);
```

---

# 85. `Array.from()`

`Array.from()` creates an array from an iterable or array-like value.

Example:

```javascript
const text = "Hello";

const characters =
    Array.from(text);

console.log(characters);
```

Output:

```javascript
[
    "H",
    "e",
    "l",
    "l",
    "o"
]
```

---

# 86. Spread with Strings

Spread can also turn a string's iterable characters into array elements.

```javascript
const characters = [
    ..."Hello"
];

console.log(characters);
```

Output:

```javascript
[
    "H",
    "e",
    "l",
    "l",
    "o"
]
```

---

# 87. `Array.of()`

`Array.of()` creates an array from its arguments.

```javascript
const numbers =
    Array.of(1, 2, 3);

console.log(numbers);
```

Output:

```javascript
[1, 2, 3]
```

This is especially useful for understanding the difference between:

```javascript
Array.of(3);
```

Result:

```javascript
[3]
```

and:

```javascript
new Array(3);
```

which creates an array with length `3` containing empty slots.

---

# 88. Mutating vs Non-Mutating Methods

This distinction is extremely important.

## Common Mutating Methods

These modify the original array:

```text
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
fill()
copyWithin()
```

Modern JavaScript also has some non-mutating alternatives to historically mutating operations.

## Common Non-Mutating Methods

These return values/new arrays without directly changing the original array:

```text
slice()
concat()
map()
filter()
flat()
flatMap()
toSorted()
toReversed()
toSpliced()
with()
```

Methods such as:

```text
find()
findIndex()
some()
every()
includes()
indexOf()
reduce()
join()
```

also do not directly mutate the array, though callbacks passed to some methods could themselves mutate external values.

---

# 89. `toSpliced()`

Modern JavaScript provides a non-mutating alternative to `splice()`.

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const result =
    numbers.toSpliced(
        1,
        2
    );
```

Original:

```javascript
[1, 2, 3, 4]
```

Result:

```javascript
[1, 4]
```

Difference:

```text
splice()
→ mutates original

toSpliced()
→ returns modified copy
```

---

# 90. `with()`

`with()` returns a new array with one element replaced.

```javascript
const numbers = [
    10,
    20,
    30
];

const updated =
    numbers.with(1, 100);
```

Original:

```javascript
[10, 20, 30]
```

Updated:

```javascript
[10, 100, 30]
```

This is a modern non-mutating alternative to:

```javascript
numbers[1] = 100;
```

when you specifically want a new array.

---

# 91. Method Chaining

Array methods can be chained.

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

const result = numbers
    .filter(
        (number) =>
            number % 2 === 0
    )
    .map(
        (number) =>
            number * 10
    );
```

Execution:

```text
Original

[1, 2, 3, 4, 5, 6]

        ↓ filter even

[2, 4, 6]

        ↓ map × 10

[20, 40, 60]
```

Result:

```javascript
[20, 40, 60]
```

This style is very common in modern JavaScript.

---

# 92. Chaining with Arrays of Objects

```javascript
const users = [
    {
        name: "John",
        active: true,
        age: 30
    },
    {
        name: "Alice",
        active: false,
        age: 20
    },
    {
        name: "Bob",
        active: true,
        age: 25
    }
];

const names = users
    .filter(
        (user) => user.active
    )
    .map(
        (user) => user.name
    );
```

Result:

```javascript
[
    "John",
    "Bob"
]
```

Think:

```text
users
↓
filter active users
↓
map users to names
↓
["John", "Bob"]
```

---

# 93. Choosing the Correct Array Method

A useful mental model:

```text
Need to execute something for each item?
→ forEach()

Need to transform every item?
→ map()

Need some items based on condition?
→ filter()

Need the first matching item?
→ find()

Need the index of first matching item?
→ findIndex()

Need to know if any item matches?
→ some()

Need to know if all items match?
→ every()

Need to combine everything into one result?
→ reduce()

Need to check whether a value exists?
→ includes()

Need the index of a value?
→ indexOf()

Need to extract part of an array?
→ slice()

Need to mutate by adding/removing/replacing?
→ splice()

Need a non-mutating splice-style update?
→ toSpliced()

Need sorting?
→ sort() / toSorted()

Need reversing?
→ reverse() / toReversed()
```

---

# 94. `map()` vs `forEach()`

A very important distinction.

`forEach()`:

```javascript
const numbers = [
    1,
    2,
    3
];

numbers.forEach(
    (number) => {
        console.log(number);
    }
);
```

Use it primarily for side effects.

`map()`:

```javascript
const doubled =
    numbers.map(
        (number) => number * 2
    );
```

Use it when transforming elements into a new array.

Remember:

```text
forEach()
→ perform operation for each item
→ returns undefined

map()
→ transform each item
→ returns new array
```

---

# 95. `map()` vs `filter()`

```text
map()
→ transforms elements

filter()
→ selects elements
```

Example:

```javascript
const numbers = [
    1,
    2,
    3,
    4
];
```

Map:

```javascript
numbers.map(
    (number) => number * 2
);
```

Result:

```javascript
[2, 4, 6, 8]
```

Filter:

```javascript
numbers.filter(
    (number) => number > 2
);
```

Result:

```javascript
[3, 4]
```

---

# 96. `map()` Usually Preserves Length

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) => number * 10
);
```

Input length:

```text
3
```

Output length:

```text
3
```

Conceptually:

```text
map()
1 input element
→ 1 output element
```

For ordinary dense arrays, the resulting array has the same `length` as the source.

---

# 97. `filter()` Can Change Length

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const result =
    numbers.filter(
        (number) =>
            number > 2
    );
```

Input:

```text
4 elements
```

Output:

```text
2 elements
```

Because elements that fail the condition are excluded.

---

# 98. Common Mistake: Missing `return` in `map()`

Incorrect:

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
```

Result:

```javascript
[
    undefined,
    undefined,
    undefined
]
```

Why?

The callback does not return anything.

Correct:

```javascript
const result = numbers.map(
    (number) => {
        return number * 2;
    }
);
```

Or:

```javascript
const result = numbers.map(
    (number) => number * 2
);
```

---

# 99. Common Mistake: Returning an Object from Arrow Function

This:

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) => {
        value: number;
    }
);
```

does not return an object.

The braces are interpreted as the function body.

Correct:

```javascript
const result = numbers.map(
    (number) => ({
        value: number
    })
);
```

Result:

```javascript
[
    { value: 1 },
    { value: 2 },
    { value: 3 }
]
```

Remember:

```javascript
() => ({
    key: value
})
```

---

# 100. Common Mistake: Using `map()` Only for Side Effects

Avoid:

```javascript
users.map((user) => {
    console.log(user.name);
});
```

if you do not need the resulting array.

Prefer:

```javascript
users.forEach((user) => {
    console.log(user.name);
});
```

Use:

```text
map()
→ when you need transformation

forEach()
→ when you only need an operation/side effect
```

---

# 101. Common Mistake: Mutating Inside `map()`

Consider:

```javascript
const users = [
    {
        name: "John"
    },
    {
        name: "Alice"
    }
];

const updated = users.map(
    (user) => {
        user.active = true;

        return user;
    }
);
```

Although `map()` creates a new outer array, the callback mutated the original user objects.

Therefore:

```javascript
console.log(users);
```

also contains:

```javascript
active: true
```

Prefer immutable-style transformation:

```javascript
const updated = users.map(
    (user) => ({
        ...user,
        active: true
    })
);
```

---

# 102. Common Mistake: `sort()` Mutates

Consider:

```javascript
const numbers = [
    3,
    1,
    2
];

const sorted =
    numbers.sort(
        (a, b) => a - b
    );
```

Now:

```javascript
console.log(numbers);
```

Output:

```javascript
[1, 2, 3]
```

The original array changed.

If mutation is undesirable:

```javascript
const sorted =
    numbers.toSorted(
        (a, b) => a - b
    );
```

Or, for compatibility with older environments:

```javascript
const sorted = [
    ...numbers
].sort(
    (a, b) => a - b
);
```

---

# 103. Common Mistake: Object Equality Inside Arrays

Consider:

```javascript
const users = [
    {
        id: 1
    }
];

console.log(
    users.includes({
        id: 1
    })
);
```

Output:

```text
false
```

Why?

The object passed to `includes()` is a different object.

Instead:

```javascript
const exists = users.some(
    (user) => user.id === 1
);
```

Result:

```text
true
```

---

# 104. Array Interview Example 1

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const copy = numbers;

copy.push(4);

console.log(numbers);
```

Answer:

```javascript
[1, 2, 3, 4]
```

Both variables refer to the same array.

---

# 105. Array Interview Example 2

What is the output?

```javascript
const a = [
    1,
    2
];

const b = [
    1,
    2
];

console.log(a === b);
```

Answer:

```text
false
```

They are different arrays.

---

# 106. Array Interview Example 3

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers.map(
    (number) =>
        number * 2
);

console.log(result);
```

Answer:

```javascript
[2, 4, 6]
```

---

# 107. Array Interview Example 4

What is the output?

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const result =
    numbers.filter(
        (number) =>
            number % 2 === 0
    );

console.log(result);
```

Answer:

```javascript
[2, 4]
```

---

# 108. Array Interview Example 5

What is the output?

```javascript
const numbers = [
    10,
    20,
    30
];

const result =
    numbers.find(
        (number) =>
            number > 10
    );

console.log(result);
```

Answer:

```text
20
```

`find()` returns the first matching element.

---

# 109. Array Interview Example 6

What is the output?

```javascript
const numbers = [
    1,
    2,
    3,
    4
];

const result =
    numbers.reduce(
        (total, number) =>
            total + number,
        0
    );

console.log(result);
```

Answer:

```text
10
```

---

# 110. Array Interview Example 7

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const copy = [
    ...numbers
];

copy.push(4);

console.log(numbers);
console.log(copy);
```

Answer:

```javascript
[1, 2, 3]

[1, 2, 3, 4]
```

The spread created a new outer array.

---

# 111. Array Interview Example 8

What is the output?

```javascript
const users = [
    {
        name: "John"
    }
];

const copy = [
    ...users
];

copy[0].name = "Alice";

console.log(
    users[0].name
);
```

Answer:

```text
Alice
```

Because spread performs a shallow copy.

---

# 112. Array Interview Example 9

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const [
    first,
    ...rest
] = numbers;

console.log(first);
console.log(rest);
```

Answer:

```text
1

[2, 3]
```

---

# 113. Array Interview Example 10

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result =
    numbers.some(
        (number) =>
            number > 2
    );

console.log(result);
```

Answer:

```text
true
```

At least one value satisfies the condition.

---

# 114. Array Interview Example 11

What is the output?

```javascript
const numbers = [
    2,
    4,
    6
];

const result =
    numbers.every(
        (number) =>
            number % 2 === 0
    );

console.log(result);
```

Answer:

```text
true
```

Every element is even.

---

# 115. Array Interview Example 12

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result =
    numbers.forEach(
        (number) =>
            number * 2
    );

console.log(result);
```

Answer:

```text
undefined
```

`forEach()` does not build and return a transformed array.

---

# 116. Array Interview Example 13

What is the output?

```javascript
const numbers = [
    10,
    20,
    30,
    40
];

console.log(
    numbers.slice(1, 3)
);
```

Answer:

```javascript
[20, 30]
```

Index `1` is included.

Index `3` is excluded.

---

# 117. Array Interview Example 14

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

numbers.splice(
    1,
    1,
    10
);

console.log(numbers);
```

Answer:

```javascript
[1, 10, 3]
```

At index `1`:

```text
Remove 1 element
Insert 10
```

---

# 118. Array Interview Example 15

What is the output?

```javascript
const numbers = [
    1,
    2,
    3
];

const result = numbers
    .filter(
        (number) =>
            number > 1
    )
    .map(
        (number) =>
            number * 10
    );

console.log(result);
```

Answer:

```javascript
[20, 30]
```

Execution:

```text
[1, 2, 3]

↓ filter > 1

[2, 3]

↓ map × 10

[20, 30]
```

---

# 119. Arrays and React

Arrays are extremely important in React.

Suppose:

```jsx
const users = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Alice"
    },
    {
        id: 3,
        name: "Bob"
    }
];
```

React commonly uses:

```javascript
map()
```

to render arrays.

---

# 120. Rendering Lists with `map()`

```jsx
function UserList() {
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
        <ul>
            {users.map((user) => (
                <li key={user.id}>
                    {user.name}
                </li>
            ))}
        </ul>
    );
}
```

Flow:

```text
users array
│
▼
map()
│
├── John  → <li>John</li>
│
└── Alice → <li>Alice</li>
│
▼
React renders list
```

---

# 121. React Keys

When rendering lists:

```jsx
users.map((user) => (
    <User
        key={user.id}
        user={user}
    />
));
```

React uses `key` to identify list items across renders.

Prefer stable unique identifiers:

```jsx
key={user.id}
```

Avoid unstable values such as:

```jsx
key={Math.random()}
```

Using the array index as a key can be acceptable for truly static lists, but can cause incorrect behavior when items are inserted, removed, or reordered.

---

# 122. Filtering Before Rendering

```jsx
const activeUsers = users.filter(
    (user) => user.active
);
```

Then:

```jsx
return (
    <div>
        {activeUsers.map((user) => (
            <User
                key={user.id}
                user={user}
            />
        ))}
    </div>
);
```

Or chain them:

```jsx
return (
    <div>
        {users
            .filter(
                (user) =>
                    user.active
            )
            .map(
                (user) => (
                    <User
                        key={user.id}
                        user={user}
                    />
                )
            )}
    </div>
);
```

---

# 123. React Array State

State can contain arrays.

```jsx
const [users, setUsers] =
    useState([
        {
            id: 1,
            name: "John"
        }
    ]);
```

When updating array state, avoid mutating the existing array.

---

# 124. Adding to React Array State

Avoid:

```jsx
users.push(newUser);
setUsers(users);
```

Instead:

```jsx
setUsers((previousUsers) => [
    ...previousUsers,
    newUser
]);
```

Conceptually:

```text
Old Array
│
▼
[John]

Create New Array
│
├── copy John
└── add Alice

▼

[John, Alice]
```

---

# 125. Removing from React Array State

Use:

```javascript
filter()
```

Example:

```jsx
setUsers((previousUsers) =>
    previousUsers.filter(
        (user) =>
            user.id !== userId
    )
);
```

If:

```text
users

John  id 1
Alice id 2
Bob   id 3
```

and:

```text
userId = 2
```

result:

```text
John
Bob
```

No mutation required.

---

# 126. Updating an Item in React Array State

Suppose:

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
```

Update user `2`:

```jsx
setUsers((previousUsers) =>
    previousUsers.map(
        (user) =>
            user.id === 2
                ? {
                    ...user,
                    name: "Bob"
                }
                : user
    )
);
```

Flow:

```text
John id 1
│
├── id === 2?
│       false
│
└── return original John


Alice id 2
│
├── id === 2?
│       true
│
└── return new object
        name → Bob
```

Result:

```javascript
[
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Bob"
    }
]
```

This is one of the most important React state patterns.

---

# 127. Finding Data in React

```javascript
const selectedUser =
    users.find(
        (user) =>
            user.id === selectedId
    );
```

Then:

```jsx
return (
    <div>
        {selectedUser?.name}
    </div>
);
```

This combines:

```text
Array find()
+
Objects
+
Optional chaining
```

---

# 128. Checking Permissions with Arrays

Suppose:

```javascript
const roles = [
    "user",
    "admin"
];
```

Check:

```javascript
const isAdmin =
    roles.includes("admin");
```

Result:

```text
true
```

Or with objects:

```javascript
const permissions = [
    {
        name: "read",
        enabled: true
    },
    {
        name: "write",
        enabled: false
    }
];
```

Check:

```javascript
const canWrite =
    permissions.some(
        (permission) =>
            permission.name === "write" &&
            permission.enabled
    );
```

---

# 129. API Arrays

APIs commonly return arrays of objects.

Example:

```json
[
    {
        "id": 1,
        "name": "John",
        "active": true
    },
    {
        "id": 2,
        "name": "Alice",
        "active": false
    },
    {
        "id": 3,
        "name": "Bob",
        "active": true
    }
]
```

You might process this data:

```javascript
const activeUserNames = users
    .filter(
        (user) =>
            user.active
    )
    .map(
        (user) =>
            user.name
    );
```

Result:

```javascript
[
    "John",
    "Bob"
]
```

This is the kind of JavaScript used constantly in real React applications.

---

# 130. Array Method Cheat Sheet

```text
METHOD          PURPOSE                         MUTATES?

push()          Add to end                      Yes

pop()           Remove from end                 Yes

unshift()       Add to beginning                Yes

shift()         Remove from beginning           Yes

splice()        Add/remove/replace               Yes

slice()         Extract portion                  No

concat()        Combine arrays                   No

map()           Transform elements               No

filter()        Select elements                  No

find()          Find first matching element      No

findIndex()     Find first matching index        No

some()          Check if any match               No

every()         Check if all match               No

includes()      Check if value exists            No

indexOf()       Find value index                 No

reduce()        Reduce to accumulated result     No

sort()          Sort                             Yes

toSorted()      Sorted copy                      No

reverse()       Reverse                          Yes

toReversed()    Reversed copy                    No

toSpliced()     Splice-style copied update       No

with()          Replace item in copied array     No

join()          Convert to string                No

flat()          Flatten nested arrays            No

flatMap()       Map + flatten one level          No
```

---

# 131. Important Rules to Remember

```text
1. Arrays store ordered collections of values.

2. Array indexing starts at 0.

3. length tells you the array length.

4. Last index is usually length - 1.

5. at(-1) can access the last element.

6. Arrays can contain any JavaScript values.

7. Arrays are specialized objects.

8. typeof [] returns "object".

9. Use Array.isArray() to detect arrays.

10. Arrays are reference types.

11. Assigning an array to another variable does not copy it.

12. Spread can create a new outer array.

13. Spread creates a shallow copy.

14. Nested objects/arrays remain shared after a shallow copy.

15. push() adds to the end.

16. pop() removes from the end.

17. unshift() adds to the beginning.

18. shift() removes from the beginning.

19. slice() extracts without mutation.

20. splice() modifies the original array.

21. map() transforms elements into a new array.

22. filter() selects matching elements.

23. find() returns the first matching element.

24. findIndex() returns the first matching index.

25. some() checks whether any element matches.

26. every() checks whether all elements match.

27. reduce() combines values into an accumulated result.

28. includes() checks whether a value exists.

29. indexOf() returns an index or -1.

30. sort() mutates the original array.

31. toSorted() returns a sorted copy.

32. reverse() mutates the original array.

33. toReversed() returns a reversed copy.

34. forEach() returns undefined.

35. map() should be used when you need transformed results.

36. Arrays of objects are extremely common.

37. Array destructuring works by position.

38. React commonly renders arrays using map().

39. React list items need stable keys.

40. React array state should generally be updated without mutation.
```

---

# 132. Objects + Arrays + Callbacks

These three concepts work together constantly.

Example:

```javascript
const users = [
    {
        id: 1,
        name: "John",
        active: true
    },
    {
        id: 2,
        name: "Alice",
        active: false
    },
    {
        id: 3,
        name: "Bob",
        active: true
    }
];

const names = users
    .filter(
        (user) =>
            user.active
    )
    .map(
        (user) =>
            user.name
    );

console.log(names);
```

Result:

```javascript
[
    "John",
    "Bob"
]
```

Concepts:

```text
users
│
├── Array
│
├── contains Objects
│
▼
filter()
│
├── receives Callback
│
├── callback receives Object
│
└── checks active property
│
▼
Active Users Array
│
▼
map()
│
├── receives Callback
│
├── callback receives Object
│
└── returns name property
│
▼
Names Array

["John", "Bob"]
```

This pattern appears everywhere in modern JavaScript and React.

---

# 133. React-Relevant Array Summary

For React, these are the most important array concepts to know well:

## Render

```jsx
users.map((user) => (
    <User
        key={user.id}
        user={user}
    />
));
```

---

## Add

```jsx
setUsers((users) => [
    ...users,
    newUser
]);
```

---

## Remove

```jsx
setUsers((users) =>
    users.filter(
        (user) =>
            user.id !== id
    )
);
```

---

## Update

```jsx
setUsers((users) =>
    users.map(
        (user) =>
            user.id === id
                ? {
                    ...user,
                    name: "Alice"
                }
                : user
    )
);
```

---

## Find

```javascript
const user = users.find(
    (user) =>
        user.id === id
);
```

---

## Check

```javascript
const exists = users.some(
    (user) =>
        user.id === id
);
```

---

## Filter + Render

```jsx
users
    .filter(
        (user) =>
            user.active
    )
    .map(
        (user) => (
            <User
                key={user.id}
                user={user}
            />
        )
    );
```
