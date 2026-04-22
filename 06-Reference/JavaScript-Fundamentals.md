---
title: JavaScript Fundamentals
tags:
  - reference
  - javascript
  - programming
  - scripting
  - web
  - fundamentals
  - foundational
created: 2026-03-26
updated: 2026-03-26
source: MDN Web Docs (developer.mozilla.org) — authoritative JavaScript reference
difficulty: foundational
---

# 🟨 JavaScript Fundamentals

## Overview
JavaScript (JS) is the programming language of the web. It runs in browsers (client-side) and on servers via Node.js. For ServiceNow practitioners, JavaScript is the scripting language for all platform customisation — understanding core JS first makes GlideScript (ServiceNow's JS dialect) much easier to learn.

**Two notes cover this topic:**
- This note — core JavaScript language
- [[ServiceNow-JavaScript]] — GlideScript and ServiceNow-specific patterns

**Authoritative reference:** MDN Web Docs — https://developer.mozilla.org/en-US/docs/Web/JavaScript

---

## Variables & Scope

```javascript
// var — function-scoped, hoisted (avoid in modern JS)
var name = "Alice";

// let — block-scoped, reassignable (use for variables that change)
let count = 0;
count = 1;  // OK

// const — block-scoped, cannot be reassigned (use by default)
const MAX = 100;
// MAX = 200;  // TypeError

// Block scope example
{
    let blockVar = "inside";
    const blockConst = "also inside";
}
// console.log(blockVar);  // ReferenceError — not accessible here

// Hoisting — var declarations are moved to top of function
console.log(x);  // undefined (not ReferenceError) — var is hoisted
var x = 5;
```

**Rule of thumb:** Use `const` by default. Use `let` when you need to reassign. Avoid `var`.

---

## Data Types

```javascript
// Primitive types
let str    = "hello";           // String
let num    = 42;                // Number (all numbers are 64-bit float)
let big    = 9007199254740993n; // BigInt (large integers)
let bool   = true;              // Boolean
let undef;                      // undefined (declared but not assigned)
let empty  = null;              // null (intentional absence of value)
let sym    = Symbol("id");      // Symbol (unique identifier)

// Reference types
let obj    = { name: "Alice" }; // Object
let arr    = [1, 2, 3];         // Array (special object)
let fn     = function() {};     // Function (also an object)

// Check type
typeof "hello"    // "string"
typeof 42         // "number"
typeof true       // "boolean"
typeof undefined  // "undefined"
typeof null       // "object"  ← known quirk of JS
typeof {}         // "object"
typeof []         // "object"  ← use Array.isArray() instead
typeof function(){} // "function"
Array.isArray([1,2,3])  // true
```

### Type Coercion & Truthy/Falsy

```javascript
// Falsy values — evaluate as false in boolean context
false, 0, -0, 0n, "", '', ``, null, undefined, NaN

// Everything else is truthy — including:
"0", [], {}, function(){}

// Loose equality (==) performs type coercion — avoid
0 == false    // true (coercion)
"" == false   // true (coercion)
null == undefined  // true

// Strict equality (===) — no coercion — always prefer
0 === false   // false
"1" === 1     // false
null === undefined  // false
```

---

## Strings

```javascript
const name = "Alice";
const greeting = `Hello, ${name}!`;  // Template literal (backtick) — preferred

// Common string methods
"hello".toUpperCase()          // "HELLO"
"HELLO".toLowerCase()          // "hello"
"  hello  ".trim()             // "hello"
"hello world".includes("world")  // true
"hello".startsWith("he")       // true
"hello".endsWith("lo")         // true
"hello".indexOf("l")           // 2 (first occurrence)
"hello".slice(1, 3)            // "el" (start, end exclusive)
"hello".replace("l", "r")      // "herlo" (first match only)
"hello".replaceAll("l", "r")   // "herro"
"a,b,c".split(",")             // ["a", "b", "c"]
["a","b","c"].join("-")        // "a-b-c"
"hello".length                 // 5 (property, not method)
"5".padStart(3, "0")           // "005"
```

---

## Numbers

```javascript
42 + 8      // 50
10 - 3      // 7
4 * 5       // 20
10 / 3      // 3.3333...
10 % 3      // 1 (remainder/modulo)
2 ** 10     // 1024 (exponentiation)

// Number methods
(3.14159).toFixed(2)       // "3.14" (returns string)
Number.isInteger(42)       // true
Number.isFinite(1/0)       // false
Number.isNaN(NaN)          // true
parseInt("42px")           // 42
parseFloat("3.14abc")      // 3.14
Number("42")               // 42
Number("abc")              // NaN

// Math object
Math.round(4.6)    // 5
Math.floor(4.9)    // 4
Math.ceil(4.1)     // 5
Math.abs(-7)       // 7
Math.max(1,2,3)    // 3
Math.min(1,2,3)    // 1
Math.pow(2,8)      // 256
Math.sqrt(16)      // 4
Math.random()      // 0 to <1 (random float)
Math.floor(Math.random() * 10)  // Random integer 0-9
```

---

## Arrays

```javascript
const arr = [1, 2, 3, 4, 5];

// Access and modify
arr[0]          // 1 (zero-indexed)
arr.length      // 5
arr[arr.length - 1]  // 5 (last element)

// Add / remove
arr.push(6)        // Add to end → [1,2,3,4,5,6]
arr.pop()          // Remove from end → returns 6
arr.unshift(0)     // Add to start → [0,1,2,3,4,5]
arr.shift()        // Remove from start → returns 0
arr.splice(2, 1)   // Remove 1 element at index 2
arr.splice(2, 0, 99)  // Insert 99 at index 2 (remove 0)

// Non-mutating
arr.slice(1, 3)          // [2, 3] (start, end exclusive — original unchanged)
[...arr, 6]              // Spread to new array with extra element
arr.concat([6, 7])       // [1,2,3,4,5,6,7] (new array)
arr.includes(3)          // true
arr.indexOf(3)           // 2
arr.join(", ")           // "1, 2, 3, 4, 5"
arr.reverse()            // Reverses IN PLACE — mutates original
[...arr].reverse()       // Reverse without mutating (spread first)
arr.sort()               // Sorts IN PLACE (lexicographic by default)
arr.sort((a, b) => a - b)  // Sort numbers ascending
arr.flat()               // Flatten one level: [[1,2],[3]] → [1,2,3]
arr.flat(Infinity)       // Flatten all levels
```

### Array Iteration Methods

```javascript
const nums = [1, 2, 3, 4, 5];

// forEach — execute function for each element (no return value)
nums.forEach(n => console.log(n));

// map — transform each element, return new array
const doubled = nums.map(n => n * 2);          // [2, 4, 6, 8, 10]
const strings = nums.map(n => `Item ${n}`);    // ["Item 1", ...]

// filter — keep elements where function returns true
const evens = nums.filter(n => n % 2 === 0);   // [2, 4]

// find — return first matching element (or undefined)
const first = nums.find(n => n > 3);           // 4

// findIndex — return index of first match (or -1)
const idx = nums.findIndex(n => n > 3);        // 3

// some — true if at least one element passes
nums.some(n => n > 4);    // true

// every — true if all elements pass
nums.every(n => n > 0);   // true

// reduce — accumulate to single value
const sum = nums.reduce((acc, n) => acc + n, 0);  // 15
const max = nums.reduce((acc, n) => n > acc ? n : acc, -Infinity);  // 5

// flat + map combined
const nested = [[1,2],[3,4]];
nested.flatMap(arr => arr.map(n => n * 2));  // [2, 4, 6, 8]
```

---

## Objects

```javascript
// Object literal
const user = {
    name: "Alice",
    age: 30,
    active: true,
    address: {
        city: "New York",
        zip: "10001"
    },
    greet() {                          // Method shorthand
        return `Hello, ${this.name}`;
    }
};

// Access
user.name           // "Alice" (dot notation)
user["name"]        // "Alice" (bracket notation — useful for dynamic keys)
user.address.city   // "New York" (nested)

// Optional chaining — safe access without errors on null/undefined
user?.address?.city         // "New York" (no error if address is undefined)
user?.phone?.number         // undefined (no ReferenceError)

// Nullish coalescing — default when null or undefined
user.phone ?? "No phone"    // "No phone"
user.name ?? "Unknown"      // "Alice"

// Modify
user.email = "alice@example.com";  // Add property
user.age = 31;                     // Modify property
delete user.active;                // Remove property

// Check
"name" in user         // true
user.hasOwnProperty("name")  // true

// Destructuring
const { name, age } = user;         // Extract properties
const { name: fullName } = user;    // Rename on extraction
const { name, ...rest } = user;     // Rest — rest contains everything else

// Spread
const updated = { ...user, age: 31 };   // New object with override
const merged = { ...obj1, ...obj2 };    // Merge objects

// Object methods
Object.keys(user)     // ["name", "age", "address", "greet"]
Object.values(user)   // [values...]
Object.entries(user)  // [["name", "Alice"], ["age", 30], ...]
Object.assign({}, user, { age: 31 })  // Shallow merge (prefer spread)
Object.freeze(user)   // Make immutable
```

---

## Functions

```javascript
// Function declaration (hoisted — can call before definition)
function add(a, b) {
    return a + b;
}

// Function expression (not hoisted)
const subtract = function(a, b) {
    return a - b;
};

// Arrow function — concise, no own `this`
const multiply = (a, b) => a * b;       // Implicit return
const square = n => n * n;              // Single param — no parens needed
const greet = () => "Hello!";           // No params

// Arrow with block body — explicit return required
const divide = (a, b) => {
    if (b === 0) throw new Error("Division by zero");
    return a / b;
};

// Default parameters
function greetUser(name = "Guest") {
    return `Hello, ${name}!`;
}

// Rest parameters — collect remaining args as array
function sum(...numbers) {
    return numbers.reduce((acc, n) => acc + n, 0);
}
sum(1, 2, 3, 4, 5);  // 15

// Spread in function call
const nums = [1, 2, 3];
Math.max(...nums);    // 3

// this — context
// Arrow functions inherit `this` from surrounding scope
// Regular functions have their own `this` (depends on how called)
const obj = {
    value: 42,
    regularMethod: function() { return this.value; },  // this = obj
    arrowMethod: () => { return this; }                // this = outer scope (not obj)
};
```

### Closures

```javascript
// A closure is a function that remembers its outer scope
function makeCounter() {
    let count = 0;              // count lives in makeCounter's scope
    return function() {
        count++;
        return count;
    };
}

const counter = makeCounter();
counter();  // 1
counter();  // 2
counter();  // 3 — count persists between calls
```

---

## Control Flow

```javascript
// if / else if / else
if (score >= 90) {
    grade = "A";
} else if (score >= 80) {
    grade = "B";
} else {
    grade = "F";
}

// Ternary
const result = score >= 60 ? "Pass" : "Fail";

// switch
switch (day) {
    case "Monday":
    case "Tuesday":
        console.log("Weekday");
        break;
    case "Saturday":
    case "Sunday":
        console.log("Weekend");
        break;
    default:
        console.log("Unknown");
}

// Nullish coalescing assignment
user.name ??= "Default Name";   // Assign only if null or undefined

// Logical assignment
user.name ||= "Default";        // Assign if falsy
user.count &&= user.count + 1;  // Assign if truthy
```

---

## Loops

```javascript
const arr = [10, 20, 30];
const obj = { a: 1, b: 2, c: 3 };

// for (classic — use when you need index)
for (let i = 0; i < arr.length; i++) {
    console.log(i, arr[i]);
}

// for...of — iterate over iterable values (arrays, strings, Maps, Sets)
for (const value of arr) {
    console.log(value);  // 10, 20, 30
}

// for...of with index (using entries)
for (const [index, value] of arr.entries()) {
    console.log(index, value);  // 0 10, 1 20, 2 30
}

// for...in — iterate over object keys (avoid on arrays)
for (const key in obj) {
    console.log(key, obj[key]);  // a 1, b 2, c 3
}

// while
let i = 0;
while (i < 5) {
    console.log(i);
    i++;
}

// do...while (runs at least once)
let j = 0;
do {
    console.log(j);
    j++;
} while (j < 3);

// break and continue
for (const n of [1,2,3,4,5]) {
    if (n === 3) continue;  // Skip 3
    if (n === 5) break;     // Stop at 5
    console.log(n);         // 1, 2, 4
}
```

---

## Error Handling

```javascript
// try / catch / finally
try {
    const data = JSON.parse(invalidJson);
    riskyOperation();
} catch (error) {
    console.error("Error:", error.message);
    console.error("Stack:", error.stack);
} finally {
    // Always runs — cleanup here
    closeConnection();
}

// Throw custom errors
function divide(a, b) {
    if (b === 0) {
        throw new Error("Cannot divide by zero");
    }
    return a / b;
}

// Custom error classes
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = "ValidationError";
        this.field = field;
    }
}

try {
    throw new ValidationError("Required field", "email");
} catch (e) {
    if (e instanceof ValidationError) {
        console.log(`Validation failed on: ${e.field}`);
    }
}

// Error types
// Error, TypeError, ReferenceError, SyntaxError, RangeError
```

---

## Classes & Prototypes

```javascript
class Animal {
    #name;  // Private field (ES2022)

    constructor(name, sound) {
        this.#name = name;
        this.sound = sound;
    }

    speak() {
        return `${this.#name} says ${this.sound}`;
    }

    get name() { return this.#name; }           // Getter
    set name(val) { this.#name = val; }         // Setter

    static create(name, sound) {                // Static method
        return new Animal(name, sound);
    }
}

class Dog extends Animal {
    constructor(name) {
        super(name, "woof");   // Call parent constructor
        this.tricks = [];
    }

    learn(trick) {
        this.tricks.push(trick);
    }

    speak() {
        return super.speak() + "!";  // Call parent method
    }
}

const dog = new Dog("Rex");
dog.speak();   // "Rex says woof!"
dog instanceof Dog     // true
dog instanceof Animal  // true
```

---

## Asynchronous JavaScript

### Promises

```javascript
// Create a promise
const fetchData = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = true;
        if (success) {
            resolve({ data: "result" });
        } else {
            reject(new Error("Fetch failed"));
        }
    }, 1000);
});

