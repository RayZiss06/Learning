# JavaScript — Fetch API

The **Fetch API** is a browser API used to make HTTP requests from JavaScript.

It is commonly used to communicate with:

* REST APIs
* Backend servers
* Microservices
* Authentication services
* External APIs

Basic example:

```javascript
const response = await fetch("/api/users");
const data = await response.json();

console.log(data);
```

Basic flow:

```text
JavaScript Application
        │
        │ HTTP Request
        ▼
      Server
        │
        │ HTTP Response
        ▼
   fetch() Promise
        │
        ▼
 Response Object
        │
        ▼
   Parse Body
        │
        ▼
       Data
```

---

# 1. What Is `fetch()`?

`fetch()` is a global function for making HTTP requests.

Syntax:

```javascript
fetch(resource, options);
```

Example:

```javascript
fetch("/api/users");
```

It returns a:

```text
Promise<Response>
```

Therefore:

```javascript
const result = fetch("/api/users");

console.log(result);
```

`result` is a Promise, not the actual response data.

---

# 2. Using Fetch with `async/await`

The most common modern pattern:

```javascript
async function getUsers() {
    const response = await fetch("/api/users");

    const data = await response.json();

    return data;
}
```

Usage:

```javascript
const users = await getUsers();

console.log(users);
```

---

# 3. Using Fetch with Promises

Fetch does not require `async/await`.

You can use:

```javascript
fetch("/api/users")
    .then(response => {
        return response.json();
    })
    .then(data => {
        console.log(data);
    })
    .catch(error => {
        console.error(error);
    });
```

Equivalent async/await version:

```javascript
try {
    const response = await fetch("/api/users");
    const data = await response.json();

    console.log(data);
} catch (error) {
    console.error(error);
}
```

---

# 4. HTTP Request and Response

A client-server interaction generally looks like:

```text
CLIENT
   │
   │ Request
   │
   ▼
SERVER
   │
   │ Response
   ▼
CLIENT
```

A request may contain:

```text
HTTP Method
URL
Headers
Query Parameters
Body
Credentials
```

A response may contain:

```text
Status Code
Headers
Body
```

---

# 5. HTTP Methods

Common HTTP methods:

```text
GET
POST
PUT
PATCH
DELETE
```

Typical meaning:

| Method   | Purpose                     |
| -------- | --------------------------- |
| `GET`    | Retrieve data               |
| `POST`   | Create/send data            |
| `PUT`    | Replace/update a resource   |
| `PATCH`  | Partially update a resource |
| `DELETE` | Delete a resource           |

The exact behavior is ultimately determined by the server's API contract.

---

# 6. GET Request

`GET` is the default method used by `fetch()`.

```javascript
const response = await fetch("/api/users");
```

Equivalent:

```javascript
const response = await fetch("/api/users", {
    method: "GET"
});
```

Usually, you do not need to specify `"GET"` explicitly.

---

# 7. Getting JSON Data

Suppose the server returns:

```json
[
    {
        "id": 1,
        "name": "John"
    },
    {
        "id": 2,
        "name": "Alice"
    }
]
```

Fetch:

```javascript
const response = await fetch("/api/users");

const users = await response.json();

console.log(users);
```

---

# 8. `response.json()`

`response.json()` reads the response body and parses it as JSON.

Important:

```javascript
const data = response.json();
```

returns a Promise.

Therefore:

```javascript
const data = await response.json();
```

is normally required when using async/await.

---

# 9. Why Are There Two `await`s?

You often see:

```javascript
const response = await fetch("/api/users");

const data = await response.json();
```

Why?

The first:

```javascript
await fetch(...)
```

waits for the request to produce a `Response`.

The second:

```javascript
await response.json()
```

waits for the response body to be read and parsed.

Conceptually:

```text
fetch()
   │
   ▼
Promise<Response>
   │
 await
   ▼
Response
   │
.json()
   ▼
Promise<Data>
   │
 await
   ▼
JavaScript Data
```

---

# 10. The `Response` Object

`fetch()` resolves with a `Response` object.

Example:

```javascript
const response = await fetch("/api/users");

console.log(response);
```

Useful properties include:

```javascript
response.ok
response.status
response.statusText
response.headers
response.url
response.redirected
response.type
```

The response body can be consumed using methods such as:

```javascript
response.json()
response.text()
response.blob()
response.arrayBuffer()
response.formData()
```

---

# 11. `response.ok`

`response.ok` is a Boolean indicating whether the HTTP status is in the successful `200–299` range.

Example:

```javascript
const response = await fetch("/api/users");

if (response.ok) {
    console.log("Success");
}
```

---

# 12. `response.status`

Returns the HTTP status code.

```javascript
console.log(response.status);
```

Examples:

```text
200
201
204
400
401
403
404
500
```

Common meanings:

| Code  | Meaning               |
| ----- | --------------------- |
| `200` | OK                    |
| `201` | Created               |
| `204` | No Content            |
| `400` | Bad Request           |
| `401` | Unauthorized          |
| `403` | Forbidden             |
| `404` | Not Found             |
| `409` | Conflict              |
| `429` | Too Many Requests     |
| `500` | Internal Server Error |
| `502` | Bad Gateway           |
| `503` | Service Unavailable   |

---

# 13. `response.statusText`

Example:

```javascript
console.log(response.statusText);
```

Depending on the response/environment, it may contain text such as:

```text
OK
Not Found
Internal Server Error
```

Do not rely on `statusText` as your primary error information because it may be empty or vary.

---

# 14. Important Fetch Behavior — HTTP Errors

One of the most important Fetch concepts:

> `fetch()` does not normally reject merely because the server returned an HTTP error status.

For example, the server may return:

