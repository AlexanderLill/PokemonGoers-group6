# Server-side JavaScript with Node.js Basics

# Introduction

Node.js is a JavaScript runtime environment that enables developers to build backends of sophisticated web applications. A characteristic that differentiates Node.js from other server-side technologies like Java is how concurrency is handled. Node.js' approach builds on the event-driven architecture of the JavaScript language. This means that a single JavaScript thread handles incoming requests. Node.js achieves this through asynchronous i/o. Instead of waiting for network requests or file system accesses to finish, the JavaScript thread continues its execution and a callback is executed once the data arrives. A single Node.js process can handle thousands of requests much more efficiently compared to the classical approach where for every request a new thread is created. Node is thus optimized for optimal throughput and scalability.

# Node.js history

**8. November 2009**

Ryan Dahl, the creator of Node.js, introduced Node.js at JSConf.eu in Berlin. On one of his first slides Ryan Dahl characterized Node.js with the following five points:

- Server-side JavaScript<br>
  Back in 2009 JavaScript was mainly used in the browser. The usage of JavaScript to build server-side applications was a novelty.
- Built on Google's V8<br>
  The V8 JavaScript engine developed by Google for its Chrome browser was at the time, and is still today, one of the fastest JavaScript engines on the market. Ryan Dahl chose to use it because V8 is open-source.
- Evented, non-blocking I/O<br>
  The way how Node.js deals with asynchronicity is one of its defining characteristics. Ryan Dahl later explained in his talk in great detail why asynchronous  i/o is important for Node.js.
- CommonJS module system<br>
  Back in 2009 the usage of a module system with JavaScript was still uncommon. Ryan Dahl chose to use the CommonJS module format for Node.js. In Node.js every file is a module and it is generally preferred to have more modules instead of long files.
- 8000+ lines of C/C++, 2000 lines of JavaScript<br>
  Ryan Dahl meant for Node.js to serve as the foundation for programs written in JavaScript. It itself is mainly written in the system programming languages C and C++. Today, much of the functionality originally written for Node.js has been extracted from the Node.js project into standalone libraries like "libuv".

The first four points are still true for Node.js as we know it today. Only the number of lines of code in point 5 has risen considerably since. It is apparent that the architectural design choices that Ryan Dahl made for Node.js back in 2009 were very solid. The video recording of Ryan Dahl's talk is available on YouTube.

**12. January 2010**

Isaac Schlueter introduced the package manager "npm". npm makes integrating third party code into Node.js projects very easy. The first versions of npm didn't ship with Node.js itself. Instead it had to be installed separately.

**10. November 2010**

Ryan Dahl passed the copyright for Node.js to his employer, the company Joyent. Node.js had risen in popularity and he felt that the project should be backed by a corporate entity.

**July 2011**

Microsoft and Joyent cooperated to make Node.js run on Windows which previously only ran on Linux and macOS. This step meant that Node.js was then available on all three major platforms.

**25. November 2011**

Node.js version 0.6.3 shipped as the first version with the package manager npm included. Node.js and npm are today inseparable projects. The inclusion of npm meant a major milestone.

**December 2014**

In late 2014 many of the main contributors became unhappy about how the Node.js open-source project was run. Releases were infrequent and the included version of the JavaScript engine V8 was outdated. For these reasons Fedor Idutny and other node contributors decided to fork the project. The resulting project was called "io.js". Contrary to Node.js, io.js had much more frequent releases and it shipped with a recent version of V8 that included new JavaScript features. Consequently io.js felt a lot fresher than Node.js.

**14. September 2015**

The introduction of io.js created a rift inside the Node.js community. Both factions weren't happy with the split in the ecosystem and they began talking to each other about how the two projects could merge once more. As a result, the Node.js Foundation was formed and in September 2015 Node.js version 4.0 shipped that combined both projects.

**Now**

Node.js is nowadays a very healthy open-source project.

- It consistently had 1-2 releases in the past months
- The Node.js Foundation has a technical steering committee that makes swift technical decisions
- npm, the package manager, has become very reliable

# Module pattern in Node.js

