---
layout: post
title:  "Invocation patterns and 'this'"
date:   2021-06-17 21:04:00 +0200
categories: vanillaJS
---


The `this` keyword gives context as to which object we are referring to.

When a function in invoked, in addition to its decalared parameters, it gets two additonal (hidden) parameters: `this` and `arguments`.
The bindings of the `this` parameter depends on the way the function was invoked.

There are 5 ivocation patterns detailed bellow:


## 1. Method invocation pattern

**definition:** When a function is stored as a property value of an object, we call it a *method*.

When we invoke a method call, `this` alway refer to the object that defines the method as property value.
Using its `this` parameter, the method can access the properties of its defining object.

for example:


```js
const myObject = {
  name: "John Doe",
  age: 25,
  sayHi: function() {
    console.log(`Hi, my name is ${this.name}`); // `this` refers to the wrapping object
  }
}


myObject.sayHi() // "Hi, my name is John Doe"
``` 




## 2. Function invocation pattern


**definition:** The *global scope* is the scope that contains and is visible to all other scopes. In particular, a variable that is define outside of every scope (function) is in the global scope and accessible from everywhere.

**definition:** The *global object* is 

When a function is not stored as a property value of an object in the global scope, it is called a function.  calling `this` inside it refers to the global object, or is `undefined` in `use strict` mode.

**important:** Even if the function is declared inside another function, its `this` parameter referes to the global object!
As a result, the `this` of the inner function may differ from the `this` of the wrapping function. (e.g, when the outer function is a method) 


for example:

```js
var myObject = {
  val: 1,
  increment: function(n = 1) {
    this.val += n;
    var checkMod7 = function() {
      console.log(this.val % 7 ? this.val : "Boom!");
    }

    checkMod7();
  }
}


myObject.increment(); // ERR: Cannot set property 'val' of undefined
```


To fix that we can use a simple trick to hold the `this` value of the outer function as a variable that the inner function has access to:

```js
var myObject = {
  val: 1,
  increment: function(n = 1) {
    var that = this;
    this.val += n;
    var checkMod7 = function() {
      console.log(that.val % 7 ? that.val : "Boom!");
    }

    checkMod7();
  }
}

myObject.increment(); // 2
myObject.increment(5); // "Boom!"
```

This was fixed in ES6 with the introduction of *arrow functions*, which always have their `this` as the this of the calling parent. see bellow.


## 3. Constructor invocation pattern

A *constructor function* is a function that can be invoked with the *new* keyword. Any function can be a constructor function. By convention, constrtor function name begins with a capital letter.

Invoking a construction function with the `new` keyword makes the function create a new empty object and bind `this` to that newly created object. By default the constructor function returns this object.


example:

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.sayHello = function() {
    console.log("Hello, my name is " + this.name);
  }
}


var p = new Person("John Doe", 35);

p.sayHello(); // Hello, my name is John Doe
console.log(p.age); // 35
```

This is equivalent to:

```js
function Person(name, age) {
  var obj = {};
  obj.name = name;
  obj.age = age;
  obj.sayHello = function() {
    console.log("Hello, my name is " + this.name); 
  }

  return obj;
}

var p = Person("John Doe", 35); // no `new` keyword
p.sayHello(); // Hello, my name is John Doe
console.log(p.age); // 35
```

Clearly, the `new` keyword saves us some keystrokes and is less verbose.


## 4. Apply invocation pattern

With the `apply` method, we can invoke a function, and bind its `this` parameter to any object we like.

example:

```js
var p1 = {
  name: "Satoshi Nakamoto",
  age: undefined,
  sayHello: function() {
    console.log("Hello, my name is " + this.name);
  }
};


var p2 = {
  name: "John Doe",
  age: 25,
  sayHello: function() {
    console.log("Hello, my name is " + this.name);
  }
};

p2.sayHello.apply(p1); // "Hello, my name is Satoshi Nakamoto"
```


## 5. Arrow function invocation pattern


*Arrow functions* were introduced in ES2015. They don't have their own `this` binding. Instead, they retain the `this` value of the enclosing scope.