```text
404 Not Found
```

or:

```text
500 Internal Server Error
```

and:

```javascript
const response = await fetch(url);
```

can still resolve successfully with a `Response`.

So this is insufficient:

```javascript
try {
    const response = await fetch(url);

    console.log("Success");
} catch (error) {
    console.error(error);
}
```

because a `404` may never enter `catch`.

---

# 15. Proper HTTP Error Handling

Check:

```javascript
response.ok
```

Example:

```javascript
async function getUsers() {
    const response = await fetch("/api/users");

    if (!response.ok) {
        throw new Error(
            `Request failed: ${response.status}`
        );
    }

    return response.json();
}
```

Usage:

```javascript
try {
    const users = await getUsers();

    console.log(users);
} catch (error) {
    console.error(error);
}
```

---

# 16. Network Error vs HTTP Error

These are different.

## Network Error

Examples:

```text
No network connection
DNS failure
Connection failure
Request aborted
Certain CORS failures
```

`fetch()` may reject:

```javascript
try {
    await fetch(url);
} catch (error) {
    console.error("Network/request failure");
}
```

## HTTP Error

Examples:

```text
404
401
500
503
```

The server responded.

`fetch()` generally resolves with that response.

You need:

```javascript
if (!response.ok) {
    throw new Error(...);
}
```

---

# 17. POST Request

`POST` is commonly used to create/send data.

```javascript
const response = await fetch("/api/users", {
    method: "POST"
});
```

Usually we also send a body.

---

# 18. Sending JSON

Suppose:

```javascript
const user = {
    name: "John",
    email: "john@example.com"
};
```

Send:

```javascript
const response = await fetch("/api/users", {
    method: "POST",

    headers: {
        "Content-Type": "application/json"
    },

    body: JSON.stringify(user)
});
```

---

# 19. Why `JSON.stringify()`?

JavaScript object:

```javascript
const user = {
    name: "John"
};
```

is a JavaScript value.

For a JSON request body, convert it into JSON text:

```javascript
JSON.stringify(user);
```

Result:

```json
{"name":"John"}
```

So:

```text
JavaScript Object
        │
        ▼
 JSON.stringify()
        │
        ▼
    JSON Text
        │
        ▼
  Request Body
```

---

# 20. Why `Content-Type`?

Header:

```javascript
"Content-Type": "application/json"
```

tells the server:

```text
The request body is JSON.
```

Example:

```javascript
headers: {
    "Content-Type": "application/json"
}
```

Without the expected content type, some servers may not parse the body correctly.

---

# 21. Complete POST Example

```javascript
async function createUser(user) {
    const response = await fetch("/api/users", {
        method: "POST",

        headers: {
            "Content-Type": "application/json"
        },

        body: JSON.stringify(user)
    });

    if (!response.ok) {
        throw new Error(
            `Failed to create user: ${response.status}`
        );
    }

    return response.json();
}
```

Usage:

```javascript
const user = await createUser({
    name: "John",
    email: "john@example.com"
});

console.log(user);
```

---

# 22. PUT Request

`PUT` is commonly used to replace/update a resource according to the API contract.

Example:

```javascript
async function replaceUser(id, user) {
    const response = await fetch(
        `/api/users/${id}`,
        {
            method: "PUT",

            headers: {
                "Content-Type": "application/json"
            },

            body: JSON.stringify(user)
        }
    );

    if (!response.ok) {
        throw new Error(
            `Update failed: ${response.status}`
        );
    }

    return response.json();
}
```

---

# 23. PATCH Request

`PATCH` is commonly used for partial updates.

Example:

```javascript
async function updateUser(id, updates) {
    const response = await fetch(
        `/api/users/${id}`,
        {
            method: "PATCH",

            headers: {
                "Content-Type": "application/json"
            },

            body: JSON.stringify(updates)
        }
    );

    if (!response.ok) {
        throw new Error(
            `Update failed: ${response.status}`
        );
    }

    return response.json();
}
```

Usage:

```javascript
await updateUser(10, {
    name: "Alice"
});
```

---

# 24. PUT vs PATCH

General convention:

```text
PUT
================================

Replace/update the resource
representation as defined
by the API.


PATCH
================================

Apply a partial modification.
```

Example resource:

```javascript
{
    id: 1,
    name: "John",
    email: "john@example.com"
}
```

A PATCH might send only:

```javascript
{
    name: "Alice"
}
```

Actual semantics depend on the backend API.

---

# 25. DELETE Request

Example:

```javascript
async function deleteUser(id) {
    const response = await fetch(
        `/api/users/${id}`,
        {
            method: "DELETE"
        }
    );

    if (!response.ok) {
        throw new Error(
            `Delete failed: ${response.status}`
        );
    }
}
```

Usage:

```javascript
await deleteUser(10);
```

---

# 26. `204 No Content`

DELETE and some update operations may return:

```text
204 No Content
```

There is no response body.

Therefore, this can fail:

```javascript
return response.json();
```

if the response contains no JSON body.

Instead:

```javascript
if (response.status === 204) {
    return null;
}

return response.json();
```

---

# 27. Headers

HTTP headers contain metadata about requests and responses.

Example:

```javascript
const response = await fetch("/api/users", {
    headers: {
        "Accept": "application/json"
    }
});
```

Common request headers:

```text
Accept
Content-Type
Authorization
```

There are many others.

Some headers are controlled/restricted by the browser and cannot be freely set from frontend JavaScript.

---

# 28. `Accept`

Example:

```javascript
headers: {
    "Accept": "application/json"
}
```

This communicates that the client prefers:

```text
JSON response data
```

Contrast:

```text
Content-Type
=
What format am I sending?


Accept
=
What response format do I prefer?
```

