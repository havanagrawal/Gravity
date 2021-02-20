---
layout: post
title:  "The TypeScript CheatSheet"
date:   2021-02-20 11:13:00 -0700
category: ["learning"]
---

A short personal summary of all things important or notable in TypeScript

Caveat: I am a strong believer in creating _personal_ cheat sheets and reference notes. The following sheet is by no means a comprehensive one; it simply encompasses all areas _I_ might want to refer to later. Having said that, I hope you find something useful in here too :)

[TOC]

## Variable Declarations

There are three ways you can declare a variable in TS: `var`, `let` and `const`.

### `var`

* is **global-scoped** or **function-scoped**.
* is _hoisted_ to the top of its scope, and initialized to `undefined`.
    ```typescript
    // The following code is valid because
    // the "var x" declaration will be hoisted to the top
    x = 10
    var x;
    ```
    ```typescript
    console.log(x); // this will print undefined
    var x = 100;
    ```
    The above snippet is equivalent to
    ```typescript
    var x = undefined; // hoisted
    console.log(x); // x is undefined at this point
    x = 100;
    ```
* can be redeclared
    ```typescript
    var x = 10;
    var x = 100;
    ```

### `let`

* is **block-scoped** (between curly braces `{` and `}`)
* is _hoisted_ to the top of its scope, but uninitialized. Usage will result in a [`ReferenceError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError/ReferenceError)
* cannot be redeclared in the same scope, may be redeclared in a more nested scope

### `const`

* `const` is **block-scoped** (between curly braces `{` and `}`)
* _hoisted_ to the top of its scope, but uninitialized. Usage will result in a [`ReferenceError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError/ReferenceError)
* MUST be initialized at declaration time.
* cannot be redeclared in the same scope, may be redeclared in a more nested scope

## Types

Reference: [The TypeScript Handbook: Basic Types](https://www.typescriptlang.org/docs/handbook/basic-types.html)

```typescript
// Basic Types
const num: number = 42;
const str: string = "life, the universe and everything"
const b: boolean = false;

// Arrays:
const list: number[] = [1, 2, 3];
const otherList: Array<number> = [1, 2, 3];

// Tuples
const day: [string, number] = ['Monday', 1];
console.log(day[0]) // 'Monday'
console.log(day[2]) // error

// Enums
enum Beverage {
    TEA,    // numeric value is 0
    COFFEE  // numeric value is 1
}

// Enums with explicit values
enum PowerLevel {
    WEAK = -100,
    NORMAL = 100,
    STRONG = 5001
}

// void is no type at all
function setX(x: number): void {}

// any is literally any type at all, skips type checking
const x: any = 10;
// can access arbitrary properties
x.hello();

// unknown is similar to any, but performs type checks via type narrowing
const y: unknown = 1000;
if (typeof y === number) {
    // okay, y must be number, y/10 allowed
    console.log(y/10);

    // not allowed, y cannot be string
    // y.substring(5);
}

// null and undefined literally have their type as null and undefined
const n: null = null;
const ud: undefined = undefined;

// By default null and undefined are subtypes of all other types
const nullNumber: number = null;

// object is a non-primitive type
const o: object = {'name': 'Douglas'};
```

## Jargon

### Temporal Dead Zone

All the points between a `let`/`const`'s hoisted declaration and their usage. Consider

```typescript
console.log("Hello");

function noop() {
    console.log("I don't do anything");
}

let x = 10;
```

After hoisting, this looks like:

```typescript
let x;
// temporal dead zone of x starts
console.log("Hello");

function noop() {
    console.log("I don't do anything");
}
// temporal dead zone of x ends
x = 10;
```

Usage of this `let`/`const` will result in an error

### IIFE: Immediately Invoked Function Expression

Typically used to capture the current value of a variable that may change when the inner function is actually invoked.

```typescript
// This will actually print "10" 10 times
// since the moment at which the anonymous function passed to setTimeout is invoked
// i is already 10
for (var i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i);
  }, 100 * i);
}

// Solution:
for (var i = 0; i < 10; i++) {
  // capture the current state of 'i'
  // by invoking a function with its current value
  (function (i) {
    setTimeout(function () {
      console.log(i);
    }, 100 * i);
  })(i);
}
```

Reference: [The TypeScript Handbook: Variable Capturing Quirks](https://www.typescriptlang.org/docs/handbook/variable-declarations.html#variable-capturing-quirks)