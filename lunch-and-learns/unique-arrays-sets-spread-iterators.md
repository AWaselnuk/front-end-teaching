How would I get a unique array using native JavaScript?

* [...new Set(array)] - show a demo with output
* So what is Set?
* So what is …?
* So what is an iterator

## Background

* I was doing some simple code exercises and I came across this “easy” problem. How do I take a list of items and dedup it?
* This kind of thing should be trivial: `array.uniq` or maybe `Array.uniq` but this is JavaScript so obviously we have none of that. Lodash includes a function that helps us `_.uniq` but I wanted a solution using only the standard library.
* Using modern, ES features, a great one-liner to accomplish the task is the following: `[…new Set(items)]`
* What is going on here?

## Set

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set
* Set objects are collections of values. You can iterate through the elements of a set in insertion order. A value in the Set may only occur once; it is unique in the Set's collection."
* You can add/remove items and check for them with `.add`, `.remove`, `.has`
* Set is `iterable` so you can loop through it with `.forEach` and you can apply destructuring to it like we did in our one-liner (more on that later)
* Finally, you can get the values out of the set, but here is where things go deeper: `set.values() // SetIterator`
  * A `SetIterator` is a native Javascript iterator object. This is the native primitive that drives a lot of new advanced functionality like `for..of` loops, generator functions, and `yield`.

## Iterators
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators
* An object is an iterator when it knows how to access items from a collection one at a time, while keeping track of its current position within that sequence. In JavaScript an iterator is an object that provides a `next()` method which returns the next item in the sequence. This method returns an object with two properties: `done` and `value`.
* These things can do A LOT so it's a topic for another lunch and learn. For now, let's see how it looks in the console...
* Let's look at the most interesting use of iterators briefly: Generator functions.
  * A GeneratorFunction is a special type of function that works as a factory for iterators. When it is executed it returns a new Generator object. A function becomes a GeneratorFunction if it uses the `function*` syntax.

```javascript
function* idMaker() {
  var index = 0;
  while(true)
    yield index++;
}

var gen = idMaker();

console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
```

* You can also pass values back into `next`

```javascript
function* fibonacci() {
  var fn1 = 0;
  var fn2 = 1;
  while (true) {
    var current = fn1;
    fn1 = fn2;
    fn2 = current + fn1;
    var reset = yield current;
    if (reset) {
        fn1 = 0;
        fn2 = 1;
    }
  }
}

var sequence = fibonacci();
console.log(sequence.next().value);     // 0
console.log(sequence.next().value);     // 1
console.log(sequence.next().value);     // 1
console.log(sequence.next().value);     // 2
console.log(sequence.next().value);     // 3
console.log(sequence.next().value);     // 5
console.log(sequence.next().value);     // 8
console.log(sequence.next(true).value); // 0
console.log(sequence.next().value);     // 1
console.log(sequence.next().value);     // 1
console.log(sequence.next().value);     // 2
```

* Generators and `yield` are the underpinning for some more advanced async code patterns. You may have heard of `async` and `await` for example. What you can effectively get is a two-way message passing system where `yield` pauses and waits for a value and `next` passes a value.
* We won't dive into advanced async today but if you just can't wait to learn more JavaScript then check out this very in depth exploration: https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md

## Spread operator

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator
* Spread syntax allows an iterable such as an array expression or string to be expanded in places where zero or more arguments (for function calls) or elements (for array literals) are expected, or an object expression to be expanded in places where zero or more key-value pairs (for object literals) are expected.
* This is an amazing and widely-used new feature. It allowed us to coerce a `Set` into a comma-separated list of it's values: `[...new Set(values)]`.
* The easiest way to grok it, is to look at some examples:

```javascript
function myFunction(x, y, z) { }
var args = [0, 1, 2];
myFunction(...args);

// You can add multiple spread operators at any point
function myFunction(v, w, x, y, z) { }
var args = [0, 1];
myFunction(-1, ...args, 2, ...[3]);

// Combine arrays with ease
var arr1 = ['two', 'three'];
var arr2 = ['one', ...arr1, 'four', 'five'];
// ["one", "two", "three", "four", "five"]

// Convert NodeList to array
[...document.querySelectorAll('div')]

// Clone and merge objects
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
// Object { foo: "bar", x: 42 }

var mergedObj = { ...obj1, ...obj2 };
// Object { foo: "baz", x: 42, y: 13 }
```