---

# 29. Authorization Header

Token-based APIs often use:

```javascript
headers: {
    "Authorization": `Bearer ${token}`
}
```

Example:

```javascript
const response = await fetch("/api/profile", {
    headers: {
        "Authorization": `Bearer ${token}`
    }
});
```

Conceptually:

```text
Client
  │
  │ Authorization:
  │ Bearer <token>
  ▼
Server
  │
  ▼
Validate Token
```

---

# 30. Multiple Headers

```javascript
const response = await fetch("/api/users", {
    method: "POST",

    headers: {
        "Content-Type": "application/json",
        "Accept": "application/json",
        "Authorization": `Bearer ${token}`
    },

    body: JSON.stringify(user)
});
```

---

# 31. `Headers` Object

Instead of a plain object:

```javascript
const headers = new Headers();

headers.set(
    "Content-Type",
    "application/json"
);

headers.set(
    "Authorization",
    `Bearer ${token}`
);

const response = await fetch("/api/users", {
    headers
});
```

Useful methods include:

```javascript
headers.set()
headers.get()
headers.has()
headers.delete()
headers.append()
```

---

# 32. Reading Response Headers

```javascript
const response = await fetch("/api/users");

console.log(
    response.headers.get("Content-Type")
);
```

Example result:

```text
application/json
```

Header names are case-insensitive.

---

# 33. Query Parameters

Suppose you need:

```text
/api/users?page=2&limit=20
```

You can write:

```javascript
const response = await fetch(
    "/api/users?page=2&limit=20"
);
```

But dynamic parameters should be encoded properly.

---

# 34. `URLSearchParams`

```javascript
const params = new URLSearchParams({
    page: "2",
    limit: "20",
    search: "john"
});

const response = await fetch(
    `/api/users?${params}`
);
```

Generated query:

```text
page=2&limit=20&search=john
```

---

# 35. Dynamic Query Example

```javascript
async function getUsers({
    page,
    limit,
    search
}) {
    const params = new URLSearchParams();

    params.set("page", String(page));
    params.set("limit", String(limit));

    if (search) {
        params.set("search", search);
    }

    const response = await fetch(
        `/api/users?${params}`
    );

    if (!response.ok) {
        throw new Error(
            `Request failed: ${response.status}`
        );
    }

    return response.json();
}
```

Usage:

```javascript
const users = await getUsers({
    page: 2,
    limit: 20,
    search: "John Doe"
});
```

`URLSearchParams` handles URL encoding.

---

# 36. Path Parameters vs Query Parameters

Path:

```text
/api/users/10
```

Usually identifies a specific resource:

```javascript
fetch(`/api/users/${id}`);
```

Query:

```text
/api/users?page=2&active=true
```

Often controls:

```text
Filtering
Sorting
Pagination
Searching
```

Example:

```javascript
fetch(
    `/api/users?active=true&page=2`
);
```

---

# 37. Response Body Types

A response is not always JSON.

Common body methods:

```javascript
response.json()
response.text()
response.blob()
response.arrayBuffer()
response.formData()
```

Choose according to the response content.

---

# 38. Text Response

```javascript
const response = await fetch("/message.txt");

const text = await response.text();

console.log(text);
```

---

# 39. Blob Response

A `Blob` represents binary-like data.

Example:

```javascript
const response = await fetch("/image.png");

const blob = await response.blob();
```

Common uses:

```text
Images
PDFs
Downloads
Binary files
```

---

# 40. ArrayBuffer

```javascript
const response = await fetch("/file");

const buffer =
    await response.arrayBuffer();
```

Useful for lower-level binary processing.

---

# 41. Response Body Can Normally Be Consumed Once

Example:

```javascript
const response = await fetch("/api/users");

const data1 = await response.json();

const data2 = await response.json();
```

The second attempt will generally fail because the body has already been consumed.

You can inspect:

```javascript
response.bodyUsed
```

---

# 42. `response.clone()`

If you genuinely need to consume the response body more than once, clone it before consumption:

```javascript
const response = await fetch("/api/users");

const copy = response.clone();

const json = await response.json();

const text = await copy.text();
```

Use cloning deliberately because it has resource implications.

---

# 43. FormData

`FormData` is useful for submitting forms and files.

Example:

```javascript
const formData = new FormData();

formData.append("name", "John");
formData.append("email", "john@example.com");

const response = await fetch("/api/users", {
    method: "POST",
    body: formData
});
```

---

# 44. File Upload

Suppose:

```html
<input type="file" id="fileInput">
```

JavaScript:

```javascript
const input =
    document.querySelector("#fileInput");

const file =
    input.files[0];

const formData =
    new FormData();

formData.append("file", file);

const response = await fetch("/api/upload", {
    method: "POST",
    body: formData
});
```

---

# 45. Important FormData Rule

When using `FormData`, normally do **not** manually set:

```javascript
"Content-Type": "multipart/form-data"
```

The browser sets the correct header including the required multipart boundary.

Correct:

```javascript
fetch("/api/upload", {
    method: "POST",
    body: formData
});
```

---

# 46. Request Options

The second argument to `fetch()` is a configuration object.

Example:

```javascript
fetch(url, {
    method: "POST",
    headers: {},
    body: data,
    credentials: "include",
    signal: controller.signal
});
```

Other options exist, including:

```text
mode
cache
redirect
referrer
referrerPolicy
integrity
keepalive
```

You usually only configure the ones needed by the request.

---

# 47. Cookies and Credentials

For cross-origin requests where cookies/HTTP authentication credentials need to be included:

```javascript
fetch(url, {
    credentials: "include"
});
```

Credential modes include:

```text
omit
same-origin
include
```

