# FED Workshop: Prototypes and Classes

**Length:** 2 hours
**Requirements:**

* You understand basic JavaScript syntax and concepts, for example, assigning variables or declaring functions.

## Topic

In this workshop we will discuss prototypes and how the prototype chain works to let us create classical object hierarchy.
We will also delve into the new `class` keyword syntax available in ES2015.

## Workshop Content

### What is a prototype?

* In JavaScript, objects have a special attribute called a prototype. The prototype is a pointer to another object.
* If you try to look up a property on an object, and it does not exist, JS will look for that property in the prototype.
  If it can't find the property on the prototype, it will look at the prototype's prototype.
  It will walk up this 'prototype chain' until it finds `null`.
* You can look up an object's prototype using `Object.getPrototypeOf`.
* You can set an object's prototype using `Object.create(prototypeObject)`

```javascript
const defaultShape = Object.create(null);
defaultShape.x = 0;
defaultShape.y = 0;
defaultShape.context = '2d';

const rectangle = Object.create(defaultShape);
rectangle.sides = 4;
rectangle.width = 20;
rectangle.height = 10;

const square = Object.create(rectangle);
square.width = 10;
square.height = 10;

console.log(square.sides)
// 4
// (square? -> rectangle!)

console.log(square.x) 
// 0
// (square? -> rectangle? -> defaultShape!)

console.log(square.color)
// undefined
// (square? -> rectangle? -> defaultShape? -> null!)
```

* Prototypes are a great way to efficiently share behavious or attributes.
* You can also create inheritance for 'is a' relationships.
* You can set an object's prototype using `Object.setPrototypeOf(someObject, prototypeObject)`.
  Switching prototypes is a very slow operation. You would almost always just set prototypes at creation time only.

```javascript
const newShape = Object.create(null);
console.log(newShape.x) 
// undefined

Object.setPrototypeOf(newShape, defaultShape)
console.log(newShape.x)
// 0
```

### Avoiding prototype lookup with hasOwnProperty

* There are some cases where you want to do something with the properties in your object, without going up the prototype chain.
  A common example is when you need to iterate over every property in your object.

```javascript
// const defaultShape = Object.create(Object.prototype)

for (prop in square) {
  console.log(square[prop]);
}
// 10
// 10
// 4
// 0
// 0
// "2d"

for (prop in square) {
  if (square.hasOwnProperty(prop)) {
    console.log(square[prop]);
  }
}
// 10
// 10
```

### Object literal syntax 

* Object literal syntax points the object's prototype to `Object.prototype`. That is what provides us with the standard functions.

```javascript
const thing = {x: 1, y: 1}

const thing = Object.create(Object.prototype)
thing.x = 1;
thing.y = 1;
```

### Constructor functions

* A constructor function is a function we use to instantiate an object.
  A constructor function will usually apply some initial data or state to the object.
* In addition, the constructor function has the job of setting the prototype. This is done using the `new` keyword.

```javascript
// Constructor function which sets state AND gives you a prototype object to work with
const Shape = function(x, y) {
  this.x = x;
  this.y = y;
}

Shape.prototype.toString = function() {
  return `Shape is located at (${this.x}, ${this.y}).`;
}

const shape = new Shape(10, 20);
console.log(shape.toString());
// "Shape is located at (10, 20)."
```

* WARNING: the `new` keyword is kind of magical. This example won't work without it.

```javascript
const Shape = function(x, y) {
  // If I call this function without 'new', then 'this' will be the global context (which is 'window' in a web browser).
  this.x = x;
  this.y = y;
  // If I call this function without 'new', then it will implicitly return 'undefined' just like every other JS function.
}

Shape.prototype.toString = function() {
  return `Shape is located at (${this.x}, ${this.y}).`;
}

const shape = Shape(10, 20);
console.log(window.x);
// 10
console.log(shape.toString());
// "TypeError: Cannot read property 'toString' of undefined"
```

### The magic of 'new'

* So how does `new` give us the proper context AND prototype object to work with?

```javascript
function newObject(func) {  
  // get an Array of all the arguments except the first one
  const args = Array.prototype.slice.call(arguments, 1);
  
  // PROTIP: Using destructuring assignment 
  // const [_, ...args] = arguments;

  // create a new object with its prototype assigned to func.prototype
  const object = Object.create(func.prototype);

  // invoke the constructor, passing the new object as 'this'
  // and the rest of the arguments as the arguments
  func.apply(object, args);

  // return the new object
  return object;
}

const shape = newObject(Shape, 10, 20);  
console.log(shape.toString())
// "Shape is located at (10, 20)."
```

### Classes

* So what is a class? A class is a blueprint for creating objects.
  In JavaScript, the way we implement them is exactly what we just saw - a constructor function, with methods attached to the prototype.
* The `new` keyword was developed as a way to ease the transition to prototypical inheritance from other languages like Java or Ruby.
* There is a new `class` keyword in ES2015 that gives us more sweet sugar on top of this.

```javascript
// Old way
const Shape = function(x, y) {
  this.x = x;
  this.y = y;
}

Shape.prototype.toString = function() {
  return `Shape is located at (${this.x}, ${this.y}).`;
}

// New way
class Shape {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return `Shape is located at (${this.x}, ${this.y}).`;
  }
}
```

* One big benefit of this new syntax, is it makes class inheritance a lot easier to follow.

```javascript
// Old way

const Square = function(x, y) {
  Shape.call(this, x, y);
}

Square.prototype.toString = function() {
  return `Square is located at (${this.x}, ${this.y}).`;
}

// New way
class Square extends Shape {
  toString() {
    return `Square is located at (${this.x}, ${this.y}).`;
  }
}
```

## Further Learning

### To explore

* What is `this` in a `new MyObject` vs `Object.create`?
* The splat operator, i.e. `...args`
* Property descriptors. `Object.getOwnPropertyDescriptor`
* Getter and Setter functions
* Avoid creating subclasses

### Readings

* [Understanding Prototypes in JavaScript](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)
* [Chapter on Objects from Eloquent JavaScript](http://eloquentjavascript.net/06_object.html)
* [Article on classes from 2ality](http://www.2ality.com/2015/02/es6-classes-final.html). This is a very technical blog with every detail possible.
* [Shopify JavaScript Styleguide](https://github.com/shopify/javascript)
* [this and Object Prototypes from the YDKJS series](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/README.md#you-dont-know-js-this--object-prototypes)
* [MDN article on classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)
* [How Coffeescript creates classes](js2.coffee)