# Reasons for using a modular system

  A big server can contain a lot of code which makes it harder to work on if everything is in one file. The idea behind modular systems is to use a set of highly decoupled files with distinct functionalities. By removing as many dependencies as possible, you can increase team scalability, maintainability and decrease testing costs. The modules can be authored individually, debugged independently and their scope and context can be controlled easily. In short you keep your develop environment clean.    

# CommonJS - Node's Module System

  For writing about the module pattern in Node.js it is important to know where it came from. That's why we'll take a closer look at CommonJS. CommonJS is a project made for creating a JavaScript ecosystem outside the browser.

**January 2009**

  CommonJS was created by the software developer Kevin Dangoor under the name "ServerJS". Kevin Dangoor's idea was to create a JavaScript environment, which is able to [include other modules, package up code and install packages for creating cleaner server-side environments](http://www.blueskyonmars.com/2009/01/29/what-server-side-javascript-needs).

**March 2009**

  The CommonJS API version 0.1 with its "securable modules" was released. The idea behind secure modules is basically to have additional constraints. E.g. a module must not write to any free variables or their transitive members, it must not refer to any free variables apart from primordials (Defined by [ECMAScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm) primodials are `Object`, `Array`, etc.), `require`, `environment` and `exports` and it must not tamper with or mutate the transitive primordials.

**April 2009**

  The CommonJS modules were shown off with several implementations at the first JSConf in Washington, DC. 

**August 2009**

  The group was formally renamed CommonJS to reflect the broader applicability of the APIs.

**8 November 2009**

  Ryan Dahl announced Node.js with the modular environment of CommonJS.