// Consume a promise
fetchData
    .then(result => {
        console.log(result.data);   // "result"
        return processData(result); // Chain
    })
    .then(processed => console.log(processed))
    .catch(error => console.error(error.message))
    .finally(() => console.log("Done"));

// Promise combinators
Promise.all([p1, p2, p3])         // All resolve, or first rejection
Promise.allSettled([p1, p2, p3])  // Wait for all, regardless of outcome
Promise.race([p1, p2, p3])        // First to settle (resolve or reject)
Promise.any([p1, p2, p3])         // First to resolve
```

### async / await

```javascript
// async function always returns a Promise
async function loadUser(id) {
    try {
        const response = await fetch(`/api/users/${id}`);  // Pause until resolved
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
        }
        const user = await response.json();
        return user;
    } catch (error) {
        console.error("Failed to load user:", error.message);
        throw error;  // Re-throw if caller needs to handle it
    }
}

// Parallel execution — don't await sequentially if independent
async function loadMultiple() {
    // Sequential (slow — waits for each)
    const user1 = await loadUser(1);
    const user2 = await loadUser(2);

    // Parallel (fast — both start simultaneously)
    const [userA, userB] = await Promise.all([loadUser(1), loadUser(2)]);
}
```

---

## Modules (ES6)

```javascript
// math.js — named exports
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export function subtract(a, b) { return a - b; }