---

# 48. `credentials: "same-origin"`

```javascript
fetch(url, {
    credentials: "same-origin"
});
```

This is the default credential mode for `fetch()`.

Credentials are included for same-origin requests.

---

# 49. `credentials: "include"`

```javascript
fetch(url, {
    credentials: "include"
});
```

Allows credentials to be included for both same-origin and cross-origin requests, subject to browser security rules and server configuration.

For cross-origin credentialed requests, the server must explicitly support them.

---

# 50. `credentials: "omit"`

```javascript
fetch(url, {
    credentials: "omit"
});
```

Tells Fetch not to send credentials and to ignore credentials from the response.

---

# 51. CORS

**CORS** means:

```text
Cross-Origin Resource Sharing
```

Suppose frontend:

```text
https://frontend.example.com
```

calls backend:

```text
https://api.example.com
```

These are different origins.

The browser applies cross-origin security rules.

The server must return appropriate CORS headers to allow the frontend origin when required.

---

# 52. What Is an Origin?

An origin is determined by:

```text
Scheme
+
Host
+
Port
```

Example:

```text
https://example.com:443
```

Changing any relevant part can create a different origin.

Examples:

```text
http://example.com
https://example.com

Different scheme
```

```text
https://example.com
https://api.example.com

Different host
```

```text
http://localhost:3000
http://localhost:8080

Different port
```

---

# 53. CORS Is Enforced by Browsers

A common misunderstanding:

```text
Frontend should "fix CORS"
```

Usually CORS permission must be configured by the server.

The frontend cannot grant itself permission to read a cross-origin response.

---

# 54. CORS Preflight

Some cross-origin requests trigger an:

```text
OPTIONS
```

request before the actual request.

This is called a:

```text
Preflight Request
```

Flow:

```text
Browser
   │
   │ OPTIONS
   ▼
Server
   │
   │ CORS response
   ▼
Browser
   │
   │ If allowed
   ▼
Actual Request
```

The browser handles this process automatically.

---

# 55. CORS Is Not Authentication

CORS answers roughly:

```text
May browser JavaScript from this origin
access this cross-origin response?
```

Authentication answers:

```text
Who is this user/client?
```

Authorization answers:

```text
What is this identity allowed to do?
```

They are separate concepts.

---

# 56. Reusable Fetch Function

Instead of repeating:

```javascript
const response = await fetch(...);

if (!response.ok) {
    throw ...
}

return response.json();
```

you can build a reusable API helper.

Example:

```javascript
async function apiRequest(
    url,
    options = {}
) {
    const response =
        await fetch(url, options);

    if (!response.ok) {
        throw new Error(
            `HTTP ${response.status}`
        );
    }

    if (response.status === 204) {
        return null;
    }

    return response.json();
}
```

Usage:

```javascript
const users =
    await apiRequest("/api/users");
```

---

# 57. Reusable GET Function

```javascript
async function get(url) {
    const response = await fetch(url);

    if (!response.ok) {
        throw new Error(
            `GET ${url} failed: ${response.status}`
        );
    }

    return response.json();
}
```

---

# 58. Reusable POST Function

```javascript
async function post(url, data) {
    const response = await fetch(url, {
        method: "POST",

        headers: {
            "Content-Type": "application/json"
        },

        body: JSON.stringify(data)
    });

    if (!response.ok) {
        throw new Error(
            `POST ${url} failed: ${response.status}`
        );
    }

    return response.json();
}
```

---

# 59. Better API Client

```javascript
async function apiRequest(
    path,
    {
        method = "GET",
        body,
        headers = {},
        ...options
    } = {}
) {
    const response = await fetch(path, {
        method,

        headers: {
            "Accept": "application/json",

            ...(body !== undefined && {
                "Content-Type": "application/json"
            }),

            ...headers
        },

        ...(body !== undefined && {
            body: JSON.stringify(body)
        }),

        ...options
    });

    if (!response.ok) {
        throw new Error(
            `HTTP ${response.status}`
        );
    }

    if (response.status === 204) {
        return null;
    }

    return response.json();
}
```

Usage:

```javascript
const users = await apiRequest(
    "/api/users"
);
```

POST:

```javascript
const user = await apiRequest(
    "/api/users",
    {
        method: "POST",

        body: {
            name: "John"
        }
    }
);
```

This is the beginning of an:

```text
API Client Layer
```

---

# 60. API Service Pattern

In a larger application:

```text
src/
│
├── api/
│   ├── client.js
│   ├── users.js
│   └── products.js
│
├── components/
├── pages/
└── ...
```

`client.js`:

```javascript
export async function apiRequest(
    url,
    options
) {
    // shared fetch logic
}
```

`users.js`:

```javascript
import { apiRequest }
    from "./client.js";

export function getUsers() {
    return apiRequest("/api/users");
}

export function getUser(id) {
    return apiRequest(
        `/api/users/${id}`
    );
}
```

Components do not need to know every low-level Fetch detail.

---

# 61. Custom API Error

Instead of throwing generic errors:

```javascript
class ApiError extends Error {
    constructor(
        message,
        status,
        data
    ) {
        super(message);

        this.name = "ApiError";
        this.status = status;
        this.data = data;
    }
}
```

Then:

```javascript
throw new ApiError(
    "Request failed",
    response.status,
    errorData
);
```

Caller:

```javascript
try {
    await getUsers();
} catch (error) {
    if (error instanceof ApiError) {
        console.log(error.status);
        console.log(error.data);
    }
}
```

---

# 62. Reading Error Response Bodies

APIs often return useful errors:

```json
{
    "message": "Email already exists",
    "code": "USER_EXISTS"
}
```

You can preserve them:

```javascript
async function apiRequest(url, options) {
    const response =
        await fetch(url, options);

    const contentType =
        response.headers.get(
            "Content-Type"
        ) || "";

    let data = null;

    if (
        contentType.includes(
            "application/json"
        )
    ) {
        data = await response.json();
    }

    if (!response.ok) {
        throw new ApiError(
            data?.message ||
                `HTTP ${response.status}`,
            response.status,
            data
        );
    }

    return data;
}
```

Now the UI can distinguish errors such as:

```text
401 → Login required
403 → Access denied
404 → Resource missing
409 → Conflict
422 → Validation problem
429 → Rate limited
500 → Server failure
```

depending on the API contract.

---

# 63. Parsing Errors

This can fail:

```javascript
const data = await response.json();
```

if the body is not valid JSON.

Example server response:

```text
Internal Server Error
```

but your code assumes JSON.

Therefore:

```text
Network errors
HTTP errors
Body parsing errors
```

are separate failure categories.

---

# 64. Parallel Requests

Independent requests:

```javascript
const [
    users,
    products
] = await Promise.all([
    fetch("/api/users")
        .then(response => response.json()),

    fetch("/api/products")
        .then(response => response.json())
]);
```

Better with service functions:

```javascript
const [
    users,
    products
] = await Promise.all([
    getUsers(),
    getProducts()
]);
```

---

# 65. Dependent Requests

Suppose orders require a user ID:

```javascript
const user =
    await getCurrentUser();

const orders =
    await getOrders(user.id);
```

Correct sequence:

```text
User Request
     ↓
User
     ↓
user.id
     ↓
Orders Request
```

These requests cannot be fully concurrent because the second depends on the first result.

---

# 66. Mixed Sequential + Concurrent Requests

Suppose:

1. Get current user.
2. Once user is known, get orders and notifications independently.

```javascript
const user =
    await getCurrentUser();

const [
    orders,
    notifications
] = await Promise.all([
    getOrders(user.id),
    getNotifications(user.id)
]);
```

Flow:

```text
        getUser
           │
           ▼
          User
         /    \
        /      \
       ▼        ▼
   Orders   Notifications
```

This is a common real-world pattern.

---

# 67. AbortController

Fetch supports cancellation through:

```javascript
AbortController
```

Basic example:

```javascript
const controller =
    new AbortController();

fetch("/api/users", {
    signal: controller.signal
});
```

Cancel:

```javascript
controller.abort();
```

---

# 68. Handling Abort

```javascript
const controller =
    new AbortController();

try {
    const response = await fetch(
        "/api/users",
        {
            signal: controller.signal
        }
    );
} catch (error) {
    if (error.name === "AbortError") {
        console.log(
            "Request cancelled"
        );
    } else {
        throw error;
    }
}
```

We will cover `AbortController` deeply in the next topic.

---

# 69. Timeout Concept

A timeout can be implemented by aborting a request after a duration.

Example:

```javascript
const controller =
    new AbortController();

const timeoutId =
    setTimeout(() => {
        controller.abort();
    }, 5000);

try {
    const response =
        await fetch("/api/users", {
            signal: controller.signal
        });

    return response;
} finally {
    clearTimeout(timeoutId);
}
```

This is actual cancellation of the fetch, unlike simply racing it against a rejecting Promise.

Modern environments may also provide APIs such as:

```javascript
AbortSignal.timeout(...)
```

where supported.

---

# 70. Retry Concept

Some failures may be temporary:

```text
429 Too Many Requests
502 Bad Gateway
503 Service Unavailable
Network interruption
```

A retry strategy may:

```text
Request
   │
   ▼
Failure
   │
   ▼
Wait
   │
   ▼
Retry
```

But do not blindly retry every failure.

For example:

```text
400
401
403
404
```

usually require a different action rather than repeated requests.

Retry behavior should depend on the API and operation.

---

# 71. Exponential Backoff

A retry strategy may increase the wait between attempts:

```text
Attempt 1
↓
Wait 1 second

Attempt 2
↓
Wait 2 seconds

Attempt 3
↓
Wait 4 seconds

Attempt 4
↓
Wait 8 seconds
```

This is called:

```text
Exponential Backoff
```

Production systems commonly add randomization called:

```text
Jitter
```

to avoid many clients retrying simultaneously.

---

# 72. Retry Safety and Idempotency

Retries are not equally safe for every operation.

Repeated:

```text
GET /users
```

is generally safer than blindly retrying:

```text
POST /payments
```

because a repeated state-changing request could create duplicate effects unless the server provides appropriate idempotency guarantees.

So retry logic must understand the API operation.

---

# 73. Fetch Security — Never Trust Frontend Secrets

Do not put secrets such as:

```text
Database passwords
Private API keys
Service credentials
Signing secrets
Client secrets
```

inside frontend JavaScript.

Anything shipped to the browser should be considered visible to the user.

Bad:

```javascript
const SECRET_KEY =
    "my-super-secret-key";
```

Frontend code is not a secure secret-storage location.

---

# 74. Frontend Cannot Secure an API by Hiding the URL

This:

```javascript
fetch("/api/admin");
```

does not make the endpoint secure merely because users do not see a button for it.

Backend security must enforce:

```text
Authentication
Authorization
Input validation
Permissions
Rate limits
Other server-side controls
```

The client is not a trusted security boundary.

---

# 75. Authentication vs Authorization

Authentication:

```text
Who are you?
```

Authorization:

```text
What are you allowed to do?
```

Example:

```text
Login
↓
Authenticated User
↓
Request /api/admin
↓
Server checks permissions
↓
Allow / Deny
```

Frontend checks can improve UX.

Backend checks provide actual enforcement.

