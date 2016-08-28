What is Zappajs?
================

ZappaJS is a framework to rapidly build dynamic, distributed web applications.

It is based on two well-known frameworks: ExpressJS to handle native HTTP requests, and Socket.io to handle asynchronous messaging. ExpressJS is well-established, performant and offers a large library of extensions, known as "middleware", which simplify designing web applications. Socket.io supports a large range of transport methods, offers message routing and broadcast, and can transport relatively large objects. Both technology scale well independently, and ZappaJS helps to make them scale together.

Although ZappaJS is written in CoffeeScript and designed to be extremely simple to use in that language, it is in no way bound to it and can be used in many other languages, JavaScript included.

Why use ZappaJS?
================

ZappaJS benefits from the large middleware libraries that make part of the ExpressJS and Connect ecosystem. ExpressJS, Socket.io, and ZappaJS are all mature technologies and are used in production.

ZappaJS provides additional benefits to using plain ExpressJS and Socket.io:
- Domain-Specific-Language (DSL) that shortens writing and troubleshooting your code;
- tight integration and data-sharing between Express and Socket.io, allowing to distribute them over multiple processes or servers, allowing for scale and redundancy;
- middleware sharing between ExpressJS and Socket.io code.

A little bit of history
=======================

ZappaJS (under the name `zappa`) was originally designed my Maurice Machado, who ported the concept of Sinatra (built under programming language Ruby) to Node.js. Development has since then been handled by Stéphane Alnet.

ZappaJS components
==================

ZappaJS consists of:
- the Node.js module `zappajs`;
- the client-side module `zappajs-client`;
- server-side plugins.

No vendor lock-in
=================

ZappaJS allows you to use your own versions of ExpressJS and Socket.io if you'd like to.
