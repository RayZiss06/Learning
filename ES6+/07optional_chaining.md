# Optional Chaining (`?.`)

**Optional chaining** allows you to safely access properties, methods, or array elements when an intermediate value might be `null` or `undefined`.

It uses the operator:

```javascript
?.
```

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    user.address?.city
);
```

Output:

```text
undefined
```

Instead of throwing an error because `address` does not exist, JavaScript returns:

```javascript
undefined
```

The main idea:

```text
Value exists?
     │
 ┌───┴───┐
Yes      No
 │        │
 ▼        ▼
Continue  undefined
```

---

# 1. The Problem Optional Chaining Solves

Consider:

```javascript
const user = {
    name: "John"
};

console.log(
    user.address.city
);
```

This throws an error because:

```javascript
user.address
```

is:

```text
undefined
```

JavaScript then tries to access:

```javascript
undefined.city
```

which causes a `TypeError`.

---

# 2. Using Optional Chaining

Instead:

```javascript
const user = {
    name: "John"
};

console.log(
    user.address?.city
);
```

Output:

```text
undefined
```

JavaScript checks:

```text
user.address
```

If it is `null` or `undefined`, evaluation stops and the optional chain returns:

```javascript
undefined
```

---

# 3. Basic Syntax

Normal property access:

```javascript
object.property
```

Optional property access:

```javascript
object?.property
```

Example:

```javascript
const user = {
    name: "John"
};

console.log(
    user?.name
);
```

Output:

```text
John
```

---

# 4. Optional Chaining Checks `null` and `undefined`

Optional chaining short-circuits only when the value immediately before `?.` is:

```javascript
null
```

or:

```javascript
undefined
```

These are called **nullish values**.

Example:

```javascript
const user = null;

console.log(
    user?.name
);
```

Output:

```text
undefined
```

---

# 5. `undefined` Example

```javascript
let user;

console.log(
    user?.name
);
```

Output:

```text
undefined
```

Because:

```text
user → undefined
```

---

# 6. Optional Chaining Does Not Check All Falsy Values

Values such as:

```text
0
false
""
NaN
```

are not nullish.

Optional chaining does not stop simply because a value is falsy.

Example:

```javascript
const data = {
    count: 0
};

console.log(
    data.count?.toString()
);
```

Output:

```text
0
```

`0` is a valid value, so the chain continues.

---

# 7. Nested Object Access

Consider:

```javascript
const user = {
    name: "John",

    address: {
        city: "New York"
    }
};
```

Access:

```javascript
console.log(
    user.address.city
);
```

Output:

```text
New York
```

But if `address` might not exist:

```javascript
console.log(
    user.address?.city
);
```

This safely handles a missing `address`.

---

# 8. Deeply Nested Optional Chaining

Suppose:

```javascript
const user = {
    profile: {
        address: {
            city: "New York"
        }
    }
};
```

You can write:

```javascript
const city =
    user?.profile?.address?.city;
```

Result:

```text
New York
```

If any optional link in the chain is `null` or `undefined`, the expression returns:

```javascript
undefined
```

---

# 9. Every Uncertain Level Should Be Protected

Consider:

```javascript
user?.profile.address.city
```

Only:

```javascript
user
```

is protected by `?.`.

If `user` exists but:

```javascript
user.profile
```

is `undefined`, then accessing:

```javascript
.address
```

can still throw.

If all levels may be missing:

```javascript
user?.profile?.address?.city
```

Use `?.` at every uncertain level.

---

# 10. Optional Chaining Is Not "Protect Everything"

This:

```javascript
user?.profile.address
```

does not mean:

```text
Safely access everything after user
```

It means:

```text
If user is null/undefined
→ return undefined

Otherwise
→ evaluate user.profile.address normally
```

So place `?.` where a value may actually be nullish.

---

# 11. Old Approach with `&&`

Before optional chaining, code often looked like:

```javascript
const city =
    user &&
    user.address &&
    user.address.city;
```

With optional chaining:

```javascript
const city =
    user?.address?.city;
```

This is shorter and, importantly, has different behavior for falsy-but-valid values.

---

# 12. `&&` vs `?.`

Consider:

```javascript
const data = {
    count: 0
};
```

Using `&&`:

```javascript
const result =
    data.count &&
    data.count.toString();
