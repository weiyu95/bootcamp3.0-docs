# 3.1: Express.js

## Learning Objectives

1. Express.js is a server application framework that helps us receive requests from clients and respond with data
2. Understand how to create routes and corresponding middleware functions that handle requests to those routes
3. Know how to parse URL path and query parameters in route middleware
4. Middleware functions are functions that run during the "request-response cycle" and have access to Express request and response objects

## Introduction

Express.js is a server application framework that helps us receive requests from clients and respond with data. Servers are computers without a screen that perform logic on behalf of clients such as storing and retrieving data.

## Basic Express App

The following code is a minimal Express app that hosts a server at port 3000 and responds with "Hello, World!" at the root route, i.e. `localhost:3000` when run locally or `mysite.com` when deployed to `mysite.com`. [Check it out on StackBlitz](https://stackblitz.com/edit/basic-express-app-rocket?file=index.js), a popular online IDE!

{% code title="index.js" %}
```javascript
// require Express NPM library
const express = require('express');

// Declare the port to listen to and initialise Express
const PORT = 3000;
const app = express();

// Define a route and corresponding middleware function
app.get("/", (req, res) => {
  res.send("Hello, World!");
});

// Start the server
app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}!`);
});
```
{% endcode %}

Let's break down the above code.

1. Requiring `express` imports the Express library for us to initialise, configure and run our server
2. `PORT` defines the port that our Express server will listen on. Recall from Module 2 that ports determine which applications receive which requests on servers. We use SCREAM\_CASE to define constant variables like `PORT` at the top of our files or in a separate constants file for easy access.
3. `const app = express()` initialises our Express application
4. `app.get` is a route middleware (more on this below) that routes requests to a specific URL path to a specific middleware function to handle that request
5. `req` and `res` parameters to the middleware function are Express [Request](https://expressjs.com/en/4x/api.html#req) and [Response](https://expressjs.com/en/4x/api.html#res) objects respectively
6. `res.send` is a [method of the Express Response object](https://expressjs.com/en/4x/api.html#res.send) that sends a response to the requesting client
7. `app.listen` tells the Express app to start listening for requests at the specified port and execute the specified callback function after successfully starting

In the following sections we will dig deeper into route middleware and middleware functions in general.

## Routes

Routes (aka "route middleware", "routing methods") are middleware functions that define how servers handle requests to specific URL paths with specific URL methods. Routes provide some of the most basic infrastructure for server applications. Read [Express' official introduction to routes](https://expressjs.com/en/starter/basic-routing.html) for context.

{% embed url="https://expressjs.com/en/starter/basic-routing.html" %}
Official Express introduction to routes
{% endembed %}

```javascript
// Define a route and corresponding middleware function
app.get("/", (req, res) => {
  res.send("Hello, World!");
});
```

In the above example, our route middleware defines how our server responds to GET requests to the root route `/`. Express applications typically have many routes that serve requests with various HTTP methods to many URL paths. We can handle other request methods by changing `.get` to `.post`, `.put` or `.delete`, and we can handle requests to other paths by changing the path parameter that is currently `'/'`. We can change how our server responds to specific requests by changing logic in the middleware function, for example to query a database and return results. More on databases in coming submodules.

Read [Express' official routing guide](https://expressjs.com/en/guide/routing.html) for a full introduction to Express routes, including how to use the `express.Router` class to decompose our routes into router modules for clearer organisation and abstraction.

{% embed url="https://expressjs.com/en/guide/routing.html" %}
Official Express routing guide
{% endembed %}

{% hint style="info" %}
**Require vs Import Statements**

You may notice that Express docs use `require` syntax to import modules. This is an older import syntax that is still supported by Node.js. Luckily we can generally use `require` and `import` statements interchangeably.

Require (aka CommonJS) syntax:

```javascript
const express = require("express");
```

Import (aka ES Modules) syntax:

```javascript
import express from "express";
```
{% endhint %}

## Middleware

Middleware functions (aka "middleware") are functions that run during the "request-response cycle" and have access to Express request and response objects. The request-response cycle is the logic a server executes between when the server receives a request and when the server sends a response for that request. Routing methods of the format `app.<METHOD>` are 1 form of middleware.

We can run other non-routing middleware in the request-response cycle by attaching middleware functions with `app.use` before routing middleware. This will allow us to execute arbitrary logic using `req` and `res` objects before our routes, such as logging requests, adding metadata to our requests or validating authentication. Express executes middleware in the order the middleware is bound to the `app` object, until any middleware sends a response back to the client.c

{% code title="index.js" %}
```javascript
const express = require('express');
const app = express();

// Define a custom middleware function myLogger to log requests
const myLogger = function (req, res, next) {
  console.log("LOGGED");
  // Call the next parameter to trigger the next middleware
  next();
}

// Attach myLogger to app with app.use before routes below
app.use(myLogger);

// Attach routes after attaching any non-route middleware
app.get("/", (req, res) => {
  res.send("Hello, World!")
});

app.listen(3000);
```
{% endcode %}

Notice in the above code we attach non-route middleware above route middleware because route middleware typically terminates the request-response cycle by calling [response methods](https://expressjs.com/en/guide/routing.html#response-methods) like `res.send`.&#x20;

Also notice how `myLogger` calls `next()` at the end of its execution to trigger the next middleware. Without calling `next()` the client would never receive a response because Express would not call the subsequent middleware function.

Read [Express' official guide to writing middleware](https://expressjs.com/en/guide/writing-middleware.html) for details.

{% embed url="https://expressjs.com/en/guide/writing-middleware.html" %}
Official Express guide to writing middleware
{% endembed %}

Read [Express' official guide to using middleware](https://expressjs.com/en/guide/using-middleware.html) for details on how to use middleware in Express apps, including how to apply middleware on Express routers and how to apply imported 3rd-party middleware, which we will do often.

{% embed url="https://expressjs.com/en/guide/using-middleware.html" %}
Official Express guide to using middleware
{% endembed %}

## CORS

CORS (Cross-Origin Resource Sharing) is a security mechanism that allows servers to specify which domains other than their own to accept requests from. Without CORS, hackers at malicious websites could induce users to perform sensitive actions to manipulate legitimate backends using authentication information stored in the browser. With CORS, legitimate backends can prevent such attacks by only allowing requests from legitimate domains.

CORS is relevant for us now because we will host our frontends and backends on different domains, and we will need to configure our backends to allow requests from our frontends. Express provides an [official CORS middleware NPM library `cors`](https://expressjs.com/en/resources/middleware/cors.html) to configure CORS for our backends.

For now we will use the most open and least secure `cors` configuration (`app.use(cors());`) to get our apps working. There are many ways to configure Express' `cors` library to be most secure that we can learn about later.

## Additional Resources

1. [Web Dev Simplified's intro to Express](https://youtu.be/lY6icfhap2o) provides a video tutorial to the above Express concepts
2. Stackoverflow shares [API server route design best practices](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)
3. Portswigger provides a [detailed explanation of CORS](https://portswigger.net/web-security/cors)
