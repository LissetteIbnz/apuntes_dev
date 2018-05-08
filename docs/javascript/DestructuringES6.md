
# Destructuring ES6
> http://exploringjs.com/es6/ch_destructuring.html#ch_destructuring
> _Destructuring_  is a convenient way of extracting multiple values from data stored in (possibly nested) objects and Arrays. It can be used in locations that receive data (such as the left-hand side of an assignment). How to extract the values is specified via patterns (read on for examples).

## Object destructuring

Destructuring objects:

```js
const obj = { first: 'Jane', last: 'Doe' };
const {first: f, last: l} = obj; // Renombrando la extracción
    // f = 'Jane'; l = 'Doe'

// {prop} is short for {prop: prop}
const {first, last} = obj;
    // first = 'Jane'; last = 'Doe'
```

## Array destructuring

Array destructuring (works for all iterable values):

```js
const iterable = ['a', 'b'];
const [x, y] = iterable;
    // x = 'a'; y = 'b'
```

Destructuring helps with processing return values:

```js
const [all, year, month, day] =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
```

### Where can destructuring be used?

Destructuring can be used in the following locations (I’m showing Array patterns to demonstrate; object patterns work just as well):

```js
// Variable declarations:
const [x] = ['a'];
let [x] = ['a'];
var [x] = ['a'];

// Assignments:
[x] = ['a'];

// Parameter definitions:
function f([x]) { ··· }
f(['a']);
```

You can also destructure in a  `for-of`  loop:

```js
const arr = ['a', 'b'];
for (const [index, element] of arr.entries()) {
    console.log(index, element);
}
// Output:
// 0 a
// 1 b
```

### More Array destructuring features

#### Elision

Elision lets you use the syntax of Array “holes” to skip elements during destructuring:

```js
const [,, x, y] = ['a', 'b', 'c', 'd']; // x = 'c'; y = 'd'
```

#### Rest operator (`...`)

The  _rest operator_  lets you extract the remaining elements of an iterable into an Array. If this operator is used inside an Array pattern, it must come last:

```js
const [x, ...y] = ['a', 'b', 'c']; // x='a'; y=['b', 'c']
```

[The spread operator](http://exploringjs.com/es6/ch_parameter-handling.html#sec_spread-operator)  has exactly the same syntax as the rest operator – three dots. But they are different: the former contributes data to Array literals and function calls, whereas the latter is used for destructuring and extracts data.

If the operator can’t find any elements, it matches its operand against the empty Array. That is, it never produces  `undefined`  or  `null`. For example:

```js
const [x, y, ...z] = ['a']; // x='a'; y=undefined; z=[]
```

The operand of the rest operator doesn’t have to be a variable, you can use patterns, too:

```js
const [x, ...[y, z]] = ['a', 'b', 'c'];
    // x = 'a'; y = 'b'; z = 'c'
```

The rest operator triggers the following destructuring:

```js
[y, z] = ['b', 'c']
```

### You can assign to more than just variables

If you assign via destructuring, each assignment target can be everything that is allowed on the left-hand side of a normal assignment.

For example, a reference to a property (`obj.prop`):

```js
const obj = {};
({ foo: obj.prop } = { foo: 123 });
console.log(obj); // {prop:123}
```

Or a reference to an Array element (`arr[0]`):

```js
const arr = [];
({ bar: arr[0] } = { bar: true });
console.log(arr); // [true]
```

You can also assign to object properties and Array elements via the rest operator (`...`):

```js
const obj = {};
[first, ...obj.prop] = ['a', 'b', 'c'];
    // first = 'a'; obj.prop = ['b', 'c']
```

If you  _declare_  variables or define parameters via destructuring then you must use simple identifiers, you can’t refer to object properties and Array elements.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NDgwMDI1ODddfQ==
-->