# 01 - New variables

var
  - can be redeclared
  - *function scoped* (only available inside function)
    + only available inside function
    + if outside function, available outside function
  - BAD: vars defined within if/for are available outside it
    + lets and consts are *block scoped*

let/consts
  - cannot be redeclared in the same block

const
  - cannot be redeclared
    + However, attributes of a const object can be changed!
  - 

## Immediately-Invoked Function Expression (IIFE) are obsolete
A function that is immediately executed, and the vars in it don't leak to global scope
```js
(function() {
  // the var 'wes' does not leak to global scope (which in the case of window, also has 'wes' defined as a var)
  var name = 'wes';
})();
```

This is no longer needed when you use `const` or `let`

## Example: why vars in a for loop are bad

```js
for(var i = 0; i < 10; i++) {
  console.log(i);
  setTimeout(function() {
    console.log('The number is ' + i);
  },1000);
}
```

## Temporal dead zone 

A var can be accessed before it is declared (and will return 'undefined')
A const/let will throw an error.
So, const/let are constrained by the *temporal dead zone*, which is the zone where the variable is not yet defined.


# 02 - Arrow functions

Advantages
  - concise
  - implicit return
  - does not rebind the value of `this`

Properties
  - Arrow functions are always *anonymous functions* (or, nameless functions)

## Syntax rules

```js
// old
const fullNames = names.map(function(name) {
  return `${name} bos`;
});

// no need for 'function', but use a '=>' (fat arrow) 
const fullNames2 = names.map((name) => {
  return `${name} bos`;
});

// if there is only parameter, then no parentheses are needed
const fullNames3 = names.map(name => {
  return `${name} bos`;
});

// implicit return: no curly brackets, then 'return' is implied
const fullNames4 = names.map(name => `${name} bos`);

// 0 arguments, then use empty parentheses '()''
const fullNames5 = names.map(() => `cool bos`);

// arrow functions are anonymous, so to call it later, assign it to a variable to give it a name.
const sayMyName = (name) => {
  alert(`Hello ${name}!`)
}
sayMyName('wes');
```

## How to return a object literal with implicit return
Use parentheses, otherwise there would be a syntactical conflict between curly brackets meaning 'object literal' and 'implicit return'.
```js
const contestants = ['Henk', 'Wes', 'Piet'];
const race = 'Dam tot dam loop';
const win = contestants.map((name, index) => ({name, place: index, race}));

// the wrong way
const win = contestants.map((name, index) => {name, place: index, race});
```

## `this` rebinding

```js
const box = document.querySelector('.box');
box.addEventListener('click', function() {
  // to access 'box', we can use 'this' because it is rebound to box
  // if this was an arrow funciton, 'this' would be inherited (in this case, it would still be bound to 'window')
  // in this case, using a traditional function is the way to go
  this.classList.toggle('opening'); // toggle class 'opening' to box

  // use arrow function is you want to keep 'this' binding intact, such as here:
  setTimeout(() => {
    this.classList.toggle('open'); // 'this' is still bound to 'box'
  }, 500)
})
```

## Default arguments
  - use `argument = defaultValue` syntax in function signature
  - use 'undefined' to explicitly use the default

## When not to use a arrow function
  - when you really need `this` to be rebound (see previous `addEventListener` function)

```js
// When you really need `this`
const button = document.querySelector('#pushy');
button.addEventListener('click', function() {
  console.log(this);
  this.classList.toggle('on');
});

// When you need a method to bind to an object
const person = {
  points: 23,
  score() {
    console.log(this);
    this.points++;
  }
}

// When you need to add a prototype method
class Car {
  constructor(make, colour) {
    this.make = make;
    this.colour = colour;
  }
}

const beemer = new Car('bmw', 'blue');
const subie = new Car('Subaru', 'white');

Car.prototype.summarize = function() {
   return `This car is a ${this.make} in the colour ${this.colour}`;
};

// When you need arguments object
const orderChildren = function() {
  const children = Array.from(arguments);
  return children.map((child, i) => {
    return `${child} was child #${i + 1}`;
  })
  console.log(arguments);
}
```

# 03 - Template Strings
  - newlines, tabs, etc.. are persisted into the string
  - use `${dog.name}` to *interpolate* variables or execute javascript code 
  - Template literals can be nested, and you can loop over arrays within it using map
  - You can write markup easily with template strings, however: always sanitize.

## Tagged Template String
Template strings can be tagged, sort of preprocessed, like so:

```js

// strings is an array of the template literal parts that are pure string (i.e. the template literal split on '${...}') 
function highlight(strings, ...values) {
  let str = '';
  strings.forEach((string, i) => {
    str += `${string} <span contenteditable class="hl">${values[i] || ''}</span>`;
  });
  return str;
}

const name = 'Snickers';
const age = 100;

