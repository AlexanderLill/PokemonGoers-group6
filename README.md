# Server-side JavaScript with Node.js Basics

# Introduction

Node.js is a JavaScript runtime environment that enables developers to build backends of sophisticated web applications. A characteristic that differentiates Node.js from other server-side technologies like Java is how concurrency is handled. Node.js' approach builds on the event-driven architecture of the JavaScript language. This means that a single JavaScript thread handles incoming requests. Node.js achieves this through asynchronous i/o. Instead of waiting for network requests or file system accesses to finish, the JavaScript thread continues its execution and a callback is executed once the data arrives. A single Node.js process can handle thousands of requests much more efficiently compared to the classical approach where for every request a new thread is created. Node is thus optimized for optimal throughput and scalability.

# Node.js history

8. Novermber 2009

Ryan Dahl, the created of Node.js, introduced Node.js at JSConf.eu in Berlin. On one of the first slides Ryan Dahl characterized Node.js with the following five points:

- Server-side JavaScript
  Back in 2009 JavaScript was mainly used in the browser. The usage of JavaScript to build a server-side application was a novelty.
- Built on Google's V8
  The V8 JavaScript engine developed by Google for its Chrome browser was at the time, and is still today, one of the fastest JavaScript engines on the market. Ryan Dahl chose to use it because V8 is also open-source.
- Evented, non-blocking I/O
  The way how Node.js deals with asynchronicity is one of its defining characteristics. Ryan Dahl later explained in his talk in great detail why asychronous i/o is imporant for Node.js.
- CommonJS module system
  Back in 2009 the usage of a module system with JavaScript was still uncommon. Ryan Dahl chose to use the CommonJS module format for Node.js. In Node.js every file is a module and it is generally perfered to have more modules instead of long files.
- 8000+ lines of C/C++, 2000 lines of JavaScript
  Ryan Dahl meant for Node.js to serve as the foundation for programs written in JavaScript. It itself is mainly written in the system programming languages C and C++. Today much of the functionality originally written for Node.js has been extracted from the Node.js project into standalone libraries like "libuv".

The first four points are still true for Node.js as we know it today. Only the number of lines of code in point 5 have risen considerably since. It is apparent that the architectural design choices that Ryan Dahl made for Node.js back in 2009 were very solid. The video recording of Ryan Dahl's talk is avaiable on YouTube.

12. January 2010

Isaac Schlueter introduces the package manager "npm". npm makes integrating third party code into Node.js projects very easy. The first versions of npm didn't ship with Node.js itself. Instead it had to be installed seperately.

10. November 2010

Ryan Dahl passes the copyright for Node.js to his employer, the company Joyent. Node.js had risen in popularity and he felt that the project should be backed by a corporate entity.

July 2011

Microsoft and Joyent cooperate to make Node.js run on Windows which previously only ran on Linux and macOS. This step meant that Node.js was then avaiable on all three major platforms.

25. November 2011

Node.js version 0.6.3 shipped as the first version with the package manager npm included. Node.js and npm are today inseparable projects. The inclusion of npm meant a major milestone.

December 2014

In late 2014 many of the main contributers became unhappy about how the Node.js open-source project was run. Releases were infrequent and the included version of the JavaScript engine V8 was outdated. That is why Fedor Idutny and other node contributers decided to fork the project. The resulting project was called "io.js". Contrary to Node.js io.js had much more frequent releases and it shipped with a recent version of V8 that included new JavaScript features. Consequently io.js felt a lot fresher than Node.js.

14. September 2015

The introduction of io.js created a rift inside the Node.js community. Both factions weren't happy with the split in the ecosystem and they began talking to each other about how the two projects could merge once more. As a result the Node.js Foundation was formed and in September 2015 Node.js version 4.0 shipped that combined both projects.

Now

Node.js is nowadays a very healthy open-source project.

- It consitently had 1-2 releases in the past months
- The Node.js Foundation has a technical steering committe that makes swift technical decisions
- npm, the package manager, has become very reliable

# CommonJS - Node's Module System

# npm - Node's package manager

# Excellent ES2015 support in Node.js

The objective of this section is to highlight the good support for modern JavaScript in Node.js.

## ES2015
"ES" stands for "ECMAScript". "ECMA" is the standards body that standardizes JavaScript. "ECMAScript" is the official name of JavaScript inside of its standard. "ES2015", often also called "ES6", introduces many new features to the language. In recent months the version of V8 included with Node.js began to support more and more ES2015 features. According to the feature test on  [kangax.github.io](http://kangax.github.io/compat-table/es6/) Node.js version 6 supports 92% of the ES2015 specification.

## 7 useful ES2015 features

### Property Shorthand

```JS
const name = 'Max Mustermann'
const age = '67'

// Old
const person = { name: name, age: age }

// New
const person = { name, age }```


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

obj.hello() ```

This object literal enhancement makes the definition of methods on an object briefer.

### Arrow functions

```JS
// Old
let studentNames = students.map(function (s) { return s.name })

// New
let studentNames = students.map(s => s.name)```

Arrow functions are a new syntax for defining functions. Arrow functions are less verbose that the tranditional function literal. The example above shows how omitting the curly braces around the function body introduces and implied return statement. Another useful feature of arrow functions is that they preserve the `this` of the outer scope. This is typically useful for defining callbacks.


### Classes

```JS
class Student extends Person {
  constructor (name, university) {
  super(name)
    this._university = university
  }

  get university () { return this._university }
}```

ES2015 introduces a syntax for defining classes. It should be noted that the new class syntax is just an alternative syntacticaly way for defining classes in JavaScript. Under the hood ES2015 classes work with the same prototypal inheritance system that JavaScript always had.


### Promises
```JS
const fs = require('fs-promise')

fs.readFile('hello.text')
  .then(buffer => { ... })
  .catch(error => { ... })```

Node.js uses traditionally callbacks for asynchronicity. ES2015 introduces an alternative way called a "promise". The advantages of promises are that they are objects that can be returned as a return value by function. Also, promises provide a robust way for error handling which reduces the risk for bugs inside error handling code.

### Destructuring Assignment
```JS
// Old
function processPerson (options) {
  console.log(options.name, options.age)
}

// New
function processPerson ({ name, age }) {
  console.log(name, age)
}```

In Node.js it is quite frequent that a function accepts a single "options" parameter instead of having multiple parameters. This options parameter is an object and its properties need to be acccessed in the function body. ES2015 introduces a convenient way for extracting values from such an object and introduce the as variables inside the function.

Destructuring also works with arrays and in other places like variable declarations and assigment statements.

### Default Parameter Values

```JS
function inviteToParty (name = 'Max Mustermann') {
...
}```

Default parameters provide a convenient way to assign values to variables that would otherwise be `undefined`.

# Pracitical Example: Unit testing with Mocha

# Useful npm Packages
