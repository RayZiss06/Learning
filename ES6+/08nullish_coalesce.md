# Nullish Coalescing (`??`)

**Nullish Coalescing** allows us to provide a fallback value when a value is specifically:

```javascript
null
```

or:

```javascript
undefined
```

It uses the operator:

```javascript
??
```

Basic example:

```javascript
const username = null;

const name =
    username ?? "Guest";

console.log(name);
```

Output:

```text
Guest
```

The main idea:

```text
value ?? fallback
```

means:

```text
If value is null or undefined
        ↓
Use fallback

Otherwise
        ↓
Keep value
```

---

# 1. Basic Syntax

```javascript
value ?? fallbackValue
```

Example:

```javascript
const name =
    undefined ?? "Guest";

console.log(name);
```

Output:

```text
Guest
```

Because:

```javascript
undefined
```

is nullish.

---

# 2. What Does "Nullish" Mean?

JavaScript has many falsy values:

```text
false
0
-0
0n
""
NaN
null
undefined
```

But only two values are **nullish**:

```text
null
undefined
```

Therefore:

```javascript
value ?? fallback
```

uses `fallback` only when `value` is:

```text
null
undefined
```

---

# 3. `null` Uses the Fallback

```javascript
const value = null;

const result =
    value ?? "Default";

console.log(result);
```

Output:

```text
Default
```

---

# 4. `undefined` Uses the Fallback

```javascript
const value = undefined;

const result =
    value ?? "Default";

console.log(result);
```

Output:

```text
Default
```

---

# 5. `0` Does NOT Use the Fallback

```javascript
const value = 0;

const result =
    value ?? 100;

console.log(result);
```

Output:

```text
0
```

Although `0` is falsy, it is not nullish.

---

# 6. `false` Does NOT Use the Fallback

```javascript
const value = false;

const result =
    value ?? true;

console.log(result);
```

Output:

```text
false
```

`false` is a valid value.

---

# 7. Empty String Does NOT Use the Fallback

```javascript
const value = "";

const result =
    value ?? "Guest";

console.log(result);
```

Output:

```text
""
```

The empty string is falsy but not nullish.

---

# 8. `NaN` Does NOT Use the Fallback

```javascript
const value = NaN;

const result =
    value ?? 100;

console.log(result);
```

Result:

```text
NaN
```

`NaN` is falsy, but not nullish.

---

# 9. The Most Important Rule

Remember:

```text
?? checks NULLISHNESS
```

Not:

```text
TRUTHINESS
```

Therefore:

```text
Value       value ?? "Default"

null        → "Default"
undefined   → "Default"

0           → 0
false       → false
""          → ""
NaN         → NaN
```

---

# 10. Logical OR (`||`) Recap

The logical OR operator:

```javascript
||
```

returns the right side when the left side is falsy.

Example:

```javascript
const value = 0;

const result =
    value || 100;

console.log(result);
```

Output:

```text
100
```

Because:

```javascript
0
```

is falsy.

---

# 11. `??` vs `||`

Compare:

```javascript
const value = 0;

console.log(
    value || 100
);
```

Output:

```text
100
```

But:

```javascript
console.log(
    value ?? 100
);
```

Output:

```text
0
```

Why?

Because:

```text
||

Checks whether value is falsy.
```

While:

```text
??

Checks whether value is nullish.
```

---

# 12. Comparison Table

| Value | `value || "Default"` | `value ?? "Default"` |
|---|---|---|
| `undefined` | `"Default"` | `"Default"` |
| `null` | `"Default"` | `"Default"` |
| `false` | `"Default"` | `false` |
| `0` | `"Default"` | `0` |
| `""` | `"Default"` | `""` |
| `NaN` | `"Default"` | `NaN` |
| `"John"` | `"John"` | `"John"` |
| `25` | `25` | `25` |

This table represents the core difference between:

```javascript
||
```

and:

```javascript
??
```

---

# 13. Why `??` Was Introduced

Suppose a volume setting can legitimately be:

```javascript
0
```

Using:

```javascript
const volume =
    userVolume || 50;
```

If:

```javascript
const userVolume = 0;
```

then:

```text
volume → 50
```

This is incorrect if `0` means muted.

With nullish coalescing:

```javascript
const volume =
    userVolume ?? 50;
```

Result:

```text
volume → 0
```

The fallback is only used when the value is actually missing:

```text
null
undefined
```

---