```

Result:

```text
0
```

Because `0` is falsy and stops the `&&` expression.

Using optional chaining:

```javascript
const result =
    data.count?.toString();
```

Result:

```text
"0"
```

because `0` is not `null` or `undefined`.

Optional chaining is based on **nullishness**, not truthiness.

---

# 13. Optional Chaining with Arrays

Optional chaining can safely access array elements.

Syntax:

```javascript
array?.[index]
```

Example:

```javascript
const users = [
    "John",
    "Alice"
];

console.log(
    users?.[0]
);
```

Output:

```text
John
```

---

# 14. Missing Array

Example:

```javascript
const users = null;

console.log(
    users?.[0]
);
```

Output:

```text
undefined
```

Without optional chaining:

```javascript
users[0]
```

would throw because `users` is `null`.

---

# 15. Optional Chaining Does Not Validate Array Bounds

Example:

```javascript
const users = [
    "John"
];

console.log(
    users?.[10]
);
```

Output:

```text
undefined
```

But this is normal array behavior.

Even:

```javascript
users[10]
```

returns:

```text
undefined
```

Optional chaining protects against `users` itself being nullish. It does not perform bounds validation.

---

# 16. Array of Objects

Example:

```javascript
const users = [
    {
        name: "John"
    },
    {
        name: "Alice"
    }
];
```

Safe access:

```javascript
const name =
    users?.[0]?.name;
```

Result:

```text
John
```

If:

```javascript
users
```

is missing, or:

```javascript
users[0]
```

is missing, the expression safely returns:

```text
undefined
```

---

# 17. Dynamic Property Access

Normal bracket notation:

```javascript
object[property]
```

Optional chaining:

```javascript
object?.[property]
```

Example:

```javascript
const key = "name";

const user = {
    name: "John"
};

console.log(
    user?.[key]
);
```

Output:

```text
John
```

---

# 18. Dynamic Property with Missing Object

```javascript
const key = "name";
const user = null;

console.log(
    user?.[key]
);
```

Output:

```text
undefined
```

This is useful when the property name is determined at runtime.

---

# 19. Optional Method Calls

Optional chaining can also safely call methods.

Syntax:

```javascript
object.method?.()
```

Example:

```javascript
const user = {
    name: "John",

    greet() {
        return "Hello";
    }
};

console.log(
    user.greet?.()
);
```

Output:

```text
Hello
```

---

# 20. Missing Optional Method

Example:

```javascript
const user = {
    name: "John"
};

const result =
    user.greet?.();

console.log(result);
```

Output:

```text
undefined
```

Since `greet` is missing, the function call is skipped.

---

# 21. Protecting the Object and Method

Consider:

```javascript
user.greet?.()
```

This protects:

```javascript
user.greet
```

but assumes:

```javascript
user
```

exists.

If `user` itself may be nullish:

```javascript
user?.greet?.()
```

Now both are protected.

---

# 22. Optional Function Calls

Suppose:

```javascript
let callback;
```

Instead of:

```javascript
if (callback) {
    callback();
}
```

you can write:

```javascript
callback?.();
```

If `callback` is:

```javascript
undefined
```

the call is skipped.

---

# 23. Callback Example

```javascript
function processData(
    data,
    callback
) {
    console.log(data);

    callback?.(data);
}
```

Call:

```javascript
processData("Hello");
```

No error occurs.

Call:

```javascript
processData(
    "Hello",
    value => {
        console.log(
            `Processed: ${value}`
        );
    }
);
```

Output:

```text
Hello
Processed: Hello
```

---

# 24. Optional Call Does Not Check Whether Value Is Callable

Important:

```javascript
const user = {
    greet: "Hello"
};

user.greet?.();
```

This still throws a `TypeError`.

Why?

Because `greet` exists and is not nullish, so JavaScript attempts to call it.

But:

```text
"Hello"
```

is not a function.

Optional chaining checks whether the value is `null` or `undefined`.

It does not verify its type.

---

# 25. Optional Chaining with Function Results

Suppose:

```javascript
function getUser() {
    return {
        profile: {
            name: "John"
        }
    };
}
```

You can write:

```javascript
const name =
    getUser()?.profile?.name;
