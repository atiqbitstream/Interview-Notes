# JavaScript

## Overview
JavaScript is a high-level, interpreted programming language primarily used for web development (both client and server-side with Node.js).

## Key Features
- **Interpreted**: Runs in browsers and Node.js
- **Dynamically Typed**: Types determined at runtime
- **Prototype-based**: Object-oriented through prototypes
- **First-class Functions**: Functions are objects
- **Event-driven**: Asynchronous programming with callbacks, promises, async/await

## Data Types

### Primitives
```javascript
// Number
let integer = 42;
let float = 3.14;

// String
let text = "Hello";
let template = `Hello, ${name}!`;

// Boolean
let isTrue = true;

// Undefined & Null
let notDefined;  // undefined
let empty = null;

// Symbol (ES6)
let sym = Symbol('description');

// BigInt (ES2020)
let bigNum = 1234567890123456789012345678901234567890n;
```

### Objects and Arrays
```javascript
// Object
const person = {
    name: "John",
    age: 30,
    greet() {
        return `Hello, I'm ${this.name}`;
    }
};

// Array
const numbers = [1, 2, 3, 4, 5];
const mixed = [1, "two", true, null];

// Array methods
numbers.map(x => x * 2);        // [2, 4, 6, 8, 10]
numbers.filter(x => x % 2 === 0); // [2, 4]
numbers.reduce((sum, x) => sum + x, 0); // 15
```

## Functions

### Function Declaration
```javascript
function add(a, b) {
    return a + b;
}
```

### Function Expression
```javascript
const multiply = function(a, b) {
    return a * b;
};
```

### Arrow Functions (ES6)
```javascript
const square = x => x * x;
const add = (a, b) => a + b;
const greet = name => {
    console.log(`Hello, ${name}`);
};
```

### Rest and Spread
```javascript
// Rest parameters
function sum(...numbers) {
    return numbers.reduce((a, b) => a + b, 0);
}

// Spread operator
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5, 6]; // [1, 2, 3, 4, 5, 6]

const obj1 = {a: 1, b: 2};
const obj2 = {...obj1, c: 3}; // {a: 1, b: 2, c: 3}
```

## Destructuring (ES6)
```javascript
// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];

// Object destructuring
const {name, age} = {name: "John", age: 30};

// With defaults
const {x = 0, y = 0} = {x: 5};

// Function parameters
function greet({name, age}) {
    console.log(`${name} is ${age} years old`);
}
```

## Asynchronous JavaScript

### Callbacks
```javascript
function fetchData(callback) {
    setTimeout(() => {
        callback('Data loaded');
    }, 1000);
}

fetchData(data => console.log(data));
```

### Promises
```javascript
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('Success!');
        // or reject('Error!');
    }, 1000);
});

promise
    .then(result => console.log(result))
    .catch(error => console.error(error))
    .finally(() => console.log('Done'));

// Promise chaining
fetch('/api/user')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
```

### Async/Await (ES2017)
```javascript
async function fetchUser() {
    try {
        const response = await fetch('/api/user');
        const data = await response.json();
        return data;
    } catch (error) {
        console.error(error);
    }
}

// Multiple async operations
async function fetchMultiple() {
    const [users, posts] = await Promise.all([
        fetch('/api/users').then(r => r.json()),
        fetch('/api/posts').then(r => r.json())
    ]);
}
```

## Classes (ES6)
```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    static species() {
        return 'Homo sapiens';
    }
}

class Student extends Person {
    constructor(name, age, studentId) {
        super(name, age);
        this.studentId = studentId;
    }
    
    study() {
        return `${this.name} is studying`;
    }
}
```

## Closures
```javascript
function counter() {
    let count = 0;
    return {
        increment: () => ++count,
        decrement: () => --count,
        getCount: () => count
    };
}

const myCounter = counter();
myCounter.increment(); // 1
myCounter.increment(); // 2
```

## Prototypes
```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