# 14. Another Practical Example — Item Count

Suppose:

```javascript
const itemCount = 0;
```

Using:

```javascript
const count =
    itemCount || 10;
```

Result:

```text
10
```

But `0` may be a valid number of items.

Using:

```javascript
const count =
    itemCount ?? 10;
```

Result:

```text
0
```

---

# 15. Boolean Example

Suppose:

```javascript
const notificationsEnabled =
    false;
```

Using:

```javascript
const enabled =
    notificationsEnabled || true;
```

Result:

```text
true
```

This changes an intentional `false`.

Using:

```javascript
const enabled =
    notificationsEnabled ?? true;
```

Result:

```text
false
```

The user's actual value is preserved.

---

# 16. Empty String Example

Suppose:

```javascript
const nickname = "";
```

Using:

```javascript
const displayName =
    nickname || "Guest";
```

Result:

```text
Guest
```

Using:

```javascript
const displayName =
    nickname ?? "Guest";
```

Result:

```text
""
```

Whether an empty string should be preserved depends on your application's requirements.

---

# 17. When Should You Use `||`?

Use:

```javascript
||
```

when you want a fallback for **any falsy value**.

Example:

```javascript
const displayName =
    inputName || "Guest";
```

If these should all mean "no usable name":

```text
undefined
null
""
```

then `||` may be appropriate.

---

# 18. When Should You Use `??`?

Use:

```javascript
??
```

when:

```text
null
undefined
```

mean:

```text
Value missing
```

but values such as:

```text
0
false
""
```

are valid and should be preserved.

This is common for:

```text
Configuration
API responses
User preferences
Form values
Counters
Boolean settings
React props/state
```

---

# 19. Optional Chaining + Nullish Coalescing

These operators are frequently used together.

Example:

```javascript
const user = {};

const city =
    user?.address?.city
    ?? "Unknown";

console.log(city);
```

Output:

```text
Unknown
```

Flow:

```text
user?.address?.city
        ↓
    undefined
        ↓
       ??
        ↓
   "Unknown"
```

---

# 20. What Each Operator Does

Consider:

```javascript
const name =
    user?.profile?.name
    ?? "Guest";
```

Optional chaining:

```javascript
?.
```

means:

```text
Safely access the value.
```

Nullish coalescing:

```javascript
??
```

means:

```text
If the resulting value is
null or undefined,
use the fallback.
```

Therefore:

```text
?.
↓
SAFE ACCESS

??
↓
FALLBACK VALUE
```

---

# 21. API Response Example

Suppose:

```javascript
const response = {
    data: {
        user: {
            name: null
        }
    }
};
```

Use:

```javascript
const name =
    response
        ?.data
        ?.user
        ?.name
    ?? "Guest";
```

Result:

```text
Guest
```

Because the final value is:

```javascript
null
```

---

# 22. Missing API Property

Suppose:

```javascript
const response = {
    data: {}
};
```

Use:

```javascript
const name =
    response
        ?.data
        ?.user
        ?.name
    ?? "Guest";
```

Result:

```text
Guest
```

Optional chaining returns:

```javascript
undefined
```

Then `??` provides:

```text
Guest
```

---

# 23. API Value of `0`

Suppose:

```javascript
const response = {
    data: {
        stock: 0
    }
};
```

Using:

```javascript
const stock =
    response?.data?.stock
    ?? 100;
```

Result:

```text
0
```

This is often exactly what we want.

The API returned a real value:

```text
0
```

so it should not be replaced.

---

# 24. API Boolean Value

Suppose:

```javascript
const response = {
    data: {
        active: false
    }
};
```

Use:

```javascript
const active =
    response?.data?.active
    ?? true;
```

Result:

```text
false
```

The explicit `false` is preserved.

---

# 25. Multiple `??` Operators

You can chain nullish coalescing:

```javascript
const result =
    value1 ??
    value2 ??
    value3 ??
    "Default";
```

JavaScript uses the first value that is not:

```text
null
undefined
```

---

# 26. Multiple Fallback Example

```javascript
const username = null;
const email = undefined;
const id = 1001;

const display =
    username ??
    email ??
    id ??
    "Unknown";

console.log(display);
```

Output:

```text
1001
```

Flow:

```text
username
↓
null
↓
skip

email
↓
undefined
↓
skip

id
↓
1001
↓
use it
```

---

# 27. First Non-Nullish Value Wins

Example:

```javascript
const a = null;
const b = 0;
const c = 50;

const result =
    a ?? b ?? c;

console.log(result);
```

Output:

```text
0
```

Because `0` is the first non-nullish value.

---

# 28. Short-Circuit Evaluation

Nullish coalescing short-circuits.

Example:

```javascript
function getDefault() {
    console.log(
        "Default calculated"
    );

    return 100;
}

const value = 50;

const result =
    value ?? getDefault();
```

Output from `getDefault()`:

```text
Nothing
```

Because:

```javascript
value
```

is already:

```text
50
```

The right side is not evaluated.

---

# 29. Right Side Runs Only When Needed

Example:

```javascript
function getDefault() {
    console.log(
        "Default calculated"
    );

    return 100;
}

const value = null;

const result =
    value ?? getDefault();
```

Output:

```text
Default calculated
```

And:

```text
result → 100
```

---

# 30. Expressions on the Right Side

The fallback can be any expression.

Example:

```javascript
const value = null;

const result =
    value ?? (10 * 5);
```

Result:

```text
50
```

---

# 31. Function Calls as Fallbacks

Example:

```javascript
function createDefaultUser() {
    return {
        name: "Guest"
    };
}

const user = null;

const result =
    user ?? createDefaultUser();
```

Result:

```javascript
{
    name: "Guest"
}
```

The function is only called when `user` is nullish.

---

# 32. Default Parameters vs `??`

Consider:

```javascript
function greet(
    name = "Guest"
) {
    console.log(name);
}
```

The default parameter is used for:

```javascript
undefined
```

but not:

```javascript
null
```

Therefore:

```javascript
greet(undefined);
```

Output:

```text
Guest
```

But:

```javascript
greet(null);
```

Output:

```text
null
```

---

# 33. `??` Handles Both `null` and `undefined`

Inside the function:

```javascript
function greet(name) {
    const finalName =
        name ?? "Guest";

    console.log(finalName);
}
```

Now:

```javascript
greet(undefined);
```

Output:

```text
Guest
```

And:

```javascript
greet(null);
```

Output:

```text
Guest
```

---

# 34. Default Parameter vs `??` Table

| Value       | Parameter `value = 10` | `value ?? 10` |
| ----------- | ---------------------: | ------------: |
| `undefined` |                   `10` |          `10` |
| `null`      |                 `null` |          `10` |
| `0`         |                    `0` |           `0` |
| `false`     |                `false` |       `false` |
| `""`        |                   `""` |          `""` |

Important difference:

```text
Default Parameter
↓
Fallback for undefined

??

Fallback for null OR undefined
```

---

# 35. Nullish Coalescing Assignment (`??=`)

JavaScript also provides:

```javascript
??=
```

This is called the:

```text
Nullish Coalescing Assignment Operator
```

Example:

```javascript
let username = null;

username ??= "Guest";

console.log(username);
```

Output:

```text
Guest
```

---

# 36. How `??=` Works

This:

```javascript
value ??= defaultValue;
```

is conceptually similar to:

```javascript
value =
    value ?? defaultValue;
```

But `??=` is an assignment operator with short-circuiting behavior and evaluates the left-hand reference only once.

The main idea is:

```text
If value is null/undefined
↓
Assign default

Otherwise
↓
Keep existing value
```

---

# 37. `??=` with `undefined`

```javascript
let value;

value ??= 100;

console.log(value);
```

Output:

```text
100
```

---

# 38. `??=` with `null`

```javascript
let value = null;

value ??= 100;

console.log(value);
```

Output:

```text
100
```

---

# 39. `??=` with `0`

```javascript
let value = 0;

value ??= 100;

console.log(value);
```

Output:

```text
0
```

No assignment occurs.

---

# 40. `??=` with `false`

```javascript
let value = false;

value ??= true;

console.log(value);
```

Output:

```text
false
```

Again, `false` is not nullish.

---

# 41. `??=` with Empty String

```javascript
let value = "";

value ??= "Default";

console.log(value);
```

Output:

```text
""
```

The empty string remains unchanged.

---

# 42. `||=` vs `??=`

Logical OR assignment:

```javascript
value ||= fallback;
```

assigns when `value` is falsy.

Nullish assignment:

```javascript
value ??= fallback;
```

assigns when `value` is nullish.

Example:

```javascript
let a = 0;
let b = 0;

a ||= 100;
b ??= 100;

console.log(a);
console.log(b);
```