---

# 76. Token Storage Considerations

You may see authentication tokens stored in:

```text
Memory
Cookies
localStorage
sessionStorage
```

These approaches have different security characteristics.

For example:

* JavaScript-readable storage can be exposed by XSS.
* `HttpOnly` cookies cannot be read by frontend JavaScript.
* Cookie-based authentication requires considering CSRF and cookie configuration.
* Cross-origin authentication requires appropriate CORS and credential configuration.

There is no universal:

```text
Always store tokens in X
```

rule independent of the application's architecture and threat model.

We will cover authentication separately.

---

# 77. Fetch in React

A simplified React pattern:

```javascript
useEffect(() => {
    async function loadUsers() {
        try {
            const users =
                await getUsers();

            setUsers(users);
        } catch (error) {
            setError(error);
        }
    }

    loadUsers();
}, []);
```

But production React introduces additional concerns:

```text
Loading states
Errors
Cancellation
Stale requests
Race conditions
Caching
Deduplication
Retries
Revalidation
Server state
```

These will be covered during React.

---

# 78. Request Lifecycle in UI

A request often has states:

```text
Idle
  │
  ▼
Loading
  │
  ├─────────────┐
  ▼             ▼
Success        Error
```

Example state:

```javascript
{
    data: null,
    loading: false,
    error: null
}
```

When request begins:

```javascript
{
    data: null,
    loading: true,
    error: null
}
```

On success:

```javascript
{
    data: users,
    loading: false,
    error: null
}
```

On failure:

```javascript
{
    data: null,
    loading: false,
    error: error
}
```

This pattern becomes important in React.

---

# 79. Common Mistake — Forgetting `await response.json()`

Wrong:

```javascript
const response =
    await fetch("/api/users");

const data =
    response.json();

console.log(data);
```

`data` is a Promise.

Correct:

```javascript
const data =
    await response.json();
```

---

# 80. Common Mistake — Assuming `fetch()` Returns Data

Wrong:

```javascript
const users =
    await fetch("/api/users");

console.log(users[0]);
```

`users` is actually a:

```text
Response
```

Correct:

```javascript
const response =
    await fetch("/api/users");

const users =
    await response.json();
```

---

# 81. Common Mistake — Assuming 404 Rejects

Wrong assumption:

```javascript
try {
    const response =
        await fetch("/api/user/999");

    // Assume success
} catch (error) {
    // Assume HTTP errors come here
}
```

Correct:

```javascript
const response =
    await fetch("/api/user/999");

if (!response.ok) {
    throw new Error(
        `HTTP ${response.status}`
    );
}
```

---

# 82. Common Mistake — Sending Object Directly

Usually wrong for a JSON API:

```javascript
body: {
    name: "John"
}
```

Correct:

```javascript
body: JSON.stringify({
    name: "John"
})
```

with:

```javascript
headers: {
    "Content-Type": "application/json"
}
```

---

# 83. Common Mistake — Parsing `204` as JSON

Potentially wrong:

```javascript
const response =
    await fetch(url, {
        method: "DELETE"
    });

return response.json();
```

If response:

```text
204 No Content
```

there is no JSON to parse.

Handle accordingly.

---

# 84. Common Mistake — Manual Multipart Header

Avoid:

```javascript
fetch("/upload", {
    method: "POST",

    headers: {
        "Content-Type":
            "multipart/form-data"
    },

    body: formData
});
```

Normally let the browser create the correct multipart header and boundary.

---

# 85. Common Mistake — Hardcoding Query Strings

Instead of:

```javascript
const url =
    `/api/users?search=${search}`;
```

prefer:

```javascript
const params =
    new URLSearchParams({
        search
    });

const url =
    `/api/users?${params}`;
```

This handles encoding correctly.

---

# 86. Common Mistake — Sequential Independent Fetches

Potentially slow:

```javascript
const users =
    await getUsers();

const products =
    await getProducts();

const orders =
    await getOrders();
```

If independent:

```javascript
const [
    users,
    products,
    orders
] = await Promise.all([
    getUsers(),
    getProducts(),
    getOrders()
]);
```

---

# 87. Common Mistake — Swallowing Errors

Avoid:

```javascript
try {
    await fetchData();
} catch (error) {
}
```

If the error should be handled:

```javascript
try {
    await fetchData();
} catch (error) {
    console.error(error);

    throw error;
}
```

Or convert it into an appropriate application-level error.

---

# 88. Common Mistake — Returning Raw Fetch Everywhere

You can write:

```javascript
fetch("/api/users");
```

throughout every component.

But larger applications benefit from abstraction:

```text
Component
    │
    ▼
User Service
    │
    ▼
API Client
    │
    ▼
fetch()
```

This centralizes concerns such as:

```text
Base URL
Headers
Authentication
Parsing
Errors
Cancellation
Logging
```

---

# 89. Common Mistake — Client-Side Authorization Only

Wrong security model:

```javascript
if (user.role === "admin") {
    showDeleteButton();
}
```

and assuming the API is protected.

An attacker can call APIs independently of your UI.

The backend must verify authorization for:

```text
DELETE /api/users/10
```

regardless of whether the frontend displays the button.

---

# 90. Common Mistake — Exposing Secrets

Environment variables used in frontend builds are not automatically secret.

If the value is included in the client bundle or sent to the browser, users can inspect it.

Frontend:

```text
Public configuration
=
fine

Private credentials
=
not fine
```

Secrets belong in trusted backend/server environments.

---

# 91. Interview Question — What Does Fetch Return?

`fetch()` returns a:

```text
Promise<Response>
```

It does not directly return the parsed response body.

---

# 92. Interview Question — Why Two Awaits?

