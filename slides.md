title: Agenda
class: big
build_lists: true

Agenda for today:

- Functions and scopes
- Prototypes
- Hands-on exercise

---

title: Functions
class: segue dark nobackground

---

title: Declaration syntax

Two ways to declare a function.

<pre class="prettyprint" data-lang="javascript" contenteditable>
// 1. Named function
function f (a, b) {
  // ...
}

// 2. Function expression
var f = function (a, b) {
  // ...
}

var myObj = {
  // A member function
  g: function (x, y) {
    // ...
  }
};
</pre>

For sake of clarity, we'll call member functions *methods*.

---

title: Function invocation

There are three ways to invoke a function.

<pre class="prettyprint" data-lang="javascript" contenteditable>
function hello (name) {
  console.log(name + ' says hello');
}
</pre>

- Execute it: `hello('Bob');`

- .call(): `hello.call(window, 'Bob');`

- .apply(): `hello.apply(window, ['Bob']);`

---

title: What's `this`?
build_lists: true

- The keyword `this` inside a function refers to its context.

- A function's context depends on its declaration and invocation.

- This can be a great source of confusion. :(

---

title: Function context based on declaration

- For functions, `this` is the global context (e.g. window).

- For methods, `this` is the object the method belongs to.


<pre class="prettyprint" data-lang="javascript" contenteditable>
function hello () {
  console.log(this + ' says hello');
}

var bob = {
  name: 'Bob',
  toString: function () { return this.name; },
  hello: function() {
    console.log(this + ' says hello');
  }
};

hello(); // '[object Window] says hello'
bob.hello(); // 'Bob says hello'
</pre>

---

title: Function context based on invocation

The context of functions and methods can be modified via `.call()`
and `.apply()`.


<pre class="prettyprint" data-lang="javascript" contenteditable>
function hello () {
  console.log(this + ' says hello');
}

var bob = {
  name: 'Bob',
  toString: function () { return this.name; },
  hello: function() {
    console.log(this + ' says hello');
  }
};

hello.call('Joe'); // 'Joe says hello'
bob.hello.call('Joe'); // 'Joe says hello'
</pre>

---

title: Function.prototype.bind

`Function.prototype.bind` returns a new function that, when called,
will call the original function with the bound context.

<pre class="prettyprint" data-lang="javascript" contenteditable>
function hello () {
  console.log('Hello ' + this.name);
}

var helloBob = hello.bind({name: 'Bob'});
helloBob(); // 'Hello Bob'

helloBob.call({name: 'Joe'}); // 'Hello Bob'
</pre>

---

title: Scopes
class: segue dark nobackground

---

titles: Functions create scopes

JavaScript only has function scopes. (no block scopes)

<pre class="prettyprint" data-lang="javascript" contenteditable>
var x = 1;
var y = 2;

function foo () {
  var x = 3;
}

if (true) {
  var y = 4;
  foo();
}

console.log(x); // 1
console.log(y); // 4
</pre>

---

title: Closure
build_lists: true

- A closure is a function or reference to a function together with
a referencing environment.

- Allows a function to access non-local variables even when invoked
outsides of its lexical scope.

- It's actually very simple!

---

title: Closure example

<pre class="prettyprint" data-lang="javascript" contenteditable>
function makeAdder (a) {
  return function (b) {
    // `a` is non-local
    return a + b;
  };
}

var plusOne = makeAdder(1);
plusOne(2); // 3
</pre>

---

title: Scope chain

A function resolves non-local variables by tracing the scope chain.

The resolution first goes to the immediate parent, and steps all the
way up until `window` is reached.

<pre class="prettyprint" data-lang="javascript" contenteditable>
// Global var
window.x = 1;

function f () {
  var y = 2;
  var z = 3;
  function g () {
    var z = 5;
    console.log(x, y, z);
  }
  g();
}

f(); // 1 2 5
</pre>

---

title: Immediately Invoked Function Expressions (IIFE)

An anonymous function can be immediately invoked to create
a new context.

<pre class="prettyprint" data-lang="javascript" contenteditable>
(function () {
  // ...
}());
</pre>

This is useful for creating "private" variables.

<pre class="prettyprint" data-lang="javascript" contenteditable>
var x = 1;

(function () {
  var x = 2;
  console.log(x); // 2
}());

console.log(x); // 1
</pre>

---

title: Module pattern

An IIFE that returns an object is called a **module**.

This pattern hides internal module implemetations, and
creates privacy for its variables.

<pre class="prettyprint" data-lang="javascript" contenteditable>
var CounterModule = (function () {
  var counter = 0;
  return {
    get: function () { return counter; },
    increment: function () { return ++counter; },
    reset: function () {
      console.log('resetting counter');
      counter = 0;
    }
  };
}());
CounterModule.get(); // 0
CounterModule.increment(); // 1
CounterModule.reset(); // 'resetting counter'
</pre>

---

title: Prototypes
class: segue dark nobackground

---

title: Object prototypes

All object types in JavaScript have a special `prototype` property.

<pre class="prettyprint" data-lang="javascript" contenteditable>
Boolean.prototype;
Number.prototype;
String.prototype;
Object.prototype;
Array.prototype;
Date.prototype;
RegExp.prototype;
</pre>

The prototype object holds properties and methods for a "class".

<pre class="prettyprint" data-lang="javascript" contenteditable>
Number.prototype.toFixed;
String.prototype.split;
Object.prototype.toString;
Date.prototype.getTime;
Array.prototype.slice;
RegExp.prototype.test;
</pre>

---

title: Function as constructors

Every function can be used as a constructor
by using the `new` operator.

You can think of these functions as "classes".

<pre class="prettyprint" data-lang="javascript" contenteditable>
function Foo () {
  this.bar = function () {
    console.log('foobar')
  };
};

var foo = new Foo();

foo.bar(); // 'foobar'
</pre>

Note: When function is called using the `new` operator, the `this`
keyword refers to the new instance.

---

title: Prototypes on functions

- Functions have a special `prototype` object attached to them.

- When an instance is newed using a function, it shares all of
the properties of its `prototype`.

- This allows instances to share methods and properties.

<pre class="prettyprint" data-lang="javascript" contenteditable>
function Foo () {};
Foo.prototype.x = {};
var foo1 = new Foo();
var foo2 = new Foo();
console.log(foo1.x === foo2.x); // true
</pre>

---

title: Inheritance chain

- When an object is instantiated, it actually has two prototypes set.
    - The prototype of the function/"class" (`.prototype`)
    - An internal pointer of the original prototype (`__proto__` or
      `Object.getPrototypeOf()`)

<pre class="prettyprint" data-lang="javascript" contenteditable>
function Person () {};
Person.prototype.kick = function (type) {
  console.log(type + ' kick');
};
function ChuckNorris () {};
ChuckNorris.prototype = new Person();
ChuckNorris.prototype.kick = function () {
  Object.getPrototypeOf(this).kick('Roundhouse');
};
var chuck = new ChuckNorris();
chuck.kick(); // 'Roundhouse kick'

chuck instanceof Person && chuck instanceof ChuckNorris; // true
</pre>

---

title: A few caveats...
build_lists: true

- `Object.getPrototypeOf()` is only available in ES5.
- `__proto__` is non-standard and not in all browsers.
- We can get around this by manually setting a "super" prototype property.

    <pre class="prettyprint" data-lang="javascript" contenteditable>
    function Person () {};
    Person.prototype.kick = function (type) {
      console.log(type + ' kick');
    };

    function ChuckNorris () {};
    ChuckNorris.prototype = new Person();
    ChuckNorris.\_\_super\_\_ = Person.prototype;
    ChuckNorris.prototype.kick = function () {
      ChuckNorris.\_\_super\_\_.kick('Roundhouse');
    };
    </pre>

---

title: Another problem

- The constructor of the "super" prototype is called
    whenever a new prototype inherits from it.

- Two ways around it
    - Using `Object.create()`
    - Using a surrogate function

<pre class="prettyprint" data-lang="javascript" contenteditable>
function Foo () { console.log('Foo constructor called!'); }
function Bar () {};
Bar.prototype = new Foo(); // 'Foo constructor called!'

Bar.prototype = Object.create(Foo.prototype); // Only in ES5

function Surrogate () {};
Surrogate.prototype = Foo.prototype;
Bar.prototype = new Surrogate();
</pre>

---

title: Exercise

Address book