Output:

```text
100
0
```

---

# 43. `&&=` Brief Comparison

Modern JavaScript also has:

```javascript
&&=
```

Example:

```javascript
value &&= newValue;
```

It assigns when the existing value is truthy.

So:

```text
||=
↓
Assign when falsy

&&=
↓
Assign when truthy

??=
↓
Assign when nullish
```

---

# 44. Configuration Example

Suppose:

```javascript
const config = {
    retries: 0,
    logging: false
};
```

Use:

```javascript
const retries =
    config.retries ?? 3;

const logging =
    config.logging ?? true;
```

Results:

```text
retries → 0
logging → false
```

This preserves intentional configuration values.

---

# 45. Why `||` Could Be Wrong for Configuration

Consider:

```javascript
const config = {
    retries: 0
};

const retries =
    config.retries || 3;
```

Result:

```text
3
```

But `0` may intentionally mean:

```text
Do not retry.
```

Using:

```javascript
const retries =
    config.retries ?? 3;
```

Result:

```text
0
```

---

# 46. React Props Example

Suppose:

```jsx
function Counter({
    count
}) {
    const displayCount =
        count ?? 0;

    return (
        <p>{displayCount}</p>
    );
}
```

If:

```jsx
<Counter count={5} />
```

it displays:

```text
5
```

If:

```jsx
<Counter count={null} />
```

it displays:

```text
0
```

---

# 47. React Boolean Example

```jsx
function Feature({
    enabled
}) {
    const isEnabled =
        enabled ?? true;

    return (
        <p>
            {
                isEnabled
                    ? "Enabled"
                    : "Disabled"
            }
        </p>
    );
}
```

If:

```jsx
<Feature enabled={false} />
```

the value remains:

```text
false
```

and the component displays:

```text
Disabled
```

---

# 48. React with Optional Chaining

A very common pattern:

```jsx
function UserProfile({
    user
}) {
    const name =
        user?.profile?.name
        ?? "Guest";

    return (
        <h1>{name}</h1>
    );
}
```

Here:

```text
?.
↓
Safely retrieve name

??
↓
Fallback to Guest
```

---

# 49. React Array Example

Suppose:

```javascript
const users = undefined;
```

Use:

```javascript
const firstUser =
    users?.[0]?.name
    ?? "No User";
```

Result:

```text
No User
```

---

# 50. Form Value Example

Suppose:

```javascript
const formData = {
    age: 0,
    newsletter: false,
    nickname: ""
};
```

Using:

```javascript
const age =
    formData.age ?? 18;

const newsletter =
    formData.newsletter ?? true;

const nickname =
    formData.nickname ?? "Guest";
```

Results:

```text
age        → 0
newsletter → false
nickname   → ""
```

The original values are preserved.

---

# 51. Nested Configuration Example

```javascript
const config = {
    server: {
        port: 0
    }
};
```

Use:

```javascript
const port =
    config
        ?.server
        ?.port
    ?? 3000;
```

Result:

```text
0
```

Because `0` is not nullish.

---

# 52. Operator Precedence

When expressions become complicated, use parentheses to make your intention clear.

Example:

```javascript
const result =
    (value ?? 10) * 2;
```

This clearly means:

```text
1. Use value if non-nullish
2. Otherwise use 10
3. Multiply result by 2
```

If:

```javascript
value = null
```

result:

```text
20
```

---

# 53. `??` Cannot Be Directly Mixed with `||`

This is invalid syntax:

```javascript
const result =
    a ?? b || c;
```

JavaScript requires explicit grouping when `??` is mixed directly with `||`.

Use:

```javascript
const result =
    (a ?? b) || c;
```

or:

```javascript
const result =
    a ?? (b || c);
```

These expressions can produce different results, so choose based on the intended logic.

---

# 54. `??` Cannot Be Directly Mixed with `&&`

Similarly, this is invalid:

```javascript
const result =
    a ?? b && c;
```

Use parentheses:

```javascript
const result =
    a ?? (b && c);
```

or:

```javascript
const result =
    (a ?? b) && c;
```

---

# 55. Why Parentheses Matter

Consider:

```javascript
const a = null;
const b = 0;
const c = 100;
```

Expression:

```javascript
(a ?? b) || c
```

First:

```javascript
a ?? b
```

returns:

```text
0
```

Then:

```javascript
0 || 100
```

returns:

```text
100
```

But:

