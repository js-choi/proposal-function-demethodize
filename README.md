# Demethodize function for JavaScript
ECMAScript Stage-0 Proposal. J. S. Choi, 2022.

* Formal specification: Not yet
* Babel plugin: Not yet
* [Proposal history][HISTORY.md]

[HISTORY.md]: ./HISTORY.md

## Rationale
The dynamic `this` binding is a fundamental part of JavaScript design and
practice today. Many APIs use the `this` binding functionally as another
“argument” to their functions. Because of this, developers frequently need to
change the `this` binding. However, dynamic prototype-based dispatch may often
be undesirable, and using a `.call.bind()` method is frequent in many
codebases.

We therefore propose exploring the addition of an API to the JavaScript
language that would convert `this`-based methods into ordinary functions that
do not use `this`.

If this proposal is approved for Stage 1, then we would explore various
directions for the API’s design. We would also assemble as many real-world use
cases as possible and shape our design to fulfill them.

In addition, if we would explore cross-cutting concerns and overlap with the
proposed [call-this syntax][] and other [dataflow proposals][].

[dataflow proposals]: https://jschoi.org/22/es-dataflow/

## Description
The `Function.prototype.demethodize` method would create a new function that
calls the original function, supplying its first argument as the original
function’s `this` receiver, and supplying the rest of its arguments as the
original function’s ordinary arguments.

This is useful for converting `this`-based functions into non-`this`-based
functions.

```js
fn.demethodize();

const $slice = Array.prototype.slice.demethodize();
$slice([ 0, 1, 2 ], 1); // [ 1, 2 ].
```

This is not a substitute for a [call-this syntax][], which allows developers to
change the receiver of functions without creating a wrapper function.

[call-this syntax]: https://github.com/tc39/proposal-call-this

`fn.demethodize()` is equivalent to\
`Function.prototype.call.bind(fn)` and to\
`Function.prototype.bind.bind(Function.prototype.call)(fn)`.

Therefore, `fn.demethodize()(thisArg, ...restArgs)` is equivalent to
`fn.call(thisArg, ...restArgs)`.

The following real-world example originally used [call-bind][] or a manually
created similar function.

```js
// From chrome-devtools-frontend@1.0.934332
// node_modules/array-includes/test/implementation.js.
runTests(implementation.demethodize(), t);

// From string.prototype.trimstart@1.0.4/index.js:
var bound = getPolyfill().demethodize();

// From andreasgal/dom.js (84b7ab6) src/snapshot.js.
const /* … */
  join = A.join || Array.prototype.join.demethodize(),
  map = A.map || Array.prototype.map.demethodize(),
  push = A.push || Array.prototype.push.demethodize(),
  /* … */;
```

Precedents include:
* [call-bind][]: `callBind`

[call-bind]: https://www.npmjs.com/package/call-bind