```

Result:

```text
John
```

If `getUser()` returns:

```javascript
null
```

the chain safely returns:

```javascript
undefined
```

---

# 26. API Response Example

Suppose an API response looks like:

```javascript
const response = {
    data: {
        user: {
            profile: {
                name: "John"
            }
        }
    }
};
```

Traditional access:

```javascript
const name =
    response.data.user.profile.name;
```

If any intermediate property is missing, this may throw.

Safer access:

```javascript
const name =
    response?.data?.user?.profile?.name;
```

---

# 27. API Data May Be Incomplete

Suppose:

```javascript
const response = {
    data: {}
};
```

Then:

```javascript
const name =
    response?.data?.user?.profile?.name;
```

returns:

```text
undefined
```

instead of throwing.

This is one of the most common uses of optional chaining.

---

# 28. Optional Chaining with Default Values

Optional chaining commonly works with:

```javascript
??
```

the nullish coalescing operator.

Example:

```javascript
const user = {};

const city =
    user?.address?.city
    ?? "Unknown";
```

Result:

```text
Unknown
```

Optional chaining:

```javascript
user?.address?.city
```

returns:

```javascript
undefined
```

Then:

```javascript
?? "Unknown"
```

provides the fallback.

---

# 29. `?.` and `??` Work Well Together

Example:

```javascript
const username =
    response
        ?.data
        ?.user
        ?.name
    ?? "Guest";
```

Think:

```text
?. 
↓
Safely access data

??
↓
Provide fallback if result is
null or undefined
```

We will cover `??` in detail in the next topic.

---

# 30. Optional Chaining with `||`

You can also write:

```javascript
const name =
    user?.name || "Guest";
```

But remember:

```javascript
||
```

uses the fallback for all falsy values.

So if:

```javascript
const user = {
    name: ""
};
```

then:

```javascript
user?.name || "Guest"
```

returns:

```text
Guest
```

while:

```javascript
user?.name ?? "Guest"
```

returns:

```text
""
```

because an empty string is not nullish.

---

# 31. React Example — Data Not Loaded Yet

Suppose:

```jsx
function UserProfile({
    user
}) {
    return (
        <h1>
            {user.profile.name}
        </h1>
    );
}
```

If `user` has not loaded yet and is:

```javascript
undefined
```

this can throw.

Use:

```jsx
function UserProfile({
    user
}) {
    return (
        <h1>
            {user?.profile?.name}
        </h1>
    );
}
```

Now rendering can continue safely while `user` is nullish.

---

# 32. React with Fallback

Example:

```jsx
function UserProfile({
    user
}) {
    return (
        <h1>
            {
                user
                    ?.profile
                    ?.name
                ?? "Guest"
            }
        </h1>
    );
}
```

If the name is missing:

```text
Guest
```

is rendered.

---

# 33. React Arrays

Suppose:

```javascript
const users = undefined;
```

Instead of:

```jsx
<p>
    {users[0].name}
</p>
```

you can write:

```jsx
<p>
    {users?.[0]?.name}
</p>
```

This safely handles both a missing array and a missing first element.

---

# 34. Optional Callback Props in React

Suppose a component accepts an optional callback:

```jsx
function Button({
    onClick
}) {
    function handleClick() {
        onClick?.();
    }

    return (
        <button
            onClick={handleClick}
        >
            Click
        </button>
    );
}
```

If `onClick` was not provided, the optional call simply returns `undefined`.

---

# 35. Short-Circuiting

Optional chaining uses short-circuit evaluation.

Example:

```javascript
const user = null;

const result =
    user?.profile?.name;
```

Once JavaScript sees:

```text
user → null
```

the chain stops.

It does not continue trying to access:

```text
profile
name
```

The result is:

```text
undefined
```

---

# 36. Side Effects Can Be Skipped

Consider:

```javascript
let index = 0;

const users = null;

const value =
    users?.[index++];
```

After this:

```javascript
console.log(index);
```

Output:

```text
0
```

Why?

Because `users` is nullish, so the optional chain short-circuits before:

```javascript
index++
```

is evaluated.

---

# 37. Grouping Can Break the Chain

Consider:

```javascript
const user = null;
```

This is safe:

```javascript
user?.profile?.name
```

But:

```javascript
(user?.profile).name
```

can throw.

Why?

First:

```javascript
user?.profile
```

returns:

```javascript
undefined
```

Then JavaScript evaluates:

```javascript
undefined.name
```

outside the optional chain.

So grouping can end the chain.

---

# 38. Continuous Chain

Safe:

```javascript
user
    ?.profile
    ?.address
    ?.city;