```javascript
a ?? (b || c)
```

evaluates:

```javascript
b || c
```

to:

```text
100
```

and because `a` is nullish, the result is also `100`.

With other values, grouping can change the result significantly. Parentheses make the intended logic explicit.

---

# 56. `??` Does Not Modify the Original Value

Example:

```javascript
let value = null;

const result =
    value ?? 100;
```

Afterward:

```text
value  → null
result → 100
```

`??` selects a result.

It does not assign anything.

---

# 57. `??=` Does Modify the Variable

Compare:

```javascript
let value = null;

const result =
    value ?? 100;
```

with:

```javascript
let value = null;

value ??= 100;
```

After the second example:

```text
value → 100
```

because `??=` performs assignment.

---

# 58. Common Mistake — Thinking `??` Checks Falsy Values

Wrong assumption:

```javascript
const value = 0;

const result =
    value ?? 100;
```

Actual result:

```text
0
```

Because:

```text
0 is falsy
BUT
0 is not nullish
```

---

# 59. Common Mistake — Expecting Empty String to Use Fallback

```javascript
const name = "";

const result =
    name ?? "Guest";
```

Result:

```text
""
```

If you want empty strings to use the fallback, your logic needs to account for that, for example:

```javascript
const result =
    name || "Guest";
```

if all falsy values should trigger the fallback.

---

# 60. Common Mistake — `false`

```javascript
const enabled = false;

const result =
    enabled ?? true;
```

Result:

```text
false
```

This is usually desirable when `false` is an intentional setting.

---

# 61. Common Mistake — `NaN`

```javascript
const value = NaN;

const result =
    value ?? 100;
```

Result:

```text
NaN
```

If `NaN` should be treated as invalid, validate it separately.

For example:

```javascript
const result =
    Number.isNaN(value)
        ? 100
        : value;
```

---

# 62. Common Mistake — Confusing `??` with Optional Chaining

This:

```javascript
user?.name
```

does not provide a custom fallback.

It returns:

```javascript
undefined
```

when the optional chain short-circuits.

To provide a fallback:

```javascript
user?.name ?? "Guest"
```

---

# 63. Common Mistake — Confusing `??` with Default Parameters

Default parameter:

```javascript
function test(
    value = 10
) {
}
```

handles:

```text
undefined
```

Nullish coalescing:

```javascript
value ?? 10
```

handles:

```text
undefined
null
```

They are related, but not identical.

---

# 64. Common Mistake — Using `||` When `0` Is Valid

Potential problem:

```javascript
const quantity =
    data.quantity || 10;
```

If:

```javascript
data.quantity = 0;
```

then:

```text
quantity → 10
```

If `0` is valid, use:

```javascript
const quantity =
    data.quantity ?? 10;
```

---

# 65. Common Mistake — Using `||` When `false` Is Valid

Potential problem:

```javascript
const enabled =
    settings.enabled || true;
```

If:

```javascript
settings.enabled = false;
```

then:

```text
enabled → true
```

Use:

```javascript
const enabled =
    settings.enabled ?? true;
```

Result:

```text
false
```

---

# 66. Common Mistake — Mixing Operators Without Parentheses

Invalid:

```javascript
a ?? b || c
```

Invalid:

```javascript
a || b ?? c
```

Invalid:

```javascript
a ?? b && c
```

Use explicit parentheses.

Example:

```javascript
(a ?? b) || c
```

or:

```javascript
a ?? (b || c)
```

---

# 67. Interview Question 1

What is the output?

```javascript
console.log(
    null ?? "Guest"
);
```

Answer:

```text
Guest
```

---

# 68. Interview Question 2

What is the output?

```javascript
console.log(
    undefined ?? "Guest"
);
```

Answer:

```text
Guest
```

---

# 69. Interview Question 3

What is the output?

```javascript
console.log(
    0 ?? 100
);
```

Answer:

```text
0
```

---

# 70. Interview Question 4

What is the output?

```javascript
console.log(
    false ?? true
);
```

Answer:

```text
false
```

---

# 71. Interview Question 5

What is the output?

```javascript
console.log(
    "" ?? "Guest"
);
```

Answer:

```text
""
```

---

# 72. Interview Question 6

What is the output?

```javascript
console.log(
    NaN ?? 100
);
```

Answer:

```text
NaN
```

---

# 73. Interview Question 7

What is the difference?

```javascript
0 || 100
```

Result:

```text
100
```