const john = new Person('John');
john.greet(); // "Hello, I'm John"
```

## this Keyword
```javascript
// Global context
console.log(this); // window (browser) or global (Node)

// Object method
const obj = {
    name: 'John',
    greet: function() {
        console.log(this.name); // 'John'
    }
};

// Arrow functions don't bind this
const obj2 = {
    name: 'John',
    greet: () => {
        console.log(this.name); // undefined (this from outer scope)
    }
};

// Explicit binding
function greet() {
    console.log(this.name);
}
const person = {name: 'John'};
greet.call(person);   // 'John'
greet.apply(person);  // 'John'
const boundGreet = greet.bind(person);
boundGreet();         // 'John'
```

## ES6+ Features

### let and const
```javascript
let variable = 1;    // Block-scoped, mutable
const constant = 2;  // Block-scoped, immutable binding

// var is function-scoped (avoid using)
```

### Template Literals
```javascript
const name = 'World';
const greeting = `Hello, ${name}!`;
const multiline = `
    Line 1
    Line 2
`;
```

### Default Parameters
```javascript
function greet(name = 'Guest') {
    return `Hello, ${name}!`;
}
```

### Object Property Shorthand
```javascript
const name = 'John';
const age = 30;

// Shorthand
const person = {name, age};
// Equivalent to: {name: name, age: age}
```

### Optional Chaining (ES2020)
```javascript
const user = {
    name: 'John',
    address: {
        city: 'NYC'
    }
};

const city = user?.address?.city; // 'NYC'
const zip = user?.address?.zip;   // undefined (no error)
```

### Nullish Coalescing (ES2020)
```javascript
const value = null ?? 'default';  // 'default'
const value2 = 0 ?? 'default';    // 0 (not 'default')
```

## Common Array Methods
```javascript
const arr = [1, 2, 3, 4, 5];

// Iterating
arr.forEach(x => console.log(x));

// Transforming
arr.map(x => x * 2);              // [2, 4, 6, 8, 10]

// Filtering
arr.filter(x => x % 2 === 0);     // [2, 4]

// Reducing
arr.reduce((sum, x) => sum + x, 0); // 15

// Finding
arr.find(x => x > 3);             // 4
arr.findIndex(x => x > 3);        // 3
arr.includes(3);                  // true

// Checking
arr.some(x => x > 4);             // true
arr.every(x => x > 0);            // true

// Sorting
arr.sort((a, b) => b - a);        // [5, 4, 3, 2, 1]
```

## Event Loop
JavaScript is single-threaded with an event loop:

```
Call Stack → Web APIs → Callback Queue → Event Loop
```

```javascript
console.log('1');

setTimeout(() => {
    console.log('2');
}, 0);

Promise.resolve().then(() => {
    console.log('3');
});

console.log('4');

// Output: 1, 4, 3, 2
// Microtasks (Promises) run before macrotasks (setTimeout)
```

## Common Patterns

### Module Pattern
```javascript
const module = (function() {
    let privateVar = 'private';
    
    return {
        publicMethod() {
            return privateVar;
        }
    };
})();
```

### Singleton Pattern
```javascript
const Singleton = (function() {
    let instance;
    
    function createInstance() {
        return {data: 'Singleton'};
    }
    
    return {
        getInstance() {
            if (!instance) {
                instance = createInstance();
            }
            return instance;
        }
    };
})();
```

## Best Practices

1. **Use const/let**, not var
2. **Use arrow functions** for callbacks
3. **Use async/await** instead of callbacks
4. **Use strict mode**: `'use strict';`
5. **Avoid global variables**
6. **Use === instead of ==**
7. **Handle errors** with try/catch
8. **Use meaningful variable names**
9. **Keep functions small**
10. **Use modern ES6+ features**

## Common Interview Questions

- Explain closures
- What's the difference between var, let, and const?
- What are promises?
- Explain async/await
- What is the event loop?
- What's the difference between == and ===?
- Explain prototypal inheritance
- What is 'this' keyword?
- What are arrow functions?
- Explain hoisting