```javascript
const response =
    await fetch(url);

const data =
    await response.json();
```

The first waits for the HTTP response object.

The second waits for the response body to be read and parsed as JSON.

---

# 93. Interview Question — Does Fetch Reject on 404?

Normally, no.

A `404` is an HTTP response.

Fetch generally resolves with the `Response`.

Check:

```javascript
response.ok
```

or:

```javascript
response.status
```

and throw an application error when appropriate.

---

# 94. Interview Question — When Does Fetch Reject?

Fetch rejects when the request itself cannot complete successfully at the Fetch/network layer, such as certain network failures or when the request is aborted.

HTTP error status codes alone do not normally cause rejection.

---

# 95. Interview Question — What Is `response.ok`?

A Boolean that is true when the response status is within:

```text
200–299
```

---

# 96. Interview Question — `json()` vs `text()`?

```javascript
response.json()
```

reads the body and parses it as JSON.

```javascript
response.text()
```

reads the body as text.

Both return Promises.

---

# 97. Interview Question — Can Response Body Be Read Twice?

Normally not from the same response stream.

Once consumed:

```javascript
await response.json();
```

the body is used.

If needed, clone the response beforehand:

```javascript
const copy =
    response.clone();
```

---

# 98. Interview Question — What Is CORS?

CORS is a browser-controlled mechanism that allows servers to specify which cross-origin browser requests may access their responses.

---

# 99. Interview Question — Can Frontend Fix CORS?

Usually no.

The server must provide the required CORS permissions.

Frontend JavaScript cannot grant itself cross-origin access.

---

# 100. Interview Question — Why Not Manually Set Multipart Content-Type?

When sending `FormData`, the browser generates a multipart boundary.

If you manually set:

```text
multipart/form-data
```

without the correct boundary, the server may be unable to parse the request.

---

# 101. Interview Question — What Is `AbortController`?

`AbortController` provides an abort signal that supported APIs such as Fetch can use to cancel an operation.

Example:

```javascript
const controller =
    new AbortController();

fetch(url, {
    signal: controller.signal
});

controller.abort();
```

---

# 102. Interview Question — Promise.race Timeout vs Abort?

`Promise.race()` can stop your code from waiting for a fetch result.

It does not itself cancel the underlying fetch.

`AbortController` can actually signal Fetch to abort the request.

---

# 103. Interview Question — GET vs POST?

General convention:

```text
GET
=
Retrieve resource/data.


POST
=
Submit/create/process data.
```

Actual semantics depend on the API.

---

# 104. Interview Question — PUT vs PATCH?

General convention:

```text
PUT
=
Replace/update a resource
representation.


PATCH
=
Apply partial changes.
```

The server's API contract defines the exact semantics.

---

# 105. Interview Question — What Is FormData?

`FormData` represents form-style key/value data and is especially useful for:

```text
Forms
File uploads
multipart/form-data requests
```

---

# 106. Interview Question — Why Use URLSearchParams?

It provides a structured way to create and encode URL query parameters.

Example:

```javascript
const params =
    new URLSearchParams({
        search: "John Doe",
        page: "2"
    });
```

---

# 107. Interview Question — Is Fetch a JavaScript Language Feature?

Fetch is a Web API provided by the runtime environment rather than part of the ECMAScript language itself.

It is available in modern browsers and is also available in modern Node.js environments.

---

# 108. Interview Question — Are Frontend Environment Variables Secret?

Not if their values are shipped to the browser.

Anything included in client-side code should be treated as accessible to users.

---

# 109. Interview Question — Authentication vs CORS?

Authentication determines:

```text
Who is making the request?
```

CORS controls:

```text
Whether browser JavaScript
from another origin may access
the response.
```

They solve different problems.

---

# 110. Real-World Example — User Service

```javascript
const BASE_URL = "/api";

async function request(
    path,
    options = {}
) {
    const response = await fetch(
        `${BASE_URL}${path}`,
        options
    );

    if (!response.ok) {
        throw new Error(
            `HTTP ${response.status}`
        );
    }

    if (response.status === 204) {
        return null;
    }

    return response.json();
}

export function getUsers() {
    return request("/users");
}

export function getUser(id) {
    return request(
        `/users/${id}`
    );
}

export function createUser(user) {
    return request("/users", {
        method: "POST",

        headers: {
            "Content-Type":
                "application/json"
        },

        body:
            JSON.stringify(user)
    });
}

export function updateUser(
    id,
    updates
) {
    return request(
        `/users/${id}`,
        {
            method: "PATCH",

            headers: {
                "Content-Type":
                    "application/json"
            },

            body:
                JSON.stringify(
                    updates
                )
        }
    );
}

export function deleteUser(id) {
    return request(
        `/users/${id}`,
        {
            method: "DELETE"
        }
    );
}
```

Now application code can simply use:

```javascript
const users =
    await getUsers();
```

rather than rebuilding HTTP logic everywhere.

---

# 111. Request Flow in a Real Application

```text
React Component
      │
      ▼
User Service
      │
      ▼
API Client
      │
      ▼
fetch()
      │
      ▼
HTTP Request
      │
      ▼
API Gateway
      │
      ▼
Backend Service
      │
      ▼
Database / Other Service
      │
      ▼
HTTP Response
      │
      ▼
API Client
      │
      ▼
Service
      │
      ▼
React Component
      │
      ▼
UI Update
```

This separation becomes increasingly important as applications grow.

---

# 112. Fetch Cheat Sheet