const sentence = highlight`My dog's name is ${name} and he is ${age} years old`;
```

# 04 - Additional String improvements
  - `.startsWith(searchString [,position=0])`
    + position: The position in this string at which to begin searching for searchString; defaults to 0.
  - `.endsWith(searchString [,position=0])`
  - `.includes()`
  - `.repeat()`
    + `'🐍'.repeat(3)` returns => `'🐍🐍🐍'`

# 05 - Destructuring
  - simple: `const {field} = object` is equivalent to `const field = object.field`
  - nested: `const {nestedField} = object.fieldWithSubfields`
  - rename: `const {field:renamedField} = object` is equivalent to `const renamedField = object.field`
  - default: set default values for variables if they are missing in the object that is being destructured.

## Example destructuring with defaults
```js
const settings = { width: 300, color: 'black' }  // height, fontSize
const { width = 100, height = 100, color = 'blue', fontSize = 25} = settings;
```
This destructures to the following variables: `width = 300`, `height = 100`, `color = black` and `fontSize = 25`.

## Destructuring arrays
Example:
```js
const [first, second, ...others] = ['hi', 'how', 'are', 'you']
// `...` is called the *rest operator*
```
This destructures to the following variables: `first = 'hi'`, `second = 'how'` and `others = ['are', 'you']`

## Destructuring in action
Swap variables:
```js
let foo = 'x'
let bar = 'y'

[foo, bar] = [bar, foo] // create array containing the elements foo and bar, and then immediately destructure
//Now, foo = 'y', bar = 'x'
```

## Destructuring functions
  - The return object of a function can also be destructured: `foo(){return {x:2,y:3,z:4}}; const {x,y} = foo();` destructures to `x=2` and `y=3`
  - If the arguments of a function is an object, they can be destructured like so:

```js
function tipCalc({ total = 100, tip = 0.15, tax = 0.13 } = {}) {
  return total + (tip * total) + (tax * total);
}

const bill = tipCalc({ tip: 0.20, total: 200 });
```

# 06 - Iterables & Looping
*maybe redo later*
- a 'for ... of ...
`for const x of iterable` can be used on iterable collections (such as `NodeList`)


# 07 - An Array of Improvements
  - The `Array.from()` method creates a new Array instance from an array-like or iteraible (such as `NodeList`, or the `arguments` keyword)
    +  Also takes optional argument: a map function
  -  `Array.of()` return an array which contains its arguments
  - The `find()` method returns a value in the array, if an element in the array satisfies the provided testing function. Otherwise `undefined` is returned.
    + If you want to find multiple elements, use `filter()`
    + If you want to find the index of the first element that satisfies the testing function, use `findIndex()`
  - `some()` and `every()` check if some or every element satisfies the testing function, respectively

# 08 - Spread and Rest operator

## Spread operator
  - The spread operator takes the elements of one array and puts them in another array
  - Used to make copies of array, of construct arrays from a mix of other arrays and single elements
  - Can also turn a string into an array of its characters (ex: `[...'wes'] === ['w','e','s']`)
  - 

### Example: remove element from array with slide and spread
'''js
// get index
const id = '17633724'
const index = array.findIndex((el) => el.id === id)  
const newArray = [...array.slice(0, index), ...array.slide(index+1)]
```

### Example: spreading into a function
Easy way to input the arguments
```js
const name = ['Wes', 'Bos'];

function sayHi(first, last) {
  alert(`Hey there ${first} ${last}`);
}

sayHi(...name);
```

## The Rest operator
Basically the opposite of Spread: take multiple values and put them in one array.

### Rest operator in a function

```js

function convertCurrency(rate, ...amounts) {
  // all arguments after rate are put in an array called `amounts`
  return amounts.map(amount => amount * rate)
}

//these both work
convertCurrency(0.2, 10, 20)
convertCurrency(0.2, 10, 20, 50, 30, 40)
```

### Rest operator in destructuring
```js
const runnner = ['Wes Bos', 84352397, 12, 14, 8, 9]
const [name, id, ...runs] = runner
// now we have name = 'Wes Bos', id = 84352397 and runs = [12, 14, 8, 9]
```

# 09 - Object Literal Upgrades
If the property name and the variable name are the exact same, you can give put that name when constructing the object

## Object literal with functions
```js
const objectWithFunction = {
  foo: function(bar) { // old way
  ...
  },
  foo(bar) { // new shorthand way

  },
  //don't use arrow functions here, because you probably need `this`
}
```

## Computed property names
Use [foo] as fieldname to evaluate foo to the fieldname

const key = 1232141;
const value = 'hello'
const alternativeValue = 'hi'

const object = {
  [key]: value,  // `value` will be stored in `object.1232141`
  [`${key}_alt`]: alternativeValue // `alternativeValue` will be stored in `object.1232141_alt`
}

# 10 - Promises
Often used when 