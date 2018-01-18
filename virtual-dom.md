# FED Workshop: Virtual DOM

**Length:** 2 hours

**Prerequirements:**

* A vague idea of what the DOM is;
* Familiarity with JS;


## Topic

The past few years we've seen the term "virtual DOM", or "vdom", thrown around a lot by frameworks like React. Often cited as a performance feature, what exactly is a virtual DOM and why is it any more performant than the real DOM?


## Workshop Content

1. Why not just use jQuery?
2. What is a virtual DOM?
3. Implementing a vdom
4. Benefits/drawbacks of using a vdom

### Why not just use jQuery?

Traditionally, web developers have just modified the DOM directly, or through a standardizing library like jQuery. This worked fine in the IE6 era. Recently though, JS has become a very fast language, and  we've seen a shift towards web apps and interactive pages. As a community we realized that the old jQuery approach wasn't going to work anymore. Having to specify not only the functionality of a page, but also how to transition to a certain state from any other state was incredibly hard to do manually, and very prone to bugs. We've had a lot of bugs like this here at Shopify. The web community realized that there had to be a better way of writing web pages, and vdom was one of the answers we came up with.

### What is a virtual DOM?

The DOM is the representation of an HTML tree that's then rendered on the page. A tree is any branching data structure (https://upload.wikimedia.org/wikipedia/commons/f/f7/Binary_tree.svg). If you think of an HTML text file as a template, then the DOM is an instance of that file. It follows then that a **virtual** DOM is a user-defined representation of an HTML tree, the main difference to the real DOM being that the virtual DOM is not drawn on the page. The way frameworks like React make use of a vdom is by applying **state changes** to the virtual DOM instead of the real DOM, and then calculating the **difference** between the two trees and the **minimal number of operations** that need to be applied to the real DOM to get it to match the virtual DOM. That was a lot, but it means that a vdom implementation needs to do the following:
 - Accept changes to elements;
 - Calculate the difference between itself and another DOM, and come up with a series of operations that will sync both DOMs;
 - Render itself by modifying the real DOM;

### Implementing a vdom

#### Representing HTML nodes in Javascript

Let's jump into implementation:
```HTML
<p class='paragraph'>
  This is a paragraph
</p>
```
How do we represent an XML tag in Javascript? How about:
```javascript
{type: 'p', properties: {class: 'paragraph'}, children: ['This is a paragraph']}
```
What about this:
```HTML
<ul class='list'>
  <li class='list-item'>Item 1</li>
  <li class='list-item'>Item 2</li>
</ul>
```
Doing the same as above we end up with:
```javascript
{type: 'ul', properties: {class: 'list'}, children: [
  {type: 'li', properties: {class: 'list-item'}, children: ['Item 1']},
  {type: 'li', properties: {class: 'list-item'}, children: ['Item 2']},
]}
```
Notice how we have two different types of children:
 - Text elements;
 - Node elements;

This is something we'll have to take into account when **diff** ing the two DOMs.

Now that we have an object representation of an HTML element, let's wrap it in a function so it's easier to work with:
```javascript
function createElement(type, props, ...children) {
  return { type, props, children };
}
```
```javascript
createElement('ul', { class: 'list' },
  createElement('li', { class: 'list-item' }, 'Item 1'),
  createElement('li', { class: 'list-item' }, 'Item 2'),
)
```

As an aside, if you've heard of JSX then babel performs the same conversion when compiling JSX code: http://codepen.io/amrocha/pen/jyMXjz

#### Rendering and Diffing

Alright, now we have our own representation of an HTML tree, written entirely in JS. Next up we need a way to render this virtual DOM on the screen by creating real DOM elements that correspond to the virtual elements:
```javascript
function renderElement(vnode) {
  let domNode;
  if (typeof vnode === 'string') {
    domNode = document.createTextNode(vnode);
    return domNode;
  }

  domNode = document.createElement(vnode.type);
  vnode.base = domNode;
  Object.keys(vnode.props).forEach((prop) => domNode.setAttribute(prop, vnode.props[prop]));
  vnode.children.forEach(
    (child) => { domNode.appendChild(renderElement(child)); }
  );
  return domNode;
}
```

What we have so far is enough to render something on the screen: http://codepen.io/amrocha/pen/wggjLW?editors=0010

Lastly, we need a way to calculate differences between two DOMs and the operations necessary to bring them in sync. There are 4 cases we need to handle:
 - An element was added;
 - An element was removed;
 - The element has been changed;
  - The element type has been changed;
  - A property was added;
  - A property was removed;
  - An existing property was modified;
 - The element's children have been changed

The first three cases are simple enough to implement:
```javascript
function updateElement(parent, oldNode, newNode) {
  if (!oldNode) {
    parent.appendChild(renderElement(newNode));
  } else if (!newNode) {
    oldNode.base.remove();
  } else if (compareNodes(oldNode, newNode)) {
    if (oldNode.base) {
      oldNode.base.replaceWith(renderElement(newNode))
    } else {
      parent.textContent = newNode;
    }
  } else if (typeof newNode !== 'string') {
    newNode.base = oldNode.base;
    const maxChildrenNode = newNode.children.length > oldNode.children.length ? newNode : oldNode;
    maxChildrenNode.children.forEach((_, index) => {
      updateElement(newNode.base, oldNode.children[index], newNode.children[index]);
    });
  }
}
function compareNodes(node1, node2) {
  // If the nodes are different types or they're both strings but are different strings, return true
  if (typeof node1 !== typeof node2 || typeof node1 === 'string' && node1 !== node2 || node1.type !== node2.type) {
    return true
  }
  // If the nodes are identical strings then return false
  if (typeof node1 === 'string' && node1 === node2) {
    return false;
  }
  // If the nodes are both objects, have the same length, and all of their properties are equal, then return false, otherwise return true
  return !(
    Object.keys(node1.props).length === Object.keys(node2.props).length
    && Object.keys(node1.props).reduce(
      (prev, key) => prev && node1.props[key] === node2.props[key], true // Compare all properties of the nodes and aggregate them in a boolean value
    )
  );
}
```

And that's it! We have a functional virtual DOM implementation, as seen here: http://codepen.io/amrocha/pen/VPKNPO?editors=0010 . There's much that can be improved, but this is a good starting point.

### Benefits/drawbacks of using a vdom

Discussion. Possible topics:
 - Implementation of vDOMs in different frameworks
 - How to implement web components?
 - Performance benefits and developer efficiency
 - Immutability
 - Pure functions
 - Tree edit distance


## Further Learning

### Challenge

Right now we don't support boolean props. How would you go about implementing that? If you want to take it a step further, try implementing components as well.

### Readings

https://medium.com/@deathmood/how-to-write-your-own-virtual-dom-ee74acc13060 - Another take on the content from this workshop
https://www.accelebrate.com/blog/the-real-benefits-of-the-virtual-dom-in-react-js/ - A good post on what the benefits of using a vDOM are
https://calendar.perfplanet.com/2013/diff/ - Some of the optimizations React performs to be performant
http://elm-lang.org/blog/blazing-fast-html - Awesome post on why functional programming makes diffing so much more efficient



## Example code

Read the source code for preact:
https://github.com/developit/preact

With the knowledge of this workshop you should be able to follow along
