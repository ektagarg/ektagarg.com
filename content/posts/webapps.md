---
title: "How web applications work?"
description: "A little digging into the history of web applications."
date: 2020-12-30T16:08:56+05:30
author: "Ekta Garg"
---

You must be thinking that such a basic question doesn't need an entire article. But as you will read, you'll understand why this basic question do deserve an article.

Well, I asked this same question written above to a room full of programmers/developers. I could see the look of total contempt and amazement for asking such a basic question. But as I started getting answers from assembled technologies, I realised that the core understanding of how web apps work isn't that simple.

In a basic sense, a web application is a computer program where a client sends an HTTP request and a server sends an HTML page in the form of an HTTP response. You must be thinking "Isn't it exactly like a web server?". No, it's not! A web server looks for the file in a directory and return those files, but a web application doesn't simply return files; it first processes and request and performs programmed operations and then return the files.

A web application is a program which needs to fulfill this criteria:

* The program must return HTML to a client that renders HTML and then displays it to a user.
The data must be transported to the client through a protocol i.e. HTTP.

* If a program doesn't render HTML, instead it provides data in some other form then it's a web service, not a web application.
A little digging into the history of web applications
HTTP

Earlier in this article, I used term HTTP. 

### What is HTTP?

HTTP is the application-level communications, a stateless, text-based, request-response protocol that uses the client-server computing model. 
A client-server computing model is one where a client (a requester) always initiates the conversation with a server (the responder). Here, a browser works as a client and web server works as a server. Everything in HTTP request starts with a request. 

##### The HTTP request, like any message, consists of a few lines of text in the following order:

```
Request-line
Zero or more request headers
An empty line
The message body (optional)
```

##### This is how an HTTP request looks:
```
GET /Protocols/rfc2616/rfc2616.html HTTP/1.1
Host: www.w3.org
User-Agent: Mozilla/5.0
(empty line)
```

In this request, the first line is the request-line. The first word in the request-line is the request method, followed by the URI(Uniform Resource Identifier) and HTTP version. The next two lines are the request headers. Notice the last line is empty, which must exist even though there's no message body. Whether the message body exists depends on the request method.
Let's have a look at all the request methods:

`GET` - Tells the server to return the specified resource.

`HEAD` - The same as GET except that the server must not return a message body. This method is often used to get the response headers without carrying the weight of the rest of the message body over the network.

`POST` - Tells the server that the data in the message body should be passed to the resource identified by the URI. What the server does with the message body is up to the server.

`PUT` - Tells the server that the data in the message body should be the resource at the given URI. If data already exists at the resource identified by the URI, that data is replaced. Otherwise, a new resource is created at the place where the URI is.

`DELETE` - Tells the server to remove the resource identified by the URI.

`TRACE` - Tells the server to return the request. This way, the client can see what the intermediate servers did to the request.

`OPTIONS` - Tells the server to return a list of HTTP methods that the server supports.

`CONNECT` - Tells the server to set up a network connection with the client. This method is used mostly for setting up SSL tunneling (to enable HTTPS).

`PATCH` - Tells the server that the data in the message body modifies the resource identified by the URI.