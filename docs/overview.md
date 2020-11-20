# Overview

The Notes **Node.js backend** application is a live demonstration project. The [source code](https://github.com/wkande/notes) lives on GitHub and the application is hosted on Heroku.

The articles in the **Source Code** section of the Notes portal explain how the backend server works. Most importantly the articles will detail how the source code is structured and how it was assembled.

The **Notes backend** server application is written in [Node.js](https://nodejs.org/en/) using the [Express](https://expressjs.com) framework.

## Node.js

As an asynchronous event-driven JavaScript runtime, Node.js is designed to build scalable network applications. Many connections can be handled concurrently. Upon each connection, a callback is fired, but if there is no work to be done, Node.js will sleep.

This is in contrast to today's more common concurrency model, in which OS threads are employed. Thread-based networking is relatively inefficient and very difficult to use. Furthermore, users of Node.js are free from worries of dead-locking the process, since there are no locks. Almost no function in Node.js directly performs I/O, so the process never blocks. Because nothing blocks, scalable systems are very reasonable to develop in Node.js.

## Express

Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications. With a myriad of HTTP utility methods and middleware, Express creates a robust API environment.

## Best Practices

The Notes backend server application is careful to adhere to the best practices specified by both Node.js and Express to implement a scalable environment. It is important to create all routes without any synchronous blocking and to foster low latency. 