// utils.js — default export
export default function formatDate(date) {
    return date.toISOString().split("T")[0];
}

// main.js — import
import formatDate from "./utils.js";              // Default import
import { add, subtract } from "./math.js";        // Named imports
import { add as sum } from "./math.js";           // Rename on import
import * as Math from "./math.js";                // Import all as namespace
import formatDate, { add } from "./combined.js";  // Default + named
```

---

## JSON

```javascript
// Parse JSON string to JS object
const jsonString = '{"name":"Alice","age":30}';
const obj = JSON.parse(jsonString);
obj.name  // "Alice"

// Stringify JS object to JSON
const user = { name: "Alice", age: 30 };
const json = JSON.stringify(user);           // '{"name":"Alice","age":30}'
const pretty = JSON.stringify(user, null, 2);  // Indented (readable)

// Deep clone via JSON (simple objects only — loses functions, Dates, undefined)
const clone = JSON.parse(JSON.stringify(obj));

// JSON.parse with error handling (always wrap in try/catch)
try {
    const data = JSON.parse(possiblyBadInput);
} catch (e) {
    console.error("Invalid JSON:", e.message);
}
```

---

## Useful Patterns

```javascript
// Guard clause — reduce nesting
function processUser(user) {
    if (!user) return null;
    if (!user.active) return null;
    // Main logic here — no deep nesting
    return user.name.toUpperCase();
}

