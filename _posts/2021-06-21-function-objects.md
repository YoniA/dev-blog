---
layout: post
title:  "Function Objects"
date:   2021-06-17 21:04:00 +0200
categories: vanillaJS
---


In JavaScript everything is an object. in particular, functions are objects too.
**reminder:** An object is a datastructure for storing pairs of name:value (where name is called a *property*),  and a hidden link to another object, via the `prototype` property.


## Function structure

A function declaration creates a *function object*, which by default inherits from `Function.prototype` (which in turn, inherits from `Object.prototype`.

let's look at an example:

```js
var foo = function () {
	console.log(42);
};
```


what is actually created is an object:

```js
{
	foo: function() {
		console.log(42);
	},
	prototype: {
		constructor: 

	}
}

```
