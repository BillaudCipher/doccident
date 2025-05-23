[![npm version](https://badge.fury.io/js/doccident.svg)](http://badge.fury.io/js/doccident)
[![Build Status](https://travis-ci.org/Widdershin/doccident.svg?branch=master)](https://travis-ci.org/Widdershin/doccident)
[![Greenkeeper badge](https://badges.greenkeeper.io/Widdershin/doccident.svg)](https://greenkeeper.io/)

* * *

# doccident

Test all the code in your markdown docs!

Why on earth?
---

As an open source developer, there are few things more embarrassing than a user opening an issue to inform you that your README example is broken! With  `doccident`, you can rest easy knowing that your example code is *actually runnable*.

Installation
---
Just `npm install @doccident/doccident` and run `doccident`. It will run all of the Javascript code examples tucked away in your markdown, and let you know if any blow up.

Okay, how do I use it?
---

Let's try it on this repo!

```js
var a = 5;

var b = 10;

console.log(a + c);
```

There's a problem with that example. `doccident` finds it for us:

```bash
$ doccident
x..

Failed - README.md:32:17
evalmachine.<anonymous>:7
console.log(a + c);
                ^

ReferenceError: c is not defined
```

Awesome! No excuse for broken documentation ever again, right? :wink:

We can also run specific files or folders by running `doccident` with a glob, like `doccident docs/**/*.md`. By default `doccident` will recursively run all the `.md` or `.markdown` files starting with the current directory, with the exception of the `node_modules` directory.

Note: `doccident` doesn't actually attempt to provide any guarantee that your code worked, only that it didn't explode in a horrible fashion. If you would like to use `doccident` for actually testing the correctness of your code, you can add some `assert`s to your examples.

`doccident` is not a replacement for your test suite. It's designed to run with your CI build and give you peace of mind that all of your examples are at least vaguely runnable.

So how do I write those examples?
---

In your markdown files, anything inside of code blocks with 'js' or 'es6' will be run. E.g:

    ```js
    console.log("Yay, tests in my docs");
    ```

    ```es6
    const a = 5;
    console.log({a, foo: 'test'});
    ```

I have a code example I don't want tested!
---
You can tell `doccident` to skip examples by adding `<!-- skip-example -->` before the example. E.g:

    <!-- skip-example -->
    ```js
    // not a runnable example

    var foo = download(...);
    ```

How do requires work? And other setup logic?
---

You can `require` any needed modules or example helpers in `.doccident-setup.js`. E.g:

<!-- skip-example -->
```js
// .doccident-setup.js
module.exports = {
  require: {
    Rx: require('rx')
  },

  globals: {
    $: require('jquery')
  }
}
```

Anything exported under `require` will then be used by any examples that `require` that key.
You must explicitly configure all of the dependencies used in your examples.

Anything exported under `globals` will be available globally across all examples.

You can also specify a regexRequire section to handle anything more complex than an exact string match!

<!-- skip-example -->
```js
// .doccident-setup.js
module.exports = {
  require: {
    Rx: require('rx')
  },

  regexRequire: {
    'rx/(.*)': function (fullPath, matchedModuleName) {
      return require('./dist/' + matchedModuleName);
    }
  }
}
```

Do I have to enable es6 support?
---

Nope, ES6 support is on by default. You can disable `babel` support
in your `.doccident-setup.js` file.
This will speed things up drastically:

<!-- skip-example -->
```js
//.doccident-setup.js
module.exports = {
  babel: false
}
```

What if I have global state that needs to be reset after my examples run?
---
<!-- skip-example -->
```js
//.doccident-setup.js
module.exports = {
  beforeEach: function () {
    // reset your awesome global state
  }
}
```

You can specify a function to be run before each example in your `.doccident-setup.js`.

What if I want to remove custom syntax from examples before processing?
---

<!-- skip-example -->
```js
//.doccident-setup.js
module.exports = {
  transformCode(code) {
    // Remove ... from code syntax
    return code.replace(/\.\.\./g, "");
  }
}
```

Who uses doccident?
---

All of these projects either run `doccident` with `npm test` or as part of their CI process:

* [lodash](https://github.com/lodash/lodash)
* [Moment](https://github.com/moment/momentjs.com)
* [RxJS](https://github.com/ReactiveX/RxJS)
* [most](https://github.com/cujojs/most)
* [xstream](https://github.com/staltz/xstream)
* [cyclejs/time](https://github.com/cyclejs/time)
* [rx.schedulers](https://github.com/Reactive-Extensions/rx.schedulers)
* [rx.priorityqueue](https://github.com/Reactive-Extensions/rx.priorityqueue)
* [rx.disposables](https://github.com/Reactive-Extensions/rx.disposables)
* [rx-undoable](https://github.com/Widdershin/rx-undoable)