But:

```javascript
0 ?? 100
```

Result:

```text
0
```

Because `||` checks falsiness while `??` checks nullishness.

---

# 74. Interview Question 8

What is the output?

```javascript
const user = null;

const name =
    user?.name
    ?? "Guest";

console.log(name);
```

Answer:

```text
Guest
```

---

# 75. Interview Question 9

What is the output?

```javascript
const user = {
    name: ""
};

const name =
    user?.name
    ?? "Guest";

console.log(name);
```

Answer:

```text
""
```

The empty string is preserved.

---

# 76. Interview Question 10

What is the output?

```javascript
const user = {
    active: false
};

const active =
    user?.active
    ?? true;

console.log(active);
```

Answer:

```text
false
```

---

# 77. Interview Question 11

What is the output?

```javascript
const a = null;
const b = undefined;
const c = 0;
const d = 100;

const result =
    a ?? b ?? c ?? d;

console.log(result);
```

Answer:

```text
0
```

`0` is the first non-nullish value.

---

# 78. Interview Question 12

What happens?

```javascript
const result =
    a ?? b || c;
```

Answer:

```text
SyntaxError
```

when written as a direct unparenthesized mixture of `??` and `||`.

Use parentheses to express the intended grouping.

---

# 79. Interview Question 13

What is the output?

```javascript
let value = null;

value ??= 100;

console.log(value);
```

Answer:

```text
100
```

---

# 80. Interview Question 14

What is the output?

```javascript
let value = 0;

value ??= 100;

console.log(value);
```

Answer:

```text
0
```

No assignment occurs because `0` is not nullish.

---

# 81. Interview Question 15

What is the output?

```javascript
function test(
    value = 10
) {
    console.log(
        value ?? 20
    );
}

test(null);
```

Answer:

```text
20
```

Why?

The default parameter does not replace `null`, so:

```text
value → null
```

Then:

```javascript
null ?? 20
```

returns:

```text
20
```

---

# 82. Interview Question 16

What is the output?

```javascript
function test(
    value = 10
) {
    console.log(
        value ?? 20
    );
}

test(undefined);
```

Answer:

```text
10
```

First the default parameter converts:

```text
undefined
↓
10
```

Then:

```javascript
10 ?? 20
```

returns:

```text
10
```

---

# 83. Interview Question 17

What is the output?

```javascript
const config = {
    retries: 0
};

const retries =
    config?.retries
    ?? 3;

console.log(retries);
```

Answer:

```text
0
```

---

# 84. Interview Question 18

What is the output?

```javascript
let enabled = false;

enabled ??= true;

console.log(enabled);
```

Answer:

```text
false
```

---

# 85. Nullish Coalescing Mental Model

Consider:

```javascript
const result =
    value ?? "Default";
```

Think:

```text
             value
               │
               ▼
       null or undefined?
          /          \
        YES           NO
         │             │
         ▼             ▼
    "Default"        value
```

Examples:

```text
null
↓
"Default"

undefined
↓
"Default"

0
↓
0

false
↓
false

""
↓
""
```

---

# 86. `||` vs `??` Mental Model

Logical OR:

```javascript
value || fallback
```

asks:

```text
Is value falsy?
```

Nullish coalescing:

```javascript
value ?? fallback
```

asks:

```text
Is value null or undefined?
```

Therefore:

```text
             Falsy
              │
    ┌─────────┼─────────┐
    │         │         │
    0       false       ""
    │         │         │
    └─────────┼─────────┘
              │
           nullish
              │
        ┌─────┴─────┐
        │           │
       null     undefined
```

More accurately:

```text
Falsy Values
├── false
├── 0
├── -0
├── 0n
├── ""
├── NaN
├── null       ← nullish
└── undefined  ← nullish
```

So:

```text
Nullish values are a subset
of falsy values.
```

---

# 87. `?.` + `??` Mental Model

Example:

```javascript
const name =
    user?.profile?.name
    ?? "Guest";
```

Flow:

```text
user
 │
 ▼
profile exists?
 │
 ├── NO
 │    ↓
 │ undefined
 │
 └── YES
      │
      ▼
     name
      │
      ▼
null / undefined?
      │
  ┌───┴───┐
 YES      NO
  │        │
  ▼        ▼
Guest     value
```

Remember:

```text
?. = safe access

?? = safe fallback
```

---

# 88. Nullish Assignment Mental Model