// IIFE — Immediately Invoked Function Expression
(function() {
    const private = "not accessible outside";
    console.log(private);
})();

// Memoization — cache expensive function results
function memoize(fn) {
    const cache = new Map();
    return function(...args) {
        const key = JSON.stringify(args);
        if (cache.has(key)) return cache.get(key);
        const result = fn(...args);
        cache.set(key, result);
        return result;
    };
}

// Debounce — delay execution until after last call
function debounce(fn, delay) {
    let timeout;
    return function(...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => fn(...args), delay);
    };
}
```

---

## Quick Reference — Common Gotchas

| Situation | Correct Approach |
|---|---|
| Comparing values | Always use `===` not `==` |
| Checking if array | `Array.isArray(x)` not `typeof x === "object"` |
| Checking if null | `x === null` not `x == null` (unless also catching undefined) |
| Copying array | `[...arr]` or `arr.slice()` — not `arr` (that's a reference) |
| Copying object | `{...obj}` — note: shallow copy only |
| Sorting numbers | `arr.sort((a,b) => a - b)` — not `arr.sort()` (lexicographic) |
| Async in forEach | Use `for...of` with `await` — `forEach` doesn't handle async |
| `this` in callbacks | Use arrow function or `.bind(this)` |

---

## Related Notes
- ServiceNow-JavaScript
- ServiceNow-Flow-Designer
- Python-for-Security
- Bash-Scripting

## References
- MDN Web Docs — JavaScript: https://developer.mozilla.org/en-US/docs/Web/JavaScript
- MDN — JavaScript Reference: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference
- ECMAScript Specification (ECMA-262): https://tc39.es/ecma262/

---
<- [[Reference-MOC]]