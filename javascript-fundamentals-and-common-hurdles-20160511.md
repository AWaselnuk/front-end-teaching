# FED Workshop: JavaScript Fundamentals and Common Hurdles

**Date:** May 11, 2016
**Length:** 2 hours
**Requirements:**

* You understand basic JavaScript syntax and concepts, for example, assigning variables or declaring functions.

## Topic

In this workshop we will discuss some fundamental JavaScript concepts that are often tripping points for programmers. These concepts include: Closures, 'this', and the module pattern

## Workshop Content

### Closures and this

* 'this' is the current world you are in. Every time you enter a new function body, you enter a new world.
* Lexical scoping
* 'Function' scoping/context
  * Proof of this is in the idea of (hoisting variables)[http://adripofjavascript.com/blog/drips/variable-and-function-hoisting]
* Why would you use a closure?
  * Information hiding, e.g. faking private methods.
  * Retaining information about the context the inner function was declared in. Example: you need to loop through a bunch of elements and attach a click event.
* All example code above uses a closure to implement the module pattern.
* Try putting the keyword `debugger` into some running JavaScript. You will be able to see the call stack, the current local variables, what `this` is, the function scope you are in, and closures you are in.

### The module pattern

* An example of using closures for information hiding
* Every example above uses this pattern by defining functions and variables within an IIFE (Immediately Invoked Function Execution). Look at how the information is hidden and what information is hidden in the examples above.
* Closures allow the module pattern to work
* The CoffeeScript EcmaScript 2015 `class` keyword are doing roughly the same thing under the hood: using a closure to hide information and return a usable object.
* CoffeeScript actually wraps entire files in an IIFE to avoid polluting the global namespace. This is why you will see closures appearing in the debugger even if you didn't explicitly create one yourself.

## Further Learning

### To explore

* Function declarations vs function expressions. What is advantage to using function declarations?

### Challenge

* Use the module pattern to create a small module that validates an email address. The module should expose only a single public method `validate` which accepts any string.

### Readings

* [MDN article on Closures](https://developer.mozilla.org/en/docs/Web/JavaScript/Closures)
* [The Module Pattern](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)
* [Chapter on Modules from Eloquent JavaScript](http://eloquentjavascript.net/10_modules.html)
* [Advanced Javascript Course by Kyle Simpson](https://frontendmasters.com/courses/advanced-javascript/)
