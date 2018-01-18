# FED Workshop: Async programming patterns in JS - Promises

**Length:** 2 hours
**Requirements:**

* You understand basic JavaScript syntax and concepts, for example, assigning variables or declaring functions.
* You have used a basic callback function before, for example: `$button.on('click', callback)`

## Topic

Asynchronous programming is difficult and confusing but it is also a core part of programming in JavaScript.
Luckily, there are a few patterns and tools we can learn to make our lives easier and our async programs easier to reason about.

## Workshop Content

### Concurrency in JavaScript

* We need to know about concurrency when writing JavaScript. Concurrency means more than one thing happening at the same moment in time.
* In JavaScript, things happen asynchronously. This means we give control of when it happens to JavaScript and let it figure things out.
* JavaScript is a single threaded environment so things don't happen in parallel (at the exact same time). They just get coordinated by the JS environment (async).

### Callbacks

* What is a callback?

```javascript
setInterval(pollForVoteStatus, 1000)
```

* We mark a part of the program as something that will happen later.
* Things we trust about callbacks:
  * not too early
  * not too late
  * not too many times
  * not too few times
  * no lost context
  * no swallowed errors
* Unfortunately, we can't actually trust in any of those conditions to be met

### Promises

* What is a Promise?
  * A way to reason about code independent of whether or not the value is currently available
  * Metaphor of going up and ordering a cheeseburger from a restaurant. You pay for it and get a receipt with an order number (the promise). You can then go start getting your napkins, drink, thinking about your cheeseburger even though you don't have the real thing yet.

```javascript
orderCheeseburger.then(consumeCheeseburger)
```

* A promise object accepts a function. That function is passed two params `resolve` and `reject` which are also functions.

```javascript
function loadImage(url) {
  return new Promise((resolve, reject) => {
    let image = new Image()

    image.onload = function() {
      resolve(image)
    }

    image.onerror = function() {
      let message =
        'Could not load image at ' + url
      reject(new Error(msg))
    }

    image.src = url
  })
}

export default loadImage
```

* Promises can be composed which is a major advantage over callbacks.

```javascript
import loadImage from './load-image'

let addImg = (src) => {
  let imgElement =
    document.createElement("img")
  imgElement.src = src
  document.body.appendChild(imgElement)
}

Promise.all([
  loadImage('images/cat1.jpg'),
  loadImage('images/cat2.jpg'),
  loadImage('images/cat3.jpg'),
  loadImage('images/cat4.jpg')
]).then((images) => {
  images.forEach(img => addImg(img.src))
}).catch((error) => {
  // handle error later
})
```

* Promises and generators are the building blocks for future JS async constructs.
  You should learn them first before going further.

### Useful promise techniques

* `.then`
* `.catch`
* Composing promises with `.all`

## Further Learning

### To explore

* Promise composition error handling. What if we want step 2 out of 4 to be allowed to fail?

### Challenge

* Read the article ‘We have a problem with promises’ listed below! It gives a more nuanced look at Promises with a ton of examples and common rookie/intermediate code smells.

### Readings

* [Promises intro video by FunFunFunction](https://www.youtube.com/watch?v=2d7s3spWAzo) - Provided the sourcecode for the cat image examples.
* [We have a problem with promises](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html) <-- MUST READ
* [You Don't Know JS - Chapter 3 on promises](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20&%20performance/ch3.md)
* [Advanced Async and Concurrency Patterns in JS](https://youtu.be/Qg1SvpIau6U?t=7m30s)
* [The TodoMVC of async patterns](https://github.com/getify/a-tale-of-three-lists)


## Example code

[Add Image Callbacks CodePen](http://codepen.io/AWaselnuk/pen/vKRXyz)
[Add Image Promises CodePen](http://codepen.io/AWaselnuk/pen/dXmpzj)