```text
FETCH
================================

fetch(url)

Returns:

Promise<Response>


GET
================================

fetch("/api/users")


POST
================================

fetch("/api/users", {
    method: "POST",
    headers: {
        "Content-Type":
            "application/json"
    },
    body:
        JSON.stringify(data)
});


RESPONSE
================================

const response =
    await fetch(url);


JSON
================================

const data =
    await response.json();


HTTP ERROR CHECK
================================

if (!response.ok) {
    throw new Error(
        `HTTP ${response.status}`
    );
}


STATUS
================================

response.status


SUCCESS STATUS
================================

response.ok

true for 200–299


QUERY PARAMETERS
================================

new URLSearchParams(...)


AUTHORIZATION
================================

Authorization:
Bearer <token>


FORM DATA
================================

const form =
    new FormData();

form.append(...);


FILE UPLOAD
================================

body: formData

Do not normally set
Content-Type manually.


CREDENTIALS
================================

credentials:
    "include"


CORS
================================

Server controls
cross-origin permissions.


ABORT
================================

const controller =
    new AbortController();

fetch(url, {
    signal:
        controller.signal
});

controller.abort();


PARALLEL REQUESTS
================================

await Promise.all([
    getA(),
    getB()
]);


DEPENDENT REQUESTS
================================

const a =
    await getA();

const b =
    await getB(a.id);


204
================================

No response body.


SECURITY
================================

Never store private
server secrets in
frontend JavaScript.
```

---

# 113. Important Rules to Remember

```text
1. fetch() returns a Promise.

2. The Promise resolves to a Response object.

3. fetch() does not directly return parsed data.

4. response.json() returns a Promise.

5. response.text() returns a Promise.

6. response.blob() returns a Promise.

7. HTTP error statuses do not normally reject fetch().

8. Check response.ok for HTTP success/failure.

9. response.ok is true for statuses 200–299.

10. Network/request failures can reject fetch().

11. Network errors and HTTP errors are different.

12. GET is the default fetch method.

13. POST commonly sends/creates data.

14. PUT commonly replaces/updates a resource.

15. PATCH commonly performs partial updates.

16. DELETE commonly removes a resource.

17. API semantics are defined by the server contract.

18. JSON request bodies usually require JSON.stringify().

19. JSON requests commonly use Content-Type: application/json.

20. Accept describes preferred response formats.

21. Authorization headers commonly carry bearer tokens.

22. URLSearchParams helps encode query parameters.

23. Path parameters and query parameters serve different purposes.

24. Response bodies are normally consumed once.

25. response.clone() can create another response body stream.

26. FormData is useful for form and file uploads.

27. Do not normally manually set multipart Content-Type for FormData.

28. The browser adds the multipart boundary.

29. credentials controls credential handling.

30. Cross-origin credentialed requests require server support.

31. CORS is a browser security mechanism.

32. CORS permissions come from the server.

33. CORS is not authentication.

34. Authentication is not authorization.

35. Frontend code is not a trusted security boundary.

36. Frontend secrets are not secret once shipped to the browser.

37. Backend APIs must enforce authorization.

38. Independent requests can use Promise.all().

39. Dependent requests should execute in the required sequence.

40. Mixed sequential/concurrent request flows are common.

41. AbortController can cancel supported fetch requests.

42. Promise.race() does not cancel fetch by itself.

43. Timeouts can be implemented using AbortController.

44. Some environments provide AbortSignal.timeout().

45. Retry logic should not retry every failure blindly.

46. Exponential backoff spaces retries progressively.

47. Jitter helps avoid synchronized retries.

48. Retry safety depends on the operation.

49. State-changing operations may require idempotency protection.

50. 204 means No Content.

51. Do not blindly call response.json() on an empty response.

52. Parsing can fail independently of the HTTP request.

53. Error response bodies may contain useful application information.

54. Custom API errors can preserve status and response data.

55. Larger applications benefit from an API client layer.

56. API services keep HTTP logic out of UI components.

57. Reusable clients can centralize headers and error handling.

58. Request loading/error/success states matter in UI applications.

59. Async requests can create race conditions.

60. React request cleanup becomes important when components change or unmount.

61. Fetch is a Web API, not an ECMAScript language feature.

62. Modern Node.js environments also provide fetch().

63. Browser security restrictions still apply to frontend fetch requests.

64. Always understand the API contract instead of assuming HTTP behavior.

65. Fetch, Promises, async/await, and the Event Loop work together.
```

---

# Fetch API in One Sentence

> The Fetch API is a Promise-based Web API for making HTTP requests and receiving `Response` objects that can be inspected, validated, and parsed into application data.

---

# Final Mental Model

```text
              fetch(url, options)
                      │
                      ▼
               HTTP Request
                      │
                      ▼
                   Server
                      │
                      ▼
               HTTP Response
                      │
                      ▼
             Promise<Response>
                      │
                    await
                      │
                      ▼
                  Response
                 /        \
                /          \
               ▼            ▼
         response.ok     Body Method
               │            │
          ┌────┴────┐       ▼
          │         │   .json()
        true       false .text()
          │         │    .blob()
          ▼         ▼       │
       Success    Throw      ▼
          │       Error   Promise
          │         │       │
          │         │     await
          │         │       │
          └────┬────┘       ▼
               │           Data
               ▼
        Application Logic
```

For a typical JSON API:

```text
JavaScript Object
      │
      ▼
JSON.stringify()
      │
      ▼
Request Body
      │
      ▼
fetch()
      │
      ▼
Backend API
      │
      ▼
JSON Response
      │
      ▼
Response Object
      │
      ▼
response.ok?
   /       \
 YES       NO
  │         │
  ▼         ▼
.json()    Error
  │
  ▼
JavaScript Data
```

For application architecture:

```text
UI
│
▼
Service
│
▼
API Client
│
▼
Fetch
│
▼
HTTP
│
▼
Backend
```
