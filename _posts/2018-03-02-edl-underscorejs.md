---
layout: post
title: "Inside Underscorejs"
subtitle: "Empathy Driven Learning: Exploring, learning and rewriting opensource JavaScript libraries"
img: "laptop-heart.png"
date:  "2018-03-02 00:00 UTC"
tags: underscore, underscorejs, javascript, opensource
---

In this post, I dive into the codebase of
**[Underscorejs](http://underscorejs.org/)** to understand its structure, design patterns and how it works overall.

My aim is to share my learnings for three main reasons:
1. to help reduce the daunting feeling of reading code
2. to help you get a better understanding of JavaScript design patterns used in popular OpenSource code bases
3. to help you be able to rewrite a simplified version of this library by yourself which is
   extremely helpful if you like to become a better develper and/or
   contribute to OpenSource yourself.

## Introduction

According to their website, [Underscorejs](http://underscorejs.org/) is a JavaScript library that provides a whole mess of useful functional programming helpers without extending any built-in objects. It’s the answer to the question: “If I sit down in front of a blank HTML page, and want to start being productive immediately, what do I need?”

If you haven't already, it's very easy to use. You can simply put it
as a script inside your html page and access to all those utility
methods by typing \_ in your browser console.

The version that I'm analysing in this post is **version 1.8.3**
which is the latest version at this time.

To study the codebase, I simply go to the [Underscorejs github repository](https://github.com/jashkenas/underscore/blob/master/underscore.js) and scan through the code. I also save a copy of this file locally so that I can put a `debugger` or `console.log` wherever I need, in order to learn and play around with the code.

Note that I won't get into the details of methods. That's something
you can easily figure out by checking
[Underscorejs documentaion](http://underscorejs.org/docs/underscore.html).

The main focus of this post is the way Underscore is designed and structured.
By knowing this, you can simply examine the code, find things and
understand how they are connected together. Also, some or all or these
patterns may be used or even come from elsewhere. That is not the
purpose of this post. We are simply digging into the sourcecode to find
interesting stuff.

Now if you are still here, let's dive in!

### Wrapper parentheses

One of the first things you notice in the code is that he whole code is wrapped inside a function which is invoked inside parentheses:
```javascript
(function() {
  // ..... code .....
 }()); // ----> the function is immidietly invoked
```
This technique is called [**IIFE** (Immidietly Invoked Function Expression)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE).
The most important thing about this technique is that The `variable
within the expression can not be accessed from outside`. This means that
the variables we define inside the function, will be only accessible in
that scope and will not leak into the global scope unless specifically
told so.

This pattern is very useful for several reasons:
1. Global scope will remain clean
2. You can neatly decide what to expose to global scope and what to keep
private inside the function (refer to the section on [Public and Private methods](#public-and-private-methods))
3. You can namespace your public methods and properties

### Root setup and default values

This is how the root object is set up inside Underscorejs:

```javascript
var root =
  // browser window
  typeof self == 'object' &&
  self.self === self &&
  self || // -> root = window
  // node global
  typeof global == 'object' &&
  global.global === global &&
  global || // -> root = global
  this ||
  {};
```

This magic line sets up root object to the appropriate global obj based on the wrapping environment.
[Based on the condition that passes(window or global), the obj after || is set. This is a technique that takes advantage of Javascript Coercion](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical\_Operators) to set default values for variables.

```javascript
var harry = { name: 'harry' };
var i = true || harry; // -> true
var j = false || harry; // -> harry

function setName(name) {
  this.name = name || 'Harry';
  return this.name;
}
setName('Hermione'); // -> Hermione
setName();           // -> Harry
```

The interesting thing is that if the first condition passes, javascript
never looks into the second one. So, if self refers to the window
object, there is a good chance that global object is not even defined
but the code never throws an Undefined exception. Instead, it will
gracefully sets the root variable to the window object and moves on.

In case the first check returns false, then root will be set to global object or if none of the conditions returns true, then it will be set to an empty obj {} at the end of the expression.

### Underscore as a function

One of the confusing patterns I found in this codebase was the
definition of \_ itself. At first glance, when I tried to rewrite a
basic utility library by mimicing Underscorejs, this was how I did it:

```javascript
(function(global){
  var _ = {};

  _.printMeInConsole = function(msg) {
    console.log(msg);
  }

  global._ = _;
}(window));

_.printMeInConsole('Harry said Riddikulus!');
```

Only by defining \_ like the following we can make this code work fine:

```javascript
var _ = {};
```

However inside the Underscorejs codebase, \_ is defined as a function with one parameter:

```javascript
var _ = function(obj) {
  // .... code ....
};
```

This means:
1. unlike the first definition, you can invoke the second one since its a function
2. if you just reference it without any invocation, it still acts like an obj similar to the first definition

The reason that \_ is defined as a function is to be able to chain
methods. (refer to [Chaining methods](#chaining-methods))

### Public and Private methods

One simple, yet elegant pattern that is used inside Underscore is the
way it categorizes public and private functions.
By looking at this sample code that I've written mimicing Underscorejs,
   you can see that all public methods are those defined on \_ whereas
   all private ones are simply assigned to new variables.

```javascript
(function(global){
  var _ = {};

  // public
  _.printMeInConsole = function(msg) {
    say(msg);
  }

  // private
  var say = function (msg, type) {
    console.log(msg);
  }

  global._ = _;
}(window));

_.printMeInConsole('hello man');
_.say('hello man'); // --> Error: _.sayMe is not a function
say('hello man'); // --> Error: _.sayMe is not a function
```

The magic happens where \_ is exposed to the global object which in this
case is the window object passed to the main function.
To truely mimic the way Underscore does this, we should write something
like the following.

```javascript
(function(){
  var root =
    // browser window
    typeof self == 'object' &&
    self.self === self &&
    self || // -> root = window
    // node global
    typeof global == 'object' &&
    global.global === global &&
    global || // -> root = global
    this ||
    {};

  var _ = {};

  // public
  _.printMeInConsole = function(msg) {
    say(msg);
  }

  // private
  var say = function (msg, type) {
    console.log(msg);
  }

  root._ = _;
}());
```

As you see in the sample, any method or properties that are attached to
\_ will be later exposed to root which points to the global object and
therefore will be accessible via global scope under the underscore namespace.

### Function as function argument

Another interesting pattern used in Underscorejs codebase is sending a
function as a param to another function in which case the passed function is not invoked until later when it's called somewhere inside the first function.

```javascript
(function(global){
  var _ = {};

  // public
  _.printAge = function(personObj) {
    say(personObj, calculateAge);
  }

  _.printName = function(personObj) {
    say(personObj, getFullName);
  }

  // private
  var calculateAge = function(obj) {
    return 'Age: ' + (2018 - obj.dob.getFullYear());
  }

  var getFullName = function(obj) {
    return 'Name: ' + obj.fname + ' ' + obj.lname;
  }

  var say = function (obj, msgFunc) {
    console.log(msgFunc(obj));
  }

  global._ = _;
}(window));

var harry = {
  fname: 'Harry',
  lname: 'Potter',
  dob: new Date(1980, 7, 31)
}

_.printName(harry); // -> 'Name: Harry Potter'
_.printAge(harry);  // -> 'Age: 30'
```

In the sample code above, both public functions \_.printAge and
\_.printName call private function say. However they send different
functions to say as their msgFunc. When calling say, only the reference
to that method (calculateAge and getFullName) will be passed. This means
that those two methods get only invoked when say is called with an
object. Notice that when invoking \_.printName and \_.printAge, we only
send the object and not any reference to the msgFunc. But those methods
know which msgFunc to call under the hood.

One of the places in the Underscorejs codebase that this pattern is used is createAssigner function which
is a private function that both \_.extend and \_.extendOwn use under the hood. Each of those extend functions pass a different key function to createAssigner to determine which set of keys they need to get the proper result.

[\_.extend](http://underscorejs.org/#extend) passes \_.allKeys function to createAssigner which results in
all the properties and methods of the source object be copied in the
destination object, including the inherited methods and properties under the \_\_proto\_\_
object.

[\_.extendOwn](http://underscorejs.org/#extendOwn) passes \_.keys function to createAssigner which results in
all the properties and methods of the source object be copied in the
destination object, excluding the inherited methods and properties under the \_\_proto\_\_
object.


### Function that returns another function

Take a look at the following example:

```javascript
(function(global){
  var _ = {};

  // public
  _.printAge = function(personObj) {
    say(calculateAge)(personObj);
  }

  _.printName = function(personObj) {
    say(getFullName)(personObj);
  }

  // private
  var calculateAge = function(obj) {
    return 'Age: ' + (2018 - obj.dob.getFullYear());
  }

  var getFullName = function(obj) {
    return 'Name: ' + obj.fname + ' ' + obj.lname;
  }

  var say = function (msgFunc) {
    return function(obj) {
      console.log(msgFunc(obj));
    };
  }

  global._ = _;
}(window));

var harry = {
  fname: 'Harry',
  lname: 'Potter',
  dob: new Date(1980, 7, 31)
}

_.printName(harry); // -> 'Name: Harry Potter'
_.printAge(harry);  // -> 'Age: 30'
```

Can you detect the difference between this code and the one in [Function as function argument](function-as-function-argument)?

Both say function and its callers are changed. Here, private function 'say' returns a function that
prints a message in console, instead of doing that right away in the
previous section.

Cool thing about this pattern is that this time function 'say' does not at
all concerns itself with personObj. As far as 'say' is concerned, it
receives a msgFunc and returns another function. This technique
encapsulates the behaviour of each function to itself.

Another cool thing is that inside the public functions \_.printName and
\_.printAge, 'say' is called like this `say(getAge)(harry);`
which reads something like ***'setup funcion 'say' to perform calculateAge on the input objects and
then pass object harry to get its age'***.

Inside Underscorejs, we can again detect this pattern in createAssigner function.

### Chaining methods

In Underscorejs, you can [chain](http://underscorejs.org/#chaining) functions together.
By chaining, each function uses the result of the previously invoked function as an argument.

The following code mimics the behaviour of chaining in Underscorejs.

```javascript
(function(global){

  var _ = function(obj) {
    if (obj instanceof _) {
      return obj;
    }

    if (!(this instanceof _)) {
      // creates the _ instance here
      return new _(obj);
    }

    // second time it gets to this point but 'this' is now an instance of _
    this._wrapped = obj;
  }

  // public

  _.calculateAge = function(obj) {
    return 'Age: ' + (2018 - obj.dob.getFullYear());
  }

  _.getFullName = function(obj) {
    return 'Name: ' + obj.fname + ' ' + obj.lname;
  }

  _.print = function(msg) {
    console.log('Wrote: ', msg);
  }

  _.chain = function(obj) {
    var instance = _(obj);
    instance._chain = true;
    return instance;
  }

  // private

  var allFunctions = ['calculateAge', 'getFullName', 'print', 'chain'];

  var addToPrototype = function() {
    for(var i = 0; i < allFunctions.length; i++) {
      // !! use 'let', 'var' won't work here due to closure and scope chain.
      //    here, 'var' will always return the first item of the array
      let funcName = allFunctions[i];

      _.prototype[funcName] = function() {
        var args = [this._wrapped];
        var funcResult = _[funcName].apply(_, args);
        return _(funcResult);
      };
    }
  }
  addToPrototype();

  global._ = _;
}(window));

var harry = {
  fname: 'Harry',
  lname: 'Potter',
  dob: new Date(1980)
};

_.chain(harry).getFullName().print(); // -> Wrote:  Name: Harry Potter
_.chain(harry).calculateAge().print();// -> Wrote:  Age: 48
```

In the example above, addToPrototype function is mainly where the magic
happens. By calling `_.chain(harry)`, we are telling the code to get ready
for a chain of method invokactions. So, it initially calls \_ with the object that
we send to chain.

By existing chain function, we have an instance of \_ that has:
1. a property called `_wrapped` which is set to object harry
2. a property called `_chain` which is set to true
3. no access to any of the \_ methods unless `addToPrototype` is already
invoked!

So, the role and invokaction of function addToPrototype is essential
here. Why is that you ask?

Because \_ by itself is only a function object with a list of \_
methods. However when an instance of any function in javascript is
created, it won't inherit all those methods. The only way to have access
to those methods is through function's prototype object. Anything that
lives inside the function's prototype can be accessed by instances of
that function. Therefore, when we create an instance of \_ inside
\_.chain function, we need to make sure that all those utility methods
exist inside \_.prototype too.

The interesting thing here is that since we only need that \_ instance
in the case of chaining methods, we have to make sure that the functions
added to \_.prototype are handling the case of \_wrapped objects:

```javascript
// We are adding a new function to 'prototype' that invokes
// the original function with _wrapped as arguments
_.prototype[funcName] = function() {
  var args = [this._wrapped];
  var funcResult = _[funcName].apply(_, args);
  return _(funcResult);
};
```

Notice that \_wrapped always holds the output of the last chained
method.

We can see this technique inside \_.mixin function in Underscorejs
codebase.
To summerise the behaviour of chaining functionality in Underscorejs:

1. if \_(obj) is called, an instance of \_ is returned, not \_ itself
2. this kind of call happens inside \_.chain method and the object is whatever object we are chaining
3. \_.chain then returns that instance of the \_ function with only two properties set (\_chain = true and \_wrapped = obj)
4. since \_ is now an instance of the function and not the function itself, we won't have direct access to function's methods (which previously we did). In order to have access to the same methods, we need to have added them to function's prototype object before the instance was even created. That's why we invoke that 'addToPrototype' (or [\_.mixin](https://github.com/jashkenas/underscore/blob/master/underscore.js#L1626-L1640) in Underscorejs) function inside the codebase itself.
5. Since we only want our chained methods to perform on the result of the previous method, we want the arguments to be whatever we find in the \_wrapped of the \_ instance plus any new arguments that are given (this last bit is not seen in my code but you can checkout the Underscorejs version [here](https://github.com/jashkenas/underscore/blob/master/underscore.js#L1632)
6. Every time a chained function is invoked, we need to put the result inside \_wrapped to be accessed by the next chained function. Checkout [chainResult](https://github.com/jashkenas/underscore/blob/master/underscore.js#L1622-L1624)
7. For every chained method, a new \_ instance is created with a new \_wrapped value
8. Inside Underscorejs, [\_.mixin is called right away to add all those methods to the function prototype](https://github.com/jashkenas/underscore/blob/master/underscore.js#L1640)


There! finished. I hope you enjoyed reading this post.
Happy learning!

**Let's learn with care!**
