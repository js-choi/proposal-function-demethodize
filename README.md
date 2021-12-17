# unThis function for JavaScript
ECMAScript Stage-0 Proposal. J. S. Choi, 2021.

* Formal specification: Not yet
* Babel plugin: Not yet
* [Proposal history][HISTORY.md]

[HISTORY.md]: https://github.com/js-choi/proposal-function-unthis/blob/main/HISTORY.md

The `Function.prototype.unThis` method creates a new function that calls the
original function, supplying its first argument as the original function’s
`this` receiver, and supplying the rest of its arguments as the original
function’s ordinary arguments.

This is useful for converting `this`-based functions
into non-`this`-based functions.

```js
fn.unThis();

const $slice = Array.prototype.slice.unThis();
$slice([ 0, 1, 2 ], 1); // [ 1, 2 ].
```

This is not a substitute for a [bind-this syntax][], which allows developers to
change the receiver of functions without creating a wrapper function.

[bind-this syntax]: https://github.com/js-choi/proposal-bind-this

`fn.unThis()` is equivalent to\
`Function.prototype.call.bind(fn)` and to\
`Function.prototype.bind.bind(Function.prototype.call)(fn)`.

Therefore, `fn.unThis()(thisArg, ...restArgs)` is equivalent to
`fn.call(thisArg, ...restArgs)`.

The following real-world example originally used [call-bind][] or a manually
created similar function.

```js
// From chrome-devtools-frontend@1.0.934332
// node_modules/array-includes/test/implementation.js.
runTests(implementation.unThis(), t);

// From string.prototype.trimstart@1.0.4/index.js:
var bound = getPolyfill().unThis();

// From andreasgal/dom.js (84b7ab6) src/snapshot.js.
const /* … */
  join = A.join || Array.prototype.join.unThis(),
  map = A.map || Array.prototype.map.unThis(),
  push = A.push || Array.prototype.push.unThis(),
  /* … */;
```

Precedents include:
* [call-bind][]: `callBind`

[call-bind]: https://www.npmjs.com/package/call-bind

## Polyfill

- A polyfill is available in the [core-js] library. You can find it in the [ECMAScript proposals section][core-js-section].

[core-js]: https://github.com/zloirock/core-js
[core-js-section]: https://github.com/zloirock/core-js#functionprototypeunthis
