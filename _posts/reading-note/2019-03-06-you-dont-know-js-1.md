---
layout: post
title: "Notes on 'You Don't Know JS (Up & Going)'"
categories: [tech]
tags: [reading-note]
excerpt: "Notes on 'You Don't Know JS (Up & Going)'. Kyle Simpson's book is a must-read for frontend developers with over a year of experience. It provides a deep understanding of JavaScript's complex concepts, offering clarity and a renewed perspective on the language."
---

# Notes on 'You Don't Know JS (Up & Going)'

> Kyle Simpson's book *You Don't Know JavaScript* is an invaluable resource, offering clear and in-depth explanations of many of JavaScript's complex and confusing concepts. After reading, you'll have a much clearer understanding of the language and a rebuilt perspective on JS. It's highly recommended for frontend developers with over a year of experience.

## Scope

### JavaScript is a Compiled Language
- Tokenizing/Lexing
- Parsing: generates the Abstract Syntax Tree (AST)
- Code generation

### Declarations and Assignments
- Variable assignment operation
    1. LHS: Declare the variable
    2. Assignment
- LHS: Finds the target for the operation, such as `b` in `var b = a`. In non-strict mode, if the variable is not found, it will be created. In strict mode, it will throw a ReferenceError.
- RHS: Finds the value of the target, such as `a` in `var b = a`. If the target is not found, it will throw a ReferenceError.

### Lexical Scope
- JavaScript uses *lexical scope* (as opposed to *dynamic scope* like bash scripts)
- `eval` and `with` can manipulate lexical scope, making optimizations difficult and leading to performance issues.
- Scope nesting:
    - Global scope
    - Function scope
        - Anonymous vs. Named (Anonymous functions are harder to debug, can't be self-invoked, and have lower readability)
        - Immediately Invoked Function Expression (IIFE)
    - Block scope
        - `try...catch` is also block scope (babel transpiles to ES3/5)

### Hoisting
- Declarations are made during the compilation phase, and assignments occur during the execution phase, leading to variable hoisting.
- Function declarations are hoisted before variable declarations.
- Multiple `var` declarations or function declarations with the same name will ignore the previous ones.

### Closures
- A closure is when an inner function is called outside of its *lexical scope*.
- Common uses of closures:
    - `setTimeout`, `setInterval`
    - Event listeners
    - Ajax
    - `postMessage`
    - Web workers
    - Any asynchronous or synchronous methods that use callback functions

## `this`

### Misconceptions about `this`
- `this` refers to itself
- `this` refers to the function's scope

### What is `this`?
- `this` refers to the caller of the function (in ascending order of priority):
    - Default binding (to the global object)
    - Implicit binding (to the parent object)
    - Explicit binding (`call`, `apply`, `forEach`, etc.)
    - `new` binding:
        - Creates a new object
        - Links the new object to the prototype
        - Binds the new object to `this` in the function call
        - If the function doesn't return anything else, it returns the new object
- If `call` is passed a primitive value (number, string, boolean), it will be converted to its object form (e.g., `new Number(...)`).

## Objects

### Built-in Objects
- String
- Number
- Boolean
- Object
- Symbol
- Function
- Array
- Date
- RegExp
- Error

### Object Concepts
- Strings, numbers, booleans, etc., are automatically converted to objects when necessary, e.g., `42.1.toFixed(0)`.
- Why does `typeof null === 'object'`?
    - In JavaScript, if the first three bits of a binary value are all `0`, it is classified as an object. Since `null` is all `0`s, it's a bug in the language itself.
- Property names are always strings. Non-string properties are automatically converted to strings.
    - However, in ES6, `Map` allows non-string property names. The book suggests they are also converted to strings, but this is questionable.
- Arrays can also have properties and methods, but their `length` does not change.
- Cloning an object:
    - JSON-safe object: `JSON.parse(JSON.stringify(obj))`
    - `Object.assign({}, obj)`

- Property descriptors:
    - `writable`: Whether the property can be modified.
    - `configurable`: Whether the property's descriptor can be changed (irreversible if set to `false`).
    - `enumerable`: Whether the property is enumerable.
- How to make an object immutable?
    - `writable: false`, `configurable: false`
    - `Object.preventExtensions()`: Prevents adding new properties.
    - `Object.seal()`: Seals the object, equivalent to `Object.preventExtensions()` + all properties `configurable: false`.
    - `Object.freeze()`: Freezes the object, equivalent to `Object.seal()` + all properties `writable: false`.
- How to distinguish between `undefined` and a non-existent property?
    - `'a' in obj`
    - `obj.hasOwnProperty()`
- How to check if a property is enumerable?
    - `'a' in obj`
    - `obj.propertyIsEnumerable()`

## Mixing Objects and "Classes"

- JavaScript does not have real classes. The relationship between a class and an instance is abstract and concrete, while JavaScript's "class" implementation is based on the prototype chain. There is no inheritance or instantiation, only association.

## Prototypes
- All normal prototype chains eventually point to the built-in `Object.prototype`.
- JavaScript has objects, not classes, making it a truly "object-oriented" language.
- One of the most abused and misunderstood behaviors in JavaScript is the attempt to emulate classes.

- Modify an object's prototype: `Object.setPrototypeOf(obj, pObj)`
- Check a prototype: `obj.prototype.isPrototypeOf(x)`
- Get a prototype: `Object.getPrototypeOf(obj)`

## Behavior Delegation

- Delegation is simpler than "class."
- The `Object.create()` syntax is better than `class`.
- The `class` syntax in ES6 is just syntactic sugar, still based on prototypes.

## Errata
### p57 ES6 Module Syntax
First mistake:
```
bar.js
    function hello(who){
        return "Let me introduce:" + who;
    }    
    export hello;

foo.js
    // Import hello() from the bar module
    import hello from "bar";
    // Other code
```
This syntax is incorrect. When using `import` directly, the default export of the target module is imported. The browser will throw an error: *Uncaught SyntaxError: The requested module 'xx.js' does not provide an export named 'default'*.

Use object destructuring syntax `import {hello} from "bar";` if bar.js uses `export {hello};`, or `export function hello...`.

Three correct syntaxes:
```js
bar.js
    function hello(who){
        return "Let me introduce:" + who;
    }    
    export {hello};

foo.js
    import {hello} from "bar";
```
```js
bar.js
    export function hello(who){
        return "Let me introduce:" + who;
    }
    
foo.js
    import {hello} from "bar";
```
```js
bar.js
    export default function hello(who){
        return "Let me introduce:" + who;
    }
    
foo.js
    import hello from "bar";
```
Second mistake:
```js
module foo from "foo";
```
The `module` keyword does not actually exist.

### p80 `this`
```js
function foo() {
  var a = 2;
  this.bar();
}
function bar() {
  console.log(this.a);
}
foo(); // ReferenceError: a is not defined
```
In fact, it will not throw an error but will output *undefined*.

### p106 Object Property Names

The book says that object property names are always strings, but in ES6, `Map` allows non-string property names. The book suggests they are also converted to strings, but this is questionable.
