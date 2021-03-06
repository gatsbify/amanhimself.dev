---
date: 2018-06-12
title: 'Getting Started With Expressjs'
template: post
thumbnail: '../thumbnails/node.png'
slug: getting-started-with-expressjs
categories:
  - Nodejs
tags:
  - nodejs
  - expressjs
---

In previous two articles, we discussed on how to build a basic web server using Node.js alone. By Node.js alone, I mean using its core API modules such as `url` and `HTTP`. In the second article, we glimpsed briefly on how Node.js works behind the scenes. There is a lot in depth we haven't discussed such as working of libuv and role V8 in depth. I consider those things out of the scope of these articles since our sole focus is to get started in web development and build production level API servers. Using Expressjs we can do that.

When it comes to building a fully functional web server using Node.js, it can take a huge amount of time since Node.js core modules are raw. Over the years Node.js has matured enough due to the support from the community. Using Node.js as a backend for web applications and websites help the developers to start working on their application or product in a quick manner. In this tutorial, we are going to look into Expressjs which is a Node.js framework for web development that comes with features like routing and rendering and support for REST APIs.

Expressjs is the most popular framework when it comes to building a web server using Node.js at its core. Express requires minimal setup to kickstart an application and is mostly unopinionated in many ways. This gives the flexibility to follow a design pattern such as MVC to build a web application. Other backend frameworks follow a certain amount of philosophy that how a web application or an API should be written. This certainly affects development time.

Expressjs is developed by TJ Holowaychuk and is currently maintained by Node.js foundation and various open source developers around the world. To get started in web development using Expressjs, you need to have Node.js and npm installed. You can install [Node.js](https://nodejs.org/en/) on your local machine and along with it comes the command line utility `npm` that will help us to install plugins or as called dependencies later on in our project. If you have followed the previous articles, we have already installed Node.js and npm on our dev machines. So let us take a look at why we should use Expressjs:

- Since Expressjs is an unopinionated framework, you can use it to build single page, multi page, or even hybrid web applications. These days it is common to use Node.js in mobile development to provide an API, and Expressjs works very well there.
- By default, Express comes with a default templating engine called Jade/Pug. If you want to quickly prototype a website or web application, this is helpful.
- It supports MVC (Model-View-Controller), a very common architecture to design web applications.
- Since it is build on Node.js core API, it does leverage upon its asynchronous architecture. Please note that Express is mostly a wrapper written on Node.js core API to make things easy for the developer when it comes to writing code.

## Installing Express

To initiate any Expressjs project, we need a package.json file inside the root directory where will be storing files or modules written in JavaScript. First, we need to create it.

```shell
mkdir express-server-example
cd express-server-example
npm init --yes
```

The `npm init --yes` is available to us because we have installed `npm`. `npm` is the Node Package Manager, a public registry that has a collection of open-source packages for Node.js, front-end applications, mobile, APIs, databases, etc. There are two ways `npm` allows us to access to these packages or modules. We can install them locally or globally. Not every module is required to install globally but almost every other module is required to install locally. We will make use of both local and global installation so stay tuned.

This will generate a `package.json` file in the root of the project directory. We need to install `express` module from `npm` to initiate a web server.

```shell
npm install express --save
```

If you get the below in output in your terminal, this means the package was installed successfully.

```shell
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN express-server-example@1.0.0 No description
npm WARN express-server-example@1.0.0 No repository field.

+ express@4.16.3
added 50 packages in 4.214s
```

However, a better way to check if the package has installed correctly is to take a look at `package.json` file.

```json
{
  "name": "express-server-example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.16.3"
  }
}
```

This file is a manifesto for any Node.js or JavaScript related project. It is in JOSN format that means you can write JavaScript inside it. This is what triggers our JavaScript project or in this case, our Express web server when we deploy our application on a production ready service such as Heroku or AWS. Notice, the `dependencies` section which contains the name and the semantic version of the module we currently installed. Also, notice that a new folder called `node_modules` suddenly appeared in the root of our project directory. This folder stores the packages we install locally in our project.

## Building a Basic Web Server

Now at the root of our project, create a new file called `index.js`. Inside this file, we are going to write our first web server using Expressjs.

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => res.send('Hello World!'));

app.listen(3000, () => console.log('Example app listening on port 3000!'));
```

The first line in our `index.js` file declares that we import our open-sourced express module that we installed using npm in the previous section. This is how we will initialize every express server file we are going to create. This line confirms that we are defining an instance of `express` and are going to use it in the file below. This instance of `express` is going to provide us ways to handle requests coming from the client and deliver responeses to those requests from our web server. The `require` function is part of the core Node.js API and helps us to define an instance of any `npm` moduel or core Node.js module in any JavaScript file. Not just this one.

This `express` module is the wrapper or a class we were talking about earlier. It provides its own methods such as `app`. We can use this `app` to define routes and communicate with HTTP methods such as GET, POST, etc. and listen for incoming requests from the client. It will also be responsible to send a response to the server. In other words, `app` has access to request and response objects. The `.get()` signifies that it is an HTTP GET methods. We will learn more about HTTP methods in this series. It also has a callback function that contains two arguments `req` and `res`.

In our example above, we are using `req` for request and `res` for response as arguments. This `req` is an object that signifies HTTP request and `res` signifies response. We are listening to any request coming from a client on route path `/` using `app.listen()` that bootstraps the Express web server using a port such as `3000` in our case. We can use any port number when developing our web server. The console.log method is just an indication to us that the web servers starts without any errors. This will be echoed in your terminal.

To start this server, from your terminal, write:

```shell
node index.js
```

You will be prompted by a message:

```shell
Example app listening on port 3000!
```

You can also verify that our web server is working by visiting `http://localhost:3000` from a browser. In the browser window, you will be prompted by a `Hello World!` message. Congratulations! You just developed your first web server using Expressjs. Now let us proceed further. To close the server, press control + c from your keyboard.

## Anatomy of an Express Application

It is better to get familiar with some jargons or characteristics of an Express app before we dive deep into building a REST API. Generally, an Expressjs application will always have:

**Dependencies**

We have seen how to use dependencies in the previous section. It is simple, by requiring them using `require()` method. These dependencies can be installed using `npm`. There are two types of depedencies: `devDependencies` and `dependencies`. You will learn more about them as we proceed but the basic difference between them that `devDependencies` are not going to install when we deploy our web server. Our deployment server will only need a list `dependencies` in our `package.json` file.

**Instantiations**

The second thing we did on our express web server, is to get an instance of the `express` module as `app` to use its properties. This has to be done every time when we are writing an Express web server from scratch.

**Configurations**

Although our minimalistic application lacks any configuration in no time, we are going to see some, especially when we will be making use of environment variables and database. They are mostly statements that are defined in a separate file or can be defined in our `index.js` too.

**Middleware**

Middleware functions are the core philosophy as well as paradigm behind an Express application. The determine the flow of a request-response cycle. We already know that a web server is like a two way communication between a client and a server. When the client sends a request, a server has to respond, does not matter now whether the server responds successfully or with an error message. To manipulate this cycle between the two we use middleware functions.

**Bootstrapping Server**

This is simple. This is something that is always defined in the last. We have seen it already in our example is called `app.listen()` method.

## RESTful Service

If you are going to develop a web application, you are definitely going to come across this term called RESTful Service. REST is an acronym for Representation State Transfer and is an industry standard for defining how an API and its endpoint (routes) should communicate with the server of your application.

A REST API contains HTTP methods such GET, POST, etc. with endpoints that are nothing but URLs so that you can use these routes to fetch appropriate data or update/create new data or delete existing data in the database. In his [whitepaper](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm), Roy Fielding described the paradigms and practical use of REST as a protocol. I am going to summarise the points what he exactly says in that paper. The basis of a RESTful API depends on below:

- Client-Server
- Stateless
- Uniform Interface
- Cacheable
- Layered System
- Code-On-Demand style

An application that makes use of REST APIs perform four basic operations that are known as CRUD.

- C: Creating data
- R: Reading data
- U: Updating data
- D: Deleting/removing data​

To understand this concept, let us consider a real world example. Suppose we have a company called _Rentflix_ that rents out movies to various customers. On our client part of the application, we have a way to manage the list of our customers. To fetch this data or the list form our database, on our server we will have to expose this service in the form of an endpoint like: `http://rentflix.com/api/users`. Now, our client can send an HTTP request (GET in this case) to display this data in the browser.

Thus in Express, similar to our `app.get()` we do have other methods to hanlde each HTTP request:

- `app.post()`
- `app.put()`
- `app.delete()`

## Using `nodemon`

Let us create a new endpoint in our app called `numbers` which will send an array full of numbers to our client. Now, whenever we make changes to our Express web server, we need to quit the previous running web server and add the changes and then restart it. This is a very tedious way of working and to overcome this, we will install `nodemon` which is short for _node monitor_ as a devDependency in our package.json file.

To install:

```shell
npm install nodemon -D
```

Now go back to package.json file and notice this section below our previous `dependencies` section:

```json
"devDependencies": {
    "nodemon": "^1.17.5"
  }
```

This is how you install devDependencies, use the flag `-D` instead of `-S` or `--save`.

Now in order to make it work, we will also modify our package.json file and a new script called `dev` under `scripts` section. The `dev` is just naming convention that signifies that we are using for the development environment.

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js",
	"test": "echo \"Error: no test specified\" && exit 1"
}
```

To start this script, type:

```shell
npm run dev
```

You will been prompted with the same message as previous example. Now, without closing it, we will modify `index.js`file. Add a new route `/api/numbers` below our `app.get('/', ...)` endpoint

```javascript
app.get('/api/numbers', (req, res) => {
  res.send([1, 2, 3]);
});
```

Visit the url `http://localhost:3000/api/numbers` and you will notice the same result we are sending from our web server. Do notice that terminal now and you will see that it has been restarted on its own (using nodemon) at least once or whenever you have saved the file.

This concludes our first section of getting started with Expressjs and build a web server. In the next article, we are going to make use of Express Generator, a better way of getting started whether you are writing an API or an Express web application. More to follow about middleware functions using custom and both third-party middlewares, template engines and many more. To get the code or follow along, you can visit [this Github Reposiory](https://github.com/amandeepmittal/express-server-example).

---

[Originally published at javabeginnerstutorial.com](https://javabeginnerstutorial.com/node-js/getting-started-with-expressjs/)
