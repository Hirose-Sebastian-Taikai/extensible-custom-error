# Extensible Custom Error
JavaScript extensible custom error that can take a message and/or an Error object

## Notable Features
There are some pains around JavaScript error handling.
Two of them are:

1. Define your custom errors
2. Wrap errors without losing any data

This `ExtensibleCustomError` class enables you to do both - you can define your custom errors easily and wrap errors with them while merging stack traces prettily.

### Define your custom errors
To define custom errors in Vanilla JS, you need to set names and stack traces manually, but you no longer need to do that with `ExtensibleCustomError`.

```js
class MyError extends ExtensibleCustomError {}
```

N.B. With an uglifier, class names might get obsecure. See [this issue comment](https://github.com/bjyoungblood/es6-error/issues/31#issuecomment-301128220).

### Wrap errors without losing any data
Built-in errors only take a message, so they can't wrap any errors, which means stack traces so far will be lost.
However, `ExtensibleCustomError` can take a message and/or an Error object while merging stack traces.

```js
catch (error) {
  throw new MyError(error);
}
```

```js
catch (error) {
  throw new MyError('message', error);
}
```

## Usage

### Define custom errors
```js
const BaseError = require('extensible_custom_error');

// That's it!
class MyError extends BaseError {}
```

```js
// Should you need to set custom properties
class MyErrorWithCustomProperty extends BaseError {
  constructor(...args) {
    // Ensure calling the super constructor
    super(...args);

    Object.defineProperty(this, 'customProperty', {
      configurable: true,
      enumerable : false,
      value : 'I am the Bone of my Sword',
      writable : true,
    });
  }
}
```

### Instantiate custom errors
You can instantiate your custom errors in the same way as built-in errors.

```js
// Do it as usual!
throw new MyError('Steel is my Body and Fire is my Blood');
```

```js
try {
  // Do something that may cause errors
} catch (error) {
  // Pass an error instance, then stack traces will be merged
  throw new MyError(error);
}
```

```js
try {
  // Do something that may cause errors
} catch (error) {
  // Pass a message and an error instance, then stack traces will be merged
  throw new MyError('I have created over a Thousand Blades', error);
}
```

## Examples

### Wrap an error
If you run:

```js
function throwBuiltinError() {
  throw new Error('Unknown to Death, Nor known to Life');
}

function wrapErrorWithMyError() {
  try {
    throwBuiltinError();
  } catch (error) {
    throw new MyError(error);
  }
}

function main() {
  try {
    wrapErrorWithMyError();
  } catch (error) {
    console.log(error)
  }
}

main()
```

you'll get:

```bash
MyError: Error: Unknown to Death, Nor known to Life
    at wrapErrorWithMyError (/home/necojackarc/custom_error.js:101:11)
Error: Unknown to Death, Nor known to Life
    at throwBuiltinError (/home/necojackarc/custom_error.js:94:9)
    at wrapErrorWithMyError (/home/necojackarc/custom_error.js:99:5)
    at main (/home/necojackarc/custom_error.js:107:5)
    at Object.<anonymous> (/home/necojackarc/custom_error.js:113:1)
    at Module._compile (module.js:652:30)
    at Object.Module._extensions..js (module.js:663:10)
    at Module.load (module.js:565:32)
    at tryModuleLoad (module.js:505:12)
    at Function.Module._load (module.js:497:3)
    at Function.Module.runMain (module.js:693:10)
```

### Wrap an error while passing a new message

If you run:

```js
function throwBuiltinError() {
  throw new Error('Have withstood Pain to create many Weapons');
}

function wrapErrorWithMyError() {
  try {
    throwBuiltinError();
  } catch (error) {
    throw new MyError('Unlimited Blade Works', error);
  }
}

function main() {
  try {
    wrapErrorWithMyError();
  } catch (error) {
    console.log(error)
  }
}

main()
```

you'll get:

```bash
MyError: Unlimited Blade Works
    at wrapErrorWithMyError (/home/necojackarc/custom_error.js:101:11)
Error: Have withstood Pain to create many Weapons
    at throwBuiltinError (/home/necojackarc/custom_error.js:94:9)
    at wrapErrorWithMyError (/home/necojackarc/custom_error.js:99:5)
    at main (/home/necojackarc/custom_error.js:107:5)
    at Object.<anonymous> (/home/necojackarc/custom_error.js:113:1)
    at Module._compile (module.js:652:30)
    at Object.Module._extensions..js (module.js:663:10)
    at Module.load (module.js:565:32)
    at tryModuleLoad (module.js:505:12)
    at Function.Module._load (module.js:497:3)
    at Function.Module.runMain (module.js:693:10)
```

## Special Thanks
* [@yszk0123](https://github.com/yszk0123) as a reviewer
* [bjyoungblood/es6-error](https://github.com/bjyoungblood/es6-error) as a reference