```javascript
value ??= defaultValue;
```

Think:

```text
value
 │
 ▼
null / undefined?
 │
 ├── YES
 │    │
 │    ▼
 │ Assign defaultValue
 │
 └── NO
      │
      ▼
   Keep value
```

---

# 89. Cheat Sheet

## Basic Nullish Coalescing

```javascript
const result =
    value ?? fallback;
```

## `null`

```javascript
null ?? "Default";
```

Result:

```text
Default
```

## `undefined`

```javascript
undefined ?? "Default";
```

Result:

```text
Default
```

## `0`

```javascript
0 ?? 100;
```

Result:

```text
0
```

## `false`

```javascript
false ?? true;
```

Result:

```text
false
```

## Empty String

```javascript
"" ?? "Default";
```

Result:

```text
""
```

## Optional Chaining

```javascript
user?.name ?? "Guest";
```

## Multiple Fallbacks

```javascript
username ??
email ??
id ??
"Unknown";
```

## Nullish Assignment

```javascript
value ??= defaultValue;
```

## Configuration

```javascript
const retries =
    config?.retries ?? 3;
```

## API Response

```javascript
const name =
    response
        ?.data
        ?.user
        ?.name
    ?? "Guest";
```

---

# 90. Important Rules to Remember

```text
1. Nullish coalescing uses the ?? operator.

2. ?? provides a fallback value.

3. The fallback is used for null.

4. The fallback is used for undefined.

5. These two values are called nullish values.

6. ?? does not use the fallback for 0.

7. ?? does not use the fallback for false.

8. ?? does not use the fallback for "".

9. ?? does not use the fallback for NaN.

10. Nullishness and falsiness are different concepts.

11. null and undefined are both falsy and nullish.

12. 0, false, "", and NaN are falsy but not nullish.

13. || checks truthiness/falsiness.

14. ?? checks nullishness.

15. Use || when any falsy value should trigger a fallback.

16. Use ?? when only null or undefined should trigger a fallback.

17. ?? preserves valid 0 values.

18. ?? preserves valid false values.

19. ?? preserves empty strings.

20. ?? preserves NaN.

21. ?? uses short-circuit evaluation.

22. The right side is evaluated only when the left side is nullish.

23. Multiple ?? operators can be chained.

24. The first non-nullish value is returned.

25. ?. and ?? are commonly used together.

26. ?. safely accesses values.

27. ?? provides fallback values.

28. Default parameters and ?? are not identical.

29. Default parameters trigger for undefined.

30. ?? triggers for null and undefined.

31. ??= is the nullish coalescing assignment operator.

32. ??= assigns only when the current value is null or undefined.

33. ||= assigns when the current value is falsy.

34. &&= assigns when the current value is truthy.

35. ?? itself does not modify the original variable.

36. ??= performs assignment.

37. ?? is useful for API responses.

38. ?? is useful for configuration values.

39. ?? is useful for user preferences.

40. ?? is useful for React props and state.

41. ?? is especially useful when 0 is valid.

42. ?? is especially useful when false is valid.

43. ?? is useful when an empty string is intentionally valid.

44. Do not directly mix ?? with || without parentheses.

45. Do not directly mix ?? with && without parentheses.

46. Parentheses should make the intended logic explicit.

47. ?? does not perform validation.

48. ?? does not treat NaN as missing.

49. Use Number.isNaN() if NaN requires special handling.

50. Think "missing value" when deciding whether ?? is appropriate.
```

---

# 91. Most Important Concept

The most important distinction is:

```text
FALSY
================

false
0
-0
0n
""
NaN
null
undefined


NULLISH
================

null
undefined
```

Therefore:

```javascript
0 || 100
```

returns:

```text
100
```

while:

```javascript
0 ?? 100
```

returns:

```text
0
```

And:

```javascript
false || true
```

returns:

```text
true
```

while:

```javascript
false ?? true
```

returns:

```text
false
```

And:

```javascript
"" || "Guest"
```

returns:

```text
Guest
```

while:

```javascript
"" ?? "Guest"
```

returns:

```text
""
```

So remember:

```text
|| → "Do I have a truthy value?"

?? → "Do I have a non-nullish value?"
```

---

# 92. Nullish Coalescing in One Sentence

> The nullish coalescing operator (`??`) returns its right-hand fallback only when the left-hand value is `null` or `undefined`, preserving other falsy values such as `0`, `false`, `""`, and `NaN`.