**26 May 2013**

  Isaac Z. Schlueter wrote a statement where he explained why CommonJS is [made obsolete by Node.js and is avoided by the core Node.js developers](https://github.com/nodejs/node-v0.x-archive/issues/5132#issuecomment-15432598). Node.js needed asynchronous IO paradigms, cross-platform compatibility and streaming APIs, which CommonJS never really achieved. Another critical difference was the missing priority of performance for the CommonJS community which was one of the main priorities of Node.js. That's why Schlueter separated Node.js from the CommonJS keeping some core features like the module system from the original "securable modules".

# Using the module pattern

  The modular structure of Node.js right now is easy to use. There are three scoped variables `require`, `exports` and `module`. 

**require**

  `require(...)` is general enough to support many directory structures for building native packages from Node.js modules without modification. `require(...)` can load core modules of Node.js, .js files as JavaScript text files, .json files as JavaScript Objects, .node files as binary add-ons and already installed node modules. `require` caches the loaded modules which means multiple calls of `require` will get exactly the same object returned, if it's resolving to the same file. Because of this caching, calling `require` twice on the same file may not execute it twice. `require(...)` is case-sensitive. That's why the cache will reload `require('./file')` and `require('./FILE')` even if it's the same file. `require(...)` does always prefer core modules even if a file with the same name exists in the directory. 

**exports**

  To access objects and functions we need to export them. Functions and objects can be added to the root of your module by adding them as property to the `exports` variable. Variables local to the module will be private, because the module is wrapped in a function by Node.js itself. 

**module**

  Using `module.exports` instead of `exports` changes the root of your module's export to be a function or object and lets you export a complete object in one assignment without the need to use properties. The `module` variable provides metadata about the module itself, e.g. it provides a `filename` property which is equivalent to `__filename`.

# npm - Node's package manager

A package manager or package management system is a tool that automates the the process of installing, updating and removing packages. Packages in Node.js are modules that provide a certain functionality (see the next section about useful npm packages). The default package manager for Node.js "npm" is completely written in JavaScript and is included in every standard installation.

"npm" is controlled using a Command Line Interface. The most basic commands are as follows:
* __npm install__
  This looks up all packages defined in the *package.json* and installs those on the current system. Packages are installed into the *node_modules* folder of the current project.
* __npm install <packagename>__
  This installs the given package for the current project.
* __npm install -s <packagename>__
  This installs the given package for the current project and adds this package to the *package.json* file.
* __npm install -g <packagename>__ installs a package globally, using a system-wide destination directory for the module. This is used for packages that will be used on the Command Line (e.g. *gulp <task> <othertask>*). `//TODO: Explain this more?`

npm is also widely used for automating reoccuring tasks. This can be accomplished by defining so called "scripts" in the *package.json* file. The following shows an example definition of skripts for the *start* and *test* commands:
```JS
{ ...,
  "scripts": {
    "start": "node lib/main",
    "test": "node tests/main && standard"
  }, ...
}
```

Those pre-defined scripts can then be executed using `npm start` and `npm test`. This section can also be extended with other scripts, which can be run with `npm run-script scriptname`. Many more commands can be found on the  [project website](https://docs.npmjs.com/cli/npm).

"npm" currently has more than 300,000 packages, and about 400 new packages get added every day (Source: http://www.modulecounts.com/)

# Excellent ES2015 support in Node.js

The objective of this section is to highlight the good support for modern JavaScript in Node.js.

## ES2015

"ES" stands for "ECMAScript". "ECMA" is the standards body that standardizes JavaScript. "ECMAScript" is the official name of JavaScript and the name refers to the language inside the standard. "ES2015", formerly called "ES6", introduces many new features to the language. In recent months the version of V8 included with Node.js began to support more and more ES2015 features. According to the feature test on  [kangax.github.io](http://kangax.github.io/compat-table/es6/) Node.js version 6 supports 92% of the ES2015 specification.

## 7 useful ES2015 features

In the following persents 7 useful ES2015 features for Node.js development.

### Property Shorthand

```JS
const name = 'Max Mustermann'
const age = '67'

// Old
const person = { name: name, age: age }

// New
const person = { name, age }
```

The property shorthand is an object literal enhancement that makes it less verbose to define properties that have the same name as a variable.

### Method Definition Shorthand

```JS
// Old
const obj = {
  hello: function hello () { console.log('hello') }
}

// New
const obj = {
  hello () { console.log('hello') }
}

obj.hello()
```

This object literal enhancement makes the definition of methods on an object briefer.

### Arrow functions

```JS
// Old
let studentNames = students.map(function (s) { return s.name })

// New
let studentNames = students.map(s => s.name)
```

Arrow functions represent a new syntax for defining functions. The arrow function syntax is less verbose than the traditional function literal. For instance the example above shows how omitting the curly braces around the function body works as an implied return statement. Another useful feature of arrow functions is that they preserve the `this` of the outer scope which is useful for defining callbacks.


### Classes

```JS
class Student extends Person {
  constructor (name, university) {
  super(name)
    this._university = university
  }

  get university () { return this._university }
}
```

ES2015 introduces a new syntax for defining classes. It should be noted that the new class syntax is just an alternative syntactical way for defining classes in JavaScript. Under the hood ES2015 classes work with the same prototypal inheritance system that JavaScript always had.


### Promises
```JS
const fs = require('fs-promise')

fs.readFile('hello.text')
  .then(buffer => { ... })
  .catch(error => { ... })
```

Node.js traditionally uses callbacks for asynchronicity. ES2015 introduces an alternative mechanism called "promises". A promise is an object that represents an eventual value. The advantage of promises is that they, since they are objects, can be returned as a return value by functions. Also, promises provide a robust way for error handling which reduces the risk for bugs inside error handling code.

### Destructuring Assignment
```JS
// Old
function processPerson (options) {
  console.log(options.name, options.age)
}

// New
function processPerson ({ name, age }) {
  console.log(name, age)
}
```

In Node.js it is quite frequent that a function accepts a single "options" object as parameter instead of having multiple parameters. ES2015 introduces a convenient way for extracting values from such an object and introduce them as variables inside the function.

Destructuring also works with arrays and in other places like variable declarations and assignment statements.

### Default Parameter Values

```JS
function inviteToParty (name = 'Max Mustermann') {
...
}
```

Default parameters provide a convenient way to assign values to variables that would otherwise be `undefined`. Note that default values can also be used in destructuring patterns like the one seen in the previous example.

## Conclusion

Node.js version 6 has good support for modern JavaScript. The ES2015 features highlighted above have in common that they can improve code quality by enhancing clarity and reducing verbosity.

# Pracitical Example: Unit testing with Mocha

This section serves as a practical example of how programming in Node.js looks like. We'll be using CommonJS modules, npm and also some modern JavaScript features presented in the previous sections.

Doing unit testing is good practice in programming projects. In Node.js projects it is also common practice to have unit tests. All major packages in the npm registry employ unit testing to ensure correct behavior. Luckily unit testing is very easy in Node.js. This section illustrates how to use the `mocha` testing framework and the `chai` assertion library.

## Project Setup

Create a project folder with a `package.json` inside. It you can use a test editor or use `npm init` to create the `package.json` file.

## Installation

Use `npm install --save mocha chai` to install `mocha` and `chai` as packages within the project.

## package.json Script
``` JS
{
  "name": "testing-demo",
  "scripts": {
    "test": "mocha test/**/*.js" // Add this
  },
  "dependencies": {
    "chai": "^3.5.0",
    "mocha": "^3.0.2"
  }
}
```

Add `"mocha test/**/*.js"` as the test script in your `package.json`. `test/**/*.js` is a glob pattern. It means "Select all `.js` files inside the `test/` directory". We can use `mocha` as a command because we installed `mocha` as a local package in the previous step.

## Create a Module with Tests

Create `lib/hello-world.js` with the following content:

```JS
module.exports = function helloWorld () {
  return 'hello world!'
}
```

The above module exports a single function which returns a string.

Create `test/hello-world.js` with the following content:

``` JS
const helloWorld = require('../lib/hello-world')
require('chai').should() // Chai

describe('helloWorld()', function () { // Mocha
  it('returns "hello world!"', () => { // Mocha
    helloWorld().should.equal('hello world!') // Chai
  })
})
```

The comments indicate whether the line uses functionality provided by the testing framework `mocha` or by the assertion library `chai`. The `should` mechanism is one of three different notations supported by the `chai` assertion library (Technical note: `should()` adds a `should` property on `Object.prototype` to make this work).

## Run It

Use `npm test` to run the test.

## Conclusion

This section showcased how working with Node.js commonly looks like by demonstrating how to do unit testing. More information and documentation about `mocha` and `chai` can be found on their respective project websites.

To conclude let it be said that it is most beneficial to create tests parallel to the development of a feature's functionality. The creation of a plethora of tests beforehand is usually impractical because the tests are frequently rendered obsolete as a result of the project evolving into a different direction. Also, it is not recommended to create the unit tests afterwards because treating testing as an afterthought usually leads to bad test coverage.

# Useful npm Packages

# Project

Our project was to create a mobile-first website providing information for [Pokemon Go](http://example.com) players. This website should show current sightings of pokemon on a map and predicted sightings in the near future. Additionally some more information about the Pokemon like an overview over all Pokemon and their attributes as well as a sentiment analysis for every Pokemon should be shown.

## Overview
The website consists of the following basic parts which will be explained in the upcoming sections:
* **Sidebar** for navigating through the app
* **PokeMap** for showing the map with sightings, predictions and mobs
* **PokeDex** for showing an overview of all Pokemon and searching
* **PokeDetail** for providing all information about a Pokemon
* **About page**
* **Imprint & Disclaimer**

## Sidebar
The sidebar enables navigating through the app by providing links to the **PokeMap**, the **PokeDex**, the **About page** and the **Imprint & Disclaimer** page.

## PokeMap
The PokeMap is screen showing a map with current Pokemon sightings, Pokemon predictions and PokeMobs. Additionally you can:
* Search for a Location or a Pokemon
* Filter by Time and/or Pokemon
* Show a detail sheet for a Pokemon-Sighting/Prediction/PokeMob

## PokeDex
The PokeDex shows a list of Pokemon including some of their attributes like name, number and rarity. Additionally it enables you to search for pokemon and open a Pokemons PokeDetail page.

## PokeDetail
The PokeDetail page shows all relevant information about a Pokemon, including:
* Name and number of Pokemon
* Description, types, weaknesses and strengths
* Attributes (like weight, flee rate, ...) and evolution
* Possible attacks
And additionally a sentiment analysis for the Pokemon. This data is extracted from [Twitter](https://www.twitter.com)

## About page
This page provides background information about the project and a list of all the contributors.

## Imprint & Disclaimer
This page shows legal information about the project.
