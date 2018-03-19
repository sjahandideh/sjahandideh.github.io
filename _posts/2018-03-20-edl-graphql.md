---
layout: post
title: "Inside GraphQL"
subtitle: "Empathy Driven Learning: Exploring, reading and learning opensource JavaScript libraries"
img: "laptop-heart-graphql.png"
date:  "2018-03-20 00:00 UTC"
tags: GraphQL, javascript, opensource
---

In this post, I dive into the codebase of
**[Graphqljs](http://graphql.org/graphql-js/)** to understand its structure, design patterns and how it works overall.

My aim is to share my learnings for three main reasons:
1. to help reduce the daunting feeling of reading code
2. to help you get a better understanding of JavaScript design patterns used in popular OpenSource code bases
3. to help you be able to rewrite a simplified version of this library by yourself which is
   extremely helpful if you like to become a better develper and/or
   contribute to OpenSource yourself.

## Background

The main focus of this post is to describe the way Graphqljs is designed and structured.
By knowing this, you can simply examine the code, find things and
understand how they are connected together. Also, some or all of these
patterns may be used or even come from elsewhere. That is not the
purpose of this post. We are simply digging into the sourcecode to find
interesting stuff.

Now if you are still here, let's dive in!

## Introduction

According to their website,

> GraphQL is a query language for your API, and a server-side runtime for executing queries by using a type system you define for your data. GraphQL isn't tied to any specific database or storage engine and is instead backed by your existing code and data.

It basically provides a replacement and/or a complimentory tool for RESTful apis. What you need to know is that it consists of a server and a client.
graphqljs provides what you need to implement a graphql server in JavaScript which accepts a
graphql schema and a query and returns the data to the client.

There are a bunch of good articles about GraphQL, its components and how
it works:


1. [Introduction to GraphQL](https://www.howtographql.com/basics/0-introduction/)
2. [Core concepts of GraphQL](https://www.howtographql.com/basics/2-core-concepts/)
3. [Graphql explained](https://dev-blog.apollodata.com/graphql-explained-5844742f195e)

I summarize the two important elements of a graphql server according to
[Graphql explained](https://dev-blog.apollodata.com/graphql-explained-5844742f195e):

1. Schema:
  > The schema tells the server what queries clients are allowed to make,
  and how different types are related, but there is one critical piece
  of information that it doesn’t contain: where the data for each type
  comes from!

  An example of a schema is:

  ```graphql
  type Person {
    id: Int
    name: String
    pets: [Pet]
  }
  type Pet {
    id: Int
    name: String
    bread: String
    type: String
    owner: [Person]
  }
  type Query {
    getPerson(id: Int): Person
    getPetsByOwner(owner: Person): [Pet]
  }
  schema {
    query: Query
  }
  ```

2. Resolve functions:

Resolve functions answer where the data for each type comes from.

> GraphQL resolve functions can contain arbitrary code, which means a GraphQL server can talk to any kind of backend, even other GraphQL servers. For example, the Author type could be stored in a SQL database, while Posts are stored in MongoDB, or even handled by a microservice.


> Perhaps the greatest feature of GraphQL is that it hides all of the backend complexity from clients. No matter how many backends your app uses, all the client will see is a single GraphQL endpoint with a simple, self-documenting API for your application.


### ES6+

GraphQL is written in ES6. There are a few things you need to
consider:

1. Whether you know ES5 or not, ES6 has introduced a bunch of syntax, concepts and patterns that needs to be studies and learned.

2. Unlike ES5, you can't write a small code and test it on your browser
   with only accessing the file with file protocol. Specifically to use
   module loading in ES6 (Import, Export), you need to run a simple web
   server locally to serve your files. I'm using [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server) that
   not only serves my files with http protocol, but also watches for any
   changes that I make to the code and does a live reloading behind the
   scenes.

3. ES6 module loading is implemented by the container that runs
   javascript and not js itself. So, you need to make sure that the
   browser, node server, etc that you are running your script in
   supports module loading. Otherwise, you need to use polyfills and
   transpilers such as babel.

   Note: Modern browsers currently
   support module loading in the following way:

   `<script type='module' src='my-js-module.js'></script>`

   There is a great article about ES6 browser support [here](https://jakearchibald.com/2017/es-modules-in-browsers/).

   Since ES6 has been around for a while, there are many resources to master ES6 out there. A few that I found
useful are:

1. Post by Eric Elliot [here](https://medium.com/javascript-scene/how-to-learn-es6-47d9a1ac2620)
2. Book by Kyle Simpson called [You Don’t Know JS: ES6 & Beyond](https://github.com/getify/You-Dont-Know-JS/tree/master/es6%20%26%20beyond#you-dont-know-js-es6--beyond)
3. Book by Nicholas Zakas called [Understanding ES6](https://leanpub.com/understandinges6)
4. And as reference, you can always go to [MDN Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)

### Flow

GraphQL also uses [flow](https://flow.org/en/docs/getting-started/)
which makes the syntax a bit different from what you would normally
expect from ES6 code.

You need to know a bit about flow in order to read and understand
graphql codebase. Here is a bunch of flow types that are used in graphql
codebase:

1. [Generic types](https://flow.org/en/docs/types/generics/)
2. [Type aliases](https://flow.org/en/docs/types/aliases/)
3. [Mixed types](https://flow.org/en/docs/types/mixed/)
4. [Function types](https://flow.org/en/docs/types/functions/)

### Import, Export

If you are not familiar with the way Module Loading is expected to work
in ES6, please take a look at [Export](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export), [Import](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import) and also [this chapter about Modules](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#modules).

Now the first thing I noticed inside graphQL sourcecode was this type of
export:

```javascript
export type { GraphQLArgs } from './graphql';
```

There are two interesting facts about this line of code:
1. it is importing GraphQLArgs from another file and then exporting that
   same object to outside.
2. Keyword `type` is a keyword defined in [flow](https://flow.org/en/docs/types/aliases/). In this case, it means
   'export GraphQLArgs which is a flow type'.


### StarWars example

GraphQL suggests reading through this
[README](https://github.com/facebook/graphql/blob/master/README.md) as
well as checking the code inside [Starwars
example](https://github.com/graphql/graphql-js/tree/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__)
to get started with the repository. So, this is what I'm going to do
here.

There are a bunch of query tests living in [Query test
file](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsQuery-test.js).
We want to look at [the first test](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsQuery-test.js#L17-L33) and track the code that executes it.

1. The test creates a [graphql query](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsQuery-test.js#L18-L23) (Look [here](https://github.com/facebook/graphql/blob/master/README.md#query-syntax) to learn how query definition works in graphql). It then invokes graphql function with [StarWars Schema](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsSchema.js) and the query as its arguments. StarWars Schema and graphql function are both [imported at the beginning of the test file](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsQuery-test.js#L12-L13).

2. Inside
[Schema](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsSchema.js#L10-L20), a number of graphql types are imported as well as a bunch of functions that are defined inside a separate file called [starWarsData](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsData.js).
starWarsData is the data source of this test meaning we fetch data from this file. It can be replaced by a database or an API.

3. The function which is responsible for executing the test query lives inside a file called
   [graphql](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/graphql.js#L71-L106).
   This is where the magic happens. We pass two arguments, Schema and
   query and expect to get the result of that query from this function.

Note: The [declare lines](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/graphql.js#L60-L70)
are written in flow and are added in order to add typechecking capabilities to
the code. It means that if someone tries to invoke graphql function in
the wrong way or with the wrong arguments, flow will throw a proper
error.

You can test flow by cloning graphql repository on your
machine by running

`npm install`

and then running

`npm run check` or `./node_modules/.bin/flow
check`

locally which will not raise any errors.

However if you change [line 25 of the test](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/__tests__/starWarsQuery-test.js#L25) to something such as
```javascript
const result = await graphql('blah');
```
and rerun flow, you will get an error that points to the [declarations](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/graphql.js#L60-L70).

And if you delete the declaratoins, you will get a list of less comprehensible errors by running flow.


### graphql function

The structure of graphql function in a nutshell looks like the following:

```javascript
function graphql(/*...some args...*/) {
  return new Promise(resolve => resolve( graphqlImpl(/*..some args...*/) ));
}

// test file
const result = await graphql(StarWarsSchema, query);
```

graphql function returns a new
[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) with a resolve function - the reject function will be the default one.
Promise acts like an event listener which waits for graphqlImpl to
finish execution. As you see, the main function
that performs the graphql functionality is in fact that graphqlImpl.

Inside our test, we call graphql with
[await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
which is a synchronous style syntax for an asynchronous job. In other
words, no matter what happens inside graphqlImpl, our test file waits
for it to finish execution before setting the result and moving on to executing the next line of code.
This means that if graphqlImpl is calling some API endpoints or
connecting to a database, JavaScript will not move on before making sure
graphqlImpl is finished executing. This behaviour would not be the same
without that Promise in place.

### graphqlImpl function

[graphqlImpl](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/graphql.js#L164-L203)
is a private function which performs the job of a graphql server. It is
invoked inside graphql.

```javascript
function graphqlImpl(
    schema,
    source,
    /*...rest of arguments...*/
): MaybePromise<ExecutionResult> {
  // Validate Schema
  const schemaValidationErrors = validateSchema(schema);
  if (schemaValidationErrors.length > 0) {
    return { errors: schemaValidationErrors };
  }

  // Parse
  let document;
  try {
    document = parse(source);
  } catch (syntaxError) {
    return { errors: [syntaxError] };
  }

  // Validate
  const validationErrors = validate(schema, document);
  if (validationErrors.length > 0) {
    return { errors: validationErrors };
  }

  // Execute
  return execute(schema, document, /*...rest of arguments...*/);
}
```

I have simplified graphqlImpl function so that its easier to read.

1. `MaybePromise<ExecutionResult>` is telling `flow` that the
   return value of this function is of flowtype
   [MaybePromise](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/jsutils/MaybePromise.js).
   If we look inside that type, we find this code:

   `export type MaybePromise<T> = Promise<T> | T;`

   which is saying that T can either be itself or
   wrapped in a Promise. In this case, T is [ExecutionResult](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/execution/execute.js#L105-L114).
   Again, ExecutionResult is only a javascript object literal that can include
   data and/or error fields. This means that if graphqlImpl returns an
   error, we need to return `{ error: error }` and if it succeeds, we
   need to return `{ data: data }` which can be simplified to `{ data }`.

2. graphqlImpl performs four operations: validating schema, parsing
   query, validating parsed query against schema and finally executing
   parsed query.

3. If any of the first three operations fails, graphqlImpl exits with a
   returning object of type ExecutionResult which only holds errors
   field and not the data.

4. If all of the first three operations succeed, graphqlImpl calls
[execute](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/execution/execute.js#L126-L222) function.
As you can see, execute function follows the same pattern that we have
enountered before. If we follow the code, we will get to [this
function](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/execution/execute.js#L224-L239)
which is called at the end of executeImpl.

5. As you see in
   [buildResponse](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/execution/execute.js#L236-L238),
   the last line checks for errors. If there is no errors, it will
   return `{ data }`. Otherwise, it returns

   `{ errors: context.errors, data }`.

   If you notice, both return values are objects of the same custom flowtype [ExecutionResult](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/execution/execute.js#L105-L114) which is what we expect to get as [return value](https://github.com/graphql/graphql-js/blob/5fe39262a308df944a87cc85b225228e7556aaa4/src/graphql.js#L70) in graphqlImpl function.

### Summary

GraphQL codebase may seem a bit intimidating at first. However,
after understanding flow types and some of the cool features of ES6
such as Promises, Module loading, etc it is pretty straight
forward. I think its a good codebase to look at since it follows some
of the recent patterns and techniques introduced in the JavaScript
community. Also, its a good project to learn and contribute to, as it is
a good alternative to RESTful api and maybe the future of all api
development.

There! finished. I hope you enjoyed reading this post.
Happy learning!

**Let's learn with compassion!**
