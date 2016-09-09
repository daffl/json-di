# json-di

[![Build Status](https://travis-ci.org/daffl/json-di.png?branch=master)](https://travis-ci.org/daffl/json-di)

> JSON Dependency Injection

## What is it?

`json-di` is a module for loading and initializing other modules through JSON configuration files. The main reason for this is that JSON files are much easier to modify and analyze than source code directly. They allow tools like generators to understand and modify your application structure and dependencies which otherwise would only be possible with messy templates and brittle abstract syntax tree transformations.

[feathers-bootstrap](https://github.com/feathersjs/feathers-bootstrap) uses it to easily create and configure [Feathers](http://feathersjs.com/) applications.

## Usage

> npm install json-di

`json-di` requires a data object, a parent filename (usually `__dirname`) and an optional converter which can run to convert properties and returns a standard `Promise`:

```js
const di = require('json-di');

di({
  path: { require: "path" },
  otherOption: 'test'
}, __dirname).then(result => {
  // result.path === Node's `path` module
  // otherOption === 'test'
});
```

### `require`

- `{ "require": "modulename" }` will load the module `modulename`
- `{ "require": "./mymodule" }` will load `mymodule.js` relative to the JSON file

### `options`

If the module declared in `require` returns a function the `options` property will be passed as the `arguments` to that function. The function can return a promise in which case it will wait until the promise is resolved. With a `mainmodule.js` like this:

```js
module.exports = function(options) {
  return new Promise(resolve => {
    setTimeout(() => resolve(`Hello ${options.text}`), 500);
  });
}
```

A `world.js` like this:

```js
module.exports = 'World';
```

And a `main.json` like this:

```js
{
  "require": "./mainmodule",
  "options": [{
    "text": { "require": "./world" }
  }]
}
```

`json-di` can be used like this:

```js
const di = require('json-di');

di({
  require: "./main.json"
}, __dirname).then(result => {
  // result === 'Hello World'
});
```

## License

Copyright (c) 2016

Licensed under the [MIT license](LICENSE).