```

The optional chain remains continuous.

Be careful when splitting or grouping expressions in ways that resume normal property access.

---

# 39. Optional Chaining Does Not Hide Undeclared Variables

This is important.

```javascript
user?.name
```

is safe if `user` exists as a declared variable whose value may be nullish.

But:

```javascript
someVariableThatWasNeverDeclared?.name
```

still throws:

```text
ReferenceError
```

Optional chaining does not make undeclared identifiers valid.

---

# 40. Declared but Undefined Is Fine

This is valid:

```javascript
let user;

console.log(
    user?.name
);
```

Output:

```text
undefined
```

Because `user` exists as a variable.

Its value is simply:

```javascript
undefined
```

---

# 41. Optional Chaining Cannot Be Used for Assignment

This is invalid:

```javascript
user?.name = "John";
```

Optional chaining cannot be used as the assignment target.

You cannot say:

```text
If user exists, assign name
```

using this syntax.

Instead:

```javascript
if (user) {
    user.name = "John";
}
```

---

# 42. Reading vs Writing

Valid:

```javascript
const name =
    user?.name;
```

Invalid:

```javascript
user?.name = "John";
```

Think:

```text
Optional chaining
↓
Safe access / call

Not
↓
Conditional assignment target
```

---

# 43. Optional Chaining with `delete`

Optional chaining can be used with `delete`.

Example:

```javascript
const user = {
    name: "John"
};

delete user?.name;
```

Now:

```javascript
console.log(user);
```

Output:

```javascript
{}
```

If `user` were nullish, the delete operation would not throw because of the optional chain.

---

# 44. Optional Chaining with Private Class Fields

Private fields have special syntax and access rules.

For normal learning and React usage, focus primarily on:

```javascript
object?.property
object?.[property]
function?.()
```

These cover the vast majority of practical optional chaining usage.

---

# 45. Optional Chaining Does Not Replace Validation

Suppose:

```javascript
const email =
    user?.profile?.email;
```

If `email` is required for your application, simply receiving:

```javascript
undefined
```

may not be enough.

You may still need:

```javascript
if (!email) {
    throw new Error(
        "Email is required"
    );
}
```

Optional chaining prevents certain access errors.

It does not validate business requirements.

---

# 46. Optional Chaining Does Not Catch Errors

Consider:

```javascript
const user = {
    getName() {
        throw new Error(
            "Something failed"
        );
    }
};
```

Calling:

```javascript
user.getName?.();
```

still throws.

Optional chaining only handles a nullish function reference.

It does not catch errors thrown inside the function.

For error handling, use mechanisms such as:

```javascript
try {
    // code
} catch (error) {
    // handle error
}
```

---

# 47. Optional Chaining Does Not Check Types

Consider:

```javascript
const user = {
    profile: 10
};
```

This:

```javascript
user?.profile
```

returns:

```text
10
```

Optional chaining does not ask:

```text
Is profile an object?
```

It only checks whether the value before `?.` is:

```text
null
or
undefined
```

---

# 48. Method Call Difference

These two expressions protect different things:

```javascript
user?.getName()
```

and:

```javascript
user.getName?.()
```

First:

```javascript
user?.getName()
```

protects against:

```text
user being null/undefined
```

Second:

```javascript
user.getName?.()
```

protects against:

```text
getName being null/undefined
```

To protect both:

```javascript
user?.getName?.()
```

---

# 49. Property Access Difference

Compare:

```javascript
user?.profile.name
```

with:

```javascript
user.profile?.name
```

First protects:

```text
user
```

Second protects:

```text
user.profile
```

If both may be nullish:

```javascript
user?.profile?.name
```

---

# 50. Optional Chaining in `map()`

Suppose:

```javascript
const users = [
    {
        profile: {
            name: "John"
        }
    },
    {},
    {
        profile: {
            name: "Alice"
        }
    }
];
```

You can write:

```javascript
const names =
    users.map(
        user =>
            user?.profile?.name
    );
```

Result:

```javascript
[
    "John",
    undefined,
    "Alice"
]
```

---

# 51. Optional Chaining in `filter()`

Example:

```javascript
const users = [
    {
        profile: {
            active: true
        }
    },
    {},
    {
        profile: {
            active: false
        }
    }
];
```

Use:

```javascript
const activeUsers =
    users.filter(
        user =>
            user?.profile?.active
    );
```

This keeps users whose nested `active` value is truthy.

Note that this filtering behavior comes from `filter()`'s truthiness test, not from optional chaining itself.

---

# 52. Nested API Example

Suppose:

```javascript
const response = {
    data: {
        users: [
            {
                profile: {
                    contact: {
                        email:
                            "john@example.com"
                    }
                }
            }
        ]
    }
};
```

Safe access:

```javascript
const email =
    response
        ?.data
        ?.users
        ?.[0]
        ?.profile
        ?.contact
        ?.email;
```

Result:

```text
john@example.com
```

---

# 53. Array Access Syntax

Notice:

```javascript
users?.[0]
```

Not:

```javascript
users?[0]
```

The operator is still:

```javascript
?.
```

Therefore:

```javascript
users?.[0]
```

---

# 54. Dynamic Key Syntax

Correct:

```javascript
user?.[key]
```

Not:

```javascript
user?[key]
```

Again, the operator includes the dot:

```text
?.
```

---

# 55. Optional Method Call Syntax

Correct:

```javascript
callback?.()
```

Not:

```javascript
callback?()
```

The operator remains:

```javascript
?.
```

---

# 56. Three Main Forms

Optional chaining has three forms you should remember.

## Property

```javascript
object?.property
```

## Bracket Property / Array Index

```javascript
object?.[property]
```

## Function / Method Call

```javascript
function?.()
```

These three forms cover most practical usage.

---

# 57. Common Mistake — Thinking `?.` Checks Truthiness

Example:

```javascript
const data = {
    count: 0
};

console.log(
    data.count?.toString()
);
```

Output:

```text
0
```

The chain continues because `0` is not nullish.

Optional chaining checks:

```text
null
undefined
```

not all falsy values.

---

# 58. Common Mistake — Protecting Only the First Level

Consider:

```javascript
user?.profile.address.city
```

If:

```javascript
user
```

exists but:

```javascript
profile
```

is `undefined`, an error can still occur.

If each level is uncertain:

```javascript
user?.profile?.address?.city
```

---

# 59. Common Mistake — Using `?.` Everywhere

You do not need:

```javascript
user
    ?.profile
    ?.name
```

if your application guarantees that `user` and `profile` always exist.

Optional chaining should represent actual uncertainty.

Overusing it can hide unexpected missing data.

---

# 60. Common Mistake — Using It Instead of Validation

Suppose:

```javascript
const paymentId =
    payment?.id;
```

If your application requires every payment to have an ID, silently getting:

```javascript
undefined
```

may hide a data problem.

Optional chaining is not a replacement for required-data validation.

---

# 61. Common Mistake — Assuming Optional Call Validates Function Type

This can fail:

```javascript
const object = {
    callback: "Hello"
};

object.callback?.();
```

Because:

```javascript
callback
```

exists, but it is not callable.

Optional chaining checks nullishness, not whether a value has the expected type.

---

# 62. Common Mistake — Using It with Undeclared Variables

Invalid assumption:

```javascript
notDeclared?.name
```

This still causes:

```text
ReferenceError
```

The root identifier must exist.

---

# 63. Common Mistake — Assignment

Invalid:

```javascript
user?.name = "John";
```

Optional chaining cannot appear on the left side of a normal assignment.

---

# 64. Common Mistake — Grouping

Potentially unsafe:

```javascript
(user?.profile).name
```

Safer if `profile` may be missing:

```javascript
user?.profile?.name
```

Grouping can end the optional chain.

---

# 65. Common Mistake — Confusing `?.` and `??`

Optional chaining:

```javascript
user?.name
```

means:

```text
Safely access name
```

Nullish coalescing:

```javascript
name ?? "Guest"
```

means:

```text
Use fallback if name is
null or undefined
```

Together:

```javascript
user?.name ?? "Guest"
```

means:

```text
Safely access name
+
Provide fallback if missing
```

---

# 66. Interview Question 1

What is the output?

```javascript
const user = null;

console.log(
    user?.name
);
```

Answer:

```text
undefined
```

---

# 67. Interview Question 2

What is the output?

```javascript
const user = {
    name: "John"
};

console.log(
    user?.name
);
```

Answer:

```text
John
```

---

# 68. Interview Question 3

What is the output?

```javascript
const user = {};

console.log(
    user.address?.city
);
```

Answer:

```text
undefined
```

Because `user.address` is `undefined`, and the optional chain stops there.

---

# 69. Interview Question 4

What happens?

```javascript
const user = {};

console.log(
    user.address.city
);
```

Answer:

```text
TypeError
```

because JavaScript tries to access `city` from `undefined`.

---

# 70. Interview Question 5

What is the output?

```javascript
const data = {
    count: 0
};

console.log(
    data.count?.toString()
);
```

Answer:

```text
0
```

More precisely, the result of `toString()` is the string:

```javascript
"0"
```

Optional chaining does not stop on `0`.

---

# 71. Interview Question 6

What is the output?

```javascript
const users = null;

console.log(
    users?.[0]
);
```

Answer:

```text
undefined
```

---

# 72. Interview Question 7

What is the output?

```javascript
const user = {
    greet() {
        return "Hello";
    }
};

console.log(
    user.greet?.()
);
```

Answer:

```text
Hello
```

---

# 73. Interview Question 8

What is the output?

```javascript
const user = {};

console.log(
    user.greet?.()
);
```

Answer:

```text
undefined
```

---

# 74. Interview Question 9

What happens?

```javascript
const user = {
    greet: "Hello"
};

user.greet?.();
```

Answer:

```text
TypeError
```

`greet` exists, but it is not callable.

---

# 75. Interview Question 10

What is the output?

```javascript
const user = null;

console.log(
    user?.profile?.name
        ?? "Guest"
);
```

Answer:

```text
Guest
```

Optional chaining produces:

```javascript
undefined
```

Then `??` uses the fallback.

---

# 76. Interview Question 11

What happens?

```javascript
let user;

console.log(
    user?.name
);
```

Answer:

```text
undefined
```

`user` is declared but contains `undefined`.

---

# 77. Interview Question 12

What happens?

```javascript
console.log(
    completelyUnknown?.name
);
```

Assuming `completelyUnknown` was never declared:

```text
ReferenceError
```

Optional chaining does not protect undeclared root identifiers.

---

# 78. Interview Question 13

Is this valid?

```javascript
user?.name = "John";
```

Answer:

```text
No
```

Optional chaining cannot be used as an assignment target.

---

# 79. Interview Question 14

What is the difference?

```javascript
user?.getName()
```

vs:

```javascript
user.getName?.()
```

Answer:

```text
user?.getName()

protects user.


user.getName?.()

protects getName.
```

To protect both:

```javascript
user?.getName?.()
```

---

# 80. Interview Question 15

What is the output?

```javascript
const user = {
    profile: null
};

console.log(
    user?.profile?.name
);
```

Answer:

```text
undefined
```

The chain stops when `profile` is `null`.

---

# 81. Interview Question 16

What happens?

```javascript
const user = null;

console.log(
    (user?.profile).name
);
```

Answer:

```text
TypeError
```

`user?.profile` returns `undefined`, then `.name` is accessed outside the optional chain.

---

# 82. Interview Question 17

What is the output?

```javascript
const data = {
    enabled: false
};

console.log(
    data.enabled?.valueOf()
);
```

Answer:

```text
false
```

`false` is not nullish, so the method is called.

---

# 83. Interview Question 18

What is the output?

```javascript
const user = {
    name: ""
};

console.log(
    user?.name ?? "Guest"
);
```

Answer:

```text
""
```

An empty string is not nullish, so `??` does not use the fallback.

---

# 84. Optional Chaining Mental Model

Consider:

```javascript
const city =
    user
        ?.profile
        ?.address
        ?.city;
```

Think:

```text
user
 │
 ▼
null/undefined?
 │
 ├── YES ──→ undefined
 │
 NO
 │
 ▼
profile
 │
 ▼
null/undefined?
 │
 ├── YES ──→ undefined
 │
 NO
 │
 ▼
address
 │
 ▼
null/undefined?
 │
 ├── YES ──→ undefined
 │
 NO
 │
 ▼
city
```

---

# 85. Optional Chaining vs Normal Access

Normal:

```javascript
user.profile.name
```

Mental model:

```text
Access everything.

If something is missing
↓
Possible TypeError
```

Optional:

```javascript
user?.profile?.name
```

Mental model:

```text
Access value

If current optional link is
null / undefined
↓
Stop

Return
undefined
```

---

# 86. Optional Chaining vs `&&`

Old style:

```javascript
const city =
    user &&
    user.address &&
    user.address.city;
```

Modern style:

```javascript
const city =
    user?.address?.city;
```

But they are not perfectly equivalent.

`&&` short-circuits on:

```text
false
0
""
null
undefined
NaN
```

Optional chaining short-circuits on:

```text
null
undefined
```

---

# 87. Optional Chaining Cheat Sheet

## Property

```javascript
user?.name
```

## Nested Property

```javascript
user?.profile?.name
```

## Array Element

```javascript
users?.[0]
```

## Array of Objects

```javascript
users?.[0]?.name
```

## Dynamic Property

```javascript
user?.[key]
```

## Optional Method

```javascript
user.greet?.()
```

## Optional Object + Method

```javascript
user?.greet?.()
```

## Optional Callback

```javascript
callback?.()
```

## Function Result

```javascript
getUser()?.profile?.name
```

## With Fallback

```javascript
user?.name ?? "Guest"
```

## API Response

```javascript
response
    ?.data
    ?.user
    ?.profile
    ?.name;
```

---

# 88. Important Rules to Remember

```text
1. Optional chaining uses the ?. operator.

2. It safely accesses values when something may be null or undefined.

3. If the value before ?. is null, the chain returns undefined.

4. If the value before ?. is undefined, the chain returns undefined.

5. Optional chaining checks nullish values, not all falsy values.

6. 0 does not stop optional chaining.

7. false does not stop optional chaining.

8. "" does not stop optional chaining.

9. NaN does not stop optional chaining.

10. Nested properties can use multiple ?. operators.

11. Every uncertain level should be protected separately.

12. ?. does not automatically protect everything after it.

13. Arrays can use array?.[index].

14. Dynamic properties can use object?.[key].

15. Functions can use function?.().

16. Methods can use object.method?.().

17. The object and method can both be protected with object?.method?.().

18. Optional calls do not verify that an existing value is actually callable.

19. Optional chaining short-circuits evaluation.

20. Expressions later in a short-circuited chain may not be evaluated.

21. Grouping can end an optional chain.

22. Optional chaining cannot protect an undeclared root identifier.

23. A declared variable containing undefined can safely use ?..

24. Optional chaining cannot normally be used as an assignment target.

25. Optional chaining can be used with delete.

26. Optional chaining does not perform data validation.

27. Optional chaining does not catch exceptions thrown by methods.

28. Optional chaining does not check whether values have expected types.

29. Optional chaining is useful with API responses.

30. Optional chaining is useful with nested objects.

31. Optional chaining is useful with optional callbacks.

32. Optional chaining is useful with arrays.

33. Optional chaining is common in React when data may not exist yet.

34. ?. and ?? are frequently used together.

35. ?. safely accesses data.

36. ?? provides a fallback for null or undefined.

37. ?. is not the same as &&.

38. && checks truthiness.

39. ?. checks nullishness.

40. Use optional chaining where missing data is genuinely expected.
```

---

# 89. Most Important Concept

Remember:

```text
?. IS A NULLISH CHECK
```

It cares about:

```text
null
undefined
```

It does not care about:

```text
0
false
""
NaN
```

Example:

```javascript
const user = null;

const name =
    user?.profile?.name;
```

Flow:

```text
user
 ↓
null
 ↓
?. detects null
 ↓
STOP
 ↓
undefined
```

But:

```javascript
const data = {
    count: 0
};

const value =
    data.count?.toString();
```

Flow:

```text
count
 ↓
0
 ↓
Is 0 null or undefined?
 ↓
NO
 ↓
Continue
 ↓
toString()
 ↓
"0"
```

The core distinction:

```text
Falsy
================

false
0
""
NaN
null
undefined


Nullish
================

null
undefined
```

Optional chaining only cares about the second group.

---

# 90. Optional Chaining in One Sentence

> Optional chaining (`?.`) allows JavaScript to safely access properties, array elements, and functions when a value may be `null` or `undefined`, returning `undefined` instead of throwing from that nullish access